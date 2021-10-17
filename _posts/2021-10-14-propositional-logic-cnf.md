---
title: Models of Computation(3) - Propositional Logic (CNF)
use_math: true
---

In this section, we are going to use the original syntax of propositional logic.
i.e. Only the operators $\lnot, \land, \lor$ will be used. If there is a formula with any other operator, we will first get rid of it by replacing by equivalent formulas. This is the idea behind turning formulas into normal forms.

For example,
- replace $(p \rightarrow q) \text{by} (\lnot p \lor q)$
- replace $\bot text\{by} (p \land \lnot p)$

## Normal Froms: NNF
<u>Definition</u>
A formula $F$ is in negation normal form(NNF) if negations only occur **immediately in front of atoms**.

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

$\lnot \lnot \text{by} G$ ... Double Negation Law
$\lnot(G \land H) \text{by} (\lnot G \lor \lnot H)$ ... de Morgan's Law
$\lnot(G \lor H) \text{by} (\lnot G \land \lnot H)$ ... deMorgan's Law

until no such subformulas occur, and return the result.

Let us look at an example.
Put $\lnot(\not p \land (\lnot(r ]land s) \lor q))$ into NNF.

To simplify things, let $\lnot p = G$, and $\lnot (r \land s) \or q = H$.
Then, we have $\lnot (G \land H) \equiv \lnot G \lor \lnot H$ by deMorgan's Law.
$$
\equiv \lnot \lnot p \lor \lnot (\lnot (r \land s) \lor q)
\equiv p \lor \lnot (\lnot (r \land s) \lor q) \text{... deMorgan's Law}
\equiv p \lor (\lnot \lnot (r \land s) \land \lnot q) \text{... deMoragn's Law}
\equiv p \lor ((r \land s) \land \lnot q) \text{... Double Negation Law}
$$

<u>Definition</u>
- A *literal* is an atomic formula or the negation of an atomic formula.
i.e. It has the form $p \text{or} \lnot p$. $\lnot \lnot p$ is not a literal.

- A *clause* is a disjunction of literals or a literal.
eg. $(p \lor \lnot q \lor r) \text{and} \lnot r$ are clauses, but $(p \lor q) \land r$ is not a clause.

- A formula $F$ is in *conjunctive normal form(CNF)* if it is a conjunction of clauses or a clause.
eg. $(p \lor \lnot q \lor r) \land (\lnot p \lor r) \land q \land \lnot p$ is in CNF.
$p \land q \text{and} p \lor q$ is in CNF.
$(p \land q) \lor r$ is not in CNF. 

<u>Theorem</u>
For every formula $F$, there is an equivalent formula in CNF.

<u>Proof</u>
Here is an algorithm that transforms $F$ into an equivalent formula in CNF.
1. Put $F$ in NNF, call it $F'$.
2. Substitute if $F'$ each occurrence of a subformula of the form,
$((H \land I) \lor G) \text{or} (G \lor (H \land I)) \text{by} ((G \or H) \land (G \lor I))$ until no such subformulas occur, and return the result.

**NOTE**: This algorithm relies on the Commutative and Distributive Laws.

So, why is CNF important?
- It allows us to restrict to formulas with a uniform structure.
- It is used in practice. There are many tools for solving the Boolean Satisfiability Problem, that take CNF formulas as input.

## Reference
USYD COMP Lectures, 2021 September