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

I shipped the most complex app I've ever created.

<!-- more -->

---

## Consuming Without Processing

The [DOA Archive](https://doa-archive-wiki.vercel.app/) is a live, production military sci-fi lore wiki I built for my novel — a classified intelligence database for the Department of Otherworldly Affairs, a fictional military agency I've been developing. It's a React + Vite SPA connected to a Supabase backend with:

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

I'm here to learn how to make better products, not just to learn how to code. Coding is happening as a byproduct — the syntax is getting more familiar, the architectural patterns are starting to repeat, I'm getting faster. But that's not the main event.

The main event is shipping. Starting from a planning document, executing against it, troubleshooting when things break, deploying something functional at the end of two days. That's the exercise.

And I've shipped enough things — novels, podcasts, videos, client projects nobody told me how to structure — that the shape of a project like this doesn't intimidate me. I can hold the whole thing in my head while I'm down in the weeds of a single phase. I know the database wiring phase is a quagmire but it ends. I know that "basically done" at lunch means two and a half more hours of small fires. I know that the frustration is proportional to proximity to the goal, not to the actual severity of the problem.

That knowledge is the advantage. The technical stuff matters. But what gets you to the finish line is everything around it.

More reps tomorrow.
