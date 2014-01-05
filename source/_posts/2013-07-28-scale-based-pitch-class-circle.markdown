---
layout: post
title: "Scale-based Pitch Class Circle"
date: 2013-07-28 12:16:00 +0200
comments: true
categories:
- music theory
---

{%img right http://static.harmoneye.com/music-theory/7scale-step2-4-cmaj7-chord.png 200 %}

In this article I'd like to introduce a new way visualizing chords generated from some particular scale. It is a generalization of the classic pitch class circle but in contrast it captures the chord progressions very clearly. It is a powerful tool able to visualize the natural feeling of distance between chords by their similarity and the two directions of motion. Besides the description there is a plenty of animated examples.</p>

<!--more-->

## Linear vs. circular visualization
<p>On the piano the pitches are represented by keys which are arranged linearly. The white keys are the pitches from the C diatonic (C major) scale are the black keys are their complement to the full chromatic scale. The patterns of keys are repeated for each octave. The pitches on the same position in the pattern but without regard to the octave sound similar and can be treated as equal thus creating a pitch class. In the common western music the octave consists of 12 pitch class.</p>

<p><img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-c-diatonic-scale.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-c-diatonic-scale.png" /></p>

### The C diatonic scale visualized in linear and circular way.

<p>The linear arrangement is quite illustrative (eg. compared to some wind instruments) but does not capture the fact that the pitch classes form a circular group. In simple words when a pitch class is transposed it wraps around. Thus when the octave is not important a circular visualizaiton for pitch classes is more illustrative than linear. Eg. when transpositions of a chord are visualized linearly they form several different patterns beacause the "wrapping" is not continuous. On the other hand transposing chords in a pitch class circle amounts just to a rotation with the "shape" being preserved.</p>

<p class="slider"><img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-c-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-db-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-d-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-eb-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-e-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-f-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-gb-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-g-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-ab-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-a-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-bb-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-linear-step1-b-chord.png" /></p>

<p><em>Transpositions of a major triad, 12-tone linear (an idealized part of a piano keyboard). It wraps around.</em></p>

<p class="slider"><img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-c-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-db-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-d-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-eb-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-e-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-f-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-gb-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-g-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-ab-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-a-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-bb-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-b-chord.png" /></p>

<p><em>Transpositions of a major triad, 12-tone circle. It rotates.</em></p>

<p>If we take a scale and make some melody or chords, we use a subset of the whole set of 12 pitch classes. When we generate a set of chords from a scale, eg. by stepping over the scale degrees as described in the previous article <a href="/blog/2012/generating-chords-the-math-behind/">Generating Chords - The Math Behind</a>, they are visualized by several, generally different, shapes. Then in a usual chord progression the subsequent chords often share some pitch classes, while the others are changed. The result is the visualization is still not as illustrative as it can be.</p>

<p class="slider"><img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-c-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-dm-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-em-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-f-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-g-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-am-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/12scale-step1-3-bmb5-chord.png" /></p>

<p><em>Diatonic triads, 12-tone circle</em></p>

## Scale-based circular visualization

<p>What can we do with it? In case the song or its part is based just on some scale and does not contain alterations (using pitch classes outside the scale) what about restricting the visualization just to the pitch classes from that scale? Eg. for the diatonic scale we can make a circle of just 7 instead of 12 elements. The ordering can be preserved. Now the chords of the same size generated from the scale by making uniform steps over the scale degrees have all the same shape! That's because we eliminated the pitch classes outside the scale.</p>

<p class="slider"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-3-c-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-3-dm-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-3-em-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-3-f-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-3-g-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-3-am-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-3-bmb5-chord.png" /></p>

<p><em>Diatonic triads, 7-tone circle, step 1</em></p>

<p class="slider"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-4-cmaj7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-4-dm7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-4-em7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-4-fmaj7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-4-g7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-4-am7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step1-4-bm7b5-chord.png" /></p>

<p><em>Diatonic tetrachords, 7-tone circle, step 1</em></p>

## Reordered scale-based circular visualization

<p>Still, there is something to be improved. When you play music or listen to it you may notice that the chords move back and forth or follow a longer line. For sure you may distinguish whether the movement goes in one direction or the other. You may also feel whether two chords are close or further apart. However, in the present visualization we do not see these two aspects well.</p>

<p>The solution is to follow the way the chords were generated. Eg. in the diatonic scale the common usable chords are generated with step 2 which means that we take every second scale degree. Thus we might reorder the pitch classes in the set the same way. Then the pitch classes making the chord get grouped together. A triad makes an arc of three pitch classes, a tetrachord makes an arc of size four and so on.</p>

<p class="slider"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-c-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-dm-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-em-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-f-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-g-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-am-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-bmb5-chord.png" /></p>

<p><em>Diatonic triads, 7-tone circle, step 2. Without "holes". Subsequent chords share no tones.</em></p>

<p class="slider"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-cmaj7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-dm7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-em7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-fmaj7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-g7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-am7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-bm7b5-chord.png" /></p>

<p><em>Diatonic tetrachords, 7-tone circle, step 2. Subsequent chords share one tone.</em></p>

## Examples - common chord progressions

<p>How the chords are far apart directly correlates with the number of pitch classes they share. Also there are two directions of movement on the perimeter of the circle. Just as we intuitively feel. A chord's quality can be also expressed by the angle of its "center of gravity". Note that it is different from the chord's root.</p>

### I IV I V progression

<p class="slider infinite"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-c-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-f-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-c-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-g-chord.png" /></p>

<p><em>A common country or folk progression: C, F, C, G. Notice the back-and-forth movement.</em></p>

<p class="slider infinite"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-am-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-dm-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-am-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-em-chord.png" /></p>

<p><em>A similar progression using relative minor chords: Am, Dm, Am, Em. Rotated from the previous one by one step counter-clockwise.</em></p>

### II V I progression

<p>In jazz the absolutely basic progression which is used in many variations is the II-V-I progression. Eg. in the C key it is Dm7 G7 Cmaj7. In fact there is just a movement by fifths upwards. Notice the symmetry on the diagram - the Cmaj7 and Dm7 share just one common tone, while G7 shares two common tones with the other chords.</p>

<p class="slider infinite"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-dm7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-g7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-cmaj7-chord.png" /></p>

<p><em>The II-V-I progression (Dm7 G7 Cmaj7)</em></p>

<p>Again there is a commonly utilized minor variant of the II-V-I progression.</p>

<p class="slider infinite"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-bm7b5-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-em7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-am7-chord.png" /></p>

<p>In practice this progression gets altered a bit. Especially Em7 is altered to E7 to act as a dominant chord in the minor II-V-I progression Bm7b5 E7 Am7. The raised third, Ab, resolves to the A tone in Am7. The other point of view on this is that the chords are generated from the harmonic minor scale instead of the diatonic one.</p>

### Circular progression

<p>In fact those two progressions are part of a more interesting progression that is also commonly used in practice - Cmaj7 Fmaj7 Bm7b5 Em7 (or altered to E7) Am7 Dm7 G7 Cmaj7. It can be very naturally visualized as an arc moving around in a single direction. Note that the progression ends with the II-V-I progression mentioned before. All subsequent chords share exactly two tones.</p>

<p class="slider infinite"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-cmaj7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-fmaj7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-bm7b5-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-em7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-am7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-dm7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-g7-chord.png" /></p>

<p><em>A common jazz progression with a circular movement.</em></p>

## Complements - upper structure

<p>When we take a chord its complementary pitch classes make another chord with is farthest apart on the circle. We could take a tetrachord and combine with its complementary triad. What we obtain would be a 13 chord comprising of all the pitch classes in the scale. Such a triad is than called the upper structure.</p>

<p>There's no reason to use this visualization tool for any other scale than the diatonic one. This can be equally useful for chords generated from harmonic minor or even quartal chords generated from the chromatic scale. In fact the widely known circle of fifths is a special case of this for the chromatic scale and step size 7.</p>

<p class="slider"><img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-4-cmaj7-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-3-dm-chord.png" /> <img alt="image" src="http://static.harmoneye.com/music-theory/7scale-step2-diatonic-scale.png" /></p>

## Caveats

<p>Although this visualization is very suitable for chords generated from the same scale the circle is built upon it falls short with alterations, ie. pitch clases from the scale's complement. There is not a straightforward way to add the pitch classes complementary to the selected scale since the pitch classes inside the scale are reordered by scale degrees.</p>

## Final notes

<p>The illustrations have been created using the free interactive web application <a href="http://bzamecnik.github.io/music-theory-toolbox/">Music Theory Toolbox</a>, that I'm developing. Currently it only supports the 12-tone circle but I hope I'll extend it soon to the described scale-basic circular visualization. Feel free to play with it and send me your oppinion how it could be improved.</p>

<p>Also don't forget to visit <a href="http://harmoneye.com/?utm_source=blog&amp;utm_campaign=scale_circle&amp;utm_medium=blog">HarmonEye</a>, the real-time tone and chords visualization apllication. <del>To appreciate that you read this sooo looong article to the end you might use the <code>SCALE-CIRCLE</code> discount coupon and get it for just $2. It works until the end of August 2013.</del> Thanks and enjoy!</p>

<p><a href="http://harmoneye.com/?utm_source=blog&amp;utm_campaign=scale_circle&amp;utm_medium=blog"><img alt="image" src="http://harmoneye.com/img/harmoneye-e-major-7.png" width="300" /></a></p>

<link href='//netdna.bootstrapcdn.com/font-awesome/3.2.1/css/font-awesome.css' rel='stylesheet' type='text/css'>
<link href="http://static.harmoneye.com/jquery-image-slider/slider.css" media="screen" rel="stylesheet" type="text/css" />
<script src="http://static.harmoneye.com/jquery-image-slider/slider.js" type="text/javascript"></script>