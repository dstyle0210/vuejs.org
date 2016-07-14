---
type: api
---

# 전역 설정

`Vue.config`은 Vue의 전역 설정을 가지고 있는 객체입니다. 아래에 있는 `Vue.config`의 속성들을 사용 전에 변경할 수 있습니다:

## debug

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

## delimiters

- **자료형:** `Array<String>`

- **기본값:** `{% raw %}["{{", "}}"]{% endraw %}`

- **사용방법:**

  ```javascript
  // ES6 템플릿 스타일로 변경
  Vue.config.delimiters = ['${', '}']
  ```

  일반 텍스트 구분자(delimiter)를 변경합니다.

## unsafeDelimiters

- **자료형:** `Array<String>`

- **기본값:** `{% raw %}["{{{", "}}}"]{% endraw %}`

- **사용방법:**

  ```javascript
  // 더 위험하게 보이도록 합니다
  Vue.config.unsafeDelimiters = ['{!!', '!!}']
  ```

  원시 HTML을 출력하기 위한 구분자를 변경합니다.

## silent

- **자료형:** `Boolean`

- **기본값:** `false`

- **사용방법:**

  ```javascript
  Vue.config.silent = true
  ```

  모든 Vue.js의 로그와 경고를 나오지 않게 합니다.

## async

- **자료형:** `Boolean`

- **기본값:** `true`

- **사용방법:**

  ```javascript
  Vue.config.async = false
  ```

  비동기모드가 꺼진 상태에는 Vue는 데이터 변경시 모든 DOM 갱신을 동기적으로 처리합니다. 일부 시나리오에서 디버깅에 도움이 됩니다. 하지만 성능을 떨어뜨리고 콜백을 처리하는 watcher에 영향을 줄 수 있습니다. **배포 시 `async : false`는 추천하지 않습니다.**

## devtools

- **자료형:** `Boolean`

- **기본값:** `true` (배포용 빌드는 `false` 입니다.)

- **사용방법:**

  ```javascript
  // Vue가 불러진 이후에 동기적으로 설정됩니다.
  Vue.config.devtools = true
  ```

  [vue-devtools](https://github.com/vuejs/vue-devtools) 사용을 가능하게 합니다. 개발용 빌드는 `true`, 배포용 빌드는 `false`가 기본값입니다. 배포용 빌드에서 `true`로 사용할 수도 있습니다.

# 전역 API

## Vue.extend( options )

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

- **참고하세요:** [Components](/guide/components.html)

## Vue.nextTick( callback )

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

## Vue.set( object, key, value )

- **전달인자:**

  - `{Object} object`
  - `{String} key`
  - `{*} value`

- **반환 값:** the set value.

- **사용방법:**

  객체의 속성을 설정합니다. 오브젝트가 반응형이면 속성이 반응형 속성으로 생성되는 것을 보장하고 View 업데이트를 트리거합니다. 이것은 주로 Vue이 속성 추가를 감지 할 수 없다는 제약을 피하기 위해 사용됩니다.

- **참고하세요:** [Reactivity in Depth](/guide/reactivity.html)

## Vue.delete( object, key )

- **전달인자:**

  - `{Object} object`
  - `{String} key`

- **사용방법:**

  Delete a property on an object. If the object is reactive, ensure the deletion triggers view updates. This is primarily used to get around the limitation that Vue cannot detect property deletions, but you should rarely need to use it.

- **참고하세요:** [Reactivity in Depth](/guide/reactivity.html)

## Vue.directive( id, [definition] )

- **전달인자:**

  - `{String} id`
  - `{Function | Object} [definition]`

- **사용방법:**

  Register or retrieve a global directive.

  ```javascript
  // register
  Vue.directive('my-directive', {
    bind: function () {},
    update: function () {},
    unbind: function () {}
  })

  // register (simple function directive)
  Vue.directive('my-directive', function () {
    // this will be called as `update`
  })

  // getter, return the directive definition if registered
  var myDirective = Vue.directive('my-directive')
  ```

- **참고하세요:** [Custom Directives](/guide/custom-directive.html)

## Vue.elementDirective( id, [definition] )

- **전달인자:**

  - `{String} id`
  - `{Object} [definition]`

- **사용방법:**

  Register or retrieve a global element directive.

  ```javascript
  // register
  Vue.elementDirective('my-element', {
    bind: function () {},
    // element directives do not use `update`
    unbind: function () {}
  })

  // getter, return the directive definition if registered
  var myDirective = Vue.elementDirective('my-element')
  ```

- **참고하세요:** [Element Directives](/guide/custom-directive.html#Element-Directives)

## Vue.filter( id, [definition] )

- **전달인자:**

  - `{String} id`
  - `{Function | Object} [definition]`

- **사용방법:**

  Register or retrieve a global filter.

  ```javascript
  // register
  Vue.filter('my-filter', function (value) {
    // return processed value
  })

  // two way filter
  Vue.filter('my-filter', {
    read: function () {},
    write: function () {}
  })

  // getter, return the filter if registered
  var myFilter = Vue.filter('my-filter')
  ```

- **참고하세요:** [Custom Filter](/guide/custom-filter.html)

## Vue.component( id, [definition] )

- **전달인자:**

  - `{String} id`
  - `{Function | Object} [definition]`

- **사용방법:**

  Register or retrieve a global component.

  ```javascript
  // register an extended constructor
  Vue.component('my-component', Vue.extend({ /* ... */ }))

  // register an options object (automatically call Vue.extend)
  Vue.component('my-component', { /* ... */ })

  // retrieve a registered component (always return constructor)
  var MyComponent = Vue.component('my-component')
  ```

- **참고하세요:** [Components](/guide/components.html).

## Vue.transition( id, [hooks] )

- **전달인자:**

  - `{String} id`
  - `{Object} [hooks]`

- **사용방법:**

  Register or retrieve a global transition hooks object.

  ```javascript
  // register
  Vue.transition('fade', {
    enter: function () {},
    leave: function () {}
  })

  // retrieve registered hooks
  var fadeTransition = Vue.transition('fade')
  ```

- **참고하세요:** [Transitions](/guide/transitions.html).

## Vue.partial( id, [partial] )

- **전달인자:**

  - `{String} id`
  - `{String} [partial]`

- **사용방법:**

  Register or retrieve a global template partial string.

  ```javascript
  // register
  Vue.partial('my-partial', '<div>Hi</div>')

  // retrieve registered partial
  var myPartial = Vue.partial('my-partial')
  ```

- **참고하세요:** [Special Elements -

  <partial>
  </partial>

  ](#partial)

  .

## Vue.use( plugin, [options] )

- **전달인자:**

  - `{Object | Function} plugin`
  - `{Object} [options]`

- **사용방법:**

  Install a Vue.js plugin. If the plugin is an Object, it must expose an `install` method. If it is a function itself, it will be treated as the install method. The install method will be called with Vue as the argument.

- **참고하세요:** [Plugins](/guide/plugins.html).

## Vue.mixin( mixin )

- **전달인자:**

  - `{Object} mixin`

- **사용방법:**

  Apply a mixin globally, which affects every Vue instance created afterwards. This can be used by plugin authors to inject custom behavior into components. **Not recommended in application code**.

- **참고하세요:** [Global Mixins](/guide/mixins.html#Global-Mixin)

# Options / Data

## data

- **자료형:** `Object | Function`

- **제한:** Only accepts `Function` when used in a component definition.

- **상세:**

  The data object for the Vue instance. Vue.js will recursively convert its properties into getter/setters to make it "reactive". **The object must be plain**: native objects, existing getter/setters and prototype properties are ignored. It is not recommended to observe complex objects.

  Once the instance is created, the original data object can be accessed as `vm.$data`. The Vue instance also proxies all the properties found on the data object.

  Properties that start with `_` or `$` will **not** be proxied on the Vue instance because they may conflict with Vue's internal properties and API methods. You will have to access them as `vm.$data._property`.

  When defining a **component**, `data` must be declared as a function that returns the initial data object, because there will be many instances created using the same definition. If we still use a plain object for `data`, that same object will be **shared by reference** across all instance created! By providing a `data` function, every time a new instance is created, we can simply call it to return a fresh copy of the initial data.

  If required, a deep clone of the original object can be obtained by passing `vm.$data` through `JSON.parse(JSON.stringify(...))`.

- **예제**

  ```javascript
  var data = { a: 1 }

  // direct instance creation
  var vm = new Vue({
    data: data
  })
  vm.a // -> 1
  vm.$data === data // -> true

  // must use function when in Vue.extend()
  var Component = Vue.extend({
    data: function () {
      return { a: 1 }
    }
  })
  ```

- **참고하세요:** [Reactivity in Depth](/guide/reactivity.html).

## props

- **자료형:** `Array | Object`

- **상세:**

  A list/hash of attributes that are exposed to accept data from the parent component. It has a simple Array-based syntax and an alternative Object-based syntax that allows advanced configurations such as type checking, custom validation and default values.

- **예제**

  ```javascript
  // simple syntax
  Vue.component('props-demo-simple', {
    props: ['size', 'myMessage']
  })

  // object syntax with validation
  Vue.component('props-demo-advanced', {
    props: {
      // just type check
      size: Number,
      // type check plus other validations
      name: {
        type: String,
        required: true,
        // warn if not two way bound
        twoWay: true
      }
    }
  })
  ```

- **참고하세요:** [Props](/guide/components.html#Props)

## propsData

> 1.0.22+

- **자료형:** `Object`

- **제한:** only respected in instance creation via `new`.

- **상세:**

  Pass props to an instance during its creation. This is primarily intended to make unit testing easier.

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

## computed

- **자료형:** `Object`

- **상세:**

  Computed properties to be mixed into the Vue instance. All getters and setters have their `this` context automatically bound to the Vue instance.

- **예제**

  ```javascript
  var vm = new Vue({
    data: { a: 1 },
    computed: {
      // get only, just need a function
      aDouble: function () {
        return this.a * 2
      },
      // both get and set
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

  - [Computed Properties](/guide/computed.html)
  - [Reactivity in Depth: Inside Computed Properties](/guide/reactivity.html#Inside-Computed-Properties)

## methods

- **자료형:** `Object`

- **상세:**

  Methods to be mixed into the Vue instance. You can access these methods directly on the VM instance, or use them in directive expressions. All methods will have their `this` context automatically bound to the Vue instance.

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

- **참고하세요:** [Methods and Event Handling](/guide/events.html)

## watch

- **자료형:** `Object`

- **상세:**

  An object where keys are expressions to watch and values are the corresponding callbacks. The value can also be a string of a method name, or an Object that contains additional options. The Vue instance will call `$watch()` for each entry in the object at instantiation.

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
      // string method name
      'b': 'someMethod',
      // deep watcher
      'c': {
        handler: function (val, oldVal) { /* ... */ },
        deep: true
      }
    }
  })
  vm.a = 2 // -> new: 2, old: 1
  ```

- **참고하세요:** [Instance Methods - vm.$watch](#vm-watch)

# Options / DOM

## el

- **자료형:** `String | HTMLElement | Function`

- **제한:** only accepts type `Function` when used in a component definition.

- **상세:**

  Provide the Vue instance an existing DOM element to mount on. It can be a CSS selector string, an actual HTMLElement, or a function that returns an HTMLElement. Note that the provided element merely serves as a mounting point; it will be replaced if a template is also provided, unless `replace` is set to false. The resolved element will be accessible as `vm.$el`.

  When used in `Vue.extend`, a function must be provided so each instance gets a separately created element.

  If this option is available at instantiation, the instance will immediately enter compilation; otherwise, the user will have to explicitly call `vm.$mount()` to manually start the compilation.

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

## template

- **자료형:** `String`

- **상세:**

  A string template to be used as the markup for the Vue instance. By default, the template will **replace** the mounted element. When the `replace` option is set to `false`, the template will be inserted into the mounted element instead. In both cases, any existing markup inside the mounted element will be ignored, unless content distribution slots are present in the template.

  If the string starts with `#` it will be used as a querySelector and use the selected element's innerHTML as the template string. This allows the use of the common `<script type="x-template">` trick to include templates.

  Note that under certain situations, for example when the template contains more than one top-level element, or contains only plain text, the instance will become a fragment instance - i.e. one that manages a list of nodes rather than a single node. Non flow-control directives on the mount point for fragment instances are ignored.

- **참고하세요:**

  - [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)
  - [Content Distribution](/guide/components.html#Content-Distribution-with-Slots)
  - [Fragment Instance](/guide/components.html#Fragment-Instance)

## replace

- **자료형:** `Boolean`

- **기본값:** `true`

- **제한:** only respected if the **template** option is also present.

- **상세:**

  Determines whether to replace the element being mounted on with the template. If set to `false`, the template will overwrite the element's inner content without replacing the element itself. If set to `true`, the template will overwrite the element and merge the element's attributes with the attributes of the component's root node.

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

  Will result in:

  ```html
  <p class="foo bar" id="replace">replaced</p>
  ```

  In comparison, when `replace` is set to `false`:

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

  Will result in:

  ```html
  <div id="insert" class="foo">
    <p class="bar">inserted</p>
  </div>
  ```

# 옵션 / 라이프사이클 훅

## init

- **자료형:** `Function`

- **상세:**

  Called synchronously after the instance has just been initialized, before data observation and event / watcher setup.

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

## created

- **자료형:** `Function`

- **상세:**

  Called synchronously after the instance is created. At this stage, the instance has finished processing the options which means the following have been set up: data observation, computed properties, methods, watch/event callbacks. However, DOM compilation has not been started, and the `$el` property will not be available yet.

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

## beforeCompile

- **자료형:** `Function`

- **상세:**

  Called right before the compilation starts.

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

## compiled

- **자료형:** `Function`

- **상세:**

  Called after the compilation is finished. At this stage all directives have been linked so data changes will trigger DOM updates. However, `$el` is not guaranteed to have been inserted into the document yet.

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

## ready

- **자료형:** `Function`

- **상세:**

  Called after compilation **and** the `$el` is **inserted into the document for the first time**, i.e. right after the first `attached` hook. Note this insertion must be executed via Vue (with methods like `vm.$appendTo()` or as a result of a directive update) to trigger the `ready` hook.

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

## attached

- **자료형:** `Function`

- **상세:**

  Called when `vm.$el` is attached to DOM by a directive or a VM instance method such as `$appendTo()`. Direct manipulation of `vm.$el` will **not** trigger this hook.

## detached

- **자료형:** `Function`

- **상세:**

  Called when `vm.$el` is removed from the DOM by a directive or a VM instance method. Direct manipulation of `vm.$el` will **not** trigger this hook.

## beforeDestroy

- **자료형:** `Function`

- **상세:**

  Called right before a Vue instance is destroyed. At this stage the instance is still fully functional.

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

## destroyed

- **자료형:** `Function`

- **상세:**

  Called after a Vue instance has been destroyed. When this hook is called, all bindings and directives of the Vue instance have been unbound and all child Vue instances have also been destroyed.

  Note if there is a leaving transition, the `destroyed` hook is called **after** the transition has finished.

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

# Options / Assets

## directives

- **자료형:** `Object`

- **상세:**

  A hash of directives to be made available to the Vue instance.

- **참고하세요:**

  - [Custom Directives](/guide/custom-directive.html)
  - [Assets Naming Convention](/guide/components.html#Assets-Naming-Convention)

## elementDirectives

- **자료형:** `Object`

- **상세:**

  A hash of element directives to be made available to the Vue instance.

- **참고하세요:**

  - [Element Directives](/guide/custom-directive.html#Element-Directives)
  - [Assets Naming Convention](/guide/components.html#Assets-Naming-Convention)

## filters

- **자료형:** `Object`

- **상세:**

  A hash of filters to be made available to the Vue instance.

- **참고하세요:**

  - [Custom Filters](/guide/custom-filter.html)
  - [Assets Naming Convention](/guide/components.html#Assets-Naming-Convention)

## components

- **자료형:** `Object`

- **상세:**

  A hash of components to be made available to the Vue instance.

- **참고하세요:**

  - [Components](/guide/components.html)

## transitions

- **자료형:** `Object`

- **상세:**

  A hash of transitions to be made available to the Vue instance.

- **참고하세요:**

  - [Transitions](/guide/transitions.html)

## partials

- **자료형:** `Object`

- **상세:**

  A hash of partial strings to be made available to the Vue instance.

- **참고하세요:**

  - [Special Elements - partial](#partial)

# 옵션 / 기타

## parent

- **자료형:** `Vue instance`

- **상세:**

  Specify the parent instance for the instance to be created. Establishes a parent-child relationship between the two. The parent will be accessible as `this.$parent` for the child, and the child will be pushed into the parent's `$children` array.

- **참고하세요:** [Parent-Child Communication](/guide/components.html#Parent-Child-Communication)

## events

- **자료형:** `Object`

- **상세:**

  An object where keys are events to listen for and values are the corresponding callbacks. Note these are Vue events rather than DOM events. The value can also be a string of a method name. The Vue instance will call `$on()` for each entry in the object at instantiation.

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
      // can also use a string for methods
      bye: 'sayGoodbye'
    },
    methods: {
      sayGoodbye: function () {
        console.log('goodbye!')
      }
    }
  }) // -> created!
  vm.$emit('greeting', 'hi!') // -> hi!
  vm.$emit('bye')             // -> goodbye!
  ```

- **참고하세요:**

  - [Instance Methods - Events](#Instance-Methods-Events)
  - [Parent-Child Communication](/guide/components.html#Parent-Child-Communication)

## mixins

- **자료형:** `Array`

- **상세:**

  The `mixins` option accepts an array of mixin objects. These mixin objects can contain instance options just like normal instance objects, and they will be merged against the eventual options using the same option merging logic in `Vue.extend()`. e.g. If your mixin contains a created hook and the component itself also has one, both functions will be called.

  Mixin hooks are called in the order they are provided, and called before the component's own hooks.

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

- **참고하세요:** [Mixins](/guide/mixins.html)

## name

- **자료형:** `String`

- **제한:** only respected when used in `Vue.extend()`.

- **상세:**

  Allow the component to recursively invoke itself in its template. Note that when a component is registered globally with `Vue.component()`, the global ID is automatically set as its name.

  Another benefit of specifying a `name` option is console inspection. When inspecting an extended Vue component in the console, the default constructor name is `VueComponent`, which isn't very informative. By passing in an optional `name` option to `Vue.extend()`, you will get a better inspection output so that you know which component you are looking at. The string will be camelized and used as the component's constructor name.

- **예제**

  ```javascript
  var Ctor = Vue.extend({
    name: 'stack-overflow',
    template:
      '<div>' +
        // recursively invoke self
        '<stack-overflow></stack-overflow>' +
      '</div>'
  })

  // this will actually result in a max stack size exceeded
  // error, but let's assume it works...
  var vm = new Ctor()

  console.log(vm) // -> StackOverflow {$el: null, ...}
  ```

## extends

> 1.0.22+

- **자료형:** `Object | Function`

- **상세:**

  Allows declaratively extending another component (could be either a plain options object or a constructor) without having to use `Vue.extend`. This is primarily intended to make it easier to extend between single file components.

  This is similar to `mixins`, the difference being that the component's own options takes higher priority than the source component being extended.

- **예제**

  ```javascript
  var CompA = { ... }

  // extend CompA without having to call Vue.extend on either
  var CompB = {
    extends: CompA,
    ...
  }
  ```

# 인스턴스 속성

## vm.$data

- **자료형:** `Object`

- **상세:**

  The data object that the Vue instance is observing. You can swap it with a new object. The Vue instance proxies access to the properties on its data object.

## vm.$el

- **자료형:** `HTMLElement`

- **읽기 전용**

- **상세:**

  The DOM element that the Vue instance is managing. Note that for [Fragment Instances](/guide/components.html#Fragment-Instance), `vm.$el` will return an anchor node that indicates the starting position of the fragment.

## vm.$options

- **자료형:** `Object`

- **읽기 전용**

- **상세:**

  The instantiation options used for the current Vue instance. This is useful when you want to include custom properties in the options:

  ```javascript
  new Vue({
    customOption: 'foo',
    created: function () {
      console.log(this.$options.customOption) // -> 'foo'
    }
  })
  ```

## vm.$parent

- **자료형:** `Vue instance`

- **읽기 전용**

- **상세:**

  The parent instance, if the current instance has one.

## vm.$root

- **자료형:** `Vue instance`

- **읽기 전용**

- **상세:**

  The root Vue instance of the current component tree. If the current instance has no parents this value will be itself.

## vm.$children

- **자료형:** `Array<Vue instance>`

- **읽기 전용**

- **상세:**

  The direct child components of the current instance.

## vm.$refs

- **자료형:** `Object`

- **읽기 전용**

- **상세:**

  An object that holds child components that have `v-ref` registered.

- **참고하세요:**

  - [Child Component Refs](/guide/components.html#Child-Component-Refs)
  - [v-ref](#v-ref).

## vm.$els

- **자료형:** `Object`

- **읽기 전용**

- **상세:**

  An object that holds DOM elements that have `v-el` registered.

- **참고하세요:** [v-el](#v-el).

# Instance Methods / Data

## vm.$watch( expOrFn, callback, [options] )

- **전달인자:**

  - `{String | Function} expOrFn`
  - `{Function} callback`
  - `{Object} [options]`

    - `{Boolean} deep`
    - `{Boolean} immediate`

- **반환 값:** `{Function} unwatch`

- **사용방법:**

  Watch an expression or a computed function on the Vue instance for changes. The callback gets called with the new value and the old value. The expression can be a single keypath or any valid binding expressions.

Note: when mutating (rather than replacing) an Object or an Array, the old value will be the same as new value because they reference the same Object/Array. Vue doesn't keep a copy of the pre-mutate value.

- **예제**

  ```javascript
  // keypath
  vm.$watch('a.b.c', function (newVal, oldVal) {
    // do something
  })

  // expression
  vm.$watch('a + b', function (newVal, oldVal) {
    // do something
  })

  // function
  vm.$watch(
    function () {
      return this.a + this.b
    },
    function (newVal, oldVal) {
      // do something
    }
  )
  ```

  `vm.$watch` returns an unwatch function that stops firing the callback:

  ```javascript
  var unwatch = vm.$watch('a', cb)
  // later, teardown the watcher
  unwatch()
  ```

- **옵션: deep**

  To also detect nested value changes inside Objects, you need to pass in `deep: true` in the options argument. Note that you don't need to do so to listen for Array mutations.

  ```javascript
  vm.$watch('someObject', callback, {
    deep: true
  })
  vm.someObject.nestedValue = 123
  // callback is fired
  ```

- **옵션: immediate**

  Passing in `immediate: true` in the option will trigger the callback immediately with the current value of the expression:

  ```javascript
  vm.$watch('a', callback, {
    immediate: true
  })
  // callback is fired immediately with current value of `a`
  ```

## vm.$get( expression )

- **전달인자:**

  - `{String} expression`

- **사용방법:**

  Retrieve a value from the Vue instance given an expression. Expressions that throw errors will be suppressed and return `undefined`.

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

## vm.$set( keypath, value )

- **전달인자:**

  - `{String} keypath`
  - `{*} value`

- **사용방법:**

  Set a data value on the Vue instance given a valid keypath. In most cases you should prefer setting properties using plain object syntax, e.g. `vm.a.b = 123`. This method is only needed in two scenarios:

  1. When you have a keypath string and want to dynamically set the value using that keypath.

  2. When you want to set a property that doesn't exist.

  If the path doesn't exist it will be recursively created and made reactive. If a new root-level reactive property is created due to a `$set` call, the Vue instance will be forced into a "digest cycle", during which all its watchers are re-evaluated.

- **예제**

  ```javascript
  var vm = new Vue({
    data: {
      a: {
        b: 1
      }
    }
  })

  // set an existing path
  vm.$set('a.b', 2)
  vm.a.b // -> 2

  // set a non-existent path, will force digest
  vm.$set('c', 3)
  vm.c // -> 3
  ```

- **참고하세요:** [Reactivity in Depth](/guide/reactivity.html)

## vm.$delete( key )

- **전달인자:**

  - `{String} key`

- **사용방법:**

  Delete a root level property on the Vue instance (and also its `$data`). Forces a digest cycle. Not recommended.

## vm.$eval( expression )

- **전달인자:**

  - `{String} expression`

- **사용방법:**

  Evaluate a valid binding expression on the current instance. The expression can also contain filters.

- **예제**

  ```javascript
  // assuming vm.msg = 'hello'
  vm.$eval('msg | uppercase') // -> 'HELLO'
  ```

## vm.$interpolate( templateString )

- **전달인자:**

  - `{String} templateString`

- **사용방법:**

  Evaluate a piece of template string containing mustache interpolations. Note that this method simply performs string interpolation; attribute directives are ignored.

- **예제**

  ```javascript
  // assuming vm.msg = 'hello'
  vm.$interpolate('{{msg}} world!') // -> 'hello world!'
  ```

## vm.$log( [keypath] )

- **전달인자:**

  - `{String} [keypath]`

- **사용방법:**

  Log the current instance data as a plain object, which is more inspection-friendly than a bunch of getter/setters. Also accepts an optional key.

  ```javascript
  vm.$log() // logs entire ViewModel data
  vm.$log('item') // logs vm.item
  ```

# 인스턴스 메소드 / 이벤트

## vm.$on( event, callback )

- **전달인자:**

  - `{String} event`
  - `{Function} callback`

- **사용방법:**

  Listen for a custom event on the current vm. Events can be triggered by `vm.$emit`, `vm.$dispatch` or `vm.$broadcast`. The callback will receive all the additional arguments passed into these event-triggering methods.

- **예제**

  ```javascript
  vm.$on('test', function (msg) {
    console.log(msg)
  })
  vm.$emit('test', 'hi')
  // -> "hi"
  ```

## vm.$once( event, callback )

- **전달인자:**

  - `{String} event`
  - `{Function} callback`

- **사용방법:**

  Listen for a custom event, but only once. The listener will be removed once it triggers for the first time.

## vm.$off( [event, callback] )

- **전달인자:**

  - `{String} [event]`
  - `{Function} [callback]`

- **사용방법:**

  Remove event listener(s).

  - If no arguments are provided, remove all event listeners;

  - If only the event is provided, remove all listeners for that event;

  - If both event and callback are given, remove the listener for that specific callback only.

## vm.$emit( event, [...args] )

- **전달인자:**

  - `{String} event`
  - `[...args]`

  Trigger an event on the current instance. Any additional arguments will be passed into the listener's callback function.

## vm.$dispatch( event, [...args] )

- **전달인자:**

  - `{String} event`
  - `[...args]`

- **사용방법:**

  Dispatch an event, first triggering it on the instance itself, and then propagates upward along the parent chain. The propagation stops when it triggers a parent event listener, unless that listener returns `true`. Any additional arguments will be passed into the listener's callback function.

- **예제**

  ```javascript
  // create a parent chain
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
  // parent is NOT notified, because child1 didn't return
  // true in its callback
  ```

- **참고하세요:** [Parent-Child Communication](/guide/components.html#Parent-Child-Communication)

## vm.$broadcast( event, [...args] )

- **전달인자:**

  - `{String} event`
  - `[...args]`

- **사용방법:**

  Broadcast an event that propagates downward to all descendants of the current instance. Since the descendants expand into multiple sub-trees, the event propagation will follow many different "paths". The propagation for each path will stop when a listener callback is fired along that path, unless the callback returns `true`.

- **예제**

  ```javascript
  var parent = new Vue()
  // child1 and child2 are siblings
  var child1 = new Vue({ parent: parent })
  var child2 = new Vue({ parent: parent })
  // child3 is nested under child2
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
  // child3 is NOT notified, because child2 didn't return
  // true in its callback
  ```

# 인스턴스 메소드 / DOM

## vm.$appendTo( elementOrSelector, [callback] )

- **전달인자:**

  - `{Element | String} elementOrSelector`
  - `{Function} [callback]`

- **반환 값:** `vm` - the instance itself

- **사용방법:**

  Append the Vue instance's DOM element or fragment to target element. The target can be either an element or a querySelector string. This method will trigger transitions if present. The callback is fired after the transition has completed (or immediately if no transition has been triggered).

## vm.$before( elementOrSelector, [callback] )

- **전달인자:**

  - `{Element | String} elementOrSelector`
  - `{Function} [callback]`

- **반환 값:** `vm` - the instance itself

- **사용방법:**

  Insert the Vue instance's DOM element or fragment before target element. The target can be either an element or a querySelector string. This method will trigger transitions if present. The callback is fired after the transition has completed (or immediately if no transition has been triggered).

## vm.$after( elementOrSelector, [callback] )

- **전달인자:**

  - `{Element | String} elementOrSelector`
  - `{Function} [callback]`

- **반환 값:** `vm` - the instance itself

- **사용방법:**

  Insert the Vue instance's DOM element or fragment after target element. The target can be either an element or a querySelector string. This method will trigger transitions if present. The callback is fired after the transition has completed (or immediately if no transition has been triggered).

## vm.$remove( [callback] )

- **전달인자:**

  - `{Function} [callback]`

- **반환 값:** `vm` - the instance itself

- **사용방법:**

  Remove the Vue instance's DOM element or fragment from the DOM. This method will trigger transitions if present. The callback is fired after the transition has completed (or immediately if no transition has been triggered).

## vm.$nextTick( callback )

- **전달인자:**

  - `{Function} [callback]`

- **사용방법:**

  Defer the callback to be executed after the next DOM update cycle. Use it immediately after you've changed some data to wait for the DOM update. This is the same as the global `Vue.nextTick`, except that the callback's `this` context is automatically bound to the instance calling this method.

- **예제**

  ```javascript
  new Vue({
    // ...
    methods: {
      // ...
      example: function () {
        // modify data
        this.message = 'changed'
        // DOM is not updated yet
        this.$nextTick(function () {
          // DOM is now updated
          // `this` is bound to the current instance
          this.doSomethingElse()
        })
      }
    }
  })
  ```

- **참고하세요:**

  - [Vue.nextTick](#Vue-nextTick)
  - [Async Update Queue](/guide/reactivity.html#Async-Update-Queue)

# 인스턴스 메소드 / 라이프사이클

## vm.$mount( [elementOrSelector] )

- **전달인자:**

  - `{Element | String} [elementOrSelector]`

- **반환 값:** `vm` - the instance itself

- **사용방법:**

  If a Vue instance didn't receive the `el` option at instantiation, it will be in "unmounted" state, without an associated DOM element or fragment. `vm.$mount()` can be used to manually start the mounting/compilation of an unmounted Vue instance.

  If no argument is provided, the template will be created as an out-of-document fragment, and you will have to use other DOM instance methods to insert it into the document yourself. If `replace` option is set to `false`, then an empty `<div>` will be automatically created as the wrapper element.

  Calling `$mount()` on an already mounted instance will have no effect. The method returns the instance itself so you can chain other instance methods after it.

- **예제**

  ```javascript
  var MyComponent = Vue.extend({
    template: '<div>Hello!</div>'
  })

  // create and mount to #app (will replace #app)
  new MyComponent().$mount('#app')

  // the above is the same as:
  new MyComponent({ el: '#app' })

  // or, compile off-document and append afterwards:
  new MyComponent().$mount().$appendTo('#container')
  ```

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

## vm.$destroy( [remove] )

- **전달인자:**

  - `{Boolean} [remove] - default: false`

- **사용방법:**

  Completely destroy a vm. Clean up its connections with other existing vms, unbind all its directives, turn off all event listeners and, if the `remove` argument is true, remove its associated DOM element or fragment from the DOM.

  Triggers the `beforeDestroy` and `destroyed` hooks.

- **참고하세요:** [Lifecycle Diagram](/guide/instance.html#Lifecycle-Diagram)

# 지시어

## v-text

- **요구조건** `String`

- **상세:**

  Updates the element's `textContent`.

  Internally, `{% raw %}{{ Mustache }}{% endraw %}` interpolations are also compiled as a `v-text` directive on a textNode. The directive form requires a wrapper element, but offers slightly better performance and avoids FOUC (Flash of Uncompiled Content).

- **예제**

  ```html
  <span v-text="msg"></span>
  <!-- same as -->
  <span>{{msg}}</span>
  ```

## v-html

- **요구조건** `String`

- **상세:**

  Updates the element's `innerHTML`. The contents are inserted as plain HTML - data bindings are ignored. If you need to reuse template pieces, you should use [partials](#partial).

  Internally, `{% raw %}{{{ Mustache }}}{% endraw %}` interpolations are also compiled as a `v-html` directive using anchor nodes. The directive form requires a wrapper element, but offers slightly better performance and avoids FOUC (Flash of Uncompiled Content).

  Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to [XSS attacks](https://en.wikipedia.org/wiki/Cross-site_scripting). Only use `v-html` on trusted content and **never** on user-provided content.

- **예제**

  ```html
  <div v-html="html"></div>
  <!-- same as -->
  <div>{{{html}}}</div>
  ```

## v-if

- **요구조건** `*`

- **사용방법:**

  Conditionally render the element based on the truthy-ness of the expression value. The element and its contained data bindings / components are destroyed and re-constructed during toggles. If the element is a `<template>` element, its content will be extracted as the conditional block.

- **참고하세요:** [Conditional Rendering](/guide/conditional.html)

## v-show

- **요구조건** `*`

- **사용방법:**

  Toggle's the element's `display` CSS property based on the truthy-ness of the expression value. Triggers transitions if present.

- **참고하세요:** [Conditional Rendering - v-show](/guide/conditional.html#v-show)

## v-else

- **요구조건이 없습니다**

- **제한:** previous sibling element must have `v-if` or `v-show`.

- **사용방법:**

  Denote the "else block" for `v-if` and `v-show`.

  ```html
  <div v-if="Math.random() > 0.5">
    Sorry
  </div>
  <div v-else>
    Not sorry
  </div>
  ```

- **참고하세요:** [Conditional Rendering - v-else](/guide/conditional.html#v-else)

- **참고하세요:** [Conditional Rendering - Component caveat](/guide/conditional.html#Component-caveat)

## v-for

- **요구조건** `Array | Object | Number | String`

- **파라미터 속성:**

  - [`track-by`](/guide/list.html#track-by)
  - [`stagger`](/guide/transitions.html#Staggering-Transitions)
  - [`enter-stagger`](/guide/transitions.html#Staggering-Transitions)
  - [`leave-stagger`](/guide/transitions.html#Staggering-Transitions)

- **사용방법:**

  Render the element or template block multiple times based on the source data. The directive's value must use the special syntax `alias (in|of) expression` to provide an alias for the current element being iterated on:

  ```html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  Note using `of` as the delimiter is only supported in 1.0.17+.

  Alternatively, you can also specify an alias for the index (or the key if used on an Object):

  ```html
  <div v-for="(index, item) in items"></div>
  <div v-for="(key, val) in object"></div>
  ```

  The detailed usage for `v-for` is explained in the guide section linked below.

- **참고하세요:** [List Rendering](/guide/list.html).

## v-on

- **짧게쓰기:** `@`

- **요구조건** `Function | Inline Statement`

- **전달인자:** `event (required)`

- **변경자:**

  - `.stop` - call `event.stopPropagation()`.
  - `.prevent` - call `event.preventDefault()`.
  - `.capture` - add event listener in capture mode.
  - `.self` - only trigger handler if event was dispatched from this element.
  - `.{keyCode | keyAlias}` - only trigger handler on certain keys.

- **사용방법:**

  Attaches an event listener to the element. The event type is denoted by the argument. The expression can either be a method name or an inline statement, or simply omitted when there are modifiers present.

  When used on a normal element, it listens to **native DOM events** only. When used on a custom element component, it also listens to **custom events** emitted on that child component.

  When listening to native DOM events, the method receives the native event as the only argument. If using inline statement, the statement has access to the special `$event` property: `v-on:click="handle('ok', $event)"`.

  **1.0.11+** When listening the custom events, inline statements have access to the special `$arguments` property, which is an array of the additional arguments passed to the child components' `$emit` call.

- **예제**

  ```html
  <!-- method handler -->
  <button v-on:click="doThis"></button>

  <!-- inline statement -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- shorthand -->
  <button @click="doThis"></button>

  <!-- stop propagation -->
  <button @click.stop="doThis"></button>

  <!-- prevent default -->
  <button @click.prevent="doThis"></button>

  <!-- prevent default without expression -->
  <form @submit.prevent></form>

  <!-- chain modifiers -->
  <button @click.stop.prevent="doThis"></button>

  <!-- key modifier using keyAlias -->
  <input @keyup.enter="onEnter">

  <!-- key modifier using keyCode -->
  <input @keyup.13="onEnter">
  ```

  Listening to custom events on a child component (the handler is called when "my-event" is emitted on the child):

  ```html
  <my-component @my-event="handleThis"></my-component>

  <!-- inline statement -->
  <my-component @my-event="handleThis(123, $arguments)"></my-component>
  ```

- **참고하세요:** [Methods and Event Handling](/guide/events.html)

## v-bind

- **짧게쓰기:** `:`

- **요구조건** `* (with argument) | Object (without argument)`

- **전달인자:** `attrOrProp (optional)`

- **변경자:**

  - `.sync` - make the binding two-way. Only respected for prop bindings.
  - `.once` - make the binding one-time. Only respected for prop bindings.
  - `.camel` - convert the attribute name to camelCase when setting it. Only respected for normal attributes. Used for binding camelCase SVG attributes.

- **사용방법:**

  Dynamically bind one or more attributes, or a component prop to an expression.

  When used to bind the `class` or `style` attribute, it supports additional value types such as Array or Objects. See linked guide section below for more details.

  When used for prop binding, the prop must be properly declared in the child component. Prop bindings can specify a different binding type using one of the modifiers.

  When used without an argument, can be used to bind an object containing attribute name-value pairs. Note in this mode `class` and `style` does not support Array or Objects.

- **예제**

  ```html
  <!-- bind an attribute -->
  <img v-bind:src="imageSrc">

  <!-- shorthand -->
  <img :src="imageSrc">

  <!-- class binding -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">

  <!-- style binding -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- binding an object of attributes -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- prop binding. "prop" must be declared in my-component. -->
  <my-component :prop="someThing"></my-component>

  <!-- two-way prop binding -->
  <my-component :prop.sync="someThing"></my-component>

  <!-- one-time prop binding -->
  <my-component :prop.once="someThing"></my-component>
  ```

- **참고하세요:**

  - [Class and Style Bindings](/guide/class-and-style.html)
  - [Component Props](/guide/components.html#Props)

## v-model

- **요구조건** varies based on input type

- **제한됨:**

  - `<input>`
  - `<select>`
  - `<textarea>`

- **파라미터 속성:**

  - [`lazy`](/guide/forms.html#lazy)
  - [`number`](/guide/forms.html#number)
  - [`debounce`](/guide/forms.html#debounce)

- **사용방법:**

  Create a two-way binding on a form input element. For detailed usage, see guide section linked below.

- **참고하세요:** [Form Input Bindings](/guide/forms.html)

## v-ref

- **요구조건이 없습니다**

- **제한됨:** child components

- **전달인자:** `id (required)`

- **사용방법:**

  Register a reference to a child component on its parent for direct access. Does not expect an expression. Must provide an argument as the id to register with. The component instance will be accessible on its parent's `$refs` object.

  When used on a component together with `v-for`, the registered value will be an Array containing all the child component instances corresponding to the Array they are bound to. If the data source for `v-for` is an Object, the registered value will be an Object containing key-instance pairs mirroring the source Object.

- **참고:**

  Because HTML is case-insensitive, camelCase usage like `v-ref:someRef` will be converted to all lowercase. You can use `v-ref:some-ref` which properly sets `this.$refs.someRef`.

- **예제**

  ```html
  <comp v-ref:child></comp>
  <comp v-ref:some-child></comp>
  ```

  ```javascript
  // access from parent:
  this.$refs.child
  this.$refs.someChild
  ```

  With `v-for`:

  ```html
  <comp v-ref:list v-for="item in list"></comp>
  ```

  ```javascript
  // this will be an array in parent
  this.$refs.list
  ```

- **참고하세요:** [Child Component Refs](/guide/components.html#Child-Component-Refs)

## v-el

- **요구조건이 없습니다**

- **전달인자:** `id (required)`

- **사용방법:**

  Register a reference to a DOM element on its owner Vue instance's `$els` object for easier access.

- **참고:**

  Because HTML is case-insensitive, camelCase usage like `v-el:someEl` will be converted to all lowercase. You can use `v-el:some-el` which properly sets `this.$els.someEl`.

- **예제**

  ```html
  <span v-el:msg>hello</span>
  <span v-el:other-msg>world</span>
  ```

  ```javascript
  this.$els.msg.textContent // -> "hello"
  this.$els.otherMsg.textContent // -> "world"
  ```

## v-pre

- **요구조건이 없습니다**

- **사용방법**

  Skip compilation for this element and all its children. You can use this for displaying raw mustache tags. Skipping large numbers of nodes with no directives on them can also speed up compilation.

- **예제**

  ```html
  <span v-pre>{{ this will not be compiled }}</span>
  ```

## v-cloak

- **요구조건이 없습니다**

- **사용방법:**

  This directive will remain on the element until the associated Vue instance finishes compilation. Combined with CSS rules such as `[v-cloak] { display: none }`, this directive can be used to hide un-compiled mustache bindings until the Vue instance is ready.

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

  The `<div>` will not be visible until the compilation is done.

# Special Elements

## component

- **속성:**

  - `is`

- **파라미터 속성:**

  - [`keep-alive`](/guide/components.html#keep-alive)
  - [`transition-mode`](/guide/components.html#transition-mode)

- **사용방법:**

  Alternative syntax for invoking components. Primarily used for dynamic components with the `is` attribute:

  ```html
  <!-- a dynamic component controlled by -->
  <!-- the `componentId` property on the vm -->
  <component :is="componentId"></component>
  ```

- **참고하세요:** [Dynamic Components](/guide/components.html#Dynamic-Components)

## slot

- **속성:**

  - `name`

- **사용방법:**

  `<slot>` elements serve as content distribution outlets in component templates. The slot element itself will be replaced.

  A slot with the `name` attribute is called a named slot. A named slot will distribute content with a `slot` attribute that matches its name.

  For detailed usage, see the guide section linked below.

- **참고하세요:** [Content Distribution with Slots](/guide/components.html#Content-Distribution-with-Slots)

## partial

- **속성:**

  - `name`

- **사용방법:**

  `<partial>` elements serve as outlets for registered template partials. Partial contents are also compiled by Vue when inserted. The `<partial>` element itself will be replaced. It requires a `name` attribute which will be used to resolve the partial's content.

- **예제**

  ```javascript
  // registering a partial
  Vue.partial('my-partial', '<p>This is a partial! {{msg}}</p>')
  ```

  ```html
  <!-- a static partial -->
  <partial name="my-partial"></partial>

  <!-- a dynamic partial -->
  <!-- renders partial with id === vm.partialId -->
  <partial v-bind:name="partialId"></partial>

  <!-- dynamic partial using v-bind shorthand -->
  <partial :name="partialId"></partial>
  ```

# 필터

## capitalize

- **예제**

  ```html
  {{ msg | capitalize }}
  ```

  _'abc' => 'Abc'_

## uppercase

- **예제**

  ```html
  {{ msg | uppercase }}
  ```

  _'abc' => 'ABC'_

## lowercase

- **예제**

  ```html
  {{ msg | lowercase }}
  ```

  _'ABC' => 'abc'_

## currency

- **전달인자:**

  - `{String} [symbol] - default: '$'`
  - **1.0.22+** `{Number} [decimal places] - default: 2`

- **예제**

  ```html
  {{ amount | currency }}
  ```

  _12345 => $12,345.00_

  Use a different symbol:

  ```html
  {{ amount | currency '£' }}
  ```

  _12345 => £12,345.00_

  Some currencies have 3 or 4 decimal places, while some others have none, for example Japanese Yen (¥) or Vietnamese Dong (₫):

  ```html
  {{ amount | currency '₫' 0 }}
  ```

  _12345 => ₫12,345_

## pluralize

- **전달인자:**

  - `{String} single, [double, triple, ...]`

- **사용방법:**

  Pluralizes the argument based on the filtered value. When there is exactly one argument, plural forms simply add an "s" at the end. When there are more than one argument, the arguments will be used as array of strings corresponding to the single, double, triple ... forms of the word to be pluralized. When the number to be pluralized exceeds the length of the arguments, it will use the last entry in the array.

- **예제**

  ```html
  {{count}} {{count | pluralize 'item'}}
  ```

  _1 => '1 item'_ _2 => '2 items'_

  ```html
  {{date}}{{date | pluralize 'st' 'nd' 'rd' 'th'}}
  ```

  Will result in:

  _1 => '1st'_ _2 => '2nd'_ _3 => '3rd'_ _4 => '4th'_ _5 => '5th'_

## json

- **전달인자:**

  - `{Number} [indent] - default: 2`

- **사용방법:**

  Output the result of calling `JSON.stringify()` on the value instead of outputting the `toString()` value (e.g. `[object Object]`).

- **예제**

  Print an object with 4-space indent:

  ```html
  <pre>{{ nestedObject | json 4 }}</pre>
  ```

## debounce

- **제한됨:** directives that expect `Function` values, e.g. `v-on`

- **전달인자:**

  - `{Number} [wait] - default: 300`

- **사용방법:**

  Wrap the handler to debounce it for `x` milliseconds, where `x` is the argument. Default wait time is 300ms. A debounced handler will be delayed until at least `x` ms has passed after the call moment; if the handler is called again before the delay period, the delay period is reset to `x` ms.

- **예제**

  ```html
  <input @keyup="onKeyup | debounce 500">
  ```

## limitBy

- **제한됨:** directives that expect `Array` values, e.g. `v-for`

- **전달인자:**

  - `{Number} limit`
  - `{Number} [offset]`

- **사용방법:**

  Limit the array to the first N items, as specified by the argument. An optional second argument can be provided to set a starting offset.

  ```html
  <!-- only display first 10 items -->
  <div v-for="item in items | limitBy 10"></div>

  <!-- display items 5 to 15 -->
  <div v-for="item in items | limitBy 10 5"></div>
  ```

## filterBy

- **제한됨:** directives that expect `Array` values, e.g. `v-for`

- **전달인자:**

  - `{String | Function} targetStringOrFunction`
  - `"in" (optional delimiter)`
  - `{String} [...searchKeys]`

- **사용방법:**

  Return a filtered version of the source Array. The first argument can either be a string or a function.

  When the first argument is a string, it will be used as the target string to search for in each element of the Array:

  ```html
  <div v-for="item in items | filterBy 'hello'">
  ```

  In the above example, only items that contain the target string `"hello"` will be displayed.

  If the item is an object, the filter will recursively search every nested property of the object for the target string. To narrow down the search scope, additional search keys can be specified:

  ```html
  <div v-for="user in users | filterBy 'Jack' in 'name'">
  ```

  In the above example, the filter will only search for `"Jack"` in the `name` field of each user object. **It is a good idea to always limit the search scope for better performance.**

  The examples above are using static arguments - we can, of course, use dynamic arguments as target string or search keys. Combined with `v-model` we can easily implement type-ahead filtering:

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

  Multiple search keys:

  ```html
  <li v-for="user in users | filterBy searchText in 'name' 'phone'"></li>
  ```

  Multiple search keys with a dynamic Array argument:

  ```html
  <!-- fields = ['fieldA', 'fieldB'] -->
  <div v-for="user in users | filterBy searchText in fields">
  ```

  Use a custom filter function:

  ```html
  <div v-for="user in users | filterBy myCustomFilterFunction">
  ```

## orderBy

- **제한됨:** directives that expect `Array` values, e.g. `v-for`

- **전달인자:**

  - `{String | Array<String> | Function} ...sortKeys`
  - `{String} [order] - default: 1`

- **사용방법:**

  Return a sorted version of the source Array. You can pass any number of Strings to sort on keys. You can also pass an array containing the sorting keys or a Function if you want to use your own sorting strategy instead. The optional `order` argument specifies whether the result should be in ascending (`order >= 0`) or descending (`order < 0`) order.

  For arrays of primitive values, simply omit `sortKeys` and provide the order, e.g. `orderBy 1`.

- **예제**

  Sort users by name:

  ```html
  <ul>
    <li v-for="user in users | orderBy 'name'">
      {{ user.name }}
    </li>
  </ul>
  ```

  In descending order:

  ```html
  <ul>
    <li v-for="user in users | orderBy 'name' -1">
      {{ user.name }}
    </li>
  </ul>
  ```

  Sort primitive values:

  ```html
  <ul>
    <li v-for="n in numbers | orderBy true">
      {{ n }}
    </li>
  </ul>
  ```

  Dynamic sort order:

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

  Sort using two keys:

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

Sort using a Function:

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

# 배열 확장 메소드

Vue.js extends `Array.prototype` with two additional methods that makes it easier to perform some common Array operations while ensuring reactive updates are properly triggered.

## array.$set(index, value)

- **전달인자:**

  - `{Number} index`
  - `{*} value`

- **사용방법**

  Set an element in the array to a value by index and trigger view updates.

  ```javascript
  vm.animals.$set(0, { name: 'Aardvark' })
  ```

- **참고하세요:** [Array Detection Caveats](/guide/list.html#Caveats)

## array.$remove(reference)

- **전달인자:**

  - `{Reference} reference`

- **사용방법**

  Remove an element from an array by reference and trigger view updates. This is a sugar method for first searching for the element in the array, and then if found, calling `array.splice(index, 1)`.

  ```javascript
  var aardvark = vm.animals[0]
  vm.animals.$remove(aardvark)
  ```

- **참고하세요:** [Mutation Methods](/guide/list.html#Mutation-Methods)
