---
layout: post
title: "HarmonEye: hard-won experience after three years of my life"
date: 2015-11-16 15:15:15 +0100
comments: true
categories:
---

In this article I'd like to summarize lessons learned during three years of development of software for music analysis, visualization and machine learning and of my life. You can find a lot of experience and advice not to repeat the mistakes I did.

The goal: see what you hear in music. When you think you're genius and can do all features at once, stop... It's a trap! Go by small steps and get much further.

<!--more-->

Almost two years ago in December 2013 I quit my regular job at [Etnetera](http://www.etnetera.cz/). Working on e-commerce backend I was missing applied mathematics badly and wanted to build my own products. Martin Holečko, one of the founders of Etnetera gave me a nice book tip: [4-Hour Work Week](http://fourhourworkweek.com/) by Tim Ferris. I decided [I want more freedom](http://bohumirzamecnik.cz/blog/2013/refactoring-the-path-to-freedom/) and do what I was dreaming of and working on before.

The idea is simple. I love music and want to understand better how it works. But my eyes are much more efficient and trained in finding patterns than my hearing.

{% youtube mStudajyJvo %}

## A bit of history

Since being a child I was fascinated by some beautifully strange moments in songs. But classical music theory was a complete mess for me. My brother Vojta "Pango" has been a [piano](http://www.stuff.co.nz/the-press/news/mid-canterbury-selwyn/72751381/nature-inspires-pangos-tunes)  [genius](http://www.stuff.co.nz/timaru-herald/news/72121876/Piano-man-fills-Stafford-St-with-music) and I was more interested in computers. However, at the high school I started [singing in a choir](http://corale.cz/) and to play guitar at jam sessions. And I've been wondering if it possible to automatically recognize chords from audio recording, how to measure similarity between songs and things like that. Also Honza Hovorka, one of my friends from the choir and a very talented clarinet player, inspired me with this master's thesis on recognizing voice and music in audio.

This ideas of audio processing and pattern recognition lead me to study Computer Science at the Charles University. There were indeed some lectures on DSP and machine learning, however only theoretical, without any practice. In the master's years I was more interested in computer graphics and computational photography, however.

Then after graduating I came to Etnetera to do something practical. I did backend for e-commerce and [personalization](http://soyka.etnetera.cz/en/). Still, there was some internal urge to deal with music processing and visualization. Web development was not that satisfying.

In the meantime years I became more and more interested in music theory - how it works. I realized that there are some deep connections between physics, algebra, number theory and calculus in music theory and that the underlying models are far simpler that what is taught in schools as "classical music theory". Imagine 400-years old open-source code with backwards compatible API and no chance to be refactored. When I googled what I found out I realized there's about 30 years of [research](https://en.wikipedia.org/wiki/Set_theory_%28music%29) in this area, so it is not a complete nonsense.

## Birth of HarmonEye

I set the goal to build a tool that automatically recognizes what tones (or more precisely [pitch classes](https://en.wikipedia.org/wiki/Pitch_class)) are being played in audio and visualize them. Ideally in real-time from microphone on a mobile phone. The visualization was quite simple, just a [circle like a clock](http://www.visualmusictheory.com/tone-circle.html) with each of 12 segments representing intensity of each pitch class.

![](http://i.visualmusictheory.com/tone-circle/pen_and_paper_examples.jpg)

As I learned music theory by myself I have drawn a vast number of such circle diagrams with pen and paper. It was a beautiful tool to understand a lot of concepts. But my idea was to put this into a mobile phone app and leave the work to machines. The name HarmonEye popped in my mind as a portmanteau of "harmony" and "eye" denoting the connection between musical chords and visual perception.

![](http://harmoneye.com/img/harmoneye-logo-512-512.png)

I researched tons of papers on audio processing and made model of the computational pipeline in my head. In December 2012 I started coding. The work was quite irregular, just when I had some free time. In one year I had a working prototype running on Mac desktop.

<img src="{% asset_path harmoneye-github-timeline.png %}">

![](http://harmoneye.com/img/harmoneye-mac-c-maj7.png)

On 6th June 2013 the HarmonEye for Mac was released as a paid app.

Also I shared the progress on Twitter [@harmoneye](https://twitter.com/HarmonEye).

## Features

The basic features of HarmonEye is to show what pitch classes are being played at the moment. Imagine a piano keyboard with sound intensity on each key. Then you roll the piano into a circle where tones are aligned by octaves. That's the [mental model](http://www.visualmusictheory.com/tone-circle.html).

I wanted to analyze audio from the microphone in real-time so that I can experiment with it interactively. For the prototype I just wanted it to run on my computer.

{% youtube eIoJYKPXecM %}

In the first step I wanted to show just the raw intensities so that one can see what's there. In the second step I wanted to move the classification of the pitch classes from human brain to some machine learning algorithm.

Key detection for coloring.

## The architecture

I will describe the architecture of HarmonEye and how it evolved over time in more detail in separate article. This is just a quick high-level overview.

So we have microphone input on one side and a animated plot on the other side. We can go backwards to find out what computation is required.

A smooth animation requires something about 25 frames per second. In each frame we need the data for backing the plot (either raw intensity features or ideally the recognized pitch classes). To obtain this we need to do some kind of spectral decomposition that detects amount of energy in each pitch bin. In other words we need to quantize the frequency domain of time-frequency plane into bins that correspond to musical semitones, ie. are uniform in the log-frequency domain. There are various methods, for the initial implementation I chose CQT (Constant-Q Transform), afterwards I found TF-reassignment is much more suitable. The input to this is a plain spectrogram, ie. STFT (Short-Time Fourier Transform). Each frame in STFT needs a frame of the original audio. Thus we get to the audio source, that is in this case a microphone.

{% youtube 9YkXvjwQNlE %}

Things get a bit complicated in this part. Audio samples are read from the sound interface in batches in a blocking manner. However, we want to process blocks of samples that are of different size than audio buffer size, are overlapping and the processing is done at an rate independent on the input rate. This is described in Computer Science as the producer-consumer problem. One way to solve this problem is a read data in one thread, process it and another one and hold the data in a shared resource. Such a efficient resource that allows writing blocks of samples of one size and reading blocks of other size is ring buffer.

When we look at the results of such system, we quickly realize that showing the spectrogram with log-frequency quantization is not enough. There are multiple problems. First, typical instruments produce not a single sinusoid but multiple sinusoids, typically integer multiples (these are called harmonic tones). But we need to show only energy corresponding to the fundamental frequency. Second, typical music is not composed only of harmonic tones but also from a lot of percussive or noisy sounds. Third, with STFT windowing spectrum of each sinusoid is not a single peak but has side-lobes. So we need to filter all of this noise out. Fourth, for visualization we need to normalize the results so that the important information is not hidden in the remaining background noise.

The other rather technical challenges were in obtaining the audio via Java API, drawing OpenGL in a Swing UI, getting it work with Mac OS. Back then I wanted a paid Mac application, so I put inside some licensing (LimeLM, now gone), payment processing (FastSpring, deprecated) and code obfuscation (ProGuard). At least ProGuard turned out to be useful for code minimization on Android.

## Half-a-year sprint

After reading the 4HWW book I realized I could make some money from such an app and I also wanted to work while traveling like a digital nomad. So on 1st December 2013 I started on my own. From my savings with a runway of a few months.

Since I started I was thinking that I need to make a product quickly so that I could launch it and start making money. The sprint started with a Week of Hustle in the community of product makers called [JFDI](http://jfdi.bz/) (Just Fucking Do it) by [Justin Jackson](http://justinjackson.ca/) - now called [productpeople.club](http://productpeople.club/). There are a [few articles from that time](http://bohumirzamecnik.cz/blog/categories/week-of-hustle/) on my blog.

I focused much on building the application. Also I tried to find if other people are interested. I talked with many people live and some said they love the idea. I can remember when I was returning back from one Scala Dojo, we were talking about HarmonEye with one friend in the metro and some other guy listened to us and told me that he wants to buy it, just to tell him where. He was a musician.

This validated that there's something interesting not only for me in the product.

I created a [website](http://harmoneye.com/) a newsletter and posted some messages and screenshots to some online fora. Sometimes the reaction was "we like it", most often "fuck off with your commercial shit".

I read some self-development books to understand how online marketing can work. Most notably Amplification by Justin Jackson. The basic message was:

- research for some pain, at best your own pain that you see also at others
- determine who is your audience
- start creating some useful content, eg. blog posts, videos, etc.
- start creating a community, eg. using a newsletter
- build your product and then launch it to your community

Although I knew theoretically what to do I didn't do it.

I thought my pain is in finding chords/tones in some unknown music. I thought other people also like to find chords and visualize the music. I though my audience are amateur musicians or people who start learning music.

I didn't start writing articles for such people. I created a newsletter but didn't write anything useful to it, only some info about the app being available. When I launched the Mac app a handful of people bought it. As for the Android app I launched only a free version without a price tag or ads.

Why?

I was too focused on making the app working before my runway ends so I didn't start writing about music and using HarmonEye for music. I needed to have a working app to show examples. When the app was ready it was almost impossible to create a meaningful video since it was completely hardcoded with mic input and OpenGL output.

Along the way I wrote a lot of code and solved a lot of implementation problems but those were not useful for the musicians audience.

When I showed the app to the musicians some said it is not needed for them, some were interested.

I desperately wanted to create the full-featured app that I skipped even the initial phase, that is validating the problem and audience. It is an important step and its outcome determines what should be the next goal. I set the goal without knowing if it is really useful. I set the inital goal too high and too broad. There's need to focus on something small and do it. And iterate.

## The pains with Java, etc.

For the implementation I chose Java. Three years before it was quite natural, since I was using Java every day at work and it is of of the native languages for Android. However, from today's perspective it was a really a bad decision. As bad a using C# at the university for team project, bachelor thesis and master thesis. Honestly, Java or C# are not suitable for this kind of projects at all. The reasons are multiple.

### It is hard and expensive to experiment

I've became a great fan of prototyping and found that crucial for this is to be able to quickly and cheaply experiment.

### Lack of shell

For experimenting I find interactive shell an indispensable tool. You just open up the shell, type a few lines of code, see the results, type other code (while still having the previous results at hand) and iterate until you're satisfied. Quick and dirty.

### Initial overhead with build tools

In contrast, in Java world you need to create a project, set up a build tool, write some test class, some class with the code, run it and see the results. When you want to explore to results, you need to modify the code, compile and run again. This is just a huge overhead that quickly makes prototyping expensive, annoying and leads to writing lots of code blindly and then debugging the same amount of time. At least there are languages on JVM such as Scala or Groovy that provide shell.

### Lack of usable libraries - numeric computation, plotting

A big pain when working on HarmonEye and the university projects was that I had to reinvent wheel a lot of time just to have some basic tools. This takes a lot of time and effort, leads to suboptimal tools and projects since you have to invest your time not only in your own project but to all the tooling. I admit sometimes it is necessary to create the tooling - when there's none available or doesn't not fit your application at all. But otherwise it's just a waste of time. You can do wonders if you can stand on shoulders of giants. And that's exactly what existing libraries provide. Thanks to our predecessors who creted the tooling. Sometime you find yourself to create tooling along the way, that will be useful for other as well. That's just a wonderful open-source ecosystem.

We, programmers, tend to be very arrogant when it comes other people's code. Sometimes the critique is valid. Most often the problems of existing code results not from other people being totally stupid morons, just from lack of experience, lack of time, too much politics, etc. We think we'll handle the problem better and often we fall into the same trap.

I needed to represent and handle multidimensional arrays efficiently. In Python you have numpy which has wonderful API, efficient memory representation and super-optimized C/Fortran backend. In Java I had plain arrays of integers. Now, you have [ND4J](https://github.com/deeplearning4j/nd4j), but this was not available at that time (initial commit at 31 Aug 2014). So since ND-arrays were not my main business I made some working code but it quickly became really painful to use.

The same goes for FFT, window functions, sparse matrices, complex numbers, zero-phase low-pass filtering, decimation and a ton of other numerical goodies. Scipy has all this stuff already available, tested and optimized. By hand you spend a lot of time to find bugs afterwards and you app waits for your attention and suffers.

Plotting is a chapter in itself. When you do signal processing you need visibility to the amounts of your data. A lot of various plots of different types. Sometimes at best interactive or animated. Doing this stuff by hand is possible of course but eats huge amounts of time and it really suboptimal and not nicely reusable. First I had to draw to the canvas in Swing UI, then in OpenGL. Both were really really painful and not suitable for experimenting at all. At least I found [Processing](https://processing.org/) to be a nice tool for prototyping, even with a Java-like code. It helped me a lot.

In Python world you can use matplotlib or Bokeh. Output can go to a GUI, notebook, web page or to a file and could be interactive. There are tons of plots, options and examples. It is possible to create animations quite easily and produce videos that can be uploaded to YouTube. The integration between numpy or matplotlib is excellent.

### Memory management with GC

When I first ran HarmonEye [ported to Android](https://play.google.com/store/apps/details?id=com.harmoneye.android&hl=cs) it ran for about 2 seconds. Then it crashed. Why? It turned out that some parts of the app including some third-party libraries allocate on-heap memory in each iteration. This quickly overloaded the GC and the application was terminated. The old HTC Android phone had a little memory and GC was not so fast.

I had to rewrite the code so that already allocated arrays are reused. This lead to abandoning functional paradigm and overwriting some shared memory. Bad.

*First port of HarmonEye to Android, with rather geeky text mode plotting.*

{% youtube HoPOUV4EAeo %}

*Pango playing on software keyboard that is visualized by some old versions of HarmonEye for Mac and Android 2.3. Note the slow response and lots of background noise.*

{% youtube tW1H8GP_1tk %}

### Lack of functional programming tools

Java was created with procedural and object-oriented paradigm in mind. Functional paradigm in contrast to the previously mentioned is based on the idea that the elements of code are pure transformations, functions that do not have any side-effects. This allows for better testing, much higher-level code and is very suitable for today's need for concurrent and parallel computation. Most importantly it relieves the programmer of considering the behavior of some code in all possible states because there's no mutable state or it is very isolated.

As said this is a very powerful tool that allows to express code very concisely and minimizes the room for errors from mutable state. Java didn't support functional paradigm up to version 8. And I used Java 6 or 7 at that time since Java 8 was released at Mar 18, 2014. Scala managed to join object-oriented and functional paradigm but I didn't use it due to lack of experience and since it running it on Android was uncertain.

After experience with Python I wouldn't give up functional programming for prototyping or production code until absolutely necessary. Well, when you have working code and measure a clear bottleneck that can be solved by some very optimized procedural code, then OK. Bot not before. Otherwise functional programming saves a lot of time and nerves which is usually the bottleneck during development.

### Static typing and explicit naming

One thing I noticed when I was comparing my Java and Python code was that code in Java needed a lot more names for everything. Classes, methods, variable for everything. In Python using functional constructs, lambda functions and high-level APIs there was much less need for naming.

Also Java is strongly typed and Python duck-typed. This means that in Java you need to make custom classes and interfaces all the time. Things that behave the same way but have incompatible interfaces just can't be used together without all the adapters and crap like that.

In duck-typed languages it doesn't matter whether you provide a `plot()` function with a numpy array, list, tuple or you custom object as long as it behaves as expected. No need for some common explicit interface. No need for lots of names. Most of the time numpy arrays are the universal interface between various libraries.

A Computer Science cliche quotations says two hardest things in CS to do right is caching and naming things. When you don't have to name a thing you don't have a hard problem to solve. And this means more time for the actual business. I don't say make anything anonymous, just don't make everything explicit unless it's really necessary.

## Practical Machine learning

After getting the pipeline to work I was happy. But soon I realized this is not what people would like to see. It is very hard to comprehend. The pitch class circle with intensities is just too low-level for humans to look at real-time.

What was needed was to automatically recognize whether a pitch class is active or not. And that is a machine learning problem.

One day an email came to my inbox from Coursera offering a the course of [Machine Learning](https://www.coursera.org/learn/machine-learning) course taught by prof. Andrew Ng. I signed up since this was something that could provide the solution to the problems of HarmonEye. Besides that it was the first practical experience with ML. At the university there were theoretical lectures on pattern recognition or numerical optimization, bot nothing practical. Now we worked a lot of in MATLAB/Octave and it was also nice to experiment with the DSP stuff.

At this course I was really happy we went trought all the tough and boring lectures at the university such as Linear Algebra, Calculus, Statistics, etc. Things that must have been hard for a lot of students were quite natural after having those math tools in the belt. I realized machine learning can be applied universally beyond music processing.

Since the runway was almost off and the business was not going as I expected I concluded I need to find a job again. Doing machine learning with Python would be awesome. Soon I found an offer for such position.

## Exhaustion

I developed HarmonEye about 6 months full-time. During that I worked way more than 8 hours per day (rather 12-16). I was just thinking about the project and could not relax. This is not sustainable. In order to minimize the cost of living I moved temporarily from Prague to my parents. Unfortunately the atmosphere was not quite healthy. Instead of being with people in the city I voluntarily imprisoned myself at home at my computer.

After some time I became not able to focus well and became quite depressed. I tried to reconsider if the project I'm doing is meaningful. During the last few months/weeks I tried to come up with a plans B and C.

I also create [Tone Circle](https://play.google.com/store/apps/details?id=com.harmoneye.tonecircle), an Android app that allow for playing with the pitch classes via touch gestures and shows the chord name.

![](http://i.visualmusictheory.com/tone-circle/tone_circle_c_e_g.png)

Besides that I tried to come up with some simpler product, a guitar tuner. I took the guts from Harmoneye, focused on a single harmonic signal and did a completely different UI. There are two prototypes - [beads](https://www.youtube.com/watch?v=iOsym2fTOVY) similar to the Tone Circle and [linear](https://www.youtube.com/watch?v=8mBO7Ry8uQs). It was deployed to Google Play but never published, since it lacked proper classification of tones. Note it was done before taking the ML course.

{% youtube iOsym2fTOVY %}

{% youtube 8mBO7Ry8uQs %}

At least to write some useful stuff on music theory. Without a clear focus about who is the reader (a ordinary musician, a person with maths background, a programmer) it was very hard to write something at the right level of detail. I have the stuff stored somewhere in git, but it is waiting for its time.

Finally I decided I have to quit this since it was pointless. I was so exhausted from all the things. I decided to pack my things and fuck off and clean my mind.

## Road trip

With a few bucks in my pocket and a car the obvious idea was to make a road trip. It was summer and the southern lands didn't attract me. I've already been there. An unexplored territory was to the north, though. I've never been to Scandinavia before. After looking at the map a few days before leaving the rough plan was to drive around the Baltic sea to Scandinavia and back. I had a month before starting with the new job.

6738 km, 24 days, 10 countries, 1 car, single traveller, wonderful nature, kind people, a lot of experience and a pile of photos. It was a trip of lifetime or maybe just a beginning.

The route was from Czech republic through Poland, Lithuania, Latvia, Estonia, Finland, Sweden, Norway, Sweden, Denmark, Germany back to Czech republic.

It was the best thing I could do. Wonderful experience! I alone was responsible for whatever I do. There was so much to see everywhere and beautiful nature to admire. I also met nice people on the road. Every day was different and surprise. Every day full of stories. I blogged the whole journey (unfortunately it's still available in only Czech language) and tool a lot of photos. Not all are published yet (shame on me).

[Scandinavia Road Trip 2014/06](https://www.flickr.com/photos/elgriton/albums/72157645572363341) on Flickr.

A month later I returned as someone else. Full of energy and determination to continue.

## Wikidi

In July 2014 I started at Wikidi a private startup with a few projects. I worked on some algorithmic trading stuff. It was nice that I could use Python and learn and apply some deep learning techniques. During that time I made myself familiar with Theano, word2vec, approximate nearest neighbors, etc. After a few months a colleague left the team and I took his role. This was also some trading but in Java. The code was complete ugly mess. We worked hard to make it usable. That time careful test-driven development brought its fruits. The resulting code was quite nice, flexible and maintainable. I'm really pround of it. The problem though was all the projects didn't seem to lead anywhere. Without a clear goal you cannot get to that place. The same mistake - trying to take too big steps without validating if the product is really useful. Abundance of resources might not be cure for all problems. After a few months I got another offer.

## Jumpshot/AVAST

In winter 2014 AVAST was looking for someone to scale machine learning models to big data. It seemed to me quite appealing, so that I could do ML again. In February 2015 I started, already in a spin-off called [Jumpshot](https://www.jumpshot.com/). It has been very nice to meet so much smart people. The data was a few orders larger that I ever seen before. I did a lot of various backend stuff and started to recognize familiar patterns. The same problem as ever before but on scale. In my opinion the test-driven development and business should be repeated almost like a mantra. Unfortunately until present I was not able to do ML, except for an awesome week-long deep learning workshop in a [scientific cluster in Ostrava](https://www.it4i.cz/). Besides learning a lot about big data I started using Jupyter notebooks regularly.

## Some stats from Google Play

### HarmonEye

HarmonEye for Android [released](http://bohumirzamecnik.cz/blog/2014/harmoneye-for-android-released/) at 14 January 2014, about two months after leaving Etnetera and starting the port.

Here are some number for HarmonEye for Android.

- current installations: 513
- total installations: 1738
- percentage of active installation wrt. total: 29.5%

Total active users:

<img src="{% asset_path google-play-harmoneye-users.png %}">

Total active users by OS version:

<img src="{% asset_path google-play-harmoneye-users-by-os-version.png %}">

OS versions:

<img src="{% asset_path google-play-harmoneye-os-versions.png %}">

#### Reviews

<img src="{% asset_path google-play-harmoneye-reviews-summary.png %}">

Saint Rezo 14. 2. 2015 v 1:42:
> Are u serious? If you're not then don't do this to the rest of us, if yes then you should finish it.please more update!!! This is good start!

Ian MicMultiMillian 28. 2. 2014 v 5:27:
> Amazing There's nothing like this on the market anywhere and what's better, it works perfectly! It's beautifully simplistic design is so clear and nice to look at. Now I can learn the notes of whatever tune I want :)  And it's also very pretty to look at, which, unfortunately, seems to be the most desired feature of apps today.

Adi Napiza 5. 1. 2015 v 3:15:
> Like it Very helpful especially in a jam setting. Hoping dev will improve this by adding a chord finder feature and maybe mapping out octaves but so far its good and useful!

sgtransit 74 27. 12. 2014 v 17:06:

> Something new. Hope developer can open source so it can be useful quickly.

Ethan Taylor 20. 4. 2015 v 1:02:
> Harmoneye is amazing Incredible tool for figuring out songs from recordings,  and live play, when the guitarists unexpectedly capo up.

Халиль Разиньков 10. 7. 2015 v 3:56:
> Thanx, man Nice app in duet with Tone circle. But I'm used to a chord with #, not a b, which is little confusing. Can you make settings, like color themes or/and  chords forms in both apps?

This gives me hope that there are indeed people who find it useful and want it to be even better.

### Tone Circle

- current installations: 137
- total installations: 618
- percentage of active installation wrt. total: 22.1%

## Open-sourcing

Since the original HarmonEye written in Java is not quite suitable for further development there was no point in keeping it in a drawer. So that at some point I open-sourced it ([harmoneye](https://github.com/bzamecnik/harmoneye/tree/develop), [harmoneye-android](https://github.com/bzamecnik/harmoneye-android)). Also I realized it is better to publish a lot of other research [ideas](https://github.com/bzamecnik/ideas) and question. One never knows when he's gone and it would be [shame to keep the ideas to the grave](http://bohumirzamecnik.cz/blog/2014/lets-share-the-ideas-with-the-world/).

Also I thought about the audience. I realized that what I really enjoy is to teach. I have a lot of experience from those projects and feel a lot of pain when applying mathematical methods into practice. In my opinion there's still a huge lag between researchers who speak in mathematical language with a lot of implicit context and between developers who speak in code. So another possibility is teach developers interested in music/audio processing and machine learning how to apply the mathematical methods. After all I'm one such person and I wished to have such a guidance earlier so that I can avoid all the costly mistakes.

## New goals

With this in mind I felt I need to build the tool I originally wanted to do. But in small steps and clear goal. Ie. series of tutorials for ML/MIR/DSP-interested programmers. If there's HarmonEye 2.0 for mobile it should be a by-product not the end-goal.

The other goal is to prepare a course in a non-classical visual approach to music theory for musicians. The main ideas are written in the [Manifesto of Visual Music Theory](https://github.com/bzamecnik/ideas/blob/master/visual_music_theory_manifesto.md).

There are three blogs right now and I need to figure out what to do with them (merge or keep separate):

- [bohumirzamecnik.cz](http://bohumirzamecnik.cz/) - audio processing, python, life, travels, etc.
- [visualmusictheory.com](http://www.visualmusictheory.com/) - about music theory, for musicians
- [neural.cz](http://www.neural.cz/) - about machine learning

## A lot of learning

I came across some awesome books and courses along the way. [Machine Learning Mastery](http://machinelearningmastery.com) by Jason Brownlee is a very solid foundation for every ML practitioner. One of the main points is to take very small steps focused at learning one thing at time (a kind of TDD and Unix philosophy mix). Other useful concept is being lazy in learning the stuff - take a problem, explore it with some tools, then explore the methods, then write the algorithms, then dig deep into the maths. Not learn everything up front, but learn what's needed at that point. This allows you to have meaningful results at each small steps and stay motivated.

Recently the book [Marketing for Developers](http://justinjackson.ca/marketingfordevelopers/) by Justin Jackson finally came out. It is a foundation to set the goals and do all the stuff outside pure coding (finding the audience, launching the product, scaling, etc.). Other useful books may include [Authority](http://nathanbarry.com/authority/) by Nathan Barry or [Lean Startup](http://theleanstartup.com/) by Eric Ries.

A very nice experience was the [DNX Digital Nomad Conference](http://www.dnxglobal.com/) in Berlin in summer 2015. About 500 like-minded gathered at one place sharing their thoughts and experience. There I finally realized for which audience I should create content.

By the way on a sunny Sunday you can find a vast number of street musicians in Berlin's Mauerpark. Here's how it looks when HarmonEye on a tablet shows the sound of australian rock band [The Sunpilots](http://thesunpilots.com/).

{% youtube DhnZHA0vAfk %}

Since autumn 2014 I've been attending [Machine Learning Meetups](http://www.mlmu.cz/) in Prauge. Once I gave a lightning talk about ML on Time Series - comparision of financial and music data (in Czech language).

{% youtube 8_0mUWC2XyM %}

I took or am presently attending various courses at Coursera and edX, namely [Scalable Machine Learning](https://www.edx.org/course/scalable-machine-learning-uc-berkeleyx-cs190-1x) (in Spark), [Fundamentals of Music Theory](https://www.coursera.org/course/musictheory), [Audio Processing for Music Applications](https://www.coursera.org/course/audio), [Digital Signal Processing](https://www.coursera.org/course/dsp) and the 5-course [Machine Learning Specialization](https://www.coursera.org/specializations/machine-learning).

In October 2015 we had a wonderful chance to attend a week-long workshop AVAST Deep Learning Sprint organized by Martin Vejmelka. In a group of about 20 people we had rented a few machines on Anselm, one of two [supercomputers in Ostrava](https://www.it4i.cz/), attended a lot of lectures about supercomputing, machine learning, deep learning, etc. Finally we worked on our real-world problems. We used [Torch](http://torch.ch) and [Keras](http://keras.io) and trained to models on Tesla GPUs. I was quite surprised how neural networks can predict complicated time-series so that we can detect anomalies. Awesome.

I realized good work needs a solid foundation in real life.

A wonderful experience was learning about [Comfort Zone Challenges](http://comfortzonecrusher.com) and attending the course [Make Yourself Do Anything](http://courses.comfortzonecrusher.com/courses/make-yourself-do-anything) by Till H. Gross. Fear has been something that held me back and I'm still fighting with it. But at least I have powerful weapons to overcome it.

Also I'm a geek and still fighting with relationships with girls. For years I was focusing too much on the work and had serious anxiety with approaching girls. Whenever I fell in love with any it became a catastrophe and it ended quickly. Thanks to Mark Manson and his book [Models: Attract Women Through Honesty](http://markmanson.net/books/models) which provides really an enlightenment on how and why people behave and what can be done with this. It is applicable to general behavior beyond dating. I consider it one of the most important books I ever read.

## Small steps with Python & Deep Learning

During the time at Wikidi and Jumpshot I was thinking what mistakes I did with HarmonEye and how to overcome them. I wanted to rewrite some parts of HarmonEye in Python, prepare suitable audio features and create ML models for chord recognition in the traditional way. Ie. give up real-time processing, which adds a lot of complexity, until the models are ready for it.

I've rewritten code to compute spectrograms including reassignemnt from scratch in Python. It is available in a git repo [music-processing-experiments](https://github.com/bzamecnik/music-processing-experiments). It works but still needs a lot of work to make it easy to use. Most importantly it allows to split the audio to blocks and compute chromagram features to feed into further ML models.

A wow moment was when I found the [Beatles reference annotations](isophonics.net/content/reference-annotations-beatles) dataset. Some students did the hard work, took 180 Beatles songs and annotated each beat and chord and each key by hand. This labelled dataset allows to train ML models using the supervised training approach.

I found this dataset in summer 2014. The question was how should I visualize a song if the classification was working 100% accurately? Also one of the drawbacks of present HarmonEye is that is shows just a signle present moment without any context. However, eyes excel at looking at 2D landscapes. The result was this kind of visualization made in Processing (P5): http://vid.me/harmoneye.

<iframe src="https://vid.me/e/Wat?stats=1&amp;tools=1" width="640" height="358" frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen scrolling="no"></iframe>

<iframe src="https://vid.me/e/is5?stats=1&amp;tools=1" width="640" height="242" frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen scrolling="no"></iframe>

Since the chords are represented as textual labels described by a formal grammar and I needed to work with binary vectors I wrote a parser in ANTLR: [chord-labels](https://github.com/bzamecnik/chord-labels). This proved useful also for preparing data for ML.

In ML it is said that data preparation can take 90% of time and training models is like a cherry on the cake. I agree. After long hard work on preparing the data I was finally able to [start training](https://github.com/bzamecnik/ml-playground/blob/master/beatles/simple_chord_classification.ipynb) some model for chord recognition. It employs convolutional neural nets of similar architecture to recognizing images and it's written in Keras.

It needs some work but the results are quite promising. Right now I'm doing the three courses on Coursera and it takes long hours. And I hope to continue on the project and write more articles about the various things.

In the meanwhile I'm very happy I had the priviledge to meet some people that are on the same wave and are also deeply interested in music theory, audio processing and machine learning. We met in real life, on the course fora, etc. In order to discuss the things I created a channel on Gitter called [audio_ml](https://gitter.im/bzamecnik/audio_ml). So let's meet there.

Ouch, this was a long article. Well, too much things happened in the last three years. Hope it was interesing and feel free to get in touch.

## Conclusion / TL;DR

- do not blindly focus on your product, focus on the pain of your customers
- set a clear big vision and small goals
- make small steps, overcome the temptation to do everything at once
- leverage Python with interactive notebooks and vast libraries
- make automatic tests and do not forget to refactor
- make experiments repeatable by fixing input data
- first play with the data as a data scientist, then integrate a working pipeline
- keep good visibility in your data, don't debug in production app to experiment
- write a blog about your successes and pains along the way
- write small tutorials
- don't forget to relax to avoid burning out
- find some girlfriend/boyfriend
- traveling is awesome way to relax, make yourself more courageous and see things from a new perspective
- don't let fear hold you back, you can do awesome things
