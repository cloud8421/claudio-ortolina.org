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

I picked Elixir, Phoenix and LiveView because frankly that's what I know and use at $WORK as well. I only picked SQLite as a database, which was a first for me, as I thought it would give me a boost in reaching goal 1. and 3.

For almost 10 months, I had the application deployed on Fly.io, but decided then to move it to my own infra because I wanted an affordable, EU-hosted server with decent performance without sacrificing the developer experience of pushing, running CI and having the application deployed automatically. To do that, I picked a virtual server on Hetzner and deploy via Coolify.

In a parallel universe, I would have built this application in Swift and have it work on Mac, iOS, and iPadOS, using some sort of framework for data synchronization. The big benefit in that case is that I would be able to use the application offline. I don't know anything about that world though, and didn't have the bandwidth to skill up on it before building the application I wanted. Plus, I'm sure I would have had to compromise on quality, design or features due to my skill level.

## Guidelines

I'm trying to follow a set of loose guidelines:

1. Be conservative with dependencies, and only add what is really needed.
2. Stick with Phoenix and LiveView's architectural suggestions and assumptions in order to minimize maintenance burden down the line. Challenge this guideline if and only if the application gets to the point that making changes is substantially difficult.
3. Try and reuse logic and components, but do not fall into the trap of extracting everything just for the sake of it.
4. Do not write an excessive amount of tests, and prioritize end to end tests over any other type.
5. Do not shy away from using JavaScript if it provides the best user experience.
6. Leverage as much as possible the database.

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

In all instances, working with AI mapped loosely to this process:

1. Explain feature or need - LLM gets it
2. LLM comes up with a plan, and on my request writes it to a file
3. I edit the plan, making sure that details are correct
4. In a new conversation, the plan is read and executed

In terms of results, usually we get to:

1. An initial implementation which works on the surface, but errors out outside the golden path. Such errors are not visible at compile time. Even when writing tests, the LLM somehow misses key parts of the functionality.
2. I fix enough issues to have a working vertical slice, so that I can test the feature and make sure it has value, and I like how it feels using it.
3. Assuming I'm happy to keep it, I start replacing the guts of the implementation, reduce the markup, remove unnecessary extra little features, extra copy, etc.
4. The end result is something I understand top to bottom, re-written between 30 to 50%, with a smaller surface.

## SQLite

I've been impressed by SQLite as it really packs a lot of punch despite the small size.

1. I'm using the FTS5 extension to provide full-text search. The search index is created separately from the main data tables, kept up to date via triggers, and where needed data is passed through a unicode filter that lets me easily type "Bjorn" to find "Bjørn" (which is not as simple as it seems).
2. I'm storing binary blobs for record covers directly in the database in a dedicated table, so that ALL of the data managed by the application is inside one single file (which now adds up to around 300MB).
3. For data that I pull from external APIs and where I'm allowed to, I usually keep copies of responses associated with each record, stored as JSON blobs in dedicated columns. This is helpful to quickly build additional features that leverages other parts of the data, and saves me from hitting external APIs more than once per record update. JSON support in SQLite is not as advanced as in Postgres, and the API is a little bit different, but there's pretty much everything you need to create/update/query JSON data.
4. Backups, restore, pulling data from prod are a breeze - I just need to download a file.

## The hard parts

- Delightful user interactions are hard to come up with: it's often the case of something being obvious in hindsight, with enough time spent using the app to slowly mature the conviction that something could work/look better. For example, I added exploding confetti for when I purchase a record that I had in my wishlist - a small celebration which I absolutely love.
- Architecturing domains and contexts inside the application is difficult: I have periods where I think I should increase the amount of namespaces and boundaries, and periods where I think the best design would be to merge large areas together. I try to use some sort of objective approach (e.g. dependency graph, data isolation) but it still _feels_ a process based on intuition, and prone to yak-shaving.
- Working with third party HTTP APIs remains a tedious job, no matter how well documented, easy to use they are. One needs to mock responses, save fixtures, handle the ceremony needed to be able to maintain fast and reliable tests. Some libraries make this easier (thank you Req) but there's so much you can do.
