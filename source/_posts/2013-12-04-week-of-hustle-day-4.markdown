---
layout: post
title: "Week of Hustle - Day 4"
date: 2013-12-05 18:42:00 +0100
comments: true
categories:
- harmoneye
- week of hustle
- jfdi
---
Since the music processing code is quite ready, now it's the time for the nice graphical UI - the visualization that gives sense to the numbers. The goal is clear - to port the existing visualization where each pitch class (a tone in an arbitrary octave) is represented by a circular sector. The intensity is represented by the sector's length and color. In fact each tone is divided into multiple bins for greater precision.

<!--more-->

<img alt="image" src="http://media.tumblr.com/9118d2c603047808dc31a4bc4870b795/tumblr_inline_mxcwv4t26S1s2upa7.png" />

The visualization should be done via OpenGL, since it's updated after each computation, ie. many times per second. Mobile devices such as Android or iOS phones support only OpenGL ES, a variant of OpenGL adjusted for this kind of devices. Compared to OpenGL it lack some legacy stuff, such as calls like `glBegin()`, ...draw something.., `glEnd()`. This means that the desktop OpenGL drawing code must be rewritten.

As for today, I had to familiarize a little bit with OpenGL ES, shaders etc. Well, I've done this stuff on the university and a part of the thesis project was written in OpenGL with shaders, but that's at least two years ago :)

Among other things, I've found a nice tool to write and test fragment shaders on the fly - <a href="http://glsl.heroku.com/">glsl.heroku.com</a>. Just write the code and if it compiles well it automatically draws on the background. It runs via WebGL in your browser. The future is here! Nice :)

Unfortunately, today I've focused on doing everything in a shader, which probably wasn't the best idea. Drawing shapes like lines or parametric curves and drawing them with antialiasing isn't the easiest task to begin with when writing a fragment shader. Well, it isn't impossible, but it can be done more easily with geometry like triangles or lines. The only thing is to explore the vertex buffers and index buffers - a more recent way of producing geometry than just calling `glVertex()`. Well, it's not the most recent like geometry shaders, but let's KISS for now.
