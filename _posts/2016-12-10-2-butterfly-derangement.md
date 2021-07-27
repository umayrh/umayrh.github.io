---
layout: post
title: "2-butterfly derangement"
date: "2016-12-10"
description:
image: /assets/images/res3.png
author: Umayr Hassan
tags: 
- Algorithms
- Java
- Mathematics
- Permutations
- Switching networks
---

The hallmark of the [Cooley-Tukey algorithm](https://en.wikipedia.org/wiki/Cooley–Tukey_FFT_algorithm) for  
[Fast Fourier Transform](https://en.wikipedia.org/wiki/Fast_Fourier_transform) is the 
[butterfly network](https://en.wikipedia.org/wiki/Butterfly_network), which helps reduce $O(n^2)$ computations to $O(n log n)$. 
Butterflies are very special graphs entangled in routing [Arora], switching [Chung], shuffling [Yang], and mixing [Czumaj].

![Three butterflies - FFT flow graph, shuffling network, and a nonblocking interconnect](/assets/images/res3.png) 

The butterfly with 2 inputs and outputs (aka [radix-2 butterfly](https://en.wikipedia.org/wiki/Butterfly_diagram#Radix-2_butterfly_diagram), 
or the [bipartite graph](http://mathworld.wolfram.com/CompleteBipartiteGraph.html) $K(2, 2)$ is a building block for larger 
networks [Chung]. It can implement two distinct operations: pass-through and swap.

![pass-through, and swap](/assets/images/screen-shot-2016-12-09-at-1-49-32-pm.png) 

**Problem 1**: Can an array of size $N$ be [permuted](https://en.wikipedia.org/wiki/Permutation) using only swapping 
2-butterflies in a single stage? Single stage implies that every element is swapped exactly once.

Clearly, it can be iff $N$ is even (otherwise the mapping is not bijective). Each permutation is also a 
[maximal matching](https://en.wikipedia.org/wiki/Matching_(graph_theory)) on the $K(N/2, N/2)$ spanning $N$ elements.

**Problem 2**: Can an array be [deranged](https://en.wikipedia.org/wiki/Derangement) using only swapping 2-butterflies 
in a single stage?

There are always at least two derangements for any even-sized array when $N > 2$ (only one if $N = 2$). One can be obtained 
by swapping $N/2$ non-overlapping pairs. Another, by swapping the two halves of the array. Here're two for $N = 16$:

![Any even N has at least these two derangements](/assets/images/fft16.png)

**Problem 3**: How many such derangements are possible?

Let $N = m * 2^k$, where $m >= 1$ is some odd integer, and $k > 0$. There are at most $log(k)$ - or, $log(N)$, 
if $m = 1$ - distinct derangements.

**Problem 4**: Is there a permutation of the first $N$ natural numbers, $[1..N]$, such that if a number $x$ is mapped to a 
number $y$ in the permutation, then $| x - y | == K$ for all x and y, and for a given $0 < K < N$? $|.|$ denotes the absolute 
value. In other words, is there a bijective function f mapping $[1..N]$ to $[1..N]$ such that $|x - f(x)| == K$?

![screen-shot-2016-12-09-at-3-14-18-pm](/assets/images/screen-shot-2016-12-09-at-3-14-18-pm.png)

Since K > 0, this permutation must be a derangement. The previous two examples for N = 16 illustrate K = 1 and K = 8. 
They represent a derangement that swaps N / K groups of K neighbors with each other. If K leaves an even factor to N, 
and K <= N/2, then such a derangement always exists. Here's a short program to print them out:

[code language="java"] public static String permute(int n, int k) { StringBuilder result = new StringBuilder(2 \* n); if (n % 2 == 0 && k > 0 && k <= (n / 2)) { // any k that leaves an even factor is fine // e.g. k = 4 for n = 12 is not valid but k = 2 is if ((n % k == 0) && ((n / k) % 2 == 0)) { // groups of k are swapped with neighbors int k2 = k \* 2; for (int g = 0; g < n / k2; ++g) { int f = k2 \* g; for (int i = k + 1; i <= k2; ++i) { result.append(f + i).append(" "); } for (int i = 1; i <= k; ++i) { result.append(f + i).append(" "); } } return result.toString(); } } return "No permutation exists"; } \[/code\]

* [Arora](http://www.cs.princeton.edu/~arora/pubs/nonblock.ps) S. Arora, F. T. Leighton, B. M. Maggs. _Online algorithms for path selection in a nonblocking network_. 
* [Chung](http://www.math.ucsd.edu/~fan/mypaps/fanpap/fc15algebraic.pdf) F. R. K. Chung. _An algebraic approach to switching networks_. 
* [Yang](https://arxiv.org/pdf/1204.1958.pdf) Q. Yang, J. Ellis, K. Mamakani, F. Ruskey. _In-place permuting and perfect shuffling using involutions_. 
* [Czumaj](http://www.dcs.warwick.ac.uk/~czumaj/PUBLICATIONS/CONFERENCES/Czumaj-STOC-2015-703-712.pdf) A. Czumaj. _Random permutations using __switching networks_.
