---
description: package com.arcrobotics.ftclib.command
---

# Commands

The FTCLib provides a Command-based OpMode to define your robot's `OpMode` routine. Instead of hard-coding instructions in your OpMode, you can leverage the reusability of the `CommandOpMode` structure to predefined `Command` routines and run them sequentially. You can also set timeouts for a `Command` to automatically terminate a running command.

## Command

Defines a single, executable command. A Command defines the actions of multiples parts \(or `Subsystem`\) of the robot to take at once. A Command has three main phases:

* `initialize()`: The initial subroutine of a Command. Called once when the Command is initially scheduled.
* `execute()`: The main body of a Command. Called repeatedly while the Command is scheduled.
* `end()`: The action to take once the Command is completed. Called once on the Command's completion.

`Command` also provides a `isFinished()` method which returns `true` if the Command has completed and `false` otherwise. Note: `end()` will always run after the Command is unscheduled. You do not need to check if the Command `isFinished()` to run your own `end()` method.

