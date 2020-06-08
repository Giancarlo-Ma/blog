---
title: leetcode-785
date: 2018-12-13 11:41:38
categories:
- leetcode
tags:
- graph
---

# IsBipartite

Given an undirected graph, return true if and only if it is bipartite.

Recall that a graph is bipartite if we can split it's set of nodes into two independent subsets A and B such that every edge in the graph has one node in A and another node in B.

The graph is given in the following form: graph[i] is a list of indexes j for which the edge between nodes i and j exists.  Each node is an integer between 0 and graph.length - 1.  There are no self edges or parallel edges: graph[i] does not contain i, and it doesn't contain any element twice.
<!-- more -->
Example 1:
```
Input: [[1,3], [0,2], [1,3], [0,2]]
Output: true
Explanation: 
The graph looks like this:
0----1
|    |
|    |
3----2
We can divide the vertices into two groups: {0, 2} and {1, 3}.
```
```
Example 2:
Input: [[1,2,3], [0,2], [0,1,3], [0,2]]
Output: false
Explanation: 
The graph looks like this:
0----1
| \  |
|  \ |
3----2
We cannot find a way to divide the set of nodes into two independent subsets.
``` 

Note:

- graph will have length in range [1, 100].
- graph[i] will contain integers in range [0, graph.length - 1].
- graph[i] will not contain i or duplicate values.
- The graph is undirected: if any element j is in graph[i], then i will be in graph[j].

## 题意分析

这个问题是图论里面比较经典的问题：二分图(Bipartite)的判定。可以采用染色方法来解，基本思想还是DFS，我们去从每一个节点出发，DFS整个图，维护mark和color两个状态，mark表明我们已经到访的结点，color表明两种颜色，二分图就是要所有相邻的结点的颜色不同，但凡找到一个与这个特性矛盾的结点，就不是二分图。

## 代码实现
```java
class Solution {

    public boolean isBipartite(int[][] graph) {
        boolean[] mark = new boolean[graph.length];
        // color的最后一个位置留作判定是否是二分图的判据
        boolean[] color = new boolean[graph.length + 1];

        // 可能是非连通图
        for(int i = 0; i < graph.length; i++) {
            if(!mark[i]) dfs(graph, mark, color, i);
            // 如果最后一个位置为true，说明是二分图直接返回false
            if(color[graph.length]) return false;
        }
        return true;
    }

    private void dfs(int[][] graph, boolean[] mark, boolean[] color, int i) {
        // 对当前访问的结点进行标记
        mark[i] = true;
        // 对相邻节点作dfs
        for(int adj : graph[i]) {
            // 没见过这个结点，将其颜色取反并dfs
            if(!mark[adj]) {
                color[adj] = !color[i];
                dfs(graph, mark, color, adj);
            }
            // 见过该结点并且颜色和当前结点相同就出现了矛盾的状况，不符合二分图特性
            else if(color[adj] == color[i]) {
                color[graph.length] = true; return;
            }
        }
    }
}
```
上面是自己参考Algorithm书做出的解法，有一个小问题就是算法找到矛盾条件后还要继续dfs，看了下discuss区，top vote的解法比较通用也很巧妙，都是利用双色的思想，但是将dfs设置一个返回值，找到矛盾条件后便不继续遍历，直接返回。运行效率会有提升，以下是代码：
```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] colors = new int[n];			
				
        for (int i = 0; i < n; i++) {             
            //This graph might be a disconnected graph. So check each unvisited node.
            if (colors[i] == 0 && !validColor(graph, colors, 1, i)) {
                return false;
            }
        }
        return true;
    }
    
    public boolean validColor(int[][] graph, int[] colors, int color, int node) {
        if (colors[node] != 0) {
            return colors[node] == color;
        }       
        colors[node] = color;       
        for (int next : graph[node]) {
            if (!validColor(graph, colors, -color, next)) {
                return false;
            }
        }
        return true;
    }
}
```
colors数组维护三种状态，0代表还没有访问到，1代表红色，-1代表蓝色。