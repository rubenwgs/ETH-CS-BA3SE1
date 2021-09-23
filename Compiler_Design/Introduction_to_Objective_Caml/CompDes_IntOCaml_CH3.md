**Introduction to Objective Caml - Book chapter 2**

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

