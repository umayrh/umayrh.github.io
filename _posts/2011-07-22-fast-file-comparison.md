---
title: "Fast File Comparison"
date: "2011-07-22"
---

\[caption id="attachment\_132" align="aligncenter" width="300"\][![](http://umayrh.files.wordpress.com/2011/07/blog1910sortingmail.jpg?w=300 "Sorting mail")](http://umayrh.files.wordpress.com/2011/07/blog1910sortingmail.jpg) Sorting mail at the Federal Building post office, circa 1910.\[/caption\]

Problem: Given a very large number of files, many of whom are _exact_ replicas, how can we group all files by replicas?

There are several interesting cases of this problem:

\- Files usually fit in memory - Files are very large, and can only be read in blocks - Most files belong to one or few replica groups

The obvious and correct solution is to directly compare each file with every other. Generally, this solution should be the best possible only if each file is known to contain a number. If the files contain long binary strings, then a better solution would reduce the number of file comparisons (and memory accesses in case files are very large) as far as possible. One such solution is the following three-pass algorithm:

1\. Iterate over all files and group by file size. In general, we can use any auxiliary information (such as file extension or type etc) that is known to be consistent across replicas.

2\. For each group with at least two files obtained from first pass, we now obtain an numeric representation of its contents. Such representation may be a hash, fingerprint or message digest of the file contents.

For example, if files are known to contain short strings, then a perfect hash representation of each string may be calculated, and each hash stored in hash-map that contains all files with the same hash. In this case, we don't need a third pass since the hash completely and uniquely represents file contents. Moreover, comparing files is more efficient since we avoid comparing each string with every other string by only comparing hashes.

If files are very large, then we can use incremental hash functions to gradually calculate the numeric representation of a file while simultaneously grouping files based on the hash value calculated so far. In the first round, we read a block from each file in the group, calculate the block's hash using an incremental hash function and group by all files that have the same hash (using, e.g., a hashmap). In the subsequent round, we only read the next block from files with the same previous-round incremental hash value, and calculate and group by the new block's hash, till there are no more blocks left to read.

3\. A third pass is needed if we have not used a perfect hash function in the second pass, which means that there is non-zero collision probability for hashes and thus different blocks with different content may have had the same hash value. In this case, we have groups containing files with on same hash values across all blocks, and must compare all files with each other. If we consider each file as a queue of blocks, then we can reduce the number of comparisons using an algorithm similar to the one use to merge multiple sorted lists. Instead of merging nodes (i.e. blocks) that are same across lists, we only continue comparing files as long as their blocks are the equal. During each pass, we should be able to find all replicas (if any) of the file being compared with others.

> \- INITIALIZE: all files belong to group G1
> FOR ALL groups G1, G2 ...
> - WHILE file F1 has more blocks
> -- read a block b1 from F1
> -- compare b1 with each block read from all other files (F2, F3...) in G1
> -- IFF for some file F\_i, b1 is not equals to b\_i, then try inserting F\_i based on b\_i in all groups other than G1; otherwise create a new group (if it doesn't exist) G\_j and add F\_i to G\_

The above algorithm, in the worst case, requires O(n^2) block compares.

The first and second passes might incur unnecessary overhead if it is known _a priori_ that almost all the files are exact replicas of each other.
