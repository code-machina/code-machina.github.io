---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 8-2)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: 컴포넌트 기본 (2)"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-10-04 00:00:00 +0900
categories: [ "vue.js", "node.js", "component", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 Vue(뷰).js 에서 조건 렌더링에 대해 소개해드리도록 하겠습니다. 이번 시간을 통해 Vue 가 제공하는 컴포넌트의 "단일 루트 엘리먼트", "하위 이벤트 수신", "slot 을 이용한 컨텐츠 배포", "동적 컴포넌트", "DOM 템플릿 제약사항" 들을 이해하고 자유롭게 사용할 수 있습니다. 👏👏👏 자 그럼 시작해볼까요?
 
<!--more-->

## 이어가기

만약 이전 시간의 내용이 기억나지 않는다면 아래에 참조된 이전 시리즈의 링크들을 차분히 다시 살피는 것은 어떨까요? 

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

## 이어서

이전 시간에 우리는 컴포넌트의 기능 중 아래의 내용에 대해 배웠습니다. 

- 컴포넌트란
- 컴포넌트 재사용
- 컴포넌트 구성 (Globally, Locally)
- 하위 컴포넌트에 데이터 전달

위의 내용을 복습하기 원하는 분들은 아래의 링크를 참조해 주세요.

- **[쉽게 다가가는 Vue.js 이야기 (파트8-1)]({% link _posts/2019/10/2019-10-01-Vue-Story-Part-8-1.markdown %})**

<br>
{% include advertisements.html %}
<br>

## 단일 루트 엘리먼트

단일 루트 엘리먼트는 모든 컴포넌트를 지배하는 원칙입니다. 모든 엘리먼트들은 하나의 엘리먼트의 하위이어야 한다는 점입니다. 아래의 샘플 코드를 볼까요?

```html
<div class="blog-post">
  <h3>{{ title }}</h3>
  <div v-html="content"></div>
</div>
```

그렇다면 아래의 코드를 컴포넌트의 템플릿으로 지정하면 어떻게 될까요? 정답은 에러가 발생한다는 점입니다. 즉, 렌더된 화면이 보이지 않을 것입니다. 

```html
<h3>{{ title }}</h3>
<div v-html="content"></div>
```

<br>
{% include advertisements.html %}
<br>

## 하위 컴포넌트의 이벤트 수신

부모 컴포넌트는 하위 컴포넌트의 이벤트를 수신(Listen)할 수 있습니다. 여러분들이 개발을 하다보면 하위 컴포넌트의 이벤트를 부모 컴포넌트가 인식할 수 있도록 하는 채널을 만들고 싶어할 것입니다. 

예시를 들어볼까요? 

`<blog-post>` 컴포넌트 내에서 버튼을 클릭할 경우 부모 컴포넌트가 관리하는 모든 글자가 커지도록 기능을 추가하려고 합니다. 

그렇다면 계층 구조는 아래와 같을 것입니다.

```bash
|- ParentComponent
   |- blog-post
      |- {title: 'Vue 와 여행 즐기기', /* ... */ }
      |- <Button> 글자 크기 키우기 </Button>
   |- blog-post
      |- {title: 'Vue 와 블로깅 즐기기', /* ... */ }
      |- <Button> 글자 크기 키우기 </Button>
   |- blog-post
      |- {title: 'Vue 가 재밌는 이유', /* ... */ }
      |- <Button> 글자 크기 키우기 </Button>
```

부모 컴포넌트(ParentComponent) 하위에는 여러개의 blog-post 컴포넌트들이 나열되어 있습니다. 그리고 이 컴포넌트들은 각각 `<Button> 글자 크기 키우기 </Button>` 컴포넌트를 포함하는 형태입니다. 만약 하위 컴포넌트에서 `글자 크기 키우기` 버튼을 클릭한다면 ParentComponent 의 모든 폰트 크기가 커지도록 하고 싶습니다. 

정답부터 알려드릴게요. 우선, 부모 컴포넌트는 `enlarge-text` 라는 이름의 이벤트가 발생할 것이라고 가정하여 `v-on` 디렉티브를 이용해 이벤트 수신을 정의합니다. 그리고 수신 시 동작하는 코드는 `postFontSize += 0.1` 입니다. 

그리고 하위 컴포넌트인 `blog-post` 컴포넌트는 버튼 클릭 시 `$emit` 함수를 통해 `enlarge-text` 라는 이름의 이벤트를 송출합니다. 여기서 emit 은 `발하다`, `내다` 라는 의미입니다. 즉, `enlarge-text` 라는 이름의 이벤트를 `발하다`. 라는 의미로 해석하면 이해가 빠릅니다.

```html
<!-- ParentComponent.vue 파일 -->
<blog-post
  ...
  v-on:enlarge-text="postFontSize += 0.1"
></blog-post>
```

```html
<!-- blogPost.vue 파일 -->
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
```

좀 더 간편하게 확인해 볼까요? 즉, 아래와 같은 구조로 이벤트가 발송됩니다.

```bash
|- ParentComponent <-----|
   |- blog-post ---------| (emit('enlarge-text'))
```

마지막으로 아래의 gif 를 확인하면 완벽히 구조를 이해할 수 있습니다. 

[![Child component can emit an event to parent component. and Parent Component would handle a event using v-on directives.](/assets/img/2019/09/vue-comp-event-1.gif)](/assets/img/2019/09/vue-comp-event-1.gif)

<br>
{% include advertisements.html %}
<br>

### 이벤트로 데이터 전달하기

하위 컴포넌트가 단순히 이벤트가 발생한 사실만을 부모 컴포넌트에 전달할 수 있는 것은 아닙니다. 데이터 또한 전달이 가능합니다. 이 절에서는 데이터를 발송하고 이를 수신하는 예시를 들어보겠습니다. 아래의 샘플을 볼까요?

`$emit` 인자에 두번째 인자가 추가되었습니다. 0.1 은 폰트 크기를 얼마나 키울지를 나타내는 수치입니다. 그렇다면, 이벤트를 수신하는 부모 컴포넌트는 어떻게 데이터를 받아들일까요?

```html
<!-- blogPost.vue 파일 -->
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
```

부모 컴포넌트는 `$event` 라는 특수 객체를 통해 데이터를 수신합니다. 즉, 공유 변수인 셈입니다.

```html
<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>
```

그러나, inline expression 만 사용하면 코드 구현에 여러가지 제약조건이 발생하기 마련입니다. 따라서, 아래와 같이 메서드로 분리할 수 있습니다.

```html
<blog-post
  ...
  v-on:enlarge-text="onEnlargeText"
></blog-post>
```

하위 컴포넌트가 전달한 데이터를 함수의 `enLargeAmount` 인자가 받아줍니다. 

```js
methods: {
  onEnlargeText: function (enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

<br>
{% include advertisements.html %}
<br>

### 컴포넌트에서 v-model 사용하기

이제, 파라미터를 전달하는 방법을 배웠으니 v-model 을 사용하는 방법을 알려드리겠습니다. 시작하기전에 동치관계를 한번 파악해 유념해야합니다. 아래의 코드는 동일한 코드입니다.

- 코드 A 예시

```html
<!-- 코드 A -->
<input v-model="searchText">
```

- 코드 B 예시

```html
<!-- 코드 B -->
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```

위의 코드 A 와 B 는 동일합니다. 그렇다면, 컴포넌트를 사용할 때도 이러한 공식이 적용될까요? 정답부터 말씀드리면 가능합니다. input 이라는 이름 대신에, custom-input 이라는 이름으로 치환해 보도록 하겠습니다. 그리고 예시 `코드 B` 를 변형해 보도록 하겠습니다.


```html
<!-- 코드 B` -->
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event" />
```

이제, custom-input 컴포넌트를 정의해 주어야겠죠? 아래의 사항을 준수하면 됩니다. 

- value 어트리뷰트와 props 의 value 를 연결(Binding)
- input 이벤트 발생 시 custom `input` 이름으로 이벤트를 새로운 value 와 발송(emit)

> 아래의 코드에서 템플릿의 다중 개행 문자열은 template literals 이라고 불리는 녀석입니다. `{foo}` 와 같이 변수 보간 등의 여러기능을 제공해줍니다. `(backticks) 문자를 통해서 구분합니다. 그러나 저 코마는 **HTML 을 문자열로 사용하는 것을 권장해드리지 않는 점을 명심해 주세요!**

위의 사항을 구현한 코드는 아래와 같습니다.

```js
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

이제 모든 요구사항이 만족되었으니 아래의 코드도 완벽히 동일하게 동작합니다. 위의 하위컴포넌트를 다루는 코드는 공식처럼 사용하시면 편합니다.

```html
<custom-input v-model="searchText"></custom-input>
```

<br>
{% include advertisements.html %}
<br>

## Slot을 이용한 컨텐츠 배포

HTML 엘리먼트 처럼 컨텐츠를 컴포넌트로 전달할 수 있음은 매우 유용합니다. 아래를 살펴볼까요? 아래의 코드는 예외를 alert 창으로 던지는 역할을 합니다.

```html
<alert-box>
NullReferencedException 이 발생하였습니다.
</alert-box>
```

아래의 템플릿 리터럴(template literals)를 자세히 살펴볼 경우 `<slot>` 엘리먼트가 정의된 것을 확인 가능합니다. 즉, 위에서 작성한 `NullReferencedException 이 발생하였습니다.` 라는 컨텐츠가 slot 이 정의된 영역에 들어옴을 의미합니다. 이를 응용할 경우 매우 동적인 컴포넌트의 정의가 가능합니다.

```js
Vue.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```

<br>
{% include advertisements.html %}
<br>

## 동적 컴포넌트

어떠한 컴포넌트들은 탭 형태로 나열하면 가독성에 매우 좋으며 범주별로 나누어 관리하기에 용이합니다. 아래의 gif 를 참고해보실까요?

[![Child component can emit an event to parent component. and Parent Component would handle a event using v-on directives.](/assets/img/2019/09/vue-dycomp-1.gif)](/assets/img/2019/09/vue-dycomp-1.gif)


위의 이미지의 예제 샘플은 [jsfiddle, 동적 컴포넌트 예시](https://jsfiddle.net/chrisvfritz/o3nycadu/) 링크를 참조해주세요. 

우선, 구현 예시를 보았기 때문에 약간의 설명 만으로 충분히 이해할 수 있습니다. 위의 구현은 `<component>` 엘리먼트와 `is` 특수 어트리뷰트를 통해서 구현되었습니다. 모두 Vue 에서 제공하는 규칙이므로 아 그렇게 하도록 약속하였구나라고 생각하면 됩니다. 

jsfiddle 예제에서 가장 중요한 코드는 아래의 한줄 코드입니다. 그렇다면 component 는 그렇다고 치더라도 `currentTabComponent`에 올수 있는 Value는 아래와 같습니다.

```html
<component v-bind:is="currentTabComponent"></component>
```

- 등록된 컴포넌트의 이름
- component 엘리먼트을 위한 옵션 객체입니다.

등록된 컴포넌트의 이름의 경우 아래와 같이 문자열 데이터로 등록된 컴포넌트의 이름을 전달합니다.

```html
<component is="blog-post"></component>
```

<br>
{% include advertisements.html %}
<br>

## DOM 템플릿 사용 경고

HTML 엘리먼트 중에는 `<ul>`, `<ol>`, `<table>`, `<select>` 와 같은 엘리먼트가 있습니다. 이러한 엘리먼트에는 `<li>`, `<tr>`, `<option>` 과 같은 객체들만이 표시될 수 있습니다. 

아래의 예는 table 엘리먼트 내에 선언된 `<blog-post-row>` 엘리먼트는 에러를 발생시킵니다. 다행이 이를 해결할 방법이 있습니다. 

```html
<!-- 에러가 발생합니다! -->
<table>
  <blog-post-row></blog-post-row>
</table>
```

위의 코드는 아래와 같이 작성되면 잘 작동할 수 있습니다.

```html
<!-- is 특수 어트리뷰트라면 두렵지 않습니다!  -->
<table>
  <tr is="blog-post-row"></tr>
</table>
```

<br>
{% include advertisements.html %}
<br>

## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 컴포넌트 기본 영역이 완료되었습니다! 👏 (짝짝짝) 또한, 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

다음 시간에는 **리스트 렌더링**을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
