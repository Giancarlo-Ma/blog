---
title: cs61a-disc03
date: 2018-08-29 22:05:10
categories:
- CS61A
tags:
- python
- SICP
---

# Lecture example
The order of recursion
```python
def cascade(n):
    """
    >>> cascade(1234)
    1234
    123
    12
    1
    12
    123
    1234
    """
    if n < 10:
        print(n)
    else:
        print(n)
        cascade(n // 10)
        print(n)
```
<!-- more -->
```python
def cascade1(n):
    print(n)
    if n >= 10:
        cascade(n // 10)
        print(n)
```


```python
def inverse_cascade(n):
    """
    >>> inverse_cascade(1234)
    1
    12
    123
    1234
    123
    12
    1
    """
    grow(n)
    print(n)
    shrink(n)

def f_then_g(f, g, n):
    if n:
        f(n)
        g(n)

grow = lambda n: f_then_g(grow, print, n // 10)
shrink = lambda n: f_then_g(print, shrink, n // 10)
```
Tree recursion
```python
def fib(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fib(n - 1) + fib(n - 2)
```

```python
def count_partitions(n, m):
    """Count the partitions of n using parts up to size m.

    >>> count_partitions(6, 4)
    9
    >>> count_partitions(10, 10)
    42
    """
    if n = 0:
        return 1
    elif n < 0:
        return 0
    elif m = 0:
        return 0
    else:
        with_m = count_partitions(n - m, m);
        without_m = count_partitions(n, m - 1);
        return with_m + without_m
```

# More Recursion
In discussion 1, we implemented the function `is_prime`, which takes in a positive integer and returns whether or not that integer is prime, iteratively.
Now, let’s implement it recursively! As a reminder, an integer is considered prime if it has exactly two unique factors: 1 and itself.

```python
# My solution, from iteration to recursion
from math import sqrt
def is_prime(n):
    """
    >>> is_prime(7)
    True
    >>> is_prime(10)
    False
    >>> is_prime(1)
    False
    """
    def prime_helper(n, k):
        if k > sqrt(n):
            return True
        elif n % k == 0 or n == 1:
            return False
        else:
            return prime_helper(n, k+1)
    return prime_helper(n, 2)
```
```python
def is_prime(n):
    def prime_helper(index):
        # if n > sqrt(n):
        if n == index:
            return True
        elif n % index == 0 or n == 1:
            return False
        else:
            return prime_helper(index + 1)
    return prime_helper(2)
```

Define a function `make_fn_repeater` which takes in a one-argument function `f` and an integer `x`. It should return another function which takes in one argument, another integer. This function returns the result of applying `f` to `x` this number of times.

Make sure to use recursion in your solution

```python
def make_func_repeater(f, x):
    """
    >>> incr_1 = make_func_repeater(lambda x: x + 1, 1)
    >>> incr_1(2) #same as f(f(x))
    3
    >>> inct_1(5)
    6
    """
    def repeat(i):
        if i == 0:
            return x
        else:
            return f(repeat(i - 1))
    return repeat
```

# Tree Recursion

Consider a function that requires more than one recursive call. A simple example is the recursive `fibonacci` function:
```python
def fib(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fib(n - 1) + fib(n - 2)
```
This type of recursion is called `tree recursion`, because it makes more than one recursive call in its recursive case. If we draw out the recursive calls, we see the recursive calls in the shape of an upside-down tree:
{% asset_img tree.png %}

We could, in theory, use loops to write the same procedure. However, problems that are naturally solved using tree recursive procedures are generally difficult to write iteratively. It is sometimes the case that a tree recursive
problem also involves iteration: for example, you might use a while loop to add together multiple recursive calls.
As a general rule of thumb, whenever you need to try multiple possibilities at the same time, you should consider using tree recursion.

## Question

I want to go up a flight of stairs that has `n` steps. I can either take 1 or 2 steps each time. How many different ways can I go up this flight of stairs?
Write a function `count_stair_ways` that solves this problem for me. Assume `n` is positive.
Before we start, what’s the base case for this question? What is the simplest input?

<span style="color:red">When there is only one step, we have only one way to go up the stair. When there are two steps, we have two ways: take a two-step or take 2 one-steps.</span>

What do `count_stair_ways(n - 1)` and `count_stair_ways(n - 2)` represent?

<span style="color:red">`count_stair_ways(n - 1)` represents the number of ways to go up to the first `n-1` stairs. `count_stair_ways(n - 2)` represents the number of ways to go up to the first `n-2` stairs.</span>

Use those two recursive calls to write the recursive case:
```def count_stair_ways(n):
    if n == 1:
        return 1
    elif n == 2:
        return 2
    else:
        return count_stair_ways(n - 1) + count_stair_ways(n - 2)
```

Consider a special version of the `count_stairways` problem, where instead of taking 1 or 2 steps, we are able to take **up to and including k** steps at a time.
Write a function `count_k` that figures out the number of paths for this scenario. Assume `n` and `k` are positive.
```python
def count_k(n, k):
    """
    >>> count_k(3, 3) # 3, 2 + 1, 1 + 2, 1 + 1 + 1
    4
    >>> count_k(4, 4)
    8
    >>> count_k(10, 3)
    274
    >>> count_k(300, 1) # Only one step at a time
    1
    """
    if n == 0:
        return 1
    elif n < 0:
        return 0
    else:
        i = 1
        total = 0
        while i <= k:
            total += count_k(n - i, k)
            i += 1
        return total
```
<span style="color:red">This seems to be like the partition code in the lecture, but when I use the way of partition to solve this problem, I find a significant bug which is that in out partition, 1 plus 2 and 2 plus 1 is considered to be the same kind, but in our stairs code, I first go up 2 stairs then 1 stair is considered to be different from first going up 1 stair and then go 2 stairs. So we need another kind of thought to solve this, which uses a loop.[Video walkthrough](https://www.youtube.com/watch?v=oGBcPguM9vo&list=PLx38hZJ5RLZd35oDi3TGz5p9DyyxU3WwA&index=5)</span>

Here’s a part of the Pascal’s triangle:

{% asset_img pascal.png %}

Every number in Pascal’s triangle is defined as the sum of the item above it and the item that is directly to the upper left of it, use 0 if the entry is empty. Define the procedure `pascal(row, column)` which takes a `row` and a `column`, and finds the value at that position in the triangle.

```python
# My solution, by observing that the number above leading diagonal is all zero.
def pascal(row, column):
    if column == 0:
        return 1
    elif column > row:
        return 0
    else:
        return pascal(row - 1, column - 1) + pascal(row - 1, column)
```
```python
# The pdf solution, which has a subtle difference at the condition of returning zero.
def pascal(row, column):
    if column == 0:
        return 1
    elif row == 0:
        return 0
    else:
        return pascal(row - 1, column - 1) + pascal(row - 1, column)
```
