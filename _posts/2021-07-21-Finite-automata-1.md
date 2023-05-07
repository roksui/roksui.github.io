---
title: Finite Automata
categories: 
  - Models of Computation
use_math: true
---

When I first came across the famous "Halting Problem", my first thought was, "How can it be impossible for a computer to know what a program will do when the program is actually a written implementation of a computer that runs on another computer?".

Unlikely as it may seem, Alan Turing mathematically proved this problem by contradiction. Starting from this question of mine, I wanted to understand deeper what implications this problem has and find out if there exist any other alternatives for providing at least a quasi-algorithm for solving the problem. Hence, I have decided to cover the underlying mathematical modeling of a program in my blog.

## Computability Theory

**QUESTION**: What problems can we solve with a computer?

Wouldn't it be nice if a computer could solve all kinds of problems in this world? 

But, before answering the above question, we need to clarify a few things.

First, we need a simpler way of describing computing machines, because computers in real life are very complex and messy.

An **automaton** (plural: **automata**) is a mathematical model of a computing device. An automaton has a very clean and simple structure compared to the actual computer.

So, why build models?\
1. Mathematical simplicity - It is much easier to manipulate our abstract models of computers than it is to manipulate actual computers.

2. Intellectual robustness - If we pick our models correctly, we can make broad, sweeping claims about huge classes of real computers by arguing that they're just special cases(subsets) of our more general models.

We are going to explore two different models.\
1. **Finite automate** are an abstraction of computers with finite resource constraints.\ This model provides upper bounds for the computing mathinces that we can actually build.

2. **Turing machines** are an abstraction of computers with unbounded resources. This model provides upper bounds for what we could ever hope to accomplish.

Secondly, in the question, what is a "problem"? We need a formal and specific definition of a problem.\
We want a definition that
- corresponds to the problems we want to solve.
- captures a large class of problems.
- is mathematically simple to reason about.

## Formal Language Theory
Let's go over some definitions.

- An **alphabet** is a finite set of symbols called **characters**. Generally, we use the symbol $\sum$ to refer to an alphabet.

- A **string over an alphabet** $\sum$ is a finite sequence of characters drawn from $\sum$.\
For example, if $\sum = {a, b}$, some valid strings over $\sum$ are a, aabaaabbabaaabaaaabbb, abbababba.

- The **empty string** contains no characters and is denoted $\epsilon$.

Now, we have defined the building blocks of a formal language.

- A **formal language** is a set of strings.

- We say that $L$ is a **language over** $\sum$ if it is a set of string over $\sum$.\
For example, the language of palindromes over $\sum = {a, b, c}$ is the set,\
${\epsilon, a, b, c, aa, bb, cc, aaa, aba, aca, bab, ...}$

- The set of all strings composed from letters in $\sum$ is denoted $\sum *$.

- Formally, we say that $L$ is a language over $\sum$ if $L \subseteq \sum *$.

**Fundamental Question**: Given an alphabet $\sum$ and a language $L$ over $\sum$, in what cases can we build an automaton that determines which strings are in $L$?

The answer depends on both the choice of $L$ and the choice of automaton.

TBC...





## Reference
https://web.stanford.edu/class/archive/cs/cs103/cs103.1164/fall1516/