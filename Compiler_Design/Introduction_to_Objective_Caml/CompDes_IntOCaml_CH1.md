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
