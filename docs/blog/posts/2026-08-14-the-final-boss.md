---
date: 2026-08-14
categories:
  - Code Platoon
authors:
  - keith
tags:
  - personal-project
  - full-stack-development
  - web-security
  - software-engineering
  - curriculum-design
  - code-platoon
---

# The Final Boss

In the whir of my computer, beneath the weight of eleven weeks of instruction, at the threshold of one final weekend, the gift and burden of knowing how to build the whole application waited.

<!-- more -->

---

Code Platoon Week 11 is done.

I made some progress on my personal project today, and I have already decided what this weekend is going to be: I am going to sit down and knock it out.

I don't know whether I can finish the entire thing in one weekend. I am anticipating that it will take all weekend. But I feel like I have all the notes. I have everything together. What remains is not another conceptual mystery. I need a long enough block of time to sit down, assemble the pieces, and keep going until the application works.

This is the final boss of Code Platoon. A giant puzzle room app to be assembled from scratch.

The group project comes afterward, but that will be a group project. Ill have an assigned role inside a larger build and I won't be responsible for designing and constructing an entire full-stack application from scratch by myself.

This is different.

```text
idea
  ↓
data model
  ↓
backend API
  ↓
authentication and security
  ↓
frontend interface
  ↓
deployment
```

The personal project asks me to own that whole chain. If I can finish it, the group project will feel like the bonus level at the end of *Super Mario World* where the game says, *Thank you. You did a good job.* A fun, fast victory lap.

Maybe thats too optimistic? Right now, though, the personal project feels like the true bulk of the remaining work.

## Building with Missing Pieces

Today included cookies and security, which are exactly the kinds of lessons that make the timing of the personal project complicated.

We began planning these projects before the core instruction was completely finished. Then we were given project time while still covering cookies, JWTs, security, and deployment—subjects that are not decorative additions to a full-stack application. They help determine how the finished application actually works.

An application may need to answer questions like:

```text
Where does authentication state live?
How does the server know who made the request?
What information is safe to store in the browser?
How should credentials be transmitted?
What changes when the application leaves localhost?
```

Those decisions affect the frontend and backend at the same time. They can change how requests are sent, how routes are protected, how users remain logged in, and how the deployed system is configured.

Trying to build the project before receiving those lessons feels like being told to put together a thousand-piece puzzle while five pieces are still missing. A frustrating experience.

I understand the value of having two or three weeks for the personal project. Its a lot of work, and I want every one of those days. The problem isnt the amount of project time, its the overlap between learning the last pieces and trying to implement the whole curriculum.

Once the project begins, it immediately becomes the most immeditae priority. Every new lesson has to compete with an application that already has unfinished routes, components, errors, and decisions waiting for me.

```text
Listen to the lesson
        ↕
Think about the project
        ↕
Realize the lesson changes the project
        ↕
Want to open the project immediately
```

That makes it harder to focus on the lesson, even when the lesson contains something I need.

## Learn the System, Then Build the System

The cleaner sequence seems obvious to me:

```text
finish the core curriculum
          ↓
choose and plan the personal project
          ↓
build for two or three uninterrupted weeks
```

Finish the instruction, then put it into practice.

That doesnt mean a student will know everything before starting. No project works that way. Building always reveals new gaps, and solving those gaps is part of software engineering. But theres a difference between discovering a project-specific problem and waiting for a scheduled lesson on a core part of the stack.

The personal project should test whether I can combine everything Ive learned. It should still require research, debugging, judgment, and probably several wrong turns. What it should not require is guessing how an unfinished portion of the curriculum will eventually tell me to connect the system.

This is the teacher in me talking.

Ive built curriculum before, therefore I know that sequence changes how students experience difficulty. The same assignment can feel challenging in a productive way or frustrating in an avoidable way depending on when it arrives.

Productive difficulty says:

> You have the tools. Now decide how to use them.

Avoidable difficulty says:

> Start building now. We will give you the rest of the tools while you work.

Both situations are hard, but only one lets the student focus completely on integration.

## The Weekend Fight

The good news is Im through the lessons now. The pieces I was waiting for have arrived.

I have notes on the architecture, the features, the security concepts, and the deployment process. Ive already started building. Theres no longer a lecture on the calendar that I need before I can understand the shape of the finished product.

Now the problem is execution.

And thats a problem I know how to solve: clear the time, sit down, hold the project in my head, and work through one connection after another.

```text
build → run → inspect → fix → repeat
```

I expect the weekend to be long and for the frontend and backend to disagree about data, authentication to fail for a reason that initially makes no sense, and deployment to expose an assumption that localhost politely ignored.

Thats the FIGHT.

But once its done Ill have taken an idea from a blank repository to a complete application using the full stack I came here to learn.

There are only a few weeks left in Code Platoon, and I can feel the end approaching. Before I get there, I have one large piece of evidence left to produce.

The notes are ready. My body is ready. 
The final boss is waiting.

Here we go.
