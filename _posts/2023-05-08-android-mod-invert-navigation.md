---
title: Android Mod, Invert Navigation
author: david
date: 2023-05-08 00:00:00 -0500
categories: [Problem Solving, Android]
tags: [android, reverse engineering, lineageos]
render_with_liquid: false
image:
  path: /assets/img/OneNav-Option.png
  alt: Moto OneNav Option.
---

## Android long term support is DIY (preamble)

My phone is Android based, it was first released on June 2017. I got it in spring 2018 and planned to hold onto it for a long time.

Unfortunately as with all Android phones the manufacturer EOL's any software support for it relatively soon. I've checked LineageOS every now and then to see if there is a newer release than the stock android ROM. Recently the phone was pulled out from discontinued support by marcost22 jumping past 2 or 3 versions of Android.

## The problem: Inverted nav. buttons

I proceeded to try the OS on my spare development phone. (Purchased a spare for cheap once the phone became fairly old.) Realized there was no way to invert the navigation gestures on the fingerprint sensor. I’d had years of muscle memory I was not about to unlearn. So I posted on the support forum [XDA Forum Post](https://forum.xda-developers.com/t/official-lineageos-18-1-for-the-moto-z2-play.4456633/page-5#post-88512093).

## Look to fix it myself

[TLDR;](/posts/android-mod-invert-navigation/#the-direct-fix)

I prepared a local build environment. Tried looking through things to get a sense of what might control the direction of the navigation buttons. The Android OS project is quite massive and pulls from many different code repositories. Even after building the project locally there was a lot of tracking things down and finding references going to different sections. It was large enough to know I’d need help from the maintainer marcost22 to point me in the right direction.

### Getting some help from the maintainer

He pointed me to some code for a toggle to turn the hardware navigation buttons on and another toggle for swapping the navigation buttons. Along with some other projects that implemented the navigation swapping. Unfortunately the implementations on other devices were hardware specific with some predefined path to set this functionality. Trying similar implementations did not work on my device.

There was an existing implementation to turn the hardware navigation on and off that interfaced with a package called IFingerprintNavigation. The existing implementation only had two functions defined for the interface. Figuring there were more than just two functions I extracted a list of all the available functions from the shared library file. The functions for get and setNavigationConfig jumped out at me as a good place to start digging deeper. The NavigationConfig functions passed a config object back and forth that I would need the definition for.

### Digging deeper (Research)

I would go digging through
 - LineageOS commits
 - posts about swapping navigation buttons
 - an open-source app that swapped nav buttons
 - the stock ROM to see the initial config
 - what the Moto app did to invert navigation

In the lineage commits there was another [implementation of IFingerprintNavigation](https://github.com/LineageOS/android_device_motorola_nash/commit/ee0f70af6c341aa04c8aaa28e7e7828ceaad0c67) on the big brother version of my phone. Unfortunately I was about five years too late to get any useful information from the author but it looked like the interface was written in Java. Java has stronger type definitions than the C language does, so I might be able to find a definition for the NavigationConfig object I had been looking at.

I extracted the ROM from my stock android phone to search for any missing links. I found a java wrapper for the IFingerprintNavigation library. Using a Java de-compiler tool I was able to find a definition file for the  config object being passed to the get and setNavigationConfig. This is where I realized that the IFingerprintNavigation library likely came from the fingerprint sensor manufacturer not the smartphone manufacturer. The config object was full of timings for how responsive the sensor is to touch. Makes sense once you realize the separation of concerns; the firmware would do things like turn on and off, swipe left, right, up, or down. The OS will pick it up from there and trigger whatever you want the button to do.

### Understanding the stock implementation

![OneNav Config Page](/assets/img/OneNav-Config.png){: width="280" .right}
As I went through the stock ROM looking at the nav button configuration files and the Smali code (Java assembly code) for the app that was inverting the navigation it became apperant that this was a pure software solution. Thus this comment for the Lineage solution “Opt to not use Moto's OneNav solution and instead just listen for keycodes without a middleman listener.”
```
    -  key 621   SYSTEM_NAVIGATION_RIGHT 
    -  key 620   SYSTEM_NAVIGATION_LEFT
    +  key 620   BACK          VIRTUAL
    +  key 621   APP_SWITCH    VIRTUAL
```
The stock ROM had the left and right fingerprint swipes triggering generic navigation controls. Then somewhere in software the Moto OneNav solution picks up those events and triggers the appropriate action. Now because the software is triggering the nav buttons it can easily swap the actions without modifying directly what the keys do. That setting lives on the read only partition and is not intended to be modified.

## The Direct Fix

I found [this post](https://forum.xda-developers.com/t/swapping-back-and-recent-apps-buttons-in-lineageos-14-1.3630450/) to be the best solution for my needs. It provides a detailed guide on how to modify the read-only file system using TWRP in order to swap button actions. Any alternative would require another app running in the background. Fortunately, with the built-in gesture navigation on newer Android phones you can toggle inverted controlls in the settings. For my older device I’m happy to make a few file edits instead of running another piece of software.

## Resource Links

1. [Github fork with code attempts.](https://github.com/LineageOS/android_device_motorola_albus/compare/lineage-18.1...David-bfg:android_device_motorola_albus:lineage-18.1)
2. Forum thread talking to LineageOS device maintainer. [XDA Forum Post - Official LineageOS 18.1 Moto Z2 Play](https://forum.xda-developers.com/t/official-lineageos-18-1-for-the-moto-z2-play.4456633/page-5#post-88512093)
3. Finger print navigation code implemented on the more premium version of this phone. [implementation of IFingerprintNavigation](https://github.com/LineageOS/android_device_motorola_nash/commit/ee0f70af6c341aa04c8aaa28e7e7828ceaad0c67)
4. Final fix. [XDA Form Post - Swapping back and recent apps](https://forum.xda-developers.com/t/swapping-back-and-recent-apps-buttons-in-lineageos-14-1.3630450/)
