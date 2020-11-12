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

### ConditionalCommand

`ConditionalCommand` has a wide variety of uses. `ConditionalCommand` takes two commands and runs one when supplied a value of true, and another when supplied a value of false. Two very effective utilizations are toggling and state machines.

Let's take a look at our previous example of an intake and add an active state:

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
    private boolean m_active;
    
    public Intake(Motor intakeMotor) {
        m_intakeMotor = intakeMotor;
        m_active = true;
    }
    
    // return the current active state
    public boolean active() {
        return m_active;
    }
    
    // toggle the active state
    public void toggle() {
        m_active = !m_active;
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

Instead of only intaking when the right bumper is held, let's bind it to a single button press with a toggle. The first press activates the intake, the second button press will then stop the intake. This then repeats.

```java
/* in your opmode */

Motor intakeMotor = new Motor(hardwareMap, "intake");
Intake intake = new Intake(intakeMotor);

toolOp.getGamepadButton(GamepadKeys.Button.RIGHT_BUMPER)
    .whenPressed(new ConditionalCommand(
        new InstantCommand(intake::run, intake),
        new InstantCommand(intake::stop, intake),
        intake::active))
    .whenReleased(new InstantCommand(intake::toggle, intake));
```

Another example of usage could be in Velocity Vortex, where the beacons were either red or blue. Using a color sensor, we can detect the color and then perform some action based on whether it was red or blue.

