---
title: cs61b-lab02
date: 2018-09-04 20:05:10
categories:
- CS61B
tags:
- java
- data structure
---

# Introduction/Review of IntLists
As discussed in Monday’s lecture, an IntList is our CS61B implementation for a naked recursive linked list of integers. Each IntList has a first and rest variable. The first is the int element contained by the node, and the rest is the next chain in the list (another IntList!).

In the IntList directory for this lab, we’ve provided a much larger IntList.java than the one we created in class. It has five important new static methods, two of which you’ll fill in:
<!-- more -->
```java
    /**
     * Returns a list equal to L with all elements squared. Destructive.Iteratively
     */
    public static void dSquareList(IntList L) {
        while (L != null) {
            L.first = L.first * L.first;
            L = L.rest;
        }
    }
```
```java
    /**
     * Returns a list equal to L with all elements squared. Non-destructive.iterative
     */
    public static IntList squareListIterative(IntList L) {
        if (L == null) {
            return null;
        }
        IntList res = new IntList(L.first * L.first, null);
        IntList ptr = res;
        IntList B = L.rest;
        while (L != null) {
            ptr.rest = new IntList(L.first * L.first, null);
            B = B.rest;
            ptr = ptr.rest;
        }
        return res;
    }
```
```java
    /**
     * Returns a list equal to L with all elements squared. Non-destructive.recursive
     */
    public static IntList squareListRecursive(IntList L) {
        if (L == null) {
            return null;
        }
        return new IntList(L.first * L.first, squareListRecursive(L.rest));
    }   
```
```
dcatenate(IntList A, IntList B): returns a list consisting of all elements of A, followed by all elements of B. May modify A. To be completed by you.
```
```java
    /**
     * Returns a list consisting of the elements of A followed by the
     * *  elements of B.  May modify items of A. Don't use 'new'.
     */
    // Iterative concate A, B
    public static IntList dcatenate(IntList A, IntList B) {
        if (A == null) {
            return B;
        }
        IntList ptr = A;
        while (ptr.rest != null) {
            ptr = ptr.rest;
        }
        ptr.rest = B;
        return A;
    }
```
```java
// recursive destruct???
public static IntList dcatenate(IntList A, IntList B) {
    if (A == null) {
        return B;
    } else if (A.rest == null) {
        A.rest = B;
        return null;
    }
    dcatenate(A.rest, B);
    return A;
}
```
```
catenate(IntList A, IntList B): returns a list consisting of all elements of A, followed by all elements of B. May not modify A. To be completed by you.
```
```java
    /**
     * Returns a list consisting of the elements of A followed by the
     * * elements of B.  May NOT modify items of A.  Use 'new'.
     */
    // recursive non-destruct
    public static IntList catenate(IntList A, IntList B) {
        if(A == null) {
            return B;
        }
        return new IntList(A.first, catenate(A.rest, B));
    }
```
```java
// iterative non-destruct
public static IntList catenate(IntList A, IntList B) {
    if (A == null) {
        return B;
    }
    IntList ptr = A;
    IntList res = new IntList(ptr.first, null);
    IntList ptr1 = res;
    while (ptr.rest != null) {
        ptr1.rest = new IntList(ptr.rest.first, null);
        ptr = ptr.rest;
        ptr1 = ptr1.rest;
    }
    ptr1.rest = B;
    return res;
}
```
```java
public class Lists1Exercises {
    /** Returns an IntList identical to L, but with
      * each element incremented by x. L is not allowed
      * to change. */
    public static IntList incrList(IntList L, int x) {
        /* Your code here. */
        if (L == null) {
            return L;
        } else {
            return new IntList(L.first + x, incrList(L.rest, x));
        }
    }

    /** Returns an IntList identical to L, but with
      * each element incremented by x. Not allowed to use
      * the 'new' keyword. */
    public static IntList dincrList(IntList L, int x) {
        /* Your code here. */
        IntList B = L;
        while (B != null) {
            B.first += x;
            B = B.rest;
        }
        return L;
    }

    public static void main(String[] args) {
        IntList L = new IntList(5, null);
        L.rest = new IntList(7, null);
        L.rest.rest = new IntList(9, null);

        System.out.println(L.size());
        System.out.println(L.iterativeSize());

        // Test your answers by uncommenting. Or copy and paste the
        // code for incrList and dincrList into IntList.java and
        // run it in the visualizer.
        // System.out.println(L.get(1));
        // System.out.println(incrList(L, 3));
        // System.out.println(dincrList(L, 3));        
    }
}
```
