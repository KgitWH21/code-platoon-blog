---
date: 2026-06-12
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - javascript
  - codewars
  - algorithms
  - authentication
  - story-spinner
  - code-platoon
---

# One Character at a Time

Day 10. End of week two. The fog is starting to clear — which is exactly what you want to hear, right before the weekend grinds begin.

<!-- more -->

---

## The Whole-String Problem

TIY FizzBuzz hit me this morning and I failed it. The concept isn't complicated: given a string, walk through each character and replace it based on whether it's an uppercase consonant ("Iron"), an uppercase vowel ("Iron Yard"), a lowercase vowel ("Yard"), or anything else (leave it alone). Four rules. One function.

Here's what I wrote:

```python
def tiy_fizz_buzz(string):
    char = string
    vowels = "aeiou"
    solution = ""
    if vowels not in string and string.isupper():
        solution = char.replace(string, "Iron")
    elif vowels in string and string.isupper():
        solution = char.replace(string, "Iron Yard")
    ...
    return solution
```

The logic is completely backwards. I was evaluating the **entire string** at once — asking "does this whole string contain vowels?" instead of asking "does this one character contain a vowel?" And then I tried to replace the whole string with a single word, which isn't even the right output.

The fix requires a shift in how you read the problem. You're not editing a document wholesale. You're going character by character, like a manuscript editor marking up a page one word at a time. The algorithm is:

1. Create an empty accumulator string.
2. Loop through every character in the input, one at a time.
3. Check each character against your four rules.
4. Append the replacement (or the original character) to the accumulator.
5. Return the result.

```python
def tiy_fizz_buzz(string):
    result = ""
    vowels = "aeiouAEIOU"

    for char in string:
        if char in vowels and char.isupper():
            result += "Iron Yard"
        elif char in vowels and char.islower():
            result += "Yard"
        elif char.isalpha() and char.isupper():
            result += "Iron"
        else:
            result += char

    return result
```

The concepts that unlock this: **string immutability** (you can't modify a string in place — you build a new one), **iteration** (strings are iterable, loop through them), and **mutually exclusive conditions** (if/elif chains when only one rule can apply at a time).

I've got the thinking. The translation into code is what still trips me up when the pressure is on.

---

## Authentication: Identity vs Permission

The afternoon was a deep dive on authentication — specifically how the concepts stack up: Basic auth, session-based auth, token-based (JWT), and the hybrid that most modern apps use.

The distinction I keep coming back to: **authentication** is answering *who are you?* **Authorization** is answering *what are you allowed to do?* They're sequential, not interchangeable, and the HTTP status codes that go with each have always confused me. Now they don't.

- `401 Unauthorized` = you haven't proven who you are yet. The name is misleading — it's actually an identity failure, not a permissions failure.
- `403 Forbidden` = we know who you are, and the answer is no. That's the authorization failure.

The JWT breakdown was the other piece that clicked. A token has three parts: Header (the algorithm), Payload (the claims — user ID, role, expiration), Signature (the cryptographic proof nothing was tampered with). The payload is Base64-encoded, not encrypted. Anyone can decode it and read it. So you never put passwords or sensitive data there. The security comes from the signature — the server can verify the token hasn't been modified.

The weakness of pure JWT: there's no built-in revocation. A stolen token is valid until it expires. The hybrid model solves this — short-lived access tokens (15-60 minutes) paired with long-lived refresh tokens stored in a secure session. The access token limits the damage window; the refresh token keeps the user from having to log in every hour.

Supabase uses the hybrid: short-lived JWTs, silent refresh via the refresh token, RLS policies that read `auth.uid()` from the verified JWT so the database can enforce per-user data access at the row level.

Clean system once you see how the pieces connect.

---

## The Library Is Working

Story Spinner Plus got real time today. The library feature is finally starting to feel like something I'd actually sit down and use — which is the test that matters.

The original Story Spinner always felt overwhelming. You'd get everything at once: full character breakdowns, every possible trait, all the story seeds dumped on you at the same time. Too much. Paralysis by options.

What I built now: you see a few of thousands of options at a time, scroll and pick, build up a character or story incrementally. The difference in feel is significant. Hard to believe the app didn't exist a little over a week ago. Now it's on my phone, and when I open it, it functions like a real tool.

Still needs tweaking. But the distance from "raw idea" to "thing I would actually use" is real.

---

## Lock Icons and Outlived Metaphors

Random thought I had mid-session while building: it's amazing that the lock icon on a UI element is instantly understood with no text label. Nobody explains it. You just know.

Same with the floppy disk for save — nobody under 25 has saved data to a floppy disk in their life. The magnifying glass for search has nothing to do with searching anymore. These symbols outlived the physical objects they were based on. The mental model is so deeply baked in that it survives the original metaphor.

The lock is the most interesting one because it maps to an *abstract* concept — "this thing won't change" or "this is protected" — and yet it's still immediately obvious. Good iconography doesn't just survive its physical referent; it survives the shift from concrete to abstract.

---

## End of Week Two

This week felt like the boot camp flow locked in. There are three distinct buckets of skill being developed simultaneously:

**Full stack development** — the majority of time. Lectures, integrations, wiring things together. This week was authentication front to back: database restructure, RLS policies, frontend auth flows.

**Algorithm and syntax** — the Codewars problems at the start and end of the day. Character by character. I'm still going the wrong direction too often, but it's less bad than day one.

**Builder time** — the in-between. How do you conceptualize a product idea and translate it into code? This is where I'm strongest. The syntax and algorithm side is what needs grinding.

I'm stronger on the product side. I know how to think about what to build. The gap right now is the translation layer — getting the ideas out of my head and into working code without the syntax being the bottleneck. That gap is closing, but it closes through repetition, not insight.

This weekend: Codewars, freeCodeCamp, drilling both JavaScript and Python.

Week three on Monday.
