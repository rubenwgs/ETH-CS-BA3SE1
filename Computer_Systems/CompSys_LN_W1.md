**Computer System - Notes week 1**

- Author: Ruben Schenk
- Date: 20.09.2021
- Contact: ruben.schenk@inf.ethz.ch

# Chapter 1 : Introduction

**Computer Systems** is the field of computer science that studies the design, implementation, and behaviour of real, complete systems of hardware and software.

# Chapter 2 : Naming

## 2.1 Basic definitions

A **name** is an identifier used to refer to an object in a system. It might be a character string, or a string of bits - in principle any concrete value. A **binding** is an association of a name to an object and a **context** is a particular set of bindings. We might also refer to a context as a *namespace* or *name scope*.

**Resolution** describes the process of, given a name and a context, finding the object to which the name is bound in that context.

> Remark: For a name to designate an object, it must be bound to the object in some context. In other words, whenever names are being used, there is always a context, even if it's implicit.

## 2.2 Naming networks

A **naming network** is a directed graph whose nodes are either naming contexts or objects, and whose arcs are bindings of names in one context to another context. A **pathname** is an ordered list of names, also referred to as *path components*, which therefore specify a path in the network.

A naming network which is a tree is called a **Naming hierarchy**. Pathnames in a naming hierarchy are sometimes called *tree names*. The unique starting point in a naming hierarchy is called the root.

*Example (UNIX name resolution):* A UNIX filename is a pathname. The UNIX file system is, for the most part, a naming hierarchy of directories which contain references to other directories of files:

- A UNIX filename which stats with `/`, such as `/usr/bin/dc`, is resolved using the root of the file system, i.e. all paths are bound to the current directory but `/` has an implicit binding to the root.
- A filename which doesn't start with a `/` is resolved using the current working directory as the first context.
- Every context (i.e. directory) in the UNIX file system has a binding of the name `.` to the context itself.
- Each context also has a binding of the name `..`. This is always bound to the parent directory of the context.
- A **file** object can have *more than one name bound to it*, but a **directory** *cannot*.

## 2.3 Indirect entries and symbolic links

An **indirect entry** is a name which is bound not to an object per se, but instead to another path name.

> Remark: Indirect entries can complicate name resolution since there is no guarantee that the binding will end up at an object at all, or if the name to which the indirect entry is bound is even syntactically valid in the context in which it is to be resolved.

## 2.4 Pure names and addresses

A **pure name** encodes no useful information about whatever object it refers to.

> Remark: The names we have considered so far are arguably pure names - in particular, the only thing we have done with them is to bind them to an object in a context, and look them up in a context to get the object again. Some people use the word *identifier* to mean a pure name.

An **address** is a name which encodes some information about the location of the object it refers to.

> Remark: An IP address is not a pure name, since it can be used to route a packet to the corresponding network interface without you needing to know anything more about the interface.

## 2.5 Search paths

A **search path** is an ordered list of contexts which are treated as a single context. To look up a name in such a context, each constituent context is tried in turn until a binding for the name is found.

> Remark: The UNIX shell `PATH` variable, for example:
> `/home/ruben/bin:/usr/bin:/bin:/sbin:/usr/sbin:/etc`
> - is a search path context for resolving command names: each directory in the search path is tried in turn to looking for a command.

## 2.6 Synonyms and Homonyms

**Synonyms** are different names that ultimately resolve to the same object. **Homonyms** are bindings of the same name to different objects.

> Remark: The existence of synonyms turns a naming hierarchy into a directed, possibly cyclic, graph. For this reason, the UNIX file system disallows most cycles by preventing directories from having synonyms except for `.` and `..` - *only files can have multiple names.*

# Chapter 3 : Classical Operating Systems and the Kernel

## 3.1 The role of the OS

The **operating system** for a unit of computing hardware is that part of the software running on the machine which fulfils three particular roles:

- As a **Referee**, the OS multiplexes the hardware of the machine among different principals (users, programs, etc.), and protects these principals from each other.
- As an **Illusionist**, the OS provides the illusion of *real* hardware resources to resource principals through *virtualization*.
- As **Glue**, the OS provides abstractions to tie different resources together, and hides details of the hardware to allow programs portability across different platforms.

## 3.2 Domains

A **domain** is a collection of resources or principals which can be traded as a single uniform unit with respect to some particular property. Some examples are:

- *NUMA domain:* cores sharing a group of memory controllers in a NUMA system.
- *Coherence domain:* caches which maintain coherence between themselves.
- *Failure domain:* the collection of computing resources which are assumed to fail together.
- *Shared memory domain:* cores which share the same physical address space.
- *Administrative domain:* resources which are all managed by a single central authority or organization.
- *Trust domain:* a collection of resources which mutually trust each other.
- *Protection domain:* the set of objects which are all accessible to a particular security principal.
- *Scheduling domain:* set of processes or threads which are scheduled as a single unit.

## 3.3 OS components

The **kernel** is that part of an OS which executes in privileged mode.

- While most computer systems have a kernel, very small embedded systems do not.
- The kernel is just a computer program, typically an *event driven server*. It responds to multiple entry points: *system calls, hardware interrupts, and program traps.*

**System libraries** are libraries which are there to support all programs running on the system, performing either common low-level functions or providing a clean interface to the kernel and daemons.

A **daemon** is a user-space process running as part of the operating system.

- Daemons are different from in-kernel threads. They execute OS functionality that can't be in a library, but is better off outside the kernel (for reasons of modularity, fault tolerance, and ease of scheduling).

## 3.4 Operating System models
