---
title: "First-order methods for regularization"
date: "2014-07-17"
---

General first-order methods use only the values of objective/constraints and their subgradients to optimize non-smooth (including constrained) function \[5\]. Such methods that are oblivious to the structure of the problem are termed _black-box optimization_ techniques.

**Why first-order?**

Primarily because the computation cost, per iteration, of high-accuracy methods like interior-point generally increases non-linearly, which is prohibitively expensive for very large data-sets (> 100K variables).

Though the cost per iteration of general methods is low, these methods cannot exploit the structure of the problem. This post discusses two first-order optimization techniques for regularization problems that can exploit the structure of the problem to give better convergence rate than general methods.

**Regularization**

Regularization problems in statistics and machine learning, or denoting problems in signal processing, take one of the following forms:

\[latex\]\\min{(\\frac{1}{2}||Ax - b||^2 + \\lambda ||x||)}\[/latex\]

 \[latex\]\\min{f(x)} \\\\\[/latex\] \[latex\]|| b - Ax || \\le \\epsilon\[/latex\]

where ||.|| indicates some kind of a norm (or seminorm), e.g. L1 or L2 in statistics and Total Variation norm in imaging, and λ is a real-valued regularization parameter. Optimization of such composite convex functions - often with a smooth component, f(x), and a non-smooth component, g(x) - can be expressed as:

\[latex\]\\min\_{x \\in \\mathbb{R}} {f(x) + \\lambda g(x)}\[/latex\]

Regularization imposes a structure, using a specific norm, on the solution. For example, using L1 norm encourages sparsity, which often results in more noise-tolerant solutions. This was the motivation behind ridge regression and LASSO \[8\] in statistical estimation. Unconstrained regularization problems can be solved using (constrained) linear programming while constrained problems may be solved using second-order cone program. Both programs are variants of interior-points algorithms, and too slow for very large data sets. Various first-order methods may be used to solve regularization problem. In fact, for the specific case of regularization problems, first-order method can be shown to converge to an ε-solution at an optimal rate.

**Complexity**

The complexity of first-order methods is often measured in terms of the number of calls to a first-order oracle (section 5.1 in \[5\]) required to reach an ε-solution. Conversely, the convergence rate gives the approximation error after _t_ iterations. Nemirovski and Yudin's work \[5\] established a lower bound for the information-based complexity of black-box optimization techniques: O(1/ε2) iterations (alternatively, an O(1/√t)-precise solution after t iterations). Furthermore, in many cases, the complexity is still dependent on the dimension, n, of the problem - O(n), in the worst case. For very large dimensional problems, such black-box schemes would converge too slowly.

Nonetheless, there are large classes of problems - including regularization - for which the complexity is O(1/ε) or, in some cases, O(1/√ε) (theorem 3.8 in \[7\], section 5.1 in \[9\] \[10, 12\]). In such cases, the complexity is also either independent of or only sublinearly dependent on problem dimension, making them feasible for very large dimensional but medium-accuracy problems (such as those in machine learning).

Why not use polynomial-time interior-point methods for convex optimization?

While interior-point methods converge rapidly - Ο(e\-Ο(t)) - to an ε-solution, the complexity of each iteration is super-linear - O(nk), k > 1. For very large dimensional problems, these methods are prohibitively expensive.

**Proximal algorithms**

\[latex\]\\min\_{x \\in \\mathbb{R}} {f(x) + \\lambda g(x)}\[/latex\]

For the composite function optimization problem, we assume that:

- the problem has a unique minimizer
- f(x) is β-smooth, convex and differentiable
- g(x) is convex, subdifferentiable and "simple" (to be clarified later)

From the definition of β-smooth convex function, f(x):

\[latex\]f(x\_{k+1}) \\le f(x\_k)+ \\nabla{f(x\_k)}(x\_{k+1} - x\_k) + \\frac{\\beta}{2} ||x\_{k+1} - x\_k||^2\[/latex\]

For subgradient descent from a given a point xk, we need to find the next point xk+1 such that f(xk+1) is minimized i.e \[3\].

\[latex\]x\_{k+1} = \\mathrm{argmin}\_{x \\in \\mathbb{R}} f(x\_k)+ \\nabla{f(x\_k)}(x - x\_k) + \\frac{\\beta}{2} ||x - x\_k||^2\[/latex\]

For the composite function, f(x) + λg(x), this becomes

\[latex\]x\_{k+1} = \\mathrm{argmin}\_{x \\in \\mathbb{R}} \\lambda g(x)+ \\nabla{f(x\_k)}(x - x\_k) + \\frac{\\beta}{2} ||x - x\_k||^2\[/latex\]

which can be reduced to

\[latex\]x\_{k+1} = \\mathrm{argmin}\_{x \\in \\mathbb{R}} \\lambda g(x)+\\frac{\\beta}{2} ||x - (x\_k - \\frac{1}{\\beta}\\nabla{f(x\_k)})||^2\[/latex\]

Despite the fact the each iteration here involves another optimization, a wide class of problems in this form can be solved with _proximal minimization algorithms_ \[13, 14\]_._  Proximal algorithms minimizing a β-smooth function f(x) can in general be represented as:

\[latex\]x\_{k+1} = prox\_{\\eta,f}(x\_k)\[/latex\]

where η = 1/β and the proximity operator, proxηf(y), of a scaled function η f(x) is defined as

\[latex\]\\mathrm{argmin}\_{x \\in \\mathbb{R}} f(x) + \\frac{1}{2\\eta} ||x - y||^2\[/latex\]

For the composite function, f(x) + λg(x), this implies

\[latex\]x\_{k+1} = prox\_{\\lambda\\eta,g(x)}(x\_k - \\frac{1}{2\\eta\\lambda}\\nabla{f(x\_k)})\[/latex\]

Proximal algorithms are useful when the optimization subproblems either admit a closed-form solution or can be rapidly solved numerically. If this is the case, then g(x) is considered "simple." For example, g(x) in regularization problems is an Lp norm, where p = {1, 2, ∞}. Using the calculus of proximity operators, it is possible to evaluate the closed-form solutions for these norm:

\[latex\]prox\_{\\eta,||.||\_{1}}(y)= \\begin{cases} y-\\eta, & y\\ge \\eta\\\\ 0, & |y| < \\eta\\\\ y+\\eta, & y\\le \\eta\\end{cases}\[/latex\]

This operation, for L1 norm, is also called _soft thresholding__._

\[latex\]prox\_{\\eta,||.||\_{2}}(y)= \\begin{cases} (1-\\frac{\\eta}{||y||\_2})y, & ||y||\_2\\ge \\eta\\\\ 0, & ||y||\_2 < \\eta\\end{cases}\[/latex\]

The operation for L2 norm is also called _b__lockwise soft thresholding_.

How rapidly would this scheme converge for composite functions compared to subgradient descent (since g(x) is non-smooth)? Since the proximal algorithm does not need to evaluate the subgradient of g(x) - only the gradient of f(x), which is β-smooth - the convergence rate should the same as that f(x). Following Theorem 3.3 in \[7\], given the minimum x\*,

\[latex\](f(x\_k) + \\lambda g(x\_k)) - (f(x\*) + \\lambda g(x\*)) \\le \\O(1)\\frac{\\beta ||x\_0 - x\*||}{k}\[/latex\]

which is much faster than subgradient descent's O(1/√k). Improving convergence rate to O(1/k2) \[1,2\] will be the topic of our next post.

**L1\-Prox in R**

We give sample code for a basic Iterative Shrinkage-Thresholding Algorithm in R to solve the LASSO problem:

\[latex\]\\min\_{x \\in \\mathbb{R}^n}(\\frac{1}{2}||Ax - b||\_2^2 + \\lambda ||x||\_1)\[/latex\]

which can be reformulated as

\[latex\]x\_{k+1}=prox\_{\\frac{\\lambda}{\\beta},||.||\_{1}}(x\_k- A^T(Ax - b))\[/latex\]

The smoothness parameter, β, can be evaluated directly since

\[latex\]\\beta \\le ||\\nabla^2{f(x)}|| = ||A||\[/latex\]

\[code language="r"\] ## Corrupted signal and model

x <- c(1, 2, 3, 4, 5, 6) t <- seq(from = 0, by = 0.02, to = 2\*pi) A <- cbind(sin(t), sin(2\*t), sin(3\*t), sin(4\*t), sin(5\*t), sin(6\*t)) e <- -4+8\*rnorm(length(t),0,1) #e\[100:115\] <- 30 y <- A %\*% x + e

plot(t, A %\*% x, 'l', col='blue', ylab = "signal", xlab = "time") lines(t, y)

\## Proximal algorithm for l1

\## parameters iter <- 1000 beta <- norm(A, type="F")^2 eta <- 1 / beta lambda <- 1 xx <- c(0, 0, 0, 0, 0, 0)

\## main loop for (i in 1:iter) { gradient\_x <- t(A) %\*% ( A %\*% xx - y ) xx\_tmp <- xx - eta \* gradient\_x v <- eta \* lambda # L1 prox/shrinkage-thresholding xx <- pmax(xx\_tmp - v, 0) - pmax(- xx\_tmp - v, 0) }

xx

lines(t, A %\*% xx, col="red")

legend("topright", legend=c("original signal", "corrupted signal", "recovered signal"), col=c("black", "blue", "red"), lwd=c(1, 1, 1)) \[/code\]

 

\[caption id="attachment\_736" align="aligncenter" width="300"\][![Signal recovery using L1-Prox](http://umayrh.files.wordpress.com/2014/07/l1prox1.png?w=300)](http://umayrh.files.wordpress.com/2014/07/l1prox1.png) Signal recovery using L1-Prox\[/caption\]

**References**

[\[1\]](http://mechroom.technion.ac.il/~becka/papers/71654.pdf) A. Beck, M. Teboulle. A Fast Iterative Shrinkage-Thresholding Algorithm for Linear Inverse Problems. SIAM J. Imaging Sciences, 2009 [\[2\]](http://statweb.stanford.edu/~candes/nesta/NESTA.pdf) S. Becker, J. Bobin, E. Candes. NESTA: A Fast and Accurate First-Order Method for Sparse Recovery. Technical Report, Caltech, 2009 [\[3\]](http://blogs.princeton.edu/imabandit/2013/04/11/orf523-ista-and-fista/) http://blogs.princeton.edu/imabandit/2013/04/11/orf523-ista-and-fista/ [\[4\]](https://www.ipam.ucla.edu/publications/optut/optut_9300.pdf) M. Teboulle. First-Order Algorithms for Convex Optimization. IPAM, 2010 [\[5\]](http://www2.isye.gatech.edu/~nemirovs/Lect_ModConvOpt.pdf) A. Ben-Tal, A. Nemirovski. Lectures on Modern Convex Optimization. Lecture Notes, 2013 [\[6\]](http://en.wikipedia.org/wiki/Subgradient#The_subgradient) http://en.wikipedia.org/wiki/Subgradient#The\_subgradient [\[7\]](http://www.princeton.edu/~sbubeck/Bubeck14.pdf) S. Bubeck. Theory of Convex Optimization for Machine Learning. Lecture Notes, 2014 \[8\] T. Hastie, R. Tibshirani, J. Friedman. Elements of Statistical Learning. Second Edition. [\[9\]](http://www.ecore.be/DPs/dp_1329823186.pdf) Y. Nesterov. Subgradient Methods for Huge-Scale Optimization Problems. ECORE Discussion Paper, 2012 [\[10\]](http://www2.isye.gatech.edu/~nemirovs/MLOptChapterI.pdf) A. Juditsky, A. Nemirovski. First-Order Methods for Non-Smooth Convex Large-Scale Optimization, 1. Optimization for Machine Learning, \[11\] J. F. Bonnan, J. C. Gilbert, C. Lemaréchal, C. A. Sagastizábal. Numerical Optimization: Theoretical and Practical Aspects. Second Edition, Springer, 2006 [\[12\]](https://iew3.technion.ac.il/Home/Users/becka/smoothing.pdf) A. Beck, M. Teboulle. Smoothing and First Order Methods: A Unified Framework. SIAM Journal Of Optimization, 2012 [\[13\]](http://web.stanford.edu/~boyd/papers/prox_algs.html) N. Parrikh, S. Boyd. Proximal Algorithms. Foundations and Trends in Optimization, 2014 [\[14\]](http://www.ljll.math.upmc.fr/~plc/mms1.pdf) P. L. Combette, V. R. Wajs. Signal Recovery by Proximal Forward-Backward Splitting. Multiscale Modeling and Simulation, 2005
