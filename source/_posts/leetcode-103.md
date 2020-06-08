---
title: leetcode-103
date: 2019-01-22 22:32:36
categories:
- leetcode
tags:
- tree
---

# Binary Tree Zigzag Level Order Traversal

Given a binary tree, return the zigzag level order traversal of its nodes' values. (ie, from left to right, then right to left for the next level and alternate between).

For example:
Given binary tree `[3,9,20,null,null,15,7]`,
```
    3
   / \
  9  20
    /  \
   15   7
```
return its zigzag level order traversal as:
```
[
  [3],
  [20,9],
  [15,7]
]
```

## 题意分析

以螺旋形层序遍历我们的二叉树。和普通的层序遍历相比，螺旋形只是在原来的基础上增加一个ltr变量，而在我们每次遍历完一层之后，只需要将这个变量取反即可。

## 代码实现
```javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
var zigzagLevelOrder = function(root) {
    if(root == null) return [];
    let res = [];
    let ltr = true;
    let levelResult = [];
    for(let i = 1; i <= height(root); i++) {
        traverseGivenLevel(root, i, ltr, levelResult);
        res.push(levelResult);
        ltr = !ltr;
        levelResult = [];
    }
    return res;
};


// 参数：给定树根，层，以及遍历方向
// 返回一个遍历后该层的数组
function traverseGivenLevel(root, i, ltr, levelResult) {
    if(root == null) return;
    if(i == 1) {
        levelResult.push(root.val);
    } else if(i > 1) {
        if(ltr) {
            traverseGivenLevel(root.left, i - 1, ltr, levelResult);
            traverseGivenLevel(root.right, i - 1, ltr, levelResult);
        } else {
            traverseGivenLevel(root.right, i - 1, ltr, levelResult);
            traverseGivenLevel(root.left, i - 1, ltr, levelResult);
        }
    }
}

function height(root) {
    if(root == null) return 0;
    let lheight = height(root.left);
    let rheight = height(root.right);
    return lheight > rheight ? lheight + 1 : rheight + 1;
}
```
参考https://www.geeksforgeeks.org/level-order-traversal-in-spiral-form/