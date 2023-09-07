---
title: Arduino, Auto Fire
author: david
date: 2018-09-30 00:00:00 -0500
categories: [Problem Solving, Arduino]
tags: [arduino, gaming, usability, circuits]
render_with_liquid: false
image:
  path: /assets/img/Auto-Fire-Arduino.jpg
  alt: Arduino With Button.
---

I played a game that often required you to mash a button for like 30-60 seconds. I got tired of doing it and bought an Arduino to act like a keyboard and mash a key instead of damaging my wrist. Worked off of the example code and wiring diagram for creating a button event. Edited the code so whenever you were holding down the button the arduino would be doing a button mash for you.

I wanted to avoid being flagged as a bot for what was just a usability hack. I recorded real world timings between key presses to give a realistic input. That should get past anything trying to detect inhuman reaction timings. I stored a list of like 60 real world timings and randomly pick one wait time between each press. That way it would not just repeat inputs like a macro and still give a normal distribution bell curve if someone were to look at it.
