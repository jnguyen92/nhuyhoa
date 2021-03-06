---
layout: post
title: "WLS: Weighted Least Squares"
date: "November 23, 2015"
categories: Statistics
tags: Regression
---

* TOC
{:toc}



# Why It's Needed
Linear regression assumes constant variance of the errors (homoskedasticity). In other words,

$$ Y  \sim N(X\beta, \sigma^2 I)$$

However, when this assumption of constance variance is violated (heteroskedasticity), unequal variances may be sufficiently adjusted.

Assumed $$\epsilon$$ (homoskedaskticity: <br>
$$\left[\begin{array}
{rrr}
\sigma^2 & 0 & ... & 0 \\
0 & \sigma^2 & ... & 0 \\
... & ... & ... & ... \\
0 & 0 & ... & \sigma^2
\end{array}\right]
$$

Actual $$\epsilon$$ (heterskedaskticity): <br>
$$\left[\begin{array}
{rrr}
\sigma^2_1 & 0 & ... & 0 \\
0 & \sigma^2_2 & ... & 0 \\
... & ... & ... & ... \\
0 & 0 & ... & \sigma^2_p
\end{array}\right]
$$

# Weighted Least Squares
So to alleviate this problem define a matrix 

$$\mathbf{W} = \left[\begin{array}
{rrr}
\sigma^2_1 & 0 & ... & 0 \\
0 & \sigma^2_2 & ... & 0 \\
... & ... & ... & ... \\
0 & 0 & ... & \sigma^2_p
\end{array}\right] = \left[\begin{array}
{rrr}
\sigma^2 / w_1 & 0 & ... & 0 \\
0 & \sigma^2 / w_2 & ... & 0 \\
... & ... & ... & ... \\
0 & 0 & ... & \sigma^2 / w_p
\end{array}\right]
$$
$$

The WLS estimate is then

$$ \hat{\beta}_{WLS} = (X'W^{-1} X)^{-1} X' W^{-1} Y $$

# Estimating Weights
There are several options for estimating the weights.

Scenarios in which weights are known:

* Standard deviations of individual data points are provided, use them
* If $$i^{th}$$ response is an average of $$n_i$$ equally variable obs, then $$Var(y_i) = \sigma^2 / n_i$$ and $$w_i = n_i$$
* If $$i^{th}$$ response is a sum of $$n_i$$ obs, then $$Var(y_i) = n_i \sigma^2$$ and $$w_i = 1/n_i$$
* If variance is proportional to some predictor $$x_i$$, then $$Var(y_i) = x_i \sigma^2$$ and $$ w = 1/x_i$$

Procedure if weights are unknown, first fit an OLS and use the residuals to derive the weights.

* If resids vs fitted plot has a funneling shape, regress absolute values of residuals vs fitted values
* If resids vs a predictor has a funneling shape, regress absolute values of residuals vs predictor
* If resids squared vs fitted plot has an upward trend, regress squared resids vs fitted values
* If resids squared vs predictor has an upward trend, regress squared resids vs predictor

In all these scenarios, use the resulting fitted values as estimates of $$\sigma^2_i$$. 

Using estimated variances (derived from residuals) can be highly susceptible to influential points. Thus it is better to use these measures that are correlated with variance but are more robust. 

* In designed experiments with large numbers of replicates, weights can be estimated directly from sample variances at each combination of predictor vars
* Use intuition based off what domain knowledge 

For the most part, the estimated coefficients should be similar to the unweighted estimates (OLS is unbiased). The corresponding standard errors of the estimates will be smaller (if weights chosen correctly). 

In some cases, the estimates may differ substantially. If this occurs, WLS can be iterated until the estimated coefficients stabilize (iteratively reweighted least squares). 
