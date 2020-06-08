---
title: cs61a-hw01
date: 2018-08-24 22:05:10
categories:
- CS61A
tags:
- python
- SICP
---

# Q1: A Plus Abs B
Fill in the blanks in the following function for adding a to the absolute value of b, without calling abs.
<!-- more -->
```python
from operator import add, sub

def a_plus_abs_b(a, b):
    """Return a+abs(b), but without calling abs.

    >>> a_plus_abs_b(2, 3)
    5
    >>> a_plus_abs_b(2, -3)
    5
    """
    if b < 0:
        f = a - b
    else:
        f = a + b
    return f(a, b)
```
# Q2: Two of Three
Write a function that takes three positive numbers and returns the sum of the squares of the two largest numbers. Use only a single line for the body of the function.

```python
def two_of_three(a, b, c):
    """Return x*x + y*y, where x and y are the two largest members of the
    positive numbers a, b, and c.

    >>> two_of_three(1, 2, 3)
    13
    >>> two_of_three(5, 3, 1)
    34
    >>> two_of_three(10, 2, 8)
    164
    >>> two_of_three(5, 5, 5)
    50
    """
    return max(a * a + b * b, a * a + c * c, b * b + c * c)
    # return a ** a + b ** b + c ** c - min(a, b, c) ** 2
```
# Q3: Largest Factor
Write a function that takes an integer n that is greater than 1 and returns the largest integer that is smaller than n and evenly divides n.

```python
def largest_factor(n):
    """Return the largest factor of n that is smaller than n.

    >>> largest_factor(15) # factors are 1, 3, 5
    5
    >>> largest_factor(80) # factors are 1, 2, 4, 5, 8, 10, 16, 20, 40
    40
    >>> largest_factor(13) # factor is 1 since 13 is prime
    1
    """
    d = n - 1
    while d > 0:
    	if n % d == 0:
    		return d
    	d -= 1
```
# Q4: If Function vs Statement
Let's try to write a function that does the same thing as an if statement.

```python
def if_function(condition, true_result, false_result):
    """Return true_result if condition is a true value, and
    false_result otherwise.

    >>> if_function(True, 2, 3)
    2
    >>> if_function(False, 2, 3)
    3
    >>> if_function(3==2, 3+2, 3-2)
    1
    >>> if_function(3>2, 3+2, 3-2)
    5
    """
    if condition:
        return true_result
    else:
        return false_result
```

Despite the doctests above, this function actually does not do the same thing as an if statement in all cases. To prove this fact, write functions c, t, and f such that with_if_statement prints the number 2, but with_if_function prints both 1 and 2.

```python
def with_if_statement():
    """
    >>> result = with_if_statement()
    2
    >>> print(result)
    None
    """
    if c():
        return t()
    else:
        return f()

def with_if_function():
    """
    >>> result = with_if_function()
    1
    2
    >>> print(result)
    None
    """
    return if_function(c(), t(), f())

def c():
    return False

def t():
    print(1)

def f():
    print(2)
```
Remeber that when we call a function, we must evaluate every operand as parameter in the function, so anyway, c function, t function and f function must be called regardless of the return value(True or False) by c function, while in if statement, the true clause can not be called if evaluated value is False. So the two is not exactly the same.
# Q5: Hailstone
Douglas Hofstadter's Pulitzer-prize-winning book, Gödel, Escher, Bach, poses the following mathematical puzzle.

Pick a positive integer n as the start.
If n is even, divide it by 2.
If n is odd, multiply it by 3 and add 1.
Continue this process until n is 1.
The number n will travel up and down but eventually end at 1 (at least for all numbers that have ever been tried -- nobody has ever proved that the sequence will terminate). Analogously, a hailstone travels up and down in the atmosphere before eventually landing on earth.

This sequence of values of n is often called a Hailstone sequence. Write a function that takes a single argument with formal parameter name n, prints out the hailstone sequence starting at n, and returns the number of steps in the sequence:

```python
def hailstone(n):
    """Print the hailstone sequence starting at n and return its
    length.

    >>> a = hailstone(10)
    10
    5
    16
    8
    4
    2
    1
    >>> a
    7
    """
    steps = 1
    while n != 1:
        print(n)
        if n % 2 == 0:
            n //= 2
        else:
            n = n * 3 + 1
        steps += 1
    print(n)
    return steps
```
# Q6: Quine
Write a one-line program that prints itself, using only the following features of the Python language:

- Number literals
- Assignment statements
- String literals that can be expressed using single or double quotes
- The arithmetic operators +, -, *, and /
- The built-in print function
- The built-in eval function, which evaluates a string as a Python expression
- The built-in repr function, which returns an expression that evaluates to its argument
- You can concatenate two strings by adding them together with + and repeat a string by multipying it by an integer.
- Semicolons can be used to separate multiple statements on the same line. E.g.,

```
>>> c='c';print('a');print('b' + c * 2)
a
bcc
```
Hint: Explore the relationship between single quotes, double quotes, and the repr function applied to strings.

A program that prints itself is called a Quine. Place your solution in the multi-line string named quine.
