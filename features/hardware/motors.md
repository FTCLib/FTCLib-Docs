---
description: package com.arcrobotics.ftclib.hardware.motors
---

# Motors

FTCLib offers fully-featured motor wrappers for the ease of the user. Behind the scenes, it utilizes the advanced features of FTCLib to produce a more powerful implementation of the DcMotor objects offered in the SDK. Similarly, FTCLib has a `Motor` and `MotorEx` object, each of which allow for the user to directly access the instance object from the hardware map in the case of wanting to work with it directly.

## Creating a Motor Object

Creating a motor is as simple as passing in the hardware map, the name of the device in the robot controller config, and an optional third parameter of a GoBILDA motor type. This is necessary because the GoBILDA motors in the configuration don't specify the different max RPM \(rotations per minute\) and CPR \(counts per revolution\).

```java
Motor m_motor_1 = new Motor(hardwareMap, "motorOne");
Motor m_motor_2 = new Motor(hardwareMap, "motorTwo", GoBILDA.RPM_312);
```

### Establishing a RunMode

A RunMode is a method of running the motor when power is supplied. There are three modes: `VelocityControl`, `PositionControl`, and `RawPower`.

```java
// in Motor.java

/**
 * The RunMode of the motor.
 */
public enum RunMode {
    VelocityControl, PositionControl, RawPower
}
```

Raw power sets the motor power directly through a value of $$[-1, 1]$$ where the value represents the percentage of its maximum speed. This is also open loop control, which means there is no feedback control. This is the default mode of the motor.

```java
// set the run mode
m_motor.setRunMode(Motor.RunMode.RawPower);

// set the proportional output power of the motor
m_motor.set(0.37);    // 37% of maximum speed in current direction
```

Position control has the motor run to a desired position based on the input speed and the distance between current motor position and target position \(in counts\). This utilizes a P controller whose coefficient can be changed using `setPositionCoefficient(double)`. This is a tuned value. For tuning, we currently recommend using [FTC Dashboard](https://acmerobotics.github.io/ftc-dashboard/basics).

```java
// set the run mode
m_motor.setRunMode(Motor.RunMode.PositionControl);

// set and get the position coefficient
m_motor.setPositionCoefficient(0.05);
double kP = m_motor.getPositionCoefficient();

// set the target position
m_motor.setTargetPosition(1200);      // an integer representing
                                      // desired tick count

// set the tolerance
m_motor.setPositionTolerance(13.6);   // allowed maximum error

// perform the control loop
while (!m_motor.atTargetPosition()) {
  m_motor.set(0.75);
}
m_motor.stop(); // stop the motor
```

Velocity control has the motor run using velocity in ticks per second with both a feedback and feedforward controller rather than simply setting the speed to a percentage of the maximum output speed. This can lead to smoother control for your motors, and is highly recommended for autonomous programs.

```java
// set the run mode
m_motor.setRunMode(Motor.RunMode.VelocityControl);

// set and get the coefficients
m_motor.setVeloCoefficients(0.05, 0.01, 0.31);
double[] coeffs = m_motor.getVeloCoefficients();
double kP = coeffs[0];
double kI = coeffs[1];
double kD = coeffs[2];

// set and get the feedforward coefficients
m_motor.setFeedforwardCoefficients(0.92, 0.47);
double[] ffCoeffs = m_motor.getFeedforwardCoefficients();
double kS = ffCoeffs[0];
double kV = ffCoeffs[1];

// set the inversion factor of the motor
m_motor.setInverted(true);
boolean isInverted = m_motor.getInverted();

// set the output of the motor
m_motor.set(-0.54);
```

