---
date: 2026-06-15
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - codewars
  - algorithms
  - serverless
  - api
  - semantic-search
  - story-spinner
  - doa-archive
  - code-platoon
---

# Sort By What You Mean

Day 11. Week three. The project idea I've been circling for weeks finally locked in today — and I built something that quietly impressed me before lunch.

<!-- more -->

---

## Descending Order: JavaScript Doesn't Sort Numbers

Morning Codewars was a digit-rearrangement problem. Given any non-negative integer, return it with its digits rearranged into the highest possible number. `1234` becomes `4321`. `3021` becomes `3210`.

Here's the solution I landed on:

```javascript
function descendingOrder(n) {
  return parseInt(String(n).split('').sort((a, b) => b - a).join(''), 10);
}
```

The steps map cleanly:

- `String(n)` — convert the number to a string
- `.split('')` — explode it into individual characters, because strings in JS don't have a `sort()` method
- `.sort((a, b) => b - a)` — sort descending; without the comparator function, JS sorts alphabetically by default, which gives you wrong answers on multi-digit numbers
- `.join('')` — put it back into a single string
- `parseInt(..., 10)` — convert back to an integer; the `10` forces base-10 parsing

The Python equivalent handles this more gracefully — `sorted(str(n), reverse=True)` — because Python's sort is smarter about numeric strings by default. JavaScript requires you to be explicit. Pass the comparator or accept the default, which isn't what you mean.

That "isn't what you mean" is the recurring lesson in these Codewars problems. The code does exactly what you write. If you write ambiguous instructions, it executes the wrong thing with complete confidence.

---

## Least Larger: Track Two Things at Once

The afternoon Codewars was a harder problem: given an array and an index, find the index of the *smallest number in the array that is still larger than the element at the given index*. Return `-1` if no such element exists.

My first draft got tangled trying to track the result and the value separately. The fix is clean once you see it:

```javascript
function leastLarger(a, i) {
  let target = a[i];
  let smallestLargerValue = Infinity;
  let resultIndex = -1;

  for (let j = 0; j < a.length; j++) {
    if (a[j] > target && a[j] < smallestLargerValue) {
      smallestLargerValue = a[j];
      resultIndex = j;
    }
  }

  return resultIndex;
}
```

Starting `smallestLargerValue` at `Infinity` is the key move. Every real number in the array is smaller than `Infinity`, so the first valid candidate always wins the comparison — no null checks, no edge-case handling for the first iteration. The condition `a[j] > target && a[j] < smallestLargerValue` reads as: "strictly larger than what I'm comparing against, and closer to it than anything I've found so far." Two constraints, one pass.

---

## Query Parameters vs. Request Bodies

Serverless function work this morning covered a distinction I want to nail permanently: where does data travel, and why does it matter?

**Query parameters** live in the URL:

```
/add-numbers?a=3&b=5
```

- Always arrive as strings — you have to convert manually with `Number()`, `parseInt()`, etc.
- Visible in the URL, browser history, and server logs — never put secrets here
- No practical size limit in spec but browsers cap around 2,000 characters in practice
- Conventionally used with `GET`; right for filters, lookups, simple stateless reads

**Request bodies** travel in the HTTP payload:

```javascript
fetch("/my-function", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ a: 3, b: 5 })
})
```

- Can be structured as JSON, form data, multipart, etc.
- Not stored in logs or URL history by default — safer for sensitive data
- No practical size limit for the data itself
- Must be parsed on arrival: `await req.json()` for JSON, `await req.formData()` for form data
- Conventionally used with `POST`, `PUT`, `PATCH`; right for creating, updating, anything sensitive

The clean rule: **query params for reading, bodies for writing or anything you don't want in logs.** For a simple add-numbers function that's stateless and public, query params are the right call. For anything touching a database or carrying credentials, use a body.

---

## Third-Party API: What I Actually Learned

Spent the morning integrating a third-party API into the project. AI features were involved — they didn't make the app better. Here's what actually stayed with me.

**Environment variables are not optional.** The Anthropic key landed in `backend/.env` in plaintext. That file has to be in `.gitignore` before you ever commit. Not "soon." Before. Once a key is in git history, it's compromised — you have to rotate it.

**Check what your app is actually hitting.** `VITE_API_URL` was misconfigured and pointing at the frontend itself (`5173`) the entire time. The local dev was falling through to a fallback in `client.js` and appeared to work. It wasn't working. The lesson: when something feels off, open the Network tab and watch the actual requests. The tab tells you the truth. Your assumptions don't.

**Test with the strongest model first.** When you're validating whether an AI feature concept works, don't start with the cheapest model to save money. Start with the best one. Validate the concept on full quality, then optimize down to a smaller model if the concept holds. Doing it in reverse means you might kill a good idea because it performed badly on a weak model, or keep a bad idea because the cheap model fooled you.

**Reverting cleanly is a real skill.** Rolled back a migration, stripped dead code, restored a component. Most people leave the zombie code in "just in case." Cleaning it up isn't just aesthetics — it's preventing the next person (including future you) from trying to figure out if that code is supposed to be there.

---

## Semantic Search on Story Spinner Plus

Before all the third-party API work, I shipped something that I think is the most genuinely useful thing I've added to Story Spinner Plus so far: semantic search over the element library.

Here's what it does and why it matters.

Before: the wheel was pure random. You could spin 20 times looking for elements with a specific emotional texture and the app had no way to help you. Random is fine as a starting point. It doesn't scale when you have a creative intent.

After: every story element in the database has a 1,536-dimensional vector — a numerical fingerprint generated by OpenAI's embedding model. When you type a search query, that query gets the same treatment: converted to a vector on the fly. The app finds the elements whose vectors are closest using cosine similarity. Those results load directly onto the wheel.

The thing that makes it more than just a better search bar: it finds concepts that *relate* to your query without containing the words. Search "loneliness" and you surface "Social isolation despite online connection" — the concept, not the keyword. That's the difference between search and *semantic* search.

The elements were already in the database. The dataset I needed existed. I just hadn't given it a shape the app could reason over.

The technical pieces: embeddings, vector storage in Postgres via pgvector, cosine similarity at query time. These are marketable, composable skills. They're also now working in a live app on my phone.

---

## The DOA Archive

This is what today was really about.

I've been carrying a question around for a few hours: what should I build for the Code Platoon agentic coding project? It has to be a full-stack app with auth, CRUD, and a third-party API integration. Beyond the requirements, I wanted something that was actually *mine* — something connected to what I'm already building, with real utility beyond the classroom.

I spent the afternoon going back and forth on a few options. The one that won: a lore wiki for *Cereus & Limnic: Escape From Okinawa*.

The thesis behind it: every novel has more world than makes it onto the page. Character backstories that never got written. Cut scenes. Location details. In-universe documents. Right now that material dies in a drawer or a Notion doc nobody sees. Readers who finish a book they love have nowhere to go. There's no sanctioned deep-dive, no reason to stay in the world.

The wiki turns that unused material into a reader retention tool. Public and searchable for everyone. Gated depth — cut scenes, voice notes, behind-the-scenes content — for members. An AI guide (Booker, already live on the site) powered by semantic search to help readers find content they didn't know to ask for.

The gating structure is the business model. Free browsing earns trust. The gated layer earns money. Booker doesn't exist for decoration — he's the conversion mechanism, surfacing the right teaser at the right depth until readers decide the gate is worth crossing.

For the capstone scope: one character or location, one genuinely irresistible piece of gated content, Booker able to guide a reader to it. Prove the thesis on a small surface before expanding. The full multi-author product comes later if readers convert on this version.

Tech stack: React + Vite + Django + Supabase. Not MkDocs — auth-gating and semantic search fight the tool at exactly the points where the value lives. Build it where I already work.

The project got approved. That's week three, day one.

---

## Claude Code for Client Work

Between the bootcamp work, I'm also doing contract work — AI audiobook production for a client. The manuscript came in as a `.docx`. My old instinct would have been to paste the text into an LLM browser tab or run it through Atticus. Instead I opened VS Code, ran Claude Code in the CLI, created a project, and converted it to EPUB in about ten seconds.

That felt good in a specific way. Not because it was faster — it probably was about the same speed. Because I did the developer thing. I have these capabilities now. I built my own converter, downloaded it, had the EPUB. Then I uploaded it and let the audiobook pipeline run.

That's the other skill I'm developing in parallel with the bootcamp: actually using what I'm learning, immediately, on real work.

---

## What's Building

Three things running simultaneously right now:

**Story Spinner Plus** is live and growing. Semantic search is a real differentiator. The wheel finally feels like something I'd actually use rather than a demo.

**The DOA Archive** is scoped and approved. The wiki builds next week: React + Vite + Django + Supabase, Booker on semantic search, gated content behind email confirmation.

**Client work** continues. Manuscript is converted and uploading. The audiobook pipeline is running.

The syntax still slows me down in Codewars. The logic is right more often than not. That will close through repetition.

Day 11. Week three. Not a bad place to be.
