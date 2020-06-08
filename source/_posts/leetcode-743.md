---
title: leetcode-743
date: 2018-12-12 14:37:08
categories:
- leetcode
tags:
- graph
---

# Network Delay Time

There are N network nodes, labelled 1 to N.

Given times, a list of travel times as directed edges `times[i] = (u, v, w)`, where `u` is the source node, `v` is the target node, and `w` is the time it takes for a signal to travel from source to target.

Now, we send a signal from a certain node `K`. How long will it take for all nodes to receive the signal? If it is impossible, return -1.
<!-- more -->
Note:
`N` will be in the range [1, 100].
`K` will be in the range [1, N].
The length of times will be in the range [1, 6000].
All edges times`[i] = (u, v, w)` will have `1 <= u`, `v <= N` and `1 <= w <= 100`.

## 题意分析

一点题外话：做到这个题的时候刚好做了一个关于网络层DV算法的项目，也比较凑巧的是，这个题和DV异曲同工的利用了Bellman-Ford方程。下面来分析以下题意，给定一个图，以及图中节点的数量，还有我们关注的K结点。我们要做的是找到这个图中距离K最远的距离，但这个距离还必须是最短距离，我称之为在最短中找最长问题。回忆一下Bellfor-Ford方程，说的是对于某一个结点来说，若关注结点K到该结点的距离已知，并且结点中存在一条边，使得我们可以经由这条边去结点K，如果当前距离大于经由这条边去结点K，那么我们更新该结点到结点K的距离。经过不断迭代，我们终于可以找到结点中所有点（如果是连通图）到结点K的最短距离。然后在所有最短距离中找到最长的距离，即为问题的解，如果图不连通，那么返回-1。外循环为V，内循环为E，时间复杂度为O（VE），V小于100，可以近似为O（E）。
## 代码实现
```java
class Solution {
    public int networkDelayTime(int[][] times, int N, int K) {
        if(times == null || times.length == 0)
            return -1;
        // 初始化一个dist数组表示所有结点到K结点的当前已知的最短距离
        int[] dist = new int[N+1];
        // 初始状态下，除了K到自己为0，其余设置为MAX_VALUE
        for(int i = 1; i <= N; i++)
            dist[i] = Integer.MAX_VALUE;
        dist[K] = 0;
        // 不断迭代直至找到所有最短距离
        for(int i = 0; i < N; i++) {
            for(int[] items : times) {
                int u = items[0];
                int v = items[1];
                int w = items[2];
                
                if(dist[u] != Integer.MAX_VALUE && dist[v] > dist[u] + w)
                    dist[v] = dist[u] + w;
            }
        }
        // 遍历所有item，找到最大的距离，如果有一个不可达结点（图不连通），则返回-1
        int max = 0;
        for(int i = 1; i <= N; i++)
            max = Math.max(max, dist[i]);
        if(max == Integer.MAX_VALUE) return -1;
        return max;
    }
}
```