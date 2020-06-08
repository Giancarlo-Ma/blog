---
title: cs61b-disc01&disc02
date: 2018-09-01 20:05:10
categories:
- CS61B
tags:
- java
- data structure
---

# Q1
Implement fib which takes in an integer n and returns the nth Fibonacci number.
The Fibonacci sequence is 0,1,1,2,3,5,8,13,21,....
```java
public static int fib(int n) {
    if (n <= 1) {
        return n;
    } else {
        return fib(n - 1) + fib(n - 2);
    }
}
```
<!-- more -->
Extra: Implement fib in 5 lines or fewer. Your answer must be efficient.
```java
public static int fib2(int n, int k, int f0, int f1) {
    if (n == k) {
        return f0;
    } else {
        return fib2(n, k + 1, f1, f0 + f1);
    }
}
```
# Q2
Implement square and squareMutative which are static methods that both take in
an IntList L and return an IntList with its integer values all squared. square does
this non-mutatively with recursion by creating new IntLists while squareMutative
uses a recursive approach to change the instance variables of the input IntList L.
```java
// recursive square non-mutatively
public static IntList square(IntList L) {
    if (L == null) {
        return L;
    } else {
        return new IntList(L.first * L.first, square(L.rest));
    }
}
```
```java
// iteration square non-mutatively
public static IntList square(IntList L) {
    if (L == null) {
        return L;
    } else {
        IntList B = L.rest;
        IntList LSquare = new IntList(L.first * L.first, null);
        IntList C = LSquare;
        while (B != null) {
            C.rest = new IntList(B.first * B.first, null);
            B = B.rest;
            C = C.rest;
        }
        return LSquare;
    }
}
```
```java
// recursive square mutatively
public static IntList squareMutative(IntList L) {
    if (L == null) {
        return L;
    } else {
        L.first = L.first * L.first;
        squareMutative(L.rest);
    }
    return L;
}
```
```java
// iteration square mutatively
public static IntList squareMutative2(IntList L) {
    IntList B = L;
    while (B != null) {
        B.first *= B.first;
        B = B.rest;
    }
    return L;
}
```
