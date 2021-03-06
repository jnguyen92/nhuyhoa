---
layout: post
title: "GLM: Testing & Diagnostics"
date: "November 25, 2015"
categories: Statistics
tags: Regression
---

* TOC
{:toc}



# Hypothesis Testing

* Null model: intercept only model
* Saturated model: model with $$n$$ obs and $$n$$ parameters, each data point has its own parameter
* Proposed model: model with $$p$$ parameters, model that is fit

## Deviance
Derived from the likelihood ratio statistic, deviance is defined as 

$$ D(y) = 2 \left( L(y \vert \hat{\theta}_s)) - L(y \vert \hat{\theta}_m)) \right) $$

where $$L$$ denotes the log likelihood, $$\hat{\theta}_m$$ denotes fitted values for the proposed model and $$\hat{\theta}_s$$ denotes fitted values for the saturated model.

The deviance is asymptotically distributed $$X^2_p$$.

## Pearson X2
The Pearson $$X^2$$ statistic is

$$X^2 = \Sigma_i \frac{(y_i - \hat{\mu}_i)^2}{var(\hat{\mu}_i)}$$

The Pearson $$X^2$$ is asymptotically distributed $$X^2_p$$.

## Goodness of Fit Test
Does the current model fit the data? The goodness of fit is a comparison of two models (the proposed model and the saturated model).

Fit a GLM in R and assess goodness of fit.

{% highlight r %}
mod1 <- glm(cbind(dead, alive) ~ conc, family = binomial, data = bliss)
summary(mod1)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## glm(formula = cbind(dead, alive) ~ conc, family = binomial, data = bliss)
## 
## Deviance Residuals: 
##       1        2        3        4        5  
## -0.4510   0.3597   0.0000   0.0643  -0.2045  
## 
## Coefficients:
##             Estimate Std. Error z value Pr(>|z|)    
## (Intercept)  -2.3238     0.4179  -5.561 2.69e-08 ***
## conc          1.1619     0.1814   6.405 1.51e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 64.76327  on 4  degrees of freedom
## Residual deviance:  0.37875  on 3  degrees of freedom
## AIC: 20.854
## 
## Number of Fisher Scoring iterations: 4
{% endhighlight %}

* Null deviance: $$D_{sat} - D_{null}$$
* Residual deviance: $$D_{sat} - D_{model}$$

With these values, conduct the hypothesis tests.

{% highlight r %}
# goodness of fit - using deviance
pchisq(mod1$deviance, mod1$df.residual, lower.tail = FALSE)
{% endhighlight %}



{% highlight text %}
## [1] 0.9445968
{% endhighlight %}
With a $$p.value = 0.94$$, there is no evidence of lack of fit. 


{% highlight r %}
# goodness of fit - using pearson X2
pchisq(sum(residuals(mod1, "pearson")^2), mod1$df.residual, lower.tail = FALSE)
{% endhighlight %}



{% highlight text %}
## [1] 0.9469181
{% endhighlight %}
Use the Pearson residuals to compute the Pearson $$X^2 = \sum r_{pearson}^2$$.

The result is comparable to the deviance.

## Compare Two Models
Compare two nested models, which one is better?


{% highlight r %}
# test significance of conc by comparing to null mod1el
pchisq(mod1$null.deviance - mod1$deviance, mod1$df.null - mod1$df.residual, lower.tail = FALSE)
{% endhighlight %}



{% highlight text %}
## [1] 1.023593e-15
{% endhighlight %}



{% highlight r %}
anova(mod1, test = "Chi")
{% endhighlight %}



{% highlight text %}
## Analysis of Deviance Table
## 
## Model: binomial, link: logit
## 
## Response: cbind(dead, alive)
## 
## Terms added sequentially (first to last)
## 
## 
##      Df Deviance Resid. Df Resid. Dev  Pr(>Chi)    
## NULL                     4     64.763              
## conc  1   64.385         3      0.379 1.024e-15 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
{% endhighlight %}
Here the concentration term is significant. 

Compare to more complex models.

{% highlight r %}
# compare to quadratic concentration term
mod2 <- glm(cbind(dead, alive) ~ conc + I(conc^2), family = binomial, bliss)
anova(mod1, mod2, test="Chi")
{% endhighlight %}



{% highlight text %}
## Analysis of Deviance Table
## 
## Model 1: cbind(dead, alive) ~ conc
## Model 2: cbind(dead, alive) ~ conc + I(conc^2)
##   Resid. Df Resid. Dev Df Deviance Pr(>Chi)
## 1         3    0.37875                     
## 2         2    0.19549  1  0.18325   0.6686
{% endhighlight %}
The results here indicate no need for a quadratic concentration term. 

# Diagnostics

## What Can Go Wrong

If the deviance is much larger than expected, then one needs to determine which aspects of the model specification is incorrect. 

* Observations are not independent
* Non linearity between covariates and link function: wrong structural form, explore different fits, transformations of predictors, etc
* Presence of outliers
* Sparse data: large samples/group sizes are needed for MLE asymptotic convergence
* Overdispersion: variance greater than assumed, which can arise when independent or identical assumptions are violated

Note that unlike in linear regression, the errors do not need to be normally distributed with constant variance.

## Residuals

* Response residuals: $$y - \hat{\mu}$$ has non-constant variance
* Pearson residuals: $$\frac{y - \hat{\mu}}{\sqrt{\hat{\phi}Var(\hat{\mu})}}$$, where $$\sum (r^p_i)^2 = X^2$$
* Deviance residuals: $$sign(y - \hat{\mu}) \sqrt{d}_i$$, where $$\sum (r^d_i)^2 = Deviance$$
* Jackknife residuals (studentized residuals): expensive to compute, but approximations are available

## Leverages
Since GLMs use the IRWLS algorithm, the leverage values are affected by the weights. The hat matrix is defined by 

$$ H = W^{1/2}X(X'WX)^{-1}X'W^{1/2} $$

where $$W = diag(w)$$

The diagonal elements of $$H$$ contain the leverages $$h_{i}$$.

## Cook's Distance
The Cook statistics: 

$$D_i = 
\frac{(\hat{\beta}_{(i)} - \hat{\beta})' (X'WX) (\hat{\beta}_{(i)} - \hat{\beta})}{p\hat{\phi}}$$

## DFBETAs
Similar to to the linear model, DFBETAs can examine the change in fit (coefficients) from omitting an observation.

## DIFDEV and DIFCHISQ
These statistics detect observations that heavily add to the lack of fit, when there is a large difference between the fitted and observed values.

DIFDEV measures the change in deviance if an individual observation is deleted.

DIFCHISQ measures the change in $$X^2$$ if an individual observation is deleted.

## How to Obtain Diagnostics in R

* Response residuals: `residuals(m, "response")`
* Pearson residuals: `residuals(m, "pearson")`
* Deviance residuals: `residuals(m)`
* Studentized residuals: `rstudent(m)`
* Leverage values: `influence(m)$hat`
* Cook statistics: `cooks.distance(m)`
* DFBETAs: `influence(m)$coef`

## Diagnostic Plots in R

### Residual vs Fitted Plots

{% highlight r %}
# fit model
mod <- glm(Species ~ ., family = poisson, data = g)
{% endhighlight %}

Fit plots equivalent to the residuals vs fitted plots in linear regression. Since the deviance residuals (which are standardized) are used, there should be patterns indicating constant variance. 


{% highlight r %}
# deviance resids vs fitted response
g1 <- qplot(y = residuals(mod), x = predict(mod, type = "response")) + 
  xlab(expression(hat(mu))) + 
  ylab("Deviance Residuals")
# deviance residuals vs fitted link
g2 <- qplot(y = residuals(mod), x = predict(mod, type = "link")) + 
  xlab(expression(hat(eta))) + 
  ylab("Deviance Residuals")

# combine
grid.arrange(g1, g2, nrow = 1)
{% endhighlight %}

<img src="/nhuyhoa/figure/source/2015-11-25-GLM-Testing-and-Diagnostics/unnamed-chunk-8-1.png" title="plot of chunk unnamed-chunk-8" alt="plot of chunk unnamed-chunk-8" style="display: block; margin: auto;" />
Two different scales for the fitted values. Using $$\hat{\eta}$$ is better than $$\hat{\mu}$$. The residuals are evenly spaced across fitted values, and there are no violation of assumptions.

What should be done there are violations?

* Nonlinear trend: consider transformation of covariates (generally better than changing the link function)
* Non-constant variance: change the model

When plotted using the response residuals, there will be variation patterns consistent with the response distribution.


{% highlight r %}
# response residuals vs fitted link
qplot(y = residuals(mod, "response"), x = exp(predict(mod, type = "link"))) + 
  xlab(expression(hat(mu))) + 
  ylab("Response Residuals")
{% endhighlight %}

<img src="/nhuyhoa/figure/source/2015-11-25-GLM-Testing-and-Diagnostics/unnamed-chunk-9-1.png" title="plot of chunk unnamed-chunk-9" alt="plot of chunk unnamed-chunk-9" style="display: block; margin: auto;" />
There is a pattern of increasing variation consistent with the Poisson distribution. 

Another way to spot outliers is to bin the response residuals into equal-sized groups by the fitted values. Within each group, Compute the average residual and average fitted value. Plot the lines $$0 \pm 2 * SE$$ (where $$SE$$ is a function of bin size) within which $$95$$% of the binned residuals to fall if the model was true. 


{% highlight r %}
# we have groups of size n
n <- 2

# organize data
df <- data.frame(resid = residuals(mod, "response"), fit = exp(predict(mod, type = "link")))

# label/group the data based off the fitted value
lab_df <- df %>% 
  arrange(fit) %>% 
  mutate(lab = rep(1:15, each = n)) 

# bin the residuals
bin_df <- lab_df %>% 
  group_by(lab) %>% 
  summarise(r = mean(resid), f = mean(fit))
{% endhighlight %}


{% highlight r %}
# compute the standard error for each bin
se <- exp(predict(mod, type = "link")) / n
bounds <- data.frame(c = rep(exp(predict(mod, type = "link")), 2), bounds = c(0 - 2 * se, 0 + 2*se), group = rep(c("below", "above"), each = 30))
bounds <- arrange(bounds, c)
{% endhighlight %}


{% highlight r %}
# plot the data
ggplot(data = bin_df, aes(f, r)) + 
  geom_point() +
  geom_line(data = bounds, aes(x = c, y = bounds, group = group), linetype = 2) + 
  xlab(expression(hat(mu))) + 
  ylab("Response Residuals")
{% endhighlight %}

<img src="/nhuyhoa/figure/source/2015-11-25-GLM-Testing-and-Diagnostics/unnamed-chunk-12-1.png" title="plot of chunk unnamed-chunk-12" alt="plot of chunk unnamed-chunk-12" style="display: block; margin: auto;" />

Here one out of the 15 bins fall outside of the bounds, with no concerning patterns. 

### Half-Normal Plot
Plot the sorted absolute residuals to the quantiles of the half normal distribution to identify outliers. Look for points off the trend. (The examples used here are different from the model above).





{% highlight r %}
par(mfrow = c(1, 2))
# half-normal of studentized residuals
halfnorm(rstudent(modpl))
# half-norm of leverages
halfnorm(influence(modpl)$hat)
{% endhighlight %}

<img src="/nhuyhoa/figure/source/2015-11-25-GLM-Testing-and-Diagnostics/unnamed-chunk-14-1.png" title="plot of chunk unnamed-chunk-14" alt="plot of chunk unnamed-chunk-14" style="display: block; margin: auto;" />
The half-norm plot of leverage seems to indicate that obs 25 may have high leverage. 


{% highlight r %}
# half-norm of cook's distance
c <- halfnorm(cooks.distance(modpl))
{% endhighlight %}

<img src="/nhuyhoa/figure/source/2015-11-25-GLM-Testing-and-Diagnostics/unnamed-chunk-15-1.png" title="plot of chunk unnamed-chunk-15" alt="plot of chunk unnamed-chunk-15" style="display: block; margin: auto;" />
The half-norm plot indicates that obs 25 has a big Cook's statistic. It might be useful to investigate this observation in closer detail. 

### Added Variable Plots
Similar to regression, one can generate added variable plots. The interpretation is similar to linear models.

In R: `car::avPlots()`

# Overdispersion
Overdispersion occurs when the $$Var(Y_i)$$ is greater than the assumed $$Var(Y_i)$$ by the model. As a result of this, the model deviance could be inflated. To adjust for overdispersion, use quasilikelihood methods. 

## Estimate of Overdispersion Parameter
To account for overdispersion, multiply the variance by a factor of $$\sigma^2$$ to obtain 

$$Var(Y_i)^* = \sigma^2 Var(Y_i)$$

When $$\sigma^2 = 1$$, then this results in the original model. If $$\sigma^2 > 1$$, then there is overdispersion. 

Estimate $$\sigma^2$$ with a Fisher scoring algorithm used to fit the model.

When overdispersion adjustment is applied to modeling, the $$\hat{\beta}$$ coefficients is the same as the original model. The covariance matrix for $$\hat{\beta}$$ changes from $$Var(\hat{\beta}) = (X'WX)^{-1}$$ to $$Var(\hat{\beta}) = \sigma^2 (X'WX)^{-1}$$.

Estimate $$\sigma^2$$ with 

$$\hat{\sigma}^2 = \frac{X^2}{n - p}$$

where $$X^2$$ is the Pearson goodness of fit statistic, $$n$$ is the number of observations, and $$p$$ is the number of parameters.

## Adjustment to Model

Adjustment to model statistics:

* $$SE(\hat{\beta})$$ adjusted to $$\sqrt{\hat{\sigma}^2} SE(\hat{\beta})$$
* Pearson residuals adjusted to $$r_i^{p*} = \frac{r_i^p}{\sqrt{\hat{\sigma}^2}}$$
* Pearson statistic adjusted to $$\frac{X^2}{\hat{\sigma}^2}$$
* Deviance residuals adjusted to $$r_i^{d*} = \frac{r_i^d}{\sqrt{\hat{\sigma}^2}}$$
* Deviance adjusted to $$\frac{deviance}{\hat{\sigma}^2}$$
* When comparing two nested models with dispersion, compare the result to a $$F$$ distribution

To fit a model with a dispersion parameter in R:

* Fit the model

{% highlight r %}
mod <- glm(cbind(damage, 6 - damage) ~ temp, data = orings, family = binomial)
{% endhighlight %}

* Compute the dispersion parameter

{% highlight r %}
o2 <- sum(residuals(mod, "pearson")^2) / (nrow(orings) - 2)
{% endhighlight %}

* Estimate the model with the dispersion parameter

{% highlight r %}
summary(mod, dispersion = o2, correlation = TRUE, symbolic.cor = TRUE)
{% endhighlight %}

Another option is to fit using the quasi-families (quasibinomial, quasipoisson, etc):

{% highlight r %}
mod <- glm(cbind(damage, 6 - damage) ~ temp, data = orings, family = quasibinomial)
{% endhighlight %}

# Solutions to Violation of Assumptions

* Non-linearity between covariates and link function: explore different fits, transformations of predictors, additional predictors, model selection, etc
* Presence of outliers: look into potential reasons (data entry error, scientific reason), fit model with and without the influential point and see if there is a difference, remove point
* Sparse data: find more data, this is needed for MLE and goodness of fit tests
* Overdispersion: adjust for dispersion using quasilikelihood
