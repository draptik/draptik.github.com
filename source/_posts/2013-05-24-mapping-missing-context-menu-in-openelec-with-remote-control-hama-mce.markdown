---
layout: post
title: "Mapping missing context menu in OpenELEC with Remote Control HAMA MCE"
date: 2013-05-24 21:27
comments: true
categories: [Raspberry Pi]
---
{% img right /images/posts/Hama_mce_remote.jpg 150 150 'Remote control HAMA MCE' 'Remote control HAMA MCE' %}
The IR remote control [HAMA MCE](http://www.amazon.de/Hama-MCE-Remote-Control-PC-Fernbedienung/dp/B000X1EL4W/ref%3Dsr_1_1?ie%3DUTF8&amp;qid%3D1363942117&amp;sr%3D8-1) works out of the box with [XBMC](http://xbmc.org). Except that XBMC's context menu is not mapped to any key.

An alternative solution to the one provided here is using your smart phone or tablet as a remote control with the excellent [Yatse app](https://play.google.com/store/apps/details?id=org.leetzone.android.yatsewidgetfree&hl=en).

## Setup
- [Raspberry Pi](http://en.wikipedia.org/wiki/Raspberry_Pi) with [OpenELEC](http://www.openelec.tv/)
- Remote control: [HAMA MCE](http://www.amazon.de/Hama-MCE-Remote-Control-PC-Fernbedienung/dp/B000X1EL4W/ref%3Dsr_1_1?ie%3DUTF8&amp;qid%3D1363942117&amp;sr%3D8-1) (~20EUR)

## Problem

Remote control does not have a key which is mapped to the context menu.

## For the impatient

- Login to your Raspberry Pi with SSH
- Create file <code>/storage/.xbmc/userdata/Lircmap.xml</code>.
-	Add following content:
``` xml /storage/.xbmc/userdata/Lircmap.xml
		<lircmap>
			<remote device="devinput">
				<title>KEY_INFO</title>
			</remote>
		</lircmap>
```
- Restart Raspberry Pi

## Solution

### File locations

- OpenELEC’s system location for keymap related configs are located here: <code>/usr/share/xbmc/system/</code> (I found this information in the [openelec forum](http://openelec.tv/forum/103-infared-remotes/37369-why-is-the-lircmap-xml-in-read-only-mode#37369))
- System default keymaps folder: <code>/usr/share/xbmc/system/keymaps/</code>
- System default Lircmap.xml: <code>/usr/share/xbmc/system/Lircmap.xml</code>
- Root user’s keymaps folder (empty by default): <code>/storage/.xbmc/userdata/keymaps/</code>
- Root user’s Lircmap.xml does not exist by default. It has to be created: <code>/storage/.xbmc/userdata/Lircmap.xml</code>

### Workflow

#### Get device name and name of desired button on remote control (‘irw’)

- Adapted from [Guide To Test a Remote and Remap Keys](http://wiki.openelec.tv/index.php?title=Guide_To_Test_a_Remote_and_Remap_Keys)
- Login via SSH
- Type command <code>irw</code>
- Press the button on remote control where you would like to map the context menu (I pressed the button with the ‘right mouse click’ symbol). This will output something like:
``` sh
root# irw
17b 0 KEY_INFO devinput
17b 0 KEY_INFO_UP devinput
```
<code>KEY_INFO</code> is the **key name**, <code>devinput</code> is the **device name**.

The next sentence in the [guide](http://wiki.openelec.tv/index.php?title=Guide_To_Test_a_Remote_and_Remap_Keys) was not clear to me (*userdata SMB share* is <code>/usr/share/xbmc/system/</code>…):

> Make sure you have a proper remote.xml in your keymaps folder which is inside your userdata SMB share (or located at /storage/.xbmc/userdata/keymaps/remote.xml)

With OpenELEC the location of *userdata SMB share* is <code>/usr/share/xbmc/system/Lircmap.xml</code>.

#### Get function name for context menu

The function name can be read from <code>/usr/share/xbmc/system/Lircmap.xml</code>. Search for <code>ContextMenu</code>. This should look like:
``` xml /usr/share/xbmc/system/Lircmap.xml
...
<title>ContextMenu</title> 
...
```
The function name for context menu is <code>title</code>.

#### Create customized Lircmap.xml

Create <code>/storage/.xbmc/userdata/Lircmap.xml</code>:
``` sh
$ nano /storage/.xbmc/userdata/Lircmap.xml
```
and add the following content (adapt to your needs):
``` xml /storage/.xbmc/userdata/Lircmap.xml
<lircmap>
	<remote device=”devinput”>
		<title>KEY_INFO</title>
	</remote>
</lircmap>
```

That’s it. Restart Raspberry Pi and the context menu should now work.
