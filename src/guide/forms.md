---
title: 폼 입력 바인딩
type: guide
order: 10
---

## 기본 사용 방법

form의 input 요소에서 양방향(two-way) 데이터 바인딩을 만드려면 `v-model` 지시어를 사용할 수 있습니다. 자동으로 입력 된 데이터의 유형에 따라 요소를 업데이트합니다. 약간의 마법같기는 하지만, `v-model`은 본질적으로 사용자 입력 이벤트에서 데이터를 업데이트 하기 위한 신택스슈가(syntax sugar)이지만 일부 케이스에서 특별한 주의가 필요합니다.

### Text

``` html
<span>Message is: {{ message }}</span>
<br>
<input type="text" v-model="message" placeholder="edit me">
```

{% raw %}
<div id="example-1" class="demo">
  <span>Message is: {{ message }}</span><br>
  <input type="text" v-model="message" placeholder="edit me">
</div>
<script>
new Vue({
  el: '#example-1',
  data: {
    message: ''
  }
})
</script>
{% endraw %}

### Multiline text

``` html
<span>Multiline message is:</span>
<p>{{ message }}</p>
<br>
<textarea v-model="message"></textarea>
```

{% raw %}
<div id="example-textarea" class="demo">
  <span>Message is:</span>
  <p style="white-space: pre">{{ message }}</p><br>
  <textarea v-model="message" placeholder="add multiple lines"></textarea>
</div>
<script>
new Vue({
  el: '#example-textarea',
  data: {
    message: ''
  }
})
</script>
{% endraw %}

### Checkbox

단일 체크박스는 boolean 값입니다:

``` html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```
{% raw %}
<div id="example-2" class="demo">
  <input type="checkbox" id="checkbox" v-model="checked">
  <label for="checkbox">{{ checked }}</label>
</div>
<script>
new Vue({
  el: '#example-2',
  data: {
    checked: false
  }
})
</script>
{% endraw %}

여러개의 체크박스는 배열과 같이 바인딩 됩니다.

``` html
<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>
<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>
<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>
<br>
<span>Checked names: {{ checkedNames | json }}</span>
```

``` js
new Vue({
  el: '...',
  data: {
    checkedNames: []
  }
})
```

{% raw %}
<div id="example-3" class="demo">
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames | json }}</span>
</div>
<script>
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
</script>
{% endraw %}

### Radio


``` html
<input type="radio" id="one" value="One" v-model="picked">
<label for="one">One</label>
<br>
<input type="radio" id="two" value="Two" v-model="picked">
<label for="two">Two</label>
<br>
<span>Picked: {{ picked }}</span>
```
{% raw %}
<div id="example-4" class="demo">
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">One</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Two</label>
  <br>
  <span>Picked: {{ picked }}</span>
</div>
<script>
new Vue({
  el: '#example-4',
  data: {
    picked: ''
  }
})
</script>
{% endraw %}

### Select

한개 선택:

``` html
<select v-model="selected">
  <option selected>A</option>
  <option>B</option>
  <option>C</option>
</select>
<span>Selected: {{ selected }}</span>
```
{% raw %}
<div id="example-5" class="demo">
  <select v-model="selected">
    <option selected>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-5',
  data: {
    selected: null
  }
})
</script>
{% endraw %}

여러개 중 선택 (배열에 바인딩 됩니다):

``` html
<select v-model="selected" multiple>
  <option selected>A</option>
  <option>B</option>
  <option>C</option>
</select>
<br>
<span>Selected: {{ selected | json }}</span>
```
{% raw %}
<div id="example-6" class="demo">
  <select v-model="selected" multiple style="width: 50px">
    <option selected>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <br>
  <span>Selected: {{ selected | json }}</span>
</div>
<script>
new Vue({
  el: '#example-6',
  data: {
    selected: []
  }
})
</script>
{% endraw %}

동적인 옵션들은 `v-for`를 이용합니다:

``` html
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selected: {{ selected }}</span>
```
``` js
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
```
{% raw %}
<div id="example-7" class="demo">
  <select v-model="selected">
    <option v-for="option in options" v-bind:value="option.value">
      {{ option.text }}
    </option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-7',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
</script>
{% endraw %}

## 값 바인딩

radio, checkbox, 그리고 select 옵션의 `v-model` 바인딩 값은 일반적으로 정적 문자열(또는 checkbox에는 boolean)를 지정합니다:

``` html
<!-- 체크되면 `picked`가 a의 값이 됩니다 -->
<input type="radio" v-model="picked" value="a">

<!-- `toggle`은 true 또는 false 값을 가집니다 -->
<input type="checkbox" v-model="toggle">

<!-- "abc"를 선택하는 경우 `selected`가 선택 값이 됩니다. -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

그러나 때때로, 우리는 Vue 인스턴스에서 동적 속성에 값을 바인딩하고 싶을지도 모릅니다. 이를 위해 `v-bind`를 사용 할 수 있습니다. 게다가 `v-bind`를 사용하면 문자열이 아닌 값을 input 값을 바인딩합니다.

### Checkbox

``` html
<input
  type="checkbox"
  v-model="toggle"
  v-bind:true-value="a"
  v-bind:false-value="b">
```

``` js
// 체크되면:
vm.toggle === vm.a
// 체크가 풀리면:
vm.toggle === vm.b
```

### Radio

``` html
<input type="radio" v-model="pick" v-bind:value="a">
```

``` js
// 체크되면:
vm.pick === vm.a
```

### Select Options

``` html
<select v-model="selected">
  <!-- 인라인 객체 리터럴 -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
```

``` js
// 선택된 경우:
typeof vm.selected // -> 'object'
vm.selected.number // -> 123
```

## 매개변수 특성

### lazy

기본적으로 `v-model`은 각 `input` 이벤트 후 데이터 입력을 동기화합니다. `change` 이벤트 후 동기화하기 위해 `lazy` 속성을 추가합니다:

``` html
<!-- "input" 대신에 "change" 이벤트 후 변경됩니다. -->
<input v-model="msg" lazy>
```

### number

사용자의 입력을 항상 숫자로 받기 위해 `number` 속성을 `v-model`이 관리하는 input에 추가하세요.

``` html
<input v-model="age" number>
```

### debounce

`debounce` 매개 변수는 입력 값이 Model에 동기화되기 전에 각 키 입력 후 최소 지연 설정을 허용합니다. 예를 들어, 입력에 대한 자동 완성을 위한 Ajax 요청을 만드는 등의 부하가 큰 작업을 수행 할 때 유용합니다.

``` html
<input v-model="msg" debounce="500">
```
 {% raw %}
<div id="debounce-demo" class="demo">
  {{ msg }}<br>
  <input v-model="msg" debounce="500">
</div>
<script>
new Vue({
  el:'#debounce-demo',
  data: { msg: 'edit me' }
})
</script>
{% endraw %}

`debounce` 매개 변수는 사용자 입력 이벤트를 `디바운스`하지 않는 점에 유의하십시오 : 이것은 기초가되는 데이터에 "쓰기"작업을 하는 경우에 사용합니다. 따라서 `debounce`를 사용하면 데이터 변경에 반응하기 위해 `vm.$watch()`를 사용해야합니다. 진짜 DOM 이벤트를 디바운스하기 위해서는 [debounce filter](/api/#debounce)를 사용합니다.
