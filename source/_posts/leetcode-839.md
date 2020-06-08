---
title: leetcode-839
date: 2018-12-14 12:43:39
categories:
- leetcode
tags:
- union find
- graph
---

# Similar String Groups

Two strings X and Y are similar if we can swap two letters (in different positions) of X, so that it equals Y.

For example, "tars" and "rats" are similar (swapping at positions 0 and 2), and "rats" and "arts" are similar, but "star" is not similar to "tars", "rats", or "arts".

Together, these form two connected groups by similarity: {"tars", "rats", "arts"} and {"star"}.  Notice that "tars" and "arts" are in the same group even though they are not similar.  Formally, each group is such that a word is in the group if and only if it is similar to at least one other word in the group.

We are given a list A of strings.  Every string in A is an anagram of every other string in A.  How many groups are there?
```
Example 1:

Input: ["tars","rats","arts","star"]
Output: 2
```
Note:

- A.length <= 2000
- A[i].length <= 1000
- A.length * A[i].length <= 20000
- All words in A consist of lowercase letters only.
- All words in A have the same length and are anagrams of each other.
- The judging time limit has been increased for this question.

## 题意分析

给定一个字符串数组，将相似的字符串分在一个group，问这样的group有多少。这里我们采用union-find策略，理由union-find数据结构的特点，只需要将相似字符串union，最后看这个uf的size就可以了。设字符串长度为M，字符串数组长度为N，则最坏时间复杂度为MN^2。

## 代码实现
```java
class Solution {
    public int numSimilarGroups(String[] A) {
        UF uf = new UF(A.length);
        for(int i = 0; i < A.length - 1; i++) {
            for(int j = i + 1; j < A.length; j++) {
                if(similar(A[i], A[j])) uf.union(i, j);
            }
        }
        return uf.count();
    }

    private boolean similar(String s1, String s2) {
        int count = 0;
        // 如果有两个字符不同则相似
        for(int i = 0; i < s1.length(); i++) {
            if(s1.charAt(i)!= s2.charAt(i) && ++count > 2) return false;
        }
        return true;
    }

    private class UF {
        private int[] parents;
        private int[] size;
        private int count;

        public UF(int n) {
            parents = new int[n];
            size = new int[n];
            
            for(int i = 0; i < n; i++) {
                parents[i] = i;
                size[i] = 1;
            }
            count = n;
        }

        public int count() {return count;}

        public boolean connected(int p, int q) {return find(p) == find(q);}

        public int find(int id) {
            while(id != parents[id])
                id = parents[id];
            return id;
        }

        public void union(int i, int j) {
            int iid = find(i);
            int jid = find(j);
            if(iid == jid) return;

            if(size[iid] > size[jid]) {
                size[iid] += size[jid];
                parents[jid] = iid;
            } else {
                size[jid] += size[iid];
                parents[iid] = jid;
            }
            count--;
        }
    }
}
```