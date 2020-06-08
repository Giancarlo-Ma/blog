---
title: leetcode-148
date: 2018-11-16 14:38:17
categories:
- leetcode
tags:
- sort
---

# Sort List

O(nlog n) time and O(1) space

## 分析题意
由于nlogn的时间复杂度，很自然想到归并排序。归并排序可以自顶向下进行归并，也可以自底向上归并。对于数组来说，自顶向下的归并排序时间复杂度nlogn，空间复杂度为n长度的辅助数组和logn深度的递归深度（n+logn），对于自底向上的归并排序，时间复杂度相同，空间复杂度去除了logn的递归深度栈。而对于链表的归并排序，时间复杂度相同，而由于链表不需要额外的辅助数组，所以自顶向下的空间复杂度为logn，自底向上的空间复杂度为O（1）。所以选择自底向上的归并排序。

## 代码实现
- 自顶向下的归并排序
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
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode slow = head;
        ListNode fast = head.next;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode mid = slow.next;
        slow.next = null;
        return merge(sortList(head), sortList(mid));
    }

    private ListNode merge(ListNode l1, ListNode l2) {
        ListNode dummyNode = new ListNode(0);
        ListNode tail = dummyNode;

        while (l1 != null && l2 != null) {
            if (l1.val > l2.val) {
                ListNode temp = l1;
                l1 = l2;
                l2 = temp;
            }
            tail.next = l1;
            l1 = l1.next;
            tail = tail.next;
        }
        tail.next = (l1 == null) ? l2 : l1;
        return dummyNode.next;
    }
}
```
- 自底向上的归并排序
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
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        int length = 0;
        for (ListNode l = head; l != null; l = l.next) length++;

        ListNode dummyNode = new ListNode(0);
        dummyNode.next = head;
        ListNode l;
        ListNode r;
        ListNode tail;
        for (int size = 1; size < length; size *= 2) {
            ListNode cur = dummyNode.next;
            tail = dummyNode;
            while (cur != null) {
                l = cur;
                r = split(l, size);
                cur = split(r, size);
                Pair p = merge(l, r);
                tail.next = p.first;
                tail = p.second;
            }
        }
        return dummyNode.next;
    }

    private ListNode split(ListNode head, int n) {
        while (--n > 0 && head != null) {
            head = head.next;
        }
        ListNode rest = (head != null) ? head.next : null;
        if (head != null) head.next = null;
        return rest;
    }

    private Pair merge(ListNode l1, ListNode l2) {
        ListNode dummyNode = new ListNode(0);
        ListNode tail = dummyNode;
        while (l1 != null && l2 != null) {
            if (l1.val > l2.val) {
                ListNode temp = l1;
                l1 = l2;
                l2 = temp;
            }
            tail.next = l1;
            l1 = l1.next;
            tail = tail.next;
        }
        tail.next = (l1 == null) ? l2 : l1;
        while (tail.next != null) tail = tail.next;
        return new Pair(dummyNode.next, tail);
    }

    private class Pair {
        ListNode first;
        ListNode second;
        Pair(ListNode f, ListNode s) {first = f; second = s;}
    }
}
```
