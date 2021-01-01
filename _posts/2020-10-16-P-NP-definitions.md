---
layout: post
title:  "P vs NP Basics"
date:   2020-10-16 22:44:56 +0900
categories: jekyll update
use_math: true
---

## Intuition and Definition behind the P vs NP Problem

# Informal Definition

**Definition (P)**: The set of problems with input size $n$ such that there exist valid-solution-generating algorithms with worst-case runtime bounded by $n^k$, where $k$ is any positive integer => Polynomial Time

Examples: sorting, finding the gcd of two numbers, etc

Consider the product of two $n \times n$ matrices $X$ and $Y$.

$X = $ 
$$
    \begin{bmatrix}
    x_{11} & x_{12} & x_{13} & \cdots & x_{1n} \\
    x_{21} & x_{22} & x_{23} & \cdots & x_{2n} \\
    \cdots & \cdots & \cdots & \ddots & \vdots \\
    x_{n1} & x_{n2} & x_{n3} & \cdots & x_{nn}
    \end{bmatrix}
$$
$Y = $ 
$$
    \begin{bmatrix}
    y_{11} & y_{12} & y_{13} & \cdots & y_{1n} \\
    y_{21} & y_{22} & y_{23} & \cdots & y_{2n} \\
    \cdots & \cdots & \cdots & \ddots & \vdots \\
    y_{n1} & y_{n2} & y_{n3} & \cdots & y_{nn}
    \end{bmatrix}
$$

If we want to find $XY$, we find the entries $XY_{ij}$ by computing $x_{i1}y_{1j} + x_{i2}y_{2j} + \cdots + x_{in}y_{nj}$.

This requires $n$ multiplications and $n$ additions, so the total number of actions to find $XY_{ij}$ is $2n$.
Since there are $n^2$ entries to be found, the total number of actions to find $XY$ is $n^2 \cdot 2n = 2n^3$. Hence the cost of this operation is $O(n) = n^3 \in P$, meaning this is polynomial time.

**Definition (NP)**: The set of problems with input size $n$, which, if given a solution, there exist valid-solution-checking algorithms with runtimes bounded by $n^k$, where $k$ is any positive integer => Nondeterministic Polynomial Time

NOTE: $P \subset NP$ because if problems can be solved in polynomial times, the solutions can be verified in polynomial time.

# Formal Definition

The formal definition requires knowledge of the Turing Machine.

We first define the following:

**Definition (alphabet)**: A finite set of symbols.

**Definition (Language)**: Given an alphabet $A$, a language $L$ is a set of finite strings, where each string is composed of symbols from $A$.

**Definition (P)**: A string $S$ is in a language $L$ if the deciding Turing Machine $M$ run on the string $S$ such that $M$ eventually halts and enters an accept state in polynomial time. We say that $M$ is polynomial time if for all strings in a language, $M$ enters either an accept state or reject state after at most $P(\lvert S\rvert)$ steps, where $P$ is a polynomial and $\lvert S\rvert$ is the length of $S$. A language is in $P$ if $\exists M$ such that the above condition is satisfied.

**Definition (NP)**: A language $L$ is in $NP$ if for every $S$ in $L$ there exists a verifier $V$ such that there exists a polynomial time Turing Machine $M$ such that $M(V,S)$ accepts if $S$ is in $L$ and rejects if $S$ is not in $L$.


Reference:

Andzu Schaefer. N vs NP, January 2017