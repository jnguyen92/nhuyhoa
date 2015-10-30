---
layout: post
title: "Interpretation of Regression Coefficients"
date: "October 21, 2015"
categories: statistics
---

* TOC
{:toc}




# Continuous Variables


{% highlight r %}
y <- mtcars$mpg
x1 <- mtcars$wt
x2 <- mtcars$drat
m1 <- lm(y ~ x1 + x2)
{% endhighlight %}

With continuous variables, the $$ \beta $$ coefficient can be interpreted as the change in Y for a unit increase in X, holding all other x-values constant. The corresponding test measures whether this effect is significantly different from 0, after all other variables have been considered.



{% highlight r %}
summary(m1)$coefficients %>% round(3)
{% endhighlight %}



{% highlight text %}
##             Estimate Std. Error t value Pr(>|t|)
## (Intercept)   30.290      7.318   4.139    0.000
## x1            -4.783      0.797  -6.001    0.000
## x2             1.442      1.459   0.989    0.331
{% endhighlight %}



{% highlight r %}
b4 <- predict(m1, data.frame(x1 = 1, x2 = 0))
after <- predict(m1, data.frame(x1 = 2, x2 = 0))
after - b4 # same as x1 coefficient
{% endhighlight %}



{% highlight text %}
##        1 
## -4.78289
{% endhighlight %}

When we fit a linear model, we generally include an intercept term. This intercept is interpreted as the expected value of Y when all x's are set to 0. The intercept is a nuisance parameter, we generally don't care about its significance. 


{% highlight r %}
summary(m1)$coefficients %>% round(3)
{% endhighlight %}



{% highlight text %}
##             Estimate Std. Error t value Pr(>|t|)
## (Intercept)   30.290      7.318   4.139    0.000
## x1            -4.783      0.797  -6.001    0.000
## x2             1.442      1.459   0.989    0.331
{% endhighlight %}



{% highlight r %}
predict(m1, data.frame(x1 = 0, x2 = 0)) # same as intercept term
{% endhighlight %}



{% highlight text %}
##        1 
## 30.29037
{% endhighlight %}

If we were to remove the intercept term, we would have

$$ Y = 0 + \beta_1 x_1 + \beta_2 x_2 $$ where $$ \beta_0 = 0 $$.

Thus when we fit a regression without an intercept, we insist that the expected value of Y when all x's are 0 is 0. If we know that the intercept is 0, doing so will give us more residual degrees of freedom. However, care should be done in setting the intercept to 0, as this may lead to errors in estimating the other coefficients. 

# Categorical Variables

{% highlight r %}
m2 <- lm(Petal.Length ~ Species, data = iris)
{% endhighlight %}

Linear models with categorical variables are converted into dummy variables. Coefficients rely some information about the expected value of Y for that category. 

The intercept is interpreted as the expected value of Y for the baseline variable. 


{% highlight r %}
summary(m2)$coefficients %>% round(3)
{% endhighlight %}



{% highlight text %}
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)          1.462      0.061  24.023        0
## Speciesversicolor    2.798      0.086  32.510        0
## Speciesvirginica     4.090      0.086  47.521        0
{% endhighlight %}



{% highlight r %}
baseline <- iris %>% 
  subset(Species == "setosa") %>% 
  summarise(v1 = mean(Petal.Length))
baseline # same as intercept term
{% endhighlight %}



{% highlight text %}
##      v1
## 1 1.462
{% endhighlight %}

The other coefficients represent the expected difference in Y between the X category and the baseline category. The corresponding test measures whether the expected value of Y for the X category and the baseline are significantly different from 0.


{% highlight r %}
summary(m2)$coefficients %>% round(3)
{% endhighlight %}



{% highlight text %}
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)          1.462      0.061  24.023        0
## Speciesversicolor    2.798      0.086  32.510        0
## Speciesvirginica     4.090      0.086  47.521        0
{% endhighlight %}



{% highlight r %}
iris %>% # same as versicolor coefficient
  subset(Species == "versicolor") %>% 
  summarise(d = mean(Petal.Length) - baseline) 
{% endhighlight %}



{% highlight text %}
##      v1
## 1 2.798
{% endhighlight %}

When the intercept term is excluded, the coefficients are no longer relative comparisons. Instead, they are the expected value of Y for that group. The test measures whether the mean is significant different from 0, doesn't portray much meaning. Because of this, there is no meaning in running a linear model without an intercept for categorical covariates.


{% highlight r %}
m2a <- lm(Petal.Length ~ Species - 1, data = iris)
summary(m2a)$coefficients %>% round(3)
{% endhighlight %}



{% highlight text %}
##                   Estimate Std. Error t value Pr(>|t|)
## Speciessetosa        1.462      0.061  24.023        0
## Speciesversicolor    4.260      0.061  69.998        0
## Speciesvirginica     5.552      0.061  91.228        0
{% endhighlight %}

# Continuous and Categorical Variables

{% highlight r %}
m3 <- lm(Sepal.Length ~ Petal.Length*Species, data = iris)
summary(m3)$coefficients %>% round(3)
{% endhighlight %}



{% highlight text %}
##                                Estimate Std. Error t value Pr(>|t|)
## (Intercept)                       4.213      0.407  10.341    0.000
## Petal.Length                      0.542      0.277   1.959    0.052
## Speciesversicolor                -1.806      0.598  -3.017    0.003
## Speciesvirginica                 -3.154      0.634  -4.973    0.000
## Petal.Length:Speciesversicolor    0.286      0.295   0.969    0.334
## Petal.Length:Speciesvirginica     0.453      0.290   1.563    0.120
{% endhighlight %}

This is a model fit with both continuous variables (Petal.Length) and categorical variables (Species). Let's break down the regression formulas.

If Species == "setosa":
$$ Sepal.Length = \beta_0 + \beta_1 * Petal.Length $$
$$ Sepal.Length = 4.2 + 0.54 * Petal.Length $$


If Species == "versicolor"
$$ Sepal.Length = \beta_0 + \beta_1 * Petal.Length + \beta_2 + \beta_4 * Petal.Length $$
$$ Sepal.Length = (\beta_0 + \beta_2) + (\beta_1 + \beta_4) * Petal.Length $$
$$ Sepal.Length = (4.2 + -1.8) + (0.54 + 0.29) * Petal.Length $$

If Species == "virginica"
$$ Sepal.Length = \beta_0 + \beta_1 * Petal.Length + \beta_3 + \beta_5 * Petal.Length $$
$$ Sepal.Length = (\beta_0 + \beta_3) + (\beta_1 + \beta_5) * Petal.Length $$
$$ Sepal.Length = (4.2 + -3.2) + (0.54 + 0.45) * Petal.Length $$

Essentially what we have is 3 separate lines for each value of Species, the categorical variable. 

![plot of chunk unnamed-chunk-10](/nhuyhoa/figure/source/2015-10-21-Interpret-Regression-Coef/unnamed-chunk-10-1.png) 

We can perform hypothesis testing to determine whether the species have similar intercepts and/or similar slopes for Petal Length, thereby simplifying the model. 


{% highlight r %}
m3a <- lm(Sepal.Length ~ Petal.Length*Species - 1, data = iris)
summary(m3a)$coefficients %>% round(3)
{% endhighlight %}



{% highlight text %}
##                                Estimate Std. Error t value Pr(>|t|)
## Petal.Length                      0.542      0.277   1.959    0.052
## Speciessetosa                     4.213      0.407  10.341    0.000
## Speciesversicolor                 2.408      0.438   5.493    0.000
## Speciesvirginica                  1.060      0.486   2.181    0.031
## Petal.Length:Speciesversicolor    0.286      0.295   0.969    0.334
## Petal.Length:Speciesvirginica     0.453      0.290   1.563    0.120
{% endhighlight %}

If we were to remove the intercept, we have the following regression lines.

If Species == "setosa":
$$ Sepal.Length = 4.2 + 0.54 * Petal.Length $$

If Species == "versicolor"
$$ Sepal.Length = 2.4 + (0.54 + 0.29) * Petal.Length $$

If Species == "virginica"
$$ Sepal.Length = 1.1 + (0.54 + 0.45) * Petal.Length $$

This is essentially the same equations as the previous model.

It is important to note even though both models generate similar regression lines, the overall model diagnostics ($$R^2$$, F statistic) will be different because the model is different.