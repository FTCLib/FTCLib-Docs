---
description: package com.arcrobotics.ftclib.command
---

# Commands

The FTCLib provides a Command-based OpMode to define your robot's `OpMode` routine. Instead of hard-coding instructions in your OpMode, you can leverage the reusability of the `CommandOpMode` structure to predefined `Command` routines and run them sequentially. You can also set timeouts for a `Command` to automatically terminate a running command.

#### Contents

* `Subsystem`
* `Command`
* `CommandOpMode`

## Subsystem

Defines an organized module on your robot. One such example is a linear slide lift powered by a motor connected to a spool, additionally using encoders or touch/magnetic sensors to act as limit switches, preventing the lift from exceeding its bounds. In this example, the motor, its encoder, and any sensors used would fall under a single "Lift" Subsystem.

A Subsystem has five lifecycle phases:

* `initialize()`: Prepares the physical hardware for activation of the Subsystem. This phase is intended to be used for hardware map initialization, zeroing of encoders \(as required\), and gathering any initially required sensor data.
* `reset()`: Returns the Subsystem back to its original state. This phase is distinct from `initialize()` as it is expected that the hardware map is already initialized. This phase is intended to return Subsystem hardware to its original condition and clear saved data.
* `loop()`: This is the main lifecycle phase of a Subsystem. This is where [Commands ](commands.md#command)could be issued to the Subsystem, or user/sensor input is used to operate the Subsystem. This phase is intended to repeatedly loop until `stop()` is called.
* `stop()`: Halts all action of the Subsystem, bringing all hardware devices to stop. It is recommended to set the Zero Power Behavior of Motors to `BRAKE` in this phase, as motion is designed to entirely cease. A Subsystem should be designed to enable a `reset()` from this phase to return it to normal operation.
* `disable()`: Deactivates the Subsystem, rendering it unusable until the next `initialize()`. It is recommended to set the Zero Power Behavior of Motors to `FLOAT` in this phase, as Subsystem will no longer be receiving input. A Subsystem should _NOT_ be designed to enable `reset()`, `initialize()` is required.

## Command

Defines a single, executable command. A Command defines the actions of multiples parts \(or [`Subsystem`](commands.md#subsystem)\) of the robot to take at once. A Command has three lifecycle phases:

* `initialize()`: The initial subroutine of a Command. Called once when the Command is initially scheduled.
* `execute()`: The main body of a Command. Called repeatedly while the Command is scheduled.
* `end()`: The action to take once the Command is completed. Called once on the Command's completion.

`Command` also provides a `isFinished()` method which returns `true` if the Command has completed and `false` otherwise. Note: `end()` will always run after the Command is unscheduled. You do not need to check if the Command `isFinished()` to run your own `end()` method.

