---
title: Models of Computation(3) - Propositional Logic (CNF)
use_math: true
---

In this section, we are going to use the original syntax of propositional logic.
i.e. Only the operators $\lnot, \land, \lor$ will be used. If there is a formula with any other operator, we will first get rid of it by replacing by equivalent formulas. This is the idea behind turning formulas into normal forms.

For example,

- replace $(p \rightarrow q) \text{ by } (\lnot p \lor q)$
- replace $\bot \text{ by } (p \land \lnot p)$

## Normal Froms: NNF

<u>Definition</u>
A formula $F$ is in *negation normal form(NNF)* if negations only occur **immediately in front of atoms**.

For example,

- $p$
- $\lnot p$
- $(\lnot q \lor p)$
are in NNF, but
- $\lnot \lnot p$
- $\lnot \lnot \lnot p$
- $\lnot(q \land \lnot p)$
are not.

<u>Theorem</u>
For every formula $F$, there is an logically equivalent formula in NNF.

<u>Algorithm</u> (Push Negation Inwards)

Idea: Substitute in $F$ every occurence of a subformula of the form:

$\lnot \lnot \text{ by } G$ ... Double Negation Law

$\lnot(G \land H) \text{ by } (\lnot G \lor \lnot H)$ ... de Morgan's Law

$\lnot(G \lor H) \text{ by } (\lnot G \land \lnot H)$ ... deMorgan's Law

until no such subformulas occur, and return the result.

Let us look at an example.

Put $\lnot(\lnot p \land (\lnot(r \land s) \lor q))$ into NNF.

To simplify things, let $\lnot p = G$, and $\lnot (r \land s) \lor q = H$.

Then, we have $\lnot (G \land H) \equiv \lnot G \lor \lnot H$ by deMorgan's Law.

$$
\begin{aligned}
&\equiv \lnot \lnot p \lor \lnot (\lnot (r \land s) \lor q) \\
&\equiv p \lor \lnot (\lnot (r \land s) \lor q) \qquad &\text{... deMorgan's Law} \\
&\equiv p \lor (\lnot \lnot (r \land s) \land \lnot q) \qquad &\text{... deMoragn's Law} \\
&\equiv p \lor ((r \land s) \land \lnot q) \qquad &\text{... Double Negation Law}
\end{aligned}
$$

<u>Definition</u>

- A *literal* is an atomic formula or the negation of an atomic formula.
i.e. It has the form $p \text{ or } \lnot p$. $\lnot \lnot p$ is not a literal.

- A *clause* is a disjunction of literals or a literal.
eg. $(p \lor \lnot q \lor r) \text{ and } \lnot r$ are clauses, but $(p \lor q) \land r$ is not a clause.

- A formula $F$ is in *conjunctive normal form(CNF)* if it is a conjunction of clauses or a clause.
eg. $(p \lor \lnot q \lor r) \land (\lnot p \lor r) \land q \land \lnot p$ is in CNF.
$p \land q \text{ and } p \lor q$ is in CNF.
$(p \land q) \lor r$ is not in CNF. 

<u>Theorem</u>
For every formula $F$, there is an equivalent formula in CNF.

<u>Proof</u>

Here is an algorithm that transforms $F$ into an equivalent formula in CNF.

1. Put $F$ in NNF, call it $F'$.
2. Substitute if $F'$ each occurrence of a subformula of the form,
$((H \land I) \lor G) \text{or} (G \lor (H \land I)) \text{by} ((G \lor H) \land (G \lor I))$ until no such subformulas occur, and return the result.

**NOTE**: This algorithm relies on the Commutative and Distributive Laws.

So, why is CNF important?

- It allows us to restrict to formulas with a uniform structure.
- It is used in practice. There are many tools for solving the Boolean Satisfiability Problem, that take CNF formulas as input.

### Solving problems with logic

Two computational problems on graphs:

- **3COL**: given an undirected graph $(V, E)$, decide if it has a proper 3-colouring (and return one)
- **CLIQUE**: given an undirected graph $(V, E)$ and an integer $K$, decide if it has a clique of size at least $K$ (and return one).

Although these problems can be solved using graph algorithms, we will solve them using logic.

**Step 1.** Encode the input as a formula.

**Step 2.** Check if the formula is satisfiable.

**Step 3.** If it is satisfiable, turn a satisfying assignment into a solution of the graph problem, otherwise say "no solution".

### Solving 3COL with logic

- A **proper 3-colouring** of an undirected graph $(V, E)$ is a colouring of the vertices by three colors such that adjacent vertices get different colours.

- That is, it is a partition of $V = R \cup G \cup B$ into three parts $R, G, B$ such that if $(v, w) \in E, \text{ then } v \text{ and } w$ are not in the same part.

- The 3COL problem is to decide, given $(V, E)$, whether or not it has a proper 3-colouring, and return one if it does.

Let's try to solve this using logic.

First, we encode the input $(V, E)$ into a Boolean logic formula $\phi_{V, E}$ as follows. This formula will be tested for satisfiability. i.e. if it turns out to be satisfiable, then there is a solution, otherwise not.

- Suppose $V = \{1, 2, \ldots , N\}$.
- For every vertex $i \in V$, introduce three variables $r_{i}, g_{i}, b{i}$.
The three variables represent a particular vertex's colouring. i.e. if $r_{i}$ is true, then the other two must be false, etc.

The idea is that the formula will say that the true variables represent a proper colouring.

We can represent constraints placed on vertexes as follows.

- Every vertex gets at least one colour:

    $\bigwedge_{i \in V}(r_{i} \lor g_{i} \lor b{i})$

- Every vertex gets at most one colour:

    $\bigwedge_{i \in V}(\lnot r_{i} \lor \lnot g_{i})$
    $\bigwedge_{i \in V}(\lnot r_{i} \lor \lnot b_{i})$
    $\bigwedge_{i \in V}(\lnot g_{i} \lor \lnot b_{i})$

- Adjacent vertices get different colours:

    $\bigwedge_{i, j \in E}(\lnot r_{i} \lor \lnot r_{j})$
    $\bigwedge_{i, j \in E}(\lnot g_{i} \lor \lnot g_{j})$
    $\bigwedge_{i, j \in E}(\lnot b_{i} \lor \lnot b_{j})$

Now, we define $\phi$ to be the conjunction of all these constraints.

## Reference

USYD COMP Lectures, 2021 September
