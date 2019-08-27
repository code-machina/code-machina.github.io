---
layout: post
title:  "Part 1: 5 분만에 구성하는 몽고DB 리플리카 셋 (feat. Docker)"
subtitle: "세상에서 제일 빠른 몽고DB Replica Set 구성하기"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-08-26 00:00:00 +0900
categories: [ "mongodb", "docker-compose", "replication", "replica_set"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 이번 시간에는 MongoDB 리플리카 셋을 도커 컴포저를 통해서 매우 빠르게 구성하는 방법을 알려드리겠습니다. 이 글은 2개의 파트로 나누어 설명되므로 꼭! 파트2를 구독해 주시길 바랍니다. 😺

<!--more-->

## 구성 전략

만약 여러분이 Swarm 과 docker-compose 를 이용하여 추후의 서비스 확장성을 가지면서 안정적인 데이터베이스 인스턴스를 관리하고자 할 때 이 글을 매우 효과적입니다. 코마의 구성 전략을 아래와 같이 적어 두었으니 여러분들도 자신의 요구사항과 비교하여 이 글이 나에게 필요한지를 먼저 검토해 보기를 바랍니다.

{% include advertisements.html %}

### 구현 목표

  - 몽고DB 는 Swarm 서비스에 올리지 않는다.
  - 몽고DB 는 가용성과 백업 관리를 위해서 리플리카 셋(Replica Set)으로 구성한다.
  - 보안을 위해서 리플리카 셋에 인증을 설정하고 키 파일을 등록한다.
  - Swarm 서비스와 통신이 가능하도록 네트워크 설정을 수립한다. (Auto Hostname Discovery)
    - 위의 구성을 위해서는 간략한 도커 네트워크 지식이 있어야 합니다. 아래의 링크를 우선 선행해 주세요.
  - 어플리케이션을 Swarm 서비스에 등록하여 추후 스케일 아웃(Scale Out, 동일 하드웨어 추가 및 스웜 노드 등록)을 통해 간편하게 서비스를 가용성을 높인다.
  - **위의 모든 과정을 명령어 몇 개로 관리하고 싶다.**

> ⚠️ **안내**: 본 글은 [도커 오버레이 네트워크(Docker Overlay Network) 알아보기 Part 1]({% link _posts/2019/08/2019-08-09-Docker-Swarm-Overlay-Network-Part-1.markdown %})를 이해할 경우 이해에 용이합니다.

{% include advertisements.html %}

구현 목표를 잘 읽어보셨나요? 이 중에 하나라도 일치한다면 분명 이 글은 여러분들에게 도움이 될 것입니다.

## 리플리카 셋이란

몽고DB 에서 리플리카 셋(Replica Set, 이하 리플리카 셋)이란 mongod 프로세스의 그룹을 의미합니다. 그러나 이 그룹은 동일한 데이터 셋을 관리하는 것이 특징입니다. 리플리카 셋은 이중화(redundancy, 이하 이중화)와 고가용성(high availability, 이하 고가용성)를 제공하기에 의의가 있습니다. 따라서, 모든 프로덕션 적용의 토대(basis)가 됩니다.

{% include advertisements.html %}

> **INFO**: A replica set in MongoDB is a group of mongod processes that maintain the same data set. Replica sets provide redundancy and high availability, and are the basis for all production deployments. This section introduces replication in MongoDB as well as the components and architecture of replica sets. The section also provides tutorials for common tasks related to replica sets. [docs.mongodb.com: replication](https://docs.mongodb.com/manual/replication/)

### 이중화와 데이터 가용성

리플리케이션(relication, 이하 복제본)은 이중화를 제공함과 동시에 데이터 가용성을 높여줍니다. 여러 데이터 베이스 서버에 데이터 복제본을 여러개 들고 있기 때문입니다. 복제본은 내결함성(fault tolerance, 이하 내결함성)을 제공하며 단일 장애점(single point of failure, SPOF, 이하 [단일장애점](https://ko.wikipedia.org/wiki/%EB%8B%A8%EC%9D%BC_%EC%9E%A5%EC%95%A0%EC%A0%90))의 우려를 감쇠해주는 역할을 합니다.

{% include advertisements.html %}

복제본은 향상된 읽기 능력(read capacity)를 제공할 수 있으며 여러 서버에 읽기 작업을 전송할 수 있습니다. 또한 재해복구를 위해 여러 데이터 센터에 카피본을 유지함으로써 데이터 지역성과 가용성을 증대할 수 있습니다. 마지막으로, 재해 복구,리포팅, 백업 등의 전용 목적으로 부수적인 카피본을 유지할 수 있습니다.

## 도커 컴포저 이용하기

도커 컴포저를 통해서 mongo 인스턴스를 3개 만들고 리플리카 셋을 구성하도록 하겠습니다. 또한, 인증 권한을 추가하고 키파일을 등록하여 보안을 강화하도록 해보겠습니다.

- **준비사항**
  - 도커 컴포저를 최신 버전으로 설치/업그레이드 해주세요.
    - 버그에 따른 마이너 버전 픽스 과정이 필요 할 수 있으니 인내심을 가져주세요.
  - 컴포저 spec 파일은 3.x 이상 버전을 사용할 예정입니다.

> ⚠️ **주의**: 몽고DB의 포트를 외부에 노출할 경우 IP 접근 제어를 통해 DB 관리자 PC 만 접속 가능하도록 해줍니다. 이때, 네트워크 방화벽 보다는 OS 레벨의 방화벽을 설정해 주는 것이 좋습니다.

### 프로젝트 구조 잡기 : 디렉터리

- docs : 각종 설정 관련 정보를 마크다운 서식으로 작성하여 보관합니다.
- mongosetup : mongo 인스턴스 설정 시 docker 명령어를 사용하기 보다는 동일 네트워크 상의 컨테이너를 통해서 설정하는 것이 좀 더 도커스럽습니다.
- scripts : 각종 설정 스크립트

```bash
$ tree -d
.
├── docs
├── mongosetup
└── scripts
```

{% include advertisements.html %}

### 프로젝트 구조 잡기 : 파일

0. `./scripts/config.sh`
  - 패스워드, 아이디 등의 정보를 담고 있는 중요 파일입니다.
1. `./docker-compose-init.yml` 
  - 초기 인스턴스 세팅을 잡아주는 스펙 파일입니다.
  - `./scripts/setup_init.sh` 스펙 파일이 동작 시 해당 파일이 동작하면서 리플리카 셋을 잡아줍니다.
2. `./docker-compose-addauth.yml`
  - 1번 스펙 파일을 구동 후 인증, 키파일 생성 작업을 처리하기 위해 구동하는 스펙 파일입니다.
  - `./scripts/setup_addauth.sh` 2번 스펙 파일이 동작 시 같이 구동되는 쉘 스크립트 입니다.
3. `./docker-compose.yml`
  - 2 번 스펙 파일 구동이 완료된 후 구동시키는 파일입니다.
  - `./scripts/setup.sh` 3 번 스펙 파일이 동작 시 구동되는 쉘 스크립트 입니다.

{% include advertisements.html %}

```bash
$ tree -f
.
├── ./docker-compose-addauth.yml
├── ./docker-compose-init.yml
├── ./docker-compose.yml
├── ./docs
│   ├── ./docs/Create-Admin-in-Replica-set.md
│   ├── ./docs/README.md
│   └── ./docs/README-old.md
├── ./mongosetup
│   └── ./mongosetup/Dockerfile
├── ./prod_restart.sh
├── ./prod_start.sh
├── ./prod_stop.sh
├── ./README.md
└── ./scripts
    ├── ./scripts/config.sh
    ├── ./scripts/setup_addauth.sh
    ├── ./scripts/setup_init.sh
    └── ./scripts/setup.sh

4 directories, 19 files
```

## 스펙 파일을 여러 개 두는 이유

위에서 보면 여러 개의 스펙 파일을 생성해두는 이유가 궁금할 수 있습니다. 사실 이것은 개인적인 기호의 문제라고 생각할 수 있습니다. 어떤 분들은 쉘 스크립트를 통해서 리플리카 셋 설정을 하기도 합니다. 하지만, 모두 OS 레벨의 명령어를 수 십번 두드려야 합니다. 

{% include advertisements.html %}

따라서, 저 코마는 좀 더 간편하게 구성할 수 있도록 명령어 세 줄이면 곧바로 구성되는 간편한 방식을 제공하기 위해서 위와 같이 컴포저 스펙 파일을 단계 별로 쪼개었습니다. (칭찬해 주시면 감사하겠습니다.)

## 구동 절차 맛보기

우선 결론부터 말씀 드리면, 아래와 같이 명령어를 타이핑하시면 모든 구성이 완료됩니다.

1. `docker-compose-init.yml` 파일 구동

위에서 설명하였 듯이 레플리카 셋 설정을 잡아줍니다.

```bash
docker-compose -f docker-compose-init.yml up
```

2. `docker-compose-addauth.yml` 파일 구동

레플리카 셋에 접속 계정등의 인증 정보를 등록하고 keyfile 을 생성합니다.

```bash
docker-compose -f docker-compose-addauth.yml up
```

3. 마지막 `docker-compose.yml`

최종 세팅이 완료되었으며 이제 구동하는 단계입니다. `&& docker-compose logs -f` 영역은 부트업 로그 모니터링 용입니다. 부팅이 정상적으로 완료되면 ctrl+c 를 눌러주세요.

{% include advertisements.html %}

```bash
docker-compose up -d && docker-compose logs -f
```

어떤가요 매우 간편하게 모든 설정을 완료할 수 있습니다. 말 그대로 간단 그 자체 입니다. 수동으로 명령어를 타이핑하는 것보다 훨씬 IT스럽다고 볼 수 있습니다. (우리는 할일이 매우 많은 사람들이니까요 ~:() )

## 마무리

파트 1은 도커 컴포즈를 통해서 몽고DB 리플리카 셋을 구성하는 방법을 설명하고 프로젝트 구조를 간략히 설명드렸습니다. 다음 시간에는 프로젝트 구성을 통해 실제로 여러분들이 리플리카 셋을 완벽하게 구성하여 보도록 하겠습니다. 지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

- [Part 2 : 5분만에 구성하는 Mongo Replica Set]({% link _posts/2019/08/2019-08-27-Configure-Mongo-Replica-Set-in-5m-Part-2.markdown %})

# 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [MongoDB: Replication](https://docs.mongodb.com/manual/replication/)
