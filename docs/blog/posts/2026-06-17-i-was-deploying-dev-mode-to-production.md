---
date: 2026-06-17
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - python
  - codewars
  - deployment
  - vite
  - flask
  - render
  - code-platoon
---

# I Was Deploying Dev Mode to Production This Whole Time

Day 13. Week three. Not a bad day — just a scattered one. Boredom as resistance, context-switching fatigue, and the single most embarrassing production deploy realization I've had since I started this bootcamp.

<!-- more -->

---

## The npm run dev Confession

It came out of a simple question I asked while working through the Vegas Heat Check deployment.

`npm run dev` starts a temporary local development server. Un-minified code. Source maps. Hot Module Replacement. Verbose warnings. Nothing written to disk. It is not a production server.

`npm run build` compiles and optimizes your code into actual files — minified, compressed, code-split — and writes them to a `dist` folder. *That* is what you deploy.

I had been running `npm run dev` output straight to production.

The proper workflow:
1. `npm run build` — let the bundler optimize everything locally
2. `npm run preview` (or `npm start`) — spin up a server using those optimized files, click around, make sure nothing broke during compression
3. Deploy the `dist` folder

My users have been loading bloated, unminified dev bundles this whole time. Switching to the build pipeline will likely shrink bundle sizes by 60–80 percent. So, yeah. Now I know.

---

## Vegas Heat Check

Today's side project: **Vegas Heat Check**. You enter how hot it is outside, toggle what you have available (water, sunscreen, shade, AC), hit a button, and it tells you whether it's safe to go outside. Very simple premise.

I based it on a freeCodeCamp Python exercise and plussed it up because those exercises are designed to teach syntax, not to be interesting. I have AI, so I can make it interesting. The core logic is one Python function with four rules:

```
Rule 0 — No temperature entered       → False (can't evaluate)
Rule 1 — Temp ≤ 90°F                  → True only if is_hydrated
Rule 2 — 90°F < Temp ≤ 105°F         → True only if has_sunscreen AND is_hydrated
Rule 3 — Temp > 105°F                 → True if has_shade_access OR has_ac_nearby
```

The app is live on Render. Stack: Python + Flask on the backend, Gunicorn as the production server, HTML + CSS + JavaScript on the frontend, GitHub → Render for deployment.

I used Claude Code's frontend skill for the first time to design it. I knew Claude was strong at design from using it in the browser, so I wasn't surprised — but seeing it applied directly in the workflow was the one genuinely new thing I got today that I wouldn't have gotten otherwise.

---

## The Map Constructor Mistake

Morning Codewars: the Naughty or Nice problem. Loop through a year of JSON data, count how many days were "Naughty" vs "Nice," return whichever is greater.

My solution was 95% right. The logic was sound. The nested loops were correct. The tie-breaker was correct. The one thing that broke it:

```javascript
// Wrong
const tally = new Map(['Nice', 0], ['Naughty', 0]);
```

The `Map` constructor takes **one argument**: an array of arrays. Passing two separate arrays means JavaScript reads only the first one and throws on the second. I was passing them as separate arguments instead of wrapping them in an outer array.

```javascript
// Right
const tally = new Map([ ['Nice', 0], ['Naughty', 0] ]);
```

One bracket. Everything else I had right. This is exactly the kind of syntax trap that doesn't come from a conceptual misunderstanding — it comes from not having enough reps with the constructor. Filed under: things I will not get wrong again.

The Python equivalent, for comparison:

```python
from collections import Counter

def naughty_or_nice(data):
    all_statuses = (status for month in data.values() for status in month.values())
    tally = Counter(all_statuses)
    return "Naughty!" if tally["Naughty"] > tally["Nice"] else "Nice!"
```

Python's `Counter` is built for exactly this — no initialization, no `.get()/.set()` dance, just count and compare.

---

## Boredom Is Usually Resistance

Today felt scattered because I was ahead of the deployment material and my attention kept drifting toward things I actually want to build with — Three.js, narrative tooling, stuff with a direct line to my creative practice.

I caught myself and stopped. Because I've been here before.

When you're getting bored and restless, that's almost always a sign that something is harder than you think and you're procrastinating on it. For me, right now, that's the raw coding fundamentals — writing JavaScript and Python without leaning on AI too quickly. That's the actual bottleneck. Not deployment. Not product thinking. Not design instinct. Just: *can you express this logic yourself?*

The creator brain wants to go to the domain where it already feels powerful. The move is to recognize that pull and keep working on the thing that's actually hard.

---

## The Language Learning Analogy, Adjusted

I've been trying to apply my language learning instincts to code syntax. This morning I wrote a Codewars solution by hand and it helped — when I write stuff down, it saves. The problem is there's no time to handwrite every solution at the volume code requires.

When I learned Chinese, I had four contact points per character: type it, look it up, write the strokes, add it to Anki, journal it. By the time I'd processed one character I'd seen it four different ways. That works for characters because you're dealing with them one at a time.

Code doesn't work like that. There's too much volume and the learning isn't just syntax — it's *when* to use it, how it behaves in context, what method fits which problem shape. `.values()` isn't a word to memorize. It's a pattern to recognize.

What I've settled on: it's just volume. You copy what already works until eventually you're like, *okay, I've seen this before, I know how this is structured*. That's how we learn anything. More reps. The AI is useful here not as an answer machine but as a tutor — it explains the why behind the pattern, which is the part you need to internalize.

---

## AI Already Killed the Self-Help Book

Tim Ferriss posted something on Hacker News this week: he's selling half as many books as he was pre-ChatGPT. The nonfiction self-help category is down, and he doesn't think it's coming back in its traditional form.

That tracks. I've felt this for a while — since I was writing *Tower of Babeling* back in 2019. There's no world where I write that book today and expect it to move. The person who would have bought a how-to language learning book now just opens Claude and has a conversation. Custom-tailored. Specific to their circumstances. No book required.

What's still viable: memoir and narrative nonfiction with a story no one else can tell. *Hayden Brave* is safe because the only way to get what's in it is to read it — there's no AI substitute for someone else's lived experience, told as a story. But the Tim Ferriss style, *change your life, do this, do that* — that category is hollowed out.

The lesson as a smaller creator: don't start with the nonfiction book. Write it after a breakout, not as the vehicle for one.

---

## Day 13 Wrap

Tired. Context-switching between bootcamp work, Codewars, freeCodeCamp, Vegas Heat Check, and client audiobook review during breaks — that last one was not a smart call. Still: I built something, deployed it, learned the dev/build distinction the hard way, and caught the procrastination pattern before it ate the afternoon.

The fundamentals are still the bottleneck. That's fine. More reps. Keep building.
