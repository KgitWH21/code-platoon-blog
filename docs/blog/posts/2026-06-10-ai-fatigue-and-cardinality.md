---
date: 2026-06-10
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - codewars
  - postgresql
  - databases
  - ai
  - code-platoon
---

# The No-AI Day, a Codewars Problem, and Learning What a Database Actually Looks Like

Something weird happened after I spent five hours building Story Spinner Plus with Claude Code. I woke up the next morning with zero desire to open any chatbot.

<!-- more -->

---

## AI Fatigue is Real

I've been using AI in my creative process for years — measured, deliberate, back-and-forth. I use it for a scene, then I go back and heavily rewrite. There's always been a human in the loop.

Yesterday was different. It was basically all AI, all day. Planning the app, building both ends of the stack, getting it deployed. And by the end of the night I had a headache and felt physically drained. I didn't feel good. I've never felt that way before after working with AI.

So today I ran an experiment: no paid AI accounts. Not Claude, not ChatGPT. I wanted to see what working felt like without it.

What actually happened was interesting. When I hit errors on my Codewars problem, my instinct was still to Google — but Google now surfaces Gemini AI instantly. I found myself on my free Gemini account, not saving chats, basically using AI like a "normie." And when I asked for a summary after finishing the problem, Gemini just stopped. *"I can't do that."* Twice. Turns out on a free plan you're getting a shadow of what it's capable of. The gap between free and paid is not a footnote — it's the whole product.

The experiment clarified something: the issue wasn't AI itself. It was the dose. Technology always promises more — more time, more productivity, more output. I'm finding that the lesser done to a higher degree is often the greater prize.

---

## Codewars: Remove BMW

The problem: write a function `remove_bmw(string)` that strips every occurrence of `b`, `m`, and `w` (upper and lower) from a string. If the input isn't a string, raise an error — *"This program only works for text."*

Simple enough on the surface. Here's what it actually taught me:

**Guard clauses first.** Validate the input type at the top of the function before doing anything else. And use `raise`, not `print()` — you want to stop execution, not just log a complaint.

**`isinstance()` over `type()`**. The industry standard for type checking in Python is `isinstance(variable, type)`. It handles subclasses correctly; `type(variable) is type` doesn't.

**Strings are immutable.** You can't modify a string in place. There's no `.remove()` for strings. You have to filter the characters and build a new string from scratch.

**Use a Set for filtering, not a List.** Checking membership in a Set is $O(1)$ — constant time, because of hashing. Checking a List is $O(n)$. For six characters it makes no difference, but the habit matters.

**`"".join()` over loop concatenation.** Doing `text += char` inside a loop allocates new memory on every iteration. `"".join(char for char in string if char not in bmw_letters)` builds the result in one pass.

The final function:

```python
def remove_bmw(string):
    if not isinstance(string, str):
        raise TypeError("This program only works for text.")

    bmw_letters = {'b', 'm', 'w', 'B', 'M', 'W'}

    return "".join(char for char in string if char not in bmw_letters)
```

Clean, fast, and every decision in it has a reason.

---

## Relational Databases and the Map I Didn't Have

The morning lesson on relational databases was the first genuinely new material I've hit in the program. **Cardinality** — the relationship between tables — is how you describe whether one record connects to one other record, one to many, or many to many. One user can have many projects. One project can have many saved spins. That structure is the architecture.

I participated more than usual, asking questions to clarify things as they came up. I understood why.

When I built Story Spinner Plus's database yesterday, I had no idea how to visualize the data I was creating. It might as well have drifted off into the morning sky — somewhere out there, maybe I'd see it again, probably not. Today I found **DrawSQL**. A tool that lets you map your tables visually, see every column, every foreign key, every relationship.

It felt like getting a map in a Zelda dungeon I'd been stumbling through blind.

Now I can see exactly where the data is, how it's related, and how to access it. More of the development fog will clear. The schema I designed is right there: `accounts_user` → `projects_project` → `spins_savedspin`, with nullable foreign keys that allow guest spins without an account and saves without a project bucket.

I built the database before I understood databases. Turns out that's one way to make the lesson land.

---

[Story Spinner Plus](https://story-spinner-plus.vercel.app/) is functional. First full-stack app, deployed. Tomorrow: get deeper into the Django ORM and write the queries that make it actually work.
