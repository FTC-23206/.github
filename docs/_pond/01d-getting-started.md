---
title: "Getting Started"
permalink: /pond/getting-started
toc: true
---

## Introduction

This page you guide you step-by-step through the Pond starter classes, while explaining their basic functionality.

## Sample Operation Drive

> File: OperationDrive
{: .notice}

This class extends the *OperationBase* base class from the Pond integrations and defines 4 methods:

| Method     | When it Executes                            | What it Does                                                                                                                       |
| :--------- | :------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| onInit     | Called on Drive Hub operation `Init`        | Initializes robot components (no movement is allowed)                                                                              |
| onStart    | Called on Drive Hub operation `Start`       | Creates commands that allow for robot control                                                                              |
| onPeriodic | Called periodically after onStart completes | Allows for any custom recurring code to be executed. Please favor using commands or subsystems instead adding code in this method. |
| onStop     | Called on Drive Hub operation `Stop`        | Allows for custom code to be executed when the drive operation ends. |

### onInit

Creates a `HardwareMapAccessor` (See [Pond Integration]) and then add the robot subsystems. Subsystems are independent robot components.

```java
    @Override
    protected void onInit() {

        HardwareMapAccessor hardwareMapAccessor = new HardwareMapAccessor(hardwareMap);

        robotSubsystems.add(new DeadWheelsLocalizer(hardwareMapAccessor, logger));
        robotSubsystems.add(new ArmController(hardwareMapAccessor, logger));
        robotSubsystems.add(new MecanumDrive(hardwareMapAccessor, logger));
    }
```

Once the subsystems are added, they are ready to be operated. Since no movement is allowed until the Drive Operation starts, commands are only added on the `onStart` method.

### onStart

Creates the required commands to operate the robot during drive operation.

```java
    @Override
    protected void onStart(){

        // Setup driving commands.
        IDrivetrain drivetrain = robotSubsystems.findFirst(IDrivetrain.class);
        ArmController armController = robotSubsystems.findFirst(ArmController.class);

        // Chassis movement
        Command chassisMovement =
            Commands.dynamic()
                .when(CommandConditionBuilder::Always)
                .execute(() -> drivetrain.setPower(new Pose2D(-gamepad1.left_stick_y, -gamepad1.left_stick_x, -gamepad1.right_stick_x)))
                .build("JoystickCommand");

        commandScheduler.runPeriodically(chassisMovement);

        // Arm Movement
        Command armMovement =
            Commands.anyOf("ArmControl",
                Commands.dynamic()
                    .when(c -> c.buttonWasJustPressed(() -> gamepad1.a))
                    .execute(() -> armController.moveToAngle(Math.toRadians(90.0), 0.8))
                    .build("RaiseArm"),
                Commands.dynamic()
                    .when(c -> c.buttonWasJustPressed(() -> gamepad1.b))
                    .execute(() -> armController.moveToAngle(Math.toRadians(0), 0.4))
                    .build("LowerArm"));

        commandScheduler.runPeriodically(armMovement);
    }
```

At the beginning of the method, the required subsystems are retrieved from the robot, so they can be referenced by commands.

The `chassisMovement` command executes continuously, and at every cycle, it reads the joystick values and sets the power targets on the drive train accordingly. This is what controls robot movement on the field.

The `armMovement` command monitors buttons and when a button is pressed it executes the intended movement on the arm. In this case button `A` raises the robot arm and button `B` lowers the arm. Note that the commands are wrapped by an `anyOf` command, which will execute one command of the set at a time.

> Notice that there are no loops in this method! The commands that control the robot are created and they will continuously do their job until the Drive Operation end. Isn't that neat 😀
{: .notice--success}

### onPeriodic

This method is called on every processing cycle. If you are making good use of Subsystems and Commands, you should not have any code in this method.

### onStop

Any final code that you may want to run when the robot is stopped.

## Sample Configuration

> File: Configuration
{: .notice}

All robots need configurations values, and most likely, lots of them! While *Pond* does not require [FTC Dashboard], it's defined in a way to make it easy for the required configuration values to be compatible with its use.

If you are using [FTC Dashboard], once you deploy the code, connect your computer to the robot wifi and open <http://192.168.43.1:8080/dash> for dynamically changing the configuration without having to re-deploy your code.

```java
public class Configuration {

    /**
     * Chassis configurations.
     */
    @Config
    public static class Chassis {

        // Motors
        public static MotorParams MotorFrontLeft    = new MotorParams("front_left", MotorParams.Mode.RUN_WITHOUT_ENCODER, false);
        public static MotorParams MotorFrontRight   = new MotorParams("front_right", MotorParams.Mode.RUN_WITHOUT_ENCODER, true);
        public static MotorParams MotorRearLeft     = new MotorParams("rear_left", MotorParams.Mode.RUN_WITHOUT_ENCODER, false);
        public static MotorParams MotorRearRight    = new MotorParams("rear_right", MotorParams.Mode.RUN_WITHOUT_ENCODER, true);

        // Dead wheels parameters
        public static DeadWheelsKinematicsParams DeadWheels = new DeadWheelsKinematicsParams(
            63,
            196,
            2 * Math.PI * 18.688524,
            8192);                          // https://www.revrobotics.com/rev-11-1271/
    }

    /**
     * Arm configuration
     */
    @Config
    public static class Arm {

        public static AngularActuatorParams PivotActuation = new AngularActuatorParams(
            StandardGearRatios.GOBILDA_223_RPM,
            5.0);

        public static MotorParams MotorArm = new MotorParams("slide_pivot", MotorParams.Mode.RUN_TO_POSITION, true);
    }

    // ...
}
```

The `Configuration` class holds all robot parameters that may need to be adjusted (either because you are a new team using the repository, or because the the team is trying out different settings).

Each subsection is defined as a child `static` class. This also allows for each one of the sections to be published to [FTC Dashboard] through the `@Config` attribute (If you don't want to use FTC Dashboard, simply remove the `@Config` attributes).

The `Chassis` subsection holds all the configuration values for your drive train. It has configurations for each motor, including the hardware names configured in your robot through the Drive Hub. It has also parameters for the odometry through dead wheels.

> **IMPORTANT**: Make sure that the motor names match your robot hardware configuration! Also, pay attention the the *boolean* (true or false) values in the end of each configuration as they indicate whether or not the direction of the motor needs to be inverted.
{: .notice--warning}

## Sample Subsystems

### Mecanum Drive

> File: subsystems/MecanumDrive
{: .notice}

The constructor uses the hardware map accessor to get the motors, apply their configurations, and store their references within the class

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
```

MecanumDrive contains two other methods `setPower` and `periodic`, the `setPower` method stores the new target power and logs it:

```java
 @Override
    public void setPower(Pose2D power) {

        this.logger.logDebug(getLogTag(), "TargetPower=%s", power);

        this.targetPower.copyFrom(power);
    }
```

Once the target power is stored, the `periodic` method calculates the power for each wheel, and applies it in the robot's processing cycle:

```java
@Override
    public void periodic() {

        MecanumWheelKinematicsData<Double> wheelsData = MecanumInverseKinematics.calculate(this.targetPower);

        this.frontLeft.setPower(wheelsData.frontLeft);
        this.frontRight.setPower(wheelsData.frontRight);
        this.rearLeft.setPower(wheelsData.rearLeft);
        this.rearRight.setPower(wheelsData.rearRight);

        logger.logDebug(getLogTag(),"Mecanum Power: %s", wheelsData);
    }
```

This method makes use of the `MecanumInverseKinematics` class to calculate the power given to each wheel.

> Remember, **Pond** assists in writing high-performance robot code by making sure that all processing happens in the "periodic" method of Commands and Subsystem. For this to work correctly, you should never block the execution in your robot code (either by having infinite loops or calling *sleep* method).
{: .notice--warning}

### Dead Wheels Localizer

> File: subsystems/DeadWheelsLocalizer
{: .notice}


`DeadWheelLocalizer` is a sample subsystem intended on providing a localization of the robot based on the encoded dead wheels of a robot. It is also a real time subsystem since it implements the `ILocalizer` interface. This example relies on 3 dead wheels (left, right, and center), and makes use of the `DeadWheelsKinematics` class to get the positions of our dead wheels on the field.

The constructor, similar to the `MecanumDrive` subsystem, uses the `HardwareMapAccessor` to get the encoders of our dead wheels and set them to the member variables. It also uses the setMode method to reset the encoders. You can also see below how the new DeadWheelsKinematics instance uses the dead wheels defined in the configuration for the chassis.

```java
public class DeadWheelsLocalizer extends SubsystemBase implements ILocalizer {

    private final DeadWheelsKinematics deadWheelsKinematics;

    private final DcMotor deadWheelRight;
    private final DcMotor deadWheelLeft;
    private final DcMotor deadWheelCenter;

    public DeadWheelsLocalizer(HardwareMapAccessor hardwareMap, ITelemetryLogger logger) {
        super(logger);

        this.deadWheelsKinematics = new DeadWheelsKinematics(logger, Configuration.Chassis.DeadWheels);

        this.deadWheelLeft = hardwareMap.getMotor(Configuration.Chassis.MotorRearLeft);
        this.deadWheelRight = hardwareMap.getMotor(Configuration.Chassis.MotorRearRight);
        this.deadWheelCenter = hardwareMap.getMotor(Configuration.Chassis.MotorFrontRight);

        this.deadWheelLeft.setMode(DcMotor.RunMode.STOP_AND_RESET_ENCODER);
        this.deadWheelRight.setMode(DcMotor.RunMode.STOP_AND_RESET_ENCODER);
        this.deadWheelCenter.setMode(DcMotor.RunMode.STOP_AND_RESET_ENCODER);
    }
```
The methods within this class are `getLocalization`, `setLocalization`, and `periodic`. 

`getLocalization` uses the `DeadWheelsKinematics` class to return the robots current pose and velocity.

```java
    @Override
    public PoseAndVelocity getLocalization() {
        return this.deadWheelsKinematics.getPositionVelocity();
    }
```
`setLocalization` sets the position of the dead wheels to the current robot position and the velocity to zero using the `updateAndReset` method (see kinematics for more information). 

```java
    @Override
    public void setLocalization(Pose2D pose) {
        this.deadWheelsKinematics.updateAndReset(
            deadWheelLeft.getCurrentPosition(),
            deadWheelRight.getCurrentPosition(),
            deadWheelCenter.getCurrentPosition(),
            pose);
    }
```

`periodic` iterates in every robot processing cycle, it updates the position of the robot using the dead wheels current position. 

```java
    @Override
    public void periodic() {
        this.deadWheelsKinematics.update(
            -deadWheelLeft.getCurrentPosition(),
            -deadWheelRight.getCurrentPosition(),
            deadWheelCenter.getCurrentPosition());

        this.logger.displayData("Localization", getLocalization().pose);
    }
```

### Arm Controller

Coming soon!

## Sample Autonomous

Coming soon!

[Pond Integration]: </pond/integration> "Pond Integration"
[FTC Dashboard]: <https://acmerobotics.github.io/ftc-dashboard/> "FTC Dashboard"
