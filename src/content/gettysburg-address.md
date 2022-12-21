---
title: 'Secure DNS On A Linux-Based Hotspot'
author: 'Parker Stephens'
tags: []
image: img/demo1.jpg
date: '1863-11-19T10:00:00.000Z'
draft: false
---

In my previous posts, I talked about the Netgear MR1100 and T-Mobile TMOHS1 hotspots. In them, I briefly spoke about mods you can perform on those devices, including modifying the TTL and changing the DNS servers. But I wanted a little more security on the DNS side, especially because we know that carriers perform deep packet inspection and God knows what else on their networks.

Originally I planned to use  [cloudflared](https://github.com/Cloudflare/cloudflared)  in its DNS mode, however, the binary file was much too big (~26MB) for the relatively small file systems on these devices and did not work half the time. I gave up, but then I remembered  [dnscrypt-proxy](https://github.com/DNSCrypt/dnscrypt-proxy). And luckily for us, its binary file size is much more forgiving at only ~7.5MB.

I'm going to be using a custom Cloudflare Zero Trust DoH server for the upstream requests on my hotspot. dnscrypt-proxy has many built-in servers and a list can be found  [here](https://dnscrypt.info/public-servers/). It also supports a vast amount of protocols, including but not limited to, DNScrypt, DNS over HTTPS, Oblivious DNS over HTTPS, and plain old DNS.

## Overview

-   Downloading dnscrypt-proxy on the device
-   Modifying the config file
-   Adding CA Certs
-   Creating a script to make dnscrypt-proxy run on boot
-   Changing the hotspot settings to use the new DNS settings

### Downloading dnscrypt-proxy On the Device

First, we need to get to a root shell on the device which I discussed how to do in my other posts. Then we'll navigate to the  `/tmp`  folder and grab the dnscrypt-proxy tar and untar it.

```text
cd /tmp
wget https://github.com/DNSCrypt/dnscrypt-proxy/releases/download/2.1.1/dnscrypt-proxy-linux_arm-2.1.1.tar.gz
tar -xf dnscrypt-proxy-linux_arm-2.1.1.tar.gz

```

If we do a  `ls`  now, we should see a linux-arm folder.

![](https://ghost.parkercs.tech/content/images/2022/07/image.png)

Now we will change the directory into that folder and move some files to their appropriate location.

```text
cd linux-arm/
mv dnscrypt-proxy /bin
mv example-dnscrypt-proxy.toml /etc/dnscrypt-proxy.toml

```

![](https://ghost.parkercs.tech/content/images/2022/07/image-1.png)

## Modifying the Config File

Now that we've moved all the needed files to their appropriate location we can edit the config file for dnscrypt-proxy. This will allow us to set our upstream servers and our binding address, because we cannot have dnscrypt-proxy and dnsmasq both listening on 127.0.0.1:53, so we'll assign dnscrypt-proxy 127.0.1.200:53, and then tell the system to forward to that address. I recommend you visit the wiki  [here](https://github.com/DNSCrypt/dnscrypt-proxy/wiki/Configuration)  and learn how the config file works.

```text
cd /etc
vi dnscrypt-proxy.toml

```

Line 32 in this file is where you specify your servers. Make sure to uncomment the line if you want to use servers other than the default so it is parsed accordingly. Line 42 is where we change our binding address, so change this 127.0.1.200:53. At the very bottom of the config is where you can specify custom servers using what's called a DNS stamp.  [This](https://dnscrypt.info/stamps/)  is a good generator.

![](https://ghost.parkercs.tech/content/images/2022/07/image-2.png)

use custom servers with the line uncommented

![](https://ghost.parkercs.tech/content/images/2022/07/image-3.png)

new listen address

If you choose to leave the server_names lines commented out, dnscrypt-proxy will choose servers that follow the options in the lines farther down.

## Adding CA Certs

Once you've edited your config, we need to add ca-certificates to the system so dnscrypt-proxy can verify that we are connecting to a server with a valid certificate. We are going to place the file in  `/etc/ssl/certs/ca-certificates.crt`

```text
mkdir ssl
mkdir ssl/certs
cd ssl/certs/
wget https://curl.se/ca/cacert.pem
mv cacert.pem ca-certificates.crt

```

![](https://ghost.parkercs.tech/content/images/2022/07/image-4.png)

Verify that everything is setup by running  `dnscrypt-proxy -config /etc/dnscrypt-proxy.toml`

![](https://ghost.parkercs.tech/content/images/2022/07/image-5.png)

## Making a Script to Run On Boot

We're going to change directory to  `/etc/init.d/`  and create a script that will wait 15 seconds on boot, then start dnscrypt-proxy. This gives the system time to establish a network connection and generally is safer than running immediately when the system comes up.

```text
cd /etc/init.d
vi dnscrypt-proxy.sh
chmod +x dnscrypt-proxy.sh
ln -s /etc/init.d/dnscrypt-proxy.sh /etc/rc5.d/S98dnscrypt-proxy

```

Contents of  `dnscrypt-proxy.sh`:

```text
#!/bin/sh
sleep 15 && \
/bin/dnscrypt-proxy -config /etc/dnscrypt-proxy.toml &

```

Reboot the hotspot, and dnscrypt-proxy should be running! You can verify by performing a nslookup on the device itself.

## Changing the Hotspot Settings to Use the New Server

On the MR1100, in the webUI, enable manual DNS and set DNS 1 to 127.0.1.200 and DNS 2 to 0.0.0.0. On the TMOHS1, edit the /etc/init.d/customdns.sh script with the new IP. A reboot may be needed to apply the changes.

### Helpful Links

[DNSCrypt Documentation](https://github.com/DNSCrypt/dnscrypt-proxy/wiki)  
[DNSCrypt Server List](https://dnscrypt.info/public-servers)  
[DNS Stamp Calculator](https://dnscrypt.info/stamps)  
[How to Use Vim](https://www.redhat.com/sysadmin/introduction-vi-editor)
