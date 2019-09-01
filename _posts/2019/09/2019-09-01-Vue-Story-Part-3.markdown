---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 3)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: Vue 인스턴스"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-01 00:00:00 +0900
categories: [ "javascript", "vue.js", "introduction", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 지난 시간에 이어서 Vue 인스턴스에 대해서 정리해보도록 할께요 😊. 이번 과정을 통해서 Vue 인스턴스의 개념을 완벽히 이해하실 수 있어요! 만약 이전 시간의 내용이 기억나지 않는다면 **[쉽게 다가가는 Vue.js 이야기 (파트2)]({% link _posts/2019/08/2019-08-31-Vue-Story-Part-2.markdown %})** 또는 **[쉽게 다가가는 Vue.js 이야기 (파트1)]({% link _posts/2019/08/2019-08-29-Vue-Story-Part-1.markdown %})** 를 참고해주세요. 자 그럼 시작해볼까요? 😺

<!--more-->

## Vue 인스턴스란

Vue 앱은 Vue 함수를 이용해 새로운 Vue 인스턴스를 만드는 것부터 시작합니다. 즉 이전 시간에 배운 Vue 컴포넌트들은 Vue 인스턴스를 통해서 생성된 것이라고 여기면 이해가 쉽습니다. 

Vue 인스턴스는 데이터, 템플릿, 마운트할 엘리먼트, 메소드, 라이프사이클 콜백 등의 옵션을 포함할 수 있는 options 객체를 전달해야 합니다. 

```javascript
var vm = new Vue({
  // options 객체를 여기에 전달합니다.
})
```

<br>
{% include advertisements.html %}
<br>

> ℹ **알아가기** : Vue 의 디자인은 MVVM 패턴에서 영감을 받았습니다. 따라서, Vue 인스턴스 생성 시 vm 이라는 단어 키워드를 주로 사용합니다. 

우선, 루트 Vue 인스턴스가 만들어지면 중첩 가능하며 재사용이 가능한 컴포넌트 트리들로 구성됩니다. 한번 Todo 앱의 컴포넌트 트리를 살펴볼까요?

```bash
Root Instance
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

<br>
{% include advertisements.html %}
<br>

## 속성과 메서드

모든 Vue 인스턴스는 data 객체에 있는 모든 속성을 프록시 처리합니다. 

프록시 처리란 무슨 뜻일까요?

객체를 Vue 인스턴스의 data 객체에 전달할 경우 shallow copy 가 이루어짐을 의미합니다. 즉, 메모리 주소의 복사가 이루어지고 객체 자체를 복제한 복제본을 생성하지 않는다는 의미입니다. 

deep copy 와 shallow copy 의 차이점은 아래의 링크에서 쉽게 확인 가능합니다. 

- [freecodecamp.org : diff between deep and shallow copy in javascript](https://www.freecodecamp.org/news/copying-stuff-in-javascript-how-to-differentiate-between-deep-and-shallow-copies-b6d8c1ef09cd/)

```javascript
var data = { a: 1} // data 객체를 생성합니다.
var vm = new Vue({
  data: data // 그리고 전달하죠.
})

vm.a === data.a  // => true 즉, 같은 객체를 참조하는 것을 알 수 있습니다.
```

그러나 Vue 인스턴스 내 data 객체는 반응형이 아니라는 점에 항상 유념하셔야 합니다. 즉, 새로운 객체 타입을 추가하더라도 이는 화면에 렌더링 될 시에 고려되지 않습니다. 따라서, 코드 작성 시 미리 필요한 속성들을 지정해 두어야 함을 의미합니다.

```javascript
data: {
  maybeUsed: '', // 사용될 속성을 미리 정의해 두세요!
  // 줄임
}
```

<br>
{% include advertisements.html %}
<br>

- 그렇다면, 속성의 변경을 막을 방법은 없나요?

`Object.freeze` 를 사용하면 문제가 간단히 해결됩니다. Vue 의 반응성 시스템이 추적할 수 없도록 설정하는 효과가 있습니다. 아래를 살펴볼까요?

```javascript
var obj = { foo: 'bar' }
Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
```

이제 우리는 버튼을 클릭해도 `v-on` 디렉티브를 통해서 설정한 코드가 동작하지 않음을 알 수 있습니다.

```html
<div id="app">
  <p>{{ foo }}</p>
  <!-- obj.foo는 더이상 변하지 않습니다! -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>
```

<br>
{% include advertisements.html %}
<br>

- Vue 인스턴스는 data 외에 지원하는 사항이 없는가요?

Vue 인스턴스는 data 속성에 정의된 객체에 대해 반응성 시스템을 통한 추적 기능을 제공해준다고 위에서 배웠습니다. 그러나 이뿐만이 아닌 더욱 유용한 인스턴스 속성 및 메서드를 제공합니다. 이러한 속성 및 메서드들은 이름 충돌 가능성이 있으므로 `$` 접두어를 통해서 표현됩니다. 

아래의 간단한 테이블을 통해서 이를 확인해 볼까요?

|키워드|타입|설명|
|:---:|:---:|:---:|
|`vm.$data`|속성|Vue 인스턴스가 사용할 속성을 지정합니다.|
|`vm.$el`|속성|Vue 인스턴스가 초기화될 엘리먼트의 레퍼런스를 가리킵니다.|
|`vm.$watch`|메서드|Vue 인스턴스의 속성이 변경될 경우 호출되는 메서드를 지정합니다. |

위에서 언급한 키워드 외에도 다양한 속성과 메서드를 Vue 인스턴스는 내장하고 있습니다. 모든 목록은 아래의 링크를 참고해주세요! 

- [Vue.js API Reference](https://vuejs.org/v2/api/)

위에서 `$watch` 를 언급하였습니다. 속성이 변할 경우 실행되는 함수를 정의하는 watchdog 의 기능을 합니다. 한번 코드를 살펴볼까요?
 
```javascript
vm.$watch('a', function(newVal, oldVal) {
  // `vm.a` 가 변경되면 호출되는 로직이 여기에 작성됩니다.
})
```

<br>
{% include advertisements.html %}
<br>

새로운 데이터가 입력될 경우 위의 로직을 통해서 다른 연관된 값을 변경하는 작업을 위의 코드를 통해서 할 수 있습니다. 흔히 단위 변환와 같은 류의 규현에 사용됩니다. 한번 예시를 살펴볼까요?

아래는 간단한 길이 단위 변환을 하는 입력 폼입니다. 여기서 Vue 인스턴스 정의 영역에서 `watch` 영역을 잘 살펴보길 바랍니다. data의 속성과 watch 속성의 키값이 동일한 것이 보이시나요?

그렇습니다. km 이라는 data 의 속성을 변경시 이를 관찰(watch)하려면 watch 속성에 동일한 속성 이름을 정의하고 적용될 함수를 맵핑합니다. 그리고 각 단위에 맞는 연산처리를 합니다.

watch 의 경우 사용자가 입력한 값을 다양한 기준으로 처리할 때 유리합니다. (환율, 단위 변환, 해시 함수 적용 등이 주요 사례입니다.)

<!-- https://codepen.io/code-machina/pen/xxKLNpv -->

<pre class="codepen" data-height="484" data-type="html,result" data-href="xxKLNpv" data-user="code-machina" data-safe="true"></pre><br>

<br>
{% include advertisements.html %}
<br>


## 인스턴스 라이프사이클 훅

Vue는 반응형 시스템을 제공하기 위해 여러 단계의 초기화 단계를 거칩니다. 각 단계에는 고유한 이름이 맵핑되어 있어 사용자 각 단계에 훅(hook)을 지정할 수 있습니다. `created`, `mounted` 등이 있습니다.

아래의 그림과 저 코마가 정리한 표를 참조해주세요. (표가 더욱 더 가독성이 높을 수 있습니다.)

> 훅(hook)을 사이에 두고 이전 동작과 이후 동작이 나누어 집니다. 이후 동작이 기입되지 않은 케이스는 특정 시퀀스의 종료를 의미합니다. `created` 의 경우 반응성 시스템이 초기화 된 경우 creation 시퀀스의 종료를 의미하며 mount 시퀀스가 발생함을 의미합니다.

| 순서 |훅|이전 동작|이후 동작|
|:---:|:---:|:---:|:---:|
| 1 |beforeCreate| 이벤트 와 라이프 사이클 초기화 | 삽입과 반응성 시스템 초기화 |
| 2 |created| 삽입과 반응성 시스템 초기화 | |
| 3 |beforeMount| 템플릿 컴파일 혹은 `el` 의 outerHTML 을 템플릿으로 컴파일 | `vm.$el` 생성 그리고 `el` 을 교체 |
| 4 |mounted| `vm.$el` 생성 그리고 `el` 을 교체 | |
| 5 |beforeUpdate| 데이터 변경 이벤트가 발생 | 가상 DOM 의 재 렌더링(re-render) 및 수정(patch) |
| 6 |updated| 가상 DOM 의 재 렌더링(re-render) 및 수정(patch) |  |
| 7 |beforeDestroy|`vm.$destroy` 가 호출 | watcher, 하위 컴포넌트, 이벤트 리스너를 해체(teardown)|
| 8 |destroyed| watcher, 하위 컴포넌트, 이벤트 리스너를 해체(teardown) | 인스턴스 삭제 |

<br>
{% include advertisements.html %}
<br>

[![Vue Lifecylce Image](/assets/img/2019/09/vue-lifecycle.png)](/assets/img/2019/09/vue-lifecycle.png)

## 주의 사항 (arrow function)

[Mozilla Developer's Arrow Function Expression](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/%EC%95%A0%EB%A1%9C%EC%9A%B0_%ED%8E%91%EC%85%98) 에 따르면 화살표 함수 구문(arrow function expression)은 this, arguments, super 또는 new.target 을 바인딩하지 않습니다. 즉, this 컨텍스트가 호출하는 VUe 인스턴스에 사용할 경우 오류가 발생되게 됩니다.

<br>
{% include advertisements.html %}
<br>

따라서, 메서드 함수가 아닌 곳에 적당합니다. 아래의 샘플 코드에서 map 함수 사용에 활용된 예제를 참고해주세요!

```javascript
var materials = [
  'Hydrogen',
  'Helium',
  'Lithium',
  'Beryllium'
];

console.log(materials.map(material => material.length));
// expected output: Array [8, 6, 7, 9]
```

- 화살표 함수 사용 예

방금 우리가 `watch` 인스턴스 메서드를 통해서 화살표 함수가 어떻게 동작하는지 살펴볼까요? 우선 아래와 같이 function 키워드를 통해서 정의한 함수를 화살표 함수로 변경해줍니다.

```javascript
km2: function(val) {
  // console.log(val) // 디버깅용입니다.
  this.km2 = val;
  this.cm = val * 100000;
  this.mm = val * 1000000;
},
```

<br>
{% include advertisements.html %}
<br>

function 키워드를 지우고 화살표(=>) 를 추가해주세요.

```javascript
km2: (val) => {
  // console.log(val) // 디버깅용입니다.
  this.km2 = val;
  this.cm = val * 100000;
  this.mm = val * 1000000;
},
```

- 반응성 시스템 동작 불가 예

아래의 예에서 킬로미터 입력 폼에 숫자를 입력해 볼까요? 아마도 위의 예제와 다르게 다른 단위의 값이 변경되지 않습니다. 맞습니다. this 키워드가 동작하지 않는 것입니다.

아래의 예를 통해 메서드를 정의할 경우 화살표 함수(arrow function expression)을 사용하면 안된 다는 것을 배울 수 있습니다.

<!-- https://codepen.io/code-machina/pen/QWLMRzB -->

<pre class="codepen" data-height="484" data-type="js,result" data-href="QWLMRzB" data-user="code-machina" data-safe="true"></pre><br>

<br>
{% include advertisements.html %}
<br>

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
