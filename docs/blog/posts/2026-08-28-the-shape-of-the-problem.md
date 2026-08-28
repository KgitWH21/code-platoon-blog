---
date: 2026-08-28
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - codewars
  - algorithms
  - problem-solving
  - group-projects
  - code-platoon
---

# The Shape of the Problem

Beneath the overpass, I realized the monster I had spent four or five hours fighting two months ago had returned as my savior. Today, I recognized the same machinery inside a hard Codewars problem, pulled up that earlier solution, adapted it, and finished in 45 minutes. The biggest improvement was not typing faster. It was knowing that I had seen the shape of the problem before.

<!-- more -->

---

Today’s Codewars problem was [Human readable duration format](https://www.codewars.com/kata/52742f58faf5485cae000b9a). Given a non-negative integer representing seconds, the function has to return a human-friendly duration using years, days, hours, minutes, and seconds.

For example:

```text
62   → "1 minute and 2 seconds"
3662 → "1 hour, 1 minute and 2 seconds"
0    → "now"
```

The arithmetic is only one part of the challenge. The function also has to handle singular and plural units, omit zero-value components, order every unit from largest to smallest, and punctuate the result like natural English.

## Reduce the Largest Unit First

I stored each unit beside the number of seconds it represents:

```python
time_secs = {
    31536000: ["year", "years"],
    86400: ["day", "days"],
    3600: ["hour", "hours"],
    60: ["minute", "minutes"],
    1: ["second", "seconds"],
}
```

Because dictionaries preserve insertion order in modern Python, the loop starts with years and works toward seconds. At every unit, floor division answers how many complete units fit into the remaining duration:

```python
time_remain = seconds // key
```

For `3662` seconds, the calculation proceeds like this:

```text
3662 // 3600 = 1 hour
62   // 60   = 1 minute
2    // 1    = 2 seconds
```

After extracting a component, I subtract the seconds it consumed:

```python
time_diff = key * time_remain
seconds -= time_diff
```

That leaves a smaller version of the same problem for the next iteration. Once an hour has been removed, I only need to format the remaining 62 seconds. Once the minute has been removed, only two seconds remain.

It is the same repeated operation at five different scales: take as many of the current unit as possible, record the result, and pass the remainder down.

## Build Components Before Building the Sentence

The loop does not try to construct the final sentence all at once. It builds a list of complete components:

```python
result = []

for key, value in time_secs.items():
    if key <= seconds:
        time_remain = seconds // key

        if time_remain > 1:
            result.append(f"{time_remain} {value[1]}")
        else:
            result.append(f"{time_remain} {value[0]}")

        seconds -= key * time_remain
```

For `3662`, that produces:

```python
["1 hour", "1 minute", "2 seconds"]
```

Keeping the components separate makes the grammar manageable. The loop handles quantities and pluralization. The final block handles punctuation:

```python
if not result:
    return "now"
elif len(result) == 1:
    return result[0]
elif len(result) == 2:
    return f"{result[0]} and {result[1]}"
else:
    return f"{', '.join(result[:-1])} and {result[-1]}"
```

There are really four formatting cases:

```text
no components    → now
one component    → 1 minute
two components   → 1 minute and 2 seconds
three or more    → 1 hour, 1 minute and 2 seconds
```

The final case joins everything except the last component with commas, then adds `and` before the last one. Separating the numeric decomposition from the English formatting kept either problem from making the other one harder.

## Recognition Is a Skill

What made this problem memorable was that I had already solved it.

Months ago, a practice assessment asked me to normalize a quantity of seconds. I spent four or five hours barely getting the Python out. Today, while working through the Codewars problem, I thought about normalizing the input into seconds. As soon as I said it, I realized this was essentially the same problem.

So I pulled up my practice-assessment code and adapted it.

That worked out great.

Reusing the earlier solution did not mean blindly copying an answer. I had to recognize that two differently presented prompts shared the same structure. Both required a table of conversion factors, an ordered pass from the largest unit to the smallest, floor division to count complete units, and a remainder that carries the unfinished work forward.

The old solution gave me a framework. I still had to fit today’s rules into it: years instead of only smaller units, singular and plural words, missing zero components, commas between early components, and `and` before the final one.

Two months ago, I was occupied with the syntax and trying to keep the whole algorithm in my head. Today, the pattern itself was available to me. I knew where to look, what was reusable, and what needed to change.

That is the kind of progress that can be easy to miss. I did not suddenly invent a more advanced algorithm. I compressed an earlier struggle into something I could recognize and use.

## Code Platoon — Week 13, Day 63

Week 13 is done. Officially, there are two weeks left in the program, and the group project started today.

The kickoff was rocky. Honestly, there was some strife and drama around the group-project workshop. It was supposed to help us get started, but it moved faster than where the teams actually were. We had only just submitted the personal projects yesterday. Several people were out, and this was the first real opportunity most of us had to meet with our teams, define the product, and decide who was going to do what.

Instead, we kept moving through planned activities before we had enough time to finish the conversations those activities were supposed to support. The workshop was steamrolling the actual project kickoff.

The tension built throughout the morning. We were told that we were not participating, asking enough questions, or paying attention. From the presenter’s perspective, that probably looked true: she had prepared an all-day workshop, and the room was not responding the way she expected. From our perspective, we were trying to catch up and do the work underneath the workshop. We did not have questions yet because we barely had a project.

Eventually, both teams were sent into breakout rooms with another assignment. Neither team completed it in the prescribed way. It was not because nobody was working. We were using the time to figure out what our projects were about and what each person needed to do. It turned out that we had essentially done the activity, just not inside the structure the presenter wanted.

That was the straw that broke the camel’s back. The workshop ended early. If somebody can storm out of a Zoom call, that is basically what happened.

Afterward, the class had a heated discussion about it. The initial reaction from the program was that we had been rude and ignored a guest presenter. Based only on what was visible from the outside, I understand how it looked that way. But once people began explaining what had happened from our side, almost everybody had some version of the same feedback: we had not been given enough time to prepare, the expectations were too high for day one, and the tone had caused people to shut down.

That unanimity was unusual. We normally participate with guest speakers. We ask questions. We are respectful. This was not a class that had collectively decided not to care. It was a class that felt the workshop was keeping us from the work we urgently needed to do.

The best part of the conflict was what happened once that became clear. The instructor who had initially confronted us listened to the entire class, backed off the original judgment, and told us to put the experience into the formal feedback process so the program could adjust. That took professionalism, humility, and flexibility. I gave him a shout-out during the weekly retro because getting angry is easy. Revising your position after hearing the whole class explain what you could not see is harder.

By retro, nobody wanted to keep dragging the conflict through the rest of the day. We had already talked about it ad nauseam. But something important had happened: for the first time, the class really united as a cohort. Almost everybody spoke, and the message was remarkably consistent. We were not rejecting the group project or refusing to work. We were asking for enough room to begin it properly.

A process can be well intended and still be wrong for the current state of a project. A facilitator can interpret silence as disengagement when it is actually overload. A class can look uncooperative while its members are working hard on a different layer of the same problem. The only way through that mismatch is for somebody to stop defending the original plan long enough to inspect what is actually happening.

That flexibility is going to matter during these final two weeks. Group projects add communication, ownership, coordination, and shared decisions to the technical work. We are not beginning with a finished architecture or even a fully formed idea. We are beginning by figuring out the shape of the project together.

That makes today’s Codewars lesson feel especially well timed. Progress does not always come from starting over. Sometimes it comes from recognizing a familiar structure, reaching back to work that cost me hours the first time, and adapting it to the problem in front of me.

Four or five hours became 45 minutes. The old struggle was not wasted time. It became pattern recognition.
