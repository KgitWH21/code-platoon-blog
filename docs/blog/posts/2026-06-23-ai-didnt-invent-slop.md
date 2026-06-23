---
date: 2026-06-23
categories:
  - Code Platoon
authors:
  - keith
tags:
  - react
  - supabase
  - spa
  - ai
  - code-platoon
  - writing
  - fiction
---

# AI Didn't Invent Slop

Day 16. Week 4. The DOA Archive is live.

But before I get into the build, I was editing an audiobook today — client work, the usual — and I kept thinking about something. The book was published in 2015. Crime noir. Depressed divorced ex-cop. Dead best friend. Beautiful widow with history. Flash drives full of secrets. And I'm sitting there going: *this is slop.* Not AI-generated slop. Human slop. Pre-AI slop. The kind that gets four stars on Amazon anyway.

<!-- more -->

---

## Consuming Without Processing

The book has all the right ingredients. Wounded protagonist, dangerous woman, dead friend with a secret criminal life, burner phones, hotel hideout. Those are fine thriller pieces. But the execution keeps defaulting to genre shorthand instead of discovery. The prose tells you what emotional category a moment belongs to instead of making the moment feel newly observed. "Flash drives mean big secrets" — that's not a sentence. That's a genre memo.

What gets me is the pacing. The central mystery — the hook, the thing on the back cover — doesn't fully arrive until chapter 13 or 14. We're over a third of the way through a thriller before the thriller starts. That's not a structural quirk. That's the author writing his personal story first and then trying to remember it's supposed to be a crime novel.

You can see the inputs. This guy has watched a lot of cop shows. His protagonist does the whole cup-your-hand-over-the-phone-while-being-tailed routine like that's a real thing. It isn't. That's how you know the experience came from the screen, not from the work.

And that's the insight: people who consume a lot of the same type of media and then try to reproduce it end up making a perfect copy of the genre gestalt. Not the real thing — the composite impression of the real thing. The characters don't clash with each other in interesting ways. They fulfill their roles. The cop is damaged. The widow is beautiful. The dead friend had secrets. Everybody knows what scene they're in.

I'm reading Brave New World at the same time. Huxley has a signature — a tonal and intellectual fingerprint that is distinctly his. He's making decisions about vocabulary, register, satirical exaggeration. He's not reproducing the default texture of dystopian fiction; he's generating something. That's the difference between influence and unconscious imitation.

The thesis that keeps forming in my head while I'm listening:

**AI didn't create cliché. It industrialized it. The threat isn't that machines can now produce bad books. The threat is that a lot of books were already built from reusable genre presets — and now the machinery can produce those presets faster than ever.**

The pre-AI version still required someone to sit down, make choices, and push through the labor. Good on him for finishing the book. Finishing a novel is hard. I know what it costs. But completion and artistic vitality are separate questions.

What this made me think about more than anything is the processing gap. Most people consume a lot, but they don't process what they consume. They may feel it. They may remember it. But when they try to write from it, they produce the surface — the vibes and the genre furniture — without the specificity underneath. You get 100 repetitions of the slogans without understanding where the slogans came from.

This is why I force myself to write at least one thought after every chapter I read. Not a book report. Just: what was I actually thinking? What surprised me? What rubbed me wrong? The reaction is where the processing lives. Skip it and you're just refilling the same tank with the same fuel.

---

## The DOA Archive

Meanwhile, I shipped the app.

The DOA Archive is a live, production military sci-fi lore wiki I built for my novel — a classified intelligence database for the Department of Otherworldly Affairs, a fictional military agency I've been developing. It's a React + Vite SPA connected to a Supabase backend with:

- **Auth** — register, login, email confirmation, session persistence
- **Gated content** — classified intel locked behind member access, enforced at the database layer with Row Level Security, not just hidden in the UI
- **Stripe** — webhook-verified, idempotency-checked, payments logged to the DB
- **Booker** — an in-world AI liaison powered by OpenAI embeddings and pgvector semantic search, with a keyword fallback if embeddings aren't ready yet
- **Admin panel** — auto-generates embeddings on entry save, admin-only nav, full CRUD

When I pull it up on my phone, it doesn't look like a vibe-coded weekend project. It looks branded. It feels like an app you'd download. That's not an accident — it comes from having shipped enough things to know what "done" actually looks like.

The whole build probably took ten hours across two days. Stripe was the biggest fight, same as the first time. It's like moving into a house and then discovering that to actually live in it, you have to move out and move into the real house. All new keys, all new setup, everything different from test mode to production. I spent a few hours just on that.

Vercel also gave me trouble on deployment — wouldn't ingest the repo cleanly, I had to delete the project and start over, another 30-45 minutes gone. Little stuff like that stacks up at the end when you can see the finish line.

---

## The Finish-Line Problem

There's a thing that happens to me in the last 10 to 15 percent of a project. I can feel the end coming. And I shift modes — I start pushing harder, I get impatient, and when I hit obstacles at that point I feel them more acutely than I would in the middle.

It's not unique to code. It happens when I'm playing games. It happens when I'm working out. I can do slow and steady for a long time, but once the finish line is visible, I'm not running the same race anymore. And problems that are objectively small — a Stripe webhook misconfiguration, a Vercel deployment not seeing the repo, a browser test mode discrepancy — feel bigger than they are because they're standing between me and being done.

But I know this about myself. Knowing it is most of the defense.

---

## When Claude Went Down

Claude was down for the first hour or so of the day. We're in an agentic coding week. That's a work stoppage for anyone who's been treating the AI as their codebase.

I pivoted. Used Codex. Kept going.

That's the soft skill underneath all the technical stuff. The AI is a tool. When one tool is unavailable, you pick up another one. The ability to do that — to not freeze, to not wait, to adapt mid-process — matters as much as knowing the right syntax. Maybe more, because the syntax can be looked up. The presence of mind to keep moving is something you either have or you're building.

---

## Product Instinct

Here's what I keep coming back to. I'm here to learn how to make products, not just to learn how to code. Coding is happening as a byproduct — the syntax is getting more familiar, the architectural patterns are starting to repeat, I'm getting faster. But that's not the main event.

The main event is shipping. Starting from a planning document, executing against it, troubleshooting when things break, deploying something functional at the end of two days. That's the exercise.

And I've shipped enough things — novels, podcasts, videos, client projects nobody told me how to structure — that the shape of a project like this doesn't intimidate me. I can hold the whole thing in my head while I'm down in the weeds of a single phase. I know the database wiring phase is a quagmire but it ends. I know that "basically done" at lunch means two and a half more hours of small fires. I know that the frustration is proportional to proximity to the goal, not to the actual severity of the problem.

That knowledge is the advantage. The technical stuff matters. But what gets you to the finish line is everything around it.

More reps tomorrow.
