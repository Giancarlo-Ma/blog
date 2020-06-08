---
title: leetcode-349
date: 2018-11-19 18:44:35
categories:
- leetcode
tags:
- sort
---

# Intersection of Two Arrays

Given two arrays, write a function to compute their intersection.

Example 1:
```
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2]
```
Example 2:
```
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [9,4]
```
Note:

- Each element in the result must be unique.
- The result can be in any order.

## 题意分析

找出两个数组的交集，不带重复元素。自然想到利用set的特性。首先遍历其中一个数组，将元素不重合的添加到set中，然后对第二个数组做遍历，若set含有这个元素，则加入到结果的set集合中，最后将结果set转换为数组。

## 代码实现

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        // init a set which contains all numbers in nums1
        Set<Integer> set = new HashSet<>();
        for(int num: nums1) {
            set.add(num);
        }

        // The set expression of the result
        Set<Integer> aux_set = new HashSet<>();
        for(int num : nums2) {
            if(set.contains(num)) {
                aux_set.add(num);
            }
        }

        // Convert the set to array
        int[] res = new int[aux_set.size()];
        int i = 0;
        for(int num : aux_set) {
            res[i++] = num;
        }
        return res;
    }
}
```
