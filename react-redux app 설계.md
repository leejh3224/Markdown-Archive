# React-Redux app 설계

프로그래밍을 할 때 항상 염두에 두고 있어야하는 세 가지는 다음의 세 가지다.

## 원칙

1. Encapsulation

모듈은 최소한의 상태만을 담고 있어야하며 그 자체로 완결성을 지녀야한다.

만약 하나의 로직이 여러 군데에 분산되어 있다면 코드에 대한 수정/삭제가 일어나는 순간

여러군데의 코드를 모두 수정해야한다. 이는 분명 비효율적인 일이다.

그러므로 모듈은 될 수 있는 한 작은 상태의 조각만을 지녀야하고, 항상 별도의 updater 를 가져야한다.

2. Scalability

모든 애플리케이션은 그 기능이 끊임없이 추가되기 마련이다. 그러므로 만약 앱의 모든 로직이 한 파일에 모여있게 되면

변화를 추적하기 어려워지고 새로운 기능을 추가할 때마다 대대적인 리팩토링을 해야만 한다.

이는 개발속도를 떨어뜨리는 주범이 되며 항상 확장성을 고민하여 작은 파일 사이즈/분리된 로직을 생성해야한다.

3. Readabiliy

어떤 휼륭한 코드도 읽으면서 쉽게 이해가 되지 않는다면 무용지물이다. 훌륭하게 로직을 분리하더라도 이게 무슨 일을 하는 함수인지

또 이 컴포넌트가 무엇을 보여주는지를 쉽게 파악할 수 없다면 좋은 코드라고 할 수 없다. 항상 자연어에 가까운 이해하기 쉬운 이름을 써

야한다.

## 고민

2018.02.07 의 고민은 아래와 같다. 현재는 올유머 프로젝트가 종반에 다다르는 중.

* Api call 로직을 어떻게 분리할 것인가?

* Container 의 크기는 어떻게 할 것인가? 페이지 전체? 큰 (block) 컴포넌트?

* 복잡한 컴포넌트는 어떻게 분리할 것인가?

* UI 액션과 Domain 액션은 어떻게 구분할 것인가?

* 만약 비슷한 류의 상태를 공유하지만 다른 ui 를 가진다면 어떻게 처리할 것인가?

## 실마리

위의 고민들에 대한 실마리는 [wp-calypso](https://github.com/Automattic/wp-calypso) 레포지토리에서 발견할 수 있었다.

1. Api call 로직은 QueryComponent, 즉 life-cycle 함수를 통해 api call 은 하지만 아무것도 render 하지 않는 component 를 이용하면 된다. 이 경우 로직을 어디에 둘 지 고민할 필요없이 api call 이 필요한 컴포넌트 내부에 집어넣으면 된다.

[data 폳더](https://github.com/Automattic/wp-calypso/tree/master/client/components/data)에 들어가보면 query-resoucre 형태의 파일을 볼 수 있는데

```js
/** @format */

/**
 * External dependencies
 */

import PropTypes from 'prop-types'
import { Component } from 'react'
import { connect } from 'react-redux'

/**
 * Internal dependencies
 */
import { requestComment } from 'state/comments/actions'

export class QueryComment extends Component {
  static propTypes = {
    commentId: PropTypes.number,
    forceWpcom: PropTypes.bool,
    siteId: PropTypes.number,
  }

  static defaultProps = {
    forceWpcom: false,
  }

  componentDidMount() {
    this.request()
  }

  componentDidUpdate({ siteId, commentId }) {
    if (siteId !== this.props.siteId || commentId !== this.props.commentId) {
      this.request()
    }
  }

  request() {
    const { siteId, commentId, forceWpcom } = this.props
    if (siteId && commentId) {
      const query = forceWpcom ? { force: 'wpcom' } : {}

      this.props.requestComment({ siteId, commentId, query })
    }
  }

  render() {
    return null
  }
}

export default connect(null, { requestComment })(QueryComment)
```

이런 식으로 api call 로직만이 분리되어 있다.

2. Container 의 범위를 확장하는 대부분의 이유는 api call 로직을 옮기기 위해서인데 만약 1.과 같이 분리가 가능하다면 가능한한 작은 container 를 갖는 편이 더 나을 듯.

3. 복잡한 컴포넌트는 현재 사용중인 layout pattern 을 계속 사용하면 좋을 듯하다.

4. UI/Domain action 은 component 에서 data 와 관련된 모든 로직을 api call 만을 위한 QueryComponent 에 분리했으므로 자동적으로 component 는 UI action 만을 가지게 된다.

5. 비슷한 상태를 공유하는 컴포넌트라면 차라리 상태의 복사본을 하나 더 만드는 편이 낫다. 상태를 공유하는 여러 컴포넌트를 만들되 그 상태의 버전은 언제나 다를 수 있다. 상태를 잘게 쪼개는 게 관리하기 훨씬 수월하다.

## 그외

1. wp-calyso 는 state 폴더 안에 상태를 관리하는데 네이밍이 상당히 직관적이다.

예를 들어 application 은 on/offline 상태를 담당하고, checklist/comment/country 같이 도메인별로 분리가 되어 있거나, ui 폴더는 모든 ui state 를 관리한다.
