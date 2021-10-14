---
title: Models of Computation(2) - Propositional Logic
use_math: true
---

## Logic
Logics are used everyday for representing, computing and reasoning.<br>
- Electronics: design and simplification of digital circuits
- Databases: basis for query languages (SQL)
- Programming Languages: DATALOG, PROLOG
- Algorithms: P vs NP
- AL: Knowledge-bases
- Foundations of mathematics: provability
- Programming: reasoning about the correctness of programs
- Automated reasoning: automated theorem proving
- Reasoning: as an aid to identify valid arguments

In this article, we are going to look at:
**Propositional logic** - about propositions (statements that can be evaluated to true or false) and,
**Predicate logic** - about relations and functions (eg. the correctness of programs)

There are also logics of time, knowledge, belief, etc.

### What makes up a logic?
1. **Syntax** tells us the **structure of expressions**, or the rules for putting symbols together to form an expression.
2. **Semantics** refers to the **meaning of expressions**, or how they are evaluated.
3. **Deduction** is a syntactic mechanism for deriving new true expressions from existing true expressions.

### Much like algebra!
- Algebra is for expressing and reasoning about arithmetic.
- Propositional logic is for expressing and reasoning about propositions(statements).

### Propositions
A *proposition* is a sentence that declares a fact that is either true or false, but not both.

For example,
"The sun is hot" and 2 < 5 are propositions but,
"Are you hungry" or "x is an integer" are not.

### Propositional Logic: Syntax
<u>Definition</u>
An *atom* is a variable of the form $p_{1}, p_{2}, p_{3},..., p, q, r,...$
A *formula* is defined by the following recursive process:
F1. Every atom is a formula.
F2. If $F$ is a formula then $\lnot F$ is a formula.
F3. If $F, G$ are formulas, then so are $(F \lor G) \text{and} (F \land G)$.


The base case specifies the simplest objects, and teh recursive cases specify how to build complex objects from the previous ones.<br>
Just like a construction manual!

For example, we can build $(p \land (q \lor \lnot p))$ in a bottom-up manner.
NOTE: top-down method is also possible.

$p$ is a formula (by F1)
$q$ is a formula (by F1)
$\lnot p$ is a formula (by F2)
$(q \lor p)$ is a formula (by F3)
$(p \land (q \lor \lnot p))$ is a formula (by F3)

So why do we need this definition anyway?

1. It specifies what we mean by a propositional formula. We can use it as a basis for proving or disproving that something is a formula.
2. It allows one to design algorithms that process/manipulate formulas using the recursive nature of the defintion.

### Semantics

*Semantics* refers to how you derive the value of a formula based on the values of its atomic subformulas.

- The elements of the set $\{0, 1\}$ are called **truth values**.
- After we assign truth values to atoms, we can give truth values to formulas.
- An *assignment* is a function from atoms to truth values. So, on three variables $p, q, r$, there are $2^3 = 8$ assignments. (i.e. $\{0, 0, 0\}, \{0, 0, 1\}, \{0, 1, 0\}$ and so on.)

To be more rigorous, the truth value of a formula under an assignment $\alpha$ is defined by the following recursive process:

TV1. $tv(p, \alpha) = \alpha(p)$ for every atom $p$.
TV2. $tv(\lnot F, \alpha) =
\begin{cases}
0, & \text{if $tv(F, \alpha) = 1$} \\
1, & \text{if $tv(F, \alpha) = 0$}
\end{cases}
TV3. $tv(F \land G, \alpha) =
\begin{cases}
1, & \text{if $tv(F, \alpha) = 1$ and $tv(G, \alpha) = 1$} \\
0, & \text{otherwise}
\end{cases}
TV4. $tv(F \lor G, \alpha) =
\begin{cases}
1, & \text{if $tv(F, \alpha) = 1$ or $tv(G, \alpha) = 1$} \\
0, & \text{otherwise}
\end{cases}

And, similar to syntax, why do we need this precise definition?

1. It specifies how to evaluate formulas.
2. The definition is implemented by the runtime environment of Python, Java and other programming languages to compute the values of Boolean expressions.
3. One can prove things about formulas or about code that processes formulas.

NOTE: We sometimes shorten $tv(F, \alpha)$ and simply write $\alpha(F)$. So, $tv(F \land G, \alpha) = 1 \leftrightarrow \alpha(F \land G) = 1$.
In case $\alph(F) = 1$, we say any of the following:
- $\alpha$ makes $F$ true.
- $\alpha$ satisfies $F$.
- $\alpha$ models $F$, which is written $a \vDash F$. (this symbole is called the double-turnstile)

### Other logical symbols
1. $\top \text{and} \bot$ are formulas and called the propositional constants, namely *top* and *bottom* respectively.
2. If $F, G$ are formulas, then so are $(F \rightarrow G) \text{and} (F \leftrightarrow G)$. $\rightarrow$ is called the conditional(implication), and $\leftrightarrow$ is called teh bi-conditional(bi-implication).

Now, in terms of semantics,
1. The formula $\top$ is always true, and the formula $\bot$ is always false.
2. The formula $(F \rightarrow G)$ is false if and only if $F$ is true and $G$ is false.
3. The formula $(F \leftrightarrow G)$ is true if and only if $F$ and $G$ have the same truth values.

Let's put it more formally.
Recall, the first definitino for the truth value of formulas under an assignment $\alpha$.
TV1. $tv(p, \alpha) = \alpha(p) \text{for atoms} p$.

Now for the next three definitions, we can replace the case definitions as follows.

TV2. $tv(\lnot F, \alpha) = 1 - tv(F, \alpha)$
TV3. $tv(F \land G, \alpha) = min{tv(F, \alpha), tv(G, \alpha)}
TV4. $tv(F \lor G, \alpha) = max{tv(F, \alpha), tv(G, \alpha)}

These are much more succinct.

Now, for the other logical formulas,

TV5. $tv(F \rightarrow G, \alpha) = tv(\lnot F \lor G, \alpha)$
TV6. $tv(F \leftrightarrow G, \alpha) = tv((F \rightarrow G) \and (G \rightarrow F), \alpha)$
TV7. $tv(\top, \alpha) = 1, tv(\bot, \alpha) = 0$

**NOTE**: For the case of implication, when $F$ is not true, then it doesn't matter whether $G$ is true or not, the formula $F \rightarrow G$ will still be true.
It might be useful to think of the formula as to mean that if $F$ is true then I **promise** to make $G$ true.

So, the question is, what is the case that I break the promise?
For example, "If I am elected, I will lower taxes" can be the formula.
1. I am elected and I lower taxes. (FINE)
2. I am elected and I do not lower taxes. (BROKEN)
3. I am not elected and I lower taxes. (FINE)
4. I am not elected and I do not lower taxes. (FINE)

This is a good way to understand by thinking about "when is the implication false?".
When $F$ is false and $G$ true or false: i.e. "I am not elected and I lower taxes", or "I am not elected and I do not lower taxes", from these cases, we cannot conclude that $F \rightarrow G$ is false. In a world where $F$ is false, $F \rightarrow G$ did not actually promise us anything. So it can't possibly have lied to us. The only time it lies to us is when $F$ is true but $G$ is false.

**REMEMBER** Propositional logic and natural language are not the same. Logic can be used to model propositions of natural language but there are important differences.
- In logic, semantics is determined by the assignment and the syntactic structure of the formula.
- In natural language, semantics also depends on the context.

eg. "He threw the stone and the window broke" would not be the same as "The window broke and he threw the stone" in English. But in formal logic, same meaning.

### Validity
<u>Definition</u>
A formula $F$ is *valid* if every assignment satisfies $F$. In other words, if its truth-table always has value 1.
For example,
- $p \lor \lnot p$ is valid.
- $p \lor \lnot q$ is not valid.

Valid formulas represent logical laws in the same way that $x + 0 = x$ is an arithmetic law.

### Satisfiability
<u>Definition</u>
A formula $F$ is *satisfiable* if at least one assignment satisfies $F$. In other words, if its truth-table has at least one 1.
For example,
- $p \lor \lnot q$ is satisfiable.
- $p \land \lnot p$ is not satisfiable.

Now, the **satisfiability problem** is to decide if a given propositional formula $F$ is satisfiable. In principal, this can be solved by checking all the rows of the truth-table for $F$.

The questions are...
**How efficient is this truth-table test for satisfiability?**
- Suppose $F$ has $n$ atoms.
- Then the truth-table has $2^n$ rows.
- So this algorithm runs in worst-case exponential time (in the size of $F$).
- Suppose you can generate a table at the rate of one row per second.
- If $n = 80$, you will need $2^80$ seconds to write out the table.
- This is about 2.8 million times the age of the universe.

Now...what if we could generate a billion rows per second?
So the question becomes,
**Is there a substantially faster method?**
This is extremely important as it is the central NP-complete problem.
One can verify in polynomial time if a given assignment satisfies a formula, but it is not known if the satisfiability problem itself can be solved in polynomial time.
And, many many problems can be efficiently reduced to the satisfiability problem.

## Reference
USYD COMP Lectures, 2021 September