---
layout: post
title: "The Algorithmic Beauty of Plants"
date: "2021-03-14"
description:
image: /assets/images/algo-beauty-plants.png
author: Umayr Hassan
tags:
- Formal languages
- Morphology
- Development
- Fractals
---

> "The idea of form implicitly contains also the history of such a form."

Notes on Prusinkiewicz and Lindenmayer's beautiful book, "The Algorithmic Beauty of Plans" (Springer-Verlag, 1990),
which is also available [online](http://algorithmicbotany.org/papers/#abop).

## 1 Graphical modeling using L-systems  

* Lindenmayer systems, or L-systems, were originally a topological model of plant development, 
  emphasizing the neighborhood relations between plant modules (cells, filaments...).
  * Geometry was added later on to make the model more expressive.

### 1.1 Rewriting systems

* Complex objects are composed by successively replaces parts on an initial simple object using 
a set of _rewriting rules_ or _productions_. Examples are:
  * von Koch's rewriting rules for open polygons (snowflake curve)
  * Conway's _game of life_
  * Chomsky's formal grammars 
* "The essential difference between Chomsky's formal grammars and L-systems lies in the method
  of applying productions." 
  * Sequentially in the former, and in parallel in the latter

### 1.2 DOL-systems

* DOL-systems, the simplest class of L-systems, is deterministic and context-free.
  * _Axiom_ is the _initiator_ string, and all string rewriting rules are applied simultaneously
    in each steps. So, given rules (1) `a -> ab`, and (2) `b -> a`, the axiom `b` after two steps
    becomes `aba`.
* Formal definition:
  * Let $$V$$ be an alphabet, $$V*$$ be the set of words, and $$V+$$ all non-empty words.
  * $$w \in V+$$ is a non-empty initial word, called _axiom_.
  * $$P \subset V x V*$$ be the set of productions.
    * $$(a, x) \in P$$ is the same as $$ a \rightarrow x$$. 
    * $$a$$ is called _predecessor_, $$x$$ is called _successor_.
    * Thus, each character maps to a word. The default production is _identity_, $$ a \rightarrow a$$.
  * An OL-system is the triplet $$G = (V, w, P)$$.
    * An OL-system is deterministic if there is exactly one $$x \in V*$$ s.t. $$ a \rightarrow x$$.
    * A simple example is the simulation of the development of a multi-cellular filament
      of bacteria _Anabaena catenula_.

### 1.3 Turtle interpretation of strings

* Graphical representations of L-systems allow modeling more complex plants.
* Turtle interpretation of L-system:
  * Current state defined by the triplet $$(x, y, \alpha)$$, where $$(x, y)$$ represent the Cartesian 
    coordinates, and angle $$\alpha$$ is the heading.
  * Given the steps size $$d$$ and angle increment $$\delta$$, the following actions are possible:
    * $$F$$: move forward a step of size $$d$$ from $$(x, y)$$ to $$(x + dcos(\alpha)), y + dsin(\alpha)$$,
      and draw a line.
    * $$f$$: move forward as above without drawing a line.
    * $$+$$: turn left by angle $$\delta$$ so the current state becomes $$(x, y, \alpha + \delta)$$
    * $$-$$: turn right by angle $$\delta$$ so the current state becomes $$(x, y, \alpha - \delta)$$
  * The following L-system approximates Mandelbrot's _quadratic Koch island_:
    * $$w: F-F-F-F$$
    * $$p: F \rightarrow F-F+F+FF-F-F+F$$

### 1.4 Synthesis of DOL-systems

* _Inference problem_: how can one generate, or discover, the generators for L-systems?
  * Random modifications don't yield insight, and in fact may causes a system to die or be 
  stuck in a loop.
  * There are no general methods still. Two well-developed heuristics are: _edge rewriting_
    and _node rewriting_. "Both approaches rely on capturing the recursive structure of
    figures and relating it to a tiling of a plane."
* Edge rewriting:
  * Productions operate on units of a figure (edges), substituting the whole figure for each unit.
  * A variation of L-systems assumes two types of edges: right (R) and (L) although both
    correspond to the F command. They are helpful in indicating which side of a line to act on.
  * The class of "space-filling, self-avoiding, simple and self-similar" (FASS) curves \ 
    are space-filling i.e. they pass through all points of a square.
  * McKenna, in his paper "SquaRecurves, E-Tours, Eddies, and Frenzies: Basic Families of
    Peano Curves on the Square Grid" [[1]](#ref1), that outlines the conditions under which FASS
    curve generators can be found. Given a figure of order n (i.e. a figure completely tiled 
    by $$n \times n$$ scaled version of itself, aka a "rep-tile"), he proves that:
    * "Theorem 1: Generator on order n triangular grids always generate Peano curves that are
      self-contacting." Hence, there are no self-avoiding curves in this case.
    * "Lemma 1: "There are no bilaterally symmetric self-avoiding Peano curve generator on
      order n fixed square grids when n is odd".
    * "Theorem 2: Bilaterally symmetric Peano curve generators on order n fixed square grids can
      never generate self-avoiding approximations, for k > 1" (k is the number of iterations).
    * "Even when a generator, consisting of $$n^2$$ connected line segments, is self-avoiding,
      this does not necessarily mean that approximations to the Peano curve it generates are
      self-avoiding."
    * Rotational (unlike bilateral) symmetry allows some generators to mesh in a self-avoiding way.
  * The hexagonal and quadratic Gosper curves are examples of FASS:

| | |
|:-------------------------:|:-------------------------:|
|<img width="560" alt="Hexagonal Gosper curve" src="/assets/images/1.11a%20-%20hexagonal%20Gosper%20curve.gif">|<img width="560" alt="Quadratic Gosper curve" src="/assets/images/1.11b%20-%20quadratic%20Gosper%20curve.gif">|

* Node rewriting:
  * Productions operate on units of a figure (nodes), substituting a new figure for each unit.
  * Turtle interpretation extension for node rewriting:
    * Given subfigure A from a set of subfigures $$ \mathbb{A} $$:
      * Two contact points: the _entry point_ $$ P_A $$, and the _exit point_ $$ Q_A $$
      * Two direction vectors: _entry vector_ $$ \vec{p}_A $$ and the _exit vector_ $$ \vec{q}_A $$
    * Subfigure A is translated and rotated to align with its entry point $$ P_A $$ and direction
      $$ \vec{p}_A $$ with the current position and orientation of the turtle.
    * Having placed A, the turtle is assigned the resulting exit point and direction.
  * The Hilbert curve L-system for subfigures L (f+F) and R (+F):
    * $$w: L$$
    * $$L \rightarrow +RF - LFL - FR+$$
    * $$R \rightarrow -LF + RFR + FL-$$
  * When L and R are subfigures reduced to a point, the result is a _pure curve_.
  * 

## References
1. <a name="ref1"></a> The Lighter Side of Mathematics: Proceedings of the Eugene Strens Memorial Conference on Recreational Mathematics and Its History [link](www.ams.org/books/spec/011)
