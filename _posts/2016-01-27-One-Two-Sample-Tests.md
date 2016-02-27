---
layout: post
title: "One and Two Sample Tests"
date: "January 27, 2016"
categories: ['statistics', 'experimental design']
---

* TOC
{:toc}




# One Sample Tests

## Z-Test and T-Test
Suppose our sample is randomly drawn from a normally distributed population. We want to test the population mean against a known standard (say $$\mu_0$$). So our null hypothesis is

$$H_0: \mu_1 = \mu_0$$

$$H_1: \mu_1 \ne \mu_0$$

There's two ways to do this. 

**Assume $$\sigma^2$$ Known**

We know that by the CLT $$\bar{X}$$ ~ $$N(\mu, \sigma^2)$$.  

We can calculate the test statistic 

$$Z = \frac{\bar{X} - \mu_0}{\sqrt{\sigma^2/n}}$$

where $$Z$$ ~ $$N(0, 1)$$ under the null hypothesis. We can compute the p-value to test $$H_0$$.

**Assume $$\sigma^2$$ Unknown**

Suppose the population variance is not known. Since we don't know $$\sigma^2$$, we can estimate it with the sample variance $$s^2$$. We can derive our test statistic from [probability theory][stat_theory_link]{:target = "_blank"}. 

Let $$Z$$ ~ $$N(0, 1)$$ and $$\frac{(n - 1)s^2}{\sigma^2}$$ ~ $$X^2_{n-1}$$

$$T = \frac{\frac{\bar{X} - \mu_0}{\sqrt{\sigma^2/n}}}{\sqrt{\frac{(n - 1)s^2}{\sigma^2}/(n-1)}} = \frac{\bar{X} - \mu_0}{\sqrt{s/n}}$$

where $$T$$ ~ $$t_{n - 1}$$ under the null hypothesis. We can compute the p-value to test $$H_0$$. 

## Nonparametric Test

### Wilcoxon Signed Rank Test
If we have non-normality or outliers, parametric tests are not ideal. Nonparametric tests are more robust to violoations of these assumptions. 

The **wilcoxon signed rank test** is a test that makes 2 assumptions. 

1. RV $$X$$ is continuous
2. The pdf of X is symmetric

We wish to test the median

$$H_0: m = m_0$$

against the potential alternative hypotheses

$$ H_1: m > m_0$$ or $$H_1: m < m_0$$ or $$H_1: m \ne m_0$$

We can perform this test as follows:

1. Calculate $$X_i - m_0$$
2. Calculate $$\vert X_i - m_0 \vert$$
3. Determine rank, $$R_i$$ of the absolute values in ascending order according to magnitude
4. Determine the value of $$W = \sum^n_i Z_i R_i$$ where $$Z_i = sign(X_i - m_0)$$
5. Determine if the observed $$W$$ is extreme under $$H_0$$

The distribution is available in software and in tables. We can also compute the exact distribution or look at the distribution of permutations (for $$>1$$ samples). For small sample sizes, the distribution of $$W$$ can be derived from the sample size. In large sample sizes, the value 

$$W' = \frac{\sum^n_{i = 1} Z_iR_i - \frac{n(n + 1)}{4}}{\sqrt{\frac{n(n + 1)(2n + 1)}{24}}}$$ 

is approximately distributed $$N(0, 1)$$

In R, we fit with `wilcox.test()`.

# Two Sample Tests
Suppose we want to test two population means where both populations are normally distributed. Let $$\mu_1$$ and $$\mu_2$$ represent population mean responses for $$trt.1$$ and $$trt.2$$. 

We want to test

$$H_0: \mu_1 = \mu_2$$

$$H_1: \mu_1 \ne \mu_2$$

It is crucial that data is analyzed according to how the experiment was designed otherwise our results may be lead to invalid results (inaccurately estimate variance).

T-tests can be fit in R with the `t.test()` function. 

## Unpaired T-Test
Suppose we randomly assign the treatments to a subject so that each subjects can receive either of the two treatments. 

We compute a test statistic

$$T = \frac{\bar{Y}_1 - \bar{Y}_2}{\sqrt{\hat{Var}(\hat{Y}_1 - \hat{Y}_2)}}$$

where $$\hat{Var}(\hat{Y}_1 - \hat{Y}_2)$$ depends on the estimated variances of the two treatment groups.

**Equal Variance**

We have independence between $$Y_1$$ and $$Y_2$$. If the variances are equal

$$\hat{Var}(\hat{Y}_1 - \hat{Y}_2) = s^2_p \left(\frac{1}{n_1} + \frac{1}{n_2} \right)$$
 
where

$$s^2_p = \frac{(n_1 - 1)s^2_1 + (n_2 - 1) s^2_2}{n_1 - 1 + n_2 - 1}$$

which is a weighted average of the sample variances.

The statistic $$T$$ ~ $$t_{n_1 + n_2 - 2}$$.

**Unequal Variance**

If the variances are not equal

$$\hat{Var}(\hat{Y}_1 - \hat{Y}_2) = \frac{s^2_x}{n_1} + \frac{s^2_y}{n_2}$$

The statistic $$T$$ ~ $$t_{r}$$ where

$$r = \frac{\left( \frac{s^2_x}{n_1} + \frac{s^2_y}{n_2} \right)^2}{\frac{(s^2_x/n_1)^2}{n_1 - 1} + \frac{(s^2_y/n_2)^2}{n_2 - 1}}$$

## Paired T-Test
Suppose that instead of randomly assigning treatments to each subject, we have some sort of pairing. (A "pair" can refer to one or two subjects). While pairs are independent, there is dependency or correlation within pairs. The more dependency there is within a pair, the more noise reduction.

For example, suppose we want to compare two shoe types. We could conduct a paired experiment where one subject receives $$trt.1$$ on their left foot and $$trt.2$$ on their right foot. This is preferred over an unpaired study because a person's activity can vary widely across the population and unpaired designs would introduce unecessary noise to the data. 

This is a more restrictive randomization scheme than for the unpaired experiment.

We compute the test statistic

$$T = \frac{\bar{D}}{\sqrt{\hat{Var}(\bar{D})}} = \frac{\bar{D}}{s^2_D / n}$$

where 

$$s^2_D = \sum (D_i - \bar{D})^2/(n - 1)$$ the sample variance of the differences and $$D$$ is the differences between the paired treatments.

The statistic $$T$$ ~ $$t_{n - 1}$$.

## Experimental Design vs Analysis
Suppose two scenarios where we assume equal variance

1. We design an unpaired experiment
2. We design a paired experiment

For both these scenarios, suppose we analyze the data using a paired analysis.

For the unpaired design, we know that "pairs" are indpendent (this is built in). So

$$Var(D_i) = Var(Y_1) + Var(Y_2) = 2\sigma^2$$ <br>
$$Var(\bar{D}) = \frac{2\sigma^2}{n}$$

For the paired design, we don't necessarily know that the "pairs" are independent. So

$$Var(D_i) = Var(Y_1) + Var(Y_2) - 2cov(Y_1, Y_2) = 2\sigma^2(1 - p)$$ <br>
$$Var(\bar{D}) = \frac{2\sigma^2(1 - p)}{n}$$

Thus this tells us that if the groups were positively correlated, the paired design will have smaller variance and greater power in a paired analysis. This difference may have lead to differing results when it comes to the p-value.

This stresses the importance of analyzing the data based off the experimental design.

## Nonparametric Tests
With nonparametric tests, we do not make the assumption of normality. In addition to permutations, we have several tests to compare two samples. 

### Mann Whitney Wilcoxon Test
For this test, we make the following assumptions

* Independent samples
* Continuous variable
* Equal variances
* Identical (but non-normal) distributions

We want to know whether one distribution is shifted relative to the other.  In other words, we wish to test

$$H_0: F_1(x) = F_2(x)$$

$$H_1: F_1(x) \ge F_2(x) or F_1(x) \le F_2(x)$$

The procedure for the **wilcoxon rank sum test** are: 

1. Combine the $$m+n$$ observations into one group and rank the observations from smallest to largest. Compute rank sum $$W$$ of treatment 1
2. Find all possible permutation of the ranks. For each permutation find $$W'$$
3. Determine the p-value

$$p.val = \frac{no. rank sums \le W_{obs}}{\binom{m + n}{n}}$$

Rather than doing a permutation test, one can compare to the exact distribution using tables. 

An equivalent test is called the **Mann-Whitney U test**. 

Let $$T_B = \sum^{n_b}_{i = 1} R_{iB}$$. 

Under $$H_0$$

* .$$E[T_B] = n_B \frac{n_A + n_B + 1}{2}$$
* .$$Var(T_B) = \frac{n_A n_B}{n_A + n_B - 1} \left( \frac{1}{n_A + n_B} \sum_{i, j} R^2_{ij} - \frac{(n_A + n_B + 1)^2}{4} \right)$$
* $$Var(T_B) = \frac{n_A n_B(n_A+n_B+1)}{12}$$ when there are no ties
* .$$U = T_B - E[T_B]$$

The statistic

$$\frac{(T_B - E[T_B])^2}{Var(T_B)}$$

is approximately distributed $$\chi^2_1$$. 

To conduct this test, we can use `wilcox.test()` in R. 

We can also define 

$$U =$$ # of pairs of $$(X_i, Y_j)$$ for which $$X_i < Y_j$$.

We can use this to computes a confidence interval based on the shift of the two distributions. This value is the difference between two means or medians. 

The difference in the two distributions can be written as

$$F_1(x) = F_2(x - \Delta)$$

The confidence interval is computed as interval where

$$pwd(k_a) < \Delta \le pwd(k_b)$$

where $$k$$ is the pair between group 1 and group 2 and
$$pwd$$ is a function denoting the pairwise distance between a point in group 1 and a point in group 2. 

We can derive the confidence interval from

$$P(k_a < U < k_b) = 1 - \alpha$$

where under large samples, $$U$$ is distributed $$N(0, 1)$$.

### Kologorov Smirnov Test
For this test, we make the following assumptions

* Independent samples
* Identical (but non-normal) distributions

Note that we do not assume equal variance. So we want to compare two identically distributed populations that have both different means and variances. 

We test

$$H_0:$$ the distributions are the same <br>
$$H_1:$$ the distributions are not the same

The Kolmogorov-Smirnov statistic is

$$KS = max_x \vert \hat{F}_1(x) - \hat{F}_2(x) \vert$$

This test is upper-tailed. The idea is to rank the combined data,  compute the CDF for each treatment, and then find the absolute difference. The maximum of those differences can be compared to permutations or known tables to obtain a p-value. 

In R, we can run this test using `ks.test()` or fit emprical cdfs with `ecdf()`.

### Wilcoxon Signed Ranks Test
This test can be used to analyze paired two-sample tests.

For this test, we make the following assumptions

* Differences are continuous
* Distribution of differences is symmetric
* Differences are mutually independent
* Differences all have the same median

To conduct the wilcoxon signed rank test for paired samples by computing the differences and running the procedure defined above. 

In R, we can run this test with `wilcox.test()`.