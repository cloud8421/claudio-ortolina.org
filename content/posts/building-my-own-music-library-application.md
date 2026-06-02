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

Over the past year and a half I've consistently worked on the most ambitious personal project I've ever tackled: an application to manage my physical music collection.

I wrote about collecting records and buying music [here](/posts/a-great-music-system/) - feel free to read that first for a rationale.

It's now large enough to start reflecting on architecture, learnings, and the road ahead.

## Use cases

- Track records I have, with plenty of **useful metadata**.
- Track records I want to buy.
- Instead of adding record metadata by hand, leverage the [MusicBrainz](https://musicbrainz.org) API and dataset. When MusicBrainz doesn't have the record I need, I just create it there, and it's instantly available in my application.
- Integration with [Last.fm](https://last.fm): scrobble records I listen to, and pull my scrobbling history for further analysis.
- Add records by search or barcode scan (useful when buying bundles).
- Group records by arbitrary criteria (I called them sets), e.g. all autographed ones.
- Write notes about records and artists.
- Ask any question about an artist or record, via a careful integration of AI.

## Goals

When I set out to build this application, I had a very precise vision:

1. It needed to be **fast**: adding records, searching them, compute stats, etc. Everything needs to be snappy.
2. **Well-designed**: obvious interactions, no weird scrolling to do things on mobile, easy to read, visually appealing (subjective). It should not emanate "half-assed-admin-dashboard" vibes.
3. **Easy to operate**, with portable data.

## Stack

I picked Elixir, Phoenix and LiveView because frankly that's what I know and use at $WORK as well. I only picked SQLite as a database, which was a first for me, as I thought it would give me a boost in reaching goal 1. and 3.

For almost 10 months, I had the application deployed on Fly.io, but decided then to move it to my own infra because I wanted an affordable, EU-hosted server with decent performance without sacrificing the developer experience of pushing, running CI and having the application deployed automatically. To do that, I picked a virtual server on Hetzner and deploy via Coolify, and the difference is night and day.

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

A personal project has also been an opportunity to experiment with LLM-assisted development, and I went through a couple of iterations on that front as well.

Initially I used Claude Code (Opus 4.6, and Opus 4.7), with a set of community skills and a couple of custom skills, a curated CLAUDE.md file, and consistent use of plan mode. It was producing decent results primarily because it could leverage a solid architecture and existing patterns.

It allowed me to quickly experiment with some features that would have required me quite some time to implement, like color extraction from record covers.

With time, the Claude Code harness started introducing more complexity I didn't need, and I found myself having to configure things to avoid excessive subagents use, or battling with the intrinsic inability to completely follow guardrails I wanted to set.

That's when I decided to try using [Pi](https://pi.dev) because that way I could just create my own set of rules, tools, and process.

Pi is essentially one step away from creating your own harness. It's completely hackable, can reload itself at runtime, and can modify itself.

The combination of Pi with self-hosted infrastructure meant I could _very quickly_ build the tools I wanted, like a TUI for production errors that doubles down as a LLM tool. I can browser errors from the harness, and the LLM can do the same.

For development of large changes, I'm using [Backlog.md](https://backlog.md/) because it keeps everything in the repo, and it lets me iterate with triage, planning, execution, and verification with full control over each step of the loop.

Model-wise, I have Pi now setup to use either GPT 5.5 (sparingly) for complex plans, and Deepseek V4 (both Pro and Flash) for implementation and checklist-driven tasks (because they're quite fast and cheap).

My own review is always necessary, and these are the things that used to creep up:

1. Too many code comments
2. Overly nested markup, with arbitrary stylistic choices that sometimes wouldn't match existing design, or would completely ignore mobile devices
3. Additional unrequested functionality that complicates implementation without reason
4. Inability to completely follow an agreed plan, particularly when it comes down to writing tests

I've largely solved these problems by:

1. Creating a set of project-specific skills extracted by examples in the history of the codebase, specifically by pointing the LLM to relevant commit that show what to avoid (deleted in the commit diff) and what to prefer instead (added/changed in the diff).
2. Maintaining 2 documents: the application architecture (which maps areas of the codebase, patterns, names, etc.) and project conventions, which establishes rules to follow when modifying code.

## SQLite

I've been impressed by SQLite as it really packs a lot of punch despite the small size.

1. I'm using the FTS5 extension to provide full-text search. The search index is created separately from the main data tables, kept up to date via triggers, and where needed data is passed through a unicode filter that lets me easily type "Bjorn" to find "Bjørn" (which is not as simple as it seems).
2. I'm storing binary blobs for record covers directly in the database in a dedicated table, so that ALL of the data managed by the application is inside one single file (which now adds up to around 800MB).
3. For data that I pull from external APIs and where I'm allowed to, I usually keep copies of responses associated with each record, stored as JSON blobs in dedicated columns. This is helpful to quickly build additional features that leverages other parts of the data, and saves me from hitting external APIs more than once per record update. JSON support in SQLite is not as advanced as in Postgres, and the API is a little bit different, but there's pretty much everything you need to create/update/query JSON data.
4. Backups are implemented via [Litestream](https://litestream.io/) running in a container sidecar, pointed at an S3 bucket on Hetzner. Data replicates every few minutes, and restoring it would imply downloading the resulting file and putting it where needed.

## The hard parts

- Delightful user interactions are hard to come up with: it's often the case of something being obvious in hindsight, with enough time spent using the app to slowly mature the conviction that something could work/look better. For example, I added exploding confetti for when I purchase a record that I had in my wishlist - a small celebration which I absolutely love.
- Architecturing domains and contexts inside the application is difficult: I have periods where I think I should increase the amount of namespaces and boundaries, and periods where I think the best design would be to merge large areas together. I try to use some sort of objective approach (e.g. dependency graph, data isolation) but it still _feels_ a process based on intuition, and prone to yak-shaving.
- Working with third party HTTP APIs remains a tedious job, no matter how well documented, easy to use they are. One needs to mock responses, save fixtures, handle the ceremony needed to be able to maintain fast and reliable tests. Some libraries make this easier (thank you Req) but there's so much you can do.
