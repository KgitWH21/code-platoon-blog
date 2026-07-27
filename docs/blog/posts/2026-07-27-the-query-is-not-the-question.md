---
date: 2026-07-27
categories:
  - Code Platoon
authors:
  - keith
tags:
  - sql
  - databases
  - django
  - docker
  - ai
  - software-engineering
  - code-platoon
---

# The Query Is Not the Question

My heartbeat suddenly became a production monitor, hammering out an alert in my chest, logging every nervous spike, and warning me that I had built a whole fucking application around our one dedicated day of SQL.

<!-- more -->

---

## One Day of SQL

Week nine began with databases.

More precisely, it began with our only full day of SQL. Tomorrow we move into Django, where the framework will generate many of the SQL queries for us. That schedule says something about the role SQL is expected to play in the course. We need to understand what the database is doing, but we are not training to spend the rest of the program composing elaborate queries by hand.

Fortunately, the basics are pretty straightforward.

SQL is unusually readable for a programming language. A query often looks close to a sentence:

```sql
SELECT name, course
FROM students
WHERE active = TRUE
ORDER BY name;
```

Select these columns from this table where this condition is true, then order the results this way.

There are still patterns to learn. I need to remember how tables relate, how joins combine them, how values are inserted or updated, and which clause belongs where. But the language does not feel full of tricks. Most of the work is getting used to the grammar and seeing the same shapes enough times that they become familiar.

That was my goal today: see more SQL.

## Build the Context Too

I tested a new approach to learning the material. Instead of completing only isolated exercises, I generated a full application that gave the day's concepts somewhere to live.

The result was HAC Studios School, a souped-up version of the assigned exercises. It had an actual application surrounding the database, which meant I could see how the SQL affected something larger than a terminal result.

This approach creates extra work. Before I could spend much time querying the database, I had to decide what the app was, get it running, and work through some Dockerization. There is always a setup cost when I turn a small lesson into a complete system. I can easily spend an hour constructing the laboratory before I begin the experiment.

But once it works, the lesson has context.

A table is no longer an abstract grid called `users` because every database tutorial apparently begins with a table called `users`. It belongs to an application. The rows represent things the application needs. A query produces information that can be displayed or used. A foreign key describes an actual relationship in the system. If I change the data model, I can see what that change means somewhere else.

That is much closer to the way I naturally understand software. I learn the individual piece better when I can see what it is connected to.

I still did the reading. I still worked through the underlying ideas. The generated app did not replace the exercises so much as expand them. It gave me more occasions to look at SQL, read SQL, and connect a query to a result.

By the afternoon, that repetition had paid off.

## The Test Was Whether I Could Do It Alone

Our final problem of the day was another SQL exercise. I completed it fairly easily without looking anything up.

That is the part that matters.

Generating a larger application can create the feeling of productivity without proving that I learned the day's concept. I can have a database, a Docker container, and a polished interface while still being unable to write a basic query myself. The surrounding app is useful only if it eventually helps me operate without the scaffolding.

Today it did.

I had seen the patterns enough times that the stand-down problem felt familiar. I could read the requirement, translate it into the structure of a query, and write the solution from memory. It was not an especially complicated problem, and SQL is not an especially cryptic language, but that made it a clean test of the method.

The app gave me context. The independent problem gave me evidence.

That combination feels promising. I do not want to turn every lesson into an enormous side project, especially when the setup can consume the time I meant to spend on the concept. But a small working application may be the best middle ground: enough of a system to make the code real, but not so much that the system becomes the assignment.

## Out of Scope

The more complicated question today was not about SQL. It was about AI and what it means when a student submits code that goes beyond what was taught.

The reasoning is understandable. A course teaches a set of tools sufficient to complete an assessment. If a solution suddenly uses a technique that has never appeared in the class, that can be a signal that the author got outside help. In the current environment, AI is an obvious possibility.

But a signal is not an answer.

Software has never had only one valid route to a solution. Documentation, Stack Overflow, blog posts, source code, previous experience, and plain curiosity can all lead someone to a method that was not covered in class. Now AI sits beside all of those resources, except it is faster, more conversational, and much better at adapting an answer to the exact code in front of it.

That makes unfamiliar code easy to produce. It does not automatically make the code misunderstood.

Someone can use an in-scope technique without understanding it. Someone else can find an out-of-scope technique, study it, test it, and understand it completely. The visible code cannot settle that distinction by itself.

The real question is ownership.

Can I explain why the code works? Can I change it when the requirements change? Can I identify the assumptions inside it? Can I reproduce the underlying idea without leaning on the generated answer? Can I tell when it solved a different problem from the one I actually had?

Those questions are harder to evaluate than whether a particular method appeared in a lecture, but they are also closer to the skill that matters.

## The Magic Thing at 1 A.M.

We are in an awkward transition period.

AI has become genuinely viable for programming. It is especially reliable at the kinds of bounded coding tasks that appear in a software course. If it is one in the morning, an assessment is due, the program is throwing errors, and a tool can inspect the problem and produce a plausible answer in seconds, of course people are going to feel the pull to use it.

Pretending that tool does not exist will not make the temptation disappear.

At the same time, accepting whatever it returns can hollow out the exact process the assessment is meant to measure. The struggle is often where the mental model forms. If AI removes every moment of uncertainty before I have to reason through it, I may finish the software without becoming more capable.

That is the tension I keep returning to. AI can accelerate learning, and AI can imitate learning. From the outside, those two things may look almost identical.

My SQL experiment today is one example of the useful version. I used generation to create more context and more exposure than the exercises alone would have provided. Then I reached a separate problem and solved it on my own. The tool increased the amount of material I could interact with, but the independent work showed me whether any of it had stuck.

That feels like a better standard than purity.

The question is not whether AI touched the work. The question is what I can do after it does.

## Tomorrow, Django Writes the Queries

Tomorrow we begin Django, and Django will abstract away much of what I practiced today.

That does not make the SQL day pointless. Abstractions are easier to trust when I have some idea what they are hiding. When Django's object-relational mapper turns a Python expression into a database query, I will know there is still a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` happening somewhere underneath. If the result is wrong or unexpectedly slow, that lower layer will matter again.

One day is not enough to become an expert in databases. It was enough to learn the basic grammar, build something around it, and prove that I could write a query without help.

Week nine, day thirty-seven, done.

Today was simple on the surface: SQL goes in, rows come out. Underneath it was the more interesting question I will probably be asking for the rest of the course: how do I use a tool that can give me the answer without giving away the learning?

For now, I have one workable answer.

Build the context. Study the patterns. Then close the tool and see what I can do.
