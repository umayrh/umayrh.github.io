---
title: "Accelerated first-order methods for regularization"
date: "2014-07-24"
---

**FISTA**

Fast Iterative Shrinkage-Thresholding Algorithm (FISTA) \[Beck09\] was one of the first algorithms to use Nesterov's accelerated gradient descent to speed up the convergence of iterative shrinkage-thresholding (ISTA) from O(β/ε) to O(√(β/ε)).  The algorithm and proof sketch for in the previous post based were based on \[Bubeck14\] and \[Beck09\] so we'll skip them. Instead give an R implementation and results that compares ISTA and FISTA performance (in terms of number of iterations) for various step sizes (iterations increase and step size decreases).

\[code language="r" wraplines="false" collapse="true"\]

\## ISTA ista <- function(A, x, beta, iter = 100) { ## parameters eta <- 1 / beta lambda <- 1 xx <- c(0, 0, 0, 0, 0, 0) ## helpers Axx <- A %\*% x Axn <- norm(A %\*% x) error <- vector(length = iter) iters <- vector(length = iter)

\## main loop for (i in 1:iter) { gradient\_x <- t(A) %\*% ( A %\*% xx - y ) xx\_tmp <- xx - eta \* gradient\_x v <- eta \* lambda # L1 prox/shrinkage-thresholding xx <- pmax(xx\_tmp - v, 0) - pmax(- xx\_tmp - v, 0) error\[i\] <- norm(A %\*% xx - Axx) / Axn iters\[i\] <- i }

return(list(xx, error, iters)) }

\## FISTA fista <- function(A, x, beta, iter = 100) { ## parameters eta <- 1 / beta lambda <- 1 x\_next <- c(0, 0, 0, 0, 0, 0) z\_prev <- x\_next z\_next <- x\_next mu\_prev <- 0 mu\_next <- 0 ## helpers Ax <- A %\*% x Ax\_norm <- norm(Ax) error <- vector(length = iter) iters <- vector(length = iter)

\## main loop for (i in 1:iter) { mu\_next <- 0.5 \* (1 + sqrt(1 + 4 \* mu\_prev^2)) gamma <- (1 - mu\_prev) / mu\_next gradient\_x\_n <- t(A) %\*% ( A %\*% x\_next - y ) z\_tmp <- x\_next - eta \* gradient\_x\_n v <- eta \* lambda # L1 prox/shrinkage-thresholding z\_next <- pmax(z\_tmp - v, 0) - pmax(- z\_tmp - v, 0) x\_next <- z\_next + gamma \* (z\_prev - z\_next) z\_prev <- z\_next mu\_prev <- mu\_next error\[i\] <- norm(A %\*% x\_next - Ax) / Ax\_norm iters\[i\] <- i }

return(list(x\_next, error, iters)) }

max\_iter <- 500; beta <- norm(A, type="F")^2

res1 <- ista(A, x, beta, max\_iter) res2 <- fista(A, x, beta, max\_iter)

plot(res1\[\[3\]\], res1\[\[2\]\], col = "red", xlab = "iterations", ylab = "error", type = 'l', lty = 1) lines(res2\[\[3\]\], res2\[\[2\]\], col = "blue", type = 'l', lty = 1) \[/code\]

\[caption id="attachment\_868" align="aligncenter" width="300" class=" "\][![ISTA vs FISTA, 500 iterations](http://umayrh.files.wordpress.com/2014/07/ista500.png?w=300)](https://umayrh.files.wordpress.com/2014/07/ista500.png) ISTA vs FISTA, 500 iterations\[/caption\]

\[caption id="attachment\_869" align="aligncenter" width="300" class=" "\][![ISTA vs FISTA, 5000 iterations](http://umayrh.files.wordpress.com/2014/07/ista5000.png?w=300)](https://umayrh.files.wordpress.com/2014/07/ista5000.png) ISTA vs FISTA, 5000 iterations\[/caption\]

Note the non-monotonic convergence in FISTA's case - \[Beck09b, Teboulle10\] describe a simple change to the algorithm to fix that. Another interesting problem with FISTA is the dependence on the worst-case smoothness parameter β in the algorithm, which can substantially reduce convergence rate for large β. This is addressed in \[Katya14\] using a backtracking strategy that  to improve the dependence from worst-case to average "local composite Lipschitz constant for ∇f", which can have a much smaller value, implying a larger step size. Another solution is presented in \[Baes12\].

Recall that ISTA, and hence FISTA, solve the following optimization problems:

\[latex\]\\min{(\\frac{1}{2}||Ax - b||^2 + \\lambda ||x||)}\[/latex\]

FISTA works for non-smooth objectives as long as they can be formulated as the sum of a smooth and a non-smooth function \[Section 2, Beck09; Section 3, Tseng08\]. Are there efficient first-order algorithms for a larger class of non-smooth problems?

**NESTA**

NESTA \[Becker09\] solves the following optimization problem that FISTA cannot since the objective function is non-composite non-smooth:

\[latex\]\\min{||x||\_1} \\\\ ||Ax - b||\_2 \\le \\epsilon\[/latex\]

This algorithm draws on \[Nesterov05\], which introduces a way to create smooth approximations of certain non-smooth function. This approximation, coupled with an accelerated gradient descent method, gives good convergence for optimizing a large class of non-smooth functions (though not as good as FISTA or Nesterov07). We'll review \[Nesterov05\] via \[Becker09\] before returning to NESTA.

_Smoothing non-smooth functions_

Nesterov considers a class of functions that can be represented as \[Section 2, Nesterov05; Section 2, Becker09; Nesterov08\]

\[latex\]\\max\_{u \\in Q\_d}\\{(Ax - b)^{T}u - \\phi{(u)}\\}\[/latex\]

where φ(u) is a convex function and Qd is the dual domain (convex, closed) of a function f(x) minimized over a domain Qp. The representation is similar to the [convex conjugate](http://en.wikipedia.org/wiki/Convex_conjugate), f\*(u), of this function. Indeed Nesterov considers using φ(u) = f\*(u), but then argues that such φ(u) might not be a simple enough function. This might be true in general, but as we'll see that in the special case of L1 regularization, φ(u) = f\*(u) will work.

Nesterov then presents a smooth approximation of the function

\[latex\]f\_{\\mu}(x) = \\max\_{u \\in Q\_d}\\{(Ax - b)^{T}u - \\phi{(u)} - \\mu\\ prox\_{\\alpha}(u)\\}\[/latex\]

where μ is the smoothness parameter, proxα(u) is an α-strongly convex proximal function over the dual domain Qd. In the original paper \[Nesterov05\], Nesterov actually allows for a composite function approximation, that is f(x) = fº(x) + fμ(x) where fº(x) is some smooth convex function. We'll follow \[Becker09\] in assuming that fº(x) = 0. \[Beck12\] extends this smoothing framework to optimize an even broader class of non-smooth functions.

The important upshot is that (1) this function is smooth with factor \[latex\]\\frac{||A||}{\\mu \\alpha}\[/latex\] \[Theorem 1, Nesterov05\], where ||A|| is the operator norm of A, and (2) that fμ(x) is a uniform smooth approximation of f(x) for μ > 0. So now we can use an optimal gradient descent for smooth optimization to minimize fμ(x).

_Smooth optimization_

Section 3 in \[Nesterov05\] describes an optimal first-order algorithm to minimize a β-smooth convex function f(x) using an α-strongly convex proximal function proxα(x).

\[latex\]y\_k = \\mathrm{argmin}\_{y}(\\nabla{f(x\_k)}^{T} + \\frac{1}{2}\\beta ||y - x\_k||^2)) \\\\ z\_k=\\mathrm{argmin}\_{x}(\\frac{\\beta}{\\alpha}\\ prox\_{\\alpha}(x)+\\sum\_{i=0}^{k}(\\frac{i+1}{2}\[\\nabla{f(x\_i)} + \\nabla{f(x\_i)}^{T}(x - x\_i)\])) \\\\ x\_{k+1} = \\frac{2}{k+1}z\_k + \\frac{k+1}{k+3}y\_k\[/latex\]

This is shown \[Theorem 2, Nesterov05\] to converge at a rate

\[latex\]f(y\_k) - f(x\*) \\le \\frac{4 \\beta\\ prox\_{\\alpha}(x\*)}{\\alpha (k+1)(k+2)}\[/latex\]

This algorithm is more complicated than the earlier ones \[Nesterov83, Nesterov88\] - especially when compared to the version FISTA uses (see [previous post](http://umayrh.wordpress.com/2014/07/21/accelerating-first-order-methods/#more-756)). The only advantage seems to be a somewhat better convergence rate by a factor of α at the cost of solving two minimization problems (instead of one as in FISTA).

Applying this algorithm to optimize  fμ(x) \[Theorem 3, Nesterov05\] gives O(1/k) convergence for the optimal value of μ since

\[latex\]f(y\_k) - f(x\*) \\le O(1)\\mu + \\frac{O(1)}{\\mu (k+1)^2} + \\frac{O(1)}{(k+1)^2}\[/latex\]

_Nesterov's Algorithm_

\[Becker09\] call their application of \[Nesterov05\] to L1 regularization, NESTA. They show that

1\. Optimizing over the convex conjugate of L1 norm when combined with an appropriate proximal function yields a simple, well-known function called the [_Huber loss_](http://en.wikipedia.org/wiki/Huber_loss_function) function. It is analytic and can be computed very efficiently, which makes it trivial to implement gradient descent (quite like the shrinkage-thresholding operation in ISTA/FISTA).

\[latex\]||x||\_1 = \\max\_{u \\in Q\_d}{(u^{T}x)} \\\\ where\\ Q\_d = {u : ||u||\_{\\infty} \\le 1}\[/latex\]

Choosing proxα(u) to be the squared Euclidean distance with α = 1 (see also \[cf. Section 4.2, Nesterov05\]),

\[latex\]f\_{\\mu}(x) = \\max\_{u \\in Q\_d}\\{(u^{T}x) - \\frac{\\mu}{2}||u||\_2^2\\} = \\begin{cases}\\frac{x^2}{2\\mu}, & |x| < \\mu \\\\ |x| - \\frac{\\mu}{2}, & otherwise\\end{cases} \\\\ \\nabla{f(x)\[i\]} = \\begin{cases}\\frac{x\[i\]}{\\mu}, & |x\[i\]| < \\mu \\\\ sgn(x\[i\]), &otherwise\\end{cases}\[/latex\]

2\. yk and zk updates in the original algorithm need to be modified taking into account the L2 constraint. This is done using the Lagrangian form of each minimization that, under certain assumptions, allows expressing Lagrangian variables in terms of ε.

3\. Another interesting idea is the use of "continuation." Starting with a large value of the smoothing parameter and multiplicatively decreasing after each iteration helps converge more quickly (although the theoretical convergence rate stays the same).

Here's R code for the unconstrained version of NESTA based on FISTA's version of accelerated gradient descent:

\[code language="r" wraplines="false" collapse="true"\] nesta <- function(A, x, beta, iter = 100) { ## parameters eta <- 1 / beta lambda <- 1 x\_next <- c(0, 0, 0, 0, 0, 0) z\_prev <- x\_next z\_next <- x\_next mu\_prev <- 0 mu\_next <- 0 smooth <- 0.9 \* norm(A, &quot;I&quot;) ## helpers Ax <- A %\*% x Ax\_norm <- norm(Ax) error <- vector(length = iter) iters <- vector(length = iter)

\## main loop for (i in 1:iter) { mu\_next <- 0.5 \* (1 + sqrt(1 + 4 \* mu\_prev^2)) gamma <- (1 - mu\_prev) / mu\_next # grad\_huber1 + grad\_huber2 = gradient of Huber function   grad\_huber1 <- (abs(x\_next) &lt; smooth) / smooth grad\_huber2 <- (abs(x\_next) &gt;= smooth) \* ifelse(x\_next==0, 0, (x\_next / abs(x\_next))) gradient\_x\_n <- t(A) %\*% ( A %\*% x\_next - y ) + grad\_huber1 + grad\_huber2 z\_next <- x\_next - eta \* gradient\_x\_n x\_next <- z\_next + gamma \* (z\_prev - z\_next) z\_prev <- z\_next mu\_prev <- mu\_next smooth <- 0.5 \* smooth error\[i\] <- norm(A %\*% x\_next - Ax) / Ax\_norm iters\[i\] <- i }

return(list(x\_next, error, iters)) } \[/code\]

**Postscript \[02/09/2014\]**

Building on the message-passing (aka belief propagation) algorithms introduced in \[Donoho09\], \[Mousavi13\] improve the convergence rate of iterative-shrinkage thresholding algorithm (ISTA) to O(e\-t). This was made possible by finding the optimal values of the regularization parameter, λ, at each iteration under the assumption of Gaussian error distribution (which is probably why it escapes the upper-bounds for first-order methods).

**References** \[[Baes12](http://arxiv.org/pdf/1207.3951v1.pdf)\] M. Baes, M. Burgisser. An acceleration procedure for optimal first-order methods. 2012 \[[Beck09](http://mechroom.technion.ac.il/~becka/papers/71654.pdf)\] A. Beck, M. Teboulle. A Fast Iterative Shrinkage-Thresholding Algorithm for Linear Inverse Problems. SIAM Journal of Imaging Sciences, 2009 \[[Beck09b](http://www.math.tau.ac.il/~teboulle/papers/tlv.pdf)\] A. Beck, M. Teboulle. Fast Gradient-Based Algorithms for Constrained Total Variation Image Denoising and Deblurring Problems. IEEE Transactions on Image Processing, 2009. \[[Beck12](https://iew3.technion.ac.il/Home/Users/becka/smoothing.pdf)\] A. Beck, M. Teboulle. Smoothing and First Order Methods: A Unified Approach. SIAM Journal of Optimization, 2012. \[[Becker09](http://statweb.stanford.edu/~candes/nesta/NESTA.pdf)\] S. Becker, J. Bobin, E. Candes. NESTA: A Fast and Accurate First-Order Method for Sparse Recovery. Technical Report, Caltech, 2009 \[[Bubeck14](http://www.princeton.edu/~sbubeck/Bubeck14.pdf)\] S. Bubeck, Theory of Convex Optimization for Machine Learning \[[Donoho09](http://www.ece.rice.edu/~mam15/amp_pnas.pdf)\] D. Donoho, A. Maleki, A. Montanari. Message-passing algorithms for compressed sensing. Proceedings of National Academy of Sciences, 2009. \[[Mousavi13](http://arxiv.org/pdf/1311.0035v1.pdf)\] A. Mousavi, A. Maleki, R. Baranuick. Parameterless optimal approximate message passing. CoRR, 2013 \[Nesterov83\] Y. Nesterov. A method for solving a convex programming problem with convergence rate O(1/k2). Dokaldy AN SSR, 1983 \[Nesterov88\] Y. Nesterov. On an approach to the construction of optimal methods of minimization of smooth convex functions. Ekonom. i. Mat. Metody, 1988 \[Nesterov04\] Y. Nesterov. Introductory Lectures On Convex Programming: A Basic Course. Kluwer Academic Publishers, 2004 \[[Nesterov05](http://luthuli.cs.uiuc.edu/~daf/courses/Optimization/MRFpapers/nesterov05.pdf)\] Y. Nesterov. Smooth minimization of non-smooth functions. Mathematical Programming, 2005. \[[Nesterov07](http://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=10&cad=rja&uact=8&ved=0CHkQFjAJ&url=http%3A%2F%2Fwww.ecore.be%2FDPs%2Fdp_1191313936.pdf&ei=2PPNU7nmEoaT8QHOioGwCA&usg=AFQjCNFLHxemWvAoXMH4NdWlKh8d9sQOrw&bvm=bv.71198958,d.b2U)\] Y. Nesterov. Gradient methods for minimizing composite objective function. Report, CORE, 2007 \[[Nesterov08](http://galton.uchicago.edu/~lekheng/courses/31060s13/nesterov.pdf)\] Y. Nesterov. How to advance in Structural Convex Optimization. Optima, 2008 \[[Katya14](http://www.optimization-online.org/DB_FILE/2011/04/3004.pdf)\] K. Scheinberg, D. Goldfarb, X. Bai. Fast First-Order Methods for Composite Convex Optimization with Backtracking. Foundations of Computational Mathematics, 2014. \[[Teboulle10](https://www.ipam.ucla.edu/publications/optut/optut_9300.pdf)\]. M. Teboulle. First-Order Methods for Optimization. IPAM Optimization Tutorials, 2010 \[[Tseng08](http://www.mit.edu/~dimitrib/PTseng/papers/apgm.pdf)\] P. Tseng. On Accelerated Proximal Gradient Algorithms for Convex-Concave Optimization. SIAM Journal of Optimization, 2008

**Implementations**

\[1\] http://web.stanford.edu/~boyd/papers/prox\_algs/lasso.html \[2\] https://github.com/gpeyre/numerical-tours/tree/master/matlab/solutions \[3\] https://www.ceremade.dauphine.fr/~peyre/numerical-tour/tours/optim\_4\_fb/#54 \[4\] http://www.caam.rice.edu/~optimization/disparse/LASSO/FISTA/pFistaLasso.html \[5\] http://www.mathworks.com/matlabcentral/fileexchange/16204-toolbox-sparse-optmization/content/toolbox\_optim/perform\_fb.m \[6\] http://slipguru.disi.unige.it/Software/L1L2Py/algorithms.html \[7\] http://www.eecs.berkeley.edu/~yang/software/l1benchmark/ \[8\] http://www.ece.rice.edu/~tag7/Tom\_Goldstein/CGIST.html \[9\] http://users.ece.gatech.edu/%7Esasif/index.html \[10\] https://www.ceremade.dauphine.fr/~peyre/numerical-tour/ \[11\] http://www.mit.edu/~dimitrib/PTseng/papers/apg\_alg.m \[12\] http://www.ece.rice.edu/~tag7/Tom\_Goldstein/CGIST.html \[13\] http://www.math.ucla.edu/~wotaoyin/software.html
