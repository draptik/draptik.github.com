---
layout: post
title: "RPi/XBMC: Keeping content in sync"
date: 2013-05-25 19:24
comments: true
categories: 
- Raspberry Pi
---

If you have multiple [Rasperry Pi](http://www.raspberrypi.org/)/[XBMC](http://xbmc.org/) clients and want to

- 	keep your "history" in sync (watch a movie in room1, stop, start the same movie in room2 and resume)
- 	do not want to individually update every RPi/XBMC client if the content of your data storage changes 

then this post might be for you.

[The XBMC Wiki decribes this in detail](http://wiki.xbmc.org/index.php?title=HOW-TO:Share_libraries_using_MySQL).
This post just describes an actual implementation using Raspberry Pi clients and server.

To do this you will need a MySQL database on a machine in your network which is always accessible from your Raspberry Pi/XBMC clients (called `RPI-Client` from now on). 
Since I didn't have a machine like this in my network I decided to buy another RPi for this purpose (referred to as `RPi-Server` from now on).

**The content of the MySQL database only contains metadata for each entry, not the actual data** (information like (a) the file path and (b) the last timestamp within the movie you were watching).

Here is a diagram showing my network setup for two Rasperry Pi clients running XBMC:

``` sh network
+---+
|   |   |---------------------------------|
|   |---| RPi-Client-1 (XBMC living room) |
|   |   |---------------------------------|
|   |
| L |   |------------------------------|
| A |---| RPi-Client-2 (XBMC bed room) |
| N |   |------------------------------|
|   |
|   |   |-----------------------|
|   |---| RPi-Server (Raspbian) |
|   |   |-----------------------|
|   |
|   |   |-----|
|   |---| NAS |
|   |   |-----|
+---+
```

- The clients `RPi-Client-X` are default [OpenELEC](http://openelec.tv/) installations.
- The server `RPi-Server` is a default [Raspbian](http://www.raspbian.org/) installation.

## RPi-Server Setup

The `RPi-Server` has to provide a MySQL database which can be accessed from each `RPi-Client-X`. 
Just follow the [simple setup instructions on the XBMC-Wiki](http://wiki.xbmc.org/index.php?title=HOW-TO:Share_libraries_using_MySQL/Setting_up_MySQL).

This is how you can check the status of your MySQL server:

`
rpi-server$ sudo /etc/init.d/mysql status
`

## RPi-Client Setup

Each `RPi-Client-X` needs to be configured to use the `RPi-Server` database.

`RPi-Server: 192.168.179.36`

``` xml advancedsettings.xml
<advancedsettings>
  <videodatabase>
        <type>mysql</type>
        <host>192.168.179.36</host>
        <port>3306</port>
        <user>xbmc</user>
        <pass>xbmc</pass>
  </videodatabase>
  <musicdatabase>
        <type>mysql</type>
        <host>192.168.179.36</host>
        <port>3306</port>
        <user>xbmc</user>
        <pass>xbmc</pass>
  </musicdatabase>
</advancedsettings>
```

Note: 
The location of the file `advancedsettings.xml` might very depending on your XBMC installation. 
For OpenELEC the file is located at `/storage/.xbmc/userdata/advancedsettings.xml`.

## XBMC Library Update (optional)

Once the above setup works you can install the [XBMC Addon:Library Auto Update](http://wiki.xbmc.org/index.php?title=Add-on:XBMC_Library_Auto_Update). 
This plugin provides a GUI to setup a cron job to sync the content your `RPi-Client-X` has acces to (i.e. your NAS) with your MySQL database.
Just install it on *one* of your `RPi-Client-X`.

## MySQL Backup (optional)

A bit overkill, but I also backup the MySQL Database from the `RPi-Server` to my NAS using [automysqlbackup](http://sourceforge.net/projects/automysqlbackup/).

