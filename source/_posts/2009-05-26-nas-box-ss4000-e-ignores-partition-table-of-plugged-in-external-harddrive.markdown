---
author: draptik
comments: true
date: 2009-05-26 23:54:45
layout: post
slug: nas-box-ss4000-e-ignores-partition-table-of-plugged-in-external-harddrive
title: NAS-Box SS4000-E ignores partition table of plugged-in external harddrive
wordpress_id: 136
categories:
- linux
---

Intel's NAS Box SS 4000-e ignores the partition table of external usb-drives if the latter is connected directly to the NAS-Box. Not nice, because my external USB-Drive is split into two FAT32 partitions.

This is not reflected by the "mount" command:
`
 root# mount
   ...snipped some lines...
/dev/sdf1 on /nas/usbdisk1 type vfat  (rw,nodiratime,gid=11578,fmask=0000,dmask=0000)
`

I'll have to copy the data via intranet (slower than via USB).
