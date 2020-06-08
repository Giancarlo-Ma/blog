---
title: cs61c-hw00
date: 2018-09-24 09:39:44
categories:
- CS61C
tags:
- Machine Structure
- C
---

# Problem 2: Picking representations

Assume we are working with 8-bit numbers for the entirety of this question.

For each part below, you will be given a choice between unsigned and two's complement. It's your job to pick the better number representation for the given criteria, or denote that both representations are equally good. Explain your answer in one sentence or less.:

You would like to represent the temperature in degrees Celsius:

**We need 2's complement becauseo of negative temperature.**

<!-- more -->

You would like to maximize the range (distance between most negative represented number, and most positive represented number):

**Both representations have the same distance between most negative and most positive numbers.**

You would like to represent the number of boxes a factory has shipped:

**This number will never be negative so unsigned can represent more useful numbers.**

# Problem 3: Counting

For a system of n-digit unsigned base 4 numbers (n > 1), how many numbers can be represented?

**Since each bit can be represented by 0,1,2,3, so we can represent 4^n numbers.**

For an n-digit 2's complement binary number (n > 1), what is the number of negative integers?

**2^(n - 1),Because negative and positive which contains zero evenly divide the 2^n numbers.**

For an n-digit 2's complement number (n > 1), how many zeros are there?

**Only one!**

For an n-digit 2's complement number (n > 1), what is the difference between the most positive number and the most negative number?

**The most postive number is 2^(n - 1) -1, the most negative number is -2^(n - 1), so the difference is 2^n - 1.**

# Problem 4: Overflow
The following addition and subtraction operations are to be carried out with 8-bit 2's complement numbers. For each operation, calculate the result and label as OVERFLOW or CORRECT

Example: 1 + 2 = 0b0000 0001 + 0b0000 0010 = 0b0000 0011 = 3, CORRECT

64 + 64 = ?

**Answer is -128, which is overflow**

-127 + 30 = ?

**Answer is -97, which is correct**

-127 - 1 = ?

**Answer is -128, which is correct**

38 - 40 = ?

**Answer is -2, which is correct.**
