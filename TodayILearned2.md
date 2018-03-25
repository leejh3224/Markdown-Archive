# Today I Learned2 (2018. 03. 25 ~)

## 2018. 03. 25 일

### Typescript / SSR

* Typescript 와 React

주의점: .ts 파일에서는 jsx 문법을 사용할 수 없다. 무조건 .tsx 인지 확인할 것!
장점: 런타임에서 에러를 잡아줌 / IDE 의 자동 완성 기능을 최대치로 사용할 수 있음

* SSR 적용하기

typescript 와 server side rendering 을 적용하며 배운 것들을 기록.

Server side rendering

장점: 서버에서 html 을 미리 렌더링한 후 클라이언트에게 넘겨주므로 초기 로딩 시간이 짧아진다.
그리고 SEO 를 향상시킨다. (미리 결과물을 렌더링해서 넘겨주므로)

단점: 서버의 부하가 늘어난다, 프로젝트가 복잡해질 수 있다, 번들의 규모가 작으면 큰 이점이 없을 수 있다.

commonjs/esnext

적용방법:

* create-react-app 의 typescript 버전을 다운로드 ([링크](https://github.com/wmonk/create-react-app-typescript))

* 그 다음으로 express 서버를 만들어준다.

```jsx
import * as express from 'express'
// 타입스크립트의 type definitions
import { Application, Request, Response } from 'express'
import * as React from 'react'
// React 16의 기능
import { renderToNodeStream } from 'react-dom/server'
import { StaticRouter as Router } from 'react-router-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
// styled-components의 ssr 유틸리티
import { ServerStyleSheet, injectGlobal } from 'styled-components'
import * as path from 'path'

import reducer from 'store/reducers'
import globalStyle from 'styles/global-style'

import App from './App'
import html from './html'

const port = process.env.PORT || 3001
const server: Application = express()

server.use(express.static(path.resolve(process.cwd(), 'public/img')))

server.get('/', (req: Request, res: Response) => {
  const store = createStore(reducer)
  // head의 script tag안에 들어감
  const preloadedState = store.getState()
  const sheet = new ServerStyleSheet()

  /* tslint:disable:no-unused-expression */
  injectGlobal`
    ${globalStyle}
  `

  const body = sheet.collectStyles(
    <Provider store={store}>
      <Router context={{}} location={req.url}>
        <App />
      </Router>
    </Provider>,
  )

  res.write(
    html({
      title: 'GPS NOREABANG FINDER',
      state: preloadedState,
    }),
  )

  const stream = sheet.interleaveWithNodeStream(renderToNodeStream(body))

  stream.pipe(res, { end: false })
  stream.on('end', () => res.end('</div></body></html>'))
})

server.listen(port)
```

* html 파일

```jsx
// JSON.stringify의 대안
import * as serialize from 'serialize-javascript'

export default ({ title, state }: { title: string, state: object }) => `
<!DOCTYPE html>
<html>
  <meta charset="UTF-8" />
  <meta
    name="viewport"
    content="width=device-width, initial-scale=1.0"
  />
  <meta http-equiv="X-UA-Compatible" content="ie=edge" />
  <title>${title}</title>
  <body>
    <div id="root">
    <script>
      window.__PRELOADED_STATE__ = ${serialize(state)}
    </script>
`
```

* index.tsx

```jsx
import * as React from 'react'
import { hydrate } from 'react-dom'
import { consolidateStreamedStyles } from 'styled-components'
import { Provider } from 'react-redux'
import configureStore from 'store/configureStore'

import App from './App'
import registerServiceWorker from './registerServiceWorker'

// as any => 강제 타입 캐스팅
const preloadedState = (window as any).__PRELOADED_STATE__

delete (window as any).__PRELOADED_STATE__

const store = configureStore(preloadedState)

consolidateStreamedStyles()

hydrate(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root') as HTMLElement,
)
registerServiceWorker()
```

* 시작하기(with nodemon)

```bash
NODE_PATH=src nodemon src/server.tsx --exec ts-node --watch src
```

** 먼저 ts-node 를 다운로드 (yarn add --dev ts-node)
** tsconfig.json 을 수정

```json
{
  "compilerOptions": {
    "outDir": "build/dist",
    "module": "commonjs", // 기본 설정은 esnext
    "target": "es5",
    "lib": ["es6", "dom"]
    ...
  }
}
```

### styled-components 유틸리티

* style-lint

styled-components 를 위한 linter ([링크](https://github.com/styled-components/stylelint-processor-styled-components))

* styled-system

styled-components 를 위한 디자인 시스템 ([링크](https://github.com/jxnblk/styled-system))

* vscode-styled-components

styled-components syntax highlighting 지원

* 디자인 시스템

[Carbon Design System](http://carbondesignsystem.com/)

[Pricelinelabs Design System](https://github.com/pricelinelabs/design-system)

### Typescript / React snippets

1.SFC (Stateless Functional Component)

먼저 파일의 확장자가 .tsx 인지 확인해보자 아닐 경우 에러가 남

```tsx
import * as React from 'react'
import { Button } from './shared'

interface Props {
  // string literal type 지원
  name: 'hello' | 'world'
  class?: number
}

const HomeButton: React.SFC<Props> = ({ name }) => <Button>{name}</Button>

export default HomeButton
```

2.Component(class)

```tsx
import * as React from 'react'
import { connect } from 'react-redux'

import * as actions from 'store/test/testActions'

interface ClassProps {
  test: typeof actions.test // 이런 식으로 변수로부터 type 지정 가능
  thunk: typeof actions.thunk
}

// <Props, State> 순임
class Class extends React.Component<ClassProps, any> {
  render() {
    return <div onClick={() => this.props.thunk(22)}>a</div>
  }
}

export default connect(state => state, actions)(Class)
```

3.Redux ActionTypes

```ts
// enum 타입 => object 처럼 사용할 수 있다.
export enum ActionType {
  REQUEST = 'actionType/REQUEST',
}

export const EXAMPLE = 'EXAMPLE'
export type EXAMPLE = typeof EXAMPLE

// RootAction type
export type RootAction = ActionType | EXAMPLE
```

4.RootReducer

```ts
import { combineReducers } from 'redux'

import test, { Test } from 'store/test/testReducer'

// 전체 상태를 export
export interface ApplicationState {
  test: Test
}

export default combineReducers<ApplicationState>({
  test,
})
```

5.Example Reducer

```ts
import { createReducer } from 'store/utils'
import { Record } from 'immutable'

import * as types from '../actionTypes'

const TestRecord = Record({
  number: 0,
  a: 12,
})

export class Test extends TestRecord {
  number: number
  a: number
}

// record를 사용하려면 initialize 해야함
const initialState = new TestRecord()

export default createReducer(initialState, {
  // types.Action => 전체 액션 type
  [types.EXAMPLE](state: Test, action: types.Action) {
    return <Test>state
  },
})
```

6.Example Action

```ts
import * as types from 'store/actionTypes'
import { Dispatch, Action } from 'redux'

import { ApplicationState } from 'store/reducers'

export const test = (id: string) => ({
  type: types.ActionType.REQUEST,
  payload: id,
})

/* tslint:disable:no-console */
export const thunk = (t: number) => {
  return async (
    // 전체 state를 generic의 인수로 받음
    dispatch: Dispatch<ApplicationState>,
    getState: () => ApplicationState,
  ): Promise<Action | undefined> => {
    // promise를 반환
    const x = getState()
    console.log(x.test.a)
    try {
      await setTimeout(() => console.log(t), 100)
      return dispatch({ type: types.EXAMPLE })
    } catch (error) {
      console.log(error)
      return
    }
  }
}
```
