---
layout: post
title: "Installing Seafile on Raspberry Pi"
date: 2014-04-15 23:23
comments: true
categories: [Raspberry Pi, cloud]
---
Installing [Seafile](http://seafile.com/en/home/) should be straightforward from following the instructions at [wiki/Download-and-setup-seafile-server](https://github.com/haiwen/seafile/wiki/Download-and-setup-seafile-server).


The goal is to install seafile with SSL at home on a RPI and to have seafile available at home and from everywhere.

As there are some pitfalls along the way, I'll describe how I installed seafile.

## First idea

```
internet/intranet <--> modem/router <--> seafile (via rpi)
```

In case the above works for you out of the box, you can skip the rest of this article.

I'm guessing that the 'internet' part works (verify with your smartphone and deactivated wlan). (if the internet part does not work: you're on your own)

But trying to reach the IP of your DDNS from within your LAN fails.

Your first solution should be deactivating your NAT loopback.


## Versions (TODO)

### Raspbian

```
echo `uname -a`
```

### Router/Modem exposed to WAN

easybox

### Router internal

bufallo
