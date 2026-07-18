---
date: 2026-06-22
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - react
  - supabase
  - spa
  - ai
  - code-platoon
  - codewars
---

# Finishing Is a Skill

Finished and oddly stranded, I stood on the far side of my first SPA while one question kept dialing a number the technical work couldn't answer: if building wasn't the hard part, what was?

<!-- more -->

---

## Three Bugs in One Function

We opened the day with Codewars and I had a broken JavaScript function. The task: count the number of unique consonants in a string. I had the right idea — use a `Set` to handle deduplication automatically. But the output was wrong. Here's what I started with:

```javascript
function countConsonants(str) {
    let strArray = str.split(" ");
    let uniqueCons = new Set(strArray);

    const vowels = "aeiou";
    let ltrCount = 0;
    for (let letter of uniqueCons) {
      if (!vowels.includes(letter)) {
        ltrCount += 1;
      }
    }
    return ltrCount;
}
```

Three bugs, all stacked on each other.

**Bug 1: `split(" ")` splits by spaces, not characters.** I was splitting the string into words, not letters. `split("")` — empty string — is what you want when you need every individual character.

**Bug 2: Case sensitivity.** A `Set` in JavaScript treats `"d"` and `"D"` as different items. The prompt said they're duplicates. Fix: `.toLowerCase()` the entire string before you do anything else.

**Bug 3: The "not a vowel" trap.** My filter was `!vowels.includes(letter)`, which correctly removes vowels. But a string of "printable ASCII characters" can also contain `@`, `!`, numbers, spaces. None of those are vowels, so my code was counting them as consonants. The fix is to check both conditions: the character must be an alphabet letter *and* not a vowel.

```javascript
function countConsonants(str) {
    let lowerStr = str.toLowerCase();
    let strArray = lowerStr.split("");
    let uniqueChars = new Set(strArray);

    const vowels = "aeiou";
    let ltrCount = 0;

    for (let char of uniqueChars) {
      if (char >= 'a' && char <= 'z' && !vowels.includes(char)) {
        ltrCount += 1;
      }
    }
    return ltrCount;
}
```

The regex version is tighter:

```javascript
function countConsonants(str) {
    const uniqueConsonants = new Set();
    const isConsonant = /[bcdfghjklmnpqrstvwxyz]/;

    for (let char of str.toLowerCase()) {
        if (isConsonant.test(char)) {
            uniqueConsonants.add(char);
        }
    }
    return uniqueConsonants.size;
}
```

What I noticed today: Code Wars are getting easier. Not because I've memorized everything — I'm still looking things up. But the same patterns keep coming back. The same tools. And I'm starting to recognize them faster. That's what three to four weeks of daily reps does.

---

## The SPA

The main event was my Department of Otherworldly Affairs Wiki App — a React + Vite frontend connected to a Supabase (Postgres) backend, with Row Level Security locking down what guests versus authenticated members can see.

The database schema is three tables: `entries` (the core wiki content), `profiles` (extends Supabase auth), and `embeddings` (for Booker, the semantic search interface powered by pgvector). No many-to-many relationships at MVP, so no junction tables. Clean.

The RLS setup was the part I didn't want to slow down on, but I had to slow down on it. It matters. The policies distinguish between anonymous users (can read published, non-gated entries), authenticated members (can also read gated entries), and admins (write access — insert, update, delete — only where `profiles.is_admin = true`). There's also a trigger that auto-creates a `profiles` row whenever a new user registers through Supabase auth, because otherwise the foreign key constraint blocks every signup.

That's a lot of wiring. I'm not going to pretend it was thrilling. Database plumbing is database plumbing. But I got through it, and I got through it faster because I changed how I was working with Claude Code.

---

## Small Commits

This is the thing I'm most proud of from today, and it's not glamorous: I did small, incremental commits the whole way through the backend work.

I don't always do this. My instinct is to batch up a bunch of work and push it all at once. But I've learned — painfully — that when you hand an AI a big lift, you get a big mess when something breaks. You don't know where it went wrong. You can't roll back cleanly. Everything is tangled.

Today I committed after each working piece. Connect the Supabase client: commit. Wire up the entries query: commit. Add auth: commit. Add RLS: commit. Each one small enough that if something went sideways, I knew exactly what broke and why.

This is what CI/CD actually is in practice, not as an abstract concept. Small, verifiable steps. The timestamp history in git is proof that I went through the process the right way — I didn't start this project until this morning, and I moved through it phase by phase until it was basically done.

---

## What Actually Transferred

Here's what I keep sitting with: the technical parts of this weren't what made the difference.

I've shipped a lot of things. Novels. Videos. Podcasts. Client projects nobody told me how to structure. I've built enough things from idea to done — with no external deadline, no rubric, no one checking my work — that the shape of a project like this doesn't intimidate me. I can see the end from the beginning. I know that the database phase feels like a quagmire but it does eventually end. I know that "basically done" means I still need to add the Booker AI interface and possibly wire up Stripe (because if someone uses this wiki and wants to donate, why not). I know those things are close, not catastrophic.

That big-picture orientation — that capacity to hold the whole shape of a project in your head while you're down in the weeds of a single phase — is something you build by finishing things repeatedly. Not just code. Anything.

---

## Writing Is an Unfair Advantage With AI

One more thing I've been chewing on. I've never maxed out my tokens in this program, not once, even on the heaviest builds. I started thinking about why.

I think it's because I know how to write. Not just technically — I mean I've spent years thinking about how to say what I mean with precision. And AI runs entirely on words. Clear input, clear output. Imprecise input, garbage output — or worse, confident-sounding garbage you can't immediately tell is wrong.

When you're not a writer, you tend to describe what you want in loose, general terms. The AI guesses at the gaps. You don't understand the output. You ask a follow-up. The AI guesses again. You spiral. That's how you max out your tokens — not because you needed more tokens, but because you needed more precision upfront.

I also know when to stop prompting. When to take what the AI gave me and run with it. When to break out of the conversation and just implement. That's not something you get automatically. That's something you learn by doing it wrong a bunch of times.

---

## What's Left

The Booker AI interface — that's the piece I still need to add. The semantic search, the pgvector query, the actual AI-powered part. And maybe Stripe, just because it'd be a shame to build something I care about and not give people a way to support it.

But today? Today I basically finished an app I started this morning. That's a good day.

More reps tomorrow.
