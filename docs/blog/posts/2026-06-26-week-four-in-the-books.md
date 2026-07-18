---
date: 2026-06-26
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - docker
  - code-platoon
  - algorithms
  - reflection
---

# Week Four in the Books

Listen: I wrote before dismissal on day nineteen, called four weeks of changed habits and working code a little progress, and prepared to leave for EVO as though nothing remarkable had happened.

What a week.

<!-- more -->

---

## List Comprehensions: One Line to Rule Them All

Earlier in the week we covered list comprehensions in Python — one of those things where once you see it, you can't unsee it.

The basic formula:

```python
new_list = [expression for item in iterable if condition]
```

That's the whole thing. Compare it to what you'd write the traditional way:

```python
# Traditional loop — 4 lines
numbers = [1, 2, 3]
doubled = []
for x in numbers:
    doubled.append(x * 2)
# Result: [2, 4, 6]
```

```python
# List comprehension — 1 line
numbers = [1, 2, 3]
doubled = [x * 2 for x in numbers]
# Result: [2, 4, 6]
```

Same output. No `.append()`. No loop boilerplate. And if you want to filter while you build, you drop an `if` at the end:

```python
numbers = [1, 2, 3, 4, 5, 6]
evens = [x for x in numbers if x % 2 == 0]
# Result: [2, 4, 6]
```

It's faster than a standard for loop at runtime, and it reads cleaner on the page once you internalize the pattern. It's the kind of thing you resist for about a day and then use everywhere.

---

## Docker: Images vs. Containers

The question that trips everyone up early in Docker: what's the difference between an image and a container?

Short answer — an image is a blueprint, a container is what you build from it.

**Docker Image** — read-only. Static snapshot. Once built, it doesn't change. If you need to update the application, you build a new image. It packages everything the app needs: source code, runtime, libraries, config. Built from a `Dockerfile`. Lives in a registry, takes up disk space.

**Docker Container** — the living execution of an image. Can be started, stopped, restarted, destroyed. Isolated process on the host OS. Gets a temporary read-write layer on top of the image while it runs — but when the container is deleted, that layer is gone unless you've mapped a volume. You can run dozens of identical containers from a single image.

The mental models that made it click for me:

*Architecture analogy:* the image is the blueprint for a house. The container is the physical house built from that blueprint. Multiple identical houses, one blueprint, what happens in one doesn't affect the others.

*OOP analogy:* the image is a class definition. The container is the instantiated object.

| Feature | Docker Image | Docker Container |
|---|---|---|
| State | Passive / Static | Active / Running |
| Modifiability | Read-only | Read-write (ephemeral) |
| Lifespan | Permanent until deleted | Temporary |
| Analogy | Recipe | The baked cake |
| Command | `docker build`, `docker pull` | `docker run`, `docker start` |

---

## What a Real Dockerfile Looks Like

We started with a bare Alpine container — `FROM alpine`, `COPY` a text file, `CMD` to print it. Simple. But the same logic scales directly to a full application. Here's what the commands actually do in the real world:

**COPY** — in homework we moved a single `.txt`. In a real app, you're moving the whole codebase:

```dockerfile
COPY . .
```

In practice, you often do it in two passes to speed up builds: copy the dependency file first, install, then copy the rest. That way Docker can cache the dependency layer and skip reinstalling every time you change application code.

**CMD** — homework used `cat` to read a file, which finished in a millisecond and shut the container down. For a web application, CMD starts the server and keeps it alive:

```dockerfile
# Python/Django
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

# Node/React
CMD ["npm", "run", "dev"]
```

**RUN** — this one trips people up because `RUN` and `CMD` sound like they do the same thing. They don't. They happen at completely different times:

- `RUN` fires during the **build phase** (`docker build`) — installing packages, setting up the environment
- `CMD` fires during the **run phase** (`docker run`) — starting the actual application

**EXPOSE** — tells Docker which port the server inside the container is listening on. Mostly documentation, but standard practice.

A full Python Dockerfile, soup to nuts:

```dockerfile
# 1. Base OS and language runtime
FROM python:3.10-slim

# 2. Working directory inside the container
WORKDIR /app

# 3. Dependencies file first (for cache efficiency)
COPY requirements.txt .

# 4. Install during build
RUN pip install -r requirements.txt

# 5. Rest of the application code
COPY . .

# 6. Document the port
EXPOSE 8000

# 7. Start the server
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

---

## ENV Variables and the Syntax Trap

Task 3 was environment variables — defining them in the Dockerfile and overriding them at runtime. The `--rm` flag deletes the container the second it finishes, which keeps your system clean and satisfied the "must exit after execution" requirement.

```dockerfile
FROM alpine:latest
ENV GREETING="Hello from inside the container!"
CMD echo $GREETING
```

```bash
docker run --rm task-3-image
# Output: Hello from inside the container!

docker run --rm -e GREETING="Stretch goal achieved!" task-3-image
# Output: Stretch goal achieved!
```

The syntax trap: if you use the bracket form — `CMD ["echo", "$GREETING"]` — Docker executes it directly without a shell, and your terminal literally prints `$GREETING` instead of the value. Remove the brackets (the "shell form") and it goes through the container's command line, which reads the variable correctly.

---

## Resource Management Questions Worth Knowing Cold

Three questions that came up in the tasks and are worth having solid answers for:

**Why don't stopped containers appear with `docker ps`?**  
By default, `docker ps` only shows active running processes. A stopped container isn't running — it's just saved state on disk. Use `docker ps -a` to see everything.

**Why can't you delete an image if a container depends on it?**  
Because the image is the structural foundation. Even a stopped container keeps a hard link to the image's read-only layers so it can be restarted. Delete the image underneath it and you corrupt the container's file system. Delete the container first.

**What does `--rm` prevent?**  
Container sprawl. Normally Docker leaves stopped containers on disk in case you want to restart them or check their logs. `--rm` tells Docker to delete the container the instant it finishes running. Clean and gone.

---

## Swimming, Not Treading

Last week I felt like I was treading water. This week I feel like I'm swimming.

The project got done, got done efficiently, and got done well. One of my classmates told me my video was great. I didn't say much about it — I don't want to make people feel bad, because I've been producing video and media for years and I have a big head start on that specific skill. But I know what I made.

Today I led the weekly retro, our class feedback session. I've been noticing that we don't get much organic interaction — there's no equivalent of hanging out before class or chatting through lunch, the way you might in a physical room. Everyone's head-down in their own corner of Zoom. The retro is the one built-in moment for us to actually talk, and I wanted to do something with it.

I told my classmates something I've been noticing: developers have a stereotype for a reason. A lot of people can build a full-stack application like a savant and then freeze when someone asks them what it does or how it helps anyone. I've watched it happen in the cohort already. And the fix isn't mysterious — it's reps. Leading retros, doing the live algorithms even when it's uncomfortable, finding any excuse to get in front of people and explain something in plain language. Those little class roles are what I remember from basic training. They seemed small, they weren't small.

I also got ahead. I was already halfway through Monday's Docker lesson, so I finished it. Then I read ahead into Tuesday's material — turns out it's essentially the algorithm work we've already been doing, just in a different form. No sweat. And I ported a game I built earlier in the week — a Python project — to JavaScript so it could run in the browser. Then I built a tool to make more games like it later.

---

## What This Month Actually Showed Me

One month in. Three months left, give or take.

I've been building things in isolation for years. No way to benchmark where I stand relative to anyone in the same lane. Code Platoon gave me that. And I'm not trying to crown myself class winner — it's not that kind of thing. But I've been able to see myself next to people with similar goals, and I'm a stronger logical thinker than I gave myself credit for. Not the fastest. Not always the most accurate under pressure. But I know how to break a problem down. I know how to start something, hit the wall, and not quit when it gets confusing.

That's the skill. Not the syntax. Not the algorithms. The refusal to let friction be the end of the conversation.

Earlier this week I hit a wall with the project and just... kept going. Found another route. It didn't knock me out. And I've noticed that's not universal. The ability to stay in a tangle and keep pulling until something comes loose — a lot of people fold before that.

I spent years making things that felt useless in the moment. Websites nobody visited. Books nobody bought. Tools I built for myself. 60-plus GitHub repositories, a lot of them small and quiet. But you never know when the skill becomes visible. You never know when someone looks up and sees what you've been doing.

This week felt like one of those moments. Not a climax. Just a signal that the direction is right.

Week five is supposed to be more hand-coding, less AI assist. That's the Code Platoon I signed up for. Let's go.

---
