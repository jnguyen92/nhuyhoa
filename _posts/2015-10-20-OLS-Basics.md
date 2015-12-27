---
layout: post
title: "OLS: Basics"
date: "October 20, 2015"
categories: ['statistics', 'regression analysis']
---

* TOC
{:toc}

The linear probability model:

$$ Y$$ ~ iid$$N(X\beta, \sigma^2 I)$$

The regression equation:

$$ Y = X\beta + \epsilon $$

# Estimating Beta Coefficients
In least squares regression, we attemt to minimize the sum squared errors (SSE). 

Let $$Y$$ = observed responses and $$\hat{Y}$$ = fitted responses. $$\hat{Y}$$ lies on the column space of $$X$$, our design matrix. The idea is that there may not be a solution to $$Y = X\beta$$, so we project $$Y$$ onto the $$col(X)$$ in which we do have a solution. 

In order to minimize the SSEs, we have $$Y-\hat{Y}$$ is perpendicular to $$col(X)$$:

$$ X^T(Y-\hat{Y}) = 0 $$
$$ X^T\hat{Y} = X^TY $$
$$ X^TX\hat{\beta} = X^TY $$


$$ \hat{\beta} = (X^TX)^{-1}X^TY $$

# Estimating Variance: Sum Square Errors
The residual is $$r = Y - \hat{Y} = Y - X\hat{\beta}$$.

In least squares, the sum of the squared residuals are minimized. 

$$ SSE = r^Tr $$
$$ = (Y - X\hat{\beta})^T(Y - X\hat{\beta}) $$
$$ = Y^TY - 2\hat{\beta}^TX^TY + \hat{\beta}^TX^TX\hat{\beta} $$
$$ = Y^TY - \hat{\beta}^TX^TY + \hat{\beta}^T[X^TX\hat{\beta} - X^TY] $$

and since $$ X^TX\hat{\beta} = X^TY $$
$$ = Y^TY - \hat{\beta}X^TY $$

So...

$$ SSE = Y^TY - \hat{\beta}^TX^TX\hat{\beta} $$

From this we can derive an unbiased estimate of $$\sigma^2$$, the mean square error:

$$ MSE = \frac{SSE}{n - p} $$

Note that in least squares, we always minimize the SSE. So the sum of the residuals is always equal to 0.

$$ min( \Sigma (y - \hat{y})^2 ) = 2 \Sigma (y - \hat{y}) = 0 $$

# Distribution of Beta Estimates
We know that 
$$ E[Y] = X\beta $$
$$ Var[Y] = \sigma^2 $$

The beta parameters are unbiased:
$$ E[\beta] = (X^TX)^{-1}X^TE[Y] $$
$$ = (X^TX)^{-1}X^TX\beta $$

$$ E[\hat{\beta}] = \beta $$

The variance of the beta parameters:
$$ Var[\hat{\beta}] = (X^TX)^{-1}X^TVar[Y]X(X^TX)^{-1} $$
$$ = \sigma^2 (X^TX)^{-1}X^TX(X^TX)^{-1} $$

$$ Var[\hat{\beta}] = \sigma^2 (X^TX)^{-1} $$

Thus $$ \hat{\beta} $$ ~ $$ N(\beta, \sigma^2(X^TX)^{-1}) $$, and since $$\sigma^2$$ is estimated with $$MSE$$, we use a t-distribution to determine the sigificance of the $$\hat{\beta}$$ parameter.
 
# Regression Assumptions
Assumptions for OLS require $$ e_i $$ ~ $$ N(0, \sigma^2I) $$, in other words:
* Normal distribution of errors
* Linear relationship
* Constant variance of the errors 
* Independent, uncorrelated errors

These assumptions fullfill the requirement of the Gauss-Markov theorem.

The Gauss-Markov theorem states that if
* $$ E[e_i] = 0 $$
* $$ Var[e_i] = \sigma^2I $$ - homoskedasticity
* $$ cov[e_i, e_j] \forall i \ne j $$ - uncorrelated errors

then the $$\hat{\beta}$$ derived above is the best linear unbiased estimator (BLUE) in that it has the lowest variance of all unbiased linear estimators.

# Example

{% highlight r %}
y <- rnorm(100)
x1 <- runif(100, 3, 7)
x2 <- rexp(100, 2.2)
x3 <- rpois(100, 1)
X <- as.matrix(data.frame(1, x1, x2, x3))
B <- solve(t(X) %*% X) %*% t(X) %*% y
coef <- coef(lm(y ~ x1 + x2 + x3))
c(B)
{% endhighlight %}



{% highlight text %}
## [1]  0.11606752 -0.05684882 -0.15140921  0.05308113
{% endhighlight %}



{% highlight r %}
c(coef)
{% endhighlight %}



{% highlight text %}
## (Intercept)          x1          x2          x3 
##  0.11606752 -0.05684882 -0.15140921  0.05308113
{% endhighlight %}
