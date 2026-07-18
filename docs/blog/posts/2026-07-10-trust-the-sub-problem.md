---
date: 2026-07-10
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - algorithms
  - recursion
  - oop
  - code-platoon
---

# Trust the Sub-Problem

Trust the smaller problem, tell the room you're only tired, and if the whole week starts leaning like funeral china in a cabinet gone soft with rot—well, no need to trouble Hanoi.

<!-- more -->

---

## Quick Sort and the Tower of Hanoi Aren't the Same Trick

First thing worth untangling: Quick Sort and the Tower of Hanoi are not the same algorithm wearing different clothes. Quick Sort sorts an array. The Tower of Hanoi is a puzzle — three pegs, a stack of disks, move the whole stack without ever putting a bigger disk on a smaller one. Different problems entirely. But they get taught back to back for a reason, and the reason is recursion.

Quick Sort works by picking a pivot, shoving everything smaller to its left and everything bigger to its right, then doing the exact same thing again to each of those halves. It doesn't sort the array so much as it keeps narrowing the problem until the pieces are too small to need sorting anymore.

The Tower of Hanoi teaches you to trust that narrowing before you've seen the whole picture. Solving it for three disks isn't a matter of memorizing eight moves — it's three moves you actually think about, wrapped around a version of the same problem, but smaller. Move the top two disks out of the way to the spare peg. Move the big one where it belongs. Move the two disks from the spare peg on top of it. Step two only works because you already trust step one and step three will handle themselves, even though "themselves" is the identical puzzle with one fewer disk.

That's the whole bridge. Quick Sort trusts that sorting the left half and sorting the right half will add up to a sorted array. Hanoi trusts that solving n-1 disks will add up to solving n. Neither algorithm requires you to hold the entire solution in your head at once. They require you to trust the sub-problem and get out of its way.

I didn't expect a sorting algorithm and a peg puzzle to be the thing that reframed my own week, but here we are.

---

## The Video Store Nobody Asked For, and Coding to the Test

We got our end-of-week assessment today: build a full app for a video rental store — an old Blockbuster, basically. Customers, movies, inventory, rentals, the whole model. On paper that's a monster. In practice it feels like the first assignment that actually resembles a real request from a real client. Someone needs to track what they own, who has it, and when it's coming back. That's not a classroom exercise, that's a business problem, and I'm genuinely excited to build it instead of just relieved to survive it.

Part of that confidence is a lesson from last week that finally paid off: code to the tests, not to the README. I got burned on an optional assignment a week ago doing it backwards — read the README, wrote code that matched my read of it, ran the tests, and watched everything fail because the tests wanted a very specific variable name or a very specific string and my version was "correct" by every measure except the one that counted. The README explains intent. The test suite is the actual contract — exact method names, exact return values, exact edge cases. This time the instructions were blunt about it going in, and it's already saved me the same afternoon of confusion. I passed the first test without drama, because I looked at what it was asking for before I wrote a line of code.

The other half of why this doesn't feel like drowning is just volume. Tuesday of this week I'd never written a class. By Friday I've probably written fifty of them — freeCodeCamp reps, lecture exercises, anything I could find an excuse to build. Instances, class attributes versus instance attributes, methods, all of it went from something I had to think through to something my hands just do. The video store project is large in scope, but it's not large in any single piece. It's the same fifty small things, stacked into something bigger. Trust the sub-problem.

---

## Week Six, and the Halfway Point Coming Up Fast

Retro this afternoon was almost unanimous: everybody's into object-oriented programming, and a lot of people said the pieces are starting to connect instead of sitting next to each other unconnected. That tracks with where I am. Today itself I was mostly running on empty — did some work in the afternoon, but this was a recovery day more than a push day, and I think that's the right call after how hard I went earlier in the week. Next week is week seven, which puts us at the halfway mark of fourteen. We've been at this a month and a half and it's already hard to remember what not being able to do this felt like.

The other real shift this week didn't happen in the code editor. I wrapped the audiobook job and deliberately cleared everything else off my plate. Starting yesterday, it's just two things: the novel, and code. Not the novel, code, three side projects, and whatever else wandered in — two things. I've spent a decade able to carry ten threads at once, and that's a real skill, but it's also the same instinct that makes a full day of nothing-but-code start to drag by hour six. Cutting it down to two isn't giving up range. It's the same lesson as Quick Sort and the Tower of Hanoi, just pointed at my own schedule instead of an array: stop trying to hold the whole thing at once, narrow it, and trust that the two pieces you're actually working will add up to something bigger than the ten you were spreading yourself across.

Looking forward to the video store build, and looking forward to an actual weekend to recover before it starts. That's week six.
