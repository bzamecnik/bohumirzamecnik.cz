---
layout: post
title: "Harmonic minor scale in action - Cancion del Mariachi"
date: 2011-10-01 00:17:00 +0100
comments: true
categories:
- music theory
- harmonic analysis
---

Let’s start the blog with with some brief, yet interesting, thoughts. I’ve ever liked the distinct sound of latin or spanish music which is full of passion. Also I’ve wondered what is the key to it. As an example take the song [Cancion del Mariachi by Los Lobos](http://www.youtube.com/watch?v=TY3F5A9L3V0).

<!--more-->

When we look at the chords of the song we can clearly see (or rather hear) the following:

```
Em / B7 / B7 / Em
Am / Em / B7 / Em
```

But what does it mean? The key is the usage of the harmonic minor scale. Simply said, it is a natural minor scale where the flat 7th is risen a half tone to the natural 7th. Or when numbering each half tone in an octave with numbers from `0` to `12` (as so called pitch classes) it can be expressed as: `[0, 2, 3, 5, 7, 8, 11]`. I love the expressive visual analogy to a clock. Click on the links below images to show an interactive pitch class set calculator (the screenshots are from an old version of this application, now it’s much nicer).

<img src="{% asset_path c-natural-minor-scale.png %}" alt="C natural minor scale">

[C natural minor scale](http://bzamecnik.github.io/music-theory-toolbox/?pcs=0,2,3,5,7,8,10)

<img src="{% asset_path c-harmonic-minor-scale-vs-natural.png %}" alt="C natural harmonic scale">

[C harmonic minor scale](http://bzamecnik.github.io/music-theory-toolbox/?pcs=0,2,3,5,7,8,11) (compared to the natural minor scale)

Given a scale we can generate some chords from it. We can make a stencil and select the 1st, 3rd, 5th tone in the scale (so that there is always a one tone gap between neighbor tones). This is called a *triad*, eg. `[0, 3, 7]`. If we subsequently start from each tone of the scale (ie. shift the pattern) we get seven different chords:

```
Im
IImb5
II#5
IVm
V
VI
VIImb5
```

The roman numerals indicate the root note (ie. the shift + 1). If we add also the 7th tone to the pattern we get those full and more interesting chords:

```
ImM7
IIm7b5 (= VII half-dim)
IIM7#5
IVm7
V7
VIM7
VIIm6b5 (= VII dim)
```

The chords in bold look a bit familiar. Im looks like Em, IVm7 looks like Am and V7 looks like B7. We can see the tonal center of the song is E and the song is based on the E harmonic minor scale. So what about the chords? Im is the tonic, the steady place, the home. V7 is the dominant which drives the passion, makes the tension and produces a feeling of returning home. IVm is the subdominant which produces weaker feeling of being on a trip. Looking at the song with those glasses we can see the harmony as follows:

```
Im / V7 / V7 / Im
IVm / Im / V7 / Im
```

Compared to the major scale the pitch class `4` is lowered to `3` and pitch class `9` is lowered to `8`. This corresponds to moving natural 3rd to flat 3rd and 6th to flat 6th. Regarding the chords the flat 3 in the scale is responsible for making the I chord minor and the flat 6 makes the IV chord minor.

<img src="{% asset_path c-major-scale.png %}" alt="C major scale">

[C major scale](http://bzamecnik.github.io/music-theory-toolbox/?pcs=0,2,4,5,7,9,11)



<img src="{% asset_path c-harmonic-minor-scale-vs-major.png %}" alt="C hamonic minor scale">

[C harmonic minor scale](http://bzamecnik.github.io/music-theory-toolbox/?pcs=0,2,3,5,7,8,11) (compared to the natural major scale)

The combination of flat 3rd `[3]` and natural 7th `[11]` produces a nice pattern `[11,0,2,3]` – half tone, whole tone, half tone – also known from the melodic minor scale. Also the one and half tone interval between the flat 6th `[8]` and 7th `[11]` sounds really good. In fact the harmonic minor scale is not only specific to latin american or spanish music since the roots are from the Middle east. But that’s another story!

PS: Would you like to see the tones in the pitch class circle from you own music in real-time? Check out the [HarmonEye](http://harmoneye.com/?utm_medium=blog) desktop application. Or would you like to play with them interactively medidate over the harmony? Then try the online [music theory toolbox](http://bzamecnik.github.io/music-theory-toolbox/). Anyway thay in touch and enjoy :)
