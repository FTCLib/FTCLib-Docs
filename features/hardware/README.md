---
description: package com.arcrobotics.ftclib.hardware
---

# Hardware

Each hardware device in FTCLib is based on the `HardwareDevice` interface. This comes with two methods inherited by every device:

* `disable()`: disables the device
* `getDeviceType()`: returns a String characterization of the device

FTCLib offers _a lot_ of hardware devices that can be implemented or customized into your program. The best advice we can give to users is to take a look at the [hardware package](https://github.com/FTCLib/FTCLib/tree/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware) in the FTCLib repository. Here is the list of devices we currently have available \(not including motors\):

## External Encoders

An external encoder is one that does not come built-in with the motor being used. For example, this could be a [REV Through Bore Encoder](https://www.revrobotics.com/rev-11-1271/). There is an abstract class that has the following methods;

* `getCounts()`: returns the encoder count
* `syncEncoder()`: synchronizes the recorded counts with the current count
* `resetEncoder()`: resets the encoder value

We currently have a sample implementation that can be found in the [JSTEncoder](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/JSTEncoder.java) class.

## Gyro Extensions

The `GyroEx` class is an extended gyro 

