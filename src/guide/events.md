---
title: 메소드와 이벤트 핸들링
type: guide
order: 9
---

## 메소드 핸들러

`v-on` 지시어를 이용해서 DOM 이벤트를 감지할 수 있습니다.

``` html
<div id="example">
  <button v-on:click="greet">Greet</button>
</div>
```

We are binding a click event listener to a method named `greet`. Here's how to define that method in our Vue instance:
우리는 greet라는 이름의 메소드에 click 이벤트 리스너를 바인딩합니다. Vue 인스턴스안에 메소드를 정의하는 방법은 다음과 같습니다:

``` js
var vm = new Vue({
  el: '#example',
  data: {
    name: 'Vue.js'
  },
  // `methods` 객체 아래에 메소드를 정의하세요.
  methods: {
    greet: function (event) {
      // `this`는 Vue 인스턴스를 가리킵니다.
      alert('Hello ' + this.name + '!')
      // `event`는 네이티브 브라우저 이벤트 입니다.
      alert(event.target.tagName)
    }
  }
})


// 자바스크립트 메소드를 호출할 수 있습니다.
vm.greet() // -> 'Hello Vue.js!'
```

스스로 테스트 해보세요:

{% raw %}
<div id="example" class="demo">
  <button v-on:click="greet">Greet</button>
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    name: 'Vue.js'
  },
  // `methods` 객체 아래에 메소드를 정의하세요.
  methods: {
    greet: function (event) {
      // `this`는 Vue 인스턴스를 가리킵니다.
      alert('Hello ' + this.name + '!')
      // `event`는 네이티브 브라우저 이벤트 입니다.
      alert(event.target.tagName)
    }
  }
})
</script>
{% endraw %}

## 인라인 구문 핸들러

메소드 이름에 직접 바인딩하는 대신, 우리는 인라인 JavaScript 구문도 사용할 수 있습니다:

``` html
<div id="example-2">
  <button v-on:click="say('hi')">Say Hi</button>
  <button v-on:click="say('what')">Say What</button>
</div>
```
``` js
new Vue({
  el: '#example-2',
  methods: {
    say: function (msg) {
      alert(msg)
    }
  }
})
```

결과:
{% raw %}
<div id="example-2" class="demo">
  <button v-on:click="say('hi')">Say Hi</button>
  <button v-on:click="say('what')">Say What</button>
</div>
<script>
new Vue({
  el: '#example-2',
  methods: {
    say: function (msg) {
      alert(msg)
    }
  }
})
</script>
{% endraw %}

인라인 표현식의 제한과 마찬가지로 이벤트 핸들러는 **한 문장으로 제한**됩니다.

때때로, 우리는 인라인 구문 핸들러에서 원래의 DOM 이벤트에 액세스해야합니다. `$event` 변수를 사용하여 메소드에 그것을 전달할 수 있습니다:

``` html
<button v-on:click="say('hello!', $event)">Submit</button>
```

``` js
// ...
methods: {
  say: function (msg, event) {
    // 이제 네이티브 이벤트를 사용할 수 있습니다.
    event.preventDefault()
  }
}
```

## 이벤트 변경자

이벤트 핸들러 내부에서 `event.preventDefault()` 또는 `event.stopPropagation()`를 호출하는 것은 보통 매우 일반적입니다. 우리는 메소드 내부에서 쉽게 작업을 수행 할 수 있지만 메소드가 순수하게 데이터만 다루는 것이 DOM 이벤트를 다루는 것을 포함하는 것보다 좋습니다.

To address this problem, Vue.js provides two **event modifiers** for `v-on`: `.prevent` and `.stop`. Recall that modifiers are directive postfixes denoted by a dot:

이 문제를 해결하기 위해, Vue.js는`v-on`에 두개의 **이벤트 변경자**를 제공합니다: `.prevent`와`.stop` 한정 지시자는 지시어에 "."을 붙여 사용합니다:

``` html
<!-- click event의 확산을 막습니다. -->
<a v-on:click.stop="doThis"></a>

<!-- submit 이벤트는 더이상 새로고침을 하지 않습니다. -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 변경자는 체이닝할 수 있습니다. -->
<a v-on:click.stop.prevent="doThat">

<!-- 변경자만 사용할 수 있습니다. -->
<form v-on:submit.prevent></form>
```

1.0.16에서는 두개의 변경자가 추가되었습니다:

``` html
<!-- 이벤트 목록에 추가 할 때 캡처 모드를 사용합니다 -->
<div v-on:click.capture="doThis">...</div>

<!-- event.target이 요소 자체 경우에만 핸들러를 트리거합니다 -->
<!-- 즉 자식 요소를 다룰 수는 없습니다 -->
<div v-on:click.self="doThat">...</div>
```

## 키보드 변경자

키보드 이벤트를 수신 할 때, 우리는 종종 일반 키 코드를 확인해야합니다. Vue.js는 `v-on`을 이용하여 키 이벤트에 반응하기 위해 키 한정자 (key modifier)를 추가 할 수 있습니다:

``` html
<!-- 키 코드가 13일때만 vm.submit()이 실행됩니다 -->
<input v-on:keyup.13="submit">
```

모든 키 코드를 기억하는 것은 힘들기 때문에 Vue.js은 가장 일반적으로 사용되는 키의 별칭을 제공합니다:

``` html
<!-- 위의 예제와 같습니다. -->
<input v-on:keyup.enter="submit">

<!-- 짧게 줄여 쓸 수도 있습니다. -->
<input @keyup.enter="submit">
```

별칭이 있는 키 한정지시자의 전체 리스트 입니다:

- enter
- tab
- delete
- esc
- space
- up
- down
- left
- right

**1.0.8+:** 한 글자에 대한 별칭을 지원합니다.

**1.0.17+:** 또한 사용자 임의의 키 한정자에 대한 별칭을 정의할 수 있습니다:

``` js
// enable @keyup.f1
Vue.directive('on').keyCodes.f1 = 112
```

## 왜 리스너를 HTML에 작성합니까?

이러한 이벤트 감시 방법은 "관심의 분리"라는 좋은 규칙을 어기는 것이 아닌가하고 걱정할 수 있습니다. 안심하십시오. 모든 Vue.js의 핸들러 함수 및 수식은 현재 View만을 취급하며 ViewModel에만 바인딩되도록 제한합니다. 이 때문에 관리가 어려워 질 수는 없습니다. 실제로 `v-on`을 이용하면 여러 가지 장점이 있습니다:

1. HTML 템플릿을 바라 보는 것만으로, 당신의 JS 코드의 핸들러 함수를 쉽게 찾을 수 있습니다.

2. JS에서 이벤트 리스너를 수동으로 연결할 필요가 없기 때문에 ViewModel 코드는 DOM이 없는 조작만 남게 됩니다. 이것은 테스트를 더 쉽게합니다.

3. ViewModel이 파기 될 때 모든 이벤트 리스너는 자동으로 삭제됩니다. 그들을 스스로 정리하는 것을 걱정할 필요도 없습니다.
