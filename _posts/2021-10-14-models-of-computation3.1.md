---
title: Models of Computation(3.1) - Propositional Logic (ND)
categories: 
  - Models of Computation
use_math: true
---

## Why do we do deduction?

Deduction is about proving statements from assumptions.

- It can be used to improve our reasoning skills. e.g. to check that an argument is logically correct.

- It forms the basis of an approach to software reliability called *Formal Methods*.

- Logic programming languages like Prolog work by proving things.

- It is used in automated reasoning systems, automated theorem provers.

A deductive system gives you **rules** for constructing **formal proofs**.

Formal proofs:

1. Allow one to be sure that the proven statement is true (as long as the assumptions are true).

2. A highly disciplined way of reasoning (good for computers).

3. A sequence of formulas where each step is an assumption, or a deduction based on earlier steps.

4. Based entirely on rewriting formulas - no semantics.

### Motivation

- The most famous deductive system is found in Euclid's *Elements* for deducing facts in elementary geometry and number theory.

- Euclid made some **assumptions** about points and lines, such as "A straight line may be drawn between any two points".

- And, he used **logical rules** to produce mathematical proofs of statements about geometry, such as "The angles in a triangle sum to 180 degrees".

## Natural Deductions (ND)

**Natural Deduction** is a deductive system for reasoning about **propositions**. It was developed by Gerhard Gentzen (20th century German logician), and Stanisław Jaskowski (20th century Polish logician).

1. It uses the connectives, $\land$, $\lor$, $\lnot$, $\to$ and the constant $\bot$.

2. Every connective has two types of inference rules:
    - **Introduction rules** introduce the connective.
    - **Elimination rules** remove the connective.

3. There is also a rule to **introduce assumptions**.

For example, consider the following argument. Does it make logical sense?

1. It is raining and it is hot.
2. So, it is raining.

Yes, it does make logical sense. Premise (1) says it is raning and it is hot. So if we forget some of the information, we can deduce the conclusion (2), that it is raning.

So we can represent the above as:

$$
\begin{align}
&A \land B \\
&A
\end{align}
$$

This if formalized as rule $\land \text{ Elim }$, and read **Conjunction Elimination**.

The mathematical notation that looks like a fraction below can be read from top to bottom.

> The inference rule on the left-hand side is applied to the proposition on the top to become the one at the bottom.

$$
(\land \text{ Elim })\frac{A \land B}{A}
$$

$$
(\land \text{ Elim })\frac{A \land B}{B}
$$

Consider the following argument. Does it make logical sense?

1. It is raining.
2. It is hot.
3. So, it is raining and hot.

Yes, represent the above like so.

$$
\begin{align}
A \\
B \\
A \land B
\end{align}
$$

If we put together the information in (1) and (2), we can deduce (3).

This is formalized as rule $\land \text{ Int }$, and read **Conjunction Introduction**.

$$
(\land \text{ Intr })\frac{A \quad B}{A \land B}
$$

Does the following argument make logical sense?

1. It is hot and it is raining.
2. So, it is raining and it is hot.

Yes. This reflects the commutativity of $\land$.

From $H \land R$, we can deduce $R \land H$. We will not introduce a new rule for this. Rather, we will use existing rules to prove it.

### Proof

Assume $H \land R$. By the $\land \text{ Elim }$ rule applied to it, we can deduce $H$.
Similarly, applying it again, we can also deduce $R$.

By, applying the $\land \text{ Intr }$ rule to $R$ and $H$, we can deduce $R \land H$.

So, we can represent this idea formally in a table.

|Line|Assumptions|Formula|Justification|References|
|---|---|---|---|---|
|1|1|$H \land R$|Asmp. Intr||
|2|1|$H$|$\land \text{ Elim }$|1|
|3|1|$R$|$\land \text{ Elim }$|1|
|4|1|$R \land H$|$\land \text{ Intr }$|3, 2|

**NOTE**: The only new rule is Asmp. Intr, read as **Assumption Introduction**.

Let's go back for a second to the idea of a **formal proof**.

- A formal proof usually starts with some assumptions.
- Each step creates another formula which is **justified** by applying an **inference rule** to formulas from previous steps.
- If we manage to prove a formula $F$ from assumptions(that have not been cancelled) $E_{1}, E_{2}, \dots, E_{k}$, we will write

$$
E_{1}, \dots, E_{k} \vdash F
$$

It is read $E_{1}, \dots, E_{k}$ proves $F$, which is called a **deductive consequence**.

**NOTE**: If there are no assumptions, then we write $\vdash F$.

Above, we showed $H \land R \vdash R \land H$.

Each line in the table used for the formal proof means the following:
> By using the *assumptions*, we conclude the *formula* holds, because it can be *justified* by applying the rule to the *referenced* lines above it.

### Rules involving implications

Consider the following argument. Does it make logical sense?

1. If it is hot then it is raining.
2. It is hot.
3. So, it is raining.

Yes, it does.

This is formalized as rule $\to \text{ Elim }$, and read **Implication Elimination**.

$$
(\to \text{ Elim })\frac{A \to B \quad A}{B}
$$

Now, consider the following argument. Does it make logical sense?

1. If it is hot then it is raining.
2. If it is raining then it is wet.
3. So, if it is hot then it is wet.

RTP: $(H \to R), (R \to W) \vdash (H \to W)$

|Line|Assumptions|Formula|Justification|References|
|---|---|---|---|---|
|1|1|$H \to R$|Asmp. Intr||
|2|2|$R \to W$|Asmp. Intr||
|3|3|$H$|Asmp.Intr||
|4|1, 3|$R$|$\to \text{ Elim }$|1, 3|
|5|1, 2, 3|$W$|$\to \text{ Elim }$|2, 4|

But, this only allows us to deduce:

$$
(H \to R), (R \to W), H \vdash W
$$

We need to introduce an implication.

The rule $\to \text{ Intr }$, read **Implication Introduction**

- formalizes that if we have a proof of $B$ by assuming $A$, then we can get "$A$ implies $B$" **without the assumption** $A$.
- has a new feature: we **assume** $A$, use it to prove $B$, and then **cancel** the assumption when deducing $A \to B$.

$$
(\to \text{ Intr })\frac{\begin{matrix}
[A] \\
\vdots \\
B \\
\end{matrix}}{A \to B}
$$

|Line|Assumptions|Formula|Justification|References|
|---|---|---|---|---|
|1|1|$H \to R$|Asmp. Intr||
|2|2|$R \to W$|Asmp. Intr||
|3|3|$H$|Asmp.Intr||
|4|1, 3|$R$|$\to \text{ Elim }$|1, 3|
|5|1, 2, 3|$W$|$\to \text{ Elim }$|2, 4|
|6|1, 2|$H \to W$|$\to \text{ Intr }$|3, 5|

In line 6, when we apply $(\to \text{ Intr })$:

- we do not have line 3 (corresponding to formula $H$) in the assumptions of line 6.

This is called **cancelling** the assumption.

So we can add to our description of Natural Deduction as follows.

**Natural Deduction** is a deductive system for reasoning about **propositions**. It was developed by Gerhard Gentzen (20th century German logician), and Stanisław Jaskowski (20th century Polish logician).

1. It uses the connectives, $\land$, $\lor$, $\lnot$, $\to$ and the constant $\bot$.

2. Every connective has two types of inference rules:
    - **Introduction rules** introduce the connective.
    - **Elimination rules** remove the connective.

3. There is also a rule to **introduce assumptions**.

4. Some of the rules **cancel (aka discharge) assumptions**.

5. If we collect all the assumptions $E_{1}, \dots, E_{k}$ **that have not been cancelled at the end of the derivation**, and if $F$ is the last formula in the proof, then we write

$$
E_{1}, \dots, E_{k} \vdash F
$$

### Rules involving disjunctions

The $\lor \text{ Intr }$ rule formalizes the following reasoning.

- If we have $A$, then we can get $A \lor B$ as well as $B \lor A$, no matter what $B$ is.

$$
(\lor \text{ Intr }\frac{A}{A \lor B})
$$

$$
(\lor \text{ Intr }\frac{A}{B \lor A})
$$

The $(\lor \text{ Elim })$ rule formalizes "reasoning by cases".

- If we have $A \lor B$, and
- a proof of $C$ by assuming $A$, and
- a proof of $C$ by assuming $B$,
- then, we can get $C$.

$$
(\lor \text{ Elim })
\frac{A \lor B
\qquad
\begin{matrix}
[A] &\quad [B]\\
\vdots &\quad \vdots\\
C &\quad C\\
\end{matrix}
}{C}
$$

e.g. The following is an example of "reasoning by cases":

If it is hot, I wear a hat. If it is cold, I wear a hat. It is hot or cold. So, I wear a hat.

Let us try proving the following formula.

$$
(A \lor B), (A \to C), (B \to C) \vdash C
$$

Proof Strategy: Get $C$ by assuming $A$, and get $C$ by assuming $B$. And then apply $(\lor \text{ Elim })$ to $A \lor B$.

|Line|Assumptions|Formula|Justification|References|
|---|---|---|---|---|
|1|1|$A \lor B$|Asmp. Intr||
|2|2|$A \to C$|Asmp. Intr||
|3|3|$B \to C$|Asmp. Intr||
|4|4|$A$|Asmp. Intr||
|5|2, 4|$C$|$\to \text{ Elim }$|2, 4|
|6|6|$B$|Asmp. Intr||
|7|3, 6|$C$|$\to \text{ Elim }$|3, 6|
|8|1, 2, 3|$C$|$\lor \text{ Elim }$|1, 4, 5, 6, 7|

In line 8, we use $(\lor \text{ Elim })$:

- References: L1: $(A \lor B)$, L4, L5: $(A \vdash C)$, L6, L7: $(B \vdash C)$

### Four more rules in ND

- The $(\bot \text{ Intr })$ rule formalizes that $\bot$ follows from any contradiction.

- The $(\lnot \text{ Intr })$ rule formalizes that if we find a proof of $\bot$ by assuming $A$, then we get $\lnot A$.
