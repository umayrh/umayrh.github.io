---
title: "Estimating uniques - sampling with replacement"
date: "2013-10-26"
---

While exploring ways to estimate multiset union and intersection cardinalities, I discovered an interesting result mentioned in a paper by Shukla \[1\], who in turn cites Feller \[2\]: If r elements are chosen uniformly and at random from a set of n elements, the expected number of distinct elements obtained is

$latex n - n (1 - 1/n)^r$.

Given an element _a_ from the set, the probability that there are none of the elements after r draws is _a_ is $latex (1 - 1/n)^r$. Thus the probability that there is at least one element is _a_ (or equivalently that there is at least one unique item) is $latex 1 - (1 - 1/n)^r$, and hence the expected number of uniques is $latex n - n (1 - 1/n)^r$. Alternatively, as Feller explains, the probability of a single unique at the ith step is $latex ((1 - 1/n)/n)^{i-1}$, hence the expected number of uniques after r draws is

 $latex \\sum\_{i=1}^{r} ((1 - 1/n)/n)^{i-1}$

A geometric series that converges to $latex n - n (1 - 1/n)^r$.

This is an instance of the [Birthday problem](https://en.wikipedia.org/wiki/Birthday_problem). [Stackexchange](http://math.stackexchange.com/questions/72223/finding-expected-number-of-distinct-values-selected-from-a-set-of-integers) has a couple of other more complicated but interesting solutions.

\[1\] A. Shukla et al, Storage estimation for multidimensional aggregates in the presence of hierarchies \[[paper](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.45.7924&rep=rep1&type=pdf)\] \[2\] W. Feller, An introduction to probability theory and its applications, vol. 1, 1957
