---
title: 리스트 렌더링
type: guide
order: 8
---

## v-for

우리는 배열에 들어있는 항목의 목록을 렌더링하기 위해 `v-for` 문을 사용 할 수 있습니다. `v-for` 지시어는 `item in items` 형태의 문법을 사용합니다. `items는` 소스 데이터의 배열이고 `item`은 반복되는 배열 요소의 **별칭**입니다:

**예제:**

``` html
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```

``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

**결과:**

{% raw %}
<ul id="example-1" class="demo">
  <li v-for="item in items">
    {{item.message}}
  </li>
</ul>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(null, '#example-1')
    }
  }
})
</script>
{% endraw %}

`v-for` 블록은 부모 범위에 대한 속성에 완전히 접근할 수 있습니다. 아마 이미 예상하듯 현재 항목에 대한 배열의 인덱스인 특별한 변수 `$index`를 가지고 있습니다:

``` html
<ul id="example-2">
  <li v-for="item in items">
    {{ parentMessage }} - {{ $index }} - {{ item.message }}
  </li>
</ul>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

**결과:**

{% raw%}
<ul id="example-2" class="demo">
  <li v-for="item in items">
    {{ parentMessage }} - {{ $index }} - {{ item.message }}
  </li>
</ul>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(null, '#example-2')
    }
  }
})
</script>
{% endraw %}

또는 인덱스 (또는 객체로 사용되는 경우에는 키)의 별칭을 지정할 수 있습니다 :

``` html
<div v-for="(index, item) in items">
  {{ index }} {{ item.message }}
</div>
```

1.0.17 이후의 버전에서 `in`이외에도 `of`또한 사용할 수 있습니다. 자바스크립트의 문법과 거의 같은 반복문처럼 사용됩니다.

``` html
<div v-for="item of items"></div>
```

## v-for 템플릿

템플릿 `v-if`뿐만 아니라 여러 요소의 블록을 렌더링하기 위해 `v-for`에 `<template>` 태그도 사용할 수 있습니다. 예를 들면 :

``` html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider"></li>
  </template>
</ul>
```

## 배열 변경 감지

### 조작 메소드

Vue.js는 View의 업데이트를 발생시키기 위해 된 배열 변경용 메소드를 감쌌(wrap)했습니다. 래핑 된 메소드는 다음과 같습니다:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

콘솔을 열고 전에 이전 items 배열의 예제에서 조작 메소드를 호출해보세요. 예를 들어 `example1.items.push ({message : 'Baz'})`를 입력하면 됩니다.

### 배열 변경

변경 방법은 이름이 보여주는 것처럼, 사용하는 원래의 배열을 변경합니다. 변경하지 않는 메소드도 있습니다. 예를 들어, `filter()`, `concat()` 및 `slice()`는  원래의 배열을 변경하지 않지만,** 항상 새로운 배열을 돌려줍니다.** 변경하지 않는 방법을 사용할 때 새로운 배열이 이전 배열을 바꿉니다 :

``` js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

이 경우 Vue.js가 기존의 DOM을 버리고 전체 목록을 다시 렌더링을 할 것이라 생각할지도 모릅니다. 다행히도 그것은 그렇지 않습니다. Vue.js는 DOM 요소의 재사용을 극대화하기 위해 일부 영리한 추론을 구현하고 있기때문에 중복된 개체를 포함하는 배열을 다른 배열로 효율적으로 바꿀 수 있습니다.

### track-by

어떤 경우에 완전히 새로운 객체를 가지는 배열 바꿀 필요가 있을지도 모릅니다 (예를 들어, API 호출에서 생성 된 객체 등). 기본적으로 `v-for`는 기존의 범위와 데이터 객체의 식별 정보를 추적하여 DOM 요소의 재사용 성을 결정하기 때문에 전체 목록이 다시 렌더링 될 수 있습니다. 그러나 만약 각 데이터 객체가 고유 ID 속성을 가지고 있다면 가능한 한 많은 인스턴스를 재사용하기위해 `track-by`를 통해 Vue.js에 힌트를 줄 수 있습니다.

예를 들어, 데이터가 아래와 같다면:

``` js
{
  items: [
    { _uid: '88f869d', ... },
    { _uid: '7496c10', ... }
  ]
}
```

힌트를 줄 수 있습니다.

``` html
<div v-for="item in items" track-by="_uid">
  <!-- content -->
</div>
```

나중에 `items` 배열을 바꾸고 그리고 Vue.js는 `_uid : '88f869d'`을 가진 새로운 개체를 검색 할 때 같은 `_uid`과 관련된 기존 범위와 DOM 요소를 재사용 할 수 있습니다.

### track-by $index

`track-by`에서 사용할 유니크 키를 가지고 있지 않은 경우, `track-by = "$index"`를 사용할 수 있습니다. 이것은 v-for를 in-place 업데이트 모드로 강제합니다. 조각은 더 이상 이동하지 않고, 그들은 단순히 대응하는 인덱스에 새로운 값으로 가져옵니다. 이 모드는 원본이 되는 배열의 중복 값을 처리 할 수 있습니다.

이를 이용해 배열의 변경을 매우 효율적으로 할 수 있지만 단점도 있습니다. 왜냐하면 DOM 노드는 더 이상 순서의 변경을 반영하지 않기 때문에, DOM 입력 값과 구성 요소의 개인 상태와 같은 일시적인 상태는 동기화 할 수 없습니다. 따라서 `v-for` 블록이 input 요소 또는 하위 구성 요소에 포함되어있는 경우 `track-by = "$index"`를 사용할 때주의하십시오.

### 주의 사항

JavaScript의 한계로 인해 Vue.js은 배열에서 다음의 변경을 **감지 할 수 없습니다**:

1. 인덱스를 이용해서 항목을 직접 설정할 때. 예 : `vm.items [0] = {}`
2. 배열의 길이를 변경하는 때. 예 : `vm.items.length = 0`

1번의 기능이 필요한 경우, Vue.js는 `$set()` 메소드를 이용해서 배열을 조작합니다.

``` js
//  `example1.items[0] = ...`와 같지만 View가 업데이트 됩니다.
example1.items.$set(0, { childMsg: 'Changed!'})
```

2번의 경우 `items`를 빈 배열로 바꾸면 됩니다.

`$set()`이외에 Vue.js는 `$remove()`를 이용해 아이템을 제거할 수 있도록 합니다. 이 메소드는 해당 아이템을 검색하고 내부에서 `splice()`를 호출하여 대상의 배열에서 항목을 삭제 합니다. 아래의 방법 대신에:

``` js
var index = this.items.indexOf(item)
if (index !== -1) {
  this.items.splice(index, 1)
}
```

아래처럼 하면 됩니다:

``` js
this.items.$remove(item)
```

#### `Object.freeze()` 사용하기

`Object.freeze()`에 의해 동결된 객체 배열을 반복 할 때 명시적으로 `track-by` 키를 사용해야합니다. Vue.js는 자동으로 객체를 추적할 수 없을 때 경고가 표시합니다.


## 객체와 v-for

객체의 속성에 `v-for`를 사용하여 순회할 수 있습니다. `$index` 이외에 각각의 범위는 `$key`로 접근할 수 있습니다.

``` html
<ul id="repeat-object" class="demo">
  <li v-for="value in object">
    {{ $key }} : {{ value }}
  </li>
</ul>
```

``` js
new Vue({
  el: '#repeat-object',
  data: {
    object: {
      FirstName: 'John',
      LastName: 'Doe',
      Age: 30
    }
  }
})
```

**결과:**

{% raw %}
<ul id="repeat-object" class="demo">
  <li v-for="value in object">
    {{ $key }} : {{ value }}
  </li>
</ul>
<script>
new Vue({
  el: '#repeat-object',
  data: {
    object: {
      FirstName: 'John',
      LastName: 'Doe',
      Age: 30
    }
  }
})
</script>
{% endraw %}

키의 별칭도 사용할 수 있습니다:

``` html
<div v-for="(key, val) in object">
  {{ key }} {{ val }}
</div>
```

<p class="tip">객체를 순회할 때 순서는 `Object.keys()`내부의 키를 기반으로하며 모든 JavaScript엔진의 구현에서 일관성이 보장되지 않습니다.</p>

## 범위 v-for

`v-for`는 정수형 숫자를 이용할 수 있습니다. 이 경우 템플릿을 여러번 반복시킬 수 있습니다.

``` html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

**결과:**

{% raw %}
<div id="range" class="demo">
  <span v-for="n in 10">{{ n }} </span>
</div>
<script>
new Vue({ el: '#range' })
</script>
{% endraw %}

## 정렬/필터된 결과 표시하기

가끔은 필터링 되거나 정렬된 상태의 배열을 출력해야 하는 경우가 있습니다. 이 경우 원래의 데이터는 수정하지 말아야 할 수도 있습니다. 여기에 두가지 방법이 있습니다.

1. 계산된 속성을 사용해서 배열을 필터링 혹은 정렬하세요
2. 내장된 `filterBy`와 `orderBy` 필터를 사용하세요

 계산된 속성은 완전한 JavaScript이기 때문에 더욱 상세한 제어와 유연성을 부여합니다. 그러나 필터는 일반적인 사용시 보다 편리하게 사용 할 수 있습니다. 배열 필터의 자세한 사용 방법은 해당 [문서](/api/#filterBy)를 확인하십시오.
