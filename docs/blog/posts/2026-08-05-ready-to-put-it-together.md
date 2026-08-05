---
date: 2026-08-05
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - codewars
  - arithmetic
  - django
  - remote-learning
  - collaboration
  - software-engineering
  - code-platoon
---

# Ready to Put It Together

When I stepped into the old schoolroom beyond the red-clay yard where my grandfather had learned his letters, three unfinished Django projects glowed beneath a fly-specked monitor, and the room was off—too quiet for questions, too tired for answers, too practiced for one more lesson.

<!-- more -->

---

## Playing with Digits

Today's Codewars problem gives me two positive integers, `n` and `p`. I have to take the digits of `n`, raise them to consecutive powers beginning with `p`, and add the results.

If that total is an exact multiple of `n`, I return the multiplier. Otherwise, I return `-1`.

For `n = 89` and `p = 1`:

```text
8¹ + 9²
= 8 + 81
= 89
= 89 × 1
```

The answer is `1`.

For `n = 695` and `p = 2`:

```text
6² + 9³ + 5⁴
= 36 + 729 + 625
= 1390
= 695 × 2
```

The answer is `2`.

The power advances once for each digit:

| Digit | Starting power | Expression |
| --- | ---: | ---: |
| `6` | `2` | `6²` |
| `9` | `3` | `9³` |
| `5` | `4` | `5⁴` |

Once I understood that sequence, the implementation felt direct.

## My Whiteboard Solution

```javascript
function digPow(n, p) {
  let nStr = n.toString();
  let nSplit = nStr.split("");
  let total = 0;

  for (let num of nSplit) {
    total += num ** p;
    p += 1;
  }

  if (total % n === 0) {
    return total / n;
  } else {
    return -1;
  }
}
```

Correct.

I solved this one within the time limit and without assistance. After several Codewars problems where I was close but had misunderstood what an iterator, index, or data structure was actually giving me, it felt good to write a solution that passed.

The process has three stages:

```text
695
 ↓ convert to a string and split
["6", "9", "5"]
 ↓ raise to powers 2, 3, and 4, then add
1390
 ↓ test whether 1390 is divisible by 695
2
```

I convert `n` to a string because a number isn't directly iterable digit by digit. Splitting the string gives me an array containing each digit.

```javascript
let nStr = n.toString();
let nSplit = nStr.split("");
```

The values in that array are strings, but JavaScript converts each numeric string to a number when the exponentiation operator is applied:

```javascript
"6" ** 2; // 36
```

Then the loop uses `p` as both the current exponent and a counter. After each calculation, I increase it by one.

```javascript
for (let num of nSplit) {
  total += num ** p;
  p += 1;
}
```

Finally, the remainder operator tells me whether the total is evenly divisible by the original number:

```javascript
total % n === 0
```

If the remainder is zero, `total / n` is the positive integer `k` the problem asks for. If not, no such integer exists.

For `n = 92` and `p = 1`:

```text
9¹ + 2² = 13
13 % 92 = 13
```

The remainder isn't zero, so the function returns `-1`.

## A Small Cleanup

My submitted solution works. If I were polishing it, I could make the conversion explicit and avoid changing the parameter `p` itself:

```javascript
function digPow(n, p) {
  const digits = String(n).split("").map(Number);
  let total = 0;

  for (let i = 0; i < digits.length; i++) {
    total += digits[i] ** (p + i);
  }

  return total % n === 0 ? total / n : -1;
}
```

Here, `i` represents the digit's position, so `p + i` produces the consecutive powers:

```text
i = 0 → p + 0
i = 1 → p + 1
i = 2 → p + 2
```

This isn't more correct than what I submitted. It simply makes each role more explicit: `p` remains the starting power, and `i` supplies the increase.

These math problems are much easier for me. Anything is better than string manipulation. Even though this problem briefly turns the number into a string, the heart of it is arithmetic: follow a sequence, maintain a total, and test divisibility. That structure makes sense to me immediately.

## Ready for the Assessment

Code Platoon Week 10, Day 44 is done.

We're getting toward the end of Django, and today I was over it. I wasn't as engaged as normal—not because the material suddenly became impossible, but because I have practiced the individual pieces enough that I am ready to put them together.

The homework continues a school database project, which is useful because it develops progressively. At the same time, another starter project no longer feels like the thing I need. I have practiced models, serializers, views, URLs, validation, and the rest of the backend sequence. Now I want the assessment to hand me the empty space and ask me to assemble it.

The same is true of my individual project. My idea has been approved. The planning has served its purpose. I am ready to start building.

There is a particular kind of fatigue that arrives near the end of a long learning block. It isn't the exhaustion of being completely lost. It is the impatience of having collected the parts but not yet being allowed to construct the whole thing.

## Running Out of Steam

There is also ordinary fatigue.

I've been getting up at 4:30 every morning and spending the day on Zoom doing technically demanding cognitive work. Getting up early is normal for me. Being on video calls all day while trying to absorb new software concepts is not.

Doing that for days is tiring. Doing it for months straight accumulates.

The course has about a month left, and that makes the finish line visible, but visibility doesn't eliminate fatigue. If anything, seeing the finish can make me more conscious of how long I've already been running.

Today's honest assessment was simple: I am making progress, I feel prepared to move forward, and I am also running out of steam. All three can be true at once.

## Collaboration Needs Room

One welcome change today was working through a problem as a group. Usually, we begin alone and ask for guidance when we need it. This time, the exercise became a shared problem, and we were trusted to combine what we knew.

That felt good because remote communication rarely becomes organic by accident.

In a physical classroom, several small exchanges can happen at once. Someone can ask a quick question while another pair discusses a strategy. Ideas can overlap without the entire room stopping. On Zoom, only one person can really occupy the room at a time. Everyone else has to wait for a turn, which makes even casual collaboration feel formal.

When we work quietly in separate rooms, the silence can last for hours because everyone is at a different point in the problem. Today broke that pattern. The work became a free-flowing exchange in which one person's missing piece could be supplied by someone else's experience.

That is evidence of progress too. Early in a course, a group may need someone else to lead every step. Later, the knowledge is distributed across the room. No one person has to know everything if the group knows how to think together.

## The Difference Between Practice and Proof

Tomorrow is supposed to be a long day, and the Django assessment is waiting this weekend. I'm glad my Codewars problem went well this morning. Solving it unassisted gave me a small, clean proof that the practice is becoming ability.

That is what I want from the assessment and the individual project now.

Starter projects let me rehearse one connection at a time. The assessment will ask whether I can make the connections without the rails. The individual project will ask whether I can decide what should be connected in the first place.

The difference is the same one I saw in today's Codewars problem. Reading the formula is not the solution. The solution appears when I can translate the formula into a sequence of operations, write them in code, and recognize why the result is correct.

I have spent enough time looking at the parts. I'm tired, but I am ready to put them together.
