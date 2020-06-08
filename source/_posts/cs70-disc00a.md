---
title: CS70-disc00a
date: 2018-10-03 14:12:12
categories:
- CS70
tags:
- math
mathjax: true
---

# Writting in Propositional Logic
For rach of the following sentences,translate the sentence into propositional logic using the notation introduced in class,and write its negation.

(a) The square of a nonzero integer is positive.

We can rephrase the sentence as "If n is a nonzero integer, then $n^2>0$". Which can be written as
$$\forall n \in \mathbb{Z},(n \neq 0) \Rightarrow (n^2>0)$$
or equivalently as
$$\forall n \in \Bbb{Z},(n = 0) \lor (n^2>0)$$
The latter is easier to negate, which is given by
$$\exists n \in \Bbb{Z},(n \neq 0) \land (n^2<=0)$$
<!-- more -->

(b) There are no integer solutions to the equation $x^2-y^2=10$.

The sentence is
$$\forall x,y \in \Bbb{Z}, x^2-y^2\neq 10$$
The negation is
$$\exists x,y \in \Bbb{Z}, x^2-y^2=10$$

(c) There is one and only one real solution to the equation $x^3 +x+1 = 0$.

Let $p(x)=x^3+x+1$.The sentence can be read "there is a aolution $x$ to the equation $p(x)=0$, and any other solution $y$ is equal to $x$". Or,
$$\exists x \in \Bbb{R},((p(x)=0)\land(\forall y \in \Bbb{R},(p(y)=0) \Rightarrow (x=y)))$$
Its negation is given by
$$\forall x \in \Bbb{R},((p(x)\neq0)\lor(\exists y \in R,(p(y)=0)\land(x\neq y)))$$

(d) For any two distinct real numbers, we can find a rational number in between them.

The sentence can be read "if $x$ and $y$ are distince real numbers, then there is a rational number $z$ between $x$ an $y$." Or,
$$\forall x,y \in \Bbb{R},(x \neq y) \Rightarrow (\exists z \in \Bbb{Q}, ((x < z < y) \lor (y<z<x))$$
Equivalently,
$$\forall x,y \in \Bbb{R},(x=y)\lor(\exists z \in \Bbb{Q},((x<z<y)\lor(y<z<x)))$$
Note that $x<z<y$ is mathematical shorthand for $(x<z)\land(z<y)$, so the above statement is equivalent to
$$\forall x,y \in \Bbb{R},(x=y)\lor(\exists z \in \Bbb{Q},((x<z)\land(z<y)) \lor ((y<z)\land(z<x))$$
Then the negation is
$$\exists x,y \in \Bbb{R},(x \neq y) \land (\forall z \in \Bbb{Q}, ((x \ge z)\lor(z\ge y))\land((y\ge z)\lor(z\ge  x)))$$

# Implication
Which of the following implications are always true, regardless of P?Give a counterexample for each false assertion(i.e. come up with a statement P(x,y) that would make the implication false).

(a) $\forall x, \forall y, P(x,y) \Rightarrow \forall y,\forall x,P(x,y)$.

This is true because $\forall x, \forall y$ and $\forall y, \forall x$ are both means all x and y in our universe.

(b) $\exists x,\exists y,P(x,y)\Rightarrow \exists x, \exists y, P(x,y)$

This is true. There exists can be switched if they are adjacent;$\exists x,\exists y$ and $\exists y,\exists x$ are both means there exists x and y in our universe.

(c) $\forall x, \exists y,P(x,y) \Rightarrow \exists y, \forall x, P(x,y)$.

False.Let P(x,y) be $x<y$ and the universe for x and y be the integers.Then we can find the former is true, while the latter is false.

(d) $\exists x,\forall y,P(x,y) \Rightarrow \forall y,\exists x,P(x,y)$

True.The first says that there exists an $x$, say $x'$ for every $y$,$P(x,y)$ is true. Thus, one can choose $x=x'$ for the second statement and that statement will be true for every $y$.Note that the two statements are not equivalent as the converse of this is statement 3, which is false.

# Logic
Decide whether each of the following is true or false and justify your answer.

(a) $\forall x(P(x)\land Q(x)) \equiv \forall x P(x) \land \forall x Q(x)$

This is true by drawing a truth table to see.

(b) $\forall x(P(x) \lor Q(x))\equiv \forall x P(x) \lor \forall x Q(x)$

This is false. If P(1) is true and Q(1) is false, P(2) is flase and Q(2) is true. Then the left hand side is true, while the right hand side is false.

(c) $\exists x(P(x) \lor Q(x)) \equiv \exists x P(x) \lor \exists x Q(x)$

This is true by drawing a truth table to see.

(d) $\exists x (P(x) \land Q(x)) \equiv \exists x P(x) \land \exists x Q(x)$

False. Let $P(1)$ be true while other values be false.Let $Q(1)$ be false while other values be true. Then the right hand side is true, while the left hand side is false.There would be no value for both $P(x)$ and $Q(x)$ simultaneously true.
