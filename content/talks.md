---
title: "Talks"
date: "2020-11-03"
summary: |
  This is a collection of conference talks I've given over time in reverse chronological order, along with their original abstracts.
keywords:
  - talks
  - videos
  - elixir
  - erlang
  - genstage
  - kitchen
  - elixirconf
  - codebeam
---

# Talks

This is a collection of conference talks I've given over time in reverse chronological order, along with their original abstracts.

## Taming side effects (Code BEAM STO 2019)

The systems we build every day have side effects: save data, send emails, write logs, push metrics, you name it! Many errors and headaches come from the difficulties involved in working and testing such code.

In this talk we'll look at a simple approach on how to write logic that is side-effects rich to make easier to maintain and test, no matter if we are interacting with databases, external apis or any other type of service.

{{< youtube Pu8v2o9L5EI >}}

## Flexible Elixir (Code BEAM SF 2017)

Writing idiomatic, efficient, and maintainable Elixir code requires approaching problems in a different way than other languages.

In this talk, we’ll be guided by examples and learn how to construct everyday tasks, combining the expressiveness of Elixir with the strong foundations of Erlang and the BEAM vm.

{{< youtube D3IftRUQgqc >}}

## Always available (ElixirConf EU 2017)

Availability should be one of our major concerns when writing web applications, yet more often than not we ship code that is simply not resilient enough.

Elixir and OTP provide powerful tools to improve resilience and increase availability: in this talk we'll look at how we can leverage them to provide continuous service even when our database is down. The talk will be a walkthrough of a refactor of an Elixir application with a Phoenix frontend, powered by PostgreSQL.

{{< youtube UTXYiV7nOpM >}}

## Back on your Feet (ElixirConf US 2017)

When writing resilient Elixir applications one of our major concerns is state: where do we store it, what happens to it when a process crashes, how do we efficiently recreate it.

In this talk, we'll look at an example application and apply different techniques that can be used to protect and recover state, each one with specific implications and tradeoffs.

{{< youtube kWYgrA2YshE >}}

## GenStage by Example (Øredev 2017)

In this talk we’ll look at GenStage, an Elixir library to structure demand-driven data flows.

The talk will be example-driven: we’ll start with a data-pipeline application written without GenStage and put it under heavy load. By doing that, we’ll have a first-hand account of the issues that GenStage aims to solve.

We’ll then look a revised implementation of the same data pipeline that uses GenStage to see how it solves the issues found in the original version of our application.

{{< youtube Y_JiVW7npuQ >}}

## Practical Elixir Flow (Øredev 2017)

In this session we’ll see how to implement a data transformation pipeline with Elixir GenStage and Flow.

We will process a infinite stream of data, performing aggregations over specific metrics.

As we dive deeper, we'll also look at how to recover and manage failures and errors.

{{< youtube F2fefDJfdRY >}}

## Idiomatic Elixir (Bristech 2016)

Elixir is growing in popularity day by day. Many developers are approaching it with prior patterns and knowledge, building software that works but that doesn’t leverage many features of Erlang and its BEAM runtime. In this talk we’ll go through some examples and learn how to write great Elixir code.

{{< youtube a-8u6g8Wbf8 >}}

## GenStage in the Kitchen (Elixir.LDN 2016)

GenStage is the Elixir core team’s effort to provide a set of flexible, composable primitives for concurrent, demand-driven event processing.

Our use case is a restaurant simulation, with tables placing orders, a waiter, a chef and line cooks ready to prepare amazing dishes. 
We’ll map GenStage’s core concepts to constraints in our restaurant simulation and see how our system copes by stressing its different components, isolating some useful principles along the way.

{{< youtube M78r_PDlw2c >}}

## Rewriting a Ruby application in Elixir (ElixirConf EU 2015)

Dragonfly is a fairly popular Ruby library to manage file uploads and it includes a Rack server to serve those files back. This talk is a postmortem of a rewrite of this server component in Elixir, so that it can be used to process Dragonfly-compatible urls with improved performance. 

The talk will focus on the structure of the application, managing pools of workers, pattern matching to simplify complex Ruby logic, interacting with external tools (like streaming data back and forth from Imagemagick) and deployment considerations.

{{< youtube lho1e04Gzzs >}}
