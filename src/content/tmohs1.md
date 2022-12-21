---
layout: post
title: T-Mobile TMOHS1 Hotspot
image: img/callum-shaw-555357-unsplash.jpg
author: [Parker Stephens]
date: 2019-11-16T12:00:00.000Z
tags: []
draft: false
excerpt: A lovely little hotspot with an even lovelier backdoor.
---
## Modifying All Outgoing TTL

Essentially masks all outgoing data as "on-device" rather than hotspot data. This will apply on each boot automatically.

```text
echo "iptables -t mangle -I POSTROUTING -o rmnet_data+ -j TTL --ttl-set 64" > /etc/init.d/ttl.sh
echo "ip6tables -t mangle -I POSTROUTING -o rmnet_data+ -m hl ! --hl-eq 255 -j HL --hl-set 64" >> /etc/init.d/ttl.sh
chmod 755 /etc/init.d/ttl.sh
ln -s /etc/init.d/ttl.sh /etc/rc5.d/S98ttl

```

## Using Custom DNS Server(s)

Editing  `/etc/resolv.conf`  provides a temporary fix, but this file will be overwritten again on reboot, and even randomly while it's on. So let's make a script to defeat that!  
Create  `/etc/init.d/customdns.sh`  with the contents:

```text
#!/bin/sh

(while true
    do echo "nameserver 1.1.1.1" > /etc/resolv.conf
    sleep 15
done) &

```

This will make the script repeat every 15 seconds, ensuring that our DNS change sticks. You can replace 1.1.1.1 with whatever IPv4/6 server you want.

```text
chmod 755 /etc/init.d/customdns.sh
ln -s /etc/init.d/customdns.sh /etc/rc5.d/S98customdns

```

## Allowing More Devices On WiFi

By default, the hotspot allows a maximum of 10 devices on a WiFi network, but we can change this to whatever we want. Edit the  `max_num_sta`  line in  `/data/configs/hostapd.conf`  for the 2.4GHz network or  `/data/configs/hostapd-wlan1.conf`  for the 5GHz network. There is also a file in  `/data/wlan_cfg/max_num`, but this doesn't seem to do anything.

## Changing USB Mode

At a shell:  
ADB:  `/sbin/usb/compositions/9025`  
ADB + RNDIS + DIAG:  `/sbin/usb/compositions/902D`  
Default:  `/sbin/usb/compositions/9057`

I've had trouble identifying whether or not this device has a MDM9207 or a MDM9607 (X5) modem, as the internal FCC photos show a MDM9207 modem and QPST also says MDM9207, but my hotspot's software references the MDM9607 several times, and considering I'm able to hit speeds of 75/15 (QCOM says the MDM9207 is only capable of 10/5), I'm just gonna assume it has the MDM9607 modem.

Considering that T-Mobile is literally handing these out for free with 30GB of data, I hope that eventually someone will figure out how to SIM unlock these devices. It appears as though there haven't been any software updates after ~January 2021 (device is on software version TMOHS1_0.04.18, and I'm able to find Google results about that version from around those times, but hell, it's possible it's even older than that).

[](https://www.t-mobile.com/offers/free-trial)

Free 30-Day Trial with Test Drive® | App & Hotspot Device | T-Mobile

Try our network with a 30-day or 30GB free trial. Choose to Test Drive® with a mobile hotspot device or an iOS app and keep your phone, number, and apps.

![](https://www.t-mobile.com/favicon.ico)Try T-Mobile free for 30 days or 30GB of data and experience our expanded network without leaving your current carrier or losing your phone number.

![](https://cdn.tmobile.com/content/dam/t-mobile/assets/backgrounds/hero/general/358590-fade-bg-23.desktop.png)

[](https://fccid.io/2APXW-TMOHS1)

FCC ID 2APXW-TMOHS1 4G Mobile Broadband Router by Wingtech Group (Hong Kong) Limited

FCC ID application submitted by Wingtech Group (Hong Kong) Limited for 4G Mobile Broadband Router for FCC ID 2APXW-TMOHS1. Approved Frequencies, User Manuals, Photos, and Wireless Reports.

![](https://fccid.io/logo.png)FCC IDFCC ID

![](https://fccid.io/png.php?id=4987085&page=0)

ati: (what the hell? i guess it's a MDM9607??)

```text
Manufacturer: QUALCOMM INCORPORATED
Model: 4108
Revision: MPSS.JO.2.0.2.c1.7-00052-9607_GENNS_PACK-1  1  [Jul 17 2020 11:00:00]
SVN: 04
IMEI: ###############
+GCAP: +CGSM

OK

```

at+cgdcont=?

```text
+CGDCONT: (1-24),"IP",,,(0-2),(0-4),(0-1),(0-1)
+CGDCONT: (1-24),"PPP",,,(0-2),(0-4),(0-1),(0-1)
+CGDCONT: (1-24),"IPV6",,,(0-2),(0-4),(0-1),(0-1)
+CGDCONT: (1-24),"IPV4V6",,,(0-2),(0-4),(0-1),(0-1)

OK
```