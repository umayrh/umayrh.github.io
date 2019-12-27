---
layout: post
title: "Data Structure Problems: Binary Trees"
date: "2010-07-28"
description:
image: 
author: Umayr Hassan
tags:
---

1. Mapping a binary tree into a list

```java
public BinNode treeList(BinNode root) {
   if (root == null) return null;
   BinNode left = treeList(root.left);
   BinNode right = treeList(root.right);
   root.left = root;
   root.right = root;
   left = splice (left, root);
   left = splice (left, right);
   return left;
 }
 
BinNode splice (BinNode list1, BinNode list2) {
   if (list1 == null) return list2;
   if (list2 == null) return list1;
   join(list1.left, list2);
   join(list2.left, list1);
   return list1;
 }
 
void join(BinNode prev, BinNode next) {
   prev.right = next;
   next.left = prev;
 }
```

2. Finding the longest path in a binary tree

Use breadth-first search to find the last node visited - this shall necessarily be one of the longest paths. 
Maintain pointers in each node to the node's parent. Use the last node to recursively find all of its ancestors. This

```java
public Queue getAncestors(BinNode child) {
   if (child == null) return null;
   BinNode current = child;
   Queue queue = new LinkedList();
   while (current != null) {
     queue.add(current);
     current = current.parent;
   }
   return queue;
 }
 
 // Returns one of the leaf nodes in the tree, in the
 // order found using breadth-first search
 public BinNode getLeafNode (BinNode root) {
   if (root == null) return null;
   Queue queue = new LinkedList();
   queue.add(root);
   root.visited=true;
   BinNode last = null; while(!queue.isEmpty()) {
     BinNode current = (BinNode) queue.remove();
     BinNode child = null;
     while( (child = getUnvisitedChild (current)) != null ) {
       child.visited = true;
       queue.add(child);
     }
     last = current;
   }
   clearVisited(root);
   return last;
 }
 
 // Returns the unvisited (i.e. visited=false) child of given node,
 // and null otherwise
 BinNode getUnvisitedChild (BinNode parent) {
   if (parent == null)
   return null;
   if (parent.left != null &amp;&amp; !parent.left.visited)
   return parent.left;
   if (parent.right != null &amp;&amp; !parent.right.visited)
   return parent.right;
   return null;
 }
 
 // Clears the visited attribute of all nodes, starting
 // from the root
 void clearVisited(BinNode root) {
   if (root == null) return;
   clearVisited(root.left);
   root.visited = false;
   clearVisited(root.right);        
 }
```

3. Find the least common ancestor for two given nodes in a tree.

Search for one of the nodes and mark each node in along path as visited: takes O(log N) time. Search for the 
other nodes and keep track of the last visited node seen. net O(log N) time. Pre-processing the tree can be 
used by [off-line algorithms](http://en.wikipedia.org/wiki/Lowest_common_ancestor) to yield result constant times.

References:

* [1] Some simple binary [tree problems](http://cslibrary.stanford.edu/110/BinaryTrees.html).
