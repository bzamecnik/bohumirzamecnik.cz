---
layout: post
title: "Pentatonics and major scale"
date: 2012-03-11 17:06:00 +0100
comments: true
categories:
- music theory
---

In this post we will explore modes, the pentatonic scale and its modes, modes of the major scale, the relationships between pentatonic scale modes and the major scale modes and also with the dorian bebop and chromatic scale.

<!--more-->

## Pentatonic scales

Generally, a <a href="http://en.wikipedia.org/wiki/Pentatonic_scale">pentatonic scale</a> can be any scale of size five. In practice there are several scales with certain structure that are referred to as the pentatonic scales and which are heavily used in many genres. Two of them are the minor and major pentatonic scale. In the example we use the <a href="http://en.wikipedia.org/wiki/Pitch_class">pitch class</a> sequence notation.

```
minor pentatonic: [0, 3, 5, 7, 10]
major pentatonic: [0, 2, 4, 7, 9]
```

The major pentatonic scale can be derived from the <a href="http://en.wikipedia.org/wiki/Major_scale">major scale</a> (the ionian mode) by taking away the third and seventh scale degrees.

```
ionian:           [0, 2, 4, 5, 7, 9, 11]
major pentatonic: [0, 2, 4,    7, 9    ]
```

Similarly the minor pentatonic scale can be derived from the natural <a href="http://en.wikipedia.org/wiki/Minor_scale">minor scale</a> (the aeolian mode) by taking the second and sixth scale degree.

```
aeolian:          [0, 2, 3, 5, 7, 8, 10]
minor pentatonic: [0,    3, 5, 7,    10]
```

## Modes

The concept of modes of a scale is quite simple. Multiple scales can be derived from a single scale by changing the root note by cyclic rotation of the pitch class sequence. This can be seen as restricting the permutations of the underlying (unordered) pitch class set using the ordering of the original sequence.

### Modes of the major scale

See below an example of modes of the major scale. Its modes have traditional names after some Greek tribes.

Modes of the C major scale:

```
1st mode - ionian:     [0, 2, 4, 5, 7, 9, 11]
2nd mode - dorian:        [2, 4, 5, 7, 9, 11, 0]
3rd mode - phrygian:         [4, 5, 7, 9, 11, 0, 2]
4th mode - lydian:              [5, 7, 9, 11, 0, 2, 4]
5th mode - mixolydian:             [7, 9, 11, 0, 2, 4, 5]
6th mode - aeolian:                   [9, 11, 0, 2, 4, 5, 7]
7th mode - locrian:                      [11, 0, 2, 4, 5, 7, 9]
```

### The rotation operators
For clarity we can denote the operation of rotation by two operators: `>>` for right rotation and `<<` for left rotation.

```
base_scale << rotation_steps = derived_mode
                 ionian << 1 = dorian
 [0, 2, 4, 5, 7, 9, 11] << 1 = [2, 4, 5, 7, 9, 11, 0]
```

Note that modes can be derived from any scale and also that the number of unique modes for that scale equals to the size of the scale.
### Normalizing the modes
The reason for dealing with modes stems from the difference in the root note. In practice the root note is the home from which we travel around and where we return back to. Thus each mode sounds quite differently. The quality of each mode can be better seen if we normalize it, ie. transpose it so that the root pitch class is zero.

The derived modes from a scale are indicated by the root note, so that C ionian, D dorian, or G mixylydian belong to the same scale. When we normalize the modes we get C ionian, C dorian, C mixolydian, each derived from a different scale, but having the same root note.

```
Normalized modes of the C major scale:

C ionian:     [0, 2, 4, 5, 7, 9, 11]
C dorian:     [0, 2, 3, 5, 7, 9, 10]
C phrygian:   [0, 1, 3, 5, 7, 8, 10]
C lydian:     [0, 2, 4, 6, 7, 9, 11]
C mixolydian: [0, 2, 4, 5, 7, 9, 10]
C aeolian:    [0, 2, 3, 5, 7, 8, 10]
C locrian:    [0, 1, 3, 5, 6, 8, 10]
```
### Modes of the major pentatonic scale

It has been already said that modes can be derived from any scale. So why not to explore the modes of the pentatonic scale? Let us select the major pentatonic as the base scale. There are the modes in the normalized form:

```
Modes of the major pentatonic scale (normalized):

1st mode [0, 2, 4, 7, 9 ] major pentatonic
2nd mode [0, 2, 5, 7, 10]
3rd mode [0, 3, 5, 8, 10]
4th mode [0, 2, 5, 7, 9 ]
5th mode [0, 3, 5, 7, 10] minor pentatonic

major pent. | 0 |   | 2 |   | 4 |   |   | 7 |   | 9 |    |   |
2nd mode    | 0 |   | 2 |   |   | 5 |   | 7 |   |   | 10 |   |
3rd mode    | 0 |   |   | 3 |   | 5 |   |   | 8 |   | 10 |   |
4th mode    | 0 |   | 2 |   |   | 5 |   | 7 |   | 9 |    |   |
minor pent. | 0 |   |   | 3 |   | 5 |   | 7 |   |   | 10 |   |
```

The interesting thing here is that the minor pentatonic scale is just a mode of the major one. In other words they share the same structure and can be derived from each other just by rotation. Formally written `major pentatonic << 4 = minor pentatonic`. The relation is also this: k-major pentatonic is related to `(k - 3 mod 12)`-minor penatonic, eg. the A minor pentatonic can be derived from the C major pentatonic.

Another interesting thing is the number of notes that are common between the base scale and its normalized modes:

```
1st mode | 0 |   | 2 |   | 4 |   |   | 7 |   | 9 |    |  | 5 common
4th mode | 0 |   | 2 |   |   | 5 |   | 7 |   | 9 |    |  | 4 common
2nd mode | 0 |   | 2 |   |   | 5 |   | 7 |   |   | 10 |  | 3 common
5th mode | 0 |   |   | 3 |   | 5 |   | 7 |   |   | 10 |  | 2 common
3rd mode | 0 |   |   | 3 |   | 5 |   |   | 8 |   | 10 |  | 1 common
```

## Pentatonic scales vs. major scale

Finally, equipped with the knowledge of modes and pentatonic scales we can explore the relation between the pentatonic scales and and the major scale and its modes.

What happens when we stack two pentatonic scales with different roots, ie. if we make a union of their pitch class sequences (and maintain the order)? Say the offset is 2 half-tones. Let us put them into a diagram and see.

```
C minor pent.    |_0_|   |   | 3 |   | 5 |   | 7 |   |   | 10 |   |
D minor pent.    | 0 |   |_2_|   |   | 5 |   | 7 |   | 9 |    |   |
C dorian         |_0_|   | 2 | 3 |   | 5 |   | 7 |   | 9 | 10 |   |
```

Hey! We've got C dorian! So that when you could play D dorian you can just restrict youself to C and D minor pentatonics and switch between them to make some contrast. Well, if we got the dorian, then we can also obtain the first mode, ie. the major scale itself. Indeed:

```
D minor pent.  | 0 |   |_2_|   |   | 5 |   | 7 |   | 9 |   |    |
E minor pent.  |   |   | 2 |   |_4_|   |   | 7 |   | 9 |   | 11 |
C ionian       |_0_|   | 2 |   | 4 | 5 |   | 7 |   | 9 |   | 11 |
```

And what about switching between minor and major pentatonic of the same root? The bluesmen do this quite often with the blues scales (which are just pentatonics with a single additional note). Note that the C major pentatonic is a mode of to the A minor pentatonic, so that it is similar to the previous situation.

```
C minor pent.    |_0_|   |   | 3 |   | 5 |   | 7 |   |   | 10 |   |
C major pent.    |_0_|   | 2 |   | 4 |   |   | 7 |   | 9 |    |   |
C bebop dorian   |_0_|   | 2 | 3 | 4 | 5 |   | 7 |   | 9 | 10 |   |
C dorian         | 0 |   | 2 | 3 |   | 5 |   | 7 |   | 9 | 10 |   |
```

We obtained the bebop dorian scale – a dorian scale with added major third. In other words if we switch between the minor and major pentatonic scale of the same root we are in fact still walking within the bebop dorian scale. And right the four-tone chromatic part can serve for some nicely sounding runs.

## Ionian + pentatonic = chromatic

Another interesting thing is that the complement of the ionian scale form a major pentatonic. Eg. C ionian union Gb major pentatonic gives the chromatic scale (the sequence of all 12 pitch classes). This corresponds to the white and black keys on the piano.

```
C ionian       |_0_|   | 2 |   | 4 | 5 |   | 7 |   | 9 |    | 11 |
Gb major pent. |   | 1 |   | 3 |   |   |_6_|   | 8 |   | 10 |    |
C chromatic    |_0_| 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 |
```
