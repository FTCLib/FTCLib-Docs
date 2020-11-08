---
description: package com.arcrobotics.ftclib.trajectory
---

# Trajectory

In FTC, there are often games that require an autonomous where robots are moving from one position to another—sometimes repeatedly. A lot of teams implement this motion by moving forward, turning, then moving forward again. Sometimes this is done with a time-base or a unit of known distance.

While these methods are functional, it is better if we can have the robot turn and drive at the same time to optimize the motion. Below is a video showing how this trajectory generation and following works:

{% embed url="https://youtu.be/yVmJDOE3M2Y" caption="" %}

You can find the same information in the [wpilib docs](https://docs.wpilib.org/en/latest/docs/software/advanced-controls/trajectories/trajectory-generation.html).

