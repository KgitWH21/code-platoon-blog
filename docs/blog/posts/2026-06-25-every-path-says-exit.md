---
date: 2026-06-25
categories:
  - Code Platoon
authors:
  - keith
tags:
  - algorithms
  - javascript
  - code-platoon
  - freecodecamp
  - codewars
---

# Every Path Says Exit

Day 18. Week 4. All algorithms, all day. Not a memorable day, but a diagnostic one.

<!-- more -->

---

## The Dictionary Problem

I got called on before lunch. Thirty minutes, in front of the class, Francisco guiding.

It didn't go as well as my last one.

The problem was this — *Find an Employee's Role*:

> You're given a preloaded array of employee objects. Find the one whose `firstName` and `lastName` match the full name string passed in, and return their `role`. If no match, return `"Does not work here!"`

The example in the prompt showed the array like this:

```js
employees = [
  { 'first_name': "Dipper", 'last_name': "Pines", 'role': "Boss" },
  ...
]
```

Snake_case keys. But the actual preloaded array used camelCase — `firstName`, `lastName`. That's a small thing. Under pressure it's a big thing. Francisco had to stop and point it out before I could even start, because I was already writing access logic against the wrong property names.

Here's where I landed:

```js
function findEmployeesRole(name) {
  let result = "";
  for (eName of employees) {
    if (eName.firstName + " " + eName.lastName === name) {
      return eName.role;
    }
  }
  return "Does not work here!";
}
```

The logic is right. Loop the array, concatenate first and last name, compare to the input, return the role. I got there. But I spent the early minutes of the window confused about the data shape — `first_name` vs `firstName`, what was actually in the object, how to pull a property off something inside a loop. The gap wasn't the logic. It was the mechanics of accessing the object that I hadn't made automatic yet.

The thing about CodeWars problems is that some of them are written by people for whom English is clearly not a first language, and reading comprehension under a spotlight is its own skill. By the time I knew what I was solving, I was already in the hole on time.

I finished it, with instructor guidance, inside the window. But I left knowing I had a gap.

---

## Three Problems Stacked Into One

What makes these live reps difficult isn't any single thing. It's that three separate problems collapse into one fog bank:

**Parsing** — what is this prompt actually asking?  
**Representation** — what does the data look like? Array? Object? Array of objects?  
**Execution** — how do I express the logic in JavaScript syntax?

Under calm conditions you can work through those sequentially. Under a timer, in front of people, they all hit at once.

And then there's the added wrinkle of being in a classroom: you're watching someone else do the problem, which means there are potentially three solutions running in parallel — the person up front, the instructor's nudges toward the answer, and whatever you're working through in your own head. When you're lost, you look to the AI. Now the AI's got a fourth route. It's like being lost in a forest where every path has a sign that says EXIT, but they don't all go to the same place. You start walking one way, you hear someone else heading in a different direction, and suddenly you're not sure which path is yours anymore.

The fix isn't more exposure to hard problems. The fix is making object access so automatic it stops being a conscious operation. Then the fog has one less thing to obscure.

---

## The Afternoon Break

By mid-afternoon I needed to be done with algorithms for a minute. I'd been grinding CodeWars since early morning — NeetCode, FreeCodeCamp, the class reps — and I hit a wall. Not a motivational wall exactly, more like the feeling you get after eight count bodybuilders or a long burpee session. Technically good for you. Doesn't feel good. Everything works but you're just going through the motions.

So I vibe-coded a game.

FreeCodeCamp has this RPG character generator exercise I'd spent time on earlier in the week. I took that code and asked myself: what can I turn this into? The answer was a small deck-builder game called [Archive Protocol](https://archiveprotocolgame--kgitwh21.replit.app/) — generic AI name, minimal thought put into it, but the intent was real. I wanted to see if I could take something sterile and make it into something that felt like mine.

I tried to get it live on Replit. This was a mistake, but also instructive.

I hadn't touched Replit in three or four years. In my memory it was a simple sandbox — think Khan Academy for code, a clean place to run stuff in the browser. What I came back to was something else entirely. It's been VC-pumped into an AI platform. Every simple thing I tried to do put me through an AI interface that was clearly more interested in being the experience than in helping me use the tool. I kept trying to do basic things. Kept getting routed through the chatbot. Eventually I basically threatened to never use it again, which worked, except then I couldn't push to GitHub without hitting token authentication issues and I just gave up.

The game exists. It ran. I didn't get it deployed. That's fine.

---

## Not the Main Show

The algorithm grind is necessary. I'm not opting out of it. But I have to keep my bearings about what I'm here for, which isn't to become the best whiteboard coder in the cohort.

I want to build things. I want to understand what I'm building. I want the syntax to stop slowing down the product instincts I already have. Algorithms are the calisthenics that keep me loose — they expose gaps, force reps on patterns I'd otherwise skip, and simulate the kind of time pressure that exists in real interviews. The JavaScript objects gap I found today is useful information. I know what to drill tomorrow.

But it's not the only thing. It's not even the most important thing.

EVO starts tomorrow. Last day of the week. The forest with a hundred exits will still be there on Monday.

---
