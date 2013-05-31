---
author: draptik
comments: true
date: 2009-07-28 22:27:22
layout: post
slug: vmware-keyboard-mapping-issues-alt-gr-solved
title: VMware keyboard mapping issues (Alt Gr) solved
wordpress_id: 226
categories:
- linux
---

If your VMWare keyboard mapping prevents you from using special keys such as Alt Gr, just add the following the your `/etc/vmware/config`:
``` sh /etc/vmware/config
xkeymap.keycode.108 = 0x138 # Alt_R
xkeymap.keycode.106 = 0x135 # KP_Divide
xkeymap.keycode.104 = 0x11c # KP_Enter
xkeymap.keycode.111 = 0x148 # Up
xkeymap.keycode.116 = 0x150 # Down
xkeymap.keycode.113 = 0x14b # Left
xkeymap.keycode.114 = 0x14d # Right
xkeymap.keycode.105 = 0x11d # Control_R
xkeymap.keycode.118 = 0x152 # Insert
xkeymap.keycode.119 = 0x153 # Delete
xkeymap.keycode.110 = 0x147 # Home
xkeymap.keycode.115 = 0x14f # End
xkeymap.keycode.112 = 0x149 # Prior
xkeymap.keycode.117 = 0x151 # Next
xkeymap.keycode.78 = 0x46 # Scroll_Lock
xkeymap.keycode.127 = 0x100 # Pause
xkeymap.keycode.133 = 0x15b # Meta_L
xkeymap.keycode.134 = 0x15c # Meta_R
xkeymap.keycode.135 = 0x15d # Menu
```

I found this solution on the [German Ubuntu forum](http://forum.ubuntuusers.de/topic/vmware-alt-gr-und-andere-tastaturproblem-unte/).

This worked with VMware-server-2.0.1-156745 in combination with host OS Ubuntu 9.04 and guest OS Windows XP SP3.
