---
date: 2026-07-15
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - algorithms
  - interviewing
  - career
  - code-platoon
---

# File Under Folder

By evening, day thirty, a debt settled with whatever keeps score on progress, I opened with the Group Anagrams NeetCode problem outpacing yesterday's warmup, banking the harder win.

<!-- more -->

---

## The Sequel Problem

Yesterday was Is Anagram — two strings, do they use the same letters. Today's Codewars pull was Group Anagrams — a whole array of strings, sort them into buckets where every bucket is a set of anagrams of each other. Same core idea, but now I needed a container that could hold groups, not just compare two things and return true or false. After making an attempt at it, I asked Gemini for a walkthrough before writing anything, and then spent the rest of the session taking the answer apart.

The clean solution uses a hash map where the key is a character-count signature and the value is the list of words that produced it:

```python
from collections import defaultdict
from typing import List

class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagram_map = defaultdict(list)

        for s in strs:
            count = [0] * 26
            for char in s:
                count[ord(char) - ord('a')] += 1
            anagram_map[tuple(count)].append(s)

        return list(anagram_map.values())
```

Every line of that made sense to me faster than it would have a month ago, which was its own small proof of progress. But two pieces still needed real interrogation before I trusted that I understood them instead of just recognized them.

## Tuples Exist Because Lists Can't Sit Still

I kept thinking about `anagram_map[tuple(count)].append(s)`. Why wrap the count array in a tuple at all?

Because Python dictionaries hash their keys to figure out where to store and retrieve things, and that hash has to stay valid for the life of the key. 

Lists are mutable — you can append to them, change an index, whatever — so if Python let you use one as a key, the moment you changed it, the dictionary would lose track of where it put your data. 

It just refuses. `TypeError: unhashable type: 'list'`. A tuple is the frozen version of the same data: same values, permanently locked, safe to hash. 

So `tuple(count)` isn't some clever trick, it's the minimum requirement to use a count array as a label at all. 

You build the count as a mutable list because you need to mutate it while counting, then you freeze a copy of it the instant you're done, purely so the dictionary has something stable to file under.

That distinction — mutable while you're building it, frozen the moment you need to use it as an identity — is one of those things I'd read about in the abstract weeks ago and filed away without it meaning anything. Today it clicked because I had a concrete reason to need it.

## Nested Loops Are Not Two Separate Passes

The part I actually had backwards was the timing. I assumed the code built all the count arrays first and then, in a second pass, filed them into the dictionary. 

That's not what's happening. The inner loop runs to completion — fully counts one word's letters — before the outer loop is even allowed to move to the next word. 

One string gets picked up, counted letter by letter, filed away, and only then does the next string get picked up at all. 

Nothing is batched. It's sequential, one word fully processed before the next word is touched.

Once I had that straight, the rest of the picture — sorting versus counting, why the counting approach is O(n·k) instead of O(n·k log k), why a fixed 26-slot array beats a general dictionary for anything constrained to lowercase letters — all landed easily. 

Those were the same tradeoffs from yesterday's problem, just now serving a bigger structure. The hard part of today wasn't the algorithm, it was making sure I actually understood the plumbing holding it together instead of pattern-matching the shape of the solution and moving on.

---

## The Mentor Call

The other real event of the day happened off the keyboard. I had my first meeting with my assigned resume mentor, a guy at Accenture named Dr. Sharma. 

We talked through my background — the languages, the businesses, the writing, everything before Code Platoon — and he said something that surprised me: he's mentored around twenty students over the years, and mine is one of the most, if not the most, unusual background he's worked with. 

He's coming into town soon, so there's a chance I get to meet him in person next week.

I don't say that to boast. I say it because it's a useful data point against the days where I feel behind on syntax. 

The technical gap is real and closing. The professional-story gap was never a gap to begin with — I've been building that story in public for years, through the books, the businesses, the online presence. 

Dr. Sharma confirmed from the outside what I already suspected from the inside: the part of this program that worries most people the least is the part I already had covered.

## LinkedIn Briefing, No Surprises There Either

We also had a LinkedIn session in the afternoon, and I get why it's in the curriculum even though none of it was new to me. 

It's easy in a bootcamp to collapse the whole goal down to "learn the code, finish the assignment, survive the lecture," and lose sight of the fact that most people get hired on more than clean algorithms. 

Presentation, likability, a legible public story — that's most of the actual filter. One of the students in my cohort used to be a hiring manager at a tech company, and hearing him talk through what he actually screened for during the team standdown was worth more than the formal briefing.

Between React fighting me yesterday and this Group Anagrams problem clicking today, the technical side is visibly moving. 

The mentor call was a reminder that the other half of getting hired — the half about being someone worth hiring — isn't something I'm building from zero. 

That one's already mine. I just need the code to catch up to it.
