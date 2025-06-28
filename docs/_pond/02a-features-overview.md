---
title: "Features Overview"
permalink: /pond/features-overview
toc: true
---
## Overview

These are all of the packages available in Pond:

<img src="/images/pond/pond-packages.png" width="600" style="display: block; margin: 0 auto;" />

### Package Functionality

| Package    | Contents                                                                        |
| :--------- | :------------------------------------------------------------------------------ |
| Common     | Common classes such as Pose2D, Duration, Stopwatch, etc.                        |
| Commands   | Command execution systems and common commands                                   |
| Control    | Systems control such as PID                                                     |
| Kinematics | Robot kinematics such as Mecanum Forward and Inverse kinematics calculators |
| Subsystems | Robot organization into subsystems                                              |
| Utility    | Utility classes for math, logging, strings, etc.                                |

## Subsystems

The use of **subsystems** promote modular, easy to maintain, robot code through the following:

* Each class has a clear job since the logic is organized by function.
* Subsystems make sure that a piece of hardware is only controlled by one command at a time.
* Having subsystems creates a modular approach that makes commands more reusable and testable.

Example:

```java
// Initalizes the robot subsystems
@Override
protected void onInit() {

    HardwareMapAccessor hardwareMapAccessor = new HardwareMapAccessor(hardwareMap);

    robotSubsystems.addIfNotNull(new ArmController(hardwareMapAccessor, logger));
    robotSubsystems.addIfNotNull(new MecanumDrive(hardwareMapAccessor, logger));
    robotSubsystems.addIfNotNull(new DeadWheelsLocalizer(hardwareMapAccessor, logger));
}
```

## Event-Driven Programming

FTC robots must be periodically evaluating their state - reading sensors, motor encoders, calculating odometry - but it is not trivial to program robot operations while ensuring that all components of the robot continue operating without delay. Event driven programming enables simple and efficient programming in a very simple an concise manner.

* Robot periodically process all its subsystems automatically
* Robot periodically monitors for the trigger conditions and runs the commands when required

The library also provides conditional helpers which makes it easy to check changes in state of buttons and sensors.

> **Competitiveness**: This structure supports intelligent, competitive robot behavior—whether you're following a trajectory or reacting to game elements.
{: .notice--info}

Example:

```java
// Moves the arm only when the button A was just pressed
commandScheduler.runPeriodically(Commands.dynamic()
    .when(CommandConditionBuilder::Always)
    .execute(() -> drivetrain.setPower(new Pose2D(-gamepad1.left_stick_y, -gamepad1.left_stick_x, -gamepad1.right_stick_x)))
    .build("JoystickCommand"));
```
