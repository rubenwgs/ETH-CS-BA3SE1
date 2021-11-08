**Compiler Design — Lecture notes week 6**

- Author: Ruben Schenk
- Date: 26.10.2021
- Contact: ruben.schenk@inf.ethz.ch

# 8. LL & LR Parsing

## 8.1 LL(1) Grammars

One problem with grammars is that not all grammars can be parsed "top-down" with a _single lookahead_. _Top-down_ means that we start frpm the start symbol ,i.e. the root of the parse tree, and go down.

**LL(1)** means:

- Left-to-right scanning
- Left-most derivation
- 1 lookahead symbol

### 8.1.1 Making a Grammar LL(1)

The main problem is that we can't decide which $S$ production to apply until we see the symbol after the first expression. The solution is to _left-factor_ the grammar. There is a common $S$ prefix for each choice, so add a new non-terminal $S'$ at the decision point:

This means that we transform our example grammar

$$S \to E + S \, | \, E \\ E \to \text{number} \, | \, (S)$$

to the following "left-factor" grammar:

$$S \to ES' \\ S' \to \epsilon \\ S' \to + S \\ E \to \text{number} \, | \, (S)$$

However, we also need to _eliminate left-recursion_ somehow. In general, this is done by rewriting the following left-recursive rule

$$S \to S \alpha_1 \, | \, \cdots \, | \, S \alpha_n \, | \, \beta_1 \, | \, \cdots \, | \, \beta_m$$

to a rule of the form:

$$S \to \beta_1 S' \, | \, \cdots \, | \, \beta_m S' \\ S' \to \alpha_1 S' \, | \, \cdots \, | \, \alpha_n S' \, | \, \epsilon$$

In our running example, this would mean to rewrite

$$S \to S + E \, | \, E \\ E \to \text{number} \, | \, (S)$$

to the following left-recursion-eliminating grammar:

$$S \to ES' \\ S' \to +ES' \, | \, \epsilon \\ E \to \text{number} \, | \, (S)$$

### 8.1.2 Predictive Parsing

Given an LL(1) grammar:

- For a given non-terminal, the lookahead symbol uniquely determines the production to apply
- The parsing is driven by a predicitive parsing table
- It is convenient to add a special _end-of-file token_ $ and a start symbol $T$ that requires $

_Example:_ Let us look at the following LL(1) grammar:

$$T \to S\$ \\ S \to ES' \\ S' \to \epsilon \\ S' \to + S \\ E \to \text{number} \, | \, (S)$$

We then propose the following **predictive parsing table:**

|      | **number**          | **+**     | **(**     | **)**          | **$ (EOF)**    |
|------|---------------------|-----------|-----------|----------------|----------------|
| $T$  | $\to S$ $           |           | $\to S$ $ |                |                |
| $S$  | $\to ES'$           |           | $\to ES'$ |                |                |
| $S'$ |                     | $\to + S$ |           | $\to \epsilon$ | $\to \epsilon$ |
| E    | $\to \text{number}$ |           | $\to (S)$ |                |                |

### 8.1.3 Construction of Parse Table

How do we construct the parse table? We examine two possible cases by considering the following production: $A \to \gamma$

#### Case 1

Construct the set of all input tokens that may appear _first_ in strings that can be derived from $\gamma$. Then add the production $\to \gamma$ to the entry $(A, \, \text{token})$ for each such token.

#### Case 2

If $\gamma$ can derive $\epsilon$, then we construct the set of all input tokens that may _follow_ the nonterminal $A$ in the grammar. We then add the production $\to \gamma$ to the entry $(A, \, \text{token})$ for each such token.

_Note:_ If there are two different productions for a given entry, then the grammar is not LL(1).

_Example:_

![](./Figures/CompDes_Fig6-1.PNG)
