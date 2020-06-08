---
title: cs61a-hw03
date: 2018-08-30 22:05:10
categories:
- CS61A
tags:
- python
- SICP
---

# Q1: Has Seven
Write a recursive function has_seven that takes a positive integer n and returns whether n contains the digit 7. Use recursion - the tests will fail if you use any assignment statements.
<!-- more -->
```python
 def has_seven(k):
    """Returns True if at least one of the digits of k is a 7, False otherwise.

    >>> has_seven(3)
    False
    >>> has_seven(7)
    True
    >>> has_seven(2734)
    True
    >>> has_seven(2634)
    False
    >>> has_seven(734)
    True
    >>> has_seven(7777)
    True
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'has_seven',
    ...       ['Assign', 'AugAssign'])
    True
    """
    "*** YOUR CODE HERE ***"
    if k % 10 == 7:
        return True
    elif k == 0:
        return False
    else:
        return has_seven(k // 10)
```
```python
# iteration
def has_seven(k):
    while k > 0:
        if k % 10 == 7:
            return True
        k //= 10
```

# Q2: Ping-pong
The ping-pong sequence counts up starting from 1 and is always either counting up or counting down. At element k, the direction switches if k is a multiple of 7 or contains the digit 7. The first 30 elements of the ping-pong sequence are listed below, with direction swaps marked using brackets at the 7th, 14th, 17th, 21st, 27th, and 28th elements:

1 2 3 4 5 6 [7] 6 5 4 3 2 1 [0] 1 2 [3] 2 1 0 [-1] 0 1 2 3 4 [5] [4] 5 6
Implement a function pingpong that returns the nth element of the ping-pong sequence without using any assignment statements.

You may use the function has_seven from the previous problem.

Hint: If you're stuck, first try implementing pingpong using assignment statements and a while statement. Then, to convert this into a recursive solution, write a helper function that has a parameter for each variable that changes values in the body of the while loop.
```python
def pingpong(n):
    """Return the nth element of the ping-pong sequence.

    >>> pingpong(7)
    7
    >>> pingpong(8)
    6
    >>> pingpong(15)
    1
    >>> pingpong(21)
    -1
    >>> pingpong(22)
    0
    >>> pingpong(30)
    6
    >>> pingpong(68)
    2
    >>> pingpong(69)
    1
    >>> pingpong(70)
    0
    >>> pingpong(71)
    1
    >>> pingpong(72)
    0
    >>> pingpong(100)
    2
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'pingpong', ['Assign', 'AugAssign'])
    True
    """
    "*** YOUR CODE HERE ***"
    return helper(n, 1, 1, 1)

def helper(n, i, curr, direction):
    if i == n:
        return curr
    else:
        if i % 7 == 0 or has_seven(i):
            return helper(n, i + 1, curr - direction, -direction)
        else:
            return helper(n, i + 1, curr + direction, direction)
```
```python
# iteration
def pingpong(n):
    i, curr, direction = 1, 1, 1
    while i < n:
        if i % 7 == 0 or has_seven(i):
            direction = -direction
        curr += direction
        i += 1
    return curr
```
```python
# Alternate solution 1
def pingpong_next(x, i, step):
    if i == n:
        return x
    return pingpong_next(x + step, i + 1, next_dir(step, i+1))

def next_dir(step, i):
    if i % 7 == 0 or has_seven(i):
        return -step
    return step

return pingpong_next(1, 1, 1)

# Alternate solution 2
def pingpong(n):
    if n <= 7:
        return n
    return direction(n) + pingpong(n-1)

def direction(n):
    if n < 7:
        return 1
    if (n-1) % 7 == 0 or has_seven(n-1):
        return -1 * direction(n-1)
    return direction(n-1)
```
This is a fairly involved recursion problem, which we will first solve through iteration and then convert to a recursive solution.

Note that at any given point in the sequence, we need to keep track of the current value of the sequence (this is the value that might be output) as well as the current index of the sequence (how many items we have seen so far, not actually output).

For example, 14th element has value 0, but it's the 14th index in the sequence. We will refer to the value as x and the index as i. An iterative solution may look something like this:
```python
def pingpong(n):
    i = 1
    x = 1
    while i < n:
        x += 1
        i += 1
    return x
```
Hopefully, it is clear to you that this has a big problem. This doesn't account for changes in directions at all! It will work for the first seven values of the sequence, but then fail after that. To fix this, we can add in a check for direction, and then also keep track of the current direction to make our lives a bit easier (it's possible to compute the direction from scratch at each step, see the direction function in the alternate solution).
```python
def pingpong(n):
    i = 1
    x = 1
    is_up = True
    while i < n:
        is_up = next_dir(...)
        if is_up:
            x += 1
        else:
            x -= 1
        i += 1
    return x
```
All that's left to do is to write the next_dir function, which will take in the current direction and index and then tell us what direction to go in next (which could be the same direction):
```python
def next_dir(is_up, i):
    if i % 7 == 0 or has_seven(i):
        return not is_up
    return is_up
```
There's a tiny optimization we can make here. Instead of calculating an increment based on the value of is_up, we can make it directly store the direction of change into the variable (next_dir is also updated, see the solution for the new version):
```python
def pingpong(n):
    i = 1
    x = 1
    step = 1
    while i < n:
        step = next_dir(step, i)
        x += step
        i += 1
    return x
```
This will work, but it uses assignment. To convert it to an equivalent recursive version without assignment, make each local variable into a parameter of a new helper function, and then add an appropriate base case. Lastly, we seed the helper function with appropriate starting values by calling it with the values we had in the iterative version.

You should be able to convince yourself that the version of pingpong in the solutions has the same logic as the iterative version of pingpong above.

Video walkthrough: https://youtu.be/74gwPjgrN_k

Several doctests refer to these functions:
from operator import add, mul, sub
```python
square = lambda x: x * x

identity = lambda x: x

triple = lambda x: 3 * x

increment = lambda x: x + 1
```
# Q3: Filtered Accumulate
Extend the accumulate function from homework 2 to allow for filtering the results produced by its term argument by filling in the implementation for the filtered_accumulate function:
```python
def filtered_accumulate(combiner, base, pred, n, term):
    """Return the result of combining the terms in a sequence of N terms
    that satisfy the predicate pred. combiner is a two-argument function.
    If v1, v2, ..., vk are the values in term(1), term(2), ..., term(N)
    that satisfy pred, then the result is
         base combiner v1 combiner v2 ... combiner vk
    (treating combiner as if it were a binary operator, like +). The
    implementation uses accumulate.
    >>> filtered_accumulate(add, 0, lambda x: True, 5, identity)  # 0 + 1 + 2 + 3 + 4 + 5
    15
    >>> filtered_accumulate(add, 11, lambda x: False, 5, identity) # 11
    11
    >>> filtered_accumulate(add, 0, odd, 5, identity)   # 0 + 1 + 3 + 5
    9
    >>> filtered_accumulate(mul, 1, greater_than_5, 5, square)  # 1 * 9 * 16 * 25
    3600
    >>> # Do not use while/for loops or recursion
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'filtered_accumulate',
    ...       ['While', 'For', 'Recursion'])
    True
    """
    def combine_if(x, y):
        "*** YOUR CODE HERE ***"
        if pred(y):
            return combiner(x, y)
        else:
            return x
    return accumulate(combine_if, base, n, term)

def odd(x):
    return x % 2 == 1

def greater_than_5(x):
    return x > 5
```
filtered_accumulate has the following parameters:

combiner, base, term and n: the same arguments as accumulate.
pred: a one-argument predicate function applied to the values of term(k) for each k from 1 to n. Only values for which pred returns a true value are included in the accumulated total. If no values satisfy pred, then base is returned.
For example, the result of filtered_accumulate(add, 0, is_prime, 11, identity) is

0 + 2 + 3 + 5 + 7 + 11
for a valid definition of is_prime.

Implement filtered_accumulate by defining the combine_if function. Exactly what this function does is something for you to discover. Do not use any loops or make any recursive calls to filtered_accumulate.

Hint: The order in which you pass the arguments to combiner in your solution to accumulate matters here.
