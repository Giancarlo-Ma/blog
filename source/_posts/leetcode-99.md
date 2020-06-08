---
title: leetcode-99
date: 2019-01-22 20:57:32
categories:
- leetcode
tags:
- tree
---

# Recover Binary Search Tree

Two elements of a binary search tree (BST) are swapped by mistake.

Recover the tree without changing its structure.

Example 1:
```
Input: [1,3,null,null,2]

   1
  /
 3
  \
   2

Output: [3,1,null,null,2]

   3
  /
 1
  \
   2
```
Example 2:
```
Input: [3,1,4,null,null,2]

  3
 / \
1   4
   /
  2

Output: [2,1,4,null,null,3]

  2
 / \
1   4
   /
  3
```
Follow up:

- A solution using O(n) space is pretty straight forward.
- Could you devise a constant space solution?

## 题意分析

给定一个二叉搜索树，其中两个节点的位置被错误放置。让我们找到这两个节点并将其交换使之成为一个合法的二叉搜索树。分析可知，二叉搜索树的中序遍历应该是递增序的，我们将给定输入的树进行中序遍历，有两种可能存在的情况：其一是两个错误的元素紧邻放置，那么中序遍历的结果中只有这两个错误的邻居元素是逆序的；其二是两个错误的元素不相邻，那么中序遍历结果中会存在两个逆序。如`3 25 7 8 10 15 20 5`，其中的25和5，这两个需要做交换使之成为`3 5 7 8 10 15 20 25`。其中25和5是应当做交换的非紧邻元素，那么我们可以发现25大于7，20大于5这两对逆序，并取第一个逆序的第一个元素和第二个逆序的第二个元素做交换。而在`3 5 8 7 10 15 20 25`这一个紧邻元素需要做交换的例子中，我们并不存在第二个逆序对，所以只需要找到唯一的逆序对，将其做交换即可。

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
 * @return {void} Do not return anything, modify root in-place instead.
 */
// first指向第一个逆序对的第一个元素，middle指向第一个逆序对的第二个元素，last指向第二个逆序对的第二个元素
var first, middle, last, prev;
var recoverTree = function(root) {
    first = middle = last = prev = null;
    correct(root);
    
    // last存在代表有两个逆序对，这时候middle也是存在的，需要严格按照这个顺序
    if(first != null && last != null) {
        let temp = first.val;
        first.val = last.val;
        last.val = temp;
    } else if(first != null && middle != null) {
        let temp = first.val;
        first.val = middle.val;
        middle.val = temp;
    }
};

function correct(root) {
    if(root != null) {
        correct(root.left);
        
        if(prev != null && prev.val > root.val) {
            if(first == null) {
                first = prev;
                middle = root;
            } else {
                last = root;
            }
        }
        prev = root;
        correct(root.right);
    }
}
```