---
layout: post
title: "Fisher Information"
date: 2021-09-04 00:00:00
image: 7.jpg
tags: [statistics]
categories: statistics
use_math: true
---

&nbsp;

수리통계학에 등장하는 Fisher Information 의 개념에 대해 간략하게 복습해보고자 한다. 수리통계학 수업을 수강한지가 벌써 꽤나 오래돼서 핵심 내용들을 많이 까먹었는데 마침 일반화 혼합모형 수업에서 해당 내용을 복습하라는 과제가 있어 겸사겸사 포스트를 작성하게 됐다. 이 포스트를 시작으로 Maximum Likelihood Estimation이랑 Statistical Test 내용 전반에 대해 정리해 볼 예정이다.



&nbsp;

---

**Fisher Information**  은 다음과 같이 정의된다.

&nbsp;

<center>


$$
\begin{aligned}
I(\theta) &= Var(\frac{\partial\;logf(X;\theta)}{\partial \theta}) \;\;\;\;\; \dots \;(1) \\[10pt]             
\end{aligned}
$$
</center>

&nbsp;

즉, 확률변수 $\frac{\partial\;logf(X;\theta)}{\partial \theta}$의 **분산**을 Fisher Information이라고 정의한다는 것인데, 구체적인 유도 과정을 살펴보기에 앞서 해당 값이 직관적으로 어떤 의미를 갖고 있는지 살펴보자.

&nbsp;

우선, 해당 확률변수 $\frac{\partial\;logf(X;\theta)}{\partial \theta}$를 구체적으로 뜯어보겠다.

&nbsp;

<center>


$$
S(\theta) = \frac{\partial\;logf(X;\theta)}{\partial \theta}
$$
</center>

&nbsp;

수리통계학 시간에 배웠던 최대우도추정 (Maximum Likelihood Estimation) 의 개념을 잠깐 복습해보자. **최대우도추정치 (MLE)** 란 특정한 확률분포의 likelihood를 최대화시키는 estimator를 의미한다. 이 때, **likelihood** 란 joint pdf 를 모수 $\theta$ 의 관점에서 해석한 것으로, 우리가 현재 알고 있는 관측치 $X$ 들이 특정한 $\theta$ 의 수준에서 얼마나 그럴싸한 값들인지를 나타내는 모수 $\theta$에 대한 함수라고 해석할 수 있다.

이러한 관점에서 주어진 관측치를 발생시킬 가능성이 가장 높은 값을 모수 $\theta$에 대한 추정치 $\hat \theta_{mle}$ 로 사용하는 것은 직관적으로 합리적인 선택이라고 할 수 있다 (물론 MLE는 추정치로서 굉장히 좋은 성질들을 갖고 있다는 것이 증명되어 있다). 이를 구체적으로 구하는 방법은 다음과 같았다.

&nbsp;

<center>

$$
\begin{aligned}
\hat \theta_{mle} = \underset{\theta}{\mathrm{argmax}} L(\theta;X) \\[10pt]
\Leftrightarrow \frac{\partial\; logf(\theta;x)}{\partial \theta} \equiv 0
\end{aligned}
$$

</center>



&nbsp;

계산의 편의를 위해 likelihood 함수에 log를 취하고, 극값을 찾기 위해 모수 $\theta$ 로 미분한 값을 0으로 놓고 푼다. 해당 등식은 **estimating equation**이라고 불린다. 만약 likelihood 함수가 concave function인 경우 어렵지 않게 MLE를 찾을 수 있다. 만약 해석적으로 구할 수 없는 경우 [Newton Raphson Method](https://ko.wikipedia.org/wiki/%EB%89%B4%ED%84%B4_%EB%B0%A9%EB%B2%95) 이나 [경사하강법](https://ko.wikipedia.org/wiki/%EA%B2%BD%EC%82%AC_%ED%95%98%EA%B0%95%EB%B2%95) 등의 근사적인 방법으로 해를 구하면 된다. 어쨌든, 중요한 점은 MLE를 구하기 위해서는 estimating equation을 풀어야 하고, 해당 estimating equation의 좌변이 우리가 앞서 정의한 확률변수 $S(\theta)$라는 것이다. 

그렇다면 도대체 $S(\theta)$는 무엇일까? 기하학적인 관점에서 $S(\theta)$ 는 특정한 log likelihood 함수와 특정한 $\theta$에서 접하는 접선의 **기울기**의 집합이다. 접선의 기울기의 절대값이 크다는 의미는 해당 지점에서 $\theta$의 값이 변함에 따라 값들이 확 바뀐다는 것을 의미한다. 다시 말해 기울기의 절대값이 큰 지점에서는 $\theta$가 약간만 변하더라도 대응되는 density가 크게 요동친다. 

따라서 $S(\theta)$는 모수 $\theta$에 대한 likelihood 함수의 **민감도 (sensitivity, volatility)** 정도로 이해하면 될 것 같다. 통계학자들은 이러한 $S(\theta)$에 대해 **Score Function** 이라는 특별한 명칭을 부여했다. 앞서 MLE가 Score Function이 0이 되는 $\theta$의 값임을 확인했다. 직관적으로 생각해보면 좋은 추정치이기 위해서는 추정치가 안정적(stable) 일 필요성이 있을 것이다. 모수를 추정하기 위해 쓰이는 값이 작은 변화에 민감하게 바뀌면 곤란할 것이기 때문이다. 따라서 주어진 관측치들에 대해 민감도가 가장 낮은, 즉 0이 되는 지점인 MLE가 좋은 추정치임을 한번 더 확인할 수 있다.

한편, Score Function은 특별한 성질이 있다. 바로 그 평균이 항상 0이라는 것인데, 왜 그런지를 유도해보자.

&nbsp;

---

우선 pdf의 성질에 의해 아래의 등식은 자명하다.

<center>

$$
1 = \int_{-\infty}^{\infty} f(x;\theta)dx
$$

</center>

해당 식에서 양변을 $\theta$에 대해 미분한다.

<center>

$$
0 = \int_{-\infty}^{\infty} \frac{\partial f(x;\theta)}{\partial \theta}dx
$$

</center>

우변은 다음과 같이 나타낼 수 있다.

<center>


$$
\begin{aligned}
0 &= \int_{-\infty}^{\infty} \frac{\partial f(x;\theta) / \partial \theta}{f(x;\theta)} f(x;\theta)dx \\[10pt]
&= \int_{-\infty}^{\infty} \frac{\partial log f(x;\theta)}{\partial \theta} f(x;\theta)dx \;\;\;\;\; \dots \;(2)
\end{aligned}
$$
</center>

로그 합성함수의 미분 공식을 사용하면 위 등식이 성립하는 것을 쉽게 확인할 수 있다. (수리통계학에서 흔히 사용되는 테크닉 중 하나라고 한다!)

식 $(2)$를 기댓값으로 다시 표현하면 다음과 같다.

<center>

$$
E[\frac{\partial log f(x;\theta)}{\partial \theta}] = 0 
$$

</center>

결론적으로 Score Function의 평균은 0임을 확인할 수 있다.

---

&nbsp;

자 그럼 다시 본론으로 돌아가 Fisher Information에 대해 이야기해보자. 

앞서 살펴본 Score Function의 관점에서 식 $(1)$을 해석하면 **Fisher Information은 Score Function의 분산**임을 확인할 수 있다. 또한 Score Function의 평균은 0이기 때문에 분산의 공식을 사용하여 이를 표현하면 다음과 같다.

&nbsp;

<center>


$$
\begin{aligned}
I(\theta) &= Var(S(\theta)) \\[10pt]
&= Var(\frac{\partial logf(x;\theta)}{\partial \theta}) \\[10pt]
&= E[(\frac{\partial logf(x;\theta)}{\partial \theta})^2] - (E[\frac{\partial logf(x;\theta)}{\partial \theta}])^2 \\[10pt]
&= E[(\frac{\partial logf(x;\theta)}{\partial \theta})^2] \\[10pt]
&= -E[\frac{\partial^2 logf(x;\theta)}{\partial \theta^2}] \;\;\;\;\; \dots \; (3)
\end{aligned}
$$
</center>

&nbsp;

마지막 $(3)$은 등식 $(2)$를 양변에 대해 한번더 $\theta$로 미분하면 쉽게 유도할 수 있다. (곱의 미분법 사용)



결론적으로, Fisher Information은 **Score Function의 변동성**을 나타내는 지표로 해석할 수 있다. 즉, 모든 가능한 관측치 $X$ 에서의 민감도를 가중치로 적용한 **"평균 민감도"** 정도로 해석할 수 있을 것 같은데, 이를 좀 더 쉽게 표현하자면 특정한 추정치가 **얼마나 확신을 갖고 모수를 추정하는지 여부**를 나타내는 지표로 해석하면 될 것 같다.

예를 들어, 만약 추정치 $\hat \theta$ 에 대응되는 Fisher Information의 값이 크다면 해당 likelihood 함수에서 관측치 $X$ 와 모수 $\theta$ 의 함수적 관계가 $\theta$ 의 작은 변화에 민감 (sensitive) 하다는 의미로, 해당 추정치가 모수를 추정함에 있어 상당한 자신감 (high confidence, precision) 을 갖고 있다는 의미이다. 

따라서 자연스럽게 Fisher Information은 특정한 추정치가 담고 있는 정보량으로 해석해도 무방할 것이며, 이 값이 크다는 얘기는 그만큼 정보량이 많다는 것을 의미한다. 사실 Fisher Information은 추정치의 효율성 (efficiency) 을 판단하는 **Rao Cramer Lower Bound** 라는 정리에 중요하게 사용되는데, 해당 내용은 추후에 다뤄보도록 하겠다.





&nbsp;































