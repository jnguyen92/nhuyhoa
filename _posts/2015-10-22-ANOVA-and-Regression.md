---
layout: post
title: "Analysis of Variance and Regression"
date: "October 22, 2015"
categories: statistics
---

* TOC
{:toc}



# Some Theory
Suppose

* $$X_1, ..., X_n$$ are observations of a random sample from $$N$$ ~ $$N(\mu, \sigma^2)$$
* $$\bar{X} = \frac{1}{n} \Sigma_i^n X_i $$ is the sample mean of the observations
* $$ S^2 = \frac{1}{n - 1} \Sigma_i^n (X_i - \bar{X})^2 $$ is the sample variance of the observations

Then:

* $$\bar{X}$$ and $$S^2$$ are independent
* $$\frac{(n-1)S^2}{\sigma^2} = \frac{\Sigma_i^n (X_i - \bar{X})^2}{\sigma^2} $$ ~ $$ X^2_{n - 1} $$

We also know that if we have $$V$$ ~ $$ X^2_m $$ and $$W$$ ~ $$ X^2_k $$ and $$ V \perp W $$ then $$ F = \frac{V/m}{W/k} $$ ~ $$F_{m, k}$$. 

# Analysis of Variance
We can use the above theory to conduct hypothesis tests regarding models. 

The basic idea here is to compare the variance accounted for by the covariates to the variance of the error. We can do that by computing the test statistic $$F = \frac{SS_R/df_R}{SS_E/df_E} $$, where $$SS_R$$ and $$df_R$$ are the sums of squares and degrees of freedom accounted for by the covariates and $$SS_E$$ and $$df_E$$ are the sums of squares and degrees of freedom of the error. Recall from above that this is the $$ F $$ distribution with $$ df_R, df_E $$ degrees of freedom. Use this distribution to determine if the ratio observed is too great to be due to chance.

Consider a simple case, where the responses are continuous and the covariates are categorical. 


{% highlight r %}
# fit model
m <- lm(Petal.Length ~ Species, data = iris)
# anova table
anova(m)
{% endhighlight %}



{% highlight text %}
## Analysis of Variance Table
## 
## Response: Petal.Length
##            Df Sum Sq Mean Sq F value    Pr(>F)    
## Species     2 437.10 218.551  1180.2 < 2.2e-16 ***
## Residuals 147  27.22   0.185                      
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
{% endhighlight %}

Here we see that the $$F$$ statistic, a ratio of the mean square errors, is too big to be due to chance with a p-value < 0.05. This means that the variability accounted for by the $$Species$$ variable is significantly greater than the variability of the error. Another way to say this is that the variability between $$Species$$ is significantly greater than the pooled variability within treatments, assuming all treatments have equal variance. 

This basic case is known as single-factor ANOVA. The procedures here can be extended to any model, with categorical or continuous variables. 

Consider the follow model:

{% highlight r %}
# fit model
m <- lm(Sepal.Length ~ Petal.Length * Species, data = iris)
# anova table
anova(m)
{% endhighlight %}



{% highlight text %}
## Analysis of Variance Table
## 
## Response: Sepal.Length
##                       Df Sum Sq Mean Sq  F value    Pr(>F)    
## Petal.Length           1 77.643  77.643 685.8998 < 2.2e-16 ***
## Species                2  7.843   3.922  34.6441 5.206e-13 ***
## Petal.Length:Species   2  0.381   0.190   1.6828    0.1895    
## Residuals            144 16.301   0.113                       
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
{% endhighlight %}

We had looked at the summary table for this model in a previous post. The results of an analysis of variance table should be read in a certain order. 
  
* 1st row: variance accounted for by $$Petal.Length$$ is significantly greater than the variance of the error
* 2nd row: after $$Petal.Length$$ has been taken into consideration, the variance accounted for by $$Species$$ is signficantly greater than the variance of the error
* 3rd row: after $$Petal.Length$$ and $$Species$$ have been taken into consideration, the variance accounted for by the interaction of $$Petal.Length$$ and $$Species$$ is not significantly different than the variance of the error

These extensions from the basic case are known as ANCOVA (analysis of covariance), MANOVA (multivariate analysis of variance), and MANCOVA (multivariate analysis of variance). They can all be assessed with linear models using the summary table or anova table in R.

# Comparing Models
Suppose we want to compare 2 models, where one is nested in the other. Let H correspond to the simplified model with p - q parameters. Let A correspond to the full model with p parameters. Then we can compare the two models with a test statistic

$$ F = \frac{(SSE_H - SSE_A)/ (df_A - df_H)}{SSE_A/df_A} $$

where $$ F $$ ~ $$ F_{q, n - p} $$ and SSE is the sum square errors and DF is the residual degrees of freedom corresponding to the specified model.

Let's look at an example:

{% highlight r %}
# fit models
modh <- lm(Sepal.Length ~ Petal.Length + Species, data = iris)
moda <- lm(Sepal.Length ~ Petal.Length * Species, data = iris)

# obtain the sum square error
RSSh <- anova(modh)["Sum Sq"][3,1]
RSSa <- anova(moda)["Sum Sq"][4,1]

# compute the F statistic & p-value by hand
F <- (RSSh - RSSa) / (2) / (RSSa/144)
pf(F, 2, 144, lower.tail = FALSE)
{% endhighlight %}



{% highlight text %}
## [1] 0.1894918
{% endhighlight %}



{% highlight r %}
# using built in R
anova(modh, moda)
{% endhighlight %}



{% highlight text %}
## Analysis of Variance Table
## 
## Model 1: Sepal.Length ~ Petal.Length + Species
## Model 2: Sepal.Length ~ Petal.Length * Species
##   Res.Df    RSS Df Sum of Sq      F Pr(>F)
## 1    146 16.682                           
## 2    144 16.301  2   0.38098 1.6828 0.1895
{% endhighlight %}

Thus we conclude that for this data set, the full and simplified model are not significantly different from each other. The interaction term is not significantly different from 0, meaning that the slope of Petal.Length on Sepal.Length is the same for all species. 