---
description: Using FTCLib-provided Commands to Enhance Your Program
---

# Convenience Commands

FTCLib offers convenience commands to make your paradigm program more compact. The idea is that it improves the overall structure of your program. Some of these convenience commands are just that: for convenience. This does not mean they are exceptional. Plenty are simplified for minimal competitive use, such as the [PurePursuitCommand](../../pathing/pure-pursuit.md#using-the-pure-pursuit-command).

## Framework Commands

Framework commands exist to decrease the amount of program needed for simplistic tasks, like updating a number. Rather than having the user create an entire command for one simplistic task \(which when done for multiple menial tasks builds up and makes the structure fairly disheveled\), the user can utilize framework commands.

### InstantCommand

`InstantCommand` is a versatile framework command that on initialization runs some task and then ends on that same iteration of the CommandScheduler's `run()` method. This is especially useful for button-triggered events.

```java
GamepadEx toolOp = new GamepadEx(gamepad2);

toolOp.getGamepadButton(GamepadKeys.Button.A)
    .whenPressed(new InstantCommand(() -> {
        // your implementation of run() here
    }));

/* AN EXAMPLE */

Motor intakeMotor = new Motor(hardwareMap, "intake");

toolOp.getGamepadButton(GamepadKeys.Button.RIGHT_BUMPER)
    .whileHeld(new InstantCommand(() -> {
        intakeMotor.set(0.75);
    }))
    .whenReleased(new InstantCommand(intakeMotor::stopMotor));
```

You should actually use subsystems here instead of the motor object. That way we can add the subsystem's requirements to the `InstantCommand`. Below is a proper example.

{% code title="Intake.java" %}
```java
/**
 * This is a pedagogical intake subsystem for
 * a universal intake consisting of one motor
 * that drives a belt that connects to a pulley
 * that drives a PVC tube.
 */
public class Intake extends SubsystemBase {
    private Motor m_intakeMotor;
    
    public Intake(Motor intakeMotor) {
        m_intakeMotor = intakeMotor;
    }
    
    public void run() {
        m_intakeMotor.set(0.75);
    }
    
    public void stop() {
        m_intakeMotor.stopMotor();
    }
}
```
{% endcode %}

And then we can set up our command bindings as such:

```java
/* in your opmode */

Motor intakeMotor = new Motor(hardwareMap, "intake");
Intake intake = new Intake(intakeMotor);

toolOp.getGamepadButton(GamepadKeys.Button.RIGHT_BUMPER)
    // the second parameter is a varargs of subsystems
    // to require
    .whileHeld(new InstantCommand(intake::run, intake))
    .whenReleased(new InstantCommand(intake::stop, intake));
```

This removes a lot of unnecessary clutter of commands since in a custom implementation the user would have to define a command for both running the intake and stopping it. With `InstantCommand`, the amount of code on the user-side is dramatically reduced.

