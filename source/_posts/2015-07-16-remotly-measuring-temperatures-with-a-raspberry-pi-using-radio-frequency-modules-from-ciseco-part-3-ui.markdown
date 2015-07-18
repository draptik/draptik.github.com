---
layout: post
title: "Remotly measuring temperatures with a Raspberry Pi using radio frequency modules from Ciseco (Part 3: UI)"
date: 2015-07-16 21:07:12 +0200
comments: true
categories: [Raspberry Pi, IoT, flotchart, charting, javascript]
---
[Part 1](/blog/2015/07/10/remotly-measuring-temperatures-with-a-raspberry-pi-using-radio-frequency-modules-from-ciseco-part-1-hardware/) describes how to setup the hardware, [part 2](/blog/2015/07/10/remotly-measuring-temperatures-with-a-raspberry-pi-using-radio-frequency-modules-from-ciseco-part-2-software/) describes how to to record/persist the sensor information.

In this post I'll describe how to display the data.

My primary goal was *explorative data visualization*. For this purpose I decided to show 2 plots:

- an overview plot showing the past 14 days
- and a detail plot, showing the selection of the overview plot

The detail plot can be dragged and the overview plot has a selection region which can be resized and dragged. 
Changes to either plot are reflected in the other. 
Mouse movement in the detail plot updates the legend.

You can test the website at 
[http://rpi-temperatures-website-demo.divshot.io/](http://rpi-temperatures-website-demo.divshot.io/)

{% img /images/posts/rpi_temperatures/screenshot-demo.png %}

## Technologies

- Backend: Nodejs/Express as web server
- Display logic: Client side Javascript ([flotchart](http://www.flotcharts.org/) (HTML canvas))

## Backend: Node/Express

## Frontend: Javascript


You can download the source code at

https://github.com/draptik/rpi-temperature-website/tree/v1.0
