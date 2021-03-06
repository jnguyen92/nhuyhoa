---
layout: post
title: "OLS: Orthogonal Columns"
date: "October 20, 2015"
categories: Statistics
tags: Regression
---

* TOC
{:toc}



# Beta Estimates

Orthogonal design matrices in regression ensures that the $$\beta$$ estimates do not depend on each other. In other words, because there is no collinearity between columns, the effect of one factor or interaction can be estimated separately from the effect of any other factor/interaction in the model.

Let the design matrix be <br>
$$\mathbf{X} = \left[\begin{array}
{rrr}
x_0 & x_1 & x_2 & ... & x_p
\end{array}\right]
$$

where $$x_i$$ are columns vectors and the columns are mutually independent.

Then <br>
$$ \hat{\beta} = (X^TX)^{-1}X^TY $$

Since the columns of $$X$$ are mutually orthogonal, then <br>
$$\mathbf{(X^TX)^{-1}} = \left[\begin{array}
{rrr}
\frac{1}{x^{T}_{0}x_0} & 0 & 0 & 0 \\
0 & \frac{1}{x^{T}_{1}x_1} & 0 & 0 \\
0 & 0 & ... & 0 \\
0 & 0 & 0 & \frac{1}{x^{T}_{p}x_p} 
\end{array}\right]
$$

Plug this in to compute $$ \hat{\beta} $$ <br>
$$ \hat{\beta} = \left[\begin{array}
{cc}
\frac{1}{x^{T}_{0}x_0} & 0 & 0 & 0 \\
0 & \frac{1}{x^{T}_{1}x_1} & 0 & 0 \\
0 & 0 & ... & 0 \\
0 & 0 & 0 & \frac{1}{x^{T}_{p}x_p} 
\end{array}\right]
\left[\begin{array}
{cc}
x^T_0Y \\
x^T_1Y \\
... \\
x^T_pY \\
\end{array}\right]
$$

Resulting in the final estimate <br>
$$ \hat{\beta} = \left[\begin{array}
{rrr}
\frac{x^T_0Y}{x^{T}_{0}x_0}  \\
\frac{x^T_1Y}{x^{T}_{1}x_1}  \\
... \\
\frac{x^T_pY}{x^{T}_{p}x_p} 
\end{array}\right]
$$


Note that the $$\beta_j$$ estimate depends only on the $$j^{th}$$ column in the design matrix. Thus the effect of one variable is estimated independently of other variables.

# Covariance Matrix
Recall that $$ Var[\beta] = \sigma^2 (X^TX)^{-1} $$.

When the columns are orthogonal, <br>
$$\mathbf{(X^TX)^{-1}} = \left[\begin{array}
{rrr}
\frac{1}{x^{T}_{0}x_0} & 0 & 0 & 0 \\
0 & \frac{1}{x^{T}_{1}x_1} & 0 & 0 \\
0 & 0 & ... & 0 \\
0 & 0 & 0 & \frac{1}{x^{T}_{p}x_p} 
\end{array}\right]
$$

Thus $$\forall$$ $$j$$, $$ Var[\hat{\beta}_j]= \frac{\sigma^2}{x^{T}_{j}x_j} $$.

As a matter of fact, the variance of the $$\hat{\beta}$$ are minimized when $$X$$ has mutually exclusive columns. 

In other words for any design matrix, <br>
$$Var[\hat{\beta}_j] \ge \frac{\sigma^2}{x_j^{*T}x_j^*} $$ <br>
and the minimum is attained when $$x_i^{*T}x^*_j = 0$$ $$\forall$$ $$i \ne j$$.

# Orthogonal Columns in Experimental Design & Clinical Trials
In experimental design, randomization ensures that the experimental groups are independent of baseline characteristics. This ensures that there are no confounding effects. Furthermore, baseline characteristics are adjusted for, the estimates of $$\hat{\beta}$$ do not change. On the other hand, precision of  estimation increases (smaller $$Var[\hat{\beta}]$$).


