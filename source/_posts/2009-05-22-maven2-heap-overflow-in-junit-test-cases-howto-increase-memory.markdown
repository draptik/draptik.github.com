---
author: draptik
comments: true
date: 2009-05-22 18:47:51
layout: post
slug: maven2-heap-overflow-in-junit-test-cases-howto-increase-memory
title: 'Maven2 Heap Overflow in JUnit test cases: Howto increase memory'
wordpress_id: 119
categories:
- programming
---

For the past few days I was wondering why Maven's install gave me a Heap Overflow exception on JUnit tests on some of my machines. I tried increasing the memory by using the environment variable MAVEN_OPTS, by passing the option "-Xmx512m" to the JVM through Eclipse and from the command line. All to no avail.

Then I found this [blog entry](http://www.keith-chapman.org/2008/06/increasing-memory-of-junit-testcases-in.html) by Keith Chapman. And it worked! Here's the solution in short:

The JUnit tests ignore the environment variable MAVEN_OPTS. You have to tell Maven's surefire plugin to increase memory. Add this to your pom.xml file:
[sourcecode language='xml']

  org.apache.maven.plugins
  maven-surefire-plugin
  
    pertest
    -Xms512m -Xmx512m
    false
    false
  

[/sourcecode]
