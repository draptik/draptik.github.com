---
author: draptik
comments: true
date: 2009-04-19 03:15:45
layout: post
slug: accessing-further-windows-partitions-with-portable-ubuntu
title: Accessing further (Windows) Partitions with Portable Ubuntu
wordpress_id: 71
categories:
---

If you've followed my blog on how to install [Portable Ubuntu to your USB Stick](http://draptik.wordpress.com/2009/04/13/first-experiences-with-portable-ubuntu-and-upgrading-from-804-to-810/), you might have noticed people asking "how do I access my other Windows partitions by default" or "How do I access partition X?".

I've already answered this question in a reply to a post, but I will describe it here in detail.

Let us assume you have two partitions on your Windows machine: "C:" and "E:". And you want to have access to both partitions once plugging in your Portable Ubuntu USB Stick.



	
  1. Add your changes to `config\portable_ubuntu.conf ` by adding a line`
cofs2=e:\`

	
  2. Add your changes to the `/etc/fstab` file by adding a line which almost duplicates the line containing cofs1:
`cofs2 /mnt/E cofs user,dmask=0777,fmask=0666 0 0`


The order in which you do 1. and 2. does not matter: you have to restart your Portable Ubuntu again for it to work.
