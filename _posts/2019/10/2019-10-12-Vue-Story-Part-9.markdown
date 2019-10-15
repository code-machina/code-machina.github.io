---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 9)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: 리스트 렌더링"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-10-04 00:00:00 +0900
categories: [ "vue.js", "node.js", "list rendering", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 Vue(뷰).js 에서 조건 렌더링에 대해 소개해드리도록 하겠습니다. 이번 시간을 통해 Vue 가 제공하는 기능 중 **리스트 렌더링** 을 완벽히 정리해 보도록 하겠습니다. "배열, 객체를 이용한 리스트 렌더링", "상태(state) 관리(Maintaing)", "v-for 디렉티브의 다양한 이용법" 들을 이해하고 자유롭게 사용할 수 있습니다. 👏👏👏 자 그럼 시작해볼까요?
 
<!--more-->

## 이어가기

만약 이전 시간의 내용이 기억나지 않는다면 아래에 참조된 이전 시리즈의 링크들을 차분히 다시 살피는 것은 어떨까요? 

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

## v-for 와 배열

이전 시간에 이미 우리는 `v-for` 디렉티브를 다루었습니다. 그러나 이것이 전부는 아니죠. 좀 더 자세히 `v-for` 의 매력에 빠져볼 수 있는 시간을 준비하였습니다. v-for 에서는 `item in items` 와 같은 특별한 문법이 적용됨을 기억한다면 모든 문제는 쉽게 해결됩니다. 아래의 소스코드를 살펴볼까요?

아래의 Vue 인스턴스에서 data 속성의 items 배열에 집중해 주길 바랍니다. 동일한 패턴의 오브젝트들이 나열되어 있는 배열(Array) 타입임에 분명하죠?

```js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

그리고 위에서 선언한 items 배열을 li 엘리먼트와 맵핑하여 각 객체를 나열해 보도록 하겠습니다. 각 객체의 속성(`message`) 에 접근하는 방법은 `.(dot)` 연산자 임에 주목해볼까요?

{% raw %}

```html
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```

{% endraw %}

그런데, item 은 무엇을 의미할까요? 파이썬 문법에 익숙하신 분들은 아마도 바로 알아차렸을 지도 모릅니다. 일종의 alias 이며 배열의 각 객체를 가리키기 위한 용도입니다. 그런데, 아래와 같은 표현으로도 사용할 수 있습니다. 

item 이 올자리에 `(item, index)` 를 표기하면 동작이 약간 달라집니다. 해당 배열의 순번(index)를 표기해 주게 됩니다. 0, 1, 2, ... 와 같이 표현됩니다. 

{% raw %}

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

{% endraw %}

> ‼ **(INFO)** > `in` 대신에 `of` 키워드를 대용할 수 있습니다. v-for=`item of items` 와 같이 말이죠 문법 스타일의 문제이지 구현에는 차이가 없습니다. 자바스크립트의 iterator 를 표현하는 스타일입니다.


<br>
{% include advertisements.html %}
<br>

## v-for 와 객체(Object)

위에서는 배열에 대해서 언급하였습니다. 정확히는 배열 내에 동일한 속성(property, message)을 가진 객체입니다. 그렇다면 순수하게 하나의 객체를 v-for 와 함께 사용 가능할까요? 이 또한 가능합니다.

아래의 코드를 살펴볼까요? 

{% raw %}

value 라는 키워드를 통해서 data 속성에 정의된 object 라는 객체의 값(value)만을 출력하고 있습니다.

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```

```js
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
```

그렇다면, 만약 이 값의 이름을 표기하고자 할때는 어떻게 할까요?  아래와 같은 스타일로 표기해주면 매우 간단합니다.

```html
<div v-for="(value, name) in object">
  {{ name }}: {{ value }}
</div>
```

그리고 여기에 더해 index 키워드를 추가하면 순번(인덱스)까지 출력 가능합니다. 편리하죠?

```html
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```

{% endraw %}

> ⚠ WARNING: 객체에 iteration 을 적용할 때 주의해야할 사항이 있습니다. 이는 **나열되는 순서**입니다. 나열되는 순서는 `Object.keys()` 에 의존하기 때문에 어떠한 자바스크립트 엔진에서 구동하느냐에 따라서 그 순서가 일관되지 않을(in-consistent) 가능성이 있습니다. 따라서 주의를 요합니다.

<br>
{% include advertisements.html %}
<br>

## 상태 관리

Vue 는 `v-for` 를 통해서 렌더링된 엘리먼트의 목록을 업데이트할 때, 기본적으로 `in-place 패치` 전략을 취합니다. `in-place` 패치라는 용어를 좀 더 상술해 볼까요? 아래의 상황을 머리 속에 그려보겠습니다.

전화번호 리스트와 사람의 이름이 함께 나열되는 컴포넌트가 있다고 가정해 보겠습니다. 만약 `{ id: 2, ext : 1234, name: 'code-machina' }` 라는 객체의 순서가 변경된다면 Vue 는 어떻게 행동할까요? 

Vue 는 A와 B 인물에 대한 순서 정보가 변경되었다고 판단하는 즉시, 엘리먼트 자체를 이동하는 것이 아닌 각 변경된 엘리먼트에 접근하여 데이터를 수정합니다. 즉, 엘리먼트를 조작하지 않고 데이터만 교체하는 것이죠.

이러한 운영 모드는 효과적일 수 있으나 특정 경우에는 적합하지 않을 수 있습니다. 즉, 나열되는 순서가 하위 컴포넌트 또는 임시 DOM 상태(폼의 입력 값)에 종속적이지 않은 경우에만 적절합니다.

이제, 이해가 적절히 되었다면 아래의 코드를 살펴보겠습니다. 엘리먼트를 재 사용하고 재 정렬하기 위해 우리는 Vue에게 힌트(hint)를 제공할 수 있습니다. 바로 `v-bind:key` 어트리뷰트를 이용해서 말이죠.

```html
<div v-for="item in items" v-bind:key="item.id">
  <!-- content -->
</div>
```

Vue 공식 문서는 가능하면 `key` 어트리뷰트를 이용하여 `v-for` 를 구현하기를 권장합니다. 그리고 key 어트리뷰트에 오는 값은 문자열또는 숫자와 같은 primitive 타입을 사용해야함을 매우 명심해 주세요.

<br>
{% include advertisements.html %}
<br>

## 배열 변경 감지

배열(Array)는 상황에 따라 변경될 수 있습니다. 이를 뮤테이션(Mutation)이라고 합니다. Mutation 메서드에는 아래와 같은 것들이 있습니다.

Vue 는 아래의 mutation 메서드를을 랩핑(Wrapping)하여 변경이 발생하였을 때, 뷰(View)를 업데이트하도록 트리거를 걸어 놓습니다. (아마 이러한 세부적인 메커니즘을 모르셨을 분도 있습니다. 간과하기 쉽습니다. 따라서, [코마의 훈훈한 블로그](/)를 많이 구독해 주시는 것을 추천드립니다.)

```js
push()
pop()
shift()
unshift()
splice()
sort()
reverse()
```

`example1.items.push({ message: 'Baz' })` 와 같이 배열에 새로운 객체를 추가하는 경우 즉시, View 업데이트가 발생하도록 되어있습니다. (Vue 만세!!)

<br>
{% include advertisements.html %}
<br>

## 배열 교체

Mutation 메서드들은 원본 배열을 변경합니다. 그러나 모든 메서드들이 mutatable 한 것은 아닙니다. 즉, non-mutatable 한 메서들이 있습니다. `filter()`, `concat()`, `slice()` 가 그것입니다. 이러한 메서드들은 언제나 새로운 배열을 리턴합니다.

그렇다면 필요에 따라 아래와 같은 구현을 하게 되는데요. 여기서 하나의 의문이 발생합니다. 

**Vue 가 현재 DOM을 버리고 새롭게 렌더링하는 것 아닌가?**

다행이도 Vue 는 휴리스틱을 이용해 DOM 재사용율을 최대화하고 있습니다. 따라서, 일부 겹치는 객체들을 포함한 배열을 다른 배열로 엎어치는 것은 매우 효과적인 작업입니다. (**즉, 권장할만한 스타일이라는 점입니다.**😊)

```js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

<br>
{% include advertisements.html %}
<br>

## 경고(Caveats): 배열 변경 감지

하지만, 모든 면에 있어서 완벽할 수 없습니다. 즉, Vue 도 변경(Mutation)을 감지하지 못하는 스타일이 있습니다. 아래를 살펴볼까요?

```js
vm.items[indexOfItem] = newValue // 직접 배열에 접근하여 값을 수정하는 케이스
vm.items.length = newLength // 배열의 길이를 직접 수정하는 케이스
```

위의 두 경우는 Vue 가 감지하지 못합니다. 그러나 우리는 안된다고 하면 되게 끔 하는 것을 원합니다. 따라서, 현명한 Vue 는 이 방법을 제공합니다. 

```js
// Vue.set 을 이용한 방법
Vue.set(vm.items, indexOfItem, newValue) // == vm.items[indexOfItem] = newValue
// Array.prototype.splice (splice 는 Mutatable method) 를 이용하는 방법
vm.items.splice(indexOfItem, 1, newValue) // == vm.items.length = newLength
```

Vue.set 키워드 대신에, `vm.$set` 를 이용하면 직접 인스턴스에 대해 변경을 트리거하는 인상을 주므로 좀 더 직관적입니다. 따라서, 위의 코드는 아래와 같이 쓸 수 있습니다.

```js
vm.$set(vm.items, indexOfItem, newValue)
vm.items.splice(newLength)
```

<br>
{% include advertisements.html %}
<br>

## 경고(Caveats) : 객체 변경 감지 

Vue 는 객체의 속성을 추가 및 삭제에 대해 감지하지 못합니다. 즉, 객체가 변경되었음에도 불구하고 View 가 업데이트 되지 않는 상황이 있을 수 있음을 의미합니다.

아래의 코드를 살펴볼까요? userProfile 이라는 객체는 name 이라는 속성(Property)를 이미 가지고 있습니다. 즉, userProfile.name 은 추적 가능한 상태입니다. 그러나 `userProfile.age = 27` 라고 선언할 경우 age 속성은 변경 감지가 되지 않습니다. 그렇다면 어떻게 해야 할 까요?

```js
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
```

`$set` 특수 메서드를 사용하면 됩니다. 아래와 같이 새롭게 추가할 경우 즉시 변경감지가 가능합니다.

```js
vm.$set(vm.userProfile, 'age', 27)
// or, Vue.set(vm.userProfile, 'age', 27)
```

그렇다면, 추가할 속성이 age 뿐만 아니라 favoriteColor 가 더 추가되어야 하는 경우는 어떻게 해야 할까요? 다수의 속성을 추가할 경우에는 아래의 형태의 코드를 사용하면 됩니다. 그리고 객체는 교체(replace) 됩니다.

```js
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

`$set` 과 `Object.assign({}, ... , ... )`을 잘 기억해둔다면, 통과입니다. 

<br>
{% include advertisements.html %}
<br>

## 필터, 정렬된 결과 출력

이 전까지의 내용을 미루어 sort, filter 한 결과를 출력할 때에는 computed 속성을 사용하면 좋다고 알고 있었습니다. 그러나 v-for 에서는 computed 보다 method 를 사용해야 합니다. computed 는 의도처럼 동작하지 않음에 주의해야합니다. (즉, 필터링, 정렬된 결과를 출력하고자 할 때에는 아래와 같은 스타일을 사용해줍니다.)

{% raw %}

```html
<li v-for="n in even(numbers)">{{ n }}</li>
```

```js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

{% endraw %}

<br>
{% include advertisements.html %}
<br>


## v-for 의 다양한 용법들

v-for 는 `n in 10` 과 같은 형태의 표현 또한 허용합니다. `1 2 3 4 ... 10` 과 같이 출력됩니다.

{% raw %}

```html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```
{% endraw %}

<br>
{% include advertisements.html %}
<br>

### 템플릿과 v-for

v-for 는 템플릿과 호응됩니다. v-if 를 설명할 때, 여러 엘리먼트를 묶어서 조건 렌더링하는 경우 template 을 사용하는 예를 명시하였습니다. 아래의 코드 또한 그와 유사한 사용의 예입니다. 

{% raw %}

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

{% endraw %}

<br>
{% include advertisements.html %}
<br>

### v-if 와 v-for

> **⚠ WARN**: v-if 와 v-for 를 함께 사용하는 것은 권장하지 않습니다. 스타일 가이드를 참고해주세요.

v-for 는 v-if 보다 더 높은 우선 순위를 가집니다. 즉, 매 루프마다 v-if 가 따로 동작할 것임을 의미합니다. 즉, 아래와 같이 완료되지 않은 todo 항목 만을 출력하는 코드가 사용될 수 있음을 의미합니다.

{% raw %}

```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

또한, 아래와 같이 코딩을 할 수 있습니다. ul 엘리먼트 대신에 template 엘리먼트를 쓸 수 있음을 또한 유념해주세요. todo 항목이 비어있다면 `할 일이 없습니다!` 라는 메시지가 출력될 것입니다.

```html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>할 일이 없습니다!</p>
```

{% endraw %}

<br>
{% include advertisements.html %}
<br>

### v-for 과 컴포넌트

v-for 를 사용자 정의 컴포넌트에 사용하는 방법을 생각해 보신적 있나요? 그리고 아래와 같은 코드를 구성해 본적이 있나요? 분명 아래의 코드는 동작하지 않을 것입니다.

{% raw %}

```html
<my-component v-for="item in items" :key="item.id"></my-component>
```

Vue 는 사용자 정의 컴포넌트에 자동으로 데이터를 전달하지 않음을 아래와 같이 설명하고 있습니다. 

**사용자 정의 컴포넌트와 v-for 의 동작을 강하게 결합하는 경향이 있기 때문에 컴포넌트를 다른 상황에 재사용하도록 만들기 위해서 명시적으로 선언하도록 하였다.**

따라서, 아래와 같이 구현합니다. 필요한 요소들을 일일이 명시적으로 선언하는 것입니다.

```html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
></my-component>
```

{% endraw %}

<br>
{% include advertisements.html %}
<br>

## todo 목록 구현 예

할일 목록을 구현한 샘플입니다. 아래의 GIF 를 참조해볼까요? 잘 작동하는 것을 확인할 수 있습니다. 

[![Todo list Example-1](/assets/img/2019/10/todo-list-ex1.gif)](/assets/img/2019/10/todo-list-ex1.gif)

위의 구현은 아래의 소스코드로 구성되었습니다. 간단히 복붙을 통해 나만의 todo 목록을 구현할 수 있습니다. 

그런데, 이 샘플이 상징하는 의미는 여기에 그치지 않습니다. 아래의 샘플 중 li 엘리먼트만을 가져왔습니다. 여기서 is 어트리뷰트가 보이시나요? todo-item 컴포넌트를 그대로 사용하지 않고 li 엘리먼트의 어트리뷰트를 통해서 간접적으로 할당한 모습입니다. 왜 일까요?

원래 ul 엘리먼트 하위에는 li 엘리먼트만이 유효한 엘리먼트입니다. 따라서, 브라우저 파싱 오류를 피하기 위해서 아래와 같이 우회 방법을 사용한 것입니다. (정말 세심한 Vue 입니다.)

```html
<li is="todo-item"
  v-for="(todo, index) in todos"
  v-bind:key="todo.id"
  v-bind:title="todo.title"
  v-on:remove="todos.splice(index, 1)"
></li>
```

그럼 마지막으로 코드를 감상하는 시간을 가져볼까요?

```html
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat"
    >
    <button>Add</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
```

```js
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Remove</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Do the dishes',
      },
      {
        id: 2,
        title: 'Take out the trash',
      },
      {
        id: 3,
        title: 'Mow the lawn'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
```

<br>
{% include advertisements.html %}
<br>

## 마무리

드디어 Vue.js 공식 문서 정복(뽀개기) 과정에서 리스트 렌더링이 완료되었습니다! 👏 (짝짝짝) 또한, 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

다음 시간에는 **이벤트 핸들링(Event Handling)**을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Vue.js 2.x : Guide Docs](https://vuejs.org/v2/guide/index.html)
