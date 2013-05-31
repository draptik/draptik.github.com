---
author: draptik
comments: true
date: 2010-12-08 21:47:42
layout: post
slug: nhibernate-code-completion-intellisense-for-visual-studio
title: NHibernate Code Completion (IntelliSense) for Visual Studio
wordpress_id: 360
categories:
- programming
---

Enable code completion (IntelliSense) in Visual Studio by copying the XSD files from NHibernate to your Visual Studio's XML folder.

For example (NHibernate 2.0.1):

Files to copy:

`${nhibernate-location}\NHibernate\2.0.1\nhibernate-configuration.xsd
${nhibernate-location}\NHibernate\2.0.1\nhibernate-mapping.xsd
`

Destination:

`C:\Programme\Microsoft Visual Studio 10.0\Xml\Schemas`


