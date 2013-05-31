---
author: draptik
comments: true
date: 2011-07-09 01:04:48
layout: post
slug: antivir-regression-bug-makes-visual-studio-2010-unusable
title: Antivir regression bug makes Visual Studio 2010 unusable
wordpress_id: 426
categories:
- programming
---

The current version of [Antivir Personal](http://www.avira.com/en/for-home) slows down Visual Studio 2010 making it unbearable to develop ASP.NET applications.

Debugging with active Antivir: appr. **5min** for an empty Default.aspx page to display.

Debugging with deactivated Antivir: **a few seconds** for an empty Default.aspx page to display.

Until Antivir fixes this bug I will give [Microsoft Security Essentials](http://www.microsoft.com/security_essentials/default.aspx?mkt=de-de) a try.

Regression because this has been addressed and fixed in December 2010:

[Antivir FAQ (English)](http://www.avira.com/en/support-for-free-faq-detail/faqid/805)

[Antivir FAQ (Deutsch)](http://www.avira.com/de/support-for-free-faq-detail/faqid/805)

This applies to the following version of Antivir:

    
    Produktversion 10.2.0.696 29.06.2011
    Suchengine 8.02.06.04 06.07.2011
    Virendefinitionsdatei 7.11.11.27 07.07.2011
    Control Center 10.00.12.31 28.06.2011
    Config Center 10.00.13.20 28.06.2011
    Luke Filewalker 10.03.00.07 28.06.2011
    AntiVir Guard 10.00.01.59 28.06.2011
    Filter 10.00.26.09 28.06.2011
    AntiVir WebGuard 10.01.09.00 28.06.2011
    Planer 10.00.00.21 02.05.2011
    Updater 10.00.00.39 21.06.2011
