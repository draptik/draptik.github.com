---
author: draptik
comments: true
date: 2009-07-31 01:31:08
layout: post
slug: eclipsesubclipse-asks-for-ssh-confirmation-solved
title: Eclipse3.5/Subclipse1.6.x asks for unnecessary SSH confirmation and details
wordpress id: 236
categories:
- programming
---

This is one of those "notes to self"...

I recently upgraded [Eclipse](http://www.eclipse.org) (3.4 -> 3.5) and [Subversion](http://subversion.tigris.org/) (1.5 -> 1.6).

I never check out my sandbox directly from within Eclipse: I do that from the command line. F.ex.:

`
$ svn co svn+ssh://myserver.com/myproject sandbox_myproject
`

From within Eclipse, I then create a new project "from existing project" and select `sandbox_myproject/trunk/project_name`.

Eclipse/Subclipse is smart enough to recognize that the project is under version control (after you've installed [Subclipse](http://subclipse.tigris.org/)).

Well, this is all very cool, and works under Windows and Linux.

Until upgrading to Eclipse 3.5 (Galileo). 

Suddenly you are asked for SSH Details. Why is that? 

If the Command-Line Interface (CLI) knows my SSH-details, why shouldn't Eclipse/Subclipse pick them up? This worked flawlessly with previous version of Eclipse and Subclipse (also with prev. versions of SVN). But no, in Eclipse 3.5 (Galileo), you have to point the Subclipse Interface (1.6.x) directly to `~/.ssh/id_dsa` (the default location). It does not make any sense to me... It seems like a regression in functionality. And there is no comment whatsoever on the tigris/subclipse homepage....

Just a rant.
