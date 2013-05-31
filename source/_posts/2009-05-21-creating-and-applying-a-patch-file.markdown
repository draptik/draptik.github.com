---
author: draptik
comments: true
date: 2009-05-21 03:00:26
layout: post
slug: creating-and-applying-a-patch-file
title: Creating and applying a patch file
wordpress_id: 103
categories:
---

This is a simple example shell script demonstrating how to apply a patch file.


``` sh
#!/bin/sh
#
# Description: Simple demo of creating and
# applying a patch file.
#
# Author: draptik
#
## TODO: Make sure you don't have a directory by 
## this name
DEMODIR="$HOME/tmp/diff-patch-demo-draptik"
ORIGDIR="$DEMODIR/original-stuff"
APPLYDIR="$DEMODIR/patch-usage"
#
## Cleanup before use
rm -rf "$DEMODIR"
mkdir "$DEMODIR"
cd "$DEMODIR"
#
## Create directories
mkdir $ORIGDIR
mkdir $APPLYDIR
#
## Switch directory
cd $ORIGDIR
#
## Create original file (the file you want to patch)
ORIGCODE="mycode.txt"
echo "This is the old content" > $ORIGCODE
## Create file with your changes
NEWCODE="mycode.new.txt"
echo "THIS IS THE NEW CONTENT" > $NEWCODE
#
## ============================
# 1. CREATE A PATCH
## ============================
#
## Make a diff between the files and save the
## result to $PATCHFILE
##
## You can use different diff options:
##     "-u" (unified)
##  OR
##     "-c" (context).
##
## The "-u" option is specific to GNU.
## PDTODO: Write about different diff formats.
PATCHFILE="patch-mycode"
echo ""
echo "***************************"
echo "*** YOU ARE NOW IN FOLDER: \"$PWD\""
echo "*** DIFF BETWEEN\n***\t\"$PWD/$ORIGCODE\"\n*** AND\n***\t\"$PWD/$NEWCODE\":"
echo "***************************"
diff -u $ORIGCODE $NEWCODE | tee $PATCHFILE
#
## Switch directory
cd "$DEMODIR"
#
## Copy the unpatched original file ($ORIGCODE) and 
## the $PATCHFILE to different directory ($APPLYDIR).
cp $ORIGDIR/$ORIGCODE $APPLYDIR
cp $ORIGDIR/$PATCHFILE $APPLYDIR
#
#
## ============================
# APPLY A PATCH
## ============================
#
## Apply the patch to a copy of the original file
##
## The "--backup" option will create a file 
## "$ORIGCODE.orig" as backup. 
## You can drop this option.
##
cd $APPLYDIR
echo ""
echo "***************************"
echo "*** YOU ARE NOW IN FOLDER: \"$PWD\" ..."
echo "*** NOW PATCHING WITH COMMAND \"patch --backup -p0 < $PATCHFILE\"..."
echo "***************************"
patch --backup -p0 < $PATCHFILE
## patch -p0 < $PATCHFILE
#
## Show that the file has been patched correctly:
cd ..
echo ""
echo "****************************"
echo "*** YOU ARE NOW IN FOLDER: \"$PWD\""
echo "*** DIFF BETWEEN\n***\t\"$ORIGDIR/$ORIGCODE\"\n*** AND\n***\t\"$APPLYDIR/$NEWCODE\":"
echo "****************************"
diff -u $ORIGDIR/$ORIGCODE $APPLYDIR/$ORIGCODE
```

