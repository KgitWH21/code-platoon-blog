---
date: 2026-09-01
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - codewars
  - algorithms
  - two-pointers
  - problem-solving
  - group-projects
  - code-platoon
---

# Pressure Is Not the Pattern

The algorithm, lo and sankofa and behold, remembered every consonant position I thought my unfinished code had forgotten. 

<!-- more -->

---

Today’s Codewars problem was Reverse Vowels In A String. The function has to reverse only the vowels while every consonant, space, and punctuation mark remains in its original position.

For example:

```text
"Hello!" => "Holle!"
"Tomatoes" => "Temotaos"
"Reverse Vowels In A String" => "RivArsI Vewols en e Streng"
```

My attempt had the right general direction:

```python
def reverse_vowels(s):
    vowels = ["a", "e", "i", "o", "u", "A", "E", "I", "O", "U"]
    left = 0
    right = len(s) - 1

    result = []

    while left < right:
        if char in vowels:
            result.append(s[left])
```

I was getting close. The two pointers were exactly the right technique. The solution is to convert the string into a list, move each pointer until it finds a vowel, and swap the vowels once both pointers are in position.

```python
def reverse_vowels(s):
    vowels = set("aeiouAEIOU")
    result = list(s)

    left = 0
    right = len(result) - 1

    while left < right:
        # Move left until it finds a vowel
        while left < right and result[left] not in vowels:
            left += 1

        # Move right until it finds a vowel
        while left < right and result[right] not in vowels:
            right -= 1

        # Swap the two vowels
        result[left], result[right] = result[right], result[left]

        left += 1
        right -= 1

    return "".join(result)
```

## Skip, Find, Swap

The important pattern is:

```text
skip consonants → find two vowels → swap → move inward
```

Both pointers begin at opposite ends of the character list. The left pointer moves right while it is looking at consonants or other non-vowels:

```python
while left < right and result[left] not in vowels:
    left += 1
```

The right pointer does the same thing in the opposite direction:

```python
while left < right and result[right] not in vowels:
    right -= 1
```

Once those loops stop, either the pointers have met or each pointer is sitting on a vowel. The tuple assignment swaps those two characters without disturbing anything between them:

```python
result[left], result[right] = result[right], result[left]
```

Then both pointers move inward to search for the next pair.

With `"Hello!"`, the first search produces this:

```text
H e l l o !
  ↑     ↑
 left  right
```

The left pointer skips `H` and stops at `e`. The right pointer skips `!` and stops at `o`. Swapping them produces:

```text
H o l l e !
```

There are no more pairs of vowels, so the loop ends and `"".join(result)` converts the character list back into a string.

## What My Attempt Still Needed

There were three problems to resolve. First, `char` was never defined:

```python
if char in vowels:
```

I needed to test a specific character, such as `s[left]`, or in the completed version, `result[left]`.

Second, appending only `s[left]` to an empty list tries to rebuild the output one character at a time. That makes it easy to lose the consonants, spaces, and punctuation that are supposed to stay exactly where they are. Starting with `result = list(s)` preserves every character from the beginning.

Third, Python strings are immutable. The algorithm needs to exchange characters in place, so the string has to become a mutable list before the swaps and then become a string again at the end.

I used a set for the vowels because the operation is membership testing: is this character one of the ten vowel characters? A set expresses that job directly and provides average `O(1)` membership checks.

The complete algorithm runs in `O(n)` time because the pointers only cross the string once. It uses `O(n)` extra space because the string is copied into a list.

## Code Platoon — Week 14, Day 65

Today progress on the group project continued smoothly. What was a real challenge of patience were the algorithms we keep getting to solve. We had four today. 

And while I understand they want to prepare us as much as possible for job interview readiness, the way they administer them is largely ineffective at helping us learn what we're doing. 

When every contact with algorithms feels like a high stakes evaluation, it's difficult to internalize the material. 

I don't think the answer is to not do them (though I doubt many would complain if they went away!), but it's to adjust how we encounter them throughout the cohort. I contacted the instructor about this. 

In my message, I told suggested that informing us of the topic that would be expected would allow us more time to actually explore the algorithm, find alternate solutions, discuss memory efficiency etc. This is preferable to watching someone struggle because they haven't memorized a two-pointer or how to set up a React app. I wrote the following note after lunch: 

## Thoughts on Code Platoon Algos for future cohorts

While I was in Code Platoon every problem we received was the purpose of interview prep. From Day 1, the goal is to simulate solving a previously unknown algorithm, while being evaluated by a potential employer and the clock is ticking. It’s maximum pressure training that’s meant to normalize the inevitable nerves that strike anyone under such extreme circumstances.

I must say it was effective. After three and a half months of this bi-daily semi-humiliation ritual, I did get used to doing problems under those conditions. But in completing 100s of algorithms, one thing I didn’t do was learn the techniques needed to succeed in an interview. At least I didn’t internalize any of the concepts well enough to be able to perform them without external assistance, when a job might be on the line.

This is one of my biggest disappointments with the program. I enjoy puzzles. And I was really looking forward to discovering how to solve logic problems with code. But because every algorithm was a simulated interview, my opportunities to really learn this felt extremely limited.

There are several ways to remedy this. First, the curriculum should gradually introduce students to the process of successfully navigating these algorithms. Initial contact should begin without time pressure, while introducing students to basic problem solving syntax, common algorithmic techniques, and strategies for dealing with uncertainty in the process.

Solving these problems has four phases:

Understanding the problem: what am I given? What assumptions can I make? What do I need to return?

Reasoning through the solution (no code): how would I do this step by step as a human? How can I break up my steps into the smallest component a computer could use?

Algorithmic translation: are there any code techniques that I can use? How should I think about this problem?

Producing syntax: what exact code do i need to write to implement the solution

Explaining how the solution works and discussing tradeoffs:

Students should be exposed to these phases one at a time at the beginning of a Code Platoon cohort. Simultaneously, they should be given clear solutions while they’re developing their skills. While at least two days a week are dedicated to exposing them to interview-like scenarios. The goal is to gradually introduce students to effective problem solving methods, before throwing them in the deep end to flounder.

I have much more to say on this topic that I didn't record here. All will be delivered in the post Code Platoon Devlog following graduation. 10 days left and we out.
