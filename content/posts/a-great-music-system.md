---
title: "A great music system"
date: 2024-03-08T09:00:00+01:00
description: "A summary of my current music setup"
tags:
  - music
summary: |
  A summary of my current music setup
cover: "/img/a-great-music-system/cover.png"
coverAlt: "A photo of the music system I use showing the R5 player unit and the Pro-ject E1 turntable"
coverCaption: "A photo of the music system I use showing the R5 player unit and the Pro-ject E1 turntable"
images:
  - /img/a-great-music-system/cover.png
---

Over the last few months I had the opportunity to spend some time ironing out a music system that would marry analog and digital listening, log everything I play for recommendations and tracking of future releases, and collect all physical releases I own along with accompanying notes and reflections.

## Music library

I own music in different formats:

- Vinyl records
- CDs
- High-resolution files (usually in [FLAC](https://en.wikipedia.org/wiki/FLAC) format)

Why 3 formats?

- Vinyl is usually the best format for old records before the 1980s, as it was the dominant medium. For a lot of music, there’s literally no other available physical format. The format had a massive revival in the last few years, and many artists release high-quality, 180g vinyls which sound amazing.
- CDs tend to be the target format for music produced in the late 1980s up to the 2010s, before streaming took off. A FLAC digital file is absolutely fine as a replacement for a CD quality-wise, but I’m a sucker for booklets and limited edition goodies. Particularly in the UK, second-hand CDs are sold in pretty much any charity shop, and more often than not you can get very good deals.
- Digital files are convenient for listening on the go, and are also sometimes the only option for bands who don’t produce physical releases. Both record labels and websites like [Bandcamp](https://bandcamp.com) give the option to purchase in FLAC format.

What’s crucial here is that for music I really love, I do wanna own it for a few reasons:

- For new releases, I want to compensate the artist or band, and the best way is to buy their music (and attend their gigs).
- Streaming platforms can pull content anytime, and in many cases they don’t have all releases I want to listen to.
- Once I have albums in my library, I can organise them how I prefer (both physically and digitally).

A priceless extra is that my 2-year old daughter loves CDs because they look like little books but they make music, and vinyls because they’re big and rotate.

## Hardware

{{< image src="/img/a-great-music-system/music-system.png" alt="A photo of the music system I use showing the R5 player unit and the Pro-ject E1 turntable" >}}

There are a few moving parts:

- A [Synology NAS](https://www.synology.com/en-uk) storing all digital files.
- A [Ruark R5](https://www.ruarkaudio.com/products/r5-high-fidelity-music-system) music system (which can play CDs directly, and that has an integrated amplifier for the turntable). It’s been recently discontinued, which does make a bit uneasy, but the manufacturer still supports it and I reckon I can go on for a few years before having to evaluate a replacement.
- A [Pro-Ject E1](https://www.project-audio.com/en/product/e1/) turntable, playing through the R5. Pricy, but it really just works.
- A [Raspberry Pi 4](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) with a [HifiBerry DAC2 Pro](https://www.hifiberry.com/shop/boards/hifiberry-dac2-pro/) hat, which allows the PI to play audio with great quality via the R5.

{{< image src="/img/a-great-music-system/raspberry-pi.png" alt="A photo of the Raspberry Pi 4 with the HifiBerry DAC2 Pro hat" >}}

None of this is cheap and there’s certainly more affordable options particularly if you’re able to source a different speakers/amp/turntable combo. The big advantage of using a Raspberry Pi (or an equivalent off the shelf solution) is that you separate “brain” from “muscle” (and the Pi + DAC hat are relatively cost-effective).

## Playing music

The whole system revolves around Plex: the NAS runs [Plex Media Server](https://www.plex.tv/en-gb/personal-media-server/), while the Raspberry Pi runs [PlexAmp](https://www.plex.tv/plexamp/) headless. My laptop and phone run PlexAmp as well.

Setting up PlexAmp headless on the Pi required minimal work thanks to the excellent [bash-plexamp-installer](https://github.com/odinb/bash-plexamp-installer) project, which makes it a breeze to both install and update the software and correctly configure the DAC2 Pro hat. Couple of reboots and everything worked like a charm (and still does).

With this setup, I can use any device to access my music collection, and play it through the Pi if I’m at home, or on-device if I’m out.

The Plex Media Server instance is configured to scrobble to [Last.fm](Last.fm), so everything gets logged automatically no matter where I play it from.

When I play physical releases, I use the excellent [OpenScrobbler.com](OpenScrobbler.com) web application to search for the album I’m playing, and scrobble it.

I’m now trying out [Tidal](https://tidal.com) as a way to listen before buying, as it provides hi-fi quality, pays artists better than other platforms, and it’s deeply integrated into Plex (which means I don’t need to use a separate app, and can rely just on PlexAmp).

If the Tidal experiment is a success, I’ll stop my subscription to Apple Music.

When music is played by the Pi, the R5 system has no information on what's currently playing. I can read that on my phone, but for other people in the house I programmed a small automation that can be run with Siri, so that anyone can ask "What's playing?" and get a good answer. This took some lightweight reverse engineering of the PlexAmp web application, and I'm not expecting it to be rock solid as it's based on private APIs.

In short, the PlexAmp web application polls an endpoint that returns a fairly comprehensive playing status with metadata information about the artist being played, both for local music in the Plex Server and on Tidal. As PlexAmp is written in Node.js, I wrote another small Node.js application to poll the same endpoint, and parse the information I need. I then created an iOS shortcut to hit my Node.js application, and read out the response in a human readable format. The shortcut is automatically available on all devices, and can be run via Siri by its name.
 
## Discovery
 
Algorithmic recommendations are now very precise - no matter the platform. I’ve come to find them _too precise_ in the sense that they don’t stray off the beaten path.

I prefer to monitor different sources, particularly when it comes to my core preference, progressive rock:
 
1. [Prog Magazine](https://www.loudersound.com/prog), which is an old-school monthly zine (both printed and digital) that covers pretty much anything happening in the progressive scene. I have a digital subscription, and read it on the iPad where I can take notes and add albums to a queue.
2. The reviews RSS feed for [progarchives.com](progarchives.com), which is a fairly old progressive rock community website where people publish reviews of whatever album they can think of. The RSS format is great because I can read the review, see the cover art (which does a lot for me), and from that decide if I’m interested to explore more.
3. The occasional visit to a few subreddits, just to get a feel of the community is looking at.
4. Newsletters from specific record labels that tend to publish music I like (e.g. [Kscope](https://kscopemusic.com)) or [Karisma Records](https://www.karismarecords.no/)) 
5. [The Album Years](https://thealbumyears.com/) podcast with Steven Wilson and Tim Bowness, which provides a **very opinionated** list of significant music releases roughly from the 70s till the 90s. Only downside is that it’s very UK-centric, and I wish there were similar lists for other parts of the world.

Once I’ve listened to something, and the artist is scrobbled to Last.fm, I’m able to monitor new releases via the excellent [MusicHarbor](https://apps.apple.com/us/app/musicharbor-track-new-music/id1440405750) iOS app. The app imports the list of artists I have on Last.fm, and keeps track of all releases by these artists. The import process is easily done manually via a few button presses in the app, and I do that once a month.

Each Friday (release day!) I get a new batch of albums to check  out, and I add whatever picks my attention to my queue.

## Physical collection management and notes

Managing a physical collection is a relatively solved problem: with [Discogs](https://discogs.com), one can simply scan or search for the correct release, and add it to their own account.

While this is great to track value and conditions of the item, it's not geared towards managing a collection with associated notes about the artist(s), or lyrics. It's also not possible to arbitrarily draw connections between albums.

I'm currently trying out [Obsidian](https://obsidian.md/) with the [MediaDB](https://github.com/mProjectsCode/obsidian-media-db-plugin) plugin. This lets me quickly add every album via the [MusicBrainz](https://musicbrainz.org/) search API (without matching to an exact release, i.e. you can't pick the correct year/label/country/limited edition).

Each album becomes its own file with a set of metadata information I can query through a couple of plugins. On top of that, I can write notes and associate albums with simple internal links.

As the data is stored as markdown files with a YAML front matter, there’s no risk of lock-in, and if I ever need to process the data for further analysis or visualization, I can write my own program that does that.

Obsidian is available on all platforms I use, and syncs both data and settings without having to do anything special.

## Too much?

I often ask myself if this system is too complicated, but I personally find that the experience of using it is simple at the expense of a reasonable amount of hidden complexity. I’m also aware that my requirements are many, and that’s because music is pretty much my only significant hobby. It still gives me the same joy I felt as a teenager, and still manages to surprise me even when I think I heard it all.

