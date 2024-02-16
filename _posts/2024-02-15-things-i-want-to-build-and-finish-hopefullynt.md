---
layout: post
title: Things I want to build (and finish, hopefullyn't)
date: 2024-02-15 21:10 -0300
---

My mind winkers for things to be mad at. I use to canalize this into some
house-fixing thing, but I've found/had something very interesting ideas over
here:

Ah, by the way, those fixes never happened.

## A VERY COOL looking tiny mechanical keyboard

<iframe width="560" height="315" src="https://www.youtube.com/embed/JqpBKuEVinw?si=Tm1sIECF4eYUznnQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Too much detail into the PCB board in the video IMO, but looks really cool and
seems to be indeed not expensive. Some things I don't yet realize from the video
is how the two parts connect to each other through that... miniplug? 0.o

Need to check also if Arduino board can replace that _[keyboard-dedicated
Elite-C microcontroller](https://keeb.io/products/elite-c-low-profile-version-usb-c-pro-micro-replacement-atmega32u4)_,
since it's an [ATmega32u4](https://store-usa.arduino.cc/products/arduino-micro).
But have to say, that USB-C port is a wise decision.

## The SIMPLEST web server in Golang you will ever found

I want to build software.

You know, _"put things together and do stuff"_ TBH. Not really an original idea.
I want to build something that I could use to compare myself to big people, like
the Nginx webserver. So let's build a web server with a few precommitments, BUT
for all of them the phrase _"at leatst for now"_ applies:

* External library usage at bare minimum.
* Only HTTP 1.1.
* No HTTPS.
* [Open-closed (you know... SOLID and all that shiny
  words)](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle):
  middleware-oriented, YAML-oriented.

I don't know. Let's see [waas sappening](https://youtu.be/-zgTVBtA-iI?si=knYMH_GaYxJVUx1e&t=192).
