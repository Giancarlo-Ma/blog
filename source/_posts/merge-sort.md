---
title: merge-sort
date: 2018-12-01 10:51:23
categories:
- sorting algorithm
mathjax: true

---

## 归并操作

归并排序算法的核心是归并操作，给定两个有序的数组，将其归并为一个更大的有序的数组。以下是归并操作的java实现。

```java
public static void merge(Comparable[] a, int lo, int mid, int hi) {
    // 辅助数组
    Comparable[] aux = new Comparable[a.length];
    // 用两个指针标识位置
    int i = lo, j = mid + 1;
    // 将数组内容复制到aux数组
    for(int k = lo; k <= hi; k++)
        aux[k] = a[k];
    for(int k = lo; k <= hi; k++) {
        // 左半边遍历完成
        if(i > mid) a[k] = aux[j++];
        // 右半边遍历完成
        else if(j > hi) a[k] = aux[i++];
        // 右半边元素小于左半边，放置小的至原数组
        else if(aux[i] > aux[j]) a[k] = aux[j++];
        else a[k] = aux[i++];
    }
}
```
<!-- more -->

## 自顶向下归并

我们利用算法设计中的分治思想去设计归并排序，拿到一个无序数组，可以这样思考，将其左半边排序，右半边排序，然后将左右两侧归并，就可以实现整个数组的排序。这样将大的数组分为若干小数组，将其分别排序归并，最后形成有序的大数组，就可以实现我们的目的，这种通过递归实现的排序方法叫做自顶向下的归并排序。
```java
public class Merge {
    private static Comparable[] aux;
    public static void sort(Comparable[] a) {
        aux = new Comparable[a.length];
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        if(lo >= hi) return;
        int mid = lo + (hi - lo) / 2;
        sort(a, lo, mid);
        sort(a, mid + 1, hi);
        merge(a, lo, mid, hi);
    }
}
```
{% asset_img merge-sort-call-trace.png %}
sort()方法的作用就是安排merge方法调用的正确顺序。我们通过分析算法的比较次数来分析这个算法的复杂度，对于下面的树来说，我们有n层，自顶向下的第k层有2^k个子数组，每个数组长度为$2^(n-k)$，归并需要2$^(n-k)$次比较。因此每层的比较次数为$2^n$，并且这里我们有n层（$n=logn$)，所以为$NlogN$。

{% asset_img merge-sort-subtree.png %}

归并排序改进
- 对小规模子数组使用插入排序，
```java
private static void sort(Comparable[] a, int lo, int hi) {
    if(hi <= lo + CUTOFF - 1) {
        Insertion.sort(a, lo, hi);
        return;
    }
    int mid = lo + (hi - lo) / 2;
    sort(a, lo, mid);
    sort(a, mid + 1, hi);
    merge(a, lo, mid, hi);
}
```
- 测试数组是否已经有序：如果数组已经有序，也就是a[mid] < a[mid + 1]，就跳过merge方法。
```java
private static void sort(Comparable[] a, int lo, int hi) {
    if(hi <= lo) return;
    int mid = lo + (hi - lo) / 2;
    sort(a, lo, mid);
    sort(a, mid + 1, hi);
    if(less(a[mid], a[mid + 1])) return;
    merge(a, lo, mid, hi);
}
```
- 交换aux和a数组：节省时间但不节省空间
```java
private static void merge(Comparable[] a, Comparable[] aux, int lo, int mid, int h) {
    int i = lo, j = mid + 1;
    for(int k = lo; k <= hi; k++) {
        if(i > mid) aux[k] = a[j++];
        else if(j > hi) aux[k] = a[i++];
        else if(a[i] < a[j]) aux[k] = a[i++];
        else aux[k] = a[j++];
    }
}

private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi) {
    if(lo >= hi) return;
    int mid = lo + (hi - lo) / 2;
    sort(aux, a, lo, mid);
    sort(aux, a, mid + 1, hi);
    merge(a, aux, lo, mid, hi);
}
```

## 自底向上归并排序

递归实现归并排序是分治算法的典型体现，我们将大问题分解为小问题，再逐个击破。另一种实现归并排序的思路是，我们通过$log N$轮的归并，设定一个sz值，这个sz值不断翻倍，先将小的sz为1的子数组归并为规模为2的有序数组，再将sz为2的有序数组归并，为sz为4的归并做准备，以此类推，我们将这种方法叫做自底向上的归并排序。需要注意的是，最后一个参与归并的子数组可能与前面参与归并的数组大小不一致，但我们的merge方法也能很好的处理这种情况。
{% asset_img bottom-up-merge.png %}
```java
public class MergeBU {
    private Comparable[] aux;
    public static void sort(Comparable[] a) {
        int N = a.length;
        aux = new Comprable[N];
        for(int sz = 1; sz < N; sz += sz)
            for(int lo = 0; lo < N - sz; lo += lo)
                merge(a, lo, lo + sz - 1, Math.min(lo + sz + sz - 1, N - 1));
    }
}
```
自顶向下的归并排序和自底向上的归并排序都是一种很直观的实现，当我们遇到一个问题的时候，如果可以考虑用其中一种方法去解决，都应很自然的试试用另外一种方法去解决。

## 排序算法的复杂度

一个学习归并排序的重要原因是因为它是证明计算复杂性中一个重要结论的基础，即任何基于比较的排序算法，时间复杂度都不会低于$NlogN$。

下面我们引入决策树这一数学模型去证明这个结论。决策树中只存在两种类型的节点，一种是叶子节点，一种是内部节点。内部节点表示一次比较，而叶子节点表明最终的排序序列。由排列组合知识可知，长度为$N$的序列最终的排序结果可能有$N!$种（假设不存在相等的键），那么决策树中的叶子节点就会是$N!$个，而对于一个完美二叉树来说，高度为n的这样的树会有$2^n$个叶子节点，这也是二叉树所能达到的最多的节点数量。所以我们有$N!<=number of leaves<=2^h$，h就是最坏情况下的比较次数。对两侧取对数运算，可知h至少是$lgN!$，根据斯特灵公式，可知$lgN ~ NlgN$。
{% asset_img decision-tree.png %}
结合归并排序最坏的时间复杂度为$NlgN$，以及任何基于比较的排序算法至少要$NlgN$，可得出下面的结论，归并排序是一种渐进最优的基于比较排序的算法。
