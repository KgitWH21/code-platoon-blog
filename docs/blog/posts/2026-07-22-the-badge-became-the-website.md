---
date: 2026-07-22
categories:
  - Code Platoon
authors:
  - keith
tags:
  - ai
  - ghost
  - web-development
  - cypress
  - software-engineering
  - code-platoon
---

# The Badge Became the Website

It began under the impatient light of my monitor with one చిన్న badge I meant to add to my website, then somehow widened into a complete custom Ghost theme before the day slipped away entirely.

<!-- more -->

---

## It Was Supposed to Be One Badge

I had a meeting with Spoken Audio at lunch, so this morning I decided I should finally display my Spoken Audio Producer badge on my website.

That should have been a small job.

My site was using the Moshi theme for Ghost, which I have liked for about a year. It looks good, but it has a very particular system underneath it. Different sections have to be built as separate pages. Those pages need the correct tags. Certain hashtags turn links into blocks, but only in particular places, and not every kind of block works everywhere. (Confused yet?)

The whole thing is manageable when I am only publishing an article, adding a picture, or attaching some audio. The moment I try to change the structure, I have to remember the theme's private grammar.

I spent about an hour trying to wrangle one badge into the site and reached the point I always reach with website problems: this is taking much longer than it has any right to take.

My website has always been difficult to fit into a stock template because it isn't one clean category of thing. It is part portfolio and part business website. It has books and audio work, but it also has interactive tools and things people can actually do. It isn't just a landing page, a reader magnet, an online store, or a blog. It is kind of all of those things at once.

A general-purpose theme has to make assumptions. The more specific the theme's visual identity is, the more likely those assumptions are to become constraints. Moshi's system worked as long as I was willing to make my website behave like Moshi expected. Today I finally stopped trying.

I made the theme myself.

## A Custom Theme Is a Set of Decisions

That sentence makes the project sound more dramatic than the actual process felt. I wasn't sitting there typing every line from memory. I used ChatGPT to generate and revise the code, redesign pages, and help turn the site's existing content into a new structure.

But the code was only one layer of the work.

The real problem was deciding what the website is. Which parts deserve to be prominent? What should someone understand about me from the home page? How do books, audio production, software projects, and writing belong together without the site feeling like four unrelated portfolios sharing a domain? Which content should be reusable, and which pages need their own layouts? What can Ghost manage cleanly without bringing back the same collection of theme-specific tricks I was trying to escape?

Those are design and systems questions. Producing Handlebars templates, CSS, and configuration matters, but none of that can answer them on its own.

AI was especially useful because it had context beyond the immediate code. It already knew enough about my work to draft a decent bio and organize the material in a way that sounded more or less like me. That compressed several different jobs—developer, designer, copywriter, and editor—into one working conversation. I could react to a complete proposal, explain what felt wrong, and keep steering.

The important word there is *steering*.

If I ask an AI for a website and accept whatever appears, I may get a pile of code that runs. That doesn't mean I have a useful website. I still have to recognize whether the hierarchy makes sense, whether the copy is true, whether the implementation fits Ghost, whether the mobile layout works, and whether the finished thing actually serves the business. The faster code arrives, the more important judgment becomes because there is suddenly much more output to evaluate.

By the end of the day, the site had gone through a total redesign. More importantly, it now had a theme tailor-made for what I need instead of a generic theme I had to keep negotiating with.

I am really proud of how it turned out.

## Writing Code Is Becoming the Smaller Part

The timing was funny because today's guest talk was largely about the same shift I was experiencing on my own screen.

The argument was that writing code is no longer the whole definition of software engineering. Syntax used to be a much larger barrier. If I didn't know how to express something, I might search Stack Overflow, find several partial examples, and stitch them into a solution that almost matched my problem. A large amount of development time went into locating the right incantation and translating it by hand.

AI is very good at that layer. It can generate the syntax, traverse an unfamiliar codebase, propose a structure, and revise the implementation without getting tired. That doesn't eliminate engineering. It moves more of the value toward understanding the problem, defining constraints, examining the result, and deciding what should happen next.

The distinction between vibe coding and agentic coding came up, but the answer was less dismissive of vibe coding than I expected. A project can still be valuable even if AI is doing most of the typing. Building something real forces me to see code, connect pieces, run the result, identify failures, and make decisions. My website redesign was exactly that kind of project.

I didn't begin the day with a formal specification for a custom Ghost theme. I began with friction. I tried to add a badge, discovered that the existing system made a small change unreasonably difficult, and gradually redefined the problem. The badge was not really the problem. The problem was that my website had outgrown the assumptions built into its theme.

That is the kind of thing an engineer has to notice. An AI can respond brilliantly to the wrong problem all day.

## Using AI Without Handing Over the Thinking

All signs point in the same direction: anyone going into tech now needs a strong understanding of how to steer AI. I see it from working engineers, in the tools themselves, and in the way I already build my own projects.

The best way to learn how to steer AI is to use it, but use it in a way where I am still learning. That means I cannot simply take its word for anything. I need enough understanding to test the result, question its choices, and notice when it has confidently solved a nearby problem instead of mine.

There are a few layers to that process:

1. Explain the actual goal, not only the next piece of syntax.
2. Give the AI the context and constraints that shape a correct solution.
3. Break the work into changes that can be inspected and tested.
4. Read what changed and understand how the pieces connect.
5. Run the software and treat the behavior as evidence.
6. Keep ownership of the final decision.

This is where programming knowledge still matters. If I understand templates, routes, styling, state, and testing, I can give better instructions and evaluate the response at a much higher level. I do not need to remember every method signature to know that a proposed design is brittle. I do need a mental model of the system.

AI makes it possible to rise above some of the syntax, but it does not make ignorance safe.

## Reading Still Gives Me a Head Start

I was checked out from the Code Platoon material for part of today, but I didn't blow it off. I had already done the reading, completed the tests, and learned the basics of Cypress. I still participated and finished the work.

Reading gives me an advantage here. I can absorb a technical concept from written material, form a decent model, and then go implement it. I don't have to wait for a replay or find another video tutorial before I can begin. That saves a lot of time, especially in a program where every hour is already spoken for.

It also fits the way I want to use AI. Reading documentation gives me the underlying vocabulary and structure. Implementation gives me evidence. AI helps me move between the two faster. None of those has to replace the others.

Cypress is a good example. I can read that Cypress drives the application through the browser and makes assertions about what a user should see or do. That gives me the model. When I run the tests, watch one fail, inspect what the page rendered, and change the application, the model becomes concrete. AI can help explain an error or draft a test, but the failing behavior is still the final authority.

The same was true of the website. ChatGPT could write a theme, but the browser told me whether the theme worked. I could look at the page and decide whether it felt like my site. The shortened feedback loop did not remove the learning. It let me spend more time on the parts that required judgment.

## The Work Was the Work

Today wasn't a normal Code Platoon day. Most of my attention went toward a personal website redesign that began as a minor task and consumed the whole day.

But it was still code. More than that, it was a real software project with an existing system, accumulated constraints, an unclear initial problem, design decisions, generated code, testing, revision, and a finished result that I will actually use.

That feels closer to the future of software engineering than another hour spent proving I can recall syntax without help. The code still matters. Understanding it still matters. But writing every character myself is not the only meaningful measure of whether I engineered the thing.

Week eight, day thirty-five, done. Tomorrow is the last day of the week because we are unexpectedly off on Friday. Today I tried to add a badge and accidentally replaced my entire website. Honestly, that seems like a pretty good use of a day.
