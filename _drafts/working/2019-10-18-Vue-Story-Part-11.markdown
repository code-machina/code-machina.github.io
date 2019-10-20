---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 11, 마지막)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: 폼 입력 바인딩(Form Input Bindings"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-10-04 00:00:00 +0900
categories: [ "vue.js", "node.js", "Form Input Bindings", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 Vue(뷰)의 폼 입력 바인딩(Form Input Bindings)에 대해 소개해드리도록 하겠습니다. 이번 시간을 통해 Vue 가 제공하는 기능 중 **폼 입력** 을 완벽히 정리해 보도록 하겠습니다. 각종 입력 컨트롤(Select, Checkbox 등)에 대한 v-model 사용법을 이해하고 자유롭게 사용할 수 있습니다. 👏👏👏 자 그럼 시작해볼까요?
 
<!--more-->

## 이어가기

만약 이전 시간의 내용이 기억나지 않는다면 아래에 참조된 이전 시리즈의 링크들을 차분히 다시 살피는 것은 어떨까요? 

- **[쉽게 다가가는 Vue.js 이야기 (파트10)]({% link _posts/2019/10/2019-10-14-Vue-Story-Part-10.markdown %})**
- **[쉽게 다가가는 Vue.js 이야기 (파트9)]({% link _posts/2019/10/2019-10-12-Vue-Story-Part-9.markdown %})**
- **[쉽게 다가가는 Vue.js 이야기 (파트8-2)]({% link _posts/2019/10/2019-10-04-Vue-Story-Part-8-2.markdown %})**
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

## 기본 사용법

v-model 은 양방향 데이터 바인딩을 위해 사용하는 디렉티브(Driectives)라고 이전 시간에 배웠습니다. 그런데, 폼(Form) 입력의 경우에는 어떻게 v-model 을 사용할 수 있을까요? 이번 시간에는 다양한 입력 컨트롤들에 대해 양방향 데이터 연결을 하는 방법을 알아보도록 하겠습니다.

시작하기 전에 Form 입력에 사용되는 컨트롤의 종류를 살펴볼까요?

- Text (인라인 텍스트 입력 컨트롤)
- Multiline Text (텍스트 입력 컨트롤)
- Checkbox (체크 박스 컨트롤)
- Radio (라디오 컨트롤)
- Select (드롭 다운 컨트롤)

### v-model 사용의 장점(Strength)

v-model을 이용하여 위의 컨트롤들과 바인딩할 경우 입력 유형에 따른 엘리먼트 업데이트 방식을 자동으로 선택하는 강점이 있습니다. 즉, 사용자 입력 이벤트에 따라 데이터를 업데이트 하기 위한 필수적인 문법입니다.

### v-model 사용 시 주의점(Caution)

v-model 은 value, checked, selected 와 같은 어트리뷰트들을 깔끔히 무시하고 오로지 Vue 인스턴스의 data 속성을 유일한 신뢰의 원천(**source of truth**)으로 다룹니다. **따라서, 자바스크립트 영역에서 초기 값(initial value)를 선언해야만 합니다.**

### 컨트롤과 v-model

v-model 은 입력 엘리먼트마다 내부에서 다른 속성(properties)를 사용하고 다른 이벤트(event)를 발생시킵니다. 몇 가지 예시를 들어볼까요?

- text 또는 textarea 엘리먼트는 value 속성과 input 이벤트를 사용합니다.
- checkbox 와 radio 버튼은 checked 속성과 change 이벤트를 사용합니다.
- select 필드들은 value 속성과 change 이벤트를 사용합니다.

### CJK 언어와 v-model

CJK (Chinese, Japanese, Korean) 언어를 입력 시 IME (Input Method Editor)를 사용하게 됩니다. IME 란 용어를 처음보시는 분들은 하단의 링크를 참고해주세요.

- [IME 란?](https://en.wikipedia.org/wiki/Input_method)

IME 를 통해서 데이터를 입력할 경우 v-model 은 작성 완료 전까지 업데이트 되지 않습니다. 만약 이러한 동작 방식을 여러분의 구미에 맞게 수정하려면 input 이벤트를 통해 조정이 가능합니다.

## 코드 샘플

이제, v-model 이 동작하는 방식을 알았으니 샘플 코드를 살펴볼까요?

{% raw %}

- text 입력 샘플

```html
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

- textarea 샘플

```html
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

- checkbox 샘플

```html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```

- 다중 체크 박스 샘플

```html
<div id='example-3'>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>
</div>
```

```js
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
```

- 라디오 컨트롤 샘플

```html
<input type="radio" id="one" value="One" v-model="picked">
<label for="one">One</label>
<br>
<input type="radio" id="two" value="Two" v-model="picked">
<label for="two">Two</label>
<br>
<span>Picked: {{ picked }}</span>
```

- Select 컨트롤 샘플

```html
<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<span>Selected: {{ selected }}</span>
```

```js
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```



{% endraw %}

<br>
{% include advertisements.html %}
<br>

## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 이벤트 핸들링이 완료되었습니다! 👏 (짝짝짝) 또한, 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

다음 시간에는 **Vue 스토리의 마지막인 폼 입력 바인딩(Form Input Binding)**을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
