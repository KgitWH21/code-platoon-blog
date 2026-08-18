---
date: 2026-08-18
categories:
  - Code Platoon
authors:
  - keith
tags:
  - linked-lists
  - data-structures
  - python
  - debugging
  - personal-project
  - artificial-intelligence
  - code-platoon
---

# The Code Has to Become Mine

If the lights failed, I could tell you I knew this house—every room, every corner, every little turn inside every corner—but you know me too well: you can hear the pause, you can find the pain. Finishing early gave me time to go back, turn on the lights, and learn the floor plan.

<!-- more -->

---

Code Platoon Week 12, Day 2—Day 51—is done.

Today was another lighter day. The main lesson was linked lists, a concept I found pretty simple. The code wasn't that bad either.

A linked list stores data in nodes. Each node carries its value and a reference to whatever comes next.

```text
[value | next] → [value | next] → [value | next] → None
```

The structure makes intuitive sense. Start at the head, follow the link, arrive at the next node, and continue until there is nowhere else to go.

What interested me more was the practical question: why use one?

Linked lists come with an obvious cost. Every node has to carry both its data and its link to the next node. Moving through the structure also means following those links in order. I can't jump directly to an arbitrary position the way I can with a Python list.

A playlist is one example. A song can point to the song that follows it. But with a singly linked list, that movement only goes one way.

```text
current song → next song → next song
```

No previous button. Not yet.

Doubly linked lists close that gap by allowing each node to point in both directions. For today, though, the singly linked version was enough to make the central idea clear: the organization of data determines what movement through that data is easy, expensive, or impossible.

## One Extra Letter

I also worked through a small Python kata about an ant stampede.

An orderly trail of living ants looks like this:

```text
..ant..ant.ant...ant.ant..ant.ant....ant..ant.ant.ant...ant..
```

After the stampede, some complete `ant`s remain while pieces of dead ants—`a`, `n`, and `t`—are scattered through the string. The challenge is to determine how many ants died.

My solution was close:

```python
def dead_ant_count(ants):
    dead_ants = ants.replace("ants", "").count("a")
    dead_ants_2 = ants.replace("ants", "").count("n")
    dead_ants_3 = ants.replace("ants", "").count("t")

    return max(dead_ants, dead_ants_2, dead_ants_3)
```

The logic was almost right. The problem was one extra letter.

Each living ant is spelled `ant`, but I was removing `ants`. That meant I wasn't removing the living ants at all. Their body parts stayed in the string and corrupted the count.

The corrected version is:

```python
def dead_ant_count(ants):
    if ants is None:
        return 0

    remains = ants.replace("ant", "")

    return max(
        remains.count("a"),
        remains.count("n"),
        remains.count("t"),
    )
```

First, remove every complete living ant. Then count the remaining heads, bodies, and tails. The largest count is the number of dead ants.

Why the largest?

The instructions say that, when in doubt, scattered pieces belong to the same ant. Two heads and one body represent two dead ants, not three. One ant can account for one `a`, one `n`, and one `t`, but a second `a` requires a second ant even if no other piece of it remains.

```text
leftover pieces: a a n

ant 1: a n
ant 2: a

dead ants: 2
```

So the answer is not the total number of pieces. It is the maximum number of any one kind of piece.

I had the right strategy. I had the right use of `max()`. I had even organized the three counts correctly. But I removed the wrong word.

That is programming in miniature: the entire idea can be sound while one character makes the implementation false.

## Running an Audit on Myself

I had a medical appointment, so I was gone for most of the middle of the day. When I returned, I got distracted by a large side project that I won't go through here. (Visit my website if you want to know more.)

The more important work waiting for me is an audit of my individual project.

I finished the application early, which means I have time to return to the functional codebase and become more familiar with the parts where I am still weak. The application runs. It meets the requirements. But soon Ill need to be able to speak for the code.

That's fair.

```text
It runs
   ↓
I can trace it
   ↓
I can explain it
   ↓
I can change it intentionally
```

Those are different levels of understanding.

Im confident in some areas of the project. In others, I know what the code does in the broad sense but would have trouble explaining exactly why a function is structured a certain way, how data moves through a particular path, or what would break if one part changed.

Finishing early bought me time to close that gap.

My plan is to use AI as a reviewer. Instead of asking it to produce more code, I can have it quiz me on the code that already exists. It can ask me to explain a route, predict what a function will return, trace state from the frontend to the backend, identify why a design decision was made, or describe how I would modify a feature without breaking the rest of the application.

Its similar to using AI for a simulated interview.

```text
AI: What does this function do?
Me: Explain it without running the code.

AI: Why is this dependency here?
Me: Trace what fails without it.

AI: Change this requirement.
Me: Identify every part of the system affected.
```

That feels like one of the most useful roles AI can play in learning: not a machine that prevents me from encountering gaps, but one that relentlessly helps me find them.

## The Floor Plan

Theres an important difference between possessing working code and owning my understanding of it.

The individual project is functional. But a functioning application can hide uncertainty. If I only interact with it through the interface, I can know that the house stands without knowing where the pipes run.

The audit forces a different relationship with the project.

I need to open the walls.

I need to follow the links.

In that sense, the project audit and the linked-list lesson are about the same thing. A linked list is not merely a collection of values. Its behavior comes from the connections between them. My application is also a set of connected nodes: components, routes, functions, models, requests, responses, and state.

```text
user action
    ↓
frontend component
    ↓
API request
    ↓
backend route
    ↓
database operation
    ↓
response
    ↓
updated interface
```

To understand the application, I have to be able to start at one node and follow the references all the way through.

Tomorrow, the goal is to lock in and begin that process. No extra scope. No ornamental features added because I finished early. Just a deliberate tour through the parts of my own project that still feel unfamiliar.

The code works.

Now the code has to become mine.
