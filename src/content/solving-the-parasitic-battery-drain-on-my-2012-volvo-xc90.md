---
layout: post
title: Solving the Parasitic Battery Drain On My 2012 Volvo XC90
author: [Parker Stephens]
date: 2020-01-18T12:00:00.000Z
tags: []
draft: false
---

Recently, I got a 2012 Volvo XC90. Within the first few weeks of driving it, I came out to it and the battery was dead. A quick jump start later, and I was fine. The battery didn’t die for several more weeks. Note that this was also with me driving almost every day.

When it happened the second time, I figured something was up. I started Googling and read about the RDAR module (Remote Digital Audio Receiver, provides Sirius XM for the car) would constantly look for a signal when the car was off if you did not have a Sirius subscription. Since I did not have a subscription, and it did not work on the radio either, I looked into ways to fix the issue.

## Ways to Fix the Issue

Some ways that included fixing it were to take it to a dealer and pay ~$150 to have Volvo do a software update and reload on the module. Another solution was to unplug the XM receiver and install a MOST loop on the receiver.

I chose to disconnect the receiver and install the MOST loop. In this car, MOST stands for the Media Oriented Systems Transport. It carries all the signals for the infotainment over plastic fiber optic cables, to various parts of the car. This includes a subwoofer in the back if equipped, the amplifier under the passenger seat, the RDAR module, the radio module in the roof headliner, and more. If you do not install the MOST loop and only disconnect the receiver, you will no longer have a working radio.

MOST Loop (P/N 8688083):  [https://www.volvopartswebstore.com/products/Volvo/Brace/1162068/8688083.html](https://www.volvopartswebstore.com/products/Volvo/Brace/1162068/8688083.html)

## Locating the Module

After finding the internal Volvo repair software online, I located everything I needed to replace and how to remove the parts.

[RDAR Module Removal](https://drive.google.com/uc?export=download&id=1hw8FEHER0D76pVuXS5ix1qtzypKWYou4)  
[Side Panel Removal](https://drive.google.com/uc?export=download&id=1k9uypGS4sZdfiVwnkuA4pBI_k3h6PvWr)

## Removing the Side Panel

In the side panel, there are a total of 4 screws to remove. There are two behind the two metal hooks. Remove those screws by prying on the plastic and jamming a socket wrench on the screw with an extension. The next screw is under a little plastic cover by the window vent. The last one is under a little plastic cover on the C pillar.

After removing the 4 screws, remove the upper trim pieces on the C and D pillar. These can be somewhat difficult to remove, but the clips are sturdy and will not likely break.

After these trim pieces are out, lift up and out on the side panel. It should loosen up. Wiggle it out, making sure it is not getting caught on anything like the seatbelt, trunk, etc.

## Disconnecting the RDAR (Sirius) Module

After the side trim is removed, the RDAR module is exposed. I found that it was easier to remove the module and to take out the wires/fiber optics then. When removing it, be careful not to bend the fiber optic cables to an extreme. Disconnect the power cable and the fiber optic cable. Connect the MOST loop to the end of the fiber optic cable. Do not reconnect the power cable, otherwise, you’ve wasted your entire time to connect back the faulty module.