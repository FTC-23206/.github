---
title: "Subsystems"
permalink: /pond/features-subsystems
toc: true
---

## Introduction

Subsystems are classes that are responsible for controlling a specific aspect of your robot hardware: drivetrain, actuators, sensors, etc.

A subsystem can be:

| Type          | Description                                                                                                                 |
| :------------ | :-------------------------------------------------------------------------------------------------------------------------- |
| **Regular**   | A subsystem that only takes commands. Typically, these are servo only controlled subsystems                                 |
| **Real-Time** | A subsystem that besides taking commands, periodically evaluates and acts on sensor reading (encoders, light sensors, etc.) |

The **Pond** library doesn't define many out-of-the-box subsystems, but rather provides your with the base classes and interfaces so you can write your own.

## Library Classes

{% raw %}
<div class="mermaid">
classDiagram
    direction RL

    class ISubsystem

    class IRealTimeSubsystem {
        +periodic()
    }

    class SubsystemBase {
        #getLogTag()
    }

    class ISubsystemHub {
        +add(ISubsystem subsystem)
        +remove(ISubsystem subsystem)
        +findFirst(ISubsystem subsystem) ISubsystem
    }

    class SubsystemHub

    class IDriveTrain {
        +setPower(Pose2D power)
    }

    class ILocalizer {
        +getLocalization() PoseAndVelocity
        +setLocalization(Pose2D)
        +reacquire()
    }

    class IHolonomicController {
        +calculate(PoseAndVelocity currentPoseVelocity, PoseAndVelocity targetPoseVelocity) Pose2D
    }

    class HolonomicController

    ISubsystemHub ..|> IRealTimeSubsystem
    IRealTimeSubsystem ..|> ISubsystem
    SubsystemBase  --|> ISubsystem
    SubsystemHub ..|> ISubsystemHub
    SubsystemHub --|> SubsystemBase

    IDriveTrain ..|> IRealTimeSubsystem
    ILocalizer ..|> IRealTimeSubsystem
    IHolonomicController ..|> ISubsystem
    HolonomicController ..|> IHolonomicController
</div>
{% endraw %}

## Base Classes and Interfaces

### ISubsystem

Interface that should be implemented by any subsystem on your robot. It does not define any method but rather identifies the extending class as a Subsystem.

```java
public interface
ISubsystem {

}
```

### Subsystem Base

Convenience base class for implementing your own subsystems. It maintains a reference to ITelemetryLogger which allows for your code to log or display telemetry on the Driver Hub.

### IRealTime Subsystem

Interface that the should be implemented by your robot subsystems that need to evaluate or act on the robot in real time. This could be reading sensor, acting on motors or simply logging parameters.

```java
public interface IRealTimeSubsystem extends ISubsystem {

    /**
     * Performs periodic processing.
     */
    void periodic();
}
```

You can add your 'evaluate and act' code on your implementation of the  `periodic` method.

> Caution: do not take time on this method! This means your should not have (long lasting) loops or calls to sleep methods. Delaying the execution on periodic methods delay the execution of every other aspect of the robot (other subsystems, commands, etc.)
{: .notice--danger}

### ISubsystemHub

A SubsystemHub stores a list of subsystems. Having a Subsystem Hub makes it easier running periodic on real time Subsystems and allows the access to multiple subsystems within a command. Think of your robot as your main subsystem hub.

```java
public interface ISubsystemHub extends IRealTimeSubsystem {

    /**
     * Add a new subsystem to the hub.
     * @param subsystem subsystem to be added.
     */
    void add(ISubsystem subsystem);

    /**
     * Find the first available subsystem by its class.
     * @param subsystemClass the subsystem class.
     * @return the first available subsystem that matches the class.
     * @throws ArrayIndexOutOfBoundsException if no subsystem is found.
     */
    <T extends ISubsystem> T findFirst(Class<T> subsystemClass);

    /**
     * Removes an subsystem to the hub.
     * @param subsystem subsystem to be removed.
     * @noinspection unused
     */
    void remove(ISubsystem subsystem);
}
```

`ISubsystemHub` : extends `IRealTimeSubsystem`. Contains the functions `add`, `findFirst`, and `remove`. The `add` function adds subsystems to the hub, the `findFirst` function returns the first subsystem in the hub that matches the given class type (throws an exception if none is found), and the `remove` function simply removes the subsystem from the hub.

### SubsystemHub

Default implementation for ISubsystemHub. It automatically calls `periodic` on every subsystem in the collection.

### IDrivetrain

Subsystem interface for Drive Train implementation (Mecanum, Differential, etc.). 

`IDrivetrain` : extends `IRealTimeSubsystem` to provide real-time power updates to the drivetrain using the `periodic` function.

```java
public interface IDrivetrain extends IRealTimeSubsystem {

    /**
     * Sets the instantaneous motor powers.
     *
     * @param power desired direction and power of movement.
     */
    void setPower(Pose2D power);
}
```

### ILocalizer

Subsystem interface for robot localization implementations (Dead Wheel, Mecanum Kinematics, etc.)

```java
public interface ILocalizer extends IRealTimeSubsystem {

    /**
     * Gets current localization.
     * @return current robot localization.
     */
    PoseAndVelocity getLocalization();

    /**
     * Sets the current localization.
     * @param pose Current localization.
     */
    void setLocalization(Pose2D pose);

    /**
     * Reacquires the Pose with an external source such as AprilTags, IMU, etc.
     * @return true if reset is successful, false otherwise.
     */
    boolean reacquire();
}
```

### IHolonomicController & HolonomicController

When used in the autonomous operations, adjusts robot power or speed given a target localization and speed on a timed trajectory. It calculates the error in position and speed, and applies the gains and robot-specific params to return the desired robot movement.

`IHolonomicController` : provides a method to calculate the desired actuation of a holonomic drivetrain.

`HolonomicController` : completes the calculations for actuation (also applies gains). Initializes the controller with params and logging utilities.

`HolonomicControllerParams` : holds the adjustable params within instsances of the Holonomic controller. These can be tuned in FTC Dashboard *or* in Configuration to your specific holonomic drivetrain. Speed gains are the factors that multiply the delta between the target speed and the current speed, and position gains are the factors that multiply the delta between the target position and current position.

## Writing Your First Subsystem

When writing your first subsystem, first decided if the subsytem only blindly performs actions (such as setting a servo position) or whether it needs to perform periodic tasks (such as evaluating encoders, calculating PIDs, reading sensors or even logging). This will determine whether to implement ISubsystem or IRealTimeSubsystem.

Second, for a subsytems to be used, it most likely needs to be added to a SubsystemHub.

### Example: Mecanum Drive Subsystem

```java
public class MecanumDrive extends SubsystemBase implements IDrivetrain {

    private final DcMotor frontLeft;
    private final DcMotor rearLeft;
    private final DcMotor frontRight;
    private final DcMotor rearRight;

private final Pose2D targetPower = new Pose2D();

    public MecanumDrive(HardwareMapAccessor hardwareMap, ITelemetryLogger logger) {

        super(logger);

        this.frontLeft = hardwareMap.getAndConfigureMotor(Configuration.Chassis.MotorFrontLeft);
        this.frontRight = hardwareMap.getAndConfigureMotor(Configuration.Chassis.MotorFrontRight);
        this.rearLeft = hardwareMap.getAndConfigureMotor(Configuration.Chassis.MotorRearLeft);
        this.rearRight = hardwareMap.getAndConfigureMotor(Configuration.Chassis.MotorRearRight);
    }

    @Override
    public void setPower(Pose2D power) {

        this.logger.logDebug(getLogTag(), "TargetPower=%s", power);

        this.targetPower.copyFrom(power);
    }

    @Override
    public void periodic() {

        MecanumWheelKinematicsData<Double> wheelsData = MecanumInverseKinematics.calculate(this.targetPower);

        this.frontLeft.setPower(wheelsData.frontLeft);
        this.frontRight.setPower(wheelsData.frontRight);
        this.rearLeft.setPower(wheelsData.rearLeft);
        this.rearRight.setPower(wheelsData.rearRight);

        logger.logDebug(getLogTag(),"Mecanum Power: %s", wheelsData);
    }
}
```
