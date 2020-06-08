---
title: leetcode-399
date: 2018-11-28 17:39:24
categories:
- leetcode
tags:
- graph
---

# Evaluate Division

Equations are given in the format A / B = k, where A and B are variables represented as strings, and k is a real number (floating point number). Given some queries, return the answers. If the answer does not exist, return -1.0.

**Example:**
Given a / b = 2.0, b / c = 3.0.
queries are: a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ? .
return [6.0, 0.5, -1.0, 1.0, -1.0 ].

The input is: vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries , where equations.size() == values.size(), and the values are positive. This represents the equations. Return vector<double>.

According to the example above:
```
equations = [ ["a", "b"], ["b", "c"] ],
values = [2.0, 3.0],
queries = [ ["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"] ].
```
The input is always valid. You may assume that evaluating the queries will result in no division by zero and there is no contradiction.

## 题意分析
给定一些方程，试图去寻找一些query的解。如果query中出现未知的字母，就返回-1，否则根据已有的方程计算。本题可以抽象为图问题，即搜索路径。思路是首先还是建图，维护两个map，两个map都是字符串到ArrayList的映射，第一个map告诉我们哪两个点存在连接关系，而第二个map告诉我们相应的连接的值是多少。有一个需要注意的是，我们在建图的时候不仅要将a到b的值给出，还要将这个值的倒数计算出赋给b到a的值。建图之后就是对query遍历，对map进行dfs遍历，dfs需要检查几种特殊情况，第一种就是在我们维护的set中，这个是作函数栈的模拟，如果重复出现了start，说明这个图出现了环，为了避免死循环，需要终止递归，在这里就是返回0.0。还有就是若第一个map不存在start的key，那么说明没有路径，因为起点都不存在了。最后就是若start和end相等，这里要注意使用String.equal方法，这时就可以返回value了，说明找到了路径，并且可以返回相应的计算的值了。检查这三种情况后，就是常规的dfs，注意set在这里对栈的模拟，注意环的存在。

## 代码实现
```java
class Solution {
    public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
        Map<String, ArrayList<String>> pairs = new HashMap<>();
        Map<String, ArrayList<Double>> valuePairs = new HashMap<>();
        // 建图
        for(int i = 0; i < equations.length; i++) {
            String[] equation = equations[i];
            String x = equation[0];
            String y = equation[1];
            if(!pairs.containsKey(x)) {
                pairs.put(x, new ArrayList<>());
                valuePairs.put(x, new ArrayList<>());
            }
            if(!pairs.containsKey(y)) {
                pairs.put(y, new ArrayList<>());
                valuePairs.put(y, new ArrayList<>());
            }

            pairs.get(x).add(y);
            valuePairs.get(x).add(values[i]);

            pairs.get(y).add(x);
            valuePairs.get(y).add(1 / values[i]);
        }

        double[] result = new double[queries.length];
        for(int i = 0; i < queries.length; i++) {
            String[] query = queries[i];
            result[i] = dfs(query[0], query[1], pairs, valuePairs, new HashSet<String>(), 1.0);
            if(result[i] == 0.0) result[i] = -1.0;
        }
        return result;
    }

    private double dfs(String start, String end, Map<String, ArrayList<String>> pairs, Map<String, ArrayList<Double>> valuePairs, Set<String> set, double value) {
        if(set.contains(start)) return 0.0;
        if(!pairs.containsKey(start)) return 0.0;
        if(start.equals(end)) return value;

        set.add(start);
        ArrayList<String> pairList = pairs.get(start);
        ArrayList<Double> valueList = valuePairs.get(start);
        double tmp = 0.0;
        for(int i = 0; i < pairList.size(); i++) {
            tmp = dfs(pairList.get(i), end, pairs, valuePairs, set, value * valueList.get(i));
            if(tmp != 0.0) break;
        }
        set.remove(start);
        return tmp;
    }
}
```
