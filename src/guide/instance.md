---
title: Vue 인스턴스
type: guide
order: 3
---

## 생성자

모든 Vue.js 응용 프로그램은 `Vue` 생성자 함수에서 **root의 Vue 인스턴스**를 자동으로 생성하며 시작합니다:

``` js
var vm = new Vue({
  // 옵션들
})
```

Vue 인스턴스는 본질적으로 [MVVM 패턴](https://en.wikipedia.org/wiki/Model_View_ViewModel)에서 정의 된 **ViewModel**이며, 따라서 변수 `vm`을 문서 중간 중간 볼 수 있습니다.

Vue 인스턴스를 인스턴스화 할 때 데이터에 대해 템플릿이나 마운트하는 요소와 메소드 라이프 사이클 콜백 등의 옵션을 포함한 **옵션 개체**에 전달해야합니다. 전체 옵션 목록은 [API reference](/api)에 있습니다.


Vue 생성자는 사전 정의 된 옵션을 사용하여 재사용 가능한 **컴포넌트 생성자**를 작성하기 위해 확장 할 수 있습니다:

``` js
var MyComponent = Vue.extend({
  // 확장 옵션들
})

// `MyComponent`의 모든 인스턴스들은
// 미리 정의된 확장옵션을 따라 만들어 집니다.
var myComponentInstance = new MyComponent()
```

명시적으로 확장 된 인스턴스를 만들 수 있지만 대부분의 경우 컴포넌트 생성자를 등록하고 이를 템플릿에서 사용할 수 있습니다. 컴포넌트 시스템에 대해서는 나중에 자세히 설명합니다. 모든 Vue.js 컴포넌트는 본질적으로 확장 된 Vue 인스턴스임을 알고 있어야 합니다.

## 속성과 메소드

각 Vue 인스턴스가 **프록시(proxy)**하는 모든 속성은 `data` 객체에 있습니다 :
``` js
var data = { a: 1 }
var vm = new Vue({
  data: data
})

vm.a === data.a // -> true

// 속성을 설정하면 원래 데이터에도 반영됩니다.
vm.a = 2
data.a // -> 2

// ... 그리고 반대의 경우도 마찬가지 입니다.
data.a = 3
vm.a // -> 3
```

이러한 프록시된 속성만 **반응형(reactive)**이라는 점에 유의해야합니다. 인스턴스가 작성된 후 해당 인스턴스에 새로운 속성을 붙여도 View의 업데이트는 실행되지 않습니다.

데이터의 속성 이외에 Vue 인스턴스는 몇 가지 유용한 인스턴스 속성과 메소드를 가지고 있습니다. 이러한 속성과 메서드는 프록시된 데이터 속성과 구별하기 위해 `$`가 앞에 붙습니다. 예를 들면 :

``` js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // -> true
vm.$el === document.getElementById('example') // -> true

// $watch 는 인스턴스 메소드입니다
vm.$watch('a', function (newVal, oldVal) {
  // `vm.a`이 변경될 때 콜백이 호출됩니다
})
```

인스턴스 속성과 메소드의 전체 목록은 [API reference](/api)를 참고하십시오.

## 인스턴스의 라이프사이클

인스턴스가 작성 될 때 각 Vue 인스턴스는, 일련의 초기화 단계를 수행합니다. 예를 들어, 데이터 모니터의 설정 및 템플릿 컴파일, 필요한 데이터 바인딩 만들기 등이 있습니다. 초기화 단계에서 사용자가 필요한 임의의 조작을 가능하게하는 몇 가지 **라이프사이클 훅**이 시작됩니다. 예를 들어, **created** 훅은 인스턴스가 생성 된 후 불립니다:

``` js
var vm = new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this`는 vm instance를 가리킵니다.
    console.log('a is: ' + this.a)
  }
})
// -> "a is: 1"
```

인스턴스의 라이프 사이클과 같은 꼴 단계로 불리는 다른 후크도 있습니다. 예를 들어, `compiled`, `ready`, 그리고 `destroyed` 등이 있습니다. 모든 라이프 사이클 훅은 Vue 인스턴스를 가리키는 `this` 함께 부릅니다. Vue.js의 세계에서 "컨트롤러"의 개념을 알고 싶은 분도 있을지도 모릅니다. 하지만 대답은 "Vue.js에는 컨트롤러가 없다". 컴포넌트에 대한 사용자 지정 코드는 이러한 라이프 사이클 후크에서 떨어져 있습니다.

## 라이프사이클 다이어그램

다음은 인스턴스 라이프사이클에 대한 다이어그램입니다. 이 단계에서 모든 것을 이해할 필요는 없지만,이 다이어그램은 앞으로도 많은 도움이 될 것입니다.
![Lifecycle](/images/lifecycle.png)
