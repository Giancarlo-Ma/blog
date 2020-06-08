---
title: cs61b-examprep03
date: 2018-09-04 20:05:10
categories:
- CS61B
tags:
- java
- data structure
---
# 1 Flatten
Write a method flatten that takes in a 2D array x and returns a 1D array that
contains all of the arrays in x concatenated together.
(Summer 2016 MT1)
```java
public static int[] flatten(int[][] x) {
    int totalLength = 0;

    for (int[] arr:x) {
        totalLength += arr.length;
    }

    int[] a = new int[totalLength];
    int aIndex = 0;

    for (int[] arr:x) {
        for(int i = 0; i < arr.length; i++) {
            a[aIndex] = arr[i];
            aIndex++;
        }
    }
     return a;
}
flatten({{1, 2, 3}, {}, {7, 8}}) // {1,2,3,7,8}
```
<!-- more -->
# 2 Skippify
Suppose we have the following IntList class, as defined in lecture and lab, with an
added skippify function.
Suppose that we define two IntLists as follows.

```java
IntList A = IntList.list(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
IntList B = IntList.list(9, 8, 7, 6, 5, 4, 3, 2, 1);
```
Fill in the method skippify such that the result of calling skippify on A and B
are as below:
- After calling A.skippify(), A: (1, 3, 6, 10)
- After calling B.skippify(), B: (9, 7, 4)
(Spring ’17, MT1)
```java
public class IntList {
public int first;
public IntList rest;

@Override
public boolean equals(Object o) { ... }
public static IntList list(int... args) { ... }

public void skippify() {
    IntList p = this;
    int n = 1;
    while (p != null) {

        IntList next = p.rest;

        for (int i = 0; i < n; i++) {
            next = next.rest;
            if (next == null) {
                break;
            }
        }
        p.rest = next;
        n++;
    }
}
```
```java
public class IntList {
    public int first;
    public IntList rest;

    @Override
    public boolean equals(Object o) { ... }
    public static IntList list(int... args) { ... }

    public void skippify() {
        IntList p = this;
        int n = 1;
        while (p != null) {
            IntList next = p.next;
            for (int i = 0; i < n; i++) {
                if (next == null) {
                    break;
                }
                next = next.rest;
            }
            p.rest = next;
            p = p.rest;
            n++;
        }
    }
}
```
# 3 Remove Duplicates
Fill in the blanks below to correctly implement removeDuplicates.
(Spring ’17, MT1)
```java
// My solution
public class IntList {
    public int first;
    public IntList rest;
    public IntList (int f, IntList r) {
        this.first = f;
        this.rest = r;
    }

     /**
    * Given a sorted linked list of items - remove duplicates.
    * For example given 1 -> 2 -> 2 -> 2 -> 3,
    * Mutate it to become 1 -> 2 -> 3 (destructively)
    */
    public static void removeDuplicates(IntList p) {
        if (p == null) {
            return;
        }

        IntList current = p.rest;

        IntList previous = p;

        while (current != null) {

            if (current.first == previous.first) {
                current = current.rest;
            } else {
                previous.rest = current;
                IntList next = current.rest;
                previous = current;
                current = next;
            }
        }
    }
}
```
```java
// PDF solution
public static void removeDuplicates(IntList p) {
    if (p == null) {
        return;
    }

    IntList current = p.rest;
    IntList previous = p;
    while (current != null) {
        if (current.first == previous.first) {
            previous.rest = current.rest;
    } else {
        previous = current;
    }
        current = current.rest;
    }
}
```
