---
date: 2026-07-14
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - javascript
  - react
  - algorithms
  - interviewing
  - ai
  - code-platoon
---

# You Are Always on Parade

Day 29. Week seven. A mock technical interview in the afternoon, React syntax fighting me after, and an anagram problem that proved I've actually gotten somewhere in the last six weeks.

<!-- more -->

---

## Watching Someone Else Get Interviewed

Francisco ran a coding interview demo today with an actual working software engineer sitting in as the evaluator. We do some version of this demo every day, so watching the problem get solved wasn't the interesting part. The interesting part was getting to ask a real interviewer what he's actually looking for.

Most of what he said comes down to a line I got hammered into me at the Air Force Academy years ago: you are always on parade. It's short, but there's a lot packed into it. You're not just being watched — you're being evaluated constantly, which means you have to carry yourself like it. And a parade isn't something you show up to in your pajamas. It's a state of readiness, dress, and presentation, sustained, not switched on for the big moment.

That's basically been my operating system as an entrepreneur for years. Every sale I've closed, every client relationship I've built, every book I've carried around and talked about — nobody assigned me any of that. I did it on my own authority, and it's on me whether it works. The business isn't hugely profitable, but I've made money independently, with nobody telling me who to call or what to say. I have to remind myself of that on the days I feel behind: those are real royalties, real dollars, earned without anyone standing over me.

A lot of the guys asking questions today were clearly nervous about interviewing in a way I'm not, and I get why. For a lot of them — especially the younger ones straight out of the military — this is close to their first real interview. 

The military doesn't really make you sell yourself; it moves you along on its own logic. Translating years of training into "here's what I can do for you" is a genuinely hard skill to build from zero. I'm not doing anything special by comparison, I've just already been doing that translation for a decade. The technical side of the interview is still a real thing I have to get right. The self-presentation side isn't the part I'm worried about.

---

## React's Syntax Is the Actual Obstacle

The afternoon was React, and honestly it kicked my ass a little, but not for the reason I expected. Conceptually, none of it is hard. Components are the visual building blocks — Lego pieces — and state is the data that lives inside them. Change the state, and React figures out which pieces depend on it and re-renders just those, instead of me manually hunting down a DOM element and mutating it by hand the way I would in vanilla JavaScript. It makes sense the first time you hear it.

What actually slowed me down was syntax, and it slowed me down more than I expected given how much vanilla HTML, CSS, and JavaScript I've already written. I spent a good chunk of the afternoon reading freeCodeCamp lessons on `async` and React in general, to understand what a promise is instead of just pattern-matching my way through `await` keywords. Going slow, doing my usual line-by-line breakdown of what I'm actually typing. Didn't get far. That's fine — this is exactly the kind of exercise where slow and thorough beats fast and hollow.

---

## The Anagram Problem, Twice

The real payoff of the day happened during afternoon Codewars, on a problem I'd already solved before: check if two strings are anagrams of each other. My first pass was a straightforward frequency dictionary:

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        freq = {}
        for char in s:
            freq[char] = freq.get(char, 0) + 1
        for char in t:
            if char not in freq or freq[char] == 0:
                return False
            freq[char] -= 1
        return True
```

That's already O(n) time and space, correct and readable. But I wanted to see how it'd look tightened up, so I pushed on it and landed on `collections.Counter`:

```python
from collections import Counter

class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        return Counter(s) == Counter(t)
```

I got curious what `Counter` was actually doing under the hood, since it felt a little like cheating to just swap in a built-in without knowing why it's faster. Turns out it's not a different algorithm at all — it's a subclass of `dict` that hands the counting loop off to a C function called `_count_elements` instead of running it through the Python interpreter. Same hash map, same O(n) logic I wrote by hand, just executed in compiled C instead of interpreted bytecode. No trick, just less overhead per step.

The version I actually found most interesting, though, was the one that drops the hash map entirely:

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        counts = [0] * 26

        for i in range(len(s)):
            counts[ord(s[i]) - ord('a')] += 1
            counts[ord(t[i]) - ord('a')] -= 1

        for count in counts:
            if count != 0:
                return False

        return True
```

If you know the input is lowercase English letters only, you don't need a dictionary at all — just a fixed 26-slot array, indexed directly off the character's position in the alphabet. Constant space instead of O(n), and array indexing beats hashing every time. Not the general solution, but the fastest one available when the constraints let you cheat like that.

Six weeks ago this same problem would've been the whole session — figuring out how to build the dictionary at all, how `.get()` works, why you'd subtract instead of just checking membership. Today it took a few minutes, and the actual conversation was about which of three correct solutions was most efficient and why. That's a different kind of problem to be solving.

---

## The Puzzle Finally Looks Like a Puzzle

That shift is the thing I keep noticing lately, and today put a sharper point on it. Early on, algorithm problems don't feel like puzzles in the way a good video game puzzle feels like a puzzle. 

In something like Zelda, you're usually equipped for the challenge in front of you — do I need the hookshot, the boots, the sword — and the game gives you clues either way. 

Coding in month one isn't like that. There's a giant search space of syntax you barely know, and the "puzzle" is really five puzzles stacked on top of each other: how do I loop through this? how do I count characters? how do dictionaries even work? how do I compare the results? and what's the actual syntax for any of it. That's not a puzzle, that's an incomplete tool tutorial wearing a puzzle's clothes.

Python and JavaScript aren't second nature yet, but they're visible to me now in a way they weren't a month ago. Today's anagram problem is the proof: I wasn't fighting the tools, I was choosing between them. Dictionary versus `Counter` versus fixed array isn't a syntax question, it's a design question, and design questions are the fun part. That's the actual escalation happening in week seven — the instructors are starting to ask "which of these is more efficient and why" instead of "can you make this work at all," and for the first time that question doesn't scare me, it interests me.

Slow React afternoon, a mock interview that reminded me the self-presentation half of this industry is already mine, and an old problem that came back around just different enough to show me how far the fundamentals have actually come. That's day 29.
