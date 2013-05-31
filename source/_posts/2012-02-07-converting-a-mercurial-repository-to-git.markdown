---
author: draptik
comments: true
date: 2012-02-07 00:40:01
layout: post
slug: converting-a-mercurial-repository-to-git
title: Converting a Mercurial repository to Git
wordpress_id: 478
categories:
- programming
---

Note to self:

Converting Hg repos to git using hg-fast-export.

Installation:

`
$ sudo aptitude install hg-fast-export
`

Usage:

``` sh
$ cd new_git_dir

$ git init

$ hg-fast-export -r <hg-repo>
```

Works with:

git version 1.7.5.4

hg version 2.1
