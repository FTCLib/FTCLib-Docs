---
description: package com.arcrobotics.ftclib.gamepad
---

# Gamepad Extensions

The FTCLib provides enhanced Gamepad features. These classes are essentially extensions of the stock FTC SDK Gamepad features but with easier implementation methods.

## GamepadKeys

Provides enum represenations of the buttons, D-Pad, bumpers, and triggers. Buttons, D-Pad, and bumpers are stored in `GamepadKeys.Button` and triggers are stored in `GamepadKeys.Trigger`.

## GamepadEx

An extension of the stock FTC SDK `Gamepad` class. Constructed simply from a Gamepad. Provides six intuitive value-getting methods:

* `getButton()`: Given a `GamepadKeys.Button`, this method will check if that Button is pressed, returning a boolean of whether that Button is pressed.
* `getTrigger()`: Given a `GamepadKeys.Trigger`, this method will return the value of the Trigger \(0 if unpressed, 1 if fully depressed\).
* `getLeftY()`: Returns the value of the y-axis of the left joystick
* `getRightY()`: Returns the value of the y-axis of the right joystick
* `getLeftX()`: Returns the value of the x-axis of the left joystick
* `getRightX()`: Returns the value of the x-axis of the right joystick

## KeyReader

## TriggerReader

## ButtonReader

## ToggleButtonReader

