---
type: api
---

## 전역 설정

`Vue.config`은 Vue의 전역 설정을 가지고 있는 객체입니다. 아래에 있는 `Vue.config`의 속성들을 사용 전에 변경할 수 있습니다:

### debug

- **자료형:** `Boolean`

- **기본값:** `false`

- **사용방법:**

  ```javascript
  Vue.config.debug = true
  ```

  Vue는 디버그 모드에서:

  1. 모든 경고에 대한 스택트레이스를 출력합니다.

  2. 모든 앵커노드를 볼 수 있도록 해 줍니다. 출력된 결과를 분석하기 쉽게 해줍니다.

  디버그 모드는 개발용 빌드에서만 사용할 수 있습니다.

### delimiters

- **자료형:** `Array<String>`

- **기본값:** `{% raw %}["{{", "}}"]{% endraw %}`

- **사용방법:**

  ```javascript
  // ES6 템플릿 스타일로 변경
  Vue.config.delimiters = ['${', '}']
  ```

  일반 텍스트 구분자(delimiter)를 변경합니다.

### unsafeDelimiters

- **자료형:** `Array<String>`

- **기본값:** `{% raw %}["{{{", "}}}"]{% endraw %}`

- **사용방법:**

  ```javascript
  // 더 위험하게 보이도록 합니다
  Vue.config.unsafeDelimiters = ['{!!', '!!}']
  ```

  원시 HTML을 출력하기 위한 구분자를 변경합니다.

### silent

- **자료형:** `Boolean`

- **기본값:** `false`

- **사용방법:**

  ```javascript
  Vue.config.silent = true
  ```

  모든 Vue.js의 로그와 경고를 나오지 않게 합니다.

### async

- **자료형:** `Boolean`

- **기본값:** `true`

- **사용방법:**

  ```javascript
  Vue.config.async = false
  ```

  비동기모드가 꺼진 상태에는 Vue는 데이터 변경시 모든 DOM 갱신을 동기적으로 처리합니다. 일부 시나리오에서 디버깅에 도움이 됩니다. 하지만 성능을 떨어뜨리고 콜백을 처리하는 watcher에 영향을 줄 수 있습니다. **배포 시 `async : false`는 추천하지 않습니다.**

### devtools

- **자료형:** `Boolean`

- **기본값:** `true` (배포용 빌드는 `false` 입니다.)

- **사용방법:**

  ```javascript
  // Vue가 불러진 이후에 동기적으로 설정됩니다.
  Vue.config.devtools = true
  ```

  [vue-devtools](https://github.com/vuejs/vue-devtools) 사용을 가능하게 합니다. 개발용 빌드는 `true`, 배포용 빌드는 `false`가 기본값입니다. 배포용 빌드에서 `true`로 사용할 수도 있습니다.

## 전역 API

### Vue.extend( options )

- **전달인자:**

  - `{Object} options`

- **사용방법:**

  Vue 생성자의 "서브클래스"를 만듭니다. 전달인자는 컴포넌트 옵션을 반드시 포함해야 합니다.

  `el`과 `data` 옵션은 `Vue.extend()`를 사용할 때 반드시 있어야 합니다.

  ```html
  <div id="mount-point"></div>
  ```

  ```javascript
  // 재사용되는 생성자를 만듭니다.
  var Profile = Vue.extend({
    template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>'
  })
  // Profile 인스턴스를 생성합니다.
  var profile = new Profile({
    data: {
      firstName: 'Walter',
      lastName: 'White',
      alias: 'Heisenberg'
    }
  })
  // 엘리먼트를 마운트합니다.
  profile.$mount('#mount-point')
  ```

  이렇게 렌더링 됩니다.

  ```html
  <p>Walter White aka Heisenberg</p>
  ```

- **참고하세요:** [컴포넌트](/guide/components.html)

### Vue.nextTick( callback )

- **전달인자:**

  - `{Function} callback`

- **사용방법:**

  callback을 연기하고 DOM의 업데이트 주기를 마친에 실행합니다. DOM 업데이트를 하려면 몇 가지 데이터를 변경 한 직후에 사용하십시오.

  ```javascript
    // modify data
    vm.msg = 'Hello'
    // DOM not updated yet
    Vue.nextTick(function () {
      // DOM updated
    })
  ```

- **참고하세요:** [비동기 업데이트 큐](/guide/reactivity.html#Async-Update-Queue)

### Vue.set( object, key, value )

- **전달인자:**

  - `{Object} object`
  - `{String} key`
  - `{*} value`

- **반환 값:** 설정한 값

- **사용방법:**

  객체의 속성을 설정합니다. 오브젝트가 반응형이면 속성이 반응형 속성으로 생성되는 것을 보장하고 View 업데이트를 트리거합니다. 이것은 주로 Vue이 속성 추가를 감지 할 수 없다는 제약을 피하기 위해 사용됩니다.

- **참고하세요:** [반응성에 대해 깊이 알기](/guide/reactivity.html)

### Vue.delete( object, key )

- **전달인자:**

  - `{Object} object`
  - `{String} key`

- **사용방법:**

  객체의 속성을 삭제합니다. 객체가 반응형이면 View 갱신을 보증합니다. 이것은 주로 Vue이 속성의 삭제를 감지 할 수 없는 제약을 피하기 위해 사용되지만 사용해야하는 이유 거의 없습니다.

- **참고하세요:** [반응성에 대해 깊이 알기](/guide/reactivity.html)

### Vue.directive( id, [definition] )

- **전달인자:**

  - `{String} id`
  - `{Function | Object} [definition]`

- **사용방법:**

  전역 지시어를 검색하거나 등록합니다.

  ```javascript
  // register
  Vue.directive('my-directive', {
    bind: function () {},
    update: function () {},
    unbind: function () {}
  })

  // 등록 (간단한 함수 지시어)
  Vue.directive('my-directive', function () {
    // this will be called as `update`
  })

  // 등록되어 있으면 지시문 정의를 반환
  var myDirective = Vue.directive('my-directive')
  ```

- **참고하세요:** [사용자 정의 지시어](/guide/custom-directive.html)

### Vue.elementDirective( id, [definition] )

- **전달인자:**

  - `{String} id`
  - `{Object} [definition]`

- **사용방법:**

  전역 엘리먼트 지시어를 검색하거나 등록합니다.

  ```javascript
  // 등록
  Vue.elementDirective('my-element', {
    bind: function () {},
    // 엘리먼트 지시어에서 `update`를 사용할 수 없습니다.
    unbind: function () {}
  })

  // getter, 등록되어 있으면 지시문 정의를 반환합니다
  var myDirective = Vue.elementDirective('my-element')
  ```

- **참고하세요:** [엘리먼트 지시어](/guide/custom-directive.html#Element-Directives)

### Vue.filter( id, [definition] )

- **전달인자:**

  - `{String} id`
  - `{Function | Object} [definition]`

- **사용방법:**

  전역 필터를 검색하거나 등록합니다.

  ```javascript
  // 등록
  Vue.filter('my-filter', function (value) {
    // 처리된 결과값을 반환합니다
  })

  // 양방향 필터
  Vue.filter('my-filter', {
    read: function () {},
    write: function () {}
  })

  // getter, 등록 되어있으면 반환합니다
  var myFilter = Vue.filter('my-filter')
  ```

- **참고하세요:** [사용자 정의 필터](/guide/custom-filter.html)

### Vue.component( id, [definition] )

- **전달인자:**

  - `{String} id`
  - `{Function | Object} [definition]`

- **사용방법:**

  전역 컴포넌트를 검색하거나 등록합니다.

  ```javascript
  // 확장된 생성자 등록
  Vue.component('my-component', Vue.extend({ /* ... */ }))

  // 옵션 객체를 등록 (Vue.extend을 자동으로 호출한다)
  Vue.component('my-component', { /* ... */ })

  // 등록된 컴포넌트를 얻습니다 (항상 생성자를 반환합니다)
  var MyComponent = Vue.component('my-component')
  ```

- **참고하세요:** [컴포넌트](/guide/components.html).

### Vue.transition( id, [hooks] )

- **전달인자:**

  - `{String} id`
  - `{Object} [hooks]`

- **사용방법:**

  전역 트랜지션 훅 객체를 검색하거나 등록합니다.

  ```javascript
  // 등록
  Vue.transition('fade', {
    enter: function () {},
    leave: function () {}
  })

  // 등록된 훅을 반환합니다
  var fadeTransition = Vue.transition('fade')
  ```

- **참고하세요:** [트랜지션](/guide/transitions.html).

### Vue.partial( id, [partial] )

- **전달인자:**

  - `{String} id`
  - `{String} [partial]`

- **사용방법:**

  전역 템플릿 조각을 검색하거나 등록합니다.

  ```javascript
  // 등록
  Vue.partial('my-partial', '<div>Hi</div>')

  // 등록된 템플릿 조각을 불러옵니다.
  var myPartial = Vue.partial('my-partial')
  ```

- **참고하세요:** [특수 엘리먼트 - partial](#partial)

### Vue.use( plugin, [options] )

- **전달인자:**

  - `{Object | Function} plugin`
  - `{Object} [options]`

- **사용방법:**

  Vue.js 플러그인을 설치합니다. plugin이 객체라면 `install` 메소드를 구현해야합니다. 그 자체가 함수라면, 그것은 `install` 메소드로 처리됩니다. 설치 방법은 Vue를 전달인자로 전달합니다.

- **참고하세요:** [플러그인](/guide/plugins.html).

### Vue.mixin( mixin )

- **전달인자:**

  - `{Object} mixin`

- **사용방법:**

  모든 Vue 인스턴스가 생성된 후 믹스인(mixin)을 전체적으로 적용합니다. 이 컴포넌트에 사용자 지정 작업을 주입하기 위해 플러그인 작성자가 사용할 수 있습니다. **애플리케이션 코드의 사용은 권장되지 않습니다.**

- **참고하세요:** [Global Mixins](/guide/mixins.html#Global-Mixin)

## 옵션 / 데이터

### data

- **자료형:** `Object | Function`

- **제한:** 컴포넌트 정의에 사용하는 경우 `Function` 타입만 받습니다.

- **상세:**

  Vue 인스턴스에 대한 데이터 객체입니다. Vue.js는 재귀적으로 인스턴스의 속성을 getter/setter를 "반응형"으로 만듭니다. **객체는 일반 네이티브 객체이어야합니다.** 기존의 getter/setter 및 프로토타입 속성은 무시됩니다. 복잡한 개체를 모니터링하는 것은 권장되지 않습니다.

  일단 인스턴스가 생성되면 원시 데이터 객체는 `vm.$data`로 사용할 수 있습니다. Vue 인스턴스는 데이터 객체에서 발견된 모든 속성에 대신 액세스합니다.

  Vue의 내부 속성이나 API 메소드와 충돌할 가능성이 있기 때문에 `_` 또는 `$`부터 시작하는 속성은 Vue 인스턴스 프록시되지 않는 점에 유의하십시오. 그들은 `vm.$data._property`으로 액세스 할 수 있습니다.

  `컴포넌트`를 정의 할 때 같은 정의를 사용하여 만들어진 객체가 많은 경우가 있기 때문에 data는 초기 데이터 객체를 반환하는 함수로 선언해야합니다. `data`에 일반 객체를 사용하는 경우 동일한 개체가 생성된 모든 인스턴스 전체를 통과하여 **참조에 의해 공유** 됩니다! `data` 함수를 제공함으로써 새 인스턴스가 생성될 때마다 단순히 그것은 초기 데이터의 새 복사본을 반환하는 함수로 호출 할 수 있습니다.

  필요한 경우 원래 데이터 객체 깊은 복사 (deep clone)는 `vm.$data`를 전달하여 `JSON.parse(JSON.stringify (...))`를 통해 얻을 수 있습니다.

- **예제**

  ```javascript
  var data = { a: 1 }

  // 직접 객체를 만듭니다.
  var vm = new Vue({
    data: data
  })
  vm.a // -> 1
  vm.$data === data // -> true

  // Vue.extend()를 사용할때 function을 이용해야 합니다.
  var Component = Vue.extend({
    data: function () {
      return { a: 1 }
    }
  })
  ```

- **참고하세요:** [반응성에 대해 깊이 알기](/guide/reactivity.html).

### props

- **자료형:** `Array | Object`

- **상세:**

  부모 컴포넌트로부터 데이터를 받기 위하여 보내진 속성 목록/해시입니다. 간단한 배열 기반의 구문 및 형식 검사, 사용자 지정 유효성 검사 그리고 기본값 등의 고급 설정을 가능하게하는 배열 기반의 대체 객체 기반 구문이 있습니다.

- **예제**

  ```javascript
  // 간단한 구문
  Vue.component('props-demo-simple', {
    props: ['size', 'myMessage']
  })

  // 객체 문법과 검증
  Vue.component('props-demo-advanced', {
    props: {
      // 간단한 형식 검사
      size: Number,
      // 형식 검사와 기타 검증
      name: {
        type: String,
        required: true,
        // 양방향 바인딩임을 명시
        twoWay: true
      }
    }
  })
  ```

- **참고하세요:** [Props](/guide/components.html#Props)

### propsData

> 1.0.22+

- **자료형:** `Object`

- **제한:** `new` 통해서만 인스턴스가 만들어집니다.

- **상세:**

  인스턴스 생성 중에 props에 전달합니다. 단위 테스트를 쉽게하는 것이 주 목적입니다.

- **예제**

  ```javascript
  var Comp = Vue.extend({
    props: ['msg'],
    template: '<div>{{ msg }}</div>'
  })

  var vm = new Comp({
    propsData: {
      msg: 'hello'
    }
  })
  ```

### computed

- **자료형:** `Object`

- **상세:**

  Vue 인스턴스에 포함되는 계산된 속성(Computed property)입니다. 모든 getter 및 setter 자동으로 Vue 인스턴스에 바인딩 된 `this` 컨텍스트를가집니다.

- **예제**

  ```javascript
  var vm = new Vue({
    data: { a: 1 },
    computed: {
      // get만 가능
      aDouble: function () {
        return this.a * 2
      },
      // get과 set 모두 가능
      aPlus: {
        get: function () {
          return this.a + 1
        },
        set: function (v) {
          this.a = v - 1
        }
      }
    }
  })
  vm.aPlus   // -> 2
  vm.aPlus = 3
  vm.a       // -> 2
  vm.aDouble // -> 4
  ```

- **참고하세요:**

  - [계산된 속성](/guide/computed.html)
  - [반응성에 대해 깊이 알기: 계산된 속성 상세](/guide/reactivity.html#Inside-Computed-Properties)

### methods

- **자료형:** `Object`

- **상세:**

  Vue 인스턴스에 포함되는 메소드입니다. VM 인스턴스에서는 이러한 메소드에 직접 액세스할 수 있는 지시어 표현식으로 사용할 수 있습니다. 모든 메소드는 Vue 인스턴스에 자동으로 바인딩 된 `this` 컨텍스트를가집니다.

- **예제**

  ```javascript
  var vm = new Vue({
    data: { a: 1 },
    methods: {
      plus: function () {
        this.a++
      }
    }
  })
  vm.plus()
  vm.a // 2
  ```

- **참고하세요:** [메소드와 이벤트 핸들링](/guide/events.html)

### watch

- **자료형:** `Object`

- **상세:**

  키가 모니터링하는 표현식으로 값이 대응하는 콜백을 가진 객체입니다. 값은 메소드 이름의 문자열 또는 추가 옵션이 포함 된 개체를 취할 수 있습니다. Vue 인스턴스는 인스턴스화 할 때 개체의 각 항목에 대해 `$watch()`를 호출합니다.

- **예제**

  ```javascript
  var vm = new Vue({
    data: {
      a: 1
    },
    watch: {
      'a': function (val, oldVal) {
        console.log('new: %s, old: %s', val, oldVal)
      },
      // 메소드 이름 문자열
      'b': 'someMethod',
      // 깊은 watcher
      'c': {
        handler: function (val, oldVal) { /* ... */ },
        deep: true
      }
    }
  })
  vm.a = 2 // -> new: 2, old: 1
  ```

- **참고하세요:** [인스턴스 메소드 - vm.$watch](#vm-watch)

## 옵션 / DOM

### el

- **자료형:** `String | HTMLElement | Function`

- **제한:** 컴포넌트 정의에서 사용하는 경우 `Function` 타입만 받아들입니다.

- **상세:**

  기존의 DOM 요소에 Vue 인스턴스를 제공합니다. CSS 선택기 문자열, 실제 HTML 요소 또는 HTML 요소를 반환하는 함수를 가질 수 있습니다. 단순히 마운팅 포인트로 유용한 요소가 제공되는 점에 유의하십시오. `replace`가 `false`로 설정되어 있지 않으면 템플릿이 제공되는 경우 바뀝니다. 해결 된 요소는 `vm.$el`으로 액세스 할 수 있습니다.

  `Vue.extend`에서 사용되는 경우 각 인스턴스가 독립적으로 엘리먼트를 만드는 함수가 필요합니다.

  인스턴스화 할 때 옵션을 사용하면 해당 인스턴스는 즉시 컴파일 단계에 들어갑니다. 그렇지 않으면, 사용자가 컴파일을 시작하기에 명시적으로 `vm.$mount()`를 호출해야합니다.

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

### template

- **자료형:** `String`

- **상세:**

  Vue 인스턴스에 대해 마크업으로 사용하는 문자열의 템플릿입니다. 기본적으로 템플릿은 마운트 된 엘리먼트로 **대체** 됩니다. `replace` 옵션이 `false` 로 설정되는 경우는 반대로 마운트 된 엘리먼트에 삽입됩니다. 두 경우에서 컨텐츠 삽입 위치가 템플릿에 없으면 마운트 된 엘리먼트 내부의 모든 기존의 태그는 무시됩니다.

  `#`로 문자열로 시작하는 경우 querySelector로 사용되어 선택된 엘리먼트의 innerHTML을 템플릿 문자열로 사용합니다. 그러면 템플릿 통합을 위해 공통의 `<script type = "x-template">` 방식을 사용할 수있게됩니다.

  템플릿이 한개의 최상위 이상의 노드를 포함하는 경우, 인스턴스는 파편화된 인스턴스인 것에 주의하십시오. 즉 그것은 단일 노드가 아니라 노드의 목록이 됩니다. 파편화된 인스턴스가 마운트된 위치에있는 비 `flow-control` 지시어는 무시됩니다.

- **참고하세요:**

  - [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)
  - [Slot과 컨텐츠 배포](/guide/components.html#Content-Distribution-with-Slots)
  - [인스턴트 파편화](/guide/components.html#Fragment-Instance)

### replace

- **자료형:** `Boolean`

- **기본값:** `true`

- **제한:** **템플릿** 옵션이 존재하는 경우에만 작동합니다.

- **상세:**

마운트된 엘리먼트를 템플릿으로 대체할지 여부를 의미합니다. `false`로 설정하는 경우 템플릿 콘텐츠 내부의 엘리먼트를 바꾸지 않고 덮어 씁니다. `true`로 설정하는 경우 템플릿 엘리먼트와 컴포넌트의 root 노드의 엘리먼트의 속성을 병합합니다.

- **예제**:

  ```html
  <div id="replace" class="foo"></div>
  ```

  ```javascript
  new Vue({
    el: '#replace',
    template: '<p class="bar">replaced</p>'
  })
  ```

  아래처럼 됩니다:

  ```html
  <p class="foo bar" id="replace">replaced</p>
  ```

  `replace`가 `false` 이면:

  ```html
  <div id="insert" class="foo"></div>
  ```

  ```javascript
  new Vue({
    el: '#insert',
    replace: false,
    template: '<p class="bar">inserted</p>'
  })
  ```

  아래처럼 됩니다:

  ```html
  <div id="insert" class="foo">
    <p class="bar">inserted</p>
  </div>
  ```

## 옵션 / 라이프사이클 훅

### init

- **자료형:** `Function`

- **상세:**

  인스턴스가 초기화될 때와 데이터 모니터링 및 이벤트/watcher가 설정될 때 동기적으로 호출됩니다.

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

### created

- **자료형:** `Function`

- **상세:**

  인스턴스가 생성 된 후 동기적으로 호출됩니다. 이 단계에서는 인스턴스는 다음 설정 옵션의 처리합니다 : data 모니터링, 계산된 속성, 메소드, watch/event 콜백. 그러나, DOM의 컴파일이 시작하지 않고, `$el` 속성은 아직 유효하지 않습니다.

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

### beforeCompile

- **자료형:** `Function`

- **상세:**

  컴파일 시작 직전에 호출됩니다.

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

### compiled

- **자료형:** `Function`

- **상세:**

  컴파일이 종료되고 호출됩니다. 이 상태에서 모든 지시어는 DOM과 연결되어 데이터가 변경되는 것을 감지할 수 있습니다. 그러나 `$el`이 추가된 것을 보장하지는 않습니다.

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

### ready

- **자료형:** `Function`

- **상세:**

  컴파일이 종료고 `$el`이 **최초로 문서에 추가된 후** 호출됩니다. 다시말해 첫번째 `attached` 훅 바로 다음에 발생합니다. 반드시 `ready` 훅이 실행할 수 있게 Vue(`vm.$appendTo()`를 통해 추가되거나 지시어 업데이트까 끝났을때)에 실행합니다.

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

### attached

- **자료형:** `Function`

- **상세:**

  `vm.$el`이 지시문 또는 VM 인스턴스 또는 `$appendTo()`와 같은 VM 인스턴스의 메소드에 의해 DOM에 추가 된 때 호출됩니다. `vm.$el`을 직접 조작하면 이 훅이 호출되지 않습니다.

### detached

- **자료형:** `Function`

- **상세:**

  지시문 또는 VM 인스턴스의 메소드에 의해 DOM에서 `vm.$el`가 삭제 된 경우 호출됩니다. 지시어 `vm.$el`을 직접 조작하면 훅이 실행되지 않습니다.

### beforeDestroy

- **자료형:** `Function`

- **상세:**

  Vue 인스턴스가 제가되기 직전에 호출됩니다. 이 상태에서 인스턴스는 여전히 모든 작업을 할 수 있습니다.

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

### destroyed

- **자료형:** `Function`

- **상세:**

  Vue 인스턴스가 제거된 후 호출됩니다. 이 훅이 호출 될 때, Vue 인스턴스의 모든 바인딩과 지시어는 제거되고 모든 자식 Vue 인스턴스도 삭제됩니다.

  leave 트랜지션이 존재하는 경우, `destroyed` 후크 전환이 끝난 **후** 호출합니다.

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

## 옵션 / 에셋

### directives

- **자료형:** `Object`

- **상세:**

  Vue 인스턴스에서 사용할 수있는 지시어의 해시입니다.

- **참고하세요:**

  - [사용자 정의 지시어](/guide/custom-directive.html)
  - [에셋 명명 규칙](/guide/components.html#Assets-Naming-Convention)

### elementDirectives

- **자료형:** `Object`

- **상세:**

  Vue 인스턴스에서 사용할 수 있는 엘리먼트 지시어의 해시입니다.

- **참고하세요:**

  - [엘리먼트 지시어](/guide/custom-directive.html#Element-Directives)
  - [에셋 명명 규칙](/guide/components.html#Assets-Naming-Convention)

### filters

- **자료형:** `Object`

- **상세:**

  Vue 인스턴스에서 사용할 수 있는 필터의 해시입니다.

- **참고하세요:**

  - [사용자 정의 필터](/guide/custom-filter.html)
  - [에셋 명명 규칙](/guide/components.html#Assets-Naming-Convention)

### components

- **자료형:** `Object`

- **상세:**

  Vue 인스턴스에서 사용할 수 있는 컴포넌트의 해시입니다.

- **참고하세요:**

  - [컴포넌트](/guide/components.html)

### transitions

- **자료형:** `Object`

- **상세:**

  Vue 인스턴스에서 사용할 수 있는 트랜지션의 해시입니다.

- **참고하세요:**

  - [트랜지션](/guide/transitions.html)

### partials

- **자료형:** `Object`

- **상세:**

  Vue 인스턴스에서 사용할 수 있는 partial의 해시입니다.

- **참고하세요:**

  - [Special Elements - partial](#partial)

## 옵션 / 기타

### parent

- **자료형:** `Vue instance`

- **상세:**

  만들어진 인스턴스의 부모 인스턴스를 지정합니다. 두 인스턴스 사이에 부자 관계를 설정합니다. 부모는 자식의 `this.$parent`로 접근 가능하며, 자식은 부모의 `$children` 배열에 추가됩니다.

- **참고하세요:** [부모 자식간 통신](/guide/components.html#Parent-Child-Communication)

### events

- **자료형:** `Object`

- **상세:**

  키를 수신하는 이벤트이며, 값은 해당 콜백을 가진 객체입니다. DOM 이벤트가 아닌 Vue 이벤트입니다. 값은 메소드 이름의 문자열을 사용합니다. Vue인스턴스는 인스턴스화 할 때 객체의 각 항목에 대해 `$on()`을 부릅니다.

- **예제**

  ```javascript
  var vm = new Vue({
    events: {
      'hook:created': function () {
        console.log('created!')
      },
      greeting: function (msg) {
        console.log(msg)
      },
      // 메소드 이름의 문자열로 쓸 수 있습니다
      bye: 'sayGoodbye'
    },
    methods: {
      sayGoodbye: function () {
        console.log('goodbye!')
      }
    }
  }) // -> 만들었습니다!
  vm.$emit('greeting', 'hi!') // -> hi!
  vm.$emit('bye')             // -> goodbye!
  ```

- **참고하세요:**

  - [인스턴스 메소드 - 이벤트](#Instance-Methods-Events)
  - [부모-자식간 통신](/guide/components.html#Parent-Child-Communication)

### mixins

- **자료형:** `Array`

- **상세:**

  `mixins` 옵션은 믹스인 객체의 배열을 받습니다. 믹스인 객체는 보통의 인스턴스 객체와 같은 인스턴스 옵션을 포함할 수 있으며, `Vue.extend()`에서 동일한 옵션을 합치는 로직을 사용한 결과 옵션에 병합됩니다. 예를 들어, 당신의 믹스인이 만든 훅을 가지고 컴포넌트 자체도 그것을 가지고 있었을 경우, 두 함수가 호출됩니다.

  믹스인 훅은 제공된 순서대로 호출되고 컴퍼넌트 자신의 훅 전에 호출됩니다.

- **예제**

  ```javascript
  var mixin = {
    created: function () { console.log(1) }
  }
  var vm = new Vue({
    created: function () { console.log(2) },
    mixins: [mixin]
  })
  // -> 1
  // -> 2
  ```

- **참고하세요:** [믹스인](/guide/mixins.html)

### name

- **자료형:** `String`

- **제한:** `Vue.extend()`에서 사용된 경우에만 유효합니다.

- **상세:**

  템플릿에서 컴포넌트 자체의 재귀 호출을 허용합니다. 구성 요소는 `Vue.component()`에 등록 된 전역ID는 그 이름으로 자동 설정되는 것에 주의하십시오.

  `name` 옵션의 또 다른 장점은 콘솔에서 확인할 때입니다. 확장된 Vue 구성 요소를 콘솔에 관리자로 볼 때 디폴트 생성자 이름은 `VueComponent`입니다. 이것은 정확히 알 수 없습니다. `name` 옵션을 `Vue.extend()`에 전달하여 더 좋은 콘솔 출력을 얻을 수있어 지금 보고있는 컴포넌트를 알 수 있습니다. 문자열은 카멜 케이스 화 된 생성자 이름에 사용됩니다.

- **예제**

  ```javascript
  var Ctor = Vue.extend({
    name: 'stack-overflow',
    template:
      '<div>' +
        // 자기 자신을 재귀 호출합니다.
        '<stack-overflow></stack-overflow>' +
      '</div>'
  })

  // 최대 스택사이즈까지 호출됩니다.
  // 오류가 발생하지만 작동한다고 가정했을때...
  var vm = new Ctor()

  console.log(vm) // -> StackOverflow {$el: null, ...}
  ```

### extends

> 1.0.22+

- **자료형:** `Object | Function`

- **상세:**

  `Vue.extend`을 사용하지 않고 다른 컴포넌트를 선언적으로 확장 할 수 있습니다.(기본 옵션 객체 또는 생성자의 어디라도 상관 없습니다). 이것은 주로 단일 파일 구성 요소에 쉬운 확장을 목적으로하고 있습니다.

  이것은 `mixins`과 유사하며, 차이점은 컴포넌트 자체의 옵션은 원래의 컴포넌트가 확장되는 것보다 우선 하는것이 차이점입니다.

- **예제**

  ```javascript
  var CompA = { ... }

  // Vue.extend를 사용하지 않고 CompA를 확장할 수 있습니다
  var CompB = {
    extends: CompA,
    ...
  }
  ```

## 인스턴스 속성

### vm.$data

- **자료형:** `Object`

- **상세:**

  Vue 인스턴스가 감시하고있는 데이터 객체. 새로운 객체로 변경할 수 있습니다. Vue 인스턴스 프록시는 데이터객체의 속성에 액세스합니다.

### vm.$el

- **자료형:** `HTMLElement`

- **읽기 전용**

- **상세:**

  Vue 인스턴스가 관리하는 DOM 요소. 이것은 [인스턴스 파편화](/guide/components.html#Fragment-Instance)가 되므로 주의가 필요하며 `vm.$el`은 파편화 시작 위치를 나타내는 앵커 노드를 반환합니다.

### vm.$options

- **자료형:** `Object`

- **읽기 전용**

- **상세:**

  현재 Vue 인스턴스에 대한 옵션으로 사용됩니다. 이 옵션에 사용자 정의 속성을 포함할 때 유용합니다:

  ```javascript
  new Vue({
    customOption: 'foo',
    created: function () {
      console.log(this.$options.customOption) // -> 'foo'
    }
  })
  ```

### vm.$parent

- **자료형:** `Vue instance`

- **읽기 전용**

- **상세:**

  현재 인스턴스가 하나있는 경우 부모의 인스턴스.

### vm.$root

- **자료형:** `Vue instance`

- **읽기 전용**

- **상세:**

  현재의 컴포넌트 트리의 root Vue 인스턴스. 현재 인스턴스가 부모가 아닌 경우, 이 값은 그 자기 자신입니다.

### vm.$children

- **자료형:** `Array<Vue instance>`

- **읽기 전용**

- **상세:**

  현재 인스턴스의 직접적인 자식 컴포넌트.

### vm.$refs

- **자료형:** `Object`

- **읽기 전용**

- **상세:**

  `v-ref`에 등록 된 자식 컴퍼넌트를 유지하는 개체입니다.

- **참고하세요:**

  - [자식 컴포넌트 참조](/guide/components.html#Child-Component-Refs)
  - [v-ref](#v-ref).

### vm.$els

- **자료형:** `Object`

- **읽기 전용**

- **상세:**

  `v-el`에 등록 된 DOM 요소를 유지하는 개체입니다.

- **참고하세요:** [v-el](#v-el).

## 인스턴스 메소드 / 데이터

### vm.$watch( expOrFn, callback, [options] )

- **전달인자:**

  - `{String | Function} expOrFn`
  - `{Function} callback`
  - `{Object} [options]`

    - `{Boolean} deep`
    - `{Boolean} immediate`

- **반환 값:** `{Function} unwatch`

- **사용방법:**

  Vue 인스턴스에서 하나의 표현식 또는 계산된 함수 (computed function)의 변경을 감시합니다. 콜백은 새로운 값과 이전 값과 함께 불려야 합니다. 표현식에는 단일 keypath 또는 유효한 바인딩 식을 넣을 수 있습니다.

  <p class="tip">객체 또는 배열을 변경(또는 대체)할 때, 그들은 같은 객체/배열을 참조하기 위해 이전 값은 새로운 값과 동일하다는 점에 유의하십시오. Vue는 이전 값을 복사하지 않습니다.</p>

- **예제**

  ```javascript
  // keypath
  vm.$watch('a.b.c', function (newVal, oldVal) {
    // 무언가 하세요
  })

  // 표현식
  vm.$watch('a + b', function (newVal, oldVal) {
    // 무언가 하세요
  })

  // 함수
  vm.$watch(
    function () {
      return this.a + this.b
    },
    function (newVal, oldVal) {
      // 무언가 하세요
    }
  )
  ```

  `vm.$watch`는 콜백의 실행을 중지하는 unwatch 함수를 반환합니다.

  ```javascript
  var unwatch = vm.$watch('a', cb)
  // 나중에, watcher를 제거합니다
  unwatch()
  ```

- **옵션: deep**

  객체 중 중첩 된 값의 변경을 감지하려면 옵션 전달인자 `deep : true`를 전달해야합니다. Array 값의 변경은 알아야 할 필요가 없다는 점에 유의하십시오.

  ```javascript
  vm.$watch('someObject', callback, {
    deep: true
  })
  vm.someObject.nestedValue = 123
  // 콜백이 불립니다
  ```

- **옵션: immediate**

  options 전달인자 `immediate: true`를 전달하면 그 때의 표현식의 값에서 콜백이 즉시 실행됩니다:

  ```javascript
  vm.$watch('a', callback, {
    immediate: true
  })
  // 현재 `a` 값 콜백이 즉시 호출됩니다
  ```

### vm.$get( expression )

- **전달인자:**

  - `{String} expression`

- **사용방법:**

  주어진 표현식을 이용해 Vue 인스턴스에서 값을 가져옵니다. 오류가 발생하는 식은 `undefined`를 반환합니다.

- **예제**

  ```javascript
  var vm = new Vue({
    data: {
      a: {
        b: 1
      }
    }
  })
  vm.$get('a.b') // -> 1
  vm.$get('a.b + 1') // -> 2
  ```

### vm.$set( keypath, value )

- **전달인자:**

  - `{String} keypath`
  - `{*} value`

- **사용방법:**

  Vue 인스턴스의 data의 해당 keypath에 값을 설정합니다. 대부분의 경우 일반 개체 문법 (예 : `vm.a.b = 123`)를 이용하여 속성을 설정하는 것이 좋습니다. 이 방법은 2 가지 시나리오에서만 필요합니다.

  1. 키 패스 문자열이 있고, 키 패스를 사용하여 동적으로 값을 설정하려는 경우.

  2. 존재하지 않는 속성을 설정하려는 경우.

  경로가 존재하지 않는 경우 재귀적으로 생성 된 반응형으로 만듭니다. 새로운 root 수준의 반응형 속성이 `$set`의 호출에 의해 생성 된 경우 Vue 인스턴스는 모든  watcher가 다시 불려지는 "digest cycle"를 강제됩니다.

- **예제**

  ```javascript
  var vm = new Vue({
    data: {
      a: {
        b: 1
      }
    }
  })

  // 존재하는 패스를 설정합니다.
  vm.$set('a.b', 2)
  vm.a.b // -> 2

  // 존재하지 않는 패스를 설정하면 새로 설정합니다.
  vm.$set('c', 3)
  vm.c // -> 3
  ```

- **참고하세요:** [반응성에 대해 깊이 알기](/guide/reactivity.html)

### vm.$delete( key )

- **전달인자:**

  - `{String} key`

- **사용방법:**

  Vue 인스턴스 (그리고, 그 `$data`)의 루트 레벨의 속성을 삭제합니다. digest cycle을 강제합니다. 추천하지 않습니다.

### vm.$eval( expression )

- **전달인자:**

  - `{String} expression`

- **사용방법:**

  현재 인스턴스의 올바른 바인딩 식을 계산합니다. 표현식 필터를 포함 할 수 있습니다.

- **예제**

  ```javascript
  // vm.msg = 'hello'라고 가정할 때
  vm.$eval('msg | uppercase') // -> 'HELLO'
  ```

### vm.$interpolate( templateString )

- **전달인자:**

  - `{String} templateString`

- **사용방법:**

  Mustache를 이용한 템플릿 문자열을 생성합니다. 이 메소드는 단순히 문자열을 생성하기만 합니다. 즉, 속성을 가지고 지시어는 컴파일되지 않습니다.

- **예제**

  ```javascript
  // vm.msg = 'hello'라고 가정할 때
  vm.$interpolate('{{msg}} world!') // -> 'hello world!'
  ```

### vm.$log( [keypath] )

- **전달인자:**

  - `{String} [keypath]`

- **사용방법:**

  현재 인스턴스를 getter와 setter보다 콘솔에서 검사가 가능한 일반 객체로 기록합니다. 부가적인 키도 사용할 수 있습니다.

  ```javascript
  vm.$log() // logs entire ViewModel data
  vm.$log('item') // logs vm.item
  ```

## 인스턴스 메소드 / 이벤트

### vm.$on( event, callback )

- **전달인자:**

  - `{String} event`
  - `{Function} callback`

- **사용방법:**

  현재 vm에서 이벤트를 모니터링합니다. 이벤트는 `vm.$emit`, `vm.$dispatch` 또는 `vm.$broadcast`에서 호출할 수 있습니다. 그 이벤트 호출하는 메소드에 전달 된 추가 전달인자는 콜백으로 모두 받을 수 있습니다.

- **예제**

  ```javascript
  vm.$on('test', function (msg) {
    console.log(msg)
  })
  vm.$emit('test', 'hi')
  // -> "hi"
  ```

### vm.$once( event, callback )

- **전달인자:**

  - `{String} event`
  - `{Function} callback`

- **사용방법:**

  한번만 사용 가능한 이벤트 리스너를 제공합니다. 리스너는 한번 실행하면 삭제됩니다.

### vm.$off( [event, callback] )

- **전달인자:**

  - `{String} [event]`
  - `{Function} [callback]`

- **사용방법:**

  이벤트 리스너를 제거합니다.

  - 전달인자가 없는 경우 모든 이벤트 리스너를 제거합니다.

  - 이벤트만 전달된 경우 모든 이벤트에 대한 리스너가 제거됩니다.

  - 이벤트와 콜백이 전달되면 해당 콜백에 대한 리스너만 제거됩니다.

### vm.$emit( event, [...args] )

- **전달인자:**

  - `{String} event`
  - `[...args]`

  현재 인스턴스에 대한 이벤트 트리거입니다. 추가적인 전달인자를 리스너의 콜백에 전달할 수 있습니다.

### vm.$dispatch( event, [...args] )

- **전달인자:**

  - `{String} event`
  - `[...args]`

- **사용방법:**

  이벤트를 디스패치합니다. 우선 자신의 인스턴스에서 이벤트를 실행하고 그 때 부모 chain의 위쪽에 이벤트를 전달합니다. 이벤트의 전파는 상위 이벤트 리스너가 true를 반환하지 않으면 부모의 이벤트 리스너를 실행 할 때 중지합니다. 추가 인수는 리스너 콜백 함수에 전달됩니다.

- **예제**

  ```javascript
  // 부모 chain을 만듭니다
  var parent = new Vue()
  var child1 = new Vue({ parent: parent })
  var child2 = new Vue({ parent: child1 })

  parent.$on('test', function () {
    console.log('parent notified')
  })
  child1.$on('test', function () {
    console.log('child1 notified')
  })
  child2.$on('test', function () {
    console.log('child2 notified')
  })

  child2.$dispatch('test')
  // -> "child2 notified"
  // -> "child1 notified"
  // child1 콜백이 true를 반환하지 않기 때문에 부모에게 알리지 않습니다.
  // true in its callback
  ```

- **참고하세요:** [부모-자식간 통신](/guide/components.html#Parent-Child-Communication)

### vm.$broadcast( event, [...args] )

- **전달인자:**

  - `{String} event`
  - `[...args]`

- **사용방법:**

  브로드캐스트는 현재 인스턴스의 모든 자손 이벤트를 아래로 전파시킵니다. 자손이 여러 하위 트리에 열기 위한 이벤트 전파는 많은 다른 "path"를 따릅니다. 각 경로의 이벤트 전파가 리스너의 콜백이 `true`를 반환하지 않으면 이벤트 리스너가 경로를 따라 실행했을 때 중지합니다.

- **예제**

  ```javascript
  var parent = new Vue()
  // child1 와 child2는 형제관계입니다
  var child1 = new Vue({ parent: parent })
  var child2 = new Vue({ parent: parent })
  // child3는 child2의 자식입니다.
  var child3 = new Vue({ parent: child2 })

  child1.$on('test', function () {
    console.log('child1 notified')
  })
  child2.$on('test', function () {
    console.log('child2 notified')
  })
  child3.$on('test', function () {
    console.log('child3 notified')
  })

  parent.$broadcast('test')
  // -> "child1 notified"
  // -> "child2 notified"
  // child3는 출력되지 않습니다, child2가 true를 반환하지 않았습니다.
  ```

## 인스턴스 메소드 / DOM

### vm.$appendTo( elementOrSelector, [callback] )

- **전달인자:**

  - `{Element | String} elementOrSelector`
  - `{Function} [callback]`

- **반환 값:** `vm` - 인스턴스 자신입니다

- **사용방법:**

  요소를 대상으로 Vue는 인스턴스의 DOM 엘리먼트 또는 파편을 추가합니다. 대상은 엘리먼트 또는 querySelector 문자열이 될 수 있습니다. 존재하는 경우 이 방법은 트랜지션을 트리거합니다. 트랜지션이(또는 어떠한 즉시 트랜지션이 발생되지 않은 경우) 완료된 후 콜백 호출합니다.

### vm.$before( elementOrSelector, [callback] )

- **전달인자:**

  - `{Element | String} elementOrSelector`
  - `{Function} [callback]`

- **반환 값:** `vm` - 인스턴스 자신입니다

- **사용방법:**

  Vue 인스턴스의 DOM 엘리먼트 또는 파편을 대상 엘리먼트 이전에 삽입합니다. 대상 엘리먼트 또는 querySelector 문자열을 지정할 수 있습니다. 이 메서드는 표시되어있는 경우 트랜지션을 시작합니다. 트랜지션이 완료된 후 (또는 트랜지션이 트리거되지 않은 경우에는 즉시) 콜백 호출합니다.

### vm.$after( elementOrSelector, [callback] )

- **전달인자:**

  - `{Element | String} elementOrSelector`
  - `{Function} [callback]`

- **반환 값:** `vm` - 인스턴스 자신입니다

- **사용방법:**

  Vue 인스턴스의 DOM 엘리먼트 또는 파편을 대상 엘리먼트의 뒤에 삽입합니다. 대상 엘리먼트 또는 querySelector 문자열을 지정할 수 있습니다. 이 메서드는 표시되어있는 경우 트랜지션을 시작합니다. 트랜지션이 완료된 후 (또는 트랜지션이 트리거되지 않은 경우에는 즉시) 콜백 호출합니다.

### vm.$remove( [callback] )

- **전달인자:**

  - `{Function} [callback]`

- **반환 값:** `vm` - 인스턴스 자신입니다

- **사용방법:**

  Vue 인스턴스의 DOM 엘리먼트 또는 파편을 DOM에서 삭제합니다. 이 메서드는 표시되어있는 경우 트랜지션을 시작합니다. 트랜지션이 완료된 후 (또는 트랜지션이 트리거되지 않은 경우에는 즉시) 콜백 호출합니다.

### vm.$nextTick( callback )

- **전달인자:**

  - `{Function} [callback]`

- **사용방법:**

  callback을 연기하고 DOM의 업데이트 주기 이후에 실행합니다. DOM 업데이트를 갱신하려면 데이터를 업데이트 한 후 사용하십시오. callback의 `this` 컨텍스트는 자동으로 이 메소드를 호출 인스턴스에 바인딩되는 것을 제외하고 전역 `Vue.nextTick`과 같습니다.

- **예제**

  ```javascript
  new Vue({
    // ...
    methods: {
      // ...
      example: function () {
        // 데이터 조작
        this.message = 'changed'
        // DOM이 아직 갱신되지 않았습니다.
        this.$nextTick(function () {
          // DOM이 갱신되었습니다.
          // `this` 는 현재 인스턴스입니다
          this.doSomethingElse()
        })
      }
    }
  })
  ```

- **참고하세요:**

  - [Vue.nextTick](#Vue-nextTick)
  - [비동기 업데이트 큐](/guide/reactivity.html#Async-Update-Queue)

## 인스턴스 메소드 / 라이프사이클

### vm.$mount( [elementOrSelector] )

- **전달인자:**

  - `{Element | String} [elementOrSelector]`

- **반환 값:** `vm` - 인스턴스 자신입니다

- **사용방법:**

  인스턴스화 할 때 Vue 인스턴스 `el` 옵션을받지 못한 경우, DOM 엘리먼트 또는 파편과 무관하게 "unmounted"상태입니다. `vm.$mount()`를 사용하는 것으로, 이러한 Vue 인스턴스의 컴파일 단계를 수동으로 시작할 수 있습니다.

  전달인자가 아무것도 주어지지 않은 경우 템플릿은 문서 외부의 파편으로 작성됩니다. 그리고 문서에 추가하기 위해 다른 DOM 인스턴스 메소드를 사용해야합니다. `replace` 옵션이 `false`로 설정되는 경우, wrapper 요소로 빈 `<div>`가 자동으로 만들어집니다.

  이미 마운트 된 상태의 인스턴스에서 `$mount()`를 호출하면, 아무것도 일어나지 않습니다. 이 메소드는 인스턴스 자체를 반환하므로 다른 인스턴스 메소드를 연결할 수 있습니다.

- **예제**

  ```javascript
  var MyComponent = Vue.extend({
    template: '<div>Hello!</div>'
  })

  // #app 을 만들고 마운트합니다 (#app을 대체합니다)
  new MyComponent().$mount('#app')

  // 위와 같습니다.
  new MyComponent({ el: '#app' })

  // 또는 문서와 분리하여 컴파일 한 후 마운트:
  new MyComponent().$mount().$appendTo('#container')
  ```

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

### vm.$destroy( [remove] )

- **전달인자:**

  - `{Boolean} [remove] - default: false`

- **사용방법:**

  vm을 완전히 제거합니다. 기존의 다른 vm와의 연결을 끄고 모든 지시어와 바인딩, 모든 이벤트 리스너을 제거하고 `remove` 전달인자가 true의 경우 vm과 관련한 DOM 엘리먼트 또는 파편을 삭제합니다.

  `beforeDestroy`와 `destroyed` 훅을 발생합니다.

- **참고하세요:** [라이프사이클 다이어그램](/guide/instance.html#Lifecycle-Diagram)

## 지시어

### v-text

- **요구조건** `String`

- **상세:**

  엘리먼트의 `textContent`를 갱신합니다.

  내부적으로는 `{% raw %}{{ Mustache }}{% endraw %}`도 textNode에 `v-text` 지시어로 컴파일됩니다. 이 지시어 형식은 wrapper 요소가 필요하지만 성능이 약간 개선되고 FOUC(아직 컴파일되지 않은 내용 깜박임)을 방지합니다.

- **예제**

  ```html
  <span v-text="msg"></span>
  <!-- same as -->
  <span>{{msg}}</span>
  ```

### v-html

- **요구조건** `String`

- **상세:**

  엘리먼트의 `innerHTML`을 업데이트합니다. 일반 HTML로 삽입 된 데이터 바인딩은 무시됩니다. 템플릿 조각을 재사용 할 수 있다면 [partials](#partial)을 사용하십시오.

  내부적으로는 `{% raw %}{{{ Mustache }}}{% endraw %}` 표현식은 앵커 노드를 이용하여 `v-html` 지시어로 컴파일됩니다. 지시어 형식은 래퍼 요소를 필요로하지만 성능이 약간 개선되고 FOUC (아직 컴파일되지 않은 내용 깜박임)을 방지합니다.

  <p class="tip">임의의 HTML을 Web 사이트에서 동적으로 렌더링 하는 경우 [XSS 공격](https://en.wikipedia.org/wiki/Cross-site_scripting)을 일으 킬 수 있어 매우 위험합니다. `v-html`은 신뢰할 수있는 콘텐츠에만 사용하고 절대로 사용자가 제공하는 콘텐츠에는 사용하지 마십시오.</p>

- **예제**

  ```html
  <div v-html="html"></div>
  <!-- 동일합니다 -->
  <div>{{{html}}}</div>
  ```

### v-if

- **요구조건** `*`

- **사용방법:**

  바인딩 값의 참 여부로 엘리먼트를 렌더링합니다. 엘리먼트 및 데이터 바인딩 또는 구성 요소를 포함하는 콘텐츠는 토글하는 동안 제거되고 재구성됩니다. 요소가 `<template>` 요소이면 그 내용은 조건부 블록으로 추출됩니다.

- **참고하세요:** [조건부 렌더링](/guide/conditional.html)

### v-show

- **요구조건** `*`

- **사용방법:**

  바인딩 값의 참 여부 따라 요소의 CSS 속성 `display`를 토글합니다. 화면에 그릴 때 트랜지션을 실행합니다.

- **참고하세요:** [조건부 렌더링 - v-show](/guide/conditional.html#v-show)

### v-else

- **요구조건이 없습니다**

- **제한:** 이 지시어가 있는 형제 노드에 `v-if` 또는 `v-show`가 있어야 합니다..

- **사용방법:**

  `v-if` and `v-show`에 대한 "else 블럭" 입니다.

  ```html
  <div v-if="Math.random() > 0.5">
    Sorry
  </div>
  <div v-else>
    Not sorry
  </div>
  ```

- **참고하세요:** [조건부 렌더링 - v-else](/guide/conditional.html#v-else)

- **참고하세요:** [조건부 렌더링 - Component caveat](/guide/conditional.html#Component-caveat)

### v-for

- **요구조건** `Array | Object | Number | String`

- **파라미터 속성:**

  - [`track-by`](/guide/list.html#track-by)
  - [`stagger`](/guide/transitions.html#Staggering-Transitions)
  - [`enter-stagger`](/guide/transitions.html#Staggering-Transitions)
  - [`leave-stagger`](/guide/transitions.html#Staggering-Transitions)

- **사용방법:**

  소스 데이터에 따라 엘리먼트 또는 템플릿 블록을 여러 번 렌더링합니다. 표현식에는 반복되는 요소에 대한 별칭을 제공하기 위해 특별한 문법 `alias (in|of) expression` 을 사용해야합니다:

  ```html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  `of`는 1.0.17버전 이후에서 제공합니다.

  또는 인덱스 (또는 객체에서 사용되는 경우 키)의 별칭을 지정할 수 있습니다 :

  ```html
  <div v-for="(index, item) in items"></div>
  <div v-for="(key, val) in object"></div>
  ```

  `v-for`의 자세한 사용 방법은 아래에 링크 된 가이드 섹션에서 설명하고 있습니다.

- **참고하세요:** [리스트 렌더링](/guide/list.html).

### v-on

- **짧게쓰기:** `@`

- **요구조건** `Function | Inline Statement`

- **전달인자:** `event (required)`

- **변경자:**

  - `.stop` - `event.stopPropagation()` 을 호출합니다.
  - `.prevent` - `event.preventDefault()` 을 호출합니다.
  - `.capture` - 캡처 모드에서 이벤트 리스너를 추가합니다.
  - `.self` - 이벤트가 이 엘리먼트에서 발생한 경우에만 핸들러를 호출합니다.
  - `.{keyCode | keyAlias}` - 특정 키에 대한 이벤트를 호출합니다.

- **사용방법:**

  엘리먼트에 이벤트 리스너를 연결합니다. 이벤트 종류는 전달인자로 표시됩니다. 표현식은 메소드 이름 또는 인라인 구문 중 하나를 지정할 수 있고 변경자(modifier)가 존재할 때, 단순히 생략됩니다.

  일반적 엘리먼트에서 사용한 경우 **네이티브 DOM 이벤트** 를 모니터링합니다. 사용자 정의 컴포넌트에서 사용한 경우 하위 컴포넌트에서 **사용자 지정 이벤트** 의 호출도 모니터링합니다.

  네이티브 DOM 이벤트를 수신 할 때, 메소드는 기본 이벤트를 전달인자로만 받습니다. 인라인 구문에서 사용하는 경우 구문은 특별한 `$event` 속성에 `v-on:click="handle('ok', $event)"`과 같이 액세스 할 수 있습니다.

  **1.0.11+** 사용자 정의 이벤트를 리스닝 할 때 인라인 구문은 자식 컴퍼넌트의 `$emit` 호출에 전달 된 추가 인수의 배열 인 특별한 `$arguments` 속성에 액세스 할 수 있습니다.


- **예제**

  ```html
  <!-- 메소드 핸들러 -->
  <button v-on:click="doThis"></button>

  <!-- 인라인 구문 -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- 짧게 쓰기 -->
  <button @click="doThis"></button>

  <!-- 전파 중지 -->
  <button @click.stop="doThis"></button>

  <!-- 기본 동작 방지 -->
  <button @click.prevent="doThis"></button>

  <!-- 표현식을 사용하지 않는 기본 동작 방지 -->
  <form @submit.prevent></form>

  <!-- 변경자 체인 -->
  <button @click.stop.prevent="doThis"></button>

  <!-- 키 별칭을 사용하는 변경자-->
  <input @keyup.enter="onEnter">

  <!-- 키 코드를 사용하는 변경자 -->
  <input @keyup.13="onEnter">
  ```

  자식 컴퍼넌트의 사용자 지정 이벤트를 모니터링 할 수 있습니다 (핸들러는 "my-event" 자식 요소에서 발행되었을 때 호출 됨) :

  ```html
  <my-component @my-event="handleThis"></my-component>

  <!-- 인라인 구문 -->
  <my-component @my-event="handleThis(123, $arguments)"></my-component>
  ```

- **참고하세요:** [메소드와 이벤트 핸들링](/guide/events.html)

### v-bind

- **짧게쓰기:** `:`

- **요구조건** `* (전달인자와 함께 사용) | Object (전달인자 없이 사용)`

- **전달인자:** `attrOrProp (optional)`

- **변경자:**

  - `.sync` - 양방향 바인딩을 만듭니다. prop 바인딩에만 유효하므로 주의하십시오.
  - `.once` - 단방향 바인딩을 만듭니다. prop 바인딩에만 유효하므로 주의하십시오.
  - `.camel` - 설정 될 때 속성 이름을 camelCase로 변환합니다. 일반적인 속성에 대해서만 유효하므로주의하십시오. camelCase 인 SVG 속성에 사용됩니다.

- **사용방법:**

  하나 이상의 속성 또는 컴포넌트 prop와 식을 동적으로 바인딩합니다.

  `class` 또는 `style` 속성과 바인딩할 때 배열이나 객체와 같은 추가적인 값 유형을 지원합니다. 자세한 내용은 아래에 링크 된 가이드 섹션을 참조하십시오.

  prop 바인딩에 사용할 경우 prop는 자식 컴퍼넌트 내에 적절하게 선언해야합니다. prop바인딩은 수식 중 하나를 이용하여 다른 바인딩 유형을 지정할 수 있습니다.

  전달인자없이 사용하면 이름과 값 쌍을 포함하는 객체를 바인딩하여 사용할 수 있습니다. 이 모드는 `class`와 `style` 배열과 객체를 지원하지 않는 점에 유의하십시오.

- **예제**

  ```html
  <!-- 속성 바인딩 -->
  <img v-bind:src="imageSrc">

  <!-- 짧게 쓰기 -->
  <img :src="imageSrc">

  <!-- 클래스 바인딩 -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">

  <!-- 스타일 바인딩 -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- 객체를 속성으로 바인딩 -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- prop 바인딩. "prop"는 반드시 my-component에 정의되어 있어합니다. -->
  <my-component :prop="someThing"></my-component>

  <!-- 양방향 prop 바인딩 -->
  <my-component :prop.sync="someThing"></my-component>

  <!-- 단방향 prop 바인딩 -->
  <my-component :prop.once="someThing"></my-component>
  ```

- **참고하세요:**

  - [클래스와 스타일 바인딩](/guide/class-and-style.html)
  - [컴포넌트 Props](/guide/components.html#Props)

### v-model

- **요구조건** input type에 따라 변화합니다.

- **제한됨:**

  - `<input>`
  - `<select>`
  - `<textarea>`

- **파라미터 속성:**

  - [`lazy`](/guide/forms.html#lazy)
  - [`number`](/guide/forms.html#number)
  - [`debounce`](/guide/forms.html#debounce)

- **사용방법:**

  form input 요소에 양방향 바인딩을 만듭니다. 자세한 내용은 아래에 링크 된 가이드 섹션을 참조하십시오.

- **참고하세요:** [폼 입력 바인딩](/guide/forms.html)

### v-ref

- **요구조건이 없습니다**

- **제한됨:** 자식 컴포넌트

- **전달인자:** `id (required)`

- **사용방법:**

  직접 액세스를 위해 부모에서 자식 요소에 대한 참조를 등록합니다. 표현식을 허용하지 않습니다. 등록 id로 전달인자가 필요합니다. 컴포넌트 인스턴스는 부모의 `$refs` 객체에서 참조할 수 있습니다.

  `v-for`와 함께 사용할 때, 값은 바인딩 된 배열에 대응하는 모든 하위 컴포넌트 인스턴스를 포함하는 배열입니다. `v-for` 데이터 소스 객체의 경우 등록 된 값은 소스 객체와 미러링 된 각 키와 인스턴스를 포함하는 객체입니다.

- **참고:**

  HTML은 case-insensitive이기 때문에 `v-ref: someRef` 같은 카멜 케이스(camlCase)의 사용은 모두 소문자로 변환됩니다. 제대로 `this.$refs.someRef`로 설정하고 `v-ref:some-ref`를 사용 할 수 있습니다.

- **예제**

  ```html
  <comp v-ref:child></comp>
  <comp v-ref:some-child></comp>
  ```

  ```javascript
  // 부모 인스턴스에서 사용합니다:
  this.$refs.child
  this.$refs.someChild
  ```

  `v-for`와 함께:

  ```html
  <comp v-ref:list v-for="item in list"></comp>
  ```

  ```javascript
  // 부모 인스턴스에서 배열로 존재합니다
  this.$refs.list
  ```

- **참고하세요:** [자식 컴포넌트 참조](/guide/components.html#Child-Component-Refs)

### v-el

- **요구조건이 없습니다**

- **전달인자:** `id (required)`

- **사용방법:**

  쉽게 액세스할 수 있도록하기 위해 소유자의 Vue 인스턴스의 `$els` 개체 DOM 요소에 대한 참조를 등록합니다.

- **참고:**

  HTML은 case-insensitive이기 때문에 `v-el:someEl` 같은 카멜 케이스(camlCase)의 사용은 모두 소문자로 변환됩니다. 제대로 `this.$els.someEl`로 설정하고 `v-el:some-el`를 사용 할 수 있습니다.

- **예제**

  ```html
  <span v-el:msg>hello</span>
  <span v-el:other-msg>world</span>
  ```

  ```javascript
  this.$els.msg.textContent // -> "hello"
  this.$els.otherMsg.textContent // -> "world"
  ```

### v-pre

- **요구조건이 없습니다**

- **사용방법**

  이 엘리먼트 및 모든 하위 엘리먼트의 컴파일을 건너 뜁니다. 원시 mustache 태그를 표시하는 데에도 사용할 수 있습니다. 지시어가 없는 대량의 노드를 생략하여 컴파일 속도를 높입니다.

- **예제**

  ```html
  <span v-pre>{{ this will not be compiled }}</span>
  ```

### v-cloak

- **요구조건이 없습니다**

- **사용방법:**

  이 지시어는 관련된 Vue 인스턴스의 컴파일이 종료 할 때까지 남아 있습니다. `[v-cloak] {display:none}`과 같은 CSS 규칙과 함께이 지시문은 Vue 인스턴스가 마련 될 때까지 컴파일되지 않은 Mustache 바인딩을 숨기기 위하여 사용할 수 있습니다.

- **예제**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  `<div>` 컴파일이 끝날 때까지 보이지 않게됩니다.

## Special Elements

### component

- **속성:**

  - `is`

- **파라미터 속성:**

  - [`keep-alive`](/guide/components.html#keep-alive)
  - [`transition-mode`](/guide/components.html#transition-mode)

- **사용방법:**

  컴포넌트를 시작하는 구문입니다. 주로 동적 컴포넌트를 위한 `is` 속성에서 사용됩니다 :

  ```html
  <!-- 동적 컴포넌트는 vm에서`componentId` 속성에 의해 제어됩니다 -->
  <component :is="componentId"></component>
  ```

- **참고하세요:** [동적 컴포넌트](/guide/components.html#Dynamic-Components)

### slot

- **속성:**

  - `name`

- **사용방법:**

  <slot> 요소는 컴포넌트 템플릿 콘텐츠 삽입용 아울렛으로 이용합니다. slot 엘리먼트 자체가 바뀌게 됩니다.

  `name` 속성을 지정한 슬롯은 명명된 슬롯이라고합니다. 명명된 슬롯은 이름과 일치 한 `slot` 속성과 함께 콘텐츠를 제공합니다.

  자세한 사용방법은 아래의 가이드를 참고하세요.

- **참고하세요:** [Slot과 컨텐츠 배포](/guide/components.html#Content-Distribution-with-Slots)

### partial

- **속성:**

  - `name`

- **사용방법:**

  `<partial>` 엘리먼트는 등록된 템플릿 조각을 위한 아울렛으로 사용합니다. 콘텐츠 조각이가 삽입 된 때, Vue의해 컴파일됩니다. `<partial>` 요소는 그 자체가 바뀝니다. partial 콘텐츠를 위해 `name` 속성이 필요합니다.

- **예제**

  ```javascript
  // partial 등록
  Vue.partial('my-partial', '<p>This is a partial! {{msg}}</p>')
  ```

  ```html
  <!-- 정적 partial -->
  <partial name="my-partial"></partial>

  <!-- 동적 partial -->
  <!-- id === vm.partialId 와 함께 렌더링 -->
  <partial v-bind:name="partialId"></partial>

  <!-- v-bind 짧게 사용하기를 적용한 동적 partial -->
  <partial :name="partialId"></partial>
  ```

## 필터

### capitalize

- **예제**

  ```html
  {{ msg | capitalize }}
  ```

  _'abc' => 'Abc'_

### uppercase

- **예제**

  ```html
  {{ msg | uppercase }}
  ```

  _'abc' => 'ABC'_

### lowercase

- **예제**

  ```html
  {{ msg | lowercase }}
  ```

  _'ABC' => 'abc'_

### currency

- **전달인자:**

  - `{String} [symbol] - default: '$'`
  - **1.0.22+** `{Number} [decimal places] - default: 2`

- **예제**

  ```html
  {{ amount | currency }}
  ```

  _12345 => $12,345.00_

  다른 통화 단위를 사용할 수 있습니다:

  ```html
  {{ amount | currency '£' }}
  ```

  _12345 => £12,345.00_

  일부 통화는 3 또는 4 개의 소수점 자릿수를 가지고 있는데, 예를 들면, 일본 엔 (¥) 또는 베트남 동 (₫)와 같이 소수점이없는 경우는 다음과 같습니다 :

  ```html
  {{ amount | currency '₫' 0 }}
  ```

  _12345 => ₫12,345_

### pluralize

- **전달인자:**

  - `{String} single, [double, triple, ...]`

- **사용방법:**

  필터링된 값에 따라 전달인자를 복수형으로합니다. 다만 1 개의 전달인자가 지정되어있을 때, 단순히 그 인수의 끝에 "s"를 추가합니다. 보다 더 많은 인수가 지정되어있는 경우 그 인수는 single, double, triple라고하는 여러 변화되는 형식에 해당하는 문자열의 배열로 사용됩니다. 변화되는 수가 전달인자의 길이를 초과하면, 그것은 배열의 마지막 항목을 이용합니다.

- **예제**

  ```html
  {{count}} {{count | pluralize 'item'}}
  ```

  _1 => '1 item'_ _2 => '2 items'_

  ```html
  {{date}}{{date | pluralize 'st' 'nd' 'rd' 'th'}}
  ```

  이렇게 변경됩니다:

  _1 => '1st'_ _2 => '2nd'_ _3 => '3rd'_ _4 => '4th'_ _5 => '5th'_

### json

- **전달인자:**

  - `{Number} [indent] - default: 2`

- **사용방법:**

  문자열 표현 (`[object Object]`와 같은 객체)을 출력할 때 `toString()`대신  `JSON.stringify()`를 이용해서 출력하는 필터입니다.
- **예제**

  공백 4개의 들여쓰기를 이용해서 출력합니다.

  ```html
  <pre>{{ nestedObject | json 4 }}</pre>
  ```

### debounce

- **제한됨:** Function 값을 요구하는 지시어. 예를 들면 `v-on`.

- **전달인자:**

  - `{Number} [wait] - default: 300`

- **사용방법:**

  `x` 인자로하면 `x` 밀리 초 동안 디바운스하기 위해 지정된 핸들러를 감쌉니다. 기본적으로 300ms입니다. 디바운스 된 핸들러는 적어도 호출 된 순간부터 `x` 밀리 초 경과 할 때까지 지연됩니다. 지연 기간이 끝나기 전에 다시 핸들러가 호출 될 경우 지연 기간은 `x` 밀리 초로 재설정됩니다.

- **예제**

  ```html
  <input @keyup="onKeyup | debounce 500">
  ```

### limitBy

- **제한됨:** `Array`를 요구하는 지시문. 예를 들면 `v-for`.

- **전달인자:**

  - `{Number} limit`
  - `{Number} [offset]`

- **사용방법:**

  전달인자에 의해 지정된대로 첫 번째 N 개의 배열을 제한합니다. 추가 가능한 두 번째 전달인자는 시작하는 오프셋을 설정하기 위해 제공 할 수 있습니다.

  ```html
  <!-- 첫 10개만 보여줍니다 -->
  <div v-for="item in items | limitBy 10"></div>

  <!-- 5번째에서 15번째까지 보여줍니다 -->
  <div v-for="item in items | limitBy 10 5"></div>
  ```

### filterBy

- **제한됨:** `Array`를 요구하는 지시문. 예를 들면 `v-for`.

- **전달인자:**

  - `{String | Function} targetStringOrFunction`
  - `"in" (optional delimiter)`
  - `{String} [...searchKeys]`

- **사용방법:**

  원래 배열에서 필터링 된 버전을 반환합니다. 첫 번째 전달인자 문자열 또는 함수입니다.

  첫번째 전달인자가 문자열인 경우 배열의 각 요소에 대한 검색을 합니다:

  ```html
  <div v-for="item in items | filterBy 'hello'">
  ```

  위의 예제에서 `"hello"`를 포함하는 항목만 출력됩니다.

  item이 객체라면 필터는 그 객체의 중첩된 속성을 모두 재귀적으로 대상 문자열에서 검색합니다. 검색 범위를 좁히기 위해 추가 검색 키를 지정할 수 있습니다:

  ```html
  <div v-for="user in users | filterBy 'Jack' in 'name'">
  ```

  In the above example, the filter will only search for `"Jack"` in the `name` field of each user object. **It is a good idea to always limit the search scope for better performance.**
  위의 예에서 필터는 각 user 객체의 `name` 필드에서 ``"Jack"``만 검색합니다. 성능 향상을 위해 항상 검색 범위를 제한하는 것은 좋은 생각입니다.

  위의 예들은 정적 전달인자를 사용하고 있습니다. 물론 동적 전달인자를 대상 문자열 또는 검색 키로 사용할 수 있습니다. `v-model`과 함께 입력중인 필터링도 쉽게 구현 할 수 있습니다.

  ```html
  <div id="filter-by-example">
    <input v-model="name">
    <ul>
      <li v-for="user in users | filterBy name in 'name'">
        {{ user.name }}
      </li>
    </ul>
  </div>
  ```

  ```javascript
  new Vue({
    el: '#filter-by-example',
    data: {
      name: '',
      users: [
        { name: 'Bruce' },
        { name: 'Chuck' },
        { name: 'Jackie' }
      ]
    }
  })
  ```

  {% raw %}

  <div id="filter-by-example" class="demo">
    <input v-model="name">
    <ul>
    <p></p>
    <pre><code>&lt;li v-for="user in users | filterBy name in 'name'"&gt;
    {{ user.name }}
  &lt;/li&gt;
  </code></pre>
    <p></p>
  </ul>
  </div>

  <script>
  new Vue({
    el: &#39;#filter-by-example&#39;,
    data: {</p>
  <pre><code>name: &#39;&#39;,
  users: [{ name: &#39;Bruce&#39; }, { name: &#39;Chuck&#39; }, { name: &#39;Jackie&#39; }]
  </code></pre><p>  }
  })
  </script>

  {% endraw %}

- **추가 예제:**

  검색을 위한 키를 여러개 설정할 수 있습니다:

  ```html
  <li v-for="user in users | filterBy searchText in 'name' 'phone'"></li>
  ```

  여러개의 검색용 키를 동적 전달인자와 함께 설정할 수 있습니다:

  ```html
  <!-- fields = ['fieldA', 'fieldB'] -->
  <div v-for="user in users | filterBy searchText in fields">
  ```

  사용자 정의 필터 함수를 사용할 수 있습니다:

  ```html
  <div v-for="user in users | filterBy myCustomFilterFunction">
  ```

### orderBy

- **제한됨:** `Array`를 요구하는 지시문. 예를 들면 `v-for`.

- **전달인자:**

  - `{String | Array<String> | Function} ...sortKeys`
  - `{String} [order] - default: 1`

- **사용방법:**

  입력 된 배열의 정렬된 버전을 반환합니다. 정렬하기 위해 몇개의 문자열 키를 전달할 수 있습니다. 사용자 정의 방식으로 정렬하려면 키 또는 함수를 포함하는 배열을 전달할 수 있습니다. 옵션 `order` 전달인자는 결과에 대ㅏㄴ 오름차순 (order> = 0) 또는 내림차순 (order <0) 여부를 지정합니다.

  원시 값의 배열은 단순히 `sortKey`를 생략하고 순서를 제공합니다. 예 : `orderBy 1`

- **예제**

  users를 이름순으로 정렬합니다:

  ```html
  <ul>
    <li v-for="user in users | orderBy 'name'">
      {{ user.name }}
    </li>
  </ul>
  ```

  내림차순으로 정렬합니다:

  ```html
  <ul>
    <li v-for="user in users | orderBy 'name' -1">
      {{ user.name }}
    </li>
  </ul>
  ```

  원시 값을 이용합니다:

  ```html
  <ul>
    <li v-for="n in numbers | orderBy true">
      {{ n }}
    </li>
  </ul>
  ```

  동적인 정렬 규칙을 적용합니다:

  ```html
  <div id="orderby-example">
    <button @click="order = order * -1">Reverse Sort Order</button>
    <ul>
      <li v-for="user in users | orderBy 'name' order">
        {{ user.name }}
      </li>
    </ul>
  </div>
  ```

  ```javascript
  new Vue({
    el: '#orderby-example',
    data: {
      order: 1,
      users: [{ name: 'Bruce' }, { name: 'Chuck' }, { name: 'Jackie' }]
    }
  })
  ```

  키 두개를 이용합니다:

  ```html
  <ul>
    <li v-for="user in users | orderBy 'lastName' 'firstName'">
      {{ user.lastName }} {{ user.firstName }}
    </li>
  </ul>
  ```

  {% raw %}

  <div id="orderby-example" class="demo">
    <button @click="order = order * -1">Reverse Sort Order</button>
    <ul>
    <p></p>
    <pre><code>&lt;li v-for="user in users | orderBy 'name' order"&gt;
    {{ user.name }}
  &lt;/li&gt;
  </code></pre>
    <p></p>
  </ul>
  </div>

  <script>
  new Vue({
    el: &#39;#orderby-example&#39;,
    data: {</p>
  <pre><code>order: 1,
  users: [{ name: &#39;Bruce&#39; }, { name: &#39;Chuck&#39; }, { name: &#39;Jackie&#39; }]
  </code></pre><p>  }
  })
  </script>

  {% endraw %}

함수를 이용해 정렬합니다:

```html
  <div id="orderby-compare-example" class="demo">
    <button @click="order = order * -1">Reverse Sort Order</button>
    <ul>
      <li v-for="user in users | orderBy ageByTen order">
        {{ user.name }} - {{ user.age }}
      </li>
    </ul>
  </div>
```

```javascript
  new Vue({
    el: '#orderby-compare-example',
    data: {
      order: 1,
      users: [
        {
          name: 'Jackie',
          age: 62
        },
        {
          name: 'Chuck',
          age: 76
        },
        {
          name: 'Bruce',
          age: 61
        }
      ]
    },
    methods: {
      ageByTen: function (a, b) {
        return Math.floor(a.age / 10) - Math.floor(b.age / 10)
      }
    }
  })
```

{% raw %}

<div id="orderby-compare-example" class="demo">
  <button @click="order = order * -1">Reverse Sort Order</button>
  <ul id="orderby-compare-example">
  <p></p>
  <pre><code>&lt;li v-for="user in users | orderBy ageByTen order"&gt;
    {{ user.name }} - {{ user.age }}
  &lt;/li&gt;
  </code></pre>
  <p></p>
</ul>
</div>

<script>
  new Vue({
    el: &#39;#orderby-compare-example&#39;,
    data: {</p>
  <pre><code>order: 1,
  users: [
    {
      name: &#39;Jackie&#39;,
      age: 62
    },
    {
      name: &#39;Chuck&#39;,
      age: 76
    },
    {
      name: &#39;Bruce&#39;,
      age: 61
    }
  ]
  </code></pre><p>  },
    methods: {</p>
  <pre><code>ageByTen: function (a, b) {
    return Math.floor(a.age / 10) - Math.floor(b.age / 10)
  }
  </code></pre><p>  }
  })
  </script>

{% endraw %}

## 배열 확장 메소드

Vue.js는  `Array.prototype`을 두개의 추가 메소드를 이용하여 일반적인 배열 사용시 반응형 업데이트를 지원합니다.

### array.$set(index, value)

- **전달인자:**

  - `{Number} index`
  - `{*} value`

- **사용방법**

  인덱스와 view의 업데이트를 호출하여 배열의 요소에 값을 설정합니다.

  ```javascript
  vm.animals.$set(0, { name: 'Aardvark' })
  ```

- **참고하세요:** [주의 사항](/guide/list.html#Caveats)

### array.$remove(reference)

- **전달인자:**

  - `{Reference} reference`

- **사용방법**

  참조 및 view의 업데이트를 호출하여 배열에서 요소를 제거합니다. 이것은 배열의 엘리먼트에 대해 검색하는 메소드로, 만약 발견되면, `array.splice(index 1)` 을 부릅니다.
  ```javascript
  var aardvark = vm.animals[0]
  vm.animals.$remove(aardvark)
  ```

- **참고하세요:** [조작 메소드](/guide/list.html#Mutation-Methods)
