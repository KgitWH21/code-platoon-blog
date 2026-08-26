---
date: 2026-08-26
categories:
  - Code Platoon
authors:
  - keith
tags:
  - linked-lists
  - circular-linked-lists
  - algorithms
  - two-pointers
  - python
  - leetcode
  - code-platoon
---

# The Fast Pointer Catches the Loop

With enough practice, a linked-list cycle becomes a familiar surprise: the missing end I once dreaded is now the clue I’m relieved to find. There may be no `None`, no tail I can eventually reach, and no obvious sign that I have already passed this node before. The solution is to stop looking for the end and make two pointers disagree about time.

<!-- more -->

---

Today’s LeetCode problem was [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/). Given the head of a singly linked list, I had to determine whether following its `next` pointers would eventually lead back to a node I had already visited.

## A List That Never Ends

A normal singly linked list eventually reaches `None`:

```text
A → B → C → D → None
```

A cyclic list reconnects some later node to an earlier one:

```text
A → B → C → D
        ↑     ↓
        └─────┘
```

Starting at `A`, I can keep following `next`, but after reaching `D` I return to `C`. From there the traversal repeats forever.

The problem description uses `pos` to explain which node the tail connects to, but `pos` is not passed into the function. That distinction matters. I do not get an index telling me where the cycle begins. I only get `head`, so the cycle has to be detected from the links themselves.

## Two Pointers, Two Speeds

My solution uses Floyd’s cycle-detection algorithm, also called the tortoise-and-hare algorithm:

```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        slow = head
        fast = head

        while fast is not None and fast.next is not None:
            slow = slow.next
            fast = fast.next.next

            if slow is fast:
                return True

        return False
```

Both pointers begin at the head. On each pass through the loop, `slow` advances one node and `fast` advances two.

If the list has no cycle, the fast pointer eventually reaches the end. If the list does have a cycle, both pointers eventually enter it. Once they are inside, the faster pointer gains one node on the slower pointer during every iteration. It cannot keep gaining forever without landing on the same node.

```text
iteration 0: slow = A, fast = A
iteration 1: slow = B, fast = C
iteration 2: slow = C, fast = C  → cycle
```

The useful part is that I do not need to know the cycle’s size or where it starts. Different speeds force the answer to reveal itself.

## Identity, Not Equality

The condition uses `is`:

```python
if slow is fast:
```

That checks whether both variables refer to the exact same node object. Comparing their values would not be enough. A perfectly valid non-cyclic list might contain duplicate values:

```text
7 → 3 → 7 → None
```

The two nodes containing `7` are not the same node. A linked-list cycle is about returning to the same object through its pointers, not seeing the same value twice.

## The Loop Condition Carries the Safety

The fast pointer moves two steps at a time:

```python
fast = fast.next.next
```

Before doing that, I have to know that both `fast` and `fast.next` exist:

```python
while fast is not None and fast.next is not None:
```

Python evaluates the condition from left to right and short-circuits it. If `fast` is `None`, it never tries to evaluate `fast.next`. That keeps an empty list, a one-node list, and every ordinary list safe.

The order matters. Writing the checks backward could try to access `.next` on `None` before discovering that the pointer does not exist.

## Why Not Remember Every Node?

Another valid solution would store visited nodes in a set:

```python
def hasCycle(head):
    visited = set()
    current = head

    while current is not None:
        if current in visited:
            return True

        visited.add(current)
        current = current.next

    return False
```

That approach is direct: if I encounter a node already in the set, the list has looped. It also takes `O(n)` additional space in the worst case.

The two-pointer solution traverses the list in `O(n)` time while using only `O(1)` extra space:

```text
visited set: O(n) time, O(n) space
two pointers: O(n) time, O(1) space
```

This is one of those algorithms that feels like a trick until the underlying relationship becomes clear. The set remembers every node explicitly. Floyd’s algorithm replaces that memory with motion. The difference in speed does the remembering.

## Code Platoon — Week 13, Day 61

Most of today was a personal-project work day, and my project was already finished. I did discover a small submission mix-up: I had placed my presentation in the shared folder two days ago, but it was removed because the name on it was not recognized. It was funny once I realized what had happened, and fortunately it was easy to correct.

Tomorrow we watch the personal-project presentations and then return for another lesson in the afternoon. We also appear to be done with stand-down problems. With only two and a half weeks left, that feels like a big sign that the course is on the downslope.

The cycle-detection problem was the main technical work I did today, and it made sense to me. It also fit neatly with yesterday’s linked-list work. Yesterday I was maintaining a circular structure intentionally. Today I had to detect when a singly linked list had become circular without being told where the connection was.

In both problems, the important information lives in the pointers. The values can repeat, the end can disappear, and the diagram can be hidden from me. But if I follow the relationships carefully—and sometimes at two different speeds—the structure tells me what it is.
