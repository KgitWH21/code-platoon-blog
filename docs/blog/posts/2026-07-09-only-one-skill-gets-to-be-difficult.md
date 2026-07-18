---
date: 2026-07-09
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - oop
  - inheritance
  - polymorphism
  - code-platoon
---

# Only One Skill Gets to Be Difficult

Open the cages; cages become classes, classes become creatures, and by the time the crowd understands that one missing `super()` has loosed a nameless bird, wings are beating through the warm sawdust stink.

<!-- more -->

---

## Building a Zoo, and Where `super()` Actually Lives

The exercise was a classic inheritance tree — `Animal` at the root, `Mammal` and `Bird` and `Reptile` branching off it, then `Primate` and `Marsupial` branching off `Mammal`. Straightforward on the page. The thing that actually taught me something was a question I almost didn't ask: why does `Bird` call `super().__init__()` and `Mammal` doesn't?

The answer is the whole rule in one sentence: if a child class writes its own `__init__`, it completely overwrites the parent's. `Mammal` doesn't need its own `__init__` because it isn't adding any new attributes — it just adds a `give_birth()` method on top of what `Animal` already sets up, so Python quietly walks up the chain and uses `Animal`'s constructor without being asked. `Bird`, on the other hand, needs a `wingspan`, which means it has to define its own `__init__` to accept that argument. But the second it does that, `Animal`'s setup process is gone unless `Bird` explicitly calls it back in with `super().__init__(name, species)`. Skip that line and you get a bird with a wingspan and no name — and the first time you ask it to `speak()`, it blows up with an `AttributeError` because `name` was never set.

I like this rule because it's not a style preference, it's a real trap with a real failure mode. You don't feel the cost of skipping `super()` until three lines later when something that should obviously work doesn't.

Polymorphism took longer to actually land, even though I'd had the syntax down for a while — every subclass overriding `speak()` to return its own string. What finally made it click wasn't more code, it was a story. One command, many forms: tell a Marine to attack and it fires a rifle, tell a parasite to attack and it releases a toxin, tell a monster to attack and it swipes. The calling code never has to know which one it's talking to. It just has to trust that whatever object it's holding knows how to answer. That's the whole point — you stop writing code that interrogates an object's identity before acting, and start writing code that just issues the command and lets the object decide how to honor it. Once I had a scene to hang it on instead of an abstract definition, it stopped being trivia.

---

## Boggle, and the Matrix Trap

The second exercise was building a Boggle board — a 4x4 grid, sixteen actual dice with six letters each, shaken and placed randomly, with 'Q' representing 'Qu' and displayed that way without breaking the grid's alignment.

The part worth writing down is a mistake I didn't make, but easily could have: building the board with `[["_"] * 4] * 4` instead of a nested list comprehension. They look identical when you print them. They are not identical in memory. The multiplication version doesn't create four independent rows — it creates one row and three pointers to that same row. Change one cell in row zero and all four rows change, because they were never four rows to begin with. The nested comprehension, `[["_" for _ in range(4)] for _ in range(4)]`, forces Python to build each row from scratch, four separate times. It's the kind of bug that would've looked completely fine until the exact moment I tried to modify a single cell — the worst kind, because everything works until it very specifically doesn't.

The other piece was cosmetic but taught me something about intent: `f"{cell:<2}"` to left-align every cell to a fixed width of two characters, because 'Qu' is two characters wide and everything else is one. Without it, a single 'Qu' shoves every letter after it in that row out of alignment. Small detail, but it's the difference between a grid that looks like a grid and one that looks broken.

---

## Roster Photos and OSI Training

Accenture came in today to talk resumes, paired up with coaches to help translate what we're building here into something that reads on a job application — geared heavily toward people transitioning out of the military, which is close to half this class. It broke the day up, and it also handed me a comparison I hadn't made yet: this feels like OSI training.

I remember getting to Okinawa, my first duty station as an agent, and being told to put down that I had experience handling evidence, experience with interrogation — because I did. Not mastery. Exposure. You touch officer response tactics and shooting and driving and interviewing so fast in training that you never feel like an expert in any one of them. You just feel like you've touched them. That's exactly where six weeks of Code Platoon has put me. Am I a wizard at Python or Docker or class design? No. But do I know what they are and how to use them? Yes. And especially now, with AI doing more of the raw keystroke work, that distinction — knowing what to ask for and why — matters more than syntax mastery ever did.

The afternoon was pair programming with two younger guys, working the exercises together instead of solo. Not my natural mode — I move faster alone — but it was a good break in rhythm, and we stayed on task the whole time. I could also feel myself running low by the end of the day. Not falling apart, just flagging — my usual level of focus wasn't sustained the way it normally is. Which is fine. It's what six weeks of full days on top of my own writing and business work looks like eventually.

---

## Only One Skill Gets to Be Difficult

Here's the part that actually matters more than the code. Finishing an audiobook project this morning, I realized the client still needed a cover. Old me — decade-of-nobody's-coming me — would've just done it myself, because I've made enough graphics for my own books and videos that it's a two-minute job with AI now. And I did. Instead of emailing the client and losing a week to back-and-forth, the job was just done.

That's the upside of having spent ten years teaching myself everything because there was no money to pay for help and nobody coming to teach me. It's real leverage. But it's also the exact same instinct that makes an eight-hour day of nothing but code start to feel tedious by hour six — because I'm used to jumping between writing and design and code and audio, and sitting in one lane that long fights against a decade of habit.

Code Platoon is showing me something I didn't fully believe before: focus compounds. The growth this month is happening specifically because the environment removed the option to context-switch. That's not an argument for abandoning everything else — writing is still the primary craft, and it's not going anywhere. It's an argument for a rule I want to carry past this bootcamp: only one skill gets to be difficult at a time. A primary craft, a secondary skill actually being pushed hard for a season, and paid work that sits adjacent to both. Everything else goes to maintenance, not zero. Not scattered evenly across five things that each get a little attention and none of them get finished.

I've spent a decade building the range. What Code Platoon is teaching me now is when to stop widening it and just go down.
