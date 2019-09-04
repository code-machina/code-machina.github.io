---
layout: post
title:  "JSON 웹 토큰(JWT) 보안"
subtitle: "웹 인증 키 관리"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-02 00:00:00 +0900
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

`[Base64(HEADER)].[Base64(PAYLOAD)].[Base64(SIGNATURE)]`

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

서버 측에서 RSA 알고리즘을 통해서 다음의 구조가 성립한다고 가정해봅니다. 비동기 키(asymmetric)의 특성상 Public 키를 통해서 암호하를 하고 서버에서 Private 키를 통해서 복호화를 하는 구성을 택하게 됩니다. 

그러나, 만약 공격자가 이 알고리즘을 HMAC 으로 전환할 경우 Public Key 를 통해서 복호화와 암호화를 하게됩니다(서버 측은 암호화를 Public Key로 하기로 로직이 구성된 상태이므로). 따라서 공격자는 알고리즘을 바꿈으로써 자신에게 교부된 Public 키를 통해 JWT 를 재서명함으로써 권한 상승을 할 수 있습니다.

1. 서버 ===== Public 키 교뷰 ======> 클라이언트
2. 서버 <==== Token (Public 키 암호화 서명, HMAC, 데이터 변조) ===== 클라이언트
3. 서버 ===== Grant Admin Privs =====> 클라이언트

### 예방책(mitigation)

JWT 라이브러리 사용 시 아래의 라이브러리에 대해 주의를 기울여야 합니다.

- node-jsonwebtoken
- pyjwt
- namshi/jose
- php-jwt
- jsjwt

### 구현 샘플

아래의 샘플에서 알고리즘 사용에 대한 인가되지 않은 제어를 막기 위해 명시적으로 HMAC-256 해싱 알고리즘 사용을 선언합니다. 그리고 토큰에 대한 검증 컨텍스트를 생성하여 토큰의 유효성 검증에 사용합니다.

```java
// JVM 메모리 상에 문자열로 키 정보를 담는 컨테이너를 생성
// transient 키워드를 통해 직렬화를 차단
private transient byte[] keyHMAC = ...;

...

// 요청한 토큰에 대해 유효성 검증 컨텍스트를 생성
// 명시적으로 HMAC-256 해싱 알고리즘 사용을 선언한다.
JWTVerifier verifier = JWT.require(Algorithm.HMAC256(keyHMAC)).build();

// 생성한 컨텐스트를 바탕으로 토큰을 검증하고 만약 검증이 실패한다면 예외가 발생한다.
DecodedJWT decodedToken = verifier.verify(token)
```

## 토큰 하이재킹

공격자에 의해서 토큰이 중간에서 가로 채기를 당하거나 도난을 당하였을 경우 시스템에 대한 접근 권한을 얻기위해 악용될 수 있습니다.

### 방어 방법

이를 효과적으로 방어하기 위해서는 사용자 컨텍스트를 토큰에 생성해야 합니다. 사용자 컨텍스트 정보는 다음의 정보로 구성됨에 유의해야 합니다.

- 인증 시퀀스 동안에 생성된 무작위 문자열, 그리고 이 문자열은 토큰내에 포함되어야 합니다. 또한, 이 정보가 클라이언트에 전송될때 (HttpOnly, Secure, SameSite, cookie prefixes 와 같은 보안 설정이 갖추어야 져야 합니다.)

- 무작위 문자열의 SHA256 해시는 토큰 내에 저장되며 XSS 이슈로 인해 공격자가 임의의 문자열 값을 일거나 예상되는 쿠키를 설정하는 것을 방지할 수 있습니다.

아래는 위에서 JWT 를 보호하기 위한 HTTP 헤더 플래그를 설명한 내용입니다. (이 부분은 매우 중요하며 면접에도 자주 나오는 단골 개념들이니 꼭 유념하시길 바랍니다.)

> `Secure` 쿠키는 `HTTPS 프로토콜을 통한 암호 요청만을 통해서 전송`할 수 있습니다. 예를들어 http 통신을 하는 안전하지 않은 사이트는 Secure 디렉티브로 설정된 쿠키를 구울 수 없습니다.

> `HttpOnly` 쿠키는 자바 스크립트의 `Document.cookie` API를 통해 접근이 불가합니다. 이들은 오로지 서버로만 전송됩니다. 예를 들어 server-side 세션은 Javascript가 접근할 필요가 없습니다. 따라서 HttpOnly 플래그가 반드시 설정되어야 합니다.

> SameSite 쿠키는 서버로 하여금 쿠키가 `교차 도메인 전송`이 차단합니다. CSRF 에 대해 보안을 제공합니다. SameSite 쿠키는 상대적으로 신규 플래그이며 모든 주요 브라우저에 의해서 지원되는 사항입니다.

> 쿠키 Prefixes 는 브라우저에게 특정 속성(attribute, like Secure etc.)이 필요하다고 이야기하는 역할을 합니다. `__Secure-`이 대표적인 예입니다. 만약 `__Host-` Prefix 가 설정된 경우 `Path=/` 와 `Secure` 속성이 모두 필요함을 브라우저에게 알려줍니다. 

IP 주소는 IP 주소의 변경 이슈로 인해 사용할 수 없습니다. 또한, IP 주소의 사용은 잠재적으로 유럽 GDPR 컴플라이언스 레벨에서 이슈를 야기할 가능성이 존재합니다.

토큰 유효성 검증 동안, 수신된 토큰이 올바른 컨텍스트를 포함하지 못한다면 다시 요청을 수신하도록 하거나 서버로 부터 거절되는 구현을 선택해야 합니다.

```java
// 위와 동
private transient byte[] keyHMAC = ...;
// 임의의 데이터 생성
private SecureRandom secureRandom = new SecureRandom();

...

// 임의의 문자열을 생성 이 사용자를 위한 핑거프린트로 사용될 예정입니다
byte[] randomFgp = new byte[50];
secureRandom.nextBytes(randomFgp);
String userFingerprint = DatatypeConverter.printHexBinary(randomFgp);

// 보안 강화된 쿠키에 핑거프린트를 추가하고 쿠키를 수동으로 추가함은 SameSite 속성은 javax.servlet.http.Cookie 클래스에 의해 지원되지 않기 때문입니다. 
String fingerprintCookie = "__Secure-Fgp=" + userFingerprint 
                           + "; SameSite=Strict; HttpOnly; Secure";
response.addHeader("Set-Cookie", fingerprintCookie);

// XSS 를 예방하기 위해  SHA256 해시 값을 계산하여 핑거프린트 해시를 토큰의 데이터로 저장하고 이를 통해 핑거 프린트값을 읽어오거나 예상되는 쿠키를 설정할 수 있다.
MessageDigest digest = MessageDigest.getInstance("SHA-256"); // 해시 계산
byte[] userFingerprintDigest = digest.digest(userFingerprint.getBytes("utf-8")); // 사용자 핑거프린트 불러오기
String userFingerprintHash = DatatypeConverter.printHexBinary(userFingerprintDigest); // 헥사 바이너리로 

// 15분 유효성을 가지는 토큰을 생성하고 클라이언트 컨텍스트 정보를 생성한다.
Calendar c = Calendar.getInstance();
Date now = c.getTime();
c.add(Calendar.MINUTE, 15); // 현재의 시간에서 15분을 추가
Date expirationDate = c.getTime(); //15분이 추가된 날짜를 Date 타입으로 반환
Map<String, Object> headerClaims = new HashMap<>(); // 헤더 
headerClaims.put("typ", "JWT");
String token = JWT.create().withSubject(login)
   .withExpiresAt(expirationDate) // 만료시간 설정
   .withIssuer(this.issuerID) // 발급자 설정
   .withIssuedAt(now) // 발급 시간 설정
   .withNotBefore(now) // 이전 시간에는 뮤효화
   .withClaim("userFingerprint", userFingerprintHash) // 
   .withHeader(headerClaims)
   .sign(Algorithm.HMAC256(this.keyHMAC)); // HMAC 으로 서명
```


```java
private transient byte[] keyHMAC = ...;

...

//쿠키에서 사용자 핑거프린트 정보를 가져온다. 
String userFingerprint = null;
if (request.getCookies() != null && request.getCookies().length > 0) {
 List<Cookie> cookies = Arrays.stream(request.getCookies()).collect(Collectors.toList());
 Optional<Cookie> cookie = cookies.stream().filter(c -> "__Secure-Fgp"
                                            .equals(c.getName())).findFirst();
 if (cookie.isPresent()) {
   userFingerprint = cookie.get().getValue();
 }
}

// 수신한 핑거프린트에서 SHA256 해시를 계산하고 토큰에 저장된 핑거프린트와 비교한다.
MessageDigest digest = MessageDigest.getInstance("SHA-256"); // SHA-256 다이제스트를 가져옴
byte[] userFingerprintDigest = digest.digest(userFingerprint.getBytes("utf-8")); // utf-8 바이트 스트림으로 변환
String userFingerprintHash = DatatypeConverter.printHexBinary(userFingerprintDigest); // 이를 헥사 바이너리 문자열로 출력

// 토큰에 대한 유효성 검증 컨텍스트를 생성
JWTVerifier verifier = JWT.require(Algorithm.HMAC256(keyHMAC))
                              .withIssuer(issuerID)
                              .withClaim("userFingerprint", userFingerprintHash)
                              .build();

// 토큰을 유효성 검증하고 유효성 검증 실패 시 예외가 던져진다.
DecodedJWT decodedToken = verifier.verify(token);
```

## 사용자에 의한 명시적 토큰 철회/취소

이 문제는 JWT 에 고유한 문제이다. 토큰이 만료될 때 유효하지 않게되며 사용자는 명시적으로 토큰의 유효성 검증을 취소할 수단이 없게된다. 이는 즉 토큰이 유출될 경우 사용자는 토큰 자체를 취소할 수 없게되며 공격자를 차단할 수 없음을 의미한다.

### 예방 방법

이러한 위험으로부터 보호하는 방법은 token 의 블랙리스트를 구현하여 logout 기능을 모방하는데 사용하는 것이다. 이는 전통적인 세션 시스템에 존재하는 방법이다.

블랙리스트는 토큰의 헥사로 인코딩된 SHA256 다이제스트와 취소 일자(revokation date)를 함께 보관한다. 이 기간동안 발급된 토큰의 기간 유효성보다 상위의 우선순위를 가진다.

사용자가 logout 을 원할때 지정된 서비스가 호출되며 제공된 사용자 토큰을 블랙리스트에 넣어둔다. 그리고 나중에 어플리케이션에서 사용 시 토큰을 말소 시킨다.

- 블랙리스트 토큰과 유효기간을 저장하는 테이블을 구성한다.

```sql
create table if not exists revoked_token(jwt_token_digest varchar(255) primary key, 
revokation_date timestamp default now());
```

- 토큰 취소 관리를 아래와 같이 구성한다.

```java
// 로그아웃 시 토큰 취소를 처리
// 다중 인스턴스로 하여금 취소 토큰을 검사할 수 있도록 DB 를 사용
// DB 수준에서 클린업을 허용
public class TokenRevoker {

 /** DB 연결 */
 @Resource("jdbc/storeDS")
 private DataSource storeDS;

 /**
  * 암호화된 토큰의 헥사 값으로 인코딩된 다이제스트가 존재하는지 유효성을 검증을 취소 테이블
  * (revokation table)에서 진행
  *
  * @param jwtInHex 헥사로 인코딩된 토큰 문자열
  * @return 플래그 반환
  * @throws Exception DB 통신 도중 문제를 예외로 반환
  */
 public boolean isTokenRevoked(String jwtInHex) throws Exception {
     boolean tokenIsPresent = false; // 토큰이 존재하지 않음을 우선 가정
     if (jwtInHex != null && !jwtInHex.trim().isEmpty()) {
         // 암호화된 토큰을 디코딩
         // 0x48454C4C4F2057... 와 같은 형태
         // 바이트 배열로 변환됨
         byte[] cipheredToken = DatatypeConverter.parseHexBinary(jwtInHex);

         // 암화화 토큰의 SHA256 해시 값을 계산 
         // 해시값을 계산하기 위해 SHA-256 의 다이제스트 인스턴스를 반환
         MessageDigest digest = MessageDigest.getInstance("SHA-256"); 
         // 암호화 토큰의 다이제트를 반환
         byte[] cipheredTokenDigest = digest.digest(cipheredToken);
         // Hex 바이너리 형태로 변환
         String jwtTokenDigestInHex = DatatypeConverter.printHexBinary(cipheredTokenDigest);

         // Hex 값이 DB 에 있는지 쿼리
         try (Connection con = this.storeDS.getConnection()) {
             String query = "select jwt_token_digest from revoked_token where jwt_token_digest = ?";
             // PreparedStatement 를 사용하여 파라미터 바인딩을 걸어줌
             try (PreparedStatement pStatement = con.prepareStatement(query)) {
                 pStatement.setString(1, jwtTokenDigestInHex);
                 try (ResultSet rSet = pStatement.executeQuery()) {
                     tokenIsPresent = rSet.next();
                 }
             }
         }
     }

     return tokenIsPresent;
 }


 /**
  * Hex 로 인코딩된 암호화된 토큰의 다이제스트를 취소 테이블에 추가 (INSERT 작업)
  *
  * @param jwtInHex 헥사로 인코딩된 토큰
  * @throws Exception DB 통신 상의 오류 발생 시 리턴
  */
 public void revokeToken(String jwtInHex) throws Exception {
     if (jwtInHex != null && !jwtInHex.trim().isEmpty()) {
         // 암호돤 Hex 토큰을 바이트로 변환
         byte[] cipheredToken = DatatypeConverter.parseHexBinary(jwtInHex);

         // 암호화된 토큰의 SHA256 해시 값을 계산
         MessageDigest digest = MessageDigest.getInstance("SHA-256");
         byte[] cipheredTokenDigest = digest.digest(cipheredToken);
         String jwtTokenDigestInHex = DatatypeConverter.printHexBinary(cipheredTokenDigest);

         // DB 에서 해당 Hex 값이 존재하는지 체크
         if (!this.isTokenRevoked(jwtInHex)) {
             try (Connection con = this.storeDS.getConnection()) {
                 String query = "insert into revoked_token(jwt_token_digest) values(?)";
                 int insertedRecordCount;
                 try (PreparedStatement pStatement = con.prepareStatement(query)) {
                     pStatement.setString(1, jwtTokenDigestInHex);
                     insertedRecordCount = pStatement.executeUpdate();
                 }
                 if (insertedRecordCount != 1) {
                     throw new IllegalStateException("Number of inserted record is invalid," +
                     " 1 expected but is " + insertedRecordCount);
                 }
             }
         }

     }
 }
```

## 토큰 정보 노출

공격자가 토큰에 접근하여 정보를 추출하는 방식이다. 이는 시스템에 대한 정보를 얻기 위한 방법입니다. 정보의 예는 보안 권한, 로그인 유형 등이 있습니다.

### 예방 방법

보호의 방법은 동기 알고리즘을 이용하여 토큰을 암호화하는 것이다. 그러나 이러한 공격 유형에는 Padding Oracle 이 있다. 보안 목적을 모두 달성하기 위해서는 AES-GCM([Galois/Counter Mode](https://en.wikipedia.org/wiki/Galois/Counter_Mode)) 알고리즘을 사용합니다. 패딩 오라클이 궁금하다면 아래의 링크를 참조해 주세요. Jupyter 노트북을 통해 원리를 완벽히 분석해 놓았습니다.
보호의 방법은 동기 알고리즘을 이용하여 토큰을 암호화하는 것입니다. 그러나 이러한 공격 유형에는 Padding Oracle 이 있다. 보안 목적을 모두 달성하기 위해서는 AES-GCM 알고리즘을 사용합니다. 패딩 오라클이 궁금하다면 아래의 링크를 참조해 주시길 바랍니다. Jupyter 노트북을 통해 원리를 완벽히 분석해 놓았습니다.

- [Jupyter Notebook을 이용한 Padding Oracle 완전 분석](https://github.com/code-machina/TheoremToReal/blob/master/Padding-Oracle-Attack.ipynb)

암호화는 내부 정보를 감추는 수단으로 사용됩니다. 그러나 JWT 토큰을 변조하는데 있어 첫번째 방어 수단은 시그니처(signature)입니다. 토큰 시그니처(signature)와 유효성 검증(verification)은 언제나 준비되어 있어야 합니다.

### 구현 샘플

- 토큰 암호화
    - `Google Tink` 는 암호 라이브러리입니다. 암호화 작업을 처리할 때 사용합니다.
    - google Tink 라이브러리에서 `Primitives` 는 암호 작업을 말합니다. 
        - `Aead aead = AeadFactory.getPrimitive(keysetHandle);` 와 같이 사용됩니다.
        - 구글은 primitives 를 가리켜 `cryptographic tasks` 라고 표현합니다. [key,keyset and keysethandle](https://github.com/google/tink/blob/master/docs/KEY-MANAGEMENT.md#key-keyset-and-keysethandle)
        - 여기서 Primitives 란 원초라는 의미로 해석이됩니다.
        - 또한 프로그래밍 언어에서 Primitive Data Type 가 있죠, int, string 등 기본 제공 타입들입니다. 
        - 즉, 암호화에 있어서 원초적이고 기본이되는 것은 암호화 스펙입니다. 따라서, Primitive 라는 단어를 사용한 것이 아닌가 하는 생각이 드네요. (추측이지만, 저는 확신합니다 👓🚦)

- Tink 를 활용한 대칭키 알고리즘(Symmetric Algorithm) 구현 예입니다.

```java
import com.google.crypto.tink.DeterministicAead;
import com.google.crypto.tink.KeysetHandle;
import com.google.crypto.tink.daead.DeterministicAeadKeyTemplates;

// 1. keyset 을 생성합니다. (대칭키로 생성)
KeysetHandle keysetHandle = KeysetHandle.generateNew(
    DeterministicAeadKeyTemplates.AES256_SIV);

// 2. Primitives 를 생성합니다.
DeterministicAead daead =
    keysetHandle.getPrimitive(DeterministicAead.class);

// 3-1. Primitive 를 이용해 평문(Plain text)을 암호화 합니다.
byte[] ciphertext = daead.encryptDeterministically(plaintext, aad);

// 3-2. 암호문을 복호화합니다.
byte[] decrypted = daead.decryptDeterministically(ciphertext, aad);
```

- Deterministic AEAD Primitive 란

바로 전에 소스코드를 본다면 분명 Deterministic 이라는 단어가 자주 등작하는 것을 알 수 있습니다. [Deterministic Authenticated Encryption with Associated Data](https://github.com/google/tink/blob/master/docs/PRIMITIVES.md#deterministic-authenticated-encryption-with-associated-data) 문서를 살펴보면 그 의미를 명확히 챙겨가실수 있습니다. 

좀더 상술하자면 Deterministic AEAD 는 DAEAD 라고 줄여 쓰며, 그 의미는 명확합니다. 아래의 문구를 보시죠.

**동일한 데이터를 암호화 한다면 항상 동일한 암호문을 출력한다. (Encrypting the same data always yields the same ciphertext)** 그리고 암호화된 데이터를 탐색하는 스키마나 키를 랩핑하는 용도에 유용하니다.

> AEAD 는 **A**uthenticated **E**ncryption with **A**ssociated **D**ata 의 약자입니다. 


- AES-GCM 을 이용한 토큰 암호화 


```java
/**
 * AES-GCM을 이용하여 토큰의 암호화와 복호화를 처리합니다.
 *
 * @see "https://github.com/google/tink/blob/master/docs/JAVA-HOWTO.md"
 */
public class TokenCipher {

    /**
     * 생성자 - AEAD 설정을 등록합니다.
     *
     * @throws Exception AEAD 설정 등록 과정에 문제가 발생할 경우 예외 전달
     */
    public TokenCipher() throws Exception {
        AeadConfig.register();
    }

    /**
     * JWT를 암호화
     *
     * @param jwt          암호화할 토큰
     * @param keysetHandle keyset 핸들러에 대한 레퍼런스(포인터)
     * @return HEX로 인코딩된 토큰의 암호 문자열
     * @throws Exception 토큰 암호화 작업 동안 이슈 발생 시 에외 전달
     */
    public String cipherToken(String jwt, KeysetHandle keysetHandle) throws Exception {
        // 파라미터 유효성 검증
        if (jwt == null || jwt.isEmpty() || keysetHandle == null) {
            throw new IllegalArgumentException("Both parameters must be specified !");
        }

        // AEAD 팩토리를 통해 Primitive 를 반환
        Aead aead = AeadFactory.getPrimitive(keysetHandle);

        //Cipher the token
        byte[] cipheredToken = aead.encrypt(jwt.getBytes(), null);

        return DatatypeConverter.printHexBinary(cipheredToken);
    }

    /**
     * JWT 를 복호화 합니다.
     *
     * @param jwtInHex     헥스로 인코딩된 암호화 토큰 데이터
     * @param keysetHandle Keyset 핸들에 대한 레퍼런스(포인터)
     * @return  평문으로 변환된 토큰
     * @throws Exception 복호화 과정에서 에러 발생 시 에외를 반환(throw)
     */
    public String decipherToken(String jwtInHex, KeysetHandle keysetHandle) throws Exception {
        // 파라미터 검증
        if (jwtInHex == null || jwtInHex.isEmpty() || keysetHandle == null) {
            throw new IllegalArgumentException("Both parameters must be specified !");
        }

        // 암호화 토큰을 디코딩 (=> byte array)
        byte[] cipheredToken = DatatypeConverter.parseHexBinary(jwtInHex);

        // Primitive 를 반환
        Aead aead = AeadFactory.getPrimitive(keysetHandle);

        // 토큰을 복호화
        byte[] decipheredToken = aead.decrypt(cipheredToken, null);

        // 복호화된 토큰을 반환
        return new String(decipheredToken);
    }
}
```

- 암호 토큰 생성 및 검증

```java

// JVM 메모리 내에서 문자열을 키로써 저장하는 것을 피하기 위해 text/json 파일들을 이용한 설정 파일로부터 키를 로드한다.
private transient byte[] keyHMAC = Files.readAllBytes(Paths.get("src", "main", "conf", "key-hmac.txt"));
private transient KeysetHandle keyCiphering = CleartextKeysetHandle.read(JsonKeysetReader.withFile(
Paths.get("src", "main", "conf", "key-ciphering.json").toFile()));

...

// 토큰 암호화 핸들러
TokenCipher tokenCipher = new TokenCipher();
```

- 토큰 생성

```java
// JWT API 를 이용하여 JWT 토큰을 생성
// 토큰을 암호화 (JSON 문자열 표현)
String cipheredToken = tokenCipher.cipherToken(token, this.keyCiphering);
// HEX 로 인코딩한 암호화 문자열을 클라이언트에게 HTTP 응답을 통해 전송한다.
```

- 토큰 유효성 검증

```java
// HEX 로 인코딩된 암호화된 토큰을 가져온다.
// 토큰을 복호화한다.
String token = tokenCipher.decipherToken(cipheredToken, this.keyCiphering);
// 접근권한 검증
// JWT API 를 사용하여 토큰 유효성 검증
```

## 클라이언트 기반의 토큰 저장소

- 브라우저를 이용한 자동 전송 (Cookie 스토리지)
- 브라우저가 재시작할지라도 쿠키 자동 전송 (localStorage 컨테이너를 사용)
- XSS 이슈에 대해 

### 예방 방법

1. sessionStorage 컨테이너를 사용하여 토큰을 보관
2. 서비스 호출 시 Javascript 를 이용하여 Bearer를 추가
3. 토큰 상에 핑거프린트 정보를 추가

## 토큰 하이재킹

이 공격은 토큰이 인터셉트되거나 도난을 당하였을 때, 유저의 컨텍스트 정보를 토큰에 추가하는 방법이 있습니다. 목표로하는 사용자 신원정보를 사용하여 시스템에 접속 권한을 얻는 위협입니다.

### 방어 방법

보호 방법은 사용자 컨텍스트를 토큰에 추가하는 것입니다. 사용자 컨텍스트는 다음의 정보로 구성됩니다. 

- 임의의 문자열 (인증 과정에서 생성되며 토큰 내에 포함되어야 합니다. 그리고 전송 시에는 보안이 강화된 쿠키 플래그를 통해 전송됩니다.)
- 임의 문자열의 SHA256 해시는 토큰 내에 저장합니다,. XSS 이슈 발생 시 임의의 문자열 값을 읽어들이고 예상되는 쿠키 정보를 설정하는 시도를 방어할 수 있습니다.


```javascript
  // JWT 토큰을 위한 요청을 처리하고 로컬 스토리지를 위한 요청을 처리
 function getToken(){
     var login = $("#login").val();
     var postData = "login=" + encodeURIComponent(login) + "&password=test";

     $.post("/services/authenticate", postData,function (data){
         if(data.status == "Authentication successful !"){
             ...
             sessionStorage.setItem("token", data.token);
         }else{
             ...
             sessionStorage.removeItem("token");
         }
     })
     .fail(function(jqXHR, textStatus, error){
             ...
         sessionStorage.removeItem("token");
     });
 }
```

```javascript
// JWT 토큰 유효성 검증을 위한 요청을 처리
 function validateToken(){
     // sessionStorage 접근
     var token = sessionStorage.getItem("token");

     if(token == undefined || token == ""){
         $("#infoZone").removeClass();
         $("#infoZone").addClass("alert alert-warning");
         $("#infoZone").text("Obtain a JWT token first :)");
         return;
     }

     $.ajax({
         url: "/services/validate",
         type: "POST",
         beforeSend: function(xhr) {
             // Bearer 를 통해서 전송
             xhr.setRequestHeader("Authorization", "bearer " + token);
         },
         success: function(data) {
           ...
         },
         error: function(jqXHR, textStatus, error) {
           ...
         },
     });
 }
```

## 토큰의 약한 암호화 키(secret)

secret 이 HMAC SHA256 알고리즘에 사용되고 이 해시 값이 token 시그니처에 사용되는 경우 만약 secret 이 취약하다면 bruteforcing 의 위험에 노출될 수 있다.

- [HMAC Secret의 브루트포스 공격 예시](https://www.notsosecure.com/crafting-way-json-web-tokens/)

### 예방 방법

screte 의 강도(strength)를 높이는 방법으로 Alphanumeric + special characters 를 조합한다.

```
A&'/}Z57M(2hNg=;LE?~]YtRMS5(yZ<vcZTA3N-($>2j:ZeX-BGftaVk`)jKP~q?,jk)EMbgt*kW'(
```


```java
 /**
 * 
 * 토큰을 서명하는데 사용된 secret 과 secret 을 매칭하는 것을 테스트
 *
 * @param token JWT 토큰
 * @param secret 테스트할 secret
 * @return null 을 리턴하거나 디코딩된 토큰을 리턴
 */
private DecodedJWT checkSecret(String token, String secret) {
     DecodedJWT t = null;
     try {
         Algorithm algorithm = Algorithm.HMAC256(secret);
         JWTVerifier verifier = JWT.require(algorithm).build();
         t = verifier.verify(token);
     } catch (JWTVerificationException | UnsupportedEncodingException e) {
         // 에러 발생 시 무시하고 null 을 반환
     }
     return t;
 }
```

- secret 딕셔너리를 테스트 기반으로 토큰을 평가하는 코드 스닛핏

```java
final String tokenTestBase = ...;
final String[] secret = new String[1];
final DecodedJWT[] decodedToken = new DecodedJWT[1];
List<String> secrets = Files.readAllLines(Paths.get("secrets-dictionary.txt"));
secrets.parallelStream().forEach(s -> {
 DecodedJWT tentative = checkSecret(tokenTestBase, s);
 if (tentative != null) {
   secret[0] = s;
   decodedToken[0] = tentative;
 }
});
```

## 마무리

지금까지 프론트 앱을 Microservice 구현을 위해 컨테이너화를 하고 Nginx, Vue 구성이 제대로 동작하는지 확인하였습니다. 다음 장에는 Backend 를 구성하여 진정한 API Gateway 패턴을 구현해 보도록 하겠습니다. 지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/JSON_Web_Token_Cheat_Sheet_for_Java.md

