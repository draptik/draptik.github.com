---
author: draptik
comments: true
date: 2010-01-15 22:00:10
layout: post
slug: using-basic-emacs-keybindings-in-gnome-terminal
title: Using basic Emacs keybindings in Gnome-Terminal
wordpress_id: 299
categories:
- linux
---

**Summary:** Use basic Emacs keyboard shortcuts (ie navigation) in Gnome-Terminal while using the Bash shell. This post is only interesting for people familiar with Emacs.

This is so simple that I almost afraid to post it...

...One of those things that has been bothering you for years(!), but was never urgent enough to invest time figuring out how to fix...

**Problem: **

You're used to Emacs shortcuts. Bash (and many other shells) support Emacs keybindings out of the box. But your default terminal comes with a stupid menu bar. So you press `M-d` (Emacs' "kill-word"; for non-Emacs users: this corresponds to the key sequence `Alt-d`) and end up calling the "File" menu entry of the Gnome-Terminal (File is called "**D**atei" in German, which is my LOCAL). If you your LOCAL settings are English, you will have the same problem with the Emacs shortcut `M-f` (in Emacs this is "word-forward"): The Gnome-Terminal will grap the key sequence and open the "**F**ile" menu.

**Solution:**



	
  1. Open your Gnome-Terminal

	
  2. Edit -> Keyboard shortcuts -> DISABLE ALL MENU ACCESS KEYS (If you have German settings the menu entry is called: "Bearbeiten -> Tastenkombinationen -> Alle Menükürzel aktivieren". Disable the checkbox..)


That's it.
