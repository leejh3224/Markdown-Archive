# Promise, Generator and Async/Await

## Promise

es2015 이전에 js 에서 비동기적으로 함수를 처리하는 방법은 함수 callback 을 사용하는 것이었다.

callback 함수는 특정 함수가 실행을 마치면 실행되는 함수로 주로 비동기함수에 두 번째 인자로 주어졌다.

그러나 callback 함수는 순차적인 처리는 가능하되 병렬 처리를 할 수 없다는 한계와 또 여러 번 실행되는 callback 함수를 작성할 경우 콜백지옥 (callback hell) 코드가 만들어진다는 단점 그리고 각 callback 함수를 실행할 때마다 모든 경우에 에러 처리 코드를 추가하여 코드가 장황해진다는 단점이 있었다.

이에 es2015 에서는 Promise 라는 개념을 도입하여 비동기 함수 처리를 "composable" 하고 "readable" 하게 또 병렬 처리가 가능하도록 바꾸었다.

Promise 란 Promise 객체를 반환하는 함수의 형태로 작성할 수 있다. Promise 의 장점은 언제든 .then 을 통해 연결할 수 있으며, .all 메쏘드를 통해 병렬 처리가 가능하고, .catch 메쏘드로 몇 개의 Promise 를 실행하든 단 한 곳에서 에러 처리를 할 수 있다는 데 있다.

그렇다면 Promise 는 어떤 식으로 구현되는 것일까?

먼저 Promise 의 동작 원리를 살펴보자. Promise 는 구현 방식이 라이브러리마다 조금씩 다를 수 있지만 기본적으로 "유한 상태 머신" 개념을 통해 이해할 수 있다.

즉, 내부적으로 어떤 비동기 동작이 처리되었는지 여부를 저장한 현재 상태를 가지고 있고, 어떠한 사건 즉 비동기 동작의 성공 혹은 실패 여부에 따라 현재 상태는 다른 상태로 전이될 수 있다. 또 성공 혹은 실패 시 반환하는 값 혹은 에러 또한 머신 내부에 저장된다.

그러므로 가장 간단한 형태의 Promise 는 아래와 같은 형태로 작성할 수 있다.

```js
/* State Machine */
var PENDING = 0
var FULFILLED = 1
var REJECTED = 2

function Promise() {
  // PENDING, FULFILLED 혹은 REJECTED 중 하나에 속하는 상태를 저장한다.
  var state = PENDING

  // 동작이 FULFILLED 상태라면 값을, REJECTED 상태라면 error를 저장한다.
  var value = null

  // .then 혹은 .done 메써드를 통해 추가되는 성공, 실패 함수 핸들러를 저장
  var handlers = []
}
```

이제 Promise 를 정의하는 핵심 전이 두 가지, fulfilling 과 rejecting 을 정의해보자.

```js
/* Transitions */
var PENDING = 0
var FULFILLED = 1
var REJECTED = 2

function Promise() {
  // PENDING, FULFILLED 혹은 REJECTED 중 하나에 속하는 상태를 저장한다.
  var state = PENDING

  // 동작이 FULFILLED 상태라면 값을, REJECTED 상태라면 error를 저장한다.
  var value = null

  // .then 혹은 .done 메써드를 통해 추가되는 성공, 실패 함수 핸들러를 저장
  var handlers = []

  // 각 전이는 두 가지 상태를 변화시킨다.
  function fulfill(result) {
    state = FULFILLED
    value = result
  }

  function reject(error) {
    state = REJECTED
    value = error
  }
}
```

이 두 가지 전이 말고도 더 높은 레벨의 resolve 라는 전이를 정의할 수 있다.

```js
var PENDING = 0
var FULFILLED = 1
var REJECTED = 2

function Promise() {
  // PENDING, FULFILLED 혹은 REJECTED 중 하나에 속하는 상태를 저장한다.
  var state = PENDING

  // 동작이 FULFILLED 상태라면 값을, REJECTED 상태라면 error를 저장한다.
  var value = null

  // .then 혹은 .done 메써드를 통해 추가되는 성공, 실패 함수 핸들러를 저장
  var handlers = []

  // 각 전이는 두 가지 상태를 변화시킨다.
  function fulfill(result) {
    state = FULFILLED
    value = result
  }

  function reject(error) {
    state = REJECTED
    value = error
  }

  // fulfill 하거나 reject 하는 더 높은 레벨의 전이 - resolve
  function resolve(result) {
    try {
      var then = getThen(result)
      if (then) {
        doResolve(then.bind(result), resolve, reject)
        return
      }
      fulfill(result)
    } catch (e) {
      reject(e)
    }
  }
}
```

주목할 부분은 Promise 가 다른 promise 혹은 일반적인 값 모두를 받아들인다는 점인데, 만약 promise 를 받아들이면 promise 가 완료될 때까지 기다린다.
promise 는 절대 다른 promise 를 통해 fulfill 될 수 없으며, 내부의 fulfill 함수보다는 resolve 함수를 통해 fulfill 전이를 일으킨다.
몇 가지 helper 메써드를 마저 정의해보자:

```js
/*
 * 만약 value가 Promise라면 then 메써드를 반환
 */
function getThen(value) {
  var t = typeof value

  if (value && (t === 'object' || t === 'function')) {
    var then = value.then
    if (typeof then === 'function') {
      return then
    }
  }
  return null
}

/* onFulfilled 함수와 onRejected 함수가 한 번만 실행되도록 보장 */
function doResolve(fn, onFulfilled, onRejected) {
  var done = false
  try {
    fn(
      function(value) {
        if (done) return
        done = true
        onFulfilled(value)
      },
      function(reason) {
        if (done) return
        done = true
        onRejected(reason)
      },
    )
  } catch (e) {
    if (done) return
    done = true
    onRejected(e)
  }
}
```

```js
var PENDING = 0
var FULFILLED = 1
var REJECTED = 2

function Promise(fn) {
  // PENDING, FULFILLED 혹은 REJECTED 중 하나에 속하는 상태를 저장한다.
  var state = PENDING

  // 동작이 FULFILLED 상태라면 값을, REJECTED 상태라면 error를 저장한다.
  var value = null

  // .then 혹은 .done 메써드를 통해 추가되는 성공, 실패 함수 핸들러를 저장
  var handlers = []

  // 각 전이는 두 가지 상태를 변화시킨다.
  function fulfill(result) {
    state = FULFILLED
    value = result
  }

  function reject(error) {
    state = REJECTED
    value = error
  }

  // fulfill 하거나 reject 하는 더 높은 레벨의 전이 - resolve
  function resolve(result) {
    try {
      var then = getThen(result)
      if (then) {
        doResolve(then.bind(result), resolve, reject)
        return
      }
      fulfill(result)
    } catch (e) {
      reject(e)
    }
  }

  doResolve(fn, resolve, reject)
}
```

코드를 보면 doResolve 함수를 다시 사용하는데 이는 다른 resolver 를 신뢰할 수 없기 때문이다.
인자로 받는 fn 은 resolve 와 reject 를 여러 번 호출할 수 있고, 예외도 만들 수 있다.
Promise 가 단 한 번만 resolved 되거나 rejected 되고, 그에 따라 다른 상태로의 전이가 여러 번 일어나지 않도록 보장하는 것은 우리의 몫이다.

다음 단계는 상태 머신의 외부에서 값을 관찰할 수 있도록 .then 메써드를 구현하는 일이다.
그 전에 .done 을 구현하는 일이 더 간단할 수 있으므로 거기서 시작하겠다.

.done 메써드의 목표는

- onFulfilled 나 onRejected 중 하나만 실행되도록 보장
- .done 메써드는 한 번만 실행된
- 함수의 다음 tick 까지 호출되지 않음
- .done 메써드 실행 이전 혹은 이후에 Promise 가 resolve 된 지에 관계없이 호출됨

```js
/* Observing (via .done) */
var PENDING = 0
var FULFILLED = 1
var REJECTED = 2

function Promise(fn) {
  // PENDING, FULFILLED 혹은 REJECTED 중 하나에 속하는 상태를 저장한다.
  var state = PENDING

  // 동작이 FULFILLED 상태라면 값을, REJECTED 상태라면 error를 저장한다.
  var value = null

  // .then 혹은 .done 메써드를 통해 추가되는 성공, 실패 함수 핸들러를 저장
  var handlers = []

  // 각 전이는 두 가지 상태를 변화시킨다.
  function fulfill(result) {
    state = FULFILLED
    value = result
  }

  function reject(error) {
    state = REJECTED
    value = error
  }

  // fulfill 하거나 reject 하는 더 높은 레벨의 전이 - resolve
  function resolve(result) {
    try {
      var then = getThen(result)
      if (then) {
        doResolve(then.bind(result), resolve, reject)
        return
      }
      fulfill(result)
    } catch (e) {
      reject(e)
    }
  }

  function handle(handler) {
    // 전달받은 핸들러를 머신의 내부 상태에 따라 처리
    if (state === PENDING) {
      handlers.push(handler)
    } else {
      if (state === FULFILLED && typeof handler.onFulfilled === 'function') {
        handler.onFulfilled(value)
      }
      if (state === REJECTED && typeof handler.onRejected === 'function') {
        handler.onRejected(value)
      }
    }
  }

  this.done = function(onFulfilled, onRejected) {
    // 비동기적으로 실행됨을 보장
    // handle 함수로 onFulfilled 함수와 onRejected 함수를 전달
    setTimeout(function() {
      handle({
        onFulfilled: onFulfilled,
        onRejected: onRejected,
      })
    }, 0)
  }

  doResolve(fn, resolve, reject)
}
```

.then 메써드는 .done 과 같은 일을 하지만 Promise 를 반환한다.

```js
/* Observing (via .then) */
this.then = function(onFulfilled, onRejected) {
  var self = this
  return new Promise(function(resolve, reject) {
    return self.done(
      function(result) {
        if (typeof onFulfilled === 'function') {
          try {
            return resolve(onFulfilled(result))
          } catch (ex) {
            return reject(ex)
          }
        } else {
          return resolve(result)
        }
      },
      function(error) {
        if (typeof onRejected === 'function') {
          try {
            return resolve(onRejected(error))
          } catch (ex) {
            return reject(ex)
          }
        } else {
          return reject(error)
        }
      },
    )
  })
}
```

## Generator and Async/Await

Generator 함수

Generator 는 여러 개의 값을 return 할 수 있는 함수로 yield 키워드를 통해 중간 값을 리턴한다.

gen().next() 를 통해 다음 값을 확인할 수 있으며, 내부적으로 상태와 yield 할 값이 남아있는지를 알려주는 상태인 done 을 저장한다.

```js
function fakeGen() {
  const values = [5,6,11]
  let currentIndex = 0

  return {
    next: function() {
      const iteration = {
        value: values[currentIndex],
        done: currentIndex === values.length - 1
      }

      currentIndex++
      return iteration
    }
  }
}

const it = fakeGen()

it.next() { value:5, done: false }
it.next() { value:6, done: false }
...
```

즉 프로미스처럼 제너레이터도 상태 머신의 역할을 수행한다.

Async/Await

async/await 은 generator 와 promise 를 간편하게 사용하기 위한 syntatic sugar 라고 할 수 있다.

정리:

1.  generator 는 next 메써드를 통해 중간 리턴 값을 전달받는다.
2.  promise 와 generator 를 결합하면 yield 를 통해 "프로미스" 값을 전달받는다.
3.  .next()를 통해 전달받은 promise 를 .then 이나 .resolve 를 통해 풀어줘야한다.
4.  async/await 은 이 과정을 단순화하여 promise 값을 그대로 전달받는 것이 아니라 풀어진 값, 즉 resolve 된 값을 바로 전달받을 수 있다.

그러므로 generator + promise 를 단순화한 것이 async/await 이라 할 수 있다.

아래는 상세한 설명

[async/await vs combining generators and promises?](https://stackoverflow.com/questions/46043201/async-await-vs-combining-generators-and-promises)

Yes, you're right. An example with a generator and promises first:

```js
function* gen() {
  const promiseValue = yield new Promise(resolve => resolve(42))
  console.log(promiseValue)
}

// .. and at some other context using 'gen*'

const iterator = gen()
const promise = iterator.next() // Promise (42)

promise.then(resolvedValue => iterator.next(resolvedValue)) // logs 42
```

This generator yields a Promise to the outside world, one whose value we pass back into the generator by passing it as an argument to the iterator.next call, once that promise resolves.

This pattern at least intersects with what is said to be a task. The disadvantage of this is that we DO have to call next manually on the iterator, every time a yielded promise resolves. That's where async await comes in:

```js
async function task() {
  const promiseValue = await new Promise(resolve => resolve(42))
  console.log(promiseValue)
}
```

And that's all there is to it. An async function will pause until a promise expression preceded by the await keyword resolves, and the expression will evaluate to an automatic unwrap of the promise - i.e. its final value.

Then the await expression's result (42, in our case) will be assigned to promiseValue, like yield works as well. This all happens before the function continues its execution to the next statement.

This is essentially equal behaviour as the above snippet with the generator.

Although in my experience this example seems to be the most common use-case for both generators and async functions, one where async/await is definitely the cleaner approach, generators do have some really strong powers that async/await does not have in the same way (but that is another topic).

## 유한 상태 기계

Promise 와 Generator 를 이해하는 중심 개념 중 하나는 유한 상태 기계이다.

상태 기계는 상태값들과 전이를 유발하는 사건으로 구성된다.

즉 Promise 를 예로 들어 설명하면, 어떤 비동기 동작의 진행 상태를 설명하는 값 (e.g. PENDING/FULFILLED)
과 그 비동기 액션의 결과값 (e.g. json/error) 을 머신 내부에 저장하고 추적하며, 전이를 유발하는 사건 (fulfill 과 reject)들을 통해 상태를 변화시킨다(전이한다).

상태 머신 내부의 상태를 돌려줄 때는 .then 메써드를 통해 새로운 Promise 로 감싼 뒤 돌려준다. 이렇게 함으로써 함수형 프로그래밍의 장점인 composability 를 살릴 수 있다.

즉, 쉽게 다른 .then 을 추가할 수 있을 뿐만 아니라 매 .then 메써드 호출 시마다 상태의 흐름을 쉽게 파악할 수 있다.

```txt
이러한 기계는 한 번에 오로지 하나의 상태만을 가지게 되며, 현재 상태(Current State)란 임의의 주어진 시간의 상태를 칭한다. 이러한 기계는 어떠한 사건(Event)에 의해 한 상태에서 다른 상태로 변화할 수 있으며, 이를 전이(Transition)이라 한다. 특정한 유한 오토마톤은 현재 상태로부터 가능한 전이 상태와, 이러한 전이를 유발하는 조건들의 집합으로서 정의된다.

출처: 위키백과
```
