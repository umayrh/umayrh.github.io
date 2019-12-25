---
title: "IP possibilities"
date: "2012-05-24"
---

A problem I was asked: suppose a file that stored many valid IPv4 addresses was corrupted such that all dot in the addresses were removed. Given such a corrupt IP, infer all possible corresponding IP addresses. For example, given 12345, all possible addresses are: 12.3.4.5, 1.23.4.5, 1.2.34.5 and 1.2.3.45. The hint that recursion should be used made the problem easier:

\[sourcecode language="java"\] public static final int MIN\_TOKEN\_SIZE = 1; public static final int MAX\_TOKEN\_SIZE = 3;

public static List getValidIPs(String ip, int remTokens) { List result = new ArrayList(); double avgTokenSize = ip.length() / (double)remTokens ;

if ((Double.compare(avgTokenSize, MIN\_TOKEN\_SIZE) < 0) || (Double.compare(avgTokenSize, MAX\_TOKEN\_SIZE) > 0)) return result; else if (remTokens ip = getValidToken(ip); if (ip == null) return result; result.add(ip); return result; }

int maxTokenLen = Math.min(ip.length() - remTokens + 1, MAX\_TOKEN\_SIZE); int minTokenLen = Math.max(ip.length() - (remTokens - 1) \* maxTokenLen, MIN\_TOKEN\_SIZE);

for (int tokenLen = minTokenLen; tokenLen String prefix = ip.substring(0, tokenLen); String remainder = ip.substring(tokenLen);

prefix = getValidToken(prefix); if (prefix == null) continue;

List substrs = getValidIPs(remainder, remTokens-1);

StringBuffer buf = new StringBuffer(); for (String substr : substrs) { buf.append(prefix); buf.append('.'); buf.append(substr); result.add(buf.toString()); buf.setLength(0); } } return result; }

private static String getValidToken(String prefix) { if (prefix.length() < 1) return null; int val = Integer.parseInt(prefix); if (val > 255) return null; return String.valueOf(val); } \[/sourcecode\]

Here a test result:

\[sourcecode language="java"\] public static void main(String\[\] args) { String ip = "8880028"; int remTokens = 4; Set; results = getValidIPs(ip, remTokens); for (String res : results) { System.out.println(res); } } \[/sourcecode\]

8.88.0.28 8.88.2.8 88.8.0.28 88.80.2.8 8.8.8.28 8.8.80.28 88.8.2.8 88.80.0.28

Assuming we weren't validating IP correctness and wanted to know the number of permutation possible for given number of digits, n, to be split in four IP tokens; how can we go about it? A simple trick is to use to program to find out the sizes, C, of result set for all possible inputs, n:

n = 4, C = 1
n = 5, C = 4
n = 6, C = 10
n = 7, C = 16
n = 8, C = 19
n = 9, C = 16
n = 10, C = 10
n = 11, C = 4
n = 12, C = 1

There are at most four recursive calls, one for each token. Each token offers at most three possibilities, but not all the time. Here's a clumsy recursion to express this program:

$latex \\begin{array} {rcl} D(m, n) & = & \\sum\\limits\_{j}^{k} D(m-i, n-1) \\\\ k & = & \\min (m-n+1, 3) \\\\ j & = & \\max (m+k-kn, 1), \\\\ D(1 \\leq m \\leq 3, 1) & = & 1, \\\\ D(m \\geq 4, 1) & = & 0, \\\\ n & = & 4, \\\\ 4 \\leq & m & \\leq 12 \\end{array} $

Desperate to find a closed-form solution to this problem, I searched and found the series (1, 4, 10, 16, 19, 16, 10, 4, 1) in Pascal's Pyramid \[1\], which is to trinomial numbers what Pascal's Triangle is to binomials. Summing the numbers in each column of the layer 4 of Pascal's pyramid gives:

| 1 | | 4 |    | 4 | | 6 |      |12|      | 6 | | 4 |    | 12 |    | 12 |    | 4 | | 1 |    | 4 |      | 6 |      | 4 |    | 1 | | 1 | 4 |10| 16 |19| 16 |10| 4 | 1 |

For which we can derive a closed form. Given C(n, i, j), the trinomial coefficient for layer n, row i and column j; and C(i, j) the binomial coefficient for row i and column j:

$latex \\begin{array} {rcl} C(n, i, j) & = & C(n, i) \\times C(i, j) \\\\ 0 \\leq & i & \\leq n \\\\ 0 \\leq & j & \\leq i \\end{array}$

A number at a given column (or position) in our desired sequence is the sum of trinomial coefficients for alternating rows (even numbered rows for even m, and odd for odd) and given column. The sequence is symmetric about the median column:

$latex \\begin{array} {rcl} D(m, n) & = & \\sum\\limits\_{i=m-n,i-=2}^{i \\geq 0} C(n, i, \\lfloor \\frac{i}{2} \\rfloor) \\\\ n \\leq & m & \\leq 2n \\\\ D(m, n) & = & D(4n-m ,n) \\end{array} $

It turns out that this problem is also related to enumerating lattice paths: Find the set A(n, k) of all lattice paths, from point (0, 0) to (n, k) in a Cartesian plane that use either an up-step (1,1), level-step (0, 1) or down-step (1, -1) \[2\].

Update (05/27/12): Corrected the closed-form equation.

References:

\[1\] [Pascal's Pyramid](http://en.wikipedia.org/wiki/Pascal%27s_pyramid) \[2\] A [Combinatorial Proof](http://www.fq.math.ca/Scanned/40-1/woan.pdf) of a Recursive Relation of the Motzkin Sequence by Lattice Paths
