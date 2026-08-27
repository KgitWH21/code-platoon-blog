---
date: 2026-08-27
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - codewars
  - debugging
  - product-demo
  - presentations
  - code-platoon
---

# Attention Is Part of the Work

Never mistake attention for a harmless guide: it may illuminate the work, expose its weakness, or pass judgment that transforms its fate. A working application does not guarantee an audience will understand it, just as mostly correct logic does not guarantee a function will run. Today put both lessons side by side: one variable name broke my Codewars solution, and one deliberately produced presentation made the work behind my project impossible to miss.

<!-- more -->

---

## Surviving the Airstrike

Today's Codewars problem was [Alphabet war - airstrike - letters massacre](https://www.codewars.com/kata/5938f5b606c3033f4700015a). Two groups of letters carry different power values, and asterisks represent bombs. Every bomb eliminates itself and the letters immediately beside it. The surviving letters determine which side wins.

The left side has positive power:

```text
w = 4
p = 3
b = 2
s = 1
```

The right side has negative power:

```text
m = -4
q = -3
d = -2
z = -1
```

Using one positive-or-negative score means I do not need separate totals. A positive result belongs to the left, a negative result belongs to the right, and zero means they fight again.

My first instinct was to build a new string without the bombed characters:

```javascript
let noStars = "";
```

But the problem does not actually require a cleaned battlefield. It only requires a final score. I can inspect each character and add its power only when that position survived:

```javascript
function alphabetWar(fight) {
  const power = {
    w: 4,
    p: 3,
    b: 2,
    s: 1,
    m: -4,
    q: -3,
    d: -2,
    z: -1,
  };

  let finalScore = 0;

  for (let i = 0; i < fight.length; i++) {
    const bombed =
      fight[i] === "*" ||
      fight[i - 1] === "*" ||
      fight[i + 1] === "*";

    if (!bombed) {
      finalScore += power[fight[i]] ?? 0;
    }
  }

  if (finalScore > 0) {
    return "Left side wins!";
  }

  if (finalScore < 0) {
    return "Right side wins!";
  }

  return "Let's fight again!";
}
```

The `bombed` condition handles the entire airstrike rule. If the current character is a bomb, or either neighbor is a bomb, that position contributes nothing.

JavaScript also makes the boundary checks pleasantly simple. At the beginning of the string, `fight[i - 1]` is `undefined`. At the end, `fight[i + 1]` is `undefined`. Neither value equals `"*"`, so I do not need special cases for the first and last characters.

The nullish coalescing operator finishes the scoring logic:

```javascript
power[fight[i]] ?? 0
```

Letters without power are victims, but they do not affect the score. Looking one up returns `undefined`, which becomes zero. Unlike `||`, `??` expresses the exact idea: use zero only when the lookup has no value.

## Correct Logic, Wrong Name

The frustrating part was that my bomb logic and score calculation were correct. The final version of my attempt calculated the result in `finalScore`, but the conditions checked a different variable:

```javascript
let finalScore = 0;

// later...
if (score > 0) {
```

`score` did not exist. The fix was only to use `finalScore` in both comparisons.

That kind of bug is humbling because it is not an algorithmic misunderstanding. I can reason correctly about the battlefield, translate the rule into a clean condition, and still lose to an identifier. The program does not award partial credit for what I meant.

It is also a useful debugging reminder. When the main idea appears sound, I should stop redesigning the algorithm and trace the actual data: where the accumulator is declared, where it changes, and which name the return conditions read. Sometimes the smallest mismatch is the entire problem.

## Code Platoon — Week 13, Day 62

Today was presentation day for the individual projects, and I was happy with how mine landed.

As soon as the HAC Studios splash screen appeared, the production value was obvious. I opened with a dramatic line, moved into a clear explanation of the problem, showed the application, and covered the technical requirements without burying the audience in architecture. The slides, delivery, screen recording, cloned narration, and custom background music all worked together as one presentation.

The reaction reminded me that the tools I use regularly arent normal to everybody. Voice cloning and AI-assisted production have become familiar parts of my creative process, but familiarity can hide how remarkable those capabilities still are. Explaining how I cloned my voice became one of the most interesting technical conversations after the presentation.

I built the demo with the product announcements Ive seen from companies like OpenAI and Anthropic in mind. Their strongest announcements tend to be clear and succinct. They show the feature, explain what it can do for the user, and give just enough information to make the benefit concrete. There is minimal techno mumbo jumbo. The architecture is not the story unless the architecture changes what the audience can do.

My presentation had to be more technical because this was a course requirement, but I still wanted the technology to serve the message. React, Django, authentication, and APIs mattered. So did the reason I assembled them into this particular product.

## Work for the Attention

Everything I do, I really try to give a shit.

That doesnt mean every assignment needs a splash screen, cloned voice, original score, and polished edit. It means I take the act of communication seriously. If I have built something worth showing, I havent finished the work merely because it runs on my machine. I still have to make the value visible.

Years of writing books, creating for social media, teaching math, and working in spaces where people arent automatically eager to hear the message taught me the same lesson: I am not guaranteed attention just because I have something important to say. I have to work for it.

The presentation wasnt decoration placed on top of the project. It was the interface between the project and its audience. A confusing interface can hide good software. A clear one lets people understand what they are seeing and why it matters.

That makes today's two lessons closer than they first appear. In the Codewars function, `finalScore` had to travel under the same name from declaration to decision. In the presentation, the project's purpose had to travel intact from my head to the audience. Both demanded care at the final boundary.

The individual presentations are done. Planning for the group project has begun. We are officially entering the final phase of Code Platoon, and Im entering it with a finished project I would be happy to show to somebody who might actually use it.

The code has to work and the presentation has to communicate. Attention is part of the work.

## Hayden's Dictionary App - Dakota Platoon - Personal Project Presentation

<div class="video-embed">
  <iframe
    src="https://www.youtube-nocookie.com/embed/LY8ry7CXmsc"
    title="Final project presentation"
    loading="lazy"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    referrerpolicy="strict-origin-when-cross-origin"
    allowfullscreen>
  </iframe>
</div>

[Watch the presentation on YouTube](https://youtu.be/LY8ry7CXmsc).
