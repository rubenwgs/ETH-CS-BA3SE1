---
title: "Embedded Systems - Complete Summary (WIP)"
author: Ruben Schenk, ruben.schenk@inf.ethz.ch
date: January 13, 2022
geometry: margin=2cm
output: pdf_document
---

# Chapter 1: Introduction

## 1.1 Impact

**Embedded systems** are information processing systems embedded into a larger product. Usually they use feedback to influence the dynamics of the physical world by taking smart decisions in the cyber world.

![](./Figures/EBDS_Fig1-1.PNG){width=50%}

## 1.2 Facts

Embedded systems are often _reactive:_ reactive systems must react to stimuli from the system environment.

> "A reactive system is one which is in continual interaction with its environment and executes at a pace determined by that environment" - Bergé, 1995

ES often must meet _real-time constraints:_ For hard real-time systems, right answers arriving too late are wrong. All other time-constraints are called soft. A _guaranteed system response_ has to be explained without statistical arguments.

> "A real-time constraint is called hard, if not meeting that constraint could result in a catastrophe" - Kopetz, 1997

It is essential to _predict_ how a cyber-physical system (CPS) is going to behave under any circumstances before it is deployed. CPS must _operate dependably,_ safely, securely, efficiently and in real-time.

ES must be _efficient:_

- Energy efficient
- Code-size and data memory efficient
- Run-time efficient
- Weight efficient
- Cost efficient

ES are often _specialized_ towards a certain application or application domain: Knowledge about the expected behavior and the system environment at design time is exploited to _minimize resource usage_ and to _maximize predictability and reliability._

## 1.3 Trends

Some trends of embedded systems:

- ES communicating with each other, with servers or with the cloud. Communication is increasingly.
- Higher degree of integration on a single chip or integrated components:
    - Memory + processor + I/O units + communication
    - Use of networks-on-chip for communication between units
    - Use of homogeneous or heterogeneous multiprocessor system on a chip (MPSoC)
- Low power and energy constraints (especially for portable or unattended devices) are increasingly important, as well as temperature constraints
- There is increasing interest in energy harvesting to achieve long term autonomous operation

# Chapter 2: Software Development

_Reminder:_ Compilation of a C program to machine language works as follows:

![](./Figures/EBDS_Fig1-2.PNG){width=50%}

The main chain-of-events for **embedded software developments** is given by the following diagram:

![](./Figures/EBDS_Fig1-3.PNG){width=75%}

Software development is nowadays usually done with the support of an IDE:

- Edit and build the code
- Debug and validate the code

A better overview on how this works with embedded systems is given below:

![](./Figures/EBDS_Fig1-4.PNG){width=75%}
