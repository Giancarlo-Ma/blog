---
title: leetcode-280
date: 2018-11-19 12:20:19
categories:
- leetcode
tags:
- sort
---

# Wiggle sort

Given an unsorted array nums, reorder it in-place such that `nums[0] <= nums[1] >= nums[2] <= nums[3]`
For example, given `nums = [3, 5, 2, 1, 6, 4]`, one possible answer is `[1, 6, 2, 5, 3, 4]`.

## 分析题意
给定题目，可以观察到从1位置开始的偶数位置的数字都大于等于前一个位置的数字，而奇数位置的数字都小于等于前一个数字，由此可以遍历数组，若遇到奇数位置且其数字大于前一个数字或者偶数位置的数字小于前一个位置的数字，那么将两者做交换处理。由于遍历数组只有交换操作，可知时间复杂度为O(n)。

## 代码实现
```java
public class Solution {
    public void wiggleSort(int[] nums) {
        if(nums.length < 2) return;
        for(int i = 1; i < nums.length; i++) {
            if((i & 1) == 1 && nums[i] < nums[i - 1] || (i & 1) == 0 && nums[i] > nums[i - 1]) {
                int t = nums[i];
                nums[i] = nums[i - 1];
                nums[i - 1] = t;
            }
        }
    }
}
```
