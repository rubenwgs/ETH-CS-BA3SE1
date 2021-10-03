**Visual Computing - Lecture notes week 1**

- Author: Ruben Schenk
- Date: 03.10.2021
- Contact: ruben.schenk@inf.ethz.ch

# 1. The Digital Image

## 1.1 What is an image?

### Image as a 2D signal

The **signal** is a function depending on some variable with pyhsical meaning. The **image** is a continuous function, where we either have 2 variables `x y` which are the coordinates, or, in case of a video, three variables `x y` and the corresponding time in the video. Usually, the value of the function is the **brightness**.

*Images in Python:*

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

In summary, an **image** is a picture or pattern of a value varying in space and/or time. It is the representation of a *continuous* function to a *discrete* domain: `f : R^n -> S`.

As an example, for greyscale CCD images, `n = 2` and `S = R^+`.

### What is a pixel?

*A pixel is not a little square!*

**Pixels** are point measurements of a function (of the above described continuous function).

## 1.2 Where do images come from?

There are several different things where pictures can come from:

- Digital cameras
- MRI scanners
- Computer graphics packages
- Body scanners
- Many more...

### Digital cameras

Simplified, the **digital camera** consists of the following parts and is said to be a *Charge Coupled Device (CCD)*:

![](./Figures/VisComp_Fig1-1.PNG)

The **sensor array** can be < 1cm^2 and is an array of *photosites*. Each photosite is a bucket of electrical charge, and this charge is proportional to the incident light intensity during the exposure.

The **analog to digital conversion (ADC)** measure the charge and digitizes the result. The conversion happens line by line in that charges in each photosite move down through the sensor array.

Example:

![](./Figures/VisComp_Fig1-2.PNG)

Because each bucket has a finite capacity, if a photosite bucket is full, it can overflow to other buckets, which leads to **blooming**.

Even without any light, there will still be some current which can degrade the quality of a picture. CCD's produce thermally-generated charge, which results in a *non-zero output* even in darkness. This effect is called the **dark current**.

## 1.3 CMOS

**CMOS** sensors have the same sensor elements as CCD. Each photo sensor has its own amplifier, which results in more noise and a lower sensitivity. However, since CMOS photo sensor use standard CMOS technology, we might put other components on the chip (such as "smart pixels").

### CCD vs. CMOS

*CCD*

- Mature technology
- Specific technology
- High production cost
- High power consumption
- Blooming
- Sequential readout

*CMOS*

- More recent technology
- Cheap
- Lower power consumption
- Less sensitive
- Per pixel amplification
- Random pixel access
- On chip integration with other components

### Rolling shutter

By resetting each line in the sensor line by line (the "shutter"), each line will start capturing light a little before the line below and so one. Each line in the picture is therefore a little behind in time as the line above.

## 1.4 Sampling in 1D

**Sampling** in 1D takes a function, and returns a vector whose elements are valuesof that function at the sample points.

Example:

![](./Figures/VisComp_Fig1-3.PNG)

Sampling solves one problem with working with continuous functions. How do we store and compute with them? A common scheme for representing continuous functions is with **samples**: we simply write down the function's values as discret values at many sample points.

### Reconstruction

**Reconstruction** describes the process of making samples back into a continuous function. We migh to this for several different reasons:

- For ouput where we need a realizable method
- For analysis or processing, where we need a mathematical method
- Instead of "guessing" what the function did in between sample points

### Undersampling

Unsurprisingly, if we undersample some function, we loose information.

Example: If we undersample the following `sin` wave, it gets indistinguishable from lower frequencies:

*Sample:*

![](./Figures/VisComp_Fig1-4.PNG)

*Reconstruction 1:*

![](./Figures/VisComp_Fig1-5.PNG)

*Reconstruction 2:*

![](./Figures/VisComp_Fig1-6.PNG)

This effect is what we call **aliasing**, i.e. *"Signals travelling in disguise as other frequencies"*.
