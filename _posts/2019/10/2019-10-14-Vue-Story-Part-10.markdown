---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 10)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: 이벤트 핸들링"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-10-04 00:00:00 +0900
categories: [ "vue.js", "node.js", "event handling", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 Vue(뷰)의 이벤트 핸들링(Event Handling)에 대해 소개해드리도록 하겠습니다. 이번 시간을 통해 Vue 가 제공하는 기능 중 **이벤트 핸들링** 을 완벽히 정리해 보도록 하겠습니다. "이벤트 수신", "메서드 이벤트 핸들링", "이벤트/키 제어자(Modifier)" 등을 이해하고 자유롭게 사용할 수 있습니다. 👏👏👏 자 그럼 시작해볼까요?
 
<!--more-->

## 이어가기

만약 이전 시간의 내용이 기억나지 않는다면 아래에 참조된 이전 시리즈의 링크들을 차분히 다시 살피는 것은 어떨까요? 

- **[쉽게 다가가는 Vue.js 이야기 (파트9)]({% link _posts/2019/10/2019-10-12-Vue-Story-Part-9.markdown %})**
- **[쉽게 다가가는 Vue.js 이야기 (파트8-2)]({% link _posts/2019/10/2019-10-04-Vue-Story-Part-8-2.markdown %})**
- **[쉽게 다가가는 Vue.js 이야기 (파트8-1)]({% link _posts/2019/10/2019-10-01-Vue-Story-Part-8-1.markdown %})**
- **[쉽게 다가가는 Vue.js 이야기 (파트7)]({% link _posts/2019/09/2019-09-29-Vue-Story-Part-7.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트6)]({% link _posts/2019/09/2019-09-19-Vue-Story-Part-6.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트5)]({% link _posts/2019/09/2019-09-18-Vue-Story-Part-5.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트4)]({% link _posts/2019/09/2019-09-06-Vue-Story-Part-4.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트3)]({% link _posts/2019/09/2019-09-01-Vue-Story-Part-3.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트2)]({% link _posts/2019/08/2019-08-31-Vue-Story-Part-2.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트1)]({% link _posts/2019/08/2019-08-29-Vue-Story-Part-1.markdown %})**

<br>
{% include advertisements.html %}
<br>

## v-on 디렉티브

우리는 이전 시간에 v-on 디렉티브를 이용해 DOM 이벤트를 수신하고 맵핑된 자바스크립트 코드를 구동(trigger)할 수 있다고 배웠습니다. 이전 시간의 내용을 상기시켜 볼까요?

{% raw %}

아래의 예제는 버튼을 누르면 카운트가 증가하는 예시입니다. 즉, v-on 디렉티브로 click 이벤트를 수신시 동작할 인라인 코드(혹은 메서드)를 지정한 것입니다. 여기서는 `counter += 1` 구문이 동작하겠네요.

```html
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
```

```js
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

{% endraw %}

[![Vue Re-use Element Example](/assets/img/2019/10/vue-event-counter-ex1.gif)](/assets/img/2019/10/vue-event-counter-ex1.gif)


<br>
{% include advertisements.html %}
<br>

## 메서드 이벤트 핸들러

위의 예시에서 우리는 인라인 자바스크립트 구문(inline expression)을 통해서 트리거의 로직을 구성하였습니다. 그러나 메서드 또한 사용 가능합니다. 메서드는 복잡한 로직을 구현할 때 매우 적절합니다. 한편 살펴볼까요?

아래와 같이 메서드로 코드를 구분함으로써 복잡한 절차를 표현하기 용이해졌습니다.

```html
<div id="example-2">
  <!-- `greet` 은 Vue 인스턴스의 methods 객체(속성) 내에 정의된 메서드의 이름입니다. -->
  <button v-on:click="greet">Greet</button>
</div>
```

```js
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // methods 객체 내에 greet 라는 메서드를 정의합니다.
  methods: {
    greet: function (event) {
      // 메서드 내에서 this 키워드는 Vue 인스턴스를 가리킵니다.
      alert('Hello ' + this.name + '!')
      // 파라미터로 전달된 event 는 native DOM 이벤트 입니다.
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
```

<br>
{% include advertisements.html %}
<br>

## 인라인 핸들러와 메서드 조합

위의 두 단계를 거쳐 우리는 인라인 핸들러와 메서드 핸들러를 체험해 보았습니다. 특히 메서드 핸들러의 샘플을 보면서 파라미터를 직접 전달하는 구현에 대해 생각해 보셨을 수 있습니다. Vue 는 이러한 요구사항을 이미 받아들여 지원하고 있습니다. 아래의 샘플을 볼까요?

`say('hi')` 구문을 보면 파라미터를 전달하고 있음을 알 수 있습니다. 즉, 인라인 구문(inline expression)입니다. 단순히 메서드 이름을 연결하는 것과는 다릅니다.

```html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
```

```js
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```

위의 구문을 응용하면 원본 DOM 이벤트를 인자로 전달할 수도 있습니다. 아래의 예는 `$event` 특수 변수를 사용하여 원본 DOM 이벤트를 인자로 전달함을 알 수 있습니다.

```html
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
```

```js
// ...
methods: {
  warn: function (message, event) {
    // event 인자를 통해서 native 이벤트에 접근이 가능합니다.
    if (event) event.preventDefault()
    alert(message)
  }
}
```

<br>
{% include advertisements.html %}
<br>

## 이벤트 한정자(Event Modifier)

event.preventDefault() 또는 event.stopPropagation() 을 이벤트 핸들러에서 호출하는 것은 매우 흔한일입니다. 비록 메서드에서 이를 작성하는 것은 매우 쉬운 일이나 한정자(Modifier)를 통해서 해결할 경우 메서드는 로직에 집중할 수 있어 더욱 효과적이라고 할 수 있습니다. 

Vue 는 v-on 디렉티브를 위한 이벤트 한정자(Event Modifier)를 제공하고 있으며 그 목록은 아래와 같습니다.

- .stop
  - 이벤트의 전파를 중지 (다른 엘리먼트로 이벤트가 전달되지 않습니다.)
- .prvent
  - 이벤트로 하여금 페이지 Reload 를 차단합니다.
- .capture
  - 하위 엘리먼트의 이벤트를 가로채어 처리합니다.
- .self
  - 오직 자신(엘리먼트)와 관련된 이벤트만을 처리합니다.
- .once
  - 오직 한번만 트리거됨을 지정합니다.
- .passive
  - 특정 이벤트의 완료을 기다리지 않고 기본적으로 정의된 행동을 취합니다.
  - 즉, `v-on:scroll.passive="onScroll"` 구문은 스크롤 동작시 onScroll 메서드 호출과 동시에 기본 브라우저 스크롤 동작을 취합니다.

```html
<!-- 클릭 이벤트의 전파를 중지합니다. -->
<a v-on:click.stop="doThis"></a>
<!-- submit 이벤트가 페이지를 리로드 하는 것을 차단합니다. -->
<form v-on:submit.prevent="onSubmit"></form>
<!-- 한정자는 연쇄(Chaining)이 가능합니다. -->
<a v-on:click.stop.prevent="doThat"></a>
<!-- 한정자만을 지정할 수 있으며 효과는 동일합니다. -->
<form v-on:submit.prevent></form>

<!-- capture 모드는 하이 엘리먼트에서 발생한 이벤트를 가로채는 역할을 하며 하위 엘리먼트의 이벤트들을 대신 처리해줍니다. -->
<div v-on:click.capture="doThis">...</div>
<!-- 이벤트가 오로지 엘리먼트 자신을 가리키는 경우(event.target == this)에만 이벤트 핸들러를 구동합니다. -->
<div v-on:click.self="doThat">...</div>
<!-- 이 클릭 이벤트는 오로지 한번만 트리거 됩니다.  -->
<a v-on:click.once="doThis"></a>
<!-- onScroll 이벤트가 완료되기를 기다리지 않고 스크롤 이벤트의 기본 동작이 즉시 발생합니다. -->
<div v-on:scroll.passive="onScroll">...</div>
```

<br>
{% include advertisements.html %}
<br>

## .prevent 와 .passive 상세 설명

모든 이벤트에는 Default Behavior 가 있습니다. 스크롤을 하면 브라우저가 다음 페이지를 보여주는 것이 그것입니다. 그러나, 이러한 동작을 원하지 않을 때도 있습니다. 아래의 예시를 들어볼까요?

특정 컴포넌트 위에서 스크롤을 할 경우 컴포넌트 하위의 블로그 제목 목록을 스크롤링한다고 가정해봅니다. 이때 `.preventDefault()`(==.prevent)를 사용하여 브라우저의 기본 동작을 차단해야 합니다. 

그러나, 반대의 경우도 있습니다. 스크롤 동작 시 팝업을 출력한다고 가정해볼까요? 그런데, 팝업의 내용에서 외부의 데이터를 끌어온다고 가정해보겠습니다. 이 경우 passive 한정자를 사용할 경우 기본 정의 동작인 브라우저 스크롤링은 되면서 데이터를 끌어오는 동작을 동시에 수행할 수 있습니다.

- [① addEventListener API 의 Passive 옵션](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters)
- [② preventDefault() API 설명](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault)
- [③ Passive Listener 를 이용한 스크롤링 성능 향상](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Improving_scrolling_performance_with_passive_listeners)

<br>
{% include advertisements.html %}
<br>

## 이벤트 한정자 연쇄(Chaining)시 주의점

위에서 이벤트 한정자를 연쇄할 수 있다고 말하였습니다. 그러나 연쇄 시 작성 순서는 적용 순서와 동일하므로 모순이 발생할 수 있습니다. 따라서, 연쇄를 구현할 시에 이 절을 꼭 참조하시길 바랍니다.

### .prevent 와 .self 연쇄

`v-on:click.prevent.self` 는 모든 클릭을 차단합니다. 반면에 `v-on:click.self.prevent` 는 자신(엘리먼트)에 대한 클릭 만을 차단합니다.

### .passive 와 .prevent 연쇄

`.passive` 와 `.prevent` 를 동시에 사용해서는 안됩니다.

<br>
{% include advertisements.html %}
<br>

## 키 한정자(Key Modifiers)

Vue 는 키보드 이벤트에 대해서도 이벤트 핸들러를를 지정할 수 있습니다. 다만, keyCode 속성은 deprecated 되었으므로 Vue 가 제공하는 alias 를 이용해야 합니다.

```html
<input v-on:keyup.page-down="onPageDown">
<input v-on:keyup.enter="submit">
<!-- keyCode 속성은 deprecated 속성으로 지원되지 않을 수 있습니다. -->
<input v-on:keyup.13="submit"> 
<!-- e -->
```

F1 키와 같은 특수키를 지원해야하는 경우 아래와 같이 `Vue.config.KeyCodes` 를 커스터마이징하여 구현할 수 있습니다. 단 이름을 정의할 때, kebab-case 로 작성하여야 합니다. 

mediasPlayPause 대신에 media-play-pause 와 같이 말이죠.

```js
Vue.config.keyCodes = {
  v: 86,
  f1: 112, // F1 키를 누를 때 이벤트를 정의할 수 있게되었습니다.
  // camelCase won`t work
  mediaPlayPause: 179,
  // instead you can use kebab-case with double quotation marks
  "media-play-pause": 179,
  up: [38, 87]
}
```

```html
<input type="text" @keyup.media-play-pause="method">
```

<br>
{% include advertisements.html %}
<br>

## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 이벤트 핸들링이 완료되었습니다! 👏 (짝짝짝) 또한, 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

다음 시간에는 **Vue 스토리의 마지막인 폼 입력 바인딩(Form Input Binding)**을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
