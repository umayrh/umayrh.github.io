---
title: "Accelerating first-order methods"
date: "2014-07-21"
---

The lower bound on the oracle complexity of continuously differentiable, β-smooth convex function is O(1/√ε) \[Theorem 2.1.6, Nesterov04; Theorem 3.8, Bubeck14; Nesterov08\]. General first-order gradient descent does not achieve this - e.g. L1\-Prox, or ISTA, achieves O(1/ε). Nesterov, in a series of papers \[Nesterov83, Nesterov88, Nesterov07\], proposed techniques to improve the convergence rate for smooth functions to O(1/t2). In this post, we discuss Nesterov acceleration.

**Accelerated gradient descent:**** α-strongly convex, β-smooth**

_Algorithm_: Given a starting point x1 = y1,  then for t ≥ 1

\[latex\]y\_{k+1} = x\_k - \\frac{1}{\\beta}\\nabla{f(x\_k)} \\\\ x\_{k+1} = (1 + \\frac{\\sqrt{Q}-1}{\\sqrt{Q}+1})y\_{k+1}-(\\frac{\\sqrt{Q}-1}{\\sqrt{Q}+1})y\_{k}\[/latex\]

_Theorem_: For α-strongly convex, β-smooth functions with condition number Q = β/α, Nesterov's accelerated gradient descent satisfies \[Theorem 3.11, Bubeck14\], which comes close to the lower bound \[Theorem 2.1.13, Nesterov04\]:

\[latex\]f(x\_k) - f(x\*) \\le \\frac{\\alpha + \\beta}{2}||x\_1 - x\*||^2 \\exp{(-\\frac{k - 1}{\\sqrt{Q}})}\[/latex\]

_Proof sketch_:

1\. Following \[Bubeck14\], one can define an α-strongly convex quadratic approximation to f(x) that improves with each iteration:

\[latex\]\\Phi\_1(x) = f(x\_1) + \\frac{\\alpha}{2}||x - x\_1||^2 \\\\ \\Phi\_{k+1}(x) = (1 - \\frac{1}{\\sqrt{Q}}) \\Phi\_k(x) + \\frac{1}{\\sqrt{Q}}(f(x\_k) + \\nabla{f(x\_k)}(x - x\_k) + \\frac{\\alpha}{2}||x\_k - x||^2)\[/latex\]

Then, using the definition of α-strong convexity, one can show that

\[latex\]\\Phi\_{k+1}(x) \\le f(x) + (1 - \\frac{1}{\\sqrt{Q}})^k (\\Phi\_1(x) - f(x)) \[/latex\]

2\. Now, assuming the following inequality is true,

\[latex\]f(y\_k) \\le \\min\_x{\\Phi\_k(x)}\[/latex\]

3\. Then one can argue that

\[latex\]f(y\_k) - f(x\*) \\le \\Phi\_k(x\*) - f(x\*) \\\\ \\le (1 - \\frac{1}{\\sqrt{Q}})^{t-1}(\\Phi\_1(x\*) - f(x\*)) \\\\ \\le \\frac{\\alpha + \\beta}{2}||x\_1 - x\*||^2 (1 - \\frac{1}{\\sqrt{Q}})^{t-1}\[/latex\]

****Accelerated gradient descent: **β-smooth**

_Algorithm_: Given a starting point x1 = y1,  then for t ≥ 1

\[latex\]\\lambda\_0 = 0, \\lambda\_k = \\frac{1+\\sqrt{1 + 4\\lambda\_{k-1}^2}}{2}, \\gamma\_k = \\frac{1 - \\lambda\_k}{\\lambda\_{k+1}} \\\\ y\_{k+1} = x\_k - \\frac{1}{\\beta}\\nabla{f(x\_k)} \\\\ x\_{k+1} = (1 - \\gamma\_k)y\_{k+1} + \\gamma\_k y\_k\[/latex\]

_Theorem_: For non-strictly convex functions, where α = 0, gradient descent satisfies \[Theorem 3.12, Bubeck14\], which is within a constant factor of the lower bound \[Theorem 2.1.7 in Nesterov04\]:

\[latex\]f(x\_k) - f(x\*) \\le \\frac{2\\beta||x\_1 - x\*||^2}{k^2}\[/latex\]

_Proof sketch_:

1\. In a gradient descent scheme for β-smooth functions \[Lemma 3.4, Bubeck14\]

\[latex\]f(y\_{k+1}) - f(y\_k) \\le \\nabla{f(x\_k)(x\_k - y\_k) - \\frac{1}{2\\beta}}||\\nabla{f(x\_k)}||^2 \\\\ = \\beta (x\_k - y\_{k+1})(x\_k - y\_k) - \\frac{\\beta}{2}||x\_k - y\_{k+1}||^2 \\\\ f(y\_{k+1}) - f(x\*) \\le \\beta (x\_k - y\_{k+1})(x\_k - y\_k) - \\frac{\\beta}{2}||x\_k - y\_{k+1}||^2\[/latex\]

2\.  Let δs = f(ys) - f(x\*), then scaling the first inequality by (λs - 1) and adding the result to second

\[latex\]\\lambda\_k \\delta\_{k+1} - (\\lambda\_k - 1) \\delta\_k \\\\ \\le \\beta (x\_k - y\_{k+1})(\\lambda\_k x\_k - (\\lambda\_k - 1)y\_k - x\*) - \\frac{\\beta}{2} \\lambda\_k||x\_k - y\_{k+1}||^2\[/latex\]

3\. Scaling by λs and after some manipulation

\[latex\]\\lambda\_k^2 \\delta\_{k+1} - \\lambda\_{k-1}^2 \\delta\_k \\\\ \\le \\frac{\\beta}{2}(||\\lambda\_k x\_k - (\\lambda\_k - 1)y\_k - x\*^2||^2 -||\\lambda\_k y\_{k+1} - (\\lambda\_k - 1)y\_k - x\*^2||^2) \\\\ = \\frac{\\beta}{2}(||u\_s||^2 - ||u\_{k+1}||^2) \\ where\\ u\_k=\\lambda\_k x\_k- (\\lambda\_k - 1)y\_k - x\*\[/latex\]

4\. Summing these inequalities from k = 1 to k = t - 1, yields

\[latex\]\\delta\_t \\le \\frac{\\beta}{2\\lambda\_{t-1}^2}||u\_1||^2, \\ and\\ \\lambda\_{t-1} \\ge \\frac{t}{2}\[/latex\]

**Interpretation: Chebychev polynomials**

Moritz Hardt elegantly describes an interpretation of accelerated gradient descent based on function interpolation \[Hardt13\]. Gradient descent can be seen as approximating a function using a degree-k polynomial. For strongly convex functions, the derivative can be expressed as a linear combination of previous steps i.e.

\[latex\]x\_{k+1} = x\_k + \\eta\_k \\nabla{f(x\_k)} \\\\ = A^{T}x + b\[/latex\]

Assuming that ∑λs = 1,

\[latex\]x\_{k+1} - x\* \\\\ = \\sum\_{s=1}^{k}(\\lambda\_s (x\_s - x\*)) \\\\ = \\sum\_{s=1}^{k}(\\lambda\_s A^s) (x\_1 - x\*) \\\\ = P\_{k}(A)(x\_1 - x\*) \\\\ where\\ P\_{k}(A) = \\sum\_{s=1}^{k}(\\lambda\_s A^s)\[/latex\]

Thus given that

\[latex\]||x\_{k+1} - x\*|| = ||P\_{k}(\\mu)||\_A\\ ||x\_1 - x\*||\[/latex\]

P(μ) needs to be chosen over all eigenvalues of A so that (1) Pk(μ) = 1, for μ = 0, and (2) its maximum norm is minimized. This is hard since it requires knowing all eigenvalues but can be achieved, under certain conditions, if the domain is assumed to be continuous \[Kelner09\]. In short, a scaled and shifted [Chebychev polynomial](http://en.wikipedia.org/wiki/Chebyshev_polynomials) is the unique polynomial that minimizes this approximation error. For Chebychev polynomial, the maximum error is

\[latex\]\\max{(||P\_{k}(\\mu)||)} = (\\frac{\\sqrt{Q}-1}{\\sqrt{Q} + 1})^k\[/latex\]

Moreover, since Chebychev polynomials can be expressed recursively, required only the two previously calculated polynomials, the gradient descent update only depends on last two values.

This method seems to be well known in Numerical Analysis, where it is used to speed up iterative linear solvers \[chapter 4, Kincaid02; section 7.4,  Saad11\]. It also seems to predate Nesterov's work \[Manteuffel77\].

**Extension: Bregman Divergence**

Paul Tseng uses [Bregman Divergences](http://mark.reid.name/blog/meet-the-bregman-divergences.html) to give a unified framework for Nesterov's methods across different classes of problems \[Tseng08\].

 

See also \[Taboulle10, Candes11, Hao11, Gordon12\] for a good overview of first-order acceleration methods. Next post discusses techniques that utilize accelerated gradient descent to solve regularization problems \[Nesterov07, Beck09, Becker09\].

**References**

\[[Beck09](http://mechroom.technion.ac.il/~becka/papers/71654.pdf)\] A. Beck, M. Teboulle. A Fast Iterative Shrinkage-Thresholding Algorithm for Linear Inverse Problems. SIAM Journal of Imaging Sciences, 2009 \[[Becker09](http://statweb.stanford.edu/~candes/nesta/NESTA.pdf)\] S. Becker, J. Bobin, E. Candes. NESTA: A Fast and Accurate First-Order Method for Sparse Recovery. Technical Report, Caltech, 2009 \[[Bubeck14](http://www.princeton.edu/~sbubeck/Bubeck14.pdf)\] S. Bubeck, Theory of Convex Optimization for Machine Learning \[[Candes11](http://statweb.stanford.edu/~candes/math301/Lectures/fast_proximal_methods.pdf)\] E. Candes. Math 301, Lectures Notes. \[[Chen11](http://statweb.stanford.edu/~candes/math301/Lectures/acc_nesterov.pdf)\] H. Chen, X. Ming. Accelerating Nesterov's Method for Strongly Convex Functions. Course presentation, 2011. \[[Hardt13](http://mrtz.org/blog/the-zen-of-gradient-descent/)\] M. Hardt. The Zen of Gradient Descent. Blog, 2013. \[[Karniadakis00](http://www.cfm.brown.edu/people/gk/chap7/node21.html)\] G. E. Karniadakis. R. M. Kirby. Parallel Scientific Computing in C++ and MPI. 2000 \[[Kelner09](http://ocw.mit.edu/courses/mathematics/18-409-topics-in-theoretical-computer-science-an-algorithmists-toolkit-fall-2009/lecture-notes/MIT18_409F09_scribe22.pdf)\] J. Kelner. Lecture Notes. MIT 18.409, Lecture 22, 2009 \[[Kincaid02](http://books.google.com/books?id=x69Q226WR8kC&pg=PA224&lpg=PA224&dq=numerical+analysis+chebyshev+acceleration&source=bl&ots=J7e8egYnwg&sig=gT-vbe7DtdAWvcUkFwHEIwNWiZw&hl=en&sa=X&ei=iUHPU5K-GobE8AHgtIHQBQ&ved=0CFoQ6AEwBg#v=onepage&q=numerical%20analysis%20chebyshev%20acceleration&f=false)\] D. R. Kincaid, E. W. Cheney. Numerical Analysis: Mathematics of Scientific Computing. AMS, 2002 \[Nesterov83\] Y. Nesterov. A method for solving a convex programming problem with convergence rate O(1/k2). Dokaldy AN SSR, 1983 \[Nesterov88\] Y. Nesterov. On an approach to the construction of optimal methods of minimization of smooth convex functions. Ekonom. i. Mat. Metody, 1988 \[Nesterov04\] Y. Nesterov. Introductory Lectures On Convex Programming: A Basic Course. Kluwer Academic Publishers, 2004 \[[Nesterov07](http://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=10&cad=rja&uact=8&ved=0CHkQFjAJ&url=http%3A%2F%2Fwww.ecore.be%2FDPs%2Fdp_1191313936.pdf&ei=2PPNU7nmEoaT8QHOioGwCA&usg=AFQjCNFLHxemWvAoXMH4NdWlKh8d9sQOrw&bvm=bv.71198958,d.b2U)\] Y. Nesterov. Gradient methods for minimizing composite objective function. Report, CORE, 2007 \[[Nesterov08](http://galton.uchicago.edu/~lekheng/courses/31060s13/nesterov.pdf)\] Y. Nesterov. How to advance in Structural Convex Optimization. Optima, 2008 \[[Manteuffel77](http://link.springer.com/article/10.1007%2FBF01389971#page-1)\] T. A. Manteuffel. The Tchebyshev iteration for nonsymmetric linear systems. Numer. Math, 1977. \[[Saad11](http://www-users.cs.umn.edu/~saad/eig_book_2ndEd.pdf)\] Y. Saad. Numerical Methods for Large Eigenvalue Problems. SIAM, 2011 \[[Taboulle10](https://www.ipam.ucla.edu/publications/optut/optut_9300.pdf)\]. M. Taboulle. First-Order Methods for Optimization. IPAM Optimization Tutorials, 2010 \[[Gordon12](https://www.cs.cmu.edu/~ggordon/10725-F12/slides/09-acceleration.pdf)\] G. Gordon, R. Tibshirani. Course Notes, 10-725 Optimization, Fall 2012 \[[Tseng08](http://www.mit.edu/~dimitrib/PTseng/papers/apgm.pdf)\] P. Tseng. On Accelerated Proximal Gradient Algorithms for Convex-Concave Optimization. SIAM Journal of Optimization, 2008
