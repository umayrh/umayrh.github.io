---
title: "α-strong convexity, β-strong smoothness"
date: "2014-07-09"
---

Strong convexity often allows for optimization algorithms that converge very quickly to an ε-optimum (rf. [FISTA](http://mechroom.technion.ac.il/~becka/papers/71654.pdf) and [NESTA](http://statweb.stanford.edu/~candes/nesta/NESTA.pdf)). This post will cover some fundamentals of strongly convex functions.

**Convexity**

For a convex function, \[latex\]f: \\mathbf{R} \\to \\mathbf{R}\[/latex\] and \[latex\]\\forall \\gamma \\in \[0, 1\]\[/latex\],

\[latex\] f(tx + (1-t)y) \\le tf(x) + (1-t)f(y) \[/latex\]

**Strict convexity**:

For a _strictly_ convex function,

\[latex\] f(tx + (1-t)y) < tf(x) + (1-t)f(y) \[/latex\]

Geometrically, the definition of convexity implies that all points on any straight line connecting any two points in a convex set also lie in the set. Strict convexity excludes linear and affine functions, or functions with linear/affine subsets in their boundaries. (How would this extend to non-Euclidean geometries?)

**α-strong convexity**

A function is α-strongly convex with respect to a norm ||.|| if, for α > 0

\[latex\]f(tx + (1-t)y) < tf(x) + (1-t)f(y) - \\frac{\\alpha}{2} ||x - y||^2\[/latex\]

Alternatively,

\[latex\](\\nabla{f(x)} - \\nabla{f(y)})(x - y) \\ge \\alpha ||x - y||^2\[/latex\]

or,

\[latex\]f(y) \\ge f(x)+ \\nabla{f(x)}(y - x) + \\frac{\\alpha}{2} ||x - y||^2\[/latex\]

Strongly convexity extends strict convexity. For twice-differentiable functions, this implies that \[latex\]\\nabla^2f(x) \\ge \\alpha\[/latex\]. As Bubeck explains \[1\], strongly convex functions speed up convergence of first-order methods. Larger values of α imply larger gradient, and hence step size, when further away from the optimum.

Strong smoothness is another property of certain convex functions:

**β-smoothness**

A function is β-smoothly convex with respect to a norm ||.|| if, for β > 0, \[4\]

\[latex\]f(y) \\le f(x)+ \\nabla{f(x)}(y - x) + \\frac{\\beta}{2} ||x - y||^2\[/latex\]

This definition gives an lower bound on the improvement in one step of (sub)gradient descent \[1\]:

\[latex\]f(x - \\frac{1}{\\beta}\\nabla{f(x)})- f(x) \\le \\frac{-1}{2 \\beta} || \\nabla{f(x)} ||^2\[/latex\]

Alternatively, β-smoothly convex function \[lemma 3.3, 1\]:

\[latex\]f(x) - f(y) \\le \\nabla{f(x)}(y - x) - \\frac{1}{2 \\beta} ||\\nabla{f(x)} - \\nabla{f(y)}||^2\[/latex\]

**Strong/smooth duality**

Under certain conditions, a-strong convexity and β-smoothness are dual notions. For now, we'll state the result without discussion.

If _f_ is a closed and convex function, then f is α-strongly convex function with respect to a norm ||.|| if and only if _f\*_ is 1/α-strongly smooth with respect to the dual norm ||.||\* (corollary 7 in \[4\]).

References:

[\[1\]](http://www.princeton.edu/~sbubeck/Bubeck14.pdf) S. Bubeck, Theory of Convex Optimization for Machine Learning, section 3.4 [\[2\]](http://en.wikipedia.org/wiki/Convex_function#Strongly_convex_functions) Wikipedia, Convex function [](http://en.wikipedia.org/wiki/Convex_function#Strongly_convex_functions) [\[3\]](http://web.stanford.edu/~boyd/cvxbook/) S. Boyd and G. Vandenberghe, Convex Optimization, section 9.1.2 [\[4\]](http://ttic.uchicago.edu/~shai/papers/KakadeShalevTewari09.pdf) S. M. Kakade, S. Shalev-Schwartz, A. Tiwari. On the duality of strong convexity and strong smoothness: Learning applications and matrix regularization. Technical Report, 2009
