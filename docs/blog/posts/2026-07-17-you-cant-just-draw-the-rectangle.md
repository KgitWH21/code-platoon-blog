---
date: 2026-07-17
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - algorithms
  - css
  - react
  - code-platoon
---

# You Can't Just Draw the Rectangle

At my desk on the final afternoon of week seven, CSS returned like a forgotten acquaintance: Code Platoon was officially halfway over, the weekend portfolio would become a review project, and Tailwind waited for me in a blank rectangle on the screen.

<!-- more -->

---

## CSS Is Visual Composition by Indirection

Today was a slog. We did a little bit of stacks and queues in the morning, then spent most of the day watching a project get transformed with Tailwind and Bootstrap. The instruction was set up well enough: take an existing thing, walk through the code, and show what changes when you bring the libraries in. I just didn't find it especially interesting.

Part of the problem with Tailwind is that it's visual, but the experience of learning it doesn't always feel visual. It can look like an enormous catalog of tiny rules:

```jsx
<h1 className="text-4xl font-bold tracking-tight mb-6">
  Quote Calculator
</h1>
```

Nobody should try to memorize all of that. Eventually you internalize the common pieces—spacing, flexbox, grid, typography, breakpoints—and look up the rest. But knowing the class names still isn't the same thing as knowing what the page needs. The difficult part of CSS isn't remembering how to make something red. It's looking at a page and realizing that the hierarchy is weak, the spacing is inconsistent, the line length is too wide, or two elements that belong together don't look like they belong together.

That requires an eye for design, and some programmers simply don't care about design. If your brain is drawn to logic, math, and puzzle solving, CSS can feel alien because you're writing code to produce a visual change. You're not telling the computer to calculate something or check a condition. You're describing space, proportion, alignment, and relationships.

You can't just draw the rectangle where you want it. You have to define the rectangle's dimensions, its position inside a container, its relationship to the things around it, and what it should do when the screen gets smaller. You're drawing indirectly, through a constraint system.

I remember having this exact problem when I first learned CSS years ago. My brain could see the thing I wanted, but the only way to put it on the screen was to translate that image into math and rules. That felt completely backwards.

I had to learn to like design. Originally, I knew nothing about typography or color and didn't particularly care. Then I started building a business online and realized the web is visual. I'm a writer by nature, but writing alone is a difficult way to stand out on the internet. Around 2022, I decided I needed to learn graphics. That led to drawing, video, websites, banners, book materials, and everything else that slowly forces you to develop some visual literacy whether you planned to or not.

So Tailwind isn't really asking me to learn design from nothing. It's a new interface for instincts I've already had to build. Remembering that makes the wall of class names feel a little less important.

## Don't Loop When the Pattern Already Has a Formula

The Codewars problem today was much more naturally in my lane: given a number, return the sum of every natural number below it that's divisible by three or five. Return zero for a nonpositive input, and don't count numbers like fifteen twice.

The obvious solution is a loop. Walk through every number below the target, test divisibility, and add the matches to an accumulator. That works, but this solution doesn't walk through the numbers at all:

```python
def solution(number):
    if number <= 0:
        return 0

    def sum_multiples_of(divisor):
        count = (number - 1) // divisor
        return divisor * count * (count + 1) // 2

    return (
        sum_multiples_of(3)
        + sum_multiples_of(5)
        - sum_multiples_of(15)
    )
```

The first condition handles the edge case directly. If `number` is zero or negative, there aren't any positive natural numbers below it to count, so the answer is zero.

The helper function is where the loop disappears. First, it determines how many multiples of a divisor exist below the target:

```python
count = (number - 1) // divisor
```

The subtraction matters because the problem says *below* the number. For multiples of three below ten, `(10 - 1) // 3` gives us three: 3, 6, and 9. If the input itself were divisible by three, subtracting one also prevents us from accidentally including it.

Once we know there are three multiples, we don't need to generate them. Every multiple of three can be written as three times another natural number:

```text
3 + 6 + 9
= 3(1 + 2 + 3)
```

The sum of the first `n` natural numbers is `n(n + 1) / 2`, so the helper multiplies that sum by the divisor:

```python
divisor * count * (count + 1) // 2
```

For the example, that's `3 * 3 * 4 // 2`, or 18. The integer division is safe because one of two consecutive numbers—`count` or `count + 1`—will always be even.

The last line uses inclusion-exclusion. We add the multiples of three and the multiples of five, but that counts every multiple of fifteen twice. Fifteen is the least common multiple of three and five, so subtracting the multiples of fifteen removes exactly the duplicate copy:

```text
multiples of 3 + multiples of 5 - multiples of 15
```

For ten, the sum of the multiples of three is 18, the sum of the multiples of five is 5, and there are no multiples of fifteen below ten. The answer is 23.

What I like about this solution is that it's constant time. A loop does more work as the input gets larger. This calculation performs the same handful of operations whether the input is ten or ten million. It sees the structure of the sequence and calculates the result instead of reenacting every step.

There is an odd connection to CSS here. Both ask you to stop thinking only about the finished objects—the individual multiples, the visible rectangle—and describe the system that produces them. The formula is much cleaner than the CSS, but the mental move is related: find the underlying rule.

## Finish the Whole Thing

We were given a bonus portfolio project for the weekend. I already have a website. I already have a blog. I don't need another portfolio in any practical sense, but I think I may build it anyway because it would be a useful review project.

The most reliable way I've found to make any of this stick is to build complete things. An isolated exercise can teach one piece of syntax, but it doesn't make you connect anything. A complete React project makes you decide where state belongs, how data moves between components, how the form behaves, how the files should be organized, how the styling interacts with the structure, and what happens when several individually correct pieces don't work together.

I've seen that with all the widgets and games on my website. Each one might be small, but it still has to be conceived, built, debugged, styled, and released. Completion is its own repetition.

It's the same with writing. You can practice dialogue, scenes, description, and characterization separately, and all of that has value. But finishing a whole book teaches structure, pacing, endurance, revision, and closure in a way that a pile of beginnings never will. You don't learn as much from ten unfinished projects as you learn from bringing one complete thing across the line. A whole thing, even done poorly, is better than another fragment.

That's probably the right reason to do the portfolio. Not because I need the artifact, but because I need the full repetition. React components, props, state, maybe an API call, Tailwind for the layout, responsive behavior—the connective tissue, all in one place.

React itself feels fine after this week. The underlying development isn't radically different from vanilla JavaScript; it's the conventions that take adjustment. Declaring everything where React expects it, wiring components together, understanding where state lives, and expressing familiar logic in React's particular grammar. That gets easier through complete builds, not by staring at one more isolated `useState` exercise.

## Halfway

Week seven is done. Thirty-two instructional days. We are officially halfway through the program, which is crazy.

I'm feeling it. I told someone today that my time hasn't been this strictly occupied since the Air Force Academy. Back then, every minute counted. You had to pay attention not only to the work but to how you spent your breaks, because your entire day was already spoken for.

Code Platoon has that feeling now. I barely have time for the things I want to do, let alone everything I need to do. Every personal project competes with class preparation, errands, family time, and sleep. Even the weekend portfolio—which is supposedly a bonus—has to justify the hours it will take from something else.

Still, the trajectory is obvious. A week ago React was new. Now the problem isn't understanding its fundamental purpose; it's getting fluent in the way it wants things declared and connected. CSS is still strange, but it's strange in a way I've encountered before. I don't need to memorize every Tailwind class. I need enough fluency to build what I can see, enough experience to recognize what isn't working, and enough complete projects for the patterns to settle in.

That seems like a fair conclusion for the halfway point: I don't have to hold the whole vocabulary in my head. I have to keep finishing whole things.
