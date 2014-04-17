---
layout: post
title: "Installing Seafile on Raspberry Pi"
date: 2014-04-15 23:23
comments: true
categories: [Raspberry Pi, cloud, seafile]
---
With all the security issues in the past relating to privacy I've been wanting to install a private cloud service similar to [Dropbox](http://www.dropbox.com) for some time now. So, here's a post on how to install Seafile on the Raspberry Pi.

I choose [Seafile](http://seafile.com/en/home/) over [Owncloud](http://owncloud.org/) because I have read multiple posts that (1) Owncloud is not very responsive an a Raspberry Pi and (2) Seafile has a better security model (see [1](http://blog.kovah.de/private-cloud-owncloud-alternativen-teil-2), [2](http://stevenhickson.blogspot.de/2013/04/cloud-storage-on-raspberry-pi.html), [3](http://stevenhickson.blogspot.de/2013/04/cloud-storage-on-raspberry-pi.html)).

Using a [Raspberry Pi](TODO) for the server seems like a good choice, because it has very low power consumption, so you can have it running 24/7. Furthermore, the Rasperry Pi can be setup with Debian GNU/Linux (f. ex. [Raspbian](TODO)) running in server mode. As Debian is a widely used Linux distribution, most problems can be easily solved by searching the web.

Installing Seafile should be straightforward from following the instructions at [wiki/Download-and-setup-seafile-server](https://github.com/haiwen/seafile/wiki/Download-and-setup-seafile-server). If you can read German, you can also follow the excellent instructions on Jan Karres's blog [Raspberry Pi: Owncloud-Alternative Seafile Server installieren](http://jankarres.de/2013/06/raspberry-pi-owncloud-alternative-seafile-server-installieren/).

As there are some pitfalls along the way, I'll describe how I installed Seafile with SSL support.

## Installation

Note: This section is mainly a merge of [Jan Karres's blog post (in German)](http://jankarres.de/2013/06/raspberry-pi-owncloud-alternative-seafile-server-installieren/) and the [official wiki documentation from seafile](https://github.com/haiwen/seafile/wiki/Download-and-setup-seafile-server).

### Prerequisites

I'll assume you have a working Raspian installation on a Raspberry Pi.

If you wan't to reach your Seafile server from the internet and your ISP only provides you with a dynamic IP you will have to register with a [DDNS](http://en.wikipedia.org/wiki/Dynamic_DNS) provider such as [Dyn](TODO) or [no-ip.com](TODO).

### Step 1

Update Raspbian:

`$ sudo aptitude update`

### Step 2

Install dependencies required by Seafile:

`$ sudo aptitude -y install python2.7 python-setuptools python-simplejson python-imaging sqlite3`

### Step 3

For security reasons we'll create a separate user for running Seafile. The user will be called `seafile` and will not require a password, since we will never be accessing this user directly through SSH.

`$ sudo adduser seafile --disabled-password`

Switch to being `seafile` user:

`$ sudo su seafile`

Change to `seafile`'s home directory:

`$ cd`

### Step 4

First we'll create a new folder `mycloud` in the seafile user's home directory:

`$ mkdir mycloud && cd mycloud`

Download and unpack Seafile Server for Raspberry Pi from [http://www.seafile.com/en/download/](http://www.seafile.com/en/download/).

``` sh
wget https://bitbucket.org/haiwen/seafile/downloads/seafile-server_2.1.5_pi.tar.gz
tar -xvzf seafile-server_*
mkdir installed
mv seafile-server_* installed
```

You should have the following directory structure:

``` sh
tree . -L 2
.
├── installed
│   └── seafile-server_2.1.5_pi.tar.gz
└── seafile-server-2.1.5
    ├── reset-admin.sh
    ├── runtime
    ├── seaf-fuse.sh
    ├── seafile
    ├── seafile.sh
    ├── seahub
    ├── seahub.sh
    ├── setup-seafile-mysql.py
    ├── setup-seafile-mysql.sh
    ├── setup-seafile.sh
    └── upgrade
```
{% blockquote https://github.com/haiwen/seafile/wiki/Download-and-setup-seafile-server %}
The benefits from this layout
{% endblockquote %}


### Step 5

Installation:

`$ cd seafile-server-2.1.5`



`./setup-seafile.sh`



TODO

## Router configurations

TODO

## First idea

{% img center /images/posts/seafile_rpi/seafile_simple.svg %}



{% img center /images/posts/seafile_rpi/seafile_final.svg %}

```
internet/intranet <--> modem/router <--> seafile (via rpi)
```

In case the above works for you out of the box, you can skip the rest of this article.

I'm guessing that the 'internet' part works (verify with your smartphone and deactivated wlan). (if the internet part does not work: you're on your own)

But trying to reach the IP of your DDNS from within your LAN fails.

Your first solution should be deactivating your NAT loopback.

## Backup of Seafile configuration files

TODO

## Versions (TODO)

### Raspbian

```
echo `uname -a`
```

### Router/Modem exposed to WAN

easybox

### Router internal

bufallo
