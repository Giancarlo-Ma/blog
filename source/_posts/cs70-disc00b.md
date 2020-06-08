---
title: cs70-disc00b
date: 2018-10-04 11:51:37
categories:
- CS70
tags:
- math
mathjax: true
---

# Contraposition

Prove the statement "if $a+b<c+d$, then $a<c$ or $b<d$".

The implication we are trying to prove is $(a+b<c+d)\Rightarrow((a<c)\land(b<d))$, so the contrapositive is $((a\ge c)\land(b\ge d)) \Rightarrow (a+b \ge c+d)$. The proof is straightforward, since we have both that $a\ge c$ and $b\ge d$,we can just add these two inequalities together, giving us $a+b\ge c+d$, which is exactly what we want to prove.

<!-- more -->
# Perfect Square

A *perfect square* is an integer $n$ of the form $n=m^2$ for some integer m. Prove that every odd perfect square is of the form $8k+1$ for some integer $k$.

We will proceed with a direct proof.Let $n=m^2$ for some integer m. Since n is odd, then m is also odd, i.e., of the form $m=2l+1$ for some integer $l$.Then $m^2=4l^2+1+4l=4l(l+1)+1$. Since one of $l$ and $l+1$ must be even, $l(l+1)$ is of the form $2k$ and $n=m^2=8k+1$.

# Number of Friends

Prove that if there are $n\ge 2$ people at a party, then at least 2 of them have the same number of friends at the party.
(Hint: *The Pigeonhole Princle* states that if $n$ items are placed in $m$ containers, where $n>m$, at least one container must contain more than one item. You may use this without proof.)

There are two thoughts of this problem. First, we will prove this using contradiction. Suppose the contrary is that everyone has a different number of friends at the party.Since the number of friends that each person can hava ranges from 0 to $n-1$, we conclude that for every $i \in {0,1,...,n-1}$, there is exactly one person who has exactly $i$ friends at the party. In paticular, there is one person who has $n-1$ fiends(i.e. friends with everyone) and there is one person who has $0$ friends(i.e. friends with no one), which is contradiction. Second, we can think of this question using pigeonhole principle. Here we have $n$ n possible containers. Each container denotes the number of friends one person can have, so the container can be labeled 0,1,...,$n - 1$. The objects assigned to these containers are the people at the party. However, container 0, $n-1$ can not be occupied at the same time, this means we are assigning $n$ people to at most $n-1$ containers, and by pigeonhole princile, at least one of the $n-1$ containers has to have two or more objects i.e. at least two people have to have the same number of friends.

# Fermat’s Contradiction

Prove that $2^{1/n}$ is not rational for any integer $n\ge 3$.(Hint: Use Fermat's Last Theorem)

We will proceed with contradiction. Assuming that $2^{1/n}$ is rational for ant integer $n\ge 3$. Then we have $2^{1/n}=\frac p q$, which p and q are all positive integers. Thus, $2q^n=p^n$, and this implies
$$q^n+q^n=p^n$$
which is a contradiction to the Fermat's Last Therom.(No positive number $a$, $b$ and $c$ satisfy the equation $a^n+b^n=c^n$ for any integer value of $n$ greater than 2).

# Prime Form

Prove that every prime number $m>3$ is either of the form $6k+1$ or $6k-1$ for some integer $k$.

We proceed using proof by cases. We know that any integer can be expressed as $6k+i$, where $i\in {0, 1, 2, 3, 4, 5}$. So we have the following 6 cases:
1. $m=6k$, which can't be true because m can't be a prime.(has other factors other than one and itself)
2. $m=6k+1$, this could be prime.
3. $m=6k+2$, this can not be prime since $m=2(3k+1)$.
4. $m=6k+3$, this can not be prime since $m=3(2k+1)$.
5. $m=6k+4$, this can not be prime since $m=2(3k+2)$.
6. $m=6k+5$, this could be prime and $m=6(k+1)-1$.

Therefore, if $m$ is prime number, it must either have form $6k+1$ or $6k-1$.
