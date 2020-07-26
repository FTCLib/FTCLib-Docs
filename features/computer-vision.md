---
description: package com.arcrobotics.ftclib.vision;
---

# Computer Vision

Computer Vision is the process of helping computers to understand the digital images such as photographs and videos provided to them. 
FTCLib provides examples on the object detection needed for the current season(right now being Skystone detection) using the [EasyOpenCV library](https://github.com/OpenFTC/EasyOpenCV).

# SkystoneDetectorPipeline

The heart of the skystone detector is the pipeline. A pipeline is just a fancy way describing the sequence of instructions given to continuously manipulate the image(in this case, what the camera sees). Ignoring the fancy code, the pipeline boils down to these following instructions: 

### Receiving the Input
``` java
public Mat processFrame(Mat input) 
```

In this line, what the camera sees is being passed in and represented as a matrix. 

### Manipulating the Input 
``` java
Imgproc.cvtColor(input, matYCrCb, Imgproc.COLOR_RGB2YCrCb);
setValues(input.width(), input.height());

for (Rect stone : blocks) {
    Mat currentMat = new Mat();
    Core.extractChannel(drawRectangle(matYCrCb, stone, new Scalar(255, 0, 255), 2), currentMat, 2);
    means.add(Core.mean(currentMat));
    currentMat.release();
}
```

The first line will convert the input matrix color space from RGB to YCRCB. Because the way YCRCB represents color by luminance(Y), chroma of red(CR), chroma of blue(CB), it keeps values consistent under different lighting. Next we draw 3 rectangles on screen with predetermined position. Each rectangle should contain part of a stone. Then we extract the CB value of each predetermined area of the stones to compare. We use CB because the color blue is futher away from yellow than red on the color wheel. This gives us more room for error. Note it is also important to release the mats when finished using them to help with memory management. 

### Determining the Position

``` java 
Scalar max = means.get(0);
int biggestIndex = 0;

for (Scalar k : means) {
    if (k.val[0] > max.val[0]) {
        max = k;
        biggestIndex = means.indexOf(k);
    }
}
```

Here is just a simple for loop to iterate through the 3 rectangles to find the highest value, which would be the skystone. It is also technically possible to do this with 2 rectangles where you compare the difference between the 2. If they are within a range, they are both just normal stones and the other one will be the skystone. If they are not within a range, the highest value would be the skystone. 



# Using the Pipeline

The SkystoneDetector is a LinearOpMode that will show how you would use pipeline. For more indepth explanation of what everything does or more functionalities, please visit [here](https://github.com/OpenFTC/EasyOpenCV/tree/master/examples/src/main/java/org/openftc/easyopencv/examples).   

### Setting Up the Camera

``` java
int cameraMonitorViewId = hardwareMap.appContext.getResources().getIdentifier("cameraMonitorViewId", "id", hardwareMap.appContext.getPackageName());
phoneCam = OpenCvCameraFactory.getInstance().createInternalCamera(OpenCvInternalCamera.CameraDirection.BACK, cameraMonitorViewId);
phoneCam.openCameraDevice();

```
These 3 lines will initialize the camera and open a connection to the camera

### Setting up the Pipeline
``` java
visionPipeLine = new SkystoneDetectorPipeline();
phoneCam.setPipeline(visionPipeLine);
```

SkystoneDetectorPipeline's constructor is overloaded. You can choose between:
``` java
public SkystoneDetectorPipeline(double firstSkystonePositionPercentage, double percentSpacing, double stoneWidth, double stoneHeight)
 ```

 ``` java
public SkystoneDetectorPipeline(double firstSkystonePositionPercentage, double percentSpacing, double stoneWidth, double stoneHeight, Telemetry tl){
```

``` java
public SkystoneDetectorPipeline()
```

``` java 
public SkystoneDetectorPipeline(Telemetry tl)
```

* `firstSkystonePositionPercentage`: The location of the first rectangle, the pecentage of the width of the user's input
* `percentSpacing`: The spacing between the stones, the percentage times width added to the position of each stone
* `stoneWidth`: The width of the rectangle drawn
* `stoneHeight`: The height of the rectangle drawn
* `tl`: Your instance of telemetry

After creating an instance of the pipeline, set it to the phone's camera. Then continiously run 
``` visionPipeLine.getSkystonePosition()``` to get the position. 





