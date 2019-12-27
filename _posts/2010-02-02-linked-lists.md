---
layout: post
title: "Data structure problems: Linked Lists."
date: "2010-02-02"
description:
image: 
author: Umayr Hassan
tags:
---

##### 1 Reversing a singly-linked list:

(a) _Iterative method_: maintain three pointers, two to the nodes being reversed, and one to the next node to be 
reversed. Continue reversing nodes till the current node goes past the last node.

```
void reverse() {
 if (head == NULL || head->next == NULL) return;
 ListNode *prev = head, *curr = head->next, *next = (head->next)->next;
 while (curr != NULL) {
   curr->next = prev;
   if (prev == head) prev->next = NULL; // loop otherwise
   prev = curr; curr = next;
   if (next != NULL) next = next->next;
 }
 ListNode *n = head;
 head = tail;
 tail = n;
 }
```

(b) _Recursive method_: Save the pointer to current head node. Starting with the first two node, reverse 
links and return the last node. Update pointers to head and tail nodes.

```
void recursiveReverse() {
   ListNode* tmp = head;
   head = recRev(head, head->next);
   tail = tmp;
}
private:
ListNode* recRev(ListNode* prev, ListNode* curr) {
 if (prev == NULL || curr == NULL) return prev;
 ListNode* next = curr->next;
 curr->next = prev;
 if (prev == head) prev->next = NULL;
 return recRev(curr, next);
}
```
##### 2 "There is linked list of millions of node and you do not know the length of it. Write a function which will 
return a random number from the list."

##### 3 "There is a linked list of numbers of length N. N is very large and you don’t know N. You have to write a function 
that will return k random numbers from the list. Numbers should be completely random. Hint: 1. Use random function rand() 
(returns a number between 0 and 1) and irand() (return either 0 or 1) 2. It should be done in O(n)."

##### 4 "Given two linked lists, return the intersection of the two lists: i.e. return a list containing only the elements 
that occur in both of the input lists."

- Sort both lists. Starting with the first element of List1, iterate till the last occurrence of current element. 
Starting with the first element of List2, iterate till the last occurrence of current element. Compare List1's 
current element with List2's, and iterate on List1 as long as List1 element is less than List2. If elements are 
equal, then add element to new list, List3. If List2 elements is less than List1, iterate on List2. Repeat while both 
lists have elements left.

##### 5 "Find or determine non existence of a number in a sorted list of N numbers where the numbers range over M, 
M>> N and N large enough to span multiple disks. Algorithm to beat O(log n) bonus points for constant time algorithm."

- Use a Bloom filter: k hash functions (where, optimally, k = (m/N)ln2) map a number to k locations in an m-bit 
array/vector. No false-negatives i.e. we can determine non-existence of a number mapped to the Bloom filter with 
certainty. See [this post](http://pages.cs.wisc.edu/~cao/papers/summary-cache/node8.html) for Bloom filter math. 
"Google [BigTable](http://en.wikipedia.org/wiki/BigTable "BigTable") uses Bloom filters to reduce the disk lookups 
for non-existent rows or columns."

##### 6 "You are given a small sorted list of numbers, and a very very long sorted list of numbers - so long that it 
had to be put on a disk in different blocks. How would you find those short list numbers in the bigger one?"

- Let k be the size of the small list, p be the size of each block and q be the total number of blocks. Then, 
using binary search on each block, we can find the small number in O(k\*q log p) time.
- Better method: Find the first (i.e. minimum) number in each block and store in a sorted list in O(q). For 
each unique number in the small list, find which block it may lie in, and then search for the number in O(k log p) 
time. Total complexity is O(q)+O(k log p).

##### 7 "Write a function to find the middle node of a single link list." 
Maintain a boolean toggle, and two pointers: slower and faster, initially pointing to the head. 
Move faster to next node till last node, and toggle counter. 
If toggle is true, move slower to next node. If faster goes beyond last node, return slower.

```
T findMid() {
 if (head == NULL) return NULL;
 ListNode* slower = head, *faster = head;
 bool toggle = false;
 while (faster->next != NULL) {
   faster = faster->next;
   if (toggle) slower = slower->next;
   toggle = !toggle;
 }
 return slower->data;
}
```

##### 8 "You are given a list of numbers. When you reach the end of the list you will come back to the beginning of the list 
(a circular list). Write the most efficient algorithm to find the minimum # in this list. Find any given # in the list. 
The numbers in the list are always increasing but you don’t know where the circular list begins, 
ie: 38, 40, 55, 89, 6, 13, 20, 23, 36."

##### 9 How to find the kth node from last in a singly-linked list?
