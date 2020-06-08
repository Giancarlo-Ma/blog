---
title: leetcode-924
date: 2018-12-19 12:50:13
categories:
- leetcode
tags:
- graph
- union-find
---

# Minimize Malware Spread

In a network of nodes, each node i is directly connected to another node j if and only if graph[i][j] = 1.

Some nodes initial are initially infected by malware.  Whenever two nodes are directly connected and at least one of those two nodes is infected by malware, both nodes will be infected by malware.  This spread of malware will continue until no more nodes can be infected in this manner.

Suppose `M(initial)` is the final number of nodes infected with malware in the entire network, after the spread of malware stops.

We will remove one node from the initial list.  Return the node that if removed, would minimize M(initial).  If multiple nodes could be removed to minimize M(initial), return such a node with the smallest index.

Note that if a node was removed from the initial list of infected nodes, it may still be infected later as a result of the malware spread.

<!-- more -->

Example 1:
```
Input: graph = [[1,1,0],[1,1,0],[0,0,1]], initial = [0,1]
Output: 0
```
Example 2:
```
Input: graph = [[1,0,0],[0,1,0],[0,0,1]], initial = [0,2]
Output: 0
```
Example 3:
```
Input: graph = [[1,1,1],[1,1,1],[1,1,1]], initial = [1,2]
Output: 1
```

Note:

- 1 < graph.length = graph[0].length <= 300
- 0 <= graph[i][j] == graph[j][i] <= 1
- graph[i][i] = 1
- 1 <= initial.length < graph.length
- 0 <= initial[i] < graph.length

## 题意分析

给定一个graph数组，表示网络中的连接情况。此外有一个initial数组，表示初始的感染节点，每一个和结点相邻的结点随着时间的推移都会被感染，最终达到稳定状态时的感染结点记为M（Initial），让我们修复一个结点，修复结点过后使得M（Initial）达到最小，找到这个被修复的节点。参考了discussion，发现了一个很好的解释，[这是链接](https://buptwc.com/2018/10/15/Leetcode-924-Minimize-Malware-Spread/)
最后我采用的和链接中的少许不同，但是思路是一样的，就是找到连通子图中只有一个malware的并且size是最大的。如果找不到，就返回initial里面最小的。

## 代码实现

```java
class Solution {
    public int minMalwareSpread(int[][] graph, int[] initial) {
        int N = graph.length;
        UF uf = new UF(N);
        Map<Integer, Integer> map = new HashMap<>();
        Arrays.sort(initial);
        int res = initial[0];

        for(int i = 0; i < N; i++) {
            for(int j = 0; j < N; j++) {
                if(graph[i][j] == 1) {
                    uf.union(i, j);
                }
            }
        }
        
        for(int i : initial) {
            if(!map.containsKey(uf.find(i))) map.put(uf.find(i), 1);
            else map.put(uf.find(i), map.get(uf.find(i))+1);
        }

        int size = 0;

        for(int i : initial) {
            int root = uf.find(i);
            
            if(map.get(root) == 1 && uf.size(root) > size) {
                res = i;
                size = uf.size(root);
            }
        }
        return res;
    }


    private class UF {
        private int[] parents;
        private int[] size;
        private int count;

        public UF(int N){
            parents = new int[N];
            size = new int[N];
            for(int i = 0; i < N; i++) {
                parents[i] = i;
                size[i] = 1;
            }
            count = N;
        }

        public int size(int i) {return size[i];}

        public int count() {return count;}

        public int find(int p) {
            while(p != parents[p])
                p = parents[p];
            return p;
        }


        public void union(int p, int q) {
            int pid = find(p);
            int qid = find(q);
            if(pid == qid) return;
            if(size[pid] < size[qid]) {
                parents[pid] = qid;
                size[qid] += size[pid];
            } else {
                parents[qid] = pid;
                size[pid] += size[qid];
            }
            count--;
        }
     }
}
```
