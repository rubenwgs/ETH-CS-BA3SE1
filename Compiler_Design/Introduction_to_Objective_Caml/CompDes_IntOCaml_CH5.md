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

