---
title: "Geometric Median & Robust Estimation"
date: "2014-07-08"
---

\[caption id="attachment\_1971" align="alignright" width="245"\]![geometric_proof](https://umayrh.files.wordpress.com/2014/07/geometric_proof.png) Fermat-Weber [problem](https://mgje.github.io/presentations/Budapest2014/#/13)\[/caption\]

Stanislav Minsker addresses the following question:

Problem: Given X1, ..., Xk that are i.i.d. random variables, how can we estimate the mean of the distribution so that the confidence interval still has a coverage probability ≥ 1 - O(e\-t)? Note: the smaller the confidence interval, the better the estimator.

1\. Assuming the variables from normal distribution N(μ, σ2). If the estimator is constructed as

\[latex\] \\hat{\\mu}\_n = \\Sigma\_i X\_i / n \[/latex\] \[latex\] Pr ( | \\mu\_n - \\hat{\\mu}\_n | \\ge \\sigma \\sqrt{2t/n} ) \\le e^{-t} \[/latex\]

For non-normal distribution, the confidence interval is much different:

\[latex\] Pr ( | \\mu\_n - \\hat{\\mu}\_n | \\ge \\sigma \\sqrt{e^t/n} ) \\le e^{-t} \[/latex\]

Since the length of this interval depends on et, can we do better?

2\. If we split the sample into k = floor(t) + 1 groups G1, ..., Gk each of size approximate n /t, then median-of-means estimator has the following confidence interval (result due to Nemirovskii and Yudin, 1983):

\[latex\] \\hat{\\mu}\_{n,k} = median(\\hat{\\mu}\_1, ..., \\hat{\\mu}\_k) \[/latex\] \[latex\] Pr ( | \\mu\_n - \\hat{\\mu}\_n | \\ge \\sigma C \\sqrt{t/n} ) \\le e^{-t} \[/latex\]

Where C is a constant. The length of confidence interval for median-of-means is much smaller than for the naive mean estimator.

3\. Minsker's work extends this to multi-dimensional distributions in Banach space using _geometric median_ to obtain confidence intervals of sub-exponential length with high coverage.

More in the paper [here](http://sminsker.files.wordpress.com/2013/11/geometric_median_minsker_2.pdf).
