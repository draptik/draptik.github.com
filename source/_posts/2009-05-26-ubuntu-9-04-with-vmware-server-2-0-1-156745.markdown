---
author: draptik
comments: true
date: 2009-05-26 20:20:42
layout: post
slug: ubuntu-9-04-with-vmware-server-2-0-1-156745
title: Ubuntu 9.04 with VMware-server-2.0.1-156745
wordpress_id: 97
categories:
- linux
---

I have used VMware for some years. There are many alternatives. I won't get into that ;-)

I always download the tar.gz file from VMware directly.

After unpacking the file and running the install script I encountered the problem vsock ([German UbuntuWiki page](http://wiki.ubuntuusers.de/VMware_Server_2#Konfiguration-des-vsock-Modul-schlaegt-fehl)).

Then I ran into the often reported error message: "Unable to make a vsock module that can be loaded in the running kernel"

I can confirm that patching vmware's configuration script works with
``` sh
$ uname -a
Linux golem 2.6.28-12-generic #43-Ubuntu
```
and
`VMware-server-2.0.1-156745.i386.tar.gz`

Here is the patch (mind to line breaks):
``` diff
--- /usr/bin/vmware-config.pl.orig	2008-11-28 12:06:35.641054086 +0100
+++ /usr/bin/vmware-config.pl	2008-11-28 12:30:38.593304082 +0100
@@ -4121,6 +4121,11 @@
return 'no';
}
+  if ($name eq 'vsock') {
+    print wrap("VMWare config patch VSOCK!\n");
+    system(shell_string($gHelper{'mv'}) . ' -vi ' . shell_string($build_dir . '/../Module.symvers') . ' ' . shell_string($build_dir . '/vsock-only/' ));
+  }
+
print wrap('Building the ' . $name . ' module.' . "\n\n", 0);
if (system(shell_string($gHelper{'make'}) . ' -C '
. shell_string($build_dir . '/' . $name . '-only')
@@ -4143,6 +4148,10 @@
if (try_module($name, $build_dir . '/' . $name . '.o', 0, 1)) {
print wrap('The ' . $name . ' module loads perfectly into the running kernel.'
. "\n\n", 0);
+      if ($name eq 'vmci') {
+	print wrap("VMWare config patch VMCI!\n");
+	system(shell_string($gHelper{'cp'}) . ' -vi ' . shell_string($build_dir.'/vmci-only/Module.symvers') . ' ' . shell_string($build_dir . '/../'));
+      }
remove_tmp_dir($build_dir);
return 'yes';
}
```

Apply the patch by
`
sudo patch --backup -p0 < vmware-patch.txt`

See my short shell script in this [post](http://draptik.wordpress.com/2009/05/21/creating-and-applying-a-patch-file/) on how applying a patch file works.
