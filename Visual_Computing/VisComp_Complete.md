---
title: "Visual Computing - Complete Summary"
author: Ruben Schenk, ruben.schenk@inf.ethz.ch
date: January 10, 2022
geometry: margin=2cm
output: pdf_document
---

# 1. The Digital Image

## 1.1 What is an image?

### Image as a 2D signal

The **signal** is a function depending on some variable with physical meaning. The **image** is a continuous function, where we either have 2 variables `x y` which are the coordinates, or, in case of a video, three variables `x y` and the corresponding time in the video. Usually, the value of the function is the **brightness**.

_Images in Python:_

```python
    # Load a picture into Python
    import cv2
    img = cv2.imread('foo.jpg')

    # Display the image in Python
    cv2.imshow('My image', img)
    cv2.waitKey(0)

    # Print the image data array
    img

    # Print the size of the image array and create a subimage
    img.shape
    subimg = img[72:92, 62:82]
```

```python
    # Random image in Python
    import numpy as np
    import cv2
    t = np.random.rand(64, 64)
    cv2.imshow('Random', t)
    cv2.waitKey(0)
```

In summary, an **image** is a picture or pattern of a value varying in space and/or time. It is the representation of a _continuous_ function to a _discrete_ domain: `f : R^n -> S`.

As an example, for grayscale CCD images, `n = 2` and `S = R^+`.

### What is a pixel?

_A pixel is not a little square!_

**Pixels** are point measurements of a function (of the above described continuous function).

## 1.2 Where do images come from?

There are several things where pictures can come from:

- Digital cameras
- MRI scanners
- Computer graphics packages
- Body scanners
- Many more...

### Digital cameras

Simplified, the **digital camera** consists of the following parts and is said to be a _Charge Coupled Device (CCD)_:

![](./Figures/VisComp_Fig1-1.PNG){width=50%}

The **sensor array** can be < 1 cm^2 and is an array of _photosites_. Each photosite is a bucket of electrical charge, and this charge is proportional to the incident light intensity during the exposure.

The **analog to digital conversion (ADC)** measure the charge and digitizes the result. The conversion happens line by line in that charges in each photosite move down through the sensor array.

Example:

![](./Figures/VisComp_Fig1-2.PNG){width=50%}

Because each bucket has a finite capacity, if a photosite bucket is full, it can overflow to other buckets, which leads to **blooming**.

Even without any light, there will still be some current which can degrade the quality of a picture. CCD's produce thermally-generated charge, which results in a _non-zero output_ even in darkness. This effect is called the **dark current**.

## 1.3 CMOS

**CMOS** sensors have the same sensor elements as CCD. Each photo sensor has its own amplifier, which results in more noise and a lower sensitivity. However, since CMOS photo sensor use standard CMOS technology, we might put other components on the chip (such as "smart pixels").

### CCD vs. CMOS

_CCD_

- Mature technology
- Specific technology
- High production cost
- High power consumption
- Blooming
- Sequential readout

_CMOS_

- More recent technology
- Cheap
- Lower power consumption
- Less sensitive
- Per pixel amplification
- Random pixel access
- On chip integration with other components

### Rolling shutter

By resetting each line in the sensor line by line (the "shutter"), each line will start capturing light a little before the line below and so on. Each line in the picture is therefore a little behind in time as the line above.

## 1.4 Sampling in 1D

**Sampling** in 1D takes a function, and returns a vector whose elements are values of that function at the sample points.

Example:

![](./Figures/VisComp_Fig1-3.PNG){width=50%}

Sampling solves one problem with working with continuous functions. How do we store and compute with them? A common scheme for representing continuous functions is with **samples**: we simply write down the function's values as discrete values at many sample points.

### Reconstruction

**Reconstruction** describes the process of making samples back into a continuous function. We might do this for several reasons:

- For output where we need a realizable method
- For analysis or processing, where we need a mathematical method
- Instead of "guessing" what the function did in between sample points

### Undersampling

Unsurprisingly, if we undersample some function, we loose information.

Example: If we undersample the following `sin` wave, it gets indistinguishable from lower frequencies:

_Sample:_

![](./Figures/VisComp_Fig1-4.PNG){width=50%}

_Reconstruction 1:_

![](./Figures/VisComp_Fig1-5.PNG){width=50%}

_Reconstruction 2:_

![](./Figures/VisComp_Fig1-6.PNG){width=50%}

This effect is what we call **aliasing**, i.e. _"Signals travelling in disguise as other frequencies"_.

## 1.5 Sampling in 2D

**Sampling** in 2D takes a function and returns an array. We allow the array to be infinite dimensional and to have negative as well as positive indices.

Example:

_Function:_

![](./Figures/VisComp_Fig1-7.PNG){width=50%}

_Sample:_

![](./Figures/VisComp_Fig1-8.PNG){width=50%}

### Reconstruction

In 2D, a simple way to reconstruct a function from a sample is to use **bilinear interpolation**, which works essentially the same as linear interpolation: we calculate two lines in each direction, and then take the intersection of the two lines.

Example:

![](./Figures/VisComp_Fig1-9.PNG){width=50%}

## 1.6 Nyquist Frequency

We define the **Nyquist Frequency** as half the sampling frequency of a discrete signal processing system. The concept tells us, that if the signal's maximum frequency is at most the Nyquist frequency, then we can reconstruct the signal.

## 1.7 Quantization

When sampling, real valued functions will get digital values, i.e. integer values. This means that **quantization** is lossy: after quantization, the original signal cannot be reconstructed anymore.

This is in contrast to sampling, as a sampled but not quantized signal _can_ be reconstructed.

### Usual quantization intervals

The following are the most widely used quantization intervals:

- Grayscale image: `8 bit = 2^8 = 256` gray values
- Color image RGB (3 channels): `8 bit/channel = 2^24 = 16.7M` colors
- 12 bit or 16 bit for some sensors

## 1.8 Image Properties

### Image resolution

Simply tells the amount of pixels our pictures has.

### Geometric resolution

_How many pixels per area?_

Tells us how many pixels we have per a given area (e.g. how many pixels per square centimeter of picture).

### Radiometric resolution

_How many bits per pixel?_

Tells us how much information each pixel can store.

## 1.9 Image Noise

A common model is the **additive Gaussian noise**, which means that we measure some signal with our processor but have some deviation/noise in our measurement:

![](./Figures/VisComp_Fig1-10.PNG){width=50%}

One might also use the much more meaningful assumption of **Poisson noise**:

![](./Figures/VisComp_Fig1-11.PNG){width=50%}

### SNR

The **signal-to-noise ration (SNR)** `s` is an index of image quality:

![](./Figures/VisComp_Fig1-12.PNG){width=50%}

## 1.10 Color Cameras

We consider three different common concepts:

1. Prism (with three different sensors)
2. Filter mosaic
3. Filter wheel

### Prism

With this type of camera, we separate the light into three beams using two _dichroic prisms_. However, this requires three sensors and a very precise alignment. The plus of this concept is that there is a very good color separation.

### Filter Mosaic

With this concept, one coats the filter directly on the sensor.

### Filter wheel

For static scenes, we can rotate multiple filters in front of the lens. This allows for more than 3 colors.

### Prism vs. mosaic vs. wheel

| Approach   | Prism            | Mosaic          | Wheel                   |
| ---------- | ---------------- | --------------- | ----------------------- |
| # Sensors  | 3                | 1               | 1                       |
| Separation | High             | Average         | Good                    |
| Cost       | High             | Low             | Average                 |
| Framerate  | High             | High            | Low                     |
| Artefacts  | Low              | Aliasing        | Motion                  |
| Bands      | 3                | 3               | 3 or more               |
| Type       | High-end cameras | Low-end cameras | Scientific applications |

### Color CMOS sensor (Foveon's X3)

![](./Figures/VisComp_Fig2-1.PNG){width=50%}

In contrast to a filter mosaic, we truly measure each color at each pixel, instead of either red, blue or green per pixel.

# 2. Image Segmentation

> Image segmentation is the ultimate classification problem. Once solved, Computer Vision is solved.

## 2.1 What is Image Segmentation?

**Image segmentation** partitions an image into regions of interest. It is the first stage in many automatic image analysis systems.

A _complete segmentation_ of an image `I` is a finite set of regions `R_1,..., R_N`, such that:

![](./Figures/VisComp_Fig2-2.PNG){width=50%}

_Excluding dark pixels from an image:_

```python
    img = cv2.imread('BlobsIP.png') # An 8-bit image
    cv2.imshow('BlobsIP', img)
    cv2.waitKey(0)
    img.shape --> [244 767 3]
    hist = np.histogram(img, bins=256)
    cv2.imshow('Histogram', hist)
    cv2.waitKey(0)
    cv2.imshow('Mask', img[:,:,1] > 20)
    cv2.waitKey(0)
```

### Segmentation quality

The **quality** of a segmentation depends on what you want to do with it. Segmentation algorithms must be chosen and evaluated with an application in mind.

## 2.2 Thresholding

**Thresholding** is a simple segmentation process. It produces a binary image `B` by labeling each pixel in or out of the region of interest by comparison of the graylevel with a threshold `T`:

![](./Figures/VisComp_Fig2-3.PNG){width=50%}

### How do we choose T?

There are several ways to choose `T`:

- Trial and error
- Compare results with ground truth
- Automatic methods (we'll discuss ROC curves later on)

## 2.3 Chromakeying

If we can control the background of a picture, segmentation becomes easier. Assume we use a green screen.

**Chromakeying** describes the process of plain distance measuring, in this case for green:

![](./Figures/VisComp_Fig2-4.PNG){width=50%}

This has some problems:

- Variation is _not_ the same in all three channels
- The alpha mask is hard: `I_comp = I_alpha I_a + (1 - I_alpha) I_b`

### Background color variation

Colors in the background may vary a lot (especially the intensity of colors and especially of green).

On way which can make segmentation easier is to **normalize colors** (per pixel):

- Intensity `I = R + G + B`
- Normalized color `(r, g, b) = (R/I, G/I, B/I)`

## 2.4 ROC Analysis

A **Receiver Operating Characteristic (ROC)** curve characterizes the performance of a binary classifier. A binary classifier distinguishes between two different types of things, e.g.:

- Healthy/afflicted patients
- Pregnancy tests
- Object detection
- Foreground/background image pixels

### Classification errors

Binary classifiers make errors. There are two types of input to a binary classifier:

- positives
- negatives

This results in four possible outcomes in any test:

- True positive
- True negative
- _False negative_
- _False positive_

### ROC Curve

The **ROC curve** characterizes the error trade-off in binary classification tasks. It plots the TP fraction against the FP fraction:

- _TP fraction_ (**sensitivity**) is `True positive count / positive count`
- _FP fraction_ (1-sensitivity) is `False positive count / negative count`

The result could look something like this:

![](./Figures/VisComp_Fig2-5.PNG){width=50%}

### Operating points

We can choose an **operating point** by assigning relative costs and values to each outcome:

- `V_TN`: value of true negative
- `V_TP`: value of true positive
- `C_FN`: cost of false negative
- `C_FP`: cost of false positive

When we assigned these costs, we can choose the point on the ROC curve with **gradient**:

![](./Figures/VisComp_Fig2-6.PNG){width=50%}

For simplicity, we often set `V_TN = V_TP = 0`.
¨

## 2.5 Limits of Thresholding

Why can we segment images much better by eye than through thresholding processes? Because we can consider the context of the whole image.

We might improve results by considering _image context_ through **surface coherence**.

## 2.6 Pixels

### Pixel connectivity

We need to define which pixels are connected/neighbors.

We define two different types of **pixel neighborhoods**:

![](./Figures/VisComp_Fig2-7.PNG){width=50%}

### Pixel paths

A **4-connected path** between pixels `p_1` and `p_n` is a set of pixels `{p_1, p_2,..., p_n}` such that `p_i` is a 4-neighbor of `p_i+1`.

In an **8-connected path**, `p_i` is an 8-neighbor of `p_i+1`.

### Connected regions

A region is 4-connected if it contains a 4-connected path between any two of its pixels. Analog, a region is 8-connected if it contains an 8-connected path between any two of its pixels.

## 2.7 Region growing

**Region growing** is defined by the following steps:

1. Start from a seed point/pixel or region
2. Add neighboring pixels that satisfy the criteria defining the region
3. Repeat until we can include no more pixels

In code, this could look like the following example:

```python
def regionGrow(I, seed) :
    X, Y = I.shape
    visited = np.zeros((X, Y))
    visited[seed] = 1
    boundary = []
    boundary.append(seed)

    while len(boundary) > 0 :
            nextPoint = boundary.pop()
            # Here is the important function which defines the region criteria
            if include(nextPoint, seed) :
                visited[nextpoint] = 2
                for (x, y) in neighbors(nextPoint) :
                    if visted[x, y] == 0 :
                        boundary.append((x, y))
                        visited [x, y] = 1
```

### Variations

There are three key indicators which lead to variation:

- Seed selection
- Inclusion criteria
- Boundary constraints and snakes

**Seed selection** can happen in different ways. This may be either by hand (point and click), or automatically by conservative thresholding.

The **inclusion criteria** could either be done by graylevel thresholding or by a _graylevel distribution model_:

- Use mean `mu` and standard deviation `sigma` in seed region and then:
  - include if `(I(x, y) - mu)^2 < (n sigma)^2` (with for example `n = 3`)
  - this also leads to the ability to update the mean and standard deviation after every iteration

### Snakes

A **snake** is an active contour. It is a polygon, i.e., an ordered set of points joined up by lines. Each point on the contour moves away from the seed while its image neighborhood satisfies an inclusion criterion.

## 2.8 Distance Measures

With a **plain background-subtraction metric** we do the following calculations:

- `I_alpha = | I - I_bg | > T`
- `T = [20 20 10]` (for example)
- `I_bg` is the background image

The background image is obtained by a "previous image", for example before a car drives into the scene. The color of the background is determined on a per-pixel basis.

When possible, we should fit a Gaussian model per pixel, just as we did for an entire green-screen. This leads to the following, better way of doing distance measurements:

![](./Figures/VisComp_Fig2-8.PNG){width=50%}

## 2.9 Spatial Relations

We introduce the concept of **Markov Random Field** for spatial relations:

- _Markov chains_ have a 1D structure. At every time, there is one state (which enables use of dynamic programming)
- _Markov Random Fields_ break this 1D structure. It is a field of sites, each of which has a label. The labels at one site depend on others, there are no 1D structure dependencies.

### Solving MRFs with graph cuts

_something something_

## 2.10 Morphological Operations

**Morphological operators** are local pixel transformers for processing region shapes. They are most often used on binary images. Logical transformations are based on comparison of pixel neighborhoods with a pattern.

### Example: 8-neighbor erode

The **8-neighbor erode** works by simply erasing any foreground pixel that has one eight-connected neighbor that belongs to the background.

_Example:_

![](./Figures/VisComp_Fig2-9.PNG){width=50%}

The contrast to this function is the **8-neighbor dilate**, where we simply paint any background pixel that has one 8-connected neighbor that is foreground.

### Structuring elements

Morphological operations take two arguments:

- A binary image
- A structuring element

We compare the structuring element to the neighborhood of each pixel, which determines the output of the morphological operation.

We can think of **binary images** and the structuring elements as _sets_ containing the pixels with value `1`.

![](./Figures/VisComp_Fig2-10.PNG){width=50%}

### Fitting, Hitting and Missing

We define the following three terms:

- `S` _fits_ `I` at `x` if `{y : y = x + s, s in S} subset I`
- `S` _hits_ `I` at `x` if `{y : y = x - s, s in S} intersection I != emptyset`
- `S` _misses_ `I` at `x` if `{y : y = x - s, s in S} intersection I = emptyset`

### Erosion

The image `E = I circ- S` is the **erosion** of image `I` by structuring element `S`:

![](./Figures/VisComp_Fig2-11.PNG){width=50%}

### Opening and Closing

The **opening** of `I` by `S` is defined by:

![](./Figures/VisComp_Fig2-12.PNG){width=50%}

The **closing** of `I` by `S` is defined by:

![](./Figures/VisComp_Fig2-13.PNG){width=50%}

### Skeletonization and the Medial Axis Transform

The **skeleton** and **medial axis transform (MAT)** are stick-figure representations of a region `X subset R^2`.
Simply speaking, one might start a "grass fire" at the boundary of the region, and the skeleton is then defined as the set of points at which two fire fronts meet.

Example:

![](./Figures/VisComp_Fig2-14.PNG){width=50%}

With a **medial axis transform** you remember for each point on the skeleton the distance you travelled to get to that point. This way, the whole shape can be reconstructed from a MAT.

# 3. Convolution and Filtering

## 3.1 Linear Shift-Invariant Filtering

**Linear shift-invariant filtering** is about modifying pixels base don its _neighborhood_. Linear means that it should be a _linear combination_ of neighbors. Shift-invariant means that we do the _same thing for each pixel_. This approach is useful for:

- Low-level image processing operations
- Smoothing and noise reduction
- Sharpening
- Detecting or enhancing features

### 3.1.1 Linear Filtering

$L$ is a **linear** operation if:

$$
L[\alpha I_1 + \beta I_2] = \alpha L [I_1] + \beta L [I_2]
$$

Linear operations can be written as:

$$
I'(x, \, y) = \sum_{(i, \, j) \in \mathcal{N}(x, \, y)} K(x, \, y; \, i, \, j)I(i, \, j)
$$

Where $I$ is the input image, $I'$ is the output of the operation, and $K$ is the **kernel** of the operation. $\mathcal{N}(m, \, n)$ denotes the _neighborhood_ of $(m, \, n)$.

> Operations are **shift-invariant** if $K$ does _not_ depend on $(x, \, y)$, i.e. we when using the same weights everywhere!

## 3.2 Correlation

In this approach, we take a **correlation mask** and apply it to an image.

> Correlation is as if we had a template (the mask), and search for it in our image.

This would look as follows:

![](./Figures/VisComp_Fig3-1.PNG){width=50%}

The linear operation of correlation looks as follows:

$$
I' = K \circ I \\ I'(x, \, y) = \sum_{(i, \, j) \in \mathcal{N}(x, \, y)}K(i, \, j)I(x+i, \, y + j)
$$

This represents the linear weights as an image.

## 3.3 Convolution

> Compared to correlation, where we looked at the neighborhood of a pixel and applied what we learned from the neighborhood to the single pixel, in convolution we look at a single pixel and apply what we can learn from it to its neighborhood.

![](./Figures/VisComp_Fig3-2.PNG){width=50%}

The linear operation of convolution is given by:

$$
I' = K * I \\ I' (x, \, y) = \sum_{(i, \, j) \in \mathcal{N}(x, \, y)}K(i, \, j)I(x-i, \, y-j)
$$

This too represents the linear weights as an image, it is actually the same as correlation, but with a reversed kernel.

### 3.3.1 Correlation vs Convolution

![](./Figures/VisComp_Fig3-3.PNG){width=50%}

## 3.4 Separable Kernels

**Separable filters** can be written as $K(m, \, n) = f(m)g(n)$. For a rectangular neighborhood with size $(2M + 1) \times (2N + 1)$, $I'(m, \, n) = f * (g * I(\mathcal{N}(m, \, n)))$. We can rewrite this to:

$$
I''(m, \, n) = \sum_{j = -N}^N g(j)I(m, \, n-j) \\ I'(m, \, n) = \sum_{i = -M}^Mf(i)I''(m-i, \, n)
$$

## 3.5 Gaussian Kernel

The idea of the **Gaussian kernel** is that we weight the contributions of neighboring pixels by their nearness:

![](./Figures/VisComp_Fig3-4.PNG){width=50%}

### 3.5.1 Gaussian Smoothing Kernels

The amount of smoothing when using Gaussian kernels depends on $\sigma$ and on the window size.

The top 5 reasons to use Gaussian smoothing are:

1. Rotationally symmetric
2. Has a single lobe (neighbor's influence decreases monotonically)
3. Still one lobe in frequency domain (no corruption from high frequencies)
4. Simple relationship to $\sigma$
5. Easy to implement efficiently

## 3.6 Filter Examples

**Differential filters**

- _Prewitt operator:_

$$
\begin{bmatrix}
    -1 & 0 & 1 \\
    -1 & 0 & 1 \\
    -1 & 0 & 1
\end{bmatrix}
$$

- _Sobel operator:_

$$
\begin{bmatrix}
    -1 & 0 & 1 \\
    -2 & 0 & 2 \\
    -1 & 0 & 1
\end{bmatrix}
$$

**High-pass filters**

- _Laplacian operator:_

$$
\begin{bmatrix}
    0 & 1 & 0 \\
    1 & -4 & 1 \\
    0 & 1 & 0
\end{bmatrix}
$$

- _High-pass filter:_

$$
\begin{bmatrix}
    -1 & -1 & -1 \\
    -1 & 8 & -1 \\
    -1 & -1 & -1
\end{bmatrix}
$$

# 4. Image Features

## 4.1 Template Matching

**Template matching** describes the problem of locating an object, described by a template $t(x, \, y)$, in the image $s(x, \, y)$. This is done by searching for the best match by minimizing mean-squared error:

$$
\begin{aligned}
    E(p, \, q) &= \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} \lbrack s(x, \, y) - t(x-p, \, y-q) \rbrack^2 \\
    &= \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} |s(x, \, y)|^2 + \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} |t(x, \, y)|^2 - 2 \cdot \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} s(x, \, y) \cdot t(x-p, \, y-q)
\end{aligned}
$$

Equivalently, we can _maximize_ the **area correlation**:

$$
r(p, \, q) = \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} s(x, \, y) \cdot t(x-p, \, y-q) = s(p, \, q) * t(-p, \, -q)
$$

The area correlation is equivalent to the convolution of image $s(x, \, y)$ with impulse response $t(-x, \, -y)$.

## 4.2 Edge Detection

One idea, in a continuous-space, is to detect the local gradient:

$$
|\text{grad}(f(x, \, y))| = \sqrt{(\frac{\partial f}{\partial x})^2 + (\frac{\partial f}{\partial y})^2}
$$

We mostly use the following **edge detection filters**:

![](./Figures/VisComp_Fig3-5.PNG){width=50%}

### 4.2.1 Laplacian Operator

The idea of a **Laplacian operator** is to detect discontinuities by considering the second derivative and searching for _zero-crossings_ (those mark edge locations):

$$
\nabla^2 f(x, \, y) = \frac{\partial^2 fx, \, y()}{\partial x^2} + \frac{\partial^2 f(x, \, y)}{\partial y^2}
$$

![](./Figures/VisComp_Fig3-6.PNG){width=50%}

We can do a _discrete-space approximation_ by convolution with a $3 \times 3$ impulse response:

$$
\begin{bmatrix}
    0 & 1 & 0 \\
    1 & -4 & 1 \\
    0 & 1 & 0
\end{bmatrix} \quad \text{or} \quad \begin{bmatrix}
    1 & 1 & 1 \\
    1 & -8 & 1 \\
    1 & 1 & 1
\end{bmatrix}
$$

However, the Laplacian operator is sensitive to very fine detail and noise, so we might want to blur the image first.

#### Laplacian of Gaussian

Blurring the image with Gaussian and Laplacian operator can be combined into convolution with **Laplacian of Gaussian operator** (LoG):

$$
\text{LoG}(x, \, y) = - \frac{1}{\pi \sigma^4} \lbrack 1 - \frac{x^2 + y^2}{2 \sigma^2} \rbrack exp ( - \frac{x^2 + y^2}{2 \sigma^2} )
$$

### 4.2.2 Canny Edge Detector

The **Canny edge detector** works with the following steps:

1. Smooth the image with a Gaussian filter
2. Compute the gradient magnitude and angle (Sobel, Prewitt, etc.):

$$
M(x, \, y) = \sqrt{( \frac{\partial f}{\partial x} )^2 + ( \frac{\partial f}{\partial y} )^2} \quad \text{and} \quad \alpha(x, \, y) = \tan^{-1} ( \frac{\partial f}{\partial y} \big / \frac{\partial f}{\partial x} )
$$

3. Apply nonmaxima suppression to gradient magnitude image
4. Double thresholding to detect strong and weak edge pixels
5. Reject weak edge pixels not connected with strong edge pixels

#### Canny nonmaxima suppression

The **Canny nonmaxima suppression** works as follows:

1. Quantize the edge normal to one of the four directions: horizontal, -45deg, vertical, +45deg
2. If $M(x, \, y)$ is smaller than either of its neighbors in edge normal direction, then suppress is, else keep it

#### Canny Thresholding

When using a Canny edge detector, we do double thresholding of the gradient magnitude:

- Strong edge: $M(x, \, y) \geq \Theta_{high}$
- Weak edge: $\Theta_{high} > M(x, \, y) \geq \Theta_{low}$

A typical setting for the thresholds would be $\frac{\Theta_{high}}{\Theta_{low}} \in \lbrack 2, \, 3 \rbrack$.

### 4.2.3 Hough Transform

The **Hough transform** solves the problem of fitting a straight line (or curve) to a set of edge pixels. The Hough transform is a _generalized template matching technique_.

It works the following way:

1. For an edge pixel in the $(x, \, y)$ plane we can draw the different lines that cross the edge pixel (all lines have the form $y = mx + c$). We can draw the $m$ and $c$ values in a $(m, \, c)$ plane and see that all those lines are linearly dependent:

![](./Figures/VisComp_Fig3-7.PNG){width=50%}

2. If we have multiple edge pixels, we can do the same procedure for each of those, giving us a line in the $(m, \, c)$ plane for each edge pixel.
3. We then subdivide the $(m, \, c)$ plane into discrete "bins" and initialize the bin count of each bin to $0$. Each time a bin is crossed by one of the lines of the different edge pixels, we increase its count by one.
4. We then simply have to detect the peaks in the $(m, \, c)$ plane to get our fitted straight line:

![](./Figures/VisComp_Fig3-8.PNG){width=50%}

We might encounter an infinite-slope problem, which can be avoided with an alternative parameterization:

![](./Figures/VisComp_Fig3-9.PNG){width=50%}

## 4.3 Detecting Corner Points

Many applications benefit from features localized in $(x, \, y)$. If edges are well localized but only in one direction, we might want to **detect corners**.

The desirable properties of a corner detector are:

- accurate localization
- invariance against shift, rotation, scale, and brightness change
- robust against noise, high repeatability

*something something what patterns can be localized most accurately?*

![](./Figures/VisComp_Fig3-10.PNG){width=50%}

### 4.3.1 Feature Point Extraction

We have that $SSD \simeq \delta^T M \delta$. Now if we shift our patterns over the picture, we assume it to change the following way:

![](./Figures/VisComp_Fig3-11.PNG){width=50%}

Now we want to find points for which the following is large:

$$
\min \delta^T M \delta \text{ for } ||\delta || = 1
$$

i.e. we want to maximize the eigenvalues of $M$.

#### Keypoint Detection

*something something*

![](./Figures/VisComp_Fig3-12.PNG){width=50%}

# 5. Fourier Transform

## 5.1 Introduction

The idea of the **Fourier Transform** is to represent functions in a new basis. We might think of functions as vectors, with many components, where we can apply a linear transformation to transform the basis (i.e. a dot product with each basis elements).

In the expressions, $u$ and $v$ select the basis elements, so a function of $x$ and $y$ becomes a function of $u$ and $v$. The **basis elements** have the form $e^{-i2 \pi (ux + vy)} = \cos 2 \pi (ux + vy) - i \sin 2 \pi (ux + vy)$. Or:

$$
F(g(x, \, y))(u, \, v) = \int \int_{\mathbb{R}^2} g(x, \, y)e^{i2 \pi (ux + vy)}\text{d}x \text{dy}
$$

The **discrete Fourier transform** therefore is of the form:

$$
F = Uf
$$

where:

- $F$ is the transformed image
- $U$ is the Fourier transform base
- $f$ is the vectorized image

## 5.2 Fourier Basis Functions

![](./Figures/VisComp_Fig4-1.PNG){width=50%}

## 5.3 Phase and Magnitude

The Fourier transform of a real function is complex. It's difficult to plot and to visualize, and instead, we can think of the phase and magnitude of the transform.

The **phase** is the phase of the complex transform, and the **magnitude** is the magnitude of the complex transform. An interesting fact is that all natural images have about the _same magnitude transform_, hence the phase seems to matter much more than the magnitude does.

## 5.4 Convolution Theorem

The **Convolution Theorem** goes as follows:

> The Fourier transform of the convolution of two functions is the product of their Fourier transforms: $F.G = U(f**g)$.
>
> The Fourier transform of the product of two functions is the convolution of the Fourier transform: $F**G = U(f.g)$.

## 5.5 Sampling

The idea of **sampling** is to go from a continuous world to a discrete world, i.e. from a function to a vector. Samples are typically measured on a _regular grid_.

For example, we might want to be able to approximate integrals sensibly.

$$
\text{Sample}_{\text{2D}}(f(x, \, y)) = \sum_{i = - \infty}^{\infty} \sum_{j = - \infty}^{\infty} f(x, \, y) \delta (x-i, \, y-j) = f(x, \, y) \sum_{i = - \infty}^{\infty} \sum_{j = - \infty}^{\infty} \delta (x-i, \, y-j)
$$

### 5.5.1 Fourier Transform of a Sampled Signal

The _Fourier transform of a sampled signal_ is given by the following equalities:

![](./Figures/VisComp_Fig4-2.PNG){width=50%}

### 5.5.2 Nyquist Sampling Theorem

The **Nyquist theorem** says that the sampling frequency must be at least twice the highest frequency, i.e. $\omega_s \geq 2 \omega$. If this is not the case, the signal needs to be bandlimited before sampling, e.g. with a low-pass filter.

## 5.2 Image Restoration

### 5.2.1 Image Restoration Problem

If we have an image transformation of the form:

$$
f(x) \to h(x) \to g(x) \to \tilde{h}(x) \to f(x)
$$

Then, the **inverse kernel** $\tilde{h}(x)$ should compensate the effect of the _image degradation_ $h(x)$, i.e.

$$
(\tilde{h} * h)(x) = \delta(x)
$$

$\tilde{h}$ may be determined more easily in the Fourier space, since:

$$
\mathcal{F}[\tilde{h}](u, \, v) \cdot \mathcal{F}[h](u, \, v) = 1.
$$

To determine $\mathcal{F}[\tilde{h}]$ we need to estimate:

1. The distortion model $h(x)$ or $\mathcal{F}[h](u, \, v)$
2. The parameters of $h(x)$, e.g. $r$ for defocussing

### 5.2.2 Image Restoration: Motion Blur

The **kernel for motion blur** is given by: $h(x) = \frac{1}{2l}(\theta(x_1 + l) - \theta(x_1 - l)) \delta(x_2)$, i.e. the transformation of a light dot into a small line in $x_1$ direction.

The Fourier transformation of this is given by:

![](./Figures/VisComp_Fig4-3.PNG){width=50%}

Which leads to:

- $\hat{h}(u) = \mathcal{F}[h](u) = \text{sinc}(2 \pi ul)$
- $\mathcal{F}[\tilde{h}](u) = \frac{1}{\hat{h}(u)}$

However, the following problems arise:

- The convolution with the kernel $h$ completely cancels the frequencies $\frac{v}{2l}$ for $v \in \mathbb{Z}$. Vanishing frequencies cannot be recovered!
- There is a lot of noise amplification for $\mathcal{F}[h](u, \, v) << 1$.

### 5.2.3 Avoiding Noise Amplification

We can avoid **noise amplification** by a _regularized reconstruction filter_ of the form:

$$
\tilde{\mathcal{F}}[\tilde{h}](u, \, v) = \frac{\mathcal{F}[h]}{|\mathcal{F}[h]|^2 + \epsilon}
$$.

The size of $\epsilon$ implicitly determines an estimate of the noise level in the image, since we discard signal which are dampened below the size $\epsilon$.

# 6. Unitary Transform

## 6.1 Introduction

A digital image can be written as a **matrix**:

$$
f = \begin{bmatrix}
    f(0, \, 0) && f(1, \, 0) && \cdots && f(N-1, \, 0) \\
    f(0, \, 1) && f(1, \, 1) && \cdots && f(N-1, \, 1) \\
    \vdots && \vdots && && \vdots \\
    f(0, \, L-1) && f(1, \, L-1) && \cdots && f(N-1, \, L-1)
\end{bmatrix}
$$

The pixels $f(x, \, y)$ are sorted into the matrix in natural order. This results in $f(x, \, y) = f_{xy}$, where $f_{xy}$ denotes an individual element in common matrix notation.

We might also write an image as a single **vector** of length $L \cdot N$. This makes the math easier:

$$
\vec{f} = \begin{bmatrix}
    f(0, \, 0) \\ f(1, \, 0) \\ \vdots \\ f(N-1, \, 0) \\ f(0, \, 1) \\ \vdots \\ f(N-1, \, 1) \\ \vdots \\ f(N-1, \, L-1)
\end{bmatrix}
$$

## 6.2 Linear Image Processing

Any linear image processing algorithm can be written as:

$$
\vec{g} = H \vec{f}
$$

We define a **linear operator** $O[.]$ as:

$$
O[\alpha_1 \cdot \vec{f}_1 + \alpha_2 \cdot \vec{f}_2] = \alpha_1 \cdot O[\vec{f}_1] + \alpha_2 \cdot O[\vec{f}_2]
$$

for all scalars $\alpha_1, \, \alpha_2$.

But how does one choose $H$

- so $\vec{g}$ separates the salient features from the rest of the image signal?
- so $\vec{g}$ looks better?
- in order for $\vec{g}$ to be sparse?

## 6.3 Unitary Transforms

For a **unitary transform** we might proceed as follows:

1. Sort samples $f(x, \, y)$ of a $M \times N$ image into a column vector of length $MN$
2. Compute the transform coefficients of $\vec{c} = A \vec{f}$, where $A$ is a matrix of size $MN \times MN$

The transform $A$ is said to be **unitary**, if and only if:

$$
A^{-1} = A^{*T} = A^H
$$

If $A$ is real valued, i.e. if $A = A^*$, then the transform is said to be **orthonormal**.

### 6.3.1 Energy Conservation

For any unitary transform $\vec{c} = A \vec{f}$, we obtain

$$
||\vec{c}||^2 = \vec{c}^H\vec{c} = \vec{f}^H A^H A \vec{f} = ||\vec{f}||^2
$$

This means, that every unitary transform is simply a rotation of the coordinate system (and, possibly, sign flips). The vector lengths, i.e. the "energies", are conserved!

### 6.3.2 Image Collection

We might denote **image collections** in the following way:

1. $f_i$ as one image
2. $F = [f_1 \, f_2 \, \cdots \, f_n]$ as an image collection
3. $R_{ff} = E[f_i \, . \, f_i^H] = \frac{F.F^H}{n}$ as an image collection _auto-correlation function_

### 6.3.3 Energy Distribution

With unitary transforms, energy is conserved, but often will be _unevenly distributed_ among the coefficients.

For the auto-correlation matrix, we have that:

$$
R_{cc} = E[\vec{c}\vec{c}^H] = E[A \vec{f} \cdot \vec{f}^H A^H] = A R_{ff}A^H
$$

This leads to the mean squared values (i.e. the "average energies") of the coefficients $c_i$ being on the diagonal of $R_{cc}$:

$$
E[c_i^2] = [R_{cc}]_{i,i} = [AR_{ff}A^H]_{i,i}
$$

### 6.3.4 Eigenmatrix of Auto-Correlation Matrix

The **eigenmatrix** $\Theta$ of an auto-correlation matrix $R_{ff}$ fulfills the following rules:

- $\Theta$ is unitary
- The columns of $\Theta$ form a set of eigenvectors of $R_{ff}$, i.e. $R_{ff} \Theta = \Theta \Lambda$, where $\Lambda$ is a diagonal matrix of eigenvalues $\lambda_i$.
- $R_{ff}$ is symmetric non-negative definite, hence $\lambda_i \geq 0$ for all $i$
- $R_{ff}$ is a normal matrix, i.e. $R_{ff}^HR_{ff} = R_{ff}R_{ff}^H$, hence the unitary eigenmatrix exists

### 6.3.5 Karhunen-Loeve Transform (aka PCA)

The **Karhunen-Loeve transform** (_KL-transform_) describes the unitary transform with the matrix $A = \Theta^H$, where the columns of $\Theta$ are ordered according to decreasing eigenvalues.

- The transform coefficients are pairwise uncorrelated
- No other unitary transform packs as much energy into the first $J$ coefficients, where $J$ is arbitrary
- Mean squared approximation error by choosing only the first $J$ coefficients is minimized

_Example:_

![](./Figures/VisComp_Fig5-1.PNG){width=50%}

### 6.3.6 Basis Images and Eigenimages

For any unitary transform, the _inverse transform_ $\vec{f} = A^H \vec{c}$ can be interpreted in terms of the superposition of basis images (columns of $A^H$) of size $M \times N$.
If the transform is a KL transform, the basis images, which are the eigenvectors of the auto-correlation matrix $R_{ff}$, are called **eigenimages.**
If energy concentration works well, only a limited number of eigenimages is needed to approximate a set of images with a small error. These eigenimages form an optimal linear subspace of dimensionality $J$.

To recognize complex patterns, e.g. faces, large portions of an image might have to be considered. With a transform of the form $\vec{c} = W\vec{f}$ we can reduce the dimensionality from $M \times N$ to $J$ by representing the image by $J$ coefficients.

### 6.3.7 Simple Recognition

For a **simple recognition** we can use the _simple euclidean distance (SSD)_ between two images and let the best match "win":

$$
\text{arg min}_i D_i = ||I_i - I||
$$

However, this is computationally expensive, i.e. it requires the presented image to be correlated with every image in the database!

### 6.3.8 Eigenspace Matching

We consider the PCA (aka KLT aka closes rank-k approximation property of SVD) $\hat{I}_i \simeq Ep_i$. Then:

$$
I_i - I = \hat{I}_i - \hat{I} \simeq E(p_i - p) \\ ||I_i - I|| \simeq ||p_i - p|| \\ \text{arg min}_i D_i = ||I_i - I|| \simeq ||p_i - p||
$$

with $\hat{I} = I - \bar{I}$ and $p = E^T \hat{I}$. This is _much cheaper to compute!_

### 6.3.9 Fisherfaces

The key ideas of **Fisherfaces** are as follows:

- Find directions where the ratio between/within individual variances are maximized
- Linearly project to the basis where the dimensions with good signal/noise ratios are maximized

![](./Figures/VisComp_Fig5-2.PNG){width=50%}

The eigenimage method maximizes _scatter_ within the linear subspace over the entire image set, regardless of the classification tasks:

$$
W_{opt} = \text{arg max}_W ( \text{det} ( WRW^H ) )
$$

The idea of the **Fisher linear discriminant analysis** is to maximize between-class scatter, while minimizing within-class scatter:

![](./Figures/VisComp_Fig5-3.PNG){width=50%}

#### Fisher Images and Varying Illumination

All images of the same Lambertian surface with different illumination (without shadows) lie in a 3D linear subspace. There is a single point source at infinity such that:

![](./Figures/VisComp_Fig5-4.PNG){width=50%}

# 7. Image Compression

## 7.1 JPEG Image Compression

### 7.1.1 Block-Based Discrete Cosine Transform (DCT)

We essentially do discrete **Cosine Transform** on our image we wish to compress:

![](./Figures/VisComp_Fig5-5.PNG){width=50%}

**DCT** is a variant of discrete Fourier transform with real numbers and a very fast implementation. We can choose the block size, its size will have an influence on the transform:

- Small blocks: faster computation and correlation between neighboring pixels
- Large blocks: better compression in smooth regions

### 7.1.2 Image Compression Using DCT

DCT enables _image compression_ by concentrating most image information in the low frequencies. We loose unimportant image info, i.e. high frequencies, by cutting $B(u, \, v)$ in the bottom right corner. The decoder computes the inverse DCT (iDCT).

## 7.2 Image Pyramid

![](./Figures/VisComp_Fig5-6.PNG){width=50%}

The application of **scaled representations,** such as _image pyramids_, is to look at coarse scaled and then refine with finer scaled. For example, a "good" edge at a finer scale has parents at some coarser scale.

### 7.2.1 Gaussian Pyramid

The different levels of the **Gaussian Pyramid** are smooth with Gaussians, since a Gaussian times a Gaussian is another Gaussian.

![](./Figures/VisComp_Fig5-7.PNG){width=50%}

### 7.2.2 Laplacian Pyramid

For the synthesis of a **Laplacian Pyramid** we have that:

- It preserves the difference between unsampled Gaussian pyramid levels and Gaussian pyramid levels
- It's a band pass filter: Each level represents spatial frequencies unrepresented at other levels

![](./Figures/VisComp_Fig5-8.PNG){width=50%}

## 7.3 Discrete Wavelet Transform

### 7.3.1 Haar Transform

The **Haar Transform** with the _Haar Basis_ has the following properties:

- Real and orthogonal
- Transition at each scale $p$ is localized according to $q$

_Comparison of DCT and Haar basis:_

![](./Figures/VisComp_Fig5-9.PNG){width=50%}

### 7.3.2 Lifting

_Analysis filters:_

![](./Figures/VisComp_Fig5-10.PNG){width=50%}

_Synthesis filters:_

![](./Figures/VisComp_Fig5-11.PNG){width=50%}

# 8. Optical Flow

## 8.1 Brightness Constancy

We define **optical flow** as the apparent motion of brightness patterns. Ideally, the optical flow is the _projection_ of the three-dimensional velocity vectors of the image.

$I(x, \, y, \, t)$ is the brightness at $(x, \, y)$ at time $t$. This way we can define:

_Brightness constancy assumption:_

$$
I(x + \frac{dx}{dt}\delta t, \, y + \frac{dy}{dt}\delta t, \, t + \delta t) = I(x, \, y, \, t)
$$

_Optical flow constraint equation:_

$$
\frac{dI}{dt} = \frac{\partial I}{\partial x} \frac{dx}{dt} + \frac{\partial I}{\partial y} \frac{dy}{dt} + \frac{\partial I}{\partial t} = 0
$$

## 8.2 The Aperture Problem

If we do the following substitution:

$$
u = \frac{dx}{dt}, \, v = \frac{dy}{dt}, \, I_x = \frac{\partial I}{\partial x}, \, I_y = \frac{\partial I}{\partial y}, \, I_t = \frac{\partial I}{\partial t}
$$

we arrive at the following equation:

$$
I_x u + I_y v + I_t = 0
$$

However, this is one equation in two unknowns, which is known as the **Aperture Problem.**

![](./Figures/VisComp_Fig6-1.PNG){width=50%}

Optical flow is not always well-defined! We can compare the different kinds of flows:

- _Motion Field:_ Projection of 3D motion field
- _Normal Flow:_ Observed tangent motion
- _Optical Flow:_ Apparent motion of the brightness pattern, hopefully equal to motion field

![](./Figures/VisComp_Fig6-2.PNG){width=50%}

## 8.3 Regularization

We might use the **Horn & Schuck algorithm,** described as follows:

- We have an additional smoothness constraint

$$
e_s = \int \int ((u_x^2 + u_y^2) + (v_x^2 + v_y^2)) dxdy
$$

- besides our Optical Flow constraint equation term:

$$
e_c = \int \int (I_x u + I_y v + I_t)^2 dxdy
$$

The goal is to _minimize_ $e_s + \lambda e_c$ !s

## 8.4 Lucas-Kanade

![](./Figures/VisComp_Fig6-3.PNG){width=50%}

With respect to singularities and the aperture problem, we proceed as follows. Let:

$$
M = \sum (\nabla I)(\nabla I)^T \quad \text{and} \quad b = \begin{bmatrix} - \sum I_x I_t \\ - \sum I_y I_t  \end{bmatrix}
$$

Then, the algorithm is as simple as solving at each pixel for $U$ in $MU = b$. $M$ is _singular_ if all gradient vectors point in the same direction.

## 8.5 Coarse To Fine

The local gradient method has some limitation:

- Fails when the intensity structure within the window is poor
- Fails when the displacement is large (typical operating range is motion of 1 pixel per iteration!)

We can combat this with a Pyramid or "_Coarse-to-fine_" estimation:

![](./Figures/VisComp_Fig6-4.PNG){width=50%}

## 8.6 Parametric Motion Models

_Global_ motion models offer:

- more constrained solutions than smoothness models (Horn-Schunck)
- integration over a larger area than a translation-only model can accommodate (Lucas-Kanade)

## 8.7 Bayesian Flow

Some low-level human motion illusions can be explained by adding an uncertainty model to the Lucas-Kanade tracking.

This will result in the brightness constancy equation _with noise_ to be:

$$
I(x, \, y, \, t) = I(x + v_x \delta t, \, y + v_y \delta t, \, t + \delta t) + \eta
$$

## 8.8 SSD Tracking

For large displacements, we do _template matching_ as we used in stereo disparity search:

1. Define a small area around a pixel as the template
2. Match the template against each pixel within a search are in the next image
3. Use a mean measure such as correlation, normalized correlation, or sum-of-squares difference
4. Choose the maximum (or minimum) as the match

![](./Figures/VisComp_Fig7-1.PNG){width=50%}

# 9. Video Compression

## 9.1 Perception Of Motion

The human visual system is specifically sensitive to motion. Our eyes follow motion automatically. Some distortions, however, are not as perceivable as in image coding.

Visual perception is limited to <24Hz, therefore, a succession of images will be perceived as continuous if the frequency is sufficiently high. We still need to avoid aliasing (i.e. the "wheel effect"), therefore high-rendering frame-rates desired in computer games (needed due to absence of motion blur).

## 9.2 Bloch's Law

**Bloch's Law** states that for human perception, it doesn't really matter whether we see something at 100% brightness for 10ms, or something at 50% for 20ms.

## 9.3 Video Format

A **video** is essentially a sequence of 2D images, so for storing a video, we need to simply store each 2D image and their corresponding place in time.

### 9.3.1 Interlaced Video Format

The **interlaced video format** consists of two temporally shifted half images, which increases the frequency from 25 to 50 Hz (for example).

![](./Figures/VisComp_Fig7-2.PNG){width=50%}

This results in a reduction of spatial resolution.

## 9.4 Lossy Video Compression

With **lossy video compression** we take advantage of redundancy:

- Spatial correlation between neighboring pixels
- Temporal correlation between frames

With the above information, we can _drop perceptually unimportant details._

### 9.4.1 Temporal Processing

With a usually high frame rate, we have significant temporal redundancy. We have two possible representations along temporal dimension:

- _Transform/subband methods:_
    - Good for textbook case of constant velocity uniform global motion
    - Inefficient for nonuniform motion, i.e. real-world motion
    - Requires many frame stores
- _Predictive methods:_
    - Good performance using only two frame stores
    - However, simple frame differencing is not enough

The goal is to exploit temporal redundancy by **predicting the current frame** based on previously coded frames. There are three types of **coded frames:**

- _I-frame:_ Intra-coded frame, coded independently of all other frames
- _P-frame:_ Predicatively coded frame, coded based on previously coded frame
- _B-frame:_ Bi-directionally predicted frame, coded based on both previous and future coded frames

![](./Figures/VisComp_Fig7-3.PNG){width=50%}

Temporal redundancy reduction may be _ineffective_ when there are many scene changes or when there is high motion.

### 9.4.2 Video Compressor Diagram

![](./Figures/VisComp_Fig7-4.PNG){width=50%}

### 9.4.3 Motion-compensated Prediction

Simple frame differencing fails when there is motion. We can account for motion with a **motion-compensated (MC) prediction.** The practical approach to this is so called **Block-Matching Motion Estimation:**

- Partition each frame into blocks, e.g. 16x16 pixels
- Describe the motion of each block
- No object identification required
- Good, robust performance

![](./Figures/VisComp_Fig7-5.PNG){width=50%}

_Example of fast motion estimation search: 3-step log search_

![](./Figures/VisComp_Fig7-6.PNG){width=50%}

### 9.4.4 Bidirectional MC Prediction

![](./Figures/VisComp_Fig7-7.PNG){width=50%}

**Bidirectional MC-Prediction** is used to estimate a block in the current frame from a block in:

1. Previous frame
2. Future frame
3. Average of a block from the previous frame and a block from the future frame
4. Neither, i.e. code the current block without prediction

## 9.5 Basic Video Compression Architecture

### 9.5.1 Example Video Encoder

![](./Figures/VisComp_Fig7-8.PNG){width=50%}

### 9.5.2 Example Video Decoder

![](./Figures/VisComp_Fig7-9.PNG){width=50%}

## 9.6 Objective Quality Measure: PSNR

The error for one pixel, as the difference between the original and decoded value:

$$
e(v, \, h) = \hat{x}(v, \, h) - x(v, \, h))
$$

The mean-squared-error, MSE, over an image:

$$
e_{mse} = \sqrt{\frac{1}{N \cdot M} \sum_{v = 1}^N \sum_{h = 1}^M e^2(v, \, h)}
$$

The peak-signal-to-noise-ration is then given by:

$$
PSNR = \frac{(\text{maximum value of } x)^2}{e_{mse}^2} = 10 \cdot \log_10 \Big(\frac{(2^K)^2}{e_{mse}^2} \Big) \text{dB}
$$

# 10. Radon Transform

## 10.1 Medical Imaging

There are two forms of _radiation sources:_

- Outside the body: X-rays and ultrasound
- Inside the body: magnetic resonance imaging (MRI), positron emission tomography (PET), and single photon emission computed tomography (SPECT)

### 10.1.1 Computed Tomography (CT)

The idea behind _CT data collection_ is to quantify the tendency of objects to absorb or scatter x-rays given by the optical density of the material measured in terms of attenuation coefficient.

A _CT image setup_ could look as follows:

![](./Figures/VisComp_Fig7-10.PNG){width=50%}

### 10.1.2 Image Reconstruction

The mathematical problem posed by **CT reconstruction** is to calculate the image data from the projection values. For the simple image of four pixels shown, algebra can be used to solve for the pixel values. For the larger images of clinical CT, algebraic solutions become unfeasible.

![](./Figures/VisComp_Fig7-11.PNG){width=50%}

### 10.1.3 Image Acquisition

The basic principle setup for **CT image acquisition** looks as follows:

![](./Figures/VisComp_Fig7-12.PNG){width=50%}

The intensity of the X-ray where it hits the detector depends on the width of the object and the length of the path travelled through the object and the air.

X-rays, moving along a straight line, have at distance $s$ the _intensity_ $I(s)$. This means, that if the x-ray travelled $\delta s$ distance, its intensity will be reduced by $\delta I(s)$.
The reduction depends on the intensity and _optical density_ $u(s)$ of the material. For small $\delta s$, it holds that:

$$
\frac{\delta I(s)}{I(s)} = - u(s) \delta s
$$

Combining all the contributions to the reduction in the intensity of an X-ray travelling along line $L$ given by all the parts of the body that it travels through, the **attenuation** (i.e. reduction in intensity) is given by:

$$
Rf(L) = \int_L f(x) |dx|
$$

This is also called the **Radon transform** of function $f(x, \, y)$.

## 10.2 The Radon Transform

### 10.2.1 Introduction

![](./Figures/VisComp_Fig7-13.PNG){width=50%}

This X-ray will pass through a series of points $(x, \, y)$ at which the optical density is $u(x, \, y)$. Using the equation for a straight line these points are given by:

$$
(x, \, y) = (\rho \cos(\theta) - s \sin(\theta), \, \rho \sin(\theta) + s \cos(\theta)),
$$

where $s$ is the arc length. In this case, we now have:

$$
I_{finish} = I_{start}^{-R(\rho, \, \theta)}
$$

where:

$$
R(\rho, \, \theta) = \int_{- \infty}^{\infty} \int_{- \infty}^{\infty} u(x, \, y) \delta(\rho - x \cos(\theta) - y \sin (\theta)) \, dx \, dy,
$$

where $\delta(x, \, y)$ is the Dirac Delta function.

### 10.2.2 Properties

_Linearity:_

$$
g(x, \, y) = \sum_q \alpha_q g_q (x, \, y) \implies \hat{g}(\rho, \, \theta) = \sum_q \alpha q \hat{g}_q(\rho, \, \theta))
$$

_Shifting:_

$$
h(x, \, y) = g(x-x_0, \, y-y_0) \implies \\ \hat{h}(\rho, \, \theta) = \hat{g}(\rho - x_0 \cos(\theta) - y_0 \sin(\theta), \, \theta)
$$

_Rotation:_

$$
h(r, \, \phi) = g(r, \, \phi - \phi_0) \implies \\ \hat{h}(\rho, \, \theta) = \hat{g}(\rho, \, \theta - \phi_0)
$$

_Convolution:_

$$
h(x, \, y) = f(x, \, y) ** g(x, \, y) \implies \\ \hat{h}(\rho, \, \theta) = \hat{f}(\rho, \, \theta) * \hat{g}(\rho, \theta)
$$

### 10.2.3 Point Source

Here, an arbitrary point source $(x^*, \, y^*)$ is assumed:

$$
g(x, \, y) = \delta(x-x^*) \delta(y-y^*) \implies \\ \hat{g}(\rho, \, \theta) = \delta(\rho - x^* \cos(\theta) - y^* \sin(\theta))
$$

The figure below illustrates a point source and the corresponding Radon transform:

![](./Figures/VisComp_Fig7-14.PNG){width=50%}

### 10.2.4 Image Reconstruction: Algebraic Formulation

We assume that the attenuation of the material within each pixel is constant and proportional to the area of the pixel illuminated by the beam, i.e.:

$$
k_{ij} = \frac{\text{area of pixel } j \text{ illuminated by ray } i}{\text{total area of pixel } j}, \quad i = 1, \, ..., \, l, \, \, j = 1, \, ..., \, nm
$$

Hence, the algebraic model reads as:

$$
Kf = g,
$$

where $f$ is the BW plane/volumetric image to be retrieved (reshaped into a vector) and $g$ is the attenuation measurements from the CT system.

We can then transform the over determined matrix $K$ into a system of _normal equations:_

$$
K^TKf = K^Tg
$$

However, this solution does not exist, is not unique or not continuously dependent on the given data.

## 10.3 Fourier / Central Slice Theorem

The **Fourier / Central Slice Theorem** is as following:

$$
\mathcal{G}(q, \, 0) = \mathcal{F}(q \cos(0), \, q \sin(0)),
$$

where $\mathcal{G}$ is the 1D Fourier transform of the attenuation measurements $g = Rf$ and $\mathcal{F}$ is the 2D Fourier transform of the object slice $f(x, \, y)$ evaluated at a particular point.

In words:

> The Fourier transform of a parallel projection of an image $f(x, \, y)$ taken at an angle $\theta$ gives a slice of the two-dimensional transform, $F(u, \, v)$, subtending an angle $\theta$ with the $u$-axis. In other words, the Fourier transform of $P_{\theta}(t)$ gives the values of $F(u, \, v)$ along line $BB$ in the following figure:

![](./Figures/VisComp_Fig7-15.PNG){width=50%}

## 10.4 Filtered Backprojection

### 10.4.1 Algorithm

![](./Figures/VisComp_Fig7-16.PNG){width=50%}

### 10.4.2 Improvements

We can apply a high-pass filter after the 1D FT projection. This will help with improving blurriness when using the naive backprojection algorithm:

![](./Figures/VisComp_Fig7-17.PNG){width=50%}

# Part 2: Computer Graphics

# 1. Introduction

## 1.1 Computer Graphics

**Computer graphics** describes the use of computers to synthesize and manipulate visual information.

It is used in many, if not all the movies we see today to synthesize movie scenes which are fictional or too expensive to shoot in real life.

In general, computer graphics is about building computational models of the real world!

## 1.2 Foundations Of Computer Graphics

Building models of the world demands sophisticated theory and systems, such as:

- geometric representations
- sampling theory
- integration and optimization
- perception
- physics-based modeling
- parallel, heterogeneous processing
- graphics-specific programming languages

## 1.3 Modeling And Drawing A Cube

We start by exploring how we draw a cube, i.e. how we describe a cube and how we can visualize our model.

We assume our cube to be as follows:

- Centered at origin `(0, 0, 0)`
- Has dimensions `2 x 2 x 2`
- Edges are aligned with `x, y, z` axes

We can then define the coordinates of the cube _vertices_ to be:

```bnf
A: (1, 1, 1)        E: (1, 1, -1)
B: (-1, 1, 1)       F: (-1, 1, -1)
C: (1, -1, 1)       G: (1, -1, -1)
D: (-1, -1, 1)      H: (-1, -1, -1)
```

With the information above, we can define the _edges_ of our cube to be:

```bnf
AB, CD, EF, GH,
AC, BD, EG, FH,
AE, CG, BF, DH
```

But how do we draw our 3D vertices as a 2D flat image? The basic strategy is:

1. Map the 3D vertices to 2D points in the image
2. Draw straight lines between 2D points corresponding to the edges

## 1.4 Perspective Projection

The **perspective projection** describes what a simple _pinhole_ model of a camera does:

![](./Figures/VisComp_Fig8-1.PNG){width=50%}

With this simple projection we can answer the following question: Where does a point `p = (x, y, z)` from our real world end up in the image `q = (u, v)`?

$$
\begin{aligned}
&v = \frac{y}{z}, \quad \text{(v is simply the slope y/z)} \\
&u = \frac{x}{z}
\end{aligned}
$$

![](./Figures/VisComp_Fig8-2.PNG){width=50%}

Applying the above learned information, we can draw our cube, assuming that the camera is at `c = (2, 3, 5)`, as follows:

1. We get the following coordinates for our vertices of the cube:

```bnf
A: (1/4, 1/2)       E: (1/6, 1/3)
B: (3/4, 1/2)       F: (1/2, 1/3)
C: (1/4, 1)         G: (1/6, 2/3)
D: (3/4, 1)         H: (1/2, 2/3)
```

2. We can draw the points on a 2D grid and connect the points which belong to our previously defined edges:

![](./Figures/VisComp_Fig8-3.PNG){width=50%}

## 1.5 Drawing On A Raster Display

### 1.5.1 Introduction

Considering we have solved our cube representation, a natural question to ask would be how a computer can draw lines.
A common abstraction is that an image is represented as a _2D grid of pixels._ Each pixel can take on a unique color value.

**Rasterization** describes the process of converting a continuous object to a discrete representation on a pixel grid (or _raster grid_). However, this approach leads to a fundamental question which we must solve: Which pixels should we color in to depict the line?

1. One simple approach is to light up all pixels intersected by the line:

![](./Figures/VisComp_Fig8-4.PNG){width=50%}

2. In modern graphics hardware, we use an approached called the _diamond rule:_

![](./Figures/VisComp_Fig8-5.PNG){width=50%}

The last question we might want to answer is how do we find the pixels satisfying a chosen rasterization rule? We could check every single pixel in the image to see if it meets the condition.
However, considering the `O(n)` pixels in an image with respect to the at most `O(n)` "lit up" pixels, we have to do way too much computation.

### 1.5.2 Incremental Line Rasterization

Let's assume that a line is represented with integer endpoints `(u1, v1)` and `(u2, v2)`, and a slope `s = (v2 - v1)/(u2 - u1)`. We consider the very easy special case where:

- `u1 < u2, v1 < v2`, i.e. the line points towards the upper-right
- `0 < s < 1`, i.e. there is more change in `x` than in `y`

A common optimization for drawing the pixels is the so-called **Bresenham algorithm:**

```c
v = v1;
for(u = u1; u <= u2; u++) {
    v += s;
    draw(u, round(v));
}
```

# 2. Drawing Triangles

## 2.1 Introduction

If you know how to draw a triangle, you'll go a long way!

But why triangles and not other shapes, like squares, circles, or stars? Because you can make a lot of different shapes with triangles, such as squares, stars, etc.
They provide a very good compromise between their overall power of representing different shapes and the need for specialized software and hardware to handle them effectively.

We introduce the following two definitions:

- **Coverage:** What pixels does an object/triangle cover in the image?
- **Occlusion:** What object/triangle is closest to the camera in each pixel?

## 2.2 The Visibility Problem

We state the following informal definition of the **visibility problem:** What scene geometry is visible within each screen pixel?

This question somewhat corresponds to out two definitions from before:

- What scene geometry projects into a screen pixel? (_coverage_)
- Which geometry is visible from the camera at that pixel? (_occlusion_)

![](./Figures/VisComp_Fig8-6.PNG){width=50%}

Said differently, in terms of _rays,_ the visibility problem becomes:

- What scene geometry is hit by a ray from a pixel through the pinhole? (_coverage_)
- What object is the first hit along that ray? (_occlusion_)

## 2.3 Computing Triangle Coverage

Similar to the line problem from the previous chapter, the main question we have to answer is which pixel is _covered_ by a triangle.

_Example:_

![](./Figures/VisComp_Fig8-7.PNG){width=50%}

But what do we do with pixels that are only _partially covered_ by the triangle? One option is to compute the fraction of pixel area which is covered by the triangle, and then color the pixel according to this fraction:

![](./Figures/VisComp_Fig8-8.PNG){width=50%}

However, computing the area covered by a triangle can get tricky very fast, for example when dealing with the interactions between multiple triangles.
We may estimate the amount of overlap between a triangle and a pixel through _sampling._

## 2.4 Sampling 101

Consider a continuous function and 5 discrete measurement, our _samples:_

![](./Figures/VisComp_Fig8-9.PNG){width=50%}

We can _reconstruct_ (or approximate) our original continuous functions with our discrete values through **sampling.**

### 2.4.1 Piecewise Constant Approximation

We define the reconstructed function $f_{recon}(x)$ to be the value of the sample closest to $x$, i.e. the nearest neighbor:

![](./Figures/VisComp_Fig8-10.PNG){width=50%}

### 2.4.2 Piecewise Linear Approximation

We define the reconstructed function $f_{recon}(x)$ to be the linear interpolation between two samples closest to $x$.

![](./Figures/VisComp_Fig8-11.PNG){width=50%}

### 2.4.3 More Accuracy

The simplest and most obvious way to reconstruct our original 1D signal more accurately is to sample the signal more densely, i.e. to _increase the sampling rate._

![](./Figures/VisComp_Fig8-12.PNG){width=50%}

### 2.4.4 Mathematical Representation Of Sampling

Consider the _Dirac delta:_

$$
\begin{aligned}
&\delta(x) = \begin{cases} 0, &\text{for } x \neq 0 \\ \text{undefined}, &\text{at } x = 0  \end{cases}, \quad \text{such that} \\
&\int_{- \infty}^{\infty} \delta(x) \, dx = 1
\end{aligned}
$$

An _impulse_ has a **sifting property** which we define as follows:

$$
\int_{- \infty}^{\infty} f(x)\delta(x-a) \, dx = f(a),
$$

with an impulse occurring at $x = a$. Sampling the function is equivalent to multiplying it (inner product) by the Dirac delta!

#### Reconstruction As Convolution

![](./Figures/VisComp_Fig8-13.PNG){width=50%}

![](./Figures/VisComp_Fig8-14.PNG){width=50%}

## 2.5 Coverage As A 2D Signal

We can think of the coverage as a 2D signal and define:

$$
\text{coverage}(x, \, y) = \begin{cases} 1, &\text{if the triangle contains point }(x, \, y) \\ 0, &\text{otherwise} \end{cases}
$$

We choose a point in the pixel which is said to be the _coverage sample point._

_Example:_

![](./Figures/VisComp_Fig8-15.PNG){width=50%}

One (literal) edge case we have to consider is what happens if the edge of a triangle exactly falls onto our sample point. The OpenGL/Direct3D **edge rules** are:

When an edge falls directly on a screen sample point, the sample is classified as within the triangle if the edge is a _top edge_ or a _left edge:_

- Top edge: horizontal edge that is above all other edges
- Left edge: edge that is not exactly horizontal and is on the left side of the triangle

![](./Figures/VisComp_Fig8-16.PNG){width=50%}

## 2.6 Aliasing

### 2.6.1 1D Example

**Aliasing** describes the observation that high frequencies in an original signal masquerade as low frequencies after reconstruction due to undersampling:

![](./Figures/VisComp_Fig8-17.PNG){width=50%}

This leads to one obvious question when sampling: How densely should we be sampling?

### 2.6.2 Nyquist-Shannon Theorem

The _Nyquist-Shannon theorem_ says that a signal can be perfectly reconstructed if it is sampled with period $T < \frac{1}{2 \omega_0}$.

### 2.6.3 Supersampling

We can increase the density of the sampling coverage signal. The following example shows _stratified sampling_ using four samples per pixel:

![](./Figures/VisComp_Fig8-18.PNG){width=50%}

However, we now have more samples than pixels! This means we have to **resample**, i.e. converting from one discrete sampled representation to another:

![](./Figures/VisComp_Fig8-19.PNG){width=50%}

## 2.7 Sampling Triangle Coverage

### 2.7.1 Point-In-Triangle Test

To decide whether a sample point is inside the triangle we have to test whether it is "inside" all the three edges of the triangle.

For a point with the tree vertices $P_0, \, P_1, \, P_2$, we define:

$$
\begin{aligned}
&P_i = (X_i, \, Y_i) \\
&dX_i = X_{i+1} - X_i \\
&dY_i = Y_{i+1} - Y_i \\
&E_i(x, \, y) = (x - X_i) dY_i - (y-Y_i)dX_i = A_ix + B_iy + C_i \\
&E_i(x, \, y) = \begin{cases} 0, &\text{if point is on the edge} \\ >0, &\text{if point is outside of the edge} \\ <0, &\text{if point is inside the edge}  \end{cases}
\end{aligned}
$$

This leaves us with the following mathematical definition of whether a sample point $S = (sx, \, sy)$ is inside or outside a triangle:

$$
\begin{aligned}
\text{inside}(sx, \, sy) = &E_0(sx, \, sy) < 0 \, \&\& \\ &E_1(sx, \, sy) < 0 \, \&\& \\ &E_2(sx, \, sy) < 0
\end{aligned}
$$

### 2.7.2 Incremental Triangle Traversal

Another approach is based on the idea that rather than testing all possible points on a screen, we traverse them incrementally:

![](./Figures/VisComp_Fig8-20.PNG){width=50%}

### 2.7.3 Tiled Triangle Traversal

A modern approach is to traverse the triangle in blocks. We test all samples in the block against the triangle in parallel:

![](./Figures/VisComp_Fig8-21.PNG){width=50%}

# 3. Transforms

## 3.1 Introduction

### 3.1.1 Linear Transforms

In computer graphics, we are mainly concerned about **linear transforms.** This is due to:

- Computationally speaking, linear transforms and linear maps are easy to solve
- Linear transforms are still very powerful
- All maps can be approximated as linear maps (though sometimes only over a short distance, or small amount of time)
- Composition of linear transformations is linear, leading to uniform representation of transformations

### 3.1.2 Algebraic Definition

A map $f$ is _linear_ if it maps vectors to vectors, and if for all vectors $u, \, v$ and scalars $\alpha$ we have:

$$
f(u + v) = f(u) + f(v) \\ f(\alpha u) = \alpha f(u)
$$

For maps between $\mathbb{R}^m$ and $\mathbb{R}^n$, we can give an even more explicit definition:

If a map can be expressed as $f(u) = \sum_{i = 1}^m u_i a_i$, with fixed vectors $a_i$, then it is linear.

_Example:_

![](./Figures/VisComp_Fig9-1.PNG){width=50%}

$u$ is a linear combination of $e_1$ and $e_2$. $f(u)$ is the _same_ linear combination, but of $a_1$ and $a_2$, and we have that $a_1 = f(e_1)$ and $a_2 = f(e_2)$.

## 3.2 Scale

![](./Figures/VisComp_Fig9-2.PNG){width=50%}

**Scaling** is simply defined by either scalar multiplication of the whole vector (_uniform scale_) or scalar multiplication of specific basis vectors (_non-uniform scale_).

$$
S(x) = x_1ae_1 + x_2be_2 = \begin{bmatrix} a & 0 \\ 0 & b \end{bmatrix} \cdot x
$$

## 3.3 Rotation

![](./Figures/VisComp_Fig9-3.PNG){width=50%}

Mathematically, **rotations** can be defined by the following two formulae:

$$
R_{\theta}(e_1) = (\cos \theta, \, \sin \theta) = a_1 \\
R_{\theta}(e_2) = (- \sin \theta, \, \cos \theta) = a_2
$$

Which leads us, due to the linearity of rotations, to the following simple formula:

$$
R_{\theta}(x) = x_1 a_1 + x_2 a_2 = \begin{bmatrix} \cos \theta & -\sin \theta \\ \sin \theta & \cos \theta \end{bmatrix} \cdot x
$$

_Remark:_ Rotation around any other point than the origin is _not linear,_ since for linearity, the origin must map to the origin!

## 3.4 Reflection

For now, we only consider **reflection** about the $y$-axis and $x$-axis. They are expressed as:

- $Re_y(x) = x_1e_x \cdot (-1) + x_2e_y$
- $Re_x(x) = x_1e_x + x_2e_2 \cdot (-1)$

Those special cases are actually simple _non-uniform scales._

## 3.5 Shear

A **shear operation** (in the $x$ direction) is done by moving the upper edge along the $x$-axis by some defined amount.

![](./Figures/VisComp_Fig9-4.PNG){width=50%}

Mathematically, this operation is defined through:

$$
H_a(x) = x_1 \begin{bmatrix} 1 \\ 0 \end{bmatrix} + x_2 \begin{bmatrix} a \\ 1 \end{bmatrix} = \begin{bmatrix} 1 & a \\ 0 & 1  \end{bmatrix} \cdot x
$$

## 3.6 Translation

**Translation** describes mappings of the following form:

![](./Figures/VisComp_Fig9-5.PNG){width=50%}

We can denote this transformation by:

$$
T_b(x) = x_1 \begin{bmatrix} ? \\ ? \end{bmatrix} + x_2 \begin{bmatrix} ? \\ ? \end{bmatrix}
$$

such that $T_b(x) = x + b$ for some translation vector $b$.

_Remark:_ Translation is _not linear_, but affine.

## 3.7 2D Homogeneous Coordinates (2D-H)

### 3.7.1 Introduction

The key idea with **2D-H coordinates** is to _lift_ our 2D points into the 3D space. For example, our 2D point $(x_1, \, x_2)$ is represented as:

$$
(x_1, \, x_2) \Rightarrow \begin{bmatrix} x_1 \\ x_2 \\ 1 \end{bmatrix}
$$

This also leads to the change, that our 2D transforms are now represented by $3 \times 3$ matrices instead of $2 \times 2$.

_Example:_ The previously seen 2D rotation in homogeneous coordinates is defined by:

$$
\begin{bmatrix} \cos \theta & -\sin \theta & 0 \\ \sin \theta & \cos \theta & 0 \\ 0 & 0 & 1 \end{bmatrix} \cdot \begin{bmatrix} x_1 \\ x_2 \\ 1 \end{bmatrix}
$$

### 3.7.2 Translation In 2D-H Coordinates

One of our main interests in 2D-H coordinates is that we are able to define non-linear maps in 2D as linear maps in 3D/2D-H coordinates!

The translation operation expressed in 2D-H, i.e. as a $3 \times 3$ matrix multiplication, is given by:

$$
T_b(x) = x + b = \begin{bmatrix} 1 & 0 & b_1 \\ 0 & 1 & b_2 \\ 0 & 0 & 1 \end{bmatrix} \cdot \begin{bmatrix} x_1 \\ x_2 \\ x_3 \end{bmatrix} = \begin{bmatrix} x_1 + b_1x_3 \\ x_2 + b_2x_3 \\ x_3 \end{bmatrix}
$$

In our homogeneous coordinates, translation is a linear transformation!

_Remark:_ $x_3$ is usually set to $1$.

### 3.7.3 Points vs. Vectors

In computer graphics we often have to distinguish between _points_ and _vectors._

We define a vector to have $x_3 = 0$ in 2D-H and a point to have $x_3 \neq 0$ in 2D-H. To get from a point in 2D-H back to 2D, we simply divide all components by $x_3$.

![](./Figures/VisComp_Fig9-6.PNG){width=50%}

## 3.8 Composition Of Linear Transformations

We can **compose linear transforms** via matrix multiplication. This enables for simple and efficient implementation, since we can reduce a complex chain of transforms to a single matrix.

_Example:_ We take a look at the following transform: $R_{\pi / 4} S_{[1.5, \, 1.5]}x$

![](./Figures/VisComp_Fig9-7.PNG){width=50%}

## 3.9 Moving To 3D (And 3D-H)

Similar to 2D, we represent 3D transforms as $3 \times 3$ matrices and 3D-H transforms as $4 \times 4$ matrices.

_Example:_ 

- A _scale_ in 3D is given by:

$$
S_S = \begin{bmatrix} S_x & 0 & 0 \\ 0 & S_y & 0 \\ 0 & 0 & S_z  \end{bmatrix}, \quad S_S = \begin{bmatrix} S_x & 0 & 0 & 0 \\ 0 & S_y & 0 & 0 \\ 0 & 0 & S_z & 0 \\ 0 & 0 & 0 & 1  \end{bmatrix}
$$

- A _shear_, in $x$ and based on the $y, \, z$ position, is given by:

$$
H_{x, \, d} = \begin{bmatrix} 1 & d_y & d_z \\ 0 & 1 & 0 \\ 0 & 0 & 1  \end{bmatrix}, \quad H_{x, \, d} = \begin{bmatrix} 1 & d_y & d_z & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1  \end{bmatrix}
$$

- A _translation_ by vector $b$:

$$
T_b = \begin{bmatrix} 1 & 0 & 0 & b_x \\ 0 & 1 & 0 & b_y \\ 0 & 0 & 1 & b_z \\ 0 & 0 & 0 & 1 \end{bmatrix}
$$

- Rotation about $x$-axis:

$$
R_{x, \, \theta} = \begin{bmatrix} 1 & 0 & 0 \\ 0 & \cos \theta & - \sin \theta \\ 0 & \sin \theta & \cos \theta  \end{bmatrix}
$$

- Rotation about $y$-axis:

$$
R_{y, \, \theta} = \begin{bmatrix} \cos \theta & 0 & \sin \theta \\ 0 & 1 & 0 \\ - \sin \theta & 0 & \cos \theta  \end{bmatrix}
$$

- Rotation about $z$-axis:

$$
R_{z, \, \theta} = \begin{bmatrix} \cos \theta & - \sin \theta & 0 \\ \sin \theta & \cos \theta & 0 \\ 0 & 0 & 1  \end{bmatrix}
$$

# 4. Perspective Projection Transformations, Geometry and Texture Mapping

## 4.1 Perspective Projection Transformations

### 4.1.1 Basic Perspective Projection

When doing basic perspective projection, the desired _perspective projection result_ (some 2D point), is given by:

$$
p_{2D} = (\frac{x_x}{x_z}, \, \frac{x_y}{x_z})
$$

The procedure for a basic perspective projection, follows 4 steps:

1. Input point in 3D-h: $x = (x_x, \, x_y, \, x_z, \, 1)$
2. Applying map to get the projected point in 3D-H: $Px = (x_x, \, x_y, \, x_z, \, x_z)$
3. Point projected to 2D-H by dropping the $z$ coordinate: $p_{2D-H} = (x_x, \, x_y, \, x_z)$
4. Point in 2D by homogeneous divide: $p_{2D} = (\frac{x_x}{x_z}, \, \frac{x_y}{x_z})$

![](./Figures/VisComp_Fig9-8.PNG){width=50%}

### 4.1.2 The View Frustum

The **view frustum** denotes the region in space that will appear on the screen.

![](./Figures/VisComp_Fig9-9.PNG){width=50%}

We want a transformation that maps view frustum to a unit cube, such that computing screen coordinates in that space becomes trivial.

Define the following properties:

- $\theta$ : The field of view in the $y$ direction ($h = 2 \cdot \tan \Big(\frac{\theta}{2} \Big)$)
- $f = \cdot \Big(\frac{\theta }{2} \Big)$
- $r$ : The aspect ratio, i.e. $\frac{\text{width}}{\text{height}}$

Then, we can define the _transformation matrix from frustum to unit cube_ as:

$$
P = \begin{bmatrix} \frac{f}{r} & 0 & 0 & 0 \\ 0 & f & 0 & 0 \\ 0 & 0 & \frac{zfar + znear}{znear - zfar} & \frac{2 \cdot zfar \cdot znear}{znear - zfar} \\ 0 & 0 & -1 & 0  \end{bmatrix}
$$

![](./Figures/VisComp_Fig9-10.PNG){width=50%}

## 4.2 Geometry

### 4.2.1 Implicit Representations of Geometry

In an **implicit representation**, points aren't known directly, but satisfy some relationship. For example, we might define a unit sphere as all points $x$ such that $x^2 + y^2 + z^2 = 1$.
Implicit surfaces make some tasks easy, such as deciding whether some point is inside or outside our implicit surface.

### 4.2.2 Explicit Representations of Geometry

In an **explicit representation,** all points are given directly. For example, the points on a sphere are $(\cos u \sin v, \, \sin u \sin v, \, \cos v)$ for $0 \leq u < 2 \pi$ and $0 \leq v < \pi$.
There are many explicit representations in graphics, such as:

- Triangle meshes
- Polygon meshes
- Point clouds
- etc.

Explicit surfaces make some tasks easy, such as sampling. However, they also make some tasks hard, such as deciding whether a given point is inside or outside our surface.

### 4.2.3 L-Systems (Implicit)

#### Introduction

The underlying computational system to **L-systems** is simple:

- The geometry is governed by simple rules
- The end result has interesting, useful, and surprising properties
- **L** stands for _Lindenmayer,_ the inventor of these types of systems

An **L-system** is a set of grammar rules, a start symbol, and semantics, i.e. a way of interpreting the grammar strings.

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

![](./Figures/VisComp_Fig10-1.PNG){width=50%}

#### von Koch Snowflake Curve

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

![](./Figures/VisComp_Fig10-2.PNG){width=50%}

#### Branching Structures

We assume that `[` pushes the state onto the stack and `]` pops the state from the stack. Furthermore, we use `[` to start a branch and `]` when finished returning to its base.

With those additional symbols, we can define three rules for different types of branching:

- _Monopodial:_ The trunk extends undeviated to the top and branches extend perpendicularly: `T -> T[+B][-B]T`
- _Sympodial:_ The trunk deviates to the top and branches extend perpendicularly: `T -> T[--B]+T`
- _Binary:_ Trunk terminates at the first branching point and branches deviate uniformly: `T -> T[+B][-B]`

_Example:_ The following figure shows an example of the L-System `F -> F[+F]F[-F]F`:

![](./Figures/VisComp_Fig10-3.PNG){width=50%}

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

![](./Figures/VisComp_Fig10-4.PNG){width=50%}

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

![](./Figures/VisComp_Fig10-5.PNG){width=50%}

#### Mesh Data Structures

With **mesh datastructures** we can store the geometry and topology of some object:

- _Geometry:_ The vertex locations
- _Topology:_ How vertices are connected (i.e. the edges and faces)

The simplest storage option for mesh datastructures is to simply store them in an **indexed face set:**

![](./Figures/VisComp_Fig10-6.PNG){width=50%}

### 4.2.6 Surface Normals

We can describe the power of a (light) beam in terms of its **irradiance,** i.e. the energy per time, per area, or:

$$
E = \frac{\Phi}{A},
$$

where $\Phi$ is the flux of the beam of light and $A$ is the surface area to which the beam is incident.

If the beam is incident to an angled surface, it will cover more area, and therefore the irradiance must be smaller! **Lambert's Law** states that the irradiance at some surface is proportional to the cosine of the angle between the light direction and the surface normal, or:

$$
E = \frac{\Phi}{A'} = \frac{\Phi \cos \theta}{A}
$$

![](./Figures/VisComp_Fig10-7.PNG){width=50%}

#### N-dot-L Lightning

**N-dot-L lightning** is one of the most basic ways to shade a surface: One simply takes the dot product of a unit surface normal (_N_) and the unit direction to the light source (_L_).

![](./Figures/VisComp_Fig10-8.PNG){width=50%}

## 4.3 Texture Mapping

### 4.3.1 Texture Coordinates

**Texture coordinates** define a mapping from surface coordinates (points on a triangle) to points in the texture domain.

![](./Figures/VisComp_Fig10-9.PNG){width=50%}

### 4.3.2 Texture Space Samples

To do a texture mapping, we need some space samples. We can sample some positions in the $XY$ screen space and then use the same sample positions in the texture space.
However, this can lead to **perspective-incorrect interpolation:**

![](./Figures/VisComp_Fig10-10.PNG){width=50%}

This is due to perspective projection. Barycentric interpolation of values in $XY$ screen coordinates do not correspond to values that vary linearly on the original triangle!

![](./Figures/VisComp_Fig10-11.PNG){width=50%}

### 4.3.3 Filtering Textures

Due to resizing, our rendering pixels from our image may be much larger or smaller than the pixels available of our texture. This leads to minification and magnification:

- _Minification:_ The area of screen pixel maps to a large region of texture (filtering required). One texel corresponds to far less than a pixel on the screen.
- _Magnification:_ The area of screen pixel maps to a tiny region of texture (interpolation required). One texel maps to many screen pixels.

#### Mipmap (L. Williams 83)

The idea of the **Mipmap** is to prefilter the texture data to remove high frequencies. Texels at higher levels store the integral of the texture function over a region of texture space (downsampled images), i.e. they represent low-pass filtered versions of the original texture signal.

![](./Figures/VisComp_Fig10-12.PNG){width=50%}

To decide which level $d$ to use, we calculate the differences between texture coordinate values of neighboring screen samples:

![](./Figures/VisComp_Fig10-13.PNG){width=50%}

# 5. The Rasterization Pipeline

## 5.1 Occlusion

### 5.1.1 Introduction

**Occlusion** tries to answer an important question we have already seen: Given some number of overlapping triangles, which triangle is visible at each pixel?

We will explore some techniques to solve this problem in the following subchapters.

### 5.1.2 The Depth Buffer (Z-Buffer)

We can determine the occlusion by using a **Z-buffer.** For each coverage sample point, the depth-buffer stores the depth of the closest triangle at this sample point that has been processed by the render so far. We use a grayscale value for each sample point to indicate the distance:

- Black = indicates a small distance
- White = indicates a large distance

_Example: Rendering three opaque triangles_

1. We start by processing a yellow triangle with depth 0.5:

![](./Figures/VisComp_Fig10-14.PNG){width=50%}

2. After processing the first triangle, we end up with a color buffer and depth buffer as shown below:

![](./Figures/VisComp_Fig10-15.PNG){width=50%}

3. Next we process a blue triangle with depth 0.75. We end up with buffers like this:

![](./Figures/VisComp_Fig10-16.PNG){width=50%}

4. Finally, we process a red triangle with depth 0.25. Our final color and depth buffer looks like this:

![](./Figures/VisComp_Fig10-17.PNG){width=50%}

5. The final picture is:

![](./Figures/VisComp_Fig10-18.PNG){width=50%}

The occlusion algorithm using the depth buffer is simple and given by:

```c
bool pass_depth_test(d1, d2) {
    return d1 < d2;
}

depth_test(tri_d, tri_color, x, y) {
    if(pass_depth_test(tri_d, zbuffer[x][y])) {
        zbuffer[x][y] = tri_d;
        color[x][y] = tri_color;
    }
}
```

## 5.2 Compositing

### 5.2.1 Alpha / RGBA

We can represent _opacity_ as alpha. **Alpha** describes the opacity of an object:

- Fully opaque: $\alpha = 1$
- 50% transparent: $\alpha = 0.5$
- Fully transparent: $\alpha = 0$

![](./Figures/VisComp_Fig10-19.PNG){width=50%}

### 5.2.2 The Over Operator

The **over operator** defines what happens if we composite image `B` with opacity $\alpha_B$ over an image `A` with opacity $\alpha_A$.
We have the images `A` and `B` as:

$$
A = [A_r \quad A_g \quad A_b \quad \alpha_A]^T \\
B = [B_r \quad B_g \quad B_b \quad \alpha_B]^T
$$

The _composited color_ is then given by:

$$
C = \alpha_BB + (1 - \alpha_B)\alpha_AA
$$

Furthermore, the opacity of the "image of overlap" is given by:

$$
\alpha_C = \alpha_B + (1 - \alpha_B)\alpha_A
$$

We might do some optimization and use _premultiplied alpha._ The result is equivalent to above:

$$
A' = [\alpha_AA_r \quad \alpha_AA_g \quad \alpha_AA_b \quad \alpha_A]^T \\
B' = [\alpha_BB_r \quad \alpha_BB_g \quad \alpha_AA_b \quad \alpha_B]^T \\
C' = B' + (1 - \alpha_B)A'
$$

The color buffer algorithm with semi-transparent surfaces is given by:

```c
over(c1, c2) {
    return c1 + (1 - c1.a) * c2;
}

update_color_buffer(tri_d, tri_color, x, y) {
    if(pass_depth_test(tri_d, zbuffer[x][y])) {
        color[x][y] = over(tri_color, color[x][y]);
    }
}
```

_Remark:_ Triangles must be rendered in back to front order!

## 5.3 End-to-end Rasterization Pipeline

### 5.3.1 The Real-time Graphics Pipeline

![](./Figures/VisComp_Fig10-20.PNG){width=50%}

### 5.3.2 Drawing Triangles: Step-by-step

#### Inputs

We are given the following inputs:

```c
list_of_positions = {
    v0x, v0y, v0z,
    v1x, v1y, v1z,
    v2x, v2y, v2z,
    v3x, v3y, v3z,
    v4x, v4y, v4z,
    v5x, v5y, v5z,
};

list_of_textcoords = {
    v0u, v0v,
    v1u, v1v,
    v2u, v2v,
    v3u, v3v,
    v4u, v4v,
    v5u, v5v
};
```

Additionally:

- Some texture map
- Object-to-camera-space transform `T`
- Perspective projection transform `P`
- Size of the output image `(W, H)`

#### Step 1

Transform the triangle vertices into the camera space:

![](./Figures/VisComp_Fig10-21.PNG){width=100%}

#### Step 2

Apply the perspective projection transform to transform the triangle vertices into a normalized coordinate space:

![](./Figures/VisComp_Fig10-22.PNG){width=100%}

#### Step 3

Discard triangles that lie completely outside the unit cube (since they are off-screen) and clip triangles that extend beyond the unit cube to the unit cube:

![](./Figures/VisComp_Fig10-23.PNG){width=100%}

#### Step 4

Transform the vertex $xy$ positions from the normalized coordinates into the screen coordinates (based on the screen size `(w, h)`):

![](./Figures/VisComp_Fig10-24.PNG){width=100%}

#### Step 5

Preprocess the triangles, i.e. compute the triangle edge equations and the triangle attribute equations.

#### Step 6

Do the sample coverage and evaluate attributes `Z, u, v` at all covered samples:

![](./Figures/VisComp_Fig10-25.PNG){width=100%}

#### Step 7

Compute the triangle color at the sample point through color interpolation, sample texture map, or more advanced shading algorithms:

![](./Figures/VisComp_Fig10-26.PNG){width=100%}

#### Step 8

Perform the depth test and update the depth value at the covered samples:

![](./Figures/VisComp_Fig10-27.PNG){width=100%}

#### Step 9

Update the color buffer if the depth test passed:

![](./Figures/VisComp_Fig10-28.PNG){width=100%}

### 5.3.3 Shadow Mapping

**Shadow mapping** is a multi-pass rasterization approach:

- We render every scene (depth buffer only) from the location of the light source. Everything "seen" from this point of view is directly lit.
- We then render the scene from the location of the camera. We need to transform every screen sample to light coordinate frames and perform a depth test (if the test fails, the sample is in the shadow).

## 5.4 Reflections

### 5.4.1 Modelling Reflections

Reflections are modelled based on an environment mapping:

1. Place the camera at the origin of the location of the reflective object, and render 6 different views.
2. Use the real camera ray reflected about the surface normal to determine which texel in the cube map it hits.
3. This will approximate the appearance of the reflective surface.

### 5.4.2 GPUs

GPU's are specialized processor for executing graphics pipeline computations. They render highly complex 3D scenes:

- 100's of thousands to millions of triangles in a scene
- Complex vertex and fragment shader computations
- High resolution screen outputs (2-4 Mpixel + supersampling)
- 30-60 fps

# 6. Light, Color And The Rendering Equation

## 6.1 What Is Color?

### 6.1.1 Introduction

**Light** is electromagnetic radiation, and **color** is its frequency, in other word: Light is oscillating electric and magnetic fields and its frequency determines the color of the light.

Most light is _not visible_ to the human eye. The frequencies that are visible to the human eyes are called the **visible spectrum.** These frequencies are what we think of as color.

![](./Figures/VisComp_Fig11-1.PNG){width=50%}

### 6.1.2 Description Of Light

We already saw the **emission spectrum** for the sun. In general, it tells us how much light is _produced_ and is useful to compare against other sources of light, such as lightbulbs.
Another very useful description is the **absorption spectrum**, which tells us how much light is _absorbed,_ e.g. turned into heat. It is useful to characterize color of paint, ink, etc.

While the emission spectrum is intensity as a function of frequency, the absorption spectrum is a fraction absorbed as a function of frequency. Light that is not absorbed is _reflected._

This is the fundamental description of light: Intensity, emission and absorption as a function of frequency.

## 6.2 The Eye

The eye consists of two types of photoreceptor cells: rods and cones.

- _Rods_ are primary photoreceptors under dark conditions.
- _Cones_ are primary receptors under high-light viewing conditions.

There are three types of cones: S, M, and L cones. These correspond to a peak response at short, medium, and long wavelengths.

The human eye does not directly measure the spectrum of incoming light, but three response values `(S, M, L)` by integrating the incoming spectrum against response functions of S-, M-, and L-cones. The brain then interprets these functions as colors.

![](./Figures/VisComp_Fig11-2.PNG){width=50%}

## 6.3 Additive And Subtractive Color Models

### 6.3.1 Introduction

Just like we had emission and absorption spectra, we have _additive_ and _subtractive_ color models:

- Additive: Used for combining colored lights, prototypical example is RGB
- Subtractive: used for combining paint colors, prototypical example is CMYK

### 6.3.2 Practical Encoding Of Color Values

One might ask how we encode colors digitally. One common encoding is through 8-bit per color _hexadecimal values._ Each hexadecimal number represents the intensity of red, green, and blue:

- `#000000` represents black
- `#ffffff` represents white

## 6.4 Geometric Model Of Light

**Photons** are a type of elementary particle. We can think of it as the most basic unit of light or electromagnetic radiation, they each carry a small amount of energy:

- Photons are massless and travel at the speed of light in a vacuum
- They bounce around when they interact with matter
- They travel in straight lines
- A ray of light informally means a lot of photons all moving in the same direction

### 6.4.1 Radiometry

One idea to capture photons hitting some surface is to just store the total number of hits that occur anywhere in the scene, over the complete duration of the scene. This captures the total energy of all the photons hitting the scene and is said to be the **radiant energy** (total number of hits).

The **radiant flux** describes the number of hits per second. Rather than recording the total energy over some arbitrary duration, it makes much more sense to record the total hits per second.

To make images, we also need to know where the hits occurred. So, we compute the hits per second in some unit area, which is called the **irradiance.**

![](./Figures/VisComp_Fig11-3.PNG){width=50%}

### 6.4.2 Measuring Illumination

For the radiant energy, we need to know how much energy is carried by a photon:

$$
Q = \frac{hc}{\lambda},
$$

where:

- $h$: Plack's constant
- $c$: speed of light
- $\lambda$: wavelength

The radiant flux is then equal to the energy per unit time received by the sensor:

$$
\Phi = \lim_{\Delta \to 0} \frac{\Delta Q}{\Delta t} = \frac{\text{d}Q}{\text{d}t}
$$

We can also go the other way around and say that the time integral of the flux is equal to the total radiant energy:

$$
Q = \int_{t_0}^{t_1} \Phi(t) \, \text{d}t
$$

If we are now given a sensor with area $A$, we can consider the average flux over the entire sensor area to be $\frac{\Phi}{A}$. The irradiance $E$ is then given by taking the limit of the flux as the sensor are becomes tiny:

$$
E(p) = \lim_{\Delta \to 0} \frac{\Delta \Phi(p)}{\Delta A} = \frac{\text{d}\Phi(p)}{\text{d}A}
$$

### 6.4.3 Radiance

The irradiance, i.e. the number of photons per area per time, along a given direction is called the **radiance.** We can compute:

$$
E = \int_{H^2} L(\omega) \cos \theta \, \text{d} \omega,
$$

where $E$ is the irradiance, $L$ is the radiance in direction $\omega$, and $\cos \theta$ is the angle between the normal and $\omega$.

The radiance is the solid angle density of irradiance:

$$
L(p, \, \omega) = \lim_{\Delta \to 0} \frac{\Delta E_{\omega}(p)}{\Delta \omega} = \frac{\text{d}E_{\omega}(p)}{\text{d}\omega},
$$

where $E_{\omega}$ means that the differential surface area is oriented to face in the direction $\omega$. In other words, radiance is energy along a ray defined by some origin point $p$ and a direction $\omega$.

#### Surface Radiance

We somehow need to distinguish between incident radiance and exitant radiance functions at a point on a surface:

![](./Figures/VisComp_Fig11-4.PNG){width=50%}

In general, $L_i(p, \, \omega) \neq L_o(p, \, \omega)$

## 6.5 The Rendering Equation

The core functionality of photorealistic renderer is to estimate the radiance at a given point, in a given direction. This is summed up by the **rendering equation:**

![](./Figures/VisComp_Fig11-5.PNG){width=50%}

### 6.5.1 Scattering Function

How can we model the **scattering** of light? There are many things that could happen to a photon:

- Bounces of the surface
- Transmitted through the surface
- Bounces around inside the surface
- Absorbed and re-emitted

The _bidirectional reflectance distribution function (BRDF)_ $f_r(\omega_i \to \omega_o)$ encodes the behavior of light that bounces off the surface. It answers the following question: Given some incoming direction $\omega_i$, how much light is scattered in the outgoing direction $\omega_o$?

The following properties hold:

- $f_r(\omega_i \to \omega_o) \geq 0$
- $\int_{H^2} f_r(\omega_i \to \omega_o) \cos \theta \, \text{d}\omega_i \leq 1$
- $f_r(\omega_i \to \omega_o) = f_r(\omega_o \to \omega_i)$

# 7. Ray Tracing

## 7.1 Rasterization & Ray-Casting

### 7.1.1 Rasterization

![](./Figures/VisComp_Fig11-6.PNG){width=50%}

The basic rasterization algorithm consists of obtaining 2D samples and then computing the coverage, i.e. whether a projected triangle covers a 2D sample point, and the occlusion, i.e. calculating the depth buffer.

Finding samples in this case is easy since they are distributed uniformly on screen.

## 7.1.2 Ray-Casting

An alternative to rasterization is **ray-casting.**

![](./Figures/VisComp_Fig11-7.PNG){width=50%}

The basic ray casting algorithm looks as follows:

- Sample: some ray in 3D
- Coverage: does a ray hit the triangle? (ray-triangle intersection tests)
- Occlusion: closest intersection along the ray

### 7.1.3 Rasterization vs. Ray-Casting

Rasterization:

- Proceeds in triangle order
- Most processing is based on 2D primitives
- Stores a depth buffer

Ray-Casting:

- Proceeds in screen sample order
    - Never have to store depth buffer
    - Natural order for rendering transparent surfaces
- Must store entire scene

Both are approaches for solving the same problem: _determining "visibility"._

## 7.2 Shadows

Shadow can be computed by _recursive ray tracing:_

-  Shoot shadow rays towards the light source from points where camera rays intersect the scene
    - If they are unclouded, the point is directly lit by the light source

![](./Figures/VisComp_Fig11-8.PNG){width=50%}

Shadows computed via ray tracing are correct hard shadows. If done via rasterization, shadow map texture can lead to aliasing.

## 7.3 Reflections

Similar to shadow, reflections can be computed with recursive ray tracing by "simply" adding more secondary arrays:

![](./Figures/VisComp_Fig11-9.PNG){width=50%}

## 7.4 Ray-Scene Intersections

### 7.4.1 Line-Line Intersection

Assume we have two lines $ax = b$ and $cx = d$. How do we fine the intersection?

We simply have to check if there is a simultaneous solution which leads to the following liner system of equations:

$$
\begin{bmatrix} a_1 & a_2 \\ c_1 & c_2  \end{bmatrix} \begin{bmatrix} x_1 \\ x_2  \end{bmatrix} = \begin{bmatrix} b \\ d \end{bmatrix}
$$

### 7.4.2 Ray-Mesh Intersection

One very important question to answer is where a ray pierces a surface, since this allows us to do:

- Rendering: visibility, ray tracing
- Simulation: collision detection
- Interaction: mouse picking

The parametric equation of a ray is given by: $r(t) = o + t\text{d}$, where $r(t)$ is a point along the ray, $o$ is the origin, and $\text{d}$ is some unit direction.

#### Intersection With Implicit Surface

Recall that implicit surfaces are given by some function $f(x)$, i.e. all points such that $f(x) = 0$. If we want to find all points where a ray intersects a surface, we can simply plug in $r(t)$ for $x$ in $f(x)$ and then solve for $t$.

#### Ray-Plane Intersection

Suppose we are given some plane $N^Tx = c$. Then we can find the intersection with ray $r(t)$ with the following equation:

$$
r(t) = o + \frac{c - N^To}{N^T \text{d}} \text{d}
$$

#### Ray-Triangle Intersection

If we want to find the intersection of a ray and a triangle, we proceed as follows:

1. Parameterize the triangle by vertices $p_0, \, p_1, \, p_2$ using the barycentric coordinates:

$$
f(u, \, v) = (1-u-v)p_0 + up_1 + vp_2
$$

2. Plug parametric ray equation directly into the equation for the points on the triangle:

$$
p_0 + u(p_1-p_0) + v(p_2 - p_0) = o + td
$$

3. Solve for $u, \, v, \, t$:

$$
\begin{bmatrix} p_1 - p_0 & p_2 - p_0 & -d  \end{bmatrix} \begin{bmatrix} u \\ v \\ t  \end{bmatrix} = o - p_0
$$

### 7.4.3 Core Methods For Ray-Primitive Queries

We want to solve the following query: Given some primitive `p`, `p.intersect(r)` returns the value `t` corresponding to the point of intersection with ray `r`.

Now given a scene defined by a set of `N` primitives and a ray `r`, find the closes point of intersection of `r` with the scene. A very simple algorithm to solve this query could look like this:

```python
p_closest = NULL
t_closes = INF
for each primitive p in scene:
    t = p.intersect(r)
    if t >= 0 && t < t_closest:
        t_closest = t
        p_closest = p
```

This has complexity $O(n)$.

# 8. Computer Animation

## 8.1 Describing Motion

### 8.1.1 Introduction

How we describe motion on a computer is probably the most important question to answer when talking about motion in general. From a computer graphics perspective, there are three main techniques to that:

- Artist directed, such as keyframing
- Data-driven, such as motion capturing
- Procedural, such as simulations

### 8.1.2 Keyframing

**Keyframing** is an important yet quite "simple" idea of describing motion. The basic idea is to specify important events in our motion only, and let the computer fill in the rest via interpolation or approximation.

![](./Figures/VisComp_Fig12-1.PNG){width=50%}

## 8.2 Spline Interpolation

### 8.2.1 Interpolation

The basic idea behind **interpolation** data is to connect the dots, i.e. the given sample points. One such technique is _piecewise linear interpolation:_

![](./Figures/VisComp_Fig12-2.PNG){width=50%}

It might be simple, but it yields a rather rough motion.

Another common interpolation technique is _piecewise polynomial interpolation:_

![](./Figures/VisComp_Fig12-3.PNG){width=50%}

### 8.2.2 Splines

In general, a **spline** is any piecewise polynomial function. In 1D, splines interpolate data over the real line:

$$
(t_i, \, f_i), \quad i = 0,...,n
$$

"_Interpolates_" in that case just means that the function exactly passes through those values, i.e.:

$$
f(t_i) = f_i \quad \forall i
$$

The only other condition is that the function is a _polynomial_ when restricted to any interval between the knots:

$$
\text{for } t_i \leq t \leq t_{i+1}, \, f(t) = \sum_{j = 1}^d c_it^j =: p_i(t)
$$

The splines most commonly used for interpolation are _cubic,_ i.e. $d = 3.$

### 8.2.3 Fitting Cubic Polynomials To Endpoints

If we want to connect two end points with a cubic polynomial, there are many solutions! Cubic polynomials have four _degrees of freedom,_ namely the four coefficients $(a, \, b, \, c, \, d)$ that we can manipulate and control.

However, we only need two degrees of freedom to specify the endpoints!

$$
p(t) = at^3 + bt^2 + ct + d \\
p(0) = p_0 \Rightarrow d = p_0
p(1) = p_1 \Rightarrow a + b + c + d = p_1
$$

This leaves us with four unknowns but only two equations, which is obviously not enough to determine the curve.

However, what if we also want to match the _derivatives_ at the endpoints?

![](./Figures/VisComp_Fig12-4.PNG){width=50%}

Then we end up with 4 equations and 4 unknowns, which lets us uniquely identify the cubic polynomial we are looking for.

### 8.2.4 Natural Splines

**Natural splines** are piecewise splines made out f cubic polynomials $p_i$.

![](./Figures/VisComp_Fig12-5.PNG){width=50%}

We want three conditions to hold for natural splines:

1. Interpolation at both endpoints:

$$
p_i(t_i) = f_i, \, p_i(t_{i + 1}) = f_{i + 1}, \quad i = 0,...,n-1
$$

2. Tangents agree at the endpoints, i.e. $C^1$ continuity:

$$
p'_i(t_{i + 1}) = p'_{i + 1}(t_{i + 1}), \quad i = 0,...,n-2
$$

3. Curvatures agree at the endpoints, i.e. $C^2$ continuity:

$$
p''_i(t_{i+1}) P p''_{i+1}(t_{i+1}), \quad i = 0,...,n-2
$$

If we look at the picture above, we see that for $n+1$ points we have $4n$ DOFs, which leads us to $2n + (n-1) + (n-1) = 4n-2$ equations. WE can pin down the remaining DOFs by setting the curvature to zero at the endpoints. This is what makes the curvature "_natural_".

### 8.2.5 Hermite/Bézier Splines

**Hermite/Bézier splines** are based on the idea that each cubic piece is specified by the endpoints and tangents, in contrast to natural splines where we define an additional point on which we have to exactly meet:

![](./Figures/VisComp_Fig12-6.PNG){width=50%}

Hermite splines have the following properties:

1. Endpoints interpolate data:

$$
p_i(t_i) = f_i, \, p_i(t_{i+1}) = f_{i + 1}, \quad i = 0,...,n-1
$$

2. Tangents interpolate some given data:

$$
p'_i(t_i) = u_i, \, p'_i(t_{i+1}) = u_{i+1}, \quad i = 0,...,n-1
$$

## 8.3 Rigging

### 8.3.1 Introduction

**Animation rigs** are user-defined mappings between a few parameters and the deformations of a high-res mesh. Animations are simply time trajectories specified for rig parameters.

### 8.3.2 Blend Shape Rigs

**Blend Shape rigs** are simple rigs based on a set of meshes (the input). The output is a blended mesh obtained through interpolation. The splines (or keyframes) specify the blending weights over time.

_Mathematically:_

- Input: set of meshes $M_i$ with vertices $x_i^j$ and blending weights $\alpha = (\alpha_1,...,\alpha_n)$
- Output: blended mesh $M$ through linear combination: $M = \sum_i\alpha_iM_i$, i.e. $x^j = \sum_i \alpha_ix_i^j$

### 8.3.3 Cage-Base Deformers

The idea behind **cage-based deformers** is to embed the model in a coarse mesh (cage), then deform the coarse mesh and reconstruct the hi-res geometry. For example, we can model the vertices as weighted sums of the cage vertices:

$$
v = \sum_{j = 1}^m w_j(v)c_j
$$

### 8.3.4 Skeletal Animation

Very often, shape implies the existence of a _skeleton,_ and a skeleton imposes a lot of structure in ho a character can move.

The key idea behind **skeletal animation** is to animate just the skeleton (much less DOFs), and then have the mesh to follow automatically.

### 8.3.5 Forward Kinematics

We define _kinematic skeletons_ as follows:

- Joints: local coordinate frames
- Bones: vectors between consecutive pairs of joints
- Each non-root bone defined in the frame of a unique parent
- Changes to parent frame affects all descendant bones
- Both skeleton and skin are designed in a rest pose

Assuming $n+1$ joints $0, \, 1,..., \, n$, where joint $0$ is the root, then each joint corresponds to a frame. $p(j)$ denotes the parent of joint $j$, and the frame of joint $j$ is expressed w.r.t the frame of $p(j)$:

$$
_{p(j)}R_j = \begin{bmatrix} r_{11}(j) & r_{12}(j) & r_{13}(j) & t_1(j) \\ r_{21}(j) & r_{22}(j) & r_{23}(j) & t_2(j) \\ r_{31}(j) & r_{32}(j) & r_{33}(j) & t_3(j) \\ 0 & 0 & 0 & 1  \end{bmatrix} = \begin{bmatrix} Rot(j) & t(j) \\ 0 & 1  \end{bmatrix},
$$

where $t(j)$ typically comes from a bind pose and $Rot(j)$ comes from some animation.

The transformation from frame $j$ to world is then given by:

![](./Figures/VisComp_Fig12-7.PNG){width=50%}

### 8.3.6 Skinning

The basic idea behind the _skinning process_ is that we simply move the vertices of the skin along with the bones! In a first attempt, we might assign each vertex to the closest bone, compute the world coordinates according to the bone's transformation and move the skin vertices along with it:

![](./Figures/VisComp_Fig12-8.PNG){width=50%}

This process is also called **rigid skinning.**

#### Linear Blend Skinning

The attempt is similar to above, however we assign each vertex to multiple bones, and then compute the world coordinates as a convex combination. Weights define the influence of each bone on the vertex. This leads to an overall smoother deformation of the skin:

$$
v = \sum_j \alpha_j \,_wR_j \,_{\bar{w}}\bar{R}_j^{-1}v'
$$

![](./Figures/VisComp_Fig12-9.PNG){width=50%}

### 8.3.7 Inverse Kinematics

**Inverse kinematics** describes the problem that given some position of our character, compute the joint angles. This is one of the most fundamental techniques in animation and robotics!

The basic idea behind an IK algorithm is as follows:

1. Write down the distance between the final point and the target and set up the objective
2. Compute the gradient with respect to angles
3. Go downhill from there

### 8.3.8 The Uncanny Valley

> The uncanny valley is a concept first introduced in the 1970s by Masahiro Mori, then a professor at the Tokyo Institute of Technology. Mori coined the term "uncanny valley" to describe his observation that as robots appear more humanlike, they become more appealing—but only up to a certain point. Upon reaching the uncanny valley, our affinity descends into a feeling of strangeness, a sense of unease, and a tendency to be scared or freaked out. So the uncanny valley can be defined as people's negative reaction to certain lifelike robots.

![](./Figures/VisComp_Fig12-10.PNG){width=50%}

### 8.3.9 Motion Capture

**Motion capture** provides sparse signals, such as marker trajectories, from which a full body motion needs to be reconstructed.

This is a problem which is often-times posed as an optimization problem, for example in inverse kinematics.

## 8.4 Physics-Based Animation

### 8.4.1 Introduction

We first differentiate between two important terms in the field of physics and physics-based animation:

- **Kinematics:** The branch of mechanics concerned with the motion of objects without reference to the forces which cause the motion.
- **Dynamics:** The branch of mechanics concerned with the motion of bodies under the action of forces.

### 8.4.2 The Animation Equation

We have already seen the _rendering equation_ which is concerned with rasterization and path tracing which give approximate solutions to the rendering equation.

The **animation equation** is concerned with the large spectrum of physical systems and phenomena, such as solids, fluids, elasticity, etc. For animations, the connection between force and motion is essential:

> A change in motion is proportional to the motive force impressed and takes place along the straight line in which that force is impressed. - Sir Isaac Newton, 1687

However, there is more to be said than $F = ma$:

- Every system has a _configuration_ $q(t)$
- It also has a _velocity_ $\dot{q} := \frac{d }{dt}q$
- It has some kind of _mass_ $M$
- There are _forces_ $F$ acting on the system

### 8.4.3 Generalized Coordinates

In physics, we often need to describe a system with many moving parts, e.g. a collection of billiard balls, each with position $x_i$. We usually collect them all into a single vector of **generalized coordinates.**

![](./Figures/VisComp_Fig13-1.PNG){width=50%}

We can think of $q$ as a single point moving along some trajectory in $\mathbb{R}^n$. If we take the time derivative of the generalized coordinates, we get **generalized velocity:**

![](./Figures/VisComp_Fig13-2.PNG){width=50%}

### 8.4.4 Ordinary Differential Equations

Many dynamical systems can be described via an **ordinary differential equation:**

$$
\frac{d}{dt}q = f(q, \, \dot{q}, \, t),
$$

where $\frac{d}{dt}$ is the change in configuration over time and $f$ is the velocity function.

_Example:_ Assume we have a function where the rate of growth is proportional to the value, i.e.

$$
\frac{d}{dt}u(t) = au,
$$

then our solution is given by $u(t) = be^{at}$.

Note that Newton's 2nd law is an ODE as well, i.e. $\ddot{q} = F/m$. We can also write this as a system of two first order ODEs, by introducing new variables for velocity:

$$
\dot{q} = v, \quad \dot{v} = \frac{F}{m} \\ \frac{d}{dt} \begin{bmatrix} q \\ v  \end{bmatrix} = \begin{bmatrix} v \\ F/m  \end{bmatrix}.
$$

### 8.4.5 Solving ODEs Numerically

When we are talking about solving ODEs we mean that given some initial conditions $q(0)$ and $\dot{q}(0)$, we want to find the function $q(t)$. Solving ODEs _numerically_ means solving numerical time integration:

$$
q(t + h) = q(t) + \int_t^{t + h} \dot{q}(t) \, dt
$$

We use some discrete approximation of the form

$$
\Delta q_i \simeq \int_t^{t + h} \dot{q}(t) \, dt,
$$

and then apply the following **numerical integration rules:**

![](./Figures/VisComp_Fig13-3.PNG){width=50%}

### 8.4.6 Forward Euler

**Forward Euler** describes a simple scheme: We evaluate the derivative at the current configuration and write the new state explicitly in terms of known data:

$$
q_{i + 1} = a_i + h \cdot \dot{q}_i \\
\dot{q}_{i + 1} = \dot{q}_i + h \cdot \ddot{q}_i = \dot{q}_i + hM^{-1}F(q_i, \, \dot{q}_i)
$$

_Example:_ Assume some simple linear ODE, i.e. $\dot{u} = -au, \, a > 0$. The exact solution to this ODE would be $u(t) = u(0)e^{-at}$, so $u_k \to 0$ as $l \to \infty$. The forward Euler approximation is given by:

$$
u_n = (1-ha)^nu_0,
$$

from where we can derive that this decays only if $|1-ha| < 1$, or equivalently $h < 2/a$, so in practice we many need very small time-steps!

### 8.4.7 Backward Euler

We might try something else and evaluate the velocity at some new configuration. This scheme is also known as **backward Euler.** The new configuration is then implicit, and we must solve for it:

$$
q_{i + 1} = q_i + h \cdot \dot{q}_{i + 1} \\
\dot{q}_{i + 1} = \dot{q}_i + h \cdot \ddot{q}_{i + 1} = \dot{q}_i + hM^{-1}F(q_{i + 1}, \, \dot{q}_{i+1})
$$

We can again observe the stability of the backward Euler with our previous example, i.e. $\dot{u} = -au, \, a > 0.$ The backward Euler approximation is given by:

$$
u_n = \Big (\frac{1}{1 + ha} \Big)^nu_0,
$$

which decays if $|1 + ha| > 1$, which is always true! Backward Euler is _unconditionally stable_ for linear ODEs.

### 8.4.8 Partial Differential Equations

In contrast to ODEs, where an unknown function is described through its derivatives with respect to a single variable, **partial differential equations (PDEs)** describe an unknown function through its partial derivatives with respect to _multiple_ variables:

$$
\frac{\partial u (t, \, x)}{\partial t^2} = c^2 \frac{\partial u(t, \, x)}{\partial x^2}
$$

#### Fluid Simulation in Graphics

_Incompressible Navier Stokes Equations:_

$$
\nabla \cdot u = 0 \\
\frac{\partial u}{\partial t} + (u \cdot \nabla)u - v \nabla^2u = - \nabla w + g
$$

#### Elasticity in Graphics

_Governing Equations of Continuum Mechanics:_

$$
\nabla \cdot \sigma + f = m \cdot a
$$

#### Magnetism in Graphics

_Maxwell Equations (static case):_

$$
\nabla \cdot B = 0, \quad \nabla \times H = J \\
H = \frac{1}{\mu_0}B - M
$$

#### 1D Advection

Consider the following example, where we are given some initial temperature distribution $T_0(x) = T(x, \, 0)$ and some wind speed $c$. We want to find the temperature distribution $T(x, \, t)$ for any $t$:

![](./Figures/VisComp_Fig14-1.PNG){width=50%}

We can solve this problem _analytically:_

- Any $T(x, \, t)$ of the form $T(x, \, t) = f(x-ct)$ solves $\frac{\partial T}{\partial t} = -c \frac{\partial T}{\partial x}$
- The solution also needs to satisfy the initial condition $T(x, \, 0) = T_0(x)$
- The solution therefore is given by $T(x, \, t) = T_0(x-ct)$

_Note:_ Only simple PDEs can be solved analytically!

We might also solve the problem _numerically:_

![](./Figures/VisComp_Fig14-2.PNG){width=50%}

#### Some Notation

![](./Figures/VisComp_Fig14-3.PNG){width=50%}

#### PDE Classification

The **order** of a PDE is the order of the highest partial derivative. A PDE is said to be **linear** if the unknown function $u$ and its partial derivatives only occur linearly.

Second order linear PDEs are of high practical relevance. A second order linear PDE in 2 variables has the following form:

$$
Au_{xx} + 2Bu_{xy} + Cu_{yy} = F(x, \, y, \, u, \, u_x, \, u_y)
$$

A second order linear PDE in 2 variables can be classified into:

- _Hyperbolic:_ $B^2-AC > 0$ (wave equation)
- _Parabolic:_ $B^2-AC = 0$ (heat equation)
- _Elliptic:_ $B^2-AC < 0$ (Laplace equation)

#### Solving PDEs

Like ODEs, many interesting PDEs are difficult or impossible to solve analytically. The basic strategy is as follows:

- Pick a spatial discretization
- Pick a time discretization (forward Euler, backward Euler, etc.)
- As with ODEs, run a time-stepping algorithm

#### Spatial Discretization

Two basic ways to **discretize space** are the Lagrangian and the Eulerian approach:

![](./Figures/VisComp_Fig14-4.PNG){width=50%}

We observe the following trade-offs:

- Lagrangian:
    - Conceptually easy
    - Resolution/domain not limited by grid
    - Good particle distribution can be tough
    - Finding neighbors can be expensive
- Eulerian:
    - Fast, regular computation
    - Easy to represent
    - Simulation is "trapped" in a grid

#### The Laplace Operator

![](./Figures/VisComp_Fig14-5.PNG){width=50%}

_Discretization:_

![](./Figures/VisComp_Fig14-6.PNG){width=50%}

_Numerically solving the Laplace equation:_

![](./Figures/VisComp_Fig14-7.PNG){width=50%}
