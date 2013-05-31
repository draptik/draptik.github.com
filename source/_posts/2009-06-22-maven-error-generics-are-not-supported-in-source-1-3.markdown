---
author: draptik
comments: true
date: 2009-06-22 19:57:32
layout: post
slug: maven-error-generics-are-not-supported-in-source-1-3
title: 'Maven Error: generics are not supported in source 1.3'
wordpress_id: 217
categories:
- linux
---

Just stumbled across this error while trying to compile a simple Java Maven project:

`Maven Error: generics are not supported in source 1.3`

It turns out that maven uses the Java compiler 1.3 (!) by default. So you have to add the configuration settings to your pom.xml file...

See [Hobione's Blog entry](http://hobione.wordpress.com/2009/03/04/myeclipse-maven-errorgenerics-are-not-supported-in-source-13/) on the same issue.
