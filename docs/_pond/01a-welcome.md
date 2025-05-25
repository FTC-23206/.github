---
title: "Welcome to the Pond!"
permalink: /pond/welcome/
toc: true
---
## What is Pond

## Functionality

These are a few of the benefits of using Pond library:
* It allows for event-driven programming.
  * The robot reacts only when something is changed, saving processing time and making it more responsive.
  * It becomes easier to describe what the robot should do *when* something happens.
  * The code will match driver and robot interactions naturally since it works well with individual events that perform specific tasks and that happen at a specific point in time.
      * Fun fact: These are called **discrete events**!
  * You can use CommandScheduler to run only the needed commands at the right time when reacting to events (eg, pressing a button).
* The library is built for subsystems; a **subsystem** is what handles the logic and state of a physical part of the robot.
  * Each class has a clear job since the logic is organized by function.
  * Subsystems make sure that a piece of hardware is only controlled by one command at a time.
  * Having subsystems creates a modular approach that makes commands more reusable and testable.

## Library Dependencies

Pond is a stand-alone library that does not have any additional dependency other than the Java Android SDK. It doesn't even depend on the FTC SDK, which means you don't have to worry about upgrading the Pond library when FIRST releases a new FTC SDK (although you still have to upgrade your robot code!).

> **Note:** Pond is a stand-alone library that does not require you to use any other additional FTC library, unless you choose to do so!

But that doesn't mean Pond doesn't work well with other libraries, in fact, it is built with the aim to take full advantage of common FTC Libraries:

* [Road Runner](https://rr.brott.dev/){:target="_blank" rel="noopener"}
* [FTC Dashboard](https://acmerobotics.github.io/ftc-dashboard/){:target="_blank" rel="noopener"}
