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
If the constraint is for the return type of the function, it can be palced after the final parameter:

```ocaml
# let do_if_int b i j : int = if b then i else j;;
val do_if_int : bool -> int -> int -> int = <fun>
```

### 5.1.1 Value restriction

What happens if we apply the polymorphic `identity` to a value with a polymorhpic function type?

```ocaml
# let identity' = identity indentity;;
val identity' : '_a -> '_a = <fun>
# identity' 1;;
- : int = 1
# identity';;
- : int -> int = <fun>
```

Note the type assignment `indentity' : '_a -> '_a`. The type variables `'_a` specify that the `identity'` function takes an argument of *some* (as yet unknown) type, and returns a value of the same type. When we apply the `identity'` function to a number, the type of the `identity'` function becomes `int -> int`, and it is no longer possible to apply it to any other type.

This behavior is due to the **value restriction**: for an expression to be truly polymorphic, it must be an immutable value, which means 1) it is already fully evaluated, and 2) it can't be modified by an assignment.

The general point of the value restriction is that mutable values are not polymorphic. In addition, function applications are no polymorhpic because evaluating the function might create a mutable value or perform an assignment.

It is usually easy to get around the value restriction by using a technique called **eta-expansion**. Suppose we have an expression `e` of function type. The expression `(fun x -> e x)` is nearly equivalent - in fact, it is equivalent if `e` does not contain side-effects. Consider this redefinition of the `identity'` function:

```ocaml
# let identity' = (fun x -> (identity identity) x);;
val identity' : 'a -> 'a = <fun>
```

### 5.1.2 Other kinds of polymorphism

Polymorphism can be a powerful tool. In ML, a single identity function can be defined that works on values of any type. In a non-polymorhpic language like C, a separate identity function would have to be defined for each type.

#### Overloading

Another kind of polymorphism present in some languages is **overloading**. Overloading allows function definitions to have the same name if they have different paramatery types.

OCaml does not provide overlaoding. There are probably two main reasons. One has to do with a technical difficulty. It is hard to provide both type inference and overlaoding at the same time. Another possible reason for not providing overlaoding is that programs become more difficult to understand.

#### Subtype polymorphism and dynamic method dispatch

Subtype polymorphism and dynamic method dispatch are concepts used extensively in object-oriented programs. Both kinds of poylmorphism are fully supported in OCaml.

## 5.2 Tuples

**Tuples** are the simplest ahhrehate data type. They correspond to the ordered tuples you have seen in mathematics, or in set theory. A tuple is a collection of values of arbitrary types.

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

Tuple patterns in a function parameter must be enclosed in parantheses. Example:

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

We have seen several examples of recursive functions so far. A function is **recursive** if it calls itself. Recursion is the primary meands for specifying looping and iteration, making it one of the most important concepts in functional programming.

**Tail recursion** is a specific kind of recursion where the value produced by a recursive call is retunred directly by the caller without further computation.

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

### 5.4.1 Optimization of tail-recusrion

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

The function is simple, but not tail-recursive. To obtain a tail recursive version, we collect the result in ana rgument `accum`:

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

