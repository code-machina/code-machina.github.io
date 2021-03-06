---
layout: post
title:  "R로 배우는 통계적 분석 이론 (1)"
subtitle: "쉽게 이해하는 모집단과 표본"
author: "코마"
date:   2019-03-11 1:00:00 +0900
categories: [ "r", "statistics" ]
excerpt_separator: <!--more-->
---

안녕하세요 코마입니다. 오늘은 통계 이론을 다루어보도록 하겠습니다. 실제 분석을 위해서 꼭 필요한 통계적인 지식을 통해 여러분들이 쉽게 분석 방법론을 이해할 수 있는 문서입니다.

<!--more-->

# 목표 (Goals)

통계학을 심도있게 다루고자하면 끝이 없을 것입니다. 아마도 근래에 통계를 배우고자 하는 분들은 순수한 학문으로서 통계라기 보다는 자료를 분석하기 위한 데이터 분석의 목적을 가지고 있을 것입니다.

무른 도구란 그 쓰임에 맞게 도구를 사용하는 방법을 익혀야 합니다. 망치로 구멍을 팔 수는 없으니까요. 따라서, 우리는 통계를 어느 정도 수준으로 익힐지를 가늠해 가면서 범위를 정해야 합니다.

저 코마는 여러분들을 위해서 데이터 분석 목적만을 위해 자료를 재정리하여 제공하도록 하겠습니다.

- 수학 식보다는 분석 기법 중심
- 실무적인 데이터 분석 과정

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- 수평형 광고 -->
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-7572151683104561"
     data-ad-slot="5543667305"
     data-ad-format="auto"
     data-full-width-responsive="true"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 통계학이란

통계학이란 다음과 같이 생각할 수 있습니다.

> 자료에서 유용한 정보를 이끌어 내는 학문

유용한 정보를 이끌어 내기 위해서는 자료를 잘 정리하고 잘 수집해야 합니다. 그리고 정리된 자료를 해석하는 방법이 필요합니다.

- 자료 정리
- 자료 수집
- 자료 해석

# 모집단: 무엇을 위해 자료를 모으는가?

만약에 모든 자료를 가지고 있다면 자료를 힘들게 모을 필요는 없습니다. 하지만 모든 자료를 보관한다는 것은 매우 비효율적이고 엄청난 비용을 소모하게됩니다.

예를 들어보면 요즘 빅데이터 분석을 통해 여론을 조사하는 기업들이 생겨나고 있습니다. 그러나 이 기업의 주 고객층은 기업이거나 유명인, 그리고 정치가들입니다. 이는 아직 이 서비스가 일반인들이 이용하기에 가격이 비싸다는 것을 의미합니다.

여기서 중요한 것은 데이터 수집은 곧 비용이다 라는 점입니다.

따라서, 엄청난 비용을 감당하면서 인구주택총조사를 진행하지 않으려면 **표본**이라는 것이 필요합니다. 이 표본은 전체 중에 극히 일부분입니다.

그렇습니다. 통계를 우리가 **인구주택총조사(모집단)**을 진행하지 않더라도 필요한 정보를 알 수 있도록 도와줍니다.

## 세 줄 요약 요약: 모집단이란

- 유용한 정보의 대상이되는 것을 모집단이라고 한다.  
- 즉, 모집단은 우리가 알고자하는 전체를 의미한다.
- 모집단을 구성하는 개체를 추출단위 혹은 원소라고 한다.

# 표본 : 모집단을 알기 위한 자료

국민 전체 투표와 같은 류의 조사를 총조사(census)라고 합니다. 비용의 문제로 일부 만을 조사하는 경우를 표본(sample)조사라고 합니다.

자 그렇다면 궁극에는 우리가 알기 원하는 목표치가 있을 것입니다. 하나를 보면 열을 안다는 말처럼 우린 일부를 보더라도 제대로 보아 전체를 짐작하려는 목표가 있습니다. 그렇다면 알고자 하는 목표에 대한 이름 명명이 필요합니다.

- 모집단에 대해서 알고자하는 값을 모수(parameter)라고 부른다.
- 이 모수를 추론하기 위해 표본에서 추출한 값을 통계량(statistic)이라고 부른다.

## 표본 추출하는 방법

전체에 대해 조사를 하는 것이 아니기 때문에 우린 표본을 만들어 낼 때 세심한 주의가 필요합니다. 만약 여러분이 HR 에 소속된 직원이라고 가정해볼까요? 사내 설문 조사를 통해 직원 복지를 개선하는 방법을 고민하고 있다고 가정하겠습니다.

그런데 만약 1000여명의 회사이고 게대가 각 개소(지역 사무소)에 뿔뿔이 흩어져 있는 경우에 전체를 조사하기 힘듭니다. 그리고 선배에게 물어보니 설문 자료를 취합하는데 100명 기준으로 3주 정도가 걸린다고 합니다.

만약 여러분이 HR 직원이라면 과연 어떻게 해야 할까요? 그렇습니다. 설문 조사 대상(표본)을 잘못 설정하면 다시 3주를 소모해야 합니다. 이는 표본 추출 실패로 인한 비용(cost)인 것입니다. 따라서, 우리는 알고자하는 모집단에 맞추어 표본을 추출하는 방법을 잘 적용해야 합니다.

통계학은 여러분이 오류를 범하지 않도록 그리고 직장에서 유능한 직원으로 인정 받을 수 있도록 하기 위해 이미 방법을 마련해 놓았습니다. 아래를 보실 까요?

## 표본 추출 방법

표본조사를 할 때 아래의 사항을 명확하게 정의하고 조사를 진행해야합니다.

- 모집단의 정의
- 표본의 크기
- 조사 방법(면접, 우편, 전화, 이메일 조사 등)
- 조사 기간
- 표본 추출 방법

여기서 가장 중요한 부분은 바로 **표본추출 방법**입니다. 표본 추출 방법들을 나열해 볼까요? 각 추출법에 대한 세부적인 설명은 나중으로 미루겠습니다. 

- 단순랜덤추출법(simple random sampling)
- 계통추출법(systematic sampling)
- 집락추출법(cluster sampling)
- 층화추출법(stratified sampling)

# 실험 (≒ 표본조사)

실험은 표본조사랑 같은 레벨의 단계입니다. 즉, 조사가 아닌 실험을 하여 데이터를 확보하는 것입니다. 

> 실험 대상에게 처리를 가한 뒤에 그 결과를 관측해 자료를 수집

예를 들어 볼까요?

새로운 암 치료제의 효과를 분석하기 위해 절반에게는 새로운 치료제를 투여하고 나머지 집단에게는 비타민을 처방한 뒤에 치료효과를 분석 하는 등의 방식으로 실험을 진행할 수 있습니다.

# 결론

오늘은 통계에 대해서 간단히 알아보았습니다. 통계를 공부하는 분들에게 도움이 되고자 핵심적인 내용을 요약 정리하였습니다. 다음 시간에는 자료의 종류, 통계 분석에 대해서 알아보도록 하겠습니다. 앞으로도 좋은 자료를 제공해 드리기 위해서 노력하는 **코마**가 되겠습니다. 감사합니다.