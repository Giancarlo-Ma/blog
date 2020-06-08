---
title: leetcode-685
date: 2018-12-13 15:33:55
categories:
- leetcode
tags:
- graph
- union-find
---

# Redundant Connection II

In this problem, a rooted tree is a directed graph such that, there is exactly one node (the root) for which all other nodes are descendants of this node, plus every node has exactly one parent, except for the root node which has no parents.

The given input is a directed graph that started as a rooted tree with N nodes (with distinct values 1, 2, ..., N), with one additional directed edge added. The added edge has two different vertices chosen from 1 to N, and was not an edge that already existed.

The resulting graph is given as a 2D-array of edges. Each element of edges is a pair [u, v] that represents a directed edge connecting nodes u and v, where u is a parent of child v.
<!-- more -->
Return an edge that can be removed so that the resulting graph is a rooted tree of N nodes. If there are multiple answers, return the answer that occurs last in the given 2D-array.
```
Example 1:
Input: [[1,2], [1,3], [2,3]]
Output: [2,3]
Explanation: The given directed graph will be like this:
  1
 / \
v   v
2-->3
```
```
Example 2:
Input: [[1,2], [2,3], [3,4], [4,1], [1,5]]
Output: [4,1]
Explanation: The given directed graph will be like this:
5 <- 1 -> 2
     ^    |
     |    v
     4 <- 3
```
Note:
- The size of the input 2D-array will be between 3 and 1000.
- Every integer represented in the 2D-array will be between 1 and N, where N is the size of the input array.

## 题意分析
684的升级版，684只需要在无向图中寻找冗余边，而这个问题明确了父子关系，所以出现冗余边的情况有两种，一种是某个结点有两个parent，另一种情况是出现环路。依然采用union-find的策略。

## 代码实现
```java
class Solution {
    public int[] findRedundantDirectedConnection(int[][] edges) {
        int[] cand1 = {-1, -1};
        int[] cand2 = {-1, -1};
        int[] root = new int[edges.length + 1];

        for(int[] edge : edges) {
            int father = edge[0];
            int son = edge[1];
            if(root[son] != 0) {
                cand1 = new int[]{root[son], son};
                cand2 = new int[]{father, son};
                // 标记第二个边为invalid
                edge[1] = 0;
            } else {
                root[son] = father;
            }
        }

        for(int i = 0; i < root.length; i++)
            root[i] = i;

        for(int[] edge : edges) {
            int father = edge[0];
            int son = edge[1];
            if(edge[1] == 0) continue;
            if(find(root, father) == son) {
                if(cand1[0] == -1) return edge;
                else return cand1;
            }
            root[son] = father;
        }
        return cand2;
    }

    private int find(int[] root, int node) {
        while(node != root[node])
            node = root[node];
        return node;
    }
}
```