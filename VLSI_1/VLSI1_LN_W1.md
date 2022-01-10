---
title: "VLSI 1 - Notes Week 1"
author: Ruben Schenk, ruben.schenk@inf.ethz.ch
date: September 21, 2021
geometry: margin=2cm
output: pdf_document
---

# Chapter 1 : Introduction to Microelectronics

## 1.1 Economic Impact

**Microelectronics** is acting as a technology driver that enables or expedites a range of industrial, commercial, and service activities. One might consider:

- Computer and software industry
- Telecommunications and media industry
- Commerce, logistics, and transportation
- Natural science and medicine
- Power generation and distribution
- Finance and administration

We make the following observation:

> Observation: Microelectronics is *the* enabler of information technology.

## 1.2 Microelectronics Viewed From Different Perspectives

### 12.2.1 Circuit Complexity

An **integrated circuit (IC)** is an electronic component that incorporates and interconnects a multitude of miniature electronic devices, mostly *transistors*, on a single piece f semiconductor material, typically *silicon*.
Many such circuits are jointly manufactured on a thin semiconductor wafer with a diameter of typically 300 mm before they get cut apart to become (*naked*) **dies**.

The vast majority of ICs or **(micro)chips** get individually encapsulated in a hermetic package before being soldered onto **printed circuit boards (PCB)**.

### 1.2.2 The marketing point of view

#### General-purpose ICs

The function of a *general-purpose IC* is either so simple or so generic that the component is being used in a multitude of applications and typically sold in huge quantities. Examples are gates, flip-flops, adders, RAMs, ROMs, etc.

#### Application-specific integrated circuit

*Application-specific integrated circuits (ASIC)* are being specified and designed with a particular purpose, equipment, or processing algorithm in mind. Today's highly-integrated ASICs are quite complex and include powerful subsystems.

The term **system-on-a-chip (SoC)** has been coined to reflect this development.

We further divide ASICs into the following categories:

- *Application-specific standard product (ASSP):* An ASSP is being sold to various customers for incorporation into their own products. Examples include graphics accelerators, multimedia chips, etc.
- *User-specific integrated circuit (USIC):* A USIC is being designed and produced for a single company that seeks a competitive advantage for their products, they are not intended to be marketed as such. Popular USICs include the Apple A4 SoC introduced with the iPad in 2010 and its successors.
