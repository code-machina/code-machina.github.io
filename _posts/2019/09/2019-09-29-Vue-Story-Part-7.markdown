---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 7)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: 조건절 렌더링"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-29 00:00:00 +0900
categories: [ "vue.js", "node.js", "computed", "watchers", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 드디어 파트 7 이네요. 처음 글을 올린 이후 한 달정도 시간이 흐른느낌입니다. 오늘은 Vue(뷰).js 에서 조건 렌더링에 대해 소개해드리도록 하겠습니다. 이번 글을 통해 v-if, v-else, v-else-if 등의 디렉티브의 사용법을 이해할 수 있으며 v-show 와 v-if 의 차이점 마지막으로 v-if 와 v-for 를 사용하는 방법을 소개해드리도록 하겠습니다.👏👏👏 만약 이전 시간의 내용이 기억나지 않는다면 본문 내 참조된 이전 시리즈들의 링크들을 참고해주세요. 자 그럼 시작해볼까요? 😺
 
<!--more-->

## 소개

만약 여러분이 특정 조건에 따라서 UI 를 표기하지 말지를 결정한다고 하였을 때, 어떻게 구현할 수 있을까요? 예전이라면 `style:none` 과 같은 방법을 통해 보여지지 않도록 하였을 것입니다. 그러나 우리에겐 이제 Vue.js 가 있습니다. 좀 더 간편하고 직관적으로 조건에 따른 표현을 할 수 있도록 하겠습니다. 

혹시 이전 내용이 기억나지 않는다구요? 아래의 링크를 정독해주세요!!

- **[쉽게 다가가는 Vue.js 이야기 (파트6)]({% link _posts/2019/09/2019-09-19-Vue-Story-Part-6.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트5)]({% link _posts/2019/09/2019-09-18-Vue-Story-Part-5.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트4)]({% link _posts/2019/09/2019-09-06-Vue-Story-Part-4.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트3)]({% link _posts/2019/09/2019-09-01-Vue-Story-Part-3.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트2)]({% link _posts/2019/08/2019-08-31-Vue-Story-Part-2.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트1)]({% link _posts/2019/08/2019-08-29-Vue-Story-Part-1.markdown %})**

<br>
{% include advertisements.html %}
<br>

## If 절 구현

`v-if` 는 각 블록을 조건에 따라 선택적으로 렌더링할지를 결정하는 특수한 디렉티브입니다. `v-if` 에 지정된 구문이 True 를 반환할 경우에만 렌더링됩니다. 아래의 샘플 코드를 볼까요?

샘플코드에서 v-else 라는 디렉티브가 등장합니다. v-if 와 연속적인 개념입니다. v-if 가 true 가 아닐 경우 렌더링될 블록입니다.

```html
<h1 v-if="awesome">Vue 는 멋집니다.</h1>
<h1 v-else>그렇지 않다구요? 😢</h1>
```

`awesome=true` 이라고 가정할 경우 렌더링될 화면은 아래와 같습니다.

```html
<!-- Rendered HTML -->
<h1>Vue는 멋집니다.</h1>
```

`awesome=false` 이라고 가정할 경우 렌더링될 화면은 아래와 같습니다.

```html
<!-- Rendered HTML -->
<h1>그렇지 않다구요? 😢</h1>
```

<br>
{% include advertisements.html %}
<br>

## template 을 이용한 조건절 그룹

위의 예에서는 간단한 h1 엘리먼트를 이용한 조건 렌더링을 보여드렸습니다. 그러나 h1 은 매우 작은 단위입니다. 보통은 수십개의 엘리먼트로 하나의 컴포넌트를 구성하게 됩니다. 즉, 그룹에 대한 조건 렌더링이 필요함을 의미합니다.

그럼 샘플코드를 먼저 보실까요?

```html
<template v-if="ok">
  <h1>제목</h1>
  <p>문단 1</p>
  <p>문단 2</p>
</template>
```

그룹에 대한 렌더링을 할때 필요한 것은 두 가지 입니다. 바로 위의 코드에서 사용된 `<template>` 과 `v-if` 디렉티브입니다. template 은 보이지 않는 wrapper 역할을 합니다. 그리고 **실제로 렌더링 되었을 때 렌더링되지 않습니다.** 

<br>
{% include advertisements.html %}
<br>

## v-else 와 v-else-if

`v-else-if` 는 2.1.0+ 버전에 등장한 디렉티브입니다. `v-else` 와 같이 사용하게되면 매우 효과적입니다. 같이 보실까요?

**`v-else-if`, `v-else` 는 `v-if` 와 연속적으로 사용해야합니다. 그렇지 않으면 제대로 동작하지 않음에 주의해주세요.**

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

<br>
{% include advertisements.html %}
<br>

## key 를 이용한 재사용 제어

Vue 는 엘리먼트를 가능한한 효과적으로 렌더링하려고 합니다. 따라서 재사용을 하기도 합니다. 아래의 예를 살펴볼까요?

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

아래의 Gif 를 한번 살펴볼까요? Toggle 을 통해서 Username 에서 Email 로 렌더링되었습니다. 그런데, 타이핑한 문자열이 그대로 이군요. 즉, 재사용(Re-Use)되었음을 의미합니다. 이는 Vue 의 효율성을 보여주는 예입니다. 동일한 엘리먼트를 재사용함으로써 절약하는 것이겠죠!

[![Vue Re-use Element Example](/assets/img/2019/09/vue-reuse-example.gif)](/assets/img/2019/09/vue-reuse-example.gif)


그러나 이러한 구현은 언제나 바람직하지는 않습니다. 따라서, Vue(뷰)는 key 어트리뷰트를 통해 이를 재사용을 제어하도록 구현하였습니다. 아래의 샘플을 볼까요?

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

key 어트리뷰트에 각기 다른 이름이 부여된 것을 알 수 있습니다. 이는 key 를 사용할 때 강제 사항입니다. 아래의 이미지를 보면서 한번 차이점을 감상해보실까요?

[![Vue Re-use Element Example](/assets/img/2019/09/vue-reuse-example-2.gif)](/assets/img/2019/09/vue-reuse-example-2.gif)

<br>
{% include advertisements.html %}
<br>


## v-show 디렉티브

v-if 만 조건 렌더링이 가능한 것은 아닙니다. `v-show` 디렉티브를 통해서도 가능합니다. 그러나 `v-show` 와 `v-if` 에는 명확한 차이점이 있습니다. `v-show` 는 단순히 CSS 프로피티 중 display 를 토글(Toggle)하는 역할을 수행합니다. 즉, vDOM(Virtual DOM) 자체에 엘리먼트가 존재하는 것이지요. 반면에 `v-if` 는 vDOM 을 제어한다는 면에서 차이를 발견할 수 있습니다.

아래는 간단한 샘플입니다. 참고해주세요.

```html
<h1 v-show="ok">Hello!</h1>
```

<br>
{% include advertisements.html %}
<br>

## v-if 와 v-show 의 비교

`v-if` 는 **실제** 조건 렌더링입니다. 즉, 조건 블록에서 이벤트 리스너와 자식(하위) 컴포넌트들이 실제로 파괴된 후에 다시 생성되는 절차를 진행하기 때문이죠.

대신, `v-if`는 게으릅니다(Lazy Loading). 즉, 초기 렌더링때 조건절이 `false` 라면 어떠한 행위도 하지 않습니다. 그러다가 추후에 `true` 가 된다면 그제서야 렌더링됩니다.

이와 대조적으로 `v-show` 의 동작은 매우 간편합니다. 항상 렌더링된 상태로 있는 것이죠. 그리고 CSS 를 기반으로 토글링됩니다. 

그렇다면 비용(cost) 측면에서는 v-show 가 v-if 보다 우위에 있습니다. 따라서, 자주 토글하는 경우 v-show 를 사용해야 함을 자연스럽게 유추할 수 있습니다.

<br>
{% include advertisements.html %}
<br>

## v-if 와 v-for 의 조합

> ⚠ WARNING: 공식문서는 v-if 와 v-for 의 조합을 권장하지 않습니다. 만약 꼭 써야되는 상황이라면, v-for 는 v-if 보다 우선순위가 높음을 기억해 주세요.

<br>
{% include advertisements.html %}
<br>

## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 조건 렌더링(Conditional Rendering) 영역이 완료되었습니다! 👏 (짝짝짝) 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

다음 시간에는 **목록 렌더링**을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
