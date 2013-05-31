---
author: draptik
comments: true
date: 2011-09-23 22:09:05
layout: post
slug: app-config-order-matters
title: 'App.Config: Order matters'
wordpress_id: 443
categories:
- programming
---

Note to self:

Within an App/Web.config in .NET... The startup node must be the last node...

Example: Create a plain WPF application from Visual Studio 2010, add an App.Config file, and try building Option 1 and Option 2 below.

Option 1:

``` xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup><supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/></startup>
  <configSections />
</configuration>
```

This throws: `The type initializer for 'System.Windows.Application' threw an exception.`

Option 2:

``` xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections />
  <startup><supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/></startup>
</configuration>
```

Fine.

Do I want to read 2.5MB of `C:\Program Files (x86)\Microsoft Visual Studio 10.0\Xml\Schemas\DotNetConfig.xsd`? No.
