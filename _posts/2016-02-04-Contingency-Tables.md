---
layout: post
title: "Contingency Tables"
date: "February 4, 2016"
categories: Statistics
tags: Experimental_Design
---

* TOC
{:toc}



# Goodness of Fit Tests

## Pearson Chi-Square and Deviance
Let $$Y_1 \sim Bin(n, p_1)$$. Then $$E[Y_1] = np_1$$ and $$Var(Y_1) = np_1(1 - p_1)$$. For large samples ($$np_1 \ge 5$$ and $$n(1 - p_1) \ge 5$$), the CLT yields the normal distribution approxiation to the binomial distribution. 

$$Z = \frac{Y_1 - np_1}{\sqrt{np_1(1 - p_1)}}$$

where $$Z \sim N(0, 1)$$. 

Squaring $$Z$$ gets

$$Z^2 = X^2 = \frac{(Y_1 - np_1)^2}{np_1(1 - p_1)}$$ 

where $$X^2 \sim \chi^2_1$$. 

Rearrange to get

--------|------------------
$$X^2$$ | $$= \frac{(Y_1 - np_1)^2}{np_1(1 - p_1)} * ((1 - p_1) + p_1)$$
        | $$= \frac{(Y_1 - np_1)^2 (1 - p_1)}{np_1(1 - p_1)} + \frac{(Y_1 - np_1)^2 p_1}{np_1(1 - p_1)}$$
        | $$ = \frac{(Y_1 - np_1)^2}{np_1} + \frac{(Y_1 - np_1)^2}{n(1 - p_1)}$$
        | $$= \frac{(Y_1 - np_1)^2}{np_1} + \frac{(n - Y_2 - n(1 - p_2))^2}{np_2}$$
        | $$= \frac{(Y_1 - np_1)^2}{np_1} + \frac{(-(Y_2 - np_2))^2}{np_2}$$
        | $$= \frac{(Y_1 - np_1)^2}{np_1} + \frac{((Y_2 - np_2))^2}{np_2}$$
        | $$= \sum^2_{i = 1} \frac{(Y_i - np_i)^2}{np_i}$$
        | $$= \sum^2_{i = 1} \frac{(Obs - Exp)^2}{Exp}$$

Resulting in the $$X^2$$ goodness of fit statistic. 

Consider the following table

Categories   | 1       | 2       | ... | |k - 1         | |k
-------------|---------|---------|-----|-|--------------|-|------------
**Observed** | $$Y_1$$ | $$Y_2$$ | ... | |$$Y_{k - 1}$$ | |$$n-Y_1-Y_2-...-Y_{k - 1}$$
**Expected** | $$np_1$$| $$np_2$$| ... | |$$np_{k - 1}$$| |$$1 - np_k$$


The chi-square statistic for a $$k$$ category table is

$$X^2 = \sum^k_{i = 1} \frac{Y_i - np_i}{np_i}$$

where $$X^2 \sim \chi^2_{k - 1}$$.

The deviance statistic is derived from the likelihood ratio test

$$G^2 = 2 \sum^k_{j = 1} X_j \log \left( \frac{X_j}{n \hat{p_j}} \right) = 2 \sum_j O_j \log \left( \frac{O_j}{E_j} \right)$$

For large sample sizes, $$X^2$$ and $$G^2$$ are equivalent. 

## Application
It is important to note that

* Require CLT so that the expected number of each category is $$\ge 5$$; if needed, may collapse cells
* The degrees of freedom of $$X^2$$ depends on the number of independent counts
* Reject the $$H_0$$ if the observed counts are very different from the expected counts

How are the probabilities obtained? They may be given from an external source. 

In other cases, the test may be comparing the data set to a specific probability distribution. Generally these probability distributions have parameters that are unspecified (Poisson, Binomial, etc). In this case, to conduct chi-square goodness of fit tests, 

1. Estimate the parameters using the maximum likelihood method
2. Calculate the chi-square statistic using the obtained estimates
3. Compare the chi-square statistic to a $$X^2_{k - 1 - d}$$ distribution where $$d = $$ number of parameters estimated

**Example:**

Is the data below is consistent with a Poisson model?

**no. of calls** | 0 | 1 | 2 | 3 | 4 | 5
-----------------|---|---|---|---|-------
**count**        | 19| 26| 29| 13| 10| 3

$$\hat{\lambda} = \frac{19(0) + 26(1) + 29(2) + 13(3) + 10(4) + 3(5)}{100} = 1.78$$

The Poisson pmf is $$P(X = x) = \frac{\lambda^x e^{-\lambda}}{x!}$$, thus $$E[Y_i] = 100 * P(X = x)$$. Collapse the last two counts to obtain values greater than $$5$$. 

**no. of calls** | 0 | 1 | 2 | 3 | 4 | 5
-----------------|---|---|---|---|-------
**count**        | 19| 26| 29| 13| 10| 3
**expect**       | 16.86 | 30.02 | 26.72 | 15.85 | 10.55

Thus $$X^2 = 2.08$$ and $$G^2 = 2.09$$. This multinomial model has $$k - 1$$ parameters and estimates $$1$$ parameter $$\lambda$$. Thus the degrees of freedom is $$k - 1 - 1 = 3$$

Compare these statistics to $$\chi^2_3$$ and obtain a p-value of $$0.56$$. Thus the conclusion is that the Poisson model fits well.

## Residuals
When results show that observed counts don't match expected counts, additional analyses on the residuals may be needed to assess what cells diverged from the expected values.

The residuals are the square root of the components that sum up to the $$X^2$$ and the $$G^2$$ statistics. (For example, in $$X^2$$ it is $$r_j = \frac{O_j - E_j}{\sqrt{E_j}}$$). 

If the model is true, $$E[X^2] = E[G^2] = df$$ and the typical size of a single $$r_j = df / k$$. So if $$\vert r_j \vert >> \sqrt{df / k}$$, the model does not appear to fit well for cell $$j$$. 


# I x J Contingency Tables

The examples below use the $$X^2$$ test statistic. The deviance statistic is generated similar to the formula defined above.

## Homogeneity
The test for homogeneity tests whether two or more multinomial distributions are equal. That is, the row totals are held fixed. 

$$H_0: p_{11} = p_{21} and p_{12} = p_{22} ... p_{1k} = p_{2k}$$

$$H_1: not H_0$$

Category | c1                           | | ... | | cj                           | | Fixed Total
---------|------------------------------|-|-----|-|-----------------------------|-|------------
**Pop1**     | $$y_{11}$$ ($$\hat{p}_{11}$$)| |...  | | $$y_{1k}$$ ($$\hat{p}_{1J}$$)| |$$n_1 = \sum^k_j y_{1j}$$
**Pop2**     | $$y_{21}$$ ($$\hat{p}_{21}$$)| |...  | | $$y_{2k}$$ ($$\hat{p}_{2J}$$)| |$$n_2 = \sum^k_j y_{2j}$$
**Total**    | $$y_{11} + y_{21}$$ ($$\hat{p}_{1}$$) | |... | | $$y_{1k} + y_{2k}$$ ($$\hat{p}_{k}$$) | |$$n_1 + n_2$$

Each cell denotes the number falling into the $$j^{th}$$ category of the $$i^{th}$$ sample. Let the estimate $$\hat{p}_j$$ is the pooled estimate for each column (under $$H_0$$, $$p_{xi} = p_{yi}$$). Under the null hypothesis,

$$E[Y_{ij}] = n_i \hat{p}_j$$

The test statistic is

$$X^2 = \sum^I_{i = 1} \sum^J_{j = 1} \frac{(y_{ij} - n_i \hat{p}_j)^2}{n_i \hat{p}_j}$$

The model has $$I(J - 1)$$ df (each row is multinomial so $$n_i$$ is fixed) and estimates $$(J - 1)$$ parameters ($$\hat{p}_j$$). So 

$$df  = IJ - I - J + 1 = (I - 1)(J - 1)$$

So the statistic $$X^2 \sim \chi^2_{(I - 1)(J - 1)}$$

## Independence

Consider that the total sample size is fixed (but not the marginal totals). Each cell is a Poisson random variable with its own rate. Each subject is cross-classified into one and only one of the mutually exclusive and exhaustive $$A_i \cap B_i$$. 

$$H_0: A \perp B; P(AB) = P(A)P(B)$$

$$H_1: A not \perp B$$

.       | |$$B_1$$                 | | ... | |$$B_J$$                 | **Total**
--------|-|-----------------------|-|-----|-|------------------------|-------
$$A_1$$ | |$$Y_{11}$$ ($$p_{11}$$) | | ... | |$$Y_{1J}$$ ($$p_{1J}$$) | ($$p_{1.}$$)
...     | |                        | | ... | |                        |
$$A_I$$ | |$$Y_{I1}$$ ($$p_{I1}$$) | | ... | |$$Y_{IJ}$$ ($$p_{IJ}$$) | ($$p_{I.}$$)
**Total**| | ($$p_{.1}$$)           | | ... | | ($$p_{.J}$$)           | $$n$$

Each cell $$Y_{ij}$$ denotes the frequency of $$A_i \cap B_j$$. Under the null hypothesis, 

$$E[Y_{ij}] = P(A_i)P(B_j)n = \frac{y_{i.}}{n} * \frac{y_{.j}}{n} * n = \frac{y_{i.} y_{.j}}{n} $$

The test statistic is

$$X^2 = \sum^I_{i = 1} \sum^J_{j = 1} \frac{(y_{ij} - \frac{y_{i.} y_{.j}}{n})^2}{\frac{y_{i.} y_{.j}}{n}}$$

The model has $$(IJ - 1)$$ df (only grand total $$n$$ is fixed) and estimates $$(I + J - 2)$$ parameters (marginal probabilities). So 

$$df  = IJ - 1 + - I - J + 2 = IJ - I - J + 1 = (I - 1)(J - 1)$$

So the statistic $$X^2 \sim \chi^2_{(I - 1)(J - 1)}$$

## Sensitivity Analysis
A test statistic or p-value is a measure of the evidence against $$H_0$$ that depends on the effect size. An effect size should not change if $$n$$ is arbitrarily increased. 
 
Measures of association should not change if cell counts $$n_{ij}$$ are replaced by $$kn_{ij}$$ for some constant factor $$k$$. Sensitivity analsysis should be done to check that this is the case in analyses. 

## Fischer's Exact Test
Hypergeometric sampling is when the row totals and column totals are fixed by design. These types of tables can be assessed with exact tests. Exact tests are also useful when sample sizes are small and asymptotic approximations are violated. 

Consider the following table

.     | **draw** | **no draw** | .
**a** | $$k$$    | $$D - k$$   | $$D$$
**b** | $$n - k$$| $$N+k-n-D$$ | $$N - D$$
      | $$n$$    | $$N - n$$   | $$N$$

Using the hypergeometric distribution, 

$$P(k, N, D, n) = \frac{\binom{D}{k} \binom{N - D}{n - k}}{\binom{N}{n}}$$

where $$k = 1, ..., n$$

Using this definition, the exact distribution of $$k$$ can be mapped out, compared to the observed $$k$$ and to obtain a p-value.

Extensions to $$I x J$$ tables are implemented in statistical software.

# Point Estimators

## Difference in Proportions
Consider the test

$$H_0: p_1 = p_2$$

$$H_1: p_1 \ne p_2$$

The difference in proportions is defined as

$$\delta = p_1 - p_2$$

This value can be estimated with

$$\hat{\delta} = \frac{n_{11}}{n_{1+}} + \frac{n_{21}}{n_{2+}} = \hat{p}_1 - \hat{p}_2$$

The variance of this estimate is

$$Var(\delta) = Var(p_1 - p_2) = Var(p_1) + Var(p_2) = \frac{p_1(1 - p_1)}{n_{1+}} + \frac{p_2(1 - p_2)}{n_{2+}}$$

The values $$p_1$$ and $$p_2$$ can be estimated with $$\hat{p}_1$$ and $$\hat{p}_2$$ from the data. This variance estimate is generally used in confidence intervals. Use the normal approximation to the bionomial distribution.

For hypothesis testing under $$H_0: p_1 = p_2$$, obtain a more efficient estimate of $$Var(\hat{\delta})$$ using the pooled proportion $$\hat{p} = \frac{n_{11} + n_{21}}{n_{1+} + n_{2+}}$$. 

$$Var(\hat{\delta}) = \hat{p}(1 - \hat{p}) \sqrt{\frac{1}{n_1} + \frac{1}{n_2}}$$

The statistic $$Z = \frac{\hat{\delta}}{\sqrt{Var(\hat{\delta})}} \sim N(0, 1)$$.

## Relative Risk
The relative risk is defined as 

$$ \rho = \frac{P(Z = 1 \vert Y = 1)}{P(Z = 1 \vert Y = 2)}$$

This value can be estimated with 

$$\hat{\rho} = \frac{n_{11} / n_{1+}}{n_{21} / n_{2+}}$$

Since this value is non-negative, take the $$\log$$ of $$\rho$$ to obtain a more normal distribution. 

The approximate variance of $$\rho$$ (using the delta method) is

--------------------|------------------------------
$$Var(\log(\rho))$$ | $$= Var(\log(p_1) - \log(p_2))$$
                    | $$ = Var(\log(p_1)) + Var(\log(p_2))$$
                    | $$ = \frac{1}{p_1^2} \frac{p_1 (1 - p_1)}{n_{1+}} + \frac{1}{p_2^2} \frac{p_2 (1 - p_2)}{n_{2+}} $$
                    | $$ = \frac{(1 - p_1)}{p_1 n_{1+}} + \frac{(1 - p_2)}{p_2 n_{2+}} $$

Thus

$$Var(\log(\hat{\rho})) = \frac{(1 - n_{11} / n_{1+})}{n_{1+} n_{11} / n_{1+}} + \frac{(1 - n_{21} / n_{2+})}{n_{2+} n_{21} / n_{2+}} = \frac{1}{n_{11}} - \frac{1}{n_{1+}} + \frac{1}{n_{21}} - \frac{1}{n_{2+}}$$

The $$(1 - \alpha)100$$% confidence interval is

$$\exp \left( log(\hat{\rho}) \pm z_{\alpha/2} \sqrt{Var(\log(\hat{\rho}))} \right)$$

(Since this is the confidence interval is the odds ratio, reject if $$1$$ is not in the interval).

## Odds Ratios
The odds is a ratio of success to failure. 

* If $$odds = 1$$, there is an equal chance of success and failure
* If $$odds > 1$$, success is more likely than failure
* If $$odds < 1$$, success is less likely than failure

The odds ratio is defined as 

$$OR = \frac{P(Z = 1 \vert Y = 1)/P(Z = 2 \vert Y = 1)}{P(Z = 1 \vert Y = 2)/P(Z = 2 \vert Y = 2)}$$

This value can be estimated with 

$$\hat{OR} = \frac{n_{11}n_{22}}{n_{12}n_{21}}$$

To estimate the variance, take the $$\log$$ and use the delta method

-----------------------|---------------------------------
$$Var(log(\hat{OR}))$$ | $$ = Var(\log(n_{11}) + \log(n_{22}) - \log(n_{12}) - \log(n_{21}))$$
                       | $$ = Var(\log(n_{11})) + Var(\log(n_{22})) - Var(\log(n_{12})) - Var(\log(n_{21}))$$
                       | $$ = \frac{1}{n_{11}} + \frac{1}{n_{22}} + \frac{1}{n_{12}} + \frac{1}{n_{21}}$$

Thus 

$$Var(log(\hat{OR})) = \frac{1}{n_{11}} + \frac{1}{n_{22}} + \frac{1}{n_{12}} + \frac{1}{n_{21}}$$ 

The $$(1 - \alpha)100$$% confidence interval is

$$\exp \left( log(\hat{OR}) \pm z_{\alpha/2} \sqrt{Var(\log(\hat{OR}))} \right)$$

(Since this is the confidence interval is the odds ratio, reject if $$1$$ is not in the interval).

# Ordinal Categories
In some cases, the tables may contain ordinal categories. If this is the case, the objective is to determine whether there exists a linear trend or correlation among the levels of the characteristics. 

The Mantel-Haenszel statistic ($$M^2$$) applies to both Pearson and Spearman correlation. 

$$H_0: \rho = 0$$

$$H_1: \rho \ne 0$$

The Mantel-Haenszel statistic is

$$ M^2 = (n - 1)r^2$$

where $$M^2 \sim X^2_1$$. 

* If the two variables are independent, $$\rho = 0, M^2 = 0$$
* If the two variables are perfectly associated, $$\rho = 1, M^2 = (n - 1)$$

In order to compute $$r$$, assign scores or numerical values to the categorical rows and columns. There are several options 

* Equal spacing integers
* Midranks
* Midpoints
* Domain knowledge

Once these scores are assigned, the relationships can be deaggregated and the correlation $$r$$ can be computed. 

# Dependence
Matched pairs are two samples that are statistically dependent. The objective is to determine whether the margins of the table are the same.

Suppose people are surveyed at two time points and the objective is to measure whether their categorization stays the same over time.

$$H_0: p_{12} = p_{21}$$

Use McNemar's test to test for marginal homogeneity and symmetry. 

Suppose that $$n_{12} + n_{21}$$ is fixed. Under the null hypothesis, the frequency of counts in $$n_{12}$$ should be the same as in $$n_{21}$$. Thus $$n_{12} \sim Bin(n_{12} + n_{21}, 0.5)$$. 

Assuming that $$n_{12} + n_{21}$$ is large, the test statistic is

$$Z = \frac{\frac{n_{12}}{n_{12} + n_{21}} - 0.5 }{\sqrt{\frac{0.5(1 - 0.5)}{n_{12} + n_{21}}}} = \frac{n_{12} - n_{21}}{\sqrt{n_{12} + n_{21}}}$$

where $$Z \sim N(0, 1)$$ if cell counts are large ($$\ge 5$$) or distributed $$Bin(n_{12} + n_{21}, 0.5)$$.

Square the above value to get

$$X^2 = \frac{(n_{12} - n_{21})^2}{\sqrt{n_{12} + n_{21}}}$$

where $$X^2 \sim \chi^2_1$$. This test is valid when $$n$$ is fixed (but not $$n_{12} + n_{21}$$). 

Obtain confidence intervals on the difference between the marginal proportions

$$d = p_{1+} - p_{+1} = p_{12} - p_{21}$$

which is estimated with

$$\hat{d} = \frac{n_{12}}{n} + \frac{n_{21}}{n}$$

Calculate

-----------------|-----------------
$$Var(\hat{d})$$ | $$ = n^{-2} Var(n_{12} - n_{21})$$
                 | $$ = n^{-2} \left( Var(n_{12}) + Var(n_{21}) - 2Cov(n_{12}, n_{21}) \right)$$
                 | $$ = n^{-2} \left( p_{12}(1 - p_{12}) + p_{21} (1 - p_{21}) + 2 p_{12} p_{21} \right)$$

Estimate the variance as

$$ \hat{Var}(\hat{d}) = \frac{1}{n} \left( \frac{n_{12}}{n}(1 - \frac{n_{12}}{n}) + \frac{n_{21}}{n}(1 - \frac{n_{21}}{n}) + 2 \frac{n_{21}n_{12}}{n^2} \right)$$

The $$(1 - \alpha)100$$% confidence interval is 

$$\hat{d} \pm z_{\alpha/2} \sqrt{\hat{Var}(\hat{d})}$$

# Three-Way Contingency Tables

With $$k$$-way tables, there is a different sampling scheme. It can be thought of as stratified sampling where there are fixed sample size for each partial table.

For modeling independence and association in $$3$$-way tables, consider several types of relationships

* Mutual independence: all variables are independent $$A \perp B \perp C$$ or $$(A, B, C)$$
* Joint independence: two variables are jointly independent given the third $$AB \perp C$$ or $$(AB, C)$$
* Marginal independence: two variables are indpendent while ignoring the third $$A \perp B$$ or $$(A, B)$$
* Conditional independence: two variables independent given the third $$A \vert C \perp B \vert C$$ or $$(AC, BC)$$
* Homogeneous association: conditional odds-ratios do not depend on the value of the third variable $$AB \perp AC \perp BC$$ or $$(AB, AC, BC)$$

Consider the following relationships

* Mutual independence $$\Rightarrow$$ joint independence
* Joint independence $$\Rightarrow$$ marginal indpependence

## Mutual Independence
Under the model of mutual independence

$$P(A = i, B = j, C = k) = P(A = i)P(B = j)P(C = k)$$

Estimate the expected values

$$E[Y_{ijk}] = np_{i++}p_{+j+}p_{++k} = \frac{n_{i++}n_{+j+}n_{++k}}{n^2}$$

To calculate the degrees of freedom, 

$$df = (IJK - 1) - [(I - 1)+(J - 1)+(K - 1)]$$

The test statistic

$$X^2 = \sum_i \sum_j \sum_k \frac{(n_{ijk} - E_{ijk})^2}{E_{ijk}}$$

where $$X^2 \sim \chi^2_{IJK - I - J - K + 2}$$.

## Joint Independence
Under the model of joint independence

$$P(A = i, B = j, C = k) = P(A = i, B = j) P(C = k)$$

Note that if mutual independence applies, then all three variants of joint independence also applies. 

Estimate the expected values

$$E[Y_{ijk}] = n p_{ij+} p_{++k} = \frac{n_{ij+} n_{++k}}{n}$$

To calculate the degrees of freedom, 

$$df = (IJK - 1) - [(IJ - 1)+(K - 1)] = (IJ - 1)(K - 1)$$

If $$AB$$ is combined into one variable, it turns into a $$2$$-way analysis on $$AB$$ and $$C$$. 

## Marginal Independence
Under the model of marginal independence

$$P(A = i, C = k) = P(A = i) P(C = k)$$

where $$B$$ variableis ignored.

By collapsing over the levels of $$B$$, it turns into a $$2$$-way analysis on $$A$$ and $$C$$. 

## Conditional Independence
Under the model of conditional independence

$$P(A = i, B = j \vert C = k) = P(A = i \vert C = k) P(B = j \vert C = k)$$

that is for each value of $$C$$, $$A \perp B$$.

There are two ways to test this hypothesis

* Sum up $$X^2$$ or $$G^2$$ statistics from the levels of $$A$$ (sum of $$\chi^2$$ is $$\chi^2$$); the total $$df = K(I - 1)(J - 1)$$
* Cochran-Mantel-Haenszel test

The Cochran-Mantel-Haenszel test for $$2x2xK$$ tables has

$$H_0: \theta_{AB(1)} = ... = \theta_{AB(K)} = 1$$

The test statistic is

$$M^2 = \frac{[\sum_k (n_{11k} - \mu_{11k})]^2}{\sum_k Var(n_{11k})}$$

where $$\mu_{11k} = E(n_{11}) = \frac{n_{1+k}n_{+1k}}{n_{++k}}$$ and $$Var(n_{11k}) = \frac{n_{1+k} n_{2+k} n_{+1k} n_{+2k}}{n_{++k}^2(n_{++k} - 1)}$$.

The statistic $$M^2 \sim \chi^2_1$$.

Obtain an estimate of the common odds ratio

$$\hat{\theta}_{MH} = \frac{\sum_k (n_{11k}n_{22k}) / n_{++k}}{\sum_k (n_{12k}n_{21k}) / n_{++k}}$$

The test works well if the conditional odds ratios are in the same direction and are comparable in size. 

The CMH can also be generalized to $$IxJxK$$ tables if needed. 

**Homogeneous Association**

Homogeneous association implies that the conditional relationship between $$AB$$ given $$C$$ is the same for each level of $$C$$. 

The homogeneous association model requires that the $$AB$$ odds ratios at each level of $$C$$ be identical (but not necessarily equal to $$1$$). 

$$H_0: \theta_{AB(1)} = ... = \theta_{AB(k)}$$

The Breslow-Day statistic is 

$$X^2 = \sum_i \sum_j \sum_k \frac{(O_{ijk} - E_{ijk}) ^2}{E_{ijk}}$$

where $$X^2 \sim \chi^2_{K - 1}$$. 

The Breslow-Day statistic does not work well with small sample sizes nor can it be applied to $$IxJxK$$ tables. 

# Log Linear Models
Recall that for testing independence in tables, each cell is distributed Poisson. 

Log linear models can be fit in R with the `glm()` function setting `family = poisson`.

## Two Way Independence
Consider the model of independence for two-way tables. Recall

$$E[Y_{ij}] = \mu_{ij} = np_{ij} = n p_{i+} p_{+j}$$

By taking $$\log$$s

$$\log(\mu_{ij}) = \log(n) + \log(p_{i+}) + \log(p_{+j})$$

$$\log(\mu_{ij}) = \lambda + \lambda^A_i + \lambda^B_j$$

where $$A$$ and $$B$$ denote the two categorical variables. 

With the following notations

* $$\lambda$$ represents the overall effect
* $$\lambda^A_i$$ represents the main effect of variable $$A$$ or classification in row $$i$$
* $$\lambda^B_j$$ represents the main effect of variable $$B$$ or classification in row $$j$$
* $$\lambda^A_I = \lambda^B_J = 0$$ due to categorical variable coding
* $$\lambda^{AB}_{ij}$$ represents the interaction effect of $$AB$$. Note that in this model $$\lambda^{AB}_{ij} = 0$$ implying independence

This test is essentially the same as the one above. The test compares independence to dependence. 

$$H_0: \mu_{ij} = \lambda + \lambda^A_i + \lambda^B_j$$

$$H_1: \mu_{ij} = \lambda + \lambda^A_i + \lambda^B_j + \lambda^{AB}_{ij}$$

**Relationship to Odds and Odds Ratio**

---------------|--------------
$$\log(odds)$$ | $$ = \log \left( \frac{\mu_{i1}}{\mu_{i2}} \right)$$
               | $$ = \log(\mu_{i1}) - \log(\mu_{i2})$$
               | $$ = (\lambda + \lambda^A_i + \lambda^B_1) - (\lambda + \lambda^A_i + \lambda^B_2)$$
               | $$ = \lambda^B_1 - \lambda^B_2$$

$$odds = exp(\lambda^B_1 - \lambda^B_2)$$

---------------------|--------------------------------
$$\log(odds.ratio)$$ | $$ = \log \left( \frac{\mu_{11} \mu_{22}}{\mu_{12} \mu_{21}} \right)$$
                     | $$ = \log(\mu_{11}) + \log(\mu_{22}) - \log(\mu_{12}) - \log(\mu_{21})$$
                     | $$ = (\lambda + \lambda^A_1 + \lambda^B_1) + (\lambda + \lambda^A_2 + \lambda^B_2) - (\lambda + \lambda^A_1 + \lambda^B_2) - (\lambda + \lambda^A_2 + \lambda^B_1)$$
                     | $$ = 0$$
                     
So given that $$H_0$$ is true, $$odds.ratio = 1$$.

If $$H_1$$ is true then $$odds.ratio = exp(\lambda^{AB}_{11} + \lambda^{AB}_{22} - \lambda^{AB}_{12} - \lambda^{AB}_{21})$$

## Three Way Tables

Recall the variants of independence in the $$3$$-way tables. These can be summarised into log linear models.

Type | Model
-------------------------|---------------------
Mutual Independence      | $$\mu_{ij} = \lambda + \lambda^A_i + \lambda^B_j + \lambda^C_k$$
Joint Independence       | $$\mu_{ij} = \lambda + \lambda^A_i + \lambda^B_j + \lambda^C_k + \lambda^{AB}_{ij}$$
Condiitonal Independence | $$\mu_{ij} = \lambda + \lambda^A_i + \lambda^B_j + \lambda^C_k + \lambda^{AC}_{ik} + \lambda^{BC}_{jk}$$
Homogeneous Association  | $$\mu_{ij} = \lambda + \lambda^A_i + \lambda^B_j + \lambda^C_k + \lambda^{AB}_{ij} + \lambda^{AC}_{ik} + \lambda^{BC}_{jk}$$
Complete Dependence      | $$\mu_{ij} = \lambda + \lambda^A_i + \lambda^B_j + \lambda^C_k + \lambda^{AB}_{ij} + \lambda^{AC}_{ik} + \lambda^{BC}_{jk} + \lambda^{ABC}_{ijk}$$

The same model testing and diagnostic procedures of GLMs can be applied to log linear models.

