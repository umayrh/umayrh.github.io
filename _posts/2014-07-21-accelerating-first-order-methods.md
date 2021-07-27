---
layout: post
title: "Accelerating first-order methods"
date: "2014-07-21"
description:
image: /assets/images/ChebyshevTSpiral.gif
author: Umayr Hassan
tags:
- Convex optimization
- First-order methods
- Mathematics
---

The lower bound on the oracle complexity of continuously differentiable, $\beta$-smooth convex function is $O(\frac{1}{\sqrt{\epsilon}})$ 
[Theorem 2.1.6, Nesterov04; Theorem 3.8, Bubeck14; Nesterov08]. General first-order gradient descent does not achieve 
this - e.g. L1-Prox, or ISTA, achieves $O(\frac{1}{\epsilon})$. Nesterov, in a series of papers [Nesterov83, Nesterov88, Nesterov07], 
proposed techniques to improve the convergence rate for smooth functions to $O(\frac{1}{t^2})$. In this post, we discuss Nesterov 
acceleration.

## Accelerated gradient descent: $\alpha$-strongly convex, $\beta$-smooth

_Algorithm_: Given a starting point x1 = y1,  then for t ≥ 1

$$y_{k+1} = x_k - \frac{1}{\beta}\nabla{f(x_k)} \\ x_{k+1} = (1 + \frac{\sqrt{Q}-1}{\sqrt{Q}+1})y_{k+1}-(\frac{\sqrt{Q}-1}{\sqrt{Q}+1})y_{k}$$

_Theorem_: For α-strongly convex, β-smooth functions with condition number Q = β/α, Nesterov's accelerated gradient 
descent satisfies [Theorem 3.11, Bubeck14], which comes close to the lower bound [Theorem 2.1.13, Nesterov04]:

$$f(x_k) - f(x^{\ast}) \le \frac{\alpha + \beta}{2}||x_1 - x^{\ast}||^2 \exp{(-\frac{k - 1}{\sqrt{Q}})}$$

_Proof sketch_:

1. Following [Bubeck14], one can define an α-strongly convex quadratic approximation to f(x) that improves with each 
iteration:

$$\Phi_1(x) = f(x_1) + \frac{\alpha}{2}||x - x_1||^2 \\ \Phi_{k+1}(x) = (1 - \frac{1}{\sqrt{Q}}) \Phi_k(x) + \frac{1}{\sqrt{Q}}(f(x_k) + \nabla{f(x_k)}(x - x_k) + \frac{\alpha}{2}||x_k - x||^2)$$

Then, using the definition of α-strong convexity, one can show that

$$\Phi_{k+1}(x) \le f(x) + (1 - \frac{1}{\sqrt{Q}})^k (\Phi_1(x) - f(x)) $$

2. Now, assuming the following inequality is true,

$$f(y_k) \le \min_x{\Phi_k(x)}$$

3. Then one can argue that

$$f(y_k) - f(x^{\ast}) \le \Phi_k(x^{\ast}) - f(x^{\ast}) \\ \le (1 - \frac{1}{\sqrt{Q}})^{t-1}(\Phi_1(x^{\ast}) - f(x^{\ast})) \\ \le \frac{\alpha + \beta}{2}||x_1 - x^{\ast}||^2 (1 - \frac{1}{\sqrt{Q}})^{t-1}$$

### Accelerated gradient descent: $\beta$-smooth

_Algorithm_: Given a starting point x1 = y1,  then for t ≥ 1

$$\lambda_0 = 0, \lambda_k = \frac{1+\sqrt{1 + 4\lambda_{k-1}^2}}{2}, \gamma_k = \frac{1 - \lambda_k}{\lambda_{k+1}} \\ y_{k+1} = x_k - \frac{1}{\beta}\nabla{f(x_k)} \\ x_{k+1} = (1 - \gamma_k)y_{k+1} + \gamma_k y_k$$

_Theorem_: For non-strictly convex functions, where α = 0, gradient descent satisfies [Theorem 3.12, Bubeck14], which 
is within a constant factor of the lower bound [Theorem 2.1.7 in Nesterov04]:

$$f(x_k) - f(x^{\ast}) \le \frac{2\beta||x_1 - x^{\ast}||^2}{k^2}$$

_Proof sketch_:

1. In a gradient descent scheme for β-smooth functions [Lemma 3.4, Bubeck14]

$$f(y_{k+1}) - f(y_k) \le \nabla{f(x_k)(x_k - y_k) - \frac{1}{2\beta}}||\nabla{f(x_k)}||^2 \\ = \beta (x_k - y_{k+1})(x_k - y_k) - \frac{\beta}{2}||x_k - y_{k+1}||^2 \\ f(y_{k+1}) - f(x^{\ast}) \le \beta (x_k - y_{k+1})(x_k - y_k) - \frac{\beta}{2}||x_k - y_{k+1}||^2$$

2.  Let δs = f(ys) - f(x^{\ast}), then scaling the first inequality by (λs - 1) and adding the result to second

$$\lambda_k \delta_{k+1} - (\lambda_k - 1) \delta_k \\ \le \beta (x_k - y_{k+1})(\lambda_k x_k - (\lambda_k - 1)y_k - x^{\ast}) - \frac{\beta}{2} \lambda_k||x_k - y_{k+1}||^2$$

3. Scaling by λs and after some manipulation

$$\lambda_k^2 \delta_{k+1} - \lambda_{k-1}^2 \delta_k \\ \le \frac{\beta}{2}(||\lambda_k x_k - (\lambda_k - 1)y_k - x^{\ast 2}||^2 -||\lambda_k y_{k+1} - (\lambda_k - 1)y_k - x^{\ast 2}||^2) \\ = \frac{\beta}{2}(||u_s||^2 - ||u_{k+1}||^2) \ where\ u_k=\lambda_k x_k- (\lambda_k - 1)y_k - x^{\ast}$$

4. Summing these inequalities from k = 1 to k = t - 1, yields

$$\delta_t \le \frac{\beta}{2\lambda_{t-1}^2}||u_1||^2, \ and\ \lambda_{t-1} \ge \frac{t}{2}$$

## Interpretation: Chebychev polynomials

Moritz Hardt elegantly describes an interpretation of accelerated gradient descent based on function interpolation 
[Hardt13]. Gradient descent can be seen as approximating a function using a degree-k polynomial. For strongly convex 
functions, the derivative can be expressed as a linear combination of previous steps i.e.

$$x_{k+1} = x_k + \eta_k \nabla{f(x_k)} \\ = A^{T}x + b$$

Assuming that ∑λs = 1,

$$x_{k+1} - x^{\ast} \\ = \sum_{s=1}^{k}(\lambda_s (x_s - x^{\ast})) \\ = \sum_{s=1}^{k}(\lambda_s A^s) (x_1 - x^{\ast}) \\ = P_{k}(A)(x_1 - x^{\ast}) \\ where\ P_{k}(A) = \sum_{s=1}^{k}(\lambda_s A^s)$$

Thus given that

$$||x_{k+1} - x^{\ast}|| = ||P_{k}(\mu)||_A\ ||x_1 - x^{\ast}||$$

$P(\mu)$ needs to be chosen over all eigenvalues of A so that (1) $P_{k}(\mu)$ = 1, for $\mu$ = 0, and (2) its maximum norm is 
minimized. This is hard since it requires knowing all eigenvalues but can be achieved, under certain conditions, 
if the domain is assumed to be continuous [Kelner09]. In short, a scaled and shifted 
[Chebychev polynomial](http://en.wikipedia.org/wiki/Chebyshev_polynomials) is the unique polynomial that minimizes 
this approximation error. For Chebychev polynomial, the maximum error is

$$\max{(||P_{k}(\mu)||)} = (\frac{\sqrt{Q}-1}{\sqrt{Q} + 1})^k$$

Moreover, since Chebychev polynomials can be expressed recursively, required only the two previously calculated polynomials, 
the gradient descent update only depends on last two values.

This method seems to be well known in Numerical Analysis, where it is used to speed up iterative linear 
solvers [chapter 4, Kincaid02; section 7.4,  Saad11]. It also seems to predate Nesterov's work [Manteuffel77].

## Extension: Bregman Divergence

Paul Tseng uses [Bregman Divergences](http://mark.reid.name/blog/meet-the-bregman-divergences.html) to give a 
unified framework for Nesterov's methods across different classes of problems [Tseng08].

See also [Taboulle10, Candes11, Hao11, Gordon12] for a good overview of first-order acceleration methods. 
Next post discusses techniques that utilize accelerated gradient descent to solve regularization problems 
[Nesterov07, Beck09, Becker09].

## References

* [Beck09](http://mechroom.technion.ac.il/~becka/papers/71654.pdf) A. Beck, M. Teboulle. A Fast Iterative Shrinkage-Thresholding Algorithm for Linear Inverse Problems. SIAM Journal of Imaging Sciences, 2009 
* [Becker09](http://statweb.stanford.edu/~candes/nesta/NESTA.pdf) S. Becker, J. Bobin, E. Candes. NESTA: A Fast and Accurate First-Order Method for Sparse Recovery. Technical Report, Caltech, 2009 
* [Bubeck14](http://www.princeton.edu/~sbubeck/Bubeck14.pdf) S. Bubeck, Theory of Convex Optimization for Machine Learning 
* [Candes11](http://statweb.stanford.edu/~candes/math301/Lectures/fast_proximal_methods.pdf) E. Candes. Math 301, Lectures Notes. 
* [Chen11](http://statweb.stanford.edu/~candes/math301/Lectures/acc_nesterov.pdf) H. Chen, X. Ming. Accelerating Nesterov's Method for Strongly Convex Functions. Course presentation, 2011. 
* [Hardt13](http://mrtz.org/blog/the-zen-of-gradient-descent/) M. Hardt. The Zen of Gradient Descent. Blog, 2013. 
* [Karniadakis00](http://www.cfm.brown.edu/people/gk/chap7/node21.html) G. E. Karniadakis. R. M. Kirby. Parallel Scientific Computing in C++ and MPI. 2000 
* [Kelner09](http://ocw.mit.edu/courses/mathematics/18-409-topics-in-theoretical-computer-science-an-algorithmists-toolkit-fall-2009/lecture-notes/MIT18_409F09_scribe22.pdf) J. Kelner. Lecture Notes. MIT 18.409, Lecture 22, 2009 
* [Kincaid02](https://bookstore.ams.org/amstext-2) D. R. Kincaid, E. W. Cheney. Numerical Analysis: Mathematics of Scientific Computing. AMS, 2002 
* [Nesterov83] Y. Nesterov. A method for solving a convex programming problem with convergence rate O(1/k2). Dokaldy AN SSR, 1983 
* [Nesterov88] Y. Nesterov. On an approach to the construction of optimal methods of minimization of smooth convex functions. Ekonom. i. Mat. Metody, 1988 
* [Nesterov04] Y. Nesterov. Introductory Lectures On Convex Programming: A Basic Course. Kluwer Academic Publishers, 2004 
* [Nesterov07](http://www.optimization-online.org/DB_FILE/2007/09/1784.pdf) Y. Nesterov. Gradient methods for minimizing composite objective function. Report, CORE, 2007 
* [Nesterov08](http://galton.uchicago.edu/~lekheng/courses/31060s13/nesterov.pdf) Y. Nesterov. How to advance in Structural Convex Optimization. Optima, 2008 
* [Manteuffel77](http://link.springer.com/article/10.1007%2FBF01389971#page-1) T. A. Manteuffel. The Tchebyshev iteration for nonsymmetric linear systems. Numer. Math, 1977. 
* [Saad11](http://www-users.cs.umn.edu/~saad/eig_book_2ndEd.pdf) Y. Saad. Numerical Methods for Large Eigenvalue Problems. SIAM, 2011 
* [Taboulle10](https://www.ipam.ucla.edu/publications/optut/optut_9300.pdf). M. Taboulle. First-Order Methods for Optimization. IPAM Optimization Tutorials, 2010 
* [Gordon12](https://www.cs.cmu.edu/~ggordon/10725-F12/slides/09-acceleration.pdf) G. Gordon, R. Tibshirani. Course Notes, 10-725 Optimization, Fall 2012 
* [Tseng08](http://www.mit.edu/~dimitrib/PTseng/papers/apgm.pdf) P. Tseng. On Accelerated Proximal Gradient Algorithms for Convex-Concave Optimization. SIAM Journal of Optimization, 2008

## Credit

Main image from Wolfram's [Chebyshev Polynomial of the First Kind](http://mathworld.wolfram.com/ChebyshevPolynomialoftheFirstKind.html): 
"A beautiful plot can be obtained by plotting $T_{n}(x)$ radially, increasing the radius for each value of $n$, and 
filling in the areas between the curves (Trott 1999, pp. 10 and 84)."