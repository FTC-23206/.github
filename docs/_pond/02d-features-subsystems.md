---
title: "Subsystems"
permalink: /pond/features-subsystems
toc: true
---

## Controllers
### Holonomic Controller
The Holonomic controller is used when controlling the drivetrain in autonomous operations, it calculates the error in position and speed,  and applies the gains and robot-specific params to return the desired robot movement.

`IHolonomicController` : provides a method to calculate the desired actuation of a holonomic drivetrain.

`HolonomicController` : completes the calculations for actuation (also applies gains). Initializes the controller with params and logging utilities.

`HolonomicControllerParams` : holds the adjustable params within instsances of the Holonomic controller. These can be tuned in FTC Dashboard *or* in Configuration to your specific holonomic drivetrain. Speed gains are the factors that multiply the delta between the target speed and the current speed, and position gains are the factors that multiply the delta between the target position and current position.

### Drivetrain
The `IDrivetrain` interface is used to define the frame for controlling a drivetrain in driver-controlled operations, by using the `setPower` method to apply external outputs (ex. the gamepad) to your drivetrain.

`IDrivetrain` : extends `IRealTimeSubsystem` to provide real-time power updates to the drivetrain using the `periodic` function.

## Subsystems
Subsystems will extend `SubsystemBase` and implement either `ISubsystem` or `IRealTimeSubsystem`, a subsystem that is not "RealTime" does not need to provide periodic updates or execute periodic actions within the subsystem.

`ISubsystem` : interface for robot subsystems, framework for a robot subsystem.

`SubsystemBase` : implements `ISubsystem` and provides a standard logging function using `logTag` and `logger`.

## Real Time Subsystems
Real Time Subsystems are subsystems that need to repeatedly execute actions or log inputs/outputs.

`IRealTimeSubsystems` : extends `ISubsystem` but provides the `periodic` method to iterate actions within the subsystem. 

## Subsystem Hubs
A SubsystemHub stores a list of subsystems. Having a Subsystem Hub makes running periodic on Real Time Subsystems efficient and allows the access of multiple subsystems within a command.

`ISubsystemHub` : extends `IRealTimeSubsystem`. Contains the functions `add`, `findFirst`, and `remove`. The `add` function adds subsystems to the hub, the `findFirst` function returns the first subsystem in the hub that matches the given class type (throws an exception if none is found), and the `remove` function simply removes the subsystem from the hub.

`SubsystemHub` : extends `SubsystemBase` and implements `ISubsystem`. Has the same functions as `ISubsystemHub` and calls `periodic` on all subsystems in the hub that implement `IRealTimeSubsystem`.




## Writing Your First Subsystem

**Using SubsystemBase**

To keep your robot code clean and consistent, all your subsystems should extend from SubsystemBase. This gives you built-in support for logging, without repeating the same setup every time.

**What Is SubsystemBase?**

SubsystemBase is a class your team created to make writing subsystems easier. It:

- Sets up a logger for each subsystem

- Automatically generates a clear log tag (like subsys/DrivetrainSubsystem)

- Makes your code shorter, cleaner, and easier to debug