---
layout: post
title: "Note on minimax optimization"
date: "2012-05-23"
description:
image: 
author: Umayr Hassan
tags:
---

At [Adap.tv](http://www.adap.tv "Adap.tv"), one of my favorite projects is an ad delivery optimization feature that we 
internally call the "bid optimizer." It allows an advertiser to achieve her impression, average CPM (cost per thousand 
impressions) and performance (click-through rate, completion rate and/or premium impression distribution) goals for a 
given ad. The ad bids in our second-price auction market-place to try and win impressions on selected sites.

In general, the bid optimizer's objective is to minimize the $ per impression that the advertiser has to pay. But our 
market-place is quite volatile - at least in terms of the winning price of an impression on a given site, with specific 
targeting, and relatively short forecasting horizon - so forecasting errors are usually large. This problem is exacerbated 
if the optimizer decides to select most of the impressions from a few sites, in which case forecast error can threaten ad's 
impression goal. In this post, I'll focus on a way that the optimizer can use to improve the diversity of its site portfolio 
without assuming anything about the forecast accuracy.

If our ad was not constrained by cost and all the sites selected by the advertiser had the same number of forecasted 
impressions, then the optimizer would select impressions till it reaches the impression goal. For example, if each of 
the sites site1, site2 and site3 offered 10,000 impressions, then an optimizer with an impression goal of 18,000 can 
simply solve the following linear program (necessary if thousands of sites are actually involved) to see what fraction 
of impressions it needs from each site (there's no objective since impression goal is fixed and there's no cost goal):

```
Maximize
 objective_function: + 0 site3

Subject To
 impressions: + 10000 site3 + 10000 site2 + 10000 site1 = 18000

Bounds
 0 <= site1 <= 1
 0 <= site2 <= 1
 0 <= site3 <= 1

End
```

This formulation effectively only asks to see if the program has a feasible solution, or not, for a given goal and hence 
there's no restriction that impressions must be bought from all sites, not just any two. The restriction to buy from as 
many distinct sites as possible (without violating any other, say cost or performance, constraints) would maximize our 
portfolio's diversity, and is best expressed as a _maximin optimization_ objective: maximize the minimum number of 
impressions across sites.

$$ \max [\min (f_1(x),...,f_n(x))] $$

where $f_{i}(x) (i = 1 \ldots n)$ are linear functions of $x$. In fact, for our purposes, $f_{i}(x) = F_{i}x_{i}$, where $F_i$ 
represents forecasted impressions for site $i$ and $x_i$ is the fraction that the optimizer is interested in.

Maximin optimization, in general, is a non-linear convex program. Luckily, it's possible to reformulate it as a 
linear program [1] by introducing an unbounded, slack variable $site_0$:

$$ \max z \\ \mbox{ s. t.} F_{i} x_{i} \ge z $$

```
Maximize
 objective_function: + site0

Subject To
 impressions: + 10000 site3 + 10000 site2 + 10000 site1 = 18000
 maximin1: + 10000 site1 - site0 >= 0
 maximin2: + 10000 site2 - site0 >= 0
 maximin3: + 10000 site3 - site0 >= 0

Bounds
 0 <= site1 <= 1
 0 <= site2 <= 1
 0 <= site3 <= 1
 site0 free

End
```

The maximin linear program would have an optimal solution when 6000 impressions are chosen from each site, 
thus minimizing the absolute impression difference between any two sites.

But what if one of the sites could not be part of a feasible solution because otherwise it could violate other goals 
(e.g. if impression from a site are too expensive or vastly under-perform)? Such cases, which, unfortunately are 
likely to be the norm, cause this minimax formulation to fail since the minimum number of impressions across _all_ 
sites stays at zero to (no impressions selected from at least one site) regardless of the distribution of other 
fractions. It is tempting to solve this by recasting the problem as:

$$ \max z \\ \mbox{ s. t. } x^{T}_{j} F_{i} x_{i} \ge x_i z $$

But such a quadratically-constraint program is far less efficient to solve than a linear program.

I hope to find a simpler solution to this problem soon.

Note (05/23/12):

While reading a StackExchange post [2], I realized that this problem could potentially be reformulated as a 
mixed integer linear program by introducing binary variables $ t_1, \ldots ,t_n$ corresponding to $ x_1, \ldots , x_n$ 
such that

$$ t_i = 1 \Rightarrow x_i \gt 0 \\ t_i = 0 \Rightarrow x_i = 0 $$

With the objective function being:

$$ \max (\sum_i t_i) $$

But what are the right constraints for this formulation? These don't work with the maximization objective 
(though will with minimization):

$$ x_i \leq t_i $$

Note (05/27/12):

The right constraints (thanks to [3] for showing the way) are:

$$ x_i \geq \epsilon t_i \\ x_i \leq t_i $$

$\epsilon$ is the smallest possible non-zero value of $x_i$ and, in our case, must be the inverse of the maximum 
impressions from any site.

References: 

* [1] [Convex Optimization](http://www.stanford.edu/~boyd/cvxbook/) 
* [2] http://math.stackexchange.com/questions/16788/solution-technique-to-optimize-sets-of-constraint-functions-with-objective-funct 
* [3] [Integer Programming Tricks](http://www.aimms.com/aimms/download/manuals/aimms3om_integerprogrammingtricks.pdf)
