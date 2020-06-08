---
title: leetcode-94
date: 2019-01-22 11:50:29
categories:
- leetcode
tags:
- tree
---

# Binary Tree Inorder Traversal

Given a binary tree, return the inorder traversal of its nodes' values.

Example:
```
Input: [1,null,2,3]
   1
    \
     2
    /
   3
```
Output: [1,3,2]
Follow up: Recursive solution is trivial, could you do it iteratively?

## 题意分析

实现中序遍历二叉树。最简单是递归版本，然后设计一个迭代版本。迭代版本中我们设置一个栈，用来存放我们看到过的结点，当到达树最左侧结点时，就将当前结点出栈，并检查最左侧结点是否存在右子树，如果有我们需要以同样的方法迭代右子树。


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
 * @return {number[]}
 */
var inorderTraversal = function(root) {
    var res = [];
    helper(root, res);
    return res;
};

function helper(root, res) {
    if(root !== null) {
        if(root.left !== null) {
            helper(root.left, res);
        }
        res.push(root.val);
        if(root.right !== null) {
            helper(root.right, res);
        }
    }
}
```
递推方程是T(n) = 2 * T(n / 2) + 1，时间复杂度为O(n)。空间复杂度最坏情况为O(n)，当树向一侧倾斜时出现。而平均情况下，空间复杂度为O(log(n))。

迭代版本
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
 * @return {number[]}
 */
var inorderTraversal = function(root) {
    var stack = [];
    var res = [];
    var curr = root;
    while(curr !== null || stack.length !== 0) {
        while(curr !== null) {
            stack.push(curr)
            curr = curr.left;
        }
        curr = stack.pop();
        res.push(curr.val);
        curr = curr.right;
    }
    return res;
};
```
因为要遍历到所有节点，时间复杂度为O(n)，额外用了一个栈，空间复杂度为O(n)。

第三种方案是利用线索二叉树。
- 用root初始化current
- while current不空：
    - 如果current没有左子树，push进current的值，然后current指向右子树
    - 否则有左子树，找到current左子树的最右侧节点，将current作为这个节点的右子树。

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
 * @return {number[]}
 */
var inorderTraversal = function(root) {
    var curr = root;
    var res = [];
    while(curr !== null) {
        if(curr.left === null) {
            res.push(curr.val);
            curr = curr.right;
        } else {
            var pre = curr.left;           
            while(pre.right !== null) pre = pre.right;
            var temp = curr;
            pre.right = curr;
            curr = temp.left;
            temp.left = null;
        }
    }
    return res;
};
```

时间复杂度和空间复杂度都为O(n)。

该方法的策略就是用中序遍历的次序重新构建一个线索树。