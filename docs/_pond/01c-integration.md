---
title: "Integration"
permalink: /pond/integration
toc: true
---

> **Wacth Out!:** You can **skip this page if you are using the 'Quick-Start Repository' setup method** as the integration requirements are already taken care for you.
{: .notice--warning}

## Requirements

Since Pond does not take a dependency on FTC SDK types and versions, these are required steps for teams looking to leverage all the functionality in the library.

### Initialization

You need to initialize the *Command Scheduler* and the robot *Subsystem Hub* for making full use of the Pond functionality:

```java
public class MyOperation extends LinearOpMode {

    // Declare and initialize the member variables
    private final ITelemetryLogger logger = new TelemetryLogger(telemetry);
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

### Implement ITelemetryLogger

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

import com.automaducks.pond.utility.ITelemetryLogger;
import com.automaducks.pond.utility.TelemetryLoggerBase;
import com.qualcomm.robotcore.util.RobotLog;

import org.firstinspires.ftc.robotcore.external.Telemetry;

/**
 * Multi target logger: logs to DriverHub, LogCat and FTC Dashboard Graphs.
 */
public class TelemetryLogger extends TelemetryLoggerBase implements ITelemetryLogger {

    private final Telemetry telemetry;

    /**
     * Constructs a new telemetry logger.
     * @param telemetry the FTC SDK Telemetry reference.
     */
    public TelemetryLogger(Telemetry telemetry) {

        this.telemetry = telemetry;
    }

    /**
     * Displays data on the driving hub.
     */
    @Override
    protected void displayDataInternal(String key, Object value) {
        telemetry.addData(key, value);
    }

    /**
     * Graphs data on the FTC dashboard (leave it blank if you are not using a dashboard.
     */
    @Override
    protected void graphDataInternal(String key, Object value) {
        // Optional: add FTC dashboard
    }

    /**
     * Logs debug text to Logcat.
     */
    @Override
    protected void logDebugInternal(String tag, String format, Object... args) {
        RobotLog.dd(tag, format, args);
    }

    /**
     * Logs information text to Logcat.
     */
    @Override
    protected void logInformationInternal(String tag, String format, Object... args) {
        RobotLog.ii(tag, format, args);
    }

    /**
     * Logs error text to Logcat.
     */
    @Override
    protected void logExceptionInternal(String tag, Exception e, String format, Object... args) {
        RobotLog.ee(tag, e, format, args);
    }

    /**
     * Writes all the data to the intended destinations.
     */
    @Override
    protected void flushInternal() {
        // Send telemetry values
        this.telemetry.update();
    }
}
```

## Advance Usage

See Pond Quick-Start [Team Code](https://github.com/FTC-23206/automaducks-pond-starter/tree/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode){:target="_blank" rel="noopener"} for a more advanced example.
