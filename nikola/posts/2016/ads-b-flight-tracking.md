<!--
.. title: ADS-B Part 1 - Flight Tracking For The Curious
.. slug: ads-b-flight-tracking
.. date: 2016-09-09 17:34:00+02:00
.. tags: flight tracking
.. category: flight tracking
.. link: 
.. description: 
.. type: text
-->


Every moment tens of thousands airplanes are in the air and many are flying around. We can analyze their flights. In this series we will explore how to capture flight information using a DIY ADS-B receiver based on Raspberry Pi.

<img src="http://i.bohumirzamecnik.cz/adsb/vrs_2016-07-08_12-53-51_t.png">

<!-- TEASER_END -->

Altough there's plenty of focused tutorials on the web on particular topics, the reality is never straighforward. Here I'd like to provide a comprehensive guide based on my own experience with building an ADS-B receiver, cover the questions and decisions and when needed point to the existing tutorials for more details.

## Why flight tracking?

We can see airplane tracks on the sky virtually in every civilized area. Sometimes you can hear a loud hum of a plane or helicopter flying by. When you live near an airport like I was living you can ocassionally see planes going to land every two minutes. They fly so low you can read their logos at the bottom.

<a href="http://i.bohumirzamecnik.cz/adsb/fr24_2016-09-08_00-02-18.png"><img src="http://i.bohumirzamecnik.cz/adsb/fr24_2016-09-08_00-02-18_t.png" title="FlightRadar24"></a>

One day you may ask where the planes are coming from or going to. Then you find services like [FlightRadar24](https://flightradar24.com), [PlaneFinder](https://planefinder.net), [Flightaware](https://flightaware.com/) and many others that show a near real-time map of almost all planes in the air. A tremendous amount of information is available beyond the source and destination airports and current position, such as the whole path, model and manufacturing info, photos, previous flight info, etc. There are communities of people hunting for best plane photos or providing accurate plane information.

### More questions

If you're crazy into data and visualization, one day you may realize the data provided by such services for free are not enough to answer your curious questions.

- How the plane paths look in mass? Ie. what's the distribution of plane paths on the map?
- Are there any "corridors" used as highways or do planes fly just randomly?
- If so, how does this distribution change in time? Are there any periodicities?
- How does the distribution of altitude vs. distance on the path look like when projected into a 2D plane?
- What about distance to the source airport? Can we distinguish round-trip flights?
- Can we play back interesting flights quickly afterwards?
- Can we learn a machine learning model of some normal mode of traffic and detect anomalies, eg. emergency situations?
- How does the normal traffic evolve over time?

## How are the data gathered?

Where the data come from? There are several sources of flight data, including classical radar, ADS-B and MLAT.

Traditionally airplane positions were detected by airport radars. The flight tracking services use an aggregated source of radar data as one of their sources. The problem is that data from official agencies are sometimes artificially delayed.

### ADS-B

However, there's another more abundant source of data, ADS-B ([Automatic dependent surveillance – broadcast](https://en.wikipedia.org/wiki/Automatic_dependent_surveillance_%E2%80%93_broadcast)). In short in order to prevent plane collisions planes broadcast messages with their identification, location and velocity over microwave radio. The nice thing is with today's technology and software we can receive such signal with just a very cheap hardware and open-source software. My receiver cost around $150 in total, and of course some work.

Although ADS-B receivers are standard part of airport equipment, there's a whole community of people building and operating their own ADS-B receivers. So the other source of flight tracking data is ADS-B from the community.

The flight tracking services offer professional data for quite a high subscription price. But when you provide them with your raw data you can get in exchange the premium services for free.

Besides that there exist community data exchanges, such as [ADSB Exchange](http://adsbexchange.com/) or [CZADSB](http://czadsb.cz/) where the people share raw unfiltered data. Altough setting this up might be sometimes more rough around the edges the data might be worth the extra effort.

It should be noted that ADSB Exchange offers the merged data for free and you're not required to provide data in exchange. On the other hand providing own data is a way to support the site, so that it doesn't die, and it can give you more accurate data near your location and... well, it's just plain fun to build something yourself. After you did it you can be proud of youself and have learnt a lot.

### MLAT

Another source is called [MLAT](http://www.multilateration.com/surveillance/multilateration.html) (abbreviation of Multi-LATeration). It's an almost magical way of locating a plane even if it does not publish it's position in its ADS-B stream! Simply said we can compute the position of a transmitter (it in case a plane) just by observing variously delayed signal from multiple receivers. For 3D position 4 receivers are enough.

The only requirement is that the receivers must communicate with each other. For that purpose there's a set of open-source client-server programs to acomplish this task: `mlat-server` and `mlat-client`. An instance of `mlat-server` typically runs on the exchange and we only install and set up the `mlat-client`. Eg. the FlightAware receiver pack already contains a `mlat-client`.

Are you sold for building your own infrastructure for receiving ADS-B data?

## The architecture of an ADS-B receiver

We want to fulfil the following primary features:

- receive the ADS-B microwave signal in the 1090 Mhz band
- decode the signal into a stream of messages
- forward the ADS-B stream into services we want to support:
  - commercial:
    - FlightRadar24.com
    - FlightAware.com
    - PlaneFinder.net
  - community:
    - ADSBexchange.com
    - CZADSB.cz
- cooperate with MLAT servers where available
- prepare a stream for our own capturing and archiving

Other features such as capturing the our data or merged exchange data, archiving them and processing them is out of scope of this tutorial and hopefully will be covered afterwards.

<img src="http://i.bohumirzamecnik.cz/adsb/adsb-receiver-architecture-overview.png">

Here we'll try to briefly go through the steps outlined above. Each step will be then covered in detail in a separate article within the series.

### Receiving signal

First we need to recieve the microwave ADS-B signal from the planes in the 1090 MHz. Altough using some special hardware is possible it is not required. Some clever hardware hackers found out that this can be done using plain DVB-T tuners for terrestrical digital TV! Such deviced are based on the concept of Software Defined Radio (SDR), where the modulation and demodulation is not performed by custom electronic circuits but just in software! Such DVT-T sticks
can be easily found in electro stores or in e-shops. The only catch is to find one with suitable firmware. We will explore which sticks to buy in the detailed article.

In order to feed the signal into the receiver we have to use an antenna and conect both with cables and possibly some adaptors. Typically we will need an omnidirectional antenna to catch plane info all around. An optional part is an additional amplifier and or band-pass filter which can improve the receiving capabilities (specifically we could capture planes at much futher distance).

A crucial requirement is a place where the antenna is mounted. It can only catch planes with direct visibility. So a roof with good 360-degree view of the sky is ideal. Also the place needs electricity and internet connection.

### Hardware for processing the signal

We need a computer to process the signal from the DVB-T stick. Any computer with Linux, OS X or Windows will suffice, however Linux is probably most widely used and supported for this task. The computer doesn't need a lot of computing or electrical power, but needs to be always on. A very popular platform choice is Raspberry Pi.

Despite its credit-card size and low power consumption it's a fully featured computer capable of running Linux.

### Decoding a stream of messages

Once we have the radio signal ready in the receiver we need to demodulate and decode the messages into some standard text or binary stream format. We need to install drivers for the stick and the decoding software. The most popular choices are the following. A DVB-T driver is called `rtlsdr` ("SDR for Realtek chipset") and a decoder is `dump1090`.

The basic function of the `dump1090` program is to decode the raw signal into a stream of ADS-B messags and provide it via a network socket. The stream can be in several formats (plain text CSV, binary Beast or raw hexadecimal).

We can then forward these streams into exchanges we want to support or just read it from our own service.

Beyond that, `dump1090` can be used to merge streams from other receivers and publish it as a single stream - it acts as a simple exchange.

Since dump1090 is a popular open-source software there exist many forks, each with some different enhancements. We will evaluate them a choose some fork that's suitable for our needs.

### Sending message streams to exchanges

The flight tracking services or community exchanges typically provide their own software for feeding them. Most often they can read from `dump1090` or even bundle it inside. We will learn how to set up feeding multiple exchanges on one machine. Sometimes we can follow their very nice and polished tutorials, sometimes we will have to set it up in some custom way.

### Viewing planes on the map

It would be boring just to look at the text data when debugging our set up or we can just be curious to see what our receiver sees. For that purpose there are multiple programs to visualize the planes from our own stream on a map - a simple and rough version of FlightRadar24 interface. We will explore [VirtualRadarServer](http://www.virtualradarserver.co.uk), `dump1090` web UI.

### Storing the data for archiving

We can explore a prototype way of storing the data using the tools already available. This is by no means meant for production usage, just to as a proof that we are indeed able to storing our own data.

## What's next?

In the next post of this series we'll present the architecture of our system in more detail.

### Slack channel

If you have any questions or just would like to chat about flight tracking, feel free to check the [ADS-B Flight Tracking](https://adsb.slack.com) Slack channel. Automatic invitation is on [adsb.signup.team](https://adsb.signup.team).

<a href="https://adsb.signup.team"><img src="http://i.bohumirzamecnik.cz/adsb/adsb-slack.png"></a>