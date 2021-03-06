---
layout: post
title: "Multiple Sample Tests part 2"
date: "January 28, 2016"
categories: Statistics
tags: Experimental_Design
---

* TOC
{:toc}




# Other Topics

## Pairwise Comparisons and Contrasts

After rejecting an F test, the next step is to determine exactly which factor levels are different.

A contrast is a linear function of the group means. It can be used to compare two means or any set of groups of groups.

Procedure:

* An arbitrary contrast $$C = \Sigma^r_{i = 1} c_i \mu_i$$ where $$\Sigma^r_{i = 1} c_i = 0$$
* $$C$$ is estimated with $$\hat{C} = \Sigma^r_{i = 1} c_i \bar{Y}_i$$ 
* The variance of the estimate $$\hat{C}$$ is $$s_{\hat{C}}^2 = \sigma^2_e \Sigma^r_{i = 1} \frac{c^2_i}{n_i}$$

Test $$H_0: \sum c_i = 0$$ with 

$$T = \frac{\sum c_i \bar{y}_{i.}}{s_{\epsilon} \sqrt{\sum c_i^2 / n_i}}$$

where $$T \sim t_{dfE}$$ (two-sided test). A $$95$$% confidence interval may also be created.

Note that when there is an ANOVA with $$k$$ treatments and a set of $$k - 1$$ orthogonal contrasts (ie $$c_i c_j = 0$$), then the SS will add up to $$SSTrt$$. One example of a set of orthogonal contrasts are linear, quadratic, cubic, etc contrasts.

When one is assessing multiple contrasts, it would be wise to control for [multiple comparisons][multiple_comp_link]{:target = "_blank"}. 

In R, use `pairwise.t.test(y, x)`, `p.adjust()`. Contrasts can be tested using the `multcomp::glht()` function.

For example

{% highlight r %}
library(multcomp)
contr <- glht(mod, linfct = mcp(x = "Tukey"))
summary(contr)
confint(contr)

# see the following for adjustments
p.adjust.methods
{% endhighlight %}

## Unreplicated Studies

If unreplicated studies need to be run (perhaps for preliminary studies), there needs to be another way to obtain an estimate of $$\sigma^2_{\epsilon}$$. 

There are a number of ways to do this:

* Use a known value of $$\sigma^2_{\epsilon}$$ from another study
* Choose a central point of combinations and replicate it to obtain an estimate of $$\sigma^2_{\epsilon}$$ (would need to assume equal variance)
* Assume that larger order interaction terms are insignificant and pools those into the error term $$\sigma^2_{\epsilon}$$
* Assume that if none of the effects are significant, the estimated effects $$\sim iidN(0, \frac{4\sigma^2_{\epsilon}}{2^k})$$. Make a QQ-plot of estimated effects and those effects not on the QQ-line are significant

## ANOVAs as Regression To Alleviate Missing Data
There are many advantages of treating ANOVAs as a regression problem

* Incorporate continuous data that may be important
* Handle unbalanced groups and missing data
* Use Type 3 SS and lsmeans (which consider other variables in model first) to assess factor effects
* Can assess effects by comparing full and reduced models

**Unbalanced ANOVAs**

How are the effects assessed when certain factor combinations are missing? For this, contrasts can come in handy.

* Fit a one-way ANOVA as a combination of the factors 
* Fit a contrast to assess the effect (perhaps the interaction)
* Use a MSE from the one-way ANOVA as an estimate of $$s^2_{\epsilon}$$

## Statistical Software

Note that statistical software may not necessarily get the model correct. Prior to interpreting results, make sure to guarantee that the degrees of freedom are correct. 

In R, ANOVA can be fit with the `lm()`,`lme4::lmer()`,`anova()` and `aov()` commands. There is an additional package that provides the functions `lmerTest::glmer()` and `lmerTest::anova()`. For glms, there is `MASS::glmmPQL()` and `lme4::glmer()`. Confirm that the predictors are factors.

In SAS, ANOVA can be fit with the `proc glm` or `proc mixed` commands.


{% highlight r %}
# for regular models
proc glm;
  # these are the categorical variables
  class x1 x2; 
  # model statement with option for no intercept, Type 1 SS, Type 3 SS
  model y = x1 x2 x1*x2 / noint e1 e3; 
  # conducts a test on x1 factor using interaction as error (useful for mixed models)
  test h = x1 e = x1*x2; 
  random x1*x2 / test;

  # lsmeans and pairwise comparisons/contrasts
  lsmeans x1 x2 / tdiff pdiff cl adjust = tukey;
  # can also define contrasts
  contrast "name" x1 1 1 -1 -1 / e;
  # output the model diagnostics for plotting and whatnot
  output out = outfile p = yhat r = resid rstudent = rstud;
run;
{% endhighlight %}

Some notes on terms

* Type 1 SS takes takes into account fitting order given by model
* Type 3 SS is the SS of that variable when fit last
* LSMEANS is the mean after other variables in the model has been accounted for (blocks, etc)
* If there are missing data or unbalanced designs, it is best to use Type 3 SS and LSMEANS


{% highlight r %}
# for mixed models
proc mixed;
  class A B Field;
  model y = A B A*B;
  random field(A);
run;

# for repeated measure models
proc mixed;
  class Person Gender Age;
  model y = Gender Age Gender*Age;
  repeated / type = cs subject = Person;
  # repeated / type = ar(1) subject = Person;
run;

# random slope and intercept
proc mixed;
  class Person Gender;
  model y = Gender Age;
  random intercept Age / type = un subject = Person g;
run;
{% endhighlight %}

Some notes on terms

* `b(a)` means $$b$$ is nested within $$a$$
* `proc glm` requires the plot error to be in the model term as well as the random term, `proc mixed` doesn't

# Nonparametric Tests and Equal Variance Tests

## Nonparametric Tests

### Ranked ANOVA
A nonparametric alternative to ANOVA uses a rank transformation. The procedure for this method is 

1. Rank data set from largest to smallest
2. Analyze rank values in standard ANOVA

**Kruskal-Wallis Test**

This test has the following assumptions

* Independent samples
* Continuous variable
* Equal variances
* Identical (but non-normal) distributions

Procedure:

1. Rank the combined data
2. Record the mean ranks for each group $$\bar{R}_i$$
3. Compute the test statistic

$$KW = (N - 1) \frac{\sum^k_{i = 1} n_i (\bar{R}_{i.} - \bar{R})^2}{\sum^k_{i = 1} \sum^{n_i}_{j = 1} (R_{ij} - \bar{R})^2}$$

where $$KW \sim X^2_{k - 1}$$. When sample sizes are small, compare to permutations or distribution tables. 

In R, this model is fit with `kruskal.test()`.

**Friedman's Test**

The nonparametric equivalent for the two-factor ANOVA is Friedman's test. The null hypothesis is that each rank within each block is equally likely. 

Procedure:

1. Rank the observations within each block
2. Record the mean ranks for each group $$\bar{R}_i$$ across blocks
3. Compute the test statistic

$$Q = N^2(k - 1) \frac{\sum^k_{i = 1} (\bar{R}_{i.} - \bar{R})^2}{\sum^k_{i = 1} \sum^{n_i}_{j = 1} (R_{ij} - \bar{R})^2}$$

where $$Q \sim X^2_{k - 1}$$. When sample sizes are small, compare to permutations or distribution tables. 

In R, this model is fit with `friedman.test()`.

## Tests of Equal Variance
**Levene's Test**

Levene's test is a formal test to assess $$H_0: \sigma^2_1 = ... = \sigma^2_k$$. 

Procedure:

1. Let $$d_{ij} = \| y_{ij} - \tilde{y_i} \|$$ where $$\tilde{y_i}$$ is the median of group $$i$$
2. Perform a one-way ANOVA on the $$d_{ij}$$
3. Reject $$H_0: \sigma^2_1 = ... = \sigma^2_k$$ if the $$F$$-test is significant

[multiple_comp_link]: http://jennguyen1.github.io/nhuyhoa/statistics/Multiple-Comparisons.html
