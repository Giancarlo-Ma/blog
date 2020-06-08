---
title: string-sort
date: 2018-12-02 12:11:51
categories:
- string algorithm
mathjax: true
---

## String in java

java中字符串的表示是不可变的定长char型数组，char型为16位无符号整数，采用Unicode编码，最多支持65536个字符。length以及charAt, substring方法为常数时间复杂度，substring只返回一个String的实例。而concat操作由于要创建新的char数组作为字符串返回，所以时间复杂度为concat的两个字符串的长度和。

<!-- more -->

```java
public final class String implements Comparable<String> {
    private char[] value; // characters
    private int offset; // index of first char in array
    private int length; // length of string
    private int hash; // cache of hashCode()
    
    public int length() { return length; }
    public char charAt(int i) { return value[i + offset]; }


    private String(int offset, int length, char[] value) {
        this.offset = offset;
        this.length = length;
        this.value = value;
    }

    public String substring(int from, int to) {
        return new String(offset + from, to - from, value);
    }
```

```java
public static String reverse(String a) {
    String r = "";
    for(int i = a.length - 1; i >= 0; i--) 
        r += a.charAt(i);
    return r;
}
```
上述这段代码运行时间是$1 + 2 + ... + n$，为平方级别。所以我们引入java中另外一种字符串的表示，也就是StringBuilder类，如下是改进后的代码，运行时间为线性级别，StringBuilder底层为变长char型数组实现。
```java
public static String reverse(String s) {
    StringBuilder reverse = new StringBuilder();
    for(int i = s.length - 1; s >= 0; s--)
        reverse.append(s.charAt(i));
    return reverse.toString();
}
```
下图是String和StringBuilder的运行时间对比，可以看出，对于concat操作，StringBuilder明显更胜一筹，因为String每次都要新初始化一个String对象，而StringBuilder为变长数组，所以均摊时间为常数级别。但是对于substring操作，StringBuilder由于只有一个char变长数组，每次substring都要重新初始化一个StringBuilder对象，所以时间复杂度为线性级别，而对于String来说，只是返回一个String的实例而加以同的offset，所以开销很小，为常数级别。

{% asset_img complexity.png %}

## 键索引计数法

考虑一个典型案例，班级中的每个学生对应一个section，我们的目的是将这些学生按照section排序，a[i].key()返回该学生的section。

{% asset_img candidate.png %}

1. 计算count数组，在这一步中，根据每个学生的学生section，加一后作为索引存放在count数组，代码如下

```java
for(int i = 0; i < N; i++)
    count[a[i].key() + 1]++;
```
2. 根据count数组计算在最终排序的数组中每个条目对应的起始索引。举个栗子，这里对应1的有3人，对应2的有5人，所以最终对应3的起始索引就为8。其实上一步计算出来的count数组代表的是小于每个索引的上一个索引的数目。所以这里计算最终起始索引就很容易了。只需要有如下代码

```java
for(int i = 0; i < R; i++) {
    a[i + 1] += a[i];
}
```

3. 分布数据
第二步计算出了最终排序数组每个条目对应的起始索引，下面只需要利用一个aux数组将对应的索引填入数据即可，填入数据之后还要将count对应的项加一，以便下次出现相同键的时候可以放到正确的位置。这样只需要对原数组进行一次遍历，就可以完成对整个数组的排序。代码如下
```java
for(int i = 0; i < N; i++)
    aux[count[a[i].key()]++] = a[i];
```
4. 回写
将排好序的数组回写到原数组。

完整代码如下：
```java
int N = a.length;

int[] count = new int[R + 1];
String[] aux = new int[N];

// compute the count array
for(int i = 0; i < N; i++)
    count[a[i].key()+1]++;

// calculate the starting index of resulting array
for(int i = 0; i < R; i++)
    count[i + 1] += count[i];

// distribute records
for(int i = 0; i < N; i++)
    aux[count[a.key()]++] = a[i];

// copy back
for(int i = 0; i < N; i++)
    a[i] = aux[i];
```
该排序访问了$8N+3R+1$次数组，并且是个稳定排序，对于key在0到R-1范围。
初始化数组访问数组$N+R+1$次，第一次循环$2N$次，第二次循环$2R$次，第三次循环$3N$次，第四次循环$2N$次。当R相对于N较小时，这是个线性时间算法，突破了比较排序算法$NlgN$的下界。

## LSD算法

```java
public class LSD {
    public static void sort(String[] a, int m) {
        String[] aux = new String[a.length()];
        int R = 256;
        for(int d = m - 1; d >= 0; d--) {
            int[] count = new int[R + 1];
            for(int i = 0; i < a.length(); i++)
                count[a[i].charAt(d) + 1]++;
            for(int r = 0; r < R - 1; r++)
                count[r+1] += count[r];
            for(int i = 0; i < a.length(); i++)
                aux[count[a[i].charAt(d)]++] = a[i];
            for(int i = 0; i < a.length(); i++)
                a[i] = aux[i];
        }
    }
}