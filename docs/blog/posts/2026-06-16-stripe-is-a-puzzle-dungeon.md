---
date: 2026-06-16
categories:
  - Code Platoon
authors:
  - keith
tags:
  - stripe
  - payments
  - javascript
  - codewars
  - algorithms
  - multi-agent
  - story-spinner
  - code-platoon
---

# Stripe Is a Puzzle Dungeon

The Stripe integration, merely a little untidy with its keys, secrets, modes, and webhooks, occupied the desk like a drawer whose contents might take money or expose it, so I sorted each piece until the first donation cleared. And now I understand exactly why products like Ghost charge a platform cut for pre-built payment integration.

<!-- more -->

---

## Idempotent

New word of the day before I get into the Stripe thing: **idempotent**.

Idempotent describes an action or operation that can be repeated multiple times without changing the result beyond the initial application. Apply it once, apply it ten times — same outcome. In payments, this matters a lot: if a webhook fires twice for the same transaction, you don't want to record two donations. The webhook handler checks the `stripe_payment_intent_id` before inserting. Already exists? Skip it. That's the idempotency protection.

---

## Stripe Is a Puzzle Dungeon

Implementing Stripe requires holding a brutal mental map: publishable key on the client, secret key on the server, webhook secret on the server — and the test vs. live toggle affects all three independently, each living in a different system (frontend env, Supabase secrets, Stripe dashboard). Miss one and you get a cryptic error that doesn't tell you which piece is wrong.

My analogy: navigating a puzzle dungeon in an RPG. Very easy to get lost in the process, forget where you are, forget what you did. Old school video games trained me well for this.

There's even a boss order:
1. Get test keys working locally
2. Swap to live keys
3. Register the webhook → get the signing secret
4. Verify the first real charge

Skip a step and you're back at the last checkpoint.

Ghost, Lemon Squeezy, Gumroad — they charge a platform cut precisely because they map that dungeon for you. Now that I've navigated it myself, I get it. The integration I built is solid: anonymous donations allowed, idempotent webhook, no `sk_` keys anywhere in the repo, RLS on the donations table.

---

## Multi-Agent Architecture for Stripe

The way I structured Claude Code assistance for this build was the most useful approach I've tried so far: strict domain isolation.

**Agent 1 — Backend only.** Supabase database schema, Edge Functions, Row Level Security, and secrets. Zero React awareness.

**Agent 2 — Frontend only.** Routing, forms, Axios calls, Stripe Elements, state management. It consumes the API; it doesn't build or modify Supabase structures.

**Agent 3 — Supervisor.** Reviews both agents' work. Most critical job: scan all frontend files to confirm no `sk_` keys leaked into client-side code or committed environment files.

The crucial rule that surprised me: the UI agent must use `stripe.confirmCardPayment()` from the Stripe.js SDK to confirm the payment — not a second Axios call to the backend. Stripe requires this so card data never touches your server. The backend only creates the PaymentIntent and returns a `client_secret`. Stripe handles the actual charge.

The architecture forces the right separation. If you let one agent touch both domains, it starts blurring responsibilities. Key boundaries produce cleaner code and a more secure integration.

---

## Bugs That Got Me

A few things that cost me time, in case I need to debug this again later:

**Axios interceptor misfiring on Supabase 401s.** The `api` Axios instance intercepts all 401s and tries to refresh the Django JWT. When Supabase returned a 401, the interceptor fired and tried to hit local port 8000 (not running). Fix: added a guard — `const isDjangoRequest = !original.url?.includes('supabase.co')` — so the refresh only runs for Django requests.

**`VITE_API_URL` pointed at localhost.** This bypassed the Vite dev proxy entirely, so token refresh hit local Django instead of Render. Fix: pointed it at `http://localhost:5173` so `/api` requests route through Vite's proxy.

**Anonymous donations returning 401.** The edge function originally required a valid bearer token. Fix: made auth optional — missing or invalid token sets `user_id = "anonymous"` and proceeds. Requiring login to donate creates unnecessary friction.

**Trailing space in Supabase URL on Vercel.** Caused `%20` in the URL. Fix: remove the trailing space. The value must be exact.

**`.env.production` tracked by git.** File was committed before `.gitignore` was updated. Fix: `git rm --cached` and block all `.env.*` files going forward. Once a key is in git history, it's compromised — rotate it.

---

## Live Whiteboard: Go Through the Process

Got selected for the daily afternoon algorithm demo — the public Codewars display. My instinct going in: act like I'm on a livestream. Talk out loud. Show the thought process.

The problem was a math one. No string typecasting necessary. If you know loops and know how to establish variables, that's all it's testing. My decisions were sound. I solved it (mostly) on my own.

The thing that reinforced something I've been thinking about: traditional search kept serving me outdated JavaScript documentation. Mozilla, W3 docs, Stack Overflow — three times I pulled a method I didn't recognize, tried it, and it didn't work. The Stack Overflow threads were old. The answers were deprecated.

This goes against popular convention, but AI is surfacing the more relevant, timely hits. It's finding the resources with the most recent activity and feeding those back. When the search results are telling you different things depending on where you look, that's usually a signal the older sources are wrong, not a signal that the feature doesn't exist.

Two and a half weeks ago my JavaScript was shaky. Now I can do a Code Wars level 7 without a second thought. Not bad.

---

## The Long View

A Q&A session with program alumni today snapped me out of something. Several of them had job offers or internships shortly after finishing. This was surprising, because my informal research — talking to people who went the bootcamp route — suggested it wasn't that easy.

Whether it's survivorship bias or not, the conversation made me look up from the immediate game of the bootcamp and think about what comes after.

My website is five years of coding projects, writing, experiments, and anything I've wanted to experiment with. Branding was never the intent — the intent was always to document what I learned and offer anything complete enough for public consumption. Turns out that's a portfolio whether I meant it to be one or not.

The status game of the bootcamp — who solves the morning whiteboard fastest, who knows the most syntax — isn't the real game. Fundamentals, being curious, asking questions when you don't know, and having strong connections matter more. The job you get might use a completely different tech stack anyway. You'll start from the bottom learning it regardless.

What I'm not wrong about: being genuinely excited to learn how tech works. That part isn't a game. It's real.

I see myself working at the intersection of creativity and code. Narrative game dev seems like the direction everything is pointing, though the shape of it is still fuzzy. Something tells me to keep building toward it. And if an opportunity showed up that pushed toward it, I'd pursue it with urgency.

---

## Where Things Stand

Story Spinner Plus has live payment integration — anonymous donations, idempotent webhook, solid architecture. The DOA Archive project is scoped and approved for next week.

The Stripe dungeon is cleared. On to the next floor.
