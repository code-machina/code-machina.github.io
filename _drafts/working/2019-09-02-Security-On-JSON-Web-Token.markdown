---
layout: post
title:  "JSON 웹 토큰(JWT) 보안"
subtitle: "웹 인증 키 관리"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-08-12 00:00:00 +0900
categories: [ "docker", "security", "hardening"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. JSON Web 토큰 보안을 주제로하여 자세히 내용을 설명해 드리겠습니다. 😺

<!--more-->

## 개요

많은 응용프로그램들이 JSON 웹 토큰(JSON Web Tokens, 이하 JWT)을 기반으로 인증(authetication) 이후에 신원(identity)를 증명하기 위해 JWT 를 사용하고 있습니다. 그러나 JWT 기술을 정확히 이해하고 이를 올바르게 사용하지 않는다면 자칫 여러분의 서비스의 보안 위험을 초래할 수 있어 이 글을 쓰게되었습니다.

이글을 통해 JWT 란 무엇이며 어떠한 특성을 가지고 있는지 정리하며 어떻게 활용되는지를 정리한 뒤에 보안 위험성을 식별하고 위험을 방지하는 전략을 소개해 드리도록 하겠습니다.

<br> {% include advertisements.html %} <br>

## JWT 란?

RFC7519를 통해 JWT는 상호간의 정보를 안전하게 전송하는데 사용되는 기술입니다. JSON객체를 이용하며 압축적이고 스스로 무결함을 입증할 수 있는 방식이므로 효율성이 높습니다. 즉, 디지털 서명을 통해 검증 가능하고 신뢰할 수 있는 정보를 운반합니다. JWT 는 비밀키를 사용하여 서명되는데 이 때 HMAC 알고리즘을 사용합니다. 또한 RSA 를 이용한 공개키/비밀키 쌍을 이용할 수 있습니다.

JWT 는 주로 신원(identity) 정보와 클라이언트의 요청과 관련된 정보를 운반하는데 사용됩니다. 이 컨테이너는 서버에 의해서 서명되며 클라이언트의 조작을 사전에 방지합니다. 

예를 들어 일반 사용자 역햘을 관리자 역할로 승격시키거나 로그인 상태를 변경을 시도할 경우 이를 차단할 수 있습니다. (보안적 기능)

이 토큰은 인증(authentication)을 통해서 생성됩니다. 그리고 서버에의해 검증됩니다. 어플리케이션에 의해 아이디 카드(컨테이너라고 불러도 무방)처럼 서버에게 제출 할 수 있으며 서버는 무결성(integrity)와 유효성(validity)를 안전한 방식으로 검증할 수 있습니다. 

이 모든 과정이 휴대성(portability)과 비상태성(stateless) 상태로 이루어지는 JWT 속성으로 인해 클라이언트와 서버 기술의 분리가 용이합니다.

## JWT의 구조

JWT.IO 에 따르면 토큰은 아래의 구조를 갖추고 있습니다. 크게 3개의 파트로 분리됩니다. 또한, 전송을 위해 base64를 통해서 인코딩하여 전송 용이성을 높였습니다.

`[Base64(HEADER)].[Base64(PAYLOAD)].[Base64(SIGNATURE)]

- 헤더 영역(HEADER)

```json
{
  "alg": "HS256", // 알고리즘
  "typ": "JWT" // 유형
}
```

- 데이터 영역(PAYLOAD)

```json
{
  "sub": "1234567890", // 사용자 고유 번호
  "name": "John Doe", // 사용자 이름
  "admin": true // 관리자 역할 여부
}
```

- 시그니처 영역(Signature)

```javascript
// 헤더 영역과 데이터 영역을 결합한 데이터를 서버 비밀키(Secret)을 통해 HMAC 알고리즘으로 암호화합니다.
HMACSHA256( base64UrlEncode(header) + "." + base64UrlEncode(payload), KEY )
```

## JWT 고려사항

JWT 토큰이 사용하기 쉽고 stateless 한 방법으로 서비스를 노출 시킬 수 있지만, 모든 경우에 있어 해결책은 아닙니다. 만약에 어플리케이션이 완전히 stateless 한 상태를 유지할 필요가 없다면 여러분은 전통적인 session 시스템을 통해서 여러분의 웹 프레임워크를 구성할 수 있습니다. 


## None 해싱 알고리즘

이 경우는 공격자(attacker)가 해싱 알고리즘을 none 으로 변경하는 것을 의미합니다. 몇몇 라이브러리들은 none 알고리즘으로 서명된 토큰을 유효한 토큰으로 인식하는 경우가 있습니다. 따라서 공격자는 토큰의 요청(claim)을 변조하고 이는 어플리케이션에 의해서 신뢰되는 경우입니다.

## 마무리

지금까지 프론트 앱을 Microservice 구현을 위해 컨테이너화를 하고 Nginx, Vue 구성이 제대로 동작하는지 확인하였습니다. 다음 장에는 Backend 를 구성하여 진정한 API Gateway 패턴을 구현해 보도록 하겠습니다. 지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/JSON_Web_Token_Cheat_Sheet_for_Java.md

