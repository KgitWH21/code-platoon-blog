---
date: 2026-08-24
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - codewars
  - html
  - product-demo
  - video-production
  - final-project
  - code-platoon
---

# The Demo Is Part of the Product

After years of making videos, gone was the old hesitation; instinct carried my hand toward the red Record button. By the time I finished the presentation, the app no longer felt like a pile of React, Django, authentication, and API calls. It felt like a product with a reason to exist.

<!-- more -->

---

Code Platoon Week 13, Day 1. There are only three weeks left.

Today was a big day because I finished the presentation for my final project. Recording it took most of my working day, even though the final video is only 9 minutes and 59 seconds long (1 second shorter than the max length).

That ratio sounds ridiculous until the video is a technical demo.

## A Two-Dimensional Array Becomes a Table

Before getting into the presentation, today's Codewars problem asked me to turn a two-dimensional array into an HTML table.

The function takes three arguments:

- `data`, the array of rows
- `headers`, which promotes the first row into a table header
- `index`, which adds a one-based row number as the first column

Given ordinary data, the structure should look like this:

```javascript
toTable([
  ["Keith", "Writer"],
  ["Ada", "Programmer"]
]);
```

```html
<table><tbody><tr><td>Keith</td><td>Writer</td></tr><tr><td>Ada</td><td>Programmer</td></tr></tbody></table>
```

With both optional arguments enabled, the first row belongs in `<thead>`, the remaining rows belong in `<tbody>`, and the index column needs an empty heading:

```javascript
toTable(
  [
    ["Name", "Role"],
    ["Keith", "Writer"],
    ["Ada", "Programmer"]
  ],
  true,
  true
);
```

The solution is:

```javascript
function toTable(data, headers = false, index = false) {
  const cell = (value) => (value == null ? "" : String(value));

  let html = "<table>";
  let start = 0;

  if (headers) {
    html += "<thead><tr>";

    if (index) {
      html += "<th></th>";
    }

    for (const value of data[0]) {
      html += `<th>${cell(value)}</th>`;
    }

    html += "</tr></thead>";
    start = 1;
  }

  html += "<tbody>";

  for (let row = start; row < data.length; row++) {
    html += "<tr>";

    if (index) {
      html += `<td>${row - start + 1}</td>`;
    }

    for (const value of data[row]) {
      html += `<td>${cell(value)}</td>`;
    }

    html += "</tr>";
  }

  html += "</tbody></table>";
  return html;
}
```

The `cell()` helper handles an easy-to-miss requirement. Both `null` and `undefined` need to become empty cells, while values such as `0` and `false` still need to appear. A truthiness check would erase those valid values. `value == null` is useful here because it catches exactly `null` and `undefined`, and `String(value)` makes every other value safe to concatenate as text.

The `start` variable keeps the header decision from infecting the rest of the function. It begins at `0`, but moves to `1` when the first row has already been consumed by `<thead>`. The body loop can then use the same logic in either case.

That also explains the index calculation:

```javascript
row - start + 1
```

Whether the first body row is at array position `0` or `1`, its displayed index is always `1`.

This problem was mostly about translating one structure into another. The outer array becomes rows. Each inner array becomes cells. The two booleans change where iteration begins and whether each row gets one extra cell.

## Ten Minutes Takes All Day

Producing a video is easy in the most literal sense. Turn a phone around and hit record. But producing a good technical demo is different.

Theres recording, editing, timing, sequencing, and the learning curve of making all of it feel intentional. When Im demonstrating a complicated app within a strict time limit, I cant just ramble. I need to be direct, succinct, and clear while making sure I cover everything that matters.

Theres also a performance inside the performance. Im speaking to the audience, but Im also simultaneously moving through the application, clicking the right controls, and making sure the screen supports the sentence Im saying at that exact moment.

I prefer to record the narration and demonstration together. That creates an extra burden, but live streaming taught me how to manage several streams of attention at once. Even with that experience—and even after producing another technical project presentation earlier in the course—it still took several hours to get this one where I wanted it.

I wrote a script, filmed the demo, edited the clips, then created music for it. I also added light transition work so the whole thing looked clean. Then I watched it all the way through. The final cut felt more like a product pitch than a student presentation, which was exactly what I was going for.

## A Better Frame

The most important choice wasnt technical. It was the frame.

I didnt want to present myself as a student who had made an app for an assignment. So I pretended I was pitching the app product to an investor, a buyer, or somebody who might actually use it.

That one mental shift made a massive difference.

Im passionate about words (I maintain my own grwoing dictionary archive on my website). Im passionate about writing. This app is an expression of those interests. The code, authentication, frameworks, React, and Django arent the main point. Theyre the means to an end. The point is to make something that might encourage somebody to learn more words and keep the words they discover.

My pitch had to answer the obvious question: why do we need another dictionary app?

There are already countless dictionaries on the web. Most of them are good at answering a single query. You look up a word, read the definition, and leave. The gap I built around is personalization. My application is not only a place to retrieve a definition; its a place to collect words and build a relationship with them.

At first, I felt like I was making a big deal out of a student project because that was what the presentation required. Yet after working through the script and recording the pitch, I started to believe it. When I watched the finished presentation, I thought: *I kind of believe in this app.*

That doesnt mean I plan to turn it into a company or keep developing it after the course. It means I found the honest version of the story. A professional presentation didnt make the underlying project less real. It made me articulate what had been real about it all along.

## The Finished Presentation

<div class="video-embed">
  <iframe
    src="https://www.youtube-nocookie.com/embed/LY8ry7CXmsc"
    title="Final project presentation"
    loading="lazy"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    referrerpolicy="strict-origin-when-cross-origin"
    allowfullscreen>
  </iframe>
</div>

[Watch the presentation on YouTube](https://youtu.be/LY8ry7CXmsc).

Im proud that I produced a professional-level presentation for a student project. I could have introduced myself, clicked through the features, and called it complete. Instead, I treated the demo as part of the product.

## Three Weeks Left

Now that the individual project and its presentation are done, I can feel the senioritis arriving.

The daily algorithm problems are getting longer just as my enthusiasm for them is getting shorter. I know why they matter, and if I decide to pursue a traditional developer job, I can spin back up on that kind of practice. Right now, though, its hard to care about another abstract problem when the larger build is finished and the end of the program is visible.

Im working to stay motivated and finish strong. The group project is next, and after that, Code Platoon is done.

Three weeks left. Bring it on.
