---
author: draptik
comments: true
date: 2009-04-13 07:56:08
layout: post
slug: first-experiences-with-portable-ubuntu-and-upgrading-from-804-to-810
title: First experiences with Portable Ubuntu and Upgrading from 8.04 to 8.10
wordpress_id: 32
---

There is a new thing called "Portable Ubuntu". Get it here:

[http://portableubuntu.demonccc.com.ar/](http://portableubuntu.demonccc.com.ar/)

It's about 450MB download and its default size after unpacking is about 2GB.

It promises to be a Linux installation which runs side by side with your MS-Windows desktop. After you downloaded and unzipped the file to a location of your choice you can start Ubuntu from within your active Windows session by double-clicking the file `run_portable_ubuntu.bat`. That was easy, wasn't it? You now have GNU/Linux!

Let's do an update...



	
  1. Get a current version: System -> Administration -> Software Sources

	
  2. Choose a good location for "Download from" an the first tab

	
  3. Update everything. This will be a large download (in my case: ~200MB) and** take some time**

	
  4. Shutdown (click on the 'red-arrow-exit-door-symbol' on the right of the toolbar which appeared at the top) and Start the Portable Ubuntu application (double-click the `run_portable_ubuntu.bat` file).

	
  5. Everything should be fine at this state. Do not continue if something went wrong....


You now have a minimal up-to-date Ubuntu on your Windows box.

The following text will explain how to upgrade your minimal Ubuntu (version: 8.04 LTS) to the current version (8.10 and in a couple of days it will be 9.04).

First let's setup usage for the current version, not the LTS=long term support version.  System -> Administration -> Software Sources. Select the third tab labelled "Updates". At the bottom, select "Normal releases"; Then close the Software Sources GUI.

**Now**** I will explain the approach I tried which will fail**.



	
  1. System -> Administration -> Update Manager : It shows you that version 8.10 (or newer) is available -> click upgrade

	
  2. Again, click "Upgrade" after reading the release notice.

	
  3. This will fail !!


You will get an error message telling you that there is not enough disk space available. The reason being that the minimal Ubuntu installation only reserved appr.  2GB of space. And since this installation should not mess with your precious disk space, this is fine. Unless you want more GNU/Linux ;-)

Increasing the disk space is not as difficult as you might imagine.

(The official doc is here: [http://portableubuntu.demonccc.com.ar/](http://portableubuntu.demonccc.com.ar/) -> Documentation -> How to change the file system size)

But before we go on:

Files to edit:
`
config\portable_ubuntu.conf
`

Files to move/rename:
`
images\rootfs.img`

Step-by-Step Instructions:



	
  1. Goto the image folder (`cd image`)

	
  2. Create a new image file (`fsutil file createnew rootfs_.img 3758096384`). The number at the end is a multiple of 1024.  An installation of  2GB is calculated as follows: 1024*2*1024*1024=2147483648. If you want  4GB space that would be 1024*4*1024*1024=4294967296.  This process is very fast.

	
  3. Now you must edit the file config\portable_ubuntu.conf.      Change the line "`cobd3="D:\Documents and Settings\czw5hv\Application Data\Windux\images\root.img`" to "`cobd3=images\rootfs_.img`"

	
  4. Execute the script `run_portable_ubuntu.bat` (double-click the `run_portable_ubuntu.bat` file) **NOTE: THIS CAN TAKE UP TO 5MIN!!** If everything is OK, you will see the GNOME task bar at the top of your screen.

	
  5. Open a terminal:  Applications -> Accessories -> Terminal

	
  6. Switch to root user by typing: "sudo su" (your default password is 123456)

	
  7. Issue the dd command. **This is the crucial part**, because the command** dd**
will copy byte-per-byte, no-matter-what, from if (input folder =
/dev/cobd0) to the of (output folder = /dev/cobd3)

	
  8. `dd if=/dev/cobd0 of=/dev/cobd3`


If a simple restart of Portable Ubuntu works now, you can start upgrading from 8.04 to 8.10 without any disk space problems. Just repeat the 2 steps I mentioned above that failed because you did not have enough disk space.

You can now copy the folder to your USB stick.

Have fun!

**Setting the correct time zone in Portable Ubuntu**

For some reason I could not set the correct time zone using the GUI (System -> Administration -> Time and Date). Everything is grayed out (even though I had to enter the root password to open the Time&Date application).  I had to manually run

`sudo dpkg-reconfigure tzdata`

to set the correct time zone.
