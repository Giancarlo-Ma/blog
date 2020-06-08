---
title: cs61a-hw02
date: 2018-08-25 22:05:10
categories:
- CS61A
tags:
- python
- SICP
---

# Q1: Make Adder with a Lambda
Implement the make_adder function, which takes in a number n and returns a function that takes in an another number k and returns n + k. Your solution must consist of a single return statement.
<!-- more -->

```python
 def make_adder(n):
    """Return a function that takes an argument K and returns N + K.

    >>> add_three = make_adder(3)
    >>> add_three(1) + add_three(2)
    9
    >>> make_adder(1)(2)
    3
    """
    return lambda k: n + k
```
# Q2: Product
The summation(n, term) function from the higher-order functions lecture adds up term(1) + ... + term(n). Write a similar function called product that returns term(1) * ... * term(n). Do not use recursion.

```python
def product(n, term):
    """Return the product of the first n terms in a sequence.
    n    -- a positive integer
    term -- a function that takes one argument

    >>> product(3, identity)  # 1 * 2 * 3
    6
    >>> product(5, identity)  # 1 * 2 * 3 * 4 * 5
    120
    >>> product(3, square)    # 1^2 * 2^2 * 3^2
    36
    >>> product(5, square)    # 1^2 * 2^2 * 3^2 * 4^2 * 5^2
    14400
    >>> product(3, increment) # (1+1) * (2+1) * (3+1)
    24
    >>> product(3, triple)    # 1*3 * 2*3 * 3*3
    162
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'product', ['Recursion'])
    True
    """
    "*** YOUR CODE HERE ***"
    total, k = 1,
    while k <= n:
        total, k = total * term(k), k + 1
    return total
```
Now, define the factorial function in terms of product in one line.
```python
def factorial(n):
    """Return n factorial for n >= 0 by calling product.

    >>> factorial(4)  # 4 * 3 * 2 * 1
    24
    >>> factorial(6)  # 6 * 5 * 4 * 3 * 2 * 1
    720
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'factorial', ['Recursion', 'For', 'While'])
    True
    """
    "*** YOUR CODE HERE ***"
    return product(n, lambda x: x)
```
# Q3: Accumulate
Let's take a look at how summation and product are instances of a more general function called accumulate:

```python
def accumulate(combiner, base, n, term):
    """Return the result of combining the first n terms in a sequence and base.
    The terms to be combined are term(1), term(2), ..., term(n).  combiner is a
    two-argument commutative, associative function.

    >>> accumulate(add, 0, 5, identity)  # 0 + 1 + 2 + 3 + 4 + 5
    15
    >>> accumulate(add, 11, 5, identity) # 11 + 1 + 2 + 3 + 4 + 5
    26
    >>> accumulate(add, 11, 0, identity) # 11
    11
    >>> accumulate(add, 11, 3, square)   # 11 + 1^2 + 2^2 + 3^2
    25
    >>> accumulate(mul, 2, 3, square)    # 2 * 1^2 * 2^2 * 3^2
    72
    """
    "*** YOUR CODE HERE ***"
    total, k = base, 1
    while k <= n:
        total, k = combiner(total, term(k)), k + 1
    return total
```
The following are recursive solution:
```python
def accumulate2(combiner, base, n, term):
    # It seems to be the term n combines accumulate n - 1 terms.
    if n == 0:
        return base
    else:
        return combiner(term(n), accumulate(combiner, base, n - 1, term))
```
```python
def accumulate3(combiner, base, n, term):
    # This also seems to be the base which is the nth term combines original base accumulate n - 1 terms.
    if n == 0:
        return base
    else:
        return accumulate3(combiner, combiner(base, term(n)), n - 1, term)
```


accumulate has the following parameters:

- term and n: the same parameters as in summation and product
- combiner: a two-argument function that specifies how the current term is combined with the previously accumulated terms.
- base: value at which to start the accumulation.

For example, the result of accumulate(add, 11, 3, square) is
11 + square(1) + square(2) + square(3) = 25
You may assume that combiner is associative and commutative.

After implementing accumulate, show how summation and product can both be defined as simple calls to accumulate:

```python
def summation_using_accumulate(n, term):
    """Returns the sum of term(1) + ... + term(n). The implementation
    uses accumulate.

    >>> summation_using_accumulate(5, square)
    55
    >>> summation_using_accumulate(5, triple)
    45
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'summation_using_accumulate',
    ...       ['Recursion', 'For', 'While'])
    True
    """
    "*** YOUR CODE HERE ***"
    return accumulate(add, 0, n, term)
```

```python
def product_using_accumulate(n, term):
    """An implementation of product using accumulate.

    >>> product_using_accumulate(4, square)
    576
    >>> product_using_accumulate(6, triple)
    524880
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'product_using_accumulate',
    ...       ['Recursion', 'For', 'While'])
    True
    """
    "*** YOUR CODE HERE ***"
    return accumulate(mul, 1, n, term)
```
# Q4: Make Repeater
Implement a function make_repeater so that make_repeater(f, n)(x) returns f(f(...f(x)...)), where f is applied n times. That is, make_repeater(f, n) returns another function that can then be applied to another argument. For example, make_repeater(square, 3)(42) evaluates to square(square(square(42))). Yes, it makes sense to apply the function zero times! See if you can figure out a reasonable function to return for that case. You may use either loops or recursion in your implementation.

```python
def make_repeater(f, n):
    """Return the function that computes the nth application of f.

    >>> add_three = make_repeater(increment, 3)
    >>> add_three(5)
    8
    >>> make_repeater(triple, 5)(1) # 3 * 3 * 3 * 3 * 3 * 1
    243
    >>> make_repeater(square, 2)(5) # square(square(5))
    625
    >>> make_repeater(square, 4)(5) # square(square(square(square(5))))
    152587890625
    >>> make_repeater(square, 0)(5)
    5
    """
    "*** YOUR CODE HERE ***"
    def repeat(x):
        k = 0
        while k < n:
            x = f(x)
            k += 1
        return x
    return repeat
```
```python
def make_repeater2(f, n):
    g = identity
    while n > 0:
        g = compose1(f, g)
        n = n - 1
    return g
```
```python
def make_repeater3(f, n):
    if n == 0:
        return lambda x: x
    return lambda x: f(make_repeater3(f, n - 1)(x))
```
```python
def make_repeater4(f, n):
    if n == 0:
        return lambda x: x
    return compose1(f, make_repeater4(f, n - 1))
```
```python
def make_repeater5(f, n):
    return accumulate(compose1, lambda x: x, n, lambda k: f)
```

For an extra challenge, try defining make_repeater using compose1 and your accumulate function in a single one-line return statement.
```python
def compose1(f, g):
    """Return a function h, such that h(x) = f(g(x))."""
    def h(x):
        return f(g(x))
    return h
```
# Q5: Church numerals
The logician Alonzo Church invented a system of representing non-negative integers entirely using functions. The purpose was to show that functions are sufficient to describe all of number theory: if we have functions, we do not need to assume that numbers exist, but instead we can invent them.

Your goal in this problem is to rediscover this representation known as Church numerals. Here are the definitions of zero, as well as a function that returns one more than its argument:
```python
def zero(f):
    return lambda x: x

def successor(n):
    return lambda f: lambda x: f(n(f)(x))
```
First, define functions one and two such that they have the same behavior as successor(zero) and successsor(successor(zero)) respectively, but do not call successor in your implementation.

Next, implement a function church_to_int that converts a church numeral argument to a regular Python integer.

Finally, implement functions add_church, mul_church, and pow_church that perform addition, multiplication, and exponentiation on church numerals.
```python
def one(f):
    """Church numeral 1: same as successor(zero)"""
    return lambda x: f(x)

def two(f):
    """Church numeral 2: same as successor(successor(zero))"""
    return lambda x: f(f(x))

three = successor(two)

def church_to_int(n):
    """Convert the Church numeral n to a Python integer.

    >>> church_to_int(zero)
    0
    >>> church_to_int(one)
    1
    >>> church_to_int(two)
    2
    >>> church_to_int(three)
    3
    """
    return n(lambda x: x + 1)(0)

def add_church(m, n):
    """Return the Church numeral for m + n, for Church numerals m and n.

    >>> church_to_int(add_church(two, three))
    5
    """
    return lambda f: lambda x: m(f)(n(f)(x))

def mul_church(m, n):
    """Return the Church numeral for m * n, for Church numerals m and n.

    >>> four = successor(three)
    >>> church_to_int(mul_church(two, three))
    6
    >>> church_to_int(mul_church(three, four))
    12
    """
    return lambda f: m(n(f))

def pow_church(m, n):
    """Return the Church numeral m ** n, for Church numerals m and n.

    >>> church_to_int(pow_church(two, three))
    8
    >>> church_to_int(pow_church(three, two))
    9
    """
    return n(m)
```
