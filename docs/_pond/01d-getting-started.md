---
title: "Getting Started"
permalink: /pond/getting-started
toc: true
---

## Introduction

This page you guide you step-by-step through the Pond starter classes, while explaining their basic functionality.

> Remember, *Pond* assists in writing high-performance robot code by making sure that all processing happens in the "periodic" method of Commands and Subsystem. For this to work correctly, you should never block the execution in your robot code (either by having infinite loops or calling *sleep* method).
{: .notice--danger}

## Sample Operation Drive

> File: OperationDrive

This class extends the *OperationBase* base class from the Pond integrations and defines 4 methods:

| Method     | When it Executes                            | What it Does                                                                                                                       |
| :--------- | :------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| onInit     | Called on Drive Hub operation `Init`        | Initializes robot components (no movement is allowed)                                                                              |
| onStart    | Called on Drive Hub operation `Start`       | Creates commands that allow for robot control                                                                              |
| onPeriodic | Called periodically after onStart completes | Allows for any custom recurring code to be executed. Please favor using commands or subsystems instead adding code in this method. |
| onStop     | Called on Drive Hub operation `Stop`        | Allows for custom code to be executed when the drive operation ends. |

### onInit

Creates a `HardwareMapAccessor` (See [Pond Integration]) and then add the robot subsystems. Subsystems are independent robot components.

```java
    @Override
    protected void onInit() {

        HardwareMapAccessor hardwareMapAccessor = new HardwareMapAccessor(hardwareMap);

        robotSubsystems.add(new DeadWheelsLocalizer(hardwareMapAccessor, logger));
        robotSubsystems.add(new ArmController(hardwareMapAccessor, logger));
        robotSubsystems.add(new MecanumDrive(hardwareMapAccessor, logger));
    }
```

Once the subsystems are added, they are ready to be operated. Since no movement is allowed until the Drive Operation starts, commands are only added on the `onStart` method.

### onStart

Creates the required commands to operate the robot during drive operation.

```java
    @Override
    protected void onStart(){

        // Setup driving commands.
        IDrivetrain drivetrain = robotSubsystems.findFirst(IDrivetrain.class);
        ArmController armController = robotSubsystems.findFirst(ArmController.class);

        // Chassis movement
        Command chassisMovement =
            Commands.dynamic()
                .when(CommandConditionBuilder::Always)
                .execute(() -> drivetrain.setPower(new Pose2D(-gamepad1.left_stick_y, -gamepad1.left_stick_x, -gamepad1.right_stick_x)))
                .build("JoystickCommand");

        commandScheduler.runPeriodically(chassisMovement);

        // Arm Movement
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

        commandScheduler.runPeriodically(armMovement);
    }
```

At the beginning of the method, the required subsystems are retrieved from the robot, so they can be referenced by commands.

The `chassisMovement` command executes continuously, and at every cycle, it reads the joystick values and sets the power targets on the drive train accordingly. This is what controls robot movement on the field.

The `armMovement` command monitors buttons and when a button is pressed it executes the intended movement on the arm. In this case button `A` raises the robot arm and button `B` lowers the arm. Note that the commands are wrapped by an `anyOf` command, which will execute one command of the set at a time.

> Notice that there are no loops in this method! The commands that control the robot are created and they will continuously do their job until the Drive Operation end. Isn't that neat 😀
{: .notice--success}

### onPeriodic

This method is called on every processing cycle. If you are making good use of Subsystems and Commands, you should not have any code in this method.

### onStop

Any final code that you may want to run when the robot is stopped.

## Sample Configuration

> File: Configuration

Any robot needs configurations values, and most likely, lots of them! While *Pond* doesn't require [FTC Dashboard], its is defined in a way that it makes it easy to have the required configuration values defined in a way that is compatible with its use.

```java
public class Configuration {

    /**
     * Chassis configurations.
     */
    @Config
    public static class Chassis {

        // Motors
        public static MotorParams MotorFrontLeft    = new MotorParams("front_left", MotorParams.Mode.RUN_WITHOUT_ENCODER,false);
        public static MotorParams MotorFrontRight   = new MotorParams("front_right", MotorParams.Mode.RUN_WITHOUT_ENCODER,true);
        public static MotorParams MotorRearLeft     = new MotorParams("rear_left", MotorParams.Mode.RUN_WITHOUT_ENCODER,false);
        public static MotorParams MotorRearRight    = new MotorParams("rear_right", MotorParams.Mode.RUN_WITHOUT_ENCODER,true);

        // Dead wheels parameters
        public static DeadWheelsKinematicsParams DeadWheels = new DeadWheelsKinematicsParams(
            63,
            196,
            2 * Math.PI * 18.688524,
            8192);                          // https://www.revrobotics.com/rev-11-1271/
    }

    /**
     * Arm configuration
     */
    @Config
    public static class Arm {

        public static AngularActuatorParams PivotActuation = new AngularActuatorParams(
            StandardGearRatios.GOBILDA_223_RPM,
            5.0);

        public static MotorParams MotorArm = new MotorParams("slide_pivot", MotorParams.Mode.RUN_TO_POSITION,true);
    }

    // ...
}
```

The `Configuration` class holds all robot parameters that may need to be adjusted (either because you are a new team using the repository, or because the the team is trying out different settings). Each subsection is defined as a child `static` class. This also allows for each one of the sections to be published to [FTC Dashboard] through the `@Config` attribute

> If you don't want to use FTC Dashboard, simply remove the `@Config` attributes.
{: .notice}

The `Chassis` subsection holds all the configuration values for your drive train.

## Sample Subsystems

### Mecanum Drive

### Dead Wheels Localizer

### Arm Controller

## Sample Autonomous

TBD

# TO BE MOVED:



## Using Kinematics Classes

Kinematic classes in Pond are designed to abstract the complex math of robot movement and localization into reusable, testable, and modular components. Whether you're a beginner or a veteran team, they help separate what your robot should do from how it does it, keeping your code clean and scalable.

**What Are Kinematic Classes?**

A kinematic class is a custom class you create to handle the math and logic of how your robot should move — typically based on forward and inverse kinematics.
Instead of telling each motor what to do individually, you tell your kinematic class what movement you want, and it calculates the motor powers for you.

**What Are Kinematic Classes in Pond?**

 Kinematic classes in Pond, like:
- MecanumForwardKinematics
- MecanumInverseKinematics
- MecanumKinematicsParams

...are math utilities that calculate:
- Inverse kinematics: What motor outputs are needed to move the robot a certain way
- Forward kinematics: What the robot’s actual movement is based on encoder/motor inputs

These are independent of the FTC SDK and designed to integrate into Pond’s subsystems and commands model.

**In Summary**

Kinematic classes in Pond give you:
- Accurate movement & localization math
- Cleaner, more modular robot code
- A beginner-friendly path to advanced control
- A system that integrates seamlessly into Pond's subsystem & command structure

They are perfect for teams who want:
- Simplified TeleOp control
- Real-time pose estimation in Autonomous
- A customizable alternative to full localization libraries like Road Runner


## Writing Your First Subsystem

**Using SubsystemBase**

To keep your robot code clean and consistent, all your subsystems should extend from SubsystemBase. This gives you built-in support for logging, without repeating the same setup every time.

**What Is SubsystemBase?**

SubsystemBase is a class your team created to make writing subsystems easier. It:

- Sets up a logger for each subsystem

- Automatically generates a clear log tag (like subsys/DrivetrainSubsystem)

- Makes your code shorter, cleaner, and easier to debug

   
## Writing Your First Command

**How to Use It**

Here’s how you extend SubsystemBase in your own subsystem:

Step 1: Create a New Subsystem Class

Start by creating a new Java file for your subsystem. For example:
```
// DrivetrainSubsystem.java
package com.automaducks.pond.subsystems;

public class DrivetrainSubsystem extends SubsystemBase {
    // You’ll add your code here next
}
```

This makes the class inherent everything from SubystemBase, like logging and utility functions.

Step 2: 
Inside your class, create variables. You'll need a logger to return telemetry during operation:

```
    private final ITelemetryLogger logger;
```

Hardware and control parameters, variables that store your hardware and control settings:

```
private final DcMotor leftMotor;
private final DcMotor rightMotor;
```

[Pond Integration]: </pond/integration> "Pond Integration"
[FTC Dashboard]: <https://acmerobotics.github.io/ftc-dashboard/> "FTC Dashboard"
