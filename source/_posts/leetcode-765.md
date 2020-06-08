---
title: leetcode-765
date: 2018-12-17 13:15:10
categories:
- leetcode
tags:
- graph
---

# Couples Holding Hands
N couples sit in 2N seats arranged in a row and want to hold hands. We want to know the minimum number of swaps so that every couple is sitting side by side. A swap consists of choosing any two people, then they stand up and switch seats.

The people and seats are represented by an integer from 0 to 2N-1, the couples are numbered in order, the first couple being (0, 1), the second couple being (2, 3), and so on with the last couple being (2N-2, 2N-1).

The couples' initial seating is given by row[i] being the value of the person who is initially sitting in the i-th seat.

Example 1:
```
Input: row = [0, 2, 1, 3]
Output: 1
```
Explanation: We only need to swap the second (row[1]) and third (row[2]) person.
Example 2:
```
Input: row = [3, 2, 0, 1]
Output: 0
```
Explanation: All couples are already seated side by side.
Note:

- len(row) is even and in the range of [4, 60].
- row is guaranteed to be a permutation of 0...len(row)-1.

## 题意分析

给定一个row数组，代表每个座位做的人，0和1是情侣，2和3是情侣，我们最终的目标就是将情侣都安排到一起。这里采用贪心策略，首先判断是否相邻的两个已经是情侣关系，如果是继续扫描下一组，如果不是，就将第二个换成第一个的情侣，而我们怎么找到第一个人的情侣的位置呢，好让第二个做正确的交换，这里pos数组就登场啦。pos数组刚好是row数组的逆，所谓逆，就是将索引和值对调，也就是pos数组的索引是row数组的值，因为大家都是数字，且不重复，这一点可以很容易的达到，只需要遍历row数组即可。这里采用贪心的策略是因为我们的每次交换至少可以完成一对情侣，最好的情况可以使得两对情侣都坐在一起，所以算法正确性毋庸置疑。

## 代码实现
```java
class Solution {
   public int minSwapsCouples(int[] row) {
        int n = row.length;
        int[] pos = new int[n];
        int res = 0;
        for(int i = 0; i < n; i++) {
            pos[row[i]] = i;
        }

        for(int i = 0; i < n; i += 2) {
            int j = row[i] % 2 == 0 ? row[i] + 1 : row[i] - 1;
            if(row[i + 1] != j) {
                swap(row, pos, i + 1, pos[j]);
                res++;
            }
        }
        return res;
    }

    private void swap(int[] row, int[] pos, int i, int j) {
        int temp = row[i];
        row[j] = row[i];
        pos[row[j]] = i;
        row[i] =temp;
        pos[row[i]] = j;
    }
}
```