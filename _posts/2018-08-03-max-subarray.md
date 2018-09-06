---
title: Maximum Subarray Problem
key: 20180803
tags: Algorithm
---

Maximum subarray is a typical interview question you may face. Its description can be found [here](https://leetcode.com/problems/maximum-subarray/description/).

There are two common approaches to solve this problem, dynamic programing($$O(n)$$) and divide and conquer($$O(n\,log\,n)$$). I am going to share the solutions and thoughts of them in this article.  

<!--more-->
### Dynamic Programming
The end index of the maximum subarray of a given array(a[n]) can be 0 to n. For a subarray ended at N, the maximum subarray value is MaxSubarray(N-1) + a[N] or a[N]. It means we can solve this problem by using [dp](https://en.wikipedia.org/wiki/Dynamic_programming). We need to loop through the input array to get the maximum sum value of each subarray ended from 0 to n and use a extra variable to store the overall maximum value we got during the loop. For DP solution, it usually needs some extra variables to keep track of the value during the iteration.

```java
  static long maxSubArray(int a[]) {
    int globalMax = a[0], currentMax = a[0];
    for (int i = 1; i < a.length; i++) {
      if (a[i] > currentMax + a[i]) {
        currentMax = Math.max(a[i], currentMax + a[i]);
      }
      globalMax = Math.max(globalMax, currentMax);
    }
    return globalMax;
  } 
```

The time complexity of this algorithm is $$O(n)$$. It uses [Kadane’s algorithm](https://medium.com/@marcellamaki/finding-the-maximum-contiguous-sum-in-an-array-and-kadanes-algorithm-e303cd4eb98c) and can handle negative numbers. 

### Divide and Conquer
The thought of this approach is more straightforward. The maximum subarray can be fully located in the left portion of the given array or fully located int the right portion of the given array or a subarray contains the middle element of the given array. In this case, we could solve this problem by using [divide and conquer](https://en.wikipedia.org/wiki/Divide_and_conquer_algorithm). It can be implemented by recursion.

```java
  private static int maxSubArraySumHelper(int[] a, int i, int j) {
    if (i == j) {
      return a[i];
    }
    int mid = (i + j) / 2;
    int leftMax = maxSubArraySumHelper(a, i, mid);
    int rightMax = maxSubArraySumHelper(a, mid + 1, j);
    int globalMax = Math.max(leftMax, rightMax);

    int leftContiguousMax = a[mid];
    int leftCurrentSum = a[mid];
    for (int k = mid - 1; k >= i; k--) {
      leftCurrentSum += a[k];
      leftContiguousMax = Math.max(leftContiguousMax, leftCurrentSum);
    }

    if (leftContiguousMax <= 0) {
      return globalMax;
    }

    int rightContiguousMax = a[mid + 1];
    int rightCurrentSum = a[mid + 1];
    for (int k = mid + 2; k <= j; k++) {
      rightCurrentSum += a[k];
      rightContiguousMax = Math.max(rightContiguousMax, rightCurrentSum);
    }

    if (rightContiguousMax <= 0) {
      return globalMax;
    }

    return Math.max(globalMax, leftContiguousMax + rightContiguousMax);
  }

  public static long maxSubArray(int[] nums) {
    return maxSubArraySumHelper(nums, 0, nums.length - 1);
  }
```

The time complexity of this algorithm is $$O(n\,log\,n)$$ and it can handle negative numbers as well.
