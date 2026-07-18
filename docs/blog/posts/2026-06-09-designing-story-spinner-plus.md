---
date: 2026-06-09
categories:
  - Code Platoon
tags:
  - javascript
  - react
  - vite
  - django
  - docker
  - postgresql
  - full-stack
  - design
  - deployment
  - vercel
  - code-platoon
authors:
  - keith
---

# From Wireframe to Full Stack: Designing Story Spinner+ in a Day

Overrated was the modest expectation—plan, plan—that eight questions and a blank TLDraw canvas would occupy the day; before it ended, the application was live.

<!-- more -->

---

## The Eight Questions

Code Platoon's practical planning template cuts through the noise fast. Before you touch a keyboard, you have to be able to answer these:

1. What problem does the application solve?
2. Who are the users?
3. What are their primary goals?
4. What information do they need?
5. What actions can they perform?
6. What pages are required?
7. What data must be collected?
8. What permissions exist?

I had a shortlist of app ideas. A world-building tool called LoreGraph was on it. A mobile reader hub for my fiction site. But both of those felt like someone else's problem. Then I thought about Story Spinner — a random generator I already built and shipped — and the answer became obvious. Upgrade it. Make it full stack. Give users a place to save what they generate.

That's a real problem I actually have.

---

## What Story Spinner+ Is

The existing app ([storyspinner25](https://kgitwh21.github.io/storyspinner25/)) generates characters, story structures, and music inspiration from randomized arrays. The whole thing lives in the browser, stateless. Close the tab and everything disappears.

Story Spinner+ adds the missing half: authentication, persistent storage, and project notebooks so writers can accumulate their generated sparks instead of losing them. The database schema I designed breaks down to four tables:

- **User** — `id`, `email`, `password_hash`, `created_at`
- **Project** — `id`, `user_id` (FK), `name`, `description`, `created_at`
- **SavedSpin** — `id`, `user_id` (FK, nullable), `project_id` (FK, nullable), `spin_type` (enum: character / story / music), `payload` (JSON), `user_notes`, `created_at`
- **CustomPrompt** — `id`, `user_id` (FK), `category`, `prompt_text` (stretch goal for user-defined arrays)

The `spin_type` enum is the routing key. All three generation modes write to the same table and get sorted at the query layer. One clean table instead of three parallel ones.

---

## The UI Direction

The core design question was how to surface three different generation modes — characters, stories, music — without making the home screen feel like a dashboard. The answer was a prize wheel.

An 8-wedge spinner that repopulates its labels based on which mode is active. Character mode loads eight archetypes from the array. Story mode loads eight structures (Hero's Journey, Three-Act, Fichtean Curve, Dan Harmon's Story Circle). Music mode loads eight genres.

When the wheel stops, a card slides up from the bottom of the screen. No page navigation. The result — character name, tarot arc structure (`Wound → Obstacle → Transformation`), audio vibe — appears in-place. Three buttons at the bottom: Spin Again, Copy, Save to Project.

If the user isn't logged in and clicks Save to Project, the card flips in place to a two-field signup form. They register, the spin gets linked to their new account, and they're back at the wheel without losing context. That conversion flow was the part I spent the most time thinking through on the TLDraw board — it has to feel seamless or users won't bother.

The color system: three palette modes that ease in on toggle. Golden Pulp (characters) is obsidian black with parchment cards and muted gold. Dark Emerald (stories) is deep slate with sage green. Neon Purple (music) is violet-tinted charcoal with deep indigo borders.

---

## The Stack Decision

For the full-stack build, the decisions cascaded from two choices I made early:

**Docker Compose for the database.** I have Postgres installed locally, but local installations on WSL hit port conflicts and credential mismatches regularly. Docker Compose sidesteps all of that by running the database in an isolated container with its own config. A `docker-compose.yml` at the repo root spins up the database version, user, and password automatically — the same pattern used in actual cloud deployments.

**Monorepo layout.** Backend Django API in `backend/` at the repo root. Frontend Vite app in `story_spinner_plus_vite/`. The `docker-compose.yml` sits at the top level and coordinates both. This is the standard layout for a project where you want one `git` repo and one deployment pipeline.

The full structure:

```
story-spinner-plus/
├── backend/                    ← Django API (Python)
├── story_spinner_plus_vite/    ← Frontend (Vite/React)
└── docker-compose.yml          ← Postgres + Django coordination
```

---

## Deployment

Frontend goes to Vercel. Backend goes to Railway. The tricky part wasn't either deployment individually — it was getting the two to talk across the CORS boundary.

A Vercel-deployed Vite app makes API calls to a Railway-hosted Django backend. Django has to know the Vercel URL is an allowed origin. The Vite app has to point at the Railway URL for its API base. Both environment configs have to be set correctly in their respective platforms, not just locally.

I hit this wall and spent time with it. That's the learning. Getting a Dockerized Django app onto Railway with CORS correctly configured to accept requests from a Vercel frontend is exactly where most first-time deployments quietly fall apart. The fix is not complicated, but you have to understand what you're fixing — that the browser blocks cross-origin requests unless the server explicitly permits them, and that Railway environment variables are not the same thing as your local `.env`.

Five hours. Both deployed, both talking.

---

## What This Day Was

This was the first time I built something that required me to make real architectural decisions under time pressure. Not which tutorial to follow — which structure to choose, why, and what it would cost me if I chose wrong.

The TLDraw wireframe isn't just a deliverable. It's the point where you have to commit. Once the user journey is drawn out — discovery → friction → onboarding → workspace → retention loop — you can see exactly which database queries each step requires and where the API has to be.

That connection between the wireframe and the database schema, between the UX decision and the backend logic, is what the eight-question template is trying to surface. I understand why now.

---

Tomorrow: more JavaScript fundamentals on FreeCodeCamp, and I want to get deeper into the Django ORM. The schema is designed. The data model is clear. Now I need to write the queries that make it work.
