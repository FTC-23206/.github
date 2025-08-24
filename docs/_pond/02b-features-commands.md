---
title: "Commands"
permalink: /pond/features-commands
toc: true
---

## Introduction

Commands are independent pieces of code that execute based on a give condition and can be used both for TeleOp and Autonomous operations. With commands you can focus in writing the key instructions for the robot to perform an action and you don't have to worry about writing loop or logic to execute them.

### Key Parts of a Command

| Part                | Description                                                                                 |
| :------------------ | :------------------------------------------------------------------------------------------ |
| **Start Condition** | Condition that causes the command to run. It could be a button being pressed or always true |
| **Periodic**        | Code that run on every scheduling cycle while your command is active.                       |
| **Stop Condition**  | Condition that causes the command to stop.                                                  |

This is state machine diagram of a command:

{% raw %}
<div class="mermaid">
stateDiagram-v2
    direction LR
    [*] --> IDLE
    IDLE --> ACTIVE:[ShouldStart = True]
    ACTIVE --> ACTIVE
    ACTIVE --> IDLE: [ShouldStop = True]
    ACTIVE --> [*]: [ShouldStop = True <br> and RunOnce]
</div>
{% endraw %}

1. A command starts at `Idle` state and once `ShouldStart` is true it enters the `Active` state.
2. While in `Active`, it will repeatedly call the `Periodic` method, until `ShouldStop` returns true.

A very simple command could be defined as the following:

* Always returns true on `ShouldStart`
* Performs a single action on `Periodic`
* Always returns true on `ShouldStop`

With the above, the command code on `Peridic` will execute when the command is scheduled.

### Command Scheduling

A command by itself doesn't do anything until it is scheduled. *Pond* provides a scheduler with 2 scheduling modes:

| Mode            | Description                                                                                                                |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------- |
| RunPeriodically | Command will continuously be evaluated. Once executed, the command will go back to the evaluation queue.                   |
| RunOnce         | Command will continuously be evaluated. Once executed, the command will be deleted from the scheduler and won't run again. |

## Pre-Defined Commands

Predefined commands are readily available for you to use and can be created through the `Commands` (plural) class.

### Dynamic Command

With the `DynamicCommand` you can define your own command logic without having to explicitly create a Command Class. With this, you can simply write your code logic in terms of [Java Lambda Expressions].

**Drivetrain Movement**

```java
Command chassisMovement =
    Commands.dynamic()
        .when(CommandConditionBuilder::Always)
        .execute(() -> drivetrain.setPower(new Pose2D(-gamepad1.left_stick_y, -gamepad1.left_stick_x, -gamepad1.right_stick_x)))
        .build("JoystickCommand");
```

**Arm Movement**

```java
Command armMovement =
    Commands.anyOf("ArmControl",
        Commands.dynamic()
            .when(c -> c.buttonWasJustPressed(() -> gamepad1.a))
            .execute(() -> armController.moveToAngle(Math.toRadians(90.0), 0.8))
            .build("RaiseArm"),
        Commands.dynamic()
            .when(c -> c.buttonWasJustPressed(() -> gamepad1.b))
            .execute(() -> armController.moveToAngle(Math.toRadians(0), 0.4))
            .build("LowerArm"));
```

### Grouping Commands

Grouping commands help ypu organize and execute groups of commands in different ways.

#### Any Command

Executes the first command in the list which is ready to be started. Completes when the command is completed.

```java
ExecutionCounter firstCounter = new ExecutionCounter();
Command firstCommand = Commands.dynamic()
    .when(() -> firstTriggered)
    .execute(firstCounter::increment)
    .build("firstCommand");

ExecutionCounter secondCounter = new ExecutionCounter();
Command secondCommand = Commands.dynamic()
    .when(() -> secondTriggered)
    .execute(secondCounter::increment)
    .build("secondCommand");

Command runAny = Commands.anyOf("anyCommand", firstCommand, secondCommand);
```

#### Parallel Command

Executes all the commands in the list in parallel. Completes when all commands are completed.

```java
Command parallelCommand = Commands.parallel(
    "Parallel Command",
    new TestCommand(() -> { counter.increment(); return true; }),
    new TestCommand(() -> { counter.increment(); return true; }));
```

#### Sequential Command

Executes all the commands in the list in sequence. Completes when all commands are completed.

```java
Command sequentialCommand = Commands.sequential(
    "Sequential Command",
    new TestCommand(() -> { counter.increment(); return true; } ),
    new TestCommand(() -> { counter.increment(); return true; } ));
```

### Follow Trajectory Command

FollowTrajectoryCommand makes the robot drive along a planned path and stop once it reaches the end accurately.

```java
Pose2D initialPose = new Pose2D(0, 0, 0);
FollowTrajectoryCommandParams trajectoryParams = org.firstinspires.ftc.teamcode.Configuration.Autonomous.TrajectoryParams;

ITimedTrajectory trajectory = org.firstinspires.ftc.teamcode.roadrunner.RoadRunner.createTrajectory(initialPose, b -> b
    .lineToX(600)
    .build());

Command command = Commands.followTrajectory("Test", trajectory, trajectoryParams, robotSubsystems);

commandScheduler.runOnce(command);
```

## Creating your own Command

You can define your own commands by extending the `Command` class.

[Java Lambda Expressions]: <https://www.w3schools.com/java/java_lambda.asp>
