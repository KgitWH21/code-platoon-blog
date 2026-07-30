---
date: 2026-07-30
categories:
  - Code Platoon
authors:
  - keith
tags:
  - django
  - databases
  - docker
  - postgresql
  - javascript
  - codewars
  - software-engineering
  - code-platoon
---

# Know Which Room You Are In

An hour and a half later, I was still asking where each command began, where each command led, and where each command left me, inside the nearest terminal window.

<!-- more -->

---

## I Capitalized the Words Into Oblivion

Today's Codewars problem asked me to convert a string to PascalCase: capitalize the first letter of every word, remove the spaces, and return one combined string.

```text
"hello case"       → "HelloCase"
"pascal case word" → "PascalCaseWord"
```

My first attempt had the right overall structure:

```javascript
String.prototype.camelCase = function() {
  const words = this.split(" ");
  const resultArray = [];

  for (let word of words) {
    resultArray.push(word[0][0].toUpperCase());
  }

  return resultArray.join("");
}
```

I split the string into words, looped through them, capitalized a letter, and joined the results without spaces. The problem was that I only kept the capitalized letter.

For `"hello"`, this expression:

```javascript
word[0][0].toUpperCase()
```

produces `"H"`. The first `[0]` already selects the first character, so the second one is unnecessary. More importantly, nothing in the expression preserves `"ello"`. My function did not return `"HelloCase"`. It returned `"HC"`.

The fix is to capitalize the first character and attach the rest of the word:

```javascript
String.prototype.camelCase = function() {
  const words = this.split(" ");
  const resultArray = [];

  for (const word of words) {
    if (word.length > 0) {
      resultArray.push(word[0].toUpperCase() + word.slice(1));
    }
  }

  return resultArray.join("");
}
```

`slice(1)` begins at index `1`, the second character, and continues to the end. The transformation is now:

```text
"hello"
   ↓
"h".toUpperCase() + "ello"
   ↓
"Hello"
```

The empty-string check matters if the input contains extra spaces. Splitting `"hello  case"` on a single space produces an empty string between the two words. An empty string has no character at index `0`, so trying to call `toUpperCase()` on `word[0]` would fail.

## Map the Words, Then Join Them

Once the loop worked, I wanted to see the same solution with `.map()`.

This problem is almost a definition of what `map()` is for: take an array, transform every item, and return a new array containing the transformed items.

```javascript
String.prototype.camelCase = function() {
  return this
    .split(" ")
    .map(word => word.charAt(0).toUpperCase() + word.slice(1))
    .join("");
}
```

Each method hands its result to the next one:

```text
"hello case"
      ↓ split(" ")
["hello", "case"]
      ↓ map(...)
["Hello", "Case"]
      ↓ join("")
"HelloCase"
```

Using `charAt(0)` also handles an empty string safely. It returns `""`, while `word[0]` returns `undefined`. That means an extra space does not cause the method chain to throw an error.

The important part is not that the chained version is shorter. It is that each method has one clear job. `split()` changes the string into a collection of words. `map()` transforms the words. `join()` turns the collection back into a string.

My first solution understood the beginning and the end of that pipeline. I split the words and joined the result. I just accidentally threw away most of the data in the middle.

## The Concepts Are Not the Bottleneck

Code Platoon Week 9, Day 40 was about database relationships.

Conceptually, one-to-many and many-to-many relationships make sense to me. One author can have many books. One student can enroll in many courses, and one course can contain many students. I can understand the shape of the data without much trouble.

The difficult part is translating that shape into Django while keeping track of all the surrounding machinery:

```text
models
migrations
serializers
views
fixtures
Docker containers
the Django shell
the host shell
PostgreSQL
psql
```

There is a lot of code, and there are a lot of commands. The concepts are simple compared with remembering what to type, where to type it, and what layer of the application I am communicating with.

That is operational fluency. I know what I want the system to represent. I am still building the muscle memory required to make the system represent it.

## Location Changes Meaning

Today gave me useful practice with Docker and working inside containers. I am beginning to understand that a command makes more sense once I know my location.

Before typing anything, I need to be able to answer:

```text
Am I on the host machine?
Am I inside the Django container?
Am I using manage.py to communicate with Django?
Am I using psql to communicate directly with PostgreSQL?
Am I asking Docker Compose to manage the application?
Am I using docker exec to run a command inside one container?
```

Those are different rooms.

For example:

```bash
docker compose up
```

asks Compose to start and coordinate the services defined for the application.

```bash
docker compose exec web python manage.py shell
```

runs Django's shell inside the already-running `web` service.

```bash
docker compose exec db psql -U postgres
```

opens PostgreSQL's command-line client inside the database service.

The exact service names vary by project, but the mental model stays the same. Compose manages the group. `exec` enters a running service to perform a specific action. `manage.py` talks through Django. `psql` talks to PostgreSQL.

When I do not understand that map, the commands feel like disconnected incantations. I may know that a command worked yesterday without knowing why it belongs in this terminal today. Once I know where I am and which system I am addressing, the command becomes an action instead of a spell.

That is why I spent so much time asking, “What does this command mean? What am I actually doing here?”

It was not a detour from learning Django. It was how I built the context that makes Django usable.

## Projects as External Memory

The small daily projects continue to help because they force me to start the system mostly from scratch.

Starting from scratch is slower than opening a completed exercise. I have to create the pieces, run the setup commands, enter the containers, make migrations, load data, and correct whatever I forgot. But that repetition is exactly what turns a list of commands into muscle memory.

Each project becomes three things:

1. A repetition exercise for the day's skill.
2. A complete example that gives the lecture context.
3. A personalized reference I can retrace later.

The third one may be the most valuable when assessment time arrives.

Assessments become difficult and time-consuming when I have to dig for every pattern. I vaguely remember seeing a many-to-many relationship, a serializer, or a Docker command, but I do not remember where it was or how the pieces connected.

A working project changes that. Instead of searching through disconnected notes, I can return to an application where the pattern already exists:

```text
models → migrations → serializers → views → URLs → tests
```

I do not have to remember everything. I need to remember enough to find a trail I have already walked.

That also means I do not need to finish every optional learning guide or turn every daily project into a polished product. The project has done its job when it teaches the target concept and leaves behind a reliable example. A small working skeleton can be more useful than a large unfinished ambition.

## A Library of Trails

Today's two lessons were more closely related than they first appeared.

In the Codewars problem, I understood the transformation but lost part of each word while implementing it. In Django, I understand the relationships but can lose my place while moving through the commands and environments required to build them.

In both cases, the problem is not the idea. It is preserving the full chain between the idea and the result.

For PascalCase:

```text
split → transform the whole word → join
```

For the backend:

```text
choose the environment → run the command → change the system → verify the result
```

The concepts are making sense. What I am building now is command fluency, environmental awareness, and a library of working examples I can retrace under pressure.

I do not need every command memorized today. I need to keep entering the rooms, noticing where I am, and leaving a trail back out.

Week nine, day forty, done.
