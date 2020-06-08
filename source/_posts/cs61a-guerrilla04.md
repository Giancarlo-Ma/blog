---
title: cs61a-guerrilla04
date: 2018-10-29 21:33:15
categories:
- CS61A
tags:
- SICP
- python
---

# OOP
## Question 4
Write `make_max_finder`, which takes in no arguments but returns a function which takes in a list. The function it returns should return the maximum value it’s been called on so far, including the current list and any previous list. You can assume that any list this function takes in will be nonempty and contain only non-negative values.
```python
def make_max_finder():
    """
    >>> m = make_max_finder()
    >>> m([5, 6, 7])
    7
    >>> m([1, 2, 3])
    7
    >>> m([9])
    9
    >>> m2 = make_max_finder()
    >>> m2([1])
    1
    """
    maximum = 0
    def max_finder(lst):
        nonlocal maximum
        if max(lst) > maximum:
            maximum = max(lst)
        return maximum
    return max_finder
```
<!-- more -->
# Mutable Trees
## 8a
Define `filter_tree`, which takes in a tree `t` and one argument predicate function `fn`. It should mutate the tree by removing all branches of any node where calling `fn` on its label returns `False`. In addition, if this node is not the root of the tree, it should remove that node from the tree as well.
```python
def filter_tree(t, fn):
    """
    >>> t = Tree(1, [Tree(2), Tree(3, [Tree(4)]), Tree(6, [Tree(7)])])
    >>> filter_tree(t, lambda x: x % 2 != 0)
    >>> t
    tree(1, [Tree(3)])
    >>> t2 = Tree(2, [Tree(3), Tree(4), Tree(5)])
    >>> filter_tree(t2, lambda x: x != 2)
    >>> t2
    Tree(2)
    """
    if not fn(t.label):
        t.branches = []
    else:
        for branch in t.branches:
            if not fn(branch.label):
                t.branches.remove(branch)
            else:
                filter_tree(branch, fn)
```
## 8b
Fill in the definition for `nth_level_tree_map`, which also takes in a function and a tree,
but mutates the tree by applying the function to every `nth` level in the tree, where the root is the 0th level.
```python
def nth_level_tree_map(fn, tree, n):
    """Mutates a tree by mapping a function all the elements of a tree.
    >>> tree = Tree(1, [Tree(7, [Tree(3), Tree(4), Tree(5)]),
    Tree(2, [Tree(6), Tree(4)])])
    >>> nth_level_tree_map(lambda x: x + 1, tree, 2)
    >>> tree
    Tree(2, [Tree(7, [Tree(4), Tree(5), Tree(6)]),
    Tree(2, [Tree(7), Tree(5)])])
    """
    def helper(t, level):
        if level % n == 0:
            t.label = fn(t.label)
        for b in t.branches:
            helper(b, level + 1)
    helper(tree, 0)
```
# Extra Challenge Question 9: Photosynthesis
## 9a
Fill in the methods below, so that the classes interact correctly according to the
documentation (make sure to keep track of all the counters!).
```python
"""
>>> p = Plant()
>>> p.height
1
>>> p.materials
[]
>>> p.absorb()
>>> p.materials
[|Sugar|]
>>> Sugar.sugars_created
1
>>> p.leaf.sugars_used
0
>>> p.grow()
>>> p.materials
[]
>>> p.height
2
>>> p.leaf.sugars_used
1
"""
class Plant:
    def __init__(self):
        """A Plant has a Leaf, a list of sugars created so far,
        and an initial height of 1"""
        ###Write your code here###
        self.height = 1
        self.materials = [] # list of sugars
        self.leaf = Leaf(self)

    def absorb(self):
        """Calls the leaf to create sugar"""
        ###Write your code here###
        self.leaf.absorb()
    def grow(self):
        """A Plant uses all of its sugars, each of which increases
        its height by 1"""
        ###Write your code here###
        for sugar in self.materials:
            sugar.activate()
            self.height += 1
class Leaf:
    def __init__(self, plant): # Source is a Plant instance
        """A Leaf is initially alive, and keeps track of how many
        sugars it has created"""
        ###Write your code here###
        self.alive = True
        self.sugars_used = 0
        self.plant = plant
    def absorb(self):
        """If this Leaf is alive, a Sugar is added to the plant’s
        list of sugars"""
        if self.alive:
            ###Write your code here###
            self.plant.materials.append(Sugar(self, self.plant))
class Sugar:
    sugars_created = 0
    def __init__(self, leaf, plant):
        ###Write your code here###
        self.leaf = leaf
        self.plant = plant
        Sugar.sugars_created += 1
    def activate(self):
        """A sugar is used, then removed from the Plant which
        contains it"""
        ###Write your code here###
        self.plant.materials.remove(self)
        self.leaf.sugars_used += 1
    def __repr__(self):
        return '|Sugar|'
```
## 9b
Let's make this a little more realistic by giving these objects ages. Modify the code above to satisfy the following conditions. See the doctest for further guidance.
1) Every plant and leaf should have an age, but sugar does not age. Plants have a lifetime of 20 time units, and leaves have a lifetime of 2 time units.
2) Time advances by one unit at the end of a call to a plant's absorb or grow method.
3) Every time a leaf dies, it spawns a new leaf on the plant. When a plant dies, its leaf dies, and the plant becomes a zombie plant--no longer subject to time. Zombie plants do not age or die.
4) Every time a generation of leaves dies for a zombie plant, twice as many leaves rise from the organic matter of its ancestors--defying scientific explanation.
```python
"""
>>> p = Plant()
>>> p.age
0
>>> p.leaves
[|Leaf|]
>>> p.leaves[0].age
0
>>> p.age = 18
>>> p.age
18
>>> p.height
1
>>> p.absorb()
>>> p.materials
[|Sugar|]
>>> p.age
19
>>> p.leaves[0].age
1
>>> p.grow()
>>> p.age
20
>>> p.is_zombie
True
>>>p.leaves
[|Leaf|, |Leaf|]
>>> p.leaves[0].age
0
>>> p.absorb()
>>> p.age
20
"""
You will only need to make changes to the Plant and Leaf classes.
class Plant:
    def __init__(self):
        """A Plant has a Leaf, a list of sugars created so far,
        and an initial height of 1"""
        self.materials = []
        self.height = 1
        ###Write your code here###
    def absorb(self):
        """Calls the leaf to create sugar"""
        ###Write your code here###
    def grow(self):
        """A Plant uses all of its sugars,each of which increases
        its height by 1"""
        for sugar in self.materials:
            sugar.activate()
            self.height += 1
        ###Write your code here###
    def death(self):
        ###Write your code here###
class Leaf:
    def __init__(self, plant): # plant is a Plant instance
        """A Leaf is initially alive, and keeps track of how many
        sugars it has created"""
        self.alive = True
        self.sugars_used = 0
        self.plant = plant
        ###Write your code here###
    def absorb(self):
        """If this Leaf is alive, a Sugar is added to the plant’s
        list of sugars"""
        if self.alive:
            self.plant.materials.append(Sugar(self, self.plant))
            ###Write your code here###
    def death(self):
        ###Write your code here###
    def __repr__(self):
        return ‘|Leaf|’
```
# Linked list

## Question 1
The Link class can represent lists with cycles. That is, a list may contain itself as a sublist.
```python
>>> s = Link(1, Link(2, Link(3)))
>>> s.rest.rest.rest = s
>>> s.rest.rest.rest.rest.rest.first
3
```
Implement `has_cycle` that returns whether its argument, a Link instance, contains a cycle. There are two ways to do this, both iteratively, either with two pointers or keeping track of Link objects we've seen already. Try to come up with both!
```python
def has_cycle(link):
    """
    >>> s = Link(1, Link(2, Link(3)))
    >>> s.rest.rest.rest = s
    >>> has_cycle(s)
    True
    """
    if not link:
        return False
    slow = link
    fast = link.rest
    while fast is not Link.empty:
        if fast.rest is Link.empty:
            return False
        slow = slow.rest
        fast = fast.rest.rest
        if slow == fast:
            return True
    return False
```
<span style="color:red">Solution 2</span>
```python
def has_cycle(link):
    """
    >>> s = Link(1, Link(2, Link(3)))
    >>> s.rest.rest.rest = s
    >>> has_cycle(s)
    True
    """
    if not link:
        return False
    seen = []
    while link is not Link.empty:
        if link in seen:
            return True
        else:
            seen.append(link)
            link = link.rest
    return False
```
## Question 2:
Fill in the following function, which checks to see if a particular sequence of items in one linked list, `sub_link` can be found in another linked list `link` (the items have to be in order, but not necessarily consecutive).
```python
def seq_in_link(link, sub_link):
    """
    >>> lnk1 = Link(1, Link(2, Link(3, Link(4))))
    >>> lnk2 = Link(1, Link(3))
    >>> lnk3 = Link(4, Link(3, Link(2, Link(1))))
    >>> seq_in_link(lnk1, lnk2)
    True
    >>> seq_in_link(lnk1, lnk3)
    False
    """
    if sub_link is Link.empty:
        return True
    if link is Link.empty:
        return False
    if link.first == sub_link.first:
        return seq_in_link(link.rest, sub_link.rest)
    else:
        return seq_in_link(link.rest, sub_link)
```
# Iterators & Generators
## Generator WWPD
```
>>> def g(n):
        while n > 0:
            if n % 2 == 0:
                yield n
            else:
                print('odd')
            n -= 1
>>> t = g(4)
>>> t
Generator Object
>>> next(t)
4
>>> n
NameError: name 'n' is not defined
>>> t = g(next(t) + 5)
odd
>>> next(t)
odd
6
```
## Write a generator function
`gen_inf` that returns a generator which yields all the numbers in the provided list one by one in an infinite loop.
```python
>>> t = gen_inf([3, 4, 5])
>>> next(t)
3
>>> next(t)
4
>>> next(t)
5
>>> next(t)
3
>>> next(t)
4
def gen_inf(lst):
    i = 0
    while True:
        yield lst[i % len(lst)]
        i += 1
```
```python
def gen_inf(lst):
    while True:
        for elem in lst:
            yield elem
def gen_inf(lst):
    while True:
        yield from iter(lst)
```
## Write a function
`nested_gen` which, when given a nested list of iterables (including generators) `lst`, will return a generator that yields all elements nested within lst in order.
Assume you have already implemented `is_iter`, which takes in one argument and returns True if the passed in value is an iterable and False if it is not.
```python
def nested_gen(lst):
    '''
    >>> a = [1, 2, 3]
    >>> def g(lst):
    >>>     for i in lst:
    >>>         yield i
    >>> b = g([10, 11, 12])
    >>> c = g([b])
    >>> lst = [a, c, [[[2]]]]
    >>> list(nested_gen(lst))
    [1, 2, 3, 10, 11, 12, 2]
    '''
    for item in lst:
        if is_iter(item):
            yield from nested_gen(item)
        else:
            yield item
```
<span style="color:red">(solution using try / except instead of is_iter:)</span>
```python
def nested_gen(lst):
    for elem in lst:
        try:
            iter(elem)
            yield from nested_gen(elem)
        except TypeError:
            yield elem
```
## Write a function that
when given an iterable `lst`, returns a generator object. This generator should iterate over every element of `lst`, checking each element to see if it has been changed to a different value from when `lst` was originally passed into the generator function. If an element has been changed, the generator should yield it. If the length of `lst` is changed to a different value from when it was passed into the function, and `next` is called on the generator, the generator should stop iteration.
```python
def mutated_gen(lst):
    '''
    >>> lst = [1, 2, 3, 4, 5]
    >>> gen = mutated_gen(lst)
    >>> lst[1] = 7
    >>> next(gen)
    7
    >>> lst[0] = 5
    >>> lst[2] = 3
    >>> lst[3] = 9
    >>> lst[4] = 2
    >>> next(gen)
    9
    >>> lst.append(6)
    >>> next(gen)
    StopIteration Exception
    '''
    original = list(lst)
    def generator_maker(original, lst):
        curr = 0
        while curr < len(original):
            if len(original) != len(lst):
                break
            else:
                if original[curr] != lst[curr]:
                    yield lst[curr]
                curr += 1
    return generator_maker(original, lst)
```
# Growth
## Question 0
What are the runtimes of the following?
```python
def one(n):
    if 1 == 1:
        return None
    else:
        return n
```
a) $\Theta(1)$ b) $\Theta(log n)$ c) $\Theta(n)$ d) $\Theta(n^2)$ e) $\Theta(2^n)$
<span style="color:red">a</span>
```python
def two(n):
    for i in range(n):
        print(n)
```
a) $\Theta(1)$ b) $\Theta(log n)$ c) $\Theta(n)$ d) $\Theta(n^2)$ e) $\Theta(2^n)$
<span style="color:red">c</span>
```python
def three(n):
    while n > 0:
        n = n // 2
```
a) $\Theta(1)$ b) $\Theta(log n)$ c) $\Theta(n)$ d) $\Theta(n^2)$ e) $\Theta(2^n)$
<span style="color:red">b</span>
```python
def four(n):
    for i in range(n):
        for j in range(i):
    print(str(i), str(j))
```
a) $\Theta(1)$ b) $\Theta(log n)$ c) $\Theta(n)$ d) $\Theta(n^2)$ e) $\Theta(2^n)$
<span style="color:red">d</span>
