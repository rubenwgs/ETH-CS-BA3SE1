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
Warning: this pattern-amtching is not exhaustive.
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

We know that a complete match is not needed, because `i mod 2` is always 0 or 1 - it can't be 2 as the compiler suggest. However, we should still ad a **wildcard** case that raises and exception. The `Invalid_argument` exception is desiigned for this purpose. It takes a string argument that is usually defined to identify the name of the place where the failure occurred:

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

