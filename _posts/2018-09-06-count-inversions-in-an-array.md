---
title: Count Inversions in an Array 
key: 20180906
tags: Algorithm
---

This problem was asked by Google.

We can determine how "out of order" an array A is by counting the number of inversions it has. Two elements A[i] and A[j] form an inversion if A[i] > A[j] but i < j. That is, a smaller element appears after a larger element.

Given an array, count the number of inversions it has. Do this faster than $$O(n^2)$$ time.

<!--more-->

The count of inversions can be used to determine whether an array is sorted and identify how different two given arrays are, which may be used in collaborative filter. There are two solutions of this problem and the time complexity of them are $$O(n^2)$$ and $$O(n\,log\,n)$$. Absolutely, the second solution is the accepted answer in the interview.

### Solution 1

It is very straightforward that, for each element, counting number of elements which are on right side of it and are smaller than it. The time complexity is $$O(n^2)$$ as there are two layers of loops.

```java
static int countInversions(int[] array) 
{ 
    int inversions = 0; 
    for (int i = 0; i <= array.length; i++){
        for (int j = i+1; j < n; j++){
           if (arr[i] > arr[j]){
               inversions++; 
           } 
       } 
   } 
  return inversions; 
} 
```
### Solution 2

To improve an $$O(n^2)$$ algorithm, the next better time complexity usually is $$O(n\,log\,n)$$. In most cases, $$O(n\,log\,n)$$ is achieved by using a tree structure. Divide and conquer is the methodology should come to your mind, as it divide a big problem into multiple sub small problems recursively. Recursion is a tree method and we can get the time complexity of a recursion method by drawing the [recursion tree](https://courses.csail.mit.edu/6.006/spring11/rec/rec08.pdf){:target="_blank"}. 

Let's try to divide the given array into two equal size arrays each time. Then we get a left array and a right array. The inversions of a node pair can be the following situations: 
1. Both nodes are in the left array.
2. Both nodes are in the right array. 
3. One node is in the left array and another one is in the right array(split inversions).

The number of inversions of an array is the sum value of 1, 2 and 3. For 1 and 2, we can get the count by recursion and if we could solve situation 3 in $$O(n)$$, this solution will be a $$O(n\,log\,n)$$ algorithm.

For problems related to comparison, ordering and ranking, a sorted array often makes the problem simple. What if the left array and the right array are sorted? It makes the solution very similar to [merge sort](https://en.wikipedia.org/wiki/Merge_sort){:target="_blank"}. As the same as merge sort, we can use double pointers to solve the problem. For a pointer(pl) of left array and a pointer(pr) of right array, if A[pl]>A[pr], the count of split inversions which contains element A[pr] is LeftArray.lengh - pl. Thus, while looping through all the n elements in an array, we move lp either rp and we could get the total count of split inversions in an array. It is an $$O(n)$$ method. 

The recursion function of Solution 2 is $$T(n)=2T(n/2)+O(n)$$. It is the same as merge sort and time complexity is $$O(n\,log\,n)$$.
 
The implementation in Java is shown blow:

```java
public static int countInversions(int[] array) {
    return countInversions(array, new int[array.length], 0, array.length - 1);
}

private static int countInversions(int[] array, int[] temp, int leftStart, int rightEnd) {
    if (leftStart >= rightEnd) {
         return 0;
    }
    int middle = (leftStart + rightEnd) / 2;
    int leftInversions = countInversions(array, temp, leftStart, middle);
    int rightInversions = countInversions(array, temp, middle + 1, rightEnd);
    int splitInversions = countSplitInversions(array, temp, leftStart, rightEnd);
    return leftInversions + rightInversions + splitInversions;
}

private static int countSplitInversions(int[] array, int[] temp, int leftStart, int rightEnd) {
    int inversions = 0;
    int leftEnd = (leftStart + rightEnd) / 2;
    int lp = leftStart;
    int rp, rightStart;
    rp = rightStart = leftEnd + 1;
    for (int i = 0; i <= rightEnd - leftStart; i++) {
         if (lp > leftEnd) {
                temp[leftStart + i] = array[rp++];
         } else if (rp > rightEnd) {
                temp[leftStart + i] = array[lp++];
         } else {
             if (array[lp] < array[rp]) {
                    temp[leftStart + i] = array[lp++];
             } else {
                    temp[leftStart + i] = array[rp++];
                    inversions += rightStart - lp;
             }
        }
    }
    System.arraycopy(temp, leftStart, array, leftStart, rightEnd - leftStart + 1);
    return inversions;
}
```
Be aware this algorithm requires extra n space. The space time complexity is $$O(n)$$, however the time complexity of  Solution 1 is $$O(1)$$.
