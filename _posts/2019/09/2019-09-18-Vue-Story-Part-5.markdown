---
layout: post
title:  "쉽게 다가가는 Vue.js 이야기 (파트 5)"
subtitle: "목차로 알아보는 Vue.js 의 초단간 개요: Computed 와 Watchers 속성"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-18 00:00:00 +0900
categories: [ "vue.js", "node.js", "computed", "watchers", "basic", "sytax", "10m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 지난 시간에 이어서 Vue.js 의 Computed 와 Watcher 속성에 대해서 정리해보도록 할께요 😊. 이번 과정을 통해서 템플릿 문법의 복잡성을 줄이는 방법과 데이터 변경 작업 시 computed 속성으로 처리하기엔 시간이 긴 작업을 비동기로 해결하는 watcher 속성에 대해 알아보겠습니다. 👏👏👏 만약 이전 시간의 내용이 기억나지 않는다면 본문 내 참조된 이전 시리즈들의 링크들을 참고해주세요. 자 그럼 시작해볼까요? 😺
 
<!--more-->

## 도입부

벌써 파트 5 입니다. Vue 스토리의 중반부에 이르렀습니다. 앞으로 여러분이 Vue 의 전반적인 내용을 모두 이해할 날이 얼마 남지 않았네요. 이전 과정을 복습하시려는 분들을 위해 이전 과정의 링크를 정리하였습니다. 

- **[쉽게 다가가는 Vue.js 이야기 (파트4)]({% link _posts/2019/09/2019-09-06-Vue-Story-Part-4.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트3)]({% link _posts/2019/09/2019-09-01-Vue-Story-Part-3.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트2)]({% link _posts/2019/08/2019-08-31-Vue-Story-Part-2.markdown %})** 
- **[쉽게 다가가는 Vue.js 이야기 (파트1)]({% link _posts/2019/08/2019-08-29-Vue-Story-Part-1.markdown %})**

<br>
{% include advertisements.html %}
<br>

Vue.js 는 컴포넌트를 논리적으로 분할하여 개발할 수 있는 시스템을 제공해 줍니다. 따라서, 여러분들은 아래와 같이 프로젝트 구조를 잡을 수 있습니다.

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

위의 컴포넌트들 중에 EditTodoButton 컴포넌트를 살펴볼까요? Component 의 코드는 아래와 같습니다.

{% raw %}

```js
Vue.component('edit-todos-button', {
  data: function () {
    return {
      message: "o l l e H"
    }
  },
  template: `<div id="example">
  {{ message.split('').reverse().join('') }}
  </div>`
})
```

{% endraw %}

template 을 살펴보면 inline javascript expression 의 규약에 맞추어 힘겹게 message 를 처리하는 로직을 짜낸 것으로 보입니다. 만약에 여기에 좀 더 복잡한 로직이 들어가야 한다면 개발자 분들의 멘탈은 붕괴되고 말 것입니다.

<br>
{% include advertisements.html %}
<br>

## Computed 속성

여기서 우리의 Vue.js 는 해결 책을 제공해줍니다. 바로 Computed 와 Watchers 속성입니다. 개선된 코드를 살펴볼까요?

{% raw %}

```js
Vue.component('edit-todos-button', {
  data: function () {
    return {
      message: "o l l e H"
    }
  },
  template: `<div id="example">
  {{ reversedMessage }}
  </div>`,
  computed: {
    reversedMessage: function() {
      return this.message.split('').reverse().join('')    }
  }
})
```

{% endraw %}

로직이 분할되었습니다. template 의 소스코드도 간결해 졌습니다. 그런데 다음과 같은 생각을 해볼 수 있습니다. 

**method 속성을 이용해서 구현하는 것과 똑같지 않은가?**

맞습니다. 뭔가 특별한 마법이 숨겨져 있는 것처럼 보이지만, 사실 method 를 이용해 reversedMessage 함수를 구현하는 것과 동일합니다. 그렇지만 여기에는 분명한 차이점이 있습니다.

method 는 무조건 함수를 호출하도록 되어 있지만, computed 속성은 caching 을 사용하여 종속된 데이터의 변경이 있을 때에만 구문을 재평가합니다.

{% raw %}

다시말해 `this.message` 의 값이 변경될 때만 `{{ reversedMessage }}` 가 재 평가됨을 의미합니다. 반면 method 속성의 함수는 매번 재호출되어 효율성 면에서 떨어집니다. 만약 여러분이 **Vue 로 구성된 프론트 앱을 리팩터링 한다면 method 에 정의된 내용을 computed 속성으로 변경함으로써 앱의 속도를 향상시킬 수 있습니다.** 😍

또한, reversedMessage 속성을 여러번 참조하는 경우 computed 속성으로 정의한 경우 이미 계산된 값을 캐싱하고 있다가 재출력 하기 때문에 더욱 효율적입니다. 만약 여러분이 캐싱을 원하지 않는다면 computed 속성에 정의된 로직을 method 로 옮겨주면 됩니다. 

{% endraw %}

그런데, 여기서 의문이 또 발생할 수 있습니다. 

**method 에 정의하였다면 어떠한 기준으로 재 호출 되나요?**

Vue.js 는 Virtual DOM 을 구성합니다. 그리고 데이터가 변경될 때마다 re-render 와 patch 가 호출됩니다. 이 과정을 체크하려면 beforeUpdate 와 updated 훅(hook)을 정의해주면 관찰이 용이합니다.

그렇다면 우리는 re-render 가 발생한다고 가정하였을 때, method, computed 속성 중 어디에 로직을 구현해 넣을지를 결정하면 문제가 심플해집니다.

> Vue.js 의 라이프사이클에 대해 복습이 필요하다면 [쉽게 다가가는 Vue.js 이야기 (파트3)]({% link _posts/2019/09/2019-09-01-Vue-Story-Part-3.markdown %}) 를 복습해주세요.

<br>
{% include advertisements.html %}
<br>

## Computed Setter 란

아래의 예제를 살펴볼까요? 여러분은 이미 fullName 이라는 computed 속성을 정의하였습니다. 그리고 이 값은 사용자의 입력에 따라 변경될 수 있다고 가정해볼까요? 이러한 구현은 마이페이지나 회원가입 페이지에서 사용할 수 있습니다. 

가상의 예이지만 아래와 같이 가정해 볼까요? 

사용자의 이름을 "코드 마키나" 와 같이 입력을 받고 이를 띄워쓰기를 기준으로  "코드", "마키나"와 같이 분할합니다. 그리고 첫번째 "코드"를 이름으로 "마키나"를 성으로 간주하고 각 데이터 속성에 반영하도록 합니다.

보통은 성과 이름을 따로 입력 받습니다. 이것은 가상의 예이니 이러한 부분은 차치하고 아래의 예를 확인해 볼까요?

{% raw %}

```html
<div id="demo">{{ fullName }}</div>
```

```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
  }
})
```

{% endraw %}


computed 속성의 fullName 에 get 과 set 이라는 키워드가 정의되었습니다. 우리가 흔히 getter, setter 라고 부르는 것입니다. setter 를 유심히 살펴봐 주세요. 새로운 인자를 받도록 되어 있으며 가상의 예를 구현하고 있습니다.

위의 구현을 통해 `vm.fullName = "Code Machina"` 라는 코드를 구현할 경우 `firstName` 과 `lastName` 은 setter 에서 정의된 로직에 의해 각각 Code, Machina 라는 문자열로 업데이트 됩니다. 

computed 속성에 setter 를 추가하여 구현할 경우 응용의 폭이 넓어지는 효과가 있습니다.

<br>
{% include advertisements.html %}
<br>

## Watchers 속성

드디어 Watchers 속성입니다. 여러분이 파일 시스템 상에서 파일이 변경되거나 추가될 경우 동작하는 WatchDog를 떠올린다면 이미 절반을 이해한 것입니다. 이제, Vue.js 가 watchers 속성을 구현한 철학을 이해하면 간편하게 활용할 수 있습니다.

watchers 속성은 데이터 변경과 관련하여 비동기적인 동작을 수행하거나 매우 많은 계산이 필요한 경우 유용합니다. 

**비용이 높은 처리 작업을 수행할 때, Watcher 의 사용을 고려한다** 라고 간단히 머리 속에 정의해봅시다. 그리고 비용이 높은 처리 작업을 떠올려 볼까요? 저 코마의 머리 속에는 아래와 같은 항목들이 떠오릅니다.

- axios 를 사용하여 외부 API 호출
- File 업로드 전에 파일의 유효성을 검증 (확장자에 맞는 파일 포맷인지 체크)
- 알고리즘을 통해 데이터 셋에서 원하는 값을 추출 후 서버에 전송

이 중에 axios 를 이용한 외부 API 호출 예를 살펴보겠습니다.

{% raw %}

```html
<div id="watch-example">
  <p>
    API 서버에게 데이터를 호출하는 예제: 
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

```js
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: '제출할 질문을 작성해주세요.'
  },
  watch: {
    question: function (newQuestion, oldQuestion) {
      this.answer = '타이핑 완료를 기다리는 중입니다....'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // lodash 라이브러리 중 debounce 함수는 재 요청까지 기다리는 시간을 제공합니다.
    // 즉, 500ms 에 한번씩 요청을 전송할 수 있음을 의미합니다.
    // https://lodash.com/docs#debounce 문서를 참고해주세요.
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = '죄송하지만 질문은 반드시 ?(느낌표)를 마지막에 추가해야 합니다.'
        return
      }
      this.answer = '처리 중입니다....'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'API 서버에 접근할 수 없습니다. ' + error
        })
    }
  }
})
</script>
```

{% endraw %}

created 훅을 통해 `this.deboundcedGetAnswer` 라는 함수를 정의하게 되었습니다. 그리고 lodash 라이브러리가 제공하는 `debounce` 함수를 통해 `this.getAnswer` 메서드를 500ms 마다 한번씩 호출할 수 있습니다.

그리고 watch 속성을 통해서 `question` 데이터가 변경될 경우 정의해둔 `this.debouncedGetAnswer`를 호출하는 구조입니다.

위의 예를 통해서 여러분들도 API 서버에 데이터를 전송하는 경우 watch 속성을 사용해 보시길 권해드립니다.

<br>
{% include advertisements.html %}
<br>

## Watcher 와 Computed 속성

이 문서의 마지막 단계에 도달하셨습니다. 👏👏👏 (스스로에게 칭찬의 의미로 박수를 쳐주세요!) watch 와 computed 의 매력에 푹빠져들었다면 아래의 질문을 스스로에게 던져볼까요?

**watch 와 computed 속성을 어떻게 구분해서 써야할까?**

watch 는 분명 매우 편리합니다. 그러나 computed 또한 매우 편리합니다. 저 코마는 두 예를 비교함으로써 구현 시 예상되는 문제점을 지적하여 여러분들이 실전 코딩에서 효과적으로 이론을 적용하도록 정리해보았습니다.

<br>
{% include advertisements.html %}
<br>

### watch 를 사용한 구현의 예

아래의 예는 firstName 과 lastName 데이터에 watcher 를 걸어두고 각 데이터가 변화할 경우 fullName 이라는 데이터를 반영하는 구조입니다. 

여러분의 날카로운 눈이라면 이 예제가 중복된 코드를 사용하고 있음을 파악할 수 있습니다. 즉, 로직이 변경된다면 각 watcher 를 수정해야함을 의미합니다. 아래의 예제의 경우 두 번의 수정이 필요합니다.

코딩에 있어 중복된 로직을 반복하는 것은 유지 보수나 코드 길이 측면에서 지양 해야한다고 생각합니다.

{% raw %}

```js
var vm = new Vue({
  // .... 
  watch: {
    firstName: function(val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function(val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

{% endraw %}

### Computed 속성을 상용한 예

그렇다면 computed 속성을 사용한다면 어떻게 될까요? 동일한 구현이지만, 코드 길이가 줄었습니다. 그리고 더욱 심플해졌습니다. 앞으로 fullName 을 출력하는 로직이 변경할 경우 수정할 영역이 하나입니다. 

{% raw %}

```js
var vm = new Vue({
  // .... 
 computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
 }
})
```

{% endraw %}

### 정리

분명 watch 속성을 매우 편리합니다. 그러나 과용(overuse)는 지양되어야 합니다. 위의 비교를 통해서 여러분은 각 변수의 변화를 통해 다른 변수를 수정하는 로직 구현은 computed 속성이 더 나음을 알 수 있습니다. 이는 다루는 변수의 갯수가 증가 할수록 더욱 두드러지게 될 것입니다. 

<br>
{% include advertisements.html %}
<br>

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
