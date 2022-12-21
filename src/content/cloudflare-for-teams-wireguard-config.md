---
layout: post
title: Cloudflare for Teams Wireguard Config
image: img/callum-shaw-555357-unsplash.jpg
author: [Parker Stephens]
date: 2021-07-23T12:00:00.000Z
tags: []
draft: false
---

### Warning

I'm going to go ahead and say this isn't a task for the weak and feeble. This isn't a method supported by Cloudflare, and could be against their TOS for all I know, thus making this process not straightforward.

### Summary

This process involved installing the Android SDK on Windows with the AVD manager, using some command line knowledge, and knowing how to setup a Wireguard tunnel.

We will make a device running Lollipop, as this allows us to extract the Wireguard configuration from the device, as in newer versions of Android this is locked down in hardware.

## The Process

First, let's go ahead and install the latest Android SDK from  [here](https://developer.android.com/studio). In the install process, make sure AVD Manager is checked and will be installed on the hard drive. We also want Android platform-tools, as this includes ADB which we will need later on. Download that from  [here](https://developer.android.com/studio/releases/platform-tools)  and just extract it into a folder.

### Creating our Android Device

Go ahead and launch Android Studio, and select AVD manager under the configure dropdown.

![](http://res-2.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-4.png)

A new windows will appear. Go ahead and select "Create Virtual Device."

![](http://res-2.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-6.png)

Select Tablet in the Category pane, and Nexus 7 (not 2012!) in the Device pane.

![](http://res-5.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-7.png)

In the "Select a system image" window, select Lollipop and select the download button next to it. Click accept on the terms, and wait for it to download. Select Lollipop and click next.

![](http://res-3.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-8.png)

We don't need to change anything on this next window, so go ahead and select "Finish."

![](http://res-3.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-9.png)

Select the play button inside the device you just created.

![](http://res-5.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-10.png)

If all went well, you should now be greeted with the Android homescreen!

### Installing 1.1.1.1 on the Android Device

![](http://res-1.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-11.png)

Now on your computer, open your web browser and head to APKMirror  [here](https://www.apkmirror.com/apk/cloudflare/1-1-1-1-faster-safer-internet/)  to download the latest APK of 1.1.1.1. Note that we want the APK, not a bundle. Once your apk is downloaded, place it in the folder you extracted platform-tools to. Your folder should look like something below.

![](http://res-2.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-12.png)

Go ahead and shift+right-click in the folder, and select "Open Powershell window here" or "Open Command Prompt windows here," depending on what version on Windows you have, or whatever your preference is. Type  `adb.exe devices`. This should list your emulator as a device.

![](http://res-2.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-13.png)

Now we want to install 1.1.1.1 onto the Android device. Type  `adb.exe install "apk name here"`.

![](http://res-5.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-14.png)

And like magic, 1.1.1.1 should show up in the app drawer now!

![](http://res-2.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-15.png)

### Logging into Cloudflare for Teams on the Device

If you have set up Cloudflare for Teams on any other mobile device, the process is the exact same here. Click the hamburger, "Account," "Login with Cloudflare for Teams."

![](http://res-5.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-16.png)

![](http://res-4.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-17.png)

![](http://res-3.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-18.png)

Go ahead and enter your organization name, and proceed with whatever login method is set up.

![](http://res-4.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-19.png)

And just like that Teams is set up on the device. Now it's time to extract the Wireguard configuration.

![](http://res-3.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/image-20.png)

### Pulling the Wireguard Configuration

Go back into Powershell/Command Prompt, and type  `adb pull /data/data/com.cloudflare.onedotonedotonedotone/shared_prefs/com.cloudflare.onedotonedotonedotone_preferences.xml`. This will place the configuration in the platform-tools folder. Go ahead and open it with your favorite editor, VS Code in my case.

### Tunnel Info

Within our XML file, we need to make note of a couple items.

-   Peer Public Key
-   Peer IP Address
-   Peer Port
-   Client IP/s
-   Client Private Key
-   Client Registration ID

You might have to dig around in the XML to find these, but in my case here are my values (don't worry, it won't work after).

Endpoint Public Key:  `bmXOC+F1FxEMF9dyiK2H5/1SUtzH0JuVo51h2wPfgyo=`  
Endpoint IP Address:  `162.159.193.10`  and  `[2606:4700:100::a29f:c10a]`  
Peer Port:  `2408`  (Note, if this is blocked by a firewall, port 500, 1701, and 4500 are also valid.)  
Client IP:  `172.16.0.2`  and  `fd01:5ca1:ab1e:8f0c:9fb5:4cdd:ea7e:f0d3`  
Client Private Key:  `Q28IK1ygu1MT4wumEoBf3Gl6+awKgyS3X+ioEi2HGnE=`  (I have revoked this, it will not work for you.)  
Client Registration ID:  `t.f634e942-ebe5-11eb-9b1d-a6b0c2bd8c0a`  (This is used to create Identity based-policies and other things in the Cloudflare for Teams dashboard. Note, the "t." part won't be present in the dashboard.)

### All done!

That's it! Now you can use that in pfSense to treat your whole network as one device in the dashboard, use it on a device that doesn't support the 1.1.1.1 app but supports Wireguard, or anything else you put your mind to.

Keep in mind, some online service will recognize the Warp IP as a VPN. Specifically Hulu (but not Netflix?), Wikipedia, and others. You can try using split tunneling, but this will have to be configured on the local device, not the Cloudflare for Teams dashboard.