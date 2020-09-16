---
description: package com.arcrobotics.ftclib.util;
---

# Look Up Tables

The FTClib provides 2 different variations of look up tables. In this year's game they can be used to store different set and tested velocities or angles. You can either retrieve the closest reference or you can interpolate through them.

## LUT \(Look Up Table\)

Provides a way to store values in a table to quickly retrieve them. For example, this might be used to store different speeds or angles based on certain distances. This class allows you to find the closest entry to the input. 

For Example if you enter:

| Input | Output |
| :---: | :---: |
| 0 | 0 |
| 1 | 1 |
| 2 | 1 |

When you request 1.1, it will return 1.

### Example Usage:

```java
import com.arcrobotics.ftclib.util.LUT;

LUT<Double, Double> speeds = new LUT<Double, Double>()
{{
    add(5.0, 1.0);
    add(4.0, 0.9);
    add(3.0, 0.75);
    add(2.0, 0.5);
    add(1.0, 0.2);
}};

double distance = odometry.getPose().getTranslation().getDistance(new Translation2d(5, 10));
shooter.set(speeds.getClosest(distance));
```

## InterpLUT \(Interpolated Look Up Table\)

Provides a way to fill in the gaps in the data. Similarly to the LUT above, this allows you to add data points and retrieve a data point given an output. The difference between a normal LUT and interpLUT is that interpolated LUT uses math to fill in all the gaps. Effectively generating filler data based on the data around it.

### Example Usage:

```java
import com.arcrobotics.ftclib.util.InterpLUT;

InterpLUT lut;

//Adding each val with a key
lut.add(5, 1);
lut.add(4.1, 0.9);
lut.add(3.6, 0.75);
lut.add(2.7, .5);
lut.add(1.1, 0.2);
//generating final equation
lut.createLUT();

double distance = odometry.getPose().getTranslation().getDistance(new Translation2d(5, 10));
shooter.set(lut.get(distance));

//getting the velo required and passing it to the shooter.
```



