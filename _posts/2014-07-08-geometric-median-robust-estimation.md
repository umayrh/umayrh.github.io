---
layout: post
title: "Geometric Median & Robust Estimation"
date: "2014-07-08"
description: Fermat-Weber problem and mean estimation.
image: /assets/images/geometric_proof-g.png 
author: Umayr Hassan
tags:
- Robust Estimation
- Mathematics
---

Stanislav Minsker addresses the following question:

Problem: Given $X_1, \ldots, X_k$ that are i.i.d. random variables, how can we estimate the mean of the distribution so 
that the confidence interval still has a coverage probability $\ge 1 - O(e^{-t})$? Note: the smaller the confidence interval, 
the better the estimator.

1. Assuming the variables from normal distribution $N(\mu, \sigma^2)$. If the estimator is constructed 
as $ \hat{\mu}_n = \Sigma_i X_i / n $, then:

$$ Pr ( | \mu_n - \hat{\mu}_n | \ge \sigma \sqrt{2t/n} ) \le e^{-t} $$

For non-normal distribution, the confidence interval is much different:

$$ Pr ( | \mu_n - \hat{\mu}_n | \ge \sigma \sqrt{e^t/n} ) \le e^{-t} $$

Since the length of this interval depends on $e^t$, can we do better?

2. If we split the sample into $k = floor(t) + 1$ groups $G_1, \ldots, G_k$ each of size approximate $frac{n}{t}$, then 
median-of-means estimator has the following confidence interval (result due to Nemirovskii and Yudin, 1983):

$$ \hat{\mu}_{n,k} = median(\hat{\mu}_1, ..., \hat{\mu}_k) $$

$$ Pr ( \| \mu_n - \hat{\mu}_n \| \ge \sigma C \sqrt{\frac{t}{n}} ) \le e^{-t} $$

Where $C$ is a constant. The length of confidence interval for median-of-means is much smaller than for the naive mean estimator.

3. Minsker's work extends this to multi-dimensional distributions in Banach space using _geometric median_ to obtain 
confidence intervals of sub-exponential length with high coverage.

More in the paper [here](http://sminsker.files.wordpress.com/2013/11/geometric_median_minsker_2.pdf). See also, 
Fermat-Weber [problem](https://mgje.github.io/presentations/Budapest2014/#/13).

![geometric_proof](/assets/images/geometric_proof.png) 
