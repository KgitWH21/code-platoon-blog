---
date: 2026-08-10
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - codewars
  - arrays
  - algorithms
  - react
  - personal-project
  - software-engineering
  - code-platoon
---

# Don't Sprint Past the Answer

What did OPEN mean at 4:30 in the morning—lift the laptop lid, or admit that the last month still had time left to serve?

<!-- more -->

---

## Least Larger

Today's Codewars problem gives me an array of numbers and an index. I have to return the index of the smallest number that is larger than the number at the given index. If no larger number exists, I return `-1`.

For example:

```python
a = [4, 1, 3, 5, 6]
i = 0
```

The target is `a[0]`, which is `4`. Two values are larger than `4`:

```text
5 at index 3
6 at index 4
```

The smallest of those values is `5`, so the answer is its index:

```python
3
```

The important distinction is that the function does not return the smallest value in the whole array. It does not return the first larger value it encounters. It does not even return the value it eventually chooses.

It returns the **index of the smallest value that is strictly larger than the target**.

That is several requirements packed into one sentence.

## My First Attempt

```python
def least_larger(a, i): 
    smallest = 0
    for idx in range(len(a)):
        if a[i] < a[idx]:
            smallest = a[idx]
        return smallest
    return -1
```

I knew I needed to store something so I could compare candidates and keep the smallest one. For some dumbass reason, I didn't want to do it initially.

The result was code that had the outline of the solution without actually performing the search.

There were three separate problems.

## Returning Too Soon

The first problem was indentation:

```python
for idx in range(len(a)):
    if a[i] < a[idx]:
        smallest = a[idx]
    return smallest
```

Because `return smallest` is inside the loop, the function exits on the first iteration. It never gets a chance to inspect the rest of the array.

That means this isn't really a loop. Syntactically, it is a loop, but operationally it can run only once.

The return belongs after the loop:

```python
for idx in range(len(a)):
    # inspect every candidate

return best_idx
```

The whole point of this algorithm is comparison. I cannot know which qualifying value is smallest until I have given every value a chance to beat the current best candidate.

## Returning the Value Instead of the Index

The second problem was what I stored:

```python
smallest = a[idx]
```

This saves the value, but the prompt asks for its index.

That distinction appears constantly in array problems:

```text
idx     → where the item is
a[idx]  → what the item is
```

If I store the index, I get both pieces of information. I can return `best_idx` when I am finished, and I can still retrieve the current best value with `a[best_idx]` whenever I need to compare it.

```python
if a[idx] < a[best_idx]:
    best_idx = idx
```

The index is therefore the more useful state to preserve.

## Larger Is Only the First Filter

The third problem was that this condition only determines whether a value qualifies:

```python
if a[i] < a[idx]:
```

It does not determine whether that value is the best qualifying answer.

Suppose the array is:

```python
[4, 9, 7, 5]
```

With `4` as the target, `9` qualifies. So do `7` and `5`. If I overwrite my variable every time I find a larger value, I happen to finish with `5` in this example—but only because `5` appears last.

Change the order:

```python
[4, 5, 7, 9]
```

The same strategy finishes with `9`, the largest candidate instead of the smallest.

I need two questions inside the loop:

1. Is this value larger than the target?
2. Is it smaller than the best larger value I have found so far?

## Keep the Best Candidate

The corrected iterative solution is:

```python
def least_larger(a, i):
    target_value = a[i]
    best_idx = -1

    for idx in range(len(a)):
        if a[idx] > target_value:
            if best_idx == -1 or a[idx] < a[best_idx]:
                best_idx = idx

    return best_idx
```

`best_idx` begins at `-1`, which does two jobs.

First, it is the answer the problem requires when no larger value exists. Second, it tells me that I have not found a candidate yet.

```python
if best_idx == -1 or a[idx] < a[best_idx]:
```

The first qualifying value becomes the temporary best because there is nothing to compare it against. After that, a candidate can replace it only by being smaller.

For this input:

```python
a = [4, 9, 7, 5]
i = 0
```

the state changes like this:

| Current value | Larger than `4`? | Best candidate |
| ---: | :---: | ---: |
| `4` | No | none |
| `9` | Yes | `9` at index `1` |
| `7` | Yes | `7` at index `2` |
| `5` | Yes | `5` at index `3` |

The algorithm visits the array once, so it takes `O(n)` time. It stores only the target and one index, so it uses `O(1)` extra space.

## A More Pythonic Version

The same idea can be written more compactly:

```python
def least_larger(a, i):
    larger_indices = [
        idx for idx in range(len(a)) if a[idx] > a[i]
    ]

    if not larger_indices:
        return -1

    return min(larger_indices, key=lambda idx: a[idx])
```

The list comprehension collects the indices of every qualifying value. Then `min()` selects the index whose corresponding array value is smallest.

The key is this part:

```python
key=lambda idx: a[idx]
```

Without the `key`, `min()` would return the smallest index. With the key, it compares the values at those indices while still returning the index itself.

This version is expressive, but it creates an extra list. The iterative version communicates the mechanics more clearly and uses constant extra space. Right now, that is the version I need to understand—not just because it solves the kata, but because keeping track of the best candidate is a reusable algorithmic pattern.

## One Month Left

Code Platoon Week 11, Day 46 is done.

Graduation is September 11. Today is August 10. That puts me almost exactly one month from the end, and I can feel myself getting impatient.

I know this about myself. When the finish line of something gets close enough to see, I want to sprint toward it. The problem is that it is still too early for that sprint.

By the end of this week, we will have more or less covered the entire full-stack process. That is insane. Eleven weeks ago, the pieces were separate abstractions. Now I can see the path from database to backend to frontend, and today's React material was about making that frontend work easier.

The core lectures may be winding down, but the work is not finished. The next month is about proving I can assemble the whole process myself.

## Personal Project Mode

I am fully in personal project mode now.

Today I set up the workflow, wrote a README, handled the basic scaffolding, and established the beginning of the project. I know what I am going to build. I know what I have to do. None of the individual steps feels mysterious.

The difficult part is accepting that it will still take hours and hours of typing everything out by hand and carrying the process from beginning to end.

```text
idea
  ↓
planning
  ↓
scaffolding
  ↓
implementation
  ↓
testing
  ↓
finished project
```

Knowing the route does not remove the distance.

That is where my impatience comes from. I am having so much fun thinking about the things I actually want to make—and I have already started moving on some of those ideas—that the required personal project can feel like something standing between me and the work beyond graduation.

But it is not separate from that work. It is the final controlled repetition of the process I will need for all of it.

## The Work After Understanding

The Django assessment took me a day and a half. I went into it thinking it should not be too bad. Then came the long stretch of trying to pass tests, finding another problem, fixing it, and asking again: Why won't it pass? What did I do wrong this time?

That part is done.

The personal project has a different shape. I am writing the application and the tests. There is more freedom, but there is also nowhere to hide from the whole process. I have to make the decisions, create the structure, and follow it all the way through.

This is the work that comes after understanding.

Understanding a concept can happen in a lecture. Understanding the architecture can happen while reading a finished project. Building still requires sitting down for the unglamorous hours and making each connection real.

I feel ready to graduate because I can imagine what I want to build. The remaining month is there to turn that feeling into evidence.

## Don't Take the First Larger Number

Today's kata failed when I treated the first acceptable candidate as if it settled the problem. Finding a number larger than the target was only the beginning. I still had to keep moving through the array, comparing what I found, and preserving the best answer.

The finish line has created a similar temptation.

I can see that I know enough to start building my own ideas. That is a valid candidate for readiness. It is not permission to return from the loop early.

There is still a personal project to build. There is still a full-stack process to demonstrate. There is still one month of repetition that can make the difference between recognizing how software is built and being able to build it reliably myself.

So I have to take it day by day.

Do not sprint past the answer. Keep the best candidate in view, inspect what remains, and return only when the loop is actually done.
