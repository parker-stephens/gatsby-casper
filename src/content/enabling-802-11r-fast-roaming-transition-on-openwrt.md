---
layout: post
title: Enabling 802.11r (Fast Roaming/Transition) on OpenWRT
image: img/callum-shaw-555357-unsplash.jpg
author: [Parker Stephens]
date: 2019-11-16T12:00:00.000Z
tags: []
draft: false
---

802.11r, also known as Fast Roaming/Transition on OpenWRT allows clients to seamlessly switch between several access points depending on which one is closer to the client. This is great for locations with more than one access point as a client does not have to drop a connection to rescan for APs, but can instead switch between, dropping minimal packets and remaining secure. In this guide, you will learn how to enable fast roaming on OpenWRT.

### Installing WPAD

wpad is a full-featured IEEE 802.1x authenticator/supplicant for OpenWRT. Your access point may already have it installed but in another format such as wpad-mini or wpad-basic. For fast roaming, wpad, the full package, is required.

#### SSH Into The AP

SSH into your access point. I will be using Windows’ default ssh client. You may be asked to access the RSA key of the client, and select yes. Enter in the password of the AP.

![](http://res-2.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/Screen-Shot-2019-11-13-at-1.03.37-PM-1024x542.png)

#### Removing Other Forms of wpad

First in the terminal, enter in:

`opkg update`  
This will update the repositories and lists of packages on the access points. Then enter:

`opkg install wpad`

If you receive the following message:

![](http://res-1.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/Screen-Shot-2019-11-13-at-1.05.49-PM-1024x62.png)

wpad is already installed and up-to-date

Then you do not need to do anything to continue. If you receive a message saying that the  __wpad__  package could not be installed due to another package, i.e.  __wpad-basic__  or  __wpad-mini__, then type:  
__opkg remove  ****interfering-package****__  
This should remove the interfering wpad packages.

#### Installing wpad

If there are no conflicting dependencies and packages, type in:

  
_`opkg install wpad`_

If you want WPA3 support, install wpad-openssl instead of wpad

`opkg install wpad-openssl`

### Configuring Fast Roaming

Now that wpad is installed we can configure fast roaming.

Login to the Luci web interface. Navigate to  __Network>Wireless__. Then choose the SSID you want to enable fast roaming on. Note that if you have more than one SSID or radio, then you will have to repeat these steps.

Select the SSID you want to enable fast roaming on, and go to the  __Wireless Security__ tab under  __Interface Configuration__. Select the checkbox to enable 802.11r.

![](http://res-3.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/Screen-Shot-2019-11-13-at-1.12.09-PM-1024x393.png)

You will be greeted with several fields.

![](http://res-3.cloudinary.com/parkercs/image/upload/q_auto/v1/blog-images/2022/05/Screen-Shot-2019-11-13-at-1.12.56-PM-1024x191.png)

For the mobility domain, enter in an 4-character hexadecimal ID. Here are examples

-   abcd
-   12ab
-   1234
-   1a2b
-   So on and so forth.

Once you have entered these settings, copy and paste the same mobility domain if you have multiple SSIDs.  ****If you have multiple access points****, you will keep the same mobility domain.

Congrats, you should now have fast roaming enabled on your access points!