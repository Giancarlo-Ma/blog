---
title: cs61a-guerrilla02
date: 2018-10-07 15:18:30
categories:
- CS61A
tags:
- python
- SICP
---

# Higher Order Functions

## Question1
Write a `make_skipper`, which takes in a number n and outputs a function. When this function takes in a number x, it prints out all the numbers between 0 and x, skipping every nth number(meaning skip any value that is a multiple of n).
<!-- more -->
```python
# My solution
def make_skipper(n):
    """
    >>> a = make_skipper(2)
    >>> a(5)
    1
    3
    5
    """
    def skipper(x):
        i = 1
        while i <= x:
            if not i % n == 0:
                print(i)
            i += 1
    return skipper
```
```python
# PDF's solution which uses range(range(start, stop[, step]))
def make_skipper(n):
    def skipper(x):
        for i in range(x+1):
            if i % n != 0:
                print(i)
    return skipper
```
## EXTRA:Question 2
Write make_alternator which takes in two functions, f and g, and outputs a function. When this function takes in a number x, it prints out all the numbers between 1 and x, applying the function f to every odd-indexed number and g to every even-indexed number before printing.
```python
def make_alternator(f, g):
    """
    >>> a = make_alternator(lambda x: x * x, lambda x: x + 4)
    >>> a(5)
    1
    6
    9
    8
    25
    >>> b = make_alternator(lambda x: x * 2, lambda x: x + 2)
    >>> b(4)
    2
    4
    6
    6
    """
    def alternator(x):
        for i in range(1, x + 1):
            if i % 2 == 1:
                print(f(x))
            else:
                print(g(x))
    return alternator
```
# Recursion

## Question 0
a)​ What are three things you find in every recursive function?
1.<span style="color:red">One or more Base Cases</span>
2.<span style="color:red">Ways to make the problem into a smaller problem of the same type(so that it can be solved recursively).</span>
3.<span style="color:red">One or more Recursive Cases that solve the smaller problem and then uses the solution the smaller problem to solve the original(large) problem</span>
b)​ When you write a Recursive function, you seem to call it before it has been fully defined. Why doesn't this break the Python interpreter? Explain in haiku if possible.
<span style="color:red">When you define a function, Python does not evaluate the body of the function.
Python does not care
about a function’s body
until it is called</span>
## Question 1
Question 1
Hint:​ Domain is the type of data that a function takes in as argument. The Range is the type of data that a function returns.
E.g. the domain of the function square is numbers. The range is numbers.
a)​ Here is a Python function that computes the nth Fibonnaci number. What's it's domain and range? Identify those three things from **Q0a**
```python
def fib(n): # Domain is integers, Range is integers
    if n == 0: # base case
        return 0
    elif n == 1: # another base case
        return 1
    else: # ONE recursive CASE with TWO recursive CALLS
        return fib(n - 1) + fib(n - 2) #reducing the problem
```
Write out the recursive calls made when we do fib(4) (this will look like an upside down tree).
{% asset_img fibtree.png %}

b)​ What does the following cascade2 do?
```python
def cascade2(n):
    """Print a cascade of prefixes of n."""
    print(n)
    if n >= 10:
        cascade2(n//10)
        print(n)
```
<span style="color:red">Takes in a number n and prints out n, n excluding the ones digit, n excluding the tens digit, n excluding the hundreds digit, etc, then back up to the full number</span>

c)​ Describe what does each of the following functions mystery and fooply do. Identify the three things from Q0a:
```python
>>> def mystery(n):
...     if n == 0:
...         return 0
...     else:
...         return n + mystery(n - 1)
Sum integers up to n: 1 + 2 + 3 + ... + n
>>> def foo(n):
...     if n < 0:
...         return 0
...     return foo(n - 2) + foo(n - 1)
Returns the nth fibonacci number
>>> def fooply(n):
...     if n < 0:
...         return 0
...     return foo(n) + fooply(n - 1)
Returns the sum of first n fibnacci number
```
## Question 2
Mario needs to jump over a series of Piranha plants, represented as an integer composed of 0’s and 1’s. Mario only moves forward and can either step (move forward one space) or jump (move forward two spaces) from each position. How many different ways can Mario traverse a level without stepping or jumping into a Piranha plant? Assume that every level begins with a 1 (where Mario starts) and ends with a 1 (where Mario must end up).
<span style="color:red">The problem seems to be unclear, I don't know how to understand this.The solution just copies from the PDF</span>
```python
def mario_number(level):
    """
    Return the number of ways that Mario can traverse the level,
    where Mario can either hop by one digit or two digits each turn. A level is defined as being an integer with digits where a 1 is something Mario can step on and 0 is something Mario cannot step on.
    >>> mario_number(10101) # Hops each turn: (1, 2, 2)
    1
    >>> mario_number(11101) # Hops each turn: (1, 1, 1, 2), (2, 1, 2)
    2
    >>> mario_number(100101)# No way to traverse through level
    0
    """
    if level == 1:
        return 1
    elif level % 10 == 0:
        return 0
    else:
        return mario_number(level // 10) + mario_number((level // 10) // 10)
```
## EXTRA Challage: Question 3
Implement the combine function, which takes a non-negative integer n, a two-argument function f, and a number result. It applies f to the first digit of n and the result of combining the rest of the digits of n by repeatedly applying f (see the doctests). If n has no digits (because it is zero), combine returns result. Assume n is non negative.
```python
from operator import add, mul
def combine (n , f , result ):
    """
    Combine the digits in n using f.
    >>> combine (3, mul, 2) # mul (3, 2)
    6
    >>> combine (43, mul, 2) # mul (4, mul (3, 2))
    24
    >>> combine (6502, add, 3) # add (6, add (5, add (0, add (2 , 3)))
    16
    >>> combine (239, pow, 0) # pow (2, pow (3, pow (9, 0)))
    8
    """
    if n == 0:
        return result
    else:
        return combine(n // 10, f, f(n % 10, result))
```
The iteration code of this problem may be like this
```python
from operator import add, mul
def combine (n , f , result):
    """
    Combine the digits in n using f.
    >>> combine (3, mul, 2) # mul (3, 2)
    6
    >>> combine (43, mul, 2) # mul (4, mul (3, 2))
    24
    >>> combine (6502, add, 3) # add (6, add (5, add (0, add (2 , 3)))
    16
    >>> combine (239, pow, 0) # pow (2, pow (3, pow (9, 0)))
    8
    """
    while n > 0:
        n, i = n // 10, n % 10
        result = f(i, result)
    return result
```
So we can simply transform the code from iteration to recursion, only need to keep the necessary state.
