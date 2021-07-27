---
layout: post
title: "Continued Fractions"
date: "2015-05-26"
description:
image: /assets/images/fareypoincaremovie.gif
author: Umayr Hassan
tags:
- Linear Algebra
- Mathematics
- Number Theory
---

![fareypoincaremovie](https://umayrh.files.wordpress.com/2015/05/fareypoincaremovie.gif) 

[Farey tessellation](http://www-bcf.usc.edu/~fbonahon/STML49/FareyFord.html) in complex plane.

In fact, the matrix formulation of Fibonacci numbers mentioned 
[before](https://umayrh.wordpress.com/2014/08/08/jiri-matousek-miniatures-fibonacci-numbers/ "Fibonacci Numbers") 
can be generalized to represent any 2-D matrix and, by way of continued fractions, any real number - not 
just the golden mean. This post is inspired from David Austin's AMS 
[feature column](http://www.ams.org/samplings/feature-column/fcarc-stern-brocot) and draws on Phillipe Flajolet's 
beautiful [survey](http://www.lix.polytechnique.fr/Labo/Ilan.Vardi/continued_fractions.ps) on continued fractions 
to set the stage for a future post on [Diophantine approximation](http://en.wikipedia.org/wiki/Diophantine_approximation).

"_Every matrix A of determinant one with non-negative integer coefficients will have a unique factorization...
corresponding to a continued fraction expansion._" [1]

$A = \left[ \begin{array}{cc} a & b \\ c & d \end{array} \right] = U^{\alpha_0}L^{\alpha_1}U^{\alpha_2}L^{\alpha_3}... where U = \left[ \begin{array}{cc} 1 & 1 \\ 0 & 1 \end{array} \right], L = \left[ \begin{array}{cc} 1 & 0 \\ 1 & 1 \end{array} \right\], and \frac{b}{d} = a_0 + \cfrac{1}{a_1 + \cfrac{1}{a_2 + \cfrac{1}{a_3 + \dotsb}}}$

The Fibonacci numbers can be derived as

\[latex\]\\left\[ \\begin{array}{cc} F\_{n-1} & F\_{n} \\\\ F\_{n-2} & F\_{n-1} \\end{array} \\right\] = ULULUL... \\\\ \\phi(n) = \\frac{F\_{n}}{F\_{n-1}} = 1 + \\cfrac{1}{1 + \\cfrac{1}{1 + \\cfrac{1}{1 + \\dotsb}}}\[/latex\]

Of course, both the factorization series and the fraction terminate for finite values of n i.e. rational approximations 
of the golden mean. Already this formulation can be used to calculate the n-th Fibonacci number in log(n) steps.

While "every positive rational number appears in the Stern-Brocot tree," \[2\] such factorization also represents a 
specific path in the tree whose nodes provide the _optimal_ rational approximation to a real number within a given error.

**References**

* [1] P. Flajolet, B. Vallee, and I. Vardi. Continued fractions from Euclid to present day. Preprint, March 2000. 
* [2] D. Austin. Trees, Teeth, and Time: The mathematics of clock making. AMS Feature Column, December 2008.
