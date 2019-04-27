---
title: Leetcode Back Tracking Problems
key: 20190218
tags:
  - Leetcode
  - Interview
---

Back tracking is the common approach to solve problems about combinations, subsets, permutations or all possible solutions. When asked optimize result or max/min values, we should consider dynamic programming approach first as it usually has better time complexity. The time complexity of back tracking problem are various. For example, Hamiltonian cycle: O(N!), WordBreak: O(2^N) and NQueens: O(N!).

<!--more-->

The following code calculate all subsets in a given array, which can be used as a template in many questions.

```java
    public static List<List<Integer>> allCombines(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(),nums, 0);
        return result;
    }

    public static void backtrack(List<List<Integer>> result, List<Integer> tmpList, int[] nums, int index) {
        result.add(new ArrayList<>(tmpList)); //add to result set, usually only when some conditions meet.
        for (int i = index; i < nums.length; i++) { //loop all possible elements for next position.
            tmpList.add(nums[i]); //add.
            backtrack(result, tmpList, nums, i + 1); //back tracking and update parameter.
            tmpList.remove(tmpList.size() - 1); //move back to previous position
        }
    }
```

**Tip:** If there are duplicate elements in the array and we want to remove duplicate subsets, need to sort the array first.

```java
        if(i > index && nums[i] == nums[i-1]) continue;
```

**Leetcode 46.** [Permutations](https://leetcode.com/problems/permutations/)

**Leetcode 47.** [Permutations II](https://leetcode.com/problems/permutations-ii/)

**Leetcode 78.** [Subsets](https://leetcode.com/problems/subsets/)

**Leetcode 90.** [Subsets II](https://leetcode.com/problems/subsets-ii/)

**Leetcode 22.** [Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)

**Leetcode 301.** [Remove Invalid Parentheses](https://leetcode.com/problems/remove-invalid-parentheses/)
