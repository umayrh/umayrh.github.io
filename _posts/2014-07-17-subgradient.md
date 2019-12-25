---
title: "Subgradient"
date: "2014-07-17"
---

Subgradient generalizes the notion of gradient/derivative and quantifies the rate of change of non-differentiable/non-smooth function (\[1\], section 8.1 in \[2\]). For a real-valued function, the _subgradients_ of the function at a point x0 are all values of c such that:

\[latex\]f(x) - f(x\_0) \\ge c (x - x\_0)\[/latex\]

Unlike derivative of smooth function, which is a singleton value, the subgradients form a set (e.g. an interval of points). The set of all subgradients is called the _subdifferential_.

Subdifferential always exists if f(x) is a convex function with a convex domain X, and vice versa (proposition 1.1 in \[3\]):

\[latex\]\\forall x,y\\in X,f(tx + (1-t)y) \\le tf(x) + (1-t)f(y)\\leftrightarrow \\partial f \\not\\in \\emptyset\[/latex\]

The generalized subgradient descent scheme is given as:

\[latex\]x\_{k+1} = x\_k - \\lambda \\nabla{f(x\_k)}\[/latex\]

**References**

[\[1\]](http://en.wikipedia.org/wiki/Subgradient#The_subgradient) Wikipedia, Subgradient \[2\] J. F. Bonnan, J. C. Gilbert, C. Lemaréchal, C. A. Sagastizábal. Numerical Optimization: Theoretical and Practical Aspects. Second Edition, Springer, 2006 [\[3\]](http://www.princeton.edu/~sbubeck/Bubeck14.pdf) S. Bubeck. Theory of Convex Optimization for Machine Learning. Lecture Notes, 2014
