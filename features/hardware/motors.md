---
description: package com.arcrobotics.ftclib.hardware.motors
---

# Motors

FTCLib brings you the best abstraction and additions to your programmed motors. Unfortunately, unlike FRC, FTC motors are restricted heavily when it comes to software. Consequently, we decided not to directly port the `SpeedController` features from WPILib.

## The Motor Interface

Fortunately, however, we have provided plenty of motors for your customization and optimization needs. Instead of `SpeedController`, the base abstraction for your motors is the `Motor` interface that can be found [here](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/motors/Motor.java).

The javadocs on the interface explains how each method is to be used when creating a custom motor object.

Below is an example of a custom motor:

```java
import com.qualcomm.robotcore.hardware.DcMotor;
import com.qualcomm.robotcore.hardware.HardwareMap;

public class YellowJacket435 implements Motor {
  
    private DcMotor m_motor;
    private double resetVal;
    
    public static final double TICKS_PER_REV = 383.6;

    public YellowJacket435(HardwareMap hMap, String name) {
      m_motor = hMap.get(DcMotor.class, name);
    }
    
    @Override
    public void set(double speed) {
      m_motor.setPower(speed);
    }

    @Override
    public double get() {
      return m_motor.getPower();
    }

    @Override
    public void setInverted(boolean isInverted) {
      m_motor.setDirection(!isInverted ? DcMotor.Direction.FORWARD : DcMotor.Direction.REVERSE);
    }

    @Override
    public boolean getInverted() {
      return m_motor.getDirection() == DcMotor.Direction.REVERSE;
    }

    @Override
    public void disable() {
       m_motor.close();
    }

    @Override
    public void pidWrite(double output) {
      set(output);
    }
    
    @Override
    public void stopMotor() {
      set(0);
    }
    
    public double getEncoderCount() {
      return m_motor.getCurrentPosition() - resetVal;
    }

    public void resetEncoder() {
      resetVal += getEncoderCount();
    }

    public void getNumRevolutions() {
      return getEncoderCount() / TICKS_PER_REV;
    }

}
```

Alternatively to multiplying the power by some multiplier, we can just set the direction of the `DcMotor` object with `setDirection(DcMotor.Direction direction)`.

### MotorGroup

The [MotorGroup](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/motors/MotorGroup.java) class takes a set of motors and controls them like one set of objects. This is extremely useful for a differential drive.

### SimpleMotor

The implementation sample in FTCLib is the [SimpleMotor](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/motors/SimpleMotor.java) class. If you do not want to use custom motors, you can still use the FTCLib-compatible motor that has already been written for you.

## MotorEx

The [MotorEx](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/motors/MotorEx.java) class is an extension of the motor interface that allows for more customization and control. It adds in the `ZeroPowerBehavior` functionality of the `DcMotor` class in the SDK and implements a proportional control loop to control the deceleration.

For the `pidWrite(double output)` method, we introduce the `SimpleMotorFeedForward` that we discussed in [controllers](../controllers.md) along with PID control.

### SimpleMotorEx

Just like the motor interface, `MotorEx` has an implementation seen in [SimpleMotorEx](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/motors/SimpleMotorEx.java). All it does is define the abstract methods for you in case you do not want to create your own custom hardware.

### EncoderEx

The [EncoderEx](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/motors/SimpleMotorEx.java) class takes a `MotorEx` object into the constructor and makes use of custom and optimized methods for tracking position and encoder counts. This is where you can have the motor object run to a position as well, in an attempt to avoid utilizing the `RUN_TO_POSITION` run mode for the `DcMotor`.

## CRServo

Th [CRServo](https://github.com/FTCLib/FTCLib/blob/dev/FtcLib/src/main/java/com/arcrobotics/ftclib/hardware/motors/CRServo.java) class is just a motor object intended to be used for a continuous rotation servo. To use it, you create a custom implementation of the `Motor` interface where you pass a `CRServo` object from the SDK into the constructor. Then, using the `CRServo` class in FTCLib, you can extend its functionality and capabilities.

