---
layout: post
title: "Asymptoticity of Maximum Likelihood Estimator"
date: 2021-09-05 00:00:00
image: 10.jpg
tags: [statistics]
categories: statistics
use_math: true
---

&nbsp;

[저번 포스트](https://domug.github.io/2021/09/04/Fisher_Information/)에 이어 계속해서 수리통계학에서 "통계적 추정" 파트의 핵심적인 내용인 Maximum Likelihood Estimator의 유용한 성질들 몇가지에 대해서 복습을 해보고자 한다. 결론부터 이야기하면 MLE의 가장 큰 장점 중에 하나는 바로 상당히 좋은 **asymptotic properties**를 갖고 있다는 점인데, 몇가지 정리를 통해 왜 MLE가 모수의 추정치로 흔히 사용되는지를 이야기할 것이다.

&nbsp;

---

#### Asymptoticity

본격적으로 논의를 시작하기에 앞서, **asymptotic property**가 도대체 무엇인지에 대해 이야기해보자. 이는 한국어로 직역하면 "점근적 성질" 정도가 될 것 같은데, 말이 어렵고 직관적으로 와닿지를 않기 때문에 원어 그대로를 사용하도록 하겠다.

통계학에서 asymptotic property 란 간단히 말해서 샘플 사이즈가 무한히 큰 상황에서 정의되는 통계량의 성질을 의미한다. 이러한 가정을 하는 이유는 이를 통해 유한한 샘플 사이즈에서는 도출할 수 없었던 통계량들의 유용한 성질을 발견할 수 있기 때문인데, 이것의 대표적인 예시가 대수의 법칙 (weak law of large numbers) 과 중심극한정리 (Central Limit Theorem) 이다. 

가령 중심극한정리를 생각해보자. 중심극한정리란 분산이 유한한 **임의의 분포를 따르는 랜덤 샘플** $X_1, X_2, ... , X_n$ 에 대해서 그 **표본평균**이 평균이 $\mu$, 분산이 $\frac{\sigma^2}{n}$ 인 정규분포로 분포수렴한다는 정리이다. 이는 특정한 확률 변수의 분포에 대한 가정이 전혀 없어도 해당하는 모평균에 대한 근사적인 신뢰구간을 항상 구할 수 있다는 점에서 큰 의미를 갖는데, 이 경우 asymptotic한 상황 아래에서 표본평균이 정규분포로 분포수렴 한다는 성질을 도출한 것이다. 

이러한 아이디어는 통계적 추론의 프레임워크에서 상당히 유용하게 사용된다. 예를 들어, 수리통계학 시간에 특정한 추정치 (estimator) 를 평가하기 위한 여러 기준이 있다는 것을 공부했었다. 피셔가 제시한 대표적인 평가기준으로는 consistency, unbiasedness, efficiency, completeness 등이 있었는데, 현실적인 상황 속에서 유한한 샘플 사이즈에서는 해당 평가 기준들을 만족하지 않지만 asymptotic한 상황 아래에서는 해당 기준들을 만족하는 estimator 들이 상당히 많고, MLE가 그 대표적인 예시이다. 이제 MLE의 asymptotic properties에 대해 좀 더 구체적으로 살펴보도록 하자.

&nbsp;

---

#### Rao Cramer Lower Bound and Efficiency

추정치의 평가 기준 중 하나인 **efficiency** 를 정의하기 위해서는 Rao-Cramer Lower Bound라는 개념이 필요하다. 해당 정리는 다음과 같다.

> **\<Theorem>**
>
> Suppose $X_1, ... , X_2 \overset{iid}{\sim} f(x ; \theta)$,
>
> Assume that some regularity conditions hold.
>
> Let $Y = u(X_1, X_2, ... , X_n)$ with $E(Y) = k(\theta)$.
>
> Then, $Var(Y) \geq \frac{(k^{\prime}(\theta))^2}{n I(\theta)}$, where $k^{\prime}(\theta) = \frac{\partial k(\theta)}{\partial \theta}$ and $I(\theta)$ is the Fisher information of $\theta$

&nbsp;

해당 정리에서 통계량 $Y$는 랜덤 샘플들로부터 새롭게 정의된 추정치이며, Rao Cramer Lower Bound란 해당 추정치의 분산의 최소값을 제시한다. 이것이 의미하는 바가 무엇인지 살펴보자. 

특정한 모수를 추정하기 위한 estimator는 무한히 많이 정의될 수 있다. 그 중에서도 가급적이면 모수를 추정함에 있어서 변동성이 가장 낮은, 즉 최소의 분산을 갖는 estimator를 좋은 estimator로 정의하고 이를 선호하는 것은 당연할 것이다 (좀 더 구체적인 내용은 [해당 포스트](https://domug.github.io/2020/12/03/Statistical_Inference/)를 참고). 이러한 측면에서 Rao-Cramer Lower Bound는 특정한 estimator가 가질 수 있는 분산의 최소값이며, 해당 lower bound를 달성(attain)하는 estimator를 **efficient**한 estimator라고 통계적으로 정의한다. 흥미롭게도 이는 [저번 포스트](https://domug.github.io/2021/09/04/Fisher_Information/)에서 다뤘던 Fisher Information과 관련이 있는데, 위 부등식에서 estimator가 모수에 대해 담고 있는 정보량이 많으면 많을수록 ($I(\theta)$의 값이 클수록) estimator의 분산이 작아지는 것은 직관적으로 자연스럽다.

&nbsp;

그렇다면 MLE는 과연 efficient 할까? 결론부터 말하자면 그렇지 않다. 다음의 예시를 살펴보자.

>Suppose that $y = {Y_1, Y_2, · · · , Y_n}$ are i.i.d. exponential random variables with unknown mean $\frac{1}{\theta}$. Find the MLE of $\theta$ and state whether it is efficient.

&nbsp;

해당 문제에 대한 답을 구해보자.

주어진 관측치를 
$$
\textbf y = \{y_1, y_2, ... , y_n\}
$$
라고 할 때 MLE는 다음과 같이 구할 수 있다.

&nbsp;



<center>

$$
\begin{aligned}
f(\textbf y;\theta) &= \prod_{k=1}^{n} f(y_k;\theta) \\[10pt]
&= \prod_{k=1}^{n}\theta \; exp(-\theta y_k) \\[10pt]
&= \theta^n \; exp(-\theta \sum_{k=1}^n y_k)
\end{aligned}
$$

</center>

&nbsp;

현재 랜덤샘플 $y_i$ 들은 지수분포를 따르고, 지수분포의 pdf에 로그를 취한 함수가 concave function이라는 점에서 다음과 같이 간단하게 MLE를 구할 수 있다.

&nbsp;

<center>

$$
\begin{aligned}
\frac{\partial log f(\textbf y;\theta)}{\partial \theta} &= \frac{n}{\theta} - \sum_{k=1}^n y_k \\[10pt]
&\overset{set} {\equiv} 0 \\[20pt]
\end{aligned}
$$

</center>

<center>

$$
\therefore \hat\theta_{MLE} = \frac{n}{\sum_{k=1}^n y_k}
$$

</center>

&nbsp;

다음으로, Rao Cramer Lower Bound (RCLB) 를 구하기 위해 $\hat \theta_{MLE}$의 평균과 Fisher Information을 계산한다. 이 과정에서 새로운 통계량을  $T(\textbf y) = \sum_{k=1}^n y_k$와 같이 정의하면, 분포적 성질을 이용해  $T(\textbf y)$가 Erlang 분포를 따르는 것을 유도할 수 있다 (개념적으로, 지수분포에서의 구간을 K개 만큼 더한 분포가 Erlang-K 분포가 된다). Erlang 분포의 pdf $f_T(t) = \frac{\theta^n t^{n-1}}{\Gamma(n)}exp(-\theta t)$ 를 이용해 MLE의 평균과 분산을 구하면 다음과 같다.

&nbsp;

<center>

$$
\begin{aligned}
E[\hat \theta_{MLE}] &= E[\frac{n}{T(\textbf y)}] \\[10pt]
&= \int_0^{\infty} \frac{n}{t} \frac{\theta^n t^{n-1}}{\Gamma(n)}exp(-\theta t)dt \\[10pt]
&= \frac{n\theta}{n-1} \int_0^{\infty} \frac{\theta^{n-1} t^{n-2} }{\Gamma(n-1)} exp(-\theta t)dt \\[10pt]
&= \frac{n}{n-1} \theta
\end{aligned}
$$

</center>

&nbsp;

<center>


$$
\begin{aligned}
Var(\hat \theta_{MLE}) &= E[\hat \theta_{MLE}^2] - E[\hat \theta_{MLE}]^2 \\[10pt]
&= E[\frac{n^2}{T(\textbf y)^2}] - (\frac{n}{n-1}\theta)^2 \\[10pt]\\
&= n^2 \int_0^{\infty} \frac{1}{t^2} \frac{\theta^n t^{n-1}}{\Gamma(n)} exp(\theta t)dt - (\frac{n}{n-1}\theta)^2 \\[10pt]
&= \frac{n^2 \theta^2}{(n-1)(n-2)} \int_0^{\infty} \frac{\theta^{n-2} t^{n-3}}{\Gamma(n-2)} exp(-\theta t) dt - (\frac{n}{n-1}\theta)^2 \\[10pt]
&= \frac{n^2}{(n-1)^2 (n-2)} \theta^2
\end{aligned}
$$
</center>

&nbsp;

계산된 MLE의 평균을 통해서 알 수 있는 것은 MLE는 편향된 추정치라는 것이다. 즉 **MLE는 unbiased estimator가 아니다**. 왜냐하면 unbiasedness를 만족하기 위해서는 추정량의 평균이 모수와 같아야 하지만 해당 MLE는 앞에 $\frac{n}{n-1}$ 이라는 상수가 붙어있기 때문이다. 

하지만 만약 해당 상황에서 샘플 사이즈가 무한하다는 가정이 있으면 앞서 구한 MLE는 unbiased한 추정량이 된다 ($\;\because \underset{n \leftarrow \infty} {lim} \frac{n}{n-1}\theta = 1 \;$). 

다시 말해, MLE는 **asymptotic 하게는 unbiased**한 estimator라는 의미이다. 유사하게, 이번에는 Fisher Information을 구해서 해당 MLE의 RCLB를 구해보자.

해당 MLE의 Fisher Information은 다음과 같다.

<center>

$$
\begin{aligned}
I(\theta) &= -E_y[\frac{\partial^2}{\partial \theta^2}logf(\textbf y ; \theta)] \\[10pt]
&= -E_y[\frac{\partial^2}{\partial \theta^2}(nlog\theta -\theta\sum_{k=1}^n y_k)] \\[10pt]
&= -E_y[-\frac{n}{\theta^2}] \\[10pt]
&= \frac{n}{\theta^2}
\end{aligned}
$$

</center>

따라서 RCLB는 다음과 같이 구해진다.

<center>

$$
\begin{aligned}
RCLB(\theta) &= \frac{(\frac{\partial}{\partial\theta}E[\hat\theta_{MLE}])^2}{n I(\theta)} \\[10pt]
&= \frac{(\frac{n}{n-1})^2}{n \frac{n}{\theta^2}} \\[10pt]
&= \frac{\theta^2}{(n-1)^2}
\end{aligned}
$$

</center>

&nbsp;

앞서 정의한대로, 어떤 estimator가 efficient 하려면 그 분산이 RCLB의 하한값과 동일해야 한다. 하지만 위 경우, $Var(\hat \theta_{MLE}) = \frac{n^2}{(n-1)^2 (n-2)} \theta^2$, $RCLB(\theta) = \frac{\theta^2}{(n-1)^2}$로 동일하지 않다. 따라서, 결론적으로 해당 MLE는 efficient하지 않은 추정량이라는 것을 확인할 수 있다. 하지만 이 역시 샘플 사이즈가 무한하다는 가정하에는 RCLB와 분산이 모두 0으로 수렴하므로 같아진다. 결론적으로, 해당 MLE는 asymptotic하게 unbiased하며, efficient한 추정량이라는 것을 확인할 수 있다. 

위 문제는 하나의 구체적인 예시일 뿐이지만, **일반적으로 MLE는 항상 asymptotically unbiased하며 efficient한 estimator이라는 것이 밝혀져있다**. 이에 대한 아주 중요한 정리가 있는데, 이를 살펴보도록 하겠다.

&nbsp;

---

#### Asymptotic Normality of MLE

> **\<Theorem>**
>
> Assume $X_1, X_2, ... , X_n \overset{iid}{\sim} f(x;\theta)\;$ for $\theta \in \Omega$ such that some regularity conditions are satisfied.
>
> Suppose that $0 < I(\theta) < \infty\;$.
>
> Then any consistent sequence of solutions of the MLE satisfies 
>
> $\sqrt{n} (\hat \theta_n - \theta_0) \overset{D}{\rightarrow} N(0, \frac{1}{I(\theta)})$,
>
> where $\hat \theta_n$ is a solution of estimating equation ($l^{\prime}(\theta) = 0$) and $\theta_0$ is the true parameter.

&nbsp;

해당 정리는 MLE의 극한분포가 정규분포로 수렴한다는 것을 제시하는 것으로, MLE가 통계학에서 왜 중요한지를 시사한다고 할 수 있다. 

구체적인 증명 과정을 보기에 앞서, 결론부터 이야기 하자면 특정한 모수 $\theta$에 대한 MLE는 항상 다음의 정규분포로 수렴한다.

&nbsp;

<center>

$$
\hat\theta_{MLE} \overset{D}{\rightarrow} N(\theta_0, \frac{1}{nI(\theta_0)})
$$

</center>

&nbsp;

즉, MLE는 앞서 살펴본 RCLB를 분산으로 갖는 정규분포로 항상 분포수렴한다. 따라서 앞서 예시를 통해 확인했던 것처럼 **MLE는 항상 asymptotic하게 unbiased하며 efficient한 estimator** 임을 알 수 있다. 여기에 더해 정규분포를 극한분포로 갖기 때문에 추정 및 검정이 용이하다는 점에서 MLE는 수리통계학에서 상당히 중요한 역할을 한다고 할 수 있다.

마지막으로 해당 정리의 유도 과정을 살펴보고 포스팅을 끝내도록 하겠다. 해당 유도 과정은 약간 복잡할 수 있지만 수리통계학적으로 흔히 사용되는 중요한 아이디어들이 많이 때문에 한번 정도는 line-by-line으로 살펴보고 넘어가는 것도 의미있을 것 같다.

&nbsp;



##### (proof)

MLE를 구할 때 사용하는 estimating equation의 좌변을 $l^{\prime}(\hat \theta_n)$ 이라고 하자.

$l^{\prime}(\hat \theta_n)$에 대해 테일러 전개 (taylor expansion) 을 적용하면 다음과 같이 나타낼 수 있다. 

&nbsp;

<center>


$$
l^{\prime}(\hat\theta_n) = l^{\prime}(\theta_0) + l^{\prime\prime}(\theta_0)(\hat\theta_n - \theta_0) + \frac{1}{2}l^{\prime\prime\prime}(\theta_n^*)(\hat \theta_n - \theta_0)^2 \;\;\;\;\; \dots \; (1)
$$
</center>

&nbsp;

테일러 전개의 3차 급수 이하의 나머지 항들을 편의상 하나로 묶어 표현한 것이 마지막 
$$
\frac{1}{2}l^{\prime\prime\prime}(\theta_n^*)(\hat \theta_n - \theta_0)^2
$$
이다. 이를 Mean Value Theorem이라고 지칭한다고 한다. (단, $\theta_n^*$는 $\theta_0$와 $\hat \theta_n$ 사이의 임의의 값).

&nbsp;

식 $(1)$을 바탕으로 일반적으로 MLE를 구하는 것처럼 estimating equation을 풀면 다음과 같다.

&nbsp;

<center>

$$
\begin{aligned}
0 = l^{\prime}(\theta_0) + (\hat \theta_n - \theta_0)\{ l^{\prime\prime}(\theta_0) + \frac{1}{2}l^{\prime\prime\prime}(\theta_n^*)(\hat \theta_n - \theta_0)^2 \}
\end{aligned}
$$

</center>

&nbsp;

위 식을 적절히 정리하면 다음과 같이 표현할 수 있다.

&nbsp;

<center>

$$
\Leftrightarrow (\hat \theta_n - \theta_0) = \frac{-l^{\prime}(\theta_0)}{\{ l^{\prime\prime}(\theta_0) + \frac{1}{2}l^{\prime\prime\prime}(\theta_n^*)(\hat \theta_n - \theta_0)^2 \}} \frac{\frac{1}{n}}{\frac{1}{n}} \\[30pt]
\Leftrightarrow \sqrt{n}(\hat \theta_n - \theta_0) = \frac{\frac{1}{\sqrt{n}}l^{\prime}(\theta_0)}{\{ -\frac{1}{n}l^{\prime\prime}(\theta_0) + -\frac{1}{2n}l^{\prime\prime\prime}(\theta_n^*)(\hat \theta_n - \theta_0)^2 \}} \;\;\;\;\; \dots \; (2)
$$

</center>

&nbsp;

자 이제 등식 $(2)$를 자세히 살펴보자. 

현재 $(2)$의 좌변은 앞선 정리에서 우리가 유도하고자 하는 식과 동일하다. 따라서 $(2)$의 우변 $\frac{\frac{1}{n}l^{\prime}(\theta_0)}{\{ -\frac{1}{n}l^{\prime\prime}(\theta_0) + -\frac{1}{2n}l^{\prime\prime\prime}(\theta_n^*)(\hat \theta_n - \theta_0)^2 \}}$이 정규분포로 수렴하는 것을 보이는 것이 우리의 최종적인 목표이다. 편의를 위해 해당 우변을 식 $(A)$라고 지칭하겠다. 추가적으로, $(A)$ 는 총 3가지의 식의 선형결합으로 이루어져 있는 것을 확인할 수 있다. 편의상 해당 식들을 다음과 같이 지칭하겠다.

&nbsp;

- $(i): \;\frac{1}{\sqrt{n}}l^{\prime}(\theta_0)$



- $(ii): \;-\frac{1}{n}l^{\prime\prime}(\theta_0)$



- $(iii): \;-\frac{1}{2n}l^{\prime\prime\prime}(\theta_n^*)(\hat \theta_n - \theta_0)^2$

&nbsp;

이제 식 $(i)$, $(ii)$, $(iii)$ 이 어떤 asymptotic property를 갖고 있는지를 순서대로 살펴보도록 하자.

---

**$(i)$**

<center>

$$
\begin{aligned}
\frac{1}{\sqrt{n}}l^{\prime}(\theta_0) &= \frac{1}{\sqrt{n}} \sum_{i=1}^n \frac{\partial\;logf(x_i;\theta_0)}{\partial \theta} \\[10pt]
&= \frac{n}{\sqrt{n}} (\frac{1}{n}\sum_{i=1}^n S(x_i;\theta_0) ) \\[10pt]
&= \sqrt{n} \; \bar S_n
\end{aligned}
$$

</center>

&nbsp;

여기서 $\bar S_n$은 Score Function의 표본 평균을 의미한다. (Score Function에 대한 내용은 해당 [포스트](https://domug.github.io/2021/09/04/Fisher_Information/) 참고)

$\bar S_n$의 평균은 0, 분산은 Fisher Information $I(\theta)$ 이라는 사실을 바탕으로 중심극한 정리를 적용하면 다음과 같다.

&nbsp;

<center>

$$
\begin{aligned}
\frac{\bar S_n - E[\bar S_n]}{\sqrt{Var(\bar S_n)}} &= \frac{\bar S_n}{\sqrt{\frac{I(\theta)}{n}}}\\[10pt]
&= \frac{\sqrt{n}\;\bar S_n}{\sqrt{I(\theta)}} \;\overset{D}{\rightarrow}\; N(0,1) \\[10pt]
\end{aligned}
$$

</center>

<center>

$$
\therefore \sqrt{n}\bar S_n \;\overset{D}{\rightarrow}\; N(0,I(\theta))
$$

</center>

&nbsp;

**따라서 식 $(i)$는 Fisher Information을 분산으로 갖고, 평균이 0인 정규분포로 분포수렴함을 알 수 있다.**

---

**$(ii)$**



<center>

$$
\begin{aligned}
-\frac{1}{n}l^{\prime\prime}(\theta_0) &= -\frac{1}{n}\sum_{i=1}^n \frac{\partial^2 \; logf(x_i;\theta_0)}{\partial \theta^2} \\[10pt]
&= \frac{1}{n} \sum_{i=1}^{n} -\frac{\partial^2 \; logf(x_i;\theta_0)}{\partial \theta^2}\\[10pt]
&\overset{p}{\rightarrow}\; E[-\frac{\partial^2\; logf(x_i;\theta_0)}{\partial \theta^2}] = I(\theta_0)
\end{aligned}
$$

</center>

&nbsp;

**즉, 식 $(ii)$는 대수의 법칙에 의해 Fisher Information으로 확률 수렴함을 알 수 있다.** (단, 관측치들이 서로 iid라는 가정하에)

---

**$(iii)$**



$-\frac{1}{2n}l^{\prime\prime\prime}(\theta_n^*)(\hat \theta_n - \theta_0)^2$은 다시 한번 다음과 같이 분리될 수 있다.

&nbsp;

<center>

$$
-\frac{1}{2n} \; \overset{p}{\rightarrow}\; 0
$$

&nbsp;

</center>

<center>

$$
l^{\prime\prime\prime}(\theta_n^*) \leq M(x)
$$

&nbsp;

</center>

<center>

$$
(\hat \theta_n - \theta_0)^2\; \overset{p}{\rightarrow}\; 0
$$

</center>

&nbsp;

여기서 $M(x)$란 임의의 상수로 $l^{\prime\prime\prime}(\theta_n^*)$의 값이 bounded 되어 있음을 나타낸다.

&nbsp;

<center>

$$
\therefore -\frac{1}{2n}l^{\prime\prime\prime}(\theta_n^*)(\hat \theta_n - \theta_0)^2 \; \overset{p}{\rightarrow} \; 0
$$

</center>

&nbsp;

**결론적으로, 확률수렴의 성질에 의해 각 구성요소의 곱으로 표현되는 $(iii)$ 역시 0으로 확률 수렴한다.**

---

위 결과들을 종합하면 다음과 같다.

&nbsp;

- 식 $(i)$는 $N(0, I(\theta_0))$로 분포 수렴한다.
- 식 $(ii)$는 $I(\theta_0)$로 확률 수렴한다.

- 식 $(iii)$는 0으로 확률 수렴한다.

&nbsp;

따라서 $\frac{(i)}{(ii) \;+\; (iii)}$의 형태로 표현되는 식 $(A)$는 **Slutsky's Theorem**에 의해 다음의 분포로 분포 수렴한다.

&nbsp;

<center>

$$
\begin{aligned}
(A) &= \frac{1}{I(\theta_0)} N(0, I(\theta_0))\\[10pt] 
&\overset{D}{\rightarrow} N(0, \frac{1}{I(\theta_0)})
\end{aligned}
$$

</center>

&nbsp;

최종적으로, 앞선 정리와 같이 $\sqrt{n} (\hat \theta_n - \theta_0) \overset{D}{\rightarrow} N(0, \frac{1}{I(\theta)})$가 성립함을 알 수 있고, MLE의 **asymptotic normality**가 증명되었다.

증명 과정이 약간 길어보이지만 핵심 아이디어는 테일러 전개와 확률변수의 수렴성을 적절하게 응용한 것이다. 이러한 테크닉은 수리통계학에서 굉장히 중요하고 자주 등장하니 확실히 알아두면 좋을 것 같다.











