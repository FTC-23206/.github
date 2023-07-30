---
layout              : page
show_meta           : false
title               : "Odometry"
subheadline         : "Odometry Module"
header:
  title: Automaducks
  image_fullwidth: "header_odometry.png"
permalink           : "/odometry/"
---
## Background

Robot autonomous operation can be improved with odometry from dead wheels, given that they are less susceptible from slippage during acceleration and breaking. Dead wheels require an encoder and an omni wheel to be assembled is a way that they can be added to the robot chassis.

The goal is to have the whole assembly to fit inside a standard Gobilda U-Channel. The team looked into [Open Odometry](https://openodometry.weebly.com/) but the assembly required too many parts and seemed unnecessarily complicated. The team wanted something simpler, thus the conclusion was to design our own.

## Bill of Materials

| Item                           | Quantity | Link                                                 |
|--------------------------------|----------|------------------------------------------------------|
| REV Through Bore Encoder       | 1        | https://www.revrobotics.com/rev-11-1271/             |
| 38mm Omni Wheel                | 1        | https://www.aliexpress.us/item/2251832788059198.html |
| M3x18mm socket head hex screw  | 1        | (Comes with the Wheel)                               |
| M4x10mm button head hex screws | 2        |                                                      |
| M4x35mm socket head hex screw  | 1        |                                                      |
| GoBilda 43mm standoff          | 1        | [Link][GoBilda-Standoff-43]                          |
| Neiko Spring 23/64’’ x 1-3.8’’ | 1        |                                                      |
| Printed: Hexagonal Axel        | 1        | (Link to STL)                                        |
| Printed: Wheel Housing         | 1        | (Link to STL)                                        |


[GoBilda-Standoff-43]: https://www.gobilda.com/1501-series-m4-x-0-7mm-standoff-6mm-od-43mm-length-4-pack/