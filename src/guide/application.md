---
title: 대규모의 앱 만들기
type: guide
order: 18
---

> **새소식:** [vue-cli](https://github.com/vuejs/vue-cli)를 이용해 단일 파일 Vue 컴포넌트, 즉각적인 다시읽기, 저장시 정적테스트와 유닛테스트를 빠르게 시작할 수 있습니다!

Vue.js 코어 라이브러리는 View 계층에 집중되어 유연하게 설계되어 있으며, 모든 애플리케이션 수준의 아키텍처와 간섭하지 않습니다. 이것은 기존 프로젝트와 통합을 위한 좋을 수 있지만 처음부터 대규모 애플리케이션을 구축 할 경우 구축 경험이 적은 개발자에게 괴로운 문제가 될 수도 있습니다.

Vue.js의 생태계는 Vue에서 대규모 단일 페이지 애플리케이션 (SPA : single page application)을 구축하는 방법에 대한 라이브러리 도구 세트를 제공합니다. 이 파트에서는 "프레임워크"같은 것을 도입하고 있지만, 어디 까지나 권장하는 방법에 이상은 아닙니다. 지금까지의 각 장에서 소개 한 방법도 이용하면 좋습니다.

## 모듈화

대규모 프로젝트의 경우 코드를 정리하기 위해 모듈화된 빌드 시스템을 이용해야합니다. 권장하는 방법으로는 CommonJS 또는 ES6 모듈 형식으로 소스 코드를 쓰고 [Webpack](http://webpack.github.io/) 또는 [Browserify](http://browserify.org/)를 사용하여 작업한 내역을 묶는 방법입니다.

Webpack과 Browserify는 단순한 모듈 번들러 이상의 기능을 가지고 있습니다. 이들은 다른 처리기에서 소스 코드를 변환 할 수있는 소스 변환 API를 제공합니다. 예를 들어, [babel-loader](https://github.com/babel/babel-loader) 또는 [babelify](https://github.com/babel/babelify)을 사용하여 미래 지원되는 ES2015 / 2016 구문 코드를 작성할 수 있습니다.

지금까지 이러한 모듈 번들러를 사용한 적이없는 경우에는 몇 가지 사용예를 통해 모듈 번들러의 개념을 습득 한 후 최신 ECMAScript 기능을 사용하여 쓰기 시작하는 것이 좋습니다.

## 단일 파일 컴포넌트

Vue.js를 이용한 전형적인 프로젝트는 많은 개별 컴포넌트의 코드를 분할하여 각 구성 요소의 HTML / CSS / JavaScript를 배치 해 두어 편리합니다. 위에서 이야기한 바와 같이, Webpack 또는 Browserify를 사용할 때 다음과 같은 컴포넌트를 적절한 소스 변환 할 수 있습니다 :

<img src="/images/vue-component.png">

만약 전처리기에 추가하면, 다음과 같이 쓸 수 있습니다:

<img src="/images/vue-component-with-pre-processors.png">

이러한 단일 파일 Vue 구성 요소를 Webpack + [vue-loader](https://github.com/vuejs/vue-loader) 또는 Browserify + [vueify](https://github.com/vuejs/vueify)에서 빌드 할 수 있습니다. 실제로 [Webpackbin.com](http://www.webpackbin.com/vue)에서 온라인으로 시도 할 수 있습니다!

어떤 빌드 도구를 선택 하는 것은 당신의 경험과 요구에 크게 의존합니다. Webpack 기반의 설정은 코드 분할 (code splitting)과 같은보다 강력한 기능을 제공하는 모듈 종속성과 같은 정적 에셋을 처리하고 구성 요소의 CSS를 다른 파일로 추출할 수있어 좀 더 복잡하게 구성 할 수 있습니다. Browserify는 Webpack가 제공하는 고급 기능을 필요로하지 않는 시나리오에서 쉽게 설정할 수 있습니다.

시작하는 가장 빠른 방법은 공식 [vue-cli](https://github.com/vuejs/vue-cli)를 사용하여 미리 설정된 빌드 설정에서 실행하는 것입니다. GitHub에있는 공식 scaffold 템플릿을 찾을 수 있습니다:

- [Webpack + vue-loader](https://github.com/vuejs-templates/webpack)
- [Browserify + vueify](https://github.com/vuejs-templates/browserify)

## 라우팅

단일 페이지 애플리케이션은 공식 [official vue-router library](https://github.com/vuejs/vue-router) 라이브러리의 사용을 권장합니다. 자세한 내용은 vue-router [documentation](http://vuejs.github.io/vue-router/)를 참조하십시오.

만약 간단한 라우팅 로직을 필요로하는 경우는 `hashchange`에 이벤트 리스너와 동적 컴포넌트를 이용하여 구현할 수 있습니다.

**예:**

``` html
<div id="app">
  <component :is="currentView"></component>
</div>
```

``` js
Vue.component('home', { /* ... */ })
Vue.component('page1', { /* ... */ })
var app = new Vue({
  el: '#app',
  data: {
    currentView: 'home'
  }
})
// route 핸들러에서 페이지를 전환:
app.currentView = 'page1'
```

이 메커니즘은 [Page.js](https://github.com/visionmedia/page.js) 나 [Director](https://github.com/flatiron/director) 등의 외부 배관 라이브러리를 활용하면 매우 간단합니다.

## 서버와의 통신

모든 Vue 인스턴스는 `JSON.stringify()`에서 직접 직렬화되는 원시 `$data`를 가질 수 있습니다. 추가적인 노력을 할 필요 없습니다. Vue.js 커뮤니티 [vue-resource](https://github.com/vuejs/vue-resource) 플러그인에 기여하고, RESTFul API에서 작동하는 간단한 방법을 제공합니다. 예를 들어 jQuery의 `$.ajax` 또는 [SuperAgent](https://github.com/visionmedia/superagent) 등 좋아하는 Ajax 라이브러리도 사용할 수 있습니다. Vue.js는 백엔드가없는 Firebase, Parse 그리고 Hoodie 등의 서비스와의 연계에도 적합합니다.

## 상태 관리

대규모 애플리케이션에서 상태 관리는 종종 상태가 많은 컴포넌트에 흩어져 그 컴포넌트 사이의 상호 작용이 복잡해집니다. 그것은 Vue.js 응용 프로그램의 실제 소스는 원시 데이터 객체임을 간과하기 쉽습니다. Vue 인스턴스는 단순하게 프록시로 액세스합니다. 그러므로 여러 인스턴스가 공유되어야 상태를 가진 경우에는 중복을 피하고 동일한 상태로 공유해야합니다:

``` js
var sourceOfTruth = {}
ㄱ
var vmA = new Vue({
  data: sourceOfTruth
})

var vmB = new Vue({
  data: sourceOfTruth
})
```

현재 `sourceOfTruth`이 변화 될 때마다 `vmA`과 `vmB` 모두 자동으로 그 View를 갱신합니다. 이 생각이 확장되어, 우리는 **store패턴**에 도착할 것입니다:

``` js
var store = {
  state: {
    message: 'Hello!'
  },
  actionA: function () {
    this.state.message = 'action A triggered'
  },
  actionB: function () {
    this.state.message = 'action B triggered'
  }
}

var vmA = new Vue({
  data: {
    privateState: {},
    sharedState: store.state
  }
})

var vmB = new Vue({
  data: {
    privateState: {},
    sharedState: store.state
  }
})
```

우리는 store 자체에 store의 상태를 변화시키는 모든 기능을 가지고 있음을 유의하십시오. 이런 형태의 중앙 상태 관리는 이해하기 쉽고 어떤 상태가 일어날 지, 그리고 어떻게 작동하는지 알기 쉽습니다. 각 컴포넌트는 여전히 스스로의 상태를 유지하고 가질 수 있습니다.

![State Management](/images/state.png)

주의할 것은 작업 도중 원래 상태 객체를 결코 대체해서는 안된다는 것입니다. 컴포넌트와 store는 변화를 모니터링 할 수있는 동일한 객체에 대한 참조를 공유해야합니다.

우리는 컴포넌트가 직접 store의 상태를 변경하지 못하는 조건을 강제하는 경우에, 대신 작업을 수행하기 위해 store에 알리는 이벤트를 발송해야 합니다, 우리는 본질적 에 [Flux](https://facebook.github.io/flux/) 아키텍처에 다다랐습니다. 이 방식의 장점은 우리가 store에 일어나는 변화를 모든 상태를 추적 할 수 있고, 그리고 그 위에, 변경 내역, 스냅 샷, 히스토리 전환 등과 같은 고급 디버깅 도우미를 구현할 수 있습니다.

Flux 아키텍처는 React 애플리케이션에서 일반적으로 사용되고 있지만, Vue.js 동일하게 적용 할 수 있습니다. 예를 들어, [Vuex](https://github.com/vuejs/vuex/)는 대규모 Vue.js 애플리케이션의 내부 상태 관리에 대해 특별히 설계된 Flux에서 영감을 받은 애플리케이션 아키텍처입니다. [Redux](https://github.com/rackt/redux/)는 React를 위한 가장 인기있는 Flux 구현이며, view 계층에 대한 지식이 필요하지 않고 또한 몇 가지 [간단한 바인딩들](https://github.com/egoist/revue)을 통해 Vue에서 쉽게 사용 할 수 있습니다.

## 유닛 테스팅

모듈 기반의 빌드 시스템과 호환되는 것이면 원하는 것을 선택하십시오. 추천은 [Karma](http://karma-runner.github.io/0.12/index.html) 테스트 러너입니다. 많은 커뮤니티 플러그인이 있고 [Webpack](https://github.com/webpack/karma-webpack)과 [Browserify](https://github.com/Nikku/karma-browserify)을 지원하고 있습니다. 자세한 설정은 각 프로젝트의 문서를 참조하십시오.

테스트를 위해 코드 구조의 관점에서 가장 좋은 방법은 당신의 컴포넌트 모듈에서 원시 옵션/기능을 내보낼 수 있습니다. 다음의 예를 살펴 보겠습니다:

``` js
// my-component.js
module.exports = {
  template: '<span>{{msg}}</span>',
  data: function () {
    return {
      msg: 'hello!'
    }
  },
  created: function () {
    console.log('my-component created!')
  }
}
```

이 파일은 진입 모듈에서 다음과 같이 사용할 수 있습니다:

``` js
// main.js
var Vue = require('vue')
var app = new Vue({
  el: '#app',
  data: { /* ... */ },
  components: {
    'my-component': require('./my-component')
  }
})
```

그리고 가능 모듈은 다음과 같이 테스트 할 수 있습니다:

``` js
// 약간의 Jasmine 2.0 tests
describe('my-component', function () {
  // 소스 모듈이 필요합니다.
  var myComponent = require('../src/my-component')
  it('should have a created hook', function () {
    expect(typeof myComponent.created).toBe('function')
  })
  it('should set correct default data', function () {
    expect(typeof myComponent.data).toBe('function')
    var defaultData = myComponent.data()
    expect(defaultData.msg).toBe('hello!')
  })
})
```

There are example Karma configurations for both [Webpack](https://github.com/vuejs/vue-loader-example/blob/master/build/karma.conf.js) and [Browserify](https://github.com/vuejs/vueify-example/blob/master/karma.conf.js).
[Webpack](https://github.com/vuejs/vue-loader-example/blob/master/build/karma.conf.js)과 [Browserify](https://github.com/vuejs/vueify-example/blob/master/karma.conf.js)를 이용한 Karma 설정을 확인하세요.

<p class="tip">Vue.js 지시어는 비동기적으로 데이터 업데이트에 반응하므로 데이터가 업데이트 된 DOM 상태에 대해 assert하려면 `Vue.nextTick` 콜백을 이용해야합니다.</p>

## 출시용 배포

Vue.js의 축소된 독립형 빌드 버전은 크기를 줄이기 위해 모든 경고를 제거하지만 Vue.js 응용 프로그램을 구축하기 위해 Browserify과 Webpack 같은 도구를 사용할 때, 이를 위해 몇 가지 추가 설정을해야합니다.

### Webpack

경고 블록이 UglifyJS를 이용한 압축 중 자동으로 삭제되는 출시용 환경을 위해 Webpack의 [DefinePlugin](http://webpack.github.io/docs/list-of-plugins.html#defineplugin)을 사용하십시오. 설정 예:

``` js
var webpack = require('webpack')

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: '"production"'
      }
    }),
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false
      }
    })
  ]
}
```

### Browserify

NODE_ENV에 "production"을 설정하여 빌드 명령을 실행합니다. Vue는 자동으로 [envify](https://github.com/hughsk/envify) 변환시 자체적으로 적용하고 경고 블록에 연결할 수 있습니다. 예:

``` bash
NODE_ENV=production browserify -e main.js | uglifyjs -c -m > build.js
```

## 예제용 앱

[Vue.js Hackernews Clone](https://github.com/vuejs/vue-hackernews)은 Webpack과 vue-loader를 이용하며 vue-router를 이용한 라우팅의 기본 설계를 가집니다. 또한 HackerNews의 Firebase API를 백엔드로 사용하는 샘플 애플리케이션입니다. 결코 크지는 않지만, 이 장에서 소개 한 방법을 이용하여 만들어져 있습니다.
