---
title: leetcode-350
date: 2018-11-19 18:48:55
categories:
- leetcode
tags:
- sort
---

# Intersection of Two Arrays II

Given two arrays, write a function to compute their intersection.

Example 1:
```
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2,2
```
Example 2:
```
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [4,9]
```
Note:

- Each element in the result should appear as many times as it shows in both arrays.
- The result can be in any order.
Follow up:

- What if the given array is already sorted? How would you optimize your algorithm?
- What if nums1's size is small compared to nums2's size? Which algorithm is better?
- What if elements of nums2 are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?

## 题意分析

寻找两个数组的交集，并以尽可能多的次数出现。首先考虑遍历其中一个数组，将各数字出现的次数保存在map中，然后对另外一个数组做遍历，若map含有这个key就加入到结果list中，并且该key对应的value值要减小1，若减小至0，则删除这个key。最后将list转为数组即可。

## 代码实现
```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        // Construct map, key is integer, value is count
        for(int num : nums1) map.put(num, map.getOrDefault(num, 0) + 1);
        List<Integer> aux_list = new ArrayList<>();
        for(int num : nums2) {
            if(map.containsKey(num)) {
                aux_list.add(num);
                map.put(num, map.get(num) - 1);
                map.remove(num, 0);
            }
        }
        // Convert aux_list to array
        int[] result = new int[aux_list.size()];
        for(int i = 0; i < aux_list.size(); i++) {
            result[i] = aux_list.get(i);
        }
        return result;
    }
}
```
