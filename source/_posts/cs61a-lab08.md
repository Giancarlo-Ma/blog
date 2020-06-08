---
title: cs61a-lab08
date: 2018-10-30 17:02:55
categories:
- CS61A
tags:
- SICP
- python
---

# Linked Lists
## Q1: Deep Linked List Length
A linked list that contains one or more linked lists as elements is called a deep linked list. Write a function `deep_len` that takes in a (possibly deep) linked list and returns the deep length of that linked list. The deep length of a linked list is the total number of non-link elements in the list, as well as the total number of elements contained in all contained lists. See the function's doctests for examples of the deep length of linked lists.

Hint: Use `isinstance` to check if something is an instance of an object.
```python
def deep_len(lnk):
    """ Returns the deep length of a possibly deep linked list.

    >>> deep_len(Link(1, Link(2, Link(3))))
    3
    >>> deep_len(Link(Link(1, Link(2)), Link(3, Link(4))))
    4
    >>> levels = Link(Link(Link(1, Link(2)), \
            Link(3)), Link(Link(4), Link(5)))
    >>> print(levels)
    <<<1 2> 3> <4> 5>
    >>> deep_len(levels)
    5
    """
    "*** YOUR CODE HERE ***"
    if lnk is Link.empty:
        return 0
    elif not isinstance(lnk, Link):
        return 1
    else:
        return deep_len(lnk.first) + deep_len(lnk.rest)
```
