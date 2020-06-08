---
title: leetcode-147
date: 2018-11-16 13:38:53
categories:
- leetcode
tags:
- sort
---

# Insertion sort list

Sort a linked list using insertion sort.

Example 1:
```
Input: 4->2->1->3
Output: 1->2->3->4
```
Example 2:
```
Input: -1->5->3->4->0
Output: -1->0->3->4->5
```

## 分析题意
相比于原来数组的插入排序而言，该问题困难在对于链表的插入排序，需要利用多个指针保持对链表的访问。思路是：对于basic case，链表为null或只有单个元素，那么链表有序。否则，要先越过链表前部已有序的序列，然后遇到第一个逆序，那么需要利用多个指针将该逆序调整，小数插入到后面合适的位置。而这个合适的位置需要通过从dummyNode向前遍历完成，需要注意的就是前面和后面两个地方的指针修改，谨慎操作。

## 代码实现
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode dummyNode = new ListNode(0);
        dummyNode.next = head;

        while (head != null && head.next != null) {
            // 越过链表前部有序的部分
            if (head.val <= head.next.val) head = head.next;
            else {
                // 预备插入操作
                ListNode temp = head.next;
                head.next = temp.next;

                // 从dummyNode的下一个开始遍历链表找到第一个大于要插入节点的位置
                ListNode cur = dummyNode;
                while (cur.next.val <= temp.val) {
                    cur = cur.next;
                }
                temp.next = cur.next;
                cur.next = temp;
            }
        }
        return dummyNode.next;
    }    
}
```
