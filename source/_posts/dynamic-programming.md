---
title: dynamic-programming
date: 2018-12-21 11:23:17
categories:
- algorithm
tags:
- dynamic programming
---

# 动态规划算法

动态规划算法是一种算法设计范式，将含有重复子问题的复杂问题，存储子问题的计算结果以避免重复计算。问题有如下两种特征可采用动态规划算法设计：
- 重叠的子问题
- 最优子结构

<!-- more -->

## 重叠子问题

像分治算法一样，动态规划算法也是组合子问题的解来解决一个大问题。动态规划主要用于子问题的解不断重复的需要的情况下，然后将子问题的解存到一个表里面，而不需要重复计算。如果一个问题不包含很多重复子问题，那么DP算法不太合适，如二分查找。但是递归的计算斐波那契数列就包含很多的重复子问题。如：
```java
public int fib(int n) {
    if(n <= 1) return n;
    else return fib(n - 1) + fib(n - 2);
}
```
执行fib5的递归树：
```
                          fib(5)
                     /             \
               fib(4)                fib(3)
             /      \                /     \
         fib(3)      fib(2)         fib(2)    fib(1)
        /     \        /    \       /    \
  fib(2)   fib(1)  fib(1) fib(0) fib(1) fib(0)
  /    \
fib(1) fib(0)
```

可以看到fib3被调用了两次，如果我们将fib3的结果存储起来，便不用重复计算了。有两种存储值的策略，以便这些值可被重复利用：
- Memoization（自顶向下）
- Tabulation（自底向上）

### Memoization

这种策略在递归实现的程序上做了一点小小的修改。只是在计算solution之前查找lookup table。我们初始化一个lookup表，并将其全部置为NIL，当我们需要某个问题的解时，首先查找lookup表，若不存在我们再计算，然后将相应的结果存储到lookup表中，如果存在，不再继续计算，直接返回即可。如：
```java
public class Fibonacci{
    private static int n;
    private static final int NIL = -1;
    
    private static int[] lookup;

    public Fibonacci(int N) {
        n = N; 
        for(int i = 0; i < n; i++)
            lookup[i] = NIL;
    } 

    public static int fib(int n) {
        if(lookup[n] == -1) {
            if(n <= 1) return n;
            else{
                lookup[n] = fib[n - 1] + fib[n - 2];
            }
        }
        return lookup[n];
    }
    public static void main(String[] args) {
        Fibonacci f = new Fibonacci(40);
        System.out.println(f.fib(40));
    }
}
```

### Tabulation

制表法采用自底向上的策略，首先计算fib(0),fib(1)，然后是fib(2),fib(3)等等。
```java
public class Fibonacci {
    public static int fib(n) {
        int[] f = new int[n + 1];
        for(int i = 2; i <= n; i++) {
            f[i] = f[i - 1] + f[i - 2];
        }
        return f[n];
    }

    public static void main(String[] args) {
        System.out.println(Fibonacci.fib(40));
    }
}
```

Tabulation和Memoization的区别在于memoization是按需计算，而tabulation是计算出表中每一个entry。

## 最优子结构

一个问题具有最优子结构特性，当其最优解可以由子问题的最优解获得。

举个例子来说，最短路径问题有如下最优子结构特性：假设x是u到v最短路径上的一个结点。那么u到v的最短路径必定是u到x的最短路径组合x到v的最短路径。最短路径算法如Floyd-warshall和Bellman-ford算法都是动态规划的体现。但是最长简单路径（无环）没有最优子结构特性。

## 例程

### 最长递增子序列

相关题目：leetcode300

### 最长公共子序列(LCS)

给定两个字符串序列，找到它们最长的公共子序列，序列可以不连续。

具有最优子结构特性和重叠子问题，采用DP思想。可以自顶向下进行递归也可以自底向上迭代构建。

#### 自顶向下

我们维护一个二维数组，索引是两个字符数组中对应的不同长度序列，比如：`String s1 = "GTAB"; String s2 = "GTAYB";`我们要解决的问题是找到`dp[4][5]`的值，为了求`dp[4][5]`，我们要将大问题分解为若干子问题。如果我们要处理的序列末尾字符是相同的，那么我们可以成功将问题规模减小，两个数组都可以减去最后一个共有元素，而如果我们要处理的序列末尾字符是不同的，依然可以将问题规模减小，我们所需要做的是，左边数组减1的规模和右边数组不变的情况下，以及左边数组规模不变右边数组规模减1这两种情况下的最大值。如果成功在某一步计算出结果，我们将结果存储到数组中，以实现记忆化。如代码所示

```java
public class LongestCommonSubsequence {
    public static int LengthOfLCS(char[] X, char[] Y, int m, int n, int[][] seen) {
        if(m == 0 || n == 0) return 0;
        if(seen[m][n] != 0) return seen[m][n];
        if(X[m - 1] == Y[n - 1])
            seen[m][n] = LengthOfLCS(X, Y, m - 1, n - 1, seen) + 1;
        else seen[m][n] = Math.max(LengthOfLCS(X, Y, m - 1, n, seen), LengthOfLCS(X, Y, m, n - 1, seen));
        return seen[m][n];
    }

    public static void main(String[] args) {
        String s1 = "GTAB";
        String s2 = "GTAYB";

        char[] X=s1.toCharArray();
        char[] Y=s2.toCharArray();
        int m = X.length;
        int n = Y.length;
        int[][] seen = new int[m + 1][n + 1];

        System.out.println("Length of LCS is" + " " +
                LengthOfLCS( X, Y, m, n, seen ) );
    }
}
```

#### 自底向上

```java
public static int lengthOfLCS(char[] X, char[] Y, int m, int n) {
    if(m == 0 || n == 0) return 0;
    int[][] dp = new int[m + 1][n + 1];
    for(int i = 0; i <= m; i++) {
        for(int j = 0; j <= n; j++) {
            if(i == 0 || j == 0) dp[i][j] = 0;
            else if(X[i - 1] == Y[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
            else dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
        }
    }
    return dp[m][n];
}
```

### Edit Distance

给定两个数组str1和str2，可以进行insert，remove和replace操作。找到将str1转换成str2的最少次数。所有操作代价相同。

#### 自顶向下

参数m和n分别代表当前处理的两个子串的长度，如果第一个字符串长度位0，只能进行n次insert操作；如果第二个字符串长度为0，只能进行m次remove操作。然后，如果我们已经计算过这个同等子串后，就简单返回。否则，有可能当前两个字符串末尾字符相同，这种情况下规模分别减1，因为当前位不消耗任何操作；还有可能当前两个字符串末尾字符不同，这个时候末尾字符一定要消耗一次操作，可以是insert，remove，replace三种操作中的一种，于是我们通过私有的min函数返回三个数值中最小的那个。

```java
public class EditDistance {
    public static int helper(String s1, String s2, int m, int n, int[][] seen) {
        if(m == 0) return n;
        if(n == 0) return m;
        if(seen[m][n] != 0) return seen[m][n];
        if(s1.charAt(m - 1) == s2.charAt(n - 1)) seen[m][n] = helper(s1, s2, m - 1, n - 1, seen);
        else seen[m][n] = 1 + min(helper(s1, s2, m, n - 1, seen), helper(s1, s2, m - 1, n, seen), helper(s1, s2, m - 1, n - 1, seen));
        return seen[m][n];
    }

    private static int min(int a, int b, int c) {
        if(b <= a && b <= c) return b;
        else if(a <= b && a <= c) return a;
        else return c;
    }

    public static int editDist(String s1, String s2, int m, int n) {
        int[][] seen = new int[m + 1][n + 1];
        return helper(s1, s2, m, n, seen);
    }

    public static void main(String[] arga) {
        String str1 = "sunday";
        String str2 = "saturday";

        System.out.println(editDist( str1 , str2 , str1.length(), str2.length()) );
    }
}
```

#### 自底向上

```java
public static int editDist(String s1, String s2, int m, int n) {
    int[][] dp = new int[m + 1][n + 1];
    for(int i = 0; i <= m; i++) {
        for(int j = 0; j <= n; j++) {
            if(i == 0) dp[i][j] = j;
            else if(j == 0) dp[i][j] = i;
            else if(s1.charAt(i - 1) == s2.charAt(j - 1)) dp[i][j] = dp[i - 1][j - 1];
            else dp[i][j] = min(dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1]) + 1;
        }
    }
    return dp[m][n];
}
```