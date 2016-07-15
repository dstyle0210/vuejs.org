---
title: 사용자 정의 필터
type: guide
order: 15
---

## 기초

사용자 정의 지시어와 마찬가지로 글로벌 `Vue.filter()`를 사용하여 사용자 정의 필터를 등록 할 수 있습니다. 아규먼트는 `filterID`와 **filter function**을 전달합니다. 이 필터 함수는 아규먼트로 값을 받아 변환된 값을 반환합니다 :

``` js
Vue.filter('reverse', function (value) {
  return value.split('').reverse().join('')
})
```

``` html
<!-- 'abc' => 'cba' -->
<span v-text="message | reverse"></span>
```

필터 함수는 인라인 아규먼트를 사용할 수 있습니다:

``` js
Vue.filter('wrap', function (value, begin, end) {
  return begin + value + end
})
```

``` html
<!-- 'hello' => 'before hello after' -->
<span v-text="message | wrap 'before' 'after'"></span>
```

## 양방향 필터

지금까지 필터는 모델에서 전달 된 값을 View에 표시되기 전에 변환하는 데 사용하고 있었습니다. 그러나 input 요소 등의 View에서 모델에 쓰기가 되기 전에 값을 변환하는 필터의 정의도 가능합니다.

``` js
Vue.filter('currencyDisplay', {
  // model -> view
  // input 요소가 업데이트 될 때 값을 변환합니다.
  read: function(val) {
    return '$'+val.toFixed(2)
  },
  // view -> model
  // 데이터가 업데이트 될 때 값을 변환합니다.
  write: function(val, oldVal) {
    var number = +val.replace(/[^\d.]/g, '')
    return isNaN(number) ? 0 : parseFloat(number.toFixed(2))
  }
})
```

Demo:

{% raw %}
<div id="two-way-filter-demo" class="demo">
  <input type="text" v-model="money | currencyDisplay">
  <p>Model value: {{money}}</p>
</div>
<script>
new Vue({
  el: '#two-way-filter-demo',
  data: {
    money: 123.45
  },
  filters: {
    currencyDisplay: {
      read: function(val) {
        return '$'+val.toFixed(2)
      },
      write: function(val, oldVal) {
        var number = +val.replace(/[^\d.]/g, '')
        return isNaN(number) ? 0 : parseFloat(number.toFixed(2))
      }
    }
  }
})
</script>
{% endraw %}

## 동적 아규먼트

만약 필터 아규먼트가 따옴표 ('')로 닫혀 있지 않은 경우, 현재 vm 데이터 컨텍스트에서 동적인 데이터로 다루어 집니다. 게다가 필터 함수에서 항상 현재 vm은 `this` 컨텍스트로 사용합니다. 예를 들면 :

``` html
<input v-model="userInput">
<span>{{msg | concat userInput}}</span>
```

``` js
Vue.filter('concat', function (value, input) {
  // 여기서 `input` === `this.userInput`
  return value + input
})
```

위의 간단한 예는 표현식을 그대로 사용 한 것과 같은 결과를 얻을 수 있습니다. 그러나 여러 문장이 필요한 복잡한 처리에서는 계산된 속성(Computed Property) 또는 사용자 정의 필터가 필요합니다.

내장된 `filterBy`과 `orderBy` 필터는 함께 전달된 배열에 중요한 변경을 하며, 소유자의 Vue 인스턴스의 현재 상태에 의존 합니다.
