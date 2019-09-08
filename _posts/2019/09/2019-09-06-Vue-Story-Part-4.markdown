---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 4)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: Vue 템플릿"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-06 00:00:00 +0900
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

<br>
{% include advertisements.html %}
<br>

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

<br>
{% include advertisements.html %}
<br>

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

<br>
{% include advertisements.html %}
<br>

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

<br>
{% include advertisements.html %}
<br>

이러한 자바스크립트 표현식들은 뷰(vue) 인스턴스의 data 속성 내에서 평가 받습니다. (즉, data 속성에 정의된 properties 들이 해석 범위에 포함된다.) 그러나, 모든 구문이 적용되는 것은 아닙니다. 아래의 예제는 작성할 수 없는 구문의 예들입니다.

{% raw %}
```html
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```
{% endraw %}

## 디렉티브(Diretives)

이전 시간부터 여러가지 디렉티브들이 언급되어 왔습니다. 보통 `v-` 가 접두어로 붙는 특별한 속성들입니다. 이러한 디렉티브들의 값으로는 인라인 자바스크립트 구문을 작성할 수 있습니다.

여러분이 기억해야할 단 한가지는 디렉티브는 상태가 변할 때 이를 반응적으로 DOM 에 반영하는 것입니다. 아래의 `v-if` 디렉티브는 `seen` 의 값에 따라 `<p>` 엘리먼트를 삽입 혹은 삭제합니다.

<br>
{% include advertisements.html %}
<br>

```html
<p v-if="seen">Now you see me</p>
```

## 아규먼트(Arguments)

`v-bind` 와 같은 몇몇 디렉티브들은 "인자"를 받을 수 있습니다. 이 말은 즉슨 HTML 의 속성에 값을 그대로 대입하는 것을 의미합니다.

아래의 예는 엘리먼트의 href 속성으로 하여금 url 의 값을 바인딩하도록 하는 것입니다. 따라서, 아래의 `<a>` 는 url 의 값에 따라 링크 클릭 시 이동하는 위치를 제어할 수 있습니다.

> 여기서 href 가 아규먼트(arguments)에 해당함에 유의해주세요.

```html
<a v-bind:href="url"> ... </a>
```

<br>
{% include advertisements.html %}
<br>

또 하나의 예로는 `v-on` 디렉티브가 있습니다. DOM의 click 이벤트를 리스닝합니다.

```html
<a v-on:click="doSomething"> ... </a>
```

## 동적 아규먼트(Dynamic Arguments)

> 2.6.0 버전 이상에서 새롭게 적용된 개념입니다.

<br>
{% include advertisements.html %}
<br>

자바스크립트 구문이 디렉티브의 인자에도 적용될 수 있음을 의미합니다. 단, 사용하기 위해 각진 괄호([])로 감싸야 합니다.

아래의 예에서 attributeName 은 Vue 인스턴스에서 정의된 구문입니다. 그리고 각진 괄호를 통해 반영되었습니다. 만약, `attributeName` 이 `href` 문자열로 지정되었다면, 위에서 언급한 예와 동일한 소스가 됩니다.

```html
<!--
Note that there are some constraints to the argument expression, as explained
in the "Dynamic Argument Expression Constraints" section below.
-->
<a v-bind:[attributeName]="url"> ... </a>
```

### 동적 아규먼트 값의 제약사항(constraints)

동적 아규먼트, 즉 attributeName,의 값은 문자열로 평가되기를 기대합니다. 만약, null 값이 대신한다면 명시적으로 바인딩을 제거한다는 의미입니다. 이 외의 다른 타입들은 경고를 출력하게됩니다.

1. 동적 아규먼트에는 문자열 값만을 대입
2. 바인딩을 제거할 경우 null 값을 입력

### 동적 아규먼트 구문의 제약사항(constraints)

동적 아규먼트 구문에는 몇 가지 문법적 제약이 있습니다. 예를들어 `공백`, `싱글쿼터` 문자를 삽입할 경우 에러를 출력하게됩니다.

즉, 아래의 구문은 사용해서는 안됩니다.

```html
<!-- This will trigger a compiler warning. -->
<a v-bind:['foo' + bar]="value"> ... </a>
```

만약, 복잡한 표현식을 사용할 때는 어떻게 해야할 까요? 바로 computed 속성을 사용하는 것입니다. 아래의 샘플을 참조해주세요.

```javascript
computed: {
  foo_bar: function () {
    return this.foo + this.bar;
  }
}
```

<br>
{% include advertisements.html %}
<br>

이제 우리는 위의 잘못된 구문을 변경할 수 있습니다.

```html
<a v-bind:[foo_bar]="value"> ... </a>
```

in-DOM 템플릿에서는 대문자 사용에 주의가 필요합니다. 즉, 브라우저에 의해서 속성의 이름이 소문자로 변경되는 케이스가 있습니다. 아래의 구문은 에러가 발생할 수 있습니다.

여기서 in-DOM 템플릿은 HTML 파일내에 템플릿을 직접 작성한 경우입니다. index.html 파일 내에 vue.js 링크를 추가하고 곧바로 개발하는 경우입니다.

```html
<!--
This will be converted to v-bind:[someattr] in in-DOM templates.
Unless you have a "someattr" property in your instance, your code won't work.
-->
<a v-bind:[someAttr]="value"> ... </a>
```

## 수식어(Modifiers)

수식어는 디렉티브에 대한 특수 접미사입니다. 여러분이 코드를 보다 보면 `v-on:submit.prevent` 라는 구문을 보게 될 것입니다. 이는 트리거된 이벤트에서 `event.preventDefault()` 를 호출하도록 알려주는 역할입니다.

<br>
{% include advertisements.html %}
<br>

```html
<form v-on:submit.prevent="onSubmit"> ... </form>
```

## 약어(Shorthands)

v-on 과 v-bind 등을 반복해서 적는 것은 왠지 효율성이 떨어집니다. 이를 위해 이를 대신하는 표현이 있습니다. 아래의 예를 살펴볼까요?

- v-bind:href 는 :href 로 줄일 수 있습니다.

```html
<!-- 전체 문법 -->
<a v-bind:href="url"> ... </a>

<!-- 약어 -->
<a :href="url"> ... </a>
```

<br>
{% include advertisements.html %}
<br>

- v-on:click 은 @click 으로 줄일 수 있습니다.

```html
<!-- 전체 문법 -->
<a v-on:click="doSomething"> ... </a>

<!-- 약어 -->
<a @click="doSomething"> ... </a>
```

## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 Vue 템플릿 문법 영역이 완료되었습니다! 👏 (짝짝짝) 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

<br>
{% include advertisements.html %}
<br>

다음 시간에는 `computed` 와 `watch`를 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
