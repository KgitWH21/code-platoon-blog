---
date: 2026-06-18
categories:
  - Code Platoon
authors:
  - keith
tags:
  - security
  - sql-injection
  - owasp
  - ai
  - python
  - code-platoon
---

# You Can't Prompt Your Way to Competence

Day 14. The last day of week three. It turned into a security day — CSRF, SQL injection, white-hat/black-hat taxonomy, and two hours wrestling with OWASP Juice Shop. And then a retro that put a name on something I'd been watching build for weeks.

<!-- more -->

---

## The Juice Shop Challenge That Broke Me (And Then Taught Me Everything)

OWASP Juice Shop is a deliberately vulnerable web app you run locally and hack. It's used for practicing real security techniques. The first challenge I tackled: *Order the Christmas special offer of 2014 — a product that's no longer supposed to be available.*

Sounds simple. It was not.

The setup: many apps don't actually delete database records when something gets "removed." Instead they use a **soft delete** — they set a `deletedAt` timestamp and then filter that field out of every query. The product is still in the database. You just need to convince the database to ignore that filter.

The query the app is actually running looks like this:

```sql
SELECT * FROM Products 
WHERE ((name LIKE '%[YOUR_SEARCH]%' OR description LIKE '%[YOUR_SEARCH]%') 
AND deletedAt IS NULL)
```

My first approach: inject `'))--` into the search bar. The idea is to close the string and parentheses, then use `--` to comment out the `AND deletedAt IS NULL` check. I was confident this would work.

It didn't. The UI stayed blank. The Christmas box didn't appear.

I dug in. Turns out injecting `'))--` leaves an empty `''` hanging in the SQL syntax that SQLite rejects as malformed. Even if the syntax had been fine, the query would still filter by whatever string I searched — and the Christmas box might not even contain that string.

The working solution was a completely different architectural approach:

```
a')) union select * from Products;--
```

This doesn't try to patch the existing query. It hijacks it. Here's how it breaks down:

- `a'))` — gives the original query a valid string, closes the string and grouping parentheses
- `UNION` — tells the database to run a second query and stack the results
- `SELECT * FROM Products` — no `WHERE` clause at all; dumps the entire product table
- `;--` — ends the statement, comments out the remaining backend code

By using `UNION SELECT`, you guarantee the database dumps everything, completely bypassing the `deletedAt` logic the developers tried to enforce.

I did not figure this out on my own. I needed an old YouTube video, Google Gemini, the "Pwning OWASP Juice Shop" book, and a lot of clicking around. The first challenge in Juice Shop is not actually easy. But here's what I took from it: **the AI gave me the first approach, and it was wrong.** I had to understand *why* it was wrong — the actual mechanics of how the SQL query was being constructed — before I could understand why the working solution worked. You cannot shortcut that. You have to know what's actually happening.

---

## The Retro

We closed out week three with our weekly feedback session. One of the instructors brought up something that I'd been watching build for weeks and knew was coming.

It's obvious some people are using AI to solve their daily algorithms. You can see it: a tough problem drops, and two minutes later fully-formed solutions start flooding the Slack channel. But when those same people get called to demo on the spot, they freeze. They can't declare a basic variable. They can't organize fundamental code. They'll say they're nervous. But nerves don't explain not knowing how to declare a variable.

I said my piece because I have years of experience wrestling with exactly this question — how much AI is too much, how much is too little, when does it augment you versus when does it hollow you out. I've felt it in every domain I've applied these models to. I know what it looks like when AI starts masking your inability instead of building your capability.

There's a thing that happens with these models that I think about a lot. The AI doesn't just give you the answer. It gives you the *feeling* of understanding. The output is coherent, confident, and often correct. If you don't have enough baseline knowledge to interrogate it, you can absorb that output and genuinely believe you understand something you don't. And then you get put on the spot, and the understanding evaporates, because it was never yours to begin with.

---

## The Squeeze

I didn't want to call anyone out. Honestly, I get it.

By the afternoon algorithm session, we're all pressed for time. We've got five minutes to fill out daily feedback forms. Everyone's been sitting at a desk all day. Everyone wants to log off. Staring at a blank editor, feeling like your brain can't even process what the problem is demanding — that's real, and I feel it too.

Struggling through pseudocode and fumbling with syntax takes 15 to 20 minutes of genuine mental friction. Spawning an answer takes five seconds.

If the goal is just to submit something, the calculus is obvious. But if you're in this to become capable — if the goal is to actually be able to do the thing — then you have to forcefully reject that convenience. You have to decide, prompt by prompt, whether you're augmenting your thinking or replacing it.

---

## How I Actually Use AI for Algorithms

I don't avoid AI. I use it heavily. But I use a specific framework:

**Step 1: Ask for methods, not solutions.** If I'm stuck, I don't ask for the answer. I ask which methods I should consider, and for brief examples of how they work. Then I try to assemble the pieces myself.

**Step 2: Demand the why.** If I'm still stuck after 15 minutes of trying, I might ask for the solution — but my prompt specifically asks: *Don't just give me the answer. Tell me why my original approach failed. What concept am I missing?* That post-mortem is where the learning happens.

**Step 3: Translate for muscle memory.** Once I understand the Python solution, I ask for the JavaScript equivalent. Then I close the AI output and type the JavaScript version myself. No copying. This is straight from my language learning background — sometimes you just have to copy the pattern manually until the structure sticks. This morning I translated a Codewars solution to JavaScript almost entirely from memory. A few weeks ago I couldn't have done that.

The failure mode everyone is hitting is skipping all three of these steps and going straight to: *give me the solution, copy, submit, move on.* High output, zero retention. Illusion of competence.

---

## The Tesla Analogy

A lot of casual AI users think it does the thinking for you. The paradox is that if you want to actually get better, you have to think *more*, not less.

I never use autopilot on my wife's Tesla. I feel like I have to work double-time to supervise it. I can't just drive — I have to hyper-analyze what the car is doing in case it makes a bad decision and I need to take the wheel instantly. That's exhausting in a way that just driving isn't.

Using AI correctly as a learning tool feels exactly the same. The output is not going to read itself. It's not going to implement itself. It can hand you a step-by-step solution and you still have to understand it, question it, implement it, and debug it when it breaks. If you're doing that — if you're fully engaged with what the machine is telling you — you are thinking harder, not less. You're reading more, questioning more, implementing more.

If you're just copying the output and moving on, you're not even doing the easy version. You're doing the version that actively makes you worse.

---

## The Juice Shop and the Retro Were the Same Lesson

Thinking back on the day, the Juice Shop challenge and the retro were teaching the same thing from opposite directions.

The Juice Shop showed me: even with AI walking me through it step by step, I couldn't solve the challenge until I understood the underlying mechanism — why `'))--` fails at the SQL level, why `UNION SELECT` bypasses the filter entirely. The AI gave me a confident wrong answer and I had to go learn enough to know it was wrong.

The retro showed me: people are taking AI's confident answers at face value, submitting them, and calling it done. And when the moment comes to perform, the understanding isn't there.

Same trap. One I walked into during a security challenge. The other I've been watching play out in Slack for three weeks.

---

## Week 3 Wrap

Security day. Retro day. Last day of the phase. Next week: the agentic coding project. The week after: Docker, then deep Python and JavaScript fundamentals — the bootcamp territory everyone keeps warning us about.

People keep saying it's going to get harder. I take that with a grain of salt at this point. Every day is already a level of difficulty. That's not a flex — it's just where I've ended up after years of deliberately putting myself in hard positions and working through them. The pressure going up doesn't scare me. It clarifies things.

The fundamentals are still the bottleneck. Syntax. Algorithms. The ability to produce working code without reaching for the oracle first. That's where the work is. More reps.
