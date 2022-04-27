---
title: Leetcode Templates
key: 20190601
tags:
  - Leetcode
  - Interview
---

<!--more-->

## Binary Search

```java
    public int search(int[] nums, int target) {
        if(nums == null || nums.length == 0){
            return -1;
        }
        int left =0, right = nums.length - 1;
        while(left <= right){
            int middle = (left + right) / 2;
            if(nums[middle] == target) return middle; //f(x)
            if(nums[middle] > target){ // g(x)
                right = middle - 1;
            } else{
                left = middle + 1;
            }
        }
        // left is the smallest index which satisfice g(x)
        // in this case, left is upper bond
        return -1;
    }
```

## Sliding Window (Two pointers)

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

## Tree

### BST

Inorder traversal a Binary Serch Tree with iteration which will get a sorted array.

```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> list = new ArrayList<>();
    if(root == null) return list;
    Stack<TreeNode> stack = new Stack<>();
    while(root != null || !stack.empty()){
        while(root != null){
            stack.push(root);
            root = root.left;
        }
        root = stack.pop();
        list.add(root.val);
        root = root.right;

    }
    return list;
}
```

## Back Tracking

### Combination

```java
    public static List<List<Integer>> allCombine(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), nums, 0);
        return result;
    }

    public static void backtrack(List<List<Integer>> result, List<Integer> tmpList, int[] nums, int index) {
        // Make a copy of tmpList
        result.add(new ArrayList<>(tmpList));
        // Loop through all possibilities for next position (Purning)
        for (int i = index; i < nums.length; i++) {
            tmpList.add(nums[i]);
            backtrack(result, tmpList, nums, i + 1);
            tmpList.remove(tmpList.size() - 1);
        }
    }
```

### Permutation

```java
    public List<List<Integer>> permuteUnique(int[] nums) {
        // Need to srot the array first if it contains the same numbers
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), nums, new boolean[nums.length]);
        return result;
    }

    private void backtrack(List<List<Integer>> result, List<Integer> tmp, int[] nums, boolean[] visited){
        // Only add valid list to result
        if(tmp.size() == nums.length){
            result.add(new ArrayList(tmp));
            return;
        }

        for(int i=0; i < nums.length; i++){
            // Do not put the same # at the same position twice
            if(visited[i] || i > 0 && nums[i] == nums[i-1] && !visited[i-1]){
                continue;
            }
            tmp.add(nums[i]);
            visited[i] = true;
            backtrack(result, tmp, nums, visited);
            visited[i] = false;
            tmp.remove(tmp.size() -1);
        }
    }
```

## Graph

### BFS

Leetcode 286

```java
{% raw %}
    public void wallsAndGates(int[][] rooms) {
        if(rooms == null || rooms.length == 0){
            return;
        }
        int m = rooms.length, n = rooms[0].length;
        Queue<int[]> queue = new ArrayDeque<>();
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(rooms[i][j] == 0){
                    queue.offer(new int[]{i, j, 0});
                }
            }
        }
        int[][] directions = new int[][]{{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        while(!queue.isEmpty()){
            int[] cur = queue.poll();
            for(int[] dir : directions){
                int r = cur[0] + dir[0];
                int c = cur[1] + dir[1];
                // out of boundaries
                if(r < 0 || r >= m || c < 0 || c >= n){
                    continue;
                }
                // condition check/check visited or not
                if(rooms[r][c] != Integer.MAX_VALUE){
                    continue;
                }
                // set visted and update node
                rooms[r][c] = cur[2] + 1;
                // add neighbour into queue
                queue.offer(new int[]{r, c, rooms[r][c]});
            }
        }
    }
{% endraw %}
```

### DFS

Leetcode 733

```java
   public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int color = image[sr][sc];
        // do dfs for each source point
        if(color != newColor)
            dfs(image, sr, sc, color, newColor);
        return image;
    }

    private void dfs(int[][] image, int row, int col,int color, int newColor){
        // if condition do not meet, return
        if(row < 0 || row >= image.length || col < 0 || col >= image[0].length
           || image[row][col] != color){
            return;
        }
        // set visted and operate current point
        image[row][col] = newColor;
        dfs(image, row, col - 1, color, newColor);
        dfs(image, row, col + 1, color, newColor);
        dfs(image, row - 1, col, color, newColor);
        dfs(image, row + 1, col, color, newColor);
    }
```

### Dijkstra

Leetcode 743

```java
    public int networkDelayTime(int[][] times, int N, int K) {
        Map<Integer, List<int[]>> graph = new HashMap<>();
        for(int[] time : times){
            List<int[]> edges = graph.getOrDefault(time[0], new ArrayList<>());
            edges.add(new int[]{time[1], time[2]});
            graph.put(time[0], edges);
        }
        // target, weight -- use heap to reduce time complexity
        PriorityQueue<int[]> pq = new PriorityQueue<>((n1, n2) -> n1[1] -n2[1]);
        pq.offer(new int[]{K, 0});
        // Init distance map
        int[] dist = new int[N+1];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[K] = 0;
        int count = 0;
        while(!pq.isEmpty()){
            int[] cur = pq.poll();
            if(dist[cur[0]] < cur[1]) continue;
            count++;
            if(graph.get(cur[0]) != null){
                for(int[] edge : graph.get(cur[0])){
                    int d = cur[1] + edge[1];
                    if(d < dist[edge[0]]){
                        dist[edge[0]] = d;
                        pq.offer(new int[]{edge[0], d});
                    }
                }
            }
        }
        if(count != N) return -1;
        int max = 0;
        for(int i = 1; i <= N; i++){
            max = Math.max(max, dist[i]);
        }

        return max;
    }
```

### Toplogical Sort

```java
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        if(prerequisites == null || prerequisites.length == 0) return true;

        Map<Integer, Integer> inDegMap = new HashMap<>();
        Map<Integer, List<Integer>> graph = new HashMap<>();

        for(int[] pre: prerequisites){
            inDegMap.put(pre[1], inDegMap.getOrDefault(pre[1], 0) + 1);
            List<Integer> list = graph.getOrDefault(pre[0], new ArrayList<>());
            list.add(pre[1]);
            graph.put(pre[0], list);
        }

        Queue<Integer> queue = new ArrayDeque<>();

        for(int i = 0; i < numCourses; i++){
            if(!inDegMap.containsKey(i)){
                queue.offer(i);
            }
        }
        while(!queue.isEmpty()){
            int cur = queue.poll();
            List<Integer> list = graph.get(cur);
            if(list == null) continue;
            for(int n : list){
                int count = inDegMap.get(n);
                if(count == 1){
                    inDegMap.remove(n);
                    queue.offer(n);
                }else{
                    inDegMap.put(n, count -1);
                }
            }
        }

        return inDegMap.size() == 0;
    }
```

## Union Find

```java
class UnionFind{
    int[] parents;
    int[] ranks;

    public UnionFind(int n){
        parents = new int[n];
        ranks = new int[n];
        for(int i = 0; i < n; i++){
            parents[i] = i;
        }
    }

    public int find(int i){
        int p = parents[i];
        if(i == p){
            return p;
        }
        return parents[p] = find(p);
    }

    public void union(int a, int b){
        int root1 = find(a);
        int root2 = find(b);
        if(root1 == root2){
            return;
        }
        if(ranks[root1] < ranks[root2]){
            parents[root1] = root2;
        }else if(ranks[root2] < ranks[root1]){
            parents[root2] = root1;
        }else{
            parents[root2] = root1;
            ranks[root1]++;
        }
    }

}

```

## Trie

```java
class TrieNode{
    Map<Character, TrieNode> children;
    boolean isWord;

    public TrieNode(){
        children = new HashMap<>();
    }
}

class Trie{
    TrieNode root;

    public Tire(){
        root = new TrieNode();
    }

    public void insert(String word){
        TrieNode p = root;
        for(char c : word.toCharArray){
            p.children.computeIfAbsent(c, k -> new TrieNode());
            p = p.children.get(c);
        }
        p.isWord = true;
    }

    public TrieNode find(String prefix){
        TrieNode p = root;
        for(char c : prefix.toCharArray()){
            if(p.children.get(c) == null){
                return null;
            }
            p = p.children.get(c);
        }

        return p;
    }
}
```

## Segment Tree

```java
class SegmentTreeNode{
    int start;
    int end;
    int sum;
    SegmentTreeNode left;
    SegmentTreeNode right;

	public SegmentTreeNode(int start, int end){
        this.start = start;
        this.end = end;
    }

    private SegmentTreeNode buildTree(int start, int end, int[] nums){
	    if(start > end) return null;
        SegmentTreeNode node = new SegmentTreeNode(start, end);
 	    if(start == end){
	        node.sum = nums[start];
	        return node;
        } else {
            int mid = start + (end - start)/2;
            node.left = buildTree(start, mid, nums);
            node.right = buildTree(mid + 1, end, nums);
	        node.sum = node.left.sum + node.right.sum;
        }

        return node;
    }

    private void updateNode(SegmentTreeNode node, int index, int val){
	    if(node.start == node.end){
	        node.sum = val;
	        return;
        }
        int mid = node.start + (node.end - node.start)/2;
        if(mid >= index){
            updateNode(node.left, index, val);
        }else{
            updateNode(node.right, index, val);
        }
        node.sum = node.left.sum + node.right.sum;
    }

    private int sumRange(SegmentTreeNode node, int start, int end){
	    if(node.start == start && node.end == end){
	        return node.sum;
        }
        int mid = node.start + (node.end - node.start)/2;
        if(start > mid){
	        return sumRange(node.right, start, end);
        }else if(end <= mid){
	        return sumRange(node.left, start, end);
        }else{
            return sumRange(node.left, start, mid) + sumRange(node.right, mid + 1, end);
        }
    }
}
```
