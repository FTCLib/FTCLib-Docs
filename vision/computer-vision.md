---
description: package com.arcrobotics.ftclib.vision
---

# Setting Up Vision

Computer Vision is the process of helping computers to understand the digital images such as photographs and videos provided to them. FTCLib provides examples on the object detection needed for the current season \(right now being Ultimate Goal detection\) using the [EasyOpenCV library](https://github.com/OpenFTC/EasyOpenCV).

## Installation Requirements

Since FTCLib depends on EasyOpenCV for vision, and because EasyOpenCV depends on [OpenCV-Repackaged](https://github.com/OpenFTC/OpenCV-Repackaged), you will need to copy [`libOpenCvNative.so`](https://github.com/OpenFTC/OpenCV-Repackaged/blob/master/doc/libOpenCvNative.so) from the `/doc` folder of that repo into the `FIRST` folder on the USB storage of the Robot Controller \(i.e. connect the Robot Controller to your computer with a USB cable, put it into MTP mode, and drag 'n drop the file\)

Due to 64 bits vs 32 bits conflicts, after moving the so file, please remove any instance of **arm64-v8a** in `build.common.gradle`. EasyOpenCV runs in 32 bits while some phones and the Control Hub are 64 bit.

