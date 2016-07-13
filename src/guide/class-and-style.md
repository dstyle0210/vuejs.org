---
title: 클래스와 스타일 바인딩
type: guide
order: 6
---

데이터 바인딩의 일반적인 요구사항은 엘레멘트의 클래스와 인라인 스타일을 변경하는데 있습니다. 이 두 속성은 `v-bind`를 이용해서 조작할 수 있습니다: 우리는 조작후의 마지막 문자열을 사용하기만 하면 됩니다. 그러나 문자열 조작을 이용하는 방법은 짜증나며 에러를 유발하기 쉽습니다. 이 때문에 Vue.js는 `v-bind`가 `class`와 `style`에 사용되는 경우 추가적인 방법들을 제공합니다. 문자열 이외에 객체와 배열을 이용해서 표현식을 만들 수 있습니다.

## HTML 클래스 바인딩

<p class="tip">`{% raw %}class="{{ className }}"{% endraw %}` 처럼 Mustache를 이용하는 방법도 있습니다. 하지만 `v-bind:class` 와 함께 섞어 사용하는 것은 추천하는 방법이 아닙니다. 한번에 하나만 사용하거나 다른 방법을 이용하세요!</p>

### 객체 구문

객체를 `v-bind:class`에 보내 동적으로 클래스를 토글 할 수 있습니다. `v-bind:class` 지시어는 `class` 와 함께 사용할 수 있습니다.

``` html
<div class="static" v-bind:class="{ 'class-a': isA, 'class-b': isB }"></div>
```
``` js
data: {
  isA: true,
  isB: false
}
```

아래와 같이 그려집니다:

``` html
<div class="static class-a"></div>
```

`isA`와 `isB`가 변경되는 경우 클래스 목록도 따라서 변경됩니다. 예를들어 `isB`가 `true`가 되면 클래스 목록은 `"static class-a class-b"`이 됩니다.
그리고 아래 예제처럼 직접 객체를 바인딩 할 수 있습니다:

``` html
<div v-bind:class="classObject"></div>
```
``` js
data: {
  classObject: {
    'class-a': true,
    'class-b': false
  }
}
```

같은 결과를 보여줍니다. 눈치 챘겠지만, [계산된 속성](computed.html)으로 반환된 객체를 이용할 수 있습니다. 이는 일반적이고 강력한 패턴입니다.

### 배열 구문

객체를 `v-bind:class`에 보내 클래스 목록을 지정할 수 있습니다.

``` html
<div v-bind:class="[classA, classB]">
```
``` js
data: {
  classA: 'class-a',
  classB: 'class-b'
}
```

아래와 같이 그려집니다:

``` html
<div class="class-a class-b"></div>
```
원하는 경우 삼항 연산자를 이용해 클래스 목록을 상황따라 보여줄 수 있습니다:

``` html
<div v-bind:class="[classA, isB ? classB : '']">
```

`classA`는 항상 적용되고 `classB`는 `isB`이 `true`일 경우에만 적용됩니다.

그러나, 이렇게 사용하는 것은 여러개의 조건식이 생기면 장황하게 보여질 수 있습니다. 1.0.19+ 버전에서는 객체 구문을 배열 구문 안에 넣어 사용할 수 있습니다.

``` html
<div v-bind:class="[classA, { classB: isB, classC: isC }]">
```

## 인라인 스타일 바인딩

### 객체 구문

`v-bind:style`에 대한 객체 구문은 매우 간단합니다. 거의 CSS처럼 보이지만 자바스크립트 객체입니다. camelCase또는 kebak-case를 CSS 프로퍼티 이름에 사용할 수 있습니다:

``` html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```
``` js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

직접 스타일에 바인딩 하는 것은 템플릿을 깔끔하게 유지할 수 있는 좋은 생각입니다:

``` html
<div v-bind:style="styleObject"></div>
```
``` js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

여기서도, 객체 구문은 계산된 속성을 이용할 수 있습니다.

### 배열 구문

`v-bind:style`를 위한 배열 구문은 여러개의 스타일 객체를 적용할 수 있도록 허용합니다.

``` html
<div v-bind:style="[styleObjectA, styleObjectB]">
```

### 자동 접두사 추가

브라우저 공급자의 CSS 속성, 예를 들어 `transform`을 `v-bind:style`에서 사용하는 경우에 Vue.js는 자동으로 알아채서 적절한 접두어를 추가해줍니다.
