---
title: 설치하기
type: guide
order: 0
vue_version: 1.0.26
dev_size: "266.44"
min_size: "75.01"
gz_size: "26.03"
---

### 호환성에 대해

Vue.js 는 IE8을 지원하지 않습니다. Vue.js는 IE8에서 지원하지 않는 ECMAScript 5의 기능들을 사용하고 있습니다. 그러나 Vue.js는 [ECMAScript 5를 지원하는 브라우저들](http://caniuse.com/#feat=es5)을 모두 지원합니다.

### 릴리즈 노트

각 버전에 대한 릴리즈 노트 정보는 [GitHub](https://github.com/vuejs/vue/releases)에서 볼 수 있습니다.

## 독립적으로 사용하기

간단하게 다운로드 후 script 태그에 넣으면 됩니다. 그러면 `Vue`는 전역변수에 등록됩니다.

**참고하세요 : 개발시 최소화된 버전을 사용하지 마세요 이 경우 실수에 따른 경고를 보지 못할 수 있습니다.**

<div id="downloads">
<a class="button" href="/js/vue.js" download>개발용 버전</a><span class="light info">모든 경고와 디버그 모드를 지원합니다.</span>

<a class="button" href="/js/vue.min.js" download>출시용 버전</a><span class="light info">경고를 출력하지 않습니다. {{gz_size}}kb min+gzip</span>
</div>

### CDN

[jsdelivr](//cdn.jsdelivr.net/vue/{{vue_version}}/vue.min.js) 또는 [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.min.js)에서 사용할 수 있습니다. (최신 버전이 적용되는데 약간의 시간이 걸릴 수 있습니다.)

[npmcdn](https://npmcdn.com/vue/dist/vue.min.js)을 사용하면 가장 최신 버전을 받을 수 있습니다. 그리고 [npmcdn.com/vue/](https://npmcdn.com/vue/)에서 npm package 소스를 볼 수도 있습니다.

### 컨텐츠 보안 정책을 준수하는 빌드

구글 크롬 앱과 같은 일부 환경은 컨텐츠 보안 정책(CSP)를 강제하고 `new Function()` 와 같은 표현을 허용하지 않습니다. 이 경우 [CSP-준수 빌드](https://github.com/vuejs/vue/tree/csp/dist)를 사용하세요.

## NPM

NPM을 통한 설치는 Vue.js를 사용하는 큰 규모의 앱을 만드는데 적합합니다. [Webpack](http://webpack.github.io/) 또는 [Browserify](http://browserify.org/) 등의 CommonJS 모듈 번들러와 잘 작동합니다. Vue.js는 [단일 파일 컴포넌트](application.html#Single-File-Components)를 지원하는 도구를 제공합니다.

``` bash
# 가장 최신 빌드
$ npm install vue
# 가장 최신 빌드 + CSP-compliant
$ npm install vue@csp
```

## CLI

Vue.js는 [공식 CLI](https://github.com/vuejs/vue-cli)을 제공하여 빠르게 싱글 페이지 애플리케이션을 만들 수 있도록 합니다. 이는 최신의 프론트엔드 개발 방법을 위한 도구들을 제공합니다. 짧은 시간내에 실행되고 변경을 반영하며 저장시 정적 분석을 해주며 배포용 빌드를 해줍니다.

``` bash
# vue-cli 설치
$ npm install -g vue-cli
# "webpack" 보일러플레이트를 이용한 프로젝트 생성
$ vue init webpack my-project
# 의존성 설치 후 시작됩니다!
$ cd my-project
$ npm install
$ npm run dev
```

## Dev Build

**중요**: NPM에서 제공하는 CommonJS 번들(`vue.common.js`)은 `master` 브랜치에서만 확인할 수 있습니다. 그리고 `dev` 브랜치의 파일은 안정된 버전과 동일합니다. 최신의 코드는 Github에 있습니다. 스스로 빌드 해서 사용해보세요!

``` bash
git clone https://github.com/vuejs/vue.git node_modules/vue
cd node_modules/vue
npm install
npm run build
```

## Bower

``` bash
# 최신 안정화 버전 받기
$ bower install vue
```

## AMD Module Loaders

독립 버전 또는 Bower를 이용한 버전은 UMD로 감싸져 있습니다. 그래서 AMD모듈로 직접 사용할 수 있습니다.
