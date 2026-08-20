---
date: 2026-08-20
categories:
  - Code Platoon
authors:
  - keith
tags:
  - linked-lists
  - data-structures
  - algorithms
  - python
  - codewars
  - personal-project
  - artificial-intelligence
  - code-platoon
---

# Don't Lose the Rest of the List

 In five hours, the world I had been tracing—one node linked carefully to the next—ended abruptly. Today, that small rule followed me from a Codewars problem into a much larger question about AI-built software and where trained developers might fit into its future.

<!-- more -->

---

Code Platoon Week 12, Day 4—Day 53—is over.

Linked lists are still challenging. Their individual parts are simple: a node holds some data and a reference to the next node. The difficulty begins when I have to move through the chain or change it without losing track of where everything points.

## Length and Count

The first Codewars problem asked for two functions. `length()` counts every node in a linked list, while `count()` counts only the nodes containing a particular value.

```text
length(None)                         → 0
length(1 → 2 → 3 → None)            → 3
count(1 → 2 → 3 → None, 1)          → 1
count(1 → 1 → 2 → 2 → 2 → None, 2) → 3
```

My solutions use the same fundamental operation: start at the head, inspect the current node, and follow `next` until the current node is `None`.

```python
from preloaded import Node


def length(node: Node | None) -> int:
    node_count = 0
    current = node

    while current is not None:
        node_count += 1
        current = current.next

    return node_count


def count(node: Node | None, data) -> int:
    data_count = 0

    while node is not None:
        if data == node.data:
            data_count += 1

        node = node.next

    return data_count
```

The first function increments its counter for every node. The second increments only when `node.data` matches the target. In both cases, reaching `None` means I have reached the end of the list.

That was the manageable part of the day. Reading a chain is one thing. Cutting into one is another.

## Sorted Insert

The afternoon problem asked me to insert a new node into a linked list that was already sorted in ascending order.

```text
sorted_insert(1 → 2 → 3 → None, 4)
1 → 2 → 3 → 4 → None

sorted_insert(1 → 7 → 8 → None, 5)
1 → 5 → 7 → 8 → None

sorted_insert(3 → 5 → 9 → None, 7)
3 → 5 → 7 → 9 → None
```

My first attempt showed where my mental model was breaking down:

```python
def sorted_insert(head, data):
    my_index = 0
    new_node = Node(data)

    if head is None or data < head.data:
        head = new_node
        return

    current = head.data
    while current.data >= data or current.data == []:
        current = current.next
        current.next = new_node

    return current
```

The most immediate problem is that `current = head.data` stores the value inside the head rather than the node itself. A value has no `data` or `next` attribute to follow. I need `current = head` so that I can traverse the nodes.

There were several other problems hiding nearby. Inserting before the head requires the new node to point to the old head. A bare `return` gives back `None` instead of the new head. The end of the list is represented by `current.next is None`, not an empty list. Most importantly, I need to find the insertion location before changing any links.

The working solution is:

```python
def sorted_insert(head, data):
    new_node = Node(data)

    # Empty list or insert before the current head
    if head is None or data < head.data:
        new_node.next = head
        return new_node

    current = head

    # Find the node immediately before the insertion point
    while current.next is not None and current.next.data < data:
        current = current.next

    # Insert the new node
    new_node.next = current.next
    current.next = new_node

    return head
```

The key is not to stop at the insertion point. It is to stop at the node immediately before it.

For this list:

```text
3 → 5 → 9 → None
```

and `data = 7`, I start at `3`. I look ahead at `5`. Since `5 < 7`, I move to `5`. Then I look ahead at `9`. Since `9` is not less than `7`, I stop.

Now I can splice the new node into the chain:

```python
new_node.next = current.next
current.next = new_node
```

```text
Before: 5 ─────────→ 9

Step 1: 7 ────────→ 9
Step 2: 5 ────────→ 7

After:  5 → 7 → 9
```

The order matters. First, point the new node at everything that used to follow `current`. Then point `current` at the new node. If I overwrite `current.next` too early, I can discard the rest of the list.

These linked lists are challenging because the code is really a test of whether I can hold the structure in my head. Each assignment changes the map. I have to know not only what I want the final chain to look like, but also which connection must be preserved before I change the next one.

## Code Platoon Reflection

This was an exciting day. First, for Code Platoon work. I continued my audit of my final project. Going line by line to trace the data flow is revealing and somewhat fun.

It is slow though. Each code line leads to another and another. The whole thing is really similar to doing intensive reading in Japanese or Chinese. While you do it, it feels like crawling single characters at a time, however, you always end up better off after.

The real breakthrough came after I was invited on an interview with an acquaintance named Dominic. He’s a writer and founder of Legend Fiction, a faith-based writing community that I learned about a year prior.

Dominic and I share similar views about AI and writing, mainly that we both see it as a net good for creative and business work. So when he reached out to invite me on his podcast, I immediately said yes.

The conversation flowed as if it was 2025. We just updated each other on what we'd been up to over the past 9 months. I talked about my experience with Code Platoon (which made Instructor Kevin and the Career Services rep Katherine very happy) and about how my view of working with AI had evolved since we'd last spoken.

At some point he brought up Worldmaker, his vision for a sprawling digital environment where fiction writers could visualize, plan, write, and market their stories among many other features. It sounded like an ambitiously vibe-coded project.

Following the interview, he emailed me asking if I could take a look at his project. This was it! A strong demand signal that business owners who vibe-coded software in this wild AI era would want or need a trained dev to optimize, fix, or maintain the mystical code they'd conjured from the magic black box of AI.

I pitched him a $400 technical assessment. However, unfortunately he said he couldn't afford to pay me at the moment. Bummer. But I still wanted to look at his code just to see how another mind vibe-coded software, especially someone who didn't know code. That experience and my good relationship with him was ultimately more valuable.
