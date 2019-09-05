---
layout: post
title:  "Docker 보안 강화 Part 1"
subtitle: "Docker Security Hardening Part-1"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-08-12 00:00:00 +0900
categories: [ "docker", "security", "hardening"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 Docker 보안 강화에 대해서 알아보도록 하겠습니다. 😺

<!--more-->

## 개요

도커는 가장 인기있는 컨테이너링 기술입니다. 호스트에서 곧바로 어플리케이션을 구동하는 방식에 비해 향상된 보안 수준을 제공할 수 있습니다. 그러나, 이러한 도커도 잘못 구성될 경우 보안상 취약점을 노출할 수 있습니다. 저 코마는 여러분에게 쉽고도 편리하게 도커의 보안 수준을 유지하는 방안을 소개해 드리고자 합니다.

{% include advertisements.html %}

## 소개 방식

보안에는 항상 우려 사항(concern)이라는 것이 문두에 오게됩니다. 만약 ~ 하면 ~ 해질 위험이 있다고 보통 표현하게됩니다. 이는 보안이 항상 임의의 나쁜 행위자가 있음을 염두하기 때문에 나타나는 사고 방식입니다.

그리고 이러한 사고방식은 보안 상의 위험을 미리 예측하고 기민하게 대응하도록 도와줍니다. 따라서, 저 코마는 이 사고 방식에 따라 **발생할 위험에 대해 Docker 보안 강화 방안을 설명드리고자 합니다.**

## 컨테이너 탈출 위험

영어를 그대로 옮겨 적다보니 표현이 조금 어색합니다. 그러나 말하고자 하는 바는 명확합니다. 컨테이너를 탈출하여 호스트 머신에 접근하는 행위는 대표적인 위험에 속합니다. 이것은 알려진 방식과 알려지지 않은 방식으로 나눌 수 있는데요.

알려지지 않은 방식은 확률 측면에서 다수의 나쁜 행위자들에게 알려져 있을 가능성이 높지 않기 때문에 배제하도록 하겠습니다.

단, 중요한 점은 알려진 방식에 의해서 도커가 이러한 위험에 노출되어 있는 경우입니다. 이에, 도커 사용자 여러분들은 아래의 사항을 지킴으로써 위험이 발생할 확률을 가능한 낮추는게 좋습니다.

- 주기적인 도커 엔진과 도커 머신의 패치 관리

아래의 링크는 도커 Community Edition 의 Change Log 입니다. 네 맞습니다. 오픈 소스이므로 항상 코드가 공개되어 있기 때문에, 취약점이 발견되고 조치되는 주기가 매우 빠릅니다. 따라서, 정기적으로 이러한 이슈들을 관찰할 필요가 있습니다.

- [깃헙: 도커 ChangeLog](https://github.com/docker/docker-ce/releases/tag/v18.09.2)

이글을 쓰는 시점에서 최신 이슈를 살펴보니, 역시 발표된 취약점이 있습니다. 공개된 취약점 식별 번호는 `CVE-2019-5736` 입니다.

아래의 경로에 **CVE-2019-5736 PoC with Vagrant, Docker** 을 참조하시면 정확한 테스트 방법이 있으므로 이를 참조하시면 좋을 것 같습니다.

<!-- @TODO: Docker 컨테이너 탈출 취약점이랑 Docker 보안 하드닝관련 내용을 동시에 작성해야함. -->

<!-- - [Docker 컨테이너 탈출 취약점(CVE-2019-5736) 분석 및 테스트배드]({% link _drafts/working/2019-08-14-CVE-2019-5736-PoC-With-Vagrant-And-Docker.markdown %}) -->

- [CVE-2019-5736 : PoC](https://gist.github.com/bcb079e04c2a3101c422be07a262627c.git)
- [lxc : commit 내용](https://github.com/lxc/lxc/commit/6400238d08cdf1ca20d49bafb85f4e224348bf9d)

## 취약점 테스트

리눅스 가상 머신을 생성하고 Provisioning 시에 docker-ce 중에 버전이 낮은 것을 설치해 보겠습니다. 그리고 취약점 코드가 어떻게 동작하는지 살펴보겠습니다.

## 도커 보안 가이드

### 규칙 #0. 최신 버전 업데이트

### 규칙 #1. 도커 데몬 소켓을 노출하지 않기

`/var/run/docker.sock` 은 UNIX 소켓입니다. 이 소켓을 통해서 도커는 통신을 합니다. 그리고 이 파일은 도커 API 의 진입점입니다.

소켓 파일의 소유주는 root 입니다. 만약 이 파일에 대한 접근 권한을 가지게 된다면 여러분의 호스트에 대한 루트 접근이 가능함을 의미합니다.

- 도커 데몬을 구동할 때 TCP 도커 데몬 소켓을 사용하지 않습니다. 즉,  `-H tcp://0.0.0.0:XXX` 를 설정하지 않음을 의미합니다.
- `/var/run/docker.sock` 파일을 다른 컨테이너에 노출하지 않습니다. 만약 여러분이 `-v /var/run/docker.sock:/var/run/docker.sock` 과 같이 볼륨 바인딩을 건다면 권한 탈취의 가능성이 있습니다. 또한, Read-Only 를 설정하더라도 완벽한 방어 방법이 아닙니다.

```yml
volumes:
  - "/var/run/docker.sock:/var/run/docker.sock"
```

### 규칙 #2. 사용자 설정

docker 컨테이너 설정 시 권한이 없는 사용자 사용하도록 한다. 이를 통해 권한 상승 공격에 방어가 가능하다.

1. docker run 명령에서는 -u 옵션을 사용

```bash
docker run -u 4000 alpine
```

2. 빌드 시간 동안 Dockerfile 에 사용자를 추가하고 사용

```env
FROM alpine
RUN groupadd -r myuser && useradd -r -g myuser myuser
<HERE DO WHAT YOU HAVE TO DO AS A ROOT USER LIKE INSTALLING PACKAGES ETC.>
USER myuser
```

3. `--userns-remap=default` 설정을 통해서 namespace 지원을 활성화

리눅스 네임스페이스(namespace) 설정은 실행 중인 프로세스들을 서로 독립 시킬 수 있다. 그리고 시스템 리소스에 접근 제한을 부여할 수 있으며 이 과정에서 실행 프로세스들은 이러한 제한을 인지할 수 없습니다.

도커 공식 문서는 컨테이너 내에서 권한 상승 공격을 예방하는 최선의 방법으로 root 권한이 아닌 사용자의 권한으로 어플레케이션 컨테이너를 실행시키도록 제시하고 있습니다.

이러한 과정을 remap 이라고 부르는데, remap 과정을 통해서 UID를 0 에서 65536 사이로 부여를 받지만 실제 호스트 머신에서 권한을 가질 수 없게됩니다.

```bash
dockerd --userns-remap="testuser:testuser"
```

- remapped 된 정보를 확인

```bash
$ grep dockremap /etc/subuid

dockremap:231072:65536
```

namespace 설정을 통해 폴더 자체는 root 이지만 하위 생성된 파일들의 권한은 root 가 아닌 것을 알 수 있다.

```bash
$ sudo ls -l /var/lib/docker/231072.231072/

total 14
drwx------ 5 231072 231072 5 Jun 21 21:19 aufs
drwx------ 3 231072 231072 3 Jun 21 21:21 containers
drwx------ 3 root   root   3 Jun 21 21:19 image
drwxr-x--- 3 root   root   3 Jun 21 21:19 network
drwx------ 4 root   root   4 Jun 21 21:19 plugins
drwx------ 2 root   root   2 Jun 21 21:19 swarm
drwx------ 2 231072 231072 2 Jun 21 21:21 tmp
drwx------ 2 root   root   2 Jun 21 21:19 trust
drwx------ 2 231072 231072 3 Jun 21 21:19 volumes
```

- Kubernetes 에서 Security Context 를 통해 `runAsNonRoot` 설정을 할 수 있습니다.

```yml
kind: ...
apiVersion: ...
metadata:
  name: ...
spec:
  ...
  containers:
  - name: ...
    image: ....
    securityContext:
          ...
          runAsNonRoot: true
          ...
```

## RULE #3. 커널 레벨 역량(capabilities) 제한

리눅스 커널에서 역량(capability)라는 개념이 있습니다. ((리눅스 MAN 페이지의 capabilities 섹션 참고)[http://man7.org/linux/man-pages/man7/capabilities.7.html]) 커널 2.2 부터 superuser 의 권한을 단위로 세분화하여 capabilties 라고 부르며 이들은 서로 독립적으로 비/활성화될 수 있습니다. 이러한 capabilities 는 Thread 마다의 속성입니다. 

도커는 기본값으로 캐퍼빌러티 중에 일부만을 상속받아 구동됩니다. 하지만, 여러분은 실행 전에 옵션을 통해 이러한 캐퍼빌러티를 조정할 수 있습니다. 한번 살펴볼까요?

```bash
docker run --can-drop all --cap-add CHOWN alpine
```

`--can-drop all` 은 모든 캐퍼빌러티를 비황서화 한 뒤에 `CHOWN`에 대해서만 권한을 열어줍니다. 이는 man 페이지에서 CAP_CHOWN 와 동일한 캐퍼빌러티 속성으로 파일의 UID, GID 를 변경할 수 있는 권한 입니다. 

> ⚠ **주의**: 절대 `--privileged` 플래그를 통해서 컨테이너를 실행하지 않도록 합니다.

이러한 설정은 쿠버네티스(쿠베)를 통해서 동일하게 설정이 가능합니다. 역시 Security Context 를 통해서 설정하며 별도의 `capabilties` 필드(field)를 작성해 주면 간편하게 설정이 가능합니다.


```yml
kind: ...
apiVersion: ...
metadata:
  name: ...
spec:
  ...
  containers:
  - name: ...
    image: ....
    securityContext:
          ...
          capabilities:
            drop:
              - all
            add:
              - CHOWN
          ...
```

### RULE #4. -no-new-privileges 플래스 추가

이 또한 권한 상승 취약점을 염두해 둔 규칙입니다. 도커는 백엔드에서 컨테이너를 관리하는 데몬이다보니 권한 상승에 대해 상당한 비중을 두고 있습니다. 

도커 이미지를 실행할 때 `--seucirty-opt=no-new-privileges` 플래그를 추가하는 것을 고려해주세요. 이를 통해 setuid, setgid 코드를 포함한 바이너리를 통한 권한 상승 공격을 예방할 수 있습니다.

쿠베의 경우 Security Context 에 `allowPrivilegeEscalation` 필드를 사용합니다.

```yml
kind: ...
apiVersion: ...
metadata:
  name: ...
spec:
  ...
  containers:
  - name: ...
    image: ....
    securityContext:
          ...
          allowPrivilegeEscalation: false
          ...
```

### RULE #5. 컨테이너간 통신(ICC) 비활성화(--icc=false)

기본값으로 도커는 ICC(inter-container communication)이 활성화되어 있습니다. 이것의 의미는 모든 컨테이너들이 서로를 docker0 이라는 bridged network 를 통해서 대화할 수 있음을 의미합니다. 이는 `-icc=false` 플래그를 통해서 dockerd (도커 데몬)을 통해서 비활성화가 가능합니다. 

그러나 이러한 설정을 하게될 경우 컨테이너들 간에 대화를 위해 아래의 플래그를 일일이 설정해 주어야 한다는 불편함이 있습니다. 


`--link=CONATINER_NAME_or_ID:ALIAS` 와 같은 속성을 통해서 icc 를 비활성화 한 뒤에 통신이 가능하도록 설정할 수 있으므로 **기본 차단 후 사용 시 Open 이라는 방화벽 운영 개념** 정도로 여기면 좋을 것 같습니다.

### RULE #6. 리눅스 보안 모듈 사용 (seccomp, AppArmor, SELinux)

이 규칙은 OS 레벨의 보안 설정을 이용할 것을 말합니다. 세부 내용은 추후에 AppArmor, seccomp 순으로 글을 올리도록 하겠습니다.


### RULE #7. 리소스 제한 (memory, CPU, file descriptor, processes, restarts)

DoS 공격을 피하는 방법으로 리소스를 제한하는 방법이 있습니다. 즉, memory, CPU, 최대 재시작 제한 횟수, 파일 디스크립터의 최대 수, 프로세스의 최대 수를 제한함으로 써 DoS 공격을 피할 수 있습니다. 

- 옵션
  - `--restart=on-failure:<number_of_restarts>` : 최대 재시작 횟수
  - `--ulimit nofile=<number>` : 최대 fd(file descriptor) 갯수
  - `--ulimit nproc=<number>` : 최대 프로세스 수


### RULE #8. 파일시스템 그리고 볼륨을 읽기 전용으로 설정

컨테이너 구동 시 파일시스템에 대해 read-only 권한 설정을 `--read-only` 플래그를 통해 구현합니다. 아래의 예를 살펴볼까요?

아래의 명렁어를 구동하면 명령행 실행 결과와 같이 Read-only file 시스템이라는 오류가 발생합니다.

```bash
$ docker run --read-only alpine sh -c 'echo "Whatever"' > /tmp/whatever'
sh: can't create /tmp/whatever: Read-only file system
```

그러나 여러분들의 애플리케이션은 임시 파일을 생성할 피요가 있을 수 있습니다. 이러한 경우 `--tmpfs` 플래그를 같이 조합하면 좋습니다. 위의 명렁어는 실패하였지만, 아래의 명령어는 정상적으로 실행이됩니다. 그러나 걱정하지마세요. 이 명령어의 영향 범위는 컨테이너 내부입니다.

```bash
$ docker run --read-only --tmpfs /tmp alpine sh -c 'echo "whatever" > /tmp/file'
```

컨테이너 내에 파일이 생성된 것을 확인할 수 있습니다. 여기에서 root 권한이 기본으로 설정된 것을 볼 수 있습니다. `-u` 옵션을 통해 권한을 지정해 보도록 하겠습니다.

```bash
$ docker run --read-only --tmpfs /tmp alpine sh -c 'echo "whatever" > /tmp/file; ls -al /tmp'
total 8
drwxrwxrwt    2 root     root            60 Sep  5 01:49 .
drwxr-xr-x    1 root     root          4096 Sep  5 01:49 ..
-rw-r--r--    1 root     root             9 Sep  5 01:49 file
```

아래의 명령어를 통해서 위에서 배운 개념을 조금 섞어보았습니다. 앱에서 임시 파일을 생성하는 경우 root 권한으로 생성된다면 보안성 취약한 요소가 발생할 가능성이 존재합니다. 따라서 우리는 이러한 우려를 감쇄하기 위해서 `-u 4000:4000` 옵션을 통해 파일의 권한을 제어할 수 있습니다. 

```
$ docker run -u 4000:4000 --read-only --tmpfs /tmp alpine sh -c 'echo "whatever" > /tmp/file; ls -al /tmp'
total 8
drwxrwxrwt    2 root     root            60 Sep  5 01:51 .
drwxr-xr-x    1 root     root          4096 Sep  5 01:51 ..
-rw-r--r--    1 4000     4000             9 Sep  5 01:51 file
```

- docker-compose (도커 컴포즈)

도커 컴포즈를 통해서도 동일할 설정을 할 수 있습니다. 아래의 yml 파일 샘플을 살펴볼까요?

```yml
version: "3"
services:
  alpine:
    image: alpine
    read_only: true
```

- 쿠베 설정

쿠베에서도 `readOnlyRootFilesystem` 필드를 통해서 동일한 설정이 가능합니다.

```yml
kind: ...
apiVersion: ...
metadata:
  name: ...
spec:
  ...
  containers:
  - name: ...
    image: ....
    securityContext:
          ...
          readOnlyRootFilesystem: true
          ...
```

- 마운트 사용 시 준수 사항

이 외에도 우리는 주로 `-v` 옵션을 통해서 볼륨을 마운트합니다. 여기에서도 read-only 를 설정할 수 있으니 참고해 주세요.

`-v volume-name:/path/in/container:ro` 옵션을 통해 동일한 설정을 할 수 있으며 ro 는 readOnly 의 줄임말입니다.

```bash
$ docker run -v volume-name:/path/in/container:ro alpine
```

`--mount` 설정을 통해 동일한 설정을 할 수 있으며 가독성이 높습니다.

```bash
$ docker run --mount source=volume-name,destination=/path/in/container,readonly alpine
```

### RULE #9. 정적 분석 툴 사용(Static Analysis tools)

컨테이너 상의 취약점을 감지하기 위해 정적 이미지 스캔 툴을 사용하도록 한다. 

- 무료
  - Clair
  - Trivy
- 상업
  - Snyk (오픈소스 이며 무료 옵션을 제공)
  - anchore (위와 동)
  - Aqua Security's MicroScanner (free option available for rate-limited number of scans)
  - JFrog XRay
  - Qualys

쿠베의 잘못된 설정을 체크하는 서비스: 

- kubeaudit
- kubesec.io
- kube-bench

도커의 잘못된 설정을 체크하는 서비스:

- inspec.io
- dev-sec.io

### RULE #10. 로깅 레벨은 INFO 로 설정

기본값으로 도커 데몬은 info 로깅 설정 레벨로 구성된다. 만약에 이러한 경우가 아닐 경우에는 info 레벨로 데몬 로깅 레벨로 설정한다. `debug` 로깅 레벨을 설정할 경우 

```bash
$ docker-compose --log-level info up
```

# 마무리

지금까지 프론트 앱을 Microservice 구현을 위해 컨테이너화를 하고 Nginx, Vue 구성이 제대로 동작하는지 확인하였습니다. 다음 장에는 Backend 를 구성하여 진정한 API Gateway 패턴을 구현해 보도록 하겠습니다. 지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

# 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-5736
- https://github.com/Frichetten/CVE-2019-5736-PoC/blob/master/main.go
- https://gist.github.com/code-machina/bcb079e04c2a3101c422be07a262627c
- https://github.com/lxc/lxc/commit/6400238d08cdf1ca20d49bafb85f4e224348bf9d
- https://resources.whitesourcesoftware.com/blog-whitesource/top-5-docker-vulnerabilities
- https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html
- https://cheatsheetseries.owasp.org/cheatsheets/Attack_Surface_Analysis_Cheat_Sheet.html
- https://docs.docker.com/get-started/