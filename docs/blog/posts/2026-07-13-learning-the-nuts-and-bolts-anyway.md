---
date: 2026-07-13
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - dom
  - python
  - algorithms
  - ai
  - code-platoon
---

# Learning the Nuts and Bolts Anyway

Day 28. Week seven. It was a slog, mostly because it was review for me — HTML, CSS, JavaScript, in that order, in one morning. And it's the day the existential question I've been dodging finally caught up with me: what is any of this for, if a model can one-shot the whole thing anyway?

<!-- more -->

---

## HTML, CSS, and JavaScript, Before Lunch

Francisco took a back seat today and Kevin ran the lecture instead. Kevin moves fast — talks fast, geeks out on the code, clearly the kind of person who actually loves this stuff. Lecture ran over by half an hour, which meant we had about twenty minutes left to actually do anything with our hands. I said as much on the feedback form: HTML, CSS, and JavaScript, covered in a single morning, is a lot. I've been through the entire freeCodeCamp HTML curriculum something like one and a half times and it still takes months to actually sit with. The basics — structure, standard elements, naming — are easy. What's not easy is everything past the basics, and there's a lot past the basics.

I get why it's compressed. It's a boot camp, we're moving fast on purpose. But CSS in particular gets shortchanged because most people here don't care that much about design, and JavaScript gets the spotlight because it's the one of the three that actually does things. HTML and CSS alone just sit there. Fair enough — but I could see giving CSS its own day, and JavaScript definitely deserves one, because what we hit today wasn't the JavaScript we've spent six weeks doing. We've been doing problem-solving JavaScript — functions, loops, algorithms, the stuff that lives entirely in the terminal. Today was DOM JavaScript, and for a lot of people in the room, today was the first time they'd seen it at all.

Since it was mostly review for me, I skipped the recommended exercises and just read freeCodeCamp's DOM theory lessons instead. They keep the theory light on purpose, but light theory about the DOM is still dense — a lot of reading, a lot of actually sitting with it. The payoff was better than I expected, though: I kept stopping mid-lesson to go back and look at widgets I'd already built on my own website, and for the first time I could actually explain what they were doing instead of just knowing that they worked. That's a real shift — from copying something that functions to reading something and understanding why it functions. I ran out of time before I finished the section, three lessons short, even with fifteen minutes to spare at the end of class, because I chose to keep reading instead of starting the exercises. Next time I pick this back up, I'll actually get hands-on with it. Today was just building the map.

---

## Finding the Number That Shows Up an Odd Number of Times

Somewhere in the middle of the DOM reading I got pulled into an unrelated little problem, the kind that's satisfying precisely because it has nothing to do with the DOM: given an array of integers where every value appears an even number of times except one, find the one that doesn't.

The elegant answer is XOR. Loop through the array, XOR every value into a running result, and the pairs cancel each other out to zero while the odd one out survives:

```python
def find_it(seq):
    result = 0
    for num in seq:
        result ^= num
    return result
```

That's the one worth remembering — constant space, one pass, no side dictionary. But the more instructive version, at least for where I am right now, is the plain frequency dictionary, because it's the version I'd have actually written a few weeks ago without needing anyone to explain XOR to me first:

```python
def find_it(seq):
    frequency_dict = {}

    for num in seq:
        frequency_dict[num] = frequency_dict.get(num, 0) + 1

    for num, count in frequency_dict.items():
        if count % 2 != 0:
            return num
```

Build the dictionary first, counting as you go with `.get(num, 0)` so you don't need an `if/else` to handle the first time you see a value. Then walk the dictionary once looking for the count that isn't even. Same O(n) time as the XOR version, worse space, but it reads like exactly what it's doing, and there's something to be said for a solution that doesn't require a trick to understand. The XOR version is the one you reach for once you already trust the problem. The dictionary version is how you build that trust in the first place.

---

## What Is Any of This For

Then I saw that ChatGPT 5.6 one-shotted a full custom website for my novel, and it was good. Not a rough draft, not a scaffold I'd need to clean up — good, finished, the kind of thing that would've taken me a weekend to build by hand a year ago.

And I sat there, halfway through a bootcamp day spent learning what a `<div>` actually does and how the DOM tree gets built, having a genuine existential moment about it. What is this all for? I'm spending an entire summer learning the nuts and bolts of a skill a model can now do in an instant, with a better eye for design than I have on my first pass.

I don't have a clean answer to that one yet, and I don't think pretending I do would be honest. But I noticed something in the noticing itself: the reason I could even evaluate whether that one-shotted site was good is that I've spent six weeks learning what "good" means at the level of the code underneath it. Somebody who can't read HTML looks at that site and sees a nice website. I look at it and can start to guess at what it's actually doing, the same way today's DOM reading let me finally read my own widgets instead of just trusting they worked. That's not nothing. Whether it's enough to justify the summer is a question I'm going to keep sitting with, not one I'm going to resolve by lunchtime on day 28.

---

## Tired, and Not Alone in It

The whole day had a slightly wrung-out quality to it, and not just because of the crunched lecture. Everybody was tired — me included — coming off the end-of-week assessment from Monday and the weekend before it. That's its own thing worth writing about properly, and I want to get into it next. For today: review day, DOM theory, an odd-number-of-occurrences problem that turned into a small meditation on trust, and a bigger question about what a decade-plus career move into code actually buys me in a year where the machine can build the thing itself. Onward to week seven.
