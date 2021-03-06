---
layout: post
title:  "빠른 이미지 업로더 제작 (Vue.js v2.0)"
subtitle: "컴포넌트를 활용한 유연한 이미지 업로드 컴포넌트 제작"
author: "코마"
date:   2019-03-11 03:00:00 +0900
categories: [ "javascript", "vue", "image", "upload"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 이번 시간에는 Vue.js 를 이용해 이미지를 업로드하는 컴포넌트를 제작해보도록 하겠습니다. 실습 자료는 모두 codepen.io 에서 확인이 가능합니다. 실제로 파일을 업로드 하는 내용은 깃헙을 참조해 주세요.

<!--more-->

# 개요

C# 에는 DataTable 이라는 추상화된 객체가 있습니다. 이 추상화 객체는 실생활의 표와 같은 데이터를 다룰 수 있도록 도와줍니다. 이는 C# 이 제공하는 Strongly Typed Language 이므로 클래스를 지정하고 이를 구현하는데 있어 언어의 강력한 지원이 포함된 결과입니다.

그러나, Javascript 는 그렇지 않습니다. 매우 자유로운 언어입니다. 물론 장단점이 많습니다만, 저는 장점이 더 크다고 봅니다. 데이터 테이블을 이야기한 이유는 Jaavascript 를 통해서 표와 같은 데이터 유형을 표현하기 위해선 Array 와 Dictionary 타입을 이용해 `헤더` 와 `데이터` 영역을 나누어야 합니다.

다음의 상황을 만날 경우 어떻게 해야할까요?

여러분의 프론트엔드 프로젝트에서 칼럼을 동적으로 추가하거나 제거하는 기능을 가진 데이터 테이블 컴포넌트를 만들어야 합니다. 가상의 CM-Data-Table 컴포넌트는 헤더 영역과 데이터 영역이 분리되어 있습니다.

헤더를 표시하기 위한 객체는 아래와 같습니다.

```javascript
headers = [
  {
    text: '주소(ipv4)',
    align: 'center',
    value: 'addr',
    primary: true,
  },
  {
    text: '조회 결과',
    align: 'center',
    value: 'status',
  },
  {
    text: '국가',
    align: 'center',
    value: 'country',
  }
]
```

그리고 데이터는 아래와 같습니다.

```javascript
data = [
  {
    addr: '88.231.136.9',
    status: false,
    country: 'Turkey',
    network: 'TurkTelekom',
    owner: 'TT ADSL-TT net_dynamic_ulus',
  },
  // skipping
]
```

우리의 컴포넌트는 `primary` 가 `true` 인 헤더 아이템을 제외하고 표에서 칼럼이 제거가 가능하도록 하려고 합니다. UI 상에서 표의 헤더에 X 아이콘이 보이며 이 아이콘을 클릭하면 헤더 목록에서 칼럼이름이 사라집니다. 그리고 데이터를 출력하는 HTML 코드는 아래와 같습니다. **부득이 Psuedo Code 문법을 사용한 것에 양해를 구합니다.(Jekyll 의 Liquid Template 문법과 충돌로 인해)**

```html
<!-- 데이터 영역 출력 --> 
%- for item in data -%
  <td> item.addr </td>
  <!-- 삭제가 용이한 영역 -->
  %- if headers.find(item=> item.value == 'status') -%
    <td> item.status </td>
  %- endif -%
  <td> item.country </td>
  <td> item.owner </td>
%- endfor -%
```

그렇다면, 위의 data 객체에서 `owner` 칼럼을 제거하였다고 하였을 때, header 에서 `value: 'owner'` 인 항목이 제거 됩니다. 그리고 이는 `데이터 출력 영역`에 영향을 주어야 함이 당연합니다. 

저는 이러한 경우 한줄 코드로 주로 해결합니다. 이러한 구현은 Template 언어를 지원하는 `Vue`, `.NET Core`, `Liquid` 등에 자주 사용될 수 있습니다.

```javascript
headers.find(item=> item.value == 'addr')
// => undefined or object
headers.find(item=> item.value == 'addr') ? true : false
// => if find return undefined, it will return false. otherwise, it will return true.
```

## 결론

임의의 객체의 배열 `Array(Object(), Object(), Object())` 인 데이터 구조에서 `Object` 가 `K` 라는 키를 가지는지 확인하기 위해서는 `Array.find( item => { return /* comparison */ })` 과 같은 코드를 활용한다. **머리로 생각하였을 때는 좋은 소재라고 생각하였는데 막상 적어보니 조금은 애매합니다. 좀더 발전하는 코마가 되겠습니다.**