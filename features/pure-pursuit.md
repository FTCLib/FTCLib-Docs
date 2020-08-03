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

## Waypoints

There are five types of waypoints: start, general, interrupted, point-turn, and end. The starting waypoint represents the first point in the path; conversely, the ending waypoint is the last point in the path.

A general waypoint is a point where the robot performs its ordinary pursuit algorithm with the look-ahead method. A point-turn waypoint is a type of general waypoint that stops at the given point, turns, and then traverses to the next waypoint. An interrupted waypoint is a type of point-turn waypoint where other actions can occur, such as picking up a skystone.

