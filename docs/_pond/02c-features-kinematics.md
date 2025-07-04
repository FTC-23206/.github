---
title: "Kinematics"
permalink: /pond/features-kinematics
toc: true
---
Welcome to the kinematics page!

Using kinematics, we know the robot's coordinates and orientation on the field. Below is a diagram of field axes:

<img src="/images/pond/coordinate-system.jpg" width="600" style="display: block; margin: 0 auto;" />

## Dead Wheels Kinematics
### Overview
Provides an accurate **field-relative localization** using three unpowered encoded wheels (dead wheels) by comparing encoder delta's over time.

### Updates
#### Update
`update` takes the integer positions of the left, right, and center dead wheel.
#### Update and Reset
`updateAndReset` takes the integer positions of the dead wheels as well as a pose. 

### Getting Pose and Velocity
Simply returns the `currentPoseAndVelocity` in the form of a PoseAndVelocity.

## Linear Actuation

### Overview
The classes that deal with Linear Actuation convert **pulses** to **linear distance** (in millimeters) and vice versa, given the parameters of the gear-ratio's and wheel diameters. Deals with linear motion.

### Pulses/Linear Conversion

## Angular Actuatation

### Overview
The classes that deal with Angular Actuation convert **pulses** to **angular rotation** (in radians) and vice versa, given the parameters of the gear-ratio's. Deals with angular motion/rotation.

### Pulse/Angle Conversion
