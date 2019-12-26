---
layout: post
title: "Maximum weighted independent set - singletons and forest"
date: "2016-12-06"
description:
image: /assets/images/clebschgraphk16_800.gif
author: Umayr Hassan
tags:
- Algorithms
- BitSet
- Graph Theory
- Combinatorics
- Dynamic programming
- Independent sets 
- Java
- Mathematics
---

> Gus wants to open franchises of his restaurant, Los Pollos Hermanos, along Central Avenue. There are $n$ possible 
>locations for franchises, where location i is at mile i on Central. Each location i > 1, is thus a distance of 1 mile 
>from the previous one. There are two rules.
> 
> 1. At each location, there can be at most one restaurant, and the profit of a restaurant at location i is $p_i$.
> 2. Any two restaurants must be at least 2 miles apart.

![clebschgraphk16_800](/assets/images/clebschgraphk16_800.gif)

[This](http://www.cs.unm.edu/~saia/classes/561-f14/hw/hw3.pdf) is one way to state the sequential facility location problem, 
which can be solved efficiently using a memo-ized dynamic program:

```java
int sequentialFacility(idx, profits[]):
    if (idx >= profits.length) 
        return 0
    return MAX( sequentialFacility(idx + 1),  sequentialFacility(idx + 2) + profits\[idx\])
```

Its generalization, graph facility location, is NP-complete, being equivalent to finding the 
[maximum weighted independent set](https://en.wikipedia.org/wiki/Independent_set_(graph_theory)) of a graph. 
This post is about calculating the weight of such a set, and the number of ways to find this maximum weight, 
for a general graph. We'll assume non-negative weights throughout.

Let's start with graph representation. We'll assume that the graph can be conveniently represented with 
[BitSet](https://docs.oracle.com/javase/7/docs/api/java/util/BitSet.html)s (which are very 
[efficient](http://lemire.me/blog/2012/11/13/fast-sets-of-integers/) to work with). This graph can be searched with a 
map that gives the adjacent set for each node, and then node independence can be tested for quickly:

```java
/*
* @return true iff the nodes in a given set are independent. Assumes that all BitSets involved are of the same
* size, and that a node is not adjacent to itself. O(n^2) really, but still much faster
*/
private static boolean isIndependent(BitSet set, Map<Integer, BitSet> adjacent) {
    for (int i = set.length(); (i = set.previousSetBit(i - 1)) >= 0;) {
        if (set.intersects(adjacent.get(i))) {
            return false;
        }
    }
    return true;
}
```
**Singletons**

What if the given graph is composed only of singletons? The max weight of the independent set is trivial: sum of all nodes. 
The number of ways to achieve this max  depends on whether or not there are nodes with zero weight. There are three cases:

1. All node weights are positive
2. All node weight are zero
3. Non-negative weights

(1) is trivial, and there is only 1 way to achieve the max weight; (2) implies a power-set: there are $2^n$ ways to sum 
to 0 using only 0s, including the null set; (3) implies a union of (1) and (2) - there are $2^k$ ways, where $k < n$ is the 
number of nodes with zero weight.

```java
/*  * For N singletons, each with weight w[i] >= 0, returns the sum of weights, and the number of ways to achieve
* sum-weight, across singletons. For all non-zero components, there's only one way to achieve sum-weight.
*/
private static Result singletons(int[] w) {
    int zeros = 0, sum = 0;
    for (int i = 0; i < w.length; ++i) {
        if (w[i] == 0) {
            ++zeros;
        }
        sum += w[i];
    }
    // zeros translates to power-set (including the null set)
    if (zeros == w.length) {
        return new Result(0, BigInteger.valueOf(2).pow(w.length));
    } else if (zeros == 0) {
        return new Result(sum, 1);
    }
    // size of the union of non-zeros (treated as one set) and the powerset of zeros i.e. null set is accounted for
    return new Result(sum, BigInteger.valueOf(2).pow(zeros));
}
```

**Forest**

Though a forest in graph theory [refers](https://en.wikipedia.org/wiki/Tree_(graph_theory)#Forest) to a disjoint set of 
trees, we'll use it to mean any disjoint set of connected components. Forests are a natural extension of singletons, and 
help decompose a given graph into smaller independent set problems. If we are given all the connected components of a 
graph, and, for each component, the sum of its weighted max independent set and the number of ways to find the sum; how 
can we combine this information to solve for the general graph? We face the same three cases:

1. All component weights are positive: the graph sum is the sum of all components, and the number of ways is the product of the ways of all components.
2. Zero weights: perhaps the most interesting case. Consider three components with ways (1, 2, 3). The number of ways of combining them would be: 1 + (1 + 2 + 3) + (1\*2 + 2\*3 + 1\*3 )+ (1\*2\*3). This enumeration forms a pretty polynomial: given values a, b, c..., there are (a + 1)(b + 1)(c + 1)... ways (including null set). This generalizes naturally to the power-set for the case where a = b = c ...= 1.
3. As before, it's a union of (1) and (2)

```java
/*
 * For N components, each with weight w[i] >= 0, returns the sum of weights, and the number of ways to achieve
 * sum-weight, across components. For all non-zero components with ways = 1, there's only one way to achieve
 * sum-weight. For zeros, well, it's a little more complicated. E.g. given components with 0 max, and ways (a, b, * c), there're 2^3 combinations of combinations: a + b + c + (a * b) + (a * c) + (b * c) + (a * b * c) = (a + 1)(b * + 1)(c + 1) e.g. 1 + 2 + 3 + 1*2 + 2*3 + 1*3 + 1*2*3 + 1 (including null set)
 */
 private static Result components(Result[] c) {
     int zeros = 0, sum = 0;
     BigInteger nonzeroProduct = BigInteger.ONE;
     for (int i = 0; i < c.length; ++i) {
         if (c[i].max == 0) {
             ++zeros;
         } else {
             nonzeroProduct = nonzeroProduct.multiply(c[i].ways);
         }
         sum += c[i].max;
     }
     // all components have non-zero max
     if (zeros == 0) {
         return new Result(sum, nonzeroProduct);
     }
     // since we already have the number of ways to sum all non-zero components,
     // find the number of way to sum zero components by evaluating the product polynomial
     BigInteger zeroTotal = BigInteger.ONE;
     for (int i = 0; i < c.length; ++i) {
         if (c[i].max == 0) {
             BigInteger ways = c[i].ways;
             zeroTotal = zeroTotal.multiply(ways.add(BigInteger.ONE));
         }
     }
     if (zeros == c.length) {
         return new Result(sum, zeroTotal);
     }
     // now the total number is nonzeroProduct * (zeroTotal) since non-zero components together form a valid
     // combination without any zero components involved.
     return new Result(sum, nonzeroProduct.multiply(zeroTotal));
 }
```
Once we've accounted for singletons and forest, we need to solve the independent set problem for each connected component. 
This would the topic of the next post.
