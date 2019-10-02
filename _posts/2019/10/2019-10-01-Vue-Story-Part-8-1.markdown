---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 8-1)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: 컴포넌트 기본 (1)"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-10-01 00:00:00 +0900
categories: [ "vue.js", "node.js", "component", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 Vue(뷰).js 에서 조건 렌더링에 대해 소개해드리도록 하겠습니다. 이번 시간을 통해 Vue 가 제공하는 컴포넌트의 "기본 사용법", "재사용", "컴포넌트 구성하기", "하위 컴포넌트로 데이터 전달" 기능을 이해하고 자유롭게 사용할 수 있습니다. 👏👏👏 자 그럼 시작해볼까요?
 
<!--more-->

## 이어가기

만약 이전 시간의 내용이 기억나지 않는다면 아래에 참조된 이전 시리즈의 링크들을 차분히 다시 살피는 것은 어떨까요? 

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

## 컴포넌트란?

컴포넌트는 하나의 단위 기능입니다. 우리가 Navigation Bar 를 구성하는 코드를 HTML로 구현하던 시절에는 이러한 단위를 구분하기 힘들었습니다. 따라서 복잡성도 늘어만 갔죠. 이제, Vue.js 를 사용하게되면 이러한 어려움으로부터 탈출할 수 있습니다. 아래의 코드를 볼까요?

아래의 코드는 전형적인 Vue 컴포넌트를 정의하는 코드입니다. 이제 우리는 클릭 카운트를 수집하는 버튼을 하나 가지게 되었습니다.

```js
// `button-counter` 라는 새로운 컴포넌트를 정의합니다.
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">  {{ count }} 째 버튼을 클릭하였습니다. </button>'
})
```

그렇다면 이 컴포넌트를 어떻게 사용할까요? 아래의 코드를 살펴 볼 경우 쉽게 해결됩니다. 우선 Root 엘리먼트에 Vue 인스턴스를 생성하도록 합니다. 그리고 `button-counter` 컴포넌트를 엘리먼트처럼 작성해줍니다.

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

```js
new Vue({ el: '#components-demo' })
```

[![Vue Re-use Element Example](/assets/img/2019/09/vue-comp-ex-1.gif)](/assets/img/2019/09/vue-comp-ex-1.gif)

<br>
{% include advertisements.html %}
<br>

## 컴포넌트 재사용

컴포넌트는 정의하고 사용할 수 있다고 배웠습니다. 그렇다면, 모든 컴포넌트는 독립적일까요? 맞습니다. 서로가 독립적입니다. 따라서 간섭하지 않습니다. 아래의 예를 살펴볼까요?

```html
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

[![Vue Re-use Element Example](/assets/img/2019/09/vue-comp-ex-2.gif)](/assets/img/2019/09/vue-comp-ex-2.gif)

<br>
{% include advertisements.html %}
<br>


### data 속성: 함수 선언

Vue 공식 가이드는 data 속성을 **반드시** 함수로 선언해야 한다고 말하고 있습니다. 그럼 왜 함수로 선언해야 할까요? 

함수로 선언하지 않을 경우 모든 인스턴스에게 영향을 줄 수 있기 때문입니다. 즉, 컴포넌트1에서 count 를 변경할 경우 컴포넌트2 또한 영향을 받게 됨을 의미합니다.

아래와 같이 컴포넌트들이 독립적이지 않다면 참으로 당황스러운 상황이 연출될 것입니다.

[![If you don't declare data property as function, count property affects all vue instances](/assets/img/2019/09/vue-comp-ex-3.gif)](/assets/img/2019/09/vue-comp-ex-3.gif)

<br>
{% include advertisements.html %}
<br>


## 컴포넌트 구성

중첩된 컴포넌트들로 앱을 구성하는 일은 이제 일상이 되었습니다. 예를들어 헤더(Header), 사이드바(Sidebar), 컨텐츠 영역으로 구분되는 앱을 컴포넌트로 구성한다면 어떨까요? 그리고 각 컴포넌트들은 네비게이션 링크, 블로그 포스트등을 포함할 것입니다.

여기서 질문을 드릴게요. 템플릿에 컴포넌트를 사용하기 위해서는 등록(register)과정이 필요합니다. 이를 통해 Vue 인스턴스는 컴포넌트의 존재를 확인가능합니다. 

그런데, 컴포넌트 등록에 두 가지 종류가 있다는 사실을 아시나요? 바로 global 과 local 입니다. 지금까지 예제에서 `Vue.component` 문을 통해서 전역적(globally)으로 컴포넌트를 등록해왔습니다. 아래의 예시처럼 말이죠.

```js
Vue.component('my-component-name', {
  // ... options ...
})
```

전역적으로 등록된 컴포넌트들은 어떠한 루트 Vue 인스턴스(new Vue)에서도 사용할 수 있습니다. 하위 컴포넌트들 내에서도 이는 통하는 논리입니다. 로컬 컴포넌트 등록은 추후의 내용으로 미루겠습니다. 아마도 컴포넌트 응용편에서 다루는게 적당할 것 같네요. 😁

<br>
{% include advertisements.html %}
<br>


## 하위 컴포넌트에 데이터 전달

컴포넌트를 만들었다고 가정해볼까요? 그런데 이 컴포넌트에 데이터를 전달하는 구조를 갖추지 못한다면 매우 비효율적으로 작업할 수 밖에 없습니다. 아래의 상황을 가정해보죠.

**블로그 메인 뷰는 카드형 블로그 포스트 컴포넌트를 배치하여 사용자가 원하는 글을 찾아갈 수 있도록 UI  를 구성하였다.**

위의 상황을 미루어 예시를 들어볼까요? 블로그 메인 뷰(/blog/main) URL 로 접근 시 블로그 데이터를 서버로부터 전송 받도록 구현하였다고 가정해보겠습니다. 그렇다면 메인 뷰에서 전송받은 데이터를 각 컴포넌트에 전달하는 경우 전달 방법이 빌요합니다. 

이때 사용하는 속성(property)가 props 속성입니다. 아래의 예시를 살펴볼까요?

```js
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
```

`<blog-post title="안녕하세요. 코마입니다."></blog-post>` 라는 컴포넌트는 `title` 을 마치 어트리뷰트(Attribute) 처럼 받습니다. 즉, 상위 컴포넌트로부터 하위 컴포넌트로 데이터가 전달된 것입니다. 

그렇다면 아래와 같은 구현을 살펴볼까요? 루트 뷰 인스턴스가 배열을 데이터를 data 속성으로 가지고 있는 경우입니다.

```js
new Vue({
  el: '#blog-post-demo',
  data: {
    posts: [
      { id: 1, title: 'My journey with Vue' },
      { id: 2, title: 'Blogging with Vue' },
      { id: 3, title: 'Why Vue is so fun' }
    ]
  }
})
```

이제 우리는 `v-for` 디렉티브를 통해 `data.posts` 배열의 내용을 자유롭게 나열할 수 있습니다.

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
></blog-post>
```

<br>
{% include advertisements.html %}
<br>

## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 컴포넌트 기본 영역의 절반이 완료되었습니다! 👏 (짝짝짝)나머지 절반의 내용은 **쉽게 다가가는 Vue.js 이야기 (파트 8-12**에서 다룰 예정이니 많은 관심을 부탁드립니다. 또한, 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
