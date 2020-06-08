---
title: leetcode-841
date: 2018-12-13 13:40:45
categories:
- leetcode
tags:
- graph
---

# Keys and rooms
There are N rooms and you start in room 0.  Each room has a distinct number in 0, 1, 2, ..., N-1, and each room may have some keys to access the next room. 

Formally, each room i has a list of keys rooms[i], and each key rooms[i][j] is an integer in [0, 1, ..., N-1] where N = rooms.length.  A key rooms[i][j] = v opens the room with number v.

Initially, all the rooms start locked (except for room 0). 

You can walk back and forth between rooms freely.

Return true if and only if you can enter every room.

Example 1:
```
Input: [[1],[2],[3],[]]
Output: true
Explanation:  
We start in room 0, and pick up key 1.
We then go to room 1, and pick up key 2.
We then go to room 2, and pick up key 3.
We then go to room 3.  Since we were able to go to every room, we return true.
```
```
Example 2:

Input: [[1,3],[3,0,1],[2],[0]]
Output: false
Explanation: We can't enter the room with number 2.
```
Note:

- 1 <= rooms.length <= 1000
- 0 <= rooms[i].length <= 1000
- The number of keys in all rooms combined is at most 3000.

## 题意分析
该题是检查可达性问题的形式相对简单的一道题。在有向图中，从0出发，检查是否可以到达任意结点。我们维护的唯一的数据结构是set，我们每到达一个结点，都将其添加到set中，然后dfs相邻结点，完成一次完整的从0开始的dfs遍历后，若set的大小和rooms的大小一致，则说明可达所有结点，否则，返回false。

## 代码实现

```java
class Solution {
    Set<Integer> s = new HashSet<>();
    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        dfs(rooms, 0);
        return s.size() == rooms.size();
    }

    private void dfs(List<List<Integer>> rooms, int i) {
        s.add(i);
        List<Integer> keys = rooms.get(i);
        for(int key : keys) {
            if(!s.contains(key))
                dfs(rooms, key);
        }

    }
}
```