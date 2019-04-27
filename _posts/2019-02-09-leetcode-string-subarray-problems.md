---
title: Leetcode String & Subarray Problems
key: 20190209
tags: 
  - Leetcode
  - Interview
---
When asked string related problems, we usually represent string in another data structure such as array or hashmap. The frequency, last index or index lists are commonly used as the values in an array or hashmap. Sliding window is an very useful algorithm to solve these problems. Sometime, we could split the string in to many substring by using divide and conquer approach to solve the problem. In most case, string related problems have an O(n) solution.

<!--more-->

## Longest Substring

**Leetcode 3.** [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/){:target="\_blank"}

**Leetcode 159.** Longest Substring with At Most Two Distinct Characters 

**Leetcode 395.** [Longest Substring with At Least K Repeating Characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/){:target="\_blank"}

> This problem could be solved by divide and conquer. For a valid substring which must do not contain character whose frequency less than K. So we could split the string by these character until all characters in the substring satisfy this condition.

**Leetcode 763.** [Partition Labels](https://leetcode.com/problems/partition-labels/){:target="\_blank"}

## Sliding Window

When asked the min/max length of a contiguous subarray with a given constraint, these problems usually can be solved by using sliding window. The max size of the window is limited by the given constraint.

**Leetcode 340**. Longest Substring with At Most K Distinct Characters 

```java
public int lengthOfLongestSubstringKDistinct(String s, int k) {
        if(s == null || s.length() == 0){
            return 0;
        }
        int start = 0, end = 0;
        int count = 0, max = 0;
        int[] charMap = new int[128];
        while(end < s.length()){
            // increase end pointer and count for char at end
            if(charMap[s.charAt(end++)]++ == 0){
                count++;
            }
            // while conditions not met, move start pointer
            while(count > k){
                if(charMap[s.charAt(start++)]-- == 1){
                    count--;
                }
            }
            // calculate max for all valid conditions
            max = Math.max(max, end - start);
        }

        return max;
}
```

**Leetcode 424.** [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/){:target="\_blank"}

**Leetcode 209.** [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/){:target="\_blank"}

**Leetcode 713.** [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/){:target="\_blank"}

**Leetcode 567.** [Permutation in String](https://leetcode.com/problems/permutation-in-string/){:target="\_blank"}


