---
title: leetcode-56
date: 2018-11-19 15:22:02
categories:
- leetcode
tags:
- sort
---

# Merge Intervals

Given a collection of intervals, merge all overlapping intervals.

Example 1:
```
Input: [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
```
Example 2:
```
Input: [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considerred overlapping.
```

## 分析题意
该题让我们合并有交集的interval，首先利用start的大小对整个集合排序。然后对集合做遍历，若一个interval的start大于前面的end值说明存在overlap，需要将end值设为当前end和原来end二者中的最大值。否则说明interval是离散的，只需要将之前的start，end实例化为一个新的Interval实例，然后加入到集合，并初始化新的start和end值。遍历结束之后将最后一个start，end的interval加入到集合当中。

## 代码实现
```java
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
class Solution {
    public List<Interval> merge(List<Interval> intervals) {
        if(intervals == null || intervals.size() <= 1) return intervals;

        intervals.sort((i1, i2) -> i1.start - i2.start);
        List<Interval> res = new ArrayList<>();
        int start = intervals.get(0).start;
        int end = intervals.get(0).end;

        for(Interval interval : intervals) {
            // show this interval overlaps
            if(interval.start <= end) end = Math.max(end, interval.end);
            else {
                // the interval disjoints
                res.add(new Interval(start, end));
                start = interval.start;
                end = interval.end;
            }
        }
        // The last interval hasn't been added
        res.add(new Interval(start, end));
        return res;
    }
}
```
