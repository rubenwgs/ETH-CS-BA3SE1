**WORK IN PROGRESS**

**Visual Computing - Lecture notes week 1**

- Author: Ruben Schenk
- Date: 03.10.2021
- Contact: ruben.schenk@inf.ethz.ch

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

![](./Figures/VisComp_Fig1-1.PNG)

The **sensor array** can be < 1 cm^2 and is an array of _photosites_. Each photosite is a bucket of electrical charge, and this charge is proportional to the incident light intensity during the exposure.

The **analog to digital conversion (ADC)** measure the charge and digitizes the result. The conversion happens line by line in that charges in each photosite move down through the sensor array.

Example:

![](./Figures/VisComp_Fig1-2.PNG)

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

![](./Figures/VisComp_Fig1-3.PNG)

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

![](./Figures/VisComp_Fig1-4.PNG)

_Reconstruction 1:_

![](./Figures/VisComp_Fig1-5.PNG)

_Reconstruction 2:_

![](./Figures/VisComp_Fig1-6.PNG)

This effect is what we call **aliasing**, i.e. _"Signals travelling in disguise as other frequencies"_.

## 1.5 Sampling in 2D

**Sampling** in 2D takes a function and returns an array. We allow the array to be infinite dimensional and to have negative as well as positive indices.

Example:

_Function:_

![](./Figures/VisComp_Fig1-7.PNG)

_Sample:_

![](./Figures/VisComp_Fig1-8.PNG)

### Reconstruction

In 2D, a simple way to reconstruct a function from a sample is to use **bilinear interpolation**, which works essentially the same as linear interpolation: we calculate two lines in each direction, and then take the intersection of the two lines.

Example:

![](./Figures/VisComp_Fig1-9.PNG)

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

![](./Figures/VisComp_Fig1-10.PNG)

One might also use the much more meaningful assumption of **Poisson noise**:

![](./Figures/VisComp_Fig1-11.PNG)

### SNR

The **signal to noise ration (SNR)** `s` is an index of image quality:

![](./Figures/VisComp_Fig1-12.PNG)

**Visual Computing - Lecture notes week 2**

- Author: Ruben Schenk
- Date: 04.10.2021
- Contact: ruben.schenk@inf.ethz.ch

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

![](./Figures/VisComp_Fig2-1.PNG)

In contrast to a filter mosaic, we truly measure each color at each pixel, instead of either red, blue or green per pixel.

# 2. Image Segmentation

> Image segmentation is the ultimate classification problem. Once solved, Computer Vision is solved.

## 2.1 What is Image Segmentation?

**Image segmentation** partitions an image into regions of interest. It is the first stage in many automatic image analysis systems.

A _complete segmentation_ of an image `I` is a finite set of regions `R_1,..., R_N`, such that:

![](./Figures/VisComp_Fig2-2.PNG)

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

![](./Figures/VisComp_Fig2-3.PNG)

### How do we choose T?

There are several ways to choose `T`:

- Trial and error
- Compare results with ground truth
- Automatic methods (we'll discuss ROC curves later on)

## 2.3 Chromakeying

If we can control the background of a picture, segmentation becomes easier. Assume we use a green screen.

**Chromakeying** describes the process of plain distance measuring, in this case for green:

![](./Figures/VisComp_Fig2-4.PNG)

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

- Healty/afflicted patients
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

![](./Figures/VisComp_Fig2-5.PNG)

### Operating points

We can choose an **operating point** by assigning relative costs and values to each outcome:

- `V_TN`: value of true negative
- `V_TP`: value of true positive
- `C_FN`: cost of false negative
- `C_FP`: cost of false positive

When we assigned these costs, we can choose the point on the ROC curve with **gradient**:

![](./Figures/VisComp_Fig2-6.PNG)

For simplicity, we often set `V_TN = V_TP = 0`.
¨

## 2.5 Limits of Thresholding

Why can we segment images much better by eye than through thresholding processes? Because we can consider the context of the whole image.

We might improve results by considering _image context_ through **surface coherence**.

## 2.6 Pixels

### Pixel connectivity

We need to define which pixels are connected/neighbors.

We define two different types of **pixel neighborhoods**:

![](./Figures/VisComp_Fig2-7.PNG)

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

The **inclusion criteria** could either be done by greylevel thresholding or by a _greylevel distribution model_:

- Use mean `mu` and standard deviation `sigma` in seed region and then:
  - include if `(I(x, y) - mu)^2 < (n sigma)^2` (with for example `n = 3`)
  - this also leads to the ability to update the mean and standard deviation after every iteration

**Visual Computing - Lecture notes week 3**

- Author: Ruben Schenk
- Date: 19.10.2021
- Contact: ruben.schenk@inf.ethz.ch

# 3. Convolution and Filtering

## 3.1 Linear Shift-Invariant Filtering

**Linear shift-invariant filtering** is about modifying pixels base don its _neighborhood_. Linear means that it should be a _linear combination_ of neighbors. Shift-invariant means that we do the _same thing for each pixel_. This approach is useful for:

- Low-level image processing operations
- Smoothing and noise reduction
- Sharpening
- Detecting or enhancing features

### 3.1.1 Linear Filtering

$L$ is a **linear** operation if:

$$L[\alpha I_1 + \beta I_2] = \alpha L [I_1] + \beta L [I_2]$$

Linear operations can be written as:

$$I'(x, \, y) = \sum_{(i, \, j) \in \mathcal{N}(x, \, y)} K(x, \, y; \, i, \, j)I(i, \, j)$$

Where $I$ is the input image, $I'$ is the output of the operation, and $K$ is the **kernel** of the operation. $\mathcal{N}(m, \, n)$ denotes the _neighborhood_ of $(m, \, n)$.

> Operations are **shift-invariant** if $K$ does _not_ depend on $(x, \, y)$, i.e. we when using the same weights everywhere!

## 3.2 Correlation

In this approach, we take a **correlation mask** and apply it to an image.

> Correlation is as if we had a template (the mask), and search for it in our image.

This would look as follows:

![](./Figures/VisComp_Fig3-1.PNG)

The linear operation of correlation looks as follows:

$$I' = K \circ I \\ I'(x, \, y) = \sum_{(i, \, j) \in \mathcal{N}(x, \, y)}K(i, \, j)I(x+i, \, y + j)$$

This represents the linear weights as an image.

## 3.3 Convolution

> Compared to correlation, where we looked at the neighborhood of a pixel and applied what we learned from the neighborhood to the single pixel, in convolution we look at a single pixel and apply what we can learn from it to its neighborhood.

![](./Figures/VisComp_Fig3-2.PNG)

The linear operation of convolution is given by:

$$I' = K * I \\ I' (x, \, y) = \sum_{(i, \, j) \in \mathcal{N}(x, \, y)}K(i, \, j)I(x-i, \, y-j)$$

This too represents the linear weights as an image, it is actually the same as correlation, but with a reversed kernel.

### 3.3.1 Correlation vs Convolution

![](./Figures/VisComp_Fig3-3.PNG)

## 3.4 Separable Kernels

**Separable filters** can be written as $K(m, \, n) = f(m)g(n)$. For a rectangular neighborhood with size $(2M + 1) \times (2N + 1)$, $I'(m, \, n) = f * (g * I(\mathcal{N}(m, \, n)))$. We can rewrite this to:

$$I''(m, \, n) = \sum_{j = -N}^N g(j)I(m, \, n-j) \\ I'(m, \, n) = \sum_{i = -M}^Mf(i)I''(m-i, \, n)$$

## 3.5 Gaussian Kernel

The idea of the **Gaussian kernel** is that we weight the contributions of neighboring pixels by their nearness:

![](./Figures/VisComp_Fig3-4.PNG)

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

$$\begin{bmatrix}
    -1 & 0 & 1 \\
    -1 & 0 & 1 \\
    -1 & 0 & 1
\end{bmatrix}$$

- _Sobel operator:_

$$\begin{bmatrix}
    -1 & 0 & 1 \\
    -2 & 0 & 2 \\
    -1 & 0 & 1
\end{bmatrix}$$

**High-pass filters**

- _Laplacian operator:_

$$\begin{bmatrix}
    0 & 1 & 0 \\
    1 & -4 & 1 \\
    0 & 1 & 0
\end{bmatrix}$$

- _High-pass filter:_

$$\begin{bmatrix}
    -1 & -1 & -1 \\
    -1 & 8 & -1 \\
    -1 & -1 & -1
\end{bmatrix}$$

# 4. Image Features

## 4.1 Template Matching

**Template matching** describes the problem of locating an object, described by a template $t(x, \, y)$, in the image $s(x, \, y)$. This is done by searching for the best match by minimizing mean-squared error:

$$\begin{aligned}
    E(p, \, q) &= \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} \lbrack s(x, \, y) - t(x-p, \, y-q) \rbrack^2 \\
    &= \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} |s(x, \, y)|^2 + \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} |t(x, \, y)|^2 - 2 \cdot \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} s(x, \, y) \cdot t(x-p, \, y-q)
\end{aligned}$$

Equivalently, we can _maximize_ the **area correlation**:

$$r(p, \, q) = \sum_{x = - \infty}^{\infty} \sum_{y = - \infty}^{\infty} s(x, \, y) \cdot t(x-p, \, y-q) = s(p, \, q) * t(-p, \, -q)$$

The area correlation is equivalent to the convolution of image $s(x, \, y)$ with impulse response $t(-x, \, -y)$.

## 4.2 Edge Detection

One idea, in a continuous-space, is to detect the local gradient:

$$|\text{grad}(f(x, \, y))| = \sqrt{\lparen \frac{\partial f}{\partial x} \rparen^2 + \lparen \frac{\partial f}{\partial y} \rparen^2}$$

We mostly use the following **edge detection filters**:

![](./Figures/VisComp_Fig4-1.PNG)

### 4.2.1 Laplacian Operator

The idea of a **Laplacian operator** is to detect discontinuities by considering the second derivative and searching for _zero-crossings_ (those mark edge locations):

$$\nabla^2 f(x, \, y) = \frac{\partial^2 fx, \, y()}{\partial x^2} + \frac{\partial^2 f(x, \, y)}{\partial y^2}$$

![](./Figures/VisComp_Fig4-2.PNG)

We can do a _discrete-space approximation_ by convolution with a $3 \times 3$ impulse response:

$$\begin{bmatrix}
    0 & 1 & 0 \\
    1 & -4 & 1 \\
    0 & 1 & 0
\end{bmatrix} \quad \text{or} \quad \begin{bmatrix}
    1 & 1 & 1 \\
    1 & -8 & 1 \\
    1 & 1 & 1
\end{bmatrix}$$

However, the Laplacian operator is sensitive to very fine detail and noise, so we might want to blur the image first.

#### Laplacian of Gaussian

Blurring the image with Gaussian and Laplacian operator can be combined into convolution with **Laplacian of Gaussian operator** (LoG):

$$\text{LoG}(x, \, y) = - \frac{1}{\pi \sigma^4} \lbrack 1 - \frac{x^2 + y^2}{2 \sigma^2} \rbrack exp \lparen - \frac{x^2 + y^2}{2 \sigma^2} \rparen$$

### 4.2.2 Canny Edge Detector

The **Canny edge detector** works with the following steps:

1. Smooth the image with a Gaussian filter
2. Compute the gradient magnitude and angle (Sobel, Prewitt, etc.):

$$M(x, \, y) = \sqrt{\lparen \frac{\partial f}{\partial x} \rparen^2 + \lparen \frac{\partial f}{\partial y} \rparen^2} \quad \text{and} \quad \alpha(x, \, y) = \tan^{-1} \lparen \frac{\partial f}{\partial y} \big / \frac{\partial f}{\partial x} \rparen$$

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

![](./Figures/VisComp_Fig4-3.PNG)

2. If we have multiple edge pixels, we can do the same procedure for each of those, giving us a line in the $(m, \, c)$ plane for each edge pixel.
3. We then subdivide the $(m, \, c)$ plane into discrete "bins" and initialize the bin count of each bin to $0$. Each time a bin is crossed by one of the lines of the different edge pixels, we increase its count by one.
4. We then simply have to detect the peaks in the $(m, \, c)$ plane to get our fitted straight line:

![](./Figures/VisComp_Fig4-4.PNG)

We might encounter an infinite-slope problem, which can be avoided with an alternative parameterization:

![](./Figures/VisComp_Fig4-5.PNG)

## 4.3 Detecting Corner Points

Many applications benefit from features localized in $(x, \, y)$. If edges are well localized but only in one direction, we might want to **detect corners**.

The desirable properties of a corner detector are:

- accurate localization
- invariance against shift, rotation, scale, and brightness change
- robust against noise, high repeatability

*something something what patterns can be localized most accurately?*

![](./Figures/VisComp_Fig4-6.PNG)

### 4.3.1 Feature Point Extraction

We have that $SSD \simeq \delta^T M \delta$. Now if we shift our patterns over the picture, we assume it to change the following way:

![](./Figures/VisComp_Fig4-7.PNG)

Now we want to find points for which the following is large:

$$\min \delta^T M \delta \text{ for } ||\delta || = 1$$

i.e. we want to maximize the eigenvalues of $M$.

#### Keypoint Detection

*something something*

![](./Figures/VisComp_Fig4-8.PNG)

**Visual Computing - Lecture notes week 4**

- Author: Ruben Schenk
- Date: 26.10.2021
- Contact: ruben.schenk@inf.ethz.ch

# 5. Fourier Transform

## 5.1 Introduction

The idea of the **Fourier Transform** is to represent functions in a new basis. We might think of functions as vectors, with many components, where we can apply a linear transformation to transform the basis (i.e. a dot product with each basis elements).

In the expressions, $u$ and $v$ select the basis elements, so a function of $x$ and $y$ becomes a function of $u$ and $v$. The **basis elements** have the form $e^{-i2 \pi (ux + vy)} = \cos 2 \pi (ux + vy) - i \sin 2 \pi (ux + vy)$. Or:

$$F(g(x, \, y))(u, \, v) = \int \int_{\mathbb{R}^2} g(x, \, y)e^{i2 \pi (ux + vy)}\text{d}x \text{dy}$$

The **discrete fourier transform** therefore is of the form:

$$F = Uf$$

where:

- $F$ is the transformed image
- $U$ is the Fourier transform base
- $f$ is the vectorized image

## 5.2 Fourier Basis Functions

![](./Figures/VisComp_Fig4-1.PNG)

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

$$\text{Sample}_{\text{2D}}(f(x, \, y)) = \sum_{i = - \infty}^{\infty} \sum_{j = - \infty}^{\infty} f(x, \, y) \delta (x-i, \, y-j) = f(x, \, y) \sum_{i = - \infty}^{\infty} \sum_{j = - \infty}^{\infty} \delta (x-i, \, y-j)$$

### 5.5.1 Fourier Transform of a Sampled Signal

The _Fourier transform of a sampled signal_ is given by the following equalities:

![](./Figures/VisComp_Fig4-2.PNG)

### 5.5.2 Nyquist Sampling Theorem

The **Nyquist theorem** says that the sampling frequency must be at least twice the highest frequency, i.e. $\omega_s \geq 2 \omega$. If this is not the case, the signal needs to be bandlimited before sampling, e.g. with a low-pass filter.

## 5.2 Image Restoration

### 5.2.1 Image Restoration Problem

If we have an image transformation of the form:

$$f(x) \to h(x) \to g(x) \to \tilde{h}(x) \to f(x)$$

Then, the **inverse kernel** $\tilde{h}(x)$ should compensate the effect of the _image degradation_ $h(x)$, i.e.

$$(\tilde{h} * h)(x) = \delta(x)$$

$\tilde{h}$ may be determined more easily in the Fourier space, since:

$$\mathcal{F}[\tilde{h}](u, \, v) \cdot \mathcal{F}[h](u, \, v) = 1.$$

To determine $\mathcal{F}[\tilde{h}]$ we need to estimate:

1. The distortion model $h(x)$ or $\mathcal{F}[h](u, \, v)$
2. The parameters of $h(x)$, e.g. $r$ for defocussing

### 5.2.2 Image Restoration: Motion Blur

The **kernel for motion blur** is given by: $h(x) = \frac{1}{2l}(\theta(x_1 + l) - \theta(x_1 - l)) \delta(x_2)$, i.e. the transformation of a light dot into a small line in $x_1$ direction.

The Fourier transformation of this is given by:

![](./Figures/VisComp_Fig4-3.PNG)

Which leads to:

- $\hat{h}(u) = \mathcal{F}[h](u) = \text{sinc}(2 \pi ul)$
- $\mathcal{F}[\tilde{h}](u) = \frac{1}{\hat{h}(u)}$

However, the following problems arise:

- The convolution with the kernel $h$ completely cancels the frequencies $\frac{v}{2l}$ for $v \in \mathbb{Z}$. Vanishing frequencies cannot be recovered!
- There is a lot of noise amplification for $\mathcal{F}[h](u, \, v) << 1$.

### 5.2.3 Avoiding Noise Amplification

We can avoid **noise amplification** by a _regularized reconstruction filter_ of the form:

$$\tilde{\mathcal{F}}[\tilde{h}](u, \, v) = \frac{\mathcal{F}[h]}{|\mathcal{F}[h]|^2 + \epsilon}$$.

The size of $\epsilon$ implicitly determines an estimate of the noise level in the image, since we discard signal which are dampened below the size $\epsilon$.

**Visual Computing - Lecture notes week 5**

- Author: Ruben Schenk
- Date: 02.11.2021
- Contact: ruben.schenk@inf.ethz.ch

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

$$\vec{g} = H \vec{f}$$

We define a **linear operator** $O[.]$ as:

$$O[\alpha_1 \cdot \vec{f}_1 + \alpha_2 \cdot \vec{f}_2] = \alpha_1 \cdot O[\vec{f}_1] + \alpha_2 \cdot O[\vec{f}_2]$$

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

$$A^{-1} = A^{*T} = A^H$$

If $A$ is real valued, i.e. if $A = A^*$, then the transform is said to be **orthonormal**.

### 6.3.1 Energy Conservation

For any unitary transform $\vec{c} = A \vec{f}$, we obtain

$$||\vec{c}||^2 = \vec{c}^H\vec{c} = \vec{f}^H A^H A \vec{f} = ||\vec{f}||^2$$

This means, that every unitary transform is simply a rotation of the coordinate system (and, possibly, sign flips). The vector lengths, i.e. the "energies", are conserved!

### 6.3.2 Image Collection

We might denote **image collections** in the following way:

1. $f_i$ as one image
2. $F = [f_1 \, f_2 \, \cdots \, f_n]$ as an image collection
3. $R_{ff} = E[f_i \, . \, f_i^H] = \frac{F.F^H}{n}$ as an image collection _auto-correlation function_

### 6.3.3 Energy Distribution

With unitary transforms, energy is conserved, but often will be _unevenly distributed_ among the coefficients.

For the auto-correlation matrix, we have that:

$$R_{cc} = E[\vec{c}\vec{c}^H] = E[A \vec{f} \cdot \vec{f}^H A^H] = A R_{ff}A^H$$

This leads to the mean squarede values (i.e. the "average energies") of the coefficients $c_i$ being on the diagonal of $R_{cc}$$:

$$E[c_i^2] = [R_{cc}]_{i,i} = [AR_{ff}A^H]_{i,i}$$

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

![](./Figures/VisComp_Fig5-1.PNG)

### 6.3.6 Basis Images and Eigenimages

For any unitary transform, the _inverse transform_ $\vec{f} = A^H \vec{c}$ can be interpreted in terms of the superposition of basis images (columns of $A^H$) of size $M \times N$.
If the transform is a KL transform, the basis images, which are the eigenvectors of the autocorrelation matrix $R_{ff}$, are called **eigenimages.**
If energy concentration works well, only a limited number of eigenimages is needed to approximate a set of images with a small error. These eigenimages form an optimal linear subspace of dimensionality $J$.

To recognize complex patterns, e.g. faces, large portions of an image might have to be considered. With a transform of the form $\vec{c} = W\vec{f}$ we can reduce the dimensionality from $M \times N$ to $J$ by representing the image by $J$ coefficients.

### 6.3.7 Simple Recognition

For a **simple recognition** we can use the _simple euclidean distance (SSD)_ between two images and let the best match "win":

$$\text{arg min}_i D_i = ||I_i - I||$$

However, this is computationally expensive, i.e. it requires the presented image to be correlated with every image in the database!

### 6.3.8 Eigenspace Matching

We consider the PCA (aka KLT aka closes rank-k approximation property of SVD) $\hat{I}_i \simeq Ep_i$. Then:

$$I_i - I = \hat{I}_i - \hat{I} \simeq E(p_i - p) \\ ||I_i - I|| \simeq ||p_i - p|| \\ \text{arg min}_i D_i = ||I_i - I|| \simeq ||p_i - p||$$

with $\hat{I} = I - \bar{I}$ and $p = E^T \hat{I}$. This is _much cheaper to compute!_

### 6.3.9 Fisherfaces

The key ideas of **Fisherfaces** are as follows:

- Find directions where the ratio between/within individual variances are maximized
- Linearly project to the basis where the dimensions with good signal/noise ratios are maximized

![](./Figures/VisComp_Fig5-2.PNG)

The eigenimage method maximizes _scatter_ within the linear subspace over the entire image set, regardless of the classification tasks:

$$W_{opt} = \text{arg max}_W \lparen \text{det} \lparen WRW^H \rparen \rparen$$

The idea of the **Fisher linear discrimant analysis** is to maximize between-class scatter, while minimizing within-class scatter:

![](./Figures/VisComp_Fig5-3.PNG)

#### Fisher Images and Varying Illumination

All images of the same Lambertian surface with different illumination (without shadows) lie in a 3D linear subspace. There is a single point source at infinity such that:

![](./Figures/VisComp_Fig5-4.PNG)

# 7. Image Compression

## 7.1 JPEG Image Compression

### 7.1.1 Block-Based Discrete Cosine Transform (DCT)

We essential discrete **Cosine Transform** on our image we wish to compress:

![](./Figures/VisComp_Fig5-5.PNG)

**DCT** is a variant of discrete Fourier transform with real numbers and a very fast implementation. We can choose the block size, its size will have an influence on the transform:

- Small blocks: faster computation and correlation between neighboring pixels
- Large blocks: better compression in smooth regions

### 7.1.2 Image Compression Using DCT

DCT enables _image compression_ by concentrating most image information in the low frequencies. We loose unimportant image info, i.e. high frequencies, by cutting $B(u, \, v)$ at the bottom right corner. The decoder computes the inverse DCT (iDCT).

## 7.2 Image Pyramid

![](./Figures/VisComp_Fig5-6.PNG)

The application of **scaled representations,** such as _image pyramids_, is to look at corase scaled and then refine with finer scaled. For example, a "good" edge at a finer scale has parents at some coarser scale.

### 7.2.1 Gaussian Pyramid

The different levels of the **Gaussian Pyramid** are smooth with Gaussians, since a Gaussian times a Gaussian is another Gaussian.

![](./Figures/VisComp_Fig5-7.PNG)

### 7.2.2 Laplacian Pyramid

For the synthesis of a **Laplacian Pyramid** we have that:

- It preserves the difference between unsampled Gaussian pyramid levels and Gaussian pyramid levels
- It's a band pass filter: Each level represents spatial frequencies unrepresented at other levels

![](./Figures/VisComp_Fig5-8.PNG)

## 7.3 Discrete Wavelet Transform

### 7.3.1 Haar Transform

The **Haar Transform** with the _Haar Basis_ has the following properties:

- Real and orthogonal
- Transition at each scale $p$ is localized according to $q$

_Comparison of DCT and Haar basis:_

![](./Figures/VisComp_Fig5-9.PNG)

### 7.3.2 Lifting

_Analysis filters:_

![](./Figures/VisComp_Fig5-10.PNG)

_Synthesis filters:_

![](./Figures/VisComp_Fig5-11.PNG)
