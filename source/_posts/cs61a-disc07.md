---
title: cs61a-disc07
date: 2018-10-28 15:22:35
categories:
- CS61A
tags:
- SICP
- python
mathjax: true
---
# Order of growth

## Questions
What is the order of growth for the following functions?
```python
def sum_of_factorial(n):
    if n == 0:
        return 1
    else:
        return factorial(n) + sum_of_factorial(n - 1)
```
<!-- more -->
The order of growth is $\Theta(n^2)$
```python
def bonk(n):
    total = 0
    while n >= 2:
        total += n
        n = n / 2
    return total
```
The order of growth is $\Theta(log(n))$
```python
def mod_7(n):
    if n % 7 == 0:
        return 0
    else:
        return 1 + mod_7(n - 1)
```
This order of growth is $\Theta(1)$
# Linked List
## Questions

Write a function that takes in a Python list of linked lists and multiplies them
element-wise. It should return a new linked list.
If not all of the Link objects are of equal length, return a linked list whose length is
that of the shortest linked list given. You may assume the Link objects are shallow
linked lists, and that lst of lnks contains at least one linked list.
```python
def multiply_lnks(lst_of_lnks):
    """
    >>> a = Link(2, Link(3, Link(5)))
    >>> b = Link(6, Link(4, Link(2)))
    >>> c = Link(4, Link(1, Link(0, Link(2))))
    >>> p = multiply_lnks([a, b, c])
    >>> p.first
    48
    >>> p.rest.first
    12
    >>> p.rest.rest.rest
    ()
    """
    product = 1
    for lst in lst_of_lnks:
        if lst is Link.empty:
            return Link.empty
        product *= lst.first
    lst_rest = [lst.rest for lst in lst_of_lnks]
    return Link(product, multiply_lnks(lst_rest))
```
<span style="color:red">For our base case, if we detect that any of the lists in the list of Links is empty, we can return the empty linked list as we’re not going to multiply anything.</span>
<span style="color:red">Otherwise, we compute the product of all the firsts in our list of Links. Then, the subproblem we use here is the rest of all the linked lists in our list of Links.
<span style="color:red">Remember that the result of calling `multiply_lnks` will be a linked list! We’ll use the product we’ve built so far as the first item in the returned Link, and then the result of the recursive call as the rest of that Link.</span>
<span style="color:red">Iterative solution:</span>
```python
def multiply_lnks(lst_of_lnks):
    import operator
    from functools import reduce
    def prod(factors):
        return reduce(operator.mul, factors, 1)
    head = Link.empty
    tail = head
    while Link.empty not in lst_of_lnks:
        all_prod = prod([l.first for l in lst_of_lnks])
    if head is Link.empty:
        head = Link(all_prod)
        tail = head
    else:
        tail.rest = Link(all_prod)
        tail = tail.rest
    lst_of_lnks = [l.rest for l in lst_of_lnks]
    return head
```
<span style="color:red">The iterative solution is a bit more involved than the recursive solution. Instead of building the list “backwards” as in the recursive solution (because of the order that the recursive calls result in, the last item in our list will be finished first), we’ll build the resulting linked list as we go along.</span>
<span style="color:red">We use head and tail to track the front and end of the new linked list we’re creating.</span>
<span style="color:red">Our stopping condition for the loop is if any of the Links in our list of Links runs out of items.</span>
<span style="color:red">Finally, there’s some special handling for the first item. We need to update both head and tail in that case. Otherwise, we just append to the end of our list using tail, and update tail</span>

Write a function that takes a sorted linked list of integers and mutates it so that
all duplicates are removed.

```python
def remove_duplicates(lnk):
    """
    >>> lnk = Link(1, Link(1, Link(1, Link(1, Link(5)))))
    >>> remove_duplicates(lnk)
    >>> lnk
    Link(1, Link(5))
    """
    if lnk == Link.empty or lnk.rest == Link.empty:
        return
    if lnk.first == lnk.rest.first:
        lnk.rest = lnk.rest.rest
        remove_duplicates(lnk)
    else:
        remove_duplicates(lnk.rest)
```
<span style="color:red">For a list of one or no items, there are no duplicates to remove.</span>

Now consider two possible cases:
- If there is a duplicate of the first item, we will find that the first and second items in the list will have the same values (that is, `lnk.first == lnk.rest.first`).
We can confidently state this because we were told that the input linked list is in sorted order, so duplicates are adjacent to each other. We’ll remove the second item from the list.
Finally, it’s tempting to recurse on the remainder of the list (`lnk.rest`), but remember that there could still be more duplicates of the first item in the rest of the list! So we have to recurse on `lnk` instead. Remember that we have removed an item from the list, so the list is one element smaller than before.
Normally, recursing on the same list wouldn’t be a valid subproblem.
- Otherwise, there is no duplicate of the first item. We can safely recurse on the remainder of the list.
<span style="color:red">Iterative solution</span>
```python
while lst is not Link.empty or lst.rest is not Link.empty:
    if lst.first == lst.rest.first:
        lst.rest = lst.rest.rest
    else:
        lst = lst.rest
```
# Midterm Review
3.1 Write a function that takes a list and returns a new list that keeps only the evenindexed elements of lst and multiplies them by their corresponding index.
```python
def even_weighted(lst):
    """
    >>> x = [1, 2, 3, 4, 5, 6]
    >>> even_weighted(x)
    [0, 6, 20]
    """
    return [i * lst[i] for i in range(len(lst)) if i % 2 == 0]
    # return [i * lst[i] for i in range(0, len(lst), 2)]
    # Or use for loop to construct a list.
```
3.2 The **quicksort** sorting algorithm is an efficient and commonly used algorithm to order the elements of a list. We choose one element of the list to be the **pivot** element and partition the remaining elements into two lists: one of elements less than the pivot and one of elements greater than the pivot. We recursively sort the two lists, which gives us a sorted list of all the elements less than the pivot and all the elements greater than the pivot, which we can then combine with the pivot for
a completely sorted list.
First, implement the `quicksort_list` function. Choose the first element of the list as the pivot. You may assume that all elements are distinct.

*Note: in computer science, “sorting” refers to placing elements in order from least to greatest, not putting things in categories*
```python
def quicksort_list(lst):
    """
    >>> quicksort_list([3, 1, 4])
    [1, 3, 4]
    """
    if not lst or len(lst) == 1:
        return lst
    pivot = lst[0]
    less = [i for i in lst[1:] if i < pivot]
    greater = [i for i in lst[1:] if i > pivot]
    return quicksort_list(less) + [pivot] + quicksort_list(greater)
```
3.3 Write a function that takes in a list and returns the maximum product that can be formed using nonconsecutive elements of the list. The input list will contain only numbers greater than or equal to 1.
```python
def max_product(lst):
    """Return the maximum product that can be formed using lst without using any consecutive numbers
    >>> max_product([10,3,1,9,2]) # 10 * 9
    90
    >>> max_product([5,10,5,10,5]) # 5 * 5 * 5
    125
    >>> max_product([])
    1
    """
    if not lst:
        return 1
    elif len(lst) == 1:
        return lst[0]
    else:
        max(max_product(lst[1:]), lst[0] * max_product(lst[2: ]))
```
<span style="color:red">At each step, we choose if we want to include the current number in our product
or not:</span>
- If we include the current number, we cannot use the adjacent number.
- If we don’t use the current number, we try the adjacent number (and obviously ignore the current number).
The recursive calls represent these two alternate realities. Finally, we pick the one that gives us the largest product.</span>

3.4 Complete `redundant_map`, which takes a tree `t` and a function `f`, and applies `f` to each node ($2^d$) times, where d is the depth of the node. The root has a depth of 0. It should mutate the existing tree rather than creating a new tree.
```python
def redundant_map(t, f):
    """
    >>> double = lambda x: x*2
    >>> tree = Tree(1, [Tree(1), Tree(2, [Tree(1, [Tree(1)])])])
    >>> redundant_map(tree, double)
    >>> print_levels(tree)
    [2] # 1 * 2 ˆ (1) ; Apply double one time
    [4, 8] # 1 * 2 ˆ (2), 2 * 2 ˆ (2) ; Apply double two times
    [16] # 1 * 2 ˆ (2 ˆ 2) ; Apply double four times
    [256] # 1 * 2 ˆ (2 ˆ 3) ; Apply double eight times
    """
    t.label =f(t.label)
    new_f = lambda x: f(f(x))
    for b in t.branches:
        redundant_map(b, new_f)
```
<span style="color:red">Every time we recurse, we transform our map function into one that is one level
deeper in terms of calls to input function f. To see why this will achieve the result we want, let’s look at what happens to some input function f.</span>
• The first call to redundant_map will call f once.
• This means on the second call to redundant_map, we pass in a function g that
causes the original f to be called two times.
• On the third call to redundant_map, we pass in a function h that causes g to
be called two times. Remember that g calls original f twice, so h will end up
calling original f four times.
Therefore, each level will have double the calls to f as the previous level, which
matches the requirements.
