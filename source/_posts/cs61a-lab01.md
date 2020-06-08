---
title: cs61a-lab01
date: 2018-08-24 22:05:10
categories:
- CS61A
tags:
- python
- SICP
---

# Q4: Sum Digits
Write a function that takes in a nonnegative integer and sums its digits. (Using floor division and modulo might be helpful here!)
<!-- more -->
```python
def sum_digits(n):
    """Sum all the digits of n.

    >>> sum_digits(10) # 1 + 0 = 1
    1
    >>> sum_digits(4224) # 4 + 2 + 2 + 4 = 12
    12
    >>> sum_digits(1234567890)
    45
    """
    sum = 0
    while n > 0:
        # n, sum = n // 10, sum + n % 10
        n, d = n // 10, n % 10
        sum += d
    return sum
```
# Q6: Falling Factorial
Let's write a function falling, which is a "falling" factorial that takes two arguments, n and k, and returns the product of k consecutive numbers, starting from n and working downwards.

```python
def falling(n, k):
    """Compute the falling factorial of n to depth k.

    >>> falling(6, 3)  # 6 * 5 * 4
    120
    >>> falling(4, 0)
    1
    >>> falling(4, 3)  # 4 * 3 * 2
    24
    >>> falling(4, 1)  # 4
    4
    """
    fac = 1
    while k > 0:
        fac, k = n * fac, k - 1
        n -= 1
    return fac
```
# Q7: Double Eights
Write a function that takes in a number and determines if the digits contain two adjacent 8s.

```python
def double_eights(n):
    """Return true if n has two eights in a row.
    >>> double_eights(8)
    False
    >>> double_eights(88)
    True
    >>> double_eights(2882)
    True
    >>> double_eights(880088)
    True
    >>> double_eights(12345)
    False
    >>> double_eights(80808080)
    False
    """
    flag = 0
    while n > 0:
        n, d = n // 10, n % 10
        if d == 8 and flag == 1:
            return True
        elif d == 8:
            flag = 1
        else:
            flag = 0
    return False
```

# I Want to Play a Game
Now that you have learned about call expressions and control structures, you can code an algorithm! An algorithm is a set of steps to accomplish a task. You use algorithms every day -- from adding numbers by hand to getting to your next lecture.

Let's play a number guessing game with Python! Pick a number and Python will guess randomly until it guesses correctly.

All the code for this guessing game will be in lab01_extra.py. In your terminal, start an interactive session with Python:

```
python3 -i lab01_extra.py
```
The guess_random function will prompt you for a number, ask if its guess is correct (many times) and return the number of guesses Python had to make. To tell Python if its guess is correct, just enter y at the [y/n] prompt. If it's wrong, enter n. Python isn't very good at guessing yet, so if it's taking too long, you can type Ctrl-C to make it stop.
```
>>> guess_random()
Pick an integer between 1 and 10 (inclusive) for me to guess: 7
Is 1 your number? [y/n] n
Is 5 your number? [y/n] n
...
```
Randomly guessing works, but you can create an even better guessing strategy.

## Q8: Guess Linear
One weakness in the guess_random strategy is that it can repeat (incorrect) guesses. Rather than guessing wildly, let's guess numbers in increasing order.

Note: is_correct is a function that will ask the user if the guess is correct and return True if the user confirms that the guess matches the correct number. Feel free to reference the implementation of guess_random as you implement guess_linear.

```python
def guess_linear():
    """Guess in increasing order and return the number of guesses."""
    prompt_for_number(LOWER, UPPER)
    num_guesses = 1
    guess = LOWER
    while not is_correct(guess):
        guess, num_guesses = guess + 1, num_guesses + 1
    return num_guesses
```

## Q9: Guess Binary
Challenge question. The guess_linear function can take a long time if your number is large. However, a strategy called binary search can find the correct number faster. The idea is to start in the middle of the range and after each incorrect guess ask if the guess is_too_high or too low. Either way, you can eliminate half the remaining possible guesses.

Hint: Try using the is_too_high function to implement a faster strategy. is_too_high will return True if the guess is greater than the correct number.

```
>>> result = is_too_high(5)
Is 5 too high? [y/n] y
>>> result
True
```
Hint: You may want to update other variables besides guess.

```python
def guess_binary():
    """Return the number of attempted guesses. Implement a faster search
    algorithm that asks the user whether a guess is less than or greater than
    the correct number.

    Hint: If you know the guess is greater than the correct number, then your
    algorithm doesn't need to try numbers that are greater than guess.
    """
    prompt_for_number(LOWER, UPPER)
    num_guesses = 1
    lower, upper = LOWER, UPPER
    guess = (lower + upper) // 2
    "*** YOUR CODE HERE ***"
    while not is_correct(guess):
        if is_too_high(guess):
            upper = guess - 1
        else:
            lower = guess + 1
        guess = (lower + upper) // 2
        num_guesses += 1
    return num_guesses
```
