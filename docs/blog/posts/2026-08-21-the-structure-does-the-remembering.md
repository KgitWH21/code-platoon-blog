---
date: 2026-08-21
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
  - caching
  - code-platoon
---

# The Structure Does the Remembering

The linked list watched, judged, and cut loose its doubles while the universe blinked awake long enough to whisper stillhere_12 to Instructor Kevin. A cache can remember what I touched most recently. In both cases, the trick is choosing a structure that already knows enough to make the next decision.

<!-- more -->

---

Today's work stayed with linked lists, first through a Codewars problem about removing duplicates and then through an LRU cache, a data structure that turns a linked list into a record of recent attention.

## Remove the Duplicate, Keep the List

The Codewars problem began with a sorted linked list:

```text
1 → 2 → 3 → 3 → 4 → 4 → 5 → None
```

The function had to delete the duplicate nodes and return:

```text
1 → 2 → 3 → 4 → 5 → None
```

Ideally, it would traverse the list only once.

The word **sorted** does most of the intellectual work here. Because the values are arranged in increasing order, duplicates have to be adjacent. I do not need a set of everything I have seen, and I do not need to compare each node against the entire remainder of the list. I only need to compare the current node with the next one.

```python
class Node(object):
    def __init__(self, data):
        self.data = data
        self.next = None


def remove_duplicates(head):
    current = head

    while current is not None and current.next is not None:
        if current.data == current.next.data:
            current.next = current.next.next
        else:
            current = current.next

    return head
```

The loop condition protects both references I use inside the loop. `current` must exist, and `current.next` must exist, because there is nothing left to compare once I reach the final node.

When two neighboring values match, this line bypasses the duplicate:

```python
current.next = current.next.next
```

```text
Before: 3 → 3 → 4
            ╲____↑

After:  3 ─────→ 4
```

The duplicate node is no longer reachable from the head, so it is no longer part of the list.

The subtle part is what does **not** happen next. I do not advance `current` after removing a duplicate. There may be another duplicate immediately behind it:

```text
3 → 3 → 3 → 4
```

After bypassing the first duplicate, `current` still points to the first `3`, and its new neighbor is the third `3`. The function has to compare those two before moving on. Only when the values differ is it safe to advance.

That produces a single traversal with constant extra space:

```text
time:  O(n)
space: O(1)
```

This felt like a clean conclusion to a week of linked-list problems. Earlier exercises required finding nodes, inserting nodes, and preserving the rest of the chain while changing a connection. Here, the same mechanics became deletion. I am not deleting a node directly. I am changing the route so the traversal no longer reaches it.

## A Cache That Remembers Attention

An LRU cache uses linked lists for a different kind of bookkeeping.

LRU means **Least Recently Used**. A cache stores results so that an expensive operation does not have to be repeated every time. Once the cache reaches its capacity, it evicts the item that has gone unused the longest.

Python provides the easy version through `functools`:

```python
from functools import lru_cache
import time


@lru_cache(maxsize=32)
def heavy_calculation(n):
    time.sleep(2)
    return n * 100


print(heavy_calculation(5))  # Slow: cache miss
print(heavy_calculation(5))  # Fast: cache hit
```

The first call performs the work and stores the result. The second call receives the stored result for the same argument.

The decorator also exposes information and controls that are useful while inspecting the cache:

```python
print(heavy_calculation.cache_info())
heavy_calculation.cache_clear()
```

That is how I would usually want to use an LRU cache in real Python code. The interview version asks a different question: how would I build one myself while keeping both `get` and `put` operations at constant time?

A dictionary solves half the problem:

```text
key → cached node
```

It provides fast lookup, but a normal dictionary does not by itself give me a convenient way to move an accessed item to the front and evict the oldest item from the back.

A doubly linked list solves the other half:

```text
most recent ↔ ... ↔ ... ↔ least recent
```

Whenever an item is accessed, its node moves to the front. When the cache exceeds its capacity, the node at the back is removed. Because every node points both forward and backward, an existing node can be detached without traversing the list to find its predecessor.

```text
dictionary:         find any item quickly
doubly linked list: reorder and evict items quickly
```

Neither structure is enough alone. Together, they make `get` and `put` average `O(1)` operations.

The custom implementation uses dummy head and tail nodes to avoid turning every insertion and removal at the boundaries into a special case. The most recently used node lives just after the head; the least recently used node lives just before the tail.

```python
def _remove(self, node):
    node.prev.next = node.next
    node.next.prev = node.prev


def _add_to_head(self, node):
    node.next = self.head.next
    node.prev = self.head
    self.head.next.prev = node
    self.head.next = node
```

Once again, the data structure is a series of promises about what each reference means. If those promises remain true, the larger behavior follows.

Python's `OrderedDict` can hide most of this pointer work. Its `move_to_end()` method marks a key as recently used, while `popitem(last=False)` removes the oldest one. That implementation is shorter, but manually building the linked-list version makes the underlying design visible.

## August 21, 2026 — Code Platoon — Week 12 End

This was probably the perfect Code Platoon week. We had time to work on our own, a few interesting lessons (Linked Lists) and minimal briefings. For the first time in several weeks I had nothing to say during the weekly retro as far as “things that could be improved.”

During the retro I did get jibed about being “practically famous” for sharing my interview with Dominic yesterday, (Code) but it was all love. I know if I don’t put my work out there, no one will find it.

Kevin did the unthinkable today, he basically released us early. This made sense: there were no more lessons, and everyone was (supposed to be) working on their projects. So I was able to wrap up and go across town to pick up my books.
