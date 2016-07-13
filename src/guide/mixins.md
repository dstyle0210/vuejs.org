---
title: 믹스인
type: guide
order: 16
---

## 기초

믹스인(mixin)은 Vue 컴포넌트에 재사용이 가능한 유연한 기능을 추가하는 방법입니다. 믹스인 객체는 모든 컴포넌트 옵션을 포함 할 수 있습니다. 컴포넌트가 믹스인을 사용하면 믹스인의 모든 옵션은 컴포넌트 자체의 옵션에 "추가"됩니다.

예:

``` js
// 믹스인 객체를 만듭니다.
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}

// 믹스인을 사용할 객체를 만듭니다.
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // -> "hello from mixin!"
```

## 옵션 병합

믹스 인과 컴포넌트 자체의 옵션과 중복일 때, 그들은 적절한 전략을 사용하여 "병합"됩니다. 예를 들어, 같은 이름을 가지는 훅 함수를 한 배열에 병합합니다. 또한, 믹스인을 이용한 훅은 컴포넌트자신의 훅 *이전*에 호출됩니다 :

``` js
var mixin = {
  created: function () {
    console.log('mixin hook called')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('component hook called')
  }
})

// -> "mixin hook called"
// -> "component hook called"
```

객체를 기대하는 옵션은 예를 들어, `methods`, `components`, 그리고 `directives`들은 같은 객체에 병합됩니다. 컴포넌트 옵션은 이러한 객체의 키 충돌이있을 때, 우선순위를 갖습니다:

``` js
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo() // -> "foo"
vm.bar() // -> "bar"
vm.conflicting() // -> "from self"
```

같은 병합 전략이 `Vue.extend()`에서 사용되는 것을 확인하세요.

## 전역 믹스인

전역 믹스인을 적용 할 수 있습니다. 사용에 주의하십시오! 일단 전역 믹스인을 적용하면 그 다음에 만드는 **모든** Vue 인스턴스에 영향을줍니다. 적절히 사용 하면,  사용자 지정 옵션에 대한 처리 로직을 주입하는 데 사용할 수 있습니다 :

``` js
//  사용자 지정 옵션`myOption`에 핸들러를 주입합니다.
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// -> "hello!"
```
<p class="tip">써드파티 컴포넌트를 포함하는 모든 단일 작성된 Vue 인스턴스에 영향이 있기 때문에 전역 믹스인은 적게 그리고 조심스럽게 사용하십시오. 대부분의 경우, 위의 예와 같은 사용자 지정 옵션을 처리하는 것과 사용해야합니다.</p>

## 사용자 정의 옵션의 병합 전략

사용자 지정 옵션이 병합 될 때, 그들은 단순히 기존 값을 덮어 씁니다. 사용자 지정 논리를 사용하여 사용자 지정 옵션을 병합하는 경우 `Vue.config.optionMergeStrategies`를 첨부해야합니다 :

``` js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // 병합 된 값을 반환
}
```

대부분의 객체 기반의 옵션은 단순히 `methods`에서 사용되는 것과 같은 전략을 사용할 수 있습니다 :

``` js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```
