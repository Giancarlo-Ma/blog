---
title: cs61a-disc01
date: 2018-08-28 20:05:10
categories:
- CS61A
tags:
- python
- SICP
---

# Control
## If statement
Alfonso will only wear a jacket outside if it is below 60 degrees or it is raining.
Write a function that takes in the current temperature and a boolean value telling
if it is raining and returns True if Alfonso will wear a jacket and False otherwise.
First, try solving this problem using an if statement.
<!-- more -->
```python
def wears_jacket_with_if(temp, raining):
    """
    >>> wears_jacket(90, False)
    False
    >>> wears_jacket(40, False)
    True
    >>> wears_jacket(100, True)
    True
    """
    if temp < 60 or raining:
        return True
    else:
        return False
```
Note that we’ll either return True or False based on a single condition, whose
truthiness value will also be either True or False. Knowing this, try to write this
function using a single line.
```python
def wears_jacket(temp, raining):
    return temp < 60 or raining
```

## While loop
Q1: What is the result of evaluating the following code?

```python
def square(x):
    return x * x
def so_slow(num):
    x = num
    while x > 0:
        x = x + 1
    return x / 0
square(so_slow(5))
```
Infinite loop because x will always be greater than 0; the num / 0 is never executed.

Q2: Write a function that returns True if n is a prime number and False otherwise. After
you have a working solution, think about potential ways to make your solution more
efficient.
Hint: use the % operator: x % y returns the remainder of x when divided by y.

```python
from math import sqrt
def is_prime(n):
"""
>>> is_prime(10)
False
>>> is_prime(7)
True
"""
    if n == 1:
        return False
    else:
        k = 2
        while k <= sqrt(n):
            if n % k == 0:
                return False
            k += 1
        return True
```
# Environment diagram

An environment diagram keeps track of all the variables that have been defined
and the values they are bound to. We will be using this tool throughout the course
to understand complex programs involving several different objects and function
calls.

Remember that programs are simply a set of statements, or instructions, so drawing
diagrams that represent these programs also involve following sets of instructions!
Let’s dive in.
{% asset_img environment.png %}

## Assignment Statement

Assignment statements, such as x = 3, define variables in programs. To execute
one in an environment diagram, record the variable name and the value:
1. Evaluate the expression on the right side of the = sign
2. Write the variable name and the expression’s value in the current frame

Q1: Use these rules to draw a simple diagram for the assignment statements below.
```python
x = 10 % 4
y = x
x **= 2
```
{% asset_img assignment.png %}

## def Statements
def statements create function objects and bind them to a name. To diagram def
statements, record the function name and bind the function object to the name.
It’s also important to write the parent frame of the function, which is where the
function is defined.
1. Draw the function object to the right-hand-side of the frames, denoting the
intrinsic name of the function, its parameters, and the parent frame (e.g. func
square(x) [parent = Global].
2. Write the function name in the current frame and draw an arrow from the
name to the function object.

Q2: Use these rules and the rules for assignment statements to draw a diagram for the
code below.
```python
def double(x):
    return x * 2
def triple(x):
    return x * 3
hmmm = double
double = triple
```
{% asset_img def_statement.png %}
## Call Expressions
Call expressions, such as square(2), apply functions to arguments. When executing
call expressions, we create a new frame in our diagram to keep track of local
variables:
1. Evaluate the operator, which should evaluate to a function.
2. Evaluate the operands from left to right.
3. Draw a new frame, labelling it with the following: 1
    - A unique index (f1, f2, f3, ...)
    - The intrinsic name of the function, which is the name of the function
object itself. For example, if the function object is func square(x)
[parent=Global], the intrinsic name is square.
    - The parent frame ([parent=Global])
4. Bind the formal parameters to the argument values obtained in step 2 (e.g.
bind x to 3).
5. Evaluate the body of the function in this new frame until a return value is
obtained. Write down the return value in the frame.
If a function does not have a return value, it implicitly returns None. In that case,
the “Return value” box should contain None.

Q3: Let’s put it all together! Draw an environment diagram for the following code.
```python
def double(x):
    return x * 2
hmmm = double
wow = double(3)
hmmm(wow)
```
{% asset_img en1.png%}

Q4: Draw the environment diagram that results from executing the code below. What
will be displayed when running the code (note this separately from the diagram)?
```python
from operator import add
def sub(a, b):
    sub = add
    return a - b
add = sub
sub = min
print(add(2, sub(2, 3)))
```
{% asset_img en2.png %}
Print output: 0
