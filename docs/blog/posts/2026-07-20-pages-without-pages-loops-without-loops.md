---
date: 2026-07-20
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - react
  - react-router
  - codewars
  - code-platoon
---

# Pages Without Pages, Loops Without Loops

Sitting in my chair at the start of week eight, I met two familiar strangers—React Router's pages without pages and Python's loops without loops—as a crack opened under one leg.

<!-- more -->

---

## Linking Up Pages That Aren't Pages

Today's React lesson was React Router DOM. It wasn't a very tough concept, partly because I did the reading beforehand and partly because the basic idea already felt familiar. We are basically linking up HTML pages.

They're not HTML pages, exactly, but that is the closest existing model in my head.

In a traditional site, clicking a link asks the server for another document. The browser leaves one HTML page and loads the next one. A React application usually starts with one HTML document, then React changes what gets rendered inside it. React Router watches the URL and decides which component tree belongs there.

The basic shape looks something like this:

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/posts/:postId" element={<Post />} />
      </Routes>
    </BrowserRouter>
  );
}
```

`BrowserRouter` connects the application to the browser's address bar and history. `Routes` holds the available route definitions. Each `Route` pairs a path with the element React should render when the URL matches it.

So `/about` feels like a separate page to the user, but React hasn't loaded a separate `about.html` file. It has matched a string and rendered the `About` component. The URL still matters. The back button still matters. A user can still bookmark a location. The implementation underneath those familiar behaviors is what changed.

That is probably why the syntax wasn't too bad. Routing isn't a new user-facing idea. React Router is a new way to express something I already understand from ordinary websites. It is another piece of the React puzzle, and working ahead meant I could recognize the shape before the short lecture had to carry all of it.

## My Slice Was Right, but My Conversion Was Wrong

The Codewars problem asked for a function that accepts ten integers between zero and nine and returns them in phone-number format:

```python
create_phone_number([1, 2, 3, 4, 5, 6, 7, 8, 9, 0])
# "(123) 456-7890"
```

I was hoping to do it in one line. My first attempt was:

```python
def create_phone_number(n):
    return "".join("(" + str(n[0:3]) + " " + str(n[3:6]) + "-" + str(n[6]))
```

The slicing instinct was right. The problem was what happened after the slice.

`n[0:3]` returns a list:

```python
[1, 2, 3]
```

Calling `str()` on that list does not convert each integer and quietly press the results together. It converts the representation of the entire list into a string:

```python
"[1, 2, 3]"
```

The brackets, commas, and spaces are now part of the value. Python did exactly what I asked; I had asked at the wrong level. I needed to convert every item inside the collection, not the collection itself.

There was another small issue hiding in the attempt: the outer `"".join()` wasn't doing useful work. By the time all those pieces had been combined with `+`, the argument was already one string. `join()` is useful when it receives an iterable of separate strings. Handing it one completed string only makes it iterate over the characters and join them back together with nothing between them.

A clearer solution is to convert the numbers first and format the result second:

```python
def create_phone_number(n):
    s = "".join(map(str, n))
    return f"({s[0:3]}) {s[3:6]}-{s[6:10]}"
```

Once `s` is `"1234567890"`, slicing behaves exactly the way I originally wanted. The punctuation is then easy to see, including the required space after the closing parenthesis.

## `map()` Is a Loop With the Ceremony Removed

The unfamiliar part of that solution was this line:

```python
s = "".join(map(str, n))
```

`map()` takes a function and an iterable:

```python
map(function, iterable)
```

It applies the function to each item produced by the iterable. In this case, the function is Python's built-in `str`, and the iterable is the list of integers. Conceptually, `map(str, n)` does this:

```python
string_numbers = []

for number in n:
    string_numbers.append(str(number))
```

Given `[1, 2, 3]`, it produces the values `"1"`, `"2"`, and `"3"`. Then `"".join(...)` consumes those strings and places an empty separator between them, producing `"123"`.

The important Python 3 detail is that `map()` doesn't immediately build and return a list. It returns an iterator—a `map` object that produces each converted value when something requests it. If I print the object directly, I won't see a convenient list of strings. If I want one, I have to explicitly create it:

```python
list(map(str, [1, 2, 3]))
# ["1", "2", "3"]
```

But `join()` already knows how to consume an iterable, so creating that intermediate list would be unnecessary here. `join()` asks for each value, `map()` supplies the string version, and the final phone-number string gets built.

There is also a genuinely clean one-line solution using unpacking:

```python
def create_phone_number(n):
    return "({}{}{}) {}{}{}-{}{}{}{}".format(*n)
```

The `*` unpacks the list, turning its ten elements into ten separate arguments for `format()`. Each value drops into the next pair of braces. It is shorter, but the `map()` version taught me more because it exposed exactly what was wrong with my first attempt: I understood where the digits needed to go, but I hadn't yet separated transforming a collection from transforming each value inside it.

## A Short Day Can Still Add a Useful Piece

Today wasn't as productive in terms of raw individual work time. The morning lectures were short, and the afternoon was broken up by other parts of the program, leaving maybe another thirty minutes to work before stand down. That is fine. I got what I needed from the day.

React Router gave me another piece of the React model: the browser can behave as though it is moving between pages while React is really choosing between components. `map()` gave me a more precise way to think about collections: a function can be applied one item at a time without me writing the loop myself.

Those two ideas have a similar shape. React Router preserves the familiar experience of moving between pages while hiding the machinery that selects components. `map()` preserves the familiar behavior of a loop while hiding the machinery that visits each item. Neither removes the underlying process. Each gives it a smaller, more declarative interface.

Week eight, day thirty-three, done. Nothing especially dramatic—just one more piece of React, one useful Python function, and one less day between here and the end.
