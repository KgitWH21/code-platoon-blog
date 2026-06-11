---
date: 2026-06-11
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - codewars
  - javascript
  - algorithms
  - full-stack
  - story-spinner
  - code-platoon
---

# Hash Sets, Environment Variables, and Deciding to Go All In

Day 9. First day I felt genuinely beat up by this program. But it was also the day I decided to stop being afraid of something I've been putting off for years.

<!-- more -->

---

## The Console Config Analogy

The morning was a deep dive on RESTful APIs and environment variables — what they are, why you need them, why hard-coding secrets into your source code is the kind of mistake you only make once.

The explanation I was given: environment variables are dynamic values that live outside your code, provided by the operating system or hosting platform. They let the same codebase behave differently depending on where it's running — local development pointing at a test database, production pointing at the real one.

I had a question. *So environment variables are like console config settings — they live outside the game so the game stays portable?*

The answer came back: *Exactly. That is a perfect analogy.*

And it clicked. The core game files — physics, dialogue, mechanics — are the same everywhere. But the controller mapping changes per platform. You wouldn't hardcode "press spacebar to jump" into a game that also needs to run on a Switch. You'd expose a `jump_button` variable and let the system fill it in. That's what `.env` files are doing. Your app is the engine. Your local machine is one console, production is another, and as long as the host environment supplies the right config, the game runs.

---

## List Comprehensions: The Syntax That Finally Made Sense

The OSSU Python track hit list comprehensions today, and this is one that earns its place in the language.

The basic shape: `[expression for item in iterable if condition]`. One line where a for-loop would take four. But the real unlock isn't brevity — it's the pattern it teaches.

The Square Every Digit kata on Codewars put it into practice. Given an integer like 9119, return 811181 — square each digit (9²=81, 1²=1, 1²=1, 9²=81) and concatenate. The solution required three moves I had to put together:

1. **Type casting** — integers aren't iterable. You have to convert to string to loop through individual digits.
2. **List comprehension + squaring** — `[int(d)**2 for d in str(num)]`
3. **Map + join** — convert results back to strings and join them before converting the whole thing back to int.

The one-liner version:

```python
def square_digits(num):
    return int("".join(str(int(digit) ** 2) for digit in str(num)))
```

Reading the constraints matters. The problem says return an integer, not a string. That last `int()` wrap is where most people trip.

---

## Sum of Pairs: Where I Got My Ass Kicked

This one took me down. The problem: given a list of integers and a target sum, return the first pair (by the position of its second element) that adds up to the target.

My instinct was nested loops — check every number against every other number, collect all pairs, sort by index. That's brute force: O(N²) time. It also led me into an IndexError trying to look forward in the list with `ints[index+1]`.

The solution is different in a way that required reframing how I think about the problem entirely:

```python
def sum_pairs(ints, s):
    seen = set()
    for num in ints:
        complement = s - num
        if complement in seen:
            return [complement, num]
        seen.add(num)
    return None
```

The insight is the **complement**. If I'm holding a 4 and the target is 10, I don't search for "what can I add to 4?" — I ask "do I have a 6?" That one question has exactly one answer.

The **set** is what makes it fast. Checking if something exists in a list is O(n) — the computer has to look at every item. Checking a set is O(1) — it runs a hash function and looks at one address. Doesn't matter if the set has ten items or ten million.

And by iterating left to right and looking *backward* at the seen set rather than forward, the first match I find is automatically the one with the smallest second-element index. I don't need to collect pairs and sort them. I just return and exit.

After I got the walkthrough, I understood it. Before I got it, I was nowhere close. That gap is the thing I'm grinding on.

---

## Deciding to Monetize

The bigger thing that happened today: I decided to actually go through with monetizing Story Spinner Plus and putting it on the Google Play Store.

I've built a lot of things over the years. Built them, launched them, then let them sit. This is the first time I'm going back in to add a real business model — freemium tier, Stripe billing, $5/month or $25 lifetime for Pro, AI-generated character profiles behind the paywall, TWA packaging for Play Store submission.

It's a week of real work. Not lecture-following work, not tutorial work — actual engineering with billing integrations, webhook validation, tier-gating throughout the UI.

Why now? I wrote it in my notes: I can see the writing on the wall with AI. The release of Claude's Fable 5 reinforces the trend — bigger models for bigger companies, computationally expensive, financially out of reach for the average person at scale. The early AI era, where VC money is subsidizing the compute, is not going to last forever. Code Platoon is one of my last good opportunities to build something ambitious with that subsidized access still in play. So I'm going to use it.

If this doesn't work, it's still the most valuable learning I could do right now.

---

## The Syntax Problem

I'm nine days in and close to 50 Codewars problems logged. I'm still going the wrong direction more often than I'd like. The issue isn't logic — when someone walks me through a solution, I follow it. The issue is that my mind is simultaneously trying to solve the puzzle *and* translate the solution into a language I'm not fully fluent in yet. And I'm doing that in two languages — Python in the morning, JavaScript in the afternoon — which means constant context-switching.

The comparison that makes sense to me: it's like trying to immerse yourself in a novel in your second language before you've drilled enough vocabulary to read without stopping. The story is there. The logic is there. But you're bottlenecked by the words.

Strategy for the weekend and next week: trade off Codewars and freeCodeCamp. Shore up syntax so the implementation stops lagging behind the thinking. Trust that fluency compounds.

Day 10 tomorrow.
