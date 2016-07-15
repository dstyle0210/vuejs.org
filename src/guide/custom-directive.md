---
title: 사용자 정의 지시어
type: guide
order: 14
---

## 기초

코어에서 제공하는 지시어의 외에 사용자 정의 지시어 (custom directive)을 등록 할 수 있습니다. 사용자 정의 지시어는 모든 DOM의 행동에 매핑 데이터를 변경하는 메커니즘을 제공합니다.

`Vue.directive(id, definition)` 메소드에서 **directive id**와 **정의 객체**를 계속 전달하여 전역으로 지시어를 등록 할 수 있습니다. 컴포넌트의 `directives` 옵션은 로컬 사용자 정의 지시어에 등록 할 수 있습니다.


### 훅 함수

정의 객체는 일부 훅 (모두 생략가능)을 제공합니다 :

- **bind**: 지시문이 처음 대상 요소에 바인드할 때 한번만 호출됩니다.

- **update**: 처음 한 번은 `bind` 직후에 초기 값과 함께 호출되며, 이후 바인딩되는 값이 변경 될 때마다 호출됩니다. 아규먼트는 새로운 값과 이전 값이 전달됩니다.

- **unbind**: 바인딩이 제거되는 시점에 한번만 호출됩니다.

**예**

``` js
Vue.directive('my-directive', {
  bind: function () {
    // 준비 작업을합니다
    // e.g. 이벤트 리스너를 추가하거나 한 번만 수행해야하는 비용이 많이 드는 작업을 수행
  },
  update: function (newValue, oldValue) {
    // 갱신된 값이 필요한 작업을 수행
    // 이 부분은 초기화시에도 호출됩니다.
  },
  unbind: function () {
    // 정리를위한 작업을 수행합니다
    // e.g. bind()에서 추가 된 이벤트 리스너 제거
    }
})
```

일단 등록 된 후에는 다음과 같이 Vue.js 템플릿에서 사용할 수 있습니다 (`v-` 접두사를 추가하는 것을 잊지 마세요) :

``` html
<div v-my-directive="someValue"></div>
```

`update` 함수 만 필요한 경우 정의 객체 대신 함수를 하나 전달할 수 있습니다:

``` js
Vue.directive('my-directive', function (value) {
  // 이 함수는 업데이트시 호출됩니다
})
```

### 지시어 인스턴스 속성

모든 훅 함수는 실제로 **지시어 객체(directive object)**로 복사됩니다. 지시어 객체는 훅 함수 내부에서 `this` 컨텍스트로 접근할 수 있습니다. 이 지시문 객체는 몇 가지 유용한 속성을 가지고 있습니다:

- **el**: 지시어가 바인딩할 엘리먼트.
- **vm**: 이 지시어을 소유하는 ViewModel.
- **expression**: 바인딩 표현식, 아규먼트와 필터는 포함히지 않습니다.
- **arg**: 존재하는 경우, 아규먼트.
- **name**: 지시어의 이름, 접두어를 사용하지 않습니다.
- **modifiers**: 어떤 수식을 포함하는 객체.
- **descriptor**: 전체 지시어의 분석을 포함하는 객체.
- **params**: param 속성들을 가진 객체. [Explained below](#params).

<p class="tip">이러한 모든 속성은 읽기 전용(read-only)으로 다루어져야 합니다. 사용자 정의 속성을 지시문 개체에 추가 할 수 있지만 의도하지 않고 기존의 내부 속성을 덮어 쓸 수 있으니 주의해야합니다.</p>

일부 속성을 사용하여 사용자 정의 지시어의 예:

``` html
<div id="demo" v-demo:hello.a.b="msg"></div>
```

``` js
Vue.directive('demo', {
  bind: function () {
    console.log('demo bound!')
  },
  update: function (value) {
    this.el.innerHTML =
      'name - '       + this.name + '<br>' +
      'expression - ' + this.expression + '<br>' +
      'argument - '   + this.arg + '<br>' +
      'modifiers - '  + JSON.stringify(this.modifiers) + '<br>' +
      'value - '      + value
  }
})
var demo = new Vue({
  el: '#demo',
  data: {
    msg: 'hello!'
  }
})
```

**결과**

<div id="demo" v-demo:hello.a.b="msg"></div>
<script>
Vue.directive('demo', {
  bind: function () {
    console.log('demo bound!')
  },
  update: function (value) {
    this.el.innerHTML =
      'name - ' + this.name + '<br>' +
      'expression - ' + this.expression + '<br>' +
      'argument - ' + this.arg + '<br>' +
      'modifiers - '  + JSON.stringify(this.modifiers) + '<br>' +
      'value - ' + value
  }
})
var demo = new Vue({
  el: '#demo',
  data: {
    msg: 'hello!'
  }
})
</script>

### 객체 리터럴

지시어에 여러 값이 필요하면 JavaScript 객체 리터럴도 전달할 수 있습니다. 지시어가 어떤 JavaScript 표현식을 취할 수 있는 지 기억하십시오:

``` html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

``` js
Vue.directive('demo', function (value) {
  console.log(value.color) // "white"
  console.log(value.text) // "hello!"
})
```

### 리터럴 변경자

지시어에 리터럴 변경자(literal modifier)가 사용될 때, 속성 값은 일반 문자열로 해석되고, 직접 `update` 메소드에 전달됩니다. `update` 메소드는 일반 문자열은 재 활성화 할 수 없기 때문에 한 번만 호출됩니다.

``` html
<div v-demo.literal="foo bar baz">
```
``` js
Vue.directive('demo', function (value) {
  console.log(value) // "foo bar baz"
})
```

### 엘리먼트 지시어

어떤 경우에는 속성으로보다 오히려 사용자 정의 엘리먼트 형태로 지시어를 사용하고 싶은 경우가 있습니다. 이것은 Angular "E"모드 지시어의 개념과 매우 유사합니다. 엘리먼트 지시어(element directive)는 가벼운 대안을 구성 요소로 제공합니다 (가이드의 뒷부분에 설명되어 있습니다). 사용자 정의 엘리먼트를 지시어처럼 등록 할 수 있습니다:

``` js
Vue.elementDirective('my-directive', {
  // 표준 지시문과 같은 동일한 API
  bind: function () {
    // this.el을 조작 ...
  }
})
```

이 때, 다음 대신에:

``` html
<div v-my-directive></div>
```

이렇게 사용할 수 있습니다:

``` html
<my-directive></my-directive>
```

엘리먼트 지시어는 아규먼트 또는 표현식을 처리할 수 없습니다. 그러나 그 행동을 결정하는 엘리먼트의 속성을 읽을 수 있습니다.

표준 지시어와의 큰 차이는 엘리먼트 지시어는 **마지막**에서 Vue 한 번 요소 지시어가 발생하는 것을 의미합니다. 엘리먼트와 그 자식을 남겨두고 엘리먼트 지시어 자체 엘리먼트와 그 자식을 조작 할 수 있습니다.

## 추가 옵션들

### params

사용자 정의 지시어는 `params` 배열을 제공 하며 Vue 컴파일러는 자동으로 바인딩 된 엘리먼트에서 해당 속성을 추출합니다. 예:

``` html
<div v-example a="hi"></div>
```

``` js
Vue.directive('example', {
  params: ['a'],
  bind: function () {
    console.log(this.params.a) // -> "hi"
  }
})
```

이 API는 동적 속성도 지원합니다. `this.params[key]` 값은 자동으로 최신 상태로 유지 됩니다. 또한, 값이 변경되었을 때 콜백을 지정할 수 있습니다 :

``` html
<div v-example v-bind:a="someValue"></div>
```
``` js
Vue.directive('example', {
  params: ['a'],
  paramWatchers: {
    a: function (val, oldVal) {
      console.log('a changed!')
    }
  }
})
```

<p class="tip">지시어 params는 JavaScript 및 HTML에서 동일 camelCase <=> kebab-case 매핑됩니다. props또한 같습니다. 예를 들어, 템플릿에서 `disable-effect`로 param을 사용하기 위해서는, JavaScript로 `disableEffect`으로 그것에 접근해야합니다.</p>


### deep

만약 사용자 정의 지시어에서 객체를 다루고 싶은 경우에, 객체의 내부에서 중첩 된 속성이 변경되었을 경우 `update`를 호출하려면 지시어에 `deep:true`를 전달해야합니다.

``` html
<div v-my-directive="obj"></div>
```

``` js
Vue.directive('my-directive', {
  deep: true,
  update: function (obj) {
    //`obj` 안에 중첩 된 속성이 변경된 때 호출된다
  }
})
```

### twoWay

당신의 지시어가 Vue 인스턴스에 데이터를 다시 보낼 경우 `twoWay:true`를 전달해야합니다. 이 옵션은 지시어 내부에서 `this.set(value)`를 사용할 수 있습니다:

``` js
Vue.directive('example', {
  twoWay: true,
  bind: function () {
    this.handler = function () {
      // vm에 데이터를 설정합니다
      // 만약 지시어가 v-example="a.b.c" 로 바운드 되면,
      // 주어진 값을 `vm.a.b.c`에
      // 값을 설정하도록 시도합니다
      this.set(this.el.value)
    }.bind(this)
    this.el.addEventListener('input', this.handler)
  },
  unbind: function () {
    this.el.removeEventListener('input', this.handler)
  }
})
```

### acceptStatement

`acceptStatement:true`를 사용자 정의 지시어에 전달하면 `v-on`와 같은 인라인 구문을 쓸 수 있습니다.

``` html
<div v-my-directive="a++"></div>
```

``` js
Vue.directive('my-directive', {
  acceptStatement: true,
  update: function (fn) {
    // 호출 될 때 전달 된 값은 함수입니다
    // 함수 "a++" 구문을
    // 소유자 vm 범위에서 실행합니다
  }
})
```

하지만 템플릿의 사이드 이펙트를 피하기 위해 현명하게 사용해야 합니다.

### terminal

> 1.0.19+

Vue는 DOM 트리를 재귀적으로 순회하여 템플릿을 컴파일합니다. 그러나 컴파일 과정에서 **terminal** 지시어가 발견된 경우 요소의 자식을 순회하는 것을 중지합니다. terminal 지시어는 요소와 그 아이의 컴파일 작업을 수행합니다. 예를 들면, `v-if`와 `v-for`는 모두 터미널 지시어입니다.

사용자 정의 지시어를 구현하는 것은 고급 주제이며, Vue 컴파일 파이프 라인의 지식이 필요하지만 터미널 지시어를 구현하는 것이 가능합니다. `terminal:true`를 지정하여 사용자 정의 터미널 지시문을 지정 할 수 있습니다. 또한 아마 부분적인 컴파일에 `Vue.Fragmentfactory`을 사용해야합니다. 여기에서는 컴파일 및 페이지의 다른 위치에 콘텐츠 템플릿을 "주입하는" 사용자 정의 터미널 지시어의 예입니다 :

``` js
var FragmentFactory = Vue.FragmentFactory
var remove = Vue.util.remove
var createAnchor = Vue.util.createAnchor

Vue.directive('inject', {
  terminal: true,
  bind: function () {
    var container = document.getElementById(this.arg)
    this.anchor = createAnchor('v-inject')
    container.appendChild(this.anchor)
    remove(this.el)
    var factory = new FragmentFactory(this.vm, this.el)
    this.frag = factory.create(this._host, this._scope, this._frag)
    this.frag.before(this.anchor)
  },
  unbind: function () {
    this.frag.remove()
    remove(this.anchor)
  }
})
```

``` html
<div id="modal"></div>
...
<div v-inject:modal>
  <h1>header</h1>
  <p>body</p>
  <p>footer</p>
</div>
```

사용자 정의 터미널 지시어를 구현하고자하는 경우 Vue 내부의 더 나은 이해를 얻기 위해 `v-if`와 `v-for`와 같은 내장 터미널 지시어의 소스 코드를 읽는 것이 좋습니다.

### priority

지시어에는 임의로 우선 순위 값 (기본값 1000)를 지정할 수 있습니다. 만약 우선 순위를 지정하지 않은 경우 기본 우선 순위가 사용됩니다. 일반 지시어는 `1000`, 그리고 터미널 지시어는 `2000`입니다. 같은 엘리먼트에 높은 우선 순위를 가진 지시어은 다른 지시어보다 빨리 처리됩니다. 같은 우선 순위를 가지는 지시어는 엘리먼트의 속성 목록에 나타나는 순서대로 처리되지만, 브라우저가 다를 일관된 순서를 가진다는 것은 보증되지 않습니다.

일부 내장 지시어에 대한 우선 순위는 [API 문서](/api/#Directives)에서 확인할 수 있습니다. 또한 흐름 제어 지시문 `v-if`와 `v-for` 컴파일 과정에서 항상 가장 높은 우선 순위를 갖습니다.
