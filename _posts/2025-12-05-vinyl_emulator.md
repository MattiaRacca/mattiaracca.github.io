---
layout: post
author: Mattia Racca
title: Vinyl Emulator 🎶
date: 2025-12-03 21:00:00
description: Devlog of a tangible music server
tags: linux tinkering
categories: hobbies
toc:
  sidebar: right
---
> This post is 100% GenAI-free. Read [here](/blog/2025/experiment/) why.  
{: .block-tip }

This is a recap of a hobby project I intermittently worked on in the past 5 years and that only recently reached a "deployment" state worth sharing.
While writing my dissertation in spring 2020, my brain started to find *anything* that wasn't the ``dissertation.tex`` extremely interesting... and in particular one thing really caught my attention.

### The Idea
As summarized in [this article](https://magazine.raspberrypi.com/articles/sonos-spotify-vinyl-emulator) later published on the Raspberry Pi Magazine, people on Reddit were connecting Sonos speakers with NFC/RFID readers to stream music from Spotify.
By placing a nice illustration of a music album (with a hidden tag inside) in a predefined spot, the corresponding music would stream from the speakers, giving you back the tactile feeling of handling a vinyl record or a cassette tape. 
Writing about this in 2025, when the *Offline* and other digital detox movements are booming, makes me realize how ahead of time these tinkerers were for desiring to manually go through a curated music collection in a dedicated space in their homes.

<div class="row justify-content-center">
    <div class="col-6">
        {% include figure.liquid loading="eager" path="https://www.raspberrypi.com/app/uploads/2020/09/Vinyl_Emulator_anno.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
Original Vinyl Emulator Setup from pioneer Mark Hankinson.
</div>

Long story short, I wanted to build something similar to have a kind-of vinyl collection of those albums that I always go back to, like John Coltrane's [My Favorite Things](https://youtu.be/rqpriUFsMQQ) or Bob Marley's [Catch a Fire](https://youtu.be/uzv1kZBVs8w?list=PLnvVNd96RFMSNymze31EqtNLxLHqwnrKD).

### My Vinyl Emulator
Here is an attempt at separating the project in components and features that over the years were considered.

**Music Server**: I wanted something running locally on my Raspberry Pi 4, so that I could have the music server accessible both via the "fake vinyl records" and a browser interface.
I opted for a [Mopidy](https://mopidy.com/) music server to which I could talk to with any mpd client (ultimately I used [``python-mpd2``](https://github.com/Mic92/python-mpd2)) and [Iris](https://github.com/jaedb/Iris) as browser frontend. All wrapped in a unnecessarily extendable Python interface that handles the card reader → album database → mpd server pipeline.
Overall, the easiest and chill part of the project, given my background.

**Vinyl Interface**: I like tinkering with electronics, but I'm not super comfortable with it. I therefore opted for a ``MFRC522`` RFID reader that *seemed* simple to use. Yeah, seemed... I had a couple of hiccups:
- being afraid of burning my RPi4 and triple checking the wiring at every test with the sensor's datasheet. Very time consuming, until I basically taped the jumper wires and create my own connector. Like that I only hat to check that the top-left connector was going on the correct RPi pin.
- switching to a Raspberry Pi Zero 2 for the sensor side of things, because debugging on the RPi4 was cumbersome (I have other stuff running on that machine, above all a [Pi-Hole](https://pi-hole.net/) instance).
- a sudden sensor failure, cause by a jumper wire that randomly died (do wires fail? apparently).
- inconsistent card readings, only to find out that there are 2 standards of RFID cards, and the ``rc522`` only works with 13.56 MHz NFC Type 2 tags.
- other inconsistencies in the readings, this time caused by not high enough antenna gains.
- using sketchy Python library interfacing to the sensor before finding the great [``pi-rc522``](https://github.com/ondryaso/pi-rc522).

Many rookie mistakes, made me really appreciate the saying *You gotta break a few eggs to make an omelette*.

**Audio Interface**: Here as well I was in unfamiliar territory. I'm not a hardcore audiophile and hooking an audio system to the RPi seemed a bit beyond my skills. I therefore opted for a Sony Bluetooth speaker we were not heavily using anyway, and made it the default audio sink of the RPi4. 

**Overall Setup**: Here is where the real hybris happened. I liked how people were hiding the sensor (plus sometimes the music server) inside elegant wooden boxes, but I wanted something more personal. Inspired by this [DIY project](https://www.instructables.com/Commodore-PI/), I wanted to lean into the retro-computing vibe and have a Commodore 64's (the first computer I ever interacted with) Datassette (the device that reads the cassette tape) as the case for the emulator.
Furthermore, I wanted to re-purpose the buttons of the Datassette for the classic music commands: play/pause, next, repeat, and so on. *Yeah...*

If for electronics I'm a cautious amateur, I'm a complete rookie about anything mechanical. I bought a not-functioning Datassette on Ebay, made space inside for sensor+pi+ssd, made a small circuit to wire push-buttons to the pi (with a [``pcf8574``](https://www.ti.com/lit/ds/symlink/pcf8574.pdf)), and then hit a wall. I could not get the push-button to stay in place inside the case under the original Datassette's buttons, I did not have a 3D printer to make some holders (that I doubt I could anyway 3D model them in the first place)...

This side of the project almost killed the whole thing, until earlier this year my wife wanted for our kid [*"some speakers that play music/stories when you place a little toy on them"*](https://tonies.com/en-gb/). I complained that that was basically what I was doing with my emulator, to what she answered *"well, then build it before Xmas"*.

### The End Result
<div class="row justify-content-center bottom">
    <div class="col-4">
        {% include figure.liquid loading="eager" path="assets/img/blog/vinyl_user.png" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
    <div class="col">
        {% include figure.liquid loading="eager" path="assets/img/blog/vinyl_selection.jpg" class="img-fluid rounded z-depth-1" zoomable=true%}
    </div>
</div>
<div class="caption">
A happy user of the setup and an interesting selection of available albums.
</div>

And that's where I stashed my Millennial's nostalgia about retro-computing, dropped the Datassette idea, and speed-run the setup. The PiZero2 hidden in a low cabinet and RFID cards (sticked inside card-game sleeves, with paper covers made with a handy LaTeX template -- sticking to my knitting again here) placed on a target area. Done. Some minor bug/pain points fixed, some feature requests from family members, but the vinyl emulator is smoothly running since September now. Not as fancy looking as the OG ones, but functional and childproof (for now).

#### Miscellanea
- **[Kanban board](https://en.wikipedia.org/wiki/Kanban) on Notion**: Having a dedicated space for a project that saw work done on it maybe once a week was really helpful. The Kanban was not exactly used for the intended purpose of "tracking task through different stages and priorities" but rather as a project status reminder, where each task was packed with links to relevant material and information.

<div class="row justify-content-center">
    <div class="col-6">
        {% include figure.liquid loading="eager" path="assets/img/blog/vinyl_kanban.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
The aforementioned Kanban board
</div>

- Shout-outs to [Puddletag](https://docs.puddletag.net/), a handy software for tagging music files.

- Every time I add some music to the RPi's SSD, a little voice in my head whispers _"this drive will fail, where will your music go?"_. Ultimately I'll need to setup a proper NAS at home and give the RPi a honorable retirement.

- Of course, anything "git-able" has been versioned. If anybody is interested in this, I can make the repo public.

#### Closing Remarks
No poetry here. It just feels good to have done something that is far from perfect but that essentially works. Get out of here now 🙃