---
title: Models of Computation(1) - Formal Language Theory and Recursive Definitions
categories: 
  - Models of Computation
use_math: true
---

This page starts off the core CS topic of 'Models of Computation'. The whole topic will be subdivided into several articles.

A **computational problem** specifies what the input can be and what the output should be.

A **decision problem** only takes Yes(1) or No(0) outputs. So something like,

- Is the input list sorted?
- Is this string a valid Python program?
- Is the input graph connected?

Throughout this topic, we will focus on decision problems which has string as the input.
Sets of strings are called **languages**. So, we can say that we deal with computational problems of the form:

<p style="text-align: center;">"Is the input string in the language $L$?"</p>

This is called the **membership problem for $L$**.

For example,

1. $L$ = the set of all prime numbers ==> Prime number tester
2. $L$ = the set of all legal Python programs ==> Parser
3. $L$ = the set of all true arithmetic statements (eg. 4 * 5 = 20) ==> Arithmetic checker

So here are the main questions we will consider with working on models of computation.

1. How can we specify languages?\
eg. How do we specify the syntax of a programming language?

2. How can we manipulate languages? How much time/space is needed?\
eg. How do compilers work? How much time/space do they require?

3. How can we reason about languages?\
eg. How can we reason that a program we've written is syntactically correct?\
eg. How can we reason that when we execute a program, it will do what we designed it to do?

4. What are the limits of computation?\
eg. Can every decision problem be solved by some program?

To build models that specify languages $L$,

- Regular expressions specify tokens.
- Grammars generate valid programs.
- Logical formulas specify database queries.

To build models that recognize languages $L$,

- Automata recognize strings corresponding to tokens.
- Pushdown-automata recognize valid programs

To use models to reason about languages $L$,

- We use logical deduction.

In fact, specifying and recognizing are often related!

## Formal Languages

**Definition**: A string over $\sum$ is a finite sequence of symbols from $\sum$. The number of symbols in a string is its length.

For example,

- 0110 is a string of length 4 over alphabet {0, 1}.
- bob is a string of length 3 over alphabet {a, b, ..., z}.
- There is only one string of length 0, denoted by $\epsilon$.
- The set of all strings over $\sum$ is denoted $\sum^{*}$.

**Definition**: The concatenation of strings $x, y$ is the string $xy$ formed by appending $y$ to the end of $x$.

For example,

- If we concatenate $y = 01$ to $x = 010$, the resulting string is $01001$.
- $w\epsilon = \epsilon = w$ for all strings $w$.
- For $k \in \mathbb{N}, w^{k} = \underbrace{ww\cdots w}_{k\text{ times}}$.
- $w^{0} = \epsilon$.

**Definition**: A set $L \subseteq \sum^{*}$ of strings is called a language over $\sum$.

For example,
$L$ = the set of all strings representing legal Python programs
$L$ = the set of all binary strings representing prime numbers
$L$ = the set of all true statements about arithmetic
$L$ = the set of all English words (in a given dictionary)

NOTE: The empty set $\emptyset$, also written ${}$, is a language with no elements in it, but the set ${\epsilon}$ is language with one element in it, the empty string.
NOTE: The languages ${0, 1}$ and ${1, 0}$ are equal, but the strings 01 and 10 are not.

**Definition**: Let $A, B$ be languages over $\sum$.
Then, the union of $A$ and $B$, written as $A \cup B$, is the language ${x \in \sum^{*} : x \in A \text{ or } x \in B}$.

The concatenation of $A$ and $B$, written as $AB$ or $A \circ B$, is the language ${xy \in \sum^{*} : x \in A, y \in B}$.

$A^{k} = \underbrace{AA\cdots A}_{k\text{ times}}$.

$A^{0} = {\epsilon}$

The star of $A$, written as $A^{*}$, is the language $\bigcup_{n \in \mathbb{N}} A^{n} = A^{0} \cup A^{1} \cup A^{2} \cup \cdots$

i.e. $A^{\*} = \{x_{1}x_{2} \cdots x_{k} \in \sum^{*} : k \geq 0, \text{each } x_{i} \in A\}$

## Regular Expressions

1. Expressions that describe "simple" languages
2. Extremely useful for text processing(eg. pattern matching), scanners(lexical analyzers, tokenizers), foundations of query languages for graph databases, etc
3. Based on three operations:

    - Language Union
    - Language Concatenation
    - Language Iteration/star

### Syntax

Definition: Let $\sum$ be an alphabet. The regular expressions over $\sum$ are defined by the following recursive process:

1. The symbols $\emptyset$ and $\epsilon$ are regular expressions.
2. Each symbol $a$ from $\sum$ is a regular expression.
3. If $R_{1}, R_{2}$ are regular expressions, then so is $R_{1} \cup R_{2}$.
4. If $R_{1}, R_{2}$ are regular expressions, then so is $R_{1}R_{2}$.
5. If $R$ is a regular expression, then so is $R^{*}$.

For example,
Let $\sum = {a,b}$.
Some possible regular expressions are $a \cup \emptyset$, $a\epsilon$, $b^{\*}$, $((a^{\*} \cup b^{\*})(ac))^{*}$.

### Semantics

Regular expressions represent/specify languages.

Definition: Let $\sum$ be an alphabet. The language $L(R)$ represented by a reular expression $R$ is defined by the following recursive procedure:

1. $L(\emptyset) = \\{a\\} \text{ and } L(\epsilon) = \\{\epsilon\\}$
2. $L(a) = \\{a\\} \text{ for } a \in \sum$
3. $L(R_{1} \cup R_{2}) = L(R_{1}) \cup L(R_{2})$
4. $L(R_{1}R_{2}) = L(R_{1})L(R_{2})$
5. $L(R^{\*}) = L(R)^{*} = \{\epsilon\} \cup L(R)^{1} \cup L(R)^{2} \cup L(R)^{3} \cup \cdots$

If $s \in L(R)$, we say that $s \text{ matches } R$.

NOTE: Some texts use + or \| in regular expressions instead of $\cup$.

Recall the question we said at the start: "Is the string $s$ in $L$?" for suitably chosen languages $L$.

If we take $L$ to be $L(R)$, then this is called the membership problem for the RE $R$ (checks if $s \text{ matches } R$).
