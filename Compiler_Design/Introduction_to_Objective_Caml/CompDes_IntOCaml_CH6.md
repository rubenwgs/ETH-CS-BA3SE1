**Introduction to Objective Caml - Book chapter 6**

- Author: Ruben Schenk
- Date: 29.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# Chapter 6 : Unions

Dsjoint unions, also called *tagged unions, variant records,* or *algebraic data types*, are an important part of the OCaml type system. A **disjoint union**, or union for short, represents the union of several different types, where each of the parts is given an unique, explicit name.

OCaml allows the definition of *exact* and *open* union types. The following syntax is used for an exact union type:

```ocaml
type typename =
	| Identifier1 of type1
	| Identifier2 of type2
	...
	| Identifiern of typen
```

Let's look at a simple example using unions, where we wish to define a nummeric type that is either a value of type `int` or `float` or a canonical value `Zero`:

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

Binary trees are often used for representing collections of data. For our prupose, a **binary tree** is an acyclic graph, where each node has either zero or two nodes called *children*. If `n2` is a child of `n1`, then `n1` is called the *parent* of `n2`. One node, called the *root*, has ni parents. All other nodes have exactly one parent.

The following definition defines the type for a labeled tree:

```ocaml
# let 'a tree =
	| Node of 'a * 'a tree * 'a tree
	| Leaf;;
type 'a tree = | Node of 'a * 'a tree * 'a tree | Leaf
```

## 6.2 -- 6.4 (Left out)

## 6.5 Open union types

OCaml defines a second kind of union type where the type is open -- that is, subsequent definitions may add more cases to the type. The syntax is similar to the exact definition discussed previously, but the constructor names are prefixed with a back-quote (`'`) symbol, and the type definition is enclsoed in `[> ...]` brackets.

For example, let's build an extensible version of the numbers from the first example in this chapter. Initially, we migh define the implementation for `'Integer` values:

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

Type theoretically, any function defined over an open type must be polymorphic over the unspecified cases. Technically, this amounts to the same thing as saying that an open type is some type `'a` that includes at least the cases specified in the definition (and more). In order for the type definition to be accepted, we must write the type variable explicitely:

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

