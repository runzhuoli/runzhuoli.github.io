---
title: The Secret Improvement of HashMap in Java 8
key: 20180831
tags: 
  - Data Structure
  - Java 
---
HashMap is one of the most widely used data structure in Java. As we know, HashMap stores data in a number of buckets and each bucket is a linked list. The new elements will be added into the bucket at index of $$(n - 1)\,\&\,hash$$ (n is the current capacity in HashMap). When the size of a single linked list increases, the performance of retrievals gets worse. Java 8 improves the performance by converting the linked list into a [Red-black Tree](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree){:target="_blank"} if the size is bigger than a threshold. I am going to talk about the basics of red-black tree and how Java applies this methodology in HashMap.

<!--more-->

![HashMap](http://www.itcuties.com/wp-content/uploads/2014/05/ITCuties-HashMap-HashTable-linear-probing-and-separate-chaining.png)

### Red-black Tree

Red-black tree is an approximately balanced binary search tree, which has the following properties:

1. Each node is either red or black.
2. The root node and all leaves are black.
3. If a node is red, then both its children are black.
4. Every path from a given node to any of its descendant NIL nodes contains the same number of black nodes.

These constraints enforce a critical property of red–black trees: the path from the root to the farthest leaf is no more than twice as long as the path from the root to the nearest leaf. As property 4 guarantees that all the paths from root node to leaves contains the same amount of black nodes. So, the shortest path only contains black nodes. We assume the shortest path contains N black nodes. Based on property 3, the longest path in the tree could be inserting one red node after every black node in a path. So the longest path contains $$2*N$$ nodes. When considering the NIL leaves, the shortest path is $$N+1$$ and the longest path is $$2*N+1$$. 

This property guarantees log based time complexity of operations in red-black tree. The time complexities of Search, Insertion, Deletion are all $$O(log\,n)$$ 

Resources about [insertion](https://www.geeksforgeeks.org/red-black-tree-set-2-insert/){:target="_blank"} and [deletion](https://www.geeksforgeeks.org/red-black-tree-set-3-delete-2/){:target="blank"} in Red-black tree can be referred here.

### Treeify in HashMap

There are three static variables in HashMap related to "treeify" functions in HashMap:

- **TREEIFY_THRESHOLD**(8): The bin count threshold for using a tree rather than list for a bin.  Bins are converted to trees when adding an element to a bin with at least this many nodes.
- **UNTREEIFY_THRESHOLD**(6): The bin count threshold for untreeifying a (split) bin during a resize operation.
- **MIN_TREEIFY_CAPACITY**(64): The smallest table capacity for which bins may be treeified. Otherwise the table is resized if too many nodes in a bin. 

The following method is copied from the source code of HashMap in Java 8, which converts the linked list at index for a given hash value into a red-black tree. It only convert the linked list if the amount of buckets is greater than MIN_TREEIFY_CAPACITY, otherwise it calls resize method. Resize method doubles the size of the table which makes each bucket contains less nodes. 

```java
final void treeifyBin(Node<K,V>[] tab, int hash) {
    int n, index; Node<K,V> e;
    if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
        resize();
    else if ((e = tab[index = (n - 1) & hash]) != null) {
        TreeNode<K,V> hd = null, tl = null;
        do {
           TreeNode<K,V> p = replacementTreeNode(e, null);
           if (tl == null)
               hd = p;
           else {
               p.prev = tl;
               tl.next = p;
            }
            tl = p;
        } while ((e = e.next) != null);
        if ((tab[index] = hd) != null)
            hd.treeify(tab);
    }
}
```

The following code is the inner class - *TreeNode* in HashMap. It implements  Extends *LinkedHashMap.Entry*, which in turn extends the inner class *Node*, so it is easier to untreeify a red-black tree to linked list.

```java
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
    TreeNode<K,V> parent;  // red-black tree links
    TreeNode<K,V> left;
    TreeNode<K,V> right;
    TreeNode<K,V> prev;    // needed to unlink next upon deletion
    boolean red;
}
```
The treeify method loops through all nodes of a given list and uses red-black tree insertion algorithm to construct a new red-black tree. After it forms the whole tree, moveRootToFront method is called to move the root node of the red-black tree to the front of the given linked list. 

```java
static <K,V> void moveRootToFront(Node<K,V>[] tab, TreeNode<K,V> root) {
    int n;
    if (root != null && tab != null && (n = tab.length) > 0) {
        int index = (n - 1) & root.hash;
        TreeNode<K,V> first = (TreeNode<K,V>)tab[index];
        if (root != first) {
            Node<K,V> rn;
            tab[index] = root;
            TreeNode<K,V> rp = root.prev;
            if ((rn = root.next) != null)
                ((TreeNode<K,V>)rn).prev = rp;
            if (rp != null)
               rp.next = rn;
            if (first != null)
                first.prev = root;
            root.next = first;
            root.prev = null;
          }
         assert checkInvariants(root);
     }
}
```
### References
1. [Red-black tree in 4](https://www.youtube.com/watch?v=qvZGUFHWChY){:target="blank"}


