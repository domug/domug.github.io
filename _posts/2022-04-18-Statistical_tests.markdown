---
layout: post
title: "Statistical Tests for Clinical Trials"
date: 2022-04-18 00:00:00
image: thumbnail_cl_tests.jpg
tags: [statistics]
categories: [statistics]
use_math: True
---

&nbsp;

This post is an overall summary of different testing methodologies used in clinical trials.

[1. One-Sample T-test](#1-one-sample-t-test)

[2. Two-Sample T-test](#2-two-sample-t-test)

[3. One-way ANOVA](#3-one-way-anova)

[4. Two-way ANOVA](#4-two-way-anova)

[5. Linear Regression](#5-linear-regression)

[6. Analysis of Covariance](#6-analysis-of-covariance)

&nbsp;

---

# 1. **One-Sample T-test**

One-Sample T-test is used to infer whether an **unknown population mean** differs from a **hypothesized value**.

**\<Assumptions>**

- i.i.d. samples from normal distribution with unknown mean μ.



**\<Test Setting>**

<center>
  <img src="{{site.baseurl}}/images/tests/ttest1.png" width="500" height="300" /> 
</center>

&nbsp;

**\<Takeaways>**

- A special case of the *one-sample t-test* is to determine whether a mean response changes under different experimental conditions by using "paired observations" ($\mu_d$=the mean difference, $\mu_0$= 0 , $y_i$'s = the paired-differences).
- It can be shown that the *t-test* is equivalent to the ***Z-test*** for **infinite degrees of freedom**. In other words, Z-test is equivalent to t-test in the limit of sample size. In practice, a ‘large’ sample is usually considered to be $n$ ≥ 30.
- If the assumption of **normality** cannot be guarenteed, the "mean" might not be the best measure of central tendency. In such cases, a non- parametric test such as the *Wilcoxon signed-rank test* might be more appropriate choice.
- A non-significant result does not necessarily imply that the null hypothesis is true. It only asserts **insufficient evidence** for contradicting the original statement.
- Statistical significance **does not imply causality** in observational studies, only for **"randomized"** controlled trials (RCT).

&nbsp;

----

# 2. **Two-sample T-test**

Two-sample T-test is used to compare the means of two independent populations, denoted as $\mu_1$ and  $\mu_2$.

It has ubiquitous application in the analysis of controlled clinical trials.



**\<Assumptions>**

- The populations are normally distributed.
- Homogeneity of variance - the populations share the same variance $\sigma^2$.



**\<Test Setting>**

<center>
  <img src="{{site.baseurl}}/images/tests/ttest2.png" width="500" height="300" /> 
</center>

<center>
  <img src="{{site.baseurl}}/images/tests/ttest2-1.png" width="250" height="150" /> 
</center>

&nbsp;

**\<Takeaways>**

- The assumption of equal variances can be tested using the ***F-test***:

<center>
  <img src="{{site.baseurl}}/images/tests/ttest2-2.png" width="500" height="300" /> 
</center>

- If the assumption of equal variances is rejected, a slight modification of the *t-test* called as the [*Welch's t-test*](https://en.wikipedia.org/wiki/Welch%27s_t-test):

<center>
  <img src="{{site.baseurl}}/images/tests/ttest2-3.png" width="500" height="300" /> 
</center>

<center>
  <img src="{{site.baseurl}}/images/tests/ttest2-4.png" width="200" height="100" /> 
</center>

- The assumption of normality can formally be checked by the *[Shapiro-Wilk test](https://en.wikipedia.org/wiki/Shapiro%E2%80%93Wilk_test)* or the *[Kolmogorov-Smirnov test](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test)*. 
- For large enough samples, **CLT (central limit theorem)** holds. However, if the data is heavily skewed such that normal approximation guarenteed by CLT is not sufficiently accurate, we have to consider changing test statistic from mean to median and use *Wilcoxon rank-sum test* instead.

&nbsp;

---

# 3. **One-way ANOVA**

One-way ANOVA is used to simultaneously compare two or more group means based on independent samples from each group. 



**\<Assumptions>**

- normally distributed data
- samples are independent w.r.t. each group
- **variance homogeneity**, meaning that the within-group variance is constant across groups. This can be expressed as $\sigma_1 = \sigma_2 = \dots = \sigma_k = \sigma$



**\<Test Setting>**

<center>
  <img src="{{site.baseurl}}/images/tests/anova1.png" width="500" height="300" /> 
</center>

**\<Takeaways>**

- Intuitively, if the "*F* statistic" that is roughly the ratio of ***between-group variability*** and ***within-group variability*** is far from 1, it is evident that the group means indeed differ. 
- On the other hand, *F* statistic will be close to 1, if the variation **among groups** and the variation **within groups** are independent estimates of the same measurement variation, $\sigma^2$.

- In this sense, *MSG* is an estimate of the variability "among groups", and *MSE* is an estimate of the variability "within groups".

- The parameters associated with the *F*-distribution are the upper and lower degrees of freedom.
- An "ANOVA table" is the conventional method of summarizing the results (shown above).

- Similar to t-test, *Shapiro-Wilk test* or *Kolmogorov-Smirnoff test* can be used to check the normality assumption.

- For variance homogeneity assumption, *[Levene’s test](https://en.wikipedia.org/wiki/Levene%27s_test)* or *[Bartlett’s test](https://en.wikipedia.org/wiki/Bartlett%27s_test)* can be used.

- When **comparing** **more than two means** ($k$ > 2), a significance of the *F-test* indicates that at least one pair of means are different, but it doesn't tell us which specific pairs are. Therefore, if the null hypothesis is rejected, further analysis should be taken to investigate where the differences lie.

- 95% confidence intervals can also be obtained for the mean difference between any pairs of groups (e.g, Group *i* vs Group *j*) by the formula:

<center>
  <img src="{{site.baseurl}}/images/tests/anova2.png" width="300" height="150" /> 
</center>

- If there are **only two groups ($k = 2$)**, the p-value for Group effect using an ***ANOVA*** is the same as that of a standard ***two-sample t-test***. This is because the F and t-distributions enjoy the relationship that, with 1 upper degree of freedom, the **F-statistic is the square of the t-statistic**. When $k = 2$, the MSE in *ANOVA* is identical to the pooled variance in the *two-sample t-test*.

&nbsp;

---

# 4. **Two-way ANOVA**

Two-way ANOVA is a method for "simultaneously" analyzing two factors that affect a response. 

It is also called a **"randomized block design"**.



**\<Assumptions>**

- Similar to the one-way ANOVA - **normality**, **variance homogeneity**.



**\<Test Setting>**

<center>
  <img src="{{site.baseurl}}/images/tests/anova3.png" width="500" height="300" /> 
</center>

**\<Takeaways>**

- Another identifiable source of variation called a **"blocking factor"** is included in the model, whose variation can be separated from the error variation to give more precise group comparisons.

- The F-test for "group" (FG) tests the primary hypothesis of *no group effect*.
- F-test for the block effect (FB) provides a secondary test.
- Significant block effect often results in a smaller error variance (MSE) and **greater precision for testing the primary hypothesis** of *no group effect*, compared to the case where the block effects were ignored (but not always!).

- "Group-by-Block factor" (G x B) represents the statistical **interaction** between the two main effects. This indicates that trends across groups differ among the levels of the blocking factor.
- **Interaction effect is usually the first test of interest in a two-way ANOVA model** because the test for group effects might not be meaningful in the presence of a significant interaction.

- For the unbalanced groups (possibly due to missing data), sum of squares calculation is not trivial as it cannot be broken down into additive components due to the sources of variation. One way to address this issue is by using **"**weighted squares of means**"**.
- A statistical model associated with a **higher-order ANOVA** is called a ***fixed-effects* model** if all effects are fixed, a ***random-effects* model** if all effects are random, and a ***mixed effect* model** for a mixture of fixed and random effects.

&nbsp;

---

# 5. **Linear Regression**

Linear Regression is used to analyze the relationship between a response variable $y$, and predictors $x$.



**\<Assumptions>**

- Normality of the response
- Linearity between predictor and response
- **Homoscedasticity**: constant residual variance over the experimental region
- No **autocorrelation** (correlation over time)
- No **multicollinearity**: no redundant information in the predictors



**\<Test Setting>**

<center>
  <img src="{{site.baseurl}}/images/tests/lreg.png" width="500" height="300" /> 
</center>

cf) for the case of multivariate predictors $\mathbf{X}$ (i.e. **multiple linear regression**), above expressions can naturally be extended to vector dimension. Namely, the best estimator of coefficients and intercept can be attained by solving the **normal equation**:



<center>

$$
\mathbf{\hat \beta} = (X^TX)^{-1}X^Ty
$$

</center>

&nbsp;

**\<Takeaways>**

- As its name implies, "linear" regression models can only capture **linear relationship** between response and predictors. For other types of relationship (e.g. quadrature), we have to expand the model to include polynomial terms as well.

- The hypothesis of "zero regression slope" is equivalent to the hypothesis that the "covariate is not an important predictor of response".

- For the multivariate case (i.e. multiple linear regression), **coefficient of determination (R^2)** represents the percentage reduction in error variability by using the explanatory variables as predictors of $y$ over just using the mean $\bar y$. This metric can be used for model (feature) selection.
- The fitted regression line can be used to "predict" the value of response given a new predictor. 
- However, predicting a response **outside the experimental region** should be done with extra care as there is no guarentee that the same linear relationship will be held valid (a.k.a. **extrapolation**).
- To roughly diagnose the homoscedasticity assumption, we can use **residual plot** which is standardized residuals plotted against the predicted values of the response. Ideally, there should be no pattern like the one following:

<center>
  <img src="{{site.baseurl}}/images/tests/lreg2.png" width="300" height="150" /> 
</center>

- For diagnosing the normality assumption, we can draw a normal qq plot :

<center>
  <img src="{{site.baseurl}}/images/tests/lreg3.png" width="200" height="150" /> 
</center>

- For diagnosing if autocorrelation exists, there is a test called *[Durbin-Watson test](https://en.wikipedia.org/wiki/Durbin%E2%80%93Watson_statistic)*.
- Diagnostics available for detecting collinearity between independent variables include the *variance inflation factor (VIF)* and *eigenvalue analysis* of the model factors. For *VIF* approach, a widely used threshold is 10 (smaller the better).

&nbsp;

---

# 6. **Analysis of Covariance (ANCOVA)**

ANCOVA is used to compare response means among two or more groups "adjusted" for a quantitative concomitant variable (a.k.a. **covariate**), which is considered to have influenced the response.



**\<Assumptions>**

- groups are independent
- normally distributed response measure
- variance homogeneity



**\<Test Setting>**

<center>
  <img src="{{site.baseurl}}/images/tests/ancova.png" width="500" height="300" /> 
</center>

The mean response within each group depends on the covariate, which results in a model for the ith group mean as $\mu_i = \alpha_i + \beta x$. Then, the estimated regression line for $i$-th group is:

<center>
  <img src="{{site.baseurl}}/images/tests/ancova2.png" width="150" height="100" /> 
</center>

Similar to that of ANOVA, we can decompose the source of variation by the following table:

<center>
  <img src="{{site.baseurl}}/images/tests/ancova3.png" width="500" height="300" /> 
</center>

In this setting, our primary interest is the comparison of the group means adjusted to a common value of the covariate such that (little subscript 0 indicates covariates used):

<center>
  <img src="{{site.baseurl}}/images/tests/ancova4.png" width="500" height="300" /> 
</center>

This hypothesis is equivalent to the "equality of intercepts among groups", as each of the group means are only different in $\alpha$'s. Thus, the test does not depend on the value of covariate $x_0$.

<center>

$$
H_0: \alpha_1 = \alpha_2 = \dots = \alpha_k
$$

</center>

To determine whether the covariates have a significant effect on the response, use the $F_X$ ratio to test for zero-regression slope using the following test statistic:

<center>
  <img src="{{site.baseurl}}/images/tests/ancova5.png" width="500" height="300" /> 
</center>

**\<Remarks>**

- Basically, ANCOVA is combining **regression** and ***ANOVA*** methods by **fitting simple linear regression models within each group** and **comparing regressions among groups**. Therefore, the nitty-gritty details such as model intepretation and diagnosis follow that of the aformentioned two statistical models.

- ANCOVA can often **increase the precision of comparisons of the group means** and thereby decrease the estimated error variance, as the covariates can be a major source of variation among groups.
- Roughly speaking, the estimated effect among groups are shrunken towards the global mean of the model. This is often called as the "[regression towards the mean](https://en.wikipedia.org/wiki/Regression_toward_the_mean)".



