---
title: cs61a-disc04
date: 2018-10-08 14:52:32
categories:
- CS61A
tags:
- python
- SICP
---

# Lecture Code

```python
# Slicing

odds = [3, 5, 7, 9, 11]
list(range(1, 3))
[odds[i] for i in range(1, 3)]
odds[1:3]
odds[1:]
odds[:3]
odds[:]
```
<!-- more -->

```python
# Aggregation

sum(odds)
sum({3:9, 5:25})
max(range(10))
max(range(10), key=lambda x: 7 - (x-2)*(x-4))
all([x < 5 for x in range(5)])
perfect_square = lambda x: x == round(x ** 0.5) ** 2
any([perfect_square(x) for x in range(50, 60)]) # Try ,65)

# Trees

def tree(label, branches=[]):
    for branch in branches:
        assert is_tree(branch), 'branches must be trees'
    return [label] + list(branches)

def label(tree):
    return tree[0]

def branches(tree):
    return tree[1:]

def is_tree(tree):
    if type(tree) != list or len(tree) < 1:
        return False
    for branch in branches(tree):
        if not is_tree(branch):
            return False
    return True

def is_leaf(tree):
    return not branches(tree)

### +++ === ABSTRACTION BARRIER === +++ ###

def fib_tree(n):
    """Construct a Fibonacci tree.

    >>> fib_tree(1)
    [1]
    >>> fib_tree(3)
    [2, [1], [1, [0], [1]]]
    >>> fib_tree(5)
    [5, [2, [1], [1, [0], [1]]], [3, [1, [0], [1]], [2, [1], [1, [0], [1]]]]]
    """
    if n == 0 or n == 1:
        return tree(n)
    else:
        left = fib_tree(n-2)
        right = fib_tree(n-1)
        fib_n = label(left) + label(right)
        return tree(fib_n, [left, right])

def count_leaves(t):
    """The number of leaves in tree.

    >>> count_leaves(fib_tree(5))
    8
    """
    if is_leaf(t):
        return 1
    else:
        return sum([count_leaves(b) for b in branches(t)])

def leaves(tree):
    """Return a list containing the leaf labels of tree.

    >>> leaves(fib_tree(5))
    [1, 0, 1, 0, 1, 1, 0, 1]
    """
    if is_leaf(tree):
        return [label(tree)]
    else:
        return sum([leaves(b) for b in branches(tree)], [])

def print_tree(t, indent=0):
    """Print a representation of this tree in which each label is
    indented by two spaces times its depth from the root.

    >>> print_tree(tree(1))
    1
    >>> print_tree(tree(1, [tree(2)]))
    1
      2
    >>> print_tree(fib_tree(4))
    3
      1
        0
        1
      2
        1
        1
          0
          1
    """
    print('  ' * indent + str(label(t)))
    for b in branches(t):
        print_tree(b, indent + 1)

def increment_leaves(t):
    """Return a tree like t but with leaf labels incremented.


    >>> print_tree(increment_leaves(fib_tree(4)))
    3
      1
        1
        2
      2
        2
        1
          1
          2
    """
    if is_leaf(t):
        return tree(label(t) + 1)
    else:
        bs = [increment_leaves(b) for b in branches(t)]
        return tree(label(t), bs)

def increment(t):
    """Return a tree like t but with all labels incremented.

    >>> print_tree(increment(fib_tree(4)))
    4
      2
        1
        2
      3
        2
        2
          1
          2
    """
    return tree(label(t) + 1, [increment(b) for b in branches(t)])
```

# 1 List Comprehensions
A list comprehension is a compact way to create a list whose elements are the results of applying a fixed expression to elements in another sequence.
`[<map exp> for <name> in <iter exp> if <filter exp>]`
It might be helpful to note that we can rewrite a list comprehension as an equivalent for statement. See the example.
```python
new_lst = []
for <name> in <iter exp>:
    if <filter exp>:
        new_lst += [<map_exp>]
return new_lst
```
Let’s break down an example:
`[x * x - 3 for x in [1, 2, 3, 4, 5] if x % 2 == 1]`
In this list comprehension, we are creating a new list after performing a series of operations to our initial sequence `[1, 2, 3, 4, 5]`. We only keep the elements that satisfy the filter expression `x % 2 == 1` (1, 3, and 5). For each retained element, we apply the map expression `x*x - 3` before adding it to the new list that we are creating, resulting in the output `[-2, 6, 22]`.
Note: The `if` clause in a list comprehension is optional.

## Questions
What would Python display?
```
>>> [i + 1 for i in [1, 2, 3, 4, 5] if i % 2 == 0]
[3, 5]
>>> [i * i - i for i in [5, -1, 3, -1, 3] if i > 2]
[20, 6, 6]
>>> [[y * 2 for y in [x, x + 1]] for x in [1, 2, 3, 4]]
[[2, 4], [4, 6], [6, 8], [8, 10]]
```

# 2 Trees
In computer science, trees are recursive data structures that are widely used in various settings. The diagram to the right is an example of a tree.

{% asset_img tree.png %}

Notice that the tree branches downward. In computer science, the root of a tree starts at the top, and the leaves are at the bottom.
Some terminology regarding trees:
• Parent node: A node that has branches. Parent nodes can have
multiple branches.
• Child node: A node that has a parent. A child node can only belong
to one parent.
• Root: The top node of the tree. In our example, the node that contains 7 is the root.
• Label: The value at a node. In our example, all of the integers are
values.
• Leaf: A node that has no branches. In our example, the nodes that
contain −4, 0, 6, 17, and 20 are leaves.
• Branch: A subtree of the root. Note that trees have branches, which
are trees themselves: this is why trees are recursive data structures.
• Depth: How far away a node is from the root. In other words, the
number of edges between the root of the tree to the node. In the
diagram, the node containing 19 has depth 1; the node containing 3
has depth 2. Since there are no edges between the root of the tree and
itself, the depth of the root is 0.
• Height: The depth of the lowest leaf. In the diagram, the nodes
containing −4, 0, 6, and 17 are all the “lowest leaves,” and they have
depth 4. Thus, the entire tree has height 4.
In computer science, there are many different types of trees. Some vary in the number of branches each node has; others vary in the structure of the tree.

## mplementation
```python
# Constructor
def tree(label, branches=[]):
    for branch in branches:
        assert is_tree(branch)
        return [label] + list(branches)
# Selectors
def label(tree):
    return tree[0]
def branches(tree):
    return tree[1:]
# For convenience
def is_leaf(tree):
    return not branches(tree)
```
A tree has both a value for the root node and a sequence of branches, which are also trees. In our implementation, we represent the branches as a list of trees. Since a tree is an abstract data type, our choice to use lists is just an implementation detail.
• The arguments to the constructor tree are the value for the root node and a list of branches.
• The selectors for these are label and branches.
Note that branches returns a list of trees and not a tree directly. It’s important to distinguish between working with a tree and working with a list of trees.
We have also provided a convenience function, is_leaf.
Let’s try to create the tree below.

{% asset_img tree2.png %}
```python
# Example tree construction
t = tree(1,
    [tree(3,
        [tree(4),
        tree(5),
        tree(6)]),
    tree(2)])
```
## Questions
2.1 Write a function that returns the largest number in a tree.
<span style="color:red">When we talk about recursion, what we should think. That's a problem, especially dealing with trees. The base case is also a leaf, then we use recursion to find the largest label including current label. Then we have the following.</span>
```python
def tree_max(t):
    """Return the maximum label in a tree.

    >>> t = tree(4, [tree(2, [tree(1)]), tree(10)])
    >>> tree_max(t)
    10
    """
    if is_leaf(t):
        return label(t)
    else:
        return max([label(t)] + [tree_max(b) for b in branches(t)])
```
2.2 Write a function that returns the height of a tree. Recall that the height of a tree is the length of the longest path from the root to a leaf.
```python
def height(t):
    """Return the height of a tree.
    >>> t = tree(3, [tree(5, [tree(1)]), tree(2)])
    >>> height(t)
    2
    """
    if is_leaf(t):
        return 0
    else:
        return 1 + max([height(b) for b in branches(t)])
```
2.3 Write a function that takes in a tree and squares every value. It should return a new tree. You can assume that every item is a number.
```python
def square_tree(t):
    """Return a tree with the square of every element in t"""
    return tree(label(t) ** 2, [square_tree(b) for b in branches(t)])
```
2.4 Write a function that takes in a tree and a value `x` and returns a list containing the nodes along the path required to get from the root of the tree to a node containing `x`.
If `x` is not present in the tree, return `None`. Assume that the entries of the tree are unique.
For the following tree, `find path(t, 5)` should return `[2, 7, 6, 5]`

{% asset_img tree3.png %}

<span style="color:red">This seems to be a hard problem, the way we think of it is first the base case. So what is the base case of this question? I think maybe when the label of current tree is exactly the same as x, then we can return a list which consists x or label(tree). Then we should think the recursive case. When we are in the location of some path, we need to keep a paths variable, which is a list, consisting of a series of paths. But in this problem, we only have one path. So if x is in the tree, it must be one path, otherwise the path may be None. Then when we find a path, we need to append the path to our current label. And the game is over. We can either explictly return None or do nothing, which implictly returns None</span>
```python
def find_path(tree, x):
    """
    >>> t = tree(2, [tree(7, [tree(3), tree(6, [tree(5), tree(11)])] ), tree(15)])
    >>> find_path(t, 5)
    [2, 7, 6, 5]
    >>> find_path(t, 10) # returns None
    """
    if label(tree) == x:
        return [label(tree)]
    else:
        paths = [find_path(b, x) for b in branches(tree)]
        for path in paths:
            if path:
                return [label(tree)] + path
```

2.5 Write a function that takes in a tree and a depth `k` and returns a new tree that contains only the first `k` levels of the original tree.
For example, if `t` is the tree shown in the previous question, then `prune(t, 2)` should return the following tree.

{% asset_img tree4.png %}

```python
def prune(t, k):
    if k == 0:
        return tree(label(t))
    else:
        return tree(label(t), [prune(b, k - 1) for b in branches(t)])
```

2.6 We can represent the hailstone sequence as a tree in the figure below, showing the route different numbers take to reach 1. Remember that a hailstone sequence starts with a number n, continuing to n/2 if n is even or 3n + 1 if n is odd, ending with 1. Write a function hailstone tree(n, h) which generates a tree of height h, containing hailstone numbers that will reach n.
Hint: A node of a hailstone tree will always have at least one, and at most two branches (which are also hailstone trees). Under what conditions do you add the second branch?

{% asset_img tree5.png %}
```python
def hailstone_tree(n, h):
    """Generates a tree of hailstone numbers that will reach N, with height H.
    >>> hailstone_tree(1, 0)
    [1]
    >>> hailstone_tree(1, 4)
    [1, [2, [4, [8, [16]]]]]
    >>> hailstone_tree(8, 3)
    [8, [16, [32, [64]], [5, [10]]]]
    """
    if h == 0:
        return tree(n)
    branches = [hailstone_tree(n * 2, h - 1)]
    if (n - 1) % 3 == 0 and ((n - 1) // 3) % 2 == 1 and (n - 1) // 3 > 1:
        branches += [hailstone_tree((n - 1) // 3, h - 1)]
    return tree(n, branches)
```
