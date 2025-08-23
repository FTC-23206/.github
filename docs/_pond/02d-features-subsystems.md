---
title: "Subsystems"
permalink: /pond/features-subsystems
toc: true
---

## Interfaces
### Holonomic Controller
`IHolonomicControl` : extends `ISubsystem` but provides a method to calculate the desired actuation of a holonomic drivetrain.

### Drivetrain
`IDrivetrain` : extends `IRealTimeSubsystem`but provides real-time power updates to the drivetrain.

## Subsystems
Subsystems will extend `SubsystemBase` and implement either `ISubsystem` or `IRealTimeSubsystem`.

`ISubsystem` : interface for robot subsystems

`SubsystemBase` : implements `ISubsystem` and provides a standard logging function using `logTag` and `logger`.

## Real Time Subsystems
Real Time Subsystems are subsystems that need to repeatedly execute or log actions.

`IRealTimeSubsystems` : extends `ISubsystem` but provides the `periodic` method to iterate actions within the subsystem. 

## Subsystem Hubs
`ISubsystemHub` : extends `IRealTimeSubsystem`. Contains the functions `add`, `findFirst`, and `remove`. The `add` function adds subsystems to the hub, the `findFirst` function returns the first subsystem in the hub that matches the given class type (throws an exception if none is found), and the `remove` function simply removes the subsystem from the hub.

`SubsystemHub` : extends `SubsystemBase` and implements `ISubsystem`. Has the same functions as `ISubsystemHub` and calls `periodic` on all subsystems in the hub that implement `IRealTimeSubsystem`.
