---
layout: post
title:  "Part 2: 5 분만에 구성하는 몽고DB 리플리카 셋 (feat. Docker)"
subtitle: "세상에서 제일 빠른 몽고DB Replica Set 구성하기"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-08-27 00:00:00 +0900
categories: [ "mongodb", "docker-compose", "replication", "replica_set"]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 이번 시간에는 이전 시간에 이어서 Mongo Replica Set 을 실제로 Docker-composer 를 통해서 구축해보도록 하겠습니다. 매우 빠르고 간단하니 멘탈을 다잡아 주세요 ~ :) 😺

<!--more-->

## 이어서

이전 시간에는 docker-compose 를 이용하는 방법에 대해 설명드렸습니다. 쉘 스크립트가 컴포저 스펙 파일마다 별도로 설정되어 있어 혼란 스러울 수 있습니다. 그러나 그 구조를 간단히 알아두면 매우 쉽게 이해할 수 있습니다.

{% include advertisements.html %}

아래의 파일을 볼까요?

<script src="https://gist.github.com/code-machina/8e93b30a8384699485e7e12d39be3d95.js"></script>

## 차이점 발견

위의 `docker-compose-init.yml` 파일을 보게되면 하나 의문점이 들 수 있습니다. 바로 mongosetup 이라는 컨테이너 입니다. 어떤 역할을 할까요?

`mongosetup` 컨테이너는 몽고DB 컨테이너들에게 설정을 해주는 역할 입니다. 즉, 같은 네트워크에 존재하면서 mongoDB 프로세스에 접속이 가능하며 접속 후에 Config 설정을 원격에서 해주는 역할입니다. 그리고 이 컨테이너는 맡은 소임을 다하면 종료됩니다. 

```yml
  mongosetup:
    build: ./mongosetup # dockerfile 빌드
    networks:
      - mongo-bridge # 동일한 네트워크
    volumes:
      - ./scripts:/scripts # 호스트의 스크립트 파일을 구동
    entrypoint: [ "/scripts/setup_init.sh" ] # 빌드 완료 후 컨테이너 실행 시 컨피그 주입을 실행
```

`setup_init.sh` 파일을 뜯어보겠습니다. `mongosetup` 컨테이너는 구동된 후에 anguis1 이라는 몽고 인스턴스에 접속할 수 있을때 까지 대기합니다. 접속이 가능해지면 실제로 컨피그를 주입하는 과정을 거치게됩니다.

```bash
# 스테이!
echo "Waiting for MongoDB startup.."
until [ "$(mongo --host anguis1:27017 admin --eval "printjson(db.serverStatus())" | grep uptime | head -1)" ]; do
  printf '.'
  sleep 1
done
```

실제로 컨피그를 주입할 때는 레플리카 셋 설정을 주입합니다. 여러분의 컨테이너의 이름과 설정하고 싶은 레플리카 셋의 이름에 따라 설정이 바뀔 수 있으니 적절히 변경해서 사용하세요~

```bash
mongo --host anguis1:27017 <<EOF
   var cfg = {
        "_id": "anguis-repl",
        "members": [
            {
                "_id": 0,
                "host": "anguis1:27017",
                "priority": 2
            },
            {
                "_id": 1,
                "host": "anguis2:27017",
                "priority": 0
            },
            {
                "_id": 2,
                "host": "anguis3:27017",
                "priority": 1,
                "arbiterOnly": true
            }
        ]
    };
    rs.initiate(cfg, { force: true });
    rs.reconfig(cfg, { force: true });
EOF
```

{% include advertisements.html %}

## 실전 구동

우선 [코마의 docker awesome 프로젝트](https://github.com/code-machina/coma-docker-awesome)에 접속하셔서 `star` 를 눌러주세요. 여러분의 추천은 다음을 이어가는 힘이됩니다.

```bash
git clone https://github.com/code-machina/coma-docker-awesome
```

폴더로 이동합니다.

{% include advertisements.html %}

```bash
cd coma-docker-awesome
cd ./5minute-mongo-replicaset
```

아래의 명령어를 입력하셔서 설정을 완료해 주세요.

```bash
docker-compose -f docker-compose-init.yml up
docker-compose -f docker-compose-addauth.yml up
```

마지막으로 서비스를 구동합니다.

```bash
docker-compose up -d && docker-compose logs -f
```

{% include advertisements.html %}

## 마무리

지금까지 도커 컴포즈를 이용한 초간단 몽고DB 레플리카 셋을 구성하는 방법을 설명드렸습니다. 저는 여러분이 이 글을 통해 복잡한 설정 대신에 빠르게 원하는 스택을 구축하셔서 응용 개발에 집중하는데 도움이 되었으면 합니다. 지금까지 **코마** 였습니다.

{% include advertisements.html %}

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다

# 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [MongoDB: Replication](https://docs.mongodb.com/manual/replication/)
- [코마의 docker awesome 프로젝트](https://github.com/code-machina/coma-docker-awesome)
