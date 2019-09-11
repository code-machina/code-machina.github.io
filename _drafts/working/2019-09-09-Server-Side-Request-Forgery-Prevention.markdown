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

**공격 벡터의 일종으로 내부/외부 네트워크와 통신하는 어플리케이션을 악용함을 의미합니다.**

대표적으로 URL 처리 과정에서 이러한 공격 벡터가 드러나는데요. 아래의 예를 살펴보겠습니다.

1. 외부 서버의 이미지 URL
  - 만약 어플리케이션이 다른 서비스의 이미지를 다운로드하여 저장한다고 가정하게 될 경우 공격 벡터가 보입니다.
2. 사용자 정의 웹훅(Webhook)
  - 웹훅은 Git, Jenkins 에서도 많은 쓰는 방법입니다. 특정 이벤트가 발생 시 지정된 URL로 콜백 리퀘스트를 전송하여 특정 이벤트의 발생을 다른 서비스에 알리는 경우입니다.
3. 내부까지 전파되는 사용자 요청
  - 즉, 어플리케이션이 내부 서비스까지 요청을 전파하는 경우입니다.

### 기억해야할 사항
  - SSRF 는 프로토콜에 제한을 받지 않습니다. 즉, FTP, SMB, SMTP 등의 다른 프로토콜이나 `file://`, `phar://`, `gopher://` 등의 URI 스킴(URI Scheme)등을 사용 가능합니다.
  - 만약 어플리케이션이 XXE 인젝션 공격에 취약하다면 SSRF 공격을 검토해볼만 합니다. 
    - XXE 인젝션 공격을 이미 코마는 다루었습니다. 이 [XXE 인젝션 방어]({% link _posts/2019/07/2019-07-28-How-to-prevent-XXE Injection.markdown %}) 참조해주세요! 

## SSRF 발동 조건

어플리케이션의 기능과 요구사항에 따라 SSRF 공격이 발생하는 케이스를 두 가지로 분류할 수 있습니다.

1. **사례 1**: 어플리케이션이 신뢰하는 어플리케이션에게 요청을 전송할 수 있을 때
2. **사례 2**: 어플리케이션이 어떠한 외부 IP 나 도메인으로 요청을 전송할 수 있을 때

## 사례 1

어플리케이션은 상황에 따라 다른 어플리케이션에게 요청을 전달할 필요가 있습니다. 다른 어플리케이션의 위치는 다른 네트워크 대역이거나 외부 서비스인 경우 등 다양합니다. 

이러한 구성은 모두 특정한 목적 혹은 비즈니스 요구사항에 따라서 달라집니다. 예를들어 쇼핑몰 A와 쇼핑몰 B의 상품 정보를 공유하는 제휴를 맺었다고 가정해보면 이해가 쉽습니다.

### HR 사례

여러분들이 주로 사용하였을만한 그룹웨어 시스템을 알아볼까요? 아래의 조건에 유념해 주세요.

1. 신규 그룹웨어는 HR 시스템에 접근하여 임직원의 프로필 정보를 요청한다.
2. 임직원은 HR 시스템에 접근할 수 없다.
3. 그룹웨어와 HR 시스템은 프로토콜을 통해서 통신한다.

만약에 여러분이 보안 담당자의 입장이고 공격자가 신규 그룹웨어를 악용할 가능성을 검토하고자 합니다. 일반적인 상황에서 임직원은 HR 시스템에 접근이 불가능합니다. 그러나 그룹웨어를 통해서 파라미터를 변조한다면 HR 시스템은 그룹웨어를 완전히 신뢰하고 요청한 정보를 제공할 수 밖에 없습니다.

### 방어 방법

이러한 류의 공격에 대해 시스템을 안전하게 보호하는 방법은 어플리케이션 계층과 네트워크 계층에 대해 각각 보안 강화(Hardening)를 합니다.

- 어플리케이션 계층

첫번째로 떠오르는 것은 입력값 검증(Input Validation)일 것입니다. 여기서 Orange Tsai는 화이트리스트 기반의 입력값 검증방법을 제안한다. 

내부 어플리케이션에 전송되는 요청은 아래의 정보로 구성됩니다.

- 문자열이 포함된 데이터
- IP 주소 정보
- 도메인 이름
- URL 정보

### 문자열

SSRF 관점에서 검증 코드가 적절한 위치에서 동작하여 합니다. 아래의 정규표현식을 통한 문자열 데이터 검증 과정을 살펴볼까요?

```java
// 단순한 포맷의 데이터에 대한 정규식 검증
if(Pattern.matches("[a-zA-Z0-9\\s\\-]{1,50}", userInput)){
    // 유효할 경우 처리를 지속
}else{
    // 그렇지 않을 경우 요청을 거절하고 처리를 중지
}
```

### 아이피 주소

SSRF 관점에서 아이피 주소 데이터에 대해 아래의 검증이 필요합니다.

1. IP 정보가 유효한 V4 또는 V6 주소인지를 확인
2. IP 주소가 신뢰하는 어플리케이션의 주소 인지 확인

그렇다면, IP 검증을 편리하게 해주는 언어별 라이브러리 목록을 한변 살펴볼까요? 여러분에게 좋은 정보를 제공할 수 있을 듯하여 표로 정리해 보았습니다. (👏👏👏)

|언어|라이브러리|비고|
|:---:|:---:|:---:|
|Java|[Apache : Common Validator](http://commons.apache.org/proper/commons-validator/)|`InetAddressValidator.isValid` 메서드를 이용한 검증|
|.NET|.NET SDK 이용|[.NET SDK: IPAddress.TryParse](https://docs.microsoft.com/en-us/dotnet/api/system.net.ipaddress.tryparse?view=netframework-4.8) 참고|
|Javascript|[NPM : ip-address](https://www.npmjs.com/package/ip-address)|SDK 문서를 참조|
|Python|[ipaddress 모듈](https://docs.python.org/3/library/ipaddress.html)| SDK 문서를 참조|
|Ruby|[IPAddr 모듈](https://ruby-doc.org/stdlib-2.0.0/libdoc/ipaddr/rdoc/IPAddr.html)|SDK 문서를 참조|

위에서 언급한 라이브러리를 사용하여 출력된 결과 값을 IP 주소로 사용합니다. 그리고 화이트리스트 목록과 비교하여 유효성 검증을 할 경우 안전성이 높아집니다.

### 도메인 이름

도메인 이름을 기반으로 신뢰할 수 있는 호스트를 판별하는 과정은 아래의 우려 사항이 존재합니다.

- DNS 서버 설정을 이용한 공격을 통해 DNS Resoultion 과정에 개입

1. 이는 외부 DNS Resolver 로 정보를 노출할 수 있습니다.
2. 내부 IP 주소로 유효한 도메인 이름을 바인딩하는 공격에 악용
  - Exploit > 우회 > 입력값 검증 > DNS 피닝
3. 내부 DNS Resolver 에 악성 데이터(Payload)를 전달

SSRF 관점에서 아래의 유효성 검증을 생각할 수 있습니다.

1. 유효한 도메인 이름인지를 검증
2. 화이트리스트 기반의 도메인 이름에 속하는지 체크

그렇다면 이러한 검증을 도와주는 라이브러리엔 무엇이 있을까요? 아래의 테이블로 정리해보았습니다.

|언어|라이브러리|비고|
|:---:|:---:|:---:|
|Java|[Apache : Common Validator](http://commons.apache.org/proper/commons-validator/)|`DomainValidator.isValid` 메서드를 이용한 검증|
|.NET|.NET SDK 이용|[.NET SDK: Uri.CheckHostName](https://docs.microsoft.com/en-us/dotnet/api/system.uri.checkhostname?view=netframework-4.8) 참고|
|Javascript|[NPM : ip-valid-domain](https://www.npmjs.com/package/is-valid-domain)|SDK 문서를 참조|
|Python|[validators.domain 모듈](https://validators.readthedocs.io/en/latest/#module-validators.domain)| SDK 문서를 참조|
|Ruby|아쉽게도 없습니다.|하단을 참조|

- Ruby 취약점 벡터

domainator, public_suffix, addressable 등은 아래의 도메인 이름을 유효한 도메인 이름으로 치부합니다.
즉, 공격 성공 가능성이 높음을 의미합니다.

```html
<script>alert(1)</script>.owasp.org
```

Ruby 개발자분들은 아래의 코드를 참조하셔서 적절히 도메인 유효성 검증을 수행하시면 됩니다.

`^(((?!-))(xn--|_{1,1})?[a-z0-9-]{0,61}[a-z0-9]{1,1}\.)*(xn--)?([a-z0-9][a-z0-9\-]{0,60}|[a-z0-9-]{1,30}\.[a-z]{2,})$` ([출처: StackOverflow.com](https://stackoverflow.com/questions/10306690/what-is-a-regular-expression-which-will-match-a-valid-domain-name-without-a-subd/26987741#26987741))

실제 코드는 아래와 같습니다.

```ruby
domain_names = ["owasp.org","owasp-test.org","doc-test.owasp.org","doc.owasp.org", 
                "<script>alert(1)</script>","<script>alert(1)</script>.owasp.org"]
domain_names.each { |domain_name|
    if ( domain_name =~ /^(((?!-))(xn--|_{1,1})?[a-z0-9-]{0,61}[a-z0-9]{1,1}\.)*(xn--)?([a-z0-9][a-z0-9\-]{0,60}|[a-z0-9-]{1,30}\.[a-z]{2,})$/ )
        puts "[i] #{domain_name} is VALID"
    else
        puts "[!] #{domain_name} is INVALID"
    end
}
```

유효한 도메인 이름인지를 검증하였으니 이제 두번째 단계의 검증을 합니다.

1. 신뢰 할 수 있는 어플리케이션의 도메인 이름을 화이트 리스트 처리
2. 도메인 이름이 화이트 리스트에 포함되는지 체크

그러나 DNS Pinning 을 이용한 공격에는 여전히 취약합니다. 즉, 비즈니스 코드가 실행될 경우 DNS Resolution이 발생하기 때문이죠. 이러한 문제를 사전에 차단하기 위해서는 아래의 조치들을 해주어야 합니다.

1. DNS Resolver 체임을 구성 할 때, 내부 DNS 서버를 첫번째 순서로 둡니다.
2. 도메인 화이트리스트를 감시하여 이들 중 아래에 해당하는 경우를 감지합니다.
  - 로컬 IP (v4 또는 v6)
  - 내부망 IP

```python
# Dependencies: pip install ipaddress dnspython
import ipaddress
import dns.resolver

# 도메인 화이트리스트 구성
DOMAINS_WHITELIST = ["owasp.org", "labslinux"]

# DNS 질의를 전달할 DNS Resolver 를 구성
DNS_RESOLVER = dns.resolver.Resolver()
DNS_RESOLVER.nameservers = ["1.1.1.1"] # 가상의 아이피 입니다.

def verify_dns_records(domain, records, type):
    """
    공인이 아닌 아이피 주소에 대해 DNS 레코드 조회를 하며 
    에러 발생 여부를 통해 Boolean 값을 리턴한다.
    """
    error_detected = False
    if records is not None:
        for record in records:
            value = record.to_text().strip()
            try:
                ip = ipaddress.ip_address(value)
                # See https://docs.python.org/3/library/ipaddress.html#ipaddress.IPv4Address.is_global
                if not ip.is_global:
                    print("[!] DNS record type '%s' for domain name '%s' resolve to 
                    a non public IP address '%s'!" % (type, domain, value))
                    error_detected = True
            except ValueError:
                error_detected = True
                print("[!] '%s' is not valid IP address!" % value)
    return error_detected
            
def check():
    """
    도메인 화이트 리스트를 체크한다. 에러가 발생할 경우 불린값을 리턴한다.
    순서
    1. A 레코드를 조회 
    2. AAAA 레코드를 조회

    """
    error_detected = False
    for domain in DOMAINS_WHITELIST:    
        # Get the IPs of the curent domain
        # See https://en.wikipedia.org/wiki/List_of_DNS_record_types
        try:
            # A = IPv4 address record        
            ip_v4_records = DNS_RESOLVER.query(domain, "A")
        except Exception as e:
            ip_v4_records = None            
            print("[i] Cannot get A record for domain '%s': %s\n" % (domain,e))        
        try:
            # AAAA = IPv6 address record
            ip_v6_records = DNS_RESOLVER.query(domain, "AAAA")
        except Exception as e:
            ip_v6_records = None
            print("[i] Cannot get AAAA record for domain '%s': %s\n" % (domain,e))
        # Verify the IPs obtained
        if verify_dns_records(domain, ip_v4_records, "A") 
        or verify_dns_records(domain, ip_v6_records, "AAAA"):
            error_detected = True
    return error_detected

if __name__== "__main__":
    if check():
        # 에러가 검출되면 True 이므로 비정상 호스트임을 의미
        exit(1)
    else:
        # 정상인 경우 0 값을 리턴
        exit(0)
```


### URL

**URL 정보를 입력값으로 받아들이는 경우는 지양해야합니다**. 이는, URL 을 검증하는 과정이 매우 어렵고 파서를 악용할 가능성이 높기 때문입니다. 만약 네트워크 정보가 필요하다면, 유효한 IP 주소 또는 도메인 이름을 검증하도록 구현합니다.

### 네트워크 계층

네트워크 계층의 보안은 취약한 어플리케이션이 무분별하게 다른 어플리케이션을 호출하는 것을 예방하기 위함입니다. 

따라서, 허용된 라우트를 통해서만 어플리케이션이 접근할 수 있도록 구성합니다. 이때 방화벽을 이용하여 접근제어를 구성합니다.

<br>
[![SSRF 공격 구성도 2](https://raw.githubusercontent.com/OWASP/CheatSheetSeries/master/assets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet_Case1_NetworkLayer_PreventFlow.png)](https://raw.githubusercontent.com/OWASP/CheatSheetSeries/master/assets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet_Case1_NetworkLayer_PreventFlow.png)
<br>

<br>
{% include advertisements.html %}
<br>

## 사례 2

**사례1**의 내용이 길다보니 사례2의 내용을 상기해 보도록 하겠습니다.

어플리케이션이 어떠한 외부 IP 나 도메인으로 요청을 전송하는 구성인 경우를 다루도록 하겠습니다. 




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
