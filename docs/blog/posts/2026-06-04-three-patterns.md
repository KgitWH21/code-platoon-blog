---
date: 2026-06-04
categories:
  - Code Platoon
tags:
  - algorithms
  - python
  - javascript
  - fundamentals
authors:
  - keith
---

# Three Patterns That Run Half of Algorithm Problems

The more Codewars problems I work through, the more I notice that a large percentage of them are really asking the same three questions in different costumes. Not the same *answers* — the same underlying *structures*.

Accumulator. Frequency Counter. Two-Pointer. If you can recognize which one you're looking at, the problem goes from "where do I even start" to "which version of this pattern applies here."

<!-- more -->

This is how language learning actually works, by the way. You don't memorize every sentence you'll ever need. You internalize grammar — the structural patterns that let you generate any sentence. Algorithm problem-solving works the same way. The first time you see a problem, it's a mystery. The fifth time, it's a pattern. The goal is building a small library of named, reusable blueprints.

Here are the three I keep reaching for.

---

## The Accumulator

**What it is:** You initialize a container — a number, a string, a list, a dictionary — and build it up as you move through the input. By the time you've touched every element, the container holds your answer.

**When you reach for it:** Any time the problem asks you to *produce* something from a collection. Sum, product, count, filtered list, transformed values, concatenated string.

**Python:**

```python
def sum_positives(numbers):
    total = 0
    for n in numbers:
        if n > 0:
            total += n
    return total
```

**JavaScript:**

```javascript
function sumPositives(numbers) {
  let total = 0;
  for (const n of numbers) {
    if (n > 0) total += n;
  }
  return total;
}
```

The container doesn't have to be a number. You can accumulate into a list:

```python
def get_even(numbers):
    result = []
    for n in numbers:
        if n % 2 == 0:
            result.append(n)
    return result
```

Or use list comprehension in Python once the pattern is familiar:

```python
def get_even(numbers):
    return [n for n in numbers if n % 2 == 0]
```

The comprehension is shorthand for the accumulator loop. Know the loop first.

---

## The Frequency Counter

**What it is:** You walk through a collection and build a dictionary (or object) that maps each element to the number of times it appears, or to some aggregate property. Then you answer questions about that map.

**When you reach for it:** Any time the problem involves finding duplicates, checking if two collections have the same elements, counting occurrences, or identifying the most/least common item.

**Python:**

```python
def char_frequency(s):
    freq = {}
    for char in s:
        freq[char] = freq.get(char, 0) + 1
    return freq

# char_frequency("hello") → {'h': 1, 'e': 1, 'l': 2, 'o': 1}
```

**JavaScript:**

```javascript
function charFrequency(s) {
  const freq = {};
  for (const char of s) {
    freq[char] = (freq[char] || 0) + 1;
  }
  return freq;
}
```

A common application: check whether two strings are anagrams.

```python
def is_anagram(s1, s2):
    if len(s1) != len(s2):
        return False
    freq = {}
    for char in s1:
        freq[char] = freq.get(char, 0) + 1
    for char in s2:
        if char not in freq or freq[char] == 0:
            return False
        freq[char] -= 1
    return True
```

The naive approach to anagram-checking compares every character in `s1` to every character in `s2` — O(n²). The frequency counter does it in O(n). The pattern buys you a meaningful performance improvement, but more importantly it's cleaner to reason about.

---

## The Two-Pointer

**What it is:** You maintain two indices — usually one starting at each end of a sorted array, or one moving faster than the other — and converge them toward a solution without scanning the full array for each element.

**When you reach for it:** Sorted arrays, palindrome checks, pair-sum problems, removing duplicates from sorted sequences, anything where scanning from both ends simultaneously gives you information a single pass can't.

**Python — palindrome check:**

```python
def is_palindrome(s):
    left = 0
    right = len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True
```

**JavaScript:**

```javascript
function isPalindrome(s) {
  let left = 0;
  let right = s.length - 1;
  while (left < right) {
    if (s[left] !== s[right]) return false;
    left++;
    right--;
  }
  return true;
}
```

**Python — find pair that sums to target in sorted array:**

```python
def two_sum_sorted(nums, target):
    left = 0
    right = len(nums) - 1
    while left < right:
        current = nums[left] + nums[right]
        if current == target:
            return [left, right]
        elif current < target:
            left += 1
        else:
            right -= 1
    return []
```

The key intuition: because the array is sorted, moving `left` up increases the sum, moving `right` down decreases it. You can home in on the target without nested loops. O(n) instead of O(n²).

---

## How to use this

When I see a new problem now, before I write a single line of code, I ask:

1. Am I building something from a collection? → Accumulator.
2. Do I need to know how many times something appears, or whether two collections share elements? → Frequency Counter.
3. Do I have a sorted array and need to find a relationship between elements? → Two-Pointer.

Most problems that don't fall into one of these three still share a structural DNA with something I've seen before. The vocabulary helps. Named patterns are easier to search for, easier to discuss, and easier to retrieve under pressure than unnamed intuitions.

The first time I saw each of these, it was a mystery. That part goes away.
