---
title: "Commands"
permalink: /pond/features-commands
toc: true
---
This is how the commands are executed:

{% raw %}
<div class="mermaid">
stateDiagram-v2
    direction LR
    [*] --> IDLE
    IDLE --> EXECUTING:[IsTriggered = True]
    EXECUTING --> EXECUTING
    EXECUTING --> IDLE: [IsCompleted = True]
    EXECUTING --> [*]: [IsCompleted = True <br> and RunOnce]
</div>
{% endraw %}

## Available Commands' Usage

---

## Any Command
AnyCommand manages a list of commands but only runs one at a time. It waits for any command in the list to trigger, runs that one exclusively, and finishes when that command completes.

## Command Builder
AnyCommand is a command that manages a list of child commands but runs only one at a time. It waits for any command in the list to be triggered, executes that command exclusively, and completes when the active command finishes. It also ensures logging is set up for all child commands.

## Dynamic Command
DynamicCommand is a customizable command that adapts to the environment by running only when triggered, doing the provided action, and stopping when its condition is met.

`getIsTriggered` → Checks if the command should start by evaluating the triggerCondition.

`periodic` → Runs the periodic action repeatedly while the command is active.

`getIsCompleted` → Checks if the command is done by evaluating the stopCondition.

---

## Follow Trajectory Command
FollowTrajectoryCommand makes the robot drive along a planned path and stop once it reaches the end accurately.

`create` → Factory method that builds a new command using a trajectory and robot subsystems from the hub.

`onStart` → Resets the stopwatch so trajectory timing begins at 0.

`periodic` → Runs every cycle:
1. Gets the target pose from the trajectory at the current time.
2. Reads the robot’s current pose from the localizer.
3. Uses the holonomic controller to compute motor power to follow the path.
4. Sends power to the drivetrain.
5. If the trajectory time is done and the robot is within the error limits, marks the command as completed.

`onStop` → Stops the drivetrain to ensure the robot doesn’t keep moving.

`isOnTarget` → Checks if the robot is close enough to the goal (position, heading, and speed) based on allowed error tolerances.

---

## Parallel Command
ParallelCommand starts multiple commands at the same time, runs them together, and only finishes once all have completed or been removed.

`onStart` → Starts all child commands that are currently triggered.

`periodic` → Runs each active command, removes it when completed, and marks itself done when all child commands finish.

`setLogger` → Assigns the telemetry logger to this command and all its children.

---

## Sequential Command

`periodic` → Runs commands one by one in order:
1. Finds the next triggered command in the list.
2. Starts it and runs its periodic logic until it finishes or times out.
3. Once it’s done, stops it and moves to the next command.
4. Marks itself complete when all commands are finished.

`setLogger` → Passes the telemetry logger to itself and all child commands.


