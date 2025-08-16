---
title: "Conventions"
permalink: /pond/conventions
toc: true
---
## Field Orientation

The pond library assumes the standard [FIRST FTC field orientation](https://ftc-docs.firstinspires.org/en/latest/game_specific_resources/field_coordinate_system/field-coordinate-system.html#square-field){:target="_blank" rel="noopener"}:

<img src="/images/pond/pond-ftc-field-orientation.png" style="display: block; margin: 0 auto;" alt="Shows field orientation from FTC." />

> Please pay attention to field orientation as errors due to misorientation might be subtle and hard to diagnose.
{: .notice--warning}

## Robot Orientation

### Definition
Robot-centered orientation is where the movement of the robot is based on the robot’s own perspective. For instance, if the joystick is pushed forward, the robot moves forward based on its front no matter how it's oriented.

## Units

| Measurement | Unit    | Comment                                                                                     |
| ----------- | ------- | ------------------------------------------------------------------------------------------- |
| Distance    | (Any)   | Be consistent across code                                                                   |
| Angle       | Radians | Unless otherwise denoted                                                                    |
| Time        | Seconds | When possible, use the Duration class to correctly represent time with nanosecond precision |

> Per above, if distance is used in Inches, Speed will be represented in Inches/Second.
{: .notice--info}
