---
layout: post
title: "Variable Selection Methods"
date: 2020-12-04 00:30:20 +0200
image: thumbnail2.png
tags: [statistics, multicollinearity, ridge, lasso]
categories: statistics
---

### Previous Posts

[1. Statistical Estimation and Multicollinearity](https://domug.github.io/2020/12/03/Estimation_Multicollinearity/)

---


# Variable Selection

From previous post, we talked about the concept of multicollinearity and the problems associated with it. Statistically, there are two remedial methods to reduce multicollinearity and in this post we will be discussing about the first one - **"Variable Selection"**. The objective of variable selection is to find subset of variables that are most "relevant" for prediction, or in the same manner, "important" in terms of the amount of information in them. 

I know it may sound all of a sudden, but let's think of our universe. Although I have little knowledge on physics, I know that our universe is sparse. Stretch out your hand in the air. Can you feel something? Well obviously there are invisible elements like oxygen and carbon but we can't feel them nor can we see them. So when you are asked by your freinds to describe your room, you wouldn't include the trivials such as the amount of oxygen in your room. Rather, what you will mainly be telling your friends are the nitty-gritty details that are representitive of your room, such as your recently bought PS5 or a fancy poster of Lionel Messi.

In this sense, when we are looking at a particular dataset, we always have to keep mind that **we don't necessarily need every information in the data**, because some part of information is just too trivial or redundant. In statistics, these unimportant information is called as **"noise"**. So by identifying relevant variables, we are able to reduce the noise contained in the data and it will improve the performance of our estimator (or fitted model) that is measured by MSPE.

Then how can we identify these "relevant" information? The foremost and most straightforward way is by the rule of thumb. You can just exclude one variable at a time, calculate and record the MSPE, compare the MSPE scores for each of your trials and select the best subset that showed the lowest MSPE. Actually, when you use the software R and conduct regression analysis, R will return the best subset of variables by this method. This is called as **"subset selection"**. The strength of subset selection is that it is very easy to implement and it produces a model that is highly **"interpretable"** because we just discarded the irrelevant variables. There's not much of fancy statistical technic used here.

Although this method is very straightforward and easy to implement, there are few serious drawbacks. Since it is a discrete process, meaning that variables are either selected or discarded, we lose all the information that are outside of the selected variables. Moreover, it often exhibits high variance meaning that it does not perform well when new data is fed.

In order to improve these apparent weaknesses, the so called **"shrinkage methods"** had been developed, which is said to be more continuous and don't suffer from high variability as much as the subset method. The most famous shrinkage methods include **Ridge Regression** and **Lasso Regression**, and we will briefly take a look at each of them and their implementation.

---

# Ridge Regression

If you are familiar with linear regressions, you will probably remember the **normal equation**, which is used to derive the regression coefficients(LSE) of linear regression models. Here is the formula.

![]({{site.baseurl}}/images/Variable_Selection/normal_equation.png)

Note that the regression coefficient beta can only be derived when the design matrix X is of [full rank][fullrank]. When multicollinearity exists between the columns of a matrix, then that matrix is not full rank, which implies that we can't derive the unique Least Squares Estimate of beta. 

Ridge regression handles this problem by imposing a **penalty** on the size of the matrix to arbitrarily make normal equation solvable. Here's the idea.


![]({{site.baseurl}}/images/Variable_Selection/ridge.png)

By adding λ to the left side of the normal equation, we can make the matrix as full rank (or equivalently invertible). Here, λ is called as **complexity parameter** and it controls the amount of **"shrinkage"**, meaning that the regression coefficients of irrelevant variables converges to 0. Important point here is that in ridge regression, the coefficients just infinitely converges to 0 and doesn't become exactly 0. Thus the larger the value of λ, the greater the amount of shrinkage. We can also see that the ridge regression coefficient can be derived precisely in a closed form because it is a linear combination of matrix X and Y.

Another importance point is that, the normal equation for ridge regression can be expressed explicitly in terms of the constraint as the last part of the equation above. As can be seen, the constraint for ridge regression is the **square** of the norm of β, so this panelty is often called as **L2 panelty**. Also, as ridge regression sort of manages, or "regularizes" β to control variance, another name for ridge regression is **L2 Regularization**.



<p align="center">
	<img src="{{site.baseurl}}/images/Variable_Selection/ridge_ols.png">
	<img width="600" height="500" src="{{site.baseurl}}/images/Variable_Selection/ridge2.png">
</p>

The graph above is a comparison between OLS (Ordinary Least Squares) coefficients and the ridge coefficients based on optimal λ suggested by R. Each regression was fitted on a randomly created data from the multivariate normal distribution. The y-axis represents ordinary regression coefficients and the x-axis is ridge coefficients. You can clearly see that most of the ridge coefficients have converged near to 0, while in the ordinary linear regression the coefficients are all over the place.



---

# Lasso Regression

Now let's take a took at another famous shrinkage method as known as lasso regression. The general idea is similar to the ridge regression, while the only crucial difference being that lasso uses **L1 panelty**. In fact, lasso is an abbreviation for **"Least Absolute Shrinkage and Selection Operator"**. As it is explicitly mentioned in its name, lasso uses absolute panelty and although this might seem trivial, it creates a lot of difference and complexity compared to the ridge method.

<p align="center">
  <img src="{{site.baseurl}}/images/Variable_Selection/ridge_lasso.png">
</p>

Because of the absolute panelty, we cannot derive the estimated coefficients of β in a closed form since the normal equation is non-linear in X and Y. Therefore we need to use numerical optimization methods such as [Newton Rhapson Method][nr-docs]. Conveniently, R or Python automatically does all of the calculation for us so let's focus more on its implication.


<p align="center">
  <img src="{{site.baseurl}}/images/Variable_Selection/lasso_ols.png">
</p>

By fitting lasso regression to the same data used in ridge regression, we can clearly see that some of the coefficients have disappeared completely. This is because unlike ridge regression where we used square panelty, the absolute panelty of lasso regression makes the regression coefficients of "insignificant" variables exactly 0. If we visualize this result we get the following graph.

<p align="center">
  <img width="600" height="500" src="{{site.baseurl}}/images/Variable_Selection/lasso.png">
</p>

The x-axis corresponds to the log scaled values of λ, which is the shrinkage parameter that we discussed earlier and y-axis is the corresponding lasso coefficients. The vertical red line is the best λ suggested by R. We can visually examine that for the best λ, most of the coefficients became 0. Hence, we can conclude that those variables with 0 coefficients are insignificant in terms of our linear regression model. 

We have just conducted a variable selection! Keep in mind that the purpose of variable selection is to define only a subset of important variables which will be used in further analysis. Thus variable selection itself is not the ultimate goal, but rather a first step in a complete statistical analysis procedure.

Although lasso and ridge methods can be used to reduce multicollinearity, variable selection methods have inherent limitations as they merely tell us which variables to be used or not. In other words, these methods are subordinated to the original data - they don't provide new insight or new viewpoint of the data. It'll probably not be clear for now, but it will soon make sense after we talk about **dimension reduction methods** in the following posts, so let's call it a day for now.



[nr-docs]: https://en.wikipedia.org/wiki/Newton%27s_method
[fullrank]: https://en.wikipedia.org/wiki/Rank_(linear_algebra)#:~:text=A%20matrix%20is%20said%20to,does%20not%20have%20full%20rank.
