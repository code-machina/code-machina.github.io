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

## 객체 문법

Vue 는 객체 자체를 class 에 전달하여 클래스의 토글이 가능하게 합니다. 아래의 예제 코드를 살펴볼까요?

{% raw %}
```html
<div v-bind:class="{ active: isActive }"></div>
```
{% endraw %}

`active` 는 실제 클래스에 추가될 문자열입니다. 그리고 `isActive` 는 이 문자열을 토글하는 역할을 합니다. 아마도, 코마의 Vue.js 관련된 글을 읽어오신 분이라면 당연히 그렇겠지라는 생각을 하실지도 모릅니다. 그러나 보통은 `data: function(){ return { active: "active"}}` 라고 정의해두고 `v-bind:class="{ active }"` 라고 쓰는 것이 정상입니다. 즉, 불린값을 통해서 값 표현 여부를 지정할 수 없는 셈입니다.

또한, 이러한 코드 구성은 다수의 필드에 대해서도 잘 작동합니다. 아래의 코드가 동작한다면 hasError 의 값이 false 이므로 active 라는 필드만 클래스에 남게됩니다. 

그리고 우리는 때에 따라 에러가 발생하는 경우 hasError 를 true 로 변경하여 데이터를 표현할 수 있습니다.

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

## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 Computed 와 Watcher 속성 영역이 완료되었습니다! 👏 (짝짝짝) 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

다음 시간에는 엘리먼트 어트리뷰트 중 `class` 와 `style` 바인딩을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
