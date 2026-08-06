---
date: 2026-08-06
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - codewars
  - arrays
  - bitwise-operators
  - reduce
  - django
  - artificial-intelligence
  - creative-work
  - software-engineering
  - code-platoon
---

# An Archive That Can Answer Back

First I fed the machine three scraps from yesterday, then imagined my family’s yellowing letters and my own abandoned stories rising from the red-clay bardo of ruined schoolhouses and crumbling porches, until I understood that I wasn't building an archive at all, but making an immortal and condemning it to remember my sins, my blood, and my eternity.

<!-- more -->

---

## Find the Stray Number

Today's Codewars problem gives me an odd-length array of integers. Every value in the array is the same except one, and I have to return that single different number.

```javascript
[1, 1, 2]       // 2
[17, 17, 3, 17, 17] // 3
```

The input is guaranteed to be valid and contain at least three numbers.

My first attempt was:

```javascript
function stray(numbers) {
  const firstNum = numbers[0];

  for (let num of numbers) {
    if (num !== firstNum[num]) {
      return num;
    }
  }
}
```

I felt close because the basic instinct was right: choose a reference value, walk through the array, and return the value that doesn't match. Two details prevented that logic from working.

## A Number Is Not an Array

This expression was the first problem:

```javascript
firstNum[num]
```

`firstNum` is a single integer, not the original array. If the first value is `4`, the expression effectively becomes something like:

```javascript
4[4]
```

Bracket notation is used to access a property or an array element. It doesn't compare two numbers. Here it evaluates to `undefined`, so nearly any `num` will appear different from it.

The direct comparison I intended was:

```javascript
num !== firstNum
```

That fixes the syntax, but not the entire algorithm.

## The First Number Might Be the Stray

Consider this array:

```javascript
[7, 2, 2]
```

If I choose `7` as the reference value, the next value, `2`, does not match it. My function would return `2`, even though `7` is the stray.

The first two values alone don't always settle the question. The first three do. Because there is only one stray, at least two of the first three numbers must be the repeated value.

That leads to a readable comparison-based solution:

```javascript
function stray(numbers) {
  const repeated =
    numbers[0] === numbers[1] ? numbers[0] : numbers[2];

  return numbers.find((num) => num !== repeated);
}
```

If the first two values match, they establish the repeated number. If they do not match, the third value must match one of them and therefore must be the repeated number. Once I know that value, `find()` returns the first number that differs from it.

## Finding the Number That Appears Once

Another solution expresses the problem almost exactly as it is written: the stray number is the only value whose first position is also its last position.

```javascript
function stray(numbers) {
  for (const num of numbers) {
    if (numbers.indexOf(num) === numbers.lastIndexOf(num)) {
      return num;
    }
  }
}
```

For the repeated value, `indexOf()` and `lastIndexOf()` point to different locations. For the stray value, both methods point to its one location.

This is easy to read, but it repeatedly searches the array from both ends. In the worst case, that can approach `O(n²)` work. The array in this kata may be small enough that it doesn't matter, but the problem's guarantees allow a more efficient solution.

## XOR Returns

Two days ago, I used bitwise XOR to solve a related problem in which every number appeared twice except one. Today's problem is slightly different: one value appears once, while the other appears an even number of times.

The same operator still works.

```javascript
function stray(numbers) {
  return numbers.reduce((a, b) => a ^ b);
}
```

This one line combines two ideas: `reduce()` and XOR.

`reduce()` walks through an array while carrying one accumulated result forward. For an array such as `[5, 2, 5]`, this callback creates the equivalent of:

```text
5 ^ 2 ^ 5
```

Here, `a` is the accumulated result and `b` is the current number.

XOR has three useful properties:

```text
x ^ x = 0
x ^ 0 = x
order does not matter
```

A number XORed with itself becomes zero. A number XORed with zero remains itself. Since the order does not affect the final result, identical values can be treated as pairs even when they are separated in the array.

For `[5, 5, 5, 5, 2]`, the reduction is:

```text
5 ^ 5 ^ 5 ^ 5 ^ 2
= (5 ^ 5) ^ (5 ^ 5) ^ 2
= 0 ^ 0 ^ 2
= 2
```

The array is guaranteed to have odd length. After removing the one stray value, an even number of identical values remains. They cancel in pairs, leaving only the number without a partner.

The solution visits every element once, giving it `O(n)` time complexity, and it keeps only one accumulated value, giving it `O(1)` extra space.

It is concise because it is built precisely for the guarantees in the prompt. It would not be the right solution for arbitrary frequencies, decimals, or integers outside the behavior JavaScript's 32-bit bitwise operations can represent safely. Inside this kata, though, it turns the problem's structure directly into the algorithm.

This was the one-liner I'd been looking for.

## Week 10 Is Done

Code Platoon Week 10, Day 45 is over.

Most of this week has felt like waiting for the Django assessment. Today had the same anticipation, but the lectures were better because the instructor had a premade project we could follow. I kept my own dummy project open beside it and reproduced the steps there.

That gave me a useful go-by without making the lesson passive.

```text
Instructor's project → shows the pattern
My dummy project     → proves I can reproduce it
```

The day moved quickly. More importantly, the assessment that occupied so much mental space all week looks smaller now that I can see its actual boundaries.

There appears to be no Dockerization. The focus is the Django backend work we have practiced for the past two weeks: models, URLs, views, migrations, and the connections among them. I haven't studied every test yet, so I don't want to declare victory before beginning, but the scope feels reasonable.

Earlier assessments tested concepts while I was still learning the shape of the system. This one seems to ask me to assemble pieces I have already used repeatedly.

That changes the question from *Can I understand this?* to *Can I sit down and execute it?*

I know I can.

## Mapping a Creative Life

During gaps in the lecture, I also worked on something I have wanted for years: a creative map of my work.

I have made lists before. A list can tell me what I wrote, when I published it, or where a file lives. I have experimented with tools such as Obsidian, where links and tags can create a network among notes. But those systems still require me to recognize most of the relationships in advance.

I have to decide that two things belong together, give them the same tag, and remember why I did it.

The creative map is meant to go further. I want it to accept everything—published work, fiction, abandoned ideas, journal entries, research notes, fragments—and use AI to identify meaningful connections among them.

```text
Journal entry ───────┐
Published essay ─────┼─→ shared themes, origins, images, and influences
Abandoned story ─────┤
Loose note ──────────┘
```

A connection might be a repeated image. It might be the first appearance of an argument I developed years later. It might be an experience that quietly informed several works I never thought to place beside one another.

Tags record the relationships I already know. The map might discover relationships after the archive exists.

That is the part I have never had access to: not merely a long-term archive of my creative work, but an archive that can answer back.

## The First Three Entries

I only added three items today. It took time to dial in what I wanted, and all three pieces came from yesterday, so their connections are still obvious and fresh.

The real test will come when I begin moving backward.

What happens when material from 2026 sits beside work from 2020, 2012, or earlier? Where did a recurring idea first appear? Which themes disappeared and returned? What abandoned project contains the missing piece of something I am writing now? Which works belong together even though I never gave them the same label?

The value of the map will increase with distance. Recent memories don't need much help finding one another. Old work does.

There is also something satisfying about building this now. Ten weeks ago, a database-backed application that ingests creative material, stores it, visualizes it, and uses AI to analyze connections would have sounded like a large technical abstraction. Today I worked on it between parts of a Django lecture.

That does not mean the project is finished or simple. It means the pieces of software development have become tools I can pick up and arrange around a problem I have actually wanted to solve.

## What Cancels and What Remains

Today's Codewars solution works by erasing repetition. Matching values cancel until the one different number becomes visible.

The creative map does the opposite. It preserves the repetitions—the returning image, the recurring question, the old idea inside the new work—because those repetitions may reveal what matters.

One algorithm finds meaning by canceling the connections. The other finds meaning by keeping them.

Week 10 ends between those two ideas. The repeated Django practice has reduced the assessment from an unknown threat to a familiar set of operations. At the same time, the skills that practice created are beginning to connect with projects beyond the course.

Next is the assessment. Then the personal project. Then the final stretch of Code Platoon.

But today, incidentally, I began building something meant to hold a much longer stretch of time.
