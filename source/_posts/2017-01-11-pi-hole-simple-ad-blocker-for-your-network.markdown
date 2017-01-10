---
layout: post
title: "Pi Hole - simple ad blocker for your network"
date: 2017-01-11 00:01:24 +0100
comments: true
categories: [raspberry pi, iot, ad blocker, dd-wrt, open-wrt, pi hole]
---

Advertisements in web pages can be a nuisance. But they are a necessary evil because companies/bloggers producing high quality content have to earn a living. [Troy Hunt](https://en.wikipedia.org/wiki/Troy_Hunt) recently wrote a [nice post on the subject](https://www.troyhunt.com/ad-blockers-are-part-of-the-problem/).

Most **desktop browsers** provide "ad blockers" as plugins. These plugins normally also have configurable whitelists (whitelist: a list of sites exluded from being blocked) &#8212; which you should use for those sites which (a) provide high quality content and (b) rely on advertisements for a living.

Other devices in our home network also communicate with the web: 

- cell/smart phones (f.ex. iPhone, Android)
- smart TVs (f.ex. [Samsung](https://www.cnet.com/news/samsungs-warning-our-smart-tvs-record-your-living-room-chatter/))
- and all those new IoT devices (IoT: Internet of Things): those "smart" home automation devices that communicate with "the cloud" and your "smart phone"

We can't install "ad blockers" on these devices.

Here is where [Pi Hole](https://github.com/pi-hole/pi-hole) comes into play: We can plug a Raspberry Pi straight into our router!

My experience: 

On my phone (an old Galaxy-S4) web pages load a **hell of a lot faster** when I'm in my home network compared to outside of my home network. 

The Pi Hole web interface looks like this:

{% img /images/posts/pi-hole/screenshot-pi-hole-25-percent-blocked-requests.png %}

As you can see in the screenshot: ~20% traffic is blocked (!) and you can configure whitelists just like with those browser plugins. **Nobody in my household noticed that 20% traffic was blocked**.

Installing the software on your Raspberry Pi is straightforward: Just follow the instructions on the [Pi Hole](https://github.com/pi-hole/pi-hole) website.

Configuring your router is another beast: OpenWRT and DD-WRT router instructions are available ([example - short youtube promo](https://www.youtube.com/watch?v=TzFLJqUeirA)).
