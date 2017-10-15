---
layout: post
title: Discovering a Strange Signal
keywords: rtlsdr, radio, signal
description: The tale of 915.519652MHz.
---

## Introduction
I got my hands on a software defined radio recently. It's really interesting to play around with -- from things like receiving aircraft positional data, to municipal trunked radio. I spent a good chunk of time just scrolling through the electromagnetic spectrum, hoping that I'd come upon something interesting. I got a lot more than I asked for, with 915.519652MHz.

## Ghost in the Spectrometer
![image of spectrum](https://i.imgur.com/LqkKfwT.png)

The first thing that caught my eye was not the gigantic FM transmission in the middle, but all those tiny little blips. Most likely, they're just smart meters beeping away. Eventually, that FM waveform started to call out to me. I thought it was weird for an FM broadcast to be happening at this wavelength, because it is usually just radio silence (excluding the smart meters).

After listening to it, I heard a bizarre mixture of noise, gunshots, explosions and every single other disconcerting sound you can think of. I wish I made a recording, but the transmission abruptly stopped before I could do so. The sound was somewhat periodic, with strange distortions happening at an interval. I, for the life of me, could not figure out what could be causing such a transmission at this frequency.

![silence](https://i.imgur.com/NpUGXhH.png)

Here's a screenshot from a brief period of silence. For you experienced radio theorists out there, it might be clear to you what's causing this pattern. But I have absolutely no clue. From this picture, multiple spikes of a decreasing amplitude can be seen around around a larger peak. To me, this kind of looks like some inadvertent transmission caused by some faulty hardware, especially since it was broadcasting on some fractional frequency.

I decided to leave it at that, as a mystery that would never be solved. Alas, I could not have such comfort. The transmission returned at a similar time the next day, to my confusion. This time, it was even more bizarre.

## The Second Coming

[Recording 1](https://streamable.com/f27j5)<br>
[Recording 2](https://streamable.com/jtbp0)

Here are two recordings I made of the transmission. I honestly had no idea what to think at this point. My mind could not rationalize why the Space Jam remix of Renai Circulation would be broadcasted at 915MHz. A while later, I started hearing a Film Theory video on the Emoji Movie. Of course, this latest broadcast helped me understand a few more things about this transmission.

## Analysis
Some facts I collected:
- The broadcasts are temporal. They take place sometime around the day.
- They rapidly switch between different sound clips.
- Most of these clips sound like YouTube videos.
- Sometimes, the broadcast is completely silent. However, there is still a waveform shown, with multiple decreasing peaks.
- Sometimes, the broadcast is completely silent, with no waveform.
- The broadcast is on a strange frequency, and doesn't snap to a nice number.

This made me start to think. What is causing this broadcast? Since YouTube videos were being played at a random pacing, I deduced that someone must have been casually watching YouTube videos somehow. Somehow, the way they were watching the video had to have caused radio emissions.

### Theory 1: The RF Audio System
The first idea I had is that someone might be using some RF solution to broadcast audio from their computer to some audio system or television. This would explain why YouTube videos were playing, rapidly switching as a person nagivated videos. The waveform being present while no sound was playing would represent the sound system being on while nothing was being player, and the waveform being non-present would represent the sound system being off. However, this didn't explain a few things. For one, I have never heard of the use of the 33cm band for audio systems before, especially some irrational frequency. It also didn't explain all the interference present around the signal, with multiple copies of the original but at progressively quieter intervals.

### Theory 2: Spurious Transmissions
Another idea I had is that someone might be using some FCC violating speakers/sound system, that emits the audio as FM radio as a byproduct. I've heard of speaker systems receiving radio transmissions but not the other way around, so the idea doesn't seem ridiculous to me. This would explain the irrational frequency, since no one would have designed the equipment to broadcast at that strange frequency. This theory also explains the multiple diminished copies of the original signal, which may be a result of multiple resonances that lead to transmission. But, who knows?

### Theory 3: I'm completely wrong
I still have no clue, so I would not be surprised if all my conclusions were wrong. If you know what it possibly could be, please tell me so I can put this to rest.

Anyways, I'm kinda tired of talking about this so I'll leave it at that. Cheers. 
