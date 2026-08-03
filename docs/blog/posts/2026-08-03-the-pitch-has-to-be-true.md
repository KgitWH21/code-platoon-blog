---
date: 2026-08-03
categories:
  - Code Platoon
authors:
  - keith
tags:
  - javascript
  - codewars
  - django
  - django-rest-framework
  - artificial-intelligence
  - career-development
  - software-engineering
  - code-platoon
---

# The Pitch Has to Be True

Lost was the confidence I thought had defined me; found was the voice I thought had abandoned me. Again.

<!-- more -->

---

## Find the Capitals

Today's Codewars problem was simple to state: given a non-empty string containing only lowercase and uppercase ASCII letters, return an ordered list of the indices of every capital letter.

```text
"CodEWaRs" → [0, 3, 4, 6]
```

My first attempt looked like this:

```javascript
var capitals = function (word) {
  let arr = [];

  for (const [i, ltr] of word) {
    if (isUpperCase(ltr)) {
      arr.push(i);
    }
    arr.push(i);
  }

  return arr;
};
```

There were three problems hiding inside a very small function.

First, a string's normal iterator yields one character at a time. It doesn't yield index-character pairs. Destructuring each character with `[i, ltr]` doesn't turn it into an entry. For a one-character string, `i` becomes that character and `ltr` becomes `undefined`.

If I want the index, I can use a standard indexed loop:

```javascript
for (let i = 0; i < word.length; i++) {
  const ltr = word[i];
}
```

I could also use `word.split("").entries()`, but a normal loop is the clearest fit here.

Second, JavaScript doesn't have a global `isUpperCase()` function. Since the instructions guarantee that every character is a letter, I can compare the character with its uppercase version:

```javascript
word[i] === word[i].toUpperCase()
```

Third, I pushed the index twice. The second `arr.push(i)` was outside the condition, so it would add every index whether the letter was uppercase or not.

The corrected solution is:

```javascript
var capitals = function (word) {
  const indices = [];

  for (let i = 0; i < word.length; i++) {
    if (word[i] === word[i].toUpperCase()) {
      indices.push(i);
    }
  }

  return indices;
};
```

The important part isn't the capitalization check. It's keeping the character and its position connected. The function doesn't need to return the capitals. It needs to return where the capitals are.

```text
character: C o d E W a R s
index:     0 1 2 3 4 5 6 7
keep:      0     3 4   6
```

Once again, an index problem forced me to ask what the loop was actually giving me instead of trusting what I'd named the variables.

## One Resource, One ViewSet

Code Platoon Week 10, Day 42 moved into CRUD operations with Django REST Framework. I researched ViewSets because they are supposed to make life easier, and now I understand what that means.

A ViewSet groups the API operations for one resource into one class.

For an `Asset` resource, the standard operations are:

| Request | Endpoint | Operation |
| --- | --- | --- |
| `GET` | `/assets/` | List assets |
| `POST` | `/assets/` | Create an asset |
| `GET` | `/assets/3/` | Retrieve asset 3 |
| `PUT` | `/assets/3/` | Replace asset 3 |
| `PATCH` | `/assets/3/` | Partially update asset 3 |
| `DELETE` | `/assets/3/` | Delete asset 3 |

Without a ViewSet, I might create separate list and detail views, then manually connect both of them to URL patterns. With `ModelViewSet`, I can describe the resource once:

```python
from rest_framework import viewsets

from .models import Asset
from .serializers import AssetSerializer


class AssetViewSet(viewsets.ModelViewSet):
    queryset = Asset.objects.all()
    serializer_class = AssetSerializer
```

Then I register it with a router:

```python
from django.urls import include, path
from rest_framework.routers import DefaultRouter

from .views import AssetViewSet


router = DefaultRouter()
router.register(r"assets", AssetViewSet)

urlpatterns = [
    path("", include(router.urls)),
]
```

Those few lines generate the standard collection and detail routes. `ModelViewSet` already supplies six actions:

```text
list
create
retrieve
update
partial_update
destroy
```

The router connects the HTTP method and URL shape to the correct action. A `GET` request to `/assets/` means `list()`. A `GET` request to `/assets/3/` means `retrieve()`. A `PATCH` request to `/assets/3/` means `partial_update()`.

That distinction clarified ViewSets for me. A regular `APIView` often has methods named after HTTP verbs, such as `get()` and `post()`. A ViewSet describes actions: list a collection, retrieve an item, create a record, or destroy it. The router handles the translation.

## The CRUD Coordinator

The ViewSet doesn't replace the rest of Django REST Framework. The pieces still have different jobs:

```text
Model → stores the data
Serializer → validates and translates the data
ViewSet → coordinates the available operations
Router → generates URLs and connects requests to actions
```

The two attributes in the minimal ViewSet answer the questions the inherited CRUD actions need answered:

```python
queryset = Asset.objects.all()
serializer_class = AssetSerializer
```

The `queryset` says which database objects this endpoint manages. The `serializer_class` says how input should be validated and how output should be represented.

That's enough for the default behavior, but I'm not trapped inside it. I can filter the records by overriding `get_queryset()`:

```python
class AssetViewSet(viewsets.ModelViewSet):
    serializer_class = AssetSerializer

    def get_queryset(self):
        queryset = Asset.objects.all()
        category = self.request.query_params.get("category")

        if category:
            queryset = queryset.filter(category=category)

        return queryset
```

I can preserve the standard create flow while changing what gets saved:

```python
def perform_create(self, serializer):
    serializer.save(is_available=True)
```

I can also create a resource-specific endpoint with `@action`:

```python
from rest_framework.decorators import action
from rest_framework.response import Response


@action(detail=True, methods=["post"], url_path="check-out")
def check_out(self, request, pk=None):
    asset = self.get_object()
    asset.is_available = False
    asset.save()

    serializer = self.get_serializer(asset)
    return Response(serializer.data)
```

The router turns that into:

```text
POST /assets/{pk}/check-out/
```

`detail=True` means the action belongs to one asset. `detail=False` would mean it belongs to the collection.

The mental model I'm keeping is that a ViewSet is one controller for one API resource. It owns the standard interactions with that resource, while the model, serializer, and router continue doing their own work. It's the CRUD coordinator.

## The Poor Elevator Pitch

The more difficult lesson today had nothing to do with Django.

We had to deliver one-minute elevator pitches on Zoom. I hadn't prepared because I assumed there'd be more instruction before we had to speak. Then I volunteered to go first, which I often do because I like speaking.

It went poorly.

I read the pitch. I was nervous. The nervousness showed. I became frustrated with myself while I was still speaking, which made the rest of the pitch worse. Nobody had to tell me it was bad, because I knew I hadn't represented myself to personal standards.

Several conditions hit weak spots at the same time:

- I hadn't rehearsed.
- I was being timed, and I hate being rushed.
- Zoom gives me almost none of the realtime audience feedback I use in person.
- The audience was an amorphous collection of squares rather than someone I could actually address.
- Most importantly, I didn't believe the pitch.

The pitch I wrote described me as a software-engineering graduate looking for a conventional software-engineering job. That's technically plausible, but it's not how I introduce myself, and it's not the future I'm trying to build.

Usually, I begin with the thing that's both true and distinctive: I'm a novelist. More specifically, I'm a digital novelist. That gives someone a reason to lean in. It also creates a natural path into the technical work instead of making me pretend the technical work erased everything that came before it.

Reading the generic pitch, I could hear myself being flattened in real time. I was presenting myself as one more junior developer listing JavaScript, React, Python, Django, PostgreSQL, and Docker—the same stack everyone in the program is learning. Those skills matter, but the list isn't the story.

## The Software Is an Extension of the Story

My actual story is that I've maintained my own website and publishing business for nearly ten years. I built a book-production workflow with Typst, a programmable typesetting language, that turns my writing into professionally designed print books. I run a company. I've taught, written books, produced media, and repeatedly learned unfamiliar systems because I needed them to do something real.

Software engineering extends that work.

AI belongs in the story too, but not as the magic word I insert because every software company wants to hear it. I've used frontier and local open-source models across coding, publishing, research, and creative production. I use AI to plan, prototype, debug, review code, test ideas, and document systems. More importantly, I inspect what it produces, verify whether it works, and adapt it to the actual problem.

That's a stronger claim than saying AI writes code for me. I'm an AI-fluent software developer. AI is part of the engineering workflow, not a substitute for having one.

The revised pitch is something I can actually practice because it's something I can actually believe:

> Hi, I’m Keith Hayden. I’m a digital novelist, entrepreneur, and AI-fluent software developer. For nearly ten years, I’ve maintained my own website and publishing business, and I’ve increasingly used software and AI to turn creative ideas into working systems.
>
> Recently, I built an AI-assisted book-production workflow using Typst, a programmable typesetting language, that transforms my writing into professionally designed print books. I’m also completing Code Platoon’s full-stack engineering program, where I’ve built applications using JavaScript, React, Python, Django, PostgreSQL, and Docker.
>
> I’m especially interested in helping small businesses and independent creators turn rough ideas, inefficient processes, or unfinished software into practical tools they can actually use. 
> Hire me.

That's what I should have delivered. A pitch that hooks and still establishes technical credibility. It simply puts the technology inside a life instead of trying to replace the life with a technology list.

It also gives the listener several threads to pull:

```text
What is a digital novelist?
What did you build with Typst?
How do you use AI in development?
What kind of tools do small businesses need?
```

That's what I want the pitch to do. I don't want to compress my entire background into sixty seconds. I want to give someone a reason to ask the next question. Once the performance becomes a conversation, I'm in much stronger territory.

## Rehearsing the Container

The one-minute limit still matters. In a real conversation, I can be funny, charming, and responsive. If the story is compelling, people lean in. A timed Zoom exercise removes most of those advantages.

I can't change the medium next week, but I can train for it.

I'm going to treat the pitch like a performance rather than a paragraph:

1. Remember the route: digital novelist, engineering and AI, useful software for small businesses and creators.
2. Practice it aloud without reading.
3. Record repeated versions until the timer stops feeling like an ambush.
4. Look into the camera instead of monitoring my own little surveillance image.
5. Imagine I'm speaking to one technically curious Las Vegas small-business owner, not a dozen squares.

The timer can become a container I've rehearsed inside instead of a threat deciding when I'm finished.

This is also why the bad performance was useful. I don't merely need a better classroom answer. I need a pitch I can use when I begin presenting myself to small businesses in Las Vegas as someone who can build their software. Next week gives me a clean chance to practice that real skill inside an artificial situation.

## An Uneventful Day Still Counts

The elevator pitch carried most of the emotional weight today. The rest of Day 42 wasn't especially momentous.

The normal class schedule broke up after lunch, which gave me more time to work through assignments and practice the backend material. That was probably the right kind of work for the condition I was in. I'd awakened around two in the morning, and by the afternoon I was dead tired.

Backend development already requires me to hold several connected systems in my head:

```text
Django settings
models and migrations
databases
serializers
views and ViewSets
routers and URLs
authentication
Docker
```

When I'm exhausted, those connections become even more slippery. A command, class, or file may make sense on its own, but it's harder to remember where it belongs in the complete request-and-response path.

Today didn't need another major breakthrough. It became a repetition day. I kept touching the machinery, working through the assignments, and trying to make the backend workflow more familiar.

That counts.

Not every day of an intensive program needs a dramatic lesson, finished project, or triumphant conclusion. Sometimes the useful thing is simply receiving a few quieter hours to revisit complicated material. Given how little sleep I'd had and how much frustration the pitch created, staying engaged was enough.

Tomorrow doesn't need to be spectacular either. A normal night of sleep and another clean pass through the backend workflow would already make it better.

## Return the Right Identity

Today's lessons were all about putting the correct responsibility in the correct place.

In Codewars, the loop must preserve the relationship between a capital letter and its index. Naming a character `i` doesn't make it an index.

In Django REST Framework, the ViewSet coordinates the operations for one resource. The model stores, the serializer validates, the router connects, and the ViewSet decides what can be done.

In the pitch, the technology stack is evidence. It's not the identity.

```text
novelist + entrepreneur + AI + software engineering
                         ↓
              practical working systems
```

My first Codewars function returned the wrong values because I misunderstood what the iterator supplied. My first elevator pitch returned the wrong version of me because I misunderstood what the exercise required. I thought professionalism meant leading with the same stack as everyone else and pretending to want the same destination.

It doesn't.

The challenge is to compress the story without falsifying it. I can present myself as a professional software developer because I have the training, the tools, and the projects to support that claim. I don't have to erase the novelist, the teacher, the entrepreneur, or the decade of publishing work to make the claim legible.

The pitch has to fit inside a minute, but it still has to be true.

Day forty-two: uneventful, exhausted, still moving.
