---
layout: post
title:  "P vs NP Basics"
date:   2020-10-16 22:44:56 +0900
categories: jekyll update
use_math: true
---

## Intuition and Definition behind the P vs NP Problem

# Informal Definition

Definition (P): The set of problems with input size $n$ such that there exist valid-solution-generating algorithms with worst-case runtime bounded by $n^k$, where $k$ is any positive integer. => Polynomial Time

Examples: sorting, finding the gcd of two numbers, etc

Consider the product of two $n \times n$ matrices $X$ and $Y$.

$$
    \begin{bmatrix}
    x_{11} & x_{12} & x_{13} & \cdots & x_{1n} \\
    x_{21} & x_{22} & x_{23} & \cdots & x_{2n} \\
    \cdots & \cdots & \cdots & \ddots & \vdots \\
    x_{n1} & x_{n2} & x_{n3} & \cdots & x_{nn}
    \end{bmatrix}
$$

