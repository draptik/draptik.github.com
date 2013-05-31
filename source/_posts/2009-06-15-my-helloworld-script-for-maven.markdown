---
author: draptik
comments: true
date: 2009-06-15 22:26:55
layout: post
slug: my-helloworld-script-for-maven
title: My HelloWorld script for Maven
wordpress_id: 167
categories:
- programming
---
I'm still in the learning phase of using [Maven](http://maven.apache.org/), so bare with me. Here is my script to create an executable HelloWorld-jar file using Maven (it requires the file sample-pom.xml listed after the script):

``` sh
#!/bin/bash
#
# Description: Create a simple Java project using Maven.
#
# Status: Works
#
# Author: draptik
#
# Created: Mon Jun 15 21:17:24 2009
#
# REQUIREMENTS
# ============
#
# 1. Maven
#
# 2. This script requires the file "sample-pom.xml"!!!
#
# 3. The "sample-pom.xml" file should be located in the same folder as
# this script.
#
# USAGE
# =====
#
# 1. Copy this script and "sample-pom.xml" to a newly created test
# folder (ie ~/tmp/maventesting/).
#
# 2. Run this script
#
# DETAILS
# =======
#
# This script creates a "quickstart" Maven project, then overwrites
# the created pom.xml with something more sensible (including some
# useful Maven plugins), and runs "java -jar .jar".

## User settings
companyName="org.foo"
appName="my-app"

## Static file
samplePom="sample-pom.xml"
pdinfo="[PDINFO] "

## Delete old builds
echo "$pdinfo" "====== REMOVING PREVIOUS BUILDS ====================="
echo "$pdinfo" "rm -rf \"$appName\""
rm -rf "$appName"

## Create Maven skeleton
##
## NOTE: You can exchange the first 2 lines with:
##
## mvn archetype:create \
##         -DarchetypeGroupId=org.apache.maven.archetypes \
##
## This will result in a warning that "archetype:create" is
## deprecated. The warning states that you should use
## "archetype:generate". This archetype is interactive by default. To
## skip the interactive part and choose the default (="quickstart")
## you have to pass the option "-Dinteractive=false" to Maven.
echo "$pdinfo" "====== MAVEN CREATION ================================"
echo "$pdinfo" ""
mvn archetype:generate \
	-DinteractiveMode=false \
	-DarchetypeGroupId=org.apache.maven.archetypes \
	-DgroupId="$companyName" \
	-DartifactId="$appName"

## Show the diff between generated pom.xml and sample-pom.xml
echo "$pdinfo" "====== DIFF BETWEEN GENERATED AND MY POM.XML ========"
echo "$pdinfo" "diff -u \"$appName\"/pom.xml \"$samplePom\" "
diff -u "$appName"/pom.xml "$samplePom" 

## Overwrite generated pom.xml
echo "$pdinfo" "====== OVERWRITING GENERATED POM.XML ================"
echo "$pdinfo" "cp  \"$samplePom\" \"$appName\"/pom.xml"
cp  "$samplePom" "$appName"/pom.xml

## Move to newly created project
cd "$appName"

## Make an executable jar file
echo "$pdinfo" "====== MAKING THE FINAL JAR FILE ===================="
echo "$pdinfo" "mvn package"
mvn package

## Move to target folder
cd target

## Run the Hello-World example
echo "$pdinfo" "====== TESTING THE JAR FILE =========================="
echo "$pdinfo" "java -jar $appName-1.0-SNAPSHOT.jar..."
echo "$pdinfo" "If the next line does not read \"Hello World!\", something is wrong"
java -jar "$appName"-1.0-SNAPSHOT.jar
```

Here is the file `sample-pom.xml`:

``` xml
  4.0.0
  org.foo
  my-app
  jar
  1.0-SNAPSHOT
  my-app
  http://maven.apache.org
  

  
    org.apache.maven.plugins
    maven-compiler-plugin
  
  
    org.apache.maven.plugins
    maven-eclipse-plugin
  
  
    org.apache.maven.plugins
    maven-jar-plugin
    
      
        
          true
          org.foo.App
        
      
    
  
  
    org.apache.maven.plugins
    maven-surefire-plugin
  


  
    
      junit
      junit
      3.8.1
      test
    
  

```

