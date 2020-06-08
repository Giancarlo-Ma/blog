---
title: cs61a-hw05
date: 2018-10-16 17:55:04
categories:
- CS61A
tags:
- python
- SICP
---

# Trees
## Q1: Replace Leaf
Define `replace_leaf`, which takes a tree `t`, a value `old`, and a value `new`. `replace_leaf` returns a new tree that's the same as `t` except that every leaf value equal to `old` has been replaced with `new`.
<!-- more -->
```python
 def replace_leaf(t, old, new):
    """Returns a new tree where every leaf value equal to old has
    been replaced with new.

    >>> yggdrasil = tree('odin',
    ...                  [tree('balder',
    ...                        [tree('thor'),
    ...                         tree('loki')]),
    ...                   tree('frigg',
    ...                        [tree('thor')]),
    ...                   tree('thor',
    ...                        [tree('sif'),
    ...                         tree('thor')]),
    ...                   tree('thor')])
    >>> laerad = copy_tree(yggdrasil) # copy yggdrasil for testing purposes
    >>> print_tree(replace_leaf(yggdrasil, 'thor', 'freya'))
    odin
      balder
        freya
        loki
      frigg
        freya
      thor
        sif
        freya
      freya
    >>> laerad == yggdrasil # Make sure original tree is unmodified
    True
    """
    "*** YOUR CODE HERE ***"
    if is_leaf(t) and label(t) == old:
        return tree(new)
    return tree(label(t), [replace_leaf(b, old, new) for b in branches(t)])
```

# Mobiles
**Acknowledgements**. This mobile example is based on a classic problem from Structure and Interpretation of Computer Programs, Section 2.2.2.

A mobile is a type of hanging sculpture. A binary mobile consists of two sides. Each side is a rod of a certain length, from which hangs either a weight or another mobile.

We will represent a binary mobile using the data abstractions below.

- A `mobile` has a left `side` and a right `side`.
- A `side` has a positive length and something hanging at the end, either a `mobile` or `weight`.
- A `weight` has a positive size.
## Q2: Weights
Implement the `weight` data abstraction by completing the `weight` constructor and the `size` selector so that a weight is represented using a two-element list where the first element is the string `'weight'`. The `total_weight` example is provided to demonstrate use of the mobile, side, and weight abstractions.

```python
def mobile(left, right):
    """Construct a mobile from a left side and a right side."""
    assert is_side(left), "left must be a side"
    assert is_side(right), "right must be a side"
    return ['mobile', left, right]

def is_mobile(m):
    """Return whether m is a mobile."""
    return type(m) == list and len(m) == 3 and m[0] == 'mobile'

def left(m):
    """Select the left side of a mobile."""
    assert is_mobile(m), "must call left on a mobile"
    return m[1]

def right(m):
    """Select the right side of a mobile."""
    assert is_mobile(m), "must call right on a mobile"
    return m[2]
def side(length, mobile_or_weight):
    """Construct a side: a length of rod with a mobile or weight at the end."""
    assert is_mobile(mobile_or_weight) or is_weight(mobile_or_weight)
    return ['side', length, mobile_or_weight]

def is_side(s):
    """Return whether s is a side."""
    return type(s) == list and len(s) == 3 and s[0] == 'side'

def length(s):
    """Select the length of a side."""
    assert is_side(s), "must call length on a side"
    return s[1]

def end(s):
    """Select the mobile or weight hanging at the end of a side."""
    assert is_side(s), "must call end on a side"
    return s[2]
def weight(size):
    """Construct a weight of some size."""
    assert size > 0
    "*** YOUR CODE HERE ***"
    return ['weight', size]

def size(w):
    """Select the size of a weight."""
    assert is_weight(w), 'must call size on a weight'
    "*** YOUR CODE HERE ***"
    return w[1]

def is_weight(w):
    """Whether w is a weight."""
    return type(w) == list and len(w) == 2 and w[0] == 'weight'
```

## Q3: Balanced
Implement the `balanced` function, which returns whether `m` is a balanced mobile. A mobile is balanced if two conditions are met:

1. The torque(转矩) applied by its left side is equal to that applied by its right side. Torque of the left side is the length of the left rod multiplied by the total weight hanging from that rod. Likewise for the right.
2. Each of the mobiles hanging at the end of its sides is balanced.
Hint: You may find it helpful to assume that weights themselves are balanced.

```python
def balanced(m):
    """Return whether m is balanced.

    >>> t, u, v = examples()
    >>> balanced(t)
    True
    >>> balanced(v)
    True
    >>> w = mobile(side(3, t), side(2, u))
    >>> balanced(w)
    False
    >>> balanced(mobile(side(1, v), side(1, w)))
    False
    >>> balanced(mobile(side(1, w), side(1, v)))
    False
    """
    "*** YOUR CODE HERE ***"
    if is_weight(m):
        return True
    else:
        left_end, right_end = end(left(m)), end(right(m))
        torque_left = length(left(m)) * total_weight(left_end)
        torque_right = length(right(m)) * total_weight(right_end)
        return torque_left == torque_right and balanced(left_end) and balanced(right_end)
```

<span style="color:red">The balanced weights assumption is important, since we will be solving this recursively like many other tree problems (even though this is not explicitly a tree).</span>

<span style="color:red">**Base case**: if we are checking a weight, then we know that this is balanced. Why is this an appropriate base case? There are two possible approaches to this:</span>

<span style="color:red">1. Because we know that our data structures so far are trees, weights are the simplest possible tree since we have chosen to implement them as leaves.</span>
<span style="color:red">2. We also know that from an ADT standpoint, weights are the terminal item in a mobile. There can be no further mobile structures under this weight, so it makes sense to stop check here.</span>
<span style="color:red">**Otherwise**: note that it is important to do a recursive call to check if both sides are balanced. However, we also need to do the basic comparison of looking at the total weight of both sides as well as their length. For example if both sides are a weight, trivially, they will both be balanced. However, the torque must be equal in order for the entire mobile to balanced (i.e. it's insufficient to just check if the sides are balanced).</span>

## Q4: Totals
Implement `totals_tree`, which takes a `mobile` (or `weight`) and returns a `tree` whose root is its total weight and whose branches are trees for the ends of the sides.
<span style="color:red">My solution</span>
```python
def totals_tree(m):
    """Return a tree representing the mobile with its total weight at the root.

    >>> t, u, v = examples()
    >>> print_tree(totals_tree(t))
    3
      2
      1
    >>> print_tree(totals_tree(u))
    6
      1
      5
        3
        2
    >>> print_tree(totals_tree(v))
    9
      3
        2
        1
      6
        1
        5
          3
          2
    """
    "*** YOUR CODE HERE ***"
    if is_weight(m):
        return tree(total_weight(m))
    else:
        return tree(total_weight(m), [totals_tree(end(left(m))), totals_tree(end(right(m)))])
```
<span style="color:red">The PDF solution, which I think is so intellectual!!</span>
```python
def totals_tree(m):
    """Return a tree representing the mobile with its total weight at the root.

    >>> t, u, v = examples()
    >>> print_tree(totals_tree(t))
    3
      2
      1
    >>> print_tree(totals_tree(u))
    6
      1
      5
        3
        2
    >>> print_tree(totals_tree(v))
    9
      3
        2
        1
      6
        1
        5
          3
          2
    """
    if is_weight(m):
        return tree(size(m))
    else:
        branches = [totals_tree(end(f(m))) for f in [left, right]]
        return tree(sum([label(b) for b in branches]), branches)
```
# Mutable functions
## Q5: Counter
Define a function `make_counter` that returns a `counter` function, which takes a string and returns the number of times that the function has been called on that string.
<span style="color:red">My solution(a little stupid, not general)</span>
```python
def make_counter():
    """Return a counter function.

    >>> c = make_counter()
    >>> c('a')
    1
    >>> c('a')
    2
    >>> c('b')
    1
    >>> c('a')
    3
    >>> c2 = make_counter()
    >>> c2('b')
    1
    >>> c2('b')
    2
    >>> c('b') + c2('b')
    5
    """
    "*** YOUR CODE HERE ***"
    m = {}
    def f(s):
        if m.get(s, 0) == 0:
            m[s] = 1
        else:
            m[s] += 1
        return m[s]
    return f
```
<span style="color:red">The PDF solution(smart and beautiful, general)</span>
```python
def make_counter():
    """Return a counter function.

    >>> c = make_counter()
    >>> c('a')
    1
    >>> c('a')
    2
    >>> c('b')
    1
    >>> c('a')
    3
    >>> c2 = make_counter()
    >>> c2('b')
    1
    >>> c2('b')
    2
    >>> c('b') + c2('b')
    5
    """
    "*** YOUR CODE HERE ***"
    m = {}
    def counter(key):
        m[key] = m.get(key, 0) + 1
        return m[key]
    return counter
```
```python
def make_counter():
    """Return a counter function.

    >>> c = make_counter()
    >>> c('a')
    1
    >>> c('a')
    2
    >>> c('b')
    1
    >>> c('a')
    3
    >>> c2 = make_counter()
    >>> c2('b')
    1
    >>> c2('b')
    2
    >>> c('b') + c2('b')
    5
    """
    "*** YOUR CODE HERE ***"
    m = {}
    def counter(s):
        if not s in m:
            m[s] = 0
        m[s] += 1
        return m[s]
    return counter
```

## Q6: Next Fibonacci
Write a function `make_fib` that returns a function that returns the next Fibonacci number each time it is called. (The Fibonacci sequence begins with 0 and then 1, after which each element is the sum of the preceding two.) Use a `nonlocal` statement!
```python
def make_fib():
    """Returns a function that returns the next Fibonacci number
    every time it is called.

    >>> fib = make_fib()
    >>> fib()
    0
    >>> fib()
    1
    >>> fib()
    1
    >>> fib()
    2
    >>> fib()
    3
    >>> fib2 = make_fib()
    >>> fib() + sum([fib2() for _ in range(5)])
    12
    """
    "*** YOUR CODE HERE ***"
    current, next = 0, 1
    def fib():
        nonlocal current, next
        result = current
        current, next = next, current + next
        return result
    return fib
```
## Q7: Password Protected Account
In lecture, we saw how to use functions to create mutable objects. Here, for example, is the function `make_withdraw` which produces a function that can withdraw money from an account:
```python
def make_withdraw(balance):
    """Return a withdraw function with BALANCE as its starting balance.
    >>> withdraw = make_withdraw(1000)
    >>> withdraw(100)
    900
    >>> withdraw(100)
    800
    >>> withdraw(900)
    'Insufficient funds'
    """
    def withdraw(amount):
        nonlocal balance
        if amount > balance:
           return 'Insufficient funds'
        balance = balance - amount
        return balance
    return withdraw
```
Write a version of the `make_withdraw` function that returns password-protected withdraw functions. That is, `make_withdraw` should take a password argument (a string) in addition to an initial balance. The returned function should take two arguments: an amount to withdraw and a password.

A password-protected `withdraw` function should only process withdrawals that include a password that matches the original. Upon receiving an incorrect password, the function should:

1. Store that incorrect password in a list, and
2. Return the string 'Incorrect password'.
If a withdraw function has been called three times with incorrect passwords `p1`, `p2`, and `p3`, then it is locked. All subsequent calls to the function should return:
```
"Your account is locked. Attempts: [<p1>, <p2>, <p3>]"
```
The incorrect passwords may be the same or different:
<span style="color:red">My solution<span>
```python
 def make_withdraw(balance, password):
    """Return a password-protected withdraw function.

    >>> w = make_withdraw(100, 'hax0r')
    >>> w(25, 'hax0r')
    75
    >>> error = w(90, 'hax0r')
    >>> error
    'Insufficient funds'
    >>> error = w(25, 'hwat')
    >>> error
    'Incorrect password'
    >>> new_bal = w(25, 'hax0r')
    >>> new_bal
    50
    >>> w(75, 'a')
    'Incorrect password'
    >>> w(10, 'hax0r')
    40
    >>> w(20, 'n00b')
    'Incorrect password'
    >>> w(10, 'hax0r')
    "Your account is locked. Attempts: ['hwat', 'a', 'n00b']"
    >>> w(10, 'l33t')
    "Your account is locked. Attempts: ['hwat', 'a', 'n00b']"
    >>> type(w(10, 'l33t')) == str
    True
    """
    "*** YOUR CODE HERE ***"
    l = []
    num = 0
    def withdraw(amount, p):
        nonlocal num, balance
        if num == 3:
            return 'Your account is locked. Attempts: ' + str(l)
        if p != password:
            l.append(p)
            num += 1
            return 'Incorrect password'
        if amount > balance:
            return 'Insufficient funds'
        balance -= amount
        return balance
    return withdraw
```
<span style="color:red">The PDF solution, which have not a num variable.</span>
```python
def make_withdraw(balance, password):
    """Return a password-protected withdraw function.

    >>> w = make_withdraw(100, 'hax0r')
    >>> w(25, 'hax0r')
    75
    >>> error = w(90, 'hax0r')
    >>> error
    'Insufficient funds'
    >>> error = w(25, 'hwat')
    >>> error
    'Incorrect password'
    >>> new_bal = w(25, 'hax0r')
    >>> new_bal
    50
    >>> w(75, 'a')
    'Incorrect password'
    >>> w(10, 'hax0r')
    40
    >>> w(20, 'n00b')
    'Incorrect password'
    >>> w(10, 'hax0r')
    "Your account is locked. Attempts: ['hwat', 'a', 'n00b']"
    >>> w(10, 'l33t')
    "Your account is locked. Attempts: ['hwat', 'a', 'n00b']"
    >>> type(w(10, 'l33t')) == str
    True
    """
    attempts = []
    def withdraw(amount, password_attempt):
        nonlocal balance
        if len(attempts) == 3:
            return 'Your account is locked. Attempts: ' + str(attempts)
        if password_attempt != password:
            attempts.append(password_attempt)
            return 'Incorrect password'
        if amount > balance:
            return 'Insufficient funds'
        balance = balance - amount
        return balance
    return withdraw
```
## Q8: Joint Account
Suppose that our banking system requires the ability to make joint accounts. Define a function `make_joint` that takes three arguments.

1. A password-protected `withdraw` function,
2. The password with which that `withdraw` function was defined, and
3. A new password that can also access the original account.
The `make_joint` function returns a `withdraw` function that provides additional access to the original account using either the new or old password. Both functions draw from the same balance. Incorrect passwords provided to either function will be stored and cause the functions to be locked after three wrong attempts.

Hint: The solution is short (less than 10 lines) and contains no string literals! The key is to call `withdraw` with the right password and amount, then interpret the result. You may assume that all failed attempts to withdraw will return some string (for incorrect passwords, locked accounts, or insufficient funds), while successful withdrawals will return a number.

Use `type(value) == str` to test if some `value` is a string:
```python
def make_joint(withdraw, old_password, new_password):
    """Return a password-protected withdraw function that has joint access to
    the balance of withdraw.

    >>> w = make_withdraw(100, 'hax0r')
    >>> w(25, 'hax0r')
    75
    >>> make_joint(w, 'my', 'secret')
    'Incorrect password'
    >>> j = make_joint(w, 'hax0r', 'secret')
    >>> w(25, 'secret')
    'Incorrect password'
    >>> j(25, 'secret')
    50
    >>> j(25, 'hax0r')
    25
    >>> j(100, 'secret')
    'Insufficient funds'

    >>> j2 = make_joint(j, 'secret', 'code')
    >>> j2(5, 'code')
    20
    >>> j2(5, 'secret')
    15
    >>> j2(5, 'hax0r')
    10

    >>> j2(25, 'password')
    'Incorrect password'
    >>> j2(5, 'secret')
    "Your account is locked. Attempts: ['my', 'secret', 'password']"
    >>> j(5, 'secret')
    "Your account is locked. Attempts: ['my', 'secret', 'password']"
    >>> w(5, 'hax0r')
    "Your account is locked. Attempts: ['my', 'secret', 'password']"
    >>> make_joint(w, 'hax0r', 'hello')
    "Your account is locked. Attempts: ['my', 'secret', 'password']"
    """
    "*** YOUR CODE HERE ***"
    error = withdraw(0, old_password)
    if type(error) == str:
         return error
    def joint(amount, attempt):
        if attempt == new_password:
            return withdraw(amount, old_password)
        else:
            return withdraw(amount, attempt)
    return joint
```
# Generators
## Q9: Generate Paths
Define a generator function `generate_paths` which takes in a tree `t`, a value `x`, and yields each path from the root of `t` to a node that has label `x`. Each path should be represented as a list of the labels along that path in the tree. You may yield the paths in any order.
```python
def generate_paths(t, x):
    """Yields all possible paths from the root of t to a node with the label x as a list.

    >>> t1 = tree(1, [tree(2, [tree(3), tree(4, [tree(6)]), tree(5)]), tree(5)])
    >>> print_tree(t1)
    1
      2
        3
        4
          6
        5
      5
    >>> next(generate_paths(t1, 6))
    [1, 2, 4, 6]
    >>> path_to_5 = generate_paths(t1, 5)
    >>> sorted(list(path_to_5))
    [[1, 2, 5], [1, 5]]

    >>> t2 = tree(0, [tree(2, [t1])])
    >>> print_tree(t2)
    0
      2
        1
          2
            3
            4
              6
            5
          5
    >>> path_to_2 = generate_paths(t2, 2)
    >>> sorted(list(path_to_2))
    [[0, 2], [0, 2, 1, 2]]
    """
    "*** YOUR CODE HERE ***"
    if label(t) == x:
        yield [x]
    for b in branches(t):
        for path in generate_paths(b, x):
            yield [label(t)] + path
```
<span style="color:red">If our current label is equal to x, we've found a path from the root to a node containing x containing only our current label, so we should yield that. From there, we'll see if there are any paths starting from one of our branches that ends at a node containing x. If we find these "partial paths" we can simply add our current label to the beinning of a path to obtain a path starting from the root.</span>

<span style="color:red">In order to do this, we'll create a generator for each of the branches which yields these "partial paths". By calling generate_paths on each of the branches, we'll create exactly this generator! Then, since a generator is also an iterable, we can iterate over the paths in this generator and yield the result of concatenating it with our current label.</span>

# Interval
**Acknowledgements**. This interval arithmetic example is based on a classic problem from Structure and Interpretation of Computer Programs, Section 2.1.4.

**Introduction**. Alyssa P. Hacker is designing a system to help people solve engineering problems. One feature she wants to provide in her system is the ability to manipulate inexact quantities (such as measured parameters of physical devices) with known precision, so that when computations are done with such approximate quantities the results will be numbers of known precision.

Alyssa's idea is to implement interval arithmetic as a set of arithmetic operations for combining "intervals" (objects that represent the range of possible values of an inexact quantity). The result of adding, subracting, multiplying, or dividing two intervals is itself an interval, representing the range of the result.

Alyssa postulates the existence of an abstract object called an "interval" that has two endpoints: a lower bound and an upper bound. She also presumes that, given the endpoints of an interval, she can construct the interval using the data constructor interval. Using the constructor and selectors, she defines the following operations:
```python
def str_interval(x):
    """Return a string representation of interval x."""
    return '{0} to {1}'.format(lower_bound(x), upper_bound(x))

def add_interval(x, y):
    """Return an interval that contains the sum of any value in interval x and
    any value in interval y."""
    lower = lower_bound(x) + lower_bound(y)
    upper = upper_bound(x) + upper_bound(y)
    return interval(lower, upper)
```
## Q10: Interval Abstraction
Alyssa's program is incomplete because she has not specified the implementation of the interval abstraction. She has implemented the constructor for you; fill in the implementation of the selectors.
```python
def interval(a, b):
    """Construct an interval from a to b."""
    assert a <= b, 'Lower bound cannot be greater than upper bound'
    return [a, b]

def lower_bound(x):
    """Return the lower bound of interval x."""
    "*** YOUR CODE HERE ***"
    return x[0]

def upper_bound(x):
    """Return the upper bound of interval x."""
    "*** YOUR CODE HERE ***"
    return x[1]
```
Louis Reasoner has also provided an implementation of interval multiplication. Beware: there are some data abstraction violations, so help him fix his code before someone sets it on fire.
```python
def mul_interval(x, y):
    """Return the interval that contains the product of any value in x and any
    value in y."""
    p1 = x[0] * y[0]
    p2 = x[0] * y[1]
    p3 = x[1] * y[0]
    p4 = x[1] * y[1]
    return [min(p1, p2, p3, p4), max(p1, p2, p3, p4)]
```
The modification
```python
def mul_interval(x, y):
    """Return the interval that contains the product of any value in x and any value in y."""
    p1 = lower_bound(x) * lower_bound(y)
    p2 = lower_bound(x) * upper_bound(y)
    p3 = higher_bound(x) * lower_bound(y)
    p4 = higher_bound(x) * upper_bound(y)
    return [min(p1, p2, p3, p4), max(p1, p2, p3, p4)]
```
## Q11: Sub Interval
Using reasoning analogous to Alyssa's, define a subtraction function for intervals. Try to reuse functions that have already been implemented if you find yourself repeating code.
```python
def sub_interval(x, y):
    """Return the interval that contains the difference between any value in x and any value in y."""
    "*** YOUR CODE HERE ***"
    negative_y = interval(-upper_bound(y), -lower_bound(y))
    return add_interval(x, negative_y)
```
## Q12: Div Interval
Alyssa implements division below by multiplying by the reciprocal of `y`. Ben Bitdiddle, an expert systems programmer, looks over Alyssa's shoulder and comments that it is not clear what it means to divide by an interval that spans zero. Add an `assert` statement to Alyssa's code to ensure that no such interval is used as a divisor:
```python
def div_interval(x, y):
    """Return the interval that contains the quotient of any value in x divided by
    any value in y. Division is implemented as the multiplication of x by the
    reciprocal of y."""
    "*** YOUR CODE HERE ***"
    assert lower_bound(y) > 0 or upper_bound(y) < 0, 'The divisor cannot contain 0'
    reciprocal_y = interval(1/upper_bound(y), 1/lower_bound(y))
    return mul_interval(x, reciprocal_y)
```
## Q13: Par Diff
After considerable work, Alyssa P. Hacker delivers her finished system. Several years later, after she has forgotten all about it, she gets a frenzied call from an irate user, Lem E. Tweakit. It seems that Lem has noticed that the formula for parallel resistors can be written in two algebraically equivalent ways:
```
par1(r1, r2) = (r1 * r2) / (r1 + r2)
```
or
```
par2(r1, r2) = 1 / (1/r1 + 1/r2)
```
He has written the following two programs, each of which computes the `parallel_resistors` formula differently::
```python
def par1(r1, r2):
    return div_interval(mul_interval(r1, r2), add_interval(r1, r2))

def par2(r1, r2):
    one = interval(1, 1)
    rep_r1 = div_interval(one, r1)
    rep_r2 = div_interval(one, r2)
    return div_interval(one, add_interval(rep_r1, rep_r2))
```
Lem complains that Alyssa's program gives different answers for the two ways of computing. This is a serious complaint.

Demonstrate that Lem is right. Investigate the behavior of the system on a variety of arithmetic expressions. Make some intervals `r1` and `r2`, and show that `par1` and `par2` can give different results.
```python
def check_par():
    """Return two intervals that give different results for parallel resistors.

    >>> r1, r2 = check_par()
    >>> x = par1(r1, r2)
    >>> y = par2(r1, r2)
    >>> lower_bound(x) != lower_bound(y) or upper_bound(x) != upper_bound(y)
    True
    """
    r1 = interval(1, 1) # Replace this line!
    r2 = interval(1, 1) # Replace this line!
    return r1, r2
```
```python
def check_par():
    """Return two intervals that give different results for parallel resistors.

    >>> r1, r2 = check_par()
    >>> x = par1(r1, r2)
    >>> y = par2(r1, r2)
    >>> lower_bound(x) != lower_bound(y) or upper_bound(x) != upper_bound(y)
    True
    """
    r1 = interval(1, 2)
    r2 = interval(3, 4)
    return r1, r2
```
Video walkthrough: https://youtu.be/H8slb5KCbU4

## Q14: Multiple References
Eva Lu Ator, another user, has also noticed the different intervals computed by different but algebraically equivalent expressions. She says that the problem is multiple references to the same interval.

The Multiple References Problem: a formula to compute with intervals using Alyssa's system will produce tighter error bounds if it can be written in such a form that no variable that represents an uncertain number is repeated.

Thus, she says, par2 is a better program for parallel resistances than par1. Is she right? Why? Write a function that returns a string containing a written explanation of your answer:
```python
def multiple_references_explanation():
    return """The multiple reference problem exists.  The true value
    within a particular interval is fixed (though unknown).  Nested
    combinations that refer to the same interval twice may assume two different
    true values for the same interval, which is an error that results in
    intervals that are larger than they should be.

    Consider the case of i * i, where i is an interval from -1 to 1.  No value
    within this interval, when squared, will give a negative result.  However,
    our mul_interval function will allow us to choose 1 from the first
    reference to i and -1 from the second, giving an erroneous lower bound of
    -1.

    Hence, a program like par2 is better than par1 because it never combines
    the same interval more than once.
    """
```
Video walkthrough: https://youtu.be/H8slb5KCbU4

## Q15: Quadratic
Write a function `quadratic` that returns the interval of all values f(t) such that t is in the argument interval x and f(t) is a quadratic function:
```
f(t) = a*t*t + b*t + c
```
Make sure that your implementation returns the smallest such interval, one that does not suffer from the multiple references problem.

Hint: the derivative `f'(t) = 2*a*t + b`, and so the extreme point of the quadratic is `-b/(2*a)`:
```python
def quadratic(x, a, b, c):
    """Return the interval that is the range of the quadratic defined by
    coefficients a, b, and c, for domain interval x.

    >>> str_interval(quadratic(interval(0, 2), -2, 3, -1))
    '-3 to 0.125'
    >>> str_interval(quadratic(interval(1, 3), 2, -3, 1))
    '0 to 10'
    """
    extremum = -b / (2*a)
    f = lambda x: a * x * x + b * x + c
    l, u, e = map(f, (lower_bound(x), upper_bound(x), extremum))
    if extremum >= lower_bound(x) and extremum <= upper_bound(x):
        return interval(min(l, u, e), max(l, u, e))
    else:
        return interval(min(l, u), max(l, u))
```
Video walkthrough: https://youtu.be/qgSn_RNBs4A
