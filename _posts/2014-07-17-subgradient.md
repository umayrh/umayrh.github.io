---
layout: post
title: "Subgradient"
date: "2014-07-17"
description:
image: /assets/images/subgradient.png
author: Umayr Hassan
tags:
- Convex optimization
- Function analysis
- Mathematics
---

Subgradient generalizes the notion of gradient/derivative and quantifies the rate of change of 
non-differentiable/non-smooth function ([1], section 8.1 in [2]). For a real-valued function, the "subgradients" 
of the function at a point $x_0$ are all values of $c$ such that:

$$f(x) - f(x_0) \ge c (x - x_0)$$

Unlike derivative of smooth function, which is a singleton value, the subgradients form a set 
(e.g. an interval of points). The set of all subgradients is called the "subdifferential".

Subdifferential always exists if $f(x)$ is a convex function with a convex domain $X$, and vice versa (proposition 1.1 in [3]):

$$\forall x,y\in X,f(tx + (1-t)y) \le tf(x) + (1-t)f(y)\leftrightarrow \partial f \not\in \emptyset$$

The generalized subgradient descent scheme is given as:

$$x_{k+1} = x_k - \lambda \nabla{f(x_k)}$$

## References

* [1](http://en.wikipedia.org/wiki/Subgradient#The_subgradient) Wikipedia, Subgradient 
* [2] J. F. Bonnan, J. C. Gilbert, C. Lemaréchal, C. A. Sagastizábal. Numerical Optimization: Theoretical and Practical Aspects. Second Edition, Springer, 2006 
* [3](http://www.princeton.edu/~sbubeck/Bubeck14.pdf) S. Bubeck. Theory of Convex Optimization for Machine Learning. Lecture Notes, 2014

## Credits

Image from the Hal Daumé's post, [Subgradient descent, or something...](https://nlpers.blogspot.com/2016/06/subgradient-descent-or-something.html).