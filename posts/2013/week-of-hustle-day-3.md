<!--
.. title: Week of Hustle - Day 3
.. slug: week-of-hustle-day-3
.. date: 2013-12-03 16:25:00+01:00
.. tags: harmoneye,week of hustle,jfdi
.. category: harmoneye
.. link: 
.. description: 
.. type: text
-->

Worries are gone, euphoria is back! After today's handful of hacking it finally works, albeit on a very rudimental level. I've managed to get the audio input code working and ported the computation code to Android along with all the libraries. Thanks to the fact there's no native code, just Java, the transition was quite smooth. There's no fancy OpenGL graphical visualization yet, just a text output showing a bar graph of the intensisites of each tone bin in an octave. But when I sing or whistle to it, it shows the expected pattern.

<!-- TEASER_END -->

<a href="https://twitter.com/bzamecnik/status/407922221277904896/photo/1" title="The original tweet"><img alt="image" src="http://media.tumblr.com/e1c0f6c4a9259a9d59214cb46a52b114/tumblr_inline_mx91zfxcjI1s2upa7.jpg" /></a>

Also I was affraid of memory and CPU consuption on a phone. Well, if the quality parameters are reduced, it is quite fast, but for parameters normal on desktop it needs optimization. On the other hand I'm aware that the current algorithm is not the most memory efficient and there are several obvious optimizations. However, "premature optimization is the root of all evil", so let's have a working product first.

Here's a video showing the app in action:


<div class="embed-video-container"><iframe src="https://www.youtube.com/embed/HoPOUV4EAeo"></iframe></div>

So let's look forward to the following days until HarmonEye for Andoid gets a nicer graphical look. It'll be fun porting the old OpenGL code to OpenGL ES.