---
date: 2026-07-31
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - codewars
  - map
  - sorting-algorithms
  - django
  - software-engineering
  - code-platoon
---

# The Index Belongs to the Letter

Beneath the overpass, I watched every light in the city disappear except the flashlight still tracing even and odd positions in the hands of myself.

<!-- more -->

---

## Weird Case, Wrong Index

Today's Codewars problem was **WeIrD StRiNg CaSe**. The function receives a sentence and returns the same sentence with the even-indexed characters in each word uppercase and the odd-indexed characters lowercase.

The index is zero-based, so the first character is uppercase:

```text
"String case" → "StRiNg CaSe"
```

The important rule is that the index resets for every word. The `S` in `String` is index `0`, and the `c` in `case` is also index `0`.

I felt like I was super close with my first solution:

```javascript
function toWeirdCase(string) {
  const strSplit = string.split(" ");
  const arr = [];

  for (let [i, ltr] of strSplit.entries()) {
    if (i % 2 === 0) {
      arr.push(ltr.toUpperCase());
    } else if (i % 2 !== 0) {
      arr.push(ltr.toLowerCase());
    } else if (ltr === " ") {
      arr.push(" ");
    }
  }

  return arr.join(" ");
}
```

I was close in one sense: the modulo check was correct.

```javascript
i % 2 === 0
```

That correctly identifies an even index. The problem was what `i` represented.

After this line:

```javascript
const strSplit = string.split(" ");
```

the value of `strSplit` is an array of whole words:

```javascript
["String", "case"]
```

That means `.entries()` gives me the index and value of each **word**, not each letter:

```text
i = 0, ltr = "String"
i = 1, ltr = "case"
```

My function was making the zeroth word entirely uppercase and the first word entirely lowercase. It would return:

```text
"STRING case"
```

The variable name `ltr` made the mistake harder to see. I had named the value according to what I wanted it to be, not what it actually was. A more honest name like `word` would have exposed the mismatch immediately.

## One Loop for Each Level

The sentence has two levels of structure:

```text
sentence
└── words
    └── characters
```

Because I need to visit both levels, the direct loop solution uses two loops. The outer loop visits each word. The inner loop visits each character inside that word.

```javascript
function toWeirdCase(string) {
  const words = string.split(" ");
  const weirdWords = [];

  for (const word of words) {
    let weirdWord = "";

    for (let i = 0; i < word.length; i++) {
      if (i % 2 === 0) {
        weirdWord += word[i].toUpperCase();
      } else {
        weirdWord += word[i].toLowerCase();
      }
    }

    weirdWords.push(weirdWord);
  }

  return weirdWords.join(" ");
}
```

The outer loop does not need its own index because the position of the word is irrelevant. Every word follows the same rule. The inner loop starts `i` at zero each time the outer loop reaches a new word, which gives the kata exactly the reset it asks for.

I also did not need this condition from my original solution:

```javascript
else if (ltr === " ")
```

Once I split the sentence on spaces, there are no spaces inside the resulting words. The spaces temporarily disappear from the data and return when I call `join(" ")` at the end.

That is the full transformation:

```text
"String case"
      ↓ split(" ")
["String", "case"]
      ↓ transform each word
["StRiNg", "CaSe"]
      ↓ join(" ")
"StRiNg CaSe"
```

## Map Is a Transformation

I also wanted to understand the shorter solution using `.map()` instead of just copying it.

`map()` takes an array, runs a function on every item, and returns a new array containing the returned values. It does not change the original array, and the new array has the same number of items.

```javascript
const numbers = [1, 2, 3, 4];

const doubled = numbers.map((number) => {
  return number * 2;
});

// [2, 4, 6, 8]
```

The callback receives the current item and, if I need it, the current index:

```javascript
array.map((item, index) => {
  return transformedItem;
});
```

The weird-case solution uses two maps for the same reason the loop solution uses two loops:

```javascript
function toWeirdCase(string) {
  return string
    .split(" ")
    .map((word) => {
      return word
        .split("")
        .map((char, index) => {
          return index % 2 === 0
            ? char.toUpperCase()
            : char.toLowerCase();
        })
        .join("");
    })
    .join(" ");
}
```

The outer `map()` transforms words. The inner `map()` transforms characters.

For the first word, the inner pipeline looks like this:

```text
"String"
    ↓ split("")
["S", "t", "r", "i", "n", "g"]
    ↓ map by character index
["S", "t", "R", "i", "N", "g"]
    ↓ join("")
"StRiNg"
```

That finished string becomes the return value for one pass through the outer map. After the outer map processes every word, it has:

```javascript
["StRiNg", "CaSe"]
```

The final `.join(" ")` turns that array back into a sentence.

The key to reading nested `map()` calls is to stop trying to understand the entire chain at once. Ask what kind of value exists at the current level:

```text
What do I have?       An array of words.
What do I want?       An array of transformed words.

What do I have next?  An array of characters.
What do I want?       An array of transformed characters.
```

Then `join()` rebuilds the strings after the transformations are complete.

The shorter solution is not automatically better because it uses fewer lines. The loop version makes the control flow easier to watch while I am learning. The map version becomes readable once I understand that every callback must return the item that belongs in the new array.

## Sorting the Long Way on Purpose

I also made a [Bubble and Insertion Sort lab](https://github.com/KgitWH21/bubble-insertion-sort-lab) to practice the sorting algorithms.

JavaScript already has a built-in `.sort()` method, but using it would defeat the point. The goal was to practice what a sorting algorithm actually does: inspect values, compare them, move them, and repeat until the collection is ordered.

Bubble sort repeatedly compares neighboring values and swaps them when they are in the wrong order. With every pass, another large value moves toward the end of the array.

Insertion sort treats part of the array as already sorted. It takes the next value and moves it left until it reaches the position where it belongs.

They approach the problem differently, but both make me keep track of what a loop's index represents. An index might identify the current pass, a pair being compared, the boundary between sorted and unsorted values, or the position where a value should be inserted.

That connects directly to my Codewars mistake. The expression involving the index was not wrong. My mental label for the index was wrong.

```text
Correct calculation + wrong level of data = wrong result
```

Writing sorting algorithms by hand is useful because they do not let me hide that distinction behind a built-in method. Every comparison forces me to answer: what value am I looking at, and what does this index mean right now?

## Small Projects Are Closing the Gap

Code Platoon Week 9, Day 41 is the end of week nine, and we are getting closer to the end of the course. There are so many little pieces to Django, but they have been broken up pretty well.

What is helping me most is continuing to build small projects around those pieces.

With React, the jump from piecing features together to building a whole application for an assessment felt huge. Django feels more manageable because I have been practicing the parts on my own as I learn them. Instead of waiting for the assessment to discover whether I can start from scratch, I am repeatedly starting from scratch now.

Every small project rehearses some portion of the larger sequence:

```text
create the project
      ↓
configure the application
      ↓
define the models
      ↓
make and run migrations
      ↓
build serializers and views
      ↓
connect URLs
      ↓
test the behavior
```

I still have to remember many files, commands, and relationships, but they are becoming parts of a process instead of isolated facts. The assessment will test all those little parts together. Practicing with small applications is reducing the size of that eventual leap.

The sorting lab serves the same purpose. It is a focused project where the entire reason for building it is to make an abstract process concrete. It does not need to become a giant application. It needs to give me somewhere to practice the algorithm, make mistakes, and leave behind working code I can study later.

## Reset at the Right Boundary

Today's Codewars bug came from applying the right rule at the wrong level.

I needed alternating capitalization for the characters inside each word. I was alternating capitalization across the words inside the sentence. Splitting the input gave me a useful structure, but I stopped one level too early.

The fix was not a clever trick. It was recognizing the boundary where the work resets:

```text
sentence → word → character
                    ↑
              index resets here
```

That is also how the rest of the course is starting to feel. A full Django application can look like one enormous problem, but it is made of smaller boundaries. Models solve one part. Serializers solve another. Views, URLs, containers, and databases each operate at their own level.

I do not have to hold the whole application as one undivided task. I need to know which level I am working on, what the current value represents, and what should come out of that step.

Week nine is done. The individual project is next, and I have a semi-free weekend to start sketching it out and planning it. More importantly, I am heading into it with a growing collection of small projects and a clearer idea of how to break the work into levels.

Sometimes being super close means the logic is right and the index belongs to something else.

Week nine, day forty-one, done.
