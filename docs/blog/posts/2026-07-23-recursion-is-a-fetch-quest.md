---
date: 2026-07-23
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - recursion
  - codewars
  - react
  - github-pages
  - code-platoon
---

# Recursion Is a Fetch Quest

Reluctantly, I return to a memory I no longer entirely trust—the blue light of my monitor, recursion becoming a *Mega Man* charge shot and an RPG fetch quest, and me returning to it reluctantly—discovering that new ideas sometimes make sense only when they borrow the shape of old ones.

<!-- more -->

---

## My First One-Liner

Today's Codewars problem was called Shortest Word. Given a string of words, return the length of the shortest one. The string will never be empty, so there isn't much defensive work to do.

My solution was one line:

```python
def find_short(s):
    return len(min(s.split(), key=len))
```

This is the first Codewars one-liner I have written that felt natural instead of compressed for the sake of being clever.

The operations form a clean pipeline. `s.split()` turns the sentence into a list of words. `min(..., key=len)` searches that list, but instead of comparing the words alphabetically, it compares the value returned by `len` for each word. That gives me the shortest word. The outer `len()` returns its length.

```python
"bitcoin take over the world maybe who knows perhaps".split()
```

becomes:

```python
["bitcoin", "take", "over", "the", "world", "maybe", "who", "knows", "perhaps"]
```

Then `min(..., key=len)` selects `"the"` or `"who"`. It doesn't matter which tied word it returns because both have the same length. The final answer is `3`.

One-liners can become unreadable quickly, but this one works because each function does one recognizable job. Split the input. Select the minimum according to length. Return that length.

That felt like a small milestone. In week one, even the basic Codewars problems could be a struggle. Now I can see a problem and occasionally recognize that Python already contains almost the entire solution.

## Hold the Charge Button

Recursion did not feel nearly as natural.

The exercise was simple to describe:

1. Write a function that takes an integer and prints every integer from zero up to it.
2. Write another function that starts at the integer and counts down to zero.

I found the base case for the first function, but I still didn't understand why the rest of the solution worked:

```python
def count_up(n):
    if n < 0:
        return

    count_up(n - 1)
    print(n)
```

If I call `count_up(2)`, the function doesn't print `2` immediately. It calls `count_up(1)` first. That function doesn't print `1` immediately either. It calls `count_up(0)`, which calls `count_up(-1)`.

At `-1`, the function reaches the base case and returns.

The important idea is that every earlier function call has been paused. `count_up(2)` is still waiting to execute `print(2)`. `count_up(1)` is waiting to execute `print(1)`. `count_up(0)` is waiting to execute `print(0)`. Once the base case stops the descent, those paused calls can finish in reverse order:

```text
count_up(-1) returns
count_up(0) prints 0
count_up(1) prints 1
count_up(2) prints 2
```

I understand the words *call stack* and *unwinding*, but the image that made the process click was the charge shot in *Mega Man X*.

Every recursive call is another moment spent holding down the charge button. The shot doesn't fire yet because the function still has unfinished work waiting after the recursive call. The base case is the point where the charge is complete. Then I release the button, the stack unwinds, and all those delayed `print()` statements fire in order.

That is `count_up`: charge on the way down and release on the way back up.

Counting down only requires swapping the final two lines:

```python
def count_down(n):
    if n < 0:
        return

    print(n)
    count_down(n - 1)
```

This version prints before it makes the recursive call. There is no charged blast waiting at the bottom. It fires one little pellet—what I sometimes called an egg in the original *Mega Man*—then moves forward and fires another.

`count_up` is the charge shot. `count_down` is rapid fire.

That is going to stick in my head much longer than a formal definition.

## Every Recursive Return Is a Fetch Quest

Printing explains when work happens, but recursion can also pass values back through the waiting calls. The analogy that made that part work was an RPG fetch quest.

Factorial is the usual example:

```python
def get_boss_key(level):
    if level == 1:
        return 1

    return level * get_boss_key(level - 1)
```

Calling `get_boss_key(3)` is like asking the guard on level three for the key to the boss door. The guard can't give it to me until I bring back the item from level two. The person on level two needs something from level one. I keep descending through the dungeon until I reach the chest at the bottom.

The chest is the base case. It simply returns `1`.

Now the fetch quest resolves in reverse. Level two receives the `1`, multiplies it by `2`, and returns `2`. Level three receives that `2`, multiplies it by `3`, and returns `6`. The original caller finally gets the finished result, and the boss door opens.

In ordinary notation:

```text
3! = 3 × 2 × 1 = 6
```

In stack terms:

```text
get_boss_key(3)
└── 3 * get_boss_key(2)
    └── 2 * get_boss_key(1)
        └── return 1
    └── return 2 * 1
└── return 3 * 2
```

The recursive calls travel down to find something that can be answered directly. The return values carry that answer back up, allowing each paused call to finish its part of the work.

Recursion is a fetch quest. Perfect.

## The Game-Over Screen

Every game mechanic also needs a failure state.

The game-over screen of recursion is a stack overflow—or, in Python, usually:

```text
RecursionError: maximum recursion depth exceeded
```

If the dungeon has no chest, every NPC sends me down one more level. Level one points to level zero. Level zero points to level negative one. The quest never reaches an answer that can travel back up.

```python
def endless_quest(level):
    return level * endless_quest(level - 1)
```

Every call adds another paused function to the stack, and every paused function consumes memory. Python eventually stops the program before the descent can continue indefinitely.

That gives me two useful debugging questions:

1. Does the function have a base case?
2. Does every recursive step actually move toward it?

A base case that can never be reached is not really a base case. The game still has no bottom floor.

## Another App in the World

I also got my [Japanese Sentence Trainer](https://kgitwh21.github.io/japanese-sentence-trainer-app/) running on GitHub Pages today.

Deployment is one of those steps that can make a finished application feel unfinished. The code works locally, the interface works in the browser, and then hosting introduces a new collection of path and routing problems. A single-page application expects client-side routing to decide what to render, while a static host naturally looks for a matching file at the requested path.

Getting the app onto GitHub Pages meant working through that boundary and making the build behave in its production environment. It is a small deployment compared with some of the projects I want to make, but it matters that the application now has a real URL. It has crossed the line from something on my machine to something another person can use.

That feels especially appropriate at the end of this week. Eight weeks ago, the morning problems were difficult. Now I am solving some of them in one line, building React applications, and deploying them.

## The Fastest Feedback Loop I Have Seen

Today's retrospective was mostly people thanking the instructors, and they deserve it.

I know what teaching looks like from the other side. Students see the lesson, the assignment, and the feedback. They don't always see the preparation, meetings, evaluations, administrative demands, and the work of keeping a room full of people moving through difficult material at roughly the same pace.

The instructors said they enjoy watching us go from struggling with basic Codewars problems in week one to building complete applications now. That transformation has happened remarkably fast. The only training I can compare it to is OSI training, where I also arrived knowing almost nothing and had to leave professionally ready. That ran from January to May and combined two courses, but the pace felt similar: there was never enough time to become comfortable before it was time to become capable.

One thing I appreciate about Code Platoon is how quickly the course can respond to feedback. I mentioned that the morning and afternoon coding problems sometimes feel rushed. In the morning, we may have twenty or thirty minutes before the lecture begins. In the afternoon, the exercise happens just before release. If I don't solve it in the allotted window, there often isn't another obvious block of time to return to it.

The instructors didn't defend the format or explain why it could never change. They said they could try something different.

That kind of feedback loop is unusual. At many schools, the structure arrives from somewhere above the classroom and everyone is expected to live with it. Here, we can mention a problem in a retrospective and potentially see a different approach on Monday morning. The instructors have both the willingness and the freedom to adjust.

Software development is supposed to value short feedback loops. It is nice to be in a program willing to apply the same idea to itself.

## Over the Halfway Mark

Week eight, day thirty-six, is done. We are officially over the halfway mark.

Our React assessment is essentially a version of the Pokémon project we have been building in class. I didn't finish every part of that class project because we had been working on it for almost two weeks, I was getting bored with it, and I still had separate homework to complete. Fortunately, the assessment covers familiar territory, and we have an unexpected three-day weekend to work on it.

I am not especially worried, but I am also not going to underestimate it. There are a lot of components to connect. The assessment is more visual than the previous terminal-based store project and closer to building a complete application, which makes it feel more familiar to me. It also means there are more ways for individually reasonable pieces to fail when they meet.

I will probably start tonight because tomorrow I want to run errands, go to the gym, and get out of the house. I have spent an extraordinary amount of this summer in one seat.

Still, I can feel the progress. The pace is blistering, the schedule is crowded, and recursion briefly made me feel like I had fallen through the bottom of the level. Then I found the right mental model.

Hold the charge button. Find the chest at the bottom of the dungeon. Bring the answer back up. And always make sure the game has a base case.
