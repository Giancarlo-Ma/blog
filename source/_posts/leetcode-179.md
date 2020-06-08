---
title: leetcode-179
date: 2018-11-17 14:06:14
categories:
- leetcode
tags:
- sort
---

# Largest Number

Given a list of non negative integers, arrange them such that they form the largest number.

Example 1:
```
Input: [10,2]
Output: "210"
```
Example 2:
```
Input: [3,30,34,5,9]
Output: "9534330"
```
Note: The result may be very large, so you need to return a string instead of an integer.

## 分析题意
这道题难度不大，主要考察的还是Comparator接口的使用方法。我们需要通过该接口重新定义比较机制。假定数组长度为n的话，数组中平均字符串长为k，比较2个字符串花费O(k)时间，排序花费O(nlogn)，向StringBuilder中追加字符串花费O(n)，所以总花费为O(nklognk) + O(n) = O(nklognk)，空间复杂度O(n)。

## 代码实现
```java
class Solution {
    public String largestNumber(int[] nums) {
        if(nums == null || nums.length == 0) return "";
        String[] s_num = new String[nums.length];
        // Convert num array to string array
        for(int i = 0; i < nums.length; i++)
            s_num[i] = String.valueOf(nums[i]);
        Comparator<String> cm = new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                String s1 = o1 + o2;
                String s2 = o2 + o1;
                return s2.compareTo(s1);
            }
        };
        Arrays.sort(s_num, cm);
        // Extreme case when the nums is all zero
        if(s_num[0].charAt(0) == '0') return "0";

        StringBuilder sb = new StringBuilder();
        for(String s : s_num)
            sb.append(s);
        return sb.toString();
    }
}
```
