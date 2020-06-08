---
title: cs70-disc01a
date: 2018-10-04 22:13:57
categories:
- CS70
tags:
- math
mathjax: true
---

# Induction

Prove the following using induction:
(a) For all natural numbers $n>2,2^n>2n+1$.

We proceed by induction on the variable $n$.
Base case$(n=3)$:$8>7$, which is correct.
Inductive Hypothesis:For arbitrary $n=m>2$, assume that $2^m>2m+1$.
Inductive Step: Because $m$ is positive number, then we have $2m>1$, so:
$$2^{m+1}=2\cdot 2^m > 2 \cdot (2m+1) = 4m+2 =2m+2m+2>2m+3$$
So we have $2^{m+1}>2(m+1)+1$,which completes the inductive step.
<!-- more -->
(b) For all positive integers $n$, $1^3+3^3+5^3+...+(2n-1)^3=n^2(2n^2-1)$.

For $n=1$, the statement is $1=1$, which is true. Assume that it holds for $n=m$. Then,
$$
\begin{align}
\sum_{k=1}^{m+1} (2k-1)^3 & = \sum_{k=1}^{m} (2k-1)^3 + (2m+1)^3=m^2(2m^2-1)+(2m+1)^3 \\
& = 2m^4+8m^3+11m^2+6m+1=(m+1)^2(2(m+1)^2-1)
\end{align}
$$

(c) For all positive natural numbers $n$,$\frac 5 4 \cdot 8^n+3^{3n-1}$ is divisible by 19.

For $n=1$, the statement is true because of 19 is divisible by 19. Assume that the statement holds for $n=m$, such that $\frac 5 4 \cdot 8^m + 3^{3m-1}$ is divisible by 19. Then we have
$$
\begin{align}
\frac 5 4 \cdot 8^{m+1} + 3^{3(m+1)-1} & = 8 \cdot \frac 5 4 8^m + 3^{3m-1+3} \\
& = 8\cdot (\frac 5 4 \cdot 8^m + 3^{3m-1}) + 19\cdot 3^{3m-1}
\end{align}
$$
The first term is divisible by 19 because of induction hypothesis, and the second term is clearly divisible by 19.This completes our proof, as we have shown the statement holds for $m+1$.

# Make It Stronger

Suppose that the sequence $$a_1$$,$$a_2$$,$$...$$ is defined by $$a_1=1$$ and $a_{n+1}=3a_n^2$ for $n \ge 1$. We want to prove that $a_n \le 3^{2^n}$ for every positive integer $n$.

(a) Suppose that we want to prove this statement using induction, can we let our induction hypothesis be simply $a_n \le 3^{2^n}$? Show why this does not work.

Try to prove for every $n\ge 1$, we have $a_n\le 3^{2^n}$ using induction.
Base case: For $n=1$, we have $a_1=1 \le 3^{2^1}=9$.
Inductive hypothesis: For $n>1$, assume that $a_n\le 3^{2^n}$.
Inductive step:
$$
\begin{align}
a_{n+1}\le 3\cdot (3^{2^n})^2 & =3\cdot 3^{2^{n+1}} \\
& = 3^{2^{n+1}+1}
\end{align}
$$
However, what we wanted to prove is $a_{n+1} \le 3^{2^{n+1}}$. There is an extra +1 in the exponent of what we derived.

(b) Try to instead prove the statement $a_n\le 3^{2^n-1}$ using induction. Does this statement imply what you tried to prove in the previous part?

Base case: $n=1, a_1=1 \le 3^{2^1 - 1}=3$
Inductive hypothesis: For every $n>1$, assume that $a_n \le 3^{2^n-1}$
Inductive step:
$$
\begin{align}
a_{n+1} \le 3\cdot (3^{2^n -1})^2 & = 3\cdot 3^{2^{n+1}-2} \\
& = 3^{2^{n+1}-1}
\end{align}
$$
This is exactly the induction hypothesis for $n+1$. Note that for every $n\ge 1$, we have $2^n-1\le2^n$ and therefore $3^{2^n-1}\le3^{2^n}$. This means that our modified hypothesis which we proved here is indeed imply what we wanted to prove in the previous part. This is called "strengthening" the induction hypothesis because we proved a stronger statement and by proving this statement to be true, we proved the original statement to be true as well.

# Bit String

Prove that every positive integer $n$ can be written with a string of 0s and 1s. In other words, prove that we can write
$$n = c_k\cdot 2^k + c_{k-1}\cdot 2^{k-1}+...+c_1\cdot 2^1+c_0\cdot 2^0$$
where $k \in \Bbb{N}$ and $c_k \in {0,1}$.

Base case: $n=1$ can be written as $1 = 1 \times 2^0$
Inductive hypothesis: Assume that the statement holds for all $1\le k\le n$
Inductive step: There are two kinds of possibilities for $n+1$. If $n+1$ is even, then we can apply our inductive hypothesis to $(n+1)/2$ and use its representation to express $n+1$ in the desired form.
$$
\begin{align}
(n+1)/2 & = c_k\cdot 2^k + c_{k-1}\cdot 2^{k-1}+...+c_1\cdot 2^1+c_0\cdot 2^0 \\
n + 1 = 2\cdot (n+1) / 2 & = c_k\cdot 2^{k+1} + c_{k-1}\cdot 2^{k}+...+c_1\cdot 2^2+c_0\cdot 2^1
\end{align}
$$
Otherwise, $n$ must be even and have $c_0=0$. We can obtain the representation of $n+1$ from $n$.
$$
\begin{align}
n & =c_k\cdot 2^k + c_{k-1}\cdot 2^{k-1}+...+c_1\cdot 2^1+0\cdot 2^0 \\
n+1 & = c_k\cdot 2^k + c_{k-1}\cdot 2^{k-1}+...+c_1\cdot 2^1+1\cdot 2^0
\end{align}
$$
Therefore, the statement is true.
