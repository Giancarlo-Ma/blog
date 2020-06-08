---
title: leetcode-928
date: 2018-12-21 10:56:51
categories:
- leetcode
tags:
- graph
---

# Minimize Malware Spread II

(This problem is the same as Minimize Malware Spread, with the differences bolded.)

In a network of nodes, each node i is directly connected to another node j if and only if graph[i][j] = 1.

<!-- more -->

Some nodes initial are initially infected by malware.  Whenever two nodes are directly connected and at least one of those two nodes is infected by malware, both nodes will be infected by malware.  This spread of malware will continue until no more nodes can be infected in this manner.

Suppose M(initial) is the final number of nodes infected with malware in the entire network, after the spread of malware stops.

We will remove one node from the initial list, completely removing it and any connections from this node to any other node.  Return the node that if removed, would minimize M(initial).  If multiple nodes could be removed to minimize M(initial), return such a node with the smallest index.

 

Example 1:
```
Input: graph = [[1,1,0],[1,1,0],[0,0,1]], initial = [0,1]
Output: 0
```
Example 2:
```
Input: graph = [[1,1,0],[1,1,1],[0,1,1]], initial = [0,1]
Output: 1
```
Example 3:
```
Input: graph = [[1,1,0,0],[1,1,1,0],[0,1,1,1],[0,0,1,1]], initial = [0,1]
Output: 1
``` 

Note:

- 1 < graph.length = graph[0].length <= 300
- 0 <= graph[i][j] == graph[j][i] <= 1
- graph[i][i] = 1
- 1 <= initial.length < graph.length
- 0 <= initial[i] < graph.length

## 题意分析

这道题类似leetcode924，不同的地方在于924是修复某个初始感染节点，而本题是直接移除节点以及相邻的链路。这道题的关键在于我们要找到那些只能被某一个感染节点感染的非感染节点。因为一旦断开链路，保证再也不能被感染的节点是唯一能够被remove节点感染的节点，而其它节点均可能被另外的感染节点所感染。而924的关键是它是修复节点，修复节点不是移除节点，涉及到的是连通子图内的问题，如果连通子图内有两个感染节点，无论移除哪一个都没法使得其他节点不被感染，所以问题演变成寻找只有一个感染节点的最大连通子图。

## 代码实现
```java
class Solution {
    public int minMalwareSpread(int[][] graph, int[] initial) {
        // construct infectedBy which represents every node in graph except initial node infected by which initial node
        int N = graph.length;
        ArrayList<Integer>[] infectedBy = new ArrayList[N];
        for(int i = 0; i < N; i++)
            infectedBy[i] = new ArrayList<>();
        // mark initial node which is 0, normal node is 1
        int[] clean = new int[N];
        Arrays.fill(clean, 1);
        for(int i : initial)
            clean[i] = 0;
        for(int i : initial) {
            Set<Integer> seen = new HashSet<>();
            dfs(graph, clean, seen, i);
            for(int v : seen)
                infectedBy[v].add(i);
        }

        // find size of infectedBy item is 1, which is contributes to the initial node
        int[] contributions = new int[N];
        for(int i = 0; i < infectedBy.length; i++) {
            if(infectedBy[i].size() == 1) {
                contributions[infectedBy[i].get(0)]++;  
            }
        }

        // if find, return initial node which has most contributions, else return the node has smallest index in initial
        Arrays.sort(initial);
        int res = initial[0], max = 0;
        for(int i = 0; i < contributions.length; i++) {
            if(contributions[i] > max){
                res = i;
                max = contributions[i];
            }
        }
        return res;
    }

    private void dfs(int[][] graph, int[] clean, Set<Integer> seen, int v) {
        for(int j = 0; j < graph[v].length; j++) {
            if(graph[v][j] == 1 && clean[j] == 1 && !seen.contains(j)) {
                seen.add(j);
                dfs(graph, clean, seen, j);
            }
        }
    }
}
```