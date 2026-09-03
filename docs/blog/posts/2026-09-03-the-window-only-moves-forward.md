---
date: 2026-09-03
categories:
  - Code Platoon
authors:
  - keith
tags:
  - algorithms
  - sliding-window
  - binary-search-trees
  - recursion
  - leetcode
  - group-projects
  - code-platoon
---

# The Window Only Moves Forward

Today we got nearly 85% done with the group project. It's live at https://youngmoney.money/

<!-- more -->

---

We did another algorithm rush today, but the lead instructor had use challenge him to a few tough algorithms. It was good to see how he handled a complex problem like this Codewars: In this kata you're expected to find the longest consecutive sequence of positive squares that sums up to a number.

For example:

595 = 6² + 7² + 8² + 9² + 10² + 11² + 12²

Your task is to write a function that either finds the longest consecutive sequence of integer roots whose squares sum to the number n, or an empty sequence if no such sequence exists.  A solution:

```python
def longest_sequence(n: int) -> list[int]:
    left = 1
    right = 1
    current_sum = 1
    
    # We only need to check starting numbers whose square is less than or equal to n
    while left * left <= n:
        if current_sum == n:
            # Because we start checking from left = 1 (the smallest possible numbers),
            # the very first valid sequence we find is mathematically guaranteed to 
            # require the most terms. We can return immediately.
            return list(range(left, right + 1))
            
        elif current_sum < n:
            # Our sum is too small, so expand the window to include the next number
            right += 1
            current_sum += right ** 2
            
        else:
            # Our sum is too big, so remove the leftmost number from our window
            current_sum -= left ** 2
            left += 1
            
    # Return an empty list if the loop finishes with no match
    return []
```

## Why the Sliding Window Works

The interval from `left` through `right` is the window, and `current_sum` is the sum of the squares inside it. There are only three cases:

- If `current_sum` equals `n`, the window is a solution.
- If `current_sum` is less than `n`, moving `right` forward adds the next square and makes the sum larger.
- If `current_sum` is greater than `n`, moving `left` forward removes the smallest square in the window and makes the sum smaller.

This works because every square in the sequence is positive. Expanding the window can never decrease the sum, and shrinking it can never increase the sum. Neither pointer ever moves backward, so the algorithm does not have to recalculate the sum of every possible sequence from scratch.

Starting with the smallest possible `left` value also matters. If two consecutive sequences have the same sum, the one that begins with smaller roots must use more terms than the one that begins with larger roots. Because the algorithm checks starting positions in increasing order, the first match is the longest match.

The pointers only move forward, and they never need to move beyond roughly `√n`, giving the algorithm `O(√n)` time and `O(1)` extra working space. The returned list uses `O(k)` space, where `k` is the length of the answer.

## How a Binary Search Tree (BST) Works

We also had a lesson on binary search trees. I practiced with basic LeetCode problems to get a handle on it. Here's what I learned: How a Binary Search Tree (BST) Works
A Binary Tree is a data structure made of nodes, where each node has at most two children (a left child and a right child).

A Binary Search Tree (BST) adds one golden rule to this structure:

Left Subtree: Every node to the left of the current node must have a value less than the current node.

Right Subtree: Every node to the right of the current node must have a value greater than the current node.

Because of this organized structure, searching a BST is incredibly fast—it works exactly like searching for a word in a physical dictionary. You don't read every page; you open to the middle, check if your word comes before or after that page, and rip the remaining search space in half.

The Search Algorithm:

Start at the root node.

Compare your target value to the current node's value.

If they match, you're done! Return the node.

If your target is less than the current node, move down to the left child.

If your target is greater, move down to the right child.

Repeat until you find the value or hit an empty spot (null/None), which means the value doesn't exist in the tree.

This cuts the number of nodes you have to check in half at every step, resulting in a time complexity of O(log n) (assuming the tree is relatively balanced).

## The Problem: LeetCode 700 - Search in a Binary Search Tree

Then I completed this beginner LeetCode problem: The Problem: LeetCode 700 - Search in a Binary Search Tree
Difficulty: Easy

Prompt:
You are given the root of a binary search tree (BST) and an integer val.
Find the node in the BST that the node's value equals val and return the subtree rooted with that node. If such a node does not exist, return null.

Example:

Input: root = [4,2,7,1,3], val = 2

Output: [2,1,3] (You return the actual node object containing 2, which brings its children 1 and 3 along with it). Solution: The Solution (Recursive)
Recursion is usually the most elegant way to solve tree problems. We just translate the search algorithm steps directly into code:

Python

```python
class Solution:
    def searchBST(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        # Base case: If the tree is empty, or we found the value
        if root is None or root.val == val:
            return root
        
        # If the target value is greater, search the right subtree
        if val > root.val:
            return self.searchBST(root.right, val)
        
        # If the target value is smaller, search the left subtree
        if val < root.val:
            return self.searchBST(root.left, val)
```

## Why the Function Returns a Subtree

When the function finds the target, it returns `root`, not `root.val`. A tree node holds both its value and references to its children. Returning the node containing `2` therefore returns access to the entire subtree rooted there: `[2,1,3]`.

Each recursive call discards the half of the current subtree that cannot contain the target. The base case handles both possible endings: it returns the matching node when `root.val == val`, or `None` after the search moves past a leaf without finding the value.

Complexity Analysis:

Time Complexity: O(H) where H is the height of the tree. In the worst case (a completely unbalanced, straight-line tree), this is O(n). In a perfectly balanced tree, it is O(log n).

Space Complexity: O(H) to account for the recursive call stack in memory.

## Code Platoon — Week 14, Day 67

After a brutal week of algorithms, group projects, lectures and mock interview prep, today was the first day the pace began to slow as we near the end of the program next week.
