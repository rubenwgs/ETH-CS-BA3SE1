**Introduction to Objective Caml - Book chapter 2**

- Author: Ruben Schenk
- Date: 22.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# Chapter 2 : Simple Expressions

Most functional programming implementations include a runtime environment and a garbage collector. They often include an evaluator that can be used to interact with the system, called **toploop**. OCaml provides all of those and by default, the toploop is called `ocaml`. Expressions in the toploop are terminated by a double-semicolon `;;`.

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

The integer arithmetic operators *do not work* with floating point values. The operators for floating-point numbers include a `.` as follows:

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

In addition, there are several kinds of escape sequences with an alternate syntax. Each escape sequence begins with the backslash character `\`.

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

Examples:

```ocaml
# '\120';;
- : char = 'x'
# Char.code 'x';;
- : int = 120
# '\x7e';;
- : char = '~'
```

### 2.2.5 `string` : character strings

In OCaml, **character strings** belong to a primitive type `string`. Character strings are not arrays of characters, and they do not use the null-character `'\000'` for termination.

The syntax for strings uses the double quote symbol `"` as a delimiter. Characters in the string may be specified using the same escape sequences used for characters.

The operator `^` performs **string concatenation**:

```ocaml
# Hello " ^ "world\n";;
- : string = "Hello world\n"
# "\072\105";;
- : string = "Hi"
```

Strings also support **random access**. The expression `s.[i]` returns the i-th character from string `s`, and the expression `s.[i] <- c` replaces the i-th character in string `s` by `c`, returning a `unit` value.

```ocaml
# "Hello".[1];;
- : char = 'e'
# "Hello".[0] <- 'h';;
- : unit = ()
# String.length "Ab\000cd";;
- : int = 5
```

### 2.2.6 `bool` : the Boolean values

The `bool` type includes the Boolean values `true` and `false`. Logical negation is done with the `not` function. There are several relations (such as `x = y, x == y, x != y, x <> y, x >= y`, etc), returning `true` if the comparison holds and `false` otherwise.

Examples:

```ocaml
# 2 + 6 = 8;;
- : bool = true
# 1.0 = 1.0;;
- : bool = true;
# 1.0 == 1.0;;
- : bool = false
# 2 == 1 + 1;;
- : bool = true
```

> Remark: The comparison `1.0 == 1.0` in this case returns `false` because the 2 floating-point numbers are represented by different values in memory, but it performs "normal" comparison in `int` values.

There are two **logical operators**: `&&` is *conjunction* (which can also be written as `&`), and `||` is *disjunction* (which can also be written as `or`).

```ocaml
# 1 < 2 || (1 / 0) > 0;;
- : bool = true
# 1 < 2 && (1 / 0) > 0;;
Exception: Division_by_zero
```

> Remark: Both operators are the "*short-circuit*" versions: the second clause is not evaluated if the result can be determined from the first clause.

## 2.3 Operator precedences

The **precedences** of operators on the basic types are as follows, listen in increasing order:

| Operators                     | Associativity |
| :---------------------------- | :------------ |
| `|| &&` 	       	        | left	        |
| `= == != <> < <= > >=`        | left          |
| `+ - +. -.`                   | left          |
| `* / *. /. mod land lor lxor` | left          |
| `lsl lsr asr`                 | right         |
| `lnot`                        | left          |
| `~- - ~-. -.`                 | right         |

## 2.4 The OCaml type system

The ML languages are **statically and strictly typed**. In addition, every expression has exactly one type. The strictly typed languages are **safe**.

But what is "safety"? An approximate definition is that a valid program will never fault because of an invalid machine operation. All memory accesses will be valid. ML guarantees safety by proving that every program that passes the type checker can never produce a machine fault.

Here are some rules about **type checking**:

1. Every expression has exactly one type.
2. When an expression is evaluated, one of four things may happen:
	- it may evaluate to a *value* of the same type as the expression
	- it may raise an exception
	- it may not terminate
	- it may exit

One of the important points here is that there are no "*pure commands*". Even assignments produce a value - although it has the trivial `unit` type.

Example:

```ocaml
% cat -b x.ml
	1	if 1 < 2 then
	2		1
% ocamlc -c x.ml
"File 'x.ml', line 2, characters 3.4:
This expression has type int but is here used with type unit"
```

In this case, the expression 1 is flagged as a type error, because it does not have the same type as the omitted `else` branch (which in this case has type `unit`).

## 2.5 Compiling your code

If you wish to compile your code, you should place it in a file with the `.ml` suffix. In INRIA OCaml there are two compilers: `ocamlc` compiles to byte-code, and `ocamlopt` compiles to native machine code. The native code is several times faster, but compile times is longer. The double-semicolon terminators are not necessary in `.ml` source files, you may omit them if the source code is unambiguous.

- To compile a single executable, use `ocamlc -g -c file.ml`. This will produce a file `file.cmo`. The `ocamlopt` program produces a file `file.cmx`. The `-g` option causes debugging information to be included in the output file.
- To link together several files into a single executable, use `ocamlc` to link the `.cmo` files. Normally, you would also specify the `-o program_file` option to specify the output file. For example, if you have two program files `x.cmo` and `y.cmo`, the command would be:
	```ocaml
	% ocamlc -g -o program x.cmo y.cmo
	% ./program
	...
	```

There is also a **debugger** `ocamldebug` that you can use to debug your programs. The usage is a lot like `gdb`, with one major exception: execution can go backwards. The `back` command will go back one instruction.
