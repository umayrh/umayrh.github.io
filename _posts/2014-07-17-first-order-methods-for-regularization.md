---
layout: post
title: "First-order methods for regularization"
date: "2014-07-17"
description:
image: /assets/images/first-regular.png
author: Umayr Hassan
tags:
- Convex optimization
- First-order methods
- Robust Estimation
- R
- Mathematics
---

General first-order methods use only the values of objective/constraints and their subgradients to optimize non-smooth 
(including constrained) function [5]. Such methods that are oblivious to the structure of the problem are 
termed _black-box optimization_ techniques.

## Why first-order?

Primarily because the computation cost, per iteration, of high-accuracy methods like interior-point generally increases 
non-linearly, which is prohibitively expensive for very large data-sets (> 100K variables).

Though the cost per iteration of general methods is low, these methods cannot exploit the structure of the problem. 
This post discusses two first-order optimization techniques for regularization problems that can exploit the structure 
of the problem to give better convergence rate than general methods.

## Regularization

Regularization problems in statistics and machine learning, or denoting problems in signal processing, take one of the 
following forms:

$$\min{(\frac{1}{2}\|Ax - b\|^2 + \lambda \|x\|)}$$
$$\min{f(x)} \mbox{ s.t. } \| b - Ax \| \le \epsilon$$

where $\|.\|$ indicates some kind of a norm (or seminorm), e.g. $L_1$ or $L_2$ in statistics and Total Variation norm in 
imaging, and $\lambda$ is a real-valued regularization parameter. Optimization of such composite convex functions - 
often with a smooth component, $f(x)$, and a non-smooth component, $g(x)$ - can be expressed as:

$$\min_{x \in \mathbb{R}} {f(x) + \lambda g(x)}$$

Regularization imposes a structure, using a specific norm, on the solution. For example, using L1 norm encourages 
sparsity, which often results in more noise-tolerant solutions. This was the motivation behind ridge regression and 
LASSO [8] in statistical estimation. Unconstrained regularization problems can be solved using (constrained) linear 
programming while constrained problems may be solved using second-order cone program. Both programs are variants of 
interior-points algorithms, and too slow for very large data sets. Various first-order methods may be used to solve 
regularization problem. In fact, for the specific case of regularization problems, first-order method can be shown 
to converge to an ε-solution at an optimal rate.

## Complexity

The complexity of first-order methods is often measured in terms of the number of calls to a first-order oracle 
(section 5.1 in [5]) required to reach an $\epsilon$-solution. Conversely, the convergence rate gives the approximation error 
after _t_ iterations. Nemirovski and Yudin's work [5] established a lower bound for the information-based complexity of 
black-box optimization techniques: $O(\frac{1}{\epsilon^2})$ iterations (alternatively, an $O(\frac{1}{\sqrt{t}})$-precise 
solution after $t$ iterations). 
Furthermore, in many cases, the complexity is still dependent on the dimension, $n$, of the problem - $O(n)$, in the worst 
case. For very large dimensional problems, such black-box schemes would converge too slowly.

Nonetheless, there are large classes of problems - including regularization - for which the complexity is $O(\frac{1}{\epsilon})$ 
or, in some cases, $O(\frac{1}{\sqrt{\epsilon}})$ (theorem 3.8 in [7], section 5.1 in [9] [10, 12]). In such cases, the 
complexity is also either independent of or only sublinearly dependent on problem dimension, making them feasible for 
very large dimensional but medium-accuracy problems (such as those in machine learning).

Why not use polynomial-time interior-point methods for convex optimization?

While interior-point methods converge rapidly - $Ο(e^{Ο(t)})$ - to an ε-solution, the complexity of each iteration is 
super-linear - $O(nk)$, $k > 1$. For very large dimensional problems, these methods are prohibitively expensive.

## Proximal algorithms

$$\min_{x \in \mathbb{R}} {f(x) + \lambda g(x)}$$

For the composite function optimization problem, we assume that:

- the problem has a unique minimizer
- $f(x)$ is $\beta$-smooth, convex and differentiable
- $g(x)$ is convex, subdifferentiable and "simple" (to be clarified later)

From the definition of $\beta$-smooth convex function, f(x):

$$f(x_{k+1}) \le f(x_k)+ \nabla{f(x_k)}(x_{k+1} - x_k) + \frac{\beta}{2} \|x_{k+1} - x_k\|^2$$

For subgradient descent from a given a point $x_k$, we need to find the next point xk+1 such that $f(x_{k}+1)$ is minimized i.e [3].

$$x_{k+1} = \mathrm{argmin}_{x \in \mathbb{R}} f(x_k)+ \nabla{f(x_k)}(x - x_k) + \frac{\beta}{2} \|x - x_k\|^2$$

For the composite function, $f(x) + \lambda g(x)$, this becomes

$$x_{k+1} = \mathrm{argmin}_{x \in \mathbb{R}} \lambda g(x)+ \nabla{f(x_k)}(x - x_k) + \frac{\beta}{2} \|x - x_k\|^2$$

which can be reduced to

$$x_{k+1} = \mathrm{argmin}_{x \in \mathbb{R}} \lambda g(x)+\frac{\beta}{2} \|x - (x_k - \frac{1}{\beta}\nabla{f(x_k)})\|^2$$

Despite the fact the each iteration here involves another optimization, a wide class of problems in this form can 
be solved with _proximal minimization algorithms_ [13, 14]_._  Proximal algorithms minimizing a $\beta$-smooth function 
$f(x)$ can in general be represented as:

$$x_{k+1} = prox_{\eta,f}(x_k)$$

where $\eta = \frac{1}{\beta}$ and the proximity operator, $prox_{\eta}f(y)$, of a scaled function $f(x)$ is defined as

$$\mathrm{argmin}_{x \in \mathbb{R}} f(x) + \frac{1}{2\eta} \|x - y\|^2$$

For the composite function, $f(x) + \lambda g(x)$, this implies

$$x_{k+1} = prox_{\lambda\eta,g(x)}(x_k - \frac{1}{2\eta\lambda}\nabla{f(x_k)})$$

Proximal algorithms are useful when the optimization subproblems either admit a closed-form solution or can be 
rapidly solved numerically. If this is the case, then $g(x)$ is considered "simple." For example, $g(x)$ in regularization 
problems is an $L_p$ norm, where p = {1, 2, $\infty$}. Using the calculus of proximity operators, it is possible to evaluate the 
closed-form solutions for these norm:

$prox_{\eta,\|.\|_{1}}(y)= \begin{cases} y-\eta, & y \ge \eta \\ 0, & \|y\| < \eta \\ y+\eta, & y \le \eta \end{cases}$

This operation, for $L_1$ norm, is also called _soft thresholding_.

$prox_{\eta,\|.\|_{2}}(y)= \begin{cases} (1-\frac{\eta}{\|y\|_2})y, & \|y\|_2 \ge \eta \\ 0, & \|y\|_2 < \eta\end{cases}$

The operation for $L_2$ norm is also called _blockwise soft thresholding_.

How rapidly would this scheme converge for composite functions compared to subgradient descent (since $g(x)$ is 
non-smooth)? Since the proximal algorithm does not need to evaluate the subgradient of $g(x)$ - only the gradient of $f(x)$, 
which is $\beta$-smooth - the convergence rate should the same as that $f(x)$. Following Theorem 3.3 in [7], given 
the minimum $x^{\ast}$,

$$(f(x_k) + \lambda g(x_k)) - (f(x^{\ast}) + \lambda g(x^{\ast})) \le O(1)\frac{\beta \|x_0 - x^{\ast}\|}{k}$$

which is much faster than subgradient descent's $O(\frac{1}{\sqrt{k}})$. Improving convergence rate to $O(\frac{1}{k^2})$ [1,2] 
will be the topic of our next post.

```R
## Corrupted signal and model
 
x <- c(1, 2, 3, 4, 5, 6)
t <- seq(from = 0, by = 0.02, to = 2*pi)
A <- cbind(sin(t), sin(2*t), sin(3*t), sin(4*t), sin(5*t), sin(6*t))
e <- -4+8*rnorm(length(t),0,1)
#e[100:115] <- 30
y <- A %*% x + e
 
plot(t, A %*% x, 'l', col='blue', ylab = "signal", xlab = "time")
lines(t, y)
 
## Proximal algorithm for l1
 
## parameters
iter <- 1000
beta <- norm(A, type="F")^2
eta <- 1 / beta
lambda <- 1
xx <- c(0, 0, 0, 0, 0, 0)
 
## main loop
for (i in 1:iter) {
 gradient_x <- t(A) %*% ( A %*% xx - y )
 xx_tmp <- xx - eta * gradient_x
 v <- eta * lambda
 # L1 prox/shrinkage-thresholding
 xx <- pmax(xx_tmp - v, 0) - pmax(- xx_tmp - v, 0)
}
 
xx
 
lines(t, A %*% xx, col="red")
 
legend("topright", legend=c("original signal", "corrupted signal", "recovered signal"),
 col=c("black", "blue", "red"), lwd=c(1, 1, 1))
```
![Signal recovery using L1-Prox](http://umayrh.files.wordpress.com/2014/07/l1prox1.png?w=300)
Signal recovery using L1-Prox

## References

[1](http://mechroom.technion.ac.il/~becka/papers/71654.pdf) A. Beck, M. Teboulle. A Fast Iterative Shrinkage-Thresholding Algorithm for Linear Inverse Problems. SIAM J. Imaging Sciences, 2009 
[2](http://statweb.stanford.edu/~candes/nesta/NESTA.pdf) S. Becker, J. Bobin, E. Candes. NESTA: A Fast and Accurate First-Order Method for Sparse Recovery. Technical Report, Caltech, 2009 
[3](http://blogs.princeton.edu/imabandit/2013/04/11/orf523-ista-and-fista/) http://blogs.princeton.edu/imabandit/2013/04/11/orf523-ista-and-fista/ 
[4](https://www.ipam.ucla.edu/publications/optut/optut_9300.pdf) M. Teboulle. First-Order Algorithms for Convex Optimization. IPAM, 2010 
[5](http://www2.isye.gatech.edu/~nemirovs/Lect_ModConvOpt.pdf) A. Ben-Tal, A. Nemirovski. Lectures on Modern Convex Optimization. Lecture Notes, 2013 
[6](http://en.wikipedia.org/wiki/Subgradient#The_subgradient) http://en.wikipedia.org/wiki/Subgradient#The_subgradient 
[7](http://www.princeton.edu/~sbubeck/Bubeck14.pdf) S. Bubeck. Theory of Convex Optimization for Machine Learning. Lecture Notes, 2014 
[8] T. Hastie, R. Tibshirani, J. Friedman. Elements of Statistical Learning. Second Edition. 
[9](http://www.ecore.be/DPs/dp_1329823186.pdf) Y. Nesterov. Subgradient Methods for Huge-Scale Optimization Problems. ECORE Discussion Paper, 2012 
[10](http://www2.isye.gatech.edu/~nemirovs/MLOptChapterI.pdf) A. Juditsky, A. Nemirovski. First-Order Methods for Non-Smooth Convex Large-Scale Optimization, 1. Optimization for Machine Learning, 
[11] J. F. Bonnan, J. C. Gilbert, C. Lemaréchal, C. A. Sagastizábal. Numerical Optimization: Theoretical and Practical Aspects. Second Edition, Springer, 2006 
[12](https://iew3.technion.ac.il/Home/Users/becka/smoothing.pdf) A. Beck, M. Teboulle. Smoothing and First Order Methods: A Unified Framework. SIAM Journal Of Optimization, 2012 
[13](http://web.stanford.edu/~boyd/papers/prox_algs.html) N. Parrikh, S. Boyd. Proximal Algorithms. Foundations and Trends in Optimization, 2014 
[14](http://www.ljll.math.upmc.fr/~plc/mms1.pdf) P. L. Combette, V. R. Wajs. Signal Recovery by Proximal Forward-Backward Splitting. Multiscale Modeling and Simulation, 2005

## Credits

The image comes from [regularize.wordpress](https://regularize.wordpress.com/2012/05/21/problems-solved-rip-and-nsp-are-np-hard-homotopy-for-l1-has-exponential-complexity/), 
and shows a 3d Mairal-Yu simplex, the equivalent of a Klee-Minty cube for the $L_1$ homotopy method.
