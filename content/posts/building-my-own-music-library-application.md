---
title: "Building my own Music Library Application"
date: 2025-09-14T11:42:42.000Z
draft: true
description: |
  Reflections on building a music management application in Phoenix LiveView
tags:
  - elixir
  - phoenix
  - music
  - software development
keywords:
  - elixir
  - phoenix
  - music
  - software development
images:
  - img/a-short-profiling-story/before.png
summary: |
  Reflections on building a music management application in Phoenix LiveView
---


{{< image src="/img/building-my-own-music-library-application/commits-over-time.png" alt="A screenshot of the commit activity over time for my application, showing weekly work without interruptions." >}}

Over the past year I've consistently worked on the most ambitious personal project I've ever tackled: an application to manage my physical music collection.

I wrote about collecting records and buying music [here](/posts/a-great-music-system/) - feel free to read that first for a rationale.

I've recently crossed the 15k LOC mark (between implementation and tests) which is by no means a large application, but it's enough to start reflecting on the road so far and what lies ahead.

## Goals

When I set out to build this application, I had a very precise vision:

1. It needed to be **fast**: adding records, searching them, compute stats, etc. Everything needs to be snappy.
2. **Well-designed**: obvious interactions, no weird scrolling to do things on mobile, easy to read, visually appealing (subjective). It should not emanate "half-assed-admin-dashboard" vibes.
3. **Easy to operate**, with portable data.

## Stack

I picked Elixir, Phoenix and LiveView because frankly that's what I know and use at $WORK as well. I only picked SQLite as a database, which was a first for me, as I thought it would give me a boost in reaching goal 1. and 3. (it did, but with some caveats).

For almost 10 months, I had the application deployed on Fly.io, but decided then to move it to my own infra because I wanted an affordable, EU-hosted server with decent performance without sacrificing the developer experience of pushing, running CI and having the application deployed automatically. To do that, I picked a virtual server on Hetzner and deploy via Coolify.

## Experimenting with AI-assisted development

I tried a few times to experiment with AI-assisted development, specifically with Claude CLI, for very specific use cases:

1. I wanted to build something I had absolutely no knowledge of: for example, I added a vanity feature where the UI shows the dominant colors for each album cover. In that case, I asked Claude to research approaches, and write a fast-but-inaccurate extractor, and a slow-but-more-accurate one. It produced a summary of available techniques, and two implementations which seem to work reasonably well.
2. I wanted to test an idea for a feature and see if I really needed it. I would know exactly how to build it, so a fast-tracked prototype would be desirable. When confirmed, I would start refactoring it for longer-term maintanability.

I spent a few hours all in all trying to get prompt, usage rules, documentation etc. in shape for the LLM to work effectively and despite my best efforts I keep noticing the same problems:

1. Too many code comments which really don't provide any value
2. Overly nested markup, with arbitrary stylistic choices that sometimes wouldn't match existing design, or would completely ignore mobile devices
3. Additional unrequested functionality that complicates implementation without reason
4. Inability to completely follow an agreed plan, particularly when it comes down to writing tests

I suspect that part of the problem is on me - while I tried to iterate and produce better prompts (both as context/memory) and how precisely I instruct the LLM, I also find the process somehow tedious and devoid of the human aspect of working with another person.

## The hard parts

- Delightful user interactions are hard to come up with: it's often the case of something being obvious in hindsight, with enough time spent using the app to slowly mature the conviction that something could work/look better. For example, I added exploding confetti for when I purchase a record that I had in my wishlist - a small celebration which I absolutely love.
- Architecturing domains and contexts inside the application is difficult: I have periods where I think I should increase the amount of namespaces and boundaries, and periods where I think the best design would be to merge large areas together. I try to use some sort of objective approach (e.g. dependency graph, data isolation) but it still _feels_ a process based on intuition, and prone to yak-shaving.
