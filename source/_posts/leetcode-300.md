---
title: leetcode-300
date: 2018-12-23 06:23:54
categories:
- leetcode
tags:
- dynamic programming
---

# Longest Increasing Subsequence

Given an unsorted array of integers, find the length of longest increasing subsequence.

Example:
```
Input: [10,9,2,5,3,7,101,18]
Output: 4 
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4. 
```

<!-- more -->

Note:

- There may be more than one LIS combination, it is only necessary for you to return the length.
- Your algorithm should run in O(n2) complexity.
Follow up: Could you improve it to O(n log n) time complexity?

## 题意分析

作为动态规划做的第一道题，查找了一些资料，包括tutorialpoints和YouTube上花花酱对这个问题的解读。总结了一下，有如下几个思路可以考虑：
- 首先是最naive的穷举法，我们设置一个和原数组等长的数组，将所有可能的子序列枚举，体现在我们这个new出来的数组对应位置为1，代表原数组对应位置的元素在这次排列中，于是我们穷举出所有的子序列，判断其递增性，然后每次将计数器加1即可。我们每个位置都有两种可能，取还是不取。那么时间复杂度在这种情况下是2^n。可见这种naive的方法只能是在脑海中想想，实际中一定会超时。需要对该方法利用DP的思想进行改良
- 第二种方法的思想是自顶向下的动态规划。(图片摘自花花酱的个人网站)，LIS这个函数，接收一个数组，返回以数组最后一项结尾的LIS长度。所以我们拿到一个数组，这个数组中的每一项都可能是LIS的最后一项，那么我们就分别假定数组中的每一项位LIS的最后一项，然后就会形成若干子问题，如图示的那样。这里面有一个剪枝就是如果数组中前面有某项大于最后一项了，就不要计算这个分支，因为肯定不是递增的一个序列了。所以我们将数组规模减小，递归的求解前面比末尾一项小的各项的LIS长度，然后加1，简单将最后一项加到这个LIS中，就形成了一个LIS，我们计算以每个项结尾的LIS，最后从这些LIS取最大。就可以找到LIS的长度了。但是这个过程有许多重复计算，我们开一个seen数组用来记录我们已经确定的以某项结尾的LIS长度，这样可以节省很多不必要的计算。

{% asset_img 300-ep48-2.png %}

- 第三种是自底向上的动态规划。我们从数组头开始迭代，双重循环，外层表示到哪里结束的子序列，内层对产生的小问题向前查找比外层对应元素小的数字，这样才会形成一个递增序列。然后我们找到最长的那个LIS，将其置于seen数组中。循环结束后返回数组中最大的那个元素。

{% asset_img 300-ep48-3-1.png %}

## 代码实现
自顶向下
```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int N = nums.length;
        if(N == 0) return 0;
        int[] seen = new int[N];
        int res = 0;
        for(int i = 0; i < N; i++)
            res = Math.max(res, lis(seen, nums, i));
        return res;
    }

    private int lis(int[] seen, int[] nums, int i) {
        if(i == 0) return 1;
        else if(seen[i] > 0) return seen[i];
        int res = 1;
        for(int j = 0; j < i; j++)
            if(nums[j] < nums[i])
                res = Math.max(res, lis(seen, nums, j) + 1);
        seen[i] = res;
        return seen[i];
    }
        
}
```
自底向上
```java
public int lengthOfLIS(int[] nums) {
    if(nums == null || nums.length == 0) return 0;
    int N = nums.length;
    int[] seen = new int[N];
    Arrays.fill(seen, 1);
    for(int i = 0; i < N; i++) {
        for(int j = 0; j < i; j++) {
            if(nums[j] < nums[i]) {
                seen[i] = Math.max(seen[i], seen[j] + 1);
            }
        }
    }
    Arrays.sort(seen);
    return seen[N-1];
}
```