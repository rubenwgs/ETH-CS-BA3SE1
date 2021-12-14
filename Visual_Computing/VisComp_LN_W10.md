**Visual Computing - Lecture notes week 10**

- Author: Ruben Schenk
- Date: 13.12.2021
- Contact: ruben.schenk@inf.ethz.ch

### 4.2.3 L-Systems (Implicit)

#### Introduction

The underlying computational system to **L-systems** is simple:

- The geometry is governed by simple rules
- The end result has interesting, useful, and surprising properties
- **L** stands for _Lindenmayer,_ the inventor of these types of systems

A **L-system** is a set of grammar rules, a start symbol, and semantics, i.e. a way of interpreting the grammar strings.

_Example:_ We define our grammar to have a start string `AAA` and the following two rules:

```bnf
rule 1: A -> BBC
rule 2: B -> abA
```

We might furthermore define the following semantics:

```bnf
a = forward 10
b = left rand(10, 40)
A = right rand(10, 40), foward 5
C = disc diameter rand(2, 5), forward 10
```

This can lead to something like the following geometry:

![](./Figures/VisComp_Fig10-1.PNG)

#### von Kock Snowflake Curve

We define the following grammar:

```bnf
Symbols: {F, +, -}
Start symbol: {F}
Rules: F -> F+F--F+F
Semantics:
- F = forward 1
- + = rotate left 60
- - = rotate right 60
```

![](./Figures/VisComp_Fig10-2.PNG)

#### Branching Structures

We assume that `[` pushes the state onto the stack and `]` pops the state from the stack. We use `[` to start a branch and `]` when finished to return to its base.

With those additional symbols, we can define three rules for different types of branching:

- _Monopodial:_ The trunk extends undeviated to the top and branches extend perpendicularly: `T -> T[+B][-B]T`
- _Sympodial:_ The trunk deviates to the top and branches extend perpedicularly: `T -> T[--B]+T`
- _Binary:_ Trunk terminates at the first branching point and branches deviate uniformly: `T -> T[+B][-B]`

_Example:_ The following figure shows an example of the L-System `F -> F[+F]F[-F]F`:

![](./Figures/VisComp_Fig10-3.PNG)

#### Stochastic L-System

In **stochastic L-systems** we have conditional firing of rules:

```bnf
F -> <something> : probability1
F -> <something> : probability2
...
```

_Example:_ We can generate random bushes with the following three rules:

```bnf
F -> F[+F]F[-F]F : 0.33
F -> F[+F]F : 0.33
F -> F[-F]F : 0.34
```

Multiple applications of the above rules will lead to a geometry similar to the figure below:

![](./Figures/VisComp_Fig10-4.PNG)

### 4.2.4 Point Cloud (Explicit)

**Point clouds** are the simplest representation for geometries, they are simply a list of points:

- Easily represent any kind of geometry
- Useful for large datasets (>> 1 point per pixel)
- Difficult to draw in undersampled regions
- Hard to do processing and simulation

### 4.2.5 Polygonal Mesh (Explicit)

In a **polygonal mesh,** we store vertices and polygons (most often triangles or quads). They are easier to do processing, simulation, and adaptive sampling.
This is perhaps the most common representation in computer graphics.

A **polygon** consists of vertices $v_0, \, v_1,..., \, v_{n-1}$ and edges $\{(v_0, \, v_1), \, (v_1, \, v_2),... \}$. The line segments must for a closed loop!

A **polygonal mesh** is a set $M = \langle V, \, E, \, F \rangle$, consisting of a set of vertices $V$, a set of edge $E$, and a set of faces $F$. It has the following properties:

- Every edge belongs to at least one polygon
- The intersection of two polygons in $M$ is either empty, a vertex, or an edge

Let's introduce some useful definitions:

- _Vertex degree (Valence):_ Number of edges incident to a vertex.
- _Boundary:_ The set of all edges that belong to only one polygon

![](./Figures/VisComp_Fig10-5.PNG)

#### Mesh Data Structures

With **mesh datastructures** we can store the geometry and topology of some object:

- _Geometry:_ The vertex locations
- _Topology:_ How vertices are connected (i.e. the edges and faces)

The simplest storage option for mesh datastructures is to simply store them in a **indexed face set:**

![](./Figures/VisComp_Fig10-6.PNG)

### 4.2.6 Surface Normals

We can describe the power of a (light) beam in terms of it's **irradiance,** i.e. the enery per time, per area, or:

$$
E = \frac{\Phi}{A},
$$

where $\Phi$ is the flux of the beam of light and $A$ is the surface area to which the beam is incident.

If the beam is incident to an angled surface, it will cover more area, and therefore the irradiance must be smaller! **Lambert's Law** states that the irradiance at some surface is proportional to the cosine of the angle between the light direction and the surface normal, or:

$$
E = \frac{\Phi}{A'} = \frac{\Phi \cos \theta}{A}
$$

![](./Figures/VisComp_Fig10-7.PNG)

#### N-dot-L Lightning

**N-dot-L lightning** is one of the most basic ways to shade a suface: One simply takes the dot product of a unit surface normal (_N_) and the unit direction to the light source (_L_).

![](./Figures/VisComp_Fig10-8.PNG)

## 4.3 Texture Mapping

### 4.3.1 Texture Coordinates

**Texture coordinates** define a mapping from surface coordinates (points on a triangle) to points in the texture domain.

![](./Figures/VisComp_Fig10-9.PNG)

### 4.3.2 Texture Space Samples

To do a texture mapping, we need some space samples. WE can sample some some positions in the $XY$ screen space and then use the same sample positions in the texture sapce.
However, this can lead to **perspective-incorrect interpolation:**

![](./Figures/VisComp_Fig10-10.PNG)

This is due to perspective projection. Barycentric interpolation of values in $XY$ screen coordinates do not correspond to values that vary linearly on the original triangle!

![](./Figures/VisComp_Fig10-11.PNG)

### 4.3.3 Filtering Textures

Due to resizing, our rendering pixels from our image may be much larger or smaller than the pixels available of our texture. This leads to minification and magnification:

- _Minification:_ The area of screen pixel maps to a large region of texture (filtering required). One texel corresponds to far lesse than a pixel on the screen.
- _Magnification:_ The area of screen pixel maps to a tiny region of texture (interpolation required). One texel maps to many screen pixels.

#### Mipmap (L. Williams 83)

The idea of the **Mipmap** is to prefilter the texture data to remove high frequencies. Texels at higher levels store the itnegral of the texture function over a region of texture space (downsampled images), i.e. they represent low-pass filtered versions of the original texture signal.

![](./Figures/VisComp_Fig10-12.PNG)

To decide which level $d$ to use, we calculate the differences between texture coordinate values of neighboring screen samples:

![](./Figures/VisComp_Fig10-13.PNG)
