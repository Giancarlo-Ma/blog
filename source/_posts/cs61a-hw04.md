---
title: cs61a-hw04
date: 2018-10-07 17:51:19
categories:
- CS61A
tags:
- python
- SICP
---

# Lecture Code
```python
def fib_iter(n):
    i = 0
    curr, next = 0, 1
    while i < n:
        curr, next = next, curr + next
    	   i += 1
    return curr
```
```python
# Rational arithmetic

def add_rational(x, y):
    """The sum of rational numbers X and Y."""
    nx, dx = numer(x), denom(x)
    ny, dy = numer(y), denom(y)
    return rational(nx * dy + ny * dx, dx * dy)

def mul_rational(x, y):
    """The product of rational numbers X and Y."""
    return rational(numer(x) * numer(y), denom(x) * denom(y))

def rationals_are_equal(x, y):
    """True iff rational numbers X and Y are equal."""
    return numer(x) * denom(y) == numer(y) * denom(x)

def print_rational(x):
    """Print rational X."""
    print(numer(x), "/", denom(x))
```
<!-- more -->
```python
# Constructor and selectors

def rational(n, d):
    """A representation of the rational number N/D."""
    return [n, d]

def numer(x):
    """Return the numerator of rational number X."""
    return x[0]

def denom(x):
    """Return the denominator of rational number X."""
    return x[1]


# Improved specification

from fractions import gcd
def rational(n, d):
    """A representation of the rational number N/D."""
    g = gcd(n, d)
    return [n//g, d//g]

def numer(x):
    """Return the numerator of rational number X in lowest terms and having
    the sign of X."""
    return x[0]

def denom(x):
    """Return the denominator of rational number X in lowest terms and positive."""
    return x[1]


# Functional implementation

def rational(n, d):
    """A representation of the rational number N/D."""
    g = gcd(n, d)
    n, d = n//g, d//g
    def select(name):
        if name == 'n':
            return n
        elif name == 'd':
            return d
    return select

def numer(x):
    """Return the numerator of rational number X in lowest terms and having
    the sign of X."""
    return x('n')

def denom(x):
    """Return the denominator of rational number X in lowest terms and positive."""
    return x('d')
```

Containers
```python
# Lists

odds = [41, 43, 47, 49]
len(odds)
odds[1]
odds[0] - odds[3] + len(odds)
odds[odds[3]-odds[2]]

# Containers

digits = [1, 8, 2, 8]
1 in digits
'1' in digits
[1, 8] in digits
[1, 2] in [[1, 2], 3]

# For statements


def count_while(s, value):
    """Count the number of occurrences of value in sequence s.

    >>> count_while(digits, 8)
    2
    """
    total, index = 0, 0
    while index < len(s):
        if s[index] == value:
            total = total + 1
        index = index + 1
    return total

def count_for(s, value):
    """Count the number of occurrences of value in sequence s.

    >>> count_for(digits, 8)
    2
    """
    total = 0
    for elem in s:
        if elem == value:
            total = total + 1
    return total


def count_same(pairs):
    """Return how many pairs have the same element repeated.

    >>> pairs = [[1, 2], [2, 2], [2, 3], [4, 4]]
    >>> count_same(pairs)
    2
    """
    same_count = 0
    for x, y in pairs:
        if x == y:
            same_count = same_count + 1
    return same_count


# Ranges

list(range(5, 8))
list(range(4))
len(range(4))

def sum_below(n):
    total = 0
    for i in range(n):
        total += i
    return total

def cheer():
    for _ in range(3):
        print('Go Bears!')


# List comprehensions

odds = [1, 3, 5, 7, 9]
[x+1 for x in odds]
[x for x in odds if 25 % x == 0]

def divisors(n):
    """Return the integers that evenly divide n.

    >>> divisors(4)
    [1, 2]
    >>> divisors(12)
    [1, 2, 3, 4, 6]
    >>> [n for n in range(1, 1000) if sum(divisors(n)) == n]
    [6, 28, 496]
    """
    return [x for x in range(1, n) if n % x == 0]

# Dicts

def dict_demos():
    numerals = {'I': 1, 'V': 5, 'X': 10}
    numerals['X']
    numerals.values()
    list(numerals.values())
    sum(numerals.values())
    dict([(3, 9), (4, 16), (5, 25)])
    numerals.get('X', 0)
    numerals.get('X-ray', 0)
    {x: x*x for x in range(3,6)}

    {1: 2, 1: 3}
    {[1]: 2}
    {1: [2]}
```

# Q1: Taxicab Distance
An intersection in midtown Manhattan can be identified by an avenue and a street, which are both indexed by positive integers. The Manhattan distance or taxicab distance between two intersections is the number of blocks that must be traversed to reach one from the other, ignoring one-way street restrictions and construction. For example, Times Square is on 46th Street and 7th Avenue. Ess-a-Bagel is on 51st Street and 3rd Avenue. The taxicab distance between them is 9 blocks (5 blocks from 46th to 51st street and 4 blocks from 7th avenue to 3rd avenue). Taxicabs cannot cut diagonally through buildings to reach their destination!

Implement taxicab, which computes the taxicab distance between two intersections using the following data abstraction. *Hint*: You don't need to know what a Cantor pairing function is; just use the abstraction.
```python
def intersection(st, ave):
    """Represent an intersection using the Cantor pairing function."""
    return (st+ave)*(st+ave+1)//2 + ave

def street(inter):
    return w(inter) - avenue(inter)

def avenue(inter):
    return inter - (w(inter) ** 2 + w(inter)) // 2

w = lambda z: int(((8*z+1)**0.5-1)/2)

def taxicab(a, b):
    """Return the taxicab distance between two intersections.

    >>> times_square = intersection(46, 7)
    >>> ess_a_bagel = intersection(51, 3)
    >>> taxicab(times_square, ess_a_bagel)
    9
    >>> taxicab(ess_a_bagel, times_square)
    9
    """
    "*** YOUR CODE HERE ***"
    return abs(street(a) - street(b)) + abs(avenue(a) - avenue(b))
```
# Q2:Squares only
Implement the function `squares`, which takes in a list of positive integers. It returns a list that contains the square roots of the elements of the original list that are perfect squares. Try using a list comprehension.

You may find the round function useful.
```python
>>> round(10.5)
10
>>> round(10.51)
11
```
```python
from math import sqrt
def squares(s):
    """Returns a new list containing square roots of the elements of the
    original list that are perfect squares.

    >>> seq = [8, 49, 8, 9, 2, 1, 100, 102]
    >>> squares(seq)
    [7, 3, 1, 10]
    >>> seq = [500, 30]
    >>> squares(seq)
    []
    """
    "*** YOUR CODE HERE ***"
    return [sqrt(x) for x in seq if x == round(sqrt(x)) ** 2]
    # return [round(x ** 0.5) for x in seq if x == round(x ** 0.5) ** 2]
```
It might be helpful to construct a skeleton list comprehension to begin with:
```python
[sqrt(x) for x in s if is_perfect_square(x)]
```
This is great, but it requires that we have an is_perfect_square function. How might we check if something is a perfect square?

If the square root of a number is a whole number, then it is a perfect square. For example, sqrt(61) = 7.81024... (not a perfect square) and sqrt(49) = 7 (perfect square).
Once we obtain the square root of the number, we just need to check if something is a whole number. The is_perfect_square function might look like:
```python
def is_perfect_square(x):
    return is_whole(sqrt(x))
```

One last piece of the puzzle: to check if a number is whole, we just need to see if it has a decimal or not. The way we've chosen to do it in the solution is to compare the original number to the round version (thus removing all decimals), but a technique employing floor division (//) or something else entirely could work too.
We've written all these helper functions to solve this problem, but they are actually all very short. Therefore, we can just copy the body of each into the original list comprehension, arriving at the solution we finally present.

Video walkthrough: https://youtu.be/YwLFB9paET0

We can also use `pow` function to calculate the power of some number. And `int(pow(n, 0.5)) == pow(n, 0.5)` can judge whether or nor n is perfect square number.

# Q3:G function
A mathematical function `G` on positive integers is defined by two cases:
```
G(n) = n,                                       if n <= 3
G(n) = G(n - 1) + 2 * G(n - 2) + 3 * G(n - 3),  if n > 3
```
Write a recursive function `g` that computes `G(n)`. Then, write an iterative function `g_iter` that also computes `G(n)`:
*Hint: The `fibonacci` example in the tree recursion lecture is a good illustration of the relationship between the recursive and iterative definitions of a tree recursive problem.*

```python
 def g(n):
    """Return the value of G(n), computed recursively.

    >>> g(1)
    1
    >>> g(2)
    2
    >>> g(3)
    3
    >>> g(4)
    10
    >>> g(5)
    22
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'g', ['While', 'For'])
    True
    """
    "*** YOUR CODE HERE ***"
    if n <= 3: # n in (1,2,3)
        return n
    else:
        return g(n - 1) + 2 * g(n - 2) + 3 * g(n - 3)
# My solution which is like the lecture code(fibnacci sequence iteration)
def g_iter(n):
    """Return the value of G(n), computed iteratively.

    >>> g_iter(1)
    1
    >>> g_iter(2)
    2
    >>> g_iter(3)
    3
    >>> g_iter(4)
    10
    >>> g_iter(5)
    22
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'g_iter', ['Recursion'])
    True
    """
    "*** YOUR CODE HERE ***"
    i = 1
    current, middle, next = 1, 2, 3
    while i < n:
        current, middle, next = middle, next, next + middle * 2 + current * 3
        i += 1
    return current
```
The PDF solution
```python
def g_iter2(n):
    if n == 1 or n == 2 or n == 3:
        return n
    a, b, c = 1, 2, 3
    while n > 3:
        a, b, c = b, c, c + 2*b + 3*a
        n = n - 1
    return c
```
This is an example of how a function might be easier to write recursively versus iteratively. Since we have defined the g function in terms of older versions of itself, the solution tends very naturally towards recursion.

The iterative solution is trickier, since we can only track a finite amount of state at a given time. We need to pick our variables carefully so that we have just the information we need to calculate the next step. In a sense, this problem is very similar to the Fibonacci sequence (assuming we start at the 0th Fibonacci number):
```python
f(n) = n,               if n <= 2
f(n) = f(n-1) + f(n-2), if n > 2
```
As you may recall, the solution for Fibonacci carried two variables around for the two previous values.
```python
def fib_iter(n):
    prev, curr = 0, 1
    while n > 0:
        prev, curr = curr, prev + curr
    return prev
```
Since the g function depends on the three previous values, it might make sense that we might have to track three values instead!

Consider the three previous values old, older, and oldest. To do an update, the older value ages and becomes oldest, the old value ages and becomes older. Finally, old gets the new value which is derived from the three previous values: old + 2 * older + 3 * oldest.

<span style="color:red">Video walkthrough: https://youtu.be/pltx7u2kGGE</span>

# Q4: Count change
Once the machines take over, the denomination of every coin will be a power of two: 1-cent, 2-cent, 4-cent, 8-cent, 16-cent, etc. There will be no limit to how much a coin can be worth.

Given a positive integer `amount`, a set of coins makes change for `amount` if the sum of the values of the coins is `amount`. For example, the following sets make change for `7`:

- 7 1-cent coins
- 5 1-cent, 1 2-cent coins
- 3 1-cent, 2 2-cent coins
- 3 1-cent, 1 4-cent coins
- 1 1-cent, 3 2-cent coins
- 1 1-cent, 1 2-cent, 1 4-cent coins
Thus, there are 6 ways to make change for `7`. Write a recursive function `count_change` that takes a positive integer `amount` and returns the number of ways to make change for `amount` using these coins of the future.

*Hint: Refer the implementation of `count_partitions` for an example of how to count the ways to sum up to an amount with smaller parts. If you need to keep track of more than one value across recursive calls, consider writing a helper function.*

<span style="color:red">My solution: First find the largest denomination in our change, which is implemented by a while loop, then we find that the program is so like the count_partitions program, we think about this problem as either having the largest denomination or not having it. Then we use tree recursion to calculate the result.</span>
```python
def count_change(amount):
    """Return the number of ways to make change for amount.

    >>> count_change(7)
    6
    >>> count_change(10)
    14
    >>> count_change(20)
    60
    >>> count_change(100)
    9828
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'count_change', ['While', 'For'])
    True
    """
    "*** YOUR CODE HERE ***"
    def lessThanN(n):
        i = 1
        while i <= n:
            i *= 2
        return i // 2
    def change(amount, m):
        if amount < 0:
            return 0
        elif m == 1 or amount == 0:
            return 1
        else:
            return change(amount - m, m) + change(amount, m // 2)
    return change(amount, lessthanN(amount))
```
<span style="color:red">PDF solution, I think PDF solution is cleverer than mine, it uses the smallest denomination, which needn't to implement a `lessThanN` function, but the intrinsic mind is the same.</span>

This is remarkably similar to the count_partitions problem, with a few minor differences:

- A maximum partition size m is not given, so we need to create a helper function that takes in two arguments and also create another helper function to find the max coin.
- Partition size is not linear, but rather multiples of two. To get the next partition you need to divide by two instead of subtracting one.
One other implementation detail here is that we enforce a maximum partition size, rather than a minimum coin. Many students attempted to start at 1 and work there way up. That will also work, but is less similar to count_partitions. As long as there is some ordering on the coins being enforced, we ensure we cover all the combinations of coins without any duplicates.

See the walkthrough for a more thorough explanation and a visual of the recursive calls. Video walkthrough: https://youtu.be/EgZJPNFnoxM.
```python
def count_change(amount):
    """Return the number of ways to make change for amount.

    >>> count_change(7)
    6
    >>> count_change(10)
    14
    >>> count_change(20)
    60
    >>> count_change(100)
    9828
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'count_change', ['While', 'For'])
    True
    """
    def constrained_count(amount, smallest_coin):
        if amount == 0:
            return 1
        if smallest_coin > amount:
            return 0
        without_coin = constrained_count(amount, smallest_coin * 2)
        with_coin = constrained_count(amount - smallest_coin, smallest_coin)
        return without_coin + with_coin
    return constrained_count(amount, 1)
```

# Q5: Towers of Hanoi
A classic puzzle called the Towers of Hanoi is a game that consists of three rods, and a number of disks of different sizes which can slide onto any rod. The puzzle starts with `n` disks in a neat stack in ascending order of size on a `start` rod, the smallest at the top, forming a conical shape.

{% asset_img hanoi.png %}

The objective of the puzzle is to move the entire stack to an `end` rod, obeying the following rules:

- Only one disk may be moved at a time.
- Each move consists of taking the top (smallest) disk from one of the rods and sliding it onto another rod, on top of the other disks that may already be present on that rod.
- No disk may be placed on top of a smaller disk.
Complete the definition of `move_stack`, which prints out the steps required to move `n` disks from the `start` rod to the `end` rod without violating the rules. The provided `print_move` function will print out the step to move a single disk from the given `origin` to the given `destination`.

*Hint: Draw out a few games with various `n` on a piece of paper and try to find a pattern of disk movements that applies to any `n`. In your solution, take the recursive leap of faith whenever you need to move any amount of disks less than `n` from one rod to another.*

<span style="color:red">My solution: We can simply think this question as recursion. The base case is when n is 1, then we only need to move the disk from start to end, otherwise we first need to move n - 1 disks from start to median, which is calculated by 6 - start - end. Then we need to move the largest disk to the end rod. Last we need to move the median n - 1 disks to end rod.</span>
```python
def print_move(origin, destination):
    """Print instructions to move a disk."""
    print("Move the top disk from rod", origin, "to rod", destination)

def move_stack(n, start, end):
    """Print the moves required to move n disks on the start pole to the end
    pole without violating the rules of Towers of Hanoi.

    n -- number of disks
    start -- a pole position, either 1, 2, or 3
    end -- a pole position, either 1, 2, or 3

    There are exactly three poles, and start and end must be different. Assume
    that the start pole has at least n disks of increasing size, and the end
    pole is either empty or has a top disk larger than the top n start disks.

    >>> move_stack(1, 1, 3)
    Move the top disk from rod 1 to rod 3
    >>> move_stack(2, 1, 3)
    Move the top disk from rod 1 to rod 2
    Move the top disk from rod 1 to rod 3
    Move the top disk from rod 2 to rod 3
    >>> move_stack(3, 1, 3)
    Move the top disk from rod 1 to rod 3
    Move the top disk from rod 1 to rod 2
    Move the top disk from rod 3 to rod 2
    Move the top disk from rod 1 to rod 3
    Move the top disk from rod 2 to rod 1
    Move the top disk from rod 2 to rod 3
    Move the top disk from rod 1 to rod 3
    """
    assert 1 <= start <= 3 and 1 <= end <= 3 and start != end, "Bad start/end"
    "*** YOUR CODE HERE ***"
    if n == 1:
        print_move(start, end)
        return
    median = 6 - start - end
    move_stack(n - 1, start, median)
    move_stack(1, start, end)
    move_stack(n - 1, median, end)
```
<span style="color: red">The pdf solution:</span>
To solve the Towers of Hanoi problem for n disks, we need to do three steps:

Move everything but the last disk (n-1 disks) to someplace in the middle (not the start nor the end rod).
Move the last disk (a single disk) to the end rod. This must occur after step 1 (we have to move everything above it away first)!
Move everything but the last disk (the disks from step 1) from the middle on top of the end rod.
We take advantage of the fact that the recursive function move_stack is guaranteed to move n disks from start to end while obeying the rules of Towers of Hanoi. The only thing that remains is to make sure that we have set up the playing board to make that possible.

Since we move a disk to end rod, we run the risk of move_stack doing an improper move (big disk on top of small disk). But since we're moving the biggest disk possible, nothing in the n-1 disks above that is bigger. Therefore, even though we do not explicitly state the Towers of Hanoi constraints, we can still carry out the correct steps.

Video walkthrough: https://youtu.be/VwynGQiCTFM</span>

```python
def print_move(origin, destination):
    """Print instructions to move a disk."""
    print("Move the top disk from rod", origin, "to rod", destination)

def move_stack(n, start, end):
    """Print the moves required to move n disks on the start pole to the end
    pole without violating the rules of Towers of Hanoi.

    n -- number of disks
    start -- a pole position, either 1, 2, or 3
    end -- a pole position, either 1, 2, or 3

    There are exactly three poles, and start and end must be different. Assume
    that the start pole has at least n disks of increasing size, and the end
    pole is either empty or has a top disk larger than the top n start disks.

    >>> move_stack(1, 1, 3)
    Move the top disk from rod 1 to rod 3
    >>> move_stack(2, 1, 3)
    Move the top disk from rod 1 to rod 2
    Move the top disk from rod 1 to rod 3
    Move the top disk from rod 2 to rod 3
    >>> move_stack(3, 1, 3)
    Move the top disk from rod 1 to rod 3
    Move the top disk from rod 1 to rod 2
    Move the top disk from rod 3 to rod 2
    Move the top disk from rod 1 to rod 3
    Move the top disk from rod 2 to rod 1
    Move the top disk from rod 2 to rod 3
    Move the top disk from rod 1 to rod 3
    """
    assert 1 <= start <= 3 and 1 <= end <= 3 and start != end, "Bad start/end"
    if n == 1:
        print_move(start, end)
    else:
        other = 6 - start - end
        move_stack(n-1, start, other)
        print_move(start, end)
        move_stack(n-1, other, end)
```

# Q6: Anonymous factorial
The recursive factorial function can be written as a single expression by using a conditional expression.
```
>>> fact = lambda n: 1 if n == 1 else mul(n, fact(sub(n, 1)))
>>> fact(5)
120
```
However, this implementation relies on the fact (no pun intended) that `fact` has a name, to which we refer in the body of `fact`. To write a recursive function, we have always given it a name using a `def` or assignment statement so that we can refer to the function within its own body. In this question, your job is to define fact recursively without giving it a name!

Write an expression that computes `n` factorial using only call expressions, conditional expressions, and lambda expressions (no assignment or def statements). Note in particular that you are not allowed to use `make_anonymous_factorial` in your return expression. The `sub` and `mul` functions from the `operator` module are the only built-in functions required to solve this problem:
```python
from operator import sub, mul

def make_anonymous_factorial():
    """Return the value of an expression that computes factorial.

    >>> make_anonymous_factorial()(5)
    120
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'make_anonymous_factorial', ['Assign', 'AugAssign', 'FunctionDef', 'Recursion'])
    True
    """
    return (lambda f: lambda k: f(f, k))(lambda f, k: k if k == 1 else mul(k, f(f, sub(k, 1))))
    # Alternate solution:
    #   return (lambda f: f(f))(lambda f: lambda x: 1 if x == 0 else x * f(f)(x - 1))
```
