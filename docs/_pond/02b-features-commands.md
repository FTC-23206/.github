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
| **Periodic**        | Code that run on every scheduling cycle while your command is running.                      |
| **Stop Condition**  | Condition that causes the command to stop.                                                  |

This is state machine diagram of a command:

{% raw %}
<div class="mermaid">
stateDiagram-v2
    direction LR
    [*] --> IDLE
    IDLE --> RUNNING:[getIsRunnable() = True]
    RUNNING --> RUNNING
    RUNNING --> IDLE: [getIsCompleted() = True]
    RUNNING --> [*]: [getIsCompleted() = True <br> and RunOnce]
</div>
{% endraw %}

1. A command starts at `Idle` state and once `getIsRunnable()` is true it enters the `Running` state.
2. While in `Running`, it will repeatedly call the `periodic` method, until `getIsCompleted()` returns true.

A very simple command could be defined as the following:

* Always returns true on `getIsRunnable()`
* Performs a single action on `periodic`
* Always returns true on `getIsCompleted()`

With the above, the command code on `periodic` will execute when the command is scheduled.

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
    commandFactory.dynamic(
        "JoystickCommand",
        d -> d
        .when(CommandConditionBuilder::Always)
        .execute(() -> drivetrain.setPower(new Pose2D(-gamepad1.left_stick_y, -gamepad1.left_stick_x, -gamepad1.right_stick_x)))
    );

commandScheduler.runPeriodically(chassisMovement);
```

**Arm Movement**

```java
// Arm Movement
Command armMovement =
    commandFactory.anyOf("ArmControl",
        commandFactory.dynamic("RaiseArm",
            d -> d
            .when(c -> c.buttonWasJustPressed(() -> gamepad1.a))
            .execute(() -> armController.moveToAngle(Math.toRadians(90.0), 0.8))),
        commandFactory.dynamic("LowerArm",
            d -> d
            .when(c -> c.buttonWasJustPressed(() -> gamepad1.b))
            .execute(() -> armController.moveToAngle(Math.toRadians(0), 0.4)))
    );

commandScheduler.runPeriodically(armMovement);
```

### Grouping Commands

Grouping commands help ypu organize and execute groups of commands in different ways.

#### Any Command

Executes the first command in the list which is ready to be started. Completes when the command is completed.

```java
ExecutionCounter firstCounter = new ExecutionCounter();
Command firstCommand = commandFactory.dynamic("firstCommand", d -> d
    .when(() -> firstTriggered)
    .execute(firstCounter::increment));

ExecutionCounter secondCounter = new ExecutionCounter();
Command secondCommand = commandFactory.dynamic("secondCommand", d -> d
    .when(() -> secondTriggered)
    .execute(secondCounter::increment));

Command runAny = commandFactory.anyOf("anyCommand", firstCommand, secondCommand);
```

#### Parallel Command

Executes all the commands in the list in parallel. Completes when all commands are completed.

```java
ExecutionCounter counter = new ExecutionCounter();

Command parallelCommand = commandFactory.parallel(
    "Parallel Command",
    new TestCommand(this.logger, () -> { counter.increment(); return true; }),
    new TestCommand(this.logger, () -> { counter.increment(); return true; }));

this.scheduler.runOnce(parallelCommand);

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
CommandFactory commandFactory = new CommandFactory(this.robotSubsystems, this.logger);
FollowTrajectoryCommandParams trajectoryParams = Configuration.Autonomous.TrajectoryParams;

ITimedTrajectory trajectory = RoadRunner.createTrajectory(initialPose, b -> b
    .lineToX(60)
    .build());

Command command = commandFactory.followTrajectory("Test", trajectory, trajectoryParams);

commandScheduler.runOnce(command);
```

## Creating your own Command

You can define your own commands by extending the `Command` class.

[Java Lambda Expressions]: <https://www.w3schools.com/java/java_lambda.asp>
