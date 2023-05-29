---
title: Upgrade 3D Printer
author: david
date: 2021-01-01 00:00:00 -0500
categories: [resourcefulness, 3D Printing]
tags: [3d printing]
render_with_liquid: false
---

2021 upgraded printer:

  Fix Temp Sensor for good several attempts,

    diagnosing finding documentation on the circuit design

    replacing sensing resistor from 4.7k to 1k ohm using secondary temp definitions for a less common setup (a work around for some internal issues with what was likely an internal short in the controller board.)

    Similar issues persisted so I rewired things and modified the firmware to use the port for a second extruder. it seemed that the circuit for temp sensing was intact on the second extruder. This board initially was capable of being set up with 2 extruders for prints with dual colors.

    At some point during upgrades I had been planning or not relying on the sensors on the initial board.

    Converted to Klipper Firmware using octoprint controller running on raspberry pi. Tried connecting a second controller board using an arduino I had lying around thinking if the sensors are off but the power controls are good I could transfer the temp sensors to a seperate board that was working.
  Upgrade to all metal printing nozzles for ABS
  Upgrade to glass bed.
  Auto bed leveling sensor.