---
title: "Kinematics"
permalink: /pond/features-kinematics
toc: true
---
Welcome to the kinematics page!

Using kinematics, we know the robot's coordinates and orientation on the field. Below is a diagram of field axes:

<img src="/images/pond/coordinate-system.jpg" width="600" style="display: block; margin: 0 auto;" />

## Dead Wheel Kinematics
### Overview
Provides an accurate **field-relative localization** using three unpowered encoded wheels (dead wheels) by comparing encoder delta's over time.

### Updates
`update` takes the integer positions of the left, right, and center dead wheel to calculate the new robot position on the field.

`updateAndReset` takes the integer positions of the dead wheels as well as a pose to set the robots current position to the current one and the velocity to zero. 

### Getting Pose and Velocity
Simply returns the `currentPoseAndVelocity` in the form of a PoseAndVelocity.

## Linear Actuation

### Overview
The classes that deal with Linear Actuation convert **pulses** to **linear distance** (in millimeters) and vice versa, given the parameters of the gear-ratio's and wheel diameters. Deals with linear motion. This type of motion refers to linear slides, linear actuators, and etc.

### Pulses/Linear Conversion
The `LinearActuatorCalculator` converts between measurements in **linear distance** to **pulses**, and vice versa. When converting from encoder pulses to distance, you use the `pulsesToDistance` method which receives an integer (pulses) and returns a double (linear distance), and when converting from linear distance to encoder pulses, you use the `lengthToPulses` method which receives a double (linear distance) and returns an integer (pulses).

## Angular Actuatation

### Overview
The classes that deal with Angular Actuation convert **pulses** to **angular rotation** (in radians) and vice versa, given the parameters of the gear-ratio's. Deals with angular motion/rotation. This type of motion refers to rotatin arms, joints, and etc.

### Pulse/Angle Conversion
The `AngularActuatorCalculator` converts between encoder **pulses** to measurement in **radians**, and vice versa. When converting from encoder pulses to radians, you use the `pulsesToAngle` method which receives a integer (pulses) and returns a double (radians), and when converting from radians to encoder pulses, you use the `angleToPulses` method which receives a double (radians) and returns a double (pulses).







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

