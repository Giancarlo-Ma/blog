---
title: quick-sort
date: 2018-12-01 15:28:29
categories:
- sorting algorithm
mathjax: true
---

## 基本算法
快排是一种基于分治的算法，将数组分成两部分，将两部分独立排序，当两个子数组都有序时，整个数组也就有序了。
```java
public class Quick {
    public static void sort(Comparable[] a) {
        StdRandom.shuffle(a); // 打乱数组
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        if(lo >= hi) return;
        int j = partition(a, lo, hi);
        sort(a, lo, j - 1);
        sort(a, j + 1, hi);
    }
}
```
<!-- more -->
实现算法的关键是partition方法，在这里，我们每次选用第一个元素作为partition元素，将他放在合适的位置，保证在他之前的元素都小于他，在他之后的元素都大于他。通过维护两个指针变量实现这个策略，代码如下。
```java
private static int partition(Comparable[] a, int lo, int hi) {
    int i = lo, j = hi + 1;
    Comparable v = a[lo];

    while(true) {
        while(less(a[++i], v)) if(i > hi) break;
        while(less(v, a[--j])) if(j < lo) break;
        if(i >= j) break;
        exch(a, i, j);
    }
    exch(a, lo, j);
    return j;
}
```

{% asset_img partition-trace.png %}

## 性能分析

快排之所以快，是因为他的内循环特别短小，只将有限数量的元素和固定的值相比。而归并排序或者希尔排序，还要将元素进行移动。快排还快在只需少量的比较，快排的最佳情况是每次都将partition置于中间位置，如此一来，时间复杂度分析就类似归并排序，有$C(N) = 2C(N/2) + N$的递推关系，可知时间复杂度为$NlgN$。

下面分析一下快速排序在平均情况下的时间复杂度，首先说明我们要证明的结论：快排在平均情况下需要$2NlnN$次比较（对$N$个不同键的元素排序）。$C(N)$表示排序$N$个不同的键所需要的比较次数。
$$C_N = N + 1 + (C_0 + C_1 + ... + C_(N - 2) + C_(N - 1)) / N + (C_(N - 1) + C_(N - 2) + ... + C_0) / N$$

$$NC_N=N(N+1)+2(C_0+C_1+...+C_(N-2)+C_(N-1))$$
两侧同时减去$N-1$相同的项。
$$NC_N-(N-1)C_(N-1)=2N+2C_(N-1)$$
同时除以$N(N-1)$
$$C_N/(N+1)=C_(N-1)/N+2(N+1)$$
$$C_N~2(N+1)(1/3+1/4+...+1/(N+1))$$
利用微积分知识可知，$C(N)~2NlnN$，也就是$1.39NlgN$，平均情况只比最好情况坏39%。

最坏情况下是数组已经有序，在这种情况下partition完全没有意义，每次partition得到的子数组之一总是为空。
$$N+(N-1)+(N-2)+...+2+1=(N+1)N/2$$

## 实践中改善

- 插入排序小型数组。
```java
private static void sort(Comparable[] a, int lo, int hi) {
    if(hi <= lo + CUTOFF - 1) {
        Insertion.sort(a, lo, hi);
        return;
    }
    int j = partition(a, lo, hi);
    sort(a, lo, j - 1);
    sort(a, j + 1, hi);
}
```
- 进行三取样切分，减少了比较次数，增长了交换次数，当partition元素在中间时，需要更多的交换次数。
```java
private static void sort(Comparable[] a, int lo, int hi) {
    if(hi <= lo) return;
    int m = medianOfThree(a, lo, lo + (hi - lo) / 2, hi);
    exch(a, lo, m);

    int j = partition(a, lo, hi);
    sort(a, lo, j - 1);
    sort(a, j + 1, hi);
}
```
- 熵最优排序
数组中可能包含大量重复元素，我们采用一种简单策略，将数组切分为小于partition元素的部分，等于partition元素的部分和大于partition元素的部分，dijkstra推广了这个策略，也就是著名的荷兰国旗问题。

Dijkstra的解法是维护三个指针，lt和gt以及i。维持如下的不变性：a[lo...lt-1]元素都小于v，a[gt+1..hi]元素都大于v,a[lt..i-1]都等于v，a[i...gt]元素还未确定。一开始令i和lo相等，有如下三种情况：
- a[i]小于v，交换a[i]和a[lt]，并且增加lt的值，增加i的值
- a[i]大于v，交换a[i]和a[gt]，并减小gt的值，
- a[i]等于v，增加i的值

最初这种策略并没有很流行，因为相对于并没有很多相等键的常规情况下，这种算法所需要的交换次数较多。但在上世纪90年代，bentley和Mcilroy解决了这个问题，并且观察到3-way partition对于重复键很多的序列排序在实际应用中要快于归并排序等其他排序算法，之后，bentley和Sedgewick证明了这一点。

```java
public class Quick3way {
    private static void sort(Comparable[] a, int lo, int hi) {
        if(hi <= lo) return;
        int lt = lo, gt = hi, i = lo + 1;
        Comparable v = a[lo];
        while(i <= gt) {
            if(less(a[i], v)) exch(a, i++, lt++);
            else if(less(v, a[i])) exch(a, i, gt--);
            else i++;
        }
        sort(a, lo, lt - 1);
        sort(a, gt + 1, hi);
    }
}
```

当数组中包含大量重复键时，考虑一种极端情况，数组所有的键值相同。那么在实现partition时，如果遇到相等元素不停下时，就会产生快排的最坏情况，此时，每次partition只去除数组中一个元素，算法的运行时间是平方级别。而三项切分的好处就是能将与partition元素相等的键值放到正确的位置，而在递归调用中不需要对这部分再进行排序，无疑大大缩短了有重复元素数组的排序时间。可以证明，在含有大量重复元素的数组中运用三向切分的快速排序的排序时间可以下降到线性级别。