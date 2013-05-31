---
author: draptik
comments: true
date: 2009-06-17 20:39:05
layout: post
slug: installing-adobe-air-and-tweetdeck-on-ubuntu-9-04
title: Installing Adobe AIR and TweetDeck on Ubuntu 9.04
wordpress_id: 172
categories:
- linux
---

This is a short manual on how to install [Adobe AIR](http://www.adobe.com/de/products/air/) and Adobe AIR applications such as [TweetDeck](http://www.tweetdeck.com/beta) on [Ubuntu 9.04](http://www.ubuntu.com) (This instruction should also work with most up-to-date Linux distributions). Once you have confirmed that everything works (i.e. TweetDeck works), you can delete all downloaded files.






	
  1. Download Adobe AIR from their homepage: [http://airdownload.adobe.com/air/lin/download/latest/AdobeAIRInstaller.bin](http://airdownload.adobe.com/air/lin/download/latest/AdobeAIRInstaller.bin)

	
  2. Make `AdobeAIRInstaller.bin` executable

`$ chmod +x AdobeAIRInstaller.bin`


	
  3. Install Adobe AIR

`$ sudo ./AdobeAIRInstaller.bin`This will install Adobe AIR to `/opt/Adobe AIR`


	
  4. Download TweetDeck

`$ wget \
[http://tweetdeck.com/beta/TweetDeck_0_25_manual.air](http://tweetdeck.com/beta/TweetDeck_0_25_manual.air)`


	
  5. Install TweetDeck using Adobe AIR installer

`$ /opt/Adobe\ AIR/Versions/1.0/airappinstaller`


	
  6. This will open a GUI installer and automatically start TweetDeck. Furthermore, a starter will be created on your desktop.
[gallery]


	
  7. The desktop starter is just a script. Here's the content:
``` sh
`~/Desktop$ cat \
tweetdeckfast.f9...21.1.desktop
[Desktop Entry]
Name=TweetDeck
Comment=Welcome to TweetDeck - a unique way to view your twitter timeline
GenericName=TweetDeck
Exec='_opt'/'TweetDeck'/bin_'TweetDeck'
Type=Application
Terminal=false
Icon=tweetdeckfast.f9...4c21.1
StartupNotify=true
X-KDE-StartupNotify=true
Categories=Utility;
X-AppInstall-Package=tweetdeckfast.f9..4c21.1
X-AppInstall-Section=main`
```

	
  8. To uninstall Adobe AIR and Adobe AIR applications, select "Programs" -> "Applications" -> Adobe AIR Uninstaller". This will also automatically uninstall Adobe AIR applications (i.e. TweetDeck).


