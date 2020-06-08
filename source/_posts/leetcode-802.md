---
title: leetcode-802
date: 2018-12-13 13:04:37
categories:
- leetcode
tags:
- graph
---

# EventualSafeNode
In a directed graph, we start at some node and every turn, walk along a directed edge of the graph.  If we reach a node that is terminal (that is, it has no outgoing directed edges), we stop.

Now, say our starting node is eventually safe if and only if we must eventually walk to a terminal node.  More specifically, there exists a natural number K so that for any choice of where to walk, we must have stopped at a terminal node in less than K steps.
<!-- more -->
Which nodes are eventually safe?  Return them as an array in sorted order.

The directed graph has N nodes with labels 0, 1, ..., N-1, where N is the length of graph.  The graph is given in the following form: graph[i] is a list of labels j such that (i, j) is a directed edge of the graph.

Example:
```
Input: graph = [[1,2],[2,3],[5],[0],[5],[],[]]
Output: [2,4,5,6]
```
Note:

- graph will have length at most 10000.
- The number of edges in the graph will not exceed 32000.
- Each graph[i] will be a sorted list of different integers, chosen within the range [0, graph.length - 1].

## 题意分析

该题让我们找安全结点，所谓安全结点，就是在有向图中，从该点出发，绝不会走到一个环路，最终都会走到一个出度为0的结点。我们维护一个color数组，数值0表示还没访问，1表示不安全，2表示安全。采用dfs策略，我们初始都要设置结点为不安全，然后对邻居进行dfs，如果出现环路，表明我们将其设置为1是正确的，一路在递归调用栈中返回false就可以。如果dfs所有邻居还找不到一个环路，也就是我们没有遇到一个结点在递归调用栈上（，因为如果在dfs的过程中，遇到一个结点在调用栈上（就是为color对应项为1，那肯定出现环路。）我们就可以将对应结点设置为2，返回true。而在主调函数中，我们需要从每个结点出发去遍历图，如果返回true，则加入返回的list中。

## 代码实现
```java
class Solution {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        List<Integer> res = new ArrayList<>();
        if(graph == null || graph.length == 0) return res;
        int N = graph.length;
        int[] color = new int[N];
        for(int i = 0; i < N; i++) {
            boolean c = dfs(graph, color, i);
            if(c) res.add(i);
        }

        return res;
    }

    private boolean dfs(int[][] graph, int[] color, int v) {
        // 对已经访问过的结点，在调用栈上为1，1！=2返回false，否则可以返回true
        if(color[v] != 0) return color[v] == 2;

        color[v] = 1;
        for(int w : graph[v]) {
            if(!dfs(graph, color, w)) return false;
        }
        color[v] = 2;
        return true;
    }
}
```