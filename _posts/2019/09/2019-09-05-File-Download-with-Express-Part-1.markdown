---
layout: post
title:  "(파트-1) Express.js 를 이용한 파일 다운로드 서버 개발"
subtitle: "CentOS 7 에 Nginx 를 설치하기"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-09-05 00:00:00 +0900
categories: [ "download", "tempurl", "express.js", "15m"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 Express.js 를 이용하여 파일 다운로드 서버를 구축하고 여기에 더 나아가 임시 URL 을 통해서 특정 기간만을 파일 다운로드 할 수 있도록 개발하는 방법에 대해 알아보도록 하겠습니다.

<!--more-->

## 시작하기 전에

오늘은 nginx 와 express.js 를 이용한 파일 다운로드 서버를 구축하기 위한 서버 설정 작업에 대해 설명드리도록 하겠습니다. 이미 구성을 완료하신 분들은 **(파트-2) Express.js 를 이용한 파일 다운로드 서버 개발** 를 구독해 주시길 바랍니다. 

이번 시간을 통해 여러분은 아래의 스펙을 가진 서버를 가지게 될 것입니다.

- gzip 압축을 이용하여 대역폭을 절감하는 Nginx 서버
- 파일 다운로드 포트를 별도로 지정한 Nginx 서버

이제, nginx 서버의 아름다움을 감상하러 여행을 떠나볼까요?

<br>
{% include advertisements.html %}
<br>


## 서버 환경 체크

- CentOS 버전 확인

- [CentOS 버전 체크](https://en.wikipedia.org/wiki/CentOS#Latest_version_information)

```bash
cat /etc/centos-release
CentOS Linux release 7.6.1810 (Core)
```

- CentOS 버전 해석

`7.6.1810` 버전은 아래의 형태로 해석하면 됩니다.

`[Major Release].[Minor Release].[Asynchronous Erratum]`

<br>
{% include advertisements.html %}
<br>

## Nginx 설치

- Nginx Repository 추가

```bash
sudo yum install epel-release
```

- Nginx 설치

```bash
sudo yum install nginx
```

- Nginx 설치 위치 확인

```bash
whereis nginx
```

- 방화벽 오픈 (HTTP, HTTPS)

```
sudo firewall-cmd --permanent --zone=public --add-service=http 
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload
```

<br>
{% include advertisements.html %}
<br>

- 서버 공인 IP 찾기

아래의 명령을 실행하면 CentOS 의 아이피 정보를 빠르게 찾아줍니다.

```bash
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

- 부트타임 서비스 실행 등록

만약의 경우 서버를 재부팅해야할 수 있습니다. 이때 nginx 를 구동하도록 등록하려면 아래의 명령어로 등록해주세요.

> 💁 **TIP**: 아래의 명령어를 통해 `/etc/systemd/system/multi-user.target.wants/nginx.service` 경로의 파일을 `/usr/lib/systemd/system/nginx.service` 경로로 심볼릭 링크를 생성합니다.

<br>
{% include advertisements.html %}
<br>

```bash
$ sudo systemctl enable nginx
Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service
```

## Server 설정

- 기본 서버 Root 경로 설정

`/usr/share/nginx/html` 경로는 기본 root 디렉터리 경로임을 기억해주세요. `/etc/nginx/conf.d/default.conf` 파일을 통해서 이러한 경로를 지정합니다.

- 서버 블록 설정

`/etc/nginx/conf.d` 는 Apache 의 가상 호스트와 같은 개념입니다. Nginx는 Server Block 이라고 표현함에 주의해주세요^%^

- 전역 Nginx 설정

`/etc/nginx/nginx.conf` 는 Nginx 의 전역 설정입니다. 


## 포트 변경 설정

`/etc/nginx/nginx.conf` 파일을 편집하여 Default 포트 정보를 변경합니다.

```bash
vim /etc/nginx/nginx.conf
```

server 절의 listen 옵션을 변경해주세요.

```bash
 server {
        # listen       80 default_server;
        # listen       [::]:80 default_server;
        listen       4093 default_server;
        listen       [::]:4093 default_server;
        # skipping ...
 }
```

## 전송 파일 압축하기

파일을 빠른 속도로 다운로드 받기 위해서는 `gzip` 설정을 해주어야 합니다. 이를 통해 전송 간에 파일 크기를 줄 일 수 있어 웹 사이트 로딩 속도를 빠르게 만들 수 있습니다. 또한 bandwidth 사용량을 줄일 수 있어 더욱 경제적이에요.

gzip 은 인기있는 데이터 압축 프로그램이에요. Nginx 를 사용하여 gzip 을 사용하도록 파일을 압축할 수 있습니다. 이러한 파일은 브라우저(chrome 등의 브라우저)를 통해서 손실 없이 압축 해제되게됩니다.

압축 효율은 아래와 같습니다. 어떤가요? gzip 방식이 인기있는 이유에는 효율에 있습니다. (개발자분에게 경의를 표합니다.)

<br>
{% include advertisements.html %}
<br>

|compressor|min ratio|max ratio|min time|max time|
|:---:|:---:|:---:|:---:|:---:|
|gzip|7.01%|10.67%|14.4s|31.4s|
|bzip2|4.85%|8.01%|295s|592s|
|xz 0-3|4.68%|5.52%|48s|133s|
|xz 4-9|4.54%|5.42%|193s|755s|

### gzip 특성

파일 유형별 gzip 압축 효율을 파악하면 좋습니다.

- 텍스트 파일의 경우 2배 이상의 압축률이 있습니다.
- 이미지(JPEG, PNG 등) 파일은 이미 압축되어 있어 효율이 적습니다.

### gzip 모듈 설정하기

- `/etc/nginx/conf.d/gzip.conf` 파일을 생성

```bash
sudo vim /etc/nginx/conf.d/gzip.conf
```

- `/etc/nginx/conf.d/gzip.conf` 파일 내 아래의 설정을 입력

<br>
{% include advertisements.html %}
<br>

```bash
##
# `gzip` Settings
#
#
gzip on;
gzip_disable "msie6";

gzip_vary on;
gzip_proxied any;
gzip_comp_level 6;
gzip_http_version 1.1;
gzip_min_length 256;
gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript application/javascript application/vnd.ms-fontobject application/x-font-ttf font/opentype image/svg+xml image/x-icon;
```

### gzip.conf 설정 리딩

저 코마는 항상 이슈를 설명하기 위해 노력합니다. 위와 같이 `gzip.conf` 파일을 구성한 이유는 아래와 같습니다.
<br>
{% include advertisements.html %}
<br>

- `gzip on` 디렉티브는 Gzip 압축을 활성화 합니다. 
- `gzip_disable "msie6"` 는 IE6(Internet Explorer 6) 브라우저를 제외합니다. 이 이유는 해당 **브라우저가 gzip 압축을 지원하지 않기 때문입니다.**
- `gzip_vary and gzip_proxied` 설정은 클라이언트와 nginx 서버의 중간에 위치한 proxy 서버가 gzip 압축을 인지하고 중계하도록 설정합니다. (헤더를 추가합니다.)
- `gzip_comp_level 6` 설정은 압축률을 설정하며 6 은 최고 압축 레벨이 아닌 납득할 만한 수준의 압축률입니다.
- `gzip_http_version 1.1` 설정은 HTTP/1.1 프로토콜을 지원하는 브라우저에게만 gzip 압축을 허용합니다. 이는 호환성을 설정하는 매우 좋은 기준입니다.
- `gzip_min_length 256` 설정은 Nginx 로 하여금 256 이하의 바이트 크기 파일에 대해 압축을 하지 않도록 지정합니다. 256 바이트 이하의 경우 압축 효율이 거의 없습니다.
- `gzip_types` 목록은 압축할 MIME 타입을 지정합니다. 여기에는 웹 폰트, 이미지, xml 등의 MIME 타입을 지정하여 선택적으로 압축을 적용하도록 합니다.

### gzip 테스트

아래의 명령어를 통해 gzip 압축을 테스트 해보세요.

```bash
$ curl -H "Accept-Encoding: gzip" -I http://localhost/test.html
```
<br>
{% include advertisements.html %}
<br>

아래 응답의 헤더 중 `Content-Encoding: gzip` 를 통해 gzip 이 활성화 되었음을 확인할 수 있습니다.

```header
HTTP/1.1 200 OK
Server: nginx/1.6.3
Date: Fri, 11 Mar 2016 13:19:16 GMT
Content-Type: text/html
Last-Modified: Fri, 11 Mar 2016 12:48:02 GMT
Connection: keep-alive
Vary: Accept-Encoding
Content-Encoding: gzip
```

## 마무리

이번 시간은 Express.js 를 이용한 파일 다운로드 서버를 구성하기 전에 nginx 를 설치하고 구성하는 방법에 대해 알아보았습니다. 👏 (짝짝짝) 이 과정은 모든 문서를 정복할 때까지 매일 매일 업로드 하도록 할테니 내일 이 시간에도 시간을 내어 [코마의 훈훈한 블로그](https://code-machina.github.io) 를 찾아주세요!

다음 시간에는 아래의 내용을 완벽하게 이해하실 수 있도록 정리해보도록 하겠습니다.

- Express.js 를 Nginx 에 바인딩 하기
- Express.js 를 이용한 파일 다운로드 구현하기

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Vue.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Gzip 모듈 추가하기](https://www.digitalocean.com/community/tutorials/how-to-add-the-gzip-module-to-nginx-on-centos-7)
