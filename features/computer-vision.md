---
description: package com.arcrobotics.ftclib.vision
---

# Computer Vision

Computer Vision is the process of helping computers to understand the digital images such as photographs and videos provided to them. FTCLib provides examples on the object detection needed for the current season \(right now being Ultimate Goal detection\) using the [EasyOpenCV library](https://github.com/OpenFTC/EasyOpenCV).

## Install Dependency on the Phone

Since FTCLib depends on EasyOpenCV for vision, and because EasyOpenCV depends on [OpenCV-Repackaged](https://github.com/OpenFTC/OpenCV-Repackaged), you will need to copy [`libOpenCvNative.so`](https://github.com/OpenFTC/OpenCV-Repackaged/blob/master/doc/libOpenCvNative.so) from the `/doc` folder of that repo into the `FIRST` folder on the USB storage of the Robot Controller \(i.e. connect the Robot Controller to your computer with a USB cable, put it into MTP mode, and drag 'n drop the file\)

## Install Dependency on Control Hub
The installation on the control hub is the exact same as the phone with one extra step. Due to 64 bits vs 32 bits conflicts, after moving the so file, please remove any instance of **arm64-v8a** in build.common.gradle. 

## UGRectRingPipeline

The heart of the Ultimate Goal detector is the pipeline. A pipeline is just a fancy way describing the sequence of instructions given to continuously manipulate the image\(in this case, what the camera sees\). Ignoring the fancy code, the pipeline boils down to these following instructions:

### Receiving the Input

```java
public Mat processFrame(Mat input)
```

In this line, what the camera sees is being passed in and represented as a matrix.

### Manipulating the Input

```java
Imgproc.cvtColor(input, matYCrCb, Imgproc.COLOR_RGB2YCrCb);

Rect topRect = new Rect(
    (int) (matYCrCb.width() * topRectWidthPercentage),
    (int) (matYCrCb.height() * topRectHeightPercentage),
    rectangleWidth,
    rectangleHeight
);

Rect bottomRect = new Rect(
    (int) (matYCrCb.width() * bottomRectWidthPercentage),
    (int) (matYCrCb.height() * bottomRectHeightPercentage),
    rectangleWidth,
    rectangleHeight
);

//The rectangle is drawn into the mat
drawRectOnToMat(input, topRect, new Scalar(255, 0, 0));
drawRectOnToMat(input, bottomRect, new Scalar(0, 255, 0));
```

The first line will convert the input matrix color space from RGB to YCrCb. Because the way YCrCb represents color by luminance\(Y\), chroma of red\(CR\), chroma of blue\(Cb\), it keeps values consistent under different lighting. Next we draw 2 rectangles on screen with predetermined position. The top rectangle should be where the 4th ring will be, and the bottom one should be where the first one will be. Then we extract the Cb value of each predetermined area of the ring to compare.

### Finding CB Values

```java
topBlock = matYCrCb.submat(topRect);
bottomBlock = matYCrCb.submat(bottomRect);

Core.extractChannel(bottomBlock, matCbBottom, 2);
Core.extractChannel(topBlock, matCbTop, 2);

Scalar bottomMean = Core.mean(matCbBottom);
Scalar topMean = Core.mean(matCbTop);

bottomAverage = bottomMean.val[0];
topAverage = topMean.val[0];
```

Here we crop the mat to just everything inside the two rectangles. Then we find the average of the values and store them in bottomAverage and topAverage. 

## Creating An Instance of UGRectDetector

The UGRectDetector is a class that will show how you would use pipeline. For more indepth explanation of what everything does or more functionalities, please visit [here](https://github.com/OpenFTC/EasyOpenCV/tree/master/examples/src/main/java/org/openftc/easyopencv/examples).
To start, create an instance of UGRectDetector. The detector's constructor is overloaded. You can choose between:

```java
public UGRectDetector(HardwareMap hMap)
```
```java
public UGRectDetector(HardwareMap hMap, String webcamName) 
```

* `hMap`: An instance of the hardware map
* `webcamName`: The webcam name

If you use the first constructor, the detector will set the camera to the phone's camera. If the second is used, the webcam will be used. 

## Setting Rectangle Positions
```java
public void setTopRectangle(double topRectHeightPercentage, double topRectWidthPercentage)
```

* `topRectHeightPercentage`: topRectHeightPercentage is the percentage of the height of the user's input and should be a decimal under 1. It is used to calculate the first y value for the top rectangle. 
* `topRectWidthPercentage`: topRectWidthPercentage is the percentage of the width of the user's input and should be a decimal under 1. It is used to calculate the first x value for the top rectangle.

```java
public void setBottomRectangle(double bottomRectHeightPercentage, double bottomRectWidthPercentage)
```

* `bottomRectHeightPercentage`: bottomRectHeightPercentage is the percentage of the height of the user's input and should be a decimal under 1. It is used to calculate the first y value for the bottom rectangle. 
* `bottomRectWidthPercentage`: bottomRectWidthPercentage is the percentage of the width of the user's input and should be a decimal under 1. It is used to calculate the first x value for the bottom rectangle.

```java
public void setRectangleSize(int rectangleWidth, int rectangleHeight)
```

* `rectangleWidth` : rectangleWidth is the width of the rectangles in terms of pixels
* `rectangleHeight` : rectangleHeight is the height of the rectangles in terms of pixels

After creating an instance of the detector and setting the rectangle positions,continuously run `DetectorInstance.getStack()` to get the number in the stack.
