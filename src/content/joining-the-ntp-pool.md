---
layout: post
title: Joining the NTP Pool
image: img/callum-shaw-555357-unsplash.jpg
author: [Parker Stephens]
date: 2021-04-06T12:00:00.000Z
tags: []
draft: false
---

## What is NTP?

NTP, or Network Time Protocol is a UDP-based protocol on port 123 that allows computers to sync their time over the internet. NTP servers have different levels on a hierarchy based system, or stratums, with the highest at 0 being the reference clock sources that are not made public and are connected directly to stratum 1 servers, and so on and so forth. Endpoints such as your phone, computer, router, etc, can connect to any server at or below stratum 1.

![](https://ghost.parkercs.tech/content/images/2021/01/image-4.png)

## What Is the NTP Pool?

The  [NTP pool](https://www.ntppool.org/)  is a large virtual cluster of timeservers that are used for millions of devices everyday. As of January 29, 2021, there are 4,392 active servers that take part in the NTP pool project. With all these servers, an accurate time can be kept, and load-balancing can be done amongst all servers to distribute a fair load upon all of them. Even with all these servers, however, more servers still need to be added to keep up with the growing demand of internet devices.

## How Can I Join the Pool?

Now you may be asking yourself, "How do I join the pool?" All that you need to join the pool is a NTP server with a static IP address (IPv4 and/or IPv6!) and some good, manually selected upstream time servers (or your own clock source!). If you've got all that, you're eligible to join the NTP pool!

## Choosing Good Upstream Time Servers

If you have a NTP server, but aren't sure of how to configure your upstream NTP servers, this can help. You want to use statically assigned NTP servers, and NOT pools (you can use servers that are in the pool, just make sure you save the specific IP address, NOT the pool hostname.) For example, if you want to use time.google.com, run a DNS lookup for time.google.com, like below, and choose one of those IP addresses instead of the hostname.

![](https://ghost.parkercs.tech/content/images/2021/01/Screen-Shot-2021-01-29-at-9.21.43-AM.png)

Also, you can configure servers found on the NTP website as well.  [Here](https://support.ntp.org/bin/view/Servers/StratumOneTimeServers)  is a list of stratum 1 time servers, and  [here](https://support.ntp.org/bin/view/Servers/StratumTwoTimeServers)  is a list of stratum 2 time servers. You want to pick servers that are marked "OpenAccess" for their access policy, and ones that are generally closer to your location. The goal here is to pick several different servers, not only for failover, but because of how NTP works, it compares the different server's times, and will only sync if three or more servers agree on the time.

## Joining the Pool

Now it's time to join the pool! Make sure you have a firewall rule to allow UDP traffic on port 123 on your timeserver, and use a website such as  [this one](https://servertest.online/ntp)  to check if your NTP server can be reached from the outside internet. Once you've made sure you've done that, head over  [here](https://manage.ntppool.org/manage)  to login in to the NTP pool website.

![](https://ghost.parkercs.tech/content/images/2021/04/chrome_ewoX3yrwBK.png)

Once you are logged in, you will be brought to your dashboard. On the right-hand side, you can see the spot to add your server. Type in the hostname or IP address of your server, IPv4 and/or IPv6, and click add. It will then be populated in the list in the middle. There is an option to change the net speed of the server. This is NOT the actual speed of what NTP clients will connect at, so if you select 1000 Mbps you will NOT receive a full gigabit of NTP traffic. The net speed is relative to all the other NTP servers, so a 500 Mbps server will receive double the amount of that of a 250 Mbps server. In my dashboard I have my servers set to 1000 Mbps, and see a max constant 5 Mbps. This may sound too good to be true, but 5 Mbps of traffic for me is over 120k states of connections in my firewall.

## When Will My Server Start Being Used?

You will notice at first that your NTP server will not be used. This is because your NTP server on the pool has a score, with a max of 20 and a low of -100, and for your server to be used in the pool it must have a minimum score of 10. What affects your score? Server downtime and if your time offset is more than 100ms. You can monitor your score on the dashboard and once it reaches 10 you will start to see NTP traffic flow to your server.