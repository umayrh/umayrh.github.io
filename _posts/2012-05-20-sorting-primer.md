---
layout: post
title: "Sorting primer"
date: "2012-05-20"
description:
image: 
author: Umayr Hassan
tags:
---

A quick overview of several important comparison-based sorting algorithms:

|Sorting technique|Time (avg)|Time (worst)|Memory|
|:---:|:---:|:---:|:---:|
|Insertion sort|$O(N^2)$|$O(N^2)$|$O(1)$|
|Heap sort|$O(NlgN)$|$O(NlgN)$|$O(1)$|
|Merge sort|$O(NlgN)$|$O(NlgN)$|$O(N)$|
|Quick sort|$O(NlgN)$|$O(N^2)$|$O(lgN)$|
|:---:|:---:|:---:|:---:|


### Insertion sort

Iterate over the array, pick each element, and, if the given element is 'smaller' than previous; 
swaps all previous elements till an element even smaller than the given is found or there are no more elements to compare.

```java
public static void insertionSort(double[] a) {
  for (int i = 1; i < a.length; i++) {
    int k = i;
    int j = i-1;
    while ((j >= 0) && (Double.comapre(a[j], a[k]) > 0)) {
      double tmp = a[j];
      a[j] = a[k];
      a[k] = tmp;
      j--; k--;
    }
  }
}
```

### Heap sort

Similar to selection sort (iterate over unsorted array, selecting the minimum value in each pass, and placing it in 
sorted order). Instead of iterating in O(N) time to select the minimum value, a heap is used to do the same in (lgN) time. 
The following implementation, though, uses a max-heap and so moves the max element found to the right-end of the array.

```java
public static void heapSort(double[] a) {
  heapify(a); // max element as root
  for (int i = a.length-1; i > 0; i--) {
    swap(a, 0, i); // put max element at array end
    siftDown(a, 0, i); // find max from the rest
  }
}
 
private static void heapify(double[] a) {
  for (int i = a.length/2-1; i >= 0; i--)
  siftDown(a, i, a.length);
}
 
private static void siftDown(double[] a, int i, int n) {
  int j = 2*i + 1;
  while (j < n) {
    if (j+1 < n) {
      if (Double.compare(a[j+1], a[j]) > 0)
        j++;
    }
    if(a[i] >= a[j])
      return;
    swap(a, i, j);
    i = j;
    j = 2*i+1;
  }
}
 
private static void swap(double[] a, int i, int j) {
  double tmp = a[i];
  a[i] = a[j];
  a[j] = tmp;
}
```

### Merge sort

Repeatedly divide the array into two, almost equal, sorted sub-array and then merge them.

```java
public static void mergeSort(double[] a, int start, int end) {
  if (start >= end-1) {
    return;
  }
  int mid = (end+start) / 2;
 
  mergeSort(a, start, mid);
  mergeSort(a, mid, end);
  merge(a, start, mid, end);
}
 
private static void merge (double[] a, int start, int mid, int end) {
  double[] tmp = new double[a.length];
  for (int i = 0; i < a.length; i++)
    tmp[i] = a[i];
 
  int i = start, j = mid, k = start;
 
  while ((i < mid) && (j < end)) {
    if (tmp[i] <= tmp[j]) {
      a[k++] = tmp[i++];
    } else {
      a[k++] = tmp[j++];
    }
  }
  while (i < mid) {
    a[k++] = tmp[i++];
  }
}
```
### Quick sort

Recursively partition an array around a pivot element such that all elements less than or equal to pivot are in the 
left sub-array, and the rest in the right. The following version requires O(lgN) auxiliary space to support recursive calls.

```java
public static void quickSort(double[] a) {
  qsort(a, 0, a.length);
}
 
private static void qsort (double[] a, int low, int high) {
  if (low >= high) return;
  double pivot = a[(low+high-1)/2];
  int i = low-1, j = high;
  while (i < j) {
    i++; while (Double.compare(a[i], pivot) < 0) i++;
    j--; while (Double.compare(a[j], pivot) > 0) j--;
    if (i < j) swap(a, i, j);
  }
 
  qsort(a, low, j);
  qsort(a, j+1, high);
}
```

### References

* [1] http://www.iti.fh-flensburg.de/lang/algorithmen/sortieren/heap/heapen.htm 
* [2] http://www.vogella.com/articles/JavaAlgorithmsMergesort/article.html 
* [3] http://www.augustana.ca/~jmohr/courses/2004.winter/csc310/source/QuickSort.java.html
