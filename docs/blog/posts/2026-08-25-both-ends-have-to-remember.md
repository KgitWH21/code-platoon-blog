---
date: 2026-08-25
categories:
  - Code Platoon
authors:
  - keith
tags:
  - linked-lists
  - doubly-linked-lists
  - circular-linked-lists
  - data-structures
  - python
  - codewars
  - code-platoon
---

# Both Ends Have to Remember

For once, I washed a dish and slid it carefully into place—plate ← plate → plate—a small arrangement I would later recognize in a doubly linked list, just before the rack began to tip. That extra memory makes operations at both ends efficient, but it also doubles the number of relationships I have to preserve whenever the list changes.

<!-- more -->

---

Today’s Codewars problem asked me to implement a doubly linked list with an array-like interface: `push`, `pop`, `shift`, and `unshift`.

The names make the operations sound familiar. The real work is underneath them.

## Links in Both Directions

In a singly linked list, each node holds a value and a pointer to the next node. A doubly linked node also knows which node came before it:

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None
        self.prev = None
```

The list itself keeps references to both ends:

```python
class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None
```

That gives the structure a useful symmetry:

```text
None ← head: A ⇄ B ⇄ C :tail → None
```

The head has no previous node. The tail has no next node. Every node in between has a valid neighbor in both directions.

Those relationships are the list’s invariants. The methods work when they preserve those rules after every change.

## Adding at Either End

`unshift` inserts at the front. If the list is empty, the new node becomes both the head and the tail. Otherwise, the new node points forward to the old head, the old head points backward to the new node, and the head reference moves.

```python
def unshift(self, value):
    new_node = Node(value)

    if self.head is None:
        self.head = new_node
        self.tail = new_node
        return

    new_node.next = self.head
    self.head.prev = new_node
    self.head = new_node
```

`push` is the mirror image at the back:

```python
def push(self, value):
    new_node = Node(value)

    if self.tail is None:
        self.head = new_node
        self.tail = new_node
        return

    new_node.prev = self.tail
    self.tail.next = new_node
    self.tail = new_node
```

This is one of the benefits of storing both `head` and `tail`. Adding to either end takes constant time. I do not have to start at the head and traverse the entire list just to find the back.

## Removing Means Moving the Boundary

My insertion logic was mostly right. The larger problem was in `shift` and `pop`.

Removing a node is not only a matter of finding its value or deleting a variable. The list’s boundary has to move. `shift` must update `self.head`, while `pop` must update `self.tail`. Both methods also need to return the value they removed.

```python
def shift(self):
    removed_node = self.head
    removed_value = removed_node.value

    if self.head is self.tail:
        self.head = None
        self.tail = None
    else:
        self.head = removed_node.next
        self.head.prev = None
        removed_node.next = None

    return removed_value
```

The one-node list is the important special case. Its only node is simultaneously the head and the tail. Removing it means both references must become `None`.

For a longer list, the second node becomes the head. Its `prev` pointer must be cleared because nothing comes before the head anymore.

`pop` performs the same operation from the other direction:

```python
def pop(self):
    removed_node = self.tail
    removed_value = removed_node.value

    if self.head is self.tail:
        self.head = None
        self.tail = None
    else:
        self.tail = removed_node.prev
        self.tail.next = None
        removed_node.prev = None

    return removed_value
```

Because the tail already has a pointer to its predecessor, `pop` does not need to traverse the list. That is the practical advantage of the backward link.

There is also no need to use `del` on the removed node. Once the list no longer holds references to it, Python’s garbage collector can reclaim it. Clearing the detached link makes the separation explicit, but changing the list’s live references is what actually removes the node from the structure.

## Python Already Has a Double-Ended Queue

There is an extremely compact way to satisfy the behavioral requirements:

```python
from collections import deque


class DoublyLinkedList(deque):
    push, shift, unshift = deque.append, deque.popleft, deque.appendleft
```

`deque`, pronounced “deck,” is Python’s built-in double-ended queue. It efficiently adds and removes values from both sides. The assignments simply give its existing methods the names the exercise expects:

```text
push(value)    → append(value)
shift()        → popleft()
unshift(value) → appendleft(value)
pop()          → inherited pop()
```

It is clever, valid Python, and probably the implementation I would prefer if I only needed the behavior in an application.

It also avoids the point of this particular lesson.

If the tests only call the four methods and inspect their returned values, the wrapper may pass. If they expect nodes with `next` and `prev` pointers or inspect `head` and `tail`, it will fail. More importantly, delegating everything to `deque` means I do not practice maintaining the structure myself.

The shortest solution and the most useful learning exercise are not always the same solution.

## Closing the Loop

We have been promised a circular linked-list problem this afternoon. The easiest way to think about it is as a small change to the invariants I already know.

Instead of the ends pointing to `None`, they point to each other:

```python
self.tail.next = self.head
self.head.prev = self.tail
```

```text
head → A ⇄ B ⇄ C ← tail
       ↑         ↓
       └─────────┘
```

An empty list still has no head or tail. In a one-node list, the node’s `next` and `prev` pointers both point back to itself. In a larger list, every insertion or removal has to restore the connection between the head and tail.

That changes traversal too. This loop would never finish:

```python
while current:
    current = current.next
```

There is no `None` waiting at the end of a circular list. Traversal has to stop when it returns to the starting node:

```python
def values(self):
    if self.head is None:
        return []

    result = []
    current = self.head

    while True:
        result.append(current.value)
        current = current.next

        if current is self.head:
            break

    return result
```

That is the recurring lesson with linked lists: the code only makes sense in relation to the structure’s promises. A normal doubly linked list promises that its ends point to `None`. A circular one promises that its ends point back into the list. Once I know which promise I am maintaining, each pointer assignment has a reason.

## Code Platoon - Week 13 - Day 60 - Reflection

Today was the first day that I spent a significant amount of time socializing with my team. Usually it’s cameras off all day until standdown. But we had some good discussions about struggles with the project, general gripes with our various projects, and just overall wellness chickens with each other.

I think we’re all over the project and the course at this point. Going this hard to learn full stack development for three months straight is brutal. And for many of us, life is increasingly placing heavy demands on our time and attention. It’s becoming more and more difficult to stay engaged.

So today was a necessary pressure release valve. Tomorrow the personal project is due. Since I completely finished, I considered each team member’s relative strengths and proposed roles for the final group project. Most seemed to agree with the ideas. Or perhaps they’re too exhausted to protest? It’s probably some of both.

Many team members haven’t been present for the last few days. Hopefully they’re able to finish with us.
