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

