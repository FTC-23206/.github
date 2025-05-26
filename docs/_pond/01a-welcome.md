---
title: "Welcome to the Pond!"
permalink: /pond/welcome
toc: true
---
## What is Pond

Pond is a Java library made for FTC Teams of all levels. Much of the code is inspired by FRC [WPILib]{:target="_blank" rel="noopener"} and FTC [FTCLib]{:target="_blank" rel="noopener"} but there are also different approaches and functionality based on our learnings over the years.

Here are the main benefits of the library:

1. Performant programming through non-blocking *event driven* commands
2. Modular hardware control through *subsystems*
3. Works seamlessly with other popular libraries such as [Road Runner]{:target="_blank" rel="noopener"}, [FTC Dashboard]{:target="_blank" rel="noopener"} without depending on them.
4. It does not depend on the FTC SDK, thus allowing teams to have full control on FTC SDK version upgrade.

> **Beginner** teams can write advanced robot code by following the examples and using out-of-the-box library functionality while **veteran** teams can extend library functionality and also decide with specific features to use, based on the team needs.
{: .notice--info}

Continue reading below for an overview of the main benefits.

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
commandScheduler.runPeriodically(command -> command
    .when(condition -> condition.buttonWasJustPressed(() -> gamepad1.a))
    .execute(() -> armController.moveToAngle(Math.toRadians(90.0), 0.8))
    .build("RaiseArm"));
```

[WPILib]: <https://frcdocs.wpi.edu/en/2020/docs/software/wpilib-overview/index.html>
[FTCLib]: <https://ftclib.org/>
[Road Runner]: <https://rr.brott.dev/>
[FTC Dashboard]: <https://acmerobotics.github.io/ftc-dashboard/>
