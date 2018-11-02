---
title: Why the size of Hashmap is power of 2 in Java 
key: 20180920
tags: 
  - Data Structure
---
As we know, the Hashmap is implemented by a bucket of linked lists or red-black trees. However, when we look into the source code of Hashmap in Java 8, there are interesting comments on attributes related to the size of bucket, e.g. *DEFAULT_INITIAL_CAPACITY* and *MAXIMUM_CAPACITY*. It is said these values **MUST be a power of two**.

So, what's the reason that the bucket size of a Hashmap has to be a number, which is power of two? 

<!--more-->

1. Java 8 uses a new method to calculate the index of a bucket from the given hash value as below:

    $$i = (h = key.hashCode()) \oplus (h >>> 16)\,\&\,(n-1)$$  

    If n is power of 2, we can have $$ h\,\&\,(n-1) = h\, mod\, n$$. So, bitwise operations are the only things needed to calculate the index. For computers, "bitwise and" operation is faster than operation. 

2. This rule makes resize method get better performance as well. Resize function is used to double the bucket size when the amount of elements in the Hashmap is greater than the thread hold( $$maximum\,capacity * load\,factor$$). Resize is an expensive operation as it does not only need to copy each element to a new bucket but also recalculate the new index value of each element. If the bucket size is always power of two, the elements from the original bucket must either stay at same index, or move with a power of two offset in the new table. So, we can easily identify an element should stay in the same index or move to a new index by using this function : $$e.hash\, \& \,oldCap$$. 

PS: Credits should be given to Meituan Technology Team. This [article](https://zhuanlan.zhihu.com/p/21673805)(Rediscover Jave 8 Hashmap) is the best resource, which I have found, in this topic. 
