**Introduction to Objective Caml - Book chapter 1**

- Author: Ruben Schenk
- Date: 22.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# Chapter 2 : Simple Expressions

Most functional programming implementations include a runtime environment and a garbage collector. They often include an evaluator that can be used to interact with the system, called **toploop**. OCaml provides all of those and by default, the toploop is called `ocaml`. Expressions in the toploop are terminated by a double-semicolo `;;`.

Example:

```ocaml
% ocaml
	Objective Caml version 4.12.0
# 1 + 4;;
- : int = 5
```

## 2.1 Comment convention

In OCaml, **comments** are enclosed in matching `(*` and `*)` pairs.

Example:

```ocaml
# 1 (* this is a comment*) + 4;;
- : int = 5
```

## 2.2 Basic expressions

OCaml is a **strongly typed** language. In OCaml every valid expression must have a type, and expressions of one type may not be used as expressions in another type. OCaml uses **type inference** to figure out the types for you.

### 2.2.1 `unit`: the singleton type

The simplest type in OCaml is the `unit` type, which contains one element: `()`. It is commonly used as the value of a procedure that computes by side-effect. It corresponds to the type `void` in C.

### 2.2.2 `int` : the integers

The type `int` is the type of signed integers: ..., -2, -1, 0, 1, 2,... Integers are usually specified in decimal, but there are several alternate forms.

There are the usual operations on `int`s, including arithmetic and bitwise operations.






