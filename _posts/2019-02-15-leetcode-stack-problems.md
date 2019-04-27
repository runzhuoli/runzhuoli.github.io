---
title: Leetcode Queue & Stack Problems
key: 20190215
tags:
  - Leetcode
  - Interview
---

<!--more-->

### Queue and Stack

There are multiple implementations of [Queue](https://docs.oracle.com/javase/7/docs/api/java/util/Queue.html) in Java, such as LinkedList, ArrayDeque, etc. `offer`, `poll`, `peek` methods are used to manipulate data in the queue. [Circular array](http://www.mathcs.emory.edu/~cheung/Courses/171/Syllabus/8-List/array-queue2.html) is another choice to implement an queue.

**Leetcode 158.** Read N Characters Given Read4 II - Call multiple times

For stack, we use [Stack](https://docs.oracle.com/javase/7/docs/api/java/util/Stack.html) class and `push`, `pop`, `peek` methods.

**Leetcode 225.** [Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)

**Leetcode 232.** [Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)

### Monotonic stack

Monotonic stack is used to find the next lager element in an array. Vice verser.

```java
Stack<Integer> stack = new Stack<>();
Map<Integer, Integer> nextGreaterMap = new HashMap();

for(int i = 0; i < nums2.length; i++){
  while(!stack.isEmpty() && stack.peek() < nums2[i]){
    nextGreaterMap.put(stack.pop(), nums2[i]); //pop all element smaller than the current one.
  }
  stack.push(nums2[i]);
}
```

**Leetcode 42.** [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

**Leetcode 84.** [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)

**Leetcode 316.** [Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters/)

**Leetcode 496.** [Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/)

**Leetcode 739.** [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)
