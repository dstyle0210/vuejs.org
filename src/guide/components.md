---
title: 컴포넌트
type: guide
order: 12
---

## 컴포넌트는 무엇인가?

컴포넌트는 Vue.js의 가장 강력한 기능 중 하나입니다. 기본적인 HTML 요소를 확장하고 재사용 가능한 코드의 캡슐화를 도와줍니다. 높은 수준에서, 컴포넌트는 Vue.js의 컴파일러가 지정된 행동을 연결하는 사용자 지정 엘리먼트입니다. 경우에 따라서 `is` 속성에서 확장 된 기본 HTML 엘리먼트 형태로 사용할 있습니다.

## 컴포넌트 사용하기

### 등록

이전 섹션에서 `Vue.extend()`를 사용하여 컴포넌트 생성자를 만드는 방법을 알아보았습니다:

``` js
var MyComponent = Vue.extend({
  // 옵션들...
})
```

이 생성자를 컴포넌트로 **등록**하려면 `Vue.component(tag, constructor)`를 사용합니다:

``` js
// 전역으로 my-component 태그로 컴포넌트를 등록 하는 방법
Vue.component('my-component', MyComponent)
```

<p class="tip">사용자 정의 태그의 이름에 대해 [W3C rules](http://www.w3.org/TR/custom-elements/#concepts) (모두 소문자로 하이픈이 포함될 수 있다)를 따르는 것이 좋은 전략이라고 생각되지만 Vue.js는 강제하지 않지는 않습니다.</p>

한번 등록하면 컴포넌트는 사용자 정의 엘리먼트 `<my-component>`로 부모 인스턴스에서 템플릿으로 사용할 수 있습니다. 컴포넌트는 root의 Vue 인스턴스를 인스턴스화하기 **이전에** 등록되어 있는지 확인하십시오. 이것이 완벽한 예입니다:


``` html
<div id="example">
  <my-component></my-component>
</div>
```

``` js
// 정의
var MyComponent = Vue.extend({
  template: '<div>A custom component!</div>'
})

// 등록
Vue.component('my-component', MyComponent)

// 루트 인스턴스 생성
new Vue({
  el: '#example'
})
```

이렇게 렌더링 됩니다:

``` html
<div id="example">
  <div>A custom component!</div>
</div>
```

{% raw %}
<div id="example" class="demo">
  <my-component></my-component>
</div>
<script>
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
new Vue({ el: '#example' })
</script>
{% endraw %}

컴포넌트의 템플릿은 사용자 정의 엘리먼트를 **대체** 한다는 것을 잊지마세요. 이 행동은 `replace` 인스턴스 옵션을 사용하여 설정할 수 있습니다.

### 지역 등록

전역으로 모든 컴포넌트를 등록 할 필요가 없습니다. 다른 구성 요소의 인스턴스 옵션 `components`에 등록하는 것으로, 그 컴포넌트의 범위 내에서만 사용할 수 있는 컴포넌트를 만들 수 있습니다:


``` js
var Child = Vue.extend({ /* ... */ })

var Parent = Vue.extend({
  template: '...',
  components: {
    // <my-component>는 부모의 템플릿에서만 사용할 수 있습니다
    'my-component': Child
  }
})
```

지시문, 필터, 그리고 트랜지션과 같은 에셋 유형에 동일한 캡슐화가 적용됩니다.

### 간단한 등록 방법

쉽게 사용하기 위해 실제 생성자 대신 `Vue.component()`와 `component` 옵션에 옵션 개체에 직접 전달할 수 있습니다. Vue.js는 내부에서 자동으로 `Vue.extend()`를 호출합니다:


``` js
// 1 단계에서 extend를 등록합니다
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

// 지역 등록에 대해서도 작동합니다
var Parent = Vue.extend({
  components: {
    'my-component': {
      template: '<div>A custom component!</div>'
    }
  }
})
```

### 컴포넌트 옵션 주의사항

Vue 생성자에 전달할 수있는 대부분의 옵션은 `Vue.extend()`에서 전달할 수 있습니다. 그러나 `data`와 `el` 두 가지 특별한 경우는 다릅니다. 순수하게 `Vue.extend()`에 `data`로 객체를 전달해야 합니다:

``` js
var data = { a: 1 }
var MyComponent = Vue.extend({
  data: data
})
```

이에 따른 문제는 같은 `data` 객체는 `MyComponent`의 모든 인스턴스간에 공유된다는 것입니다! 이런 작동이 필요한 곳은 없기 때문에, `data` 옵션으로 새로운 개체를 반환하는 함수를 사용합니다:

``` js
var MyComponent = Vue.extend({
  data: function () {
    return { a: 1 }
  }
})
```

같은 이유로, `el` 옵션도 `Vue.extend()`에서 사용하면 함수의 값이 필요합니다.

### 템플릿 파싱

Vue.js 템플릿 엔진은 DOM 기반으로 그대로 사용하지 않고 브라우저에 포함된 기본 구문 분석을 사용합니다. 문자열 기반 템플릿과 비교했을 때 이 방법은 장점이 있지만, 주의점도 있습니다. 템플릿은 적절한 HTML의 모임이어야합니다. HTML 엘리먼트 중 일부는 그 내부에 어떤 요소가 볼 수있는 한계를 가지는 것이 있습니다. 이러한 제한의 대표적인 내용입니다:


- `a`는 다른 대화형 요소에 포함 할 수 없습니다 (예 : 버튼 및 기타 링크)
- `li`는 `ul` 또한 `ol`의 직접적인 자식이어야하고 `ul`과 `ol` 모두 `li`만을 포함 할 수 있습니다
- `option`은 `select`의 직접적인 자식이어야하고, `select`는 `option` (그리고 `optgroup`) 만 포함 할 수 있습니다
- `table`은 `thead`, `tbody`, `tfoot` 그리고 `tr`만을 포함 할 수 있으며 이러한 요소는 `table`의 직접적인 자식이어야합니다
- `tr`은 `th` 그리고 `td`만을 포함 할 수 있고 이러한 요소는 `tr`의 직접적인 자식이어야 합니다

런타임시 이러한 제한이 예기치 않은 동작을 일으킬 수 있습니다. 간단한 경우에는 잘 작동하는 것처럼 보일 수도 있지만, 사용자 지정 엘리먼트가 브라우저에 의한 검증 전에 사용되는 것을 믿을 수는 없습니다. 예를 들어, `<my-select> <option> ... </ option> </ my-select>`는 결국 `<select> ... </select>`로 변환됩니다.


또한 `ul`, `select`, `table` 그리고 다른 유사한 제한을 가지는 요소의 내부에서 사용자 정의 태그 (사용자 지정 요소와 `<component>` `<template>`과 `<partial>`과 같은 특별한 태그를 포함)을 사용할 수 없습니다. 사용자 정의 엘리먼트는 밖으로 밀려 제대로 표시되지 않을 것입니다.

사용자 지정 엘리먼트 대신 특별한 특성 `is`를 사용할 수 있습니다:

``` html
<table>
  <tr is="my-component"></tr>
</table>
```


`<table>`에서 `<template>` 사용 대신 `<tbody>`를 사용합시다. 테이블은 여러 `tbody`을 가지는 것을 허용하고 있기 때문입니다.:

``` html
<table>
  <tbody v-for="item in items">
    <tr>Even row</tr>
    <tr>Odd row</tr>
  </tbody>
</table>
```

## Props

### Props을 이용한 데이터 전달

모든 컴포넌트 인스턴스는 각자의 **격리 된 범위 (isolated scope)**를 갖습니다. 즉, 자식 컴포넌트의 템플릿 부모 데이터를 직접 참조 할 수 없다(그리고 해서는 안된다)는 것입니다. 데이터는 **props**를 사용하여 하위 구성 요소에 전달합니다.


"prop"는 컴포넌트의 데이터 필드이며, 그 컴포넌트 데이터는 부모 구성 요소로부터 전달됩니다. 자식 컴포넌트는 [`props` option](/api/#props)을 이용하여 전달을 가정 props를 명시 적으로 선언해야합니다 :

``` js
Vue.component('child', {
  // props를 선언합니다
  props: ['msg'],
  // prop는 내부 템플릿으로 사용할 수 있으며,
  // `this.msg`로 설정됩니다
  template: '<span>{{ msg }}</span>'
})
```

그러면 다음과 같이 일반 문자열을 전달할 수 있습니다:

``` html
<child msg="hello!"></child>
```

**결과:**

{% raw %}
<div id="prop-example-1" class="demo">
  <child msg="hello!"></child>
</div>
<script>
new Vue({
  el: '#prop-example-1',
  components: {
    child: {
      props: ['msg'],
      template: '<span>{{ msg }}</span>'
    }
  }
})
</script>
{% endraw %}

### camelCase vs. kebab-case

HTML의 속성은 대소 문자를 구별하지 않습니다. camelCase 된 prop 이름을 속성으로 사용할 때, 그들을 kebab-case(대시로 구성 됨)를 사용하여야 합니다:


``` js
Vue.component('child', {
  // JavaScript의 camelCase
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```

``` html
<!-- HTML의 kebab-case  -->
<child my-message="hello!"></child>
```

### 동적인 Props

표현식에 일반 속성을 바인딩하는 것과 마찬가지로 `v-bind`를 사용하여 부모 데이터에 props를 동적으로 바인딩 할 수 있습니다. 부모 데이터가 업데이트 될 때마다 그 데이터가 자식으로 흘러 내려갑니다:

``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
```

`v-bind`위한 짧게 사용하기를 사용할 수 있다:

``` html
<child :my-message="parentMsg"></child>
```

**Result:**

{% raw %}
<div id="demo-2" class="demo">
  <input v-model="parentMsg">
  <br>
  <child v-bind:my-message="parentMsg"></child>
</div>
<script>
new Vue({
  el: '#demo-2',
  data: {
    parentMsg: 'Message from parent'
  },
  components: {
    child: {
      props: ['myMessage'],
      template: '<span>{{myMessage}}</span>'
    }
  }
})
</script>
{% endraw %}

### 리터럴 vs. 동적

초보자 흔한 실수는 리터럴 구문을 사용하여 숫자를 건네 주려고하는 것입니다 :

``` html
<!-- 이것은 순수한 문자열 "1"을 전달합니다 -->
<comp some-prop="1"></comp>
```

그러나 이것은 리터럴 prop이기 때문에 그 값은 실제로 몇 대신 순수한 문자열 `"1"`이 전달됩니다. 실제로 JavaScript의 숫자를 전달하려면 그 값이 JavaScript 식으로 평가 될 수 있도록 동적 구문에서 사용해야합니다 :

``` html
<!-- 이것은 실제 숫자를 전달합니다 -->
<comp :some-prop="1"></comp>
```

### Prop 바인딩 유형

기본적으로 모든 props는 자식 속성과 부모 속성 사이에서 **one-way-down** 바인딩을 형성합니다 : 부모 속성을 업데이트하면 그것은 자식으로 흘러 가지만 그 반대는 없습니다. 이 디폴트는 자식 컴포넌트가 잘못 부모의 상태를 변경하지 않도록 합니다, 그렇지 않으면 애플리케이션의 데이터 흐름을 알아내기 어려워집니다. 그러나 `.sync` 그리고 `.once` 바인딩 유형 수정자 **binding type modifier**에 따르면 two-way 또는 one-time 바인딩을 명시적으로 강제 할 수 있습니다 :

구문의 비교 :


``` html
<!-- 기본값은 one-way-down 바인딩 -->
<child :msg="parentMsg"></child>

<!-- 명시적인 two way binding -->
<child :msg.sync="parentMsg"></child>

<!-- 명시적인 one-time binding -->
<child :msg.once="parentMsg"></child>
```

two-way 바인딩은 아이의 `msg` 속성의 변경을 부모의 `parentMsg` 속성에 반환하고 동기화합니다. one-time 바인딩은 한 번 설치되면 그 앞의 변경을 부모와 자식간에 동기화하지 않습니다.


<p class="tip">만약 전달되는 prop이 객체 또는 배열이라면, 그것은 참조 이니 유의하십시오. 자식 내에서 객체 또는 배열 자체를 변경하는 것은 사용하는 바인딩의 유형에 관계없이 부모의 상태에 영향을 미칩니다.</p>

### Prop 검증

컴포넌트는 전달 받을 props에 대한 요구 사항을 지정할 수 있습니다. 이러한 검증 요구 사항은 사실상 컴포넌트의 API를 구성하고 사용자가 컴포넌트를 제대로 사용하는 것을 보장하기 때문에 다른 사람에게 사용되는 것을 의도한 컴포넌트를 만들 때 유용합니다. 문자열의 배열로 props를 정의하는 대신 검증 요구 사항을 포함한 오브젝트 해시 형식을 사용할 수 있습니다:

``` js
Vue.component('example', {
  props: {
    // 기본 유형 검사 (`null`는 어떤 형태로도 접수)
    propA: Number,

    // 여러 허용 가능한 형태 (1.0.21 이후)
    propM: [String, Number],

    // 필수 문자열
    propB: {
      type: String,
      required: true
    },

    // 기본값
    propC: {
      type: Number,
      default: 100
    },

    // 객체와 배열의 기본은 팩토리 함수에서 반환합니다
    propD: {
      type: Object,
      default: function () {
        return { msg: 'hello' }
      }
    },

    //이 prop는 two-way 바인딩을 나타냅니다
    // 바인딩 유형이 일치하지 않을 경우 경고를 던져
    propE: {
      twoWay: true
    },

    // 사용자 검증 함수
    propF: {
      validator: function (value) {
        return value > 10
      }
    },

    // 강제 함수 (1.0.12에서 새로 추가)
    // 구성 요소에서 값을 설정하기 전에 그것을 캐스팅
    propG: {
      coerce: function (val) {
        return val + '' // 값을 문자열로 변환
      }
    },
    propH: {
      coerce: function (val) {
        return JSON.parse(val) // 값을 객체로 변환합니다
      }
    }
  }
})
```

`type`은 다음의 기본 생성자 중 하나입니다 :

- String
- Number
- Boolean
- Function
- Object
- Array

또한, `type` 사용자 생성자 함수가 될 수 있고 `instanceof` 검증에서 assert 할 수 있습니다.

prop 검증이 실패하면 Vue는 자식 컴포넌트로의 값 전달을 거부합니다. 그리고 만약 개발 빌드를 사용하는 경우 경고를 표시합니다.

## 부모-자식간 통신

### 부모 체이닝

자식 컴포넌트는 `this.$parent`로 부모 컴포넌트에 대한 접근을 할 수 있습니다. root의 Vue 인스턴스는 `this.$root`로 자손 모두에서 사용할 수 있습니다. 각 부모 구성 요소는 모든 아이 컴포넌트를 포함한 `this.$children`라는 배열을 가지고 있습니다.


부모 체인에서 모든 인스턴스에 접근할 수 있지만 자식 요소에서 부모 데이터에 직접 의존하는 것은 피하고 props를 명시적으로 사용하여 데이터를 전달할 수 있도록해야합니다. 또한 아이 컴포넌트의 부모 상태를 변화시키는 것은 매우 나쁜 생각입니다. 왜냐하면:

1. 부모와 자식을 강하게 결합 해버립니다.

2. 부모 컴포넌트만을 보고 그 상태를 추리하는 것을 매우 어렵게합니다. 왜냐하면 그 상태가 모든 자식에 의해 변경될 가능성이 있기 때문입니다! 이상적으로는 구성 요소 자체에만 자신의 상태 변경을 허용한다.


### 사용자 정의 이벤트

모든 Vue 인스턴스는 컴포넌트 트리의 통신을 용이하게하는 사용자 지정 이벤트 인터페이스를 구현합니다. 이 이벤트 시스템은 기본 DOM 이벤트와는 무관하며, 동작이 다릅니다.


각 Vue 인스턴스는 Event Emitter이며, 다음이 가능합니다 :

- `$on()`을 사용하여 이벤트를 수신합니다.

- `$emit()`를 사용하여 자신에게 이벤트를 트리거합니다.

- `$dispatch()`를 사용하여 부모의 체인을 따라 위로 전달하는 이벤트를 전달합니다.

- `$broadcast()`를 사용하여 모든 자손 아래쪽으로 전파하는 이벤트를 전달합니다.

<p class="tip">DOM 이벤트와는 달리, Vue의 이벤트 콜백이 명시 적으로 true를 반환하지 않으면 전파 경로에 따라 처음 콜백을 트리거 한 후 자동으로 전달을 중지합니다.</p>

간단한 예 :

``` html
<!-- 자식을 위한 템플릿 -->
<template id="child-template">
  <input v-model="msg">
  <button v-on:click="notify">Dispatch Event</button>
</template>

<!-- 부모를 위한 템플릿 -->
<div id="events-example">
  <p>Messages: {{ messages | json }}</p>
  <child></child>
</div>
```

``` js
// 현재의 메시지로 이벤트를 전달하는 자식을 등록합니다
Vue.component('child', {
  template: '#child-template',
  data: function () {
    return { msg: 'hello' }
  },
  methods: {
    notify: function () {
      if (this.msg.trim()) {
        this.$dispatch('child-msg', this.msg)
        this.msg = ''
      }
    }
  }
})

// 자식 컴포넌트 생성 이벤트를 수신 할 때 마다 부모 컴포넌트의 message에 메시지를 추가합니다
var parent = new Vue({
  el: '#events-example',
  data: {
    messages: []
  },
  //`events` 옵션은 인스턴스가 작성 될 때,
  //이 옵션에서 지정된 콜백 이벤트 리스너로`$on`을 불러 등록합니다
  events: {
    'child-msg': function (msg) {
      // 이벤트 콜백에서`this`는
      // 그것이 등록 될 때 자동으로 인스턴스에 연결됩니다
      this.messages.push(msg)
    }
  }
})
```

{% raw %}
<script type="x/template" id="child-template">
  <input v-model="msg">
  <button v-on:click="notify">Dispatch Event</button>
</script>

<div id="events-example" class="demo">
  <p>Messages: {{ messages | json }}</p>
  <child></child>
</div>
<script>
Vue.component('child', {
  template: '#child-template',
  data: function () {
    return { msg: 'hello' }
  },
  methods: {
    notify: function () {
      if (this.msg.trim()) {
        this.$dispatch('child-msg', this.msg)
        this.msg = ''
      }
    }
  }
})

var parent = new Vue({
  el: '#events-example',
  data: {
    messages: []
  },
  events: {
    'child-msg': function (msg) {
      this.messages.push(msg)
    }
  }
})
</script>
{% endraw %}

### 사용자 이벤트를 위한 v-on

위의 예는 꽤 괜찮지만, 부모 컴포넌트의 코드를 볼 때 `"child-msg"` 이벤트가 어디에서 오는지 명확하지 않습니다. 자식 컴포넌트가 사용되는 장소에서 이벤트 핸들러를 선언 할 수 있다면 더욱 좋습니다. 이것 위해 `v-on` 자식 요소로 사용 된 경우 사용자 정의 이벤트를 수신하는 데 사용할 수 있습니다:

``` html
<child v-on:child-msg="handleIt"></child>
```

매우 명확해졌습니다. 자식이 `"child-msg"` 이벤트를 발생하면 부모의 `handleIt` 메소드가 호출됩니다. 부모의 상태에 영향을 미치는 모든 코드는 부모 메소드의 `handleIt`안에만 존재하고 하위 컴포넌트는 이벤트 트리거에 연결됩니다.

### 자식 컴포넌트 참조

props이나 이벤트의 존재에도 불구하고, 때로는 자식 컴포넌트의 JavaScript에서 직접 액세스 할 필요가 있습니다. `v-ref`를 사용하여 하위 컴포넌트에 대한 참조 ID를 할당해서 해결합니다. 예를 들면:

``` html
<div id="parent">
  <user-profile v-ref:profile></user-profile>
</div>
```

``` js
var parent = new Vue({ el: '#parent' })
// 자식 요소의 인스턴스에 접근
var child = parent.$refs.profile
```

`v-ref`와 `v-for`를 함께 사용할 때 얻는 값은 데이터 소스를 미러링 한 자식 컴포넌트가 포함된 배열 또는 객체입니다.

## Slot과 컨텐츠 배포

컴포넌트를 사용할 때, 그것은 종종 이러한 컴포넌트를 구성하는 것이 바람직합니다:

``` html
<app>
  <app-header></app-header>
  <app-footer></app-footer>
</app>
```

다음 두 가지를 알고 있어야 합니다:

1. `<app>` 엘리먼트는 어떤 내용이 마운트 되는 대상의 내부에 존재하는지 모릅니다. `<app>`를 사용하는 컴포넌트가 무엇이든간에, 부모 컴포넌트가 내부 컨텐츠를 결정합니다.

2. `<app>` 요소는 거의 대부분 자신의 템플릿을 가지고 있습니다.

컴포넌트를 동작시키기위한 부모의 "컨텐츠"및 컴포넌트 자신의 템플릿을 결합한 방법이 필요합니다. 이것은 "컨텐츠 전송"(또는 Angular에 익숙한 경우 "transclusion")라는 프로세스입니다. Vue.js는 원본 컨텐츠에 대한 통로 역할을하는 특별한 `<slot>` 요소를 사용하여 현재 [Web Components spec draft](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md)를 모방한 컨텐츠 전송 API를 구현합니다.

### 컴파일 범위

API를 알아보기 전에 먼저 컨텐츠가 컴파일되는 범위를 명확히 알아야 합니다. 아래 템플릿을 생각해보십시오:
``` html
<child-component>
  {{ msg }}
</child-component>
```

`msg`는 부모의 데이터와 자식 데이터 중 어디에 바인딩되어야 할까요? 정답은 부모입니다. 컴포넌트 범위에 대한 간단한 룰입니다:

> 부모 템플릿의 모든 것은 부모의 범위에서 컴파일되고 자식 템플릿의 모든 것은 자식의 범위에서 컴파일합니다.

일반적으로 부모 템플릿의 자식 속성 / 메소드에 지시어을 바인딩하려고 하는 실수를 할 수 있습니다:

``` html
<!-- 작동하지 않습니다 -->
<child-component v-show="someChildProperty"></child-component>
```

`someChildProperty`가 자식 컴포넌트의 속성이라고 가정하면 위 예제는 의도 한대로 작동하지 않을 것입니다. 부모의 템플릿은 자식 컴포넌트의 상태에 대해 알고 있을 필요가 없습니다.

컴포넌트 하위 범위의 지시이에 바인드할 필요가 있는 경우, 자식 컴포넌트 자신의 템플릿에서 해야합니다:

``` js
Vue.component('child-component', {
  // 올바른 범위이기 때문에 작동합니다
  template: '<div v-show="someChildProperty">Child</div>',
  data: function () {
    return {
      someChildProperty: true
    }
  }
})
```

마찬가지로, 전달 내용은 부모 범위에서 컴파일됩니다.

### 단일 Slot

부모 컨텐츠는 자식 컴포넌트의 템플릿이 하나의 `<slot>` 아울렛을 포함하지 않는한 **파기**됩니다. 속성이 없는 슬롯이 하나만 있을 때에는 모든 컨텐츠는 슬롯을 대체하고 DOM이 그 위치에 삽입됩니다.

`<slot>` 태그 내에서 사용한 모든 태그는 **대체 컨텐츠**로 간주됩니다. 대체 컨텐츠는 자식 범위에서 컴파일되어 호스팅하는 엘리먼트가 비어있고 추가할 내용이 없는 경우에만 표시됩니다.

다음 템플릿을 포함한 컨텐츠가 있다고 가정합시다:


``` html
<div>
  <h1>This is my component!</h1>
  <slot>
    컴포넌트 지시어 사이에 아무런 컨텐츠가 없는 경우에만 보입니다.
  </slot>
</div>
```

이 컴포넌트를 사용한 부모의 마크 업은 다음과 같습니다:

``` html
<my-component>
  <p>This is some original content</p>
  <p>This is some more original content</p>
</my-component>
```

렌더링 결과입니다:

``` html
<div>
  <h1>This is my component!</h1>
  <p>This is some original content</p>
  <p>This is some more original content</p>
</div>
```

### 이름을 가진 Slot

`<slot>` 엘리먼트는 특별한 속성 `name`을 가지고 컨텐츠를 전달하는 방법을 정의하는 데 사용할 수 있습니다. 다른 이름을 가지는 여러 슬롯을 가질 수 있습니다. 이름을 가진 slot 컨텐츠의 해당 `slot` 속성을 가진 모든 요소와 일치합니다.

이름이 없는 **default slot**이 있는 경우 매치되지 않는 모든 컨텐츠가 출력됩니다. 기본 슬롯이 없는 경우에는 내용이 삭제됩니다.

예를 들어, 다음의 템플릿과 같은 `다수의 컴포넌트 삽입 템플릿`이 있다고 가정합니다:

``` html
<div>
  <slot name="one"></slot>
  <slot></slot>
  <slot name="two"></slot>
</div>
```

부모의 태그는 다음과 같습니다:

``` html
<multi-insertion>
  <p slot="one">One</p>
  <p slot="two">Two</p>
  <p>Default A</p>
</multi-insertion>
```

렌더링되는 결과는 다음과 같다:


``` html
<div>
  <p slot="one">One</p>
  <p>Default A</p>
  <p slot="two">Two</p>
</div>
```

컨텐츠 전달 API는 함께 사용하기위한 컴포넌트를 설계 할 때 매우 유용한 메커니즘입니다.


## 동적 컴포넌트

예약된 `<component>` 엘리먼트와 그 `is` 속성에 동적으로 바인딩하여 동일한 마운트 위치에서 여러 컴포넌트를 동적으로 전환 할 수 있습니다:

``` js
new Vue({
  el: 'body',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
```

``` html
<component :is="currentView">
  <!-- vm.currentview이 변경되면 내용이 변경됩니다! -->
</component>
```

### `keep-alive`

교체 되어버린 컴포넌트를 유지하거나 다시 렌더링하는 것을 피하기 위해 `keep-alive` 지시어 매개변수를 추가할 수 있습니다:

``` html
<component :is="currentView" keep-alive>
  <!-- 비활성화 된 컴포넌트를 캐시합니다! -->
</component>
```

### `activate` 훅

컴포넌트를 전환 할 때 다음 컴포넌트는 미리 어떤 비동기 작업을 수행 할 필요가 있을지 모릅니다. 컴포넌트의 교체 타이밍을 제어하려면 다음 컴포넌트에서 `activate` 훅을 구현합니다:


``` js
Vue.component('activate-example', {
  activate: function (done) {
    var self = this
    loadDataAsync(function (data) {
      self.someData = data
      done()
    })
  }
})
```

`activate` 훅은 동적 컴포넌트의 전환 사이 또는 정적 컴포넌트의 첫 렌더링 때 뿐이라는 것을 유의하십시오. 인스턴스 메소드에 의한 수동 삽입에 영향을주지 않습니다.

### `transition-mode`

`transition-mode` 매개 변수 속성은 두 가지 동적 컴포넌트 간 전환이 어떻게 실행되는지 지정할 수 있습니다.

기본적으로 컴포넌트의 들어오고 나가는 전환은 동시에 발생합니다. 이 속성은 다른 두 가지 모드를 설정할 수 있습니다:

- `in-out` : 새로운 컴포넌트로의 전환이 먼저 일어나고 그 전환이 완료된 후 현재의 컴포넌트의 나가는 전환을 시작합니다.

- `out-in` : 현재의 컴포넌트가 나가는 전환이 먼저 일어나고 그 전환이 완료된 후 새 컴포넌트의 전환이 시작됩니다.

**예**

``` html
<!-- 먼저 페이드 아웃 이후 페이드 -->
<component
  :is="view"
  transition="fade"
  transition-mode="out-in">
</component>
```

``` css
.fade-transition {
  transition: opacity .3s ease;
}
.fade-enter, .fade-leave {
  opacity: 0;
}
```

{% raw %}
<div id="transition-mode-demo" class="demo">
  <input v-model="view" type="radio" value="v-a" id="a" name="view"><label for="a">A</label>
  <input v-model="view" type="radio" value="v-b" id="b" name="view"><label for="b">B</label>
  <component
    :is="view"
    transition="fade"
    transition-mode="out-in">
  </component>
</div>
<style>
  .fade-transition {
    transition: opacity .3s ease;
  }
  .fade-enter, .fade-leave {
    opacity: 0;
  }
</style>
<script>
new Vue({
  el: '#transition-mode-demo',
  data: {
    view: 'v-a'
  },
  components: {
    'v-a': {
      template: '<div>Component A</div>'
    },
    'v-b': {
      template: '<div>Component B</div>'
    }
  }
})
</script>
{% endraw %}

## 기타

### 컴포넌트와 v-for

일반적 엘리먼트와 같이 사용자 지정 컴포넌트에서 `v-for`를 직접 사용할 수 있습니다:

``` html
<my-component v-for="item in items"></my-component>
```

그러나 컴포넌트는 각자 격리 된 범위를 가지고 있기 때문에, 컴포넌트로 데이터 전달이 불가능합니다. 컴포넌트에 반복하는 데이터를 전달하기 위해 props를 사용해야합니다:

``` html
<my-component
  v-for="item in items"
  :item="item"
  :index="$index">
</my-component>
```

컴포넌트에 `item`을 자동으로 주입하지 않는 이유는 그렇게 하면 컴포넌트가 `v-for` 동작에 밀접하게 결합되기 때문입니다. 데이터가 어디에서 온 것인지를 명시할 컴포넌트를 다른 상황에서 재사용 가능하게합니다.

### 재사용 가능한 컴포넌트 작성

컴포넌트를 만들 때 이 컴포넌트를 어딘가에서 재사용할지 염두하면 좋을 것입니다. 일회성 컴포넌트가 서로 단단히 결합을 갖는 것이 좋더라도 재사용 가능한 구성 요소는 깨끗한 공개된 인터페이스를 정의해야합니다.

Vue.js 구성 요소에 대한 API는 본질적으로 props, events, slots의 세 부분으로 구성됩니다:

- **Props** 외부 환경이 구성 요소에 데이터를 제공하는 것을 허용합니다.
- **Events** 구성 요소가 외부 환경의 액션을 트리거하는 것을 허용합니다.
- **Slots** 외부 환경이 구성 요소의 view 구조에 컨텐츠를 삽입하는 것을 허용합니다.

`v-bind`와 `v-on`위한 짧게 사용하기를 사용하면 의도를 명확하고 간결하게 템플릿에서 알 수 있습니다:

``` html
<my-component
  :foo="baz"
  :bar="qux"
  @event-a="doThis"
  @event-b="doThat">
  <!-- content -->
  <img slot="icon" src="...">
  <p slot="main-text">Hello!</p>
</my-component>
```

### 비동기 컴포넌트

대규모 애플리케이션은 애플리케이션을 작은 부분으로 분할하여 실제로 필요할 때 서버에서 컴포넌트를 불러오는 것만 필요할지도 모릅니다. 이를 쉽게하기 위해 Vue.js에서 컴포넌트 정의를 비동기 적으로 해결하는 팩토리 함수로 컴포넌트를 정의할 수 있습니다. Vue.js는 컴포넌트가 실제로 렌더링이 필요할 때 팩토리 함수의 트리거만 수행하고 나중에 다시 렌더링하기 위해 결과를 캐시합니다. 예를 들면:

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

팩토리 함수는 서버에서 컴포넌트 정의를 취득한 후에 불리는 `resolve` 콜백을 받습니다. 불러오기가 실패한 것을 나타 내려면 `reject (reason)`를 호출 할 수 있습니다. 여기에서 `setTimeout`은 단순히 예제를 위한 것입니다. 어떻게 컴포넌트를 얻는 것은 모두 작성자에게 달려 있습니다. 권장하는 방법 중 하나는 [Webpack 코드 분할 기능](http://webpack.github.io/docs/code-splitting.html)에서 비동기 컴포넌트를 사용하는 것이다.

``` js
Vue.component('async-webpack-example', function (resolve) {
  // 이 require 구문은 webpack 대해
  // 빌드 코드를 자동으로 분할하고
  // ajax 요청에서 자동으로 로드되는 번들하도록 지시합니다
  require(['./my-async-component'], resolve)
})
```

### 에셋 명명 규칙

컴포넌트와 지시어와 같은 에셋은 HTML 속성 또는 HTML 사용자 정의 태그의 형태로 템플릿에 표시됩니다. HTML 속성 이름과 태그 이름은 대소 문자를 구별하지 않습니다 **case-insensitive** 때문에 종종 camelCase 대신 kebab-case를 사용하여 에셋에 이름을 붙일 필요가 있지만, 이것은 조금 불편합니다.

사실 Vue.js는 camelCase 또는 PascalCase를 사용하여 에셋에 명명하는 것을 지원하고 이를 템플릿에서 kebab-case로 자동으로 처리합니다.(props의 이름 변경과 비슷합니다):



``` js
// 구성 요소 정의합니다
components: {
  // cameCase를 사용하여 등록합니다
  myComponent: { /*... */ }
}
```

``` html
<!-- 템플릿은 kebab-case를 사용합니다 -->
<my-component></my-component>
```

이것은 [ES6 객체 리터럴 짧게 쓰기](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#New_notations_in_ECMAScript_6)와 함께 잘 작동합니다 :

``` js
// PascalCase
import TextBox from './components/text-box';
import DropdownMenu from './components/dropdown-menu';

export default {
  components: {
    // 템플릿에서 <text-box>와 <dropdown-menu>로 사용합니다
    TextBox,
    DropdownMenu
  }
}
```

### Recursive Component

컴포넌트는 템플릿에서 자신을 재귀적으로 호출 할 수 있습니다. 이는 `name` 옵션이 있을 때 뿐입니다:

``` js
var StackOverflow = Vue.extend({
  name: 'stack-overflow',
  template:
    '<div>' +
      // 재귀적으로 자신을 호출뿐니다
      '<stack-overflow></stack-overflow>' +
    '</div>'
})
```

위와 같은 컴포넌트는 "max stack size exceeded"오류로 끝납니다. 재귀는 조건부로 사용해야 합니다. `Vue.component()`를 사용하여 전역 컴포넌트를 등록 할 때, 전역 ID가 자동으로 컴포넌트의 `name` 옵션으로 설정됩니다.

### 조각 인스턴스

`template` 옵션을 사용하면 템플릿의 내용은 Vue 인스턴스가 마운트되어있는 엘리먼트를 바꿉니다. 그러므로 템플릿에 항상 하나의 루트 레벨 요소를 갖도록 권장됩니다.

이러한 템플릿 대신:

``` html
<div>root node 1</div>
<div>root node 2</div>
```

이렇게 하십시오:

``` html
<div>
  I have a single root node!
  <div>node 1</div>
  <div>node 2</div>
</div>
```

Vue 인스턴스를 **조각 인스턴스**로 바꿔야 하는 몇 가지 상황이 있습니다:

1. 여러 개의 최상위 요소를 가지는 템플릿
2. 일반 텍스트만 가지는 템플릿
3. 다른 컴포넌트 (그 자체가 조각 인스턴스지도 모른다)만 가지는 템플릿
4. 엘리먼트 지시어를 가지는 템플릿, 예를 들어 `<partial>`과 vue-router의 `<router-view>`
5. 루트 노드가 흐름 제어 지시어, 예를 들면 `v-if` 나 `v-for`을 가진 템플릿

위의 모든 인스턴스에 알수없는 갯수의 최상위 엘리먼트를 갖게 되므로 조각으로 DOM 컨텐츠를 관리합니다. 조각 인스턴스는 그래도 제대로 컨텐츠를 렌더링합니다. root 노드를 가지고 있지 않지만, 빈 텍스트 노드 (또는 디버그 모드에서 코멘트 노드) 인`$el`을 "앵커 노드"삼아 가리키도록합니다.

더 중요한 것은, **흐름 제어되지 않는 지시어, prop가 아닌 속성, 그리고 컴포넌트에서의 전환은 무시하는 것 입니다**, 왜냐하면 그들을 바인딩 root 요소가 없기 때문입니다:


``` html
<!-- root 요소가 없기 때문에 작동하지 않습니다 -->
<example v-show="ok" transition="fade"></example>

<!-- props 작동합니다 -->
<example :prop="someData"></example>

<!-- 흐름 제어는 작동하지만 전환은 되지 않습니다 -->
<example v-if="ok"></example>
```

조각 인스턴스에 대해 유효한 사용사례는 물론 있지만, 일반적으로 컴포넌트 템플릿에 하나의 root 요소를 주는 것이 좋습니다. 컴포넌트 엘리먼트의 지시어과 속성의 올바른 작동을 보장하고 성능 향상에 약간 도움이 됩니다.

### 인라인 템플릿

특별한 속성 `inline-template`이 자식 컴포넌트에 있으면, 전달된 컨텐츠로 처리하지 않고 템플릿으로 내부 컨텐츠를 사용합니다. 더 유연한 템플릿을 만들 수 있게 합니다.

``` html
<my-component inline-template>
  <p>These are compiled as the component's own template</p>
  <p>Not parent's transclusion content.</p>
</my-component>
```

그러나 `inline-template` 템플릿의 범위를 파악하기 어렵게 만들고 컴포넌트의 템플릿을 캐시 할 수 없습니다. 가장 좋은 방법은 `template` 옵션을 사용하여 구성 요소 내부에서 템플릿을 정의하도록하십시오.
