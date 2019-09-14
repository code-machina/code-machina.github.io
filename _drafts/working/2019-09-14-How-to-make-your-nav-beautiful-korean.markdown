---
layout: post
title:  "웹사이트의 네비게이션을 유려하게 만드는 초간단 팁"
subtitle: "SCSS를 활용한 네비게이션바 만들기"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-13 00:00:00 +0900
categories: [ "scss", "css", "navigation", "15m"]
excerpt_separator: <!--more-->
---


안녕하세요 코마입니다. 오늘은 웹 사이트를 좀 더 유려하게 만드는 과정의 일환으로 몇가지 SCSS 트릭과 네이게이션 바를 멋지게 꾸미는 방법을 소개해드리도록 하겠습니다. 여러분은 이 글을 통해 여러분의 사이트 중 상단 영역을 멋지게 꾸밀 수 있는 능력을 갖추게 됩니다. 그럼 출발해 보겠습니다.

<!--more-->

## 소개

저는 프로젝트 과정에서 상용 UI를 사용하고 있는 과정에 나만의 CSS 스타일링을 하면 좀 더 멋지게 UI 를 만들 수 있다는 생각에 이르렀습니다. 지금 진행 중인 프로젝트는 Vue.js 프로젝트인데요. 시간 상 백엔드를 구성해야 하므로 어쩔 수 없이 상용 UI를 구매하게 되었습니다. 😢

그러나 저 코마는 여기에 만족하지 않고 여러분에게 더 나은 정보를 제공하고자 CSS 를 파고들기로 결정하였습니다. 👏👏👏 소개 글에서 SCSS 를 활용한다고 하였는데, 먼저 간단히 SCSS 를 소개해 드리도록 하겠습니다.

## sass 란?

css 는 모든 웹에서 동작합니다. 단, sass, less, stylus 등은 그렇지 않죠. 즉, sass-loader, sass 와 같은 전처리를 수행할 모듈이 필요합니다. 

그럼에도 불구하고 scss 를 사용하는 것일까요? 정답은 언어적인 풍부함입니다. css 는 직관적이지 않고 매우 불편합니다. 중첩, 조건문, 반복문 등을 지원하지 않지요. 즉, 다수의 수작업이 많이드는 개발작업입니다. 그러나 scss 를 사용하게 되면 간단한 컴파일을 통해서 css 작업보다 더 뛰어난 효율성을 발휘할 수 있습니다. 

- **sass 는 전처리(pre-processing)가 필요한 언어입니다.**

## scss 와 sass 의 차이점

scss 는 css 구문과 완전한 호환을 이룹니다. 그리고 sass 의 기능을 모두 지원합니다. 가독성 측면에서 scss 가 더 훌륭합니다. sass 는 들여쓰기 기반으로 중첩을 구분하기 때문에 영역 구분이 어렵습니다.

## 기본 구조 잡기

scss 를 설치하는 방법을 다루지 않겠습니다. 우리는 codepen 을 이용하여 간편하게 구현할 예정입니다. 따라서, 이 글을 읽으시는 분은 단순히 codepen.io에 접속하게 되면 모든 준비가 완료됩니다. 👏👏👏

그렇다면 아래의 코드를 볼까요?

```html
<ul class="top-nav">
  <li class="nav-em">홈</li>
  <li class="nav-em">사이트 소개</li>
  <li class="nav-em active">글</li>
  <li class="nav-em">고객</li>
  <li class="nav-em">연락처</li>
</ul>
```

우리가 위의 코드에서 부여한 규칙은 매우 단순합니다. class 이름을 부여하는 과정을 거쳤을 뿐입니다. 섬세하신 분들은 class 이름 명명에도 다양한 고민을 하였을 것 입니다.

## scss 시작

scss 를 시작하기 위해서 간단한 특징을 잡아가도록 하겠습니다. 우리는 위에서 네비게이션에 들어갈 항목을 5 개로 정의하였습니다. 그리고 각 항목의 갯수에 따라서 메뉴 버튼의 크기를 조정하는 간단한 코드를 작성하였습니다.

```scss
// 네비게이션 항목의 갯수를 정의합니다.
$nav-items: 5;
// li 항목의 정확한 비율을 계산하기 위해서 아래의 수식을 정의합니다.
$li-width: (100/$nav-items) * 1%;

// 색깔은 Adobe Color 휠을 이용해주세요.
$background-color: #121212;
// 여러분의 마우스 포인터가 메뉴를 가리킬 경우, 강조하기 위한 색 설정입니다.
$marker-color: #e82d00;
```

## 스타일링 시작

자 이제 본격적인 스타일링을 시작해 볼까요? 아마 HTML 코드만을 코드 펜에서 본다면 단순한 HTML 리스트 뷰로 보일 것입니다. 이제, 변경해보겠습니다.

`float: left` 에 대해 궁금하신 분들은 아래의 링크를 참조해주세요. 언제나 Mozilla 는 우리의 친구입니다. 

- [Mozilla Docs: CSS/Float](https://developer.mozilla.org/en-US/docs/Web/CSS/float)


```scss
// nav 컨테이너의 css 를 설정합니다.
.top-nav {
  // 각 항목을 구분하는 점표기를 제거합니다.
  list-style: none;
  // margin: 0 auto; 를 통해서 보통 중앙 정렬을 합니다.
  margin: 50px auto; // 50px 는 top 입니다. 
  // 최대 폭을 정의합니다. 
  // 나중에 미디어 쿼리를 통해서 조정하도록 합시다.
  max-width: 720px;
  padding: 0;
  width: 100%;
}

// li 항목에 대한 css 를 정의합니다.
.nav-em {
  background: #fff; // 배경색을 정의합니다.
  display: block; // 블록 요소로 표현합니다.
  float: left; // 
  margin: 0;
  padding: 0; 
  text-align: center; // 문자는 중앙 정렬입니다.
  // 각 li 항목의 폭을 정의합니다.
  width: $li-width;

  // &:first-child 는 첫번째 항목을 지정하는 속성입니다.
  // 즉, 아래의 정의를 통해 네비게이션 바의 귀퉁이가 둥글게 표현됩니다.
  &:first-child {
    border-radius: 3px 0 0 3px;
  }

  &:last-child {
    border-radius: 0 3px 3px 0;
  }

  // 아래의 설정은 li 항목이 active 클래스 속성을 가질 때, 폰트 색을 변경합니다.
  &.active a {
    color: $marker-color;
  }

  a {
    color: $background-color;
    display: block;
    padding-top: 20px;
    padding-bottom: 20px;
    text-decoration: none;

    &:hover {
      color: $marker-color;
    }
  }
}
```

아래의 코드펜 샘플을 한번 살펴볼까요? 메뉴가 가지런히 정렬되어 있습니다. 마우스를 올리면 글씨의 색이 변경됩니다. 

<!-- 

https://codepen.io/code-machina/pen/vYBrRzM 
https://codepen.io/code-machina/pen/VwZddoX -->

<pre class="codepen" data-height="470" data-type="result" data-href="VwZddoX" data-user="code-machina" data-safe="true"></pre>

## 마커 붙이기

이제, 마우스 위치에 따라서 마커를 붙여보도록 하겠습니다. 마커의 형태는 다양하게 만들 수 있습니다. 아래의 코드를 볼까요?

`<ul>` 에 `marker` 라는 클래스 이름을 추가하였습니다. 


```html
<ul class="top-nav marker">
    <li class="nav-em"><a href="/">홈</a></li>
  <li class="nav-em"><a href="/">사이트 소개</a></li>
  <li class="nav-em active"><a href="/">글</a></li>
  <li class="nav-em"><a href="/">고객</a></li>
  <li class="nav-em"><a href="/">연락처</a></li>
</ul>
```

```scss

```


<!-- https://codepen.io/code-machina/pen/eYOKjeW -->


## Basic Structure

First, you have to declare your basic HTML structure for navigating your contents. U know the answer about simple structure. Let's see the below code snippet.


## 









## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 Vue 인스턴스 영역이 완료되었습니다! 👏 (짝짝짝) 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

<br>
{% include advertisements.html %}
<br>

다음 시간에는 `템플릿 문법`을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)

<br>
{% include advertisements.html %}
<br>
