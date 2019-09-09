---
layout: post
title:  "SSRF 예방법 (Server Side Request Forgery Prevention)"
subtitle: "알면 쉽지만 모르면 어려운 보안"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-09 00:00:00 +0900
categories: [ "ssrf", "security", "mitigation", "15m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 SSRF 라는 공격 기법에 대해 알아보고 효과적인 보안 방법을 모색해보려고 합니다. 

<!--more-->

## SSRF 란?

혹시 CSRF 라는 공격에 대해서 들어보신적이 있나요? 아마도 많은 분들이 XSS와 함께 가장 많이 발생하는 공격으로 알고 있을 것 같네요.

SSRF 는 CSRF 와 유사한 공격 방식입니다. CSRF는 클라이언트로 하여금 공격자가 강제한 제어 동작을 수행하도록 하는 공격 방식입니다. 대신, SSRF 는 서버로 하여금 공격자가 강제한 제어 동적을 수행하도록 하는 공격 방식입니다. 

공격의 대상이 클라이언트에서 서버로 바뀐 것입니다. 그렇다면 어떻게 이러한 공격이 가능하게 되었을까요?

이는, 신뢰 주체에 대한 맹목적인 믿음으로 인해 신뢰 주체가 제공한 데이터를 완벽히 신뢰할 경우 발생하는 취약점입니다. 아래의 조건을 살펴볼까요?

1. 클라이언트의 요청(claim)을 요청하는 서버가 2개가 존재합니다.
2. 서버 A는 서버 B의 정보를 수신한 뒤에 이를 클라이언트에 반영합니다.
  - MSA 구조에서 흔히 발생하는 취약점으로 여겨집니다.
  - MSA 아키텍처에서는 서버간의 대화를 통해 서로 다른 정보를 조인하는 과정을 수행합니다.
    - Graphql 과 같은 기술을 떠올려 보실까요?
3. SSRF 의 취약점이 발생할 경우 어떤 서버가 취약하다고 판단될까요? 
  - 서버 A에서 요청(Claim)을 무분별하게 신뢰하였으므로 취약한 서버(어플리케이션)이라고 판단합니다.

[![SSRF 공격 구성도](https://raw.githubusercontent.com/OWASP/CheatSheetSeries/master/assets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet_SSRF_Common_Flow.png)](https://raw.githubusercontent.com/OWASP/CheatSheetSeries/master/assets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet_SSRF_Common_Flow.png)

### 공격 발생 상황

여러분의 어플리케이션이 아래의 구현을 하고 있다면 SSRF 의 가능성을 염두해두고 코드를 진단해 보아야 합니다. SSRF 의 공격 벡터는 위에서 장황하게 설명하였지만 결국은 아래와 같이 정의할 수 있습니다.

**공격 벡터**

1. 

세부적인 공격 사례는 차치해두고 이제 방어 방법을 알아볼까요? 저희는 역으로 방어 방법 속에서 공격을 유추하는 과정을 통해 다양한 각도에서 공격이 가지는 의의를 알아 볼까 합니다.

{% include advertisements.html %}

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
