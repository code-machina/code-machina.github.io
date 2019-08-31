---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 2)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: Introduction 편"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-08-31 00:00:00 +0900
categories: [ "javascript", "vue.js", "introduction", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 지난 시간에 이어서 나머지 내용을 정리해 볼까합니다. 이 글을 통해서 여러분들은 Vue.js 의 Introduction 을 완벽히 이해하실 수 있어요! 이전 시간의 내용이 기억나지 않는다면 **[쉽게 다가가는 Vue.js 이야기 (파트1)]({% link _posts/2019/08/2019-08-29-Vue-Story-Part-1.markdown %})** 를 다시 복습해 주세요! 😺

<!--more-->

## 이어서

이전 시간에 여러분들은 코마와 함께 아래의 내용들을 이미 학습해 보았습니다. 오늘은 컴포넌트에 대해 간략히 알아보도록 하겠습니다. 이 과정을 읽으신다면 이제 여러분은 명실공히 Vue.js 의 기본적인 내용을 완벽히 습득하신 것입니다. 

- Vue.js 란 무엇인가?
- 개발용/배포용 Vue.js CND 라이브러리 임포트하기
- 선언적 렌더링(Declarative Rendering)
- 조건문과 루프(Conditionals and Loops)
- 사용자 입력 처리 (Handling User Input)

{% include advertisements.html %}

## 이번 시간의 내용

이번 시간에는 Introduction 하위 목차 중에서 `Composing with Components` 등을 다룰 예정입니다. ~~취소선(Cancel line)~~으로 표현되어 있는 부분은 **[쉽게 다가가는 Vue.js 이야기 (파트1)]({% link _posts/2019/08/2019-08-29-Vue-Story-Part-1.markdown %})** 에서 언급한 내용이니 참고해 주세요 ~ 

- Introduciton 의 하위 목차
  - *~~What is Vue.js~~*
  - ~~*Getting Started*~~
  - *~~Declarative Rendering~~*
  - *~~Conditionals and Loops~~*
  - *~~Handling User Input~~*
  - **[Composing with Components](#composing-with-components)**
    - **[Relation to Custom Elements](#relation-to-custom-elements)**
  - **[Ready for More?](#ready-for-More)**

{% include advertisements.html %}

## 이번 시간을 통해서

아마도 아래의 효과가 있을 것이라고 기대되요.

- **컴포넌트의 이해**를 통한 대규모 응용 프로그램 구축 역량
- 기존의 컴포넌트를 재사용하는 역량

저 코마는 항상 여러분들과 같이 실력을 향상하기 위해 많은 고민을 하고 있다는 점을 기억해주세요! 😄

{% include advertisements.html %}

## Composing with Components

Vue.js 가 제공하는 컴포넌트 시스템은 큰 규모의 어플리케이션을 작성할 때 매우 중요한 개념입니다. 예를 들어 설명해 볼까요? 

예전에 Template 엔진을 통해서 HTML 코드를 작성 한뒤에 임베드하는 방식을 취하거나 코드 스니핏을 일일이 별도로 보관한 뒤에 필요할 떄 복사여 붙여넣는 작업 방식을 차용하였을지도 모릅니다. 

그러나 Vue.js 는 역할/재사용성 등을 고려하여 개발자가 최대한 작업을 편하게 하기 위해 컴포넌트화 하여 불러오는 것이 가능합니다. 

테이블 예제를 생각해 볼까요? 우리는 `페이지 넘기기`, `검색하기`, `동적으로 필드 정의하기` 등의 기능을 부수적으로 제공하는 컴포넌트를 구상할 수 있습니다. 앞으로는 이를 `FeaturefulTableComp.vue` 와 같은 컴포넌트로 작성하여 아래와 같이 활용할 수 있게 됩니다.

<script src="https://gist.github.com/code-machina/5f4393dcefd60aca09cd83ea45d3dbd3.js"></script>


이제 여러분들도 코마처럼 아래의 예제를 통해서 컴포넌트를 자유자재로 다룰 수 있어요! 🙋
`Vue.component` 구문을 통해서 새로운 컴포넌트를 정의하고 사용해 볼까요?

`todo-item` 이라는 컴포넌트를 아래와 같이 정의해줍니다.

```javascript
// todo-item 이름을 가진 컴포넌트를 정의합니다
Vue.component('todo-item', {
  template: '<li>모두가 행복한 사회가 되었으면 합니다.</li>'
})
```

> ℹ  **TIP**: `ol` 은 `ordered list` 의 줄임말(acronym)입니다. 반대로 `ul` 은 `un-ordered list` 를 말합니다. 즉, 순서가 있는 목록, 순서가 필요없는 목록을 나타낼 때 사용합니다.

우리는 위에서 정의한 `todo-item` 컴포넌트를 마치 원래 있었던 element 처럼 사용할 수 있습니다. 어때요 멋지지 않은가요? 사용자 정의 클래스를 선언하고 사용하는 방식처럼 느껴집니다. 

심지어 속성(attribute)를 바인딩하여 사용자 정의 동작 혹은 꾸미기를 할 수 있어 동적인 속성을 강화할 수 있습니다.

```html
<ol>
  <!-- todo-item 컴포넌트의 인스턴스 만들기 -->
  <todo-item></todo-item>
</ol>
```

이제 todo 앱을 살펴보도록 하겠습니다. `v-for` 디렉티브를 이용하여 `todo-item` 컴포넌트가 `groceryList` 배열의 데이터를 출력하도록 우선 선언해줍니다.

그리고 다음으로 `v-bind` 디렉티브를 통해서 `todo-item` 에서 정의한 todo 속성과 배열의 데이터를 바인딩 해줍니다.

자 그럼 결과를 살펴볼까요?

<!-- https://codepen.io/code-machina/pen/rNBzGPX -->

<pre class="codepen" data-height="484" data-type="html,result" data-href="rNBzGPX" data-user="code-machina" data-safe="true"></pre><br>

이제 여러분은 손쉽게 컴포넌트를 분할하여 복잡한 앱의 구조를 단순화 시킬 수 있게 되었습니다. 

{% include advertisements.html %}

## Relation to Custom Elements

W3 콘소시움을 아나요? 웹 표준을 정의하는 기관입니다. 아마도 여러분들은 사용자 정의 엘리먼트를 컴포넌트를 공부하면서 떠올렸을 수 있어요. 이것은 매우 좋은 발상입니다.

하지만, 아직은 `Web Components Spec` 은 초안단계에 머물러 있습니다. 그러나 Vue 컴포넌트는 모든 브라우저에서 지원됩니다. 더불어 Vue 컴포넌트는 데이터 흐름, 사용자 지정 이벤트 통신 및 빌드 도구 통합 등을 활용할 수 있기에 더욱더 의미가 큽니다.


## Ready for More?

지금까지 여러분은 Vue 의 기본 기능을 모두 이해하게 되었습니다. 어떤까요? 멋지고 새롭지 않나요? 여러분들의 머리속에서 Vue 를 활용하여 새로운 서비스를 구현할 계획을 그리고 있다면 코마의 바램이 이루어진 것이네요!


## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 Introduction 영역이 완료되었습니다! 👏 (짝짝짝) 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

{% include advertisements.html %}

다음 시간에는 `Vue 인스턴스` 와 `템플릿 문법`을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
