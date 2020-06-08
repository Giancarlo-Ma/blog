---
title: leetcode-75
date: 2018-11-19 17:14:15
categories:
- leetcode
tags:
- sort
---

# Sort colors
Given an array with n objects colored red, white or blue, sort them in-place so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

Note: You are not suppose to use the library's sort function for this problem.

Example:
```
Input: [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```
Follow up:

A rather straight forward solution is a two-pass algorithm using counting sort.
First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
Could you come up with a one-pass algorithm using only constant space?
## 题意分析
该题就是所谓的荷兰国旗问题，红白蓝三色排序。我们对数组进行遍历，将1当作pivot，类似快排。设定两个指针，lt和gt，若该数字为0，说明应在左侧，将lt和当前位置数字做交换。若该数字为1，说明数字大于pivot，则应与gt做交换。若相等，只需要继续遍历而不需要做交换。终止条件是遍历变量i大于gt，遍历完所有元素。由于只遍历数组，所以时间复杂度为O(n)。

## 代码实现

```java
class Solution {
    public void sortColors(int[] nums) {
        if(nums == null || nums.length == 1) return;
        int lt = 0;
        int gt = nums.length - 1;
        int i = 0;
        while(i <= gt){
            if(nums[i] > 1) swap(nums, i, gt--);
            else if(nums[i] < 1) swap(nums, i++, lt++);
            else i++;
        }
    }
    private void swap(int[] arr, int i, int j) {
        int t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }
}
```
