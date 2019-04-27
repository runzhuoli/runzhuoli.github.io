---
title: Leetcode Linked List Problems
key: 20190212
tags:
  - Leetcode
  - Interview
---

The most important thing for solving linked list problems is to figure out how pointers move during addition, insertion, deletion and reverse. Make sure the solution covered all possible cases before start coding.

<!--more-->

Linked list is not a common interview question for big companies as this topic lack of follow up questions. However, there are many questions require to use linked list together with other data structure, e.g. [LRU](https://leetcode.com/problems/lru-cache/). There are some common techniques to solve linked list problems:

**Dummy node:** Sometimes we create a dummy node which points to the head/tail node. In this case, we do not need to handle head node as a special case in the algorithm. Specially, in double linked list, we create a dummy head node and a dummy tail node to avoid complicate corner cases.

**Fast & Slow pointers:** It can be used to find the middle node of a linked list or whether a linked list has a cycle. We need to define two pointers, the fast pointer moves 2 steps at once and the slow pointer moves 1 step at once.

**Leetcode 141.** [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)

**Leetcode 142.** [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)

> [Foryd's Cycle detection algorithm](https://cs.stackexchange.com/questions/10360/floyds-cycle-detection-algorithm-determining-the-starting-point-of-cycle)

**Leetcode 234.** [Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)

**Leetcode 369.** Plus One Linked List

**Leetcode 445.** [Add Two Numbers II](https://leetcode.com/problems/add-two-numbers-ii/)

**Leetcode 21.** [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

**Leetcode 328.** [Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/)

**Leetcode 86.** [Partition List](https://leetcode.com/problems/partition-list/)

**Lettcode 24.** [Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)
