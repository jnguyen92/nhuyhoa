---
layout: post
title: "Classification and Regression Trees and Ensembles"
date: "December 28, 2015"
categories: ['statistics', 'machine learning']
---

* TOC
{:toc}



![tree](http://jnguyen92.github.io/nhuyhoa/figure/images/tree.png)

# Decision Trees
Decision trees generate a tree using a top-down algorithm. The idea is to find a feature that "best" divides the data and then recur on each subset of the data that the feature divides. The goal is to predict the class labels, which is done at the leaves of the tree. 

## Algorithm

$$MakeSubtree$$(set of training instances D)

* C = $$Determine.Candidate.Splits$$(D)
* if $$Stopping.Criteria.Met$$()
  * make a leaf node N
  * determine class label/probabilities for N
* else
  * make an internal node N
  * S = $$Find.Best.Split$$(D, C)
  * for each outcome k of S
    * $$D_k$$ = subset of isntances that have outcome k
    * $$k^{th}$$ child of N = $$Make.Subtree$$($$D_k$$)
* return subtree rooted at N

## How to Find the Best Split

### Nominal vs Numeric Features
Candidate splits on nominal features is quite simple. Features can be grouped by all of their categories or some combination of those categories. 

Candidate splits on numeric features requires a bit more thought. The numeric feature can be converted into categories by cutting/binning. The cutpoints could be defined as the median numeric value or any other threshold (depends on the algorithm).

### Information Gain
Entropy (information) is defined as
$$H(Y) = - \Sigma_{y \in values(Y)} P(y)log_2(P(y))$$

The conditional entropy is defined as 
$$H(Y \vert X) = \Sigma_{x \in values(Y)} P(X = x)H(Y \vert X = x)$$

where
$$H(Y \vert X = x) = - \Sigma_{y \in values(Y)} P(Y = y \vert X = x) log_2( P(Y = y \vert X = x) ) $$

Information gain is then defined as
$$InfoGain(D, S) = H_D(Y) - H_D(Y \vert S)$$

The split $$S$$ that most reduces the conditional entropy of $$Y$$ for training set $$D$$ is chosen as the split.

This is the method used in the ID3 tree method. 

## Pruning
In order to prevent overfitting, trees can be pruned (removal of internal nodes).

Algorithm:

* Fit tree for training data, measure accuracy on tuning set
* Repeat until no progress on tuning set:
  * Consider all subtrees where 1 internal node is removed and replaced by a leaf
  * Remove the node that best improves tuning set accuracy

Another Algorithm:
Let 
$$ \Sigma^{\vert T \vert}_{m = 1} \Sigma_{i: x_i \in R_m} (y_i - \hat{y}_{R_m})^2 + \alpha \vert T \vert$$

where $$\alpha >= 0$$, $$\vert T \vert$$ is the number of terminal nodes in subtree, $$R_m$$ is the subset of predictor space corresponding to the $$m^{th}$$ terminal node, and $$\hat{y}_{R_m} is the mean of the training observations in $$R_m$$.

When $$\alpha = 0$$, we have the original tree. As $$\alpha$$ increases, the tree incurs a price for having too many terminal nodes which forces the tree to become smaller. The value $$\hat{\alpha}$$ can be found via cross-validation.

## Strengths & Weaknesses

Strengths:

* Great technique for learning models noisy models
* Results are easily interpretable
* Robust to outliers and missing data
* Fast, simple, and robust

Weaknesses:

* Only makes univariate splits; unable to consider interactions at a given node
* Greedy hill-climbing algorithm; early bad choice may doom the model
* Pruning may lead to a tree that removes a bad split early but its subtree has a good split later on
* Doesn't have the best prediction accuracy

# Regression Trees
In regression trees, the leaves of functions that predict numeric values rather than class labels. The functions may vary depending on the method. Some methods use constants whereas others use linear functions.

Rather than using information gain to score candidate splits, regression trees may use residual error, generated from a linear model. 

# Ensembles
Rather than fitting one tree, we grow a a set of trees using the training data. Then we generate predictions on each tree and combine the predictions. Ensemble methods tend to outperform simpler methods and work very well in practice. 

Predictions from models can be combined in a variety of ways

* Unweighted votes
* Weighted votes (determined by tuning set accuracy)
* Learning a combining function

Because these methods average across a set of models, the variance of the predictions are reduced. 

Unfortunately, the results are quite difficult to interpret.

## Bagging

Algorithm:

* Repeat B times
  * Choose with replacement $$N$$ examples from data set
  * Build tree to obtain the $$\hat{f}^{*b}(x)$$ prediction
  * Average all predictions (or take a majority vote for nominal responses)

If there is one very strong predictor, most of the bagged trees will use the predictor as the top split. Thus, the predictions from the trees will be highly correlated and the average predictions will have high variance. To decorrelate the predictions, we could use a method called random forests.

### Out of Bag Error
In each round of bootstrapping, about $$\frac{2}{3}$$ of the observations are sampled from the data to be used as the training set. That leaves about $$\frac{1}{3}$$ of the data that are not used to fit the data, which can be used as a testing set. So rather than using cross-validation, the out-of-bag (OOB) error can be utilized to select model parameters. 

## Random Forests

Algorithm:

Let $$N$$ = n-size, $$F$$ = # of parameters, $$i << F$$.

* Repeat k times
  * Choose with replacement $$N$$ examples from data set
  * Build tree, but in each recursive call
    * Choose (w/o replacement) $$i$$ features
    * Choose best of $$i$$ features as root of subtree
  * Do not prune
* Average all predictions (or take a majority vote for nominal responses)

The tuning parameter $$i$$ can be chosen by cross-validation. Typically we choose $$i = \sqrt{F}$$. Increasing $$i$$ may (bias-variance tradeoff)

* increase correlation among individual trees in the forest (bad)
* increase the accuracy of individual trees (good) 

Because it samples from all possible features, random forests can handle a large number of features. It can also reduce overfitting drastically.

### Variable Importance
Interpreting random forests can be quite difficult. One way to get a sense of the variables is to look at important variables (features).

Procedure:

* Use OOB samples to predict values
* Randomly permute values of one of the features and predict the values again
* Measure decrease in accuracy

Alternate Procedure:

* Measure the split criterion improvement
* Record improvements for each feature
* Accumulate over the whole ensemble

## Boosting

Algorithm:

* Set $$\hat{f}(x) = 0$$ and $$r_i = y_i \forall i$$ in the training set
* Repeat B times
  * Fit a tree with $$\hat{f}^b$$ with $$d$$ splits (d + 1 terminal nodes) to the training data (X, r)
  * Update $$\hat{f}$$ by adding in a shrunken version of the new tree
$$\hat{f}(x) \leftarrow \hat{f}(x) + \lambda \hat{f}^b(x)$$
  * Update residuals
$$ r_i \leftarrow r_i - \lambda \hat{f}^b(x_i)$$
* Output boosted model
$$\hat{f}(x) = \Sigma^B_{b = 1} \lambda \hat{f}^b(x)$$

The interaction depth $$d$$ is also chosen via cross validation. The tuning parameter $$\lambda >= 0$$ allows more trees to fit the residuals. This value is generally small (0.01 or 0.001). 

The idea with boosting is to learn slowing. The algorithm reweights examples (if wrong, increase weight; else decrease weight). Decision trees are fitted to the residuals of the model and then added to the model to update the residuals. This allows the model to improve in the areas that it doesn't perform well. 

An alternative boosting method (Adaboost) updates weights assigned to data points. 

# In R
rpart, prune
randomForest
gbm
adaboost
http://www.statmethods.net/advstats/cart.html


Decision tree alternative in R:

- fit tree
library(rpart)
tree = rpart(formula = , data = , method = “class” or etc, control = rpart.control())
predict(model, dataset, method)

- plot tree
plot(tree)
text(tree)

- fancy plotting
library(rattle)
library(rpart.plot)
library(RColorBrewer)
fancyRpartPlot(tree)



