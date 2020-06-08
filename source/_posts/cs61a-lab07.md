---
title: cs61a-lab07
date: 2018-10-24 22:17:26
categories:
- CS61A
tags:
- python
- SICP
---

# Check-Off
## Q1: A-Okay
Sign up for checkoffs in your lab if you'd like to get credit for this week's checkoff.
What happens in the following code? Make sure you can explain why each line works or breaks.
```
>>> class A:
...    y = 1
...    def __init__(self, y):
...        self.y = y
...    def f(self, x):
...        self.y += x
...  
>>> a = A(0) # Ok or not okay?
>>> a.f(6)   # Ok or not okay?
>>> a.f(A, 9) # Ok or not okay?
>>> A.f(a, 4) # Ok or not okay?
>>> A.f(A, 4) # Ok or not okay?
```
<!-- more -->
# Coding Practice
## Q4: Link to List

Write a function `link_to_list` that takes in a linked list and returns the sequence as a Python list. You may assume that the input list is shallow; none of the elements is another linked list.

Try to find both an iterative and recursive solution for this problem!
<span style="color:red">Iterative solution</span>
```python
def link_to_list(link):
    """Takes a linked list and returns a Python list with the same elements.

    >>> link = Link(1, Link(2, Link(3, Link(4))))
    >>> link_to_list(link)
    [1, 2, 3, 4]
    >>> link_to_list(Link.empty)
    []
    """
    "*** YOUR CODE HERE ***"
    lst = []
    while link != Link.empty:
      lst.append(link.first)
      link = link.rest
    return lst
```
<span style="color:red">Recursive solution</span>
```python
def link_to_list(link):
    """Takes a linked list and returns a Python list with the same elements.

    >>> link = Link(1, Link(2, Link(3, Link(4))))
    >>> link_to_list(link)
    [1, 2, 3, 4]
    >>> link_to_list(Link.empty)
    []
    """
    "*** YOUR CODE HERE ***"
    if link == Link.empty:
      return []
    else:
      return [link.first] + link_to_list(link.rest)
```
## Q5: Store Digits
Write a function `store_digits` that takes in an integer `n` and returns a linked list where each element of the list is a digit of `n`.
```python
def store_digits(n):
    """Stores the digits of a positive number n in a linked list.

    >>> s = store_digits(1)
    >>> s
    Link(1)
    >>> store_digits(2345)
    Link(2, Link(3, Link(4, Link(5))))
    >>> store_digits(876)
    Link(8, Link(7, Link(6)))
    """
    "*** YOUR CODE HERE ***"
    l = Link.empty
    while n > 0:
        l = Link(n % 10, l)
        n //= 10
    return l
```

## Q6: Cumulative Sum
Write a function `cumulative_sum` that mutates the Tree `t` so that each node's label becomes the sum of all labels in the subtree rooted at the node.
```python
def cumulative_sum(t):
    """Mutates t so that each node's label becomes the sum of all labels in
    the corresponding subtree rooted at t.

    >>> t = Tree(1, [Tree(3, [Tree(5)]), Tree(7)])
    >>> cumulative_sum(t)
    >>> t
    Tree(16, [Tree(8, [Tree(5)]), Tree(7)])
    """
    "*** YOUR CODE HERE ***"
    for b in t.branches:
        cumulative_sum(b)
    t.label = sum([b.label for b in t.branches]) + t.label
```
# Midterm Review (required)
## Q7: Is BST
Write a function `is_bst`, which takes a Tree `t` and returns `True` if, and only if, `t` is a valid binary search tree, which means that:

- Each node has at most two children (a leaf is automatically a valid binary search tree)
- The children are valid binary search trees
- For every node, the entries in that node's left child are less than or equal to the label of the node
- For every node, the entries in that node's right child are greater than the label of the node
Note that, if a node has only one child, that child could be considered either the left or right child. You should take this into consideration. **Do not use the `BST` constructor or anything from the `BST` class.**

Hint: It may be helpful to write helper functions `bst_min` and `bst_max` that return the minimum and maximum, respectively, of a Tree if it is a valid binary search tree.
```python
def is_bst(t):
    """Returns True if the Tree t has the structure of a valid BST.

    >>> t1 = Tree(6, [Tree(2, [Tree(1), Tree(4)]), Tree(7, [Tree(7), Tree(8)])])
    >>> is_bst(t1)
    True
    >>> t2 = Tree(8, [Tree(2, [Tree(9), Tree(1)]), Tree(3, [Tree(6)]), Tree(5)])
    >>> is_bst(t2)
    False
    >>> t3 = Tree(6, [Tree(2, [Tree(4), Tree(1)]), Tree(7, [Tree(7), Tree(8)])])
    >>> is_bst(t3)
    False
    >>> t4 = Tree(1, [Tree(2, [Tree(3, [Tree(4)])])])
    >>> is_bst(t4)
    True
    >>> t5 = Tree(1, [Tree(0, [Tree(-1, [Tree(-2)])])])
    >>> is_bst(t5)
    True
    >>> t6 = Tree(1, [Tree(4, [Tree(2, [Tree(3)])])])
    >>> is_bst(t6)
    True
    >>> t7 = Tree(2, [Tree(1, [Tree(5)]), Tree(4)])
    >>> is_bst(t7)
    False
    """
    "*** YOUR CODE HERE ***"
    def bst_max(t):
        if t.is_leaf():
            return t.label
        return max(t.label, bst_max(t.branches[-1]))
    def bst_min(t):
        if t.is_leaf():
            return t.label
        return min(t.label, bst_min(t.branches[0]))
    if t.is_leaf():
        return True
    if len(t.branches) == 1:
        return is_bst(t.branches[0]) and (t.label >= bst_min(t.branches[0]) or t.label < bst_max(t.branches[0]))
    if len(t.branches) == 2:
        return is_bst(t.branches[0]) and is_bst(t.branches[1]) and bst_max(t.branches[0]) <= t.label and bst_min(t.branches[1]) > t.label
    else:
        return False
```
## Q8: In-order traversal
Write a function that returns a generator that generates an "in-order" traversal, in which we yield the value of every node in order from left to right, assuming that each node has either 0 or 2 branches.
```python
def in_order_traversal(t):
    """
    Generator function that generates an "in-order" traversal, in which we
    yield the value of every node in order from left to right, assuming that each node has either 0 or 2 branches.

    For example, take the following tree t:
            1
        2       3
    4     5
         6  7

    We have the in-order-traversal 4, 2, 6, 5, 7, 1, 3

    >>> t = Tree(1, [Tree(2, [Tree(4), Tree(5, [Tree(6), Tree(7)])]), Tree(3)])
    >>> list(in_order_traversal(t))
    [4, 2, 6, 5, 7, 1, 3]
    """
    "*** YOUR CODE HERE ***"
    if t.is_leaf():
        yield t.label
    yield from in_order_traversal(t.branches[0])
    yield t.label
    yield from in_order_traversal(t.branches[1])
```
# Linked List Practice
## Q9: Remove All
Implement a function `remove_all` that takes a `Link`, and a `value`, and remove any linked list node containing that value. You can assume the list already has at least one node containing `value` and the first element is never removed. Notice that you are not returning anything, so you should mutate the list.
```python
def remove_all(link , value):
    """Remove all the nodes containing value. Assume there exists some
    nodes to be removed and the first element is never removed.

    >>> l1 = Link(0, Link(2, Link(2, Link(3, Link(1, Link(2, Link(3)))))))
    >>> print(l1)
    <0 2 2 3 1 2 3>
    >>> remove_all(l1, 2)
    >>> print(l1)
    <0 3 1 3>
    >>> remove_all(l1, 3)
    >>> print(l1)
    <0 1>
    """
    "*** YOUR CODE HERE ***"
    p1, p2 = link, link.rest
    while p2 is not Link.empty:
        if p2.first == value:
            p1.rest = p2.rest
        else:
            p1 = p1.rest
        p2 = p2.rest
```
<span style="color:red">The pdf solution</span>

```python
def remove_all(link , value):
    """Remove all the nodes containing value. Assume there exists some
    nodes to be removed and the first element is never removed.

    >>> l1 = Link(0, Link(2, Link(2, Link(3, Link(1, Link(2, Link(3)))))))
    >>> print(l1)
    <0 2 2 3 1 2 3>
    >>> remove_all(l1, 2)
    >>> print(l1)
    <0 3 1 3>
    >>> remove_all(l1, 3)
    >>> print(l1)
    <0 1>
    """
    if link is Link.empty or link.rest is Link.empty:
        return
    if link.rest.first == value:
        link.rest = link.rest.rest
        remove_all(link, value)
    else:
        remove_all(link.rest, value)

    # alternate solution
    if link is not Link.empty and link.rest is not Link.empty:
        remove_all(link.rest, value)
        if link.rest.first == value:
            link.rest = link.rest.rest
```
Video walkthrough: https://youtu.be/hdO9Ry8d5FU?t=39m33s
## Q10: Mutable Mapping
Implement `deep_map_mut(fn, link)`, which applies a function `fn` onto all elements in the given linked list `link`. If an element is itself a linked list, apply `fn` to each of its elements, and so on.

Your implementation should mutate the original linked list. Do not create any new linked lists.

Hint: The built-in `isinstance` function may be useful.
```
>>> s = Link(1, Link(2, Link(3, Link(4))))
>>> isinstance(s, Link)
True
>>> isinstance(s, int)
False
```
```python
def deep_map_mut(fn, link):
    """Mutates a deep link by replacing each item found with the result of calling fn on the item.  Does NOT create new Links (so no use of Link's constructor)

    Does not return the modified Link object.

    >>> link1 = Link(3, Link(Link(4), Link(5, Link(6))))
    >>> deep_map_mut(lambda x: x * x, link1)
    >>> print(link1)
    <9 <16> 25 36>
    """
    "*** YOUR CODE HERE ***"
    if link is Link.empty:
        return
    elif isinstance(link.first, Link):
        deep_map_mut(fn, link.first)
    else:
        link.first = fn(link.first)
    deep_map_mut(fn, link.rest)  
```
## Q11: Cycles
The `Link` class can represent lists with cycles. That is, a list may contain itself as a sublist.
```
>>> s = Link(1, Link(2, Link(3)))
>>> s.rest.rest.rest = s
>>> s.rest.rest.rest.rest.rest.first
3
```
Implement `has_cycle`,that returns whether its argument, a `Link` instance, contains a cycle.

Hint: Iterate through the linked list and try keeping track of which `Link` objects you've already seen.
```python
def has_cycle(link):
    """Return whether link contains a cycle.

    >>> s = Link(1, Link(2, Link(3)))
    >>> s.rest.rest.rest = s
    >>> has_cycle(s)
    True
    >>> t = Link(1, Link(2, Link(3)))
    >>> has_cycle(t)
    False
    >>> u = Link(2, Link(2, Link(2)))
    >>> has_cycle(u)
    False
    """
    "*** YOUR CODE HERE ***"
    if link is Link.empty:
        return False
    else:
        slow = link
        fast = link.rest
        while fast is not Link.empty:
            if fast.rest is Link.empty:
                return False
            slow = slow.rest
            fast = fast.rest.rest
            if slow is fast:
                return True
        return False
```
<span style = "color:red">The pdf solution</span>
```python
def has_cycle(link):
    """Return whether link contains a cycle.

    >>> s = Link(1, Link(2, Link(3)))
    >>> s.rest.rest.rest = s
    >>> has_cycle(s)
    True
    >>> t = Link(1, Link(2, Link(3)))
    >>> has_cycle(t)
    False
    >>> u = Link(2, Link(2, Link(2)))
    >>> has_cycle(u)
    False
    """
    links = []
    while link is not Link.empty:
        if link in links:
            return True
        links.append(link)
        link = link.rest
    return False
```
As an extra challenge, implement `has_cycle_constant` with only constant space. (If you followed the hint above, you will use linear space.) The solution is short (less than 20 lines of code), but requires a clever idea. Try to discover the solution yourself before asking around:
```python
def has_cycle_constant(link):
    """Return whether link contains a cycle.

    >>> s = Link(1, Link(2, Link(3)))
    >>> s.rest.rest.rest = s
    >>> has_cycle_constant(s)
    True
    >>> t = Link(1, Link(2, Link(3)))
    >>> has_cycle_constant(t)
    False
    """
    "*** YOUR CODE HERE ***"
    if link is Link.empty:
        return False
    slow, fast = link, link.rest
    while fast is not Link.empty:
        if fast.rest == Link.empty:
            return False
        elif fast is slow or fast.rest is slow:
            return True
        else:
            slow, fast = slow.rest, fast.rest.rest
    return False
```
This solution of this problem seems to be like my solution above.
# Tree Practice
## Q12: Reverse Other
Write a function `reverse_other` that mutates the tree such that **labels** on every other (odd-depth) level are reversed. For example, `Tree(1,[Tree(2, [Tree(4)]), Tree(3)])` becomes `Tree(1,[Tree(3, [Tree(4)]), Tree(2)])`. Notice that the nodes themselves are not reversed; only the labels are.
```python
def reverse_other(t):
    """Mutates the tree such that nodes on every other (odd-depth) level have the labels of their branches all reversed.

    >>> t = Tree(1, [Tree(2), Tree(3), Tree(4)])
    >>> reverse_other(t)
    >>> t
    Tree(1, [Tree(4), Tree(3), Tree(2)])
    >>> t = Tree(1, [Tree(2, [Tree(3, [Tree(4), Tree(5)]), Tree(6, [Tree(7)])]), Tree(8)])
    >>> reverse_other(t)
    >>> t
    Tree(1, [Tree(8, [Tree(3, [Tree(5), Tree(4)]), Tree(6, [Tree(7)])]), Tree(2)])
    """
    "*** YOUR CODE HERE ***"
    def reverse_helper(t, need_reverse):
        if t.is_leaf():
            return
        new_labs = [b.label for b in t.branches][::-1]
        for i in range(len(b.branches)):
            reverse_helper(b.branches[i], not need_reverse)
            if need_reverse:
                b.branches[i].label = new_labs[i]
    reverse_helper(t, True)
```
