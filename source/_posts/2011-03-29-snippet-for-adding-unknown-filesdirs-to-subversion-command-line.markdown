---
author: draptik
comments: true
date: 2011-03-29 19:58:31
layout: post
slug: snippet-for-adding-unknown-filesdirs-to-subversion-command-line
title: Snippet for adding unknown files/dirs to Subversion (command line)
wordpress_id: 403
categories:
- programming
---

```
svn add `svn st | grep "^?" | awk '{print $2}'`
```
