---
title: cs61b-disc08
date: 2018-10-31 21:45:44
categories:
- CS61B
tags:
- java
- data structure
---

# Intuition
For the following recursive functions, give the worst case and best case running time in the appropriate O(·), Ω(·), or Θ(·) notation.
1.1 Give the running time in terms of N.
```java
public void andslam(int N) {
    if (N > 0) {
        for (int i = 0; i < N; i += 1) {
            System.out.println("datboi.jpg");
        }
        andslam(N / 2);
    }
}
```
<span style="color:red">The worst case and best case is all $\Theta(n)$</span>

1.2 Give the running time for `andwelcome(arr, 0, N)` where N is the length of the input array arr.
```java
public static void andwelcome(int[] arr, int low, int high) {
    System.out.print("[ ");
    for (int i = low; i < high; i += 1) {
        System.out.print("loyal ");
    }
    System.out.println("]");
    if (high - low > 0) {
        double coin = Math.random();
        if (coin > 0.5) {
            andwelcome(arr, low, low + (high - low) / 2);
        } else {
            andwelcome(arr, low, low + (high - low) / 2);
            andwelcome(arr, low + (high - low) / 2, high);
        }
    }
 }
```

1.3 Give the running time in terms of N.
```java
1 public int tothe(int N) {
2 if (N <= 1) {
3 return N;
4 }
5 return tothe(N - 1) + tothe(N - 1);
6 }
```
1.4 Give the running time in terms of N.
```java
1 public static void spacejam(int N) {
2 if (N <= 1) {
3 return;
4 }
5 for (int i = 0; i < N; i += 1) {
6 spacejam(N - 1);
7 }
8 }
```
