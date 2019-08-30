---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 1)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: Introduction 편"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-08-30 00:00:00 +0900
categories: [ "javascript", "vue.js", "introduction", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 Vue.js 의 공식 문서의 목차를 통해서 Vue.js 의 내용을 아주 간략하게 요약해 보려고합니다. 만약 여러분이 Vue.js 를 사용하고 있지만, 전체적인 그림을 익히기 어려운 경우 이 문서는 여러분의 힘이 되어줄 것이 분명합니다. 저를 믿고 한번 따라와주세요~! 😺

<!--more-->

## 목차를 뜯어보는 이유

책을 사면 목차를 먼저 보아야 한다고 하죠? 그래야 책의 내용과 방향을 정확히 알 수 있겠죠! 

**그런데 왜 기술 문서는 목차를 먼저 보지 않을까요?**

그래서 한번 준비해 보았습니다. 

> ⚠️ **주의**: 이 글은 아주 얇고 빠르게 Vue.js 의 내용을 훑어보는 과정이에요. 그러나 여러분들 중에는 좀 더 자세한 내용을 원할 수 있을 것 같아요. 지금은 개발 중인 [Coma's Vue Awesome](https://code-machina.github.io/coma-vue-awesome) 페이지를 통해서 곧 확인할 수 있을 예정이니 좀 더 기다려 주세요!

{% include advertisements.html %}

아마도 아래의 효과가 있을 것이라고 기대되요.

- **Vue.js 를 구성하는 큰 틀**에 대한 이해
- **기존 Front-End 지식**을 이용한 빠른 이해

저 코마는 항상 여러분들과 같이 실력을 향상하기 위해 많은 고민을 하고 있다는 점을 기억해주세요! 😄

{% include advertisements.html %}

## Introduction 하위 목차

Vue 공식 문서는 아래의 목차를 통해 Vue 를 소개하고 있습니다. 이 중에는 Declarative Rendering, Composing with Components 그리고 Relation to Custom Elements 와 같은 항목들이 있는데요. 지금부터 의미와 개념을 알아보도록 하겠습니다.

**이번 시간에는 사용자 입력 처리(Handling User Input)까지 진행하도록 하겠습니다.** 만약, 컴포넌트와 사용자 정의 엘리먼트(태그)의 관계를 알고싶은 분들은 Part 2 를 꼭 참고해주세요!

- Introduciton 의 하위 목차
  - **[What is Vue.js](#what-is-vuejs)**
  - **[Getting Started](#getting-started)**
  - **[Declarative Rendering](#declarative-rendering)**
  - **[Conditionals and Loops](#conditionals-and-loops)**
  - **[Handling User Input](#handling-user-input)**
  - [Composing with Components](#composing-with-components)
    - [Relation to Custom Elements](#relation-to-custom-elements)
  - [Ready for More?](#ready-for-More)

## What is Vue.js

Vue 는 상당히 진보적인 프레임워크라고 소개하고 있어요. 왜 진보적라고 이들은 주장할까요? 그것은 바로 기존의 단일형(Monolithic) 프레임워크와 비교하였을 때 진보적이라고 표현하는 것입니다. 

Vue 의 특징은 아래와 같다고 소개하고 있습니다.

- Vue 는 처음부터 끝까지 점진적으로 채택 가능하도록 설계
- Vue 의 코어 라이브러리는 View 레이어에 오로지 집중하여 다른 라이브러리와 통합하거나 기존의 프로젝트에 결합하기에 매우 용이
- Vue 는 SPA(Single-Page-Applications)를 구현하는데 최적화된 라이브러리


## React 와의 유사성

SPA 를 지원하며 코어 라이브러리에 집중하는 유사한 라이브러리로 React 가 있어요. Vue 는 React 와 아래의 유사점을 가지고 있어요.

- 가상의 DOM 을 이용
- 반응성과 구성 용이성(composable)을 갖춘 View 컴포넌트를 제공
- 핵심 라이브러리에 집중하며 라우팅과 전역 상태 관리는 에코시스템의 라이브러리에 의존


## Getting Started

> ℹ **안내**: 공식 문서에서는 Vue 를 시작하기 전에 HTML, CSS, Javascript 등에 대한 중간 정도 지식을 가져야 한다고 말합니다. 그러나 저 코마의 생각은 다릅니다. codepen, jsfiddle, scrimba 등의 도움을 받으면 여러분은 매우 효과적으로 Vue(뷰)를 배울 수 있습니다.

여러분은 index.html 파일을 만들어 단순히 아래와 같이 스크립트 태그를 추가하는 것만으로도 어디서든 Vue 를 즐길 수 있습니다.

또한, Vue 는 개발 버전과 사용 버전을 나누어 관리하고 있어 소스경로만 바꾼다면 곧바로 데이터를 추가하고 관리할 수 있습니다.

```html
<!-- 도움되는 콘솔 경고를 포함한 개발 버전 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

```html
<!-- 상용버전, 속도와 용량이 최적화됨. -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

## Declarative Rendering

이른바 선언적 렌더링이라고 해석되는 Vue 의 핵심 기능입니다. 아래의 내용을 한 봐볼까요? 

선언적 렌더링은 템플릿 구문을 이용하여 데이터를 DOM 에 렌더링하는 것이라고 요약할 수 있습니다. 아래의 예는 문자열 데이터를 렌더링하여 보여줍니다. 그렇다면 텍스트 보간 이외에 속성에도 이러한 작업이 가능할까요?

<!-- https://codepen.io/code-machina/pen/oNvwOGV -->

<pre class="codepen" data-height="184" data-type="html,result" data-href="oNvwOGV" data-user="code-machina" data-safe="true"></pre><br>


Vue 는 DOM 과 데이터를 연결하여 모든 것을 반응형으로 만들어줍니다. 이제 엘리먼트 속성에 데이터를 연결해 보겠습니다. `v-bind:title="message"` 라는 코드에 집중해주세요.

아래의 코드펜 샘플은 span 태그 위에 마우스를 올리면 시간 정보를 출력하게됩니다. 

여기서 새로운 키워드가 나타났네요.`v-bind` 라는 속성을 지정해 주었습니다. 이를 Vue 문서는 **디렉티브**라고 부릅니다.

그렇다면 우리는 DOM 에 특수한 반응형 동작을 정의할 때 **디렉티브**라는 것을 사용해야 함을 유추할 수 있습니다.

<!-- https://codepen.io/code-machina/pen/KKPqYry -->

<pre class="codepen" data-height="230" data-type="html,result" data-href="KKPqYry" data-user="code-machina" data-safe="true"></pre><br>

## Conditionals and Loops

DOM 에 반응형을 정의할 수 있다고 위에서 언급한 바 있습니다. 그러나 과연 그것 뿐일까요? 조건문과 루프의 개념을 구현할 수 있습니다. 이는 liquid, jinja2 와도 같은 기능입니다. 그러나 Vue 는 이를 
엘리먼트의 속성으로 지정하여 제어할 수 있다는 점이 특별합니다. (가상 DOM 의 위력이라고 할까요?)

그렇다면 간단한 구문을 살펴보겠습니다. 

아래의 버튼을 클릭해 볼까요? 버튼 클릭 때마다 `seen` 데이터의 값이 `true` 또는 `false` 로 토글되도록 메서드를 작성하였습니다.

이제 여러분은 사용자 액션에 따른 선택적 렌더링의 방법을 조건(Conditionals) 디렉티브 `v-if` 를 통해 구현할 수 있음을 알게되었습니다. (참 쉽고 재밌는 Vue 입니다. 😍)

<!-- https://codepen.io/code-machina/pen/MWgoNjv -->

<pre class="codepen" data-height="230" data-type="html,result" data-href="MWgoNjv" data-user="code-machina" data-safe="true"></pre><br>

그렇다면, 루프를 알아볼까요? 루프는 배열 데이터를 어떻게 보여줘야 할지를 알려주는 해결책입니다. 이때는 `v-for` 디렉티브를 사용합니다. 아래의 코드펜에서 js 탭을 눌러서 배열을 선언하는 방법과 html 탭에서 v-for 을 통해 배열을 출력하는 간단한 예제를 참고해주세요!

<br>
> 단어하나에도 의미를 부여할 경우 무한한 가능성이 열립니다. 여러분들도 코마와 함께 여러분들의 가능성을 넓히는 계기가 되었으면 합니다.
<br>

<!-- https://codepen.io/code-machina/pen/MWgoNmE -->

<pre class="codepen" data-height="430" data-type="result" data-href="MWgoNmE" data-user="code-machina" data-safe="true"></pre><br>


## Handling User Input

Vue 는 사용자가 앱과 상호작용 할 수 있도록 도와줍니다. `v-on` 디렉티브를 사용하면 javascript 함수를 이벤트와 바인당할 수 있어요! 😍 (정말 멋지지 않나요!) HTML 제공하는 사용자 입력 유형은 매우 다양합니다. 그런데, 과연 Vue 가 이 모든 액션을 다 지원하는지는 사실 의문이 드는 점이네요! 나중에 시간을 내어서 알아보도록 할게요. 지금은 사용자 입력 핸들링(Handling User Input)만 집중해주세요!


<!-- https://codepen.io/code-machina/pen/gOYxOPK -->

<pre class="codepen" data-height="330" data-type="result" data-href="gOYxOPK" data-user="code-machina" data-safe="true"></pre><br>

# 마무리

벌써 Vue.js 공식 문서 정복(뽀개기) 과정에서 Introduction 영역의 70%를 완료하였네요!👏 (짝짝짝) 아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

# 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
