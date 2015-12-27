---
layout: post
title: "Bootstrapping"
date: "December 28, 2015"
categories: ['statistics', 'machine learning']
---

* TOC
{:toc}




The bootstrap is a powerful statistical tool that can be used to quantify the uncertainty or variance of a given estimator. By doing this, we can easily obtain estimates of standard error or confidence intervals.

# Bootstrapping
Rather than obtaining new data sets we can sample from our original data set. This process allows us to obtain new data sets without generating additional samples. From this, we can obtain an estimate of the variability of our point estimate. 

1. Obtain $$N$$ samples with replacement from the original data set
2. Compute the point estimate
3. Repeat step 1-2 $$B$$ (some large quantity) times
4. Compute the variance of the point estimates 

$$Var(\hat{x}) = \frac{1}{B - 1} \Sigma^B_{r = 1} (\hat{x} - \bar{\hat{x}})^2$$

Note that in order for this process to be valid, all observations must be independent and identically distributed. 

Since we are sampling with replacement, each bootstrap sample has significant overlap with the original data. About $$\frac{2}{3}$$ of the original data points appear in each bootstrapped sample. 

$$ lim_{N \rightarrow \infty} 1 - (1 - \frac{1}{N})^N = .63 $$

where $$(1 - \frac{1}{N})^N$$ is the probability that an observation is not chosen out of any of the $$N$$ draws. Thus $$1 - (1 - \frac{1}{N})^N$$ is the probability that an observation is chosen to be a part of the bootstrap sample. 

# In R
The bootstrapping procedure can be parallelized easily. In R, one can use the function `boot::boot()` to implement bootstrapping.