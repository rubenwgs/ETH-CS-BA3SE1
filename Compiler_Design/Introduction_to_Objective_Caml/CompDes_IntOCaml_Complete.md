**Introduction to Objective Caml - Book chapter 1**

- Author: Ruben Schenk
- Date: 21.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# Chapter 1 : Introduction

**OCaml** is a dialect of the **ML** *(Meta-Language)* family of languages. Throughout this document, we use the term ML to stand for any of the dialects of ML, and OCaml when a feature is specific to OCaml.

- ML is a *functional* language, meaning that functions are treated as first-class values. Functions may be nested, functions may be passed as arguments to other functions, and functions can be stored in data structures.
- ML is *strongly typed*, meaning that the type of every variable and every expression in a program is determined at compile-time.
- The ML type system is *polymorphic*, meaning that it is possible to write programs that work for values of any type.
- ML implements a *pattern matching* mechanism that unifies case analysis and data destructors.
- ML includes an expressive *module system* that allows data structures to be specified and defined abstractly. The module system includes *functors*, which are functions over modules that can be used to produce one data structure from another.
- OCaml includes an *object system*. The module system and object system complement one another: the module system provides data abstraction, and the object system provides inheritance and re-use.
- OCaml includes a compiler that supports *separate compilation*. This makes the development process easier by reducing the amount of code that must be recompiled when a program is modified.
- All the languages in the ML family have a *formal semantics*, which means that programs have a mathematical interpretation, making the programming language easier to understand and explain.

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

**Introduction to Objective Caml - Book chapter 3**

- Author: Ruben Schenk
- Date: 23.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# Chapter 3 : Variables and Functions

In ML, **variables** are *names* for values. Variable bindings are introduced with the `let` keyword. The syntax of a simple top-level definition is as follows:

```ocaml
let identifier = expression
```

Example: The following code defines two variables `x` and `y` and adds them together to get a value for `z`:

```ocaml
# let x = 1;;
val x : int = 1
# let y = 2;;
val y : int = 2
# let z = x + y;;
val z : int = 3
```

Definitions using `let` can also be nested using the form:

```ocaml
let identifier = expression1 in expression2
```

The expression `expression2` is called the *body* of the `let`. The variable named `identifier` is defined as the value of `expression1` within the body. The `identifier` is defined only in the body (`expression2`), but not in `expression1`.

Example:

```ocaml
# let z =
	let x = 1 in
	let y = 2 in
		x + y;;
val z : int = 3
```

**Binding** ist *static* (lexical scoping), meaning that the value associated with a variable is determined by the nearest enclosing definition in the program text.

Example: Consider the following program, where the variable `x` is initially defined to be 7. Within the definition for `y`, the variable `x` is redefined to be 2. The value of `x` in the final expression `x + y` is still 7, and the final result is 10:

```ocaml
# let x = 7 in
  let y =
  	let x = 2 in
		x + 1
  in
  	x + y;;
- : int = 10
```

## 3.1 Functions

Functions are defined with the keyword `fun`:

```ocaml
fun v1 v2 ... vn -> expression
```

The `fun` is followed by a sequence of variables that define the formal parameters of the function, the `->` separator, and then the body of the function `expression`. In ML, functions are values like any other. They may be constructed, passed as arguments, and applied ot arguments, and, like any other value, they may be named by using a `let`:

```ocaml
# let increment = fun i -> i + 1;;
val increment : int -> int = <fun>
```

Note the type `int -> int` for the function. The arrow `->` stands for a **function type**. The type before the arrow is the type of the function's argument, and the type after the arrow is the type of the result.

Functions may also be defined with *multiple arguments*. For example, a function to compute the sum of two integers might be defined as follows:

```ocaml
# let sum = fun i j -> i + j;;
val sum : int -> int -> int = <fun>
# sum 4 5
- : int = 9
```

Strictly speaking, all functions im ML take a single argument. Multiple-argument functions are treated as *nested* functions (this is called "*Currying*"). That is, `sum` is a function that takes a single integer argument, and returns a function that takes another integer argument and returns an integer. The definition of `sum` above is equivalent to:

```ocaml
# let sum = (fun i -> (fun j -> i + j));;
val sum : int -> int -> int = <fun>
```

The application of a multi-argument function to only one argument is called **partial application**:

```ocaml
# let incr = sum 1;;
val incr : int -> int = <fun>
```

OCaml provides an alternative syntax for functions using a `let` definition. The formal parameters of the function are listed in a let-definition after the function name, before the equality symbol:

```ocaml
let identifier v1 v2 ... vn = expression
```

For example, the following definition of the `sum` function is equivalent to the ones above:

```ocaml
# let sum i j = i + j;;
val sum : int -> int -> int = <fun>
```

### 3.1.1 Scoping and nested functions

Functions may be arbitrarily nested. They may also be passed as arguments. The rule for scoping uses static binding: the value of a variable is determined by the code in which a function is defined - not by the code in which a function is evaluated.

To illustrate the scoping rules, let's consider the following definition:

```ocaml
# let i = 5;;
val i : int = 5
# let addi j =
	i + j;;
val addi : int -> int = <fun>
# let i = 7;;
val i : int = 7
# addi 3;;
- : val = 8
```

In the `addi` function, the previous binding defines `i` as 5. The second definition of `i` has no effect on the definition used for `addi`, and the application of `addi` to the argument 3 results in 3 + 5 = 8.

### 3.1.2 Recursive functions

Suppose we want to define a **recursive function**: that is, a function that is used in its own definition. In functional languages, recursion is used to express repetition or looping. For example, the "power" function that computes `x^i` might be defined as follows:

```ocaml
# let rec power i x =
	if i = 0 then
		1.0
	else
		x *. (power (i-1) x);;
val power : int -> float -> float = <fun>
# power 5 2.0;;
- : float = 32.0
```

Note the use of the `rec` modifier after the `let` keyword. Normally, a function is not defined in its own body.

*Mutually recursive definitions** (functions tha call one another) can be defined using the `and` keyword to connect several `let` definitions:

```ocaml
# let rec f i j =
	if i = 0 then
		j
	else
		g (j - 1)
  and g j =
  	if j mode 3 = 0 then
		j
	else
		f (j - 1) j;;
val f : int -> int -> int = <fun>
val g : int -> int = <fun>
# g 5;;
- : int = 3
```

### 3.1.3 Higher order functions

Lets consider a definition where a function is passed as an argument, and another function is returned as a result.

Example: Given an arbitrary function f on the real numbers, an approximate numerical derivative can be defined as follows:

```ocaml
# let dx = 1e-10;;
val dx : float = 1e-10
# let deriv f =
	(fun x -> (f (x +. dx) -. f x) /. dx);;
val deriv : (float -> float) -> float -> float = <fun>
```

Remember, the *arrow associates* to the right, so another way to write the type is `(float -> float) -> (float -> float)`. That is, the derivative is a function that takes a function as an argument, and returns another function.

## 3.2 Variable names

In general, a **variable name** may contain letter (lower and upper case), digits, and the `'` and `_` characters, but it must begin with a lower case letter or the underscore character, and it may not be an underscore all by itself.

In OCaml, sequences of characters from the infix operators, like +, -, *, /, ... are also valid names. Example (Don't use this style in your code):

```ocaml
# let (+) = ( * )
  and (-) = (+)
  and (/) = (-);;
val + : int -> int -> int = <fun>
val - : int -> int -> int = <fun>
val / : int -> int -> int = <fun>
```

The redefinition of infix operators may make sense in some contexts. For example, a program module that defines arithmetic over complex numbers way wish to redefine the arithmetic operators.

## 3.3 Labeled parameters and arguments

OCaml allows functions to have labeled and optional parameters and arguments. **Labeled parameters** are specified with the syntax `~label: pattern`. **Labeled arguments** are similar, `~label: expression`. Labels have the same syntactic conventions as variables, i.e. the label must begin with a lowercase letter or an underscore.

Example:

```ocaml
# let f ~x:i ~y:j = i - j;;
val f : x:int -> y:int -> int = <fun>
# f ~y:1 ~x:2;;
- : int 1
```

**Optional parameters** are like labeled parameters, using a question mark `?` instead of a tilde `~` and specifying an optional value with the syntax `?(label = expression)`. Optional arguments are specified the same way as labeled arguments, or they may be omitted completely.

Example:

```ocaml
# let g ?(x = 1) y = x - y;;
val g : ?x:int -> int -> int = <fun>
# g 1;;
- : int = 0
# g ~x:3 4;;
- : int = -1
```

### 3.3.1 Rules of thumb

Labeled, unlabeled, and optional arguments can be mixed in many different combinations. However, there are some rules of thumb to follow:

- An optional parameter should always be followed by a non-optional parameter (usually unlabeled).
- The order of labeled arguments does not matter, except when a label occurs more than once.
- Labeled and optional arguments should be specified explicitly for higher-order functions.

**Introduction to Objective Caml - Book chapter 4**

- Author: Ruben Schenk
- Date: 24.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# Chapter 4 : Basic pattern matching

One of ML's most powerful features is the use of **pattern matching** to define computations by case analysis. Pattern matching is specified by a `match` expression, which has the following syntax:

```ocaml
match expression with
	| pattern1 -> expression1
	| pattern2 -> expression2
	...
	| patternn -> expressionn
```

When a `match` expression us evaluated, the expression `expression` to be matched is first evaluated, and its value is compared with the pattern in order. If `patterni` is the first pattern to match the value, then the expression `expressioni` is evaluated and returned as the result of the match.

For example, Fibonacci number can be define succinctly using pattern-matching:

```ocaml
let rec fib i =
	match i with
		| 0 -> 0
		| 1 -> 1
		| j -> fib (j - 2) + fib (j - 1)
```

In this code, the argument `i` is compared against the constants 0 and 1. If either of these cases match, the return value is equal to `i`. The final pattern is the variable `j`, which matches any argument. When this pattern is reached, `j` takes on the value of the argument, and the body `fib (j - 2) + fib (j - 1)` computes the returned value.

## 4.1 Functions with matching

It is quite common for the body of an ML function to be a `match` expression. To simplify the syntax somewhat, OCaml allows the use of the keyword `function` to specify a function that is defined by pattern matching. A `function` definition is like a `fun`, where a single argument is used in a pattern match. The `fib` definition using `function` is as follows:

```ocaml
let rec fib = function
	  0 -> 0
	| 1 -> 1
	| i -> fib (i - 2) + fib (i - 1)
```

## 4.2 Pattern expressions

Larger patterns can be constructed in several different ways. The vertical bar `|` can be used to define a `choice` pattern (`pattern1 | pattern2`) that matches any value matching `pattern1` or `pattern2`.

Example: We might write the Fibonacci function somewhat more succinctly by combining the first two cases:

```ocaml
let rec fib i =
	match i with
		(0 | 1) -> i
		| i -> fib (i - 1) + fib (i - 2)
```

PAtters can also be qualified by a predicate with the form `pattern when expression`. This matches the same values as the pattern `pattern`, but only when the predicate `expression` evaluates to true. This way, we can get yet another version of our Fibonacci example:

```ocaml
let rec fib = function
	  i when i < 2 -> i
	| i -> fib (i - 1) + fib (i - 2)
```

## 4.3 Values of other types

Patters can also be used with values having other basic types, like characters, strings, and Boolean values. In addition, multiple patters can be used for a single body.

Together with **pattern ranges** `c1 .. c2`, this allows us to define a simple `is_uppercase` function for example:

```ocaml
let is_uppercase = function
	  'A' .. 'Z' -> true
	| _ -> false
```

## 4.4 Incomplete matches

One might wonder about what happens if the match expression does not include patterns for all the possible cases.

Example: What happens if we leave off the default case in the `is_uppercase` function?

```ocaml
# let is_uppercase = function
	'A' .. 'Z' -> true;;
"Characters 19-49:
Warning: this pattern-matching is not exhaustive.
Here is an example of a value that is not matched:
'a'`"
```

The OCaml compiler and toploop are verbose about inexhaustive patterns. They war when the pattern match is inexhaustive, and even suggest a case that is not matched. Even if we know that a warning is bogus, we should *never ignore a compiler warning*.

Example:

```ocaml
# let is_odd i =
	match i mod 2 with
		  0 -> false
		| 1 -> true;;
"Characters 18-69:
Warning: this pattern-matching is not exhaustive.
Here is an example of a value that is not matched:
2
```

We know that a complete match is not needed, because `i mod 2` is always 0 or 1 - it can't be 2 as the compiler suggest. However, we should still ad a **wildcard** case that raises and exception. The `Invalid_argument` exception is designed for this purpose. It takes a string argument that is usually defined to identify the name of the place where the failure occurred:

```ocaml
let is_odd i =
	match i mod 2 with
		  0 -> false
		| 1 -> true
		| _ -> raise (Invalid_argument "is odd")
```

## 4.5 Patterns are everywhere

It may not be obvious at this point, but patters are used in all the binding mechanisms, including the `let` and `fun` constructions. The general forms are:

```bnf
let pattern = expression
let identifier pattern ... pattern = expression
fun pattern -> expression
```

These forms aren't much use with constants because the pattern match will always be inexhaustive. However, they will be handy when we introduce tuples and records.

**Introduction to Objective Caml - Book chapter 5**

- Author: Ruben Schenk
- Date: 26.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# Chapter 5 : Tuples, lists, and polymorphism

OCaml provides a rich set of types for defining data structures, including tuples, lists, disjoint unions, records, and arrays. In this chapter, we'll look at the simplest part of these - **tuples and lists**.

## 5.1 Polymorphism

As we explore the type system, **polymorphism** will be one of the first concepts that we encounter. The ML languages provide *parametric polymorphism*. That is, types and expressions may be parameterized by type variables.

Example: The identity function can be expressed in OCaml with a single function:

```ocaml
# let identity x = x;;
val identity : 'a -> 'a = <fun>
```

**Type variables** are lowercase identifiers preceded by a single quote `'`. A type variable represents an arbitrary type. The typing `identity : 'a -> 'a` says that the `identity` function takes an argument of some arbitrary type `'a` and returns a value of the same type `'a`.

There may be times when the compiler infers a polymorphic type where one wasn't intended. In this case, the type can be constrained with the syntax `(s : type)`, where `s` can be a pattern or expression:

```ocaml
# let identity:int (i : int) = i;;
val identity_int : int -> int = <fun>
```
If the constraint is for the return type of the function, it can be placed after the final parameter:

```ocaml
# let do_if_int b i j : int = if b then i else j;;
val do_if_int : bool -> int -> int -> int = <fun>
```

### 5.1.1 Value restriction

What happens if we apply the polymorphic `identity` to a value with a polymorphic function type?

```ocaml
# let identity' = identity identity;;
val identity' : '_a -> '_a = <fun>
# identity' 1;;
- : int = 1
# identity';;
- : int -> int = <fun>
```

Note the type assignment `identity' : '_a -> '_a`. The type variables `'_a` specify that the `identity'` function takes an argument of *some* (as yet unknown) type, and returns a value of the same type. When we apply the `identity'` function to a number, the type of the `identity'` function becomes `int -> int`, and it is no longer possible to apply it to any other type.

This behavior is due to the **value restriction**: for an expression to be truly polymorphic, it must be an immutable value, which means 1) it is already fully evaluated, and 2) it can't be modified by an assignment.

The general point of the value restriction is that mutable values are not polymorphic. In addition, function applications are no polymorphic because evaluating the function might create a mutable value or perform an assignment.

It is usually easy to get around the value restriction by using a technique called **eta-expansion**. Suppose we have an expression `e` of function type. The expression `(fun x -> e x)` is nearly equivalent - in fact, it is equivalent if `e` does not contain side-effects. Consider this redefinition of the `identity'` function:

```ocaml
# let identity' = (fun x -> (identity identity) x);;
val identity' : 'a -> 'a = <fun>
```

### 5.1.2 Other kinds of polymorphism

Polymorphism can be a powerful tool. In ML, a single identity function can be defined that works on values of any type. In a non-polymorphic language like C, a separate identity function would have to be defined for each type.

#### Overloading

Another kind of polymorphism present in some languages is **overloading**. Overloading allows function definitions to have the same name if they have different paramatery types.

OCaml does not provide overloading. There are probably two main reasons. One has to do with a technical difficulty. It is hard to provide both type inference and overloading at the same time. Another possible reason for not providing overloading is that programs become more difficult to understand.

#### Subtype polymorphism and dynamic method dispatch

Subtype polymorphism and dynamic method dispatch are concepts used extensively in object-oriented programs. Both kinds of polymorphism are fully supported in OCaml.

## 5.2 Tuples

**Tuples** are the simplest aggregate data type. They correspond to the ordered tuples you have seen in mathematics, or in set theory. A tuple is a collection of values of arbitrary types.

The syntax for a tuples is a sequence of expressions separated by commas. Example:

```ocaml
# let p = 1, "Hello";;
val p : int * string = 1, "Hello"
```

The syntax for the type of a tuple is **\*-separated** list of the types of the components. Tuple can be **deconstructed** by pattern matching with any of the pattern matching constructs like `let, match, fun,` or `function`. Example:

```ocaml
# let x, y = p;;
val x : int = 1
val y : string = "Hello"
```

Tuple patterns in a function parameter must be enclosed in parentheses. Example:

```ocaml
# let t = 1, "Hello", 2.7;;
val t : int * string * float = 1, "Hello", 2.7
# let fst3 (x, _, _) = x;;
val fst3 : 'a * 'b * 'c -> 'a = <fun>
# fst3 t;;
- : int = 1
```

## 5.3 Lists

**Lists** are also used extensively in OCaml programs. A list is a sequence of values of the same type. There are two constructors: the `[]` expression is the empty list, and the `e1 :: e2` expression, called a **cons** operation, creates a **cons cell** - a new list where the first element is `e1` and the rest of the list is `e2`. The shorthand notation `[e1; e2; ...; en]` is identical to `e1 :: e2 :: ... :: []`. Example:

```ocaml
# let l = "Hello" :: "World" :: [];;
val l : string list = ["Hello", "World"]
```

The syntax for the **type of a list** with elements of type `t` is `t list`. The type `list` is an example of a *parametrized type*. An `'a list` is a list containing elements of some type `'a` (but all elements have the same type!).

Lists can be deconstructed using pattern matching. For example, here is a function that adds up all the numbers in an `int list`:

```ocaml
# let rec sum = function
	  [] -> 0
	| i :: l -> i + sum l;;
val sum : int list -> int = <fun>
# sum [1 ; 2; 3; 4];;
- : int = 10
```

The **map** operation applies a function `f` to each element of a list `l` and might be defined as follows:

```ocaml
# let rec map f = function
	  [] -> []
	| x :: l -> f x :: map f l
val map : ('a -> 'b) -> 'a list -> 'b list = <fun>
# map (fun i -> (float_of_int i) +. 0.5) [1; 2; 3; 4];;
- : int list = [1.5; 2.5; 3.5; 4.5]
```

Lists are also combined with tuple to represent sets of values, or a **key-value** relationship like dictionaries. For example, the `List.assoc` function returns the value associated with a key in a list of key-value pairs. This function might be defined as follows:

```ocaml
let rec assoc key = function
	(key2, value) :: l ->
		if key2 = key then
			value
		else
			assoc key l
	| [] ->
		raise Not_found
```

## 5.4 Tail recursion

We have seen several examples of recursive functions so far. A function is **recursive** if it calls itself. Recursion is the primary means for specifying looping and iteration, making it one of the most important concepts in functional programming.

**Tail recursion** is a specific kind of recursion where the value produced by a recursive call is returned directly by the caller without further computation.

The implementation `fact2` illustrates a standard "trick", where an extra argument, often called an **accumulator**, is used to collect the result of the computation. The function `loop` is *tail-recursive* because the result of the recursive call is returned directly by the caller:

```ocaml
let fact2 i =
	let rec loop accum i =
		if i = 0 then
			accum
		else
			loop (i * accum) (i - 1)
	in
		loop 1
```

### 5.4.1 Optimization of tail-recursion

Tail-recursion is important because it can be optimized effectively by the compiler. In general, a non-tail-recursive function will require stack space linear in the number of recursive calls.

In contrast, the result of a tail-recursive call is to be returned directly by the caller, so instead of allocating stack space for the arguments, it is sufficient to overwrite the caller's state. That is, using `<-` to represent assignment, the compiler can translate the code as follows:

```ocaml
let rec loop accum i =
	if i = 0 then
		accum
	else
		accum <- i * accum;
		i <- i - 1;
		goto loop
```

### 5.4.2 Lists and tail recursion

Tail-recursion is especially important when programming with lists, because otherwise functions would usually take stack space linear in the length of the list. Not only would that be slow, but it would also mean that the list length is limited by the maximum stack size.

One standard approach is to use an accumulator that collects the result in reverse order. For example, consider the following implementation of a function map:

```ocaml
let rec map f = function
	  h :: t -> f h :: map f t
	| [] -> []
```

The function is simple, but not tail-recursive. To obtain a tail recursive version, we collect the result in an argument `accum`:

```ocaml
let rec rev accum function
	  h :: t -> rev (h :: accum) t
	| [] -> accum

let rec rev_map f accum = function
	  h :: t -> rev_map f (f h :: accum) t
	| [] -> accum

let map f l = rev [] (rev_map f [] l)
```

Note that the result is collected in `accum` is in *reverse* order, so it must be reversed (with the function `rev`) at the end of the computation.

**Introduction to Objective Caml - Book chapter 6**

- Author: Ruben Schenk
- Date: 29.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# Chapter 6 : Unions

Disjoint unions, also called *tagged unions, variant records,* or *algebraic data types*, are an important part of the OCaml type system. A **disjoint union**, or union for short, represents the union of several different types, where each of the parts is given an unique, explicit name.

OCaml allows the definition of *exact* and *open* union types. The following syntax is used for an exact union type:

```ocaml
type typename =
	| Identifier1 of type1
	| Identifier2 of type2
	...
	| Identifiern of typen
```

Let's look at a simple example using unions, where we wish to define a numeric type that is either a value of type `int` or `float` or a canonical value `Zero`:

```ocaml
# type number =
	| Zero
	| Integer of int
	| Real of float;;
type number = Zero | Integer of int | Real of float
```

Values in disjoint union are formed by applying a constructor to an expression of the appropriate type:

```ocaml
# let zero = Zero;;
val zero : number = Zero
# let i = Integer 1;;
val i = number = Integer 1
```

Patterns use the constructor name. Example:

```ocaml
# let float_of_number = function
	| Zero -> 0.0
	| Integer i -> float_of_int i
	| Real x -> x;;
```

## 6.1 Binary trees

Binary trees are often used for representing collections of data. For our purpose, a **binary tree** is an acyclic graph, where each node has either zero or two nodes called *children*. If `n2` is a child of `n1`, then `n1` is called the *parent* of `n2`. One node, called the *root*, has ni parents. All other nodes have exactly one parent.

The following definition defines the type for a labeled tree:

```ocaml
# let 'a tree =
	| Node of 'a * 'a tree * 'a tree
	| Leaf;;
type 'a tree = | Node of 'a * 'a tree * 'a tree | Leaf
```

## 6.2 -- 6.4 (Left out)

## 6.5 Open union types

OCaml defines a second kind of union type where the type is open -- that is, subsequent definitions may add more cases to the type. The syntax is similar to the exact definition discussed previously, but the constructor names are prefixed with a back-quote (`'`) symbol, and the type definition is enclosed in `[> ...]` brackets.

For example, let's build an extensible version of the numbers from the first example in this chapter. Initially, we might define the implementation for `'Integer` values:

```ocaml
# let string_of_number1 n =
	match n with
		| 'Integer i -> string_of_int i
		| _ -> raise (Invalid_argument "unknown number");;
val string_of_number1 : [> ' Integer of int] -> string = <fun>
```

Later, we might want to extend the definition to include a constructor `'Real` for floating-point values:

```ocaml
# let string_of_number2 n =
	match n with
		| 'Real x -> string_of_float x
		| _ -> string_of_number1 n;;
val string_of_number2 : [> 'Integer of int | 'Real of float] -> string = <fun>
```

### 6.5.1 Type definitions for open types

Something strange comes up when we try to write a type definition using an open union type:

```ocaml
# type number = [> 'Integer of int | 'Real of float];;
"Characters 4-50:
	type number = [> 'Integer of int | 'Real of float];;
			^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
A type variable is unbound in this type declaration.
In definition [> 'Integer of int | 'Real of float] as 'a
the variable 'a is unbound."
```

Type theoretically, any function defined over an open type must be polymorphic over the unspecified cases. Technically, this amounts to the same thing as saying that an open type is some type `'a` that includes at least the cases specified in the definition (and more). In order for the type definition to be accepted, we must write the type variable explicitly:

```ocaml
# type 'a number = [> 'Integer of int | 'Real of float] as 'a;;
type 'a number = 'a constraint 'a = [> 'Integer of int | 'Real of float]
```

The `as` form and the `constraint` form are two different ways of writing the same thing. In both cases, the type that is being defined is the type `'a` with an additional constraint that it includes at least the cases `'Integer of int | 'Real of float`.

### 6.5.2 Closed union types

The notation `[> ...]` is meant to suggest that the actual type includes the specified cases, and more. In OCaml, one can also write a **closed type** as `[< ...]` to mean that the type includes the specified values *and no more*.

## 6.6 Some common built-in unions

**Booleans:**

```ocaml
# type bool =
	| true
	| false;;
type bool = | true | false
```

**Lists:**

```ocaml
# type 'a list =
	| []
	| :: of 'a * 'a list;;
```

The `'a list` type is primitive in this case because `[]` is not considered a legal constructor name.

**Options:**

```ocaml
# type 'a option =
	| None
	| Some of 'a;;
type 'a option = | None | Some of 'a
```

The `None` case is intended to represent a `NIL` case, while the `Some` case handles non-`NIL` values.
