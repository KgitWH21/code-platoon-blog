---
date: 2026-07-21
categories:
  - Code Platoon
authors:
  - keith
tags:
  - python
  - react
  - cypress
  - react-router
  - codewars
  - code-platoon
---

# Count First, Render Second

The day's first expedition, opening my computer launched me into week eight, day thirty-four, through the fog of a React page I had no idea how to begin, toward a Codewars problem waiting to be cracked, and past both once I stopped attempting to produce the answer before modeling the problem—forward.

<!-- more -->

---

## I Was Able to Solve It

Today's Codewars problem was the Duplicate Encoder. Given a string, the function has to return a new string where every character becomes `"("` if it appears only once in the original string and `")"` if it appears more than once. Capitalization doesn't matter.

```text
"din"      => "((("
"recede"   => "()()()"
"Success"  => ")())())"
"(( @"     => "))(("
```

My first instinct was to build a set to deal with the duplicates. A set is useful when the only question is whether a value exists, because it removes duplicate values automatically. But that is also the problem here: once the duplicates are gone, the set no longer tells me how many times each character originally appeared.

What I actually needed was a frequency dictionary.

```python
def duplicate_encode(word):
    new_string = ""
    word = word.lower()
    counts = {}

    for ltr in word:
        counts[ltr] = counts.get(ltr, 0) + 1

    for char in word:
        if counts[char] == 1:
            new_string += "("
        else:
            new_string += ")"

    return new_string
```

The first loop builds the model of the original word. This line does most of the work:

```python
counts[ltr] = counts.get(ltr, 0) + 1
```

`counts.get(ltr, 0)` asks for the current count and gives back zero if the character has not been seen before. Then it adds one and stores the result. After reading `"recede"`, the dictionary looks like this:

```python
{
    "r": 1,
    "e": 3,
    "c": 1,
    "d": 1,
}
```

The second loop walks through the original word again. This matters because the output has to preserve the original order. For each character, the function checks the dictionary and appends the correct parenthesis.

The two data structures answer different questions. A set can tell me, "Have I seen this character?" A frequency dictionary can tell me, "How many times does this character appear?" The problem was asking the second question.

And I was able to solve it. That felt good.

## A Mental Model for Fetching Data in React

I also spent time building out a React page for a Rick and Morty themed project. When I first started setting it up, I had no idea how to begin. It is easy to stare at the finished page and see everything at once: the API request, the loading message, an error, the returned characters, the routes, and the tests. That makes the page feel like one large problem.

The reusable mental model is much smaller. For a page that fetches and displays data, I can think in three parts.

First, model every state the data can be in. Usually the page is waiting for data, something went wrong, or the data arrived successfully:

```jsx
const [characters, setCharacters] = useState([]);
const [loading, setLoading] = useState(true);
const [error, setError] = useState(null);
```

Those variables are not three unrelated bits of setup. They represent the situations the interface needs to explain to the user.

Second, put the fetch inside `useEffect`. Fetching is a side effect: it reaches outside the component, waits for something external, and then updates state when the result comes back.

```jsx
useEffect(() => {
  async function getCharacters() {
    try {
      const response = await fetch("/api/characters");

      if (!response.ok) {
        throw new Error("Unable to load characters");
      }

      const data = await response.json();
      setCharacters(data);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }

  getCharacters();
}, []);
```

The fetch doesn't manually put a spinner or an error message on the page. Its job is to update state. React sees those updates and renders again.

Third, make the return statement a decision tree over those states:

```jsx
if (loading) return <p>Loading...</p>;
if (error) return <p>{error}</p>;

return <CharacterList characters={characters} />;
```

This early-return pattern gives the states a clear priority. If the request is still running, show the loading interface. If it finished with an error, show the error. By the time execution reaches the final return, the component is on the success path and can render the actual data.

That is the basic recipe I can reuse:

1. Create state for the data, loading, and error.
2. Fetch the data in `useEffect`, then update the appropriate state.
3. Use early returns for loading and error before rendering the successful result.

The dependency array is the main part that changes between pages. An empty array means the effect runs once when the component mounts. A details page might use `[id]` so it fetches again whenever the route parameter changes.

Once I could see the pattern, the page stopped being one giant React problem. It became a small state machine.

## Time to Hook Things Up and Watch Them Fail

Today was the first day in weeks when I felt like I got a nice amount of work done. The afternoon review was optional, so I had a block from about 11:30 to 3:00 to work on the assignment at my own pace.

It was great.

I learn better when I have time to read, test my program, and follow my own study methodology. Usually I am moving back and forth between a lesson and trying to understand it before the schedule moves again. Today I had time to hook things up and see where stuff failed.

That made a difference. I learned more about Cypress by watching what the tests expected from the page. I learned more about React Router DOM by connecting routes instead of only reading about them. I could make a change, run the program, observe the result, and adjust my mental model while the evidence was still in front of me.

That cycle is how the abstractions become real. A route isn't just a `<Route>` element once I have watched the URL select a component. Loading state isn't just another `useState` call once I have seen a Cypress test wait for one interface and then expect another. The pieces start to feel connected because I have enough time to watch the transitions.

The Codewars solution followed the same general pattern. Before producing parentheses, I built a dictionary that described the word. Before producing JSX, I built state that described the page. In both cases, the output became straightforward after the program had an accurate model of what was happening.

Week eight, day thirty-four, done. React is finished for now, and this was a nice, chill workday—the kind where having a little room to move slowly actually let me learn faster.
