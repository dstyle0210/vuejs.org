---
title: 계산된 속성
type: guide
order: 5
---

템플릿 표현식은 매우 유용합니다. 하지만 간단한 조작만 가능합니다. 여기서 템플릿은 당신의 뷰의 구조를 말합니다. 너무 많은 로직이 템플릿에 있게 되면 너무 커지게 되어 관리도 힘들게 됩니다. 이 때문에 Vue.js는 한번에 하나의 표현식만 바인딩 하도록 제한하였습니다. 한개 이상의 표현식이 필요한 경우에 반드시 **계산된 속성**을 사용해야 합니다.

### 기본 예

``` html
<div id="example">
  a={{ a }}, b={{ b }}
</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    a: 1
  },
  computed: {
    // 계산된 게터
    b: function () {
      // `this` 는 vm 객체를 말합니다
      return this.a + 1
    }
  }
})
```

결과:

{% raw %}
<div id="example" class="demo">
  a={{ a }}, b={{ b }}
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    a: 1
  },
  computed: {
    b: function () {
      return this.a + 1
    }
  }
})
</script>
{% endraw %}

`b`라는 계산된 속성을 선언했습니다. 게터 함수를 만들고 `vm.b` 와 같이 속성 처럼 이용할 수 있습니다:

``` js
console.log(vm.b) // -> 2
vm.a = 2
console.log(vm.b) // -> 3
```

콘솔에서 vm을 이용해서 테스트 해 볼 수 있습니다. `vm.b`의 값은 `vm.a`의 값에 의존적입니다

계산된 속성을 템플릿에서 일반 속성과 동일한 방법으로 사용할 수 있습니다. Vue는 `vm.b`이 `vm.a`에 의존하는 것을 알고 있습니다. 그래서 `vm.a`이 변경될 때 `vm.b`도 함께 변경됩니다. 그리고 우리가 종속관계를 선언할 수 있는 것이 가장 중요한 부분입니다: 계산된 게터 메소드는 깔끔하고 사이드 이펙트도 없습니다. 이는 작성하고 테스트하기에 쉽게 만들어 줍니다.

### 계산된 속성 vs. $watch

Vue.js는  API 메소드 `$watch`가 Vue 객체의 데이터가 변경되는 것을 알아채도록 만들지 않았습니다. 만약 데이터가 다른 데이터에 변경됨에 따라 변경되는 것을 알아채야할 때 - 아마도 AngularJS를 아는 경우 `$watch`를 사용하고 싶어질 수 있습니다.  그러나 계산된 속성을 사용하는 것이 피할수 없는 `$watch` 콜백보다 좋은 아이디어 입니다. 다음 예제를 보세요.

``` html
<div id="demo">{{fullName}}</div>
```

``` js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  }
})

vm.$watch('firstName', function (val) {
  this.fullName = val + ' ' + this.lastName
})

vm.$watch('lastName', function (val) {
  this.fullName = this.firstName + ' ' + val
})
```

위의 코드는 반복적이고 불필요합니다. 계산된 속성을 이용하는 버전을 보세요:

``` js
var vm = new Vue({
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

더 좋아보이지 않나요?

### 계산된 세터

계산된 속성은 게터만을 기본적으로 가지고 있습니다. 그러나 세터가 필요한 경우에 사용할 수 있습니다:

``` js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```
이제 `vm.fullName = 'John Doe'`처럼 사용할 수 있습니다. 세터는 `vm.firstName`와 `vm.lastName`를 호출하여 갱신 합니다.

어떻게 계산된 속성이 갱신되는지에 대한 기술적인 자세한 부분들은 [discussed in another section](reactivity.html#Inside-Computed-Properties)를 읽어보세요.
