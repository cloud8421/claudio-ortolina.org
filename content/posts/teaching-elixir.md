---
title: "Teaching Elixir"
date: 2021-07-06T08:34:25+03:00
description: "Lessons learnt while onboarding Elixir Developers at PSPDFKit."
tags:
  - elixir
  - people
  - software development
summary: |
  Lessons learnt while onboarding Elixir Developers at PSPDFKit.
---

When I joined [PSPDFKit](https://pspdfkit.com) in 2018, I inherited the development and maintenance of [PSPDFKit Server](https://pspdfkit.com/guides/server/pspdfkit-server/overview/), the server-side component of [PSPDFKit for Web](https://pspdfkit.com/pdf-sdk/web/).

Initially, I was the only person working on the project full-time. The team eventually expanded to a total of three people and I moved on to manage the Web Team.

In addition, other developers occasionally contributed features with differing degrees of complexity (ranging from small, incremental changes to large features over a few weeks of work).

Looking at the entire process, I can safely say that onboarding has been smooth and fast, especially for internal hires from different teams.

There are, of course, lessons learnt from this process, which is exactly what this blog post is about.

> This post was sponsored by digital product consultancy [DockYard](dockyard.com) to support the Elixir community and to encourage its members to share their stories.

## The organization

PSPDFKit is a distributed company where everyone works remotely. The PSPDFKit Server team includes people with overlapping time zones and no fixed working hours.

The core business for the company is the sale of SDKs used to manipulate documents (predominantly in PDF format).

SDKs support a variety of platforms (iOS, Android, Windows, and web) and are embedded in client applications written and maintained by our customers.

## The Project

PSPDFKit Server’s codebase is more than five years old and has grown organically over time thanks to the work of people with different expertise and backgrounds.

Domain-wise, the application manages the lifetime of a document, allowing our customers to build specific workflows on top of a small set of primitives: documents, content layers, annotations of different types, and permission sets. At a very high level, a user can open a document, perform an arbitrary set of actions (with the option of leveraging real-time collaboration with other users), and finish their session with changes automatically and efficiently stored.

Due to the way the product is sold and operated, it has a few key properties that make it different from most projects I’ve ever worked on:

1. It’s both CPU and memory intensive due to the amount of operations performed on PDF files. The component that interacts with PDF files is run as a binary daemon and maintained by a different team.
2. The application has optional components that are activated depending on specific configuration options. For example, one can enable an entire caching layer based on Redis.
3. Features can be turned on and off remotely via our licensing infrastructure.
4. The release is shipped as a Docker image to be operated on premise by the customer. This property has two implications. First, it means that we don't have direct access to performance monitoring or visibility over runtime issues. Second, it drives us to minimise the operational complexity of the end product; for example, we don't leverage the native Erlang/Elixir distribution because that would complicate deployment for our customers.

## Onboarding results

In the last two years, we managed to onboard a good number of people:

- 1 external hire with extensive Elixir experience, now part of the team full time
- 1 internal hire with no Elixir experience, now part of the team full time
- 2 internal hires with no Elixir experience, in the process of joining the team full time
- 1 person contributing a significant piece of work over a few weeks
- Occasional contributions by other people

It’s great that we can facilitate different degrees of contribution, with a ramp-up time measure between a couple of days and a week.

The people involved have completely different backgrounds: heavy frontend development, systems programming, and mobile development. Furthermore, apart from one person, everyone had very little functional programming experience.

## Training structure

### Learning styles

An important aspect to consider is each person’s learning style. Some people prefer pair-programming, while others would rather go through tutorials in their own time and ask questions when needed.

To accommodate such differences, we combine learning materials with targeted pairing sessions as well as follow-up calls to clarify where needed. We also keep the schedule pretty flexible, meaning that we don't have a fixed curriculum people go through in a set period of time.

### Tooling

The company uses a diverse set of languages and associated development tools.  This ranges from text editors to full-fledged IDEs, as well as running on MacOS, Windows, and Linux.

Apart from work on platforms where there’s strong incentive to standardize on a specific tool (e.g. XCode for iOS development), people pick their tools based on personal preference.

A quick informal survey of the Server Team reveals that people predominantly use MacOS and favor Vim, Emacs, VSCode, and JetBrains IntelliJ.

We enforce code formatting at the CI level, but encourage people to set up their development environment to format on file save.

For code intelligence, Elixir has a solid implementation of a [Language Server](https://github.com/elixir-lsp/elixir-ls) that provides intelligent autocompletion, inline docs, and jump to definition. Especially for people coming from a Java background, this type of tooling helps provide a consistent experience.

In terms of domain-specific tooling, we glue everything together with shell scripts and Docker-based workflows.

### Productivity ramp-up time

People are usually productive in two to three days, where "being productive" means being able to set up the project, getting a basic understanding of its structure and architecture, taking on a small piece of work, and producing a relevant pull request containing implementation, tests, and documentation.

You may have heard the mantra "Make a commit on your first day." While I agree with the sentiment behind it, I find it puts too much pressure on the contributor.

As part of the initial onboarding, it's normal to find little issues with setup scripts (e.g. things that stop working between major OS versions) or unclear documentation. In most cases, fixes are cheap to implement and offer an opportunity to discuss the application architecture.

### Proficiency

On average, it takes two to three months for an engineer to become a proficient contributor who has worked on most areas of the codebase, including some of the internal parts which are subject to very low churn over time.

### Learning materials

We generally recommend going through the [Elixir Lang guides](https://elixir-lang.org/getting-started/introduction.html): they’re well written, accurate, and split in easy-to-digest chunks.

For all libraries, making package documentation available on [hexdocs](https://hexdocs.pm) tends to be sufficient.

While we do have a few books available in the company digital library, they're rarely consulted.

### Scope

We start with small, incremental features, as they have a very high completion success rate. For internal hires, we try to choose something connected to a use case the person is already familiar with from previous work.

A more experienced team member is available for general guidance, so that the resulting piece of work includes all necessary components: implementation, tests, and documentation.

On a technical level, we try to avoid pieces of work that introduce more than one key concept. For example, if the person is not familiar with immutable data structures, we avoid assigning a feature that would also introduce concurrency patterns.

### Example projects

There are cases where it’s worth starting from smaller, separate projects. For concurrency and distribution, it’s better to rely on smaller applications (e.g. the examples referenced in the Elixir Lang guides) to nail the basics.

Once the basics are clear, we build up by looking at codebase components to address topics like error handling, logging, production hardening, and recovery.

Learning is complemented by specific articles (e.g. an introduction to circuit breakers).

### Providing feedback

When a person starts to submit code for review, we try to provide different layers of feedback:

- At first, we focus only on functionality, so that things work as expected. At this stage, the purpose is to build confidence and get the person comfortable.
- We address code quality and language conventions in follow-up PRs, so that changes can be reviewed separately and it’s clearer why they’re necessary.
- Reviews include links for self-learning, so that the person can explore on their own.
- If a PR review becomes too long, we suggest a pairing session to discuss things directly.
- We try to use proper names for all concepts, but also be conservative about introducing too many ideas in the same feedback session.
- We make a clear distinction between company and community conventions, as they may differ. This way, when the developer reads other code on their own, they can compare and contrast approaches. This also often provides constructive criticism on the way we do things.
- We slow down as needed during pairing sessions focused on learning. We allow ourselves to diverge from the initial session goal if the person wants to drill into a different topic. Maintaining interest and enthusiasm is more important in the early stages than sticking to a predefined plan.
- We recently started recording short videos that document how to approach specific development tasks in order to facilitate independent learning.

### Milestones

Every person that learned Elixir in the company followed a different process, but it’s possible to identify common milestones.

#### Pattern matching and control flow

Understanding pattern-matching comes in the first hour of reading existing code. It takes more time to develop a sensibility around specifics, such as when to use a single function head with an internal case statement versus multiple function heads with specific pattern matching clauses.

#### Immutable data and functions

For people who approach Elixir as their first functional language coming from object-oriented languages, the first adaptation step is to start reasoning in terms of data passed through functions.

Our trainees quickly embraced immutability due perceived benefits in clarity and predictability of the program. For example, one of our engineers commented that at the beginning of his learning journey, he could sprinkle print statements all over the codebase to see values changing and visualize the application flow.

In this phase, developers tended to write very explicit code, with plenty of intermediate bindings and little use of more advanced language features like the `|>` operator or the `with` special form.

#### Configuration

Configuration usually triggers a few questions:

- Should this value be configurable?
- What about changing the value at runtime?
- How does configuration interact with building a Docker image and, later on, the container runtime?

This is usually a good opportunity to introduce compile-time vs. runtime configuration, along with the operational implications of relying on the `Application.*` functions to get and set configuration values at runtime.

Common issues include hitting some light race conditions, namely unexpected test results due to configuration values being changed non-deterministically. It's a great opportunity to reinforce that we should strive to write deterministic code that reads configuration as early as possible and passes relevant values down the line.

#### Concurrency and OTP

The application has a few concurrent components, but their structure is rarely modified and developers can use public APIs without much concern for the internals.

This is a big advantage and it lets us plan when to look at the concurrency model. We normally suggest looking at the examples in the Elixir Lang guides, then scheduling a couple of pairing sessions to look at the most significant components included in our codebase.

Once we're done with the fundamentals, we approach concurrency patterns: pools, worker queues, tasks, etc. For such cases, we train only on our application codebase.

#### State management

The application depends on a few stateful components: metadata store, assets store, and different layers of caching (both filesystem and memory).

These components provide the opportunity to introduce ETS tables, process lifetimes and recovery patterns, OTP guarantees around process trees, and error-handling logic.

#### Metaprogramming

We found very little use for meta-programming, so we don't make it an explicit part of the onboarding process. Most of the time, people look into it by themselves when they start exploring the internals of dependencies like Phoenix and Ecto.

### Bumps on the road

What follows is a list of road bumps we encountered over time. Some of them are more tied to our application and the way we ship it, so they don't necessarily represent issues with the language or the platform.

#### Dialyzer

Especially for people with knowledge of programming languages with static types, having Dialyzer as a separate step from the compiler can be frustrating.

Fortunately, the aforementioned Language Server and related projects make it possible to set up the editing environment to provide almost real-time type information with a reasonable level of accuracy.

#### Structuring large components

One common observation is that compared to other languages, Elixir applications are built on very few architectural patterns. This applies to both ours and others I've worked on in the past few years, and is especially noticeable outside concurrency-related areas (which very often implement explicit behaviours).

Most of the time, we simply take care of separating modules that perform side effects (e.g. writing/reading files, http calls, database interaction) from modules which only manipulate data.

Modules are grouped according to their main "topic," which helps exploration.

Having fewer patterns sometimes makes the conversation slightly more difficult, as you need to develop a shared, domain-specific vocabulary to quickly express more complex ideas. On the plus side, the effort of defining names helps onboarding immensely, because it forces everyone in the team to be precise and thorough in their explanations.

#### Error handling and recovery

As we don't operate the software we write, we need to take extra care with error handling. As best we can, we want to provide error messages that are simple(r) to understand for our customers (who are not Elixir developers).

This creates a very mild tension between our style of error handling and the  literature and community examples around applications being run directly by the developers who write them.

For example, it's much better for us to produce a single line error log with a specific name we understand compared to a more verbose stack trace, because it facilitates communication with the customer.

## Conclusion

I often hear the argument, "it's difficult to hire Elixir developers." In my experience, once you have two reasonably knowledgeable engineers, you can stop searching for already-trained developers and expand the search to developers willing to learn the language. With some care around the onboarding process, it's possible to successfully train a new developer every two to three months with a very reasonable impact on the rest of the team.

_Thanks to [Susan Watkins](mailto:swatkinsyl@gmail.com) for editing this article_
