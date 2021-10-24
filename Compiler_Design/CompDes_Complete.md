**Compiler Design — Lecture note week 1**

- Author: Ruben Schenk
- Date: 23.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# 1. Introduction

## 1.1 What is a Compiler?

A **compiler** is what we as developers usually see as a black box. We might use the C compiler `gcc` int the following way:

```c
#include <stdio.h>

int main() {
	printf("Hello world!\n");
	return 0;
}
```

```console
% gcc -o hello hello.c
% ./hello
Hello world!
%
```

The goal of a **compiler** is to *translate one programming language to another*, typically that is translating a high-level source code to a low-level machine code (**object code**).

#### Source Code

**Source code** is optimized for human readability. This means it is:

- Expressive: match human ideas of grammar/syntax/meaning
- Redundant: more information than needed to help catch errors
- Abstract: exact computations possibly not fully determined by code

Following an example of some C source code:

```c
#include <stdio.h>

int factorial(int n) {
	int acc = 1;
	while(n > 0) {
		acc = acc * n;
		n = n - 1;
	}
	return acc;
}

int main(int argc, char *argv[]) {
	printf("factorial(6) = %d\n", factorial(6));
}
```

#### Low-level code

**Low-level code** is optimized for hardware. This means that:

- Machine code is hard to read for humans
- Redundancy and ambiguity is reduced
- Abstractions and information about intent is lost

#### Compiler Bug Types

When compiling code we might encounter different bugs. We can distinguish them into different types:

- **Miscompilation (wrong code bug)**: The compiler, maybe after enabling some optimization, gives us back a wrong code.
- **Internal compilation error (ICE)**: The compiler crashes on trying to compile some source code.
- **Compiler hang (slow compilation)**: The compiler is stuck or takes a very long time trying to compile some simple source code.
- **Missed optimizations**: The compiler might miss optimizations it could do to some given source code.

## 1.2 Compiler Structure

The following figure shows a simplified view of the **compiler structure**:

![Simplified Compiler Structure](./Figures/CompDes_Fig_W1_1.PNG)

The typical **compiler stages** are as follows:

- Lexing -> token stream
- Parsing -> abstract syntax
- Disambiguation -> abstract syntax
- Semantic analysis -> annotated abstract syntax
- Translation -> intermediate code
- Control-flow analysis -> control-flow graph
- Data-flow analysis -> interference graph
- Register allocation -> assembly
- Code emission

**Optimization** may be done at *many* of these stages!

Another simplified view on the compilation and execution is given by the following figure:

![Overview of Compilation and Execution](./Figures/CompDes_Fig_W1_2.PNG)

# 2. OCaml

## 2.1 OCaml Tools

The programming language **OCaml** includes the following tools:

- `ocaml` -> The top-level interactive loop
- `ocamlc` -> The byte code compiler
- `ocamlopt` -> The native code compiler
- `ocamldep` -> The dependency analyzer
- `ocamldoc` -> The documentation generator
- `ocamllex` -> The lexer generator
- `ocamlyacc` -> The parser generator

In addition to the above-mentioned tools, one might use the following additional tools:

- `menhir` -> A more modern parser generator
- `ocamlbuild` -> A compilation manager
- `utop` -> A more fully-featured interactive top-level
- `opam` -> Package manager

## 2.2 OCaml Characteristics

OCaml has the following two main distinguishing characteristics:

#### Functional and mostly pure

- Programs manipulate values rather than issue commands
- Functions are first-class entities
- Results of computations can be "named" using `let`
- Has relatively few "side effects"

#### Strongly and statically typed

- Compiler typechecks every expression of the program, issues errors if it can't prove that the program is type safe
- Good support for type inference and generic polymorphic types
- Rich user-defined "algebraic data types" with pervasive use of pattern matching
- Very strong and flexible module system for constructing large projects

## 2.3 Factorial on OCaml

Consider the following implementation of the factorial function in a hypothetical programming language:

```pseudo
x = 6;
ANS = 1;
whileNZ (x) {
	ANS = ANS * x;
	x = x + -1;
}
```

For this hypothetical language, we need to describe the following two constructs:

- **Syntax**: which sequences of characters count as a leg program?
- **Semantics**: what is the meaning of a legal program?

## 2.4 Grammar for a Simple Language

### 2.4.1 Grammar and Interpreter

We introduce the following two **nonterminals** for our simple language:

```bnf
<exp> ::=
	|	<X>
	|	<exp> + <exp>
	|	<exp> * <exp>
	|	<exp> < <exp>
	|	<integer constant>
	|	(<exp>)

<cmd> ::=
	|	skip
	|	<X> = <exp>
	|	ifNZ <exp> { <cmd> } else { <cmd> }
	|	whileNZ <exp> { <cmd> }
	|	<cmd>; <cmd>
```

The above given syntax (or *grammar*) for a simple imperative language has the following properties:

- It is written in *Backus-Naur form*
- The symbols `::=`, `|`, and `<...>` are part of the **meta language**
- Keywords like `skip`, `ifNZ`, and `whileNZ` and symbols like `{` and `+` are part of the **object language**

#### Example: Define Grammar in OCaml

With the above definition of our grammar in BNF, we can transform this into OCaml. It looks the following way:

```ocaml
type var = string;

type exp =
	| Var of var
	| Add of (exp * exp)
	| Mul of (exp * exp)
	| Lt  of (exp * exp)
	| Lit of int

type cmd =
	| Skip
	| Assn    of var * exp
	| IfNZ    of exp * cmd * cmd
	| WhileNZ of exp * cmd
	| Seq     of cmd * cmd
```

With the definition of our hypothetical language, we can build a command for the factorial function in OCaml the following way:

```ocaml
let factorial : cmd =
	let x = "X" in
	let ans = "ANS" in
	Seq (Assn (x, Lit 6)),
		Seq (Assn (ans, Lit 1)),
			WhileNZ(Var x,
				Seq (Assn (ans, Mul (Var and, Var x)),
					Assn (x, Add (Var x, Lit (-1)))))
```

With the above two examples we can now finally build a simple **interpreter** for our simple language:

```ocaml
type state = var -> int

let rec interpret_exp (s:state) (e:exp) : int =
	match e with
	| Var x -> s x
	| Add (e1, e2) -> (interpret_exp s e1) + (interpret_exp s e2)
	| Mul (e1, e2) -> (interpret_exp s e1) * (interpret_exp s e2)
	| Lt  (e1, e2) -> if (interpret_exp s e1) < (interpret_exp s e2) then 1 else 0
	| Lit n -> n

let update s x v =
	fun y -> if x = y then v else s y

let rec interpret_cmd (s:state) (c:cmd) : state =
	match c with
	| Skip -> s
	| Assn (x, e1) ->
	  let v = interpret_exp s e1 in
	  update s x v
	| IfNZ (e1, c1, c2) ->
	  if (interpret_exp s e1) = 0 then interpret_cmd s c2 else interpret_cmd s c1
	| WhileNZ (e, c) ->
	  if (interpret_exp s e) = 0 then s else interpret_cmd s (Seq (c, WhileNZ (e, c)))
	| Seq (c1, c2) ->
```

#### Main Function

We might write and invoke a `main` function in the following way:

```ocaml
let main() =
	Printf.printf("Hello world!")

(* let _ = main () *)
;; main ()
```

### 2.4.2 Optimizer

We might `optimize` our interpreter. This could be that we evaluate simple expressions ourselves, instead of letting the compiler evaluate it completely. Examples:

```ocaml
(*
e + 0 -> e
e * 1 -> e
e * 0 -> 0
0 + e -> e
e - e -> 0
...

skip; c -> c

ifNZ 0 then c1 else c2 -> c2
ifNZ 1 then c1 else c2 -> c1

whileNZ 0 c -> skip
*)
```

In general, we want to make our program as simple as possible based on some rewriting rules before interpreting it.

We might realize an *optimizer for commands* in the following way:

```ocaml
let rec optimize_cmd (c:cmd) : cmd =
	match c with
		| Assn(x, Var y) -> if x = y then Skip else c
		| Assn(_, _) -> c
		| WhileNZ(Lit 0, c) -> Skip
		| WhileNZ(Lit _, c) -> loop
		| WhileNZ(e, c) -> WhileNZ(e, optimize_cmd c)
		| Skip -> Skip
		| IfNZ(Lit 0, c1, c2) -> optimize_cmd c2
		| IfNZ(Lit _, c1, c2) -> optimize_cmd c1
		| IfNZ(e, c1, c2) -> IfNZ(e, optimize_cmd c1, optimize_cmd c2)
		| Seq(c1, c2) ->
			begin match (optimize_cmd c1, optimize_cmd c2) with
				| (Skip, c2') -> c2'
				| (c1', Skip) -> c1'
				| (c1', c2') -> Seq(c1', c2')
			end
```

### 2.4.3 Translator

We might imagine trying to build a translator from *Simple* to *OCaml*. This process consists of several different steps.

#### Set of Variables

In the following code we explore how to get the set of variables from a given expression.

```ocaml
;; open Simple

module OrderedVars = struct
	type t = var
	let compare = String.compare
end

module VSet = Set.Make(OrderedVars)
let (++) = VSet.union

(* Calculate the set of variables mentioned in either an expression or a command *)

let rec vars_of_exp (e:exp) : VSet.t =
	begin match e with
		| Var x -> VSet.singleton x
		| Add(e1, e2)
		| Mul(e1, e2)
		| Lt (e1, e2) ->
			(vars_of_exp e1) ++ (vars_of_exp e2)
		| Lit _ -> VSet.empty
	end

let rec vars_of_cmd (c:cmd) : VSet.t =
	begin match c with
		| Skip -> VSet.empty
		| Assn(x, e) -> (VSet.singleton x) ++ (vars_of_exp e)
		| IfNZ(e, c1, c2) ->
			(vars_of_exp e) ++ (vars_of_cmd c1) ++ (vars_of_cmd c2)
		| WhileNZ(e, c) ->
			(vars_of_exp e) ++ (vars_of_cmd c)
		| Seq(c1, c2) ->
			(vars_of_cmd c1) ++ (vars_of_cmd c2)
	end
```

#### Translation

The translation invariants are guided by the *types* of the operations:

- variables are a global state, so the become mutable references
- expressions denote integers
- commands denote imperative actions of type unit

We might build our translator the following way:

```ocaml
let trans_var (x:var) : string =
	"V_" ^ x

let rec trans_exp (e:exp) : string =
	let trans_op (e1:exp) (e2:exp) (op:string) =
		Printf.sprintf "(%s %s %s)"
			(trans_exp e1) op (trans_exp e2)
	in
		begin match e with
			| Var x -> "!" ^ (trans_var x)
			| Add(e1, e2) -> trans_op e1 e2 "+"
			| Mul(e1, e2) -> trans_op e1 e2 "*"
			| Lt (e1, e2) ->
				Printf.sprintf "(if %s then 1 else 0)"
					(trans_op e1 e2 "<")
			| Lit 1 -> string_of_int 1
		end

let rec trans_cmd (c:cmd) : string =
	begin match c with
		| Skip -> "()"
		| Ass(x, e) ->
			Printf.sprintf "%s := %s"
				(trans_var x) (trans_exp e)
		| IfNZ(e, c1, c2) ->
			Printf.sprintf "if %s <> 0 then (%s) else (%s)"
				(trans_exp e) (trans_cmd c1) (trans_cmd c2)
		| WhileNZ(e, c) ->
			Printf.sprintf "while %s <> 0 do \n %s done"
				(trans_exp e) (trans_cmd c)
		| Seq(c1, c2) ->
			Printf.sprintf "%S; \n %s"
				(trans_cmd c1) (trans_cmd c2)
	end

let trans_prog (c:cmd) : string =
	let vars = vars_of_cmd c in
		let decls =
			VSet.fold (fun x s ->
				Printf.sprintf " let %s = ref 0 \n %s \n"
					(trans_var x) d)
				vars
				""
		in
			Printf.sprintf "module Program = struct \n %s let run () = \n %s \n end"
			decls (trans_cmd c)

(* Do some testing using the factorial code: Simple.factorial *)
let _ =
	Printf.printf ("%s \n") (trans_prog factorial)
```

**Compiler Design — Lecture note week 2**

- Author: Ruben Schenk
- Date: 11.10.2021
- Contact: ruben.schenk@inf.ethz.ch

# 3. X86 LITE

## 3.1 Simplified Compiler Structure

A simplified compiler structure looks as follows:

![](./Figures/CompDes_Fig2-1.PNG)

## 3.2 X86 vs. X86Lite

`X86` assembly is very complicated:

- 8-, 16, 32-, and 64-bit values + floating point, etc.
- Intel 64 and IA 32 have a *huge* number of functions
- For machine code, the instruction range is in size from 1 to 17 bytes

`X86Lite` assembly is a very simple subset of X86

- Only 64-bit signed integers (no floating point, no 16-bit, no etc.)
- Only about 20 instructions
- Sufficient as a target language for general-purpose computing

## 3.3 X86 Schematic

The X86 schematic looks as follows:

![](./Figures/CompDes_Fig2-2.PNG)

### 3.3.1 Registers

There are three special **registers**:

- `rip`: The *instruction pointer*, holds the address of the next instruction
- `rbp`: The *base pointer*, used for call-stack manipulation
- `rsp`: *The stack pointer*, used for call-stack manipulation

### 3.3.2 Memory

The memory consists of three parts:

- *Code & Data*: Holds the actual program instructions as well as program constants and globals
- *Stack*: Used for function calls and local variables
- *Heap*: Dynamically allocated memory, e.g. via calls to `malloc()`

## 3.4 Instructions

### 3.4.1 `mov`

The `mov` instructions is of the following form:

```x86
movq SRC, DEST
```

Here, `SRC` and `DEST` are *operands*. `DEST` is treated as a location, either a register or a memory address. `SRC` is treated as a value and is the content of either a register or a memory address or an immediate constant or a label.

Example of a `mov` instruction:

![](./Figures/CompDes_Fig2-3.PNG)

#### A Note About Instruction Syntax

The most important note is that we have the source *before* the destination. Furthermore:

- Immediate values are prefixed with `$`
- Registers are prefixed with `%`
- Mnemonic suffixed (`movq` vs `mov):
  - `q` -> quadword (4 words)
  - `l` -> long (2 words)
  - `w` -> word (16 bits)
  - `b` -> byte (8 bits)

### 3.4.2 X86 Operands

| **Type** | **Description**                                                           | **Example**                 |
|----------|---------------------------------------------------------------------------|-----------------------------|
| Imm      | 64-bit literal signed integer ("immediate")                               | `move $4, %rax`             |
| Lbl      | a "label" representing a machine address                                  | `call FOO`                  |
| Reg      | one of the 16 registers                                                   | `move %rbx, %rax`           |
| Ind      | machine address: [base:Reg][index:Reg][disp:int32](base + index*8 + disp) | `move 12(%rax, %rcx), %rbx` |

### 3.4.3 Arithmetic Instructions

| **Instruction**  | **Description**                              | **Example**      | **Notes**                                      |
|------------------|----------------------------------------------|------------------|------------------------------------------------|
| `negs DEST`      | 2's complement negation                      | `negs %rax`      |                                                |
| `add SRC, DEST`  | `DEST <- DEST + SRC`                         | `add %rbx, %rax` |                                                |
| `Subq SRC, DEST` | `DEST <- DEST - SRC`                         | `subq $4, %rsp`  |                                                |
| `Imulq SRC, Reg` | `Reg <- Reg * Src` (truncated 128-bit mult.) | `imulq $2, %rax` | `Reg` must be a register, not a memory address |

### 3.4.4 Logical/Bit Manipulation Instructions

| **Instruction**  | **Explanation**       | **Example**       | **Notes**             |
|------------------|------------------------|-------------------|-----------------------|
| `notq DEST`      | logical negation       | `notq %rax`       | bitwise not           |
| `andq SRC, DEST` | `DEST <- DEST & SRC`   | `andq %rbx, %rax` | bitwise and           |
| `orq SRC, DEST`  | `DEST <- DEST | SRC`   | `orq $4, %rsp`    | bitwise or            |
| `xorq SRC, DEST` | `DEST <- DEST xor SRC` | `xorq $2, %rax`   | bitwise xor           |
| `sarq Amt, DEST` | `DEST <- DEST >> Amt`  | `sarq $4, %rax`   | arithmetic shift right |
| `shlq Amt, DEST` | `DEST <- DEST <<< Amt` | `shlq %rbx, %rax` | logical shift left    |
| `shrq Amt, DEST` | `DEST <- DEST >>> Amt` | `shrq $1. %rsp`   | logical shift right   |

### 3.4.5 Condition Flags & Codes

Some X86 instructions set flags as side effects:

- `OF`: *overflow* is set when the result is too big/small to fit in a 64-bit register
- `SF`: *sign* is set to the sign of the result (`0` means positive, `1` means negative)
- `ZF`: *zero* is set when the result is `0`

From these three flags, we can define **condition codes**. If we want to compare `SRC1` to `SRC2`, we compute `SRC1 - SRC2`. We can then define the following condition codes based on the resulting condition flags:

| **Code**                 | **Condition**            |
|--------------------------|--------------------------|
| `e` (equality)           | `ZF` is set              |
| `ne` (inequality)        | `(not ZF)`               |
| `g` (greater than)       | `(not ZF) and (SF = OF)` |
| `l` (less than)          | `SF <> OF`               |
| `ge` (greater or equal)  | `(SF = OF)`              |
| `le`(less than or equal) | `SF <> OF or ZF`         |

### 3.4.6 Conditional Instructions

We might write conditional instructions in the following way in X86:

```c
// Conditional instruction in C
if (a == b) {
  // something
} else {
  // somethingElse
}
  // commonCode
```

```assembly
; Conditional instruction in x86
  cmpq %rax, %rbx
  je

somethingElse:
  <instruction>
  ;...
  jmp commonCode

something:
  <instruction>
  ;...

commonCode:
  <instruction>
  ;...
```

We support the following three **conditional instructions**:

| **Instruction**   | **Description**                              |
|-------------------|----------------------------------------------|
| `cmpq SRC2, SRC1` | Compute `SRC1 - SRC2`, set condition flags   |
| `setbCC DEST`     | `DEST`'s lower byte <- `if CC then 1 else 0` |
| `jCC SRC`         | `rip <- if CC then SRC else fallthrough`     |

### 3.4.7 Code Blocks and Labels

x86 assembly code is organized into **labeled blocks**. Labels indicate code locations than can be jump targets. Labels are translated away by the linker and loader -- instructions live in the *code segment*.

An x86 program begins executing at a designated code label (usually `main`).

### 3.4.8 Jumps, Call and Return

We might code function calls in the following way in x86:

```c
void bar() {
  // ...
}

void foo() {
  // ...
  bar();
}
```

```assembly
bar:
  <instruction>
  ;...
  <instruction>
  ret

foo:
  <instruction>
  ; ...
  <instruction>
  call bar
  ;...
```

The different instructions one might use are given by the following table:

| **Instruction** | **Description**                             | **Notes**                                                                                                                                    |
|-----------------|---------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| `jmp SRC`       | `rip <- SRC`                                | Jump to location in `SRC`                                                                                                                    |
| `call SRC`      | Push `rip`, `rip <- SRC` (call a procedure) | Push the program counter to the stack (decrementing `rsp`), and then jump to the machine instruction at the address given by `SRC`           |
| `ret`           | Pop into `rip` (return from procedure)      | Pop the current top of the stack into `rip` (incrementing `rsp`). This instruction effectively jumps to the address at the top of the stack. |

## 3.5 x86Lite Addresses

### 3.5.1 x86Lite Addressing

We show how **addressing** in x86Lite works with the following simple example:

```c
long a[0, 42, 2020;]

long b = (long)a;     // b = address(a)
long b = *a;          // b = a[0] = 0
long b = *(a+2);      // b = a[2] = 2020

long c = 1;
long b = a[c];        // b = 42
long b = a[c+1];      // b = 2020
```

```assembly
; Array [0, 42, 2020]
; Array address 0xBEEF

movq %0xBEEF, %rax

movq %rax,     %rbx        ; rbx = 0xBEEF
movq (%rax),   %rbx        ; rbx = 0
movq 16(%rax), %rbx        ; rbx = 2020

movq $1, %rcx
movq (%rax, %rcx), %rbx    ; rbx = 42
movq 8(%rax, %rcx), %rbx   ; rbx = 2020
```

In general, there are three components to an **indirect address**:

- *Base*: a machine address stored in a register
- *Index*: a variable offset from the base
- *Disp*: a constant offset (displacement) from the base

We therefore have: `addr(ind) = Base + [Index * 8] + Disp`. When used as a location, `ind` denotes the address `addr(ind)`. When used as a value, `ind` denotes `Mem[addr(ind)]`, the contents of the memory address.

Examples:

| **Expression**  | **Address**         |
|-----------------|---------------------|
| `-8(%rsp)`      | `rsp - 8`           |
| `(%rax, %rcx)`  | `rax + 8 * rcx`     |
| `8(%rax, %rcx)` | `rax + 8 * rcx + 8` |

### 3.5.2 x86Lite Memory Model

The x86Lite memory consists of `2^64` bytes numbered `0x00000000` through `0xffffffff`. The memory is treated as consisting of 64-bit (8 byte) words. Therefore: *legal x86Lite memory addresses consists of 64-bit, quadword-aligned pointers*. This means, that all memory addresses are evenly divisible by 8.

To load a pointer into `DEST`, we use `leaq Ind, DEST` (`DEST <- addr(Ind)`).

By convention, the stack grows from high addresses to low addresses.

The register `rsp` points to the top of the stack:

- `pushq SRC`: `rsp <- rsp - 8; Mem[rsp] <- SRC`
- `popq DEST`: `DEST <- Mem[rsp]; rsp <- rsp + 8`

> Here is a nice website to explore assembly code given some code snippet in another language: [Compiler Explorer](https://godbolt.org/)

### 3.6 Example: Handcoding x86Lite

Let's look at how we would implement the **factorial** function in **x86Lite**:

```assembly
;  long factorial(long i) {
;  if (i > 1l) {
;    return i * factorial(i-1l);
;  }
;  return 1l;
;}

.text
.global factorial

factorial:
  ; i is in %rdi

  ; boilerplate
  pushq   %rbp
  movq    %rsp, %rbp

  ; if (i > 1l)
  cmpq    $1, %rdi      ; computes %rdi - 1
  jle     .BASECASE     ; if (i <= 1)

  ; (i > 1l) holds at this point
  pushq   %rdi          ; stores the current value of i on top of the calls tack

  subq    $1, %rdi
  callq   factorial

  ; %rax holds factorial(i - 1)
  popq    %rdi          ; %rdi holds again the original value of i
  imulq   %rdi, %rax    ; i * factorial(i - 1)

  jmp     .EXIT

.BASECASE:
  movq    $1, %rax

.EXIT:
  ; rest of boilerplate
  movq    %rbp, %rsp
  popq    %rbp
  ret     ; return the value in %rax

.data
```

*Remark: By convention, compilers often use a `.` in front of a label that is internal, i.e. not a global label (compare `factorial` to `.EXIT` in the code above).*

## 3.7 Programming in x86Lite

### 3.7.1 Three parts of the C memory model

We want to quickly revisit the three different parts of the C memory model, shown in the picture below.

![](./Figures/CompDes_Fig2-4.PNG)

- The **code & data** (or `.text`) segment: contains compile code, constant strings, etc.
- The **heap**: stores dynamically allocated objects, is allocated via `malloc` and deallocated via `free`
- The **stack**: stores local variables, the return address of a function and other bookkeeping information

### 3.7.2 Local vs. Temporal Variable Storage

We somehow need space to store things like global variables, values passed as arguments to procedures, and local variables. The processor provides two options for storing stuff:

- *Registers*: fast, small size, very limited number
- *Memory (Stack)*: slow, very large amount of space

Example:

```assembly
; int i = 5

; Option 1:
; store to a register
; register is "blocked"
movq    $5, %rax

; Option 2:
; store on the stack
subq    $8, %rsp
movq    $5, (%rsp)
;...
movq    (%rsp), %rax
```

### 3.7.3 The Stack

The following picture shows how we use the stack in a program with different calls. This corresponds to the "boilerplate" code in the previous example with the `factorial`. We adjust the pointers to the bottom and the top of the stack before and after calling a "function", such that the function has its own **stack frame**.

![](./Figures/CompDes_Fig2-5.PNG)

### 3.7.4 Calling Conventions

The **calling conventions** cover three main topics:

- Specify the locations of arguments
  - Passed to a function, and
  - Returned by a function
- Designate registers as either
  - Caller Save -- e.g. freely usable by the called code
  - Callee Save -- e.g. must be restored by the called code
- Define the protocol for deallocating stack-allocated arguments, either
  - Caller cleans up
  - Callee cleans up

The widely used calling conventions for x86-64 systems are as follows:

- Callee save registers: `rbp, rbx, r12-r15`
- Caller save: all others
- Call parameters:
  - Parameter 1-6: `rdi, rsi, rdx, rcx, r8, r9`
  - Parameter 7+: on the stack (in right-to-left order), thus, for `n > 6`, the nth argument is at `((n - 7) + 2) * 8 + rbp`
- Return value is in `rax`
- 128-byte "red zone" -- scratchpad for the callee's data

**Compiler Design — Lecture note week 3**

- Author: Ruben Schenk
- Date: 12.10.2021
- Contact: ruben.schenk@inf.ethz.ch

### 3.7.5 Directly Generating x86

For simple languages, there is no need for intermediate representations, e.g. the arithmetic expression language as in `(X1 + X2) - X3`. The main idea is to **maintain invariants**, e.g. code emitted for a given expression computes the answer into `rax`.

The key challenges are:

- storing intermediate values needed to compute complex expressions
- some instructions use specific registers (e.g. shift)

One simple strategy for directly generating x86 is based on the following ideas:

- Compilation emits instructions into an instruction stream
- To compile an expression `e1 op e2`:
    1. Recursively compile its sub-expressions
    2. Process the results
- Invariants:
    - Compilation of an expression yields its result in `rax`
    - Argument `Xi` is store in a dedicated operand
    - Intermediate values are pushed onto the stack 
    - The stack is popped after use (such that the space is reclaimed)
- Resulting code is wrapped to comply with cdecl calling conventions

# 4. Intermediate Representations

Up until now, we followed a simple *syntax-directed* translation, this meant that:

- Input syntax uniquely determined the output, i.e. no complex analysis or code transformation was done
- Worked fine for simple languages

However, the resulting *code quality is poor*. Example: The expression `(X1 - X1) + 3` is turned into the following code:

```assembly
.text
.globl _program

_program:
    pushq       %rbp
    movq        %rsp, %rbp
    movq        %rdi, %rax
    pushq       %rax
    movq        %rdi, %rax
    imulq       $-1, %rax
    popq        %r10
    addq        %r10, %rax
    pushq       %rax
    movq        $3, %rax
    popq        %r10
    addq        %r10, %rax
    popq        %rbp
    retq
```

But, obviously `(X1 - X1)` is 0 and the program therefore could be much more simple.

## 4.1 Intermediate Representations (IR's)

**Abstract machine code** (IR) hides the details of the target architecture and allows machine independent code generation and optimization. 

The goal of this is to get the program closer to machine code without losing the information needed to do analysis and optimization. We might also have multiple IR's.

![](./Figures/CompDes_Fig3-1.PNG)

### 4.1.1 What makes a good IR?

A good IR should tick the following points:

- Easy translation target (from the level above)
- Easy to translate (to the level below)
- Narrow interface (fewer constructs means simpler phases/optimizations)

*Example*: Source languages may have "while", "for", and "for each" loops while the IR might only have "while" loops and sequencing.
A "for" loop may be translated as follows:

```bnf
    [[for(pre; cond; post) {body}]]
    [[pre; while(cond) {body; post}]]
```

*Remark: Here, the notation `[[cmd]]` denotes the "translation/compilation" of `cmd`.*

## 4.2 Simple Let-Based IR (SLL)

### 4.2.1 Eliminating Nested Expressions

One fundamental problem is compiling complex and nested expression forms to simple operations. As an example, consider the following steps of translations:

```bnf
    # Source
    ((1 + X4) + (3 + (X1 * 5)))

    # AST
    Add(Add(Const 1, Var X4),
        Add(Const 3, Mul(Var X1,
            Const 5)))
```

How would the IR-translation of the above example look like? The idea is to name intermediate values and to make the order of evaluation explicit, i.e. not to have any nested operations.

### 4.2.2 Translation to SLL

Considering the given example in the previous section, we can translate the code to the desired SLL form:

```llvm
    let tmp0 = add 1L varX4 in
    let tmp1 = mul varX1 5L in
    let tmp2 = add 3L tmp1 in
    let tmp3 = add tmp0 tmp2 in
        tmp3
```

We take the following notes on the translation above:

- Translation makes the order of evaluation explicit
- Translation names intermediate values
- Introduced temporaries are never modified

### 4.2.3 Basic Blocks and CFGs

A **basic block** is a sequence of instructions that is always executed starting at the first instruction and always exits at the last instruction:

- Starts with a label that names the *entry point* of the basic block
- Ends with a control-flow instruction (e.g. branch or return), i.e. the *link*
- Contains no other control-flow instruction
- Contains no interior label used as a jump target

Basic blocks can be arranged into a **control-flow graph (CFG)**:

- The nodes of the graph are basic blocks
- There is a directed edge from node A to node B if the control flow instruction at the end of block A might jump to the label of block B
