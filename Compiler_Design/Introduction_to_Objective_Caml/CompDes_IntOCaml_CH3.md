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

Note the type `int -> int` for the function. The arrow `->` stands for a **function type**. The type before the arrow is the type of the funtion's argument, and the type after the arrow is the type of the result.

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

OCaml provides an alternative syntax for functions using a `let` definition. The formal parameters of the function are lsited in a let-definition after the function name, before the equality symbol:

```ocaml
let identifier v1 v2 ... vn = expression
```

For example, the following definition of the `sum` function is equivalent to the ones above:

```ocaml
# let sum i j = i + j;;
val sum : int -> int -> int = <fun>
```

### 3.1.1 Scoping and nested functions
