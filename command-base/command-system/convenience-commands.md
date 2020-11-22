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

`ConditionalCommand` has a wide variety of uses. `ConditionalCommand` takes two commands and runs one when supplied a value of true, and another when supplied a value of false.

An example of usage could be in Velocity Vortex, where the beacons were either red or blue. Using a color sensor, we can detect the color and then perform some action based on whether it was red or blue.

```java
// pseudocode for instantiating the command
ConditionalCommand pressBeacon = new ConditionalCommand(
    new InstantCommand(beaconPresser::pushRed, beaconPresser),
    new InstantCommand(beaconPresser::pushBlue, beaconPresser),
    () -> vision.output() == BeaconColor.RED
);

pressBeacon.schedule();    // schedule the command

/* This is also useful in sequential command groups */
```

As you can see, conditional commands are very useful for switching between states with a certain state. We will see later that we would want to use a `SelectCommand` when working with several states and not a simple command that switches between two.

### ScheduleCommand

Does exactly as the name suggests: schedules commands. You can input a variable number of command arguments to schedule, and the command will schedule them on initialization. After this, the command will finish. This is useful for forking off of command groups.

So far we've been using the convenience commands we've learned in tandem and how they can be used together to produce more efficient paradigm utility. This is no exception for the `ScheduleCommand`. We can use a conditional command to schedule a desired command.

```java
SequentialCommandGroup auto = new SequentialCommandGroup(
    ...,
    new ConditionalCommand(
        new ScheduleCommand(
            // schedule commands
        ),
        new ScheduleCommand(
            // schedule commands
        ),
        ...    // boolean supplier
    ),
    ...
);
```

It is important to note that the schedule command will finish immediately. Which means any following commands in the sequential command group will be run. The point of the schedule command is to fork off from the command group so that it can be run separately by the scheduler.

### SelectCommand

The select command is similar to a conditional command but for several commands. This is especially useful for a state machine. Let's take a look at the ring stack from the 2020-2021 Ultimate Goal season.

```java
public enum Height {
    ZERO, ONE, FOUR
}

public Height height() {
    // some code to detect height of the starter stack
}

...

SelectCommand wobbleCommand = new SelectCommand(
    // the first parameter is a map of commands
    new HashMap<Object, Command>() {{
        put(Height.ZERO, new ScheduleCommand(
            new PurePursuitCommand(...)));
        put(Height.ONE, new ScheduleCommand(
            new PurePursuitCommand(...)));
        put(Height.FOUR, new ScheduleCommand(
            new PurePursuitCommand(...)));
    }},
    // the selector
    this::height
);
```

