---
author: draptik
comments: true
date: 2011-05-25 20:35:29
layout: post
slug: workaround-for-virtualbox-4-0-8-regression-bug
title: Workaround for VirtualBox 4.0.8 regression bug
wordpress_id: 419
categories:
- linux
---

<del>The current version of Oracle's VirtualBox (4.0.8) has</del> Version 4.0.8 had a regression bug which seems to only manifest itself when using Debian based hosts with 64-bit and Windows 7 64-bit guest system.

Here is the summary of the workaround posted as [VirtualBox Ticket 8948](http://www.virtualbox.org/ticket/8948):



	
  1. Edit the settings file to allow the VM to start again (Replace control characters in line 315 with 4.0.8).

	
  2. Start the Windows guest

	
  3. Run `regedit`

	
  4. Go to `HKLM\SOFTWARE\Oracle\VirtualBox Guest Additions`

	
  5. Create a string key `VersionEx` (use exactly this spelling)

	
  6. Give it a reasonable value (ie 4.0.8)

	
  7. Shut down the VM.

	
  8. Repeat step 1

	
  9. Restart and shutdown windows guest again




That's it.




Edit: This bug has been fixed in version 4.0.10 (released 2011-06-27).
