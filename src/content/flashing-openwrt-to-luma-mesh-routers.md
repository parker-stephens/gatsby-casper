---
layout: post
title: Flashing OpenWRT to Luma Mesh Routers
image: img/callum-shaw-555357-unsplash.jpg
author: [Parker Stephens]
date: 2020-10-14T12:00:00.000Z
tags: []
draft: false
---

Luma is one of those Mesh WiFi companies that didn't last very long. The last firmware pushed out for these access points was in January 2018. Over two years ago. Their website,  [https://lumahome.com](https://lumahome.com/), isn't even available anymore. However, the app still works, but don't expect to get any support.

![](https://ghost.parkercs.tech/content/images/2020/10/image.png)

3-pack of Luma Mesh System

So you may be asking yourself, what can I do to these access points to give them more customizability, make them more secure, or just have more control over them? These routers ran a customized version of OpenWRT out of the box, but recently OpenWRT has been ported over, and as of October 13, 2020, builds are in the snapshot state.

## What do you need?

A Luma Router (M/N WRTQ-329ACN)  
The install/initram image of OpenWRT (Can be found  [here](https://openwrt.org/toh/hwdata/luma/luma_wrtq-329acn))  
A USB to TTL adapter (Can be found on eBay for literally less than $5 with free shipping)  
Some Solderless Breadboard Jumper Wires  
A Computer  
A TFTP Server (for Windows you can use PumpKIN and macOS you can use TFTPServer)  
A little patience

## Getting Started

First, let's pop open the Luma. You can do this by placing a flathead screwdriver in the bottom slot of the Luma. A lot of force is required to pop it open, and you will most likely damage the plastic a little.

![](https://ghost.parkercs.tech/content/images/2020/10/IMG_20201013_215140.jpg)

![](https://ghost.parkercs.tech/content/images/2020/10/IMG_20201013_215211.jpg)

Now we can get a good look at the mainboard and the back plate, which even has a heatsink with heat pads on it!

![](https://ghost.parkercs.tech/content/images/2020/10/IMG_20201013_215245.jpg)

![](https://ghost.parkercs.tech/content/images/2020/10/IMG_20201013_215254.jpg)

Here's the area we need to focus on, the serial connector.

![](https://ghost.parkercs.tech/content/images/2020/10/IMG_20201013_215311.jpg)

Whatever you do,  **DO NOT CONNECT THE VCC CONNECTOR. YOU WILL MOST LIKELY FRY YOUR LUMA OR YOUR USB TTL ADAPTER. YOU HAVE BEEN WARNED.**

On my USB TTL Adapter, there are 4 pins. Red is VCC (REMEMBER, DO NOT CONNECT), black is ground, green is TX or transfer, and white is RX or receive.

![](https://ghost.parkercs.tech/content/images/2020/10/IMG_20201013_220050.jpg)

Let's go ahead and connect the two together. The jumper wire colors can be whatever you want, as long as you know the colors on the USB TTL Adapter and what they correspond to on the Luma.

![](https://ghost.parkercs.tech/content/images/2020/10/IMG_20201013_220338.jpg)

![](https://ghost.parkercs.tech/content/images/2020/10/IMG_20201013_220333.jpg)

Here you can see I used totally different jumper wire cables, but as long as you remember what each USB TTL Adapter wire is and what pin it goes to on the Luma, you are fine. Arguably, the most tedious part is done.

## Getting U-Boot Access

U-Boot is the bootloader on these devices, and we need to get access to it. However, Luma has disabled this out of the box, therefore, there are some extra steps needed to get access. For macOS, you can use the screen command with a baud rate of 115200 to get serial access to the device. In Windows, you can use a program such as Putty to get access, just be sure to specify the correct COM port Windows has assigned (can be checked in Device Manager) and use a baud rate of 115200. For example, in macOS I would use the command below. Note, sudo is not needed.

![](https://ghost.parkercs.tech/content/images/2020/10/Screen-Shot-2020-10-13-at-10.07.54-PM.png)

Let's go ahead and boot up the Luma. As you do, tons of messages should fill up the screen. If no characters appear, or garbled characters appear, check that your connections are good and that you have set the correct baud rate. We want to look for an option to enter failsafe mode.

![](https://ghost.parkercs.tech/content/images/2020/10/Screen-Shot-2020-10-13-at-10.13.19-PM.png)

Highlighted in red above is what you want to look out for. If you miss it, just reboot the Luma and try again. Once you see that, press "f" and then enter on your keyboard, and then you should see the splash screen. Once here we want to enter these commands:

`mount_root`

![](https://ghost.parkercs.tech/content/images/2020/10/Screen-Shot-2020-10-13-at-10.15.17-PM.png)

and:

`fw_setenv bootdelay 3`

![](https://ghost.parkercs.tech/content/images/2020/10/Screen-Shot-2020-10-13-at-10.15.57-PM.png)

This gives us a three second time delay to enter U-Boot on the device, which is plenty.

## Booting the Install Image

Once you've gained U-Boot access, let's reboot the device. You should see a prompt like below to stop the autoboot process. Just press any key on your keyboard to stop this. Once done, we are given a prompt.

![](https://ghost.parkercs.tech/content/images/2020/10/Screen-Shot-2020-10-13-at-10.17.17-PM.png)

Now to boot the image, we use the tftpboot command. At this point, make sure your ethernet cable is connected to your computer and the other end is plugged into the left port of the Luma (labeled as "out" on the backplate). If you've done this right, type  `tftpboot`  and press enter.

![](https://ghost.parkercs.tech/content/images/2020/10/Screen-Shot-2020-10-13-at-10.19.58-PM.png)

It should start attempting to load a TFTP file. Just press ctrl + c to exit this. Now this tells us we need to set our computer's static IP address to 192.168.11.168, and the install image name to  `C0A80B0E.img`. The file name can be different for each device, so copy the file name  **your**  Luma specifies.

![](https://ghost.parkercs.tech/content/images/2020/10/Screen-Shot-2020-10-13-at-10.22.18-PM.png)

Above shows the current directory I have selected for the TFTP server, with the proper install image renamed. Once you have your computer's static IP set and the proper directory set for your TFTP server, let's continue. At the prompt, enter:

`tftpboot 0x84000000`  
then:

`bootm 0x84000000`  
This will start loading the OpenWRT install image. Once it's done booting, you are now running a temporarily build of OpenWRT!

## Flashing OpenWRT Permanently

Well now that you've got the awesome OpenWRT booted, you want it installed on your Luma! The first step we need to do is connect an ethernet cable connected to your  **existing**  network, with an active internet connection and DHCP to the right ethernet port, or the "in" port according to the backplate. At the prompt type:

`opkg update`  
then:

`opkg install uboot-envtools`

This will install the necessary package to modify the U-Boot bootloader from OpenWRT. If it successfully installed, let's continue. Type:

`fw_setenv openwrt "setenv mtdids nand1=spi_nand; setenv mtdparts mtdparts=spi_nand:-(ubi); ubi part ubi; ubi read 0x84000000 kernel; bootm 0x84000000"`

`fw_setenv bootcmd "run openwrt"`

`ubirmvol /dev/ubi0 -N ubi_rootfs`.

Now for the final steps. Navigate to the /tmp/ directory on your device.

`cd /tmp/`

Now we want to grab the latest sysupgrade file from OpenWRT.

`wget https://downloads.openwrt.org/releases/21.02.2/targets/ipq40xx/generic/openwrt-21.02.2-ipq40xx-generic-luma_wrtq-329acn-squashfs-sysupgrade.bin`

NOTE: This link may be subject to change and is just the example I have provided. Make sure you grab the correct latest link for the sysupgrade.

Now let's type:

`sysupgrade -v -n openwrt-21.02.2-ipq40xx-generic-luma_wrtq-329acn-squashfs-sysupgrade.bin`

Substituting the file name I used for the file name you used. OpenWRT will start flashing and the device will reboot with a fresh config.

## Congrats!

You've just flashed OpenWRT on your Luma. You can go ahead and remove the jumper wires and place the backplate back on the Luma. The "out" port on the Luma is designated as the LAN and the "in" port is designated as the WAN. Note that with snapshot builds, the web GUI is not built in, and you will have to SSH in to install luci using opkg.

Also, the LED ring light will spin continuously as the custom Luma scripts are no longer present to control what they do. You can get around this as discussed at the bottom of the page  [here](https://git.openwrt.org/?p=openwrt/openwrt.git;a=commit;h=e24635710c7e6444afa463c59f3d81fe634eb3c7), and even do some fancy lighting changes.

Special thanks to everyone who made this possible, and especially the forum thread  [here](https://forum.openwrt.org/t/luma-home-wifi-support/50630). This would not be possible without them.

Sources:

[](https://forum.openwrt.org/t/luma-home-wifi-support/50630)

Luma Home WiFi support

Is it possible to get support for Luma Home WiFi?? Since the company abandoned the product and I haven’t seen any firmware updates for nearly two years..... I have three of these units. Descent hardware specs http://en.techinfodepot.shoutwiki.com/wiki/Luma_WRTQ-329ACN Am willing to open one up a…

![](https://forum.openwrt.org/uploads/default/optimized/2X/d/db0b03f66dd0b341e2ada2aac4957a6f4c206955_2_180x180.png)OpenWrt Forumjpstoppa

![](https://forum.openwrt.org/uploads/default/original/2X/1/1f939c64b4d285b298dd1428f5d948ef09688424.png)

[](https://git.openwrt.org/?p=openwrt/openwrt.git;a=commit;h=e24635710c7e6444afa463c59f3d81fe634eb3c7)

git.openwrt.org Git - openwrt/openwrt.git/commit

![](https://git.openwrt.org/static/git-favicon.png)openwrt/openwrt.git/commit