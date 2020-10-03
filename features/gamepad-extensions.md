---
description: package com.arcrobotics.ftclib.gamepad
---

# Gamepad

The FTCLib provides enhanced Gamepad features. These classes are essentially extensions of the stock FTC SDK Gamepad features but with easier implementation methods.

## GamepadKeys

Provides enum representations of the buttons, D-Pad, bumpers, and triggers. Buttons, D-Pad, and bumpers are stored in `GamepadKeys.Button` and triggers are stored in `GamepadKeys.Trigger`.

| Buttons |
| :--- |
| Y |
| X |
| A |
| B |
| LEFT\_BUMPER |
| RIGHT\_BUMPER |
| BACK |
| START |
| DPAD\_UP |
| DPAD\_DOWN |
| DPAD\_LEFT |
| DPAD\_RIGHT |
| LEFT\_STICK\_BUTTON |
| RIGHT\_STICK\_BUTTON |

| Trigger |
| :--- |
| LEFT\_TRIGGER |
| RIGHT\_TRIGGER |

```java
private GamepadButton grabButton = new GamepadButton(gamepad1, GamepadKeys.Button.A);
private GamepadButton releaseButton = new GamepadButton(gamepad2, GamepadKeys.Button.B);

private GamepadEx gamepadEx = new GamepadEx(gamepad1);
```

## GamepadEx

An extension of the stock FTC SDK `Gamepad` class. Constructed simply from a Gamepad. Provides six intuitive value-getting methods:

* `getButton()`: Given a `GamepadKeys.Button`, this method will check if that Button is pressed, returning a boolean of whether that Button is pressed.

```java
gamepadEx.getButton(GamepadKeys.Button.A);
```

* `getTrigger()`: Given a `GamepadKeys.Trigger`, this method will return the value of the Trigger \(0 if unpressed, 1 if fully depressed\).

```java
gamepadEx.getTrigger(GamepadKeys.Trigger.RIGHT_TRIGGER);
```

* `getLeftY()`: Returns the value of the y-axis of the left joystick

```java
gamepadEx.getLeftY();
```

* `getRightY()`: Returns the value of the y-axis of the right joystick

```java
gamepadEx.getRightY();
```

* `getLeftX()`: Returns the value of the x-axis of the left joystick

```java

```

```java
gamepadEx.getLeftX();
```

* `getRightX()`: Returns the value of the x-axis of the right joystick

```text
gamepadEx.getRightX();
```

## KeyReader

The `KeyReader` interface is the base for objects that monitor an individual button or trigger on a gamepad. All `Reader` classes must implement these functions:

* `readValue()`: Reads the current value of the key, true or false. This must be called in a loop in order to use functions that depend on the previous value of the key such as `wasJustPressed()` , `wasJustReleased()` , `stateJustChanged()` , or `getState` with `ToggleButtonReader` class. 
* `isDown()` : Checks if key is currently down. Will return a boolean of whether that key is pressed.
* `wasJustPressed()` : Returns boolean whether the key is pressed, but only if it was previously not pressed. 
* `wasJustReleased()` : Returns boolean indicating whether the key is not pressed, but only if it was previously pressed. 
* `stateJustChanged` : Returns boolean indicating that the key's value has switched.

## TriggerReader

```java
private TriggerReader triggerReader= new TriggerReader(gamepadEx, GamepadKeys.Trigger.RIGHT_TRIGGER);
```

The `TriggerReader` class implements the `KeyReader` interface. Because `GamepadEx` Triggers return a `double` , the `TriggerReader` class interprets a value of greater than `0.5` as a trigger press.

* `TriggerReader(GamepadEx gamepad, GamepadKeys.Trigger trigger, [String triggerName, Telemetry telemetry])` : Constructs a new Trigger Reader with a `GamepadEx` gamepad and `GamepadKeys.Trigger` trigger. `triggerName` and `telemetry` are optional parameters that display the boolean value of the trigger on the Driver Station phone's telemetry.

```java
triggerReader.isDown();
triggerReader.readValue();
triggerReader.stateJustChanged();
triggerReader.wasJustPressed();
triggerReader.wasJustReleased();
```

## ButtonReader

The `ButtonReader`class implements the `KeyReader` interface. It checks if a button is pressed, released, or is down.

```java
private ButtonReader reader = new ButtonReader(gamepadEx, GamepadKeys.Button.A);
```

* `ButtonReader(GamepadEx gamepad, GamepadKeys.Button button)`: Constructs a new Button Reader with a `GamepadEx` gamepad and a `GamepadKeys.Button` button. 
* `ButtonReader(BooleanSupplier supplier)`: Constructs a new Button Reader using the value of a boolean supplier instead of a gamepad, which allows reading value states easily without a gamepad.

```java
reader.readValue();
reader.wasJustPressed();
reader.stateJustChanged();
reader.isDown();
reader.wasJustPressed();
reader.wasJustReleased();
```

## ToggleButtonReader

```java
private ToggleButtonReader toggleButtonReader = new ToggleButtonReader(gamepadEx, GamepadKeys.Button.A);
```

The `ToggleButtonReader` class extends `ButtonReader` and adds the ability to get the status of a toggle. `readValue()` needs to be run in a loop to get the state of the toggle.

`getState()` : Gets the toggle value of a button or boolean supplier.

```java
toggleButtonReader.getState();
```

