---
description: package com.arcrobotics.ftclib.hardware
---

# Hardware

Each hardware device in FTCLib is based on the `HardwareDevice` interface. This comes with two methods inherited by every device:

* `disable()`: disables the device
* `getDeviceType()`: returns a String characterization of the device

FTCLib offers _a lot_ of hardware devices that can be implemented or customized into your program. The best advice we can give to users is to take a look at the [hardware package](https://github.com/FTCLib/FTCLib/tree/v1.1.0/core/src/main/java/com/arcrobotics/ftclib/hardware) in the FTCLib repository. Here is the list of devices we currently have available \(not including motors\):

## External Encoders

An external encoder is one that does not come built-in with the motor being used. For example, this could be a [REV Through Bore Encoder](https://www.revrobotics.com/rev-11-1271/). There is an abstract class that has the following methods;

* `getCounts()`: returns the encoder count
* `syncEncoder()`: synchronizes the recorded counts with the current count
* `resetEncoder()`: resets the encoder value

We currently have a sample implementation that can be found in the [JSTEncoder](https://github.com/FTCLib/FTCLib/blob/v1.1.0/core/src/main/java/com/arcrobotics/ftclib/hardware/JSTEncoder.java) class.

## Gyro Extensions

The `GyroEx` class is an extended gyro that allows users to add more configurable methods and possible control to their gyro. An example would be creating a `ModernRoboticsGyro` class. The abstract class has the following methods:

* `init()`: initializes the gyro and sets the current direction to the 0 heading
* `getHeading()`: returns the heading of the robot compared to the last reset
* `getAbsoluteHeading()`: returns the absolute heading relative to the initial direction
* `getAngles()`: returns the x, y, and z orientation of the gyro. This is functionally the same as yaw, pitch, and roll.
* `getRotation2d()`: transforms the heading into a `Rotation2d` object
* `reset()`: applies an offset so that `getHeading()` returns the 0 position

A useful implementation of this is the [RevIMU](https://github.com/FTCLib/FTCLib/blob/v1.1.0/core/src/main/java/com/arcrobotics/ftclib/hardware/RevIMU.java) class for the built-in imu on your REV hub.

## Sensors

There are a few sensors that are offered in FTCLib:

* [SensorColor](https://github.com/FTCLib/FTCLib/blob/v1.1.0/core/src/main/java/com/arcrobotics/ftclib/hardware/SensorColor.java)
* [SensorDistance](https://github.com/FTCLib/FTCLib/blob/v1.1.0/core/src/main/java/com/arcrobotics/ftclib/hardware/SensorDistance.java) & [SensorDistanceEx](https://github.com/FTCLib/FTCLib/blob/v1.1.0/core/src/main/java/com/arcrobotics/ftclib/hardware/SensorDistanceEx.java)
* [SensorRevTOFDistance](https://github.com/FTCLib/FTCLib/blob/v1.1.0/core/src/main/java/com/arcrobotics/ftclib/hardware/SensorRevTOFDistance.java)
* [RevColorSensorV3](https://github.com/FTCLib/FTCLib/blob/v1.1.0/core/src/main/java/com/arcrobotics/ftclib/hardware/RevColorSensorV3.java)

The `SensorColor` class is just an extension for the `ColorSensor` class that is in the SDK.

`SensorDistance` and `SensorDistanceEx` are interfaces for creating custom distance sensors if desired. An implementation of the `SensorDistanceEx` interface is `SensorRevTOFDistance` which utilizes the time-of-flight mechanic to track distance.

The `RevColorSensorV3` is a combination of a TOF sensor and a color sensor.

## Servos

The [ServoEx](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/ServoEx.java) interface allows for more methods and actions than the normal servo class in the SDK. You can change the position of the servo relative to the last position or set it to an absolute position. You can either specify a position within the range of the servo's motion or have it rotate in degrees.

An example implementation of this can be found in the [SimpleServo](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/SimpleServo.java) class.

