---
date: 2026-06-04
categories:
  - Code Platoon
tags:
  - python
  - algorithms
  - codewars
authors:
  - keith
---

# The Loop That Wasn't

It was the loop that I had trusted through a hundred small programs, yet it received me not as one familiar but as a stranger wearing the face of a friend—the bug beneath the bug. It wasn't a logic error. It wasn't a missing edge case. It was a mental model problem I'd been hefting without knowing it, and once I saw it, everything snapped into place.

<!-- more -->

The problem: count the truthy values in a list. Simple. I'd written this kind of loop before, in OSSU, in practice sets, in small utilities I'd built for my own work. I knew how loops worked. Except I didn't, quite.

Here's what I was thinking:

```python
sheep = [True, False, True, True, False]

sheep_count = 0
for i in sheep:
    if i == 0:
        sheep_count += 1

return sheep_count
```

The intent was to count truthy items. The output was zero. Every time.

The mental model I'd brought in from other contexts — particularly from working through OSSU problems that leaned heavily on `range()` — was that the loop variable `i` was an *index*. A position in the list. `0`, `1`, `2`, `3`. So `if i == 0` was checking whether the index was zero, not whether the value was falsy.

That's how `range()`-style loops work:

```python
for i in range(len(sheep)):
    # i is 0, 1, 2, 3, 4 — an index
    if sheep[i]:
        sheep_count += 1
```

But Python's `for x in array:` syntax hands you something different. It hands you the *value* at each position, not the position itself. `i` in `for i in sheep:` isn't a counter. It's the actual element.

```python
for i in sheep:
    # i is True, False, True, True, False — the values
    if i:
        sheep_count += 1
```

Once I made that switch, the function worked immediately.

---

The unlock here wasn't just fixing the bug. It was understanding the conceptual distinction between two different loop patterns and when each one makes sense.

**Index-based iteration** (`range(len(array))`): use this when you need the position — when you're comparing elements to their neighbors, modifying the list in place, or need to know where you are in the sequence.

**Value-based iteration** (`for x in array:`): use this when you just need to *do something* with each element. Python hands you the item directly. Cleaner, more readable, less surface area for the kind of bug I hit.

The deeper principle is one I keep encountering in different forms: **logic first, syntax second**.

The logic of "count truthy values" is clear. What I failed to do was translate that logic correctly into Python's specific syntax. I imported a pattern from a different context — one where the loop variable was always a position — and it worked until it didn't.

Every language has its idioms. Python's `for x in array:` is one of the most fundamental ones, and it's genuinely different from what C, Java, or even JavaScript's index-based `for` loop does. The syntax isn't just notation for the same operation. It encodes a different intent.

---

The cleaner version of the function, once the mental model was corrected:

```python
def count_sheeps(sheep):
    count = 0
    for animal in sheep:
        if animal:
            count += 1
    return count
```

Or the Python shorthand, once you know it exists:

```python
def count_sheeps(sheep):
    return sheep.count(True)
```

Or the functional approach:

```python
def count_sheeps(sheep):
    return sum(1 for animal in sheep if animal)
```

All three are correct. The first one is the one you should write when you're solidifying the mental model. The shorthand versions come after you understand what they're shortcutting.

The thirty minutes wasn't wasted. That's how long it takes to notice you've been carrying a wrong assumption, which is usually the hardest part. The fix itself took thirty seconds.
