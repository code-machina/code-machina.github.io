---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 4)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: Vue 템플릿"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-01 00:00:00 +0900
categories: [ "javascript", "vue.js", "template", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 지난 시간에 이어서 Vue 인스턴스에 대해서 정리해보도록 할께요 😊. 이번 과정을 통해서 Vue 인스턴스의 개념을 완벽히 이해하실 수 있어요! 만약 이전 시간의 내용이 기억나지 않는다면 아래의 링크들을 참고해주세요. 자 그럼 시작해볼까요? 😺

- **[쉽게 다가가는 Vue.js 이야기 (파트2)]({% link _posts/2019/09/2019-09-01-Vue-Story-Part-3.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트2)]({% link _posts/2019/08/2019-08-31-Vue-Story-Part-2.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트1)]({% link _posts/2019/08/2019-08-29-Vue-Story-Part-1.markdown %})**
 
<!--more-->

## 템플릿 문법이란

Vue.js(이하, 뷰)는 HTML 기반의 템플릿 문법을 제공합니다. 이를통해 여러분은 선언적으로 렌더링된 DOM 을 뷰의 인스턴스 데이터와 결합할 수 있습니다. 모든 Vue.js 템플릿들은 유효한 HTML 입니다. 따라서, Spec 을 준수하는 브라우저들이나 HTML 파서들을 통해서 파싱될 수 있습니다.

Vue 내부에서는 템플릿을 가상 DOM 렌더함수로 컴파일합니다. 앱의 상태가 변할 때, 반응성 시스템(reactivity system)과 결합하여 뷰는 지능적으로 재 랜더링(re-rendering)할 컴포넌트들의 최소 숫자를 파악하고 최소한의 크기의 DOM 을 조작합니다. 

가상 DOM 개념에 친숙하다면 그리고 Javascript 의 본래의 스타일을(raw power) 선호한다면, 여러분이 직접 render 함수를 작성하는 것도 방법입니다.

## 보간(interpolation)

- 문자열(Text)

데이터 바인딩을 위한 기본적인 폼은 텍스트 보간입니다. `Mustache 문법`을 한번 살펴볼까요?

> Mustache 는 콧수염이라는 의미입니다. 즉, `\{\{ \}\}` 가 콧수염을 닮았다는 점에서 착안된 이름입니다.

<!-- 텍스트 보간을 코드로 표현하기 위해 아래와 같이 {% raw %} /* ... */ {% endraw %} 와 같은 구문이 필요하다. -->

{% raw %}
```html
<span>메시지: {{ msg }}</span>
```
{% endraw %}

위의 예에서 mustache 태그는 `msg` 라는 data 객체의 속성의 값으로 치환될 것입니다. 

만약 여러분이 오직 한번의 보간을 하고 싶다면 v-once 디렉티브를 사용하면 됩니다. 그러나 이러한 설정은 해당 엘리먼트 하위에 모두 종속되므로 주의해주세요!

{% raw %}
```html
<span v-once>결코 변하지 않을 것입니다 : {{ msg }}</span>
```
{% endraw %}

## HTML 보간

mustaches 문법은 데이터를 단순 텍스트로만 처리할 뿐입니다. 즉, HTML 코드를 입력하여도 텍스트 입니다. 실제 HTML 을 출력하기 위해서는 v-html 이라는 디렉티브를 사용해야만 합니다.

{% raw %}
```html
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```
{% endraw %}

> ⚠ **경고**: 사용자 입력값으로부터 임의 HTML 렌더링은 XSS 취약점을 통해 위험할 수 있습니다. 사용자가 제공한 값이 아닌 신뢰할 수 있는 컨텐츠에 대해서만 HTML 보간을 사용해주세요.

span 의 컨텐츠는 rawHtml 프로퍼티 값을 통해 치환될 수 있으며 일반 HTML로 해석됩니다. v-html 을 통해서 template 을 구성할 수 없음에 주의해 주세요. 뷰(vue)는 문자열 기반의 템플릿 엔진이 아닙니다. 대신에 컴포넌츠들을 이용하세요.

## Attribute 보간

Mustache 를 HTML 속성(Attribute)에서 사용할 수 없습니다. 대신, v-bind 디렉티브를 이용해아 합니다.

```html
<div v-bind:id="dynamicId"></div>
```

boolean 속성을 바인딩할 경우 아래와 같이 지정해주세요.

```html
<button v-bind:disabled="isButtonDisabled">Button</button>
```

만약, `isButtonDisabled` 가 null 이거나 undefined 또는 false 라면 disabled 속성은 button 엘리먼트에 포함되지 않을 것입니다.

## 자바스크립트 지원 사항

지금까지 단순한 프로퍼티 키 값을 템플리에 바인딩 해왔습니다. 그러나 뷰(vue)는 자바스크립트 표현을 모두 지원합니다.

{% raw %}
```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```
{% endraw %}

이러한 자바스크립트 표현식들은 뷰(vue) 인스턴스 데이터 범위 내에서 평가 받는다. 

{% raw %}
```html
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```
{% endraw %}

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
