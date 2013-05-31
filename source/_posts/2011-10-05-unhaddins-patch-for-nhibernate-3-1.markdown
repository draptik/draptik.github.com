---
author: draptik
comments: true
date: 2011-10-05 21:07:26
layout: post
slug: unhaddins-patch-for-nhibernate-3-1
title: uNhAddIns patch for NHibernate 3.1
wordpress_id: 450
categories:
- programming
---

since most NuGet packages related to [NHibernate](http://nhforge.org/Default.aspx) come with NHibernate version 3.1.0.4000 (at the time of writing), I tried rebuilding [uNhAddIns](http://code.google.com/p/unhaddins/) (commit #773) with the newer version of NHibernate.

My changes:



	
  * upgraded NHibernate to version 3.1.0.4000 (Iesi.Collections, NHibernate, NHibernate.ByteCode.Castle)

	
  * implemented new Interface method 'IsProxy' for IProxyFactoryFactory (CSLProxyFactoryFactory, ProxyFactoryFactory)


BTW: .NET is becoming more and more like Java... Who doesn't love names like 'IProxyFactoryFactory'? ;-)

I did not update any tests.

Feel free to use this code (no licence).

My patch can be found at:

[Patch at Google Code](http://unhaddins.googlecode.com/issues/attachment?aid=260000000&name=unhaddins_cloned_774.patch.zip&token=ef134e75687db99ec50f572023909835)
