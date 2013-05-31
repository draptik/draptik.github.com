---
author: draptik
comments: true
date: 2011-05-09 19:55:56
layout: post
slug: adding-truecrypt-to-ubuntus-unity-panel
title: Adding Truecrypt to Ubuntu's Unity panel
wordpress_id: 415
categories:
- linux
---

To add applications like Truecrypt to Ubuntu's Unity panel the application has to be "white listed".



	
  1. Install dconf-editor (ie `sudo aptitude install dconf-editor`)

	
  2. Go to section desktop | unity | panel and add 'Truecrypt' to the systray-whitelist variable

	
  3. Restart Unity (or log out and back in again)




That's it. Found this solution [here](http://askubuntu.com/questions/35076/how-do-i-whitelist-truecrypt-to-work-under-unity).
