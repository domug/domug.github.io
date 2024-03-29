---
layout: post
title: "[A/B Test] 아웃라이어 핸들링"
date: 2022-04-01 00:00:00
image: abtest9.jpg
tags: [ab-test, cuped, otpsm, outlier]
categories: abtest
use_math: true
---



해당 포스트에서는 A/B Test에서 아웃라이어의 처리가 필요한 이유와 이를 위한 몇가지 방법론들을 살펴볼 예정이다.

&nbsp;

---

# 1. Outliers in A/B Test

본격적인 내용을 살펴보기에 앞서, 아웃라이어의 의미에 대해서 간략하게 생각해보도록 하자. 

통계학에서 **"아웃라이어"**란, 대다수의 **"정상적인"** 데이터와 그 성질이 사뭇 다른 데이터를 의미한다. 따라서 컴퓨터 과학, 머신러닝, 통계학의 범주를 아우르는 **아웃라이어 탐지 과제**는 주어진 데이터로부터 정상적인 패턴과 비정상적인 패턴을 구분하는 것이 주된 관심사라고 할 수 있다.

한편, 아웃라이어를 정의하는 것은 그리 간단하지만은 않다. 가령, 대한민국 남성의 평균 키가 약 170cm 중반이라는 점을 감안할 때, 일반적으로 **키가** **200cm가 넘어가는 남성**들은 아웃라이어라고 판단할 수 있을 것이다. 한편, 만약 우리의 관심사가 **농구 선수**들의 키라고 한다면 이 때는 키가 200cm가 넘어가는 남성들을 아웃라이어라고 판단하기는 다소 어려울 수 있다.

이처럼 아웃라이어 탐지는 주어진 데이터의 맥락을 고려해야 한다는 특징이 있다. 이러한 맥락에서 아웃라이어는 크게 **"절대적 아웃라이어"**와 **"상대적 아웃라이어"**의 범주로 구분될 수 있다. 

자 이제 A/B Test의 맥락에서 아웃라이어 처리의 목적에 대해서 살펴보자. A/B Test는 대조군과 시험군 간의 **"평균적인 경향성"**을 파악하기 위해 수행된다. 즉, 다른 모든 조건이 동일할 때 특정한 기능을 적용 받은 사람들이 그렇지 않은 사람들에 비해 어떠한 차이가 있는지를 파악하는 것이 일차적인 목표라고 할 수 있다. 이러한 맥락에서 A/B Test에서의 아웃라이어 탐지는 그 성격상 "상대적 아웃라이어"를 찾고, 해당 값들을 적절히 처리하기 위한 목적으로 수행된다. 

그렇다면 A/B Test에서 아웃라이어를 처리하는 것이 왜 중요할까? 설명의 편의를 위해 가장 일반적인 연속형 지표의 모평균에 대한 이표본 t검정 (two sample t-test) 를 바탕으로 논의를 이어가겠다. 이 때, 실험에 사용되는 검정통계량은 다음과 같다.

&nbsp;

<center>

$$
t_{df} = \frac{(\bar X_1 - \bar X_2) - (\mu_1 - \mu_2)}{\sqrt{\frac{s_1^2}{n_1} + \frac{s_2^2}{n_2}}}
$$

</center>

&nbsp;

이를 바탕으로, 통계적 가설 검정은 해당 검정통계량이 기각역보다 클 경우 귀무 가설을 기각하고 두 그룹간 차이가 통계적으로 유의미하다는 결론을 내리는 방식으로 진행된다. 다시 말해, 실험의 유의성을 주장하기 위해서 우리는 (일반적으로) **더 큰 검정통계량을 값을 얻고자 하는 것이다**.

이러한 맥락에서 검정통계량 $t_{df}$ 를 좀 더 구체적으로 뜯어보도록 하자. 여기서 중요한 것은 해당 식의 **분모**인데, 이는 대조군과 시험군의 표본 분산 $s_1^2, s_2^2$과 샘플 사이즈 $n_1, n_2$로 구성되어 있다는 점을 알 수 있다. 일반적인 A/B Test에서 수집될 샘플 사이즈는 실험의 기획 단계에서 정해지기 때문에 $n_1$과 $n_2$는 우리의 주된 관심사는 아니다. 

한편, 각 집단별 표본 분산 $s_1^2, s_2^2$는 수집된 데이터에 영향을 받게 되는데, 구체적으로 **$s_1^2, s_2^2$의 값이 작아지면 작아질 수록 전체적인 검정통계량 $t_{df}$의 값이 커지게 된다**. 그렇기 때문에 수집된 데이터의 분산을 줄이는 것은 우리의 분명한 관심사가 되는 것이다. 

그렇다면 어떻게 데이터의 분산을 줄일 수 있을까? 분산은 데이터의 변동성을 측정하는 지표이다. 그리고 **아웃라이어의 존재는 종종 데이터의 분포를 불안정하게 만들어 분산을 증가시킨다 (inflation of variance)**. 따라서 적절히 아웃라이어를 제거해주는 작업은 실험의 비용 측면에서 상당히 중요한 과제라고 할 수 있다.

이 밖에도 모평균에 대한 검정에서 아웃라이어를 처리하는 것은 정규 근사의 측면에서도 의미가 있다. 일반적으로 온라인 A/B Test에서 수집되는 웹 데이터는 양의 왜도 (skewness) 가 상당히 크다는 점에서 극단적인 아웃라이어를 제거하는 작업은 중심 극한 정리가 작동하기 위한 표본 평균의 정규 근사 정도를 보장하기 위한 장치로 이해할 수 있다. 

한편, 그럼에도 불구하고 아웃라이어를 어떠한 방식으로든 처리한다는 것은 수집된 데이터에 사후적인 변형을 가하는 점이라는 사실을 항상 유념해야 한다. 잘못된 아웃라이어 처리는 실험의 결과를 왜곡할 수 있기 때문에 아웃라이어 처리 로직에 대한 충분한 정당화가 가능해야 할 것이다. 이러한 맥락에서 온라인 A/B Test에서 흔히 활용되는 몇가지 아웃라이어 처리 방법론들에 대해 살펴보도록 하겠다.

&nbsp;

&nbsp;

---

# 2. Strategies

일반적인 아웃라이어 탐지 테스크와는 다르게, 온라인 A/B Test에서의 아웃라이어 처리의 특징은 거의 대부분의 경우 **일차원의 데이터를 다룬다는 점이다 (시험군 vs 대조군)**. 그렇기 때문에 학계에서 연구되는 (주로 고차원 데이터에서의) 복잡한 아웃라이어 탐지 방법론과는 다르게 비교적 간단한 통계 방법론도 상당히 효과적으로 활용될 여지가 있다.

이러한 맥락에서 온라인 A/B Test에서 활용될 수 있는 아웃라이어 처리 방법론 몇가지를 간략히 정리해보도록 하겠다.



&nbsp;

## Quantile Winsorization

통계학에서 **"winsorizing"**, 또는 **"capping"** 이란, 주어진 데이터에서 **일정한 기준점을 넘어가는 값을 특정한 값으로 대체하는 것이다**. 이와 관련해 "**quantile winsorization**"은 해당 기준점을 데이터에서의 분위 수로 지정해 아웃라이어를 처리한다.

해당 방법은 개념적으로도, 구현상으로도 상당히 간단하기 때문에 쉽게 적용할 수 있다는 장점이 있으나, 문제는 해당 방법을 적용할 경우 데이터의 형태를 고려하지 않은채 항상 일정한 %의 데이터가 아웃라이어로 간주되어 대체된다는 점이다. 가령, 아웃라이어가 없는 깨끗한 데이터의 경우 정상적인 데이터가 잘려나가는 이슈가 발생할 수 있다. 이처럼 데이터의 분포 형태를 고려하지 않은채 아웃라이어를 처리하는 방법은 안전성의 측면에서 다소 우려가 있을 수 있다.

&nbsp;

<center>
  <img src="{{site.baseurl}}/images/abtest/41.png" width=500 height=300/> 
 <br>
 <em><span style="color:grey">5% quantile winsorization 예시</span></em>
</center>

&nbsp;

## 3-$\sigma$ Rule

3-$\sigma$ rule 방법은 정규분포의 성질을 바탕으로 아웃라이어를 정의한다. 

정규 분포의 누적 확률 밀도 함수 (i.e. cdf) 를 바탕으로 할 때, 우리는 다음과 같이 평균 $\mu$ 를 기준으로 표준 편차 $\sigma$의 값에 따라 $[\mu - 3\sigma , \mu + 3\sigma]$ 의 범위 안에 약 99.7%의 확률 밀도가 존재하는 것을 이론적으로 알고 있다.

&nbsp;

<center>
  <img src="{{site.baseurl}}/images/abtest/42.png" width=500 height=300/> 
 <br>
 <em><span style="color:grey"></span></em>
</center>

&nbsp;

따라서 정규분포를 따르는 종 모양의 데이터에 대해 $\mu \pm 3\sigma$ 의 기준점을 세울 경우, 해당 범위에 포함되지 않는 약 0.3%의 데이터를 우리는 자연스레 아웃라이어로 간주할 수 있다.

이처럼 3-$\sigma$ rule은 **경험 법칙 (rule of thumb)**에 의거한 방법인데, 그 이론적 정당성은 모평균에 대한 검정에서 충분한 샘플 사이즈가 보장될 경우 중심극한정리가 작용한다는 것에 기반한다. 그렇기 때문에 많은 실험 플랫폼에서 해당 방법을 아웃라이어 처리에 활용하고 있다. (참고 - [Optimizely](https://support.optimizely.com/hc/en-us/articles/4410289414413-How-Optimizely-Handles-Outliers))

한편, 해당 방법 역시 완벽한 것은 아니다. 비록 구현이 간단하다는 장점이 있으나, 대부분의 온라인 웹 데이터의 경우 앞서 언급한 것처럼 양의 왜도가 상당하기 때문에 데이터의 오리지널 분포가 완벽한 종모양을 따르는 경우는 거의 없다. 따라서 실제로 3-$\sigma$ rule이 어느 정도 유효하게 작용하기 위해서는 수집된 데이터에 대한 로그 변환 또는 [Box-cox 변환](https://towardsdatascience.com/box-cox-transformation-explained-51d745e34203) 등의 추가적인 밑작업이 필요할 수 있다. 

&nbsp;

## CUPED (Microsoft)

앞서 살펴본 두 방법과는 다르게, 2013년에 마이크로소프트에서 발표한 [CUPED (Controlled-experiment Using Pre-Existing Data)](https://exp-platform.com/Documents/2013-02-CUPED-ImprovingSensitivityOfControlledExperiments.pdf) 라는 방법론은 좀 더 까다로운 통계적 디테일을 포함하고 있다. 

해당 방법론의 핵심적인 아이디어는 통계학의 ***control variable*** 을 응용한 것으로, 특정한 실험이 진행되기 이전에 갖고 있는 historical data를 공변량으로 활용해서 지표의 분산을 보정해주겠다는 내용이다. 이렇게만 말하면 무슨 말인지 이해하기가 어렵기 때문에 구체적인 예시를 바탕으로 살펴보도록 하자.

여기서는 설명의 편의를 위해, 특정한 유저 3명이 있다고 가정하도록 하겠다. 우리의 관심 지표는 각 유저별 **평균 매출액**이다. 직관적으로, 우리는 각 유저별로 일정한 **"소비 성향"**이 있을 것이라고 생각해볼 수 있다. 즉, 한마디로 기존에 매출액이 높았던 유저들은 앞으로도 매출액이 높을 것이며, 반대로 기존에도 거의 구매를 하지 않던 유저들은 앞으로도 별로 구매를 하지 않을 것이라는 아이디어이다. 물론 이와 같은 유저들의 행동 패턴은 시간이 지남에 따라 바뀔 수 있으나 전체적인 모집단의 수준에서는 어느 정도 일정한 경향성이 유지될 것이다. 이러한 맥락에서 ***"control variable"* 이란 각 유저별 과거 시점에서 집계된 데이터가 된다 (pre-experiment)**. 

&nbsp;

<center>
  <img src="{{site.baseurl}}/images/abtest/43.png" width=500 height=300/> 
 <br>
 <em><span style="color:grey"></span></em>
</center>

&nbsp;

이처럼 가설 검정에 대한 통계 모형에 control variable을 포함시키게 될 경우, 각 유저별 과거 시점과 현 시점의 중복된 정보량이 어느 정도 상쇄되고 오로지 관심 변수 (treatment) 의 순수한 효과만이 남게 될 것이다. 그리고 이렇게 불필요한 정보가 통계적으로 보정되었기 때문에 결과적으로 지표의 변동성 (i.e. 분산) 이 감소한다는 사실은 그리 어렵지 않게 받아들일 수 있다.

이렇게 CUPED 방법론의 직관적인 아이디어를 이해했다면, 다음으로는 통계적 측면에서의 정의를 살펴보자. 

과거 시점의 control variable를 편의상 $X$ 로 정의할 때, 대조군 $Y_C$ 와 시험군 $Y_T$ 각각에 대한 현 시점에서의 평균 $\bar Y_C, \bar Y_T$ 를 구하기 위해 CUPED는 다음과 같은 estimator (추정량) 를 활용한다.

<center>

$$
\bar Y_{i}^* = \bar Y_i - \theta^*\big(\bar X_i - \mathbb{E}(X)\big), \quad (i\in\{C,T\})
$$

</center>

&nbsp;

위 estimator을 구체적으로 뜯어보자. 우선 $\bar Y_i$는 현재 진행된 실험에서 수집된 데이터의 표본 평균이다 (노테이션의 편의상 유저에 대한 인덱스는 생략했다). 이 때 CUPED estimator $\bar Y_i^*$ 는 $\bar Y_i$ 로 부터 무언가를 뺀 값으로 정의되는 것을 확인할 수 있다. 그리고 바로 이 "무언가"가 바로 앞서 언급한 control variable, 즉 과거 시점의 데이터가 되는 것이다. 이제 남은 것은 공변량의 앞에 붙어 있는 $\theta^\*$ 인데, 이는 어떠한 상수로써 분산을 최소화시킬 수 있는 값으로 설정된다 (잠시 뒤 다시 살펴보겠다).

한편, 이렇게 새롭게 정의된 estimator는 당연하게도 통계적으로 타당해야만 한다. 이를 살펴보기 위해서 해당 estimator에 평균을 취하면 무슨일이 일어나는지를 살펴보자. 

<center>

$$
\begin{aligned}
\mathbb{E}\big[ \bar Y_i^* \big] &= \mathbb{E}\Big[\bar Y_i - \theta^*\big(\bar X_i - \mathbb{E}(X)\big) \Big] \\[10pt]

&=\mathbb{E}[\bar Y_i] - \theta^*\mathbb{E}\big[\bar X_i - \mathbb{E}[X]\big] \\[10pt]

&=\mathbb{E}[\bar Y_i] - \theta^* \big( \mathbb{E}[\bar X_i] - \mathbb{E}[X] \big) \\[10pt]

&= E[\bar Y_i]

\end{aligned}
$$

</center>

&nbsp;

즉, CUPED estimator는 **불편추정량 (unbiased estimator)** 임을 확인할 수 있다.

다음으로는 어떻게 하면 분산을 최대로 감소시킬 수 있을지와 관련해서, 가장 최적의 $\theta$ 값을 유도해보도록 하자.

&nbsp;

<center>

$$
\begin{aligned}
Var\Big(\bar Y_i - \theta\big(\bar X_i - \mathbb{E}(X)\big) \Big) &= Var\big(\bar Y_i - \theta \bar X_i \big) \\[10pt]
&= \frac{1}{n} Var(Y_i - \theta X_i) \\[10pt]

&= \frac{1}{n}\Big( Var(Y_i) + \theta^2 Var(X_i) - 2\theta\;\text{Cov}(Y_i, X_i) \Big) \\[10pt]

&= C_1\Big( \theta - \frac{\text{Cov}(Y_i, X_i)}{Var(X_i)} \Big)^2 + C_2 ,\quad (C_1, C_2 \text{ is some constant terms}) \\[10pt]
&\therefore \text{minimized when }\;\theta = \frac{\text{Cov}(Y_i, X_i)}{Var(X_i)} \;\text{ by convexity.}

\end{aligned}
$$

</center>

&nbsp;

위 유도 과정은 CUPED estimator의 분산을 $\theta$ 에 대한 2차 함수로 생각한 다음, 해당 함수를 최소화하는 극값을 찾은 것이다. 그렇다면 이어서 이렇게 찾은 $\theta^\*$ 값을 실제로 CUPED estimator에 대입한 다음, 분산을 계산해보도록 하자.

&nbsp;

<center>

$$
\begin{aligned}
Var\Big(\bar Y_i - \theta\big(\bar X_i - \mathbb{E}(X)\big) \Big) &= 
Var\Big(\bar Y_i - \frac{\text{Cov}(Y_i, X_i)}{Var(X_i)} \big(\bar X_i - \mathbb{E}(X)\big) \Big) \\[10pt]

&= Var\Big(\bar Y_i - \frac{\text{Cov}(Y_i, X_i)}{Var(X_i)} \bar X_i \Big) \\[10pt]

&= \frac{1}{n}\Big( Var(Y_i) - \frac{\text{Cov}(Y_i, X_i)^2}{Var(X_i)} \Big) \\[10pt]

&= \frac{1}{n}Var(Y_i) \Big( 1 - \frac{\text{Cov}(Y_i, X_i)^2}{Var(Y_i)\;Var(X_i)} \Big) \\[10pt]

&= \frac{1}{n}Var(Y_i) ( 1 - \rho^2 ) \leq Var(\bar Y_i) \\[10pt]
&\quad\Big(\text{where }\; \rho = Corr(Y_i, X_i) = \frac{\text{Cov}(Y_i, X_i)}{\sqrt{Var(Y_i)Var(X_i)}} \Big) \\[10pt]


\end{aligned}
$$

</center>

&nbsp;

결론적으로 CUPED estimator의 분산은 기존보다 작아진다는 것이 증명되었으며, 흥미로운 점은 분산의 감소량이 정확히 control variable $X_i$ 와 수집된 지표 $Y_i$ 의 **상관계수를 제곱한 것**과 일치한다는 것을 확인할 수 있다.

자 그렇다면 이제 마지막으로 남은 것은 CUPED estimator를 정의하는 과정에서 필요한 control variable의 모평균 $\mathbb{E}(X)$ 이다. 이것이 문제가 되는 이유는 바로 **해당 값을 정확히 구할 방법이 없기 때문이다**. 하지만 다행히도, 우리의 관심사는 개별적인 실험 집단 (대조군, 실험군) 이 아니라 바로 두 집단의 차이에 대한 결과이다. 이러한 측면에서 두 모평균의 차이는 $\mathbb{E}(X_C), \mathbb{E}(X_T)$ 에 **의존하지 않는다**. 

&nbsp;

<center>

$$
\begin{aligned}
\bar Y_T^* - \bar Y_C^* &= \Big( \bar Y_T - \theta^*\big(\bar X_T - \mathbb{E}(X)\big) \Big) - \Big( \bar Y_C - \theta^*\big(\bar X_C - \mathbb{E}(X)\big) \Big) \\[10pt]
&=(\bar Y_T - \bar Y_C) - \theta^*(\bar X_T - \bar X_C) \\[10pt]
&= (\bar Y_T - \bar Y_C) - \frac{\text{Cov}(Y_i, X_i)}{Var(X_i)} (\bar X_T - \bar X_C)
\end{aligned}
$$

</center>

&nbsp;

따라서 결론적으로 CUPED 방법론을 사용하면 동일한 모평균의 차이를 추정하되, 더욱 낮은 분산을 얻을 수 있어 실험의 유의성을 주장할 수 있는 가능성 (i.e. 검정력) 이 증가하는 큰 이점이 있는 것이다. 이러한 이유로 인해 현재 Netflix, Booking, Bing 등 다양한 플랫폼에서 CUPED를 활용하고 있다.



&nbsp;

## OTPSM (Yahoo)

OTPSM이란 "**Outward Testing Procedure with Sample Maximum**" 의 줄임말로, 한마디로 요약하자면 주어진 데이터에 특정한 형태의 통계 분포를 적합시킨 다음, 이를 바탕으로 영향점(influential point)들의 값이 아웃라이어인지 여부를 판단하겠다는 아이디어이다. 해당 방법론은 2016년 컨퍼런스 페이퍼로 발표되었으며, Oath(야후)의 실험 플랫폼에 적용되고 있는 아웃라이어 처리 방법론이다. 

앞서 아웃라이어 처리 과정에 정규분포의 성질을 활용하는 3-sigma method에 대해 살펴보았다. 하지만 대부분의 웹 데이터는 왜도가 상당히 높은 (heavily-skewed) 형태의 분포를 갖기 때문에, 정규분포는 이러한 형태의 데이터에 잘 맞지 않을 수 있다는 점을 언급한 바 있다. 이를 해결하기 위해 해당 논문은 "페이지뷰", "세션수"와 같은 "count metric"에 대해 **음이항분포 (negative binomial)** 를 기반으로 하는 혼합 분포 (mixture distribution)을 활용했다. 

논문의 저자들은 OTPSM이 온라인 실험에서 발생하기 쉬운 아웃라이어의 *masking effect*와 *swamping effect* (E. Acuna and C. Rodriguez, 2004) 를 고려할 수 있다는 점을 주장하였다. 추가적으로, 기존의 방법론들에 비해 outlier threshold가 보수적이기 때문에 데이터의 변형이 크지 않다는 점이 장점으로 제시된 바 있다 (Y. He and M. Chen, 2016).

OTPSM의 알고리즘을 요약하자면 다음과 같다.

<center>
  <img src="{{site.baseurl}}/images/abtest/44.png" width=800 height=600/> 
 <br>
 <em><span style="color:grey"></span></em>
</center>

&nbsp;

해당 step들에 대한 구체적인 내용을 살펴보기 이전에, OTPSM 알고리즘을 바탕으로 아웃라이어 처리가 수행되는 과정을 시각적으로 살펴보면 다음과 같다.

<center>
  <figure class="animated_gif_frame"> 	
    <img src="{{site.baseurl}}/images/abtest/OTPSM.gif" data-source="/images/bayesian/MH_donut.gif" width="900" height="600" /> 
      <br>
    <em><span style="color:grey">OTPSM 의 적용 절차</span></em>
  </figure>
</center>

&nbsp;

이제 본격적으로 OTPSM의 디테일을 파헤쳐보도록 하겠다.

&nbsp;

#### Step 1. DEBETAs 계산

DFBETAs란 **"Difference in Betas"**의 줄임말로, 회귀 모형에서 각 데이터 포인트의 영향력을 수치화한 값이다. 

구체적으로, DFBETAs는 **"특정 데이터를 제외한 회귀 계수"**와 **"모든 데이터를 이용한 회귀 계수"**의 차이를 이용하여 다음과 같이 정의된다. 

<center>
  <img src="{{site.baseurl}}/images/abtest/45.png" width=800 height=500/> 
 <br>
 <em><span style="color:grey"></span></em>
</center>

&nbsp;

이러한 맥락에서 왜 갑자기 회귀 모형이 등장했는지 의아할 수 있는데, 그 이유는 우리가 활용하는 **이표본 t-검정이 선형 회귀 모형의 특수한 케이스이기 때문이다**. 좀 더 구체적으로, 실험집단이 독립변수인 선형 회귀 모형에서 **기울기에 대한 가설 검정**이 바로 그 유명한 two sample t-test가 된다. 이와 관련한 자세한 설명은 [다음의 포스트](https://medium.com/@wyess/demystifying-statistical-analysis-2-the-independent-t-test-expressed-in-linear-regression-434a3b302289)를 참고하라.

결론적으로 DFBETAs 값이 크다는 것은 해당 데이터를 제거한 경우 **전체적인 regression fit이 상당히 달라졌다는 의미**이며, 따라서 해당 데이터를 아웃라이어로 판단할 수 있는 (어느 정도) 객관적인 근거로 활용될 수 있다. (아래의 그림 참고)

<center>
  <img src="{{site.baseurl}}/images/abtest/46.png" width=750 height=500/> 
 <br>
 <em><span style="color:grey"></span></em>
</center>

&nbsp;

#### Step 2. 계산된 DFBETAS를 바탕으로 inlier / outlier group 분리

Step 1에서 구한 DFBETAs 값을 바탕으로 집계된 데이터를 대략적으로 **"인라이어"**와 **"아웃라이어"**로 구분하는 작업이다.

일반적으로 회귀 분석에서는 (데이터의 개수가 충분한 경우) DFBETAs 값이 "2 / #데이터" 보다 큰 경우를 **이상점 (influential point)**라고 정의한다는 점을 참고해서 아웃라이어를 정의한다.

&nbsp;

#### Step 3. inlier group의 데이터에 대해 통계 분포를 적합

앞서 아웃라이어 처리는 통계 분포의 성질을 이용해서 진행된다는 것을 살펴보았다. 동일한 맥락에서 우리는 inlier group의 데이터를 잘 설명할 수 있는 통계 분포를 찾아 적합시키는 것이 필요하다.

그렇다면 어떠한 분포를 사용하는 것이 좋을까? 일반적으로 count data에는 **음이항분포**가 가장 적합하다는 것이 알려진 바 있다 (A. Lindén, 2011). 간단히 복습하자면, 음이항분포는 **포아송분포**의 **"평균과 분산이 동일하다"**라는 제약조건을 완화시킨 좀 더 현실적인 통계 모형이라고 할 수 있다. 해당 논문에서도 페이지뷰 지표에 대해 여러 통계분포를 적용한 결과, 음이항분포의 적합도가 정규분포와 포아송분포보다 훨씬 더 좋다는 사실이 확인되었다.

<center>
  <img src="{{site.baseurl}}/images/abtest/47.png" width=350 height=250/> 
 <br>
 <em><span style="color:grey"></span></em>
</center>

&nbsp;

한편, 온라인 A/B Test의 환경에서 수집된 데이터는 skewness가 심한 경우가 대부분이라고 했었다. 그 중에서도 특히나 값이 0인 데이터의 비율이 상당히 높은 것을 종종 확인할 수 있는데, 이러한 **"zero-inflation"** 을 고려하기 위해 해당 논문에서는 "0인 부분"과 "0이 아닌 부분"을 개별적으로 모델링하기 위한 혼합 분포 (mixture distribution), 이른바 "**Hurdle Model**" 을 소개하고 있다.

통계적인 표현을 빌리자면, 이는 two-component model로써 0값을 모델링하기 위한 generative process와, 0이 아닌 positive probability mass를 모델링하기 위한 절삭 음이항분포 (**Zero-Truncated Negative Binomial Distribution: *ZTNB***) 로 구성된 통계 모형이다. 수식으로 표현하면 다음과 같다.

<center>

$$
Y_i = \begin{cases} \mbox{0,} & \text{with probability } 1-p \\[10pt] 
ZTNB(\rho, \gamma) & \text{with probability }p \end{cases}
$$

</center>

&nbsp;

여기서 ZTNB는 아래와 같은 확률 질량 함수를 갖는다.

<center>

$$
P(y_i \; | \; y_i >0,\;\rho,\;\gamma) = \frac{1}{1-(1-\rho)^\gamma} \frac{\Gamma(y_i + \gamma)}{y_i! \;\Gamma(\gamma)} (1-\rho)^\gamma \rho^{y_i}
$$

</center>

&nbsp;

정리하자면, step 3는 inlier group의 데이터를 바탕으로 위와 같은 Hurdle Model의 모수 $p, \gamma, \rho$ 를 추정하는 과정이라고 이해할 수 있다. 해당 모델의 모수를 추정하는 방법은 여러가지가 있으나, 컴퓨팅 시간을 고려할 때 *Maximum Likelihood Estimation* 가장 효율적일 것이라고 판단된다. 이 때 모수 $p$의 MLE는 관측 데이터에서의 0인 부분의 비율에 대응되며, 모수 $\gamma$ 와 $\rho$ 는 0이 아닌 데이터에 대해 $X = X-1$의 변수변환을 적용한 다음 음이항분포를 적합시키는 것으로 구할 수 있다.

다음은 실제 데이터에 Hurdle Model 을 적합시킨 결과이다. 빨간색 선으로 표현된 확률질량함수가 데이터를 충분히 잘 설명하는 것을 확인할 수 있다.

<center>
  <img src="{{site.baseurl}}/images/abtest/49.png" width=500 height=350/> 
 <br>
 <em><span style="color:grey"></span></em>
</center>

&nbsp;

#### Step 4. 추정된 통계 분포를 바탕으로 아웃라이어 검증

앞서 step 2를 통해 수집된 데이터는 크게 인라이어와 아웃라이어로 나뉘어졌다. 이제 우리의 관심사는 해당 단계에서 임시로 아웃라이어로 구분된 데이터 중에서 실제 아웃라이어를 정의하는 것이다.

한편, 우리는 step 3를 바탕으로 inlier group의 데이터를 잘 설명하는 통계 분포를 얻을 수 있었다 (Hurdle Model). 이를 이용해 step 4에서는 적합된 Hurdle Model을 바탕으로 아웃라이어 그룹의 데이터들을 평가하는 과정이 진행된다. 해당 절차는 다음과 같다.

&nbsp;

**1. 아웃라이어 그룹의 데이터를 오름차순으로 정렬**

**2. 정렬된 아웃라이어 그룹의 최소값에 대하여, 해당 값이 Hurdle Model을 바탕으로 "이론적 최대값 (i.e. Sample Maximum)이 될 확률"을 검정통계량으로 하는 가설 검정을 수행**

<center>
  <img src="{{site.baseurl}}/images/abtest/50.png" width=700 height=500/> 
 <br>
 <em><span style="color:grey"></span></em>
</center>

&nbsp;

**3. 계산된 p-value에 따라 다음의 작업을 수행**

- **p-value가 유의수준보다 작은 경우**: 해당 최소값을 outlier threshold로 설정하고 알고리즘을 종료
- **p-value가 유의수준보다 큰 경우**: 해당 최소값을 inlier group으로 이동시킨 다음 step 3로 이동

&nbsp;

참고로, count data에 대해 OTPSM을 적용하는 경우 중복된 값을 **특정한 값을 배치단위**로 해서 알고리즘을 적용한다면 실행 속도를 높일 수 있다.

정리하자면, 실제 실험에서 OTPSM 알고리즘의 적용 절차는 다음과 같이 요약할 수 있다.

&nbsp;

**1) 아웃라이어 필터링을 적용할 주 평가지표 (primary metric) 정의**

- 정상적인 패턴과 비정상적인 패턴이 뚜렷히 구분되는 지표일수록 좋다.
- 일반적으로 "세션 수" 지표가 추천된다.



**2) outlier threshold의 업데이트 주기 정의**

- 일반적으로 "하루 단위"로 outlier threshold를 설정하는 것이 추천된다.
- 만약 연샨량이 너무 과다해 실행 속도가 느린 경우, 업데이트 주기를 좀 더 길게 가져가는 것도 방법이다. 대부분의 실험의 경우 outlier threshold는 일정한 수준으로 유지되기 때문이다 (아래 그래프 참고).
- 실험 컨텐츠에 주기성 또는 계절성이 있는 경우 이를 고려해 주기를 설정하는 것이 바람직하다. (e.g. day of week effect)

<center>
  <img src="{{site.baseurl}}/images/abtest/51.png" width=350 height=300/> 
 <br>
 <em><span style="color:grey"></span></em>
</center>&nbsp;

**3) 실험 데이터 수집 & OTPSM 적용**



**4) 계산된 outlier threshold에 부합되는 엔트리(유저)를 제거**

- "아웃라이어"로 판별된 유저의 아이디는 해당 실험에서 제외된다.



**5) 실험이 끝날 때까지 3~4를 반복**

&nbsp;

#### Implementation

OTPSM에 대한 파이썬 기반의 pseudo-code는 다음과 같다.

<script src="https://gist.github.com/domug/49afdbdb33dbf2deb156a92c41238241.js"></script>

&nbsp;

&nbsp;





---

# Reference

- [https://www.dynamicyield.com/lesson/outliers-detection/](https://www.dynamicyield.com/lesson/outliers-detection/)
- [https://towardsdatascience.com/68-95-99-7-the-three-sigma-rule-of-thumb-used-in-power-bi-59cd50b242e2](https://towardsdatascience.com/68-95-99-7-the-three-sigma-rule-of-thumb-used-in-power-bi-59cd50b242e2)
- [https://uxplanet.org/how-to-clean-ab-testing-data-before-analysis-113e6bfeb164](https://uxplanet.org/how-to-clean-ab-testing-data-before-analysis-113e6bfeb164)
- He, Y. (2018). Implementation of A Robust Universal Outlier Filter for Online Experimentation.
- Y. He and M. Chen, "A Probabilistic, Mechanism-Indepedent Outlier Detection Method for Online Experimentation," 2017 IEEE International Conference on Data Science and Advanced Analytics (DSAA), 2017, pp. 640-647, doi: 10.1109/DSAA.2017.64.
- Deng, A., Xu, Y., Kohavi, R., & Walker, T. (2013). Improving the sensitivity of online controlled experiments by utilizing pre-experiment data. *WSDM '13*.



