---
title: cs61b-disc03
date: 2018-09-02 20:05:10
categories:
- CS61B
tags:
- java
- data structure
---

# Q1 More Practice with Linked Lists
```java
public class SLList {
    private class IntNode {
        public int item;
        public IntNode next;
        public IntNode(int item, IntNode next) {
            this.item = item;
            this.next = next;
        }
    }

    private IntNode first;

    public void addFirst(int x) {
        first = new IntNode(x, first);
    }
}
```
1.1 Implement SLList.insert which takes in an integer x and inserts it at the given
position. If the position is after the end of the list, insert the new node at the end.
For example, if the SLList is 5 → 6 → 2, insert(10, 1) results in 5 → 10 → 6 → 2.
<!-- more -->
```java
public void insert(int item, int position) {
    if (first == null || position == 0) {
        addFirst(item);
        return;
    }
    IntNode p = first;
    int i = 1;
    while (i < position && p.next != null) {
        p = p.next;
        i++;
    }
    p.next = new IntNode(item, p.next);
}
```
1.2 Add another method to the SLList class that reverses the elements. Do this using
the existing IntNodes (you should not use new).
```java
// Iteratively reverse, using prev, curr and next pointer
public void reverse() {
    IntNode prevNode = null;
    IntNode currNode = first;
    IntNode nextNode = null;
    while (currNode != null) {
        nextNode = currNode.next;
        currNode.next = prevNode;
        prevNode = currNode;
        currNode = nextNode;
    }
    first = prevNode;
}
```
```java
// recursively reverse sllist, first traverse the list then go back while setting the direction reversed.
    public void reverse() {
        first = reverseRecursiveHelper(first);
    }

    public IntNode reverseRecursiveHelper(IntNode front) {
        if (front == null || front.next == null) {
            return front;
        }
        IntNode reverse = reverseRecursiveHelper(front.next);
        front.next.next = front;
        front.next = null;
        return reverse;
    }
```
# Arrays
2.1 Consider a method that inserts item into array arr at the given position. The
method should return the resulting array. For example, if x = [5, 9, 14, 15],
item = 6, and position = 2, then the method should return [5, 9, 6, 14, 15].
If position is past the end of the array, insert item at the end of the array.
Is it possible to write a version of this method that returns void and changes arr
in place (i.e., destructively)?
Extra: Write the described method:
```java
public static int[] insert(int[] arr, int item, int position) {
    int[] res = new int[arr.length + 1];
    position = Math.min(arr.length, position);
    for (int i = 0; i < position; i++) {
        res[i] = arr[i];
    }
    res[position] = item;
    for (int i = position, i < arr.length; i++) {
        res[i + 1] = arr[i];
    }
    return res;
}
```
Consider a method that destructively reverses the items in arr. For example calling
reverse on an array [1, 2, 3] should change the array to be [3, 2, 1].
What is the fewest number of iteration steps you need? What is the fewest number
of additional variables you need?
Extra: Write the method:
```java
public static void reverse(int[] arr) {
    for (int i = 0; i < arr.length / 2; i++) {
        int temp = arr[i];
        arr[i] = arr[arr.length - 1 - i];
        arr[arr.length - 1 - i] = temp;
    }
}
```
Extra: Write a non-destructive method replicate(int[] arr) that replaces the
number at index i with arr[i] copies of itself. For example, replicate([3, 2,
1]) would return [3, 3, 3, 2, 2, 1].
```java
public static int[] replicate(int[] arr) {
    int total = 0;
    for (int item : arr) {
        total += item;
    }
    int i = 0;
    int[] res = new int[total];
    for (int item : arr) {
        for (int counter = 0; counter < item; counter++) {
            res[i] = item;
            i++;
        }
    }
    return res;
}
```
