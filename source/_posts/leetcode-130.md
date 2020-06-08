---
title: leetcode-130
date: 2018-11-14 15:15:32
categories:
- leetcode
tags:
- Union-find
---

# Surrounded Regions

Given a 2D board containing 'X' and 'O' (the letter O), capture all regions surrounded by 'X'.

A region is captured by flipping all 'O's into 'X's in that surrounded region.

**Example:**
```
X X X X
X O O X
X X O X
X O X X
```
After running your function, the board should be:
```
X X X X
X X X X
X X X X
X O X X
```
**Explanation:**

Surrounded regions shouldn’t be on the border, which means that any 'O' on the border of the board are not flipped to 'X'. Any 'O' that is not on the border and it is not connected to an 'O' on the border will be flipped to 'X'. Two cells are connected if they are adjacent cells connected horizontally or vertically.

## 分析题意

这道题的tag是union_find，所以理所应当的想到用union_find的数据结构去理解本题。题意是对于任何在图中非外围的O以及内层与外层O连通的O，将O置换为X。扫描整个二维数组，将外层O连接到一个dummynode，然后对于内层的O，若其上下左右存在O，就连通到其相应的O。如此扫描过后，再遍历二维数组，检查那些不与dummynode连通的O，将其置换为X，问题得解。

## 代码实现
```java
class Solution {
    int rows, cols;
    public void solve(char[][] board) {
        if (board == null || board.length == 0) return;
        rows = board.length;
        cols = board[0].length;

        WeightedQuickUnionUF uf = new WeightedQuickUnionUF(rows * cols + 1);
        int dummyNode = rows * cols;
        // 扫描二维数组将外层O连接至dummynode，将内层O作相应连接处理
        for(int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (board[i][j] == 'O') {
                    if (i == 0 || i == rows - 1 || j == 0 || j == cols - 1) {
                        uf.union(node(i, j), dummyNode);
                    }
                    else {
                        if (i > 0 && board[i - 1][j] == 'O') uf.union(node(i - 1, j), node(i, j));
                        if (i < rows - 1 && board[i + 1][j] == 'O') uf.union(node(i + 1, j), node(i, j));
                        if (j > 0 && board[i][j - 1] == 'O') uf.union(node(i, j - 1), node(i, j));
                        if (j < cols - 1 && board[i][j + 1] == 'O') uf.union(node(i, j + 1), node(i ,j));
                    }
                }
            }
        }
        // 二次遍历该方阵，将未连接至dummynode的O做替换。
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (board[i][j] == 'O' && !uf.connected(node(i, j), dummyNode)) {
                    board[i][j] = 'X';
                }
            }
        }
    }

    private int node(int i, int j) {
        return i * cols + j;
    }

    // 带权重的快速联合数据结构
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
