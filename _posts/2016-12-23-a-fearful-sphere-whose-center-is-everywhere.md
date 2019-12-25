---
layout: post
title: "A fearful sphere, whose center is everywhere"
date: "2016-12-23"
description:
image: /assets/images/screen-shot-2016-12-16-at-10-28-14-pm.png
author: Umayr Hassan
tags: Convex geometry, Convex optimization, First-order methods, Linear Algebra, Linear programming, Linear systems, Mathematics
---

In a beautiful article, _The Mathematics of Doodling_, [Ravi Vakil](http://math.stanford.edu/~vakil/) poses two problems:

1. Given a random curve, like a doodle, in what sense do closed curves successively drawn around the doodle become more and 
more circular?
2. How do geometric invariants, like area and volume, of the closed curves relate with the original shape and with each other?

![p2e](/assets/images/p2e.gif)

The first question, at least, seemed connected to another interesting paper, _Random polygon to ellipse: an eigenanalysis._ 
Here Elmachtoub and [van Loan](http://www.cs.cornell.edu/cv/) ask:

1. If we start with a random polygon and then create a succession of polygons by averaging the vertices of the previous polygon, 
do the polygons converge to something structured?
2. How is this structure related to the initial configuration of the polygon?

![loan](/assets/images/loan.png)

One uses geometry, and the other uses matrix analysis to investigate these question but both seems to ask how certain iterations of a geometric body lead to another.

Let's pose two related problems, and explore two methods that may give insight:

1. What kind of iterations transform bounded [convex polyhedra](https://en.wikipedia.org/wiki/Convex_polytope) to a circle?
2. How are the [centroid](https://en.wikipedia.org/wiki/Centroid)s of the polyhedra related to the center of the circle?

**Cimmino's algorithm**

Gianfranco Cimmino [Benzi04] gave a unique method for solving a system of linear equations (**A . x = b **i.e. **ai** **.** **x** = **b**, i = 1, 2...m). To find the point of intersection of _m_ n-dimensional hyperplanes:

- start with an arbitrary point _y_
- find the mirror image of _y_ with respect to each hyperplane to yield _m_ projected points
- calculate a weighted centroid of these m points
- use the centroid as the new iterate

![screen-shot-2016-12-19-at-4-24-05-pm](https://umayrh.files.wordpress.com/2016/12/screen-shot-2016-12-19-at-4-24-05-pm.png) 

Cimmino iteration \[[ref](http://www.dm.unibo.it/~simoncin/cimmino.pdf)\]\[/caption\]How do we find the mirror image? 
Given a hyperplane **ai**, its unit normal vector is **\-** **ai **/ **|a|**, where **||** denotes the Euclidean norm. 
The distance between y and **ai** is **a****i** **.** **y** - **b**, hence the distance between y and its reflection 
must be twice that. The unit normal vector transports y over a distance 
2 (**ai** **.** **y** **\-** **b**) to y + 2 (**b - ****ai .** **y** **) **ai **/** |a|****. Note that we need to choose 
the negative unit normal vector, instead of positive, to reflect the point around the hyperplane.

The centroid is the average of these reflected point: 2/_m_ **∑ (**b - ****ai .** **y** **) **ai **/** |a|******. 
The weighted centroid for arbitrary normalized weights _wi_ would be: 
2 **∑** wi** (**b - ****ai .** **y** **) **ai **/** |a|**,**** where ∑_wi_ = 1.

In fact, this weighted centroid is only an [approximation](http://math.stackexchange.com/questions/3177/why-doesnt-a-simple-mean-give-the-position-of-a-centroid-in-a-polygon) to the true geometric centroid of the polygon since the entire mass of the initial polygon isn't concentrated in its vertices (otherwise, a single iteration would suffice). Successive iterations improve this approximation by shrinking the polygon to a point, which is the geometric centroid.

[Censor](http://math.haifa.ac.il/yair/) and Elving extend Cimmino's algorithm to a system of linear inequalities (Ax <= B) 
to fin one solution (i.e. a feasible point) \[Censor83\]. The calculation of the weighted centroid was all they needed to 
revamp, so the geometry of the algorithm remains unchanged.  To prove convergence, the authors appeal to the Fejér montonicity 
\[Combettes00\] of iterates (cf. with [contraction mapping](https://en.wikipedia.org/wiki/Banach_fixed-point_theorem) for 
Banach space). A sufficient condition for convergence in their scheme is that _wi_ > 0.

How does this relate to [Elmachtoub07]?

- In both Cimmino's algorithm and \[Elmachtoub07\]'s Algorithm 1, the centroid is fixed but unknown. They both construct 
successive polytopes that converge to the centroid.
- Both Cimmino's algorithm and Algorithm 1 converge to the centroid of the initial polygon.
- Cimmino's polygon, far from being random, is specifically constructed using orthogonal projections of a given point. The 
constructed polygon is [cyclic](http://mathworld.wolfram.com/CyclicPolygon.html), and its 
[circumcenter](https://en.wikipedia.org/wiki/Circumscribed_circle) and centroid coincide.
- How do normalized iterates affect convergence? If iterates in Cimmino's algorithm are normalized such that the circumcenter 
doesn't change, then they oscillate on a limit cycle.

**von Neumann's algorithm**

An early algorithm for solving a system of linear inequalities, **A . x <= b**, comes from 
[John von Neumann](https://en.wikipedia.org/wiki/John_von_Neumann) via 
[George Dantzig](https://en.wikipedia.org/wiki/George_Dantzig) \[Dantzig92\].

![screen-shot-2016-12-20-at-12-00-24-am](/assets/images/screen-shot-2016-12-20-at-12-00-24-am.png)

We start with a "standardized" polyhedron:

- Transform **A . x** **<= b** into **∑ Pj . x** \= **0**, **∑ x = 1**, **x >= 0** such that **|****Pj|** \= 1. Thus the 
  points **Pj** lie on a hypersphere of radius 1 and center 0.
- Select an arbitrary initial point **x** >= 0, such that ∑x = 1. This also corresponds to selecting an arbitrary point P1 on 
  the hyper-sphere and considered as iterate A1.
- The aim is to set the next iterate At so that the distance, ut, to origin is minimized. This is done by choosing the 
  hypersphere point, Ps, that minimizes the angle, φ, subtended by At-1 and Ps. Then At is point on the line At-1 - Ps and its normal that passes through origin.
- Iterate till ut decreases to ε.

Unlike Cimmino or Algorithm 1, the centroid is fixed and known as the circumcenter of given points. What the algorithm 
discovers, while solving the feasibility problem, is the polygon whose weighted point-mass centroid would be this circumcenter. 
Note that since the algorithm projects points to minimize distance to the center, the number of iterations is independent of 
the dimensions of the problem (unlike Cimmino). If iterates are normalized, then they are always projected on to the 
hypersphere, which would be their limit cycle.

**Fourier polygons**

Why does a general polygon, under averaging iterations, converge to an ellipse and not to a circle? The short answer is 
that the [affine transformation](https://www.cs.mtu.edu/~shene/COURSES/cs3621/NOTES/geometry/geo-tran.html#affine) of a 
circle is an ellipse.

![screen-shot-2016-12-22-at-3-18-44-pm](/assets/images/screen-shot-2016-12-22-at-3-18-44-pm.png) 

A compelling reinterpretation of the discrete Fourier transform \[Glassner99\] argues that the transform matrix can be
viewed as regular polygons in the complex plane. As a corollary, any polygon can be decomposed into a weighted sum 
(an affine transformation) of regular polygons.

$P = \dfrac1n \sum\limits_{k=0}^{n-1} X_k P_k$

In any such transformation, three regular polygons are important: $P_0$, $P_1$, and $P_{n-1}$. $P_0$ corresponds on a point, but 
the other two are [regular, convex n-gons](https://en.wikipedia.org/wiki/Regular_polygon). The averaging transformation can 
now be applied to this sum of regular polygons. As David Radcliffe 
[argues](https://mathblag.wordpress.com/2013/10/08/inscribed-polygons-and-the-fourier-transform/), all component polygons 
shrink (except for $P_0$) but $P_1$ and $P_{n-1}$ shrink at the slowest rate leaving us with an affine combination of the 
three shrunk regular polygons converging to an ellipse. While [Elmachtoub07] uses a rather involved matrix analysis 
to converge a random polygon to an ellipse, Radcliffe's argument is far more intuitive (and not too far from quantization).

**Steiner, Weyl, Minkowski**

We return to Vakil's problem. For parallel bodies drawn around a non-empty convex body, $K$, 
[Steiner](https://en.wikipedia.org/wiki/Jakob_Steiner) proposed a volume formula:

![screen-shot-2016-12-22-at-12-25-12-pm](/assets/images/screen-shot-2016-12-22-at-12-25-12-pm.png)

The formula, as Vakil notes, is polynomial in the radius, ε, of the neighborhood, and relates volume to surface area. 
Depending on ε, the volume may converge to an n-sphere, $V(Β_n)$, or to the volume of the convex body, $V(K)$.

**Notes**

A *fearful sphere* comes from a [handful of metaphors](https://sites.google.com/site/jimeikner/home/borges/the-fearful-sphere-of-pascal). 
For linear systems, [Kaczmarz](https://en.wikipedia.org/wiki/Kaczmarz_method) and Cimmino are basic iterative algorithms, 
now considered part of [projection methods](https://arxiv.org/pdf/1406.6143.pdf). von Neumann studied the general question - 
maps of [linear operators](http://press.princeton.edu/titles/3136.html) \- of early on. The discrete Fourier transform can 
be [viewed](http://www-personal.umich.edu/~pion/feat/fcarc-geometricFT1.htm) geometrically and 
[applied](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.384.3752&rep=rep1&type=pdf) to geometric shapes. The 
Steiner formula is a special instance of [Minkowski](https://en.wikipedia.org/wiki/Hermann_Minkowski)'s 
[mixed volume](https://en.wikipedia.org/wiki/Mixed_volume) [Schröder08], and 
[Weyl](https://en.wikipedia.org/wiki/Hermann_Weyl)'s formula for the 
[volume of a tube](http://www.math.uchicago.edu/~shmuel/AAT-readings/Data%20Analysis%20/Tubes/Weyl,%20volume%20of%20tubes.pdf) 
of a submanifold.

Fourier polygons

* [Benzi04](http://www.mathcs.emory.edu/~benzi/Web_papers/cimmino.pdf) M. Benzi. _Gianfranco Cimmino's contributions to numerical mathematics._ 
* [Combettes00](http://citeseerx.ist.psu.edu/viewdoc/download;jsessionid=E917107F48557468472D333B1CBCDE19?doi=10.1.1.221.2980&rep=rep1&type=pdf) P. Combettes. _Fejér monotonicity in convex optimization_. 
* [Censor83](http://www.sciencedirect.com/science/article/pii/0024379582901495) Y. Censor, T. Elfving. _New methods for linear inequalities._ 
* [Dantzig92](https://web.stanford.edu/group/SOL/reports/SOL-92-5.pdf) G. Dantzig. _An ε-precise feasible solution to a linear program with a convexity constraint in 1/ε² iterations independent of problem size._ 
* [Elmachtoub07](https://www.cs.cornell.edu/cv/ResearchPDF/EllipsePoly.pdf) A. Elmachtoub, C. van Loan. _Random polygons to ellipse: an eigenanalysis_. 
* [Glassner99](http://www.glassner.com/wp-content/uploads/2014/04/CG-CGA-PDF-99-01-Fourier-Polygons-Jan99.pdf) A. Glassner. _Fourier polygons_. 
* [Shröder08](http://www.multires.caltech.edu/pubs/GeoMeasureCourse.pdf) P. Schröder. _What can we measure?_ 
* [Vakil04](http://www.maa.org/sites/default/files/pdf/awards/The%20Mathematics%20of%20Doodling.pdf) R. Vakil. _The mathematics of doodling_. .
