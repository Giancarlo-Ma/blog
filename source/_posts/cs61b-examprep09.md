---
title: cs61b-examprep09(Hashing)
date: 2018-11-02 17:23:22
categories:
- CS61B
tags:
- java
- data structure
---

# 1 Warmup (Spring 2015 MT2: 1c)
Draw the External Chaining Hash Set that results if we insert 5. As part of this insertion, you should also resize from 4 buckets to 8 (in other words, the implementer of this data structure seems to be resizing when the load factor reaches 1.5). Assume that were using the default hashCode for integers, which simply returns the integer itself.

{% asset_img p1.png %}

{% asset_img p2.png %}

# 2 Hashtable Runtimes (Fall 2016 MT2: Q3)
Consider a hash table that uses external chaining and also keeps track of the number of keys that it contains. It stores each key at most once; adding a key a second
time has no effect. It takes the steps necessary to ensure that the number of keys is always less than or equal to twice the number of buckets (i.e., that the load factor is ≤ 2). Assume that its hash function and comparison of keys take constant time. All bounds should be a function of N, the number of elements in the table.
1. Give Θ() bounds on the worst-case times of adding an element to the table when the load factor is 1 and when it is exactly 2 before the addition.
<span style="color:red">Bound for load factor 1: Θ(N). Worse case they are all in the same bucket.</span>
<span style="color:red">Bound for load factor 2: Θ(N). Assuming that resize doesn’t do an duplicate check. If the resize is implemented such that there is a duplicate check (e.g. resize just calls put), it could be Θ(N2). </span>
