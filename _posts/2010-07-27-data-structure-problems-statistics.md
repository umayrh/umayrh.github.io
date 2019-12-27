---
layout: post
title: "Data Structure Problems: Statistics"
date: "2010-07-27"
description:
image: 
author: Umayr Hassan
tags:
---

##### 1 Algorithm to calculate running mean over a window of N values

- mean = SUM(n)/N therefore given the new data point y and old point x, new_mean = [ SUM(n) +y - x ] / N

##### 2 Algorithm to calculate running variance/standard deviation over a window of N values

- Use the fact that Var[X] = E[X²] - E[X]². Maintain the running mean for the values and the squares of these 
values to estimate Var[X] and hence standard deviation on the fly.

##### 3 Algorithm to calculate running median over a window of N values

- Maintain a queue of values. Enqueue new value, and dequeue old value in O(1) time. Use an 
[indexable skip list](http://en.wikipedia.org/wiki/Skip_list) to remove the old value and to insert the new
 value in O(logN) time. Access the middle value in O(1) time using the half of the size of the list as index.

##### 4 Algorithm to find the median value in an unsorted list of values.

- Using an array: sort in O(N log N) time (e.g. using heapsort) and access the middle values. Total time: O(N log N)
- Use the [BFPRT partitioning algorithm](http://en.wikipedia.org/wiki/Selection_algorithm#Linear_general_selection_algorithm_-_Median_of_Medians_algorithm) 
to select median in worst-case O(N) time. It is as follows: - Recursively, group the data in fives and 
find the median of each group - Use the median of medians as a pivot to partition about - if current median 
is the same as pivot, return pivot - if current median is less than pivot, find median of medians till pivot 
index - if current median is greater, find median of medians from pivot index to end of array

References

- [William Pugh's paper](ftp://ftp.cs.umd.edu/pub/skipLists/skiplists.pdf) on skip lists
