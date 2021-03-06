---
layout: post
title: "Sample Size Calculations"
date: "January 12, 2016"
categories: Statistics
tags: Experimental_Design
---

* TOC
{:toc}



When running experiments it is important that the sample size is large enough to obtain a reasonable amount of power to detect differences in effect sizes. While size differences are unknown prior to the study, researchers can calculate a sample size based of the kind of size difference they wish to detect. 

Larger sample sizes increase power (the probability of rejecting when there is a true difference) and reduces the false discovery rate (Type 1 error). However, due to limited resources it is not possible to gather an unlimited number of samples. Maintaining this balance is exactly the reason why sample size calculations are so important.

See this [resource][sample_size_calculators]{:target = "_blank"} for more information.

# Basic Idea
Consider the observations $$Y_i \sim N(\mu, \sigma^2)$$ for $$i = 1, ..., n$$ where $$\sigma^2$$ is known. 

Then <br>
$$\bar{Y} = \frac{1}{n} \sum_i y_i$$

Reject $$H_0: \mu = 0$$ if $$\bar{Y} > C_n$$ for some $$C_n$$. <br>
In standardized form, reject $$H_0$$ if $$Z = \sqrt{\frac{n}{\sigma^2}} \bar{Y} \ge Z_{1 - \alpha/2}$$. <br>
Therefore $$C_n = \sqrt{\frac{\sigma^2}{n}} Z_{1 - \alpha /2}$$. 

![Null and Alternative Distributions](http://jennguyen1.github.io/nhuyhoa/figure/images/null_and_alternative_dist.png)

Diagrams of the null (left) and alternative (right) distributions.

![Distribution for Sample Size](http://jennguyen1.github.io/nhuyhoa/figure/images/sample_size_calc_diagram.png)

Distribution of null & alternative distribution for sample size calculations.

With this example, in order to obtain the $$\alpha$$ and $$\beta$$ values desired, sample sizes need to be <br>
$$Z_{1 - \alpha/2} + Z_{1 - \beta} = \sqrt{\frac{n}{\sigma^2}} \mu_1$$ <br>
$$n = \frac{(Z_{1 - \alpha/2} + Z_{1 - \beta})^2 \sigma^2}{\mu_1^2}$$

# Sample Size Calculations for Exponential Families
Let $$\theta$$ be the parameter of interest. Then

* score function $$U(\theta)$$
* information $$I(\theta)$$

In large samples, $$E_{\theta}[U(0)] = I(0) \theta$$. 

The test statistic for $$H_0: \theta = 0$$ is 

$$E[Z(0)] = E_{\theta} \left( \frac{U(0)}{\sqrt{I(0)}} \right) = \sqrt{I(0)} \theta$$

To obtain sample sizes, 

$$Z_{1 - \alpha/2} + Z_{1 - \beta} = \sqrt{E_{\theta}[I(0)]} \theta_1$$

So the required Fischer information is

$$f(n) = E_{\theta}[I(0)] = \frac{(Z_{1 - \alpha/2} + Z_{1 - \beta})^2}{\theta_1^2}$$

From here calculate $$E[I(0)]$$, which is a function of required sample $$n$$, and solve for $$n$$. Assume that $$E_{H_0}[I(0)] = E_{H_1}[I(0)]$$.

For one-sided tests, change the $$Z_{1 - \alpha/2}$$ to $$Z_{1 - \alpha}$$.
 
**Examples**
Let $$\epsilon_1 = n_1 / n$$ and $$\epsilon_2 = n_2 / n$$. 

* Two-sample normal data:

$$n = \frac{\sigma^2(Z_{1 - \alpha/2} + Z_{1 - \beta})^2}{\epsilon_1 \epsilon_2 \theta_1^2}$$

* Two-sample binomial data where $$\theta$$ is the log odds ratio

$$n = \frac{(Z_{1 - \alpha/2} + Z_{1 - \beta})^2}{\epsilon_1 \epsilon_2 \bar{p} (1 - \bar{p}) \theta_1^2}$$

* Two-sample binomial data where $$\theta$$ proportion difference

$$n = \frac{(Z_{1 - \alpha/2} + Z_{1 - \beta})^2 \bar{p} (1 - \bar{p})}{\epsilon_1 \epsilon_2  (p_0 - p_1)^2}$$
 
* Survival data assuming proportional hazards

$$n\bar{p} = \frac{(Z_{1 - \alpha/2} + Z_{1 - \beta})^2}{\epsilon_1 \epsilon_2 \theta_1^2}$$

where $$\bar{p}$$ is the probability of an observed event.

# A Simulation Approach

The power relationship is

$$Power \propto \frac{\alpha \sqrt{n} effect.size}{\sigma}$$

For example in ANOVA, 

$$\frac{E[MSA]}{E[MSE]} = 1 + \frac{1}{k - 1} \frac{n\sum_i a^2_i}{s^2_e}$$ <br>
where $$\frac{n\sum_i a^2_i}{s^2_e} = \phi$$ is the non-centrality parameter. 

When $$H_0: a_i = 0 \forall i$$ is true, the $$F$$-statistic has a central $$F$$ distribution. When $$H_0$$ is false, the $$F$$-statistic has a non-central $$F$$ statistic. 

Simulations can be used to calculate the power, $$P(F > F_{\alpha, k-1, k(n-1)} \vert \phi)$$ (non-central F), for a wide range of $$n$$'s (and other specified parameters). 

Another option for calculating sample sizes in ANOVA would be to choose two groups and the maximum difference between them $$D$$. Then sample sizes can be computed based on a t-test approximation. This tends to work if the number of groups is not too large. 

**Selecting Parameters**

Prior to conducting a study, estimates of parameters (effect size, variability, etc) are usually not available. Estimates of these parameters can be obtained in a number of ways

* Conduct pilot studies or review literature for an estimate of variation; it is crucial that the estimate is based on same context as final data
* Specify effect size based on how large a change is of interest; can refer to previous studies, literature, etc
* Use a variety of estimates to cover possible scenarios

Power calculations after the experiment (using the observed effect size) are flawed because the observed effect size is unlikely to match the pre-specified effect size of interest. 

Being able to show good power and pre-specified effect size is important because it implies that the focus is on biological importance rather than ambiguous statistical significance.

# In R
In general, sample sizes calculations are not done by hand. There are a number of software packages that will do sample size calculations. The challenge is to determine the parameters prior to conducting the experiment. Crude estimates of these can be obtained from prior experiments or a range of likely values can be tested.

R has several functions to calculate desired power and sample sizes

* `power.t.test()`
* `power.anova.test()`
* `power.prop.test()`

There are also a number of functions in the package `pwr()`.


[sample_size_calculators]: http://powerandsamplesize.com/
