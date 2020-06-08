---
title: leetcode-274
date: 2018-11-17 15:01:48
categories:
- leetcode
tags:
- sort
---

# H-Index

Given an array of citations (each citation is a non-negative integer) of a researcher, write a function to compute the researcher's h-index.

According to the definition of h-index on Wikipedia: "A scientist has index h if h of his/her N papers have at least h citations each, and the other N − h papers have no more than h citations each."

Example:
```
Input: citations = [3,0,6,1,5]
Output: 3
Explanation: [3,0,6,1,5] means the researcher has 5 papers in total and each of them had
             received 3, 0, 6, 1, 5 citations respectively.
             Since the researcher has 3 papers with at least 3 citations each and the remaining
             two with no more than 3 citations each, her h-index is 3.
```
Note: If there are several possible values for h, the maximum one is taken as the h-index.

## 题意分析

该题是要从给出的citations数组中分析出一个数字，数组的有大于等于该数字的项数，他们的值大于等于该数字。思路就是构造一个citations出现次数数组，即代码中的aux，我们再从后向前遍历该数组，每次遍历加上citation出现的次数，若某轮出现累计的次数和大于等于循环变量i，那么我们就找到了这个数字。否则不存在这样的数字，返回0。只涉及到数组单层遍历，时间复杂度为O(N)。

## 实现代码
```java
class Solution {
    public int hIndex(int[] citations) {
        if(citations == null || citations.length == 0) return 0;
        int length = citations.length;
        int[] aux = new int[length + 1];

        for(int i = 0; i < length; i++) {
            if(citations[i] >= length) aux[length]++;
            else aux[citations[i]]++;
        }

        int t = 0;
        for(int i = length; i >= 0; i--) {
            t += aux[i];
            if(t >= i) return i;
        }
        return 0;
    }
}
```
