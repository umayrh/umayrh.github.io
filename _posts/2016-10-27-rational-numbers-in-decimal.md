---
layout: post
title: "Rational numbers in decimal"
date: "2016-10-27"
description:
image: /assets/images/repeating-dec.png
author: Umayr Hassan
tags:
- Algorithms
- Java
- Mathematics
- Number Theory
---

In decimal representation, [rational numbers](https://en.wikipedia.org/wiki/Rational_number) either terminate after a 
finite number of digits or produce a repeating sequence. Conversely, any repeating decimal can be 
[converted](http://mathcentral.uregina.ca/QQ/database/QQ.09.06/h/lil1.html) into a rational number e.g. 
$(10 * 0.333.. - 0.333...) / 9 = 1/3$.

How can one generate this representation in code with the repeating digits, if any, parenthesized? Find values $0 <= n < b$, 
the period of repeating decimal,  and 0 <= m < b, the length of the period's prefix, such that $(10^n - 1) \* 10^m / b$ is 
an integer. E.g. $1 / 3 = 0.(3)$ has $n = 1$ and $m = 0$ while $89/26 = 3.4(230769)$ has $n = 6$ and $m = 1$.

Here's code that avoids using floating-point division and shortest repeating substring to find the decimal representation. 
Note that the method used to find $n$ and $m$ have linear complexity but can be implemented using binary search. 
See note at the end for another way to find $m$, the length of the prefix.

```java
private static String printRational(long num, long den) {
   StringBuilder sb = new StringBuilder();
 
   // early return for finite-length strings
   if (!isRepeating(sb, num, den)) {
     return sb.toString();
   }
 
   // find the period of the repeating part
   int period = 1;
   long rem = 0;
   long numMult = 0;
   for (long mult = 10; period < den; ++period, mult *= 10) {
     numMult = (mult - 1) * num;
     long quot = numMult / den;
     rem = numMult - quot * den;
 
     if (rem == 0 || !isRepeating(null, numMult, den)) {
       break;
     }
   }
 
   // return if the decimal has no prefix
   if (rem == 0) {
     return sliceString(sb, 0, period);
   }
 
   // otherwise, find the length of the prefix
   int prefix = 0;
   rem = 1;
   for (; (prefix < den) && (rem != 0); ++prefix) {
     numMult = numMult * 10;
     rem = numMult - (numMult / den) * den;
   }
 
   return sliceString(sb, prefix, period);
}
// long division - return true iff given number is repeating
private static boolean isRepeating(StringBuilder sb, long num, long den) {
   long quot = num / den;
   long rem = num - quot * den;
   if (sb != null) {
     sb.append(quot);
   }
   if (rem == 0) {
     return false;
   }
   if (sb != null) {
     sb.append('.');
   }
   for (long idx = 1; (idx < den) && (rem != 0); ++idx) {
     rem = rem * 10;
     quot = rem / den;
     rem = rem - quot * den;
     if (sb != null) {
       sb.append(quot);
     }
   }
   return rem != 0;
}
 
private static String sliceString(StringBuilder sb, int prefixLength, int period) {
   int idx = sb.indexOf(".") + prefixLength + 1;
   return sb.substring(0, idx) + "(" + sb.substring(idx, idx + period) + ")";
}
```

All rational numbers with prime denominators and even length periods  have the 'nines property' i.e. the first and 
second half-periods of the repeating part add up to 9...9 (Proven in W. G. Leavitt, _A Theorem on Repeating Decimals_, 
1967 [paper](http://digitalcommons.unl.edu/cgi/viewcontent.cgi?article=1047&context=mathfacpub). Also, an interesting 
[lemma](http://mathoverflow.net/questions/41736/how-do-you-calculate-prove-the-length-of-n-the-number-of-non-repeating-digits-p) 
on the length of the prepend to the period (it is the number of times the denominator is divisible by either 2 or 5).
