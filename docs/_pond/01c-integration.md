---
title: "Integration"
permalink: /pond/integration
toc: true
---

> **Skip this page** if you are using the 'Quick-Start Repository' setup method. The integration requirements are already taken care for you.
{: .notice--primary}

> **Work in Progress:** documentation for manual integration is still in progress. Use the [Pond-Starter](https://github.com/FTC-23206/automaducks-pond-starter){:target="_blank" rel="noopener"} repository for cloning or reference.
{: .notice--warning}

## Requirements

Since Pond does not take a dependency on FTC SDK types and versions, these are required steps for teams looking to leverage all the functionality in the library.

### Initialization

You need to initialize the *Command Scheduler* and the robot *Subsystem Hub* for making full use of the Pond functionality:

```java
public class MyOperation extends LinearOpMode {

    // Declare and initialize the member variables
    private final ILogger logger = new Logger(telemetry);
    private final SubsystemHub robotSubsystems = new SubsystemHub(logger);
    private final CommandScheduler commandScheduler = new CommandScheduler(logger);


    // Invoke the period methods on every iteration of the robot loop
    @Override
    public void runOpMode() throws InterruptedException {

        while (!isStopRequested()) {
            this.robotSubsystems.periodic();
            this.commandScheduler.periodic();

            this.logger.flush(false);

            // Allow other threads to process
            idle();
        }
    }
}

```

### Implement ILogger

Pond makes use of different logging and telemetry capabilities to better help you understanding your robot code:

* **Driving Hub Telemetry**: displays data on the driving hub.
* **Logs**: logs detailed information which can be seen in Adroid Studio LogCat
* **Graphs**: graphing of data such as PID parameters or autonomous movements

In order to enable these, Pond needs to be fed an implementation of ITelemetryLogger class. Here is a minimal implementation:

```java
/**
 * Copyright (c) 2025 Automaducks - FTC 23206
 * All rights reserved.
 */
package org.firstinspires.ftc.samplecode.integration;

import com.acmerobotics.dashboard.FtcDashboard;
import com.acmerobotics.dashboard.telemetry.TelemetryPacket;
import com.automaducks.pond.subsystems.HolonomicController;
import com.automaducks.pond.utility.LoggerBase;
import com.qualcomm.robotcore.util.RobotLog;

import org.firstinspires.ftc.robotcore.external.Telemetry;

/**
 * Multi target logger: logs to DriverHub, LogCat and FTC Dashboard Graphs.
 * <p>
 * IMPORTANT: This file is not expected to be changed by teams.
 */
public class Logger extends LoggerBase {

    private final Telemetry telemetry;
    private final FtcDashboard ftcDashboard = FtcDashboard.getInstance();
    private TelemetryPacket ftcDashboardPacket = new TelemetryPacket();

    /**
     * Constructs a new telemetry logger.
     * @param telemetry the FTC SDK Telemetry reference.
     */
    public Logger(Telemetry telemetry) {

        this.telemetry = telemetry;
        this.populateGraphKeys(HolonomicController.getGraphKeys());
    }

    @Override
    protected void displayDataInternal(String key, Object value) {
        telemetry.addData(key, value);
    }

    @Override
    protected void graphDataInternal(String key, Object value) {
        this.ftcDashboardPacket.put(key, value);
    }

    @Override
    protected void logDebugInternal(String tag, String format, Object... args) {
        RobotLog.dd(tag, format, args);
    }

    @Override
    protected void logInformationInternal(String tag, String format, Object... args) { RobotLog.ii(tag, format, args); }

    @Override
    protected void logExceptionInternal(String tag, Exception e, String format, Object... args) { RobotLog.ee(tag, e, format, args); }

    @Override
    protected void flushInternal() {
        // Send telemetry values
        this.ftcDashboard.sendTelemetryPacket(ftcDashboardPacket);
        this.telemetry.update();

        ftcDashboardPacket = new TelemetryPacket();
    }
}
```
