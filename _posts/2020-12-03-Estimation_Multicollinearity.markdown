---
layout: post
title: "Statistical Estimation and Multicollinearity"
date: 2020-12-03 00:00:00
image: thumbnail1.png
tags: [statistics, estimation, multicollinearity]
categories: statistics
---

In the upcoming few posts, We will mainly be discussing about the statistical dimension reduction methods and their applications. But before digging right in to these stuffs, I personally think we have to understand why we need dimension reduction in the first place. So in this post, I'll briely talk about the concept of **statistical estimation** and **multicollinearity** problem arising with it.

---


# Statistical Estimation
Though it might sound very naive, when I'm asked to define the statistical inference procedure in a single sentence, I would say "It is all about **estimation** and **testing**". And especially for nowadays when the boundary between statistics and data science is getting blurry, I believe the very procedure of statistical estimation, which an endeavor to explain the matter of interests by our data, is gaining much of importance. So, although testing is a huge part of statistics, if we can focus on our attention to the estimation for now, how accurately we can explain and tame the unknowns based on our data is critical part of estimation procedure. 


In this sense, a statistician-like way of estimating the unknown is to first define the **parameter**, set distributional assumptions if possible, collect data and define object function to derive the optimal **"estimator"** analytically. Ideally, we would expect our derived estimator to effectively mimic the behavior of the parameter, so that we can use our estimator to predict the parameter which is by its nature unknown to us.


For example, let's say I would like to know the average height of the males in Yonsei university. To investigate this curiosity, I would first define the parameter as "Average height of males in Yonsei" which is a fixed constant (well there is a controversy on whether we should consider parameter as fixed, but that's a whole new topic on Bayesian statistics so I will not dig much into it here) and find an optimal "estimator" that is representative of the original parameter value which we can't know. In this particular problem, our best estimator is the "sample mean", which can be derived mathematically by the principle of maximum likelihood. Detailed procedure on deriving optimal estimator is a semester length topic in Mathematical Statistics so if you are interested, check the Hogg's mathematical statistics book or related courses. 


---

# Bias Variance Tradeoff
Anyways, let's consider we have found the optimal estimator of our parameter. Then as a next step, it is reasonable to question how effective is our estimator in terms of representing the parameter. Regarding this, the most prevalent way of evaluating estimator is by calculating the **prediction error**. There are several metrics, but one of the most popular is the **Mean Square Prediction Error (MSPE)**, which is just the expected value of the difference between predicted value by our estimator and the parameter. Here is the formula.

![]({{site.baseurl}}/images/Estimation_Multi/MSPE.jpg)


From the second line, we can see that MSPE can be decomposed into two parts such as Bias and Variance respectively. This decomposition is very important and called **Bias-Variance Tradeoff**. To put it simply, the thing so called "bias" is inversely proportional to "variance". Then what is bias and what is variance? Imagine a situation where you are at a shooting range. You see a target about 500 meters ahead and you took 8 shots with your rifle. The following is your performance.

<p align="center">
  <img width="460" height="300" src="{{site.baseurl}}/images/Estimation_Multi/bvtradeoff.svg">
</p>

<p align="center">
  <img width="700" height="500" src="{{site.baseurl}}/images/Estimation_Multi/bvtradeofftable.png">
</p>


Among the four results, which one do you think is the best performance? Ideally, we would like our shots to be within the target as well as the shots be precise so that they are not scattered all over the target. In this sense, we would all agree that the very first trial, which is the top left, is the best. However, in real life, only top class snipers can achieve that. Hence, our shots will most likely be among the latter three. Therefore among the latter three, I would say the ideal performance is the bottom left.

The above example is a nice illustration of the bias variance tradeoff. We want our estimator to be as closest as possible to the parameter while at the same time be consistent ("consistent" in here means that when we recalculate the predicted value by estimator, the estimated values does not vary much). But by the innate principle of our universe, we can't achieve both at once. It's like we can't kill two birds with one stone. So we have to find a decent middle place somewhere in between the ideal and reality like the bottom left result above, and that's where the concept of **multicollinearity** comes in.


---

# Multicollinearity

Now it's time to talk about multicollinearity. A nice example is worth a thousand equations so let's imagine the following situation. I am a big fan of BTS and especially the member "Jin". Suppose I have a dataset containing information about Jin such as his height, weight, birthday, preferences, etc which are all "variables" (or features equivalently). Based on my data, I want to make a regression model on Jin's personality by using all variables in the dataset. In other words, I'm using the information contained in the variables to explain Jin's personality. However, let's say that we have the following variables -  "width of eye", "length of eye", "color of eye". Don't these variables seem redundant? In other words, do we really need all these variables? These are all features of an "Eye", so we can intuitely think there's gotta be some information overlapped in them.

This is multicollinearity. **By formal definition, we say that multicollinearity exists when the (explanatory) variables are highly "correlated"** to each other. This is a very inefficient situation in terms of the information because practically, all we need is just a few subset of variables. As there is a phenomena called **"curse of dimensionality"**, we would have to find a remedy for this problem. In the upcoming posts, I will introduce two statistical methods to deal with this problem, the so called **"Variable Selection Method"** and **"Dimension Reduction Method"**






