---
title: Find the Largest K Elements 
mathjax_autoNumber: true
key: 20180813
tags:
  - Data Structure
  - Algorithm
---

Find the largest K element in an array is a common code challenge in interviews. You may see this problem in several different formats like [K Closest Points to the Origin](https://www.youtube.com/watch?v=eaYX0Ee0Kcg){:target="_blank"}.

Sorting the whole given array can solve this problem in time complexity of $$O(n\,log\,n)$$ and space complexity of $$O(n)$$. However, there is another efficient solution, which has a huge improvement of space complexity when K is small, through an import data structure - Heap.

<!--more-->

### Heap

Heap is an abstract data structure of priority queue. It is implemented by an array (index starts with 1) and visualized as a complete binary tree. 

Based on the above definition, we get the following important proprieties of Heap:

- For a give element e at index n, its children elements are at index 2n(left child) and 2n+1(right child).
- The max height of a heap with N elements is $$log\,N$$.

Heap is usually used as max heap or min heap. If all nodes are larger than its children, this heap is a max heap. It guarantees the first element(root node) is the largest one in a heap. I am going to discuss basic operations of max heap and their complexities in this post. The same applies to min heap.   

#### Heapify:

This is the most basic operation of max heap. For a given heap and an index of a node in the heap, "heapify" converts the subtree of the given element(array[i]) to a max heap. An important assumption of "heapify" is that all the sub trees of node i are max heaps already.

```java
public static <T extends Comparable> void heapify(T[] array, int i) {
  int l = i * 2;
  int r = l + 1;
  int largest = i;
  if (l <= array.length - 1 && array[l].compareTo(array[i]) > 0){
    largest = l;
  }
  if (r <= array.length - 1 && array[r].compareTo(array[largest]) > 0){
    largest = r;
  }
  if (largest != i) {
    T temp = array[i];
    array[i] = array[largest];
    array[largest] = temp;
    heapify(array, largest);
  }
}
```
As we can see the time complexity of "heapify" is the height of node i in the heap. If subtree of the give node has n elements, the time complexity is $$O(log\,n)$$. 

#### Convert an unsorted array to a max heap:

By using the "heapify" method defined above, it is easy to construct a max heap from an unsorted array.

```java
static <T extends Comparable> void buildMaxHeap(T[] array){
  int n = array.length;
  for(int i = n/2 ; i > 0 ; i --){
    heapify(array, i);
  }
}
```

The "buildMaxHeap" method calls "heapify" method of the nodes from the second lowest level up to the root node in a heap. It is very obviously that the time complexity of the above method is $$O(n\,log\,n)$$. However, after the following detailed analysis, we can get the time actual complexity is $$O(n)$$.

For a node at height l, the time complexity of "heapify" is $$O(l)$$ and max number of nodes at height l is $$\lceil {n \over {2^{l+1}}} \rceil$$. So the total time complexity of "buildMaxHeap" is:

   $$\sum_{i=1}^{\lceil log\,n \rceil} {i*n \over {2^{i+1}}}$$ 

Let's analyze whether we can converge $$\sum_{i=1}^{\lceil log\,n \rceil} {i \over {2^{i+1}}}$$ to a constant value. This expression can be written as $$S_i = 1/2^2 + 2/2^3 + ... + i/2^{i+1}$$. And, $$S_i/2 = 1/2^3 + 2/2^4 + ... + (i-1)/{2^{i+1}} + i/2^{i+2}$$.

Then by using $$ S_i - S_i/2$$, we can get:

   $$S_i = 2*(S_i - S_i/2) = 1/2 + 1/2^2 + ... + 1/2^i - i/2^{i+1} = 1 - (1/2)^i - i/2^{i+1} \lt 1$$

So, the time complexity of "buildMaxHeap" is $$O(n)$$ as  $$\sum_{i=1}^{\lceil log\,n \rceil} {i \over {2^{i+1}}}$$ in (1) is always smaller to 1.

#### Heap sort:

Based on the above operations, we could sort an unsorted array with the following algorithm:

1. Using "buildMaxHeap" to convert an unsorted array to a max heap - $$O(n)$$ 
2. Find the largest element a[1] - $$O(1)$$
3. Swap element a[n] with a[1] - $$O(1)$$
4. Discard the element a[n] from the heap - $$O(1)$$
5. Call "heapify(array, 1)" to rebuild the max heap and go to step 2 until there is only 1 element in the heap. - $$O(log\,n)$$

So, as we can see the time complexity of heap sort is $$O(n\,log\,n)$$.

### Find the K largest elements

Min heap can be used to find the largest K elements in an unsorted array of size n.

1. Create a min heap of K elements from array[1] to array[k] - $$O(K)$$
2. Loop through the rest elements from array[k+1] to array[n] 
3. For each element, if it is larger than the root of min heap, replace the root element with it. And "heapify" the new root - $$O((n - K) * log\,K)$$. 

The time complexity of this algorithm is $$O(n\,log\,K)$$ and the space complexity is $$O(K)$$. Using max heap only needs to go through the given array once. It is an effective algorithm when n is very big value. However, we can get better time complexity to solve this problem by using partion sort, whose time complexity is $$O(n)$$. 

*What if even K is too big to keep a size-K heap in memory?

We can get the K' largest elements(K is the maximum size of a heap can be kept in memory). Discard the selected K' elements in the array and then get the next K' largest elements until we have all the K element we needed. By doing this, we could keep the space complexity to $$O(K')$$ and the time complexity comes to $$\lceil {K \over K'} \rceil O(n\,log\,K)$$.

### References

1. [MIT 6.006 Introduction to Algorithms - Heaps and Heap Sort](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-006-introduction-to-algorithms-fall-2011/lecture-videos/lecture-4-heaps-and-heap-sort/){:target="_blank"}