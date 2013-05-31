---
author: draptik
comments: true
date: 2011-01-01 21:13:43
layout: post
slug: configure-virtualbox-windows-guest-system-to-use-2-monitors
title: Configure VirtualBox Windows guest system to use 2 monitors
wordpress_id: 362
categories:
- linux
---

## **﻿Purpose**


Our host system for VirtualBox is GNU/Linux. We want our guest system, windows, to use two monitors.  
This solution should work for all Linux distributions supported by VirtualBox4.


## **﻿Required software**





	
  * [Ubuntu 10.10](http://www.ubuntu.com/) (aka Maverick) as host for VirtualBox4

	
  * [VirtualBox 4](http://www.virtualbox.org/)

	
  * VirtualBox guest sytem: Windows XP (incl. VirtualBox guest additions)




## **Configuration**





	
  1. Setup virtual guest machine (windows) within vm host system (GNU/Linux) to use two monitors:

[caption id="attachment_378" align="alignnone" width="300" caption="Setting up virtualbox 4 to use 2 monitors for windows xp guest"][![virtualbox-host-ubuntu-10.10](http://draptik.files.wordpress.com/2011/01/virtualbox-host-ubuntu-10-10.png?w=300)](http://draptik.files.wordpress.com/2011/01/virtualbox-host-ubuntu-10-10.png)[/caption]

	
  2. Within virtual guest (windows): Configure dual monitor setup:

[caption id="attachment_379" align="alignnone" width="263" caption="Setup within virtualbox guest system (windows)"][![virtualbox_dualmonitor_windows_config](http://draptik.files.wordpress.com/2011/01/01012011_183458_virtualbox_dualmonitor_windows_config.png?w=263)](http://draptik.files.wordpress.com/2011/01/01012011_183458_virtualbox_dualmonitor_windows_config.png)[/caption]




## Side note


Upgrading from VirtualBox owned by Sun to VirtualBox owned by Oracle was very smooth. Even for Linux host systems. Oracle, thanks for that!
