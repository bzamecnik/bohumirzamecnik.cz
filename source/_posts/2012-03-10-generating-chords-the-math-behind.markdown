---
layout: post
title: "Generating chords - the math behind"
date: 2012-03-10 19:17:00 +0100
comments: true
categories:
- music theory
---

Musical chords might seem just as random groups of tones. What makes them sound “good” alone or together a in sequence? Are there any rules for creating chords or groups of chords that make harmony? Ever wondered why progressions like `IIm7` `V7` `Imaj7` `VIIm7b5` `III7alt` `VIm7` work so well and contain right those chords? In the following text we will explore the ways some of type simpler chord type can be generated from an ordinary scale and also quite a different approach for generating some familiar chords. We will also see some relation to mathematics.

<!--more-->

First, we will state the context and terms used throughout the text. We assume we are working with 12-tone [equal temperament](http://en.wikipedia.org/wiki/Equal_temperament), [enharmonic equivalence](http://en.wikipedia.org/wiki/Enharmonic) and also [octave equivalence](http://en.wikipedia.org/wiki/Octave_equivalence). The main tool for us will be *[pitch classes](http://en.wikipedia.org/wiki/Pitch_class)* and their *sets* (ordering does not matter) and sequences (ordering matters). Pitch class sets will be denoted by curly braces, eg. `{0, 5, 7, 2}`, while pitch class sequences by brackets, eg. `[0, 5, 7, 2]`.

In practice this means that we are working with 12 half-tones denoted by numbers from 0 to 11. The zero pitch class can be assigned to an arbitrary half-tone and we will for simplicity assign it to C as people are most used to it. The pitch class numbers then correspond to the common notation as follows:

```
| 0 | 1  | 2 | 3  | 4 | 5 | 6  | 7 | 8  | 9 | 10 | 11 |
| C | Db | D | Eb | E | F | Gb | G | Ab | A | Bb | B  |
| C | C# | D | D# | E | F | F# | G | G# | A | A# | B  |
```

The equivalence between sharp and flat tones is the enharmonic equivalence and is due to the assumption of equal temperament. When needing to express tone names in common notation we will use just the flat sign to make things clearer. Note that a single pitch class corresponds to pitches in all octaves. Also note that pitch classes are invariant to the tuning of the base frequency (we do not need it when analyzing the structure of chords).

These assumptions and notation will be used also in the subsequent articles except where notes otherwise. So let us finally start with generating some chords!

## Generating chords from a scale

A chord is nothing more difficult than a sequence of pitch classes. Sometimes when comparing chords the information about the order can be thrown away obtaining just pitch class sets. A trivial chord could just contain one or two pitch classes, eg. [6] or [2, 7]. In practice chords contain three or more pitch classes (in order to be musically interesting) which are played at the same. A scale is also a sequence of pitch classes which are in practice usually not played simultaneously. The notes in the scale are called scale 
degrees, eg. the third degree corresponds to the zero-based index 3 – 1 = 2.

### An analysis of the simple II-V-I chord progression

Let us start with an example of three simple chords Dm7, G7, Cmaj7. Those three chords types played in a sequence form the most widely used chord progression in jazz and related genres. It is called the II-V-I progression. In the following diagram we first see the chords tones in common notation and then in the pitch class notation. The root notes (that are at the first place in the pitch class sequence) are marked with an underline.

```
Dm7   | C |   |_D_|   |   | F |   |   |   | A |    |    |
G7    |   |   | D |   |   | F |   |_G_|   |   |    | B  |
Cmaj7 |_C_|   |   |   | E |   |   | G |   |   |    | B  |
------+---+---+---+---+---+---+---+---+---+---+----+----+
IIm7  | 0 |   |_2_|   |   | 5 |   |   |   | 9 |    |    |
V7    |   |   | 2 |   |   | 5 |   |_7_|   |   |    | 11 |
Imaj7 |_0_|   |   |   | 4 |   |   | 7 |   |   |    | 11 |
```

What can be seen in the diagram? On the first sight we can see that the subsequent chord pairs share some notes while differing in others. However, on the second sight we can see probably a more important thing. What do we get when taking all the notes occurring there? Pitch classes `[0, 2, 4, 5, 7, 9, 11]`, or in other words notes `[C, D, E, F, G, A, B]`, make up a scale called the C *[major scale](http://en.wikipedia.org/wiki/Major_scale)* or the C *ionian scale*. There are 12 major scales (each starting at a different pitch class) and also the major scale is the most widely used scale type in western music. Note that the scale has 7 elements which is a prime number.

What else can be seen from the diagram? If we look carefully eg. on the Cmaj7 chord we can see it contains pitch classes `[0, 4, 7, 11]` and the complement to the C major scale is `[2, 5, 9]`. If we index the scale instead of the set of all pitch classes we get indexes `[0, 2, 4, 6]` and `[1, 3, 5]`. In terms of notes it is `[C, E, G, B]` and `[D, F, A]`. Similarly for Dm7 the indexes are `[1, 3, 5, 0]` and `[2, 4, 6]`, while for G7 it is `[5, 7, 2, 4]` and `[6, 1, 3]`. Can you see it already?

### Generating the chords

If we start traversing the scale (circularly) at a given root note and skip each second element we get the known chords! Formally said, we get chords of length k from a scale `S` by using the following formula, where `|S|` is the length of the scale S and `mod` is the [modular division operation](http://en.wikipedia.org/wiki/Modulo_operation).

```
{{S[s+2i mod |S|] | i in {0, ..., k-1}} | s in {0, ..., |S|-1}}
```

#### Triads

Here is an example of taking just three elements from the scale. We get so called triads. For clearness there are only columns of the scale elements, not all the pitch classes.

```
C  |_C_|   | E |   | G |   |   |
Dm |   |_D_|   | F |   | A |   |
Em |   |   |_E_|   | G |   | B |
F  | C |   |   |_F_|   | A |   |
G  |   | D |   |   |_G_|   | B |
Am | C |   | E |   |   |_A_|   |
Bm |   | D |   | F |   |   |_B_|
```

#### Tetrachords

By taking four-element chords from the scale we get tetrachords (we have already seen them in the example at the beginning of the section).

```
Cmaj7 |_C_|   | E |   | G |   | B |
Dm7   | C |_D_|   | F |   | A |   |
Em7   |   | D |_E_|   | G |   | B |
Fmaj7 | C |   | E |_F_|   | A |   |
G7    |   | D |   | F |_G_|   | B |
Am7   | C |   | E |   | G |_A_|   |
Bm7b5 |   | D |   | F |   | A |_B_|
```

### Quality of the generated chords

Let us look at the quality of the tetrachords. Now there are columns for all pitch classes and each row starts at the particular chord’s root note. The first row represents the basic C major scale from which we derived the chords. The second row represents what is the common notation for chords notes – the index to the scale based on the chord’s root note (not the base scale), optionally shifted a half-tone down. The further rows contain pitch classes of each chord arranged so that they start at the root note.

```
scale   | 0  |   | 2 |   | 4  | 5 |   | 7  |   | 9 |   | 11 |
indexes | 1  |b2 | 2 |b3 | 3  | 4 |b5 | 5  |b6 | 6 |b7 | 7  |
--------+----+---+---+---+----+---+---+----+---+---+---+----+
Cmaj7   | 0  |   |   |   | 4  |   |   | 7  |   |   |   | 11 |
Dm7     | 2  |   |   | 5 |    |   |   | 9  |   |   | 0 |    |
Em7     | 4  |   |   | 7 |    |   |   | 11 |   |   | 2 |    |
Fmaj7   | 5  |   |   |   | 9  |   |   | 0  |   |   |   | 4  |
G7      | 7  |   |   |   | 11 |   |   | 2  |   |   | 5 |    |
Am7     | 9  |   |   | 0 |    |   |   | 4  |   |   | 7 |    |
Bm7b5   | 11 |   |   | 2 |    |   | 5 |    |   |   | 9 |    |
```

We can clearly see a lot of things. For chords generated from the major scale the second degree is either a major or minor third (index 3 or b3), ie. 3 or 4 half-tones apart from the root note. The third notes in the chord are either fifth or flat fifth (7 or 6 half-tones from the root). The fourth notes in the chords areeither seventh or flat seventh (11 or 10 half-tones from the root). To clearly see the chord quality we can transpose the pitch class sequence of a chord down by the value of the root pitch class. The ~ relation denotes equivalence of the pitch class sequences up to a transposition by a constant.

```
        chord notes       chord types
Cmaj7 [0,  4,  7,  11] ~ [0, 4, 7, 11]
Dm7   [2,  5,  9,  0 ] ~ [0, 3, 7, 10]
Em7   [4,  7,  11, 2 ] ~ [0, 3, 7, 10]
Fmaj7 [5,  9,  0,  4 ] ~ [0, 4, 7, 11]
G7    [7,  11, 2,  5 ] ~ [0, 4, 7, 10]
Am7   [9,  0,  4,  7 ] ~ [0, 3, 7, 10]
Bm7b5 [11, 2,  5,  9 ] ~ [0, 3, 6, 10]
```

We can divide the chords generated from the major scale into several categories based on the quality of the various chords:

When taking only the second chord notes into consideration the perfect third leads to major chords, while flat third to minor chord types – `{0, 3}` vs. `{0, 4}`.

When taking the second and third chord notes we obtain major, minor and diminished chord types – `{0, 4, 7}`, `{0, 3, 7}`, `{0, 3, 6}`.

When taking the second to fourth chord notes we obtain maj7, m7, dominant 7 and half-diminished m7b5 chord types –` {0, 4, 7, 11}`, `{0, 3, 7, 10}`, `{0, 4, 7, 10}`, `{0, 3, 6, 10}`.

#### Beyond tetrachords

So far we have only considered chords generated from a scale that contained three or four notes. It is clearly possible to add more notes the same way, we are only limited by the total number of notes in the scale (for the major scale it is seven). Note that all the heptachords are practically equivalent since they contain all the scale notes.

#### Pentachords

Only ninths or flat ninths are added. The chords are denoted in the common notation (without modulo 12).

```
scale   | 0  |   | 2 |   | 4  | 5 |   | 7  |   | 9 |   | 11 |
indexes | 1  |b2 | 2 |b3 | 3  | 4 |b5 | 5  |b6 | 6 |b7 | 7  |
--------+----+---+---+---+----+---+---+----+---+---+---+----+
Cmaj9   | 0  |   | 2 |   | 4  |   |   | 7  |   |   |   | 11 |
Dm9     | 2  |   | 4 | 5 |    |   |   | 9  |   |   | 0 |    |
Em b9   | 4  | 5 |   | 7 |    |   |   | 11 |   |   | 2 |    |
Fmaj9   | 5  |   | 7 |   | 9  |   |   | 0  |   |   |   | 4  |
G9      | 7  |   | 9 |   | 11 |   |   | 2  |   |   | 5 |    |
Am9     | 9  |   |11 | 0 |    |   |   | 4  |   |   | 7 |    |
Bm b9 b5| 11 | 0 |   | 2 |    |   | 5 |    |   |   | 9 |    |
```

#### Hexachords

```
scale   | 0  |   | 2 |   | 4  | 5 |   | 7  |   | 9 |   | 11 |
indexes | 1  |b2 | 2 |b3 | 3  | 4 |b5 | 5  |b6 | 6 |b7 | 7  |
--------+----+---+---+---+----+---+---+----+---+---+---+----+
Cmaj11  | 0  |   | 2 |   | 4  | 5 |   | 7  |   |   |   | 11 |
Dm11    | 2  |   | 4 | 5 |    | 7 |   | 9  |   |   | 0 |    |
Em11    | 4  | 5 |   | 7 |    | 9 |   | 11 |   |   | 2 |    |
Fmaj#11 | 5  |   | 7 |   | 9  |   |11 | 0  |   |   |   | 4  |
G11     | 7  |   | 9 |   | 11 | 0 |   | 2  |   |   | 5 |    |
Am11    | 9  |   |11 | 0 |    | 2 |   | 4  |   |   | 7 |    |
Bm11b5  | 11 | 0 |   | 2 |    | 4 | 5 |    |   |   | 9 |    |
```

#### Heptachords

Note that all the heptachords generated from the scale of length seven contain exactly the same notes. They only only differ in their interpretation based on the root note. Also note that a heptachord can be viewed as being composed of a tetrachord and a triad on top of it. This is called the [upper structure triad](http://en.wikipedia.org/wiki/Upper_structure_triad). Eg. the third row from bottom depicts the G13 consisting of a G7 tetrachord and Am triad.

```
scale   | 0  |   | 2 |   | 4  | 5 |   | 7  |   | 9 |   | 11 |
indexes | 1  |b2 | 2 |b3 | 3  | 4 |b5 | 5  |b6 | 6 |b7 | 7  |
--------+----+---+---+---+----+---+---+----+---+---+---+----+
Cmaj13  | 0  |   | 2 |   | 4  | 5 |   | 7  |   | 9 |   | 11 |
Dm13    | 2  |   | 4 | 5 |    | 7 |   | 9  |   |11 | 0 |    |
Em b13  | 4  | 5 |   | 7 |    | 9 |   | 11 | 0 |   | 2 |    |
Fmaj13  | 5  |   | 7 |   | 9  |   |11 | 0  |   | 2 |   | 4  |
G13     | 7  |   | 9 |   | 11 | 0 |   | 2  |   | 4 | 5 |    |
Am b13  | 9  |   |11 | 0 |    | 2 |   | 4  | 5 |   | 7 |    |
Bm b13b5| 11 | 0 |   | 2 |    | 4 | 5 |    | 7 |   | 9 |    |
```

#### Notes for the major scale

**Important insight**: In literature [1] the triad is defined as a three-note chord obtained by stacking third or various types. This is true, specifically for the major scale. Nevertheless it is rather a consequence of the stated method for generating chords from a scale, not the method itself!

### Generalization of the method

So far we skipped the scale indexes by a step of size two. In general we can choose an arbitrary step size t (from 0 to |S| – 1) which lead to a generalized formula for chord generation:

```
{{S[s + t*i mod |S|] | i in {0, ..., k-1}} | s in {0, ..., |S|-1}}
```

#### Considering of the scale size

The scale size plays quite an important role together with the step size. The major scale has seven elements with is a [prime number](http://en.wikipedia.org/wiki/Prime_number) which implies that no matter what step size we select the notes within a chord will not repeat until the chord size is less then equal than the scale size! 7 is comprime with 12.
As an example let us generate a hexachord with step six. From the C major scale we obtain the pitch class sequence `[0, 7, 2, 9, 4, 11]`. If we add a pitch class 6 (Gb) to the scale to increase its size to eight, we obtain the chord `[0, 6, 0, 6, 0, 6]` ~ `{0, 6}`!

Thus to be able to generate quite non-trivial chords the scale size should be prime. For the 12-tone equal temperament pitch classes the choices are: `2, 3, 5, 7, 11`. Nevertheless, the properties of non-prime-sized scale are also utilized in music and octatonic or hexatonic scales can be seen in practice.

### Generating from other scales than the major scale

We already known that is is possible to generate chords from scales other than the major scale. As for the modes of the major scale (including the aeolian minor scale) they are structurally equivalent to the major scale and thus we get the same chords only in different order. In order to convince the reader that such a method of generating chords is alright as an example we will generate chords from the two more widely used scales, the harmonic and melodic minor.

#### Melodic minor scale

The C melodic minor scale differs from the C major only by the lowered third degree and can be represented by the pitch class sequence `[0, 2, 3, 5, 7, 9, 11]`. The tetrachords generated with step equal to `2` are the following.

```
           chord notes        chord types
CmMaj7    [0,  3,  7,  11] ~ [0, 3, 7, 11]
Dm7       [2,  5,  9,  0 ] ~ [0, 3, 7, 10]
Eb maj7#5 [3,  7,  11, 2 ] ~ [0, 4, 8, 11]
F7        [5,  9,  0,  3 ] ~ [0, 4, 7, 10]
G7        [7,  11, 2,  5 ] ~ [0, 4, 7, 10]
Am7b5     [9,  0,  3,  7 ] ~ [0, 3, 6, 10]
Bm7b5     [11, 2,  5,  9 ] ~ [0, 3, 6, 10]
```

##### Pentachords

```
             chord notes            chord types
CmMaj9      [0,  3,  7,  11, 2 ] ~ [0, 3, 7, 11, 2]
Dm7 b9      [2,  5,  9,  0,  3 ] ~ [0, 3, 7, 10, 1]
Eb maj7#5 9 [3,  7,  11, 2,  5 ] ~ [0, 4, 8, 11, 2]
F9          [5,  9,  0,  3,  7 ] ~ [0, 4, 7, 10, 2]
G9          [7,  11, 2,  5,  9 ] ~ [0, 4, 7, 10, 2]
Am7b5 b9    [9,  0,  3,  7,  11] ~ [0, 3, 6, 10, 2]
Bm7b5 b9    [11, 2,  5,  9,  0 ] ~ [0, 3, 6, 10, 1]
```

#### Harmonic minor scale

The C harmonic minor scale differs from the C melodic minor by the lowered sixth degree and can be represented by the pitch class sequence `[0, 2, 3, 5, 7, 8, 11]`. The tetrachords generated with step equal to `2` are the following.

```
           chord notes        chord types
CmMaj7    [0,  3,  7,  11] ~ [0, 3, 7, 11]
Dm7b5     [2,  5,  8,  0 ] ~ [0, 3, 6, 10]
Eb maj7#5 [3,  7,  11, 2 ] ~ [0, 4, 8, 11]
Fm7       [5,  8,  0,  3 ] ~ [0, 3, 7, 10]
G7        [7,  11, 2,  5 ] ~ [0, 4, 7, 10]
Ab maj7   [8,  0,  3,  7 ] ~ [0, 4, 7, 11]
Bdim      [11, 2,  5,  8 ] ~ [0, 3, 6, 9 ]
```

This corresponds well to the results in the article [Harmonic minor scale in action](TODO).

##### Pentachods

```
            chord notes            chord types
CmMaj9     [0,  3,  7,  11, 2 ] ~ [0, 3, 7, 11, 2]
Dm7b5 b9   [2,  5,  8,  0,  3 ] ~ [0, 3, 6, 10, 1]
Eb maj9#5  [3,  7,  11, 2,  5 ] ~ [0, 4, 8, 11, 2]
Fm9        [5,  8,  0,  3,  7 ] ~ [0, 3, 7, 10, 2]
G7 b9      [7,  11, 2,  5,  8 ] ~ [0, 4, 7, 10, 1]
Ab maj7 #9 [8,  0,  3,  7,  11] ~ [0, 4, 7, 11, 3]
Bdim b9    [11, 2,  5,  8,  0 ] ~ [0, 3, 6, 9,  1]
```

Here we have an altered dominant chords G7 b9!

#### Double harmonic scale

The C [double harmonic scale](http://en.wikipedia.org/wiki/Hungarian_minor_scale) differs from the C harmonic minor by the raised fourth degree andcan be represented by the pitch class sequence `[0, 2, 3, 6, 7, 8, 11]`. The tetrachords generated with step equal to `2` are the following.

```
             chord notes         chord types
CmMaj7       [0,  3,  7,  11] ~ [0, 3, 7, 11]
D7b5         [2,  6,  8,  0 ] ~ [0, 4, 6, 10]
Eb maj7#5    [3,  7,  11, 2 ] ~ [0, 4, 8, 11]
Gb sus2 b5 6 [6,  8,  0,  3 ] ~ [0, 2, 6, 9 ]
Gmaj7        [7,  11, 2,  6 ] ~ [0, 4, 7, 11]
Ab maj7      [8,  0,  3,  7 ] ~ [0, 4, 7, 11]
Bm6          [11, 2,  6,  8 ] ~ [0, 3, 7, 9 ]
```

## Generating chords with homogeneous intervals

There can be quite an amount of different chords and also many equivalent ones, depending of the exact definitions. We have seen a way of generating some commonly used chords from scales by skipping the scale degrees. Now we will present a conceptually similar approach. Instead of skipping the scale degrees to generate chord notes we will skip pitch classes themselves. For the chromatic scale, ie. a scale consisting of an ordered sequence of all 12 pitch classes, there is a one-to-one correspondence between pitch classes and scale degrees. Thus this approach might be seen as an application of the former approach to the chromatic scale.

Having a scale of size 12 the skipping step can be from the range 0 to 11. The chord-generating formula can be simplified a bit. Remember the parameters are: *t* – the skipping step size, *k* – the chord size.

``` text
{s + t*i mod 12 | i in {0, ..., k-1}} | s in {0, ..., 11}}
```

At first, let us generate a chords of size 13 from a single root with different step size:

```
t  | generated chords
0  | 0 |_0_| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
1  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10| 11|_0_|
2  | 0 | 2 | 4 | 6 | 8 | 10|_0_| 2 | 4 | 6 | 8 | 10| 0 |
3  | 0 | 3 | 6 | 9 |_0_| 3 | 6 | 9 | 0 | 3 | 6 | 9 | 0 |
4  | 0 | 4 | 8 |_0_| 4 | 8 | 0 | 4 | 8 | 0 | 4 | 8 | 0 |
5  | 0 | 5 | 10| 3 | 8 | 1 | 6 | 11| 4 | 9 | 2 | 7 |_0_|
6  | 0 | 6 |_0_| 6 | 0 | 6 | 0 | 6 | 0 | 6 | 0 | 6 | 0 |
7  | 0 | 7 | 2 | 9 | 4 | 11| 6 | 1 | 8 | 3 | 10| 5 |_0_|
8  | 0 | 8 | 4 |_0_| 8 | 4 | 0 | 8 | 4 | 0 | 8 | 4 | 0 |
9  | 0 | 9 | 6 | 3 |_0_| 9 | 6 | 3 | 0 | 9 | 6 | 3 | 0 |
10 | 0 | 10| 8 | 6 | 4 | 2 |_0_| 10| 8 | 6 | 4 | 2 | 0 |
11 | 0 | 11| 10| 9 | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 |_0_|
```

The returns back to the root tone are denoted by underline. Only with steps sizes 1, 5, 7 and 11 all pitch classes are visited. For the others some notes repeat more or less earlier.

It might be quite surprising! Well, in fact, not that much if you know a little about algebra. The structure behind pitch classes is a group isomorphic to Z12, the group of integers modulo 12. Just the elements 1, 5, 7, 11 are its generators. The connection between music theory and mathematics is treated in detail by the [musical set theory](http://en.wikipedia.org/wiki/Set_theory_(music)).

So let us explore what kind of chords can be generated with homogeneous intervals – from the trivial ones to the more interesting. For steps 1 or 12 (= 12 – 1) we obtain [tone clusters](http://en.wikipedia.org/wiki/Tone_cluster) of size at most 12. For step 6 we get just tritone dyads as the cycle length is 12 / 6 = 2.

Steps 3 or 9 (= 12 – 3) give rise to diminished chords of 4 (= 12 / 3) unique elements. Since the intervals between chord notes are homogeneous there are only 3 different pitch class sets corresponding to different diminished chord types. Steps 4 or 8 lead us to augmented chords of just 3 (= 12 / 4) elements. There are 4 types of them.

For steps 2 or 10 are generated chords based on the symmetric hexatonic scale, the [secundal chords](http://en.wikipedia.org/wiki/Secundal). They have at most 6 unique elements and there are 2 different such hexachords.

###Quartal chords

Finally, steps 5 and 7 lead to very interesting [quartal and quintal chords](http://en.wikipedia.org/wiki/Quartal_and_quintal_harmony). Since the pitch classes 5 and 7 generate the whole chromatic scale there are 12 unique quartal and quintal types of chords of at most 12 elements. Quartal chords have utilization in various genres of music including jazz and rock. We will provide an example of quartal chords of size four derived from the C chromatic scale:

```
[0,  5,  10, 3 ]
[1,  6,  11, 4 ]
[2,  7,  0,  5 ]
[3,  8,  1,  6 ]
[4,  9,  2,  7 ]
[5,  10, 3,  8 ]
[6,  11, 4,  9 ]
[7,  0,  5,  10]
[8,  1,  6,  11]
[9,  2,  7,  0 ]
[10, 3,  8,  1 ]
[11, 4,  9,  2 ]
```

Finally, note that the full chords generated by pitch classes other that the generators lead to subgroups of the [cyclic group](http://en.wikipedia.org/wiki/Cyclic_group) Z12 (corresponding to the chromatic scale), the groups Z6, Z4, Z3, Z2, Z1 (divisors of 12). They correspond the the hexatonic, diminished, augmented symmetric scales, the tritone and a single pitch class.

##Conclusion

We have presented a mathematical method for generating musical chords – by skipping degrees of a scale or directly the pitch classes by steps of constant size. This way we are able to generate many commonly used chords, eg. in jazz. We presented the chords derived from the major scale and several minor scales and also quartal, augmented and diminished chords. Partial and altered chords were not addressed.

##References

[1] Pen, Ronald (1992). Introduction to Music, p. 81. McGraw-Hill, [ISBN 0-07-038068-6](http://en.wikipedia.org/wiki/Special:BookSources/0070380686). “A triad is a chord consisting of three notes built on successive intervals of a third. A triad can be constructed upon any note by adding alternating notes drawn from the scale.” Source: [Triad](http://en.wikipedia.org/wiki/Triad_(music\)) at Wikipedia
