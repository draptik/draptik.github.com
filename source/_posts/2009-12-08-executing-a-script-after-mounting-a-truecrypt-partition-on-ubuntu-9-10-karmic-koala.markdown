---
author: draptik
comments: true
date: 2009-12-08 00:18:40
layout: post
slug: executing-a-script-after-mounting-a-truecrypt-partition-on-ubuntu-9-10-karmic-koala
title: Executing a script after mounting a TrueCrypt partition on Ubuntu 9.10 (Karmic
  Koala)
wordpress_id: 279
categories:
- linux
---

**Goal: **Automatically run a script/program of your choice after mounting a TrueCrypt partition on Ubuntu 9.10.

**Background info on udev (very short)**



	
  * UDEV listens to certain events. Depending on the event, a certain "rule" can be executed.

	
  * To see some of the default rules, visit `lib/udev/rules.d` and study those examples.

	
  * Many current GNU/Linux distros use the udev system to manage actions that should happen after automounting.


**Setup**

My setup

Your setup might differ! I have a USB stick. Amongst other things, it includes a TrueCrypt partition. I am able to (un)mount the TrueCrypt partition using the TrueCrypt program.

Info required for udev

udev needs a way of identifying your partition. We can use udevadm info (this corresponds to udevinfo in older tutorials) to get this info. I know that my truecrypt partition is always mounted to /media/truecryt1. This is mapped from /dev/mapper/truecrypt1 (have a look at the output of the "mount" command after mounting truecrypt to see these details).

We need unique infos about the truecrypt partition for udev. To aquire this info, use:
`
$ udevadm info -a -p $(udevadm info -q path -n /dev/mapper/truecrypt1)
`

On my system the output is:

``` sh
$ udevadm info -a -p $(udevadm info -q path -n /dev/mapper/truecrypt1)
Udevadm info starts with the device specified by the devpath and then
walks up the chain of parent devices. It prints for every device
found, all possible attributes in the udev rules key format.
A rule to match, can be composed by the attributes of the device
and the attributes from one single parent device.

looking at device '/devices/virtual/block/dm-0':
KERNEL=="dm-0"
SUBSYSTEM=="block"
DRIVER==""
ATTR{range}=="1"
ATTR{extrange}=="1"
ATTR{removable}=="0"
ATTR{ro}=="0"
ATTR{size}=="6290941"
ATTR{alignmentoffset}=="0"
ATTR{capability}=="10"
ATTR{stat}=="    6521        0     7172     5870        1        0     1        0        0       70     5870"
```
Not much unique stuff there. The only thing that looks unique is `ATTR{size}=="6290941"`. Let's take that.

**udev rule**



	
  * Go to `etc/udev/rules.d`

	
  * Create a new file `85-myrules.rules`

	
  * Add the following line (adapted to your needs) 
    ``` sh
    ACTION=="add", KERNEL=="dm-[0-9]*", SUBSYSTEM=="block", ATTR{size}=="6290941", RUN+="/usr/bin/touch /home/your-user-name-here/testcrypt.txt"
    ```

	
  * Some notes:

	
    * `==` corresponds to a "check" (ie check if kernel equals dm-.., check if subsystem equals block, check if attribute-size equals 6290941)

	
    * `=`  corresponds to an assignment

	
    * `+=` corresponds to an assignment





**Test**



	
  * Unmount your TrueCrypt volume

	
  * Reload udev: `sudo reload udev`

	
  * Mount your TrueCrypt volume

	
  * Check your home directory: It should now contain a file testcrypt.txt


