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

Example:

```ocaml
# 12345 + 1;;
- : int = 12346
# 0b1110 lxor 0b1010;;
- : int = 4
#0x7fffffff;;
- : int = -1
```

### 2.2.3 `float` : the floating-point numbers

The syntax of a **floating point** requires a decimal point, an exponent (base 10) denoted by an `E` or `e`, or both. A digit is required before the decimal point, but not after. Some examples: `0.2, 2e7, 31.4159E-1`

The integer arithmetic operators *do not work* with floating point values. The operators for flaoting-point numbers include a `.` as follows:

```ocaml
-.x (* or *) ~-.x	(* floating-point negation *)
x +. x			(* floating-point addition *)
x -. y			(* floating-point subtraction *)
x *. y			(* floating-point multiplication *)
x /. y			(* floating-point division *)
int_of_float x		(* float to int conversion *)
float_of_int x		(* int to float conversion *)
```

Examples:

```ocaml
# 31.1415E-1;;
- : float = 3.14159
# float_of_int 1;;
- : float = 1.
# int_of_float 1.2;;
- : int = 1
```

### 2.2.2 `char` : the characters

The **character** type specifies characters from the ASCII character set. The syntax for a character constant uses the single quote symbol `'c'`.

In addition, there are several kinds of escape sequences with an alernate syntax. Each escape sequence begins with the backshlash character `\`.

```ocaml
'\\'	(* The backslash character itself *)
'\'	(* The single-quote character *)
'\t'	(* The tab character *)
'\r'	(* The carriage-return character *)
'\n'	(* The newline character *)
'\b'	(* The backspace character *)
'\ddd'	(* A decimal escape sequence *)
'\xhh'	(* A hexadecimal escape sequence *)
```

