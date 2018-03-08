# 큰 규모의 프로젝트에서 리덕스를 사용하기 위한 다섯 가지 팁

이 글은 Chris Dopuch의 [Five Tips for Working with Redux in Large Applications](https://techblog.appnexus.com/five-tips-for-working-with-redux-in-large-applications-89452af4fdcb)를 번역한 글입니다.

![redux](./images/redux-five-tips-translate/redux.png)

Redux는 애플리케이션의 훌륭한 상태 관리 도구다. 

Redux는 단방향 데이터 흐름과 Immutable 한 상태 유지를 통해 상태 변화를 추적하기 쉽게 만들었다. 

각각의 상태 변화는 dispatch된 action에 의해서만 발생하고, reducer 함수는 적절하게 변화를 반영하는 새로운 상태를 돌려준다. 

AppNexus(역자주: Chris Dopuch가 소속된 회사명)의 UI는 많은 양의 데이터와 다양하고 복잡한 유저의 행동에 의해 영향을 받는다. (유저가 광고를 관리하거나 Inventory를 추가하는 등) 

복잡한 인터페이스를 개발하면서 우리는 Redux를 manageble 하게 사용하기 위한 몇 가지 팁과 규칙을 발견했다. 

다음 팁들은 큰 규모의, 데이터 중심의 애플리케이션을 작업하는 개발자들에게 도움이 될 것이다.

- [Index와 Selector를 사용하자.](#first)
- [Canonical, Edit, UI 상태간 분리](#second)
- [여러 페이지간의 상태 공유가 필요한 경우 혹은 그렇지 않은 경우](#third)
- [Reducer의 재사용](#fourth)
- [React components와 Redux state를 연결하기 위한 best practice](#fifth)
- [마치며: 역자의 말](#sixth)

<h2 id="first">데이터를 저장할 때는 index를 사용하고, 접근할 때는 selector를 사용하라.</h2>

적절한 데이터 구조를 선택하는 것은 애플리케이션의 구조나 성능에 큰 변화를 미칠 수 있다. 그리고 index의 사용은 API로부터 얻는 Serializable data(id가 매겨지는)를 저장할 때 굉장히 유용하다. 여기서 index는 key로 우리가 저장할 객체의 id 그리고 value로 저장할 객체를 가지는 모습의 객체다. 이 패턴은 데이터를 저장하기 위해 hashmap을 사용하는 패턴과 유사한데, 이로 인해 원하는 객체에 접근하는 시간을 단축할 수 있다. 이 패턴은 능숙한 Redux 사용자라면 이미 알고 있을 것이다. 실제로 Redux의 창시자 Dan Abramov는 Redux tutorial에서 이 패턴을 추천하고 있다. 

REST API endpoint(/users)에서 가져온 데이터를, 가져온 모양 그대로 array에 담아 state에 저장한다고 생각해보자. 만약 특정 user object를 읽으려면 어떻게 해야 되는가? 이 경우 array 전체를 탐색해야 한다. 즉, user의 수가 많을 수록 탐색시간이 기하급수적으로 늘어나는 것이다. 만약 selected/unselected처럼 특정 상태의 유저를 계속 추적해야한다면 어떻게 할 것인가? 아마 데이터를 두 개의 서로 다른 array에 저장하거나 selected/unselected 유저의 index를 저장하는 array가 필요할 것이다.

우리는 이런 방식 대신 index를 사용하여 데이터를 저장하기로 했다. Reducer의 state는 아마 이런 모습일 것이다. 

```js
{
 "usersById": {
    123: {
      id: 123,
      name: "Jane Doe",
      email: "jdoe@example.com",
      phone: "555-555-5555",
      ...
    },
    ...
  }
}
```

자 이제 이런 구조를 사용함으로써 얻는 이익을 생각해보자. 만약 어떤 user object를 찾아야한다면 간단히 index를 사용하면 된다.
```js
  const user = state.userById[userId]
```
이 방식을 통해 전체 list를 탐색하는 시간을 절약할 수 있을 뿐만 아니라 탐색하는 코드의 양도 줄여준다.

아마 이쯤되면 array를 통해 간단하게 user를 rendering 하던 부분은 어떻게 보완할 것이냐고 물어볼 것이다. user object 가 담긴 array가 필요하다면 selector를 사용하면 된다. 아래는 모든 유저를 가져오는 간단한 예다.

```js
  const getUsers = ({ userById }) => {
    return Object.keys(usersById).map(id => usersbyId[id]);
  }
```

위 코드에서 유저 리스트를 얻기 위해 우리는 state를 집어넣었다. 이제 array를 통해 user를 rendering 할 수 있게 됐다. 만약 selected user만 필요하다면 아래와 같이 하면 된다.

```js
  const getSelectedUsers = ({ selectedUserIds, usersById }) => {
    return selectedUserIds.map(id => uersById[id]);
  }
```

Selector 패턴은 또한 코드의 유지보수도 용이하게 한다. 나중에 state의 모양을 바꿀 필요가 생겼을 때, selector가 없으면 관련된 view 코드 전부를 수정해야 한다. 그러므로 view component 수가 늘어나면 수정해야하는 코드 양도 같이 늘어난다. 이러한 문제를 피하기 위해서 우리는 selector를 통해 state에 접근한다. 이경우 state의 모양이 바뀌어도 selector만 수정하면 된다. view component는 여전히 데이터를 사용할 수 있을 것이고, 일일히 코드를 업데이트하지 않아도 된다. 이러한 이유 때문에 큰 규모의 Redux 애플리케이션은 index와 selector 패턴을 사용함으로써 큰 이득을 볼 수 있다.

<h2 id="second">2. view, edit state와 canonical state간의 분리</h2>

실제 Redux 애플리케이션은 REST API와 같은 다른 서비스에서 데이터를 가져와야 된다. 그리고 데이터를 받아오면 그 데이터를 payload로 action과 함께 dispatch하게 된다. 여기서 서비스를 통해 얻은 데이터를 "canonical state"라고 한다. 즉, canonical state 란 현재 데이터베이스에 저장된 정확한 상태를 지칭한다. 일반적으로 state는 이런 데이터 외에도 ui와 관련된 상태나 애플리케이션 전체의 상태를 포함한다. 우리는 API로부터 얻은 그대로의 canonical state를 다른 state와 함께 저장할 때가 있는데, 이런 방식은 매우 편리하지만 여러 source에서 여러 데이터를 fetch해야하는 경우 확장하기 어렵다.

대신 우리는 canonical state만을 담은 reducer를 만들었다. 이런 방식은 코드의 modularity를 높이고, 더 구조적인 코드를 만든다. 한 파일에 더 많은 코드를 집어넣는 방식은 여러 파일을 만드는 것보다 유지보수가 어렵다. Reducer를 쪼개면 재사용도 용이하게 된다.([section 3](#third)에서 더 알아보자) 추가로 이 방식은 canonical state가 아닌 상태를 canonical state와 함께 보관하지 않게끔 유도한다.

왜 canonical state를 따로 분리해야 할까? 1.과 같이 REST API로부터 받은 유저 리스트가 있다고 하자. index storage 패턴을 따라 아래와 같은 state를 가지게 될 것이다.

```js
{
  usersById: {
    123: {
      id: 123,
      name: "Jane Doe",
      email: "jdoe@example.com",
      phone: "555-555-5555",
      ...
    },
    ...
  }
}
```

이제 view에서 유저정보를 수정할 수 있게 한다고 해보자. 유저가 edit 아이콘을 클릭하면 우리는 수정 중인 상태가 반영되게끔 state를 구성해야 한다. 이번에는 canonical state와 view state를 분리하는 대신 edit state를 user object에 그냥 새로운 field로 집어넣었다.

```js
{
 usersById: {
    123: {
      id: 123,
      name: "Jane Doe",
      email: "jdoe@example.com",
      phone: "555-555-5555",
      ...
      isEditing: true,
    },
    ...
  }
}
```

유저는 몇 가지 정보를 수정한 뒤 제출 버튼을 누르고 변화는 수정(PUT)되어 REST 서비스에 반영될 것이다. 서비스는 user의 새로운 state를 돌려준다. 이때 우리는 store에 어떻게 새로운 canonical state를 반영할 수 있을까? 만약 단순하게 새로운 user object로 이전의 user object를 덮어쓴다면 isEditing field가 누락될 것이다. 이제 우리는 단순히 object를 덮어쓰는 대신 수동으로 특정 field를 배제해야만 한다. 이 경우 상태 변화 로직이 복잡해진다. 아마 canonical state에는 몇 가지 boolean, string, array 혹은 다른 종류의 field가 생겨날 것이다. 그렇게 되면 canonical state를 수정하는 action을 추가하는 건 쉽지만 object의 UI field를 덮어써버린다든가하여 잘못된 상태를 만들 수 있다. 대신 canonical state는 다른 reducer에 보관해야한다. 그렇게 해야 action을 단순하게 유지할 수 있고, 변화의 추적이 쉬워진다.

edit state를 분리하는 방식의 또다른 이점은 유저가 행동을 취소할 때, 쉽게 canonical state로 돌아갈 수 있다는 점이다. 유저가 수정버튼을 누르고 이름과 이메일 주소를 입력한 뒤에 다시 되돌리기 위해 취소버튼을 눌렀다고 가정하자. 이 경우 view는 취소 전의 상태로 돌아가야 한다. 이 때 canonical state를 단순히 덮어쓰는 접근법을 사용한다면 old state로 되돌릴 방법이 없으므로 다시 REST API로부터 데이터를 얻어와야한다. 대신 editing state를 다른 곳에 보관하자. 이제 state는 다음과 같은 모습을 띌 것이다. 

```js
{
 usersById: {
    123: {
      id: 123,
      name: "Jane Doe",
      email: "jdoe@example.com",
      phone: "555-555-5555",
      ...
    },
    ...
  },
  editingUsersById: {
    123: {
      id: 123,
      name: "Jane Smith",
      email: "jsmith@example.com",
      phone: "555-555-5555",
    }
  }
```

이제 canonical state 와 복사본(editingUsersById)을 가지고 있으므로, 유저의 취소를 쉽게 반영할 수 있다. 즉, 이경우 canonical state만 view에 돌려주면 되고, REST API를 호출할 필요는 없다. 반대로 edit state를 적용할 때는 editing state만 view에 돌려주면 된다. 종합하면 edit, view 그리고 canonical state를 서로 분리하는 방식은 코드를 조직하고 유지보수가 용이하게 하여 좋은 개발자 경험을 제공할 뿐만 아니라 form을 사용하는 사용자 경험도 향상시킨다.

<n2 id="third">3. view 간에 state를 공유할 때는 신중하게.</h2>

많은 애플리케이션이 하나의 store와 ui를 가지고 시작한다. 그리고 새로운 기능 추가 요구를 감당하기 위해 애플리케이션의 규모를 늘러갈 때 점차 서로 다른 view와 store를 관리할 필요성이 생긴다. 이 때 페이지마다 top level reducer를 두는 게 도움이 될 수 있다. 한 페이지와 그에 상응하는 top level reducer는 애플리케이션의 하나의 view를 담당하게 된다. 예를 들어 users 페이지는 API로부터 받은 데이터를 users reducer에 저장할 것이고, 현재 유저의 도메인을 추적하는 다른 페이지는 다른 도메인 API로부터 데이터를 받아 저장할 것이다. 상태는 아래와 같은 것이다.

```js
{
  usersPage: {
    usersById: {...},
    ...
  },
  domainsPage: {
    domainsById: {...},
    ...
  }
}
```

페이지를 이런 식으로 조직하면 view 와 관련된 데이터를 분리하여 관리할 수 있다. 이 때 각 페이지는 필요한 state만 관리하고, reducer 파일은 심지어 view 파일과 같은 위치에 둘 수도 있다. 우리가 계속해서 애플리케이션의 규모를 늘려갈 때 같은 데이터에 의존하는 두 가지 view 간에 state를 공유해야할 필요를 느낄 수도 있다. state를 공유하는 reducer를 만들기 전에 아래의 세 가지를 생각해보자.

- 이 데이터에 얼마나 많은 view와 reducer가 의존하는가?
- 각각의 페이지가 이 데이터의 고유한 복사본을 필요로 하는가?
- 얼마나 데이터가 자주 바뀌는가?

예를 들어 애플리케이션이 모든 페이지마다 로그인된 유저의 현재 정보를 보여줘야한다고 가정해보자. 그 정보는 API로부터 reducer에 저장될 것이다. 그리고 모든 페이지가 이 데이터에 의존성을 가지므로, 이 경우 하나의 페이지에 하나의 리듀서 전략은 통하지 않는다. 또 각 페이지는 다른 유저를 fetching 하거나 현재의 유저 정보를 수정하지도 않으므로 각자 고유한 복사본을 필요로 하지도 않는다. 또 현재 로그인한 유저의 정보는 users 페이지에서 수정하지 않는한 바뀔 일도 없다.

이 경우 reducer를 공유하는 전략은 좋은 전략이다. 이제 유저가 방문하는 첫 페이지에서 current user reducer가 load됐는지 확인할 것이고, 만약 정보가 없다면 API로부터 fetch할 것이다. 이제 redux store에 연결된 모든 페이지에서 현재 로그인된 유저의 정보를 얻을 수 있다.

state를 공유하는 것이 부적절한 경우를 살펴보자. 이번에는 유저에게 도메인뿐만 아니라 서브 도메인도 있다고 해보자. 이 경우 우리는 애플리케이션에 서브 도메인 페이지를 추가하여 유저의 모든 서브 도메인을 보여줄 것이다. 그리고 도메인 페이지는 선택된 도메인에 대해 모든 서브 도메인을 보여줄 수 있는 옵션이 있다. 우리는 도메인이 꽤 자주 바뀐다는 것을 알고 있다(도메인이나 서브 도메인은 언제든 추가 수정 삭제될 수 있으므로). 이제 각 페이지는 고유한 데이터 사본을 필요로 할 것이다. 서브 도메인 페이지는 서브 도메인 API에 읽고 쓰는 요청을 보낼 수 있고 여러 페이지의 데이터를 다룰 수도 있다. 반면 도메인 페이지는 한 번에 일부 서브 도메인 데이터만 필요로 한다(선택된 도메인에 대한 서브 도메인 정보). 그러므로 이 경우 서브 도메인 페이지와 도메인 페이지 간에 서브 도메인 정보를 공유하는 것은 적절하지 않음을 알 수 있다. 각 페이지는 고유한 서브 도메인 데이터를 가져야 한다.

<h2 id="fourth">4. 공통된 reducer 재사용하기</h2>

몇 번 reducer를 작성해보면 state간 reducer의 재사용을 생각할 수 있다. 예를 들어서 API로부터 user를 fetch하는 reducer를 생각해보자. API는 한 번에 100명의 정보만 제공하고 우리의 데이터베이스에는 수 천명의 유저가 있을 수 있다. 이 경우 현재 보여지는 users 데이터의 페이지가 몇 번째인지 알고 있어야 한다. 우리의 fetch logic은 다음 API 요청에 쓰일 pagination parameter(page_number와 같은)를 얻기 위해 reducer를 찾을 수도 있다. 이후에 도메인 리스트를 가져와야할 때 이를 fetch하는 reducer의 logic은 유저 리스트를 fetching하는 logic과 별반 다르지 않을 것이다. 이 경우 pagination은 공통된 기능이다. 노련한 개발자라면 pagination reducer를 만들어서 서로 다른 reducer에 재사용함으로써 modularity를 높일 것이다. 

reducer의 logic을 공유하는 건 redux에서는 조금 힘든 일이다. 기본적으로, 모든 reducer는 새로운 action이 dispatch될 때 호출된다. 만약 reducer를 여러 다른 reducer 간에 공유하면 모든 reducer가 같은 action에 반응할 수 있다. 물론 이는 바람직하지 않은 일이다.

우리는 reducer 공유를 위한 두 가지 방법을 제시한다. 첫번째 방법은 action의 payload에 scope를 넘겨주는 것이다. action은 state에서 update해야할 key를 type을 통해 유추한다. 이제 여러 페이지가 비동기적으로 다른 API endpoint로부터 load된다고 생각해보자. 우리가 추적해야될 state는 다음과 같다.

```js
  const initialLoadingState = {
    usersLoading: false,
    domainsLoading: false,
    subDomainsLoading: false,
    settingsLoading: false,
  };
```

state가 위와 같다면 일반적으로 각 페이지의 로딩 상태를 보여주기 위해서는 여러 reducer와 action이 필요하다. 가령 4개의 페이지 reducer에 4개의 action 말이다. 이제 많은 코드가 반복 사용되고 있다. 대신 scoped reducer와 action을 사용해보자. 우리는 SET_LOADING 이라는 하나의 타입만 가지고 있고, 이 action에 대한 reducer는 다음과 같다.

```js
  const loadingReducer = (state = initialLoadingState, action) => {
    const { type, payload } = action;
    if (type === SET_LOADING) {
      return Object.assign({}, state, {
        // sets the loading boolean at this scope
        [`${payload.scope}Loading`]: payload.loading,
      });
    } else {
      return state;
    }
  }
```

이제 scoped action creator가 필요할 것이다. 그 모습은 아래와 같다.

```js
  const setLoading = (scope, loading) => {
    return {
      type: SET_LOADING,
      payload: {
        scope,
        loading,
      },
    };
  }
  // example dispatch call
  store.dispatch(setLoading('users', true));
```

이런 식으로 scoped reducer를 사용하면 여러 reducer와 action 파일에서 같은 logic을 반복할 필요가 없게 된다. 이제 반복된 코드의 양을 줄이고, 더 적은 양의 reducer/action 파일만 남게 된다. 다른 section의 로딩 상태를 추가하는 일도 어렵지 않다.

그래도 가끔 reducer logic을 여러 군데에서 재사용해야할 때가 잇다. 하나의 reducer와 action에서 state의 여러 field를 설정하는 대신 우리는 state의 여러 부분에 쓰일 수 있는 "재사용 가능한" reducer가 필요하다. 이 리듀서는 리듀서 팩토리에 의해 적절한 prefix가 더해져서 return 될 것이다.

이런 상황에 대한 좋은 예는 pagination 정보가 필요할 때다. 이전의 유저를 fetching 하는 에를 살펴보면 API는 수 천명 이상의 유저와 여러 페이지의 유저 간에 paginate 하기 위한 정보를 가지고 있을 수 있다. 이 경우 API response는 다음과 같은 모습을 띌 것이다. 

```js
  {
    "users": ...,
    "count": 2500, // the total count of users in the API
    "pageSize": 100, // the number of users returned in one page of data
    "startElement": 0, // the index of the first user in this response
  }
```

만약 다음 페이지의 데이터가 필요하다면 아마 startElement=100인 GET 요청을 보낼 것이다. 단순히 각 API 서비스를 위한 paginate reducer를 만들 수도 있지만 logic을 여러 번 반복하게 될 것이다. 대신 우리는 전체 pagination을 담당하는 reducer를 만들 것이다. 이 reducer는 리듀서 팩토리를 통해 prefix type이 추가된 새로운 reducer를 return할 것이다.

```js
  const initialPaginationState = {
    startElement: 0,
    pageSize: 100,
    count: 0,
  };
  const paginationReducerFor = (prefix) => {
    const paginationReducer = (state = initialPaginationState, action) => {
      const { type, payload } = action;
      switch (type) {
        case prefix + types.SET_PAGINATION:
          const {
            startElement,
            pageSize,
            count,
          } = payload;
          return Object.assign({}, state, {
            startElement,
            pageSize,
            count,
          });
        default:
          return state;
      }
    };
    return paginationReducer;
  };
  // example usages
  const usersReducer = combineReducers({
    usersData: usersDataReducer,
    paginationData: paginationReducerFor('USERS_'),
  });
```

이제 USERS_SET_PAGINATION 같은 action을 dispatch 하면 users의 pagination reducer만 반응할 것이다. 이제 공통된 reducer를 store의 여러 장소에서 사용할 수 있게 됐다. 글의 완전함을 위해 여기 action creator factory 코드도 있다.

```js
  const setPaginationFor = (prefix) => { 
    const setPagination = (response) => {
      const {
        startElement,
        pageSize,
        count,
      } = response;
      return {
        type: prefix + types.SET_PAGINATION,
        payload: {
          startElement,
          pageSize,
          count,
        },
      };
    };
    return setPagination;
  };
  // example usages
  const setUsersPagination = setPaginationFor('USERS_');
```

<h2 id="fifth">5. React 와의 사용, 그리고 마무리</h2>

어떤 애플리케이션은 유저에게 view를 보여줄 일이 없지만 대부분은 어떤 식으로든 데이터를 보여줘야할 것이다. Redux와 함께 ui를 rendering 하는 가장 인기있는 라이브러리는 react이고, 아래 예시에서도 react를 사용할 것이다. 예시에서는 redux를 react와 함께 사용하기 위해 [react-redux](https://github.com/reactjs/react-redux) 라이브러리를 쓸 것이다.

selector 패턴은 UI component에서 state를 가져올 때 굉장히 유용하다. 그리고 react-redux의 connect에서 selector를 사용하기 좋은 곳은 mapStateToProps 함수 내부다. mapStateToProps 함수는 state에 담긴 데이터를 넘겨받아서 component에 prop으로 돌려주는데, selector를 사용하면 굉장히 편리하다. 다음은 그 예시이다.

```js
  const ConnectedComponent = connect(
    (state) => {
      return {
        users: selectors.getCurrentUsers(state),
        editingUser: selectors.getEditingUser(state),
        ... // other props from state go here
      };
    }),
    mapDispatchToProps // another `connect` function
  )(UsersComponent);
```

react-redux는 우리의 scoped action이나 prefix types 또한 쉽게 관리할 수 있게 해준다. 이제 mapDispatchToProps 함수를 사용해보자. 일반적으로 mapDispatchToProps 함수가 있는 장소는 Redux의 bindActionCreators 함수를 사용하여 store의 각 action을 bind하는 곳이다. 물론 우리는 우리 방식대로 action들을 bind할 수 있다. 다음은 user page의 pagination action을 bind한 예시이다. 

```js
  const ConnectedComponent = connect(
    mapStateToProps,
    (dispatch) => {
      const actions = {
        ...actionCreators, // other normal actions
        setPagination: actionCreatorFactories.setPaginationFor('USERS_'),
      };
      return bindActionCreators(actions, dispatch);
    }
  )(UsersComponent);
```

이제 userspage component는 user list와 bound action creators 그리고 다른 부분적인 state를 prop으로 받을 것이다. 컴포넌트는 state에 대해 접근하는 방식이나 어떤 action을 dispatch해야하는지 일일히 신경쓸 필요가 없다. 대신 react-redux의 mapStateToProps와 mapDispatchToProps가 이런 일을 대신한다. 이런 방식으로 state의 내부 구조와 고도로 분리된 component를 만들 수 있다. 이 다섯 가지 패턴을 사용함으로써 확장 가능하고, 유지보수가 쉬우며, 언제나 변화가 추적가능한 Redux 애플리케이션을 만들 수 있기를 바란다.

더 읽어보기 부분은 생략됐습니다.

글쓴이: Chris Dopuch

글을 잘 읽으셨다면 원 저자에게도 clap 부탁드립니다 ~

<h2 id="sixth">마치며</h2>

위의 패턴은 소위 말하는 꿀(?) 패턴입니다. index나 selector는 말할 것도 없고, 공통된 reducer를 재사용하면 코드 양을 획기적으로 줄일 수 있죠.
한 가지 아쉬웠던 점은 API response가 깊게 nested된 json을 돌려주거나 혹은 selector를 사용할 때보다 더 큰 성능향상을 원할 때 어떤 라이브러리를 선택할 수 있는지 알려주지 않았다는 점입니다. 또 state 업데이트에 spread syntax(...)를 사용하는 것보다 편한 Immutable도 말이죠.

저자의 글에 덧붙여 아래의 세 가지 라이브러리를 소개합니다.

첫번째는 2만 star에 빛나는 페이스북의 [Immutable](https://facebook.github.io/immutable-js/docs/#/) 입니다.

Immutable은 객체의 프로퍼티를 직접 수정할 수 없게만들어 안정성을 도모하고, update를 위한 편리한 syntax를 제공합니다.
또 기존 js에서 array에만 사용할 수 있었던 filter, map과 같은 함수를 object(Map)에도 사용할 수 있게 됩니다.
여러 편리한 method들은 덤이죠.

두번째는 [normalizr](https://github.com/paularmstrong/normalizr) 입니다.

normalizr github 문서의 Motivation 부분을 읽어보겠습니다.

```
  Many APIs, public or not, return JSON data that has deeply nested objects. Using data in this kind of structure is often very difficult for JavaScript applications, especially those using Flux or Redux.

  많은 API는 깊게 nested 된 JSON 데이터를 돌려준다. 이런 형태를 데이터를 사용하는 일은 자바스크립트 애플리케이션에서 어려운 부분이고 특히 Flux 구조나 Redux를 사용하고 있다면 더욱 그렇다.
```

네, 그렇습니다. 이 라이브러리는 Redux를 위해 만들어진 라이브러리인 것입니다. Quick Start에 나온 간단한 사용 예시를 살펴볼까요?

```js
  {
    "id": "123",
    "author": {
      "id": "1",
      "name": "Paul"
    },
    "title": "My awesome blog post",
    "comments": [
      {
        "id": "324",
        "commenter": {
          "id": "2",
          "name": "Nicole"
        }
      }
    ]
  }
```

일반적으로 볼 수 있는 블로그 article JSON 데이터입니다.

여기서 normalizr는 id를 혹은 id와 같은 역할을 하는 구분자를 가진 객체 하나마다 entity로 인식합니다. 즉, 여기서 entity는 author, article, comment의 세 개가 되는 것이지요.

아래의 normalizr 코드를 한 번 봅시다.

```js
  import { normalize, schema } from 'normalizr';

  // Define a users schema
  const user = new schema.Entity('users');

  // Define your comments schema
  const comment = new schema.Entity('comments', {
    commenter: user
  });

  // Define your article 
  const article = new schema.Entity('articles', { 
    author: user,
    comments: [ comment ]
  });

  const normalizedData = normalize(originalData, article);
```

우리가 앞서 말한대로 세 개의 entity를 설정하고 있죠.

자 이제 마법이 일어날 차례입니다.

```js
  {
    result: "123",
    entities: {
      "articles": { 
        "123": { 
          id: "123",
          author: "1",
          title: "My awesome blog post",
          comments: [ "324" ]
        }
      },
      "users": {
        "1": { "id": "1", "name": "Paul" },
        "2": { "id": "2", "name": "Nicole" }
      },
      "comments": {
        "324": { id: "324", "commenter": "2" }
      }
    }
  }
```

nested JSON 데이터가 갈끔하게 변했습니다.

이제 1.에서 얘기했던 index/selector 패턴을 더 쉽게 사용할 수 있겠죠?

추가) react를 사용하는 많은 분들이 nodejs/mongodb의 조합을 사용하실 겁니다.

mongodb의 경우 id로 _id를 사용하는데 만약 _id가 사용된 객체를 그냥 normalizr를 사용해버리면 key에 undefined가 저장됩니다.

이 때는 idAttribute로 _id를 넘겨줘야 합니다. [참고](https://github.com/paularmstrong/normalizr/blob/master/docs/api.md#instance-attributes)

마지막은 [Reselect](https://github.com/reactjs/reselect)입니다.

Reselect는 자신들의 장점이 세 가지라고 말하는군요.

- Selectors can compute derived data, allowing Redux to store the minimal possible state.
- Selectors are efficient. A selector is not recomputed unless one of its arguments changes.
- Selectors are composable. They can be used as input to other selectors.

천천히 읽어볼까요?

첫번째: selector는 derived data를 계산할 수 있다. 그러므로 redux는 최소한의 상태만 저장해도 된다.
두번째: selector는 효율적이다. selector는 내부의 인자가 바뀌지 않는 한 새로 계산되지 않는다.
세번째: selector는 조합이 가능하다. selector를 다른 selector의 input으로 사용하는 것이 가능하다.

공식문서에 좋은 예시가 있네요.

```js
  import { createSelector } from 'reselect'

  const shopItemsSelector = state => state.shop.items
  const taxPercentSelector = state => state.shop.taxPercent

  /*
   * shopItem과 taxPercentage 데이터만 있으면 아래의 데이터를 계산할 수 있다.
   * 즉 따로 독립된 상태로 유지할 필요가 없다.(compute derived data)
   * 그리고 각 item의 taxPercent가 변하지 않는 이상 다시 계산하지 않는다.(efficient)
   * 또 tax나 total selector를 보면 selector를 input으로 받아서 새로운 state를 계산해내는 걸 알 수 있다.(composable)
   */
  const subtotalSelector = createSelector(
    shopItemsSelector,
    items => items.reduce((acc, item) => acc + item.value, 0)
  )

  const taxSelector = createSelector(
    subtotalSelector,
    taxPercentSelector,
    (subtotal, taxPercent) => subtotal * (taxPercent / 100)
  )

  export const totalSelector = createSelector(
    subtotalSelector,
    taxSelector,
    (subtotal, tax) => ({ total: subtotal + tax })
  )

  let exampleState = {
    shop: {
      taxPercent: 8,
      items: [
        { name: 'apple', value: 1.20 },
        { name: 'orange', value: 0.95 },
      ]
    }
  }

  console.log(subtotalSelector(exampleState)) // 2.15
  console.log(taxSelector(exampleState))      // 0.172
  console.log(totalSelector(exampleState))    // { total: 2.322 }
```

위의 세 가지 라이브러리는 본글의 다섯 가지 팁과 더불어 더욱 윤택한 redux 개발을 가능하게 할 것입니다.

긴 글 읽어주셔서 감사합니다!
