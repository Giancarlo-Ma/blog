---
title: cs61b-disc07
date: 2018-10-30 21:02:43
categories:
- CS61B
tags:
- java
- data structure
mathjax: true
---
# Asymptotic Notation

1.1 Order the following big-O runtimes from smallest to largest

$O(log n), O(1), O(n^n), O(n^3), O(nlogn), O(n), O(n!), O(2^n), O(n^2logn)$

$O(1) < O(logn) < <O(n)<O(nlogn) <O(n^2logn)<O(n^3)<O(2^n)<O(n!)<O(n^n)$

1.2 Are the statements in the right column true or false? If false, correct the asymptotic notation (Ω(·), Θ(·), O(·)). Be sure to give the tightest bound. Ω(·) is the opposite of O(·), i.e. f(n) ∈ Ω(g(n)) ⇐⇒ g(n) ∈ O(f(n)).

{% asset_img analysis.png %}

<!-- more -->
# Analyzing Runtime
2.1 Give the worst case and best case runtime in terms of M and N.Assume ping is in Θ(1) and returns an int.
```java
int j = 0;
for (int i = N; i > 0; i--) {
    for (; j <= M; j++) {
        if (ping(i, j) > 64) {
            break;
        }
    }
}
```
<span style="color:red">Worst case is Θ(M+N), best case is Θ(N). Pay attention to that j is outside for loop</span>

2.2 Give the worst case and best case runtime where `N = array.length`. Assume `mrpoolsort(array)` is in Θ(N log N).
```java
public static boolean mystery(int[] array) {
    array = mrpoolsort(array);
    int N = array.length;
    for (int i = 0; i < N; i += 1) {
        boolean x = false;
        for (int j = 0; j < N; j += 1) {
            if (i != j && array[i] == array[j])
            x = true;
        }
        if (!x) {
            return false;
        }
    }
    return true;
}
```
<span style="color:red">Worst case: $\Theta(n^2)$, best case: $\Theta(nlogn)$, don't forget the nlogn sorting at the beginning.</span>
Achilles Added Additional Amazing Asymptotic And Algorithmic Analysis Achievements
(a) What is mystery() doing?
<span style="color:red">`mystery` returns true if every int has a duplicate in the array, (ex. {1,2,1,2}). `mystery` returns false if any unique int in the array</span>
(b) Using an ADT, describe how to implement `mystery()` with a better runtime. Then, if we make the assumption an int can appear in the array at most twice, develop a solution using only constant memory

<span style="color:red">A Θ(N) algorithm is to use a map and do key = element and value = number
of appearances, then make sure all values are > 1. Uses O(N) memory however. Can do constant space by sorting then going through, but sorting is generally in O(n log n) time.</span>

2.3 Give the worst case and best case running time in Θ(·) notation in terms of M and
N. Assume that comeOn() is in Θ(1) and returns a boolean.
```java
for (int i = 0; i < N; i += 1) {
    for (int j = 1; j <= M; ) {
        if (comeOn()) {
            j += 1;
        } else {
            j *= 2;     
        }
    }
}
```
<span style="color:red">The worst case is $\Theta(N \cdot M)$, the best case is $\Theta(N\cdot logM)$</span>

# Have You Ever Went Fast?
3.1 Given an int `x` and a sorted array `A` of `N` distinct integers, design an algorithm to
find if there exists indices i and j such that A[i] + A[j] == x.
Let’s start with the naive solution.
```java
public static boolean findSum(int[] A, int x) {
    for (int i = 0; i < A.length; i++){
        for (int j = 0; j < A.length; j++) {
            if (A[i] + A[j] == x) {
                return true;
            }
        }
    }
    return false;
}
```
(a) How can we improve this solution? Hint: Does order matter here?
```java
public static boolean findSum(int[] A, int x) {
    int small = 0;
    int big = A.length - 1;
    while (small <= big) {
        sum = small + big;
        if (sum == x) {
            return true;
        } else if (sum < x) {
            small++;
        } else {
            big--;
        }
    }
    return false;
}
```
(b) What is the runtime of both the original and improved algorithm?
<span style="color:red">The worst case of original is $\Theta(n^2)$, the best case is $\Theta(1)$. The worst case of improved algorithm is $\Theta(n)$, the best case is $\Theta(1)$</span>

# CTCI Extra
4.1 **Union** Write the code that returns an array that is the union between two given arrays. The union of two arrays is a list that includes everything that is in both arrays, with no duplicates. Assume the given arrays do not contain duplicates. For example, the union of {1, 2, 3, 4} and {3, 4, 5, 6} is {1, 2, 3, 4, 5, 6}.
**Hint: The method should run in O(M + N) time where M and N is the size of
each array.**
```java
public static int[] union(int[] A, int[] B) {
    HashSet<Integer> set = new HashSet<>();
    for (int num : A) {
        set.add(num);
    }
    for (int num : B) {
        set.add(num);
    }

    int[] unionArr = new int[set.size()];
    int index = 0;
    for (int num : set) {
        unionArr[index] = num;
        index++;
    }
    return unionArr;
}
```
4.2 **Intersect** Now do the same as above, but find the intersection between both arrays. The intersection of two arrays is the list of all elements that are in both arrays. Again assume that neither array has duplicates. For example, the intersection of {1, 2, 3, 4} and {3, 4, 5, 6} is {3, 4}.
Hint: Think about using ADTs other than arrays to make the code more efficient.
```java
public static int[] intersection(int[] A, int[] B) {
    HashSet<Integer> setOfA = new HashSet<>();
    HashSet<Integer> resSet = new HashSet<>();

    for (int num : A) {
        setOfA.add(num);
    }
    for (int num : B) {
        if setOfA.contains(num) {
            resSet.add(num);
        }
    }
    int[] intersectionArray = new int[resSet.size()];
    int index = 0;
    for (int num : resSet) {
        intersectionArray[index] = num;
        index++;
    }
    return intersectionArray;
}
```
