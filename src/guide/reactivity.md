---
title: 반응성에 대해 깊이 알기
type: guide
order: 13
---

우리는 기본적인 것들 중 대부분을 커버 해 왔습니다. 이제는 깊이 파고들 때입니다! Vue.js의 가장 두드러진 특징 중 하나는 눈에 거슬리지 않는 반응형 시스템입니다. 모델은 단순한 기본 JavaScript 객체에서 변경된 View를 갱신합니다. 이는 상태 관리가 매우 간단하고 직관적이지만 몇 가지 일반적인 함정을 피하기 위해 어떻게 작동하는지 이해하는 것도 중요합니다. 이 섹션에서 우리는 Vue.js의 반응형 시스템의 낮은 수준의 세부 사항 중 일부에 대해 파고 들어갑니다.

## 변경된 내역을 추적하는 방법

기본 JavaScript 객체를 `data` 옵션으로 Vue 인스턴스에 전달할 때 Vue.js은 그 모든 속성들을 돌아다니며  [Object.defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)을 사용하여 getter/setter로 변환합니다. 이것은 ES5 만의 하위호환이 불가능한 기능으로, Vue.js가 IE8 이하를 지원하지 않는 이유입니다.

getter / setter는 사용자에게 보이지 않지만 내부적으로 Vue.js에서 종속성 추적하거나 속성이 접근 또는 변경될 때 사용됩니다. 주의 할 부분 중 하나는 데이터 오브젝트가 변경 된 때 getter/setter 브라우저 콘솔의 형식이 다르기 때문에 더 친숙하게 볼 수 있도록 출력하기 위해 `vm.$log()` 인스턴스 메소드를 사용하십시오.

템플릿의 모든 지시어/데이터 바인딩은 종속적으로 평가되는 동안 모든 속성을 건드린(touched) 것으로 기록하는 **watcher** 개체가 존재하고 있습니다. 그 후, 의존하는 setter가 호출 될 때, 그것은 다시 값을 개산할 watcher을 트리거하고 관련 지시어에 DOM을 업데이트합니다.

![data](/images/data.png)

## 변경 감지에 대한 주의사항

ES5의 제한으로 인해 Vue.js는 속성의 **추가 또는 삭제를 알아 챌 수 없습니다**. Vue.js는 인스턴스의 초기화 동안 getter/setter 변환 작업을 수행하기 위해 `data` 객체에 존재해야 합니다. 예를 들면 :

``` js
var data = { a: 1 }
var vm = new Vue({
  data: data
})
// `vm.a`와 `data.a` 는 이제 반응할 수 있습니다.

vm.b = 2
// `vm.b` 는 반응하지 않습니다.

data.b = 2
// `data.b` 는 반응하지 않습니다.
```

그러나 인스턴스 생성 후 **속성을 추가하여 반응성을 갖게하는 방법** 이 있습니다.

Vue 인스턴스에 대해 `$set(path, value)` 인스턴스 메소드를 사용할 수 있습니다:

``` js
vm.$set('b', 2)
// `vm.b`와 `data.b`는 이제 반응성을 갖습니다.
```

원시 데이터 객체를 위해 전역 `Vue.set(object, key, value)` 메소드를 사용할 수 있습니다:

``` js
Vue.set(data, 'c', 3)
// `vm.c` 와 `data.c` 는 이제 반응성을 갖습니다.
```

때로는 기존의 객체에 속성 갯수를 지정할 수 있습니다. 예를 들어, `Object.assign()` 또는 `_.extend()`를 사용합니다. 그러나 새로운 객체에 속성을 추가 할 때 트리거는 변경하지 않습니다. 이런 경우 원래 객체와 믹스인 개체의 속성을 모두 가진 새로운 개체를 만듭니다.

``` js
// `Object.assign(this.someObject, { a: 1, b: 2 })` 대신에
this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
```

배열 관계에서 약간의 주의 사항이 있습니다. [이전의 리스트 렌더링 섹션]([discussed earlier in the list rendering section](/guide/list.html#Caveats)을 확인하세요

## 데이터 초기화 하기

Vue.js는 동적으로 그 자리에서 반응형 속성을 추가하기위한 API를 제공하고 `data` 옵션에서 미리 반응형 속성을 선언 할 것을 권장합니다.

이 방법 대신에:

``` js
var vm = new Vue({
  template: '<div>{{msg}}</div>'
})
// add `msg` later
vm.$set('msg', 'Hello!')
```

아래 처럼 사용하세요:

``` js
var vm = new Vue({
  data: {
    // 빈 msg 속성을 추가하세요
    msg: ''
  },
  template: '<div>{{msg}}</div>'
})
// set `msg` later
vm.msg = 'Hello!'
```

이 방법을 사용하는데는 두가지 이유가 있습니다.

1. `data` 객체는 해당 컴포넌트의 상태에 대한 스키마와 같습니다. 모든 반응형 속성을 추가하는 것은 컴포넌트 코드를 이해하기 쉽게 만듭니다.

2. 최 상위 반응형 속성을 Vue 인스턴스에 추가한 경우 모든 watcher에게 재평가를 요구합니다. 이전에 없던 watcher가 새로 생겼기 때문에 종석성을 추적해야할 필요가 생기기 때문입니다. 성능은 보통 받아들일만 합니다. (Angular의 dirty 판단과 동일합니다.) 그러나 최초 생성시 속성을 추가하면 이를 피할 수 있습니다.

## 비동기 업데이트 큐


기본적으로 Vue.js는 **비동기 적으로** DOM 업데이트를 실행합니다. 데이터 변경이 감시되고있는한, Vue는 큐를 열고 같은 이벤트 루프에서 일어나는 모든 데이터 변경을 버퍼링합니다. 같은 watcher가 여러 번 발생하는 경우 한 번만 큐에 넣게됩니다. 그리고 다음 이벤트 루프의 "tick"에서 Vue는 큐를 비우고 필요한 DOM 업데이트만 실행합니다. 내부적으로는 Vue는 만약 비동기 대기열을 위한 `MutationObserver`를 사용할 수 있고, 그렇지 않으면 `setTimeout(fn, 0)`으로 대체합니다.

예를 들어, `vm.someData = 'new value'`를 사용했을 때, DOM은 즉시 업데이트하지 않습니다. 큐를 다 비운 후 다음 "tick"때 업데이트합니다. 대부분의 경우, 우리는 이에 대해 걱정할 필요는 없지만 업데이트 된 DOM의 상태에 따라 뭔가를하고 싶을 때는 주의가 필요합니다. Vue.js는 일반적으로 "데이터 기반"으로 생각하는 것을 개발자에게 권장하고 있지만, 때때로, 그것은 당신의 손을 더럽힐 필요가 있을지도 모릅니다. Vue.js에서 데이터 변경 후 DOM의 업데이트가 완료 될 때까지 기다릴 위해서는 데이터가 변경된 직후 `Vue.nextTick(callback)`을 사용 할 수 있습니다. 콜백이 호출되었을 때, DOM은 업데이트 된 것입니다. 예를 들면 :

``` html
<div id="example">{{msg}}</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    msg: '123'
  }
})
vm.msg = 'new message' // 데이터 변경
vm.$el.textContent === 'new message' // false
Vue.nextTick(function () {
  vm.$el.textContent === 'new message' // true
})
```

특히 편리한 내부 컴포넌트의 인스턴스 메소드 `vm.$nextTick()`도 있습니다. 전역 `Vue`는 `this`가 자동으로 바운드하기 때문에 필요하지 않습니다:

``` js
Vue.component('example', {
  template: '<span>{{msg}}</span>',
  data: function () {
    return {
      msg: 'not updated'
    }
  },
  methods: {
    updateMessage: function () {
      this.msg = 'updated'
      console.log(this.$el.textContent) // => 'not updated'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => 'updated'
      })
    }
  }
})
```

## 계산된 속성 자세히 보기

Vue.js 계산된 속성(computed property)의 getter는 간단하지 않은점을 유의해야합니다. 각 계산된 속성은 고유의 반응형 종속성을 추적합니다. 계산된 속성이 평가 될 때, Vue.js 종속성 목록을 업데이트하고 결과 값을 캐시합니다. 캐시 된 값이 추적 된 종속성 중 하나가 변경되었을 때만 비활성화됩니다. 따라서 종속성이 변경되지 않은 내내 산출 속성의 액세스는 getter를 불러내는 대신 직접 캐시 된 값을 반환합니다.

왜 우리는 캐싱을 해야할까요? 우리가 거대한 배열을 반복하여 계산을 많이해야하는 비용이 큰 계산된 속성 **A**를 가지고 있다고 상상하십시오. 그 후, 우리는 **A**에 의존하는 유사한 다른 계산된 속성을 가지고 있을지도 모릅니다. 캐싱이 없다면, 불필요하게 **A**의 getter를 호출 할 것입니다!

계산된 속성의 캐싱을 위해 getter 함수는 계산된 속성에 액세스 할 때 항상 호출되지 않습니다. 다음의 예를 생각해 보겠습니다:

``` js
var vm = new Vue({
  data: {
    msg: 'hi'
  },
  computed: {
    example: function () {
      return Date.now() + this.msg
    }
  }
})
```

계산된 속성인 `example`은 `vm.msg`라는 하나의 종속성을 가지고 있습니다. `Date.now()`는 타임스탬프는 Vue 데이터 모니터링 시스템과는 아무 상관이 없기 때문에 **반응형 종속성이 없습니다**. 따라서 프로그램에서 `vm.example`에 액세스 할 때 `vm.msg`가 재평가를 호출하지 않는 한 동일한 타임 스탬프를 찾을 수 있습니다.

일부 사례에서는 단순히 다시 getter를 호출 `vm.example`에 액세스 할 때마다 간단한 getter같은 행동을 저장할 수 없습니다. 특정 산출 속성에 대해 캐시를 해제하면 그것을 할 수 있습니다:

``` js
computed: {
  example: {
    cache: false,
    get: function () {
      return Date.now() + this.msg
    }
  }
}
```

그러면 `vm.example`에 액세스 할 때마다 타임 스탬프는 최신일 것입니다. **그러나 이것은 프로그램으로 JavaScript에서 액세스에만 적용한다**는 점에 유의하십시오. 데이터 바인딩은 여전히 종속성 구동입니다. 템플릿에서 `{% raw %}{{example}}{% endraw %}`로 계산된 속성에 바인딩 할 때, DOM은 반응형 종속성이 변경된 경우에만 업데이트됩니다.
