---
date: 2026-08-13
categories:
  - Code Platoon
authors:
  - keith
tags:
  - entrepreneurship
  - career-development
  - algorithms
  - python
  - whiteboarding
  - code-platoon
---

# The Missing Letter

After the mock interview, I laid the glowing feedback beside my unfinished algorithm while the immortal at my kitchen table regarded his patient eternity and explained that every life eventually reveals its missing letter; then something began turning the knob.

<!-- more -->

---

Today we had another interview exercise, but this one felt better immediately.

The people from S&P Global were down-to-earth, the exercise happened in small groups, and we took turns playing interviewer and interviewee for different software-related positions. When it came to my turn, though, I said the obvious thing:

I'm not trying to get any of these jobs. I already have a business. I'm trying to do my own thing.

Instead of forcing that answer back into a conventional software interview, we changed the scenario. The interviewer became a potential client—a small business or creative business owner—and I explained what I do through HAC Studios.

Then I just told the truth.

I talked about building a business, managing clients, deploying software, managing long-term projects, and writing and publishing books. I pulled out physical copies of my books and showed him that I publish real print books, not just files uploaded to Amazon KDP. I explained that my experience as an author shapes the way I help other authors.

I didn't try to contort myself into what I thought he expected to hear. I didn't try to impress him by pretending my limited time in tech was the only experience that counted.

And it worked.

The written feedback said my personal journey made the business model “relatable and persuasive,” that connecting my experience as an author to the way I help authors gave the pitch “authenticity and purpose,” and that I came across as “a strong candidate with a clear sense of purpose and a compelling entrepreneurial story.” The only limitation was time. He wanted to hear more about the business model, traction, and next steps.

Home run.

For the first time in one of these career exercises, I felt like, *Yes, this is what I can do.* When I'm talking to an actual person and telling my story straight, the pieces connect. The author, publisher, business owner, project manager, and software developer do not have to compete for space. They explain one another.

## I Knew What Was Missing

Then came the whiteboard, and that didn't go nearly as well.

The problem was to find the missing letter in an array of consecutive letters:

```text
['a', 'b', 'c', 'd', 'f'] → 'e'
['O', 'Q', 'R', 'S']      → 'P'
```

I saw the logic almost immediately. Convert each letter to its ASCII number, move through the array, and look for the gap.

```text
a  b  d  e
97 98 100 101
      ↑
   99 is missing
```

The concept took maybe five minutes. Transferring it into code was the problem.

Whenever a loop involves numbers as elements, I have trouble visualizing its progress. I start mixing up the numbers in the array with their indices. I know what needs to happen, but I lose track of what the loop is holding, what it is comparing, and what advances next.

The solution is small:

```python
def find_missing_letter(chars):
    last = chars[0]

    for char in chars[1:]:
        if ord(char) - ord(last) > 1:
            return chr(ord(last) + 1)

        last = char
```

The useful move here is that the loop does not need an index at all. `last` holds the previous letter. `char` holds the current one. `ord()` turns both into numbers long enough to measure the distance between them.

```text
last  char  difference
a     b     1
b     d     2  → return c
```

That table is the part I couldn't keep visible in my head at the whiteboard. The algorithm is not really searching for an empty position. It is comparing neighbors. As soon as the numerical distance between two neighboring letters is greater than one, the missing letter is one step after the previous one.

I didn't solve it in front of the class, but I also don't dread these problems the way I did at the beginning of Code Platoon. Algorithmic problem solving feels like language learning. Over time, you build a bank of set phrases and moves you can pull out when the situation looks familiar.

Today I had some of the moves. I knew to use `ord()`. I knew to look for the break in the sequence. I just didn't have the right framework for expressing it within the time limit.

That's fine.

## The Skill I Actually Want

Some people can look at these problems and know what to do on the fly. I'm sure I could get much better if I drilled them hard enough. If I ever decide to pursue a traditional software role, that is exactly what I'll do.

But cold whiteboard problems aren't the main skill I came here to develop.

I would rather focus on building and shipping products. I know how to take a project from beginning to end because I've done it many times. That is where my skill is, and software gives me a much larger set of things I can now put together and finish.

Being mediocre at timed algorithms does not bother me as much as it once did. I can improve when the problem demands it. What matters more for the path I'm on is that I can define a useful product, manage the work, solve the real problems inside it, and deploy the result.

That path may also become a little less lonely for whoever comes next.

After yesterday's conversation about the lack of an entrepreneur interest group at Code Platoon, I spoke with career services today. I have been invited to a future meeting to discuss what an alumni track or interest group for entrepreneurial-minded graduates might look like.

I don't know what it would become yet. Maybe it is simply a group of people who can answer the questions that do not fit the standard job-search track:

```text
How are you using code in your business?
What are you building?
How do you plan to make money?
What did you wish you knew when you graduated?
```

Right now, that group is the missing letter. The standard sequence moves from curriculum to interview preparation to employment, while those of us trying to apply the same skills to our own businesses have to notice the gap and find one another.

Today I may not have closed the loop on the whiteboard, but I did recognize the opening.

That is usually where building something starts.
