---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 6)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: class 와 style 속성 바인딩"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-19 00:00:00 +0900
categories: [ "vue.js", "node.js", "computed", "watchers", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 지난 시간에 이어서 HTML 엘리먼트의 class 와 style 어트리뷰트를 바인당하는 방법을 소개해 드리겠습니다. 😊 지난 시간에 배운 내용 중 v-bind 를 통해 href 와 같은 어트리뷰트에 인스턴스의 데이터를 바인딩하여 동적으로 링크 경로를 변경할 수 있다고 하였는데요. 문자열 조작은 사실 귀찮고 에러가 발생할 가능성이 있습니다. 이러한 이유로 vue.js 는 class 나 style 어트리뷰트에 대해 특별한 기능을 제공하게 되었습니다. 오늘은 그 사용법을 알아보도록 하겠습니다. 👏👏👏 만약 이전 시간의 내용이 기억나지 않는다면 본문 내 참조된 이전 시리즈들의 링크들을 참고해주세요. 자 그럼 시작해볼까요? 😺
 
<!--more-->

## 소개

엘리먼트의 class 와 인라인 style 을 조작하기 위해 데이터 바인딩을 흔히 사용합니다. 그러나 문자열을 다루다 보면 귀찮고 에러가 발생할 가능성이 있습니다. 따라서, Vue 는 이를 위한 특별한 기능을 제공하는데요. 즉, 이 과정을 통해 안정적으로 클래스와 스타일을 조작하는 방법을 배울 수 있습니다. 이전 과정이 기억나지 않는 분들은 이전 글을 참고해주세요.


- **[쉽게 다가가는 Vue.js 이야기 (파트5)]({% link _posts/2019/09/2019-09-18-Vue-Story-Part-5.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트4)]({% link _posts/2019/09/2019-09-06-Vue-Story-Part-4.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트3)]({% link _posts/2019/09/2019-09-01-Vue-Story-Part-3.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트2)]({% link _posts/2019/08/2019-08-31-Vue-Story-Part-2.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트1)]({% link _posts/2019/08/2019-08-29-Vue-Story-Part-1.markdown %})**

<br>
{% include advertisements.html %}
<br>

## Class Binding: 객체 문법

Vue 는 객체 자체를 class 에 전달하여 클래스의 토글이 가능하게 합니다. 아래의 예제 코드를 살펴볼까요?

{% raw %}
```html
<div v-bind:class="{ active: isActive }"></div>
```
{% endraw %}

`active` 는 실제 클래스에 추가될 문자열입니다. 그리고 `isActive` 는 이 문자열을 토글하는 역할을 합니다. 아마도, 코마의 Vue.js 관련된 글을 읽어오신 분이라면 당연히 그렇겠지라는 생각을 하실지도 모릅니다. 그러나 보통은 `data: function(){ return { active: "active"}}` 라고 정의해두고 `v-bind:class="{ active }"` 라고 쓰는 것이 정상입니다. 즉, 불린값을 통해서 값 표현 여부를 지정할 수 없는 셈입니다.

또한, 이러한 코드 구성은 다수의 필드에 대해서도 잘 작동합니다. 아래의 코드가 동작한다면 hasError 의 값이 false 이므로 active 라는 필드만 클래스에 남게됩니다. 때에 따라 에러가 발생하는 경우 hasError 를 true 로 변경하여 오류 메시지를 출력할 수 있습니다.

{% raw %}
```html
<div v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
```

```js
data: {
  isActive: true,
  hasError: false
}
```
{% endraw %}

또한, 부가적인 class 를 정의할 때 아래의 코드와 같이 구현할 수 있습니다. 기존의 static 클래스를 지정한 뒤에 동적으로 클래스를 정의하는 경우 아래의 구현은 매우 효율적입니다. 

{% raw %}
```html
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

```js
data: {
  isActive: true,
  hasError: false
}
```

위의 코드는 아래와 같이 렌더링됩니다. 즉, 구현을 통해 여러 개의 필드를 자유 자재로 토글할 수 있음을 의미합니다.

```html
<div class="static active"></div>
```

즉, hasError 가 true 로 변경된다면, 아래와 같이 렌더링됨을 의믜합니다.

```html
<!-- rendered html -->
<div class="static active text-danger"></div>
```

{% endraw %}

### computed 속성

우리는 method 대신 computed 속성을 통해서 캐싱을 이용한 효율적인 렌더링 전략을 구현할 수 있음을 이전 시간에 배웠습니다. 이전 시간의 내용이 기억나지 않는다면 아래의 링크를 참조해주세요.

- **[쉽게 다가가는 Vue.js 이야기 (파트5)]({% link _posts/2019/09/2019-09-18-Vue-Story-Part-5.markdown %})** 

객체 문법(object syntax) 또한 아래의 샘플 패턴처럼 풀어낼 수 있습니다. 그리고 아래의 코드의 효과는 코드를 분리하여 HTML 템플릿 영역을 좀 더 깔끔하게 표현할 수 있다는 점입니다.

```html
<div v-bind:class="classObject"></div>
```

```js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

<br>
{% include advertisements.html %}
<br>

## Class Binding: 배열 문법

마치 배열처럼 data 속성에 정의된 변수들을 지정할 수 있습니다.

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

```js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

**렌더링된 코드는 아래와 같습니다.** 즉, activeClass 변수가 실제 문자열 데이터로 치환되는 원리입니다. 이러한 용법은 아래와 같은 이점이 있습니다.

- `'text-danger'` 와 같은 문자열 데이터를 클래스에 추가할 때 '(single quote)를 쓸 필요가 없습니다.
- 여러 개의 클래스 이름을 정의해둔 변수를 대입하여 사용하기에 편합니다. 
- 삼항 연산자(ternary expression, `condition ? exprIfTrue : exprIfFalse` ) 를 사용할 수 있습니다.

```html
<!-- rendered html -->
<div class="active text-danger"></div>
```

### 배열 문법 : 삼항 연산자 예

배열의 중간에 삼항 연산자를 통해서 선택적으로 클래스를 정의할 수 있습니다. 만약, `isActive` 변수가 true 라면 클래스에는 `active` 가 추가될 것입니다.

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

### 배열 문법 + 객체 문법

배열의 인자로 객체 문법을 사용할 수 있습니다. 조금은 지저분할 수 있지만, 이는 그만큼 Vue가 유연함을 의미합니다. 

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

<br>
{% include advertisements.html %}
<br>

## Class Binding: 컴포넌트를 이용

사용자 정의 컴포넌트에 class Attribute 를 사용하는 것을 생각해 볼까요? 아래의 규칙에 따라 클래스가 등록됩니다.

- 클래스들은 컴포넌트의 root 엘리먼트에 추가됩니다.
  - 만약 컴포넌트들은 `div` 엘리먼트로 감싸는 습관이 있으시다면 유의해야 할 것입니다.
- 이미 등록된 클래스들은 덮어씌어지지 않습니다.

예제를 통해서 이해하면 더욱 쉽습니다. 아래의 샘플 코드를 참고해볼까요?

```js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

아래와 같이 class 를 추가로 등록합니다.

```html
<my-component class="baz boo"></my-component>
```

그리고, 렌더링된 결과는 아래와 같겠지요.

```html
<!-- rendered html -->
<p class="foo bar baz boo">Hi</p>
```

이러한 유형의 사용법은 객체 문법을 통해서도 가능합니다.

```html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

위의 코드는 아래와 같이 렌더링됩니다.

```html
<!-- rendered html -->
<p class="foo bar active">Hi</p>
```

<br>
{% include advertisements.html %}
<br>

## Binding Style : 객체 문법

class attribute 와 style attribute 에 대한 바인딩은 미세한 차이점이 있습니다. 아래의 예제를 살펴볼까요? 

`Binding class : 객체 문법`에서는 데이터의 true/false 값을 통해서 클래스 문자열이 정의되었습니다. 즉, 데이터가 클래스 이름 문자열 데이터를 가지지 않은 셈입니다. 

그러나, style attribute 는 사정이 다릅니다. color 키워드에 들어갈 수 있는 요소들은 문자열로 정의됩니다.  Vue 의 구현상에도 이러한 차이점이 반영되어야 합니다. 즉, `color: read` 이며 `font-size: 30px` 인 스타일을 정의할 때는 아래와 같이 정의합니다.

`fontSize`, `font-size` 와 같은 이름 명명 규칙을 각각 카멜케이스(camelCase) 그리고 케밥케이스(kebab-case) 라고 부릅니다. 그리고 두 가지 이름 규칙은 서로 혼용됩니다. (이는 가상 DOM 을 구현하는 Vue 가 두개의 네이밍 컨벤션을 변경하는 컨버터가 있음을 의미합니다.)


```html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

객체 문법의 소개의 마지막으로 아래와 같은 스타일의 문법을 사용할 수도 있습니다.

```html
<div v-bind:style="styleObject"></div>
```

```js
data: {
  styleObject: {
    color: 'red',
    fontSize: '30px'
  }
}
```

위의 두 구현은 동치(same)입니다. 저는 이러한 스타일을 개취(개인의 취향)이라고 생각합니다. 잘 판단하셔서 적절하게 사용하시길 권해드립니다. 또한 첨언으로 computed 속성을 통해서 위의 구현을 사용할 수 있으니 캐싱 전략을 수립하고 싶은 개발자 분들에게 추천드립니다.

<br>
{% include advertisements.html %}
<br>

## Binding style : 배열 문법

아래와 같이 배열 문법을 통해서 객체를 스타일에 중복하여 등록할 수 있습니다.

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

```js
data: {
  baseStyles: {
    color: 'red',
    fontSize: '13px'
  }, 
  overridingStyles: {
    color: 'blue',
    fontSize: '21px'
  }
}
```

<br>
{% include advertisements.html %}
<br>

## Binding Style : 다중 벨류

2.3.0 버전 이상에서 지원하는 내용입니다. 아래의 예를 살펴볼까요? 

flex, -ms-flexbox 등의 속성을 정의되어 있습니다. 이는 브라우저의 지원 여부에 따라 다르게 적용되게 됩니다. 만약 렌더되는 브라우저가 flex 를 지원한다면 `display: flex` 가 될 것입니다.

```html
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

<br>
{% include advertisements.html %}
<br>

## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 class 와 style 속성 바인딩 영역이 완료되었습니다! 👏 (짝짝짝) 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

다음 시간에는 엘리먼트 어트리뷰트 중 **조건부 렌더링**을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
