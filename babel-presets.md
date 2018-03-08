# What are babel "plugins" and "presets"? (And how to use them)

바벨 plugin 과 preset 에 대하여

원저자: Anthoy Accomazzo

When working with React, developers will often come across various Babel configurations.

React 를 사용하다보면, 다양한 바벨 설정들과 마주치게 된다.

You may have seen lines like this in a package.json:

package.json 의 babel 설정이나

```json
// package.json
{
  "babel": {
    "presets": ["es2015", "stage-0"]
  }
}
```

Or maybe something like this in a .babelrc:

.babelrc 의 형태로 말이다.

```json
// .babelrc
{
  "plugins": ["transform-class-properties"]
}
```

In this post, we'll dig into what exactly `stage-x` is and why we configure Babel in this way in general.

이번 포스트에서는 stage-x 가 무엇을 의미하는지 그리고 일반적인 바벨 세팅에 대해 알아보겠습니다.

Specifically, we talk about Babel plugins and presets.

또 여러 plugin 과 preset 에 대해서도 알아보겠습니다.

We'll start with a quick intro to Babel in case you're only vaguely familiar.

먼저 바벨에 대해서 익숙하지 않은 분들을 위해 바벨이 무엇인지 간단하게 설명도록 하겠습니다.

## What is Babel?

바벨이란?

To understand why Babel exists, we need to dig into a little Javascript history.

바벨을 이해하려면 먼저 자바스크립트의 역사를 살펴봐야합니다.

ES5, ES6, ES7 and beyond

ES5, ES6, ES7, 그리고 자바스크립트의 미래

Javascript is the language of the web.

자바스크립트의 웹을 위한 언어입니다.

It runs on many different browsers, like Google Chrome, Firefox, Safari, Microsoft Edge, and Internet Explorer.

구글 크롬, 파이어폭스, 사파리, 마이크로소프트 에지 그리고 인터넷 등 수많은 브라우저 위에서 작동하죠.

Different browsers have different Javascript interpreters which execute Javascript code.

그리고 각 브라우저는 서로 다른 자바스크립트 인터프리터(역자주: 소스 코드를 바로 실행하는 컴퓨터 프로그램 또는 환경)를 사용합니다.

Its widespread adoption as the internet's client-side scripting language led to the formation of a standard body which mangages its specification.

인터넷의 주류 언어로서인 자바스크립트는 그 위상 덕에 자체적인 표준에 가지고 그에 따라 관리하게 되었습니다.

The specification is called ECMAScript or ES.

우리는 그 표준을 ECMAScript 혹은 줄여서 ES 라고 부릅니다.

The 5th edition of the specification is called ES5.

그 중 5 번째로 제정된 표준을 우리는 ES5 라고 부릅니다.

You can think of ES5 as a "version" of the Javascript programming language.

ES5 는 쉽게 말해 자바스크립트의 5 번째 "버전"입니다.

Finalized in 2009, ES5 was adopted by all major browsers withing a few years.

2009 년에 표준이 확립되어 이후 몇 년내로 모든 주요 브라우저가 지원하게 되었죠.

The 6th edition of Javascript is referred to as ES6.
자바스크립트의 6 번째 버전은 ES6 입니다.

Finalized in 2015, the latest versions of major browsers are still finishing adding support for ES6 as of 2017.

최신 버전의 주요 브라우저는 2017 년 현재(역자주: 2017 년 글) ES6 지원을 위한 작업을 계속해서 진행하고 있습니다.

ES6 is a significant update.

기억할 것은 ES6 는 포괄적인 업데이트를 포함하고 있으며,

It contains a whole host of new features for Javascript, almost two dozen in total.

새롭게 추가되는 기능의 숫자만 해도 무려 24 개 가까이 됩니다.

Javascript written in ES6 is tangibly different than Javascript written in ES5.

그러므로 ES6 문법이 사용된 자바스크립트와 ES5 문법이 사용된 자바스크립트 코드 간에는 확연한 차이가 있습니다.

ES7, a much smaller update that builds on ES6, was ratified in June 2016.

ES7 은 ES6 에 대한 약간의 수정본을 포함하며, 2016 년 6 월에 제정되었습니다.

As the future of Javascript, we want to write our code in ES6/ES7 today.

많은 개발자가 ES6/ES7 의 새로운 문법을 지금 당장 쓰고 싶어합니다.

But we also want our Javascript to run on older browsers until they fade out of widespread use.

동시에 코드가 오래된 브라우저 환경에서도 문제없이 작동하기를 바랍니다.

That's where Babel comes in.

이제 바벨의 차례입니다.

Babel is a Javascript transpiler.

바벨은 자바스크립트 트랜스파일러(역자주: 서로 다른 언어 간의 변환을 담당. 이 경우에는 ES6+의 자바스크립트를 ES5 의 자바스크립트로 변환함을 의미)입니다.

Babel enables us to write modern Javascript that will be "transpiled" to widely-supported ES5 javascript.

바벨을 사용하면 모던 자바스크립트 구문을 널리 지원되는 ES5 문법의 자바스크립트로 변환할 수 있습니다.

## Using Babel

바벨 사용하기

There are a few ways to use Babel in your projects.

바벨을 사용하는 방법은 한 가지가 아닙니다.

The simplest and fastest way is to use the package `babel-standalone`.

그리고 그 중에서 가장 간단한 방법은 babel-standalone 패키지를 사용하는 방법입니다.

You can include it in a `script` tag like this.

사용법은 무척 간단합니다.

아래와 같은 script 태그만 삽입해주면 됩니다.

```html
  <script
    src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/6.18.1/babel.min.js"
  ></script>
```

Now, Babel will automatically transpile any script tags with type `text/babel` or `text/jsx`.

이제 바벨은 type 이 text/babel 이거나 text/jsx 인 스크립트를 자동으로 변환해줍니다.

```html
  <div id="output"></div>

  <script type="text/babel">
  const getMessage = () => "Hello World"; // ES6!
  document.getElementById('output').innerHTML = getMessage();
  </script>
```

Projects like create-react-app automatically include and configure Babel for you, so you can use a sane Babel configuration out-of-the-box to write React components.

만약 React 를 사용하고 있다면, create-react-app 을 사용할 수 있습니다.

create-react-app 은 기본적인 바벨 세팅이 되어있어 별도의 설정 없이도 손쉽게 사용할 수 있는 장점이 있습니다.

## Babel plugins and presets

바벨 plugin 과 preset

In Babel, a preset is a set of plugins used to support particular language features.

바벨에서 preset 은 특정 기능을 지원하는 여러 plugin 의 모음입니다.

The two presets Babel uses by default:

es2015: Adds support for ES2015 (or ES6) Javascript
react: Adds support for JSX

바벨의 기본적인 preset 은 다음의 두 가지 입니다.

es2015/react

(역자주: 현재는 env 와 react 그리고 flow 로 바뀌었습니다.)

Remember: ES2015 is just another name used for ES6. We let Babel use the default es2015 preset for this project because we dont't need or use either of ES7's two new features.

주의: ES2015 는 ES6 의 다른 이름일 뿐입니다. 이 프로젝트에서는 ES7 의 새로운 두 가지 기능이 필요하지 않으므로 기본적인 세팅으로 진행하도록 하겠습니다.

Beyond ES7, proposed Javascript features can exist in various stages.

ES7 이외에도, 자바스크립트의 여러 기능에 대한 제안(proposal)은 다양한 stage 의 형태로 존재합니다.

A feature can be an experimental proposal, one that the community is still working out the details for ("stage1")

어떤 기능은 커뮤니티가 세부적인 사항을 만들어가는, 실험적인 단계일 수도 있습니다. (stage1)

Experimental proposals are at risk of being dropped or modified at any time.

이런 실험적인 제안은 언제든 수정되거나 폐기될 위험이 있습니다.

Or a feature might already be "ratified", which means it will be included in the next release of Javascript("stage4").

반대로 어떤 기능은 이미 그 구성이 확정되어 다음 버전의 자바스크립트에 포함될 예정인 경우가 있습니다.(stage4)

From the Babel docs:

바벨의 공식문서에 의하면

The TC39 categorizes proposals into the following stages:

stage-0 - Strawma: 구체적이지 않은 구상안, 바벨 plugin 이 될 수 있음.
stage-1 - Proposal: 받아들일 가치가 있는 제안
stage-2 - Draft: 초기 표준
stage-3 - Candidata: 표준의 정의를 마쳤으며 초기 형태의 브라우저 구현이 존재
stage-4 - Finished: 다음 표준에 포함될 예정

We can customize Babel with presets and/or plugins to take advantage of these upcoming or experimental features.

이런 preset 이나 plugin 을 추가함으로써 미래에 추가될 기능이나 실험적인 기능을 사용할 수 있습니다.

## Which plugins or presets are "safe" to use with React?

React 와 사용하기 안전한 plugin 이나 preset 에는 어떤 게 있습니까?

Babel warns that anything pre stage-3 should be used with caution.

바벨은 stage-3 이전 단계의 preset 을 사용할 때는 세심한 주의가 필요하다고 합니다.

In our book, we generally avoid features that are experimental.

저희 책(역자주: fullstack React 책)에서는 보통 실험적인 기능의 사용을 피합니다.

This is because we don't want to teach features that might be modified or dropped.

그 이유는 수정되거나 폐기될 기능을 독자에게 알리고 싶지 않기 때문입니다.

You might find that you and your team are attracted to some experimental Javascript features.

물론 당신이나 당신이 속한 팀이 몇몇 실험적인 기능을 선호하게 될 수도 있습니다.

Indeed, certain experimental features are popular among the React community.

실제로 몇몇 실험적인 기능은 React community 사이에서 인기가 높습니다.

The "property initializers" feaure is widely used because it makes declaring React ES6 class components "cleaner"

"property initializers"와 같은 기능(역자주: class 의 state 를 선언할 때 constructor 에 대한 선언없이 바로 state = {}의 형태로 선언하는 것)은 ES6 class component 구문을 더 깔끔하게 만들기 때문에 널리 사용되고 있습니다.

In deciding if you want to follow suit with certain bleeding-edge trends in your own projects, you might find comfort in tools like react-codemod.

만약 몇 가지 불안정한 최신 기술(bleeding-edge)을 프로젝트에서 사용하고 싶다면 react-codemon 같은 패키지를 사용할 수도 있습니다.

If, for instance, the property initializers proposal were to be dropped or modified, it is guarnteed that tooling would be available to automatically convert your codebase's React components to conform to the new spec.

react-codemon 은 property initializers 와 같은 제안이 폐기되거나 수정되면, 코드베이스의 component 가 새로운 스펙에 부합되게끔 자동으로 코드를 전환시켜줍니다.

Depending on which experimental featuers you decide to use, it's safe to say the risk of your codebase becoming "outdated" due to a dropped experimental feature is mitigated.

따라서 몇몇 실험적인 기능을 사용하더라도 코드베이스가 outdated 되는 위험을 피할 수 있습니다.

What's more important is ensuring everyone on your team is using the same style and Javascript feature set and composing React components in a way that new developers can quickly pick up.

더 중요한 사실은 팀원 모두가 같은 모습과 기능의 자바스크립트를 사용하므로 이로 인해 새로운 개발자가 빠르게 이해하고 따라갈 수 있다는 점입니다.

## How to use Babel plugins and presets

바벨 plugin 과 preset 을 사용하는 방법

There are two primary ways to configure Babel. The first is in package.json. You can list plugins and presets like this:

바벨설정은 크게 두 가지 방식이 있습니다.

첫 번째 방식은 package.json 을 이요하는 방식입니다. package.json 안에 아래와 같이 plugin 과 preset 을 명시할 수 있습니다.

```json
// package.json
{
  "babel": {
    // nest config under "babel"
    "presets": ["es2015", "react", "stage-3"],
    "plugins": ["transform-class-properties"]
  }
}
```

두 번째 방식은 .babelrc 를 사용하는 방법입니다.

```json
// .babelrc
{
  "presets": ["es2015", "react", "stage-3"],
  "plugins": ["transform-class-properties"]
}
```

우리는 .babelrc 를 사용할 것을 추천합니다.

Remember: In the default Babel setup, es2015 and react are automatically enabled -- no configuration needed.

주의: es2015 와 react 는 기본적인 바벨 설정에 포함됩니다.

If you're curious to see a live example of a Babel plugin in-action, check out our article on property initializers.

만약 바벨 plugin 을 사용하는 live example 을 보고 싶다면 property initializers 에 대한 이 글을 확인해주세요.

If you want to see how to make a custom Babel plugin, the Relay chapter in our book dives into this.

만약 custom Babel plugin 을 사용하는 모습을 보고 싶다면 책(역자주: fullstack React)의 Relay 챕터를 확인해주시기 바랍니다.

For more info on the available plugins and presets and how to configure them, check out the Babel docs.

더 자세한 정보는 바벨 공식 문서에서 확인하실 수 있습니다.
