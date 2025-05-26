---
title: "Welcome to the Pond!"
permalink: /pond/welcome/
toc: true
---
## What is Pond

Pond is a Java library made for FTC Teams of all levels. Much of the code is inspired by FRC [WPILib]{:target="_blank" rel="noopener"} and FTC [FTCLib]{:target="_blank" rel="noopener"} but there are also different approaches and functionality based on our learnings over the years.

Here are the main benefits of the library:

1. Promotes performant robot code through non-blocking *event driven programming*.
2. Promotes clean code organization through the use of *subsystems* for different robot modules.
3. It does not depend on the FTC SDK, thus allowing teams to have full control on FTC SDK version upgrade.
4. It is built to work seamlessly with other popular libraries such as [Road Runner]{:target="_blank" rel="noopener"}, [FTC Dashboard]{:target="_blank" rel="noopener"}, etc. without having any dependency on such libraries.

Teams can benefit from the library as follows:

* Beginner teams can write advanced robot code by following the examples and using out-of-the-box library functionality
* Veteran teams can extend library functionality and also decide with specific features to use, based on the team needs.

## In More Detail

### Event-Driven Programming

* The robot reacts only when something is changed, saving processing time and making it more responsive.
* It becomes easier to describe what the robot should do *when* something happens.
* The code will match driver and robot interactions naturally since it works well with individual events that perform specific tasks and that happen at a specific point in time.
  * Fun fact: These are called **discrete events**!
* You can use CommandScheduler to run only the needed commands at the right time when reacting to events (eg, pressing a button).

Example:

```java
// Moves the arm only when the button A was just pressed
commandScheduler.runPeriodically(d -> d
    .when(c -> c.buttonWasJustPressed(() -> gamepad1.a))
    .execute(() -> armController.moveToAngle(Math.toRadians(90.0), 0.8))
    .build("RaiseArm"));
```

### Subsystems

* The library is built for subsystems; a **subsystem** is what handles the logic and state of a physical part of the robot.
  * Each class has a clear job since the logic is organized by function.
  * Subsystems make sure that a piece of hardware is only controlled by one command at a time.
  * Having subsystems creates a modular approach that makes commands more reusable and testable.

[WPILib]: <https://frcdocs.wpi.edu/en/2020/docs/software/wpilib-overview/index.html>
[FTCLib]: <https://ftclib.org/>
[Road Runner]: <https://rr.brott.dev/>
[FTC Dashboard]: <https://acmerobotics.github.io/ftc-dashboard/>
