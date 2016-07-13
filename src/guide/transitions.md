---
title: 트랜지션
type: guide
order: 11
---

Vue.js 트랜지션(Transition)시스템을 사용하면 DOM에서 요소를 검색하고 삭제하는 등의 트랜지션 효과를 자동으로 적용 할 수 있습니다. Vue.js은 자동으로 적절한 타이밍에 CSS 전환 또는 애니메이션을 트리거하기 위해 CSS 클래스를 추가하거나 삭제하고, 전환 동안 사용자 DOM 작업을하기 위해 JavaScript 훅 함수를 제공 할 수 있습니다.

전환 효과를 적용하기 위해 해당 요소에 특별한 `transition` 속성을 사용해야합니다:

``` html
<div v-if="show" transition="my-transition"></div>
```

`transition` 속성은 다음과 함께 사용할 수 있습니다:

- `v-if`
- `v-show`
- `v-for` (삽입 및 삭제에 대해서만 트리거 애니메이션 순서 변경은 [use vue-animated-list plugin](https://github.com/vuejs/vue-animated-list))
- 동적 구성 요소 ([다음 섹션](components.html#Dynamic-Components)에서 소개)
- 구성 요소의 루트 노드, 그리고 Vue 인스턴스 DOM 메소드 통한 트리거 (예 `vm.$appendTo(el)`)

전환을 가지는 요소가 삽입 또는 삭제 될 때, Vue는 다음과 같습니다:

1. "my-transition"의 ID를 사용하여 `Vue.transition(id, hooks)` 또는 `transitions` 옵션을 통해 등록된 JavaScript의 트랜지션 훅 객체를 찾습니다. 훅이 발견되면 여러 단계에서 적절한 훅를 부릅니다.

2. 자동으로 해당 요소에 CSS 전환 또는 CSS 애니메이션이 적용되어 있는지 확인하고 적절한 타이밍에 CSS 클래스를 추가/삭제합니다.

3. JavaScript 훅이 없고 CSS 트랜지션/애니메이션 아무것도 없는 경우, DOM 조작(삽입/삭제)는 다음 프레임에서 즉시 실행됩니다.

## CSS 트랜지션

### 예

기본적인 CSS 전환은 다음과 같이됩니다.

``` html
<div v-if="show" transition="expand">hello</div>
```

또한 `.expand-transition` 클래스 `.expand-enter` 클래스 그리고 `.expand-leave` 클래스의 CSS 규칙을 정의해야합니다.

``` css
/* 항상 표시됨 */
.expand-transition {
  transition: all .3s ease;
  height: 30px;
  padding: 10px;
  background-color: #eee;
  overflow: hidden;
}

/ * .expand-enter는 entering 대한 시작 상태를 정의 * /
/ * .expand-leave는 leaving 대한 종료 상태를 정의 * /
.expand-enter, .expand-leave {
  height: 0;
  padding: 0 10px;
  opacity: 0;
}
```

동적 바인딩을 사용하여 같은 요소에서 다른 트랜지션을 사용 할 수 있습니다:

```html
<div v-if="show" :transition="transitionName">hello</div>
```

```js
new Vue({
  el: '...',
  data: {
    show: false,
    transitionName: 'fade'
  }
})
```

또한, JavaScript 훅을 제공 할 수 있습니다.

``` js
Vue.transition('expand', {

  beforeEnter: function (el) {
    el.textContent = 'beforeEnter'
  },
  enter: function (el) {
    el.textContent = 'enter'
  },
  afterEnter: function (el) {
    el.textContent = 'afterEnter'
  },
  enterCancelled: function (el) {
    // 취소된 경우 처리
  },

  beforeLeave: function (el) {
    el.textContent = 'beforeLeave'
  },
  leave: function (el) {
    el.textContent = 'leave'
  },
  afterLeave: function (el) {
    el.textContent = 'afterLeave'
  },
  leaveCancelled: function (el) {
    // 취소된 경우 처리
  }
})
```

{% raw %}
<div id="demo">
  <div v-if="show" transition="expand">hello</div>
  <button @click="show = !show">Toggle</button>
</div>

<style>
.expand-transition {
  transition: all .3s ease;
  height: 30px;
  padding: 10px;
  background-color: #eee;
  overflow: hidden;
}
.expand-enter, .expand-leave {
  height: 0;
  padding: 0 10px;
  opacity: 0;
}
</style>

<script>
new Vue({
  el: '#demo',
  data: {
    show: true,
    transitionState: 'Idle'
  },
  transitions: {
    expand: {
      beforeEnter: function (el) {
        el.textContent = 'beforeEnter'
      },
      enter: function (el) {
        el.textContent = 'enter'
      },
      afterEnter: function (el) {
        el.textContent = 'afterEnter'
      },
      beforeLeave: function (el) {
        el.textContent = 'beforeLeave'
      },
      leave: function (el) {
        el.textContent = 'leave'
      },
      afterLeave: function (el) {
        el.textContent = 'afterLeave'
      }
    }
  }
})
</script>
{% endraw %}

### CSS 클래스 트랜지션

추가 및 토글 클래스 이름의 접두사는 `transition` 속성의 값에 근거합니다. `transition = "fade"`의 경우에는 3 개의 CSS 클래스가 관여하고 있습니다 :

1. `.fade-transition` 클래스는 항상 주어집니다.

2. `.fade-enter`는 entering transition (트랜지션에 들어있는)의 시작 상태를 정의합니다. 단일 프레임에 적용한 후 즉시 삭제됩니다.

3. `.fade-leave`는 leaving transition(트랜지션 제거)의 종료 상태를 정의합니다. leaving transition이 시작할 때 적용되고 전환이 종료 할 때 삭제됩니다.

`transition`이 값을 가지지 않는 경우 클래스는 기본적으로 `.v-transition`, `.v-enter` 그리고 `v-leave`됩니다.

### 사용자 정의 트랜지션 클래스들

> New in 1.0.14
> 1.0.14에서 새로 추가 되었습니다

트랜지션 정의에서 사용자 정의 `enterClass`와 `leaveClass`을 지정할 수 있습니다. 이들은 기존의 클래스 이름을 덮어 씁니다. [Animate.css](https://daneden.github.io/animate.css/)의 예와 같은 이미 존재하는 CSS 애니메이션 라이브러리에서 Vue 전환 시스템에 결합하고 싶을 때는 유용합니다.

``` html
<div v-show="ok" class="animated" transition="bounce">Watch me bounce</div>
```

``` js
Vue.transition('bounce', {
  enterClass: 'bounceInLeft',
  leaveClass: 'bounceOutRight'
})
```

### 트랜지션 종류 선언

> 1.0.14 버전부터 지원합니다

Vue.js는 트랜지션이 종료 된 것을 알기 위해 이벤트 리스너에 연결해야합니다. CSS 규칙의 형태(type)에 따라 `transitionend` 또는 `animationend` 중 하나가 적용됩니다. 한개 또는 다른 규칙을 적용 할 경우 Vue.js은 자동으로 올바른 형태를 감지 할 수 있습니다. 예를 들어 CSS 애니메이션이 Vue 의해 트리거됩니다 호버(hover)에서 CSS 트랜지션 효과를 가지는 경우 명시적으로 선언해야 합니다.

``` js
Vue.transition('bounce', {
  // Vue는이 전환에 즉시
  //`animationend` 이벤트에만 신경을 쓸 수있게합니다
  type: 'animation'
})
```

### 트랜지션 흐름에 대한 상세

`show` 속성이 변경되면 그에 따라 Vue.js는 `<div>` 요소를 추가/삭제한 다음에 지정된대로 트랜지션 클래스를 적용합니다.

- `show` 속성이 false가 되면:
1. `beforeLeave` 훅을 부릅니다.
2. `v-leave` 클래스를 요소에 적용하고 효과를 트리거합니다.
3. `leave` 훅을 부릅니다.
4. 트랜지션이 끝날 때까지 기다립니다 (`transitionend` 이벤트를 수신합니다).
5. DOM에서 요소와 `v-leave`를 제거합니다.
6. `afterLeave` 훅을 부릅니다.

- `show` 속성이 true가 되면:
1. `beforeEnter` 훅을 부릅니다.
2. `v-enter` 클래스를 엘리먼트에 적용합니다.
3. 그것을 DOM에 삽입합니다.
4. `enter` 후크를 부릅니다.
5. `v-enter`가 실제로 적용되도록 CSS 레이아웃을 강제 한 다음 요소를 원래 상태로 전환 되 돌리는를 트리거하기 위해 `v-enter` 클래스를 삭제합니다.
6. 트랜지션이 끝날 때까지 기다립니다.
7. `afterEnter` 후크를 부릅니다.

또한, 만약 `enter` 트랜지션이 진행 중일 때 요소가 삭제되는 경우 `enterCancelled` 훅이 불립니다. 그리고 `enter`에서 타이머가 작성 될 수있는 기회를 제공합니다. 반대 인 leaving 전환도 마찬가지입니다.

위와 같은 훅 함수 모두는 그 `this` 컨텍스트는 관련된 Vue 인스턴스를 설정하고 호출됩니다. 이것은 컴파일 범위의 동일한 규칙을 따릅니다. 트랜지션의 `this` 컨텍스트는 그것이 컴파일하는 범위를 나타내게됩니다.


마지막으로, `enter`와 `leave`는 필요에 따라 두번째 아규먼트에 콜백을받을 수 있습니다. 이렇게하면 트랜지션이 종료 할 때 명시적으로 사용자가 제어할 수있습니다. CSS의 `transitionend` 이벤트를 기다리는 대신 Vue.js는 트랜지션을 완료하기 위해 콜백을 호출합니다. 예를 들면 :


``` js
enter: function (el) {
  // 두번째 아규먼트가 없으면 트랜지션 종료, CSS 트랜지션 이벤트에서 결정
}
```

vs.

``` js
enter: function (el, done) {
  // 두번째 파라미터`done`이 불릴 때만 트랜지션이 종료
}
```

<p class="tip">여러 요소를 동시에 트랜지션하는 경우 Vue.js는 그 요소를 배치하고 자동으로 연속적으로 처리합니다.</p>

### CSS 애니메이션

CSS 애니메이션은 CSS 트랜지션과 같은 방식으로 적용할 수 있지만, 대상의 엘리먼트가 추가 된 후 `animationend`이 콜백 될 때까지 `v-enter` 클래스가 삭제되지 않는다는 차이가 있습니다.


예 : (CSS 규칙의 설명은 생략)


``` html
<span v-show="show" transition="bounce">Look at me!</span>
```

``` css
.bounce-transition {
  display: inline-block; / * 다른 경우, 스케일 애니메이션이 작동하지 않습니다 * /
}
.bounce-enter {
  animation: bounce-in .5s;
}
.bounce-leave {
  animation: bounce-out .5s;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes bounce-out {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(0);
  }
}
```

{% raw %}
<div id="anim" class="demo">
  <span v-show="show" transition="bounce">Look at me!</span>
  <br>
  <button @click="show = !show">Toggle</button>
</div>

<style>
  .bounce-transition {
    display: inline-block;
  }
  .bounce-enter {
    -webkit-animation: bounce-in .5s;
    animation: bounce-in .5s;
  }
  .bounce-leave {
    -webkit-animation: bounce-out .5s;
    animation: bounce-out .5s;
  }
  @keyframes bounce-in {
    0% {
      -webkit-transform: scale(0);
      transform: scale(0);
    }
    50% {
      -webkit-transform: scale(1.5);
      transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(1);
      transform: scale(1);
    }
  }
  @keyframes bounce-out {
    0% {
      -webkit-transform: scale(1);
      transform: scale(1);
    }
    50% {
      -webkit-transform: scale(1.5);
      transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(0);
      transform: scale(0);
    }
  }
  @-webkit-keyframes bounce-in {
    0% {
      -webkit-transform: scale(0);
      transform: scale(0);
    }
    50% {
      -webkit-transform: scale(1.5);
      transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(1);
      transform: scale(1);
    }
  }
  @-webkit-keyframes bounce-out {
    0% {
      -webkit-transform: scale(1);
      transform: scale(1);
    }
    50% {
      -webkit-transform: scale(1.5);
      transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(0);
      transform: scale(0);
    }
  }
</style>

<script>
new Vue({
  el: '#anim',
  data: { show: true }
})
</script>
{% endraw %}

## JavaScript 트랜지션

어떠한 CSS도 정의하지 않고, JavaScript 훅을 사용할 수 있습니다. JavaScript 트랜지션만 이용하면 **`done` 콜백은 `enter`와 `leave` 훅을 위해 꼭 필요**하고, 그렇지 않으면 그들은 동기적으로 불리며, 그리고 전환은 즉시 종료합니다.

Vue.js는 CSS 감지를 건너 뛸 수 있기 때문에 JavaScript 트랜지션에 대해 명시적으로 `css : false`를 선언하는 것도 좋은 아이디어입니다. 이것은 트랜지션에 의한 간섭으로부터 방지합니다.

다음 예제는 jQuery를 사용하여 사용자 정의 JavaScript 전환의 정의를 등록합니다 :

``` js
Vue.transition('fade', {
  css: false,
  enter: function (el, done) {
    // 요소는 이미 DOM에 삽입되어 있으며,
    // 애니메이션이 끝났을 때 done 콜백을 호출합니다
    $(el)
      .css('opacity', 0)
      .animate({ opacity: 1 }, 1000, done)
  },
  enterCancelled: function (el) {
    $(el).stop()
  },
  leave: function (el, done) {
    // enter와 마찬가지로
    $(el).animate({ opacity: 0 }, 1000, done)
  },
  leaveCancelled: function (el) {
    $(el).stop()
  }
})
```

`transition` 속성에 의해 같은 결과를 볼 수 있습니다 :

``` html
<p transition="fade"></p>
```

## 스태거링 트랜지션

`v-for`에서 `transition`을 사용하면 스태거링 전환을 만들 수 있습니다. `stagger`, 또는 `enter-stagger`, 또는 `leave-stagger` 중 하나의 속성을 트랜지션 요소에 추가하여 이것을 할 수 있습니다 :
``` html
<div v-for="item in list" transition="stagger" stagger="100"></div>
```

또는, 보다 세밀한 제어를 위해, `stagger`, `enterStagger` 또는 `leaveStagger` 후크를 제공 할 수 있습니다:

``` js
Vue.transition('stagger', {
  stagger: function (index) {
    // 각 트랜지션당 50ms의 딜레이를 부여합니다. 최대 300ms
    return Math.min(300, index * 50)
  }
})
```

예:

<iframe width="100%" height="200" style="margin-left:10px" src="https://jsfiddle.net/yyx990803/mvo99bse/embedded/result,html,js,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
