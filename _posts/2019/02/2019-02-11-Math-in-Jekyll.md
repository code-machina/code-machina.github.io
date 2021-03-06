---
layout: post
title:  "깃헙에서 수학 공식 작성하기"
subtitle: "깃헙에서 수학 공식 작성하기"
author: "코마"
date:   2019-02-10 18:00:00 +0900
categories: [ "mathjax", "test"]
use_math: true
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 이번 시간에는 Markdown 문서에서 수학 공식을 작성하고 이를 깃헙 문서에서 올바르게 보일수 있도록 하는 작업을 준비하였습니다.

<!--more-->

{:toc}

# 개요

수학 공식을 인터넷에서 제대로 표시할려면 여러가지 알아야할 것들이 많습니다. 저 **코마**는 여러분들을 위해서 깃헙에서 이를 간단히 표현하는 방법을 알려드리려고 합니다. 이미 알고 계신 분들은 다른 글을 참조해 주세요.

수학 공식은 매우 복잡해 보이고 어렵지만, 활용하는 경우에는 큰 도움이 됩니다. 이미 수학자들이 만들어낸 모델을 우리는 사용하기만 하면 되기 때문입니다. 오픈 소스 모듈은 이러한 수학적 모델을 컴퓨터 연산에서 사용할 수 있도록 이미 많은 작업이 진행되어 있으며 제 생각에 이러한 노력의 극대화가 텐서플로우(Tensorflow)가 아닌가 생각됩니다.

각설하고 본론으로 들어가겠습니다. 우리는 우리의 생각에 수학적인 지식을 활용하였다면 이를 적절하게 표현함으로써 생각의 근거를 분명하게 밝혀야 합니다. 그래야 다른 사람들이 글을 읽고 글의 신뢰성과 논리적인 결론에 확신을 갖게됩니다.

그렇다면 어떻게 표현할까요? 바로 **MathJax** 입니다. [공식 홈페이지 - 매스 잭스](https://www.mathjax.org/) 또한, 현재 Jekyll 의 공식 makrdown 전처리기(preprocessor)인 kramdown 에 대한 내용을 확인해야 합니다. [Kramdown - Math Block](https://kramdown.gettalong.org/syntax.html#math-blocks) 본래는 위의 글을 모두 읽어야 되겠지만 저 **코마**는 여러분이 곧바로 수학 공식을 사용할 수 있도록 포스트에 정리하였습니다.

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

## Jekyll 설정

Jekyll 은 _includes 라는 폴더를 예약된 이름으로 사용하고 있습니다. 이는 Liquid 문법을 통해서 HTML 코드에 동적으로 삽입할 수 있습니다. 바로 아래와 같이 말이죠.

> TIP. 리퀴드 코드를 문서에서 보이고 싶다면 Raw 문을 사용해야 합니다. 이 문은 tag 처리를 지정된 영역에서 비활성화 시킵니다. 즉, 건너 뛰는 것입니다. 자세한 내용은 [리퀴드 Raw 문](https://shopify.github.io/liquid/tags/raw/) 을 참조해주세요.

- 매스 잭스 CDN 코드 입포트

```html
<!-- ./_includes/mathjax_support -->
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    TeX: {
      equationNumbers: {
        autoNumber: "AMS"
      }
    },
    tex2jax: {
      inlineMath: [ ['$','$'], ['\(', '\)'] ],
      displayMath: [ ['$$','$$'] ],
      processEscapes: true,
    }
  });
</script>
<script type="text/javascript"
        src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```

- mathjax_support 파일을 임포트

아래와 같이 head.html 파일에 아래와 같이 조건식을 작성하였습니다. 아마도 page.use_math 문에 대해서 의문을 가지시는 분들이 있을 것으로 생각됩니다. 이 문은 모든 페이지에서 선언되는 page 메타데이터에서 use_math 가 선언되어 있다면 매스 잭스 CDN 에서 자바스크립트 파일을 불러오는 설정입니다. 이를 통해 우리는 필요하지 않는 경우는 불러오지 않는 구현을 할 수 있습니다.

```html
<!-- ./_includes/head.html -->
<head>
  <!-- raw 를 이용해 Liquid Syntax 를 Markdown 문에서 보일 수 있다.  -->
  {% raw %}
  {% if page.use_math %}
    {% include mathjax_support %}
  {% endif %}
  {% endraw %}
</head>
```

## 수학 공식 샘플

현대에는 빅 데이터를 이용한 데이터 분석의 중요성이 더해져가고 있습니다. 수많은 데이터를 이용해서 통계적인 처리를 하고 AI 에게 학습을 시키는데 있어 빅데이터의 중요성은 날러 커져가고 있습니다. 혹자들은 이러한 데이터가 결국 자원화가 될 것이라는 이야기를 합니다. 즉, 플랫폼의 중요성이 계속 커져가는 것이죠. 이러한 변화의 시대에 개인이 살아남는 방법은 데이터를 이용하는 방법을 배우는 것이라고 생각됩니다. 그렇기 위해서는 통계와 같은 데이터의 성질과 해석을 돕는 도구가 매우 중요합니다.

코마 또한 이러한 분야에 관심을 가지고 계속 공부를 이어오고 있습니다. 부족하지만 조금씩 제가 공부한 내용을 여러분들에게 전달할 수 있도록 하겠습니다.

### 평균(Mean)

우리는 데이터의 평균을 통해서 데이터가 가지는 성질을 알아보고 싶어할 것입니다. 그렇다면, 평균을 구하는 공식은 어떻게 표현할까요 아래와 같이 작성하면됩니다. $$ \bar{x} $$ 는 데이터 집합에 대한 평균을 나타낼 때 사용하는 기호(symbol)입니다.

$$
  \bar{x} = \sum\limits_{i=1}^n \frac{x_i}{n}
$$

- 위의 식은 아래와 같이 작성되었습니다.

```raw
$$
  \bar{x} = \sum\limits_{i=1}^n \frac{x_i}{n}
$$
```

모든 수식들은 Latex 문법에 기반합니다. 일종의 표기 원칙입니다. `$$ *expressions* $$` 와 같이 블록안에서 임의의 기호를 조합해서 식을 만들어 낼 수 있습니다. `\sum` 키워드는 $$ \sum $$ (시그마)를 뜻합니다. 즉 합을 뜻합니다. 여기서 합의 범위를 지정해주고자 할 경우 `\limits_{시작}^{끝} {변수}` 과 같은 문법 형식을 사용합니다.

예를 들어 2 <= i <= 10 인 합을 식으로 표현하려면 `$$ \sum\limits_{i=2}^{10} {x_i} $$ ` 와 같이 쓸수 있습니다. 그리고 이를 실제로 표현하면 아래와 같이 됩니다.

$$ \sum\limits_{i=2}^{10} {x_i} $$ 

### 표준편차(Standard Deviation)

이제 응용을 해볼까요. 표준 편차를 구하는 공식을 수식으로 표현하겠습니다. 여기서 필요한 재료는 아래와 같습니다. 식의 가장 바깥 쪽에서 안으로 이동하며 필요한 성분을 파악하였습니다. 그리고 이를 나열해줍니다.

- 근(Square Root)을 나타내는 기호
- 분수(Fraction)를 나타내는 기호
- 합(Sigma)를 나타내는 기호

$$
{S} = \sqrt {S^2} = \sqrt { \frac{1}{n-1} \sum\limits_{i=1}^{n} (x_i - \bar{x})^{2} }
$$

아래는 위의 식의 소스입니다.

```raw
$$
{S} = \sqrt {S^2} = \sqrt { \frac{1}{n-1} \sum\limits_{i=1}^{n} (x_i - \bar{x})^{2} }
$$
```

## 결론

지금까지 Jekyll 에서 Latex 문법으로 수학 공식을 작성하는 방법을 알아보았습니다. 어떤가요 쉽지 않나요? 이제 여러분들도 깃헙에서 자신의 생각을 정리할때에 수학 기호를 사용하여 생각을 좀더 논리적으로 표현할 수 있게 되었습니다. 저 **코마** 앞으로도 여러분에게 좋은 자료를 전달하기 위해서 노력하도록 하겠습니다.