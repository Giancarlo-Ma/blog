---
title: cs61a-hw06
date: 2018-10-22 20:48:27
categories:
- CS61A
tags:
- SICP
- python
---

# Object Oriented Programming
## Q1: Next Fibonacci Object
Implement the `next` method of the `Fib` class. For this class, the `value` attribute is a Fibonacci number. The `next` method returns a `Fib` instance whose ``value`` is the next Fibonacci number. The `next` method should take only constant time.

Note that in the doctests, nothing is being printed out. Rather, each call to `.next()` returns a `Fib` instance, which is represented in the interpreter as the value of that instance (see the `__repr__` method).

Hint: Keep track of the previous number by setting a new instance attribute inside next.
<!-- more -->
```python
class Fib():
    """A Fibonacci number.

    >>> start = Fib()
    >>> start
    0
    >>> start.next()
    1
    >>> start.next().next()
    1
    >>> start.next().next().next()
    2
    >>> start.next().next().next().next()
    3
    >>> start.next().next().next().next().next()
    5
    >>> start.next().next().next().next().next().next()
    8
    >>> start.next().next().next().next().next().next() # Ensure start isn't changed
    8
    """

    def __init__(self, value=0):
        self.value = value

    def next(self):
        "*** YOUR CODE HERE ***"
        if self.value == 0:
            result = Fib(1)
        else:
            result = Fib(self.value + self.previous)
        result.previous = self.value
        return result
    def __repr__(self):
        return str(self.value)
```
<span style="color:red">Remember that next must return a Fibonacci object! With this in mind, our first goal is to calculate the next Fibonacci object and return it. One approach is to figure out the base case (self.value == 0) and then decide what information is needed for the following call to next.</span>

<span style="color:red">You might also note that storing the current value makes the solution look very similar to the iterative version of the fib problem.</span>

<span style="color:red">Video walkthrough: https://youtu.be/-_bn87W4oOE</span>

## Q2: Vending Machine
Create a class called `VendingMachine` that represents a vending machine for some product. A `VendingMachine` object returns strings describing its interactions. See the doctest below for examples:
```python
class VendingMachine:
    """A vending machine that vends some product for some price.

    >>> v = VendingMachine('candy', 10)
    >>> v.vend()
    'Machine is out of stock.'
    >>> v.deposit(15)
    'Machine is out of stock. Here is your $15.'
    >>> v.restock(2)
    'Current candy stock: 2'
    >>> v.vend()
    'You must deposit $10 more.'
    >>> v.deposit(7)
    'Current balance: $7'
    >>> v.vend()
    'You must deposit $3 more.'
    >>> v.deposit(5)
    'Current balance: $12'
    >>> v.vend()
    'Here is your candy and $2 change.'
    >>> v.deposit(10)
    'Current balance: $10'
    >>> v.vend()
    'Here is your candy.'
    >>> v.deposit(15)
    'Machine is out of stock. Here is your $15.'

    >>> w = VendingMachine('soda', 2)
    >>> w.restock(3)
    'Current soda stock: 3'
    >>> w.restock(3)
    'Current soda stock: 6'
    >>> w.deposit(2)
    'Current balance: $2'
    >>> w.vend()
    'Here is your soda.'
    """
    def __init__(self, product, price):
        self.product = product
        self.price = price
        self.stock = 0
        self.balance = 0

    def restock(self, n):
        self.stock += n
        return 'Current {0} stock: {1}'.format(self.product, self.stock)

    def deposit(self, n):
        if self.stock == 0:
            return 'Machine is out of stock. Here is your ${0}.'.format(n)
        self.balance += n
        return 'Current balance: ${0}'.format(self.balance)

    def vend(self):
        if self.stock == 0:
            return 'Machine is out of stock.'
        difference = self.price - self.balance
        if difference > 0:
            return 'You must deposit ${0} more.'.format(difference)
        message = 'Here is your {0}'.format(self.product)
        if difference != 0:
            message += ' and ${0} change'.format(-difference)
        self.balance = 0
        self.stock -= 1
        return message + '.'
```
You may find Python string formatting syntax useful. A quick example:
```python
>>> ten, twenty, thirty = 10, 'twenty', [30]
>>> '{0} plus {1} is {2}'.format(ten, twenty, thirty)
'10 plus twenty is [30]'
```
