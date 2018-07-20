---
layout: post
title: "Week of Hustle - Day 1"
date: 2013-12-01 16:41:00 +0100
comments: true
categories:
- harmoneye
- week of hustle
- jfdi
---
Over the next week I'll be trying hard to port the <a href="http://harmoneye.com/">HarmonEye </a>application from desktop to Android. You'll be able to watch the progress and experiences in this blog. And hopefully in a week there will be a app with most basic features available for downloading to your mobile device.

<!--more-->

I've been watching the communities of product bootstrapping, one of which is Justin Jackson's <a href="http://jfdi.bz/" title="Just F***ing Do It">JFDI</a>. Fortunately, new registration spots have been open recently, so I decided to join. Moreover, today has started the second round of so called <a href="http://justinjackson.ca/why-you-need-a-week-of-hustle/">Week of Hustle</a>, a nice way to get rid of distractions and let people motivate each other (read: "kick oneself in the ass") to build and launch a project within a single week sprint.

I've already started porting it several months ago and got to a point of having basic audio input, very simple processing (just an average volume) and simple rendering (a moving graph). Since then I've felt very tired and I waited for the day D.

As I left the job I had to return the MacBook and I decided to buy a new 2013 Retina one. What a coincidence, on 29th November, the last day in the work, Apple held the Black Friday and the MacBook can be had for a decent price. The downside (compared to buying it earlier) is that it will be delivered by around 10th December.

So there's a week and half to suffer from a painfully slow (albeit just a year-old) home computer in order to get to work done... At least it's a way to appreciate fast a SSD, 4x more RAM and a friendlier OS :) Anyway, what have we been using ever since? Are we just too spoilt or the system always sucked? Anyway, I have to prepare the development environment from scratch and on a different platform.

<strong>The goal features</strong>

- capture the audio from microphone
- process it - compute the volume for each tone bin
- visualize it - in a circle (a bin for each tone)
- allow play/pause

<strong>Roadmap</strong>

- prepare the development environment (including a testing phone)
- run the existing desktop application and basic Android port
- get to work rendering via OpenGL ES
- port the circle visualization into OpenGL ES (HarmonEye desktop uses some legacy functions not present in ES)
- port the buffering code
- get the FFT library working
- port the processing code (CQT and the auxilliary filters)
- implement single-octave CQT for better efficiency (both memory and CPU)
- implement the controls (play/pause)
- build the release package
- publish on Google Play

<strong>Today's summary</strong>

The environment is ready, as well as both existing applications. Both are running, however I'm suspicious that the desktop one has some data format problems as the visualization results don't seem alright. I'll have to debug it a bit.

For months the testing HTC Desire had a problem - an annoying pupup message with a stacktrace appeard about twice a minute. I wasn't able to fix it and since I'm using an iPhone recently I've almost gave up. What eventually helped was to remove all the sync accounts! So testing will hopefully be ok.

Also as you can see the blog starts to be alive again. I've written a motivation behind all this doing - [Refactoring The Path To Freedom](/blog/2013/refactoring-the-path-to-freedom/).

Last but not least if you find this interesting and would like to receive interesting information about music theory and visualization, feel free to <a href="http://eepurl.com/xX1M1" target="_blank">subscribe to my newsletter</a><span>.</span>