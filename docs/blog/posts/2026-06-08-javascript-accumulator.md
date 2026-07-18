---
date: 2026-06-08
categories:
  - Code Platoon
tags:
  - javascript
  - fcc
  - syntax
  - codewars
  - projects
  - code-platoon
authors:
  - keith
---

# The Accumulator Pattern and Two Small Bugs That Taught Me More Than the Syntax

When the solution I knew was right failed anyway, the error message held two characters hostage and named the adversary hunting my assumptions: JavaScript.

<!-- more -->

The hardest part of switching languages mid-sprint isn't the concepts. It's that you already know how to think about the problem. You know what a loop is. You know what an array is. You know exactly what you're trying to do. You just keep reaching for the syntax you already have and finding nothing there.

That gap is where today lived.

---

I spent the morning on FreeCodeCamp drilling JavaScript fundamentals before the day's builds kicked in. Three questions in a row — for loops, array methods, type conversion — and the pattern that emerged was consistent: I understood the operation, but I kept getting the syntax wrong in small, specific ways that broke the whole thing.

The Codewars problem made this concrete.

The challenge: build a function that returns an array of multiples. `countBy(x, n)` — given a step value `x` and a count `n`, return `[x, 2x, 3x, ..., nx]`. I had the logic right. My comments in the code described exactly what the function should do. What I submitted failed anyway.

The error message said: *expected `['1', '2', '3']` to deeply equal `[1, 2, 3]`*.

Two bugs. First: I was running `String(multiple)` before pushing to the array, which converted numbers to strings. The test expected raw numbers. In JavaScript, `1` and `'1'` are not the same thing, and strict equality checks (`===`) treat them as completely different values. Second: my loop condition was `i < n` instead of `i <= n`, which meant the loop stopped one short. The nth multiple was never included.

Neither bug was conceptual. Both were assumptions I brought in from not reading the language carefully enough.

The fix was two characters: remove the `String()` wrapper, change `<` to `<=`. The function passed clean.

What I took from it is a pattern called the Accumulator. Whenever a problem asks you to build up a collection from a sequence, the structure is always the same four steps: initialize an empty container before the loop, set the loop boundaries correctly, calculate and push inside the loop, return the container after the loop closes. That's it. The bugs almost always live in step two — boundary errors — or come from accidentally transforming the data in step three.

---

The afternoon was builds.

First was the [JavaScript Trivia Bot](https://kgitwh21.github.io/hac_studios_trivia_bot/), an FCC lab where you declare variables, log introduction strings, reassign a `codingFact` variable three times, and output a farewell. Low mechanical complexity, high syntax specificity — the tests check exact string formatting, so the hyphen and trailing period in the farewell line have to be there or the test fails. I named the bot `Kbot2026`, set its location to `USA`, and tied the facts to the thing I'm actually building this summer. All fifteen tests passed.

Then the [Sentence Maker](https://kgitwh21.github.io/sentence_maker/). Two stories assembled from six variables — `adjective`, `noun`, `verb`, `place`, `adjective2`, `noun2` — using the same template, reassigned between stories. I ran this one through the *Cereus & Limnic* universe: first story populated with `irradiated symbiote`, `Kadena Air Base`, `classified MREs`. Second story shifted to `clandestine operative`, `containment zone`, `bioluminescent flora`. Twenty-seven tests. Passed.

Both apps are live on GitHub Pages and embedded on my site. The Sentence Maker has a self-contained teaser card on the homepage — a locked dossier aesthetic styled after the bio-containment incident reports in the manuscript, with redaction bars and a DECLASSIFIED stamp on load.

---

Reflection on Day 1 of JavaScript week:

Python's syntax is forgiving in ways that obscure what's actually happening. JavaScript isn't forgiving, which means it tells you exactly where your thinking is imprecise. The string/number distinction that failed my Codewars submission is something Python would have handled differently and I never would have been forced to think about it directly. JavaScript made me think about it directly.

That's not a complaint. That's what I'm here for.

The syntax is starting to feel less foreign. Template literals — backtick strings with `${variable}` interpolation — are already more comfortable than string concatenation. `let` versus `const` is starting to feel like a real semantic distinction rather than a rule to remember. The accumulator pattern is now something I recognize on sight.

Two projects shipped. Two bugs understood. That's a good first day.

---

Tomorrow starts with a Codewars problem in Python. I'm ready. The logic is already there — it's just a matter of reading the problem carefully, watching the boundaries, and not transforming the data somewhere I shouldn't.
