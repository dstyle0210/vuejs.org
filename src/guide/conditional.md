---
title: 조건부 렌더링
type: guide
order: 7
---

## v-if

Handlebars와 같이 `{}` 를 사용하는 템플릿은 조건이 있는 블럭은 아래처럼 사용합니다.

``` html
<!-- Handlebars template -->
{{#if ok}}
  <h1>Yes</h1>
{{/if}}
```

Vue.js는 `v-if` 지시어를 사용해서 동일하게 사용할 수 있습니다.

``` html
<h1 v-if="ok">Yes</h1>
```

또한 "else"는 `v-else`로 사용할 수 있습니다.

``` html
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

## v-if 템플릿

`v-if` 는 지시어이기 때문에, 한 엘리먼트에만 사용할 수 있습니다. 한개 이상의 엘리먼트에 사용하려면 어떻게 해야할까요? `v-if`를 `<template>`에 사용해 안보이도록 할 수 있습니다. 마지막에 화면에 그릴때 `<template>`는 포함되지 않게 됩니다.

``` html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

## v-show

`v-show`는 조건부 렌더링을 위한 또 다른 방법입니다. 거의 동일한 방법으로 사용합니다.:

``` html
<h1 v-show="ok">Hello!</h1>
```

다른 점은 `v-show`를 가지는 엘리먼트는 실제로 DOM에 렌더링되는 것입니다; `v-show`는 단순히 `display` 엘리먼트의 CSS 속성을 토글합니다.

참고로, `v-show`는 `<template>`을 지원하지 않습니다.

## v-else

`v-if` 와 `v-show` 지시어를 사용하는 경우에 "else block"은 `v-else`를 이용합니다.

``` html
<div v-if="Math.random() > 0.5">
  Sorry
</div>
<div v-else>
  Not sorry
</div>
```

`v-if` 와 `v-show`의 바로 뒤에 나오는 `v-else`만 작동합니다. 단독으로 사용할 수 없습니다.

### 컴포넌트 사용시 주의

`v-show`, `v-else`를 컴포넌트에 사용하는 경우 동일한 컴포넌트이어야 합니다. 아래처럼 사용하는 대신에:

```html
<custom-component v-show="condition"></custom-component>
<p v-else>This could be a component too</p>
```

`v-else`를 또 다른 `v-show`로 변경합니다:

```html
<custom-component v-show="condition"></custom-component>
<p v-show="!condition">This could be a component too</p>
```

`v-if` 를 사용하는 경우와 동일하게 작동합니다.

## v-if vs. v-show

`v-if` 블록이 전환 될 때, Vue.js는 `v-if` 내부의 템플릿 콘텐츠도 데이터 바인딩 자식 요소를 포함 할 수 있기 때문에 부분적인 컴파일 및 teardown 작업을 수행해야합니다. `v-if`는 이벤트 리스너와 자식 컴퍼넌트 내부 조건 블록이 제대로 제거되고, 전환까지 다시 생성되기 때문에 "진짜" 조건부 렌더링입니다.

`v-if`는 **lazy**입니다. 초기 표시에서 false이면 아무것도하지 않습니다. 부분 컴파일 조건이 먼저 true 가 됩니다. (그리고 컴파일이 그 캐시 될 때까지)까지 시작되지 않습니다.

반면에 `v-show`는 매우 간결합니다. - 엘리먼트는 항상 컴파일된 간단한 CSS기반 토글로 저장됩니다.

일반적으로, `v-if`는 높은 전환 비용을 가지고있는 반면 `v-show`는 높은 초기 렌더링 비용을 가집니다. 따라서 너무 자주 뭔가를 바꿀 필요가 있으면 `v-show`를 선택하고 조건이 실행될 때 변경하는 것이 거의 없는 경우는 `v-if`를 선택합니다.
