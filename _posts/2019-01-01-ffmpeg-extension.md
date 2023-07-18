---
title: FFmpeg Extension
author: david
date: 2019-10-15 00:00:00 -0500
categories: [resourcefulness, 3D Printing]
tags: [3d printing]
render_with_liquid: false
---

## Background info

I’m a developer who has used Linux machines in my normal day to day. For years I have dual booted Windows and Linux in the same system. That was until Windows 10 came around and Microsoft did so much to treat their users like they were someone who did not pay for their product. No, at this time the user was transferring into someone who could buy their cloud services or have an advertising profile to show start menu ads to.

At some point I was introduced to a strategy of relegating windows to it’s own VM and passing off a secondary graphics card to it so you could still play games. Then I could be unencumbered by the annoyances of windows for doing work and still be able to relax with games at the end of the day.

Many other developer types had adopted such systems and someone made a nifty tool called Looking Glass. It allowed you to mirror the display of the VM to the host system. Why is this important? Usually in a VM the image is displayed on the host by emulating a simple generic graphics card. In our setup we give the VM a dedicated graphics card so there is no display available to the host without setting up something like Looking Glass. Thus Looking Glass allows you to control the VM in a window on the host if you use a dedicated graphics card.

## Coded FFmpeg extension to record from the VM’s desktop

There were a few games I was playing, that were great with a controller. I’d say they were couch games. I wanted to get an easy setup to play them using something like Steam’s in home streaming. My graphics card didn’t do a great job of encoding video but I thought hey there’s a second card in my computer. I could probably find a way to offload the video encoding to it using Looking Glass.

I created an extension for FFmpeg by first looking at their code for the Linux frame buffer device and how it differed from the frame buffer made by Looking Glass. For the buffer itself it was simple enough, you know first byte red, second green and third blue, repeated for each pixel. I also had to go through the Looking Glass code to see how it was declaring where the frame data started and how it was signaling to generate a new frame.

Eventually I got everything going and while I could say it worked, it wasn’t really better than the Steam in home streaming. I was encoding and could stream to other systems but the latency from encode to showing up on another screen was around half a second. Steam’s in home streaming showed a delay closer to 60 or 100 milliseconds. That’s a really big difference in input delay between what I cobbled together and the integrated solution available with Steam.

## End result

I asked around on a forum where I'd first heard of Looking Glass if there was much interest in the extension. Got some response. Unfortunately it wasn't gonna be able to do more than be used as a screen recorder and it looked like there was already an OBS plugin in development that would serve the same purpose.

At the end of the day I decided against doing the extra work to try and get my extension submitted into FFmpeg.