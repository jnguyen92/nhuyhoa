---
layout: post
title: "Discriminant Analysis & Naive Bayes"
date: "December 29, 2015"
categories: ['statistics', 'machine learning']
---

* TOC
{:toc}




# Classification Techniques
There are a number of classification techniques. We had covered logistic and multinomial regression in a previous post. These are called discriminant learning algorithms, where try to learn $$p(y\vert x)$$ directly. Here we will discuss naive bayes and linear/quadratic discriminant analysis. These algorithms are called generative learning algorithms that try to model $$p(x\vert y)$$ and $$p(y)$$. They use Bayes rule to derive $$p(y\vert x)$$.

# Probability Review

* Conditional Probability: $$P(A \vert B) = \frac{P(AB)}{P(B)}$$

* Independence: $$P(AB) = P(A)P(B)$$ or $$P(A) = P(A \vert B)$$

* Chain Rule: $$P(ABC) = P(A \vert B, C) P(B \vert C) P(C) $$

* Bayes Rule: $$ P(A \vert B) = \frac{P(B \vert A) P(A)}{ P(B)} $$

* Conditional Addition: $$P(B) = P(B \vert C) P(C) + P(B \vert C^c) P(C^c) $$

# Discriminant Analysis
Discriminant analysis seeks to model the distribution of X in each of the classes separately. Bayes theorem is used to flip the conditional probabilities to obtain $$P(Y \vert X)$$. The approach can use a variety of distributions for each class. The techniques discussed will focus on normal distributions.

## Linear Discriminant Analysis

**For p = 1:**
Recall the pdf for the Gaussian distribution:

$$f_k(x) = \frac{1}{\sigma_k \sqrt{2\pi}} e^{-\frac{1}{2}(\frac{x - \mu_k}{\sigma_k})^2}$$

Then we get
$$P(Y = k \vert X = x) = \frac{\pi_k \frac{1}{\sigma_k \sqrt{2\pi}} exp \left( -\frac{1}{2} (\frac{x - \mu_k}{\sigma})^2 \right)}{\sum_{l = 1}^K \pi_l\frac{1}{\sigma \sqrt{2\pi}}exp\left(-\frac{1}{2} (\frac{x - \mu_l}{\sigma})^2\right)}$$

where $$\pi_k = P(Y = k)$$.

We can simplify by taking logs and simplifying.
$$\log \left( P(Y = k \vert X = x) \right) = \frac{\log \left( \frac{1}{ \sigma \sqrt{2\pi}}\right) + \log(\pi_k) - \frac{x^2 - 2x\mu_k + \mu^2_k}{2\sigma^2}}{\log \left( \sum_{l = 1}^K \pi_l\frac{1}{\sigma \sqrt{2\pi}}exp\left(-\frac{1}{2} (\frac{x - \mu_l}{\sigma})^2\right) \right)} $$

Since we are concerned with maximizing, we can remove all constants (terms that do not depend on $$k$$) to obtain the discriminant score.

$$\delta_k(x) = \log \left( P(Y = k \vert X = x) \right) = x\frac{\mu_k}{\sigma^2} - \frac{\mu^2_k}{2\sigma^2} + \log(\pi_k) $$

We assign $$x$$ to the class with the largest discriminant score.

**For p > 1:**
The pdf for the multivariate Gaussian distribution:

$$f(x) = \frac{1}{(2\pi)^{p/2} \vert \Sigma \vert^{1/2}} e^{-\frac{1}{2} (x - \mu)^T \Sigma ^{-1} (x - \mu)} $$

The discriminant function is

$$\delta_k(x) = x^T \Sigma^{-1} \mu_k - \frac{1}{2} \mu_k^T \Sigma^{-1} \mu_k + \log(\pi_k)$$ 

Note that here we assume that the covariance matrix $$\Sigma$$ is the same for each class.

## Estimating Parameters
We can estimate the model parameters using the training data.

.$$ \hat{\pi}_k = \frac{n_k}{n} $$

.$$ \hat{\mu}_k = \frac{1}{n_k}\Sigma_{i} x_i * I(y_i = k)$$


.$$\Sigma = \frac{1}{n - k} \sum^K \sum_{i = 1}^n (x - \mu_k)(x - \mu-K)^T$$ where $$k$$ is the number of classes

## Response Probabilities
We can compute the class probabilities with our discriminant function

$$ P(Y = k \vert X = x) = \frac{e^{\hat{\delta}_k}}{\sum_{l = 1}^K e^{\hat{\delta}_l}}$$

## Quadratic Discriminant Analysis
In quadratic discriminant analysis, we don't make the assumption that the covariance marix $$\Sigma_k$$ is the same for each class. 

This changes our discriminant function to 

$$\delta_k(x) = -\frac{1}{2}(x - \mu_k)^T \Sigma^{-1}_k(x - \mu_k) + \log(\pi_k)$$ 

## In R

We can fit LDA in R as such

{% highlight r %}
lda.fit <- lda(Direction ~ Lag1 + Lag2, data = Smarket)
lda.fit
{% endhighlight %}



{% highlight text %}
## Call:
## lda(Direction ~ Lag1 + Lag2, data = Smarket)
## 
## Prior probabilities of groups:
##   Down     Up 
## 0.4816 0.5184 
## 
## Group means:
##             Lag1        Lag2
## Down  0.05068605  0.03229734
## Up   -0.03969136 -0.02244444
## 
## Coefficients of linear discriminants:
##             LD1
## Lag1 -0.7567605
## Lag2 -0.4707872
{% endhighlight %}
The coefficients of linear discriminants are provide the linear combinations of covariates to form the decision rule.


{% highlight r %}
lda.pred <- predict(lda.fit, sample_frac(Smarket, .1))
names(lda.pred)
{% endhighlight %}



{% highlight text %}
## [1] "class"     "posterior" "x"
{% endhighlight %}

* `class`: LDA's label prediction
* `posterior`: matrix whose $$k^{th}$$ column corresponds $$P(Y = k \vert X)$$
* `x`: linear discriminants $$\delta(x)$$

Similarly, we can fit QDA in R using `qda()`.

# Naive Bayes
Naive Bayes is a classification technique that uses Bayesian statistics. We make the assumption that all features ($$X_i$$) are conditionally independent of each other given its class ($$Y$$). That is, $$P(X_i \vert X_j, Y) = P(X_i \vert Y)$$ where $$i \ne j$$. The goal is to find the value of $$Y$$ that is most likely given $$X_i$$.
$$ argmax_y P(Y = y \vert X_i) $$
$$ argmax_y \frac{P(Y = y) \prod_{i = 1}^p P(X_i \vert Y = y)}{P(X_i)} $$

Since the denominator is constant across all values of $$y$$, we can just focus on the numerator. Thus our goal is to find (our discriminant function)

$$ argmax_y P(Y = y) \prod^p_{i = 1} P(X_i = x_i \vert Y = y)$$

Since we are dealing with probabilities, we may be multiplying very small values. So to prevent underflow, we can use logs.

$$ argmax_y \log( P(Y = y) ) \sum_{i = 1}^p \log(P(X_i = x_i \vert Y = y))$$

## Numeric Features
If our original input feature is continuous, we can convert it to a discrete value by binning. This way we can compute the probabilities. 

## Laplace Smoothing
Our probability estimates can be vulnerable to rare events. In these cases, we may estimate our probabilities to be 0 which ends up canceling all of our calculations. This is a problem especially when our data set is small. 

So the solution to this is to do Laplace estimates, where we add 1 to all counts. This smoothing step's effect is great when data sets are small and doesn't have an effect on calculations when data sets are large. 

This effect changes the probability calculations to 

$$P(a) = \frac{n_a + 1}{\Sigma_i (n_i + 1)}$$

## Example
Say we are given training data that looks like this.

|fruit  | long| sweet| yellow| total|
|:------|----:|-----:|------:|-----:|
|apple  |    0|   215|     50|   265|
|banana |  400|   215|    400|  1015|
|other  |  100|    70|     50|   220|
|total  |  500|   500|    500|  1500|

From this data, we have all the information we need to compute the conditional and marginal probabilities. 

However, we see that there are $$0$$s in the data. This is concerning because multiplying anything by zero automatically cancels it out. We can use Laplace estimates to adjust the counts (add 1 to all counts). Thus we account for very rare occurances that may not occur in this training data set. 


|fruit  | long| sweet| yellow| total|
|:------|----:|-----:|------:|-----:|
|apple  |    1|   216|     51|   268|
|banana |  401|   216|    401|  1018|
|other  |  101|    71|     51|   223|
|total  |  503|   503|    503|  1509|

Now say we are given information of a fruit that is long, sweet and yellow. Given this information we can predict what class this fruit belongs to.

---------------------------------------|---------------------
$$P(apple \vert long, sweet, yellow)$$ | $$ = P(long \vert apple)P(sweet \vert apple)P(yellow \vert apple)P(apple) $$
                                       | $$ = 0.0037 * 0.81 * 0.19 * 0.18$$
                                       | $$ = 0.0001$$

---------------------------------------|---------------------
$$P(banana \vert long, sweet, yellow)$$| $$ = P(long \vert banana)P(sweet \vert banana)P(yellow \vert banana)P(banana)$$
                                       | $$ = 0.39 * 0.21 * 0.39 * 0.67$$
                                       | $$ = 0.021$$

---------------------------------------|---------------------
$$P(other \vert long, sweet, yellow)$$ | $$ = P(long \vert other)P(sweet \vert other)P(yellow \vert other)P(other)$$
                                       | $$ = 0.45 * 0.32 * 0.23 * 0.15$$
                                       | $$ = 0.005$$

Based off the evidence, we assume that this sweet, long, and yellow fruit is a banana. 

## Gaussian Naive Bayes
Gaussian naive Bayes assumes that each $$\Sigma_k$$ is diagonal. Thus

$$\delta_k(x) \propto \log \left( \pi_k \prod_{j = 1}^p f_{kj}(x_j) \right) = -\frac{1}{2} \sum_{j = 1}^p \frac{(x_j - \mu_{kj})^2}{\sigma^2_{kj}} + \log(\pi_k) $$

## EM Algorithm for Missing Labels
Consider the scenario when we want to use Naive Bayes to evaluate a data set in which the labels are missing. This is a kind of unsupervised learning algorithm. 

We can use the [EM algorithm][em_post]{:target = "_blank"}.

* Initially guess the labels for each example
* Repeat until convergence
  * E-Step: train Naive Bayes using labeled examples
  * M-Step: use model to predict the labels for originally unlabled examples

# Logistic Regression vs. Discriminant Analysis vs. Naive Bayes 

**Best to use Logistic Regression:**

* More robust to deviations from modeling assumptions (non-Gaussian features)

**Best to use Discriminant Analysis:**

* When we can make the assumption that the features are Gaussian
* More efficient than logistic regression when the assumptions are correct
* Works better than logistic regression when data is well-separated
* Popular for multinomial responses since it provides a low-dimensional viewof data

**Best to use Naive Bayes:**

* Can make the assumption that features are independent (conditional on response)
* Despite strong assumptions, works well on many problems

[em_post]: http://jnguyen92.github.io/nhuyhoa//2015/12/ML-Generic-Algorithms.html#expectation-maximization