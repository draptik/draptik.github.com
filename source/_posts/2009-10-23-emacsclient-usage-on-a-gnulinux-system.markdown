---
author: draptik
comments: true
date: 2009-10-23 21:31:02
layout: post
slug: emacsclient-usage-on-a-gnulinux-system
title: Emacsclient usage on a GNU/Linux system
wordpress_id: 255
categories:
- linux
---

Short instructions on how to setup Emacs using the Emacs Server/Emacs Client system on a GNU/Linux machine.

This post is intended for people familiar with GNU/Emacs.

**Requirements:**

GNU/Emacs 23 or greater (for Ubuntu get the Emacs PPA: [https://launchpad.net/~ubuntu-elisp/+archive/ppa](http://https://launchpad.net/~ubuntu-elisp/+archive/ppa))

**Files to edit:**

`~/.emacs`
`~/.bashrc`

**System settings using Gnome:**

System -> Einstellungen -> Startprogramme

**For the impatient:**

Add this to your `~/.bashrc`:

``` sh ~/.bashrc
export ALTERNATE_EDITOR=emacs EDITOR=emacsclient VISUAL=emacsclient
## you can always use the command "emacs" instead of "emacsclient -c"
alias emacs='emacsclient -c'
```

Add this to your `~/.emacs`:

``` sh ~/.emacs
(server-start)
```

Add this to your system startup options:

``` sh
/path/to/your/emacs --daemon
```

ie:

``` sh
/usr/bin/emacs --daemon
```

Gnome panel starter:

``` sh
/usr/bin/emacsclient -c'
```

**Detailed instructions**

Starting Emacs from scratch will always load your init file (default: ~/.emacs). Loading this file can take a few seconds if your init file has grown over the years and if you load many external libraries.

To decrease the load time for situations like this the GNU/Emacs folks have developed the GNU/Emacs daemon and the GNU/Emacs server-client concept.

This concept allows you to attach to an already running session. No matter if you are currently using X or  a TTY session!

Just try it (without changing any settings):



	
  1. Start GNU/Emacs from a shell under X: `
$ emacs
`

	
  2. Now start the Emacs server from within Emacs: `
M-x server-start
`

	
  3. Open a file from within Emacs (it does not have to exist): `
C-x C-f ~/deleteme.txt
`

	
  4. Because we started the Emacs server, we can now start the Emacs client with close-to-zero load time:

	
  5. Starting the Emacs client from a new shell: `
$ emacsclient deleteme-from-shell.txt
`

This will open the file "deleteme-from-shell.txt" in your running Emacs session. Cool, isn't it?

**IMPORTANT:** To close the attached Emacs client without killing the Emacs server you have to use "C-x #" instead of "C-x C-c"!

	
  6. Starting the Emacs client from a new shell AND opening a new Emacs frame:Same as step 5, but use "-c": `
$ emacsclient -c deleteme-from-shell.txt
`

Again: Close the client using "C-x #"! This will close the frame (=window) while keeping the server running.

	
  7. Starting the Emacs client from a new shell AND opening it within the current shell:Same as step 5, but use "-t": `
$ emacsclient -t deleteme-from-shell.txt
`

This will also work for a true TTY session. An example of a true TTY session can be achieved by switching to TTY-0 (Ctrl+Alt+F1; to return to your graphical display: Ctrl+Alt+F7). Try it:

Ctrl+Alt+F1

Login with username and password, then type

`
$ emacsclient -t deleteme-from-shell.txt
`

We are still attached to the same session! Cool!

Switch back to your graphical display: Ctrl+Alt+F7

	
  8. The only drawback so far is that we had to start the Emacs server manually from within an Emacs session using "M-x server-start". Otherwise "emacsclient" will complain that it has no server to attach to. So let's include this feature in our Emacs init file (~/.emacs). Add this to your init file: `
(server-start)
`

	
  9. The previous step still requires us to start Emacs before we can invoke emacsclient. Let's tell our default shell (ie Bash) to use "emacsclient" as default. If that doesn't work, our shell should use "emacs". Add the following the your ~/.bashrc: `
export ALTERNATE_EDITOR=emacs EDITOR=emacsclient VISUAL=emacsclient
`

	
  10. I prefer to to only use a single command, "emacs", and "the computer" should figure out
IF an Emacs server is running (then I'll use "emacsclient" to attach to a running Emacs server)

OR if I have to start a new Emacs session (which, in turn, starts an Emacs server).


You can always use the command `emacs` instead of `emacsclient -c`
``` sh
alias emacs='emacsclient -c'
```

	
  11. Sugar: To be sure to have an Emacs server running, we can add the Emacs daemon to our system startup options. How to add scripts to your startup options differs strongly between different Linux systems. Here's how I did it (using Gnome):"System" -> "Einstellungen" -> "Startprogramme"(in English this should correspond to something like "System" -> "Settings" -> "Start programs")I added a new entry with the following content: `
/path/to/your/emacs --daemon
`

ie:

`
/usr/bin/emacs --daemon
`

	
  12. Further sugar: To use a Gnome panel starter, just add this program parameter to the starter properties: `
/usr/bin/emacsclient -c
`


