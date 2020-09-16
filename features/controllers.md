---
description: package com.arcrobotics.ftclib.controller
---

# Controllers

In FTCLib, there are controllers that can improve the motion of mechanisms in FTC. This includes PID control and feedforward control.

## PID Control

Our base class is `PIDFController` for the FTCLib PID control scheme. This class performs the calculations for PIDF, which are proportional, integral, derivative, and feedforward values.

### Using the PIDFController Class

#### Constructing a PIDFController

In order to use FTCLib's PIDF control functionality, users must first construct a `PIDFController` object with the desired gains:

```java
// Creates a PIDFController with gains kP, kI, kD, and kF
PIDFController pidf = new PIDFController(new double[]{kP, kI, kD, kF});
```

Note how the gains are passed into the constructor as a double array. Alternatively, you can also pass in an additional three parameters: the setpoint, previous value, and period. The default values for these are 0, 0, and 0.02. The default period being 0.02 represents 20 milliseconds for synchronous use in the periodic loop. If needed, set a timer to see what your loop time is and adjust accordingly.

#### Using the Feedback Loop Output

The `PIDFController` assumes that the `calculate()` method is being called regularly at an interval consistent with the configured period. Failure to do this will result in unintended loop behavior. **This means you should tune your period value**.

Using the constructed `PIDFController` is simple: call the `calculate()` method from the main loop.

```java
// Calculates the output of the PIDF algorithm based on the sensor reading
// and sends it to a motor
motor.set(pidf.calculate(encoder.getCurrentTicks(), setpoint));
```

#### Using the Motor Control Feature

The `control()` method Implements a control calculation onto the affected motor. Please note that what this does is move the motor until it reaches the setpoint. Once the motor reaches the target, the motor will continue moving with a speed of `kF * setpoint`. If you set `kF` to 0, the motor will stop. Alternatively to setting the kF value to 0, you can use a `PIDController` if you do not want to use the feedforward feature of the controller. Alternatively to the code seen in the previous example, you can:

```java
pidf.setSetPoint(setpoint);
pidf.control(motor, encoder.getCurrentTicks());
```

#### Checking Errors

The methods `getPositionError()` and `getVelocityError()` are named assuming that the loop is controlling a position - for a loop that is controlling a velocity, these return the velocity error and the acceleration error, respectively.

The current error of the measured process variable is returned by the `getPositionError()` function, while its derivative is returned by the `getVelocityError()` function.

#### Specifying and Checking Tolerances

If only a position tolerance is specified, the velocity tolerance defaults to infinity.

As above, “position” refers to the process variable measurement, and “velocity” to its derivative - thus, for a velocity loop, these are actually velocity and acceleration, respectively.

Occasionally, it is useful to know if a controller has tracked the setpoint to within a given tolerance - for example, to determine if a command should be ended, or \(while following a motion profile\) if motion is being impeded and needs to be re-planned.

To do this, we first must specify the tolerances with the `setTolerance()` method; then, we can check it with the `atSetpoint()` method.

```java
// Sets the error tolerance to 5, and the error derivative tolerance to 10 per second
pidf.setTolerance(5, 10);

// Returns true if the error is less than 5 units, and the
// error derivative is less than 10 units
pidf.atSetPoint();
```

#### Resetting the Controller

It is sometimes desirable to clear the internal state \(most importantly, the integral accumulator\) of a `PIDFController`, as it may be no longer valid \(e.g. when the `PIDFController` has been disabled and then re-enabled\). This can be accomplished by calling the `reset()` method.

## Feedforward Control

So far, we’ve used feedback control for reference tracking \(making a system’s output follow a desired reference signal\). While this is effective, it’s a reactionary measure; the system won’t start applying control effort until the system is already behind. If we could tell the controller about the desired movement and required input beforehand, the system could react quicker and the feedback controller could do less work. A controller that feeds information forward into the plant like this is called a feedforward controller.

A feedforward controller injects information about the system’s dynamics \(like a mathematical model does\) or the intended movement. Feedforward handles parts of the control actions we already know must be applied to make a system track a reference, then feedback compensates for what we do not or cannot know about the system’s behavior at runtime.

There are two types of feedforwards: model-based feedforward and feedforward for unmodeled dynamics. The first solves a mathematical model of the system for the inputs required to meet desired velocities and accelerations. The second compensates for unmodeled forces or behaviors directly so the feedback controller doesn’t have to. Both types can facilitate simpler feedback controllers. We’ll cover several examples below.

FTCLib provides a number of classes to help users implement accurate feedforward control for their mechanisms. In many ways, an accurate feedforward is more important than feedback to effective control of a mechanism. Since most FTC mechanisms closely obey well-understood system equations, starting with an accurate feedforward is both easy and hugely beneficial to accurate and robust mechanism control.

FTCLib currently provides the following three helper classes for feedforward control. The feedforward components will calculate outputs in units determined by the units of the user-provided feedforward gains. Users _must_ take care to keep units consistent as it does not have a type-safe unit system.

### SimpleMotorFeedforward

The `SimpleMotorFeedforward` class calculates feedforwards for mechanisms that consist of permanent-magnet DC motors with no external loading other than friction and inertia, such as flywheels and robot drives.

To create a `SimpleMotorFeedforward`, simply construct it with the required gains:

```java
// Create a new SimpleMotorFeedforward with gains kS, kV, and kA
SimpleMotorFeedforward feedforward = new SimpleMotorFeedforward(kS, kV, kA);
```

Please note that the `kA` value is optional. If the mechanism does not have much inertia, then it is not required. For the `pidWrite()` method in `MotorEx`, the acceleration is not used. This is true for the other feedforward classes as well.

To calculate the feedforward, simply call the `calculate()` method with the desired motor velocity and acceleration:

```java
// Calculates the feedforward for a velocity of 10 units/second and an acceleration of 20 units/second^2
// Units are determined by the units of the gains passed in at construction.
feedforward.calculate(10, 20);
```

### ArmFeedforward

The `ArmFeedforward` class calculates feedforwards for arms that are controlled directly by a permanent-magnet DC motor, with external loading of friction, inertia, and mass of the arm. This is an accurate model of most arms in FTC.

To create an `ArmFeedforward`, simply construct it with the required gains:

```java
// Create a new ArmFeedforward with gains kS, kCos, kV, and kA
ArmFeedforward feedforward = new ArmFeedforward(kS, kCos, kV, kA);
```

To calculate the feedforward, simply call the `calculate()` method with the desired arm position, velocity, and acceleration:

```java
// Calculates the feedforward for a position of 1 units, a velocity of 2 units/second, and
// an acceleration of 3 units/second^2
// Units are determined by the units of the gains passed in at construction.
feedforward.calculate(1, 2, 3);
```

### ElevatorFeedforward

The `ElevatorFeedforward` class calculates feedforwards for elevators that consist of permanent-magnet DC motors loaded by friction, inertia, and the mass of the elevator. This is an accurate model of most elevators in FTC.

To create a `ElevatorFeedforward`, simply construct it with the required gains:

```java
// Create a new ElevatorFeedforward with gains kS, kG, kV, and kA
ElevatorFeedforward feedforward = new ElevatorFeedforward(kS, kG, kV, kA);
```

To calculate the feedforward, simply call the `calculate()` method with the desired motor velocity and acceleration:

```java
// Calculates the feedforward for a position of 10 units, velocity of 20 units/second,
// and an acceleration of 30 units/second^2
// Units are determined by the units of the gains passed in at construction.
feedforward.calculate(10, 20, 30);
```

### Using Feedforward to Control a Mechanism

Feedforward control can be used entirely on its own, without a feedback controller. This is known as “open-loop” control, and for many mechanisms \(especially robot drives\) can be perfectly satisfactory. A `SimpleMotorFeedforward` might be employed to control a robot drive as follows:

```java
public void tankDriveWithFeedforward(double leftVelocity, double rightVelocity) {
  leftMotor.set(feedforward.calculate(leftVelocity));
  rightMotor.set(feedforward.calculate(rightVelocity));
}
```

