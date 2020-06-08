---
title: leetcode-854
date: 2018-12-17 11:36:42
categories:
- leetcode
tags:
- graph
- BFS
---

# K-Similar Strings

Strings A and B are K-similar (for some non-negative integer K) if we can swap the positions of two letters in A exactly K times so that the resulting string equals B.
<!-- more -->
Given two anagrams A and B, return the smallest K for which A and B are K-similar.

Example 1:
```
Input: A = "ab", B = "ba"
Output: 1
```
Example 2:
```
Input: A = "abc", B = "bca"
Output: 2
```
Example 3:
```
Input: A = "abac", B = "baca"
Output: 2
```
Example 4:
```
Input: A = "aabc", B = "abca"
Output: 2
```
Note:

- 1 <= A.length == B.length <= 20
- A and B contain only lowercase letters from the set {'a', 'b', 'c', 'd', 'e', 'f'}

## 题意分析

这道题给定两个字符串，对第一个字符串作交换操作，找到最小的交换次数，使得最终的结果和第二个字符串是相同的。做这道题的时间跨度是三天左右，昨天忙了一天的入党转正的事情，今天第一件事就是重新拿起这道题看了一下。第一次做这道题的时候想起了leetcode-765这道情侣牵手的问题，后来在采取相同策略的时候发现这个题和765最大的不同就是这个题会包含重复字符，而采用空间上增加一个map，后面添加的会覆盖前面添加的条目（写这篇文章的时候，突然想到，可不可以将map的值设置为list？这里留个疑问），还是参考了discussion里面的解答。找最小次数交换想到BFS，我们在每一轮找到可能是答案的解节点，将这个解结点加入队列，然后不断做BFS操作，为了标识访问过的字符串，增加一个set数据结构，以避免重复BFS死循环。举个例子，`A = "aabc", B = "abca"`，首先我们找到第一个不同的字符，在这里为1位置，分别对应字符a和字符b，然后我们想这一轮将第1个位置的字符解决掉，向后扫描找到一个符合条件的字符，因为这个字符的位置理应是b，所以我们找到了第2个字符，将其交换，并发现后面并没有b了，所以我们第一轮的操作就是将A字符串的第1个字符和第2个字符做交换操作，并首先观察A字符串是否已经和B字符串一样了，如果是，我们返回res，否则，判断set是否含有这个字符串，如果不含有也就是set的add操作返回true，那么我们将其加到queue。以此类推。

## 代码实现
```java
class Solution {
    public int kSimilarity(String A, String B) {
        if(A.equals(B)) return 0;
        Set<String> vis = new HashSet<>();
        Queue<String> q = new ArrayDeque<>();
        vis.add(A);
        q.offer(A);
        int res = 0;

        while(!q.isEmpty()) {
            res++;
            for(int sz = q.size(); sz > 0; sz--) {
                String s = q.poll();
                int i = 0;
                while(s.charAt(i) == B.charAt(i)) i++;
                for(int j = i + 1; j < s.length(); j++) {
                    if(s.charAt(j) == B.charAt(j) || s.charAt(j) != B.charAt(i)) continue;
                    String snew = swap(s, i, j);
                    if(snew.equals(B)) return res;
                    if(vis.add(snew)) q.offer(snew);
                }
            }
        }
        return res;
    }
    public String swap(String s, int i, int j) {
        char[] scharArr = s.toCharArray();
        char temp = scharArr[i];
        scharArr[i] = scharArr[j];
        scharArr[j] = temp;
        return new String(scharArr);
    }
}
```