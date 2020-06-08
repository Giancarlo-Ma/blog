---
title: cs61a-guerrilla
date: 2018-08-29 22:05:10
categories:
- CS61A
tags:
- python
- SICP
---

# Guerrilla Section 1: Functions, Control, Environment Diagrams

## Functions
Question 0:
What will Python output?
```
>>> from operator import add, mul
>>> mul(add(5, 6), 8)
88
>>> print(‘x’)
x
>>> y = print(‘x’)
x
>>> print(y)
None
>>> print(add(4, 2), print(‘a’))
a
6 None
```
<!-- more -->
Question 1: Raising the Bar
What will Python output?
```
>>> def foo(x):
        print(x)
        return x + 1
>>> def bar(y, x):
        print(x - y)
>>> foo(3)
3
4
>>> bar(3)
Error
>>> bar(6, 1)
-5
>>> bar(foo(10), 11)
10
0
```
## Control
Question 2: Control yourself
a) Which numbers (1-4) will be printed after executing the following code?
```python
n = 0
if n:
    print(1)
elif n < 2:
    print(2)
else:
    print(3)
print(4)
```
2 and 4
b) WWPD (What would Python Display) after evaluating each of the following expressions?
```
>>> 0 and 1 / 0
0
>>> 6 or 1 or “a” or 1 / 0
6
>>> 6 and 1 and “a” and 1 / 0
Error
>>> print(print(4) and 2)
4
None
>>> not True and print(“a”)
False
```

Question 3: You have control
a) Define a function, count_digits, which takes in a positive integer, n, and counts the
number of digits in that number.
```python
def count_digits(n):
    """
    >>> count_digits(42)
    2
    >>> count_digits(12345678)
    8
    >>> count_digits(1)
    1
    """
    count = 0
    while n > 0:
        n //= 10
        count += 1
    return count
```

b) Define a function, count_matches, which takes in two positive integers n and m, and
counts the number of digits that match.
```python
def count_matches(n, m):
    """
    >>> count_matches(10, 30)
    1
    >>> count_matches(12345, 23456)
    0
    >>> count_matches(212121, 321321)
    2
    >>> count_matches(101, 11) # only one’s place matches
    1
    >>> count_matches(101, 10) # no place matches
    0
    """
    matches = 0
    while (n > 0 and m > 0):
        k, d = n % 10, m % 10
        if k == d:
            matches += 1
        n, m = n // 10, m // 10
    return matches
```
## Environment Diagrams
Question 4: A New Environment
a) Draw the environment diagram for evaluating the following code
```python
def f(x):
    return y + x
y = 10
f(8)
```
{% asset_img en3.png %}
b) Draw the environment diagram for evaluating the following code
```python
def dessef(a, b):
     c = a + b
     b = b + 1
b = 6
dessef(b, 4)
```
{% asset_img en4.png %}

Question 5: Environmental Collapse
a) Draw an environment diagram for the following code
```python
def foo(x, y):
    foo = bar
    return foo(bar(x, x), y)
def bar(z, x):
    return z + y
y = 5
foo(1, 2)
```
{% asset_img en5.png %}

b) Draw an environment diagram for the following code
```python
def spain(japan, iran):
    def world(cup, egypt):
        return japan-poland
    return iran(world(iran, poland))
def saudi(arabia):
    return japan + 3
japan, poland = 3, 7
spain(poland+1, saudi)
```
{% asset_img en6.png %}

c) Draw an environment diagram for the following code
```python
cap = 9
hulk = 3
def marvel(cap, thor, marvel):
    iron = hulk + cap
    if thor > cap:
        def marvel(cap, thor, avengers):
            return iron
    else:
        iron = hulk
    return marvel(thor, cap, marvel)
def iron(man):
    hulk = cap - 1
    return hulk
marvel(cap, iron(3), marvel)
```
