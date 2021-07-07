---
layout: post
title: "Statistical Inference"
date: 2020-12-03 00:00:00
image: 6.jpg
tags: [statistics, estimation, testing]
categories: statistics
use_math: True
---



&nbsp;



This post will briefly investigate the key ideas behind modern statistics in terms of the concept of statistical inference.



&nbsp;



---

# What is Statistics?



Before the advance of Pearson's statistical revolution in the early 20th century, our universe was considered to be **deterministic**.  Scholars tried to find out the exact distance between an asteroid and Earth with precise math and scientific theories and thought every phenomenon is a result of an innate principle of universe.

However, starting from the famous British statistician named [Karl Pearson](https://en.wikipedia.org/wiki/Karl_Pearson), **probabilistic view of universe** became to dominate science. This was made possible with a simple shift in thought such that "**the observed data is one of the many possible outcomes from a specific value of a parameter**".

Let's assume that we are interested in finding out the average height of male students in Yonsei University. The parameter to be inferred is the "average height" and the most naive but straightforward approach would be to conduct an inquiry to all male students of Yonsei University and calculate the average based on the collected data. However, this approach is infeasible when our target of interest is sufficiently large (think about making a poll to millions of voters during the presidential election).

To deal with this problem, statisticians have came up with an idea to approximate the parameter based on **samples**. From now, we are assuming that the height of the students are the randomly generated values from the parameter and we are using these samples to make inference on the parameter in the other way around.

Thus, a **statistical inference** is composed of a series of logical procedure which can be abstracted by the following illustration. 

&nbsp;

<p align="center">
  <img width="700" height="500" src="{{site.baseurl}}/images/Estimation_Multi/statistics.png">
</p>

&nbsp;

1. Define a **parameter** from the **population space**.
   - *parameter* is our target of interest which we cannot know exactly.
2. Collect **samples** from the population.
   - samples must be representative of the population.
3. From the samples, make inference about the parameter.

&nbsp;

So in a nutshell, **statistical inference** is all about making a decision about the parameter from the collected samples. In this sense, statistical inference can mainly be decomposed into 2 sub-categories based on the objective and aim of the study - which are **Estimation** and **Testing**. Let's briefly take a look at each.



&nbsp;

---


# Statistical Estimation


Statistical estimation is an endeavor to find an **estimator** to approximate the parameter. From the collected samples, an **estimator** (or **object function**) is defined, which is a **function of random variables**, to efficiently mimic the behaviour of the original parameter. 

For example, consider a situation in which we want to make inference on the population mean. Since it is impossible to consider every single entities in a parameter space, we mostly use sample mean as a representative of the population mean. 

&nbsp;

<center>

$$
\mu \approx \bar X = \frac{\sum_{i=1}^{N} X_i }{N}
$$



</center>

&nbsp;

Note that the sample mean is an estimator because it is a function of random variables $X_1, X_2, ... X_N$.

However, defining an estimator can be largely subjective. Whereas from the above example a sample mean is used as an estimator of population mean, someone might possibly claim to use sample median instead. To prevent this possible disharmony, a famous British statistician [Ronald Fisher](https://en.wikipedia.org/wiki/Ronald_Fisher) has suggested some criteria for evaluating a defined estimator. Among them, some of the most basic criteria are **consistency**, **unbiasedness** and **efficiency**, which are introduced in Hogg's Mathematical Statistics. This is a semester-length topic to be covered, so for now let's ignore the nitty-gritty details and just remember that the **variance** of an estimator plays a key role in evaluating its performance. 

Let's assume that we have defined an estimator of our parameter. As a subsequent step, we have to question how effectively our defined estimator can represent the true parameter. Regarding this, a **loss function** is defined to get a specific numerical value of our estimator's uncertainty. The most commonly used loss function for continuous estimator is the **Mean Square Prediction Error (MSPE)**, which is just the expected value of the difference between the prediction of our estimator and the true parameter. Here is the formula.

&nbsp;

<center>

$$
\begin{aligned}

MSPE(x) &= E[ (\hat f(x) - y)^2 ] \\
&= Bias(\hat f(x))^2 + Var(\hat f(x)) + \sigma^2, \;\;\;\;where \; Bias(\hat f(x)) = E[\hat f(x) - E[y]]

\end{aligned}
$$

</center>

&nbsp;



From the second line of the equation, we can see that MSPE can be decomposed into **Bias** and **Variance** respectively. This decomposition is very important and is called as **Bias-Variance Tradeoff**. Namely, bias is inversely proportional to the variance. Then what are bias and variance? Imagine a situation in a shooting range. A sniper sees a target at about 500 meters ahead and took 8 shots with his rifle. The following is the resulting performance.

&nbsp;



<p align="center">
  <img width="460" height="300" src="{{site.baseurl}}/images/Estimation_Multi/bvtradeoff.svg">
</p>


<p align="center">
  <img width="700" height="500" src="{{site.baseurl}}/images/Estimation_Multi/bvtradeofftable.png">
</p>

&nbsp;



Among the 4 outcomes, which one would be the best performance? Ideally, a sniper would like to have his shots to be within the target as well as the shots be precise so that they are not scattered all over the place. In this sense, it looks like the very first outcome of the top left is the best. Well this is indeed ideal, but life isn't always that easy. Most likely the shots will be among the latter three depending on various factors that distract the sniper's performance. Therefore in a realistical sense, the bottom left outcome is still fair enough. 

This example is a nice illustration of the bias variance tradeoff. We want our estimator to be as closest to the parameter as possible while at the same time be consistent ("consistent" in here means that when we recalculate the predicted value by estimator, the estimated values does not vary much). But by the innate principle of the universe, we can't achieve both at once. Thus, we have to compromise between the ideal and reality like the bottom left outcome and it's totally upto the researcher to decide the amount of tradeoff depending on the goal of the research. 

In practice, a **Maximum Likelihood Estimator (MLE)**, an estimator that maximizes the **likelihood function** (likelihood function is equationally the same as the pdf of a distribution but with an alternating perspective from the data points), is most widely used as an estimator. The reason is because it has lots of desirable properties based on its asymptoticity (when sample size diverges to infinity). To be more specific, it is known that under some regularity conditions, an MLE is always asymptotically unbiased and efficient as its limiting distribution is the Gaussian distribution.

Anyways, the key concept of statistical estimation is making inference about the parameter based on the estimator. Note that **we only use the term "estimation" when our target of interest implies randomness** (Again, an estimator is a function of random variables!).

&nbsp;

---

# Testing

Another key element of statistical inference is (hypothesis) testing. A statistical test in short is a procedure to **validate the status quo by comparing it to the alternative.** This **status quo** is formally called as the **null hypothesis** and the alternative is called as the **alternative hypothesis**, which are denoted by $H_0$ and $H_1$ respectively.



Here's a brief summary of testing procedure.

1. Define and calculate the **test statistic** based on the hypothesis to be tested.
2. Divide the parameter space into two disjoint and complementary subspace. One of this subspace is called as the **critical region**.
3. Reject the null hypothesis only if a test statistic is included in the critical region.



In step (1), a test statistic is defined considering the characteristic of the hypothesis to be tested. For example, if a researcher is interested in studying whether there is difference in the average value of two population, two sample t-test is used and the test statistic is derived accordingly. 

In step (2), defining a critical region is the core of a hypothesis testing procedure (In fact, a test is defined by its critical region!). To do this, the maximum percentage of error to be tolerated based on the assumption that our null hypothesis is correct is set. This maximum value is formally called as the **significance level (or Type I error)** and 0.01 or 0.05 is most commonly used in practice. 

In step (3), the null hypothesis is rejected according to the calculated test statistic. In practice, this decision is driven by the **p-value**. A **p-value** by definition, is **a probability of obtaining a result equal to or more extreme than what was being observed by the given data under the assumption that the null hypothesis is valid.** For example, if the calculated p-value was 0.01 for a specific test, it is equivalent to saying that the observed data is likely to happen with probability of only 1% under the null hypothesis, which is a very unusal case. Thus, if this is the case, it is natural to infer that the null hypothesis was wrong in the first place so it works as an evidence for rejecting the null hypothesis. 

&nbsp;

> p-value should **never** be interpreted as the probability of the parameter itself because the **parameter is a constant**!! (This is a bit different in a field of **Bayesian Statistics**, but we don't use the term p-value in Bayesian Statistics so let's not be bothered for now.)

&nbsp;

However, **rejecting the null hypothesis doesn't necessarily mean that the alternative hypothesis is correct**. It is easy to think of the statistical testing procedure as a prosecutor accusing the defendant in a court. In a statistical test, a researcher tries to find evidence (data) to prove that the null hypothesis is wrong. But even if there is lack of evidence, it does not guarentee that the null hypothesis is correct. We can only say in terms of uncertainty, that there is "lack of evidence" to conclude that the null hypothesis is wrong. Thus, a sufficiently large p-value must be interpreted as "not enough evidence that goes against the null hypothesis", but should never be interpreted as "there is clear evidence to prove that the null hypothesis is correct".

Therefore, a small p-value is not sufficient to withdraw any meaningful conclusion because the value itself can sometimes be misleading. For example, think about a situation where we are testing the hypothesis such that "the percentage of two groups are different". Assume that we have calculated the percentage of the two groups, which were 40% and 40.2%. Regarding this, we would expect a large p-value, but this difference of 0.2%p can be statistically significant if the sample size was sufficiently large especially for us living in a world of big data.

Therefore, a researcher should always provide **confidence interval** along with the p-value for his research to be valid. **A typical 95% confidence interval in short is an interval that would encompass the true population parameter if the procedure is repeated for multiple times.** This definition is somewhat unnatural because we cannot make any probabilistic statement about the parameter as it is a constant value. Thus, the probability of the true parameter to be included in a confidence interval is always binary (0 or 1) and 95% confidence interval just guarentees that if the interval is calculated multiple times, then about 95% of these intervals would indeed contain the true parameter.

This is the reason why I personally don't prefer statistical hypothesis testing procedure because I think it is somewhat arbitrary. For disciplines in which samples are limited and conduting an experiment is costly such as the field of medicine, statistical tests are powerful tools. However, we should not have blind faith on the low p-value and keep it mind that statistical tests are merely tools and exist as a guideline for making a decision.





&nbsp;





