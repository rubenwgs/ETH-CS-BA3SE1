---
title: "Computer Systems - Notes Week 9"
author: Ruben Schenk, ruben.schenk@inf.ethz.ch
date: January 6, 2022
geometry: margin=2cm
output: pdf_document
---

# Chapter 17: Byzantine Agreement

A node which can have arbitrary behavior is called **byzantine.** This includes "anything imaginable", e.g. not sending any messages at all, or sending different and wrong messages to different neighbors, or lying about the input value.

_Remarks:_

- Byzantine behavior also includes collusion, i.e. all byzantine nodes are being controlled by the same adversary.
- We call non-byzantine nodes _correct_ nodes.

Finding consensus as defined in chapter 16 in a system with byzantine nodes is called **byzantine agreement.** An algorithm is $f$-resilient if it still works correctly with $f$ byzantine nodes.

## 17.1 Validity

For _any-input validity,_ the decision value must be the input value of any node. For _correct-input validity,_ the decision value must be the input value of a correct node. Unfortunately, implementing correct-input validity does not seem to be easy, as a byzantine node following the protocol but lying about its input value is indistinguishable from a correct node.

If all correct nodes start with the same input $v$, then under _all-same validity_ the decision must be $v$. If the input values are not binary, but for example from sensors that deliver values in $\mathbb{R}$, all-same validity is in most scenarios not really useful.

If the input values are orderable, e.g. $v \in \mathbb{R}$, byzantine outliers can be prevented by agreeing on a value close to the _median_ of the correct input values, which we call **median validity.** How close the values is depends on the number of byzantine nodes $f$.

In the **synchronous model,** nodes operate in synchronous rounds. In each round, each node may send a message to the other nodes, receive the messages sent by the other nodes, and so some local computation. For algorithms in the synchronous model, the **runtime** is simply the number of rounds from the start of the execution to its completion in the worst case.

## 17.2 How Many Byzantine Nodes?

```pseudo

```