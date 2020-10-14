---
description: package com.arcrobotics.ftclib.kinematics
---

# Odometry

FTCLib offers its own odometry classes for the use of differential and holonomic drives. The odometry classes track the robot position as a `Pose2d`, which means it is represented using the vector $$\begin{pmatrix} x\\ y\\ \theta \end{pmatrix}$$ .

When using these classes, it is important to keep your distance in inches and angles in radians.

## Euler Integration

The first method of odometry, seen in the `HolonomicOdometry` and `DifferentialOdometry` classes, is Euler integration. Euler integration makes use of the half angle method for updating the pose of the robot. Instead of relying on calculus, this method uses solely basic linear algebraic principles.

{% embed url="https://www.youtube.com/watch?v=UcUuPVjpEqs" %}

The contents of this video can be simplified to the following formula:

$$
\begin{pmatrix} x\\ y\\ \theta \end{pmatrix} = 
\begin{pmatrix} x_{i}\\ y_{i}\\ \theta_{i} \end{pmatrix} +
\begin{pmatrix}
\Delta{x_{c}\cos{(\theta_{i} + \frac{\varphi}{2})}}-\Delta{x_{\perp}\sin{(\theta_{i} + \frac{\varphi}{2})}}\\
\Delta{x_{c}\sin{(\theta_{i} + \frac{\varphi}{2})}}+\Delta{x_{\perp}\cos{(\theta_{i} + \frac{\varphi}{2})}}\\
\varphi \end{pmatrix}
$$

If you have a differential drivetrain, $$\Delta{x_{\perp}}=0$$ because the drivebase cannot move horizontally \(in the sideways direction\).

## Pose Integration

Pose integration is a general FRC term for the constant velocity method of odometry utilized in FTC. The [ConstantVeloMecanumOdometry](https://github.com/FTCLib/FTCLib/blob/v1.0.0/FtcLib/src/main/java/com/arcrobotics/ftclib/kinematics/ConstantVeloMecanumOdometry.java) class uses these calculations for a general holonomic base despite the name of the classfile.

This method uses differential equations to solve the nonlinear position of the robot given constant curvature. As such, we will not explain the math here. Instead, we will only go over how the classfile can be used.

### Offsets and Trackwidth

The `ConstantVeloMecanumOdometry` classfile utilizes a three odometer system paired with some gyroscope. The reason why a gyroscope is used here is because, although the angular rate can be calculated using encoder velocities, this method is NOT recommended because of wheel scrubbing. Consequently, the gyro is used instead for the mathematical process of heading interpolation.

The trackwidth is the distance between parallel encoders. This value should be tuned so that a precise calculation can be made. This value is a required pass into the constructor.

The offsets are values that correspond to a difference in actual vs relative positions. The center wheel offset is the distance between the horizontal odometer and the center of rotation of the robot to account for the proper angular rate as it affects the odometer's position. The gyro offset describes the difference between the initial position of the robot for the odometry and the initial value heading returned by the gyroscope. This is only accounted for if an initial `Pose2d` object is passed into the constructor.

## Updating the Position

If you are using the [suppliers](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html) constructor, you're going to want to call `updatePose()` repeatedly after every iteration. A useful way to do this would be to plug it into the `periodic` method of a [subsystem](../command-base/command-system/subsystems.md). You can learn more about how to do this in the [pure pursuit](../pathing/pure-pursuit.md#creating-your-odometry) tutorial.

Alternatively, the other methods use the `update` method to iterate the position. To use it, you will need to pass the current gyro angle, left encoder position, right encoder position, and horizontal encoder position \(if holonomic\) as parameters into the `update` method. Like with `updatePose()`, you're going to want to call this method after every successive iteration.

**Note**:  
If you're using the Euler integration classes and would like to not use the gyroscope, you will need to pass in a value of 0 for the current heading and it will use the encoder readings instead to calculate the current angle of the robot.

