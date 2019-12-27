---
layout: post
title: "α-strong convexity, β-strong smoothness"
date: "2014-07-09"
description:
image: /assets/images/heb-conv-over-g.png 
author: Umayr Hassan
tags:
- Convex optimization
- Function analysis
- Mathematics
---

Strong convexity often allows for optimization algorithms that converge very quickly to an $\epsilon$-optimum 
(rf. [FISTA](http://mechroom.technion.ac.il/~becka/papers/71654.pdf) and 
[NESTA](http://statweb.stanford.edu/~candes/nesta/NESTA.pdf)). This post will cover some fundamentals of strongly 
convex functions.

## Convexity

For a convex function, $f: \mathbf{R} \to \mathbf{R}$ and $\forall \gamma \in [0, 1]$,

$$ f(tx + (1-t)y) \le tf(x) + (1-t)f(y) $$

## Strict convexity

For a _strictly_ convex function,

$$ f(tx + (1-t)y) < tf(x) + (1-t)f(y) $$

Geometrically, the definition of convexity implies that all points on any straight line connecting any two points in a 
convex set also lie in the set. Strict convexity excludes linear and affine functions, or functions with linear/affine 
subsets in their boundaries. (How would this extend to non-Euclidean geometries?)

## α-strong convexity

A function is α-strongly convex with respect to a norm \|.\| if, for α > 0

$$f(tx + (1-t)y) < tf(x) + (1-t)f(y) - \frac{\alpha}{2} \|x - y\|^2$$

Alternatively,

$$(\nabla{f(x)} - \nabla{f(y)})(x - y) \ge \alpha \|x - y\|^2$$

or,

$$f(y) \ge f(x)+ \nabla{f(x)}(y - x) + \frac{\alpha}{2} \|x - y\|^2$$

Strongly convexity extends strict convexity. For twice-differentiable functions, this implies that 
$\nabla^2f(x) \ge \alpha$. As Bubeck explains [1], strongly convex functions speed up 
convergence of first-order methods. Larger values of α imply larger gradient, and hence step size, when further away 
from the optimum.

Strong smoothness is another property of certain convex functions:

## $\beta$-smoothness

A function is $\beta$-smoothly convex with respect to a norm \|.\| if, for $\beta$ > 0, [4]

$$f(y) \le f(x)+ \nabla{f(x)}(y - x) + \frac{\beta}{2} \|x - y\|^2$$

This definition gives an lower bound on the improvement in one step of (sub)gradient descent [1]:

$$f(x - \frac{1}{\beta}\nabla{f(x)})- f(x) \le \frac{-1}{2 \beta} \| \nabla{f(x)} \|^2$$

Alternatively, $\beta$-smoothly convex function [lemma 3.3, 1]:

$$f(x) - f(y) \le \nabla{f(x)}(y - x) - \frac{1}{2 \beta} \|\nabla{f(x)} - \nabla{f(y)}\|^2$$

## Strong/smooth duality

Under certain conditions, a-strong convexity and $\beta$-smoothness are dual notions. For now, we'll state the result 
without discussion.

If $f$ is a closed and convex function, then $f$ is $\alpha$-strongly convex function with respect to a norm $\|.\|$ if and 
only if $f^{\ast}$ is $\frac{1}{\alpha}$-strongly smooth with respect to the dual norm $\|.\|^{\ast}$ (corollary 7 in [4]).

## References:

[[1]](http://www.princeton.edu/~sbubeck/Bubeck14.pdf) S. Bubeck, Theory of Convex Optimization for Machine Learning, section 3.4 
[[2]](http://en.wikipedia.org/wiki/Convex_function#Strongly_convex_functions) Wikipedia, Convex function 
[[3]](http://web.stanford.edu/~boyd/cvxbook/) S. Boyd and G. Vandenberghe, Convex Optimization, section 9.1.2 
[[4]](http://ttic.uchicago.edu/~shai/papers/KakadeShalevTewari09.pdf) S. M. Kakade, S. Shalev-Schwartz, A. Tiwari. On the duality of strong convexity and strong smoothness: Learning applications and matrix regularization. Technical Report, 2009

## Credits

Image from Sebastian Pokutta's post, [Cheat Sheet: Smooth Convex Optimization]((http://www.pokutta.com/blog/research/2018/12/07/cheatsheet-smooth-idealized.html)).