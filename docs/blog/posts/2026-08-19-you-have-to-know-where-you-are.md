---
date: 2026-08-19
categories:
  - Code Platoon
authors:
  - keith
tags:
  - linked-lists
  - recursion
  - algorithms
  - python
  - codewars
  - personal-project
  - artificial-intelligence
  - code-platoon
---

# You Have to Know Where You Are

To make a visible mistake publicly, in front of other people, is to undergo a small human reentry: the error is observable, correction necessary, and the only reliable treatment is repetition, repetition.

<!-- more -->

---

Code Platoon Week 12, Day 3—Day 52—is done.

Today was the first day this week that I got a good amount of Code Platoon work done. The work moved between linked lists, recursion, and an audit of my personal project, but all three kept returning me to the same question:

> Where am I in the problem right now?

That question sounds almost insultingly simple. In code, it is everything.

## Getting the Nth Node

The first Codewars problem asked me to implement `get_nth()`. Given the head of a linked list and an index, the function has to return the node stored at that position.

```text
42 → 13 → 666 → None
      ↑
    index 1
```

The numbering follows the C convention, so the head is index `0`, the next node is index `1`, and so on. If the index is outside the list, the function has to raise an exception.

My solution walked through the list while keeping a separate counter:

```python
def get_nth(node, index):
    my_index = 0
    current = node

    while current is not None:
        if my_index == index:
            return current

        current = current.next
        my_index += 1

    raise IndexError("")
```

There are two movements happening together:

```text
current:   node 0 → node 1 → node 2 → None
my_index:       0 →      1 →      2
```

When the counter matches the requested index, return the current node. If `current` reaches `None` first, the index was too large.

Conceptually, it is simple. But as soon as I need to translate that concept into code, the possible solution space expands in my head. What should the loop condition be? When should the counter advance? Do I compare before moving or after? What exactly should be returned?

I generally dislike algorithm problems for that reason, but I know it is nice to know how to do them. Were I not in a coding bootcamp, I would spend more time drilling these. That is why I volunteered to do the problem this afternoon.

## Inserting the Nth Node

The afternoon problem made the bookkeeping more difficult. This time I had to insert a new node at any valid index within a linked list.

```text
(1 → 2 → 3 → None, index 1, data 7)

1 → 7 → 2 → 3 → None
```

Insertion at index `0` is its own case because it changes the head:

```python
if index == 0:
    new_node.next = head
    return new_node
```

For every other position, I do not actually want to stop on the requested index. I need to stop one node before it. That is the node whose `next` reference must change.

```python
class Node(object):
    def __init__(self, data):
        self.data = data
        self.next = None


def insert_nth(head, index, data):
    my_index = 0
    new_node = Node(data)
    current = head

    if index == 0:
        new_node.next = head
        return new_node

    while current is not None and my_index < index - 1:
        current = current.next
        my_index += 1

    if current is None:
        raise ValueError

    new_node.next = current.next
    current.next = new_node

    return head
```

The order of those final two assignments matters:

```text
Before: current ─────────→ next node

Step 1: new node ───────→ next node
Step 2: current ────────→ new node

After:  current → new node → next node
```

If I overwrite `current.next` before saving its destination in `new_node.next`, I can lose the remainder of the list. The operation is small, but it depends on having an exact picture of where every reference points before and after each line.

I struggled again. Between the given class and the task, I got lost. I had a difficult time visualizing where I was in the problem, but I was ultimately able to get it over time.

I had even practiced a few Codewars problems to prepare for my turn. The possibility of problems is just too wide. I will have to keep improving.

## The Recursion from Hell

One of those practice problems was my first 4 kyu problem: **Nesting Structure Comparison**.

The values in two lists do not need to match. Their shapes do.

```python
[1, [1, 1]]
[2, [2, 2]]
```

Those have the same structure. These do not:

```python
[1, [1, 1]]
[[2, 2], 2]
```

At every position, both items must either be lists or both be individual values. Whenever they are lists, descend one level and repeat the check.

```python
def same_structure_as(original, other):
    if not isinstance(original, list) or not isinstance(other, list):
        return False

    if len(original) != len(other):
        return False

    for original_item, other_item in zip(original, other):
        original_is_list = isinstance(original_item, list)
        other_is_list = isinstance(other_item, list)

        if original_is_list != other_is_list:
            return False

        if original_is_list:
            if not same_structure_as(original_item, other_item):
                return False

    return True
```

This was a real son of a bitch from hell involving recursion and optical illusion. I lasted 15 minutes before I tapped out and had to look up the solution.

The recursive call is the part that makes the code feel slippery. The function is moving through a list, discovers another list, and then temporarily begins the entire problem again at a deeper level. Once that nested comparison finishes, it returns to the earlier level and continues.

```text
compare outer lists
├── compare ordinary values
└── find two nested lists
    ├── compare their lengths
    ├── compare their first items
    └── compare their second items
return to outer comparison
```

It is another problem of location. I need to know which call I am in, which pair I am comparing, and what must be true before the function can return to the level above it.

## Following the Data

The most productive part of the day was returning to my personal project after clarifying what its audit should involve. I began tracing the major flow of data through the codebase.

Just an hour and a half of this was enlightening.

```text
user action
    ↓
frontend state
    ↓
request
    ↓
backend route
    ↓
database
    ↓
response
    ↓
rendered result
```

Claude AI is the perfect task master for this kind of work. It can see the entire codebase, quiz me, and fill gaps in my understanding. Instead of giving me another feature, it can point to a function and ask where the data came from, where it goes next, and why the code changes it along the way.

That is the application-scale version of today's linked-list exercises. Start at the head. Follow the reference. Keep track of the current position. Do not pretend I understand the system merely because I recognize all the individual boxes.

I'm looking forward to picking this up again tomorrow.

## Final Reflection

Today was the first day this week that I got a good amount of Code Platoon work done. Following my clarification with Instructor Kevin about the personal project audit, I returned to my codebase and began to understand the major flow of data through the code. Just an hour and half of this was enlightening. Claude AI is the perfect task master. Able to see the entire codebase and quiz me, while filling in gaps in my understanding. I'm looking forward to picking this up again tomorrow. I practice three Codewars problems before my afternoon whiteboard session. One was my first Level 4 problem. A real son of a bitch from hell that involved recursion and optical illusion. I lasted 15 minutes before I tapped out and had to look up the solution. 
