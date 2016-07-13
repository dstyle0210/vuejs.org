---
title: 플러그인
type: guide
order: 17
---



## 플러그인 작성하기

플러그인은 일반적으로 Vue 전역 수준의 기능을 추가합니다. 플러그인에 대한 엄격하게 정의 된 범위는 없습니다. 작성할 수 있는 플러그인은 몇 가지 유형이 있습니다:

1. 하나 이상의 전역 메소드를 추가합니다. 예 : [vue-element](https://github.com/vuejs/vue-element)

2. 하나 이상의 전역 에셋을 추가합니다. 지시어 / 필터 / 전환효과 등. 예 : [vue-touch](https://github.com/vuejs/vue-touch)

3. Vue 인스턴스 메소드를 Vue.prototype에 추가합니다.

4. 동시에 위의 몇 가지 조합을 주입하면서 자신의 API를 제공하는 라이브러리. 예 : [vue-router](https://github.com/vuejs/vue-router)

Vue.js 플러그인은 `install` 메소드를 공개해야합니다. 이 메소드의 첫번째 파라미터는 `Vue` 생성자, 두번째는 임의의 options을 지정하여 호출합니다:

``` js
MyPlugin.install = function (Vue, options) {
  // 1. 전역 메소드 또는 속성을 추가합니다
  Vue.myGlobalMethod = ...
  // 2. 전역 에셋을 추가합니다.
  Vue.directive('my-directive', {})
  // 3. 인스턴스 메소드를 추가합니다.
  Vue.prototype.$myMethod = ...
}
```

## 플러그인 사용하기

플러그인을 사용하려면 `Vue.use()` 전역 메소드를 사용하세요:

``` js
// `MyPlugin.install(Vue)` 호출
Vue.use(MyPlugin)
```

옵션도 전달할 수 있습니다:

``` js
Vue.use(MyPlugin, { someOption: true })
```

`vue-router`와 같은 플러그인은 `Vue`가 전역 변수인 경우에 `Vue.use()`를 자동으로 호출합니다. 그러나 모듈 환경에서는 `Vue.use()`를 명시적으로 이용해야합니다.

``` js
// Browserify 또는 Webpack과 같은 CommonJS 사용시
var Vue = require('vue')
var VueRouter = require('vue-router')

// 아래 코드를 잊지 마세요
Vue.use(VueRouter)
```

## 존재하는 플러그인과 도구

- [vue-router](https://github.com/vuejs/vue-router): Vue.js의 공식 라우터. 단일 페이지 앱을 만들기 위해 Vue.js의 내부과 깊게 연관되어 있습니다.

- [vue-resource](https://github.com/vuejs/vue-resource): XMLHttpRequest 또는 JSONP를 사용하는 Web 요청의 생성 및 응답 처리를위한 서비스를 제공하는 플러그인

- [vue-async-data](https://github.com/vuejs/vue-async-data): 비동기 데이터 로딩 플러그인.

- [vue-validator](https://github.com/vuejs/vue-validator): 폼 검증을 위한 플러그인.

- [vue-devtools](https://github.com/vuejs/vue-devtools): Vue.js 애플리케이션을 디버깅 하기 위한 크롬 확장 도구.

- [vue-touch](https://github.com/vuejs/vue-touch): Hammer.js를 사용하는 터치 이벤트 관련 플러그인.

- [vue-element](https://github.com/vuejs/vue-element): Vue.js 사용자 정의 엘리먼트를 추가하는 플러그인.

- [vue-animated-list](https://github.com/vuejs/vue-animated-list): `v-for`를 이용한 리스트를 렌더링할 때 쉬운 애니메이션을 제공.

- [사용자가 기여한 플러그인 목록](https://github.com/vuejs/awesome-vue#libraries--plugins)
