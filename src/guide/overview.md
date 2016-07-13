---
title: 요약
type: guide
order: 2
---
Vue.js (발음 / vjuː /, **view**와 같은 발음)는 인터랙티브 웹 인터페이스를 구축하기위한 라이브러리입니다. Vue.js의 목표는 가능한 한 간단한 API에서 반응형 데이터 바인딩 및 구성 가능한 화면 구성 요소를 제공하는 것입니다.

Vue.js 자체는 모든 것을 갖춘 프레임워크는 아닙니다. Vue.js는 View 레이어에만 초점을 맞추고 있습니다. 따라서 Vue.js만 선택하거나 Vue.js를 다른 라이브러리와 기존 프로젝트에 통합하는 것이 매우 간단합니다. 한편, Vue.js을 적절한 도구와 지원 라이브러리가 함께 사용하면 Vue.js를 완전히 작동하는 단일 페이지 응용 프로그램을 만들 수 있습니다.

당신은 경험이 풍부한 프론트엔드 개발자이고 Vue.js를 다른 라이브러리 / 프레임워크와 비교하려면 [다른 프레임 워크와의 비교](comparison.html)를  읽어보세요. Vue.js에서 대규모 응용 프로그램을 처리하는 방법에 관심이 있다면, [대규모 애플리케이션의 구축](application.html)을 읽어보세요.

## 반응형 데이터 바인딩

Vue.js의 핵심은 매우 간단하게 데이터와 DOM을 동기화하는 반응형 데이터 바인딩 시스템입니다. 수동으로 DOM을 조작하기 위해 jQuery를 사용하면 피할수 없이 반복이 많은 실수를 유발하는 코드를 작성하는 경우가 많습니다. Vue.js는 **데이터 주도 View**의 개념을 채용하고 있습니다. 즉, 기본이되는 데이터에 DOM을 "bind"하기 위해 일반적인 HTML 템플릿에서 특별한 구문을 사용하는 것입니다. 일단 바인딩이 되면 DOM과 데이터가 계속 동기화됩니다. 데이터를 변경할 때마다 DOM은 그에 따라 업데이트됩니다. 그 결과, 대부분의 응용 프로그램 논리는 DOM의 업데이트를 어설프지않은 직접적인 데이터 조작이 가능합니다. 따라서 코드는 더 쓰기 쉽고 보다 논리적으로 더 유지 보수하기 쉬워집니다.

![MVVM](/images/mvvm.png)

가장 간단한 예 입니다:

``` html
<!-- 이것이 View 입니다 -->
<div id="example-1">
  Hello {{ name }}!
</div>
```

``` js
// 이것이 우리의 Model 입니다
var exampleData = {
  name: 'Vue.js'
}

// Vue 인스턴스를 만듭니다.
// View와 Model을 연결하는 ViewModel 입니다.
var exampleVM = new Vue({
  el: '#example-1',
  data: exampleData
})
```

결과:
{% raw %}
<div id="example-1" class="demo">Hello {{ name }}!</div>
<script>
var exampleData = {
  name: 'Vue.js'
}
var exampleVM = new Vue({
  el: '#example-1',
  data: exampleData
})
</script>
{% endraw %}

언뜻 보면 그냥 템플릿을 렌더링하고있는 것처럼 보이지만, Vue.js 내부에서 많은 작업을하고 있습니다. 데이터와 DOM은 연결되고, 모두가 **반응적으로**되어 있습니다. 그러나 어떻게 우리는 그것을 알 수 있을까요? 브라우저 개발자 콘솔을 열고 `exampleData.name`을 입력하면 됩니다. 위의 업데이트에 따라 렌더링되는 샘플을 확인할 수 있습니다.

여기에서 모든 DOM 조작 코드를 작성할 필요가 없었다는 것을 알 수 있습니다. 바인딩에 의해 확장 된 HTML 템플릿은 기본 데이터 상태의 선언 형식의 매핑이며, 이것은 단순한 JavaScript 객체입니다. 우리가 만든 View는 완전히 데이터 주도형입니다.

두번째 예제를 살펴 보겠습니다:

``` html
<div id="example-2">
  <p v-if="greeting">Hello!</p>
</div>
```

``` js
var exampleVM2 = new Vue({
  el: '#example-2',
  data: {
    greeting: true
  }
})
```

{% raw %}
<div id="example-2" class="demo">
  <span v-if="greeting">Hello!</span>
</div>
<script>
var exampleVM2 = new Vue({
  el: '#example-2',
  data: {
    greeting: true
  }
})
</script>
{% endraw %}

여기에는 뭔가 새로운 것이 있습니다. `v-if` 속성은 **지시어**이라고합니다. **지시어**는 Vue.js에 의해 제공되는 특별한 특성을 나타 내기 위해 `v-`가 앞에 붙고 당신이 추측 한 것처럼 렌더링 된 DOM에 특정 반응을 하는 행동을 합니다. 이제 콘솔에서 exampleVM2.greeting을 false로 설정합니다. 그러면 "Hello!"메시지가 표시되지 않는 것을 확인할 수 있습니다.

이 두 번째 예는 우리가 DOM 텍스트 데이터에 바인딩 할뿐만 아니라 DOM **구조**에 데이터를 바인딩 할 수있는 것을 보여줍니다. 또한 Vue.js 요소가 Vue 의해 삽입 / 삭제 될 때 자동으로 전환 효과를 적용 할 수있는 강력한 전환 효과 시스템도 제공합니다.

Vue.js에는 상당수의 지시어가 자체적으로 특별한 기능을 가지고 있습니다. 예를 들면, `v-for`는 배열의 항목을 표시하는 지시어이며, `v-bind` 지시어은 HTML 속성을 바인딩하는 지시어입니다. 완전한 데이터 바인딩 구문에 대해 더 자세히 나중에 설명합니다.

## 컴포넌트 시스템

컴포넌트 시스템은 Vue.js 있어 또 하나의 중요한 개념입니다. 왜냐하면 컴포넌트 시스템은 작고 독립적으로 재사용 가능한 컴포넌트로 구성된 대규모 애플리케이션의 구축을 가능하게하는 추상 개념이기 때문입니다. 컴포넌트 시스템을 고려할 때 응용 프로그램 인터페이스의 거의 모든 유형을 구성 요소 트리로 추상화 할 수 있습니다:

![Component Tree](/images/components.png)

실제로 Vue.js에서 구축 된 전형적인 대규모 응용 프로그램은 바로 위 그림 오른쪽에있는 컴포넌트 트리 모양이 될 것입니다. 이 가이드의 뒷부분에서는 구성 요소에 대해 더 많은 이야기를하지만, 여기에서는 응용 프로그램 템플릿이 구성 요소에서 어떻게 보이는지, (상상 속의)의 예입니다 :

``` html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

Vue.js 컴포넌트가 [Web Components Spec](http://www.w3.org/wiki/WebComponents/)의 일부 사용자 지정 요소 **Custom Element**와 매우 비슷한 것으로 보일 수 있습니다. 실제로 Vue.js 컴포넌트 구문은 사양에 따라 느슨하게 모델링되어 있습니다. 예를 들어, Vue 구성 요소는 [Slot API](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md)와 `is`라는 특별한 속성을 구현하고 있습니다. 그러나 몇 가지 중요한 차이가 있습니다:

1. Web Components의 사양은 아직도 진행 중이며, 모든 브라우저에 기본 구현되는 것은 아닙니다. 한편, Vue.js 컴포넌트는 어떤 폴리필(polyfill)도 필요없이 지원되는 모든 브라우저 (IE9과 그 이상)에서 같은 동작을합니다. 필요에 따라 Vue.js 컴포넌트는 기본 사용자 지정 요소를 감쌀 수 있습니다.

2. Vue.js 구성 요소는 컴포넌트 간 데이터 전달, 사용자 정의 이벤트 통신, 그리고 동적인 컴포넌트 전환과 함께 작동하는 효과 등의 일반 사용자 정의 요소에서 사용할 수없는 중요한 기능을 제공합니다.

컴포넌트 시스템은 Vue.js에서 대규모 애플리케이션을 구축 하기 위한 기반이됩니다. 또한 Vue.js의 생태계는 고급 도구와 더 "프레임워크"같은 시스템을 만들기위한 많은 보조적인 라이브러리도 제공합니다.
