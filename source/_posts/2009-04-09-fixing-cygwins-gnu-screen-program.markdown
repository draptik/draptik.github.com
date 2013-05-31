---
author: draptik
comments: true
date: 2009-04-09 12:57:47
layout: post
slug: fixing-cygwins-gnu-screen-program
title: Fixing Cygwin's GNU Screen program
wordpress_id: 5
categories:
---

After years of ignorance I discovered the wonderful GNU screen program on my GNU/Linux system. Basically screen is a window manager for terminals or terminal sessions. But after installing the Cygwin package on my Windows box I ended up with this error message after trying to invoke screen:
```
C:\>screen
Cannot find terminfo entry for 'cygwin'.
```

This is unlikely to help others, but I'll keep it as a reference... The culprit was a messed up installation. I had the following directories:
```
/usr/share/tabset       # <- this folder was empty
/usr/share/tabset(2)
/usr/share/terminfo     # <- this folder was empty
/usr/share/terminfo(2)
```

I just had to delete the empty directories and rename tabset(2) and terminfo(2)
```
$ rmdir /usr/share/tabset /usr/share/terminfo
$ mv /usr/share/tabset\(2\) /usr/share/tabset
$ mv /usr/share/terminfo\(2\) /usr/share/terminfo
```
and now screen works just fine with Cygwin.
