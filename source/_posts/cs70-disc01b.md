---
title: cs70-disc01b
date: 2018-10-05 20:05:10
categories:
- CS70
tags:
- math
mathjax: true
---

# Stable Marriage

Consider the set of men $M={1,2,3}$ and the set of women $W={A,B,C}$ with the following preferences.
{% asset_img m1.png %}
Run the male propose-and-reject algorithm on this example. How many days does it take and what is the resulting pairing?(Show your work.)
<!-- more -->
The algorithm takes 3 days to produce a matching. The resulting pairing is as follows. The circles indicate a man that a woman picked on that given day(and rejected the rest).
$$\{(A,1),(B,2),(C,3)\}$$
{%asset_img m2.png %}

# Stable Marriage

The following questions refer to stable marriage instances with $n$ men and $n$ women, answer True/False or provide an expression as requested.
(a) For $n=2$, or any 2-man, 2-woman stable marriage instance, man A has the same optimal and pessimal woman.(True or False)

**False.** This says there is only one stable pairing. But if the preference list for man A is (1,2) and for man B is (2,1) and preference list for woman 1 is (B,A) and woman2 is (A,B) produce a different male and female optimal pairing.

(b) In any stable marriage instance, in the pairing the TMA(traditional stable marriage algorithm) produces there is some man who gets his favorite woman(the first woman on his preference list).(True or False.)

**False.** Let man A have preference list(1,3,2), B have (1,2,3), and C have (2,1,3). We develop a "cyclic" chain of preferences, causing A to displace B to displace C who then displaces A.
1. If woman 1 prefers A over B, she puts A on a string and rejects B.
2. B does not get his favorite and proposes to 2, who prefer B over C and then rejects C.
3. C does not get his favorite and proposes to 1, who prefer C over A and rejects A.
Thus, A also does not get his favorite, and no man gets his favorite.
{%asset_img m3.png %}

(c) In any stable marriage instance with $n$ men and women, if every man has a different favorite
woman, a different second favorite, a different third favorite, and so on, and every woman has
the same preference list, how many days does it take for TMA to finish? (Form of Answer: An
expression that may contain $n$.)

**The answer is 1**. On the first day every woman gets a proposal since each man has a different woman in their first position. The algorithm terminates.

(d) Consider a stable marriage instance with $n$ men and $n$ women, and where all men have the same preference list, and all women have different favorite men, and different second-favorite men, and so on. How many days does the TMA take to finish? (Form of Answer: An expression that may contain $n$.)

**The answer is n**. Every man proposes to their common favorite. One man is kept on the string. The rest propose to the second. And so one. After each day, a new woman gets a man on a string. After $n$ days, we finish. Note that the men's preference lists(Assuming they're the same for everyone) is irrelevant.

(e) It is possible for a stable pairing to have a man A and a woman 1 be paired if A is 1’s least preferred choice and 1 is A’s least preferred choice. (True or False.)

**True.**A and 1 are respectively all the women's and men's least favorite.

(f) It is possible for a stable pairing to have two couples where each person is paired with their least favorite choice. (True or False.)

**False**. Assume for the sake of contradiction that it is possible to have two couples where each person is paired with their least favorite choice. Consider $(m,w)$ and $$(m^*,w^*)$$, where $m$ and $w$ is paired despite being each other's last choice, as are $$m^*$$ and $$w^*$$. Since $m$ is $w$'s last choice, she prefers $$m^*$$ to her current partner. But $$w^*$$ is $$m^*$$'s last choice, he prefers $w$ to his current partner. Thus, $w$ and $$m^*$$ form a rogue couple, showing that no pairing with this situation can be stable.

(g) If there is a pairing, P, that consists of only pairs from male and female optimal pairings, then it must be stable. In other words, if every pair in P is a pair either in the male-optimal or the female-optimal pairing then P is stable. (True or False.)

**False**. Consider a woman who is matched to her pessimal partner and a man who is matched to his pessimal partner. They may well like each other.
An example is as follows.
Men’s preference list
A: 1 > ... > 2
B: 2 > ... > 1
C: 3 > ... > 4
D: 4 > ... > 3
Women’s preference list
1: B > ... > A
2: A > ... > B
3: D > ... > C
4: C > ... > D
Men’s first choices = women’s last choices and vice versa.
men-optimal: (A,1), (B,2), (C,3), (D,4)
women-optimal: (B,1), (A,2), (D,3), (C,4)
our pairing: (A,1), (B,2), (D,3), (C,4). Note that 1,2,C, and D are all with their pessimal
partner, so any pairing of 1 or 2 with C or D will be rogue. For example, (C,1) is a rogue couple.

# Good, Better, Best
In a particular instance of the stable marriage problem with $n$ men and $n$ women, it turns out that there are exactly three distinct stable matchings, $S1$, $S2$, and $S3$. Also, each man $m$ has a different partner in the three matchings. Therefore each man has a clear preference ordering of the three matchings (according to the ranking of his partners in his preference list). Now, suppose for man $m1$, this order is $S1 > S2 > S3$.

Prove that every man has the same preference ordering $S1 > S2 > S3$.

*Hint: Recall that a male-optimal matching always exists and can be generated using TMA. By reversing the roles of TMA, what other matching can we generate?*

In class, you were given the traditional propose-and-reject algorithm, which was guaranteed to produce a male-optimal matching. By switching men’s and women’s roles, you would be guaranteed to produce a female-optimal matching, which, by a lemma from class, would also be malepessimal. By the very fact that these algorithms exist and have been proven to work in this way, you’re guaranteed that a male-optimal and a male-pessimal matching always exist.

Since there are only three matchings in this particular stable matching instance, we thus know that one of them must be male-optimal and one must be male-pessimal. Since $m1$ prefers $S1$ above the other stable matchings, only that one can be male-optimal by definition of male-optimality. Similarly, since $m1$ prefers $S3$ the least, it must be the male-pessimal. Therefore, again from
definitions of optimality/pessimality, since each men have different matches in the three stable matchings, they must strictly prefer $S1$ to both of the others, and they must like $S3$ strictly less than both of the others. Thus, each man’s preference order of stable matchings must be $S1,S2,S3$.
