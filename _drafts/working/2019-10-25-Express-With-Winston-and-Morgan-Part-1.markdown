---
layout: post
title:  "Express.js 로깅(Logging) 완벽 분석 파트-1 (Winston, Morgran 을 이용한 로깅 구성)"
subtitle: "Winston 완벽 분석"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-10-25 00:00:00 +0900
categories: [ "logging", "express", "winston", "morgan" ]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. Express.js 와 로깅(Logging)을 완벽하게 이해하는 시간을 가져보겠습니다. 이번 시간에는 Winston 을 우선 이해 해 보도록 하겠습니다. 😊 이 과정을 모두 마친 뒤에는 어떠한 로깅 전략이든 쉽게 구현할 수 있습니다. 자 그럼 시작해볼까요? 😺
 
<!--more-->

## Winston 이란

Winston 은 간결하면서도 보편적으로 사용하기 위해 설계된 **로깅(logging) 라이브러리**입니다. 이 라이브러리는 transport 라는 개념을 통해 다양한 로깅 채널을 제공합니다.(Console, File 등) 그리고 다양한 로깅 레벨로 구성할 수 있습니다.

그렇다면 우리가 Winston 을 이용할 경우 어떠한 구현이 가능할지 상상해볼까요?

> ⚡ 예시: 여러분은 에러 메시지를 데이터 베이스에 저장하고 나머지 로그들은 콘솔이나 파일에 출력하거나 저장할 수 있습니다.

또한, Winston 은 로깅 절차를 분리하여 더욱 유연하고 확장성 있는 구현을 할 수 있습니다. `Formats` 를 이용하여 로깅 포맷팅을 유연하게 지정할 수 있으며 로깅 레벨 또한 마찬가지 입니다. 그리고 `Transport` 를 커스터마이징하여 로그를 저장하는 방법을 우리의 입맛대로 지정할 수 있습니다.

그렇다면 간단한 샘플을 만들어볼까요?

## 기본 샘플

우선 프로젝트 폴더를 생성합니다. 그리고 npm 을 초기화 합니다.

```bash
$ mkdir winston-ex1
$ cd winston-ex1
$ npm init -y 
```

- winston 모듈을 설치해볼까요?

제가 이글을 작성하는 시점에 winston의 버전은 winston@3.2.1 입니다. 2.x 와 사용방법이 상이하니 이 과정을 따라하시는 분들은 버전을 업데이트 해주시길 바랍니다.

```bash
$ npm i winston
+ winston@3.2.1
added 37 packages from 32 contributors and audited 43 packages in 2.819s
```

- 소스코드를 입력할 파일을 생성합니다.

```bash
$ touch index.js
```

- logger 를 생성하고 로그를 생성합니다.

```js
'use strict';
const winston = require('winston');

// createLogger 함수를 통해 logger 를 생성합니다.
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  defaultMeta: {service: 'winston-example-1'},
  trasnports: [
    new winston.transports.Console(),
  ]
})

var debug = 1234567890;

logger.info(`[DEBUG] 디버깅 모드에 진입하였습니다. (${debug})`)
```

- 생성한 파일을 실행해 보겠습니다.

```bash
$ node index.js
```


<br>
{% include advertisements.html %}
<br>

## 마무리

**Express.js 로깅(Logging) 완벽 분석** 과정에서 winston 에 관하여 다루었습니다. 👏 (짝짝짝) 이 과정을 통해 여러분은 Winston 이라는 모듈의 전체적인 구성을 배웠으며 이를 통해 언제든지 원하는 포맷과 방식으로 로깅을 할 수 있게 되었습니다. 다음 시간에는 Winston 과 Morgan 을 연동하여 사용하는 방법을 다루어보도록 하겠습니다. 

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 Express.js 를 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Github.com : Winstonjs](https://github.com/winstonjs/winston)
