---
title: leetcode-684
date: 2018-11-29 15:00:23
categories:
- leetcode
tags:
- graph
- union-find
---

# Redundant Connection

In this problem, a tree is an **undirected** graph that is connected and has no cycles.

The given input is a graph that started as a tree with N nodes (with distinct values 1, 2, ..., N), with one additional edge added. The added edge has two different vertices chosen from 1 to N, and was not an edge that already existed.

The resulting graph is given as a 2D-array of edges. Each element of edges is a pair [u, v] with u < v, that represents an undirected edge connecting nodes u and v.

Return an edge that can be removed so that the resulting graph is a tree of N nodes. If there are multiple answers, return the answer that occurs last in the given 2D-array. The answer edge [u, v] should be in the same format, with u < v.

Example 1:
```
Input: [[1,2], [1,3], [2,3]]
Output: [2,3]
```
Explanation: The given undirected graph will be like this:
  1
 / \
2 - 3
Example 2:
```
Input: [[1,2], [2,3], [3,4], [1,4], [1,5]]
Output: [1,4]
```
Explanation: The given undirected graph will be like this:
5 - 1 - 2
    |   |
    4 - 3
Note:
The size of the input 2D-array will be between 3 and 1000.
Every integer represented in the 2D-array will be between 1 and N, where N is the size of the input array.

## 题意分析
该题给定一个代表连接的二维数组，要寻找出一条冗余的边，去掉这条边可以将图变成树。最简单的考虑是利用union-find数据结构，当我们遇到一个连接时，首先检查是否这两个点已经连通，如果连通，那么说明这条边是一条冗余的边，由于题目要求多条冗余边出现时，返回最后一条，所以将这条边加入栈中。如果两点尚未连通，将其连通即可。

## 代码实现
```java
class Solution {
    public int[] findRedundantConnection(int[][] edges) {
        if(edges == null) return null;
        if(edges.length == 0) return null;
        Stack<int[]> stack = new Stack<>();
        WeightedQuickUnionUF uf = new WeightedQuickUnionUF(edges.length + 1);
        for(int i = 0; i < edges.length; i++) {
            int v = edges[i][0];
            int w = edges[i][1];

            if(uf.connected(v, w)) {
                stack.push(edges[i]);
            } else uf.union(v, w);
        }
        if(!stack.isEmpty()) return stack.pop();
        return null;
    }

   private class WeightedQuickUnionUF {
        private int[] parent;
        private int[] size;
        private int count;

        public WeightedQuickUnionUF (int n) {
            count = n;
            parent = new int[n];
            size = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i] = 1;
            }
        }

        public int count() {return count;}

        public int find(int p) {
            while (p != parent[p])
                p = parent[p];
            return p;
        }

        public boolean connected (int p, int q) {
            return find(p) == find(q);
        }

        public void union(int p, int q) {
            int rootP = find(p);
            int rootQ = find(q);
            if (rootP == rootQ) return;

            // make smaller root point to larger one
            if (size[rootP] < size[rootQ]) {
                parent[rootP] = rootQ;
                size[rootQ] += size[rootP];
            } else {
                parent[rootQ] = rootP;
                size[rootP] += size[rootQ];
            }
            count--;
        }
    }
}
```
