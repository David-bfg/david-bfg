---
title: Photogrammetry Arch Support
author: david
date: 2021-06-01 00:00:00 -0500
categories: [DIY, 3D Printing]
tags: [cad, 3d printing, photogrammetry, frugal build]
render_with_liquid: false
image:
  path: /assets/img/Dense-Point-Cloud-Foot.PNG
  alt: Photogrammetry point cloud of the arches on my foot.
---

## Background info

I have feet with hight arches and one day I had some pain that if not related was exacerbated by a lack of arch support. Looking around online or in person it was hard to find something that you could feel confident would address the problem. At least while looking online I found interesting ideas; such as a podiatrist making 3D printed supports fitted specifically to each person and a similar DIY solution to 3D print your own insoles. I thought those were good ideas and I'd try to do similar by printing my own using photogrammetry.

## Photogrammetry process

I took an old sock and added a few markings onto it giving the bottom of my foot more texture. Photogrammetry takes the differences between multiple pictures to recreate a scene in space, so If it was all just pictures of a white sock there would be no context clues to infer the shape of my foot. Went and asked someone to help take a bunch of photos that could be used for the photogrammetry process.

Now I had data as a starting point I would need to find the right software. Unfortunately most apps that would do the photo stitching required CUDA support. At the time I did not have a system that supported that feature so I had to go find a photogrammetry application that was purely a CPU solution.

![Rough Point Cloud Of My Foot](/assets/img/Dense-Point-Cloud-Foot.PNG)
_A rough and inconsistent point cloud of my high arches_

Eventually I ran across an app that would work and finally had something to get started from. Unfortunately the surface was quite rough with a lot of inconsistencies instead of being smooth. For whatever reason be it not enough pictures or my non-technical photographer taking pictures the algorithm had trouble inferring from. I would make lemons out of lemonade (yes that’s reversed on purpose.) Knowing that increasing my sample size to feed the algorithm meant restarting from scratch since I cant magically put my foot in the exact same position, I chose to try and force the data to be usable.

## 3D printing custom insole

I had extracted the model and ran it through a couple of different apps to smooth it out and it finally got me rolling hills instead of rocky road. Proceeded with adding depth to the plane to make it around 4mm thick and took off any sharp edges in the process. Without having some scale to say what was the real world size of the new insole I went and eyeballed it then checked it on my first print. To my surprise it was spot on.

![Arch Support Good Fit](/assets/img/Arch-Support.jpg)
_Final fit for insoles with arch support_

## End result

Overall I printed around four or six to have one for each foot that I could use with 3 pairs of shoes. Everything was printed using PLA plastic. It was a good solution while it lasted, but long term the PLA eventually crumbled under the weight of a person. Later I found a reasonably priced arch support at a local department store that fit the bill.

Yet that 3d print did fit like a glove.
