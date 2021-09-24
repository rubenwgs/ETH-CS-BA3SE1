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

For example, Fibonacci number can be define succinctly using patternmatching:

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

