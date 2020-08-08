---
description: package com.arcrobotics.ftclib.purepursuit
---

# Pure Pursuit

The pure pursuit algorithm in FTCLib is developed so that the user only needs to add the desired waypoints and call the `followPath()` method in the [Path](https://github.com/FTCLib/FTCLib/blob/master/FtcLib/src/main/java/com/arcrobotics/ftclib/purepursuit/Path.java) class. To use this, you need to pass the [mecanum](https://docs.ftclib.org/ftclib/features/drivebases#mecanum) drivetrain as well as the odometry for the robot. Once the method is finished, it will return true or false depending on if it was successful or not.

As an alternative, you can call the `loop()` method and directly input your odometry positions there. Make sure you update the odometry positions with each iteration of the loop.

## What is Pure Pursuit?

Pure pursuit, otherwise designated as "PP," is a path tracking algorithm that calculates the robot velocity in order to reach a designated look-ahead point from the current position. It loosely follows a path determined by a set of waypoints, which are coordinates on the field. What the pure pursuit controller does is create a circle of determined radius and follow the path by "looking ahead" with the circle and seeing where it intersects with the path. The robot's heading orientation is then compared to the radius that connects the center of the robot to that intersection. It then moves in correspondence. The radius size can be updated for each waypoint you enter into the path for specificity.

![A visual representation of look-ahead](../.gitbook/assets/look-ahead.png)

The robot continues to follow this intersection at real-time. This is how the robot "follows" the designated path. It is essentially a p controller for the heading that has the robot move at the fastest possible speed around some path.

### Waypoints

There are five types of waypoints: start, general, interrupted, point-turn, and end. The starting waypoint represents the first point in the path; conversely, the ending waypoint is the last point in the path.

A general waypoint is a point where the robot performs its ordinary pursuit algorithm with the look-ahead method. A point-turn waypoint is a type of general waypoint that stops at the given point, turns, and then traverses to the next waypoint. An interrupted waypoint is a type of point-turn waypoint where other actions can occur, such as picking up a skystone.

#### Creating Waypoints

You can create a waypoint by calling the various constructors.

**StartWaypoint**

```java
// empty constructor
StartWaypoint waypoint_1 = new StartWaypoint();
// Pose2d
waypoint_1 = new StartWaypoint(pose2d);
// X and Y coordinates
waypoint_1 = new StartWaypoint(x, y);
// Translation2d
waypoint_1 = new StartWaypoint(translation2d);
```

**GeneralWaypoint**

```java
// empty constructor
GeneralWaypoint waypoint_2 = new GeneralWaypoint();
// X and Y coordinates
waypoint_2 = new GeneralWaypoint(x, y);

/**
 * More detailed constructors
 */
waypoint_2 = new GeneralWaypoint(
    translation2d, rotation2d,
    movementSpeed, turnSpeed,
    followRadius
);
waypoint_2 = new GeneralWaypoint(
    pose2d, movementSpeed, turnSpeed,
    followRadius
);
waypoint_2 = new GeneralWaypoint(
    x, y, movementSpeed,
    turnSpeed, followRadius
);
waypoint_2 = new GeneralWaypoint(
    x, y, rotationRadians,    // if you have a preferred angle
    movementSpeed, turnSpeed,
    followRadius
);
```

**PointTurnWaypoint**

As you will see here, a "buffer" is a sort of expected error. This sets up a tolerance given that the robot might be a bit offset from the desired position or rotation.

```java
// empty constructor
PointTurnWaypoint waypoint_3 = new PointTurnWaypoint();

/**
 * More detailed constructors
 */
waypoint_3 = new PointTurnWaypoint(
    translation2d, rotation2d,
    movementSpeed, turnSpeed,
    followRadius, positionBuffer,
    rotationBuffer
);
waypoint_3 = new PointTurnWaypoint(
    pose2d, movementSpeed,
    turnSpeed, followRadius,
    positionBuffer, rotationBuffer
);
waypoint_3 = new PointTurnWaypoint(
    x, y, rotationRadians, movementSpeed,
    turnSpeed, followRadius,
    positionBuffer, rotationBuffer
);
waypoint_3 = new PointTurnWaypoint(
    x, y, movementSpeed,
    turnSpeed, followRadius,
    positionBuffer, rotationBuffer
);
```

**InterruptWaypoint**

The `action` here is an [InterruptAction](https://github.com/FTCLib/FTCLib/blob/master/FtcLib/src/main/java/com/arcrobotics/ftclib/purepursuit/actions/InterruptAction.java), which is an interface that the user can implement to create a custom action to occur at this point. A recommendation is to pair this with the [command paradigm](../command-base/command-system/) that FTCLib provides.

```java
// empty constructor
InterruptWaypoint waypoint_4 = new InterruptWaypoint();

/**
 * More detailed constructors
 */
waypoint_4 = new InterruptWaypoint(
    translation2d, rotation2d,
    movementSpeed, turnSpeed,
    followRadius, positionBuffer,
    rotationBuffer, action
);
waypoint_4 = new InterruptWaypoint(
    pose2d, movementSpeed, turnSpeed,
    followRadius, positionBuffer,
    rotationBuffer, action
);
waypoint_4 = new InterruptWaypoint(
    x, y, rotationRadians,
    movementSpeed, turnSpeed,
    followRadius, positionBuffer,
    rotationBuffer, action
);
waypoint_4 = new InterruptWaypoint(
    x, y, movementSpeed,
    turnSpeed, followRadius,
    positionBuffer, rotationBuffer,
    action
);
```

**EndWaypoint**

```java
// empty constructor
EndWaypoint waypoint_5 = new EndWaypoint();

/**
 * More detailed constructors
 */
waypoint_5 = new PointTurnWaypoint(
    translation2d, rotation2d,
    movementSpeed, turnSpeed,
    followRadius, positionBuffer,
    rotationBuffer
);
waypoint_5 = new PointTurnWaypoint(
    pose2d, movementSpeed,
    turnSpeed, followRadius,
    positionBuffer, rotationBuffer
);
waypoint_5 = new PointTurnWaypoint(
    x, y, rotationRadians, movementSpeed,
    turnSpeed, followRadius,
    positionBuffer, rotationBuffer
);
```

### Intersections

An intersection is the point where the follow distance represented by a circle around the robot meets the drawn path derived from the waypoints. The "best intersection" is determined by either waypoint order or heading. This intersection point where the circle meets the path is where the robot will move to. The pure pursuit algorithm determines the best intersection and calculates the motor powers needed to reach said position. This updates with each loop, so the intersection point can change with each step due to the movement of the robot.

## Using `followPath()`

The `followPath()` method is the automatic implementation of pure pursuit for FTCLib. For teams that want to use all of FTCLib's features to the fullest, this is the recommended process.

An important note for the pure pursuit algorithm is that it only works well with odometry. You can use the various odometry systems provided by FTCLib. An important thing to note is that `followPath()` makes use of the [Odometry](https://github.com/FTCLib/FTCLib/blob/master/FtcLib/src/main/java/com/arcrobotics/ftclib/kinematics/Odometry.java) abstract class and the [mecanum drivebase](https://docs.ftclib.org/ftclib/features/drivebases#mecanum). Then, the method will call the loop method and do everything for you.

```java
// we are using the waypoints we made in the above examples
Path m_path = new Path(waypoint_1, waypoint_2, waypoint_3,
                       waypoint_4, waypoint_5);

// follow path
m_path.followPath(m_robotDrive, m_robotOdometry);
```

An issue this method has is that we cannot directly access the hardware of the robot. As a result, the `update()` method for the odometry is not called in `followPath()`. Instead, we call `updatePose()`.

### Creating Your Odometry

As a way of working around this issue, the odometry needs to be setup in a particular way with [Suppliers](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html). A supplier is a functional interface that uses lambdas to reference a certain value. Let's work with the [HolonomicOdometry](https://github.com/FTCLib/FTCLib/blob/master/FtcLib/src/main/java/com/arcrobotics/ftclib/kinematics/HolonomicOdometry.java) class for these examples.

You're going to want to instantiate your odometry using this constructor:

```java
HolonomicOdometry m_robotOdometry = new HolonomicOdometry(
    leftValue, rightValue, horizontalValue, trackWidth
);
```

#### Creating the Suppliers

Before we can create the object, we need to make our suppliers. We will do this by using the [DoubleSupplier](https://docs.oracle.com/javase/8/docs/api/java/util/function/DoubleSupplier.html) implementation. We need to create three of these objects: one for each odometer. Let's assume the user has created a method that automatically converts ticks to inches for an external encoder.

```java
DoubleSupplier leftValue, rightValue, horizontalValue;

leftValue = () -> ticksToInches(m_lOdom.getCounts());
rightValue = () -> ticksToInches(m_rOdom.getCounts());
horizontalValue = () -> ticksToInches(m_hOdom.getCounts());
```

### Making it a Command

We can follow the command paradigm to make this a command. For each path, you can create a command and then put it into a command group. This is the recommended practice you should follow when utilizing FTCLib's pure pursuit implementation.

## Using `loop()`

The alternative to the `followPath()` method is the `loop()` method. For teams that want to use solely the FTCLib implementation of pure pursuit and perform the rest of the actions themselves, then this is the more appealing method.

The use of suppliers can be avoided using this method since it can be called in your own class with access to the hardware directly.

### Odometry Options

The odometry is much more open for this. You can use whatever constructor you desire for it. Note that you are not limited to use only [ConstantVeloMecanumOdometry](https://github.com/FTCLib/FTCLib/blob/master/FtcLib/src/main/java/com/arcrobotics/ftclib/kinematics/ConstantVeloMecanumOdometry.java) or [HolonomicOdometry](https://github.com/FTCLib/FTCLib/blob/master/FtcLib/src/main/java/com/arcrobotics/ftclib/kinematics/HolonomicOdometry.java). Since the method parameters only take x, y, and heading values, you can use whatever odometry system you desire as long as it produces such values. This is one of the more appealing aspects of the `loop()` method.

The important thing for odometry is to remember to update the position of the robot after each iteration after manually inputting the motor speeds.

### Initializing the Path

Before you can call the `loop()` method, you need to follow the proper procedure. You need to call the `init()` method to ensure your path is legal and set up the unconfigured waypoints.

```java
// we are using the waypoints we made in the above examples
Path m_path = new Path(waypoint_1, waypoint_2, waypoint_3,
                       waypoint_4, waypoint_5);

m_path.init(); // initialize the path
```

If the path is not legal, an exception will be thrown.

### Calling the Method

This is the principle path method. After everything is configured and initiated, this method can be used. Using the robot's x, y, and rotation, this method calculates the appropriate motor powers for the robot to follow the path. This method calls all triggered/interrupted actions automatically. If this returns zero motor speeds `{0, 0, 0}` that means the path has either \(1\) timed out, \(2\) lost the path and retrace was disabled, or \(3\) reached the destination. Use `isFinished()` and `timedOut()` to troubleshoot.

Below is an example using a custom robot class that includes the drivebase and odometry:

```java
while (!m_path.isFinished()) {
    if (m_path.timedOut())
        throw new InterruptedException("Timed out");
    
    // return the motor speeds
    double speeds[] = m_path.loop(m_robot.getX(), m_robot.getY(),
                                  m_robot.getHeading());
    
    m_robot.drive(speeds[0], speeds[1], speeds[2]);
    m_robot.updatePose();
}
m_robot.stop();
```

