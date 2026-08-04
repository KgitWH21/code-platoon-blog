---
date: 2026-08-04
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - codewars
  - sets
  - bitwise-operators
  - django
  - docker
  - artificial-intelligence
  - software-engineering
  - code-platoon
---

# Code Is the DNA of the World

Reluctantly, I wrote down the room as it felt today; reluctantly, I wrote down the room as it disappeared—the last classroom before everything changed.

<!-- more -->

---

## Find the Only Unique Number

Today's Codewars problem gives me an unsorted array in which every number appears exactly twice except one. My job is to return the number without a pair.

The array may contain millions of values, which means finding the right answer isn't enough. The solution also has to avoid doing expensive work over and over.

My first attempt was:

```javascript
function findUnique(numbers) {
  let seen = new Set();

  for (let num in numbers) {
    if (num !in seen) {
      seen.add(num);
      numbers.splice(numbers.indexOf(num));
    }
  }
}
```

I felt like I was on the right track, and I was. A `Set` is a useful way to remember which values I've encountered. The problem was that nearly every line after creating the Set behaved differently from what I intended.

## The Index Is Not the Number

The first issue was `for...in`.

```javascript
for (let num in numbers)
```

For an array, `for...in` iterates over property names. In this case, those property names are the indexes, represented as strings.

```javascript
const numbers = [12, 7, 12];

for (const num in numbers) {
  console.log(num);
}

// "0"
// "1"
// "2"
```

I named the variable `num`, but the loop was not giving me the numbers in the array. It was giving me their positions. This is the same category of mistake I made yesterday with the capital-letter problem: a variable name can describe what I hope a value is without changing what the language actually returns.

To iterate over the values, I need `for...of`:

```javascript
for (const num of numbers) {
  console.log(num);
}

// 12
// 7
// 12
```

Then there was the Set check:

```javascript
if (num !in seen)
```

JavaScript doesn't have a `!in` operator for this. A Set provides its own method for asking whether it contains a value:

```javascript
if (!seen.has(num))
```

The `in` operator is for checking whether a property exists on an object. `has()` checks whether a value exists in a Set. Those sound similar in English, but they answer different JavaScript questions.

## Don't Cut Apart the Array You're Walking Through

The largest problem was this line:

```javascript
numbers.splice(numbers.indexOf(num));
```

There are several things happening here.

`indexOf()` starts at the beginning of the array and searches until it finds the value. Doing that inside a loop means repeatedly rescanning an array that may contain millions of items.

`splice()` then changes the same array I'm currently iterating over. Removing elements during iteration can cause later elements to shift positions and get skipped. Without a second argument, `splice(start)` also removes everything from `start` through the end of the array, not just one item.

Even if I changed it to `splice(index, 1)`, it would still be a poor fit. Each deletion may require JavaScript to shift all the elements after it. Repeated searches and repeated shifts turn a potentially linear solution into roughly quadratic work.

```text
One pass through n values:       O(n)
Search or shift n values n times: O(n²)
```

That difference is easy to ignore with ten numbers and impossible to ignore with five million.

There was also no `return`, so the function would ultimately return `undefined` even if the other operations had found the correct value.

## Let the Pairs Remove Themselves

Turns out I can keep my original Set idea without modifying the input array. The first time I encounter a value, I add it. The second time, I delete it.

```javascript
function findUnique(numbers) {
  const seen = new Set();

  for (const num of numbers) {
    if (seen.has(num)) {
      seen.delete(num);
    } else {
      seen.add(num);
    }
  }

  return seen.values().next().value;
}
```

For this input:

```javascript
[4, 9, 4, 3, 9]
```

the Set changes like this:

| Number | Operation | Set |
| --- | --- | --- |
| `4` | add | `{4}` |
| `9` | add | `{4, 9}` |
| `4` | delete | `{9}` |
| `3` | add | `{9, 3}` |
| `9` | delete | `{3}` |

Every paired value removes itself. At the end, the Set contains only `3`.

`seen.values()` creates an iterator over the values in the Set. Calling `.next()` returns an object describing the first step of that iterator, and `.value` extracts the number:

```javascript
seen.values().next().value
```

This solution makes one pass through the array, so its time complexity is `O(n)`. It does require extra memory for the Set, so its space complexity is `O(n)` in the worst case.

## XOR: Cancellation as an Algorithm

The problem's exact rules allow an even leaner solution. Every number appears exactly twice except one, so I can use the bitwise exclusive OR operator, `^`.

XOR has two properties that matter here:

```text
x ^ x = 0
0 ^ x = x
```

A number XORed with itself becomes zero. Zero XORed with a number becomes that number. The order doesn't matter, so matching values cancel even when they are scattered across the array.

```javascript
function findUnique(numbers) {
  let result = 0;

  for (const num of numbers) {
    result ^= num;
  }

  return result;
}
```

Conceptually, the calculation for `[4, 9, 4, 3, 9]` can be rearranged into pairs:

```text
4 ^ 9 ^ 4 ^ 3 ^ 9
= (4 ^ 4) ^ (9 ^ 9) ^ 3
= 0 ^ 0 ^ 3
= 3
```

This is still `O(n)` time because every value must be visited once, but it uses `O(1)` extra space because `result` is the only accumulator.

The Set solution is more immediately readable and applies to a wider range of frequency problems. The XOR solution is better optimized for this problem because the problem guarantees pairs and valid integers within JavaScript's signed 32-bit range. It isn't magic. It is a specialized use of the information the prompt gave me.

## The Backend Is Becoming One Machine

Code Platoon Week 10, Day 43 is done.

Today's lesson was easier to follow because I had my own assignment open alongside it. That gave each new idea somewhere concrete to go. I'm still working on putting the entire Django system together in my head, but the pieces are no longer isolated mysteries.

```text
Model → Serializer → View/ViewSet → URL → API response
                           ↓
                    Docker runs it
```

Models define the data. Serializers translate and validate it. Views or ViewSets decide what operations are available. URLs expose those operations. Docker gives the application a consistent environment in which to run.

The guides are excellent, and at this point my challenge is less about discovering that the pieces exist and more about remembering the sequence in which they connect.

I'm not too concerned about this weekend's Django assessment. I expect it to emphasize the backend: creating models, serializers and validators, exposing the API, and getting the project running in Docker. I practiced more Docker this afternoon, and I feel reasonably prepared. The task will be to assemble familiar components correctly and debug the seams between them.

The individual project is a different test.

## The Final Individual Boss

We're already approaching the end of the course, which means the largest projects are coming. My individual project proposal is due tomorrow, and the project itself will require me to build both the frontend and backend on my own.

It feels like the final individual boss.

Unlike the weekend assessments, we'll have class time to work on it. Unlike other assignments, though, the implementation is supposed to demonstrate what I can build without AI writing the code like it did during the agentic coding block. I'll have to decide what the application is, define its data, build the backend, build the interface, and make both sides communicate.

The proposal matters because scope is already part of the engineering. A smaller application with a clear data model and a complete central workflow will demonstrate more than an enormous idea that never becomes functional. I need something personal enough that I understand the problem, but contained enough that I can finish the solution.

That constraint doesn't contradict my belief in AI-assisted development. It clarifies it. There are times to use a powerful tool, and there are times to prove that I understand the work the tool would accelerate.

## From Reading the Evidence to Becoming It

The unexpected event today was hearing from Code Platoon Media.

Someone contacted me last week to ask about my experience in the program. Answering those questions was fun, but it also made me realize that I am almost exactly two months into Code Platoon.

Three or four months ago, I was the prospective student doing research. I was reading other people's accounts and trying to determine what the experience would be like. I was the one asking whether I should do this at all.

Now my response may become part of the evidence someone else uses to make the same decision.

That's a strange threshold to cross. I am still inside the experience, still learning and struggling and trying to finish assignments, but I have also traveled far enough to turn around and describe the road.

The most important question was why anyone should attend a coding boot camp in 2026 when AI can already write code.

My answer is that understanding code gives me agency.

## Code Is the DNA of the World

We live in a digital world. Nearly everything on a phone or computer is governed by software, but the idea extends beyond screens. Cars, houses, books, machines, businesses, and manufactured objects begin as specifications: measurements, formulas, relationships, tolerances, rules, and plans.

In that sense, code is the DNA of the world.

It describes what exists, how the parts relate, and what should happen when conditions change. The physical object may eventually be made from steel, paper, glass, concrete, or plastic, but before it exists, someone has to describe it precisely enough to build.

Learning to code is learning to think in those descriptions. What would you do with the power to manipulate matter?

AI can help write the syntax, but understanding the system gives me the flexibility to decide what I want, recognize whether the output matches that intention, revise it when it doesn't, and troubleshoot it when it breaks. Without that understanding, AI can become a slot machine: enter a prompt, pull the lever, and hope the result works. With understanding, it becomes a force multiplier.

Today's Codewars problem is a tiny example. AI could hand me the XOR solution immediately. If I don't understand `for...in`, Sets, mutation, time complexity, or why duplicate bits cancel, then I possess an answer but not much additional ability. Once I understand those things, I can recognize the pattern again and decide when that answer does or does not apply.

The same distinction is waiting for me in the individual project. The point isn't to reject AI forever. The point is to become capable of directing it later.

## A Novelist Can Learn This

My cohort contains people from all kinds of backgrounds: law enforcement, medicine, education, computer science, and many paths in between. My own background is as a novelist. Technology has been part of my work, but software engineering hasn't been the main focus of my life.

Still, I'm here—two months into the program, building APIs, running applications in Docker, learning Django, and debugging JavaScript.

Coding does require an appetite for puzzles, numbers, abstraction, and systems that sometimes feel cryptic. It also requires imagination. A program has to exist in the mind before it can exist on the screen, just as a story does.

Not everyone needs to become a professional software engineer. But anyone who wants to understand these systems can learn more than they currently know. Background isn't destiny. The evidence is all around my cohort, and at this point, I am part of that evidence too.

Today, paired numbers canceled one another until only the unique value remained. Beneath the frameworks, tools, and AI, the unique value of learning code is the same: the ability to understand a system well enough to create within it.
