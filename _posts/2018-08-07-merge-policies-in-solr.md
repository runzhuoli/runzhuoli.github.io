---
title: Merge Policies in Solr 
key: 20180807
tags: Algorithm Solr
---

I happened to get an chance to optimize the Solr configuration of [Home Depot Canada](https://www.homedepot.ca){:target="_blank"}. My mainly improvement is focused on the segments merge policies. However there are limited resources on-line, I spent some time looking into the source code of [LogMergePolicy](https://github.com/apache/lucene-solr/blob/master/lucene/core/src/java/org/apache/lucene/index/LogByteSizeMergePolicy.java){:target="_blank"} and [TieredMergePolicy](https://github.com/apache/lucene-solr/blob/master/lucene/core/src/java/org/apache/lucene/index/TieredMergePolicy.java){:target="_blank"}. I am going to share how does these two mostly used Solr merge polices work and my findings.

<!--more-->
In these two algorithms, both would not merge segment which is being merged (By default, using ConcurrentMergeScheduler, Solr executes each merge in a separate thread).  

### Log Merge Policy

#### Assumptions: 

The number of merge-able segments is N; Level log span is P=0.75(by default); The merge factor is M; Size of a segment (exclude deflection documents) in MB is $$S_n$$, if $$S_n \lt 1.6$$, then $$S_n = 1.6$$; Any segment whose size is greater than 2 GiB will never be merged (By default, maxMergeSize has a fixed value of 2 GiB.).

#### Algorithm:

1. Sort all merge-able segments by name (created timestamp);
2. Calculate level info value for each segment:

    $$L_n=log\frac {S_n} {M}$$      

3. Set the pointer $$s$$  to the first segment ($$s=0$$). Calculate current level boundary:
   
   Maximum level info value:
   
   $$L_{max}=\max_{\atop\scriptstyle s\to n}\{l_n\}$$
   
   Minimum level info value:

   $$L_{min}=L_{max}-P$$
           
4. Find the newest segment($$v$$) whose level info value is greater than $$L_{min}$$. All segments older than or equal to this segment belong to the current level. If the number of segments of the current level is bigger than M, merge M segments recursively until current level’s size is smaller than M.

5. Set the pointer $$s=v+1$$ and go to step 3, until $$s \gt N$$;

### Tiered Merge Policy

#### Assumptions: 

The number of merge-able segment is N; Size in MB (exclude deletion documents) of a segment is $$S_n$$, if $$S_n \lt 2$$, then $$S_n = 2$$; Size in MB(include deletion documents) is $$S'_n$$; Maximum merge at once is M; Segments per tier is T; Any segment whose size is greater than 2.5 GiB will be excluded(By dufault, maxMergedSegmentBytes = $$5*1024^3$$).

#### Algorithm:

1. Sort all merge-able segments by size desc.

2. It assumes, for tier k, the average size of a segment is $$2*M^{k-1}$$:

   So, the minimum Tier needed to hold all documents:

   $$T_{min}=\lceil log_M{2T - \sum_{i=0}^n S_i*(1-M) \over {2T}}\rceil$$

   Then, the maximum segments needed (its our maximum budget):

   $$P=(t_{min}-1)*T + \lceil {\sum_{i=0}^n S_n  - \sum_{i=1}^{t_{min}-1} T*2M^{i-1} \over {2M^{t_{min}-1}}}\rceil$$

3. If $$P \lt N$$ find best merge combination based on Merge Score Sc:

   $$Sc_{max}=\max_{\atop0 \le i \lt N}\{Sc(i,i+M)\}$$

   Merge score of segments combination (i to i+M) is calculated by skew (basically how "equally sized" the segments being merged are), total size (smaller merges are favored but if total size > 5 GB, skew will be set to $$1 \over {min(M,T)}$$), and how many deleted documents will be reclaimed as below:
 
   $$Sc(i,i+M) = {S_i \over \sum_i^{i+M} S_i} (\sum_i^{i+M} S_i)^{0.05}  ({\sum_i^{i+M} S_i \over \sum_i^{i+M} {S'}_i})^2$$

4.  Merge segments combination from i to i+M with smallest merge score $$Sc(i,i+M)$$.

### Conclusion:

TeiredMerge Algorithm tries to find the best merge combinations based on several factors(segment sizes, balance, deletion rate). However, LogMerge Algorithm merges segments based on created time stamp only. The sizes of merged segments in LogMerge are unpredictable. Solr started to use TeiredMerge algorithm as the default algorithm after it introduced. Based on Michael McCandless, the author of "Lucene in Action":  To minimize merge cost, we ideally would merge only equal-sized segments, and merge a larger number of segments at a time. When I tried to solve the real problem, I tried to produce equal-sized segments by using "Auto Commit" and I set maxMergeAtOnce to a bigger number to make sure more documents are merged at a time. Moreover, I controlled allowed segments amount(maximum budget) to a smaller number by setting segmentPerTeir to a lower value.

### References:

1. [Visualizing Lucene's segment merges](http://blog.mikemccandless.com/2011/02/visualizing-lucenes-segment-merges.html){:target="_blank"}

2. [Solr: Optimize Is Not Bad For You Deep Dive Into The Segment Merge Abyss](https://www.youtube.com/watch?v=NM2dyQ3bc2w){:target="_blank"}
