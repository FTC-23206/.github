---
title: "Getting Started"
permalink: /pond/getting-started
toc: true
---
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

