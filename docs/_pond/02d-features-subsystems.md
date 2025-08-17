---
title: "Subsystems"
permalink: /pond/features-subsystems
toc: true
---

## Interfaces
### Subsystem
`ISubsystem` : interface for the standard robot subsystem

### Holonomic Controller
`IHolonomicControl` : extends `ISubsystem` but provides a method to calculate the desired actuation

### Drivetrain
`IDrivetrain` : extends `IRealTimeSubsystem`but provides real-time power updates to the drivetrain

## Real Time Subsystems
`IRealTimeSubsystems` : extends `ISubsystem` but provides the `periodic` method to iterate actions within the subsystem. 

## Subsystems
`ISubsystem` : interface for robot subsystems
`SubsystemBase` : implements `ISubsystem` and provides a standard logging function using `logTag` and `logger`.

## Subsystem Hub
`ISubsystemHub` : ...
`SubsystemHub` : ...
