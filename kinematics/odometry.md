---
description: package com.arcrobotics.ftclib.kinematics
---

# Odometry

FTCLib offers its own odometry classes for the use of differential and holonomic drives. The odometry classes track the robot position as a `Pose2d`, which means it is represented using the vector $$\begin{pmatrix} x\\ y\\ \theta \end{pmatrix}$$ .

When using these classes, it is important to keep angles in radians. Distances should be consistent.

## Pose Exponential

Pose Exponential is a general FRC term for the constant velocity method of odometry utilized in FTC.

This method uses differential equations to solve the nonlinear position of the robot given constant curvature. As such, we will not explain the math here.

### Offsets and Trackwidth

The trackwidth is the distance between parallel encoders. This value should be tuned so that a precise calculation can be made. This value is a required pass into the constructor.

The center wheel offset accounts for the distance between the center of rotation of the robot and the position of the horizontal encoder. This is only necessary for the holonomic odometry.

## Updating the Position

If you are using the [suppliers](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html) constructor, you're going to want to call `updatePose()` repeatedly after every iteration. A useful way to do this would be to plug it into the `periodic` method of a [subsystem](../command-base/command-system/subsystems.md), which is actually already available for you with the [odometry subsystem](https://github.com/FTCLib/FTCLib/blob/dev/core/src/main/java/com/arcrobotics/ftclib/command/OdometrySubsystem.java).

Alternatively, use the `update` method to iterate the position. To use it, you will need to pass the left encoder position, right encoder position, and horizontal encoder position \(if holonomic\) as parameters into the `update` method. Like with `updatePose()`, you're going to want to call this method after every successive iteration.

