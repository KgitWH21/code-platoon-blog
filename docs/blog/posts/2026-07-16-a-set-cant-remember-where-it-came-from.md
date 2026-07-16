---
date: 2026-07-16
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - algorithms
  - debugging
  - code-platoon
---

# A Set Can't Remember Where It Came From

Nevertheless, day thirty-one didn't ambush me — it opened like a door left ajar by someone who'd fled in a hurry, and I stepped inside the way you cross into a room where someone has died, searching and hunting down the one thing I needed to find: the set.

<!-- more -->

---

## Going Slow On Purpose

I'm not trying to rush these exercises anymore. Really, at this point in the program you can't rush — there's just too much to do with connecting the pieces together and actually understanding what you built, not just getting to a green checkmark. 

That's not the name of the game for me. Getting code that runs isn't the same as getting code I could explain to someone else, and I've noticed that when I slow down and insist on the second thing, it sticks. It internalizes. I can feel the difference between a solution I copied and a solution I own.

## Two Sum, and the Set That Forgot Its Own Address

Today's NeetCode pull was Two Sum — find the two indices in an array whose values add up to a target, return the smaller index first. I got the core logic right on the first pass:

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        seen = set()
        for i, num in enumerate(nums):
            comp = target - num
            if comp in seen:
                return [comp, num]
            seen.add(num)
        return None
```

That felt right walking out of it — O(n), one pass, no nested loop. But it's wrong, and the way it's wrong taught me something about the tool I'd reached for.

A `set` is good for exactly one question: *have I seen this value before?* That's it. It doesn't remember anything else about the value — not when it showed up, not where. So when I found the match, all I had left to return was the two raw numbers, `comp` and `num`, when the problem never asked for the numbers at all. It asked for their positions in the array. I had built a structure that actively threw away the one piece of information the problem needed.

The fix was swapping `set()` for a dictionary — same O(1) lookup, but now the value I store isn't just a "yes this exists," it's "yes this exists, and here's exactly where":

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        seen = {}
        for i, num in enumerate(nums):
            comp = target - num
            if comp in seen:
                return [seen[comp], i]
            seen[num] = i
        return None
```

The part that actually landed for me — not just as a syntax fix but as a rule — is that because I'm walking the array left to right, anything already sitting in `seen` necessarily has a smaller index than whatever I'm looking at right now. I don't have to sort the output or compare indices after the fact. The order I built the dictionary in *is* the order the answer needs to come out in. That's a nice thing to notice, because it means the "return smaller index first" requirement wasn't a separate step bolted onto the algorithm — it was a free consequence of how I was already iterating.

## Annotating for Future Me, Not for the Instructors

I had a conversation with my best friend days ago, a career software developer who's been doing this close to twenty years, out of UT Austin. I was fishing for advice on getting better at debugging, and he said something that felt almost embarrassingly obvious once he said it: annotate your code as you write it.

I write every single day. And somehow it hadn't occurred to me to bring that habit into my code, because I'm so locked into getting the syntax right and the architecture right — especially on the bigger projects — that I forget to leave myself a line saying *here's what I was trying to do at this exact moment.* That note isn't for whoever grades the assignment. It's for me, twenty minutes from now, when I've lost the thread and need to find my way back into my own head.

I also watched a chunk of the MIT intro-to-computing-with-Python course today and picked up a debugging technique that pairs well with the annotation idea: the bisection method. 

Drop a print statement roughly halfway through the program, see if reality still matches what you expected at that checkpoint, and cut the search space in half again from there. 

It's built for single-page scripts, but there's no reason it doesn't generalize — you just apply it one section at a time on something bigger. Today's Two Sum bug is small enough that I didn't need to bisect anything, but the underlying move was the same: I had a comment in my head — *this returns the two matching values* — and the discrepancy between what I expected and what the problem actually wanted was exactly where the bug was hiding.

## Jeopardy, Out of Nowhere

We did a Jeopardy-style review this afternoon, and apparently I came out of nowhere with it — I'd spent the first half of the session heads-down on my project, not really tracking the board. A few of the questions needed real explaining once the answer came up, and my read on that is simple: if you have to explain it, it wasn't written well. There's a difference between a question that tests whether you know something and a question that tests whether you can guess what the question-writer meant. I liked the round where we actually had to fire off a POST request from our terminals to submit — that's the input layer made real instead of abstract, and it's the kind of thing that sharpens a review instead of just decorating it.

Pretty low-key day overall. Standard. Tomorrow's Friday, and I'm looking forward to the end of the week.
