---
date: 2026-07-29
categories:
  - Code Platoon
authors:
  - keith
tags:
  - django
  - django-rest-framework
  - api
  - serializers
  - validation
  - docker
  - javascript
  - codewars
  - code-platoon
---

# The API Is the Contract

I was certain I was building the backend by brute force, blindly bouncing between its files, until the ghost living at localhost slid the contract under my door.

<!-- more -->

---

## Zero to One Hand

Today's Codewars problem simulated touch typing on a simplified keyboard. The function receives a reusable iterable of lowercase letters and has to decide whether the word uses only the left hand, only the right hand, both hands, or no hands because the iterable is empty.

The keyboard is divided like this:

```text
Left hand:  qwert asdfg zxcvb
Right hand: yuiop hjkl nm
```

My first attempt stored each side as an array of keyboard rows:

```javascript
const leftHand = ["qwert", "asdfg", "zxcvb"];
const rightHand = ["yuiop", "hjkl", "nm"];

for (let ltr of word) {
  if (ltr in leftHand) {
    // ...
  }
}
```

The problem was `in`.

In JavaScript, `in` does not ask whether an array contains a value. It asks whether an object has a particular property key. With an array, that usually means checking indices such as `0`, `1`, or `2`. The letter `"a"` is inside one of the strings in `leftHand`, but it is not an index or property on the array.

What I actually needed was a membership test. A `Set` makes that relationship explicit:

```javascript
const leftHand = new Set("qwertasdfgzxcvb");
const rightHand = new Set("yuiophjklnm");
```

Now I can ask the exact question I mean:

```javascript
leftHand.has(ltr)
```

The rest of the solution depends on two boolean flags:

```javascript
function whichHand(word) {
  const leftHand = new Set("qwertasdfgzxcvb");
  const rightHand = new Set("yuiophjklnm");

  let useLeft = false;
  let useRight = false;
  let isEmpty = true;

  for (const ltr of word) {
    isEmpty = false;

    if (leftHand.has(ltr)) {
      useLeft = true;
    } else if (rightHand.has(ltr)) {
      useRight = true;
    }

    if (useLeft && useRight) {
      return BOTH;
    }
  }

  if (isEmpty) return NONE;
  if (useLeft) return LEFT;
  if (useRight) return RIGHT;
}
```

Each flag remembers a fact about everything the loop has seen so far. The function does not need to store the letters or count them. It only needs to know whether at least one letter from each side has appeared.

The early return is more than a minor optimization. The iterable can be infinite. The kata guarantees that an infinite input will have a finite prefix containing letters from both sides, so the only safe way to handle it is to return `BOTH` as soon as both flags become true. Trying to finish the iterable first could mean never returning at all.

Then I made the least interesting but most JavaScript kind of mistake:

```javascript
const Hand = [NONE, LEFT, RIGHT, BOTH];

return HAND[3];
```

`Hand` and `HAND` are different identifiers. JavaScript is case-sensitive, and it does not care that a human can plainly see what I meant.

There was no need to redeclare the array anyway. The kata had already preloaded `NONE`, `LEFT`, `RIGHT`, and `BOTH`, so the cleanest solution was to return those constants directly.

The takeaway was `.has()`, boolean flags, and early returns—and don't be an idiot when declaring variables in JavaScript.

Or, more generously, remember that some of the most frustrating errors are not failures of logic. Sometimes the algorithm is fine and one capital letter has sabotaged the entire operation.

## Docker Is the Musician; Compose Is the Conductor

The day's larger project began with:

```bash
docker compose up --build
```

That command rebuilds the images for services that have a `build:` instruction in the Compose file, creates the necessary containers and network, and starts the application. Services that specify an existing image, such as PostgreSQL, may be downloaded rather than built locally.

Docker and Docker Compose are related, but they operate at different levels.

Docker works with individual images and containers. It can build the Django image, start the PostgreSQL container, stop the React container, or open a shell inside one running container.

Docker Compose coordinates the group:

```text
Docker
├── Django container
├── React container
└── PostgreSQL container

Docker Compose
└── Defines how they build, start, connect, and run together
```

Compose does not replace Docker. It uses Docker underneath. Docker handles the individual musicians; Compose conducts the band.

Current Docker installations include Compose as a plugin, so I do not need a separate program. The modern command uses a space:

```bash
docker compose up
```

The older standalone version used a hyphen:

```bash
docker-compose up
```

I also briefly thought I needed to stop everything because I had forgotten to activate my virtual environment before starting Compose. I did not.

My local `.venv` and the Python environment inside the Django container are separate:

```text
Local machine                 Django container
-------------                 ----------------
.venv                         Python in the image
local pip packages            packages from requirements.txt
```

If the Dockerfile runs:

```dockerfile
RUN pip install -r requirements.txt
```

that installation happens inside the image. It does not install Django into my local virtual environment or my computer's global Python environment. The same idea applies when a frontend image installs its Node packages.

My source files may still live on my machine and be mounted into a container with a volume. In that arrangement, Docker uses my local code while keeping the runtime and dependencies inside the container.

That separation is one of the main reasons containers are useful. The application carries its environment with it instead of depending on whatever happens to be installed on the host computer.

## The Address Is Not the API

The question that kept bothering me today was more fundamental.

I know what an API means in theory: it is how software applications communicate and exchange data. But what code actually *is* the API?

Take this URL:

```text
http://localhost:8000/api/leads/
```

That is not the API code. It is the address of one endpoint.

In a Django REST Framework application, several pieces cooperate behind that address:

```text
GET /api/leads/
        ↓
urls.py matches the route
        ↓
views.py decides what to do
        ↓
models.py and PostgreSQL provide the records
        ↓
serializers.py converts them to JSON
        ↓
HTTP response returns to the client
```

The route connects the public address to Python code:

```python
urlpatterns = [
    path("api/leads/", LeadListCreateView.as_view()),
]
```

The view defines what requests are supported and what data to use:

```python
class LeadListCreateView(ListCreateAPIView):
    queryset = Lead.objects.all()
    serializer_class = LeadSerializer
```

The model defines what a lead is in the database:

```python
class Lead(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()
    message = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```

The serializer translates between Django's Python objects and the JSON another application expects:

```python
class LeadSerializer(serializers.ModelSerializer):
    class Meta:
        model = Lead
        fields = ["id", "name", "email", "message", "created_at"]
```

None of those files alone is the API. The API is the interface they collectively provide to the outside world.

Then the word that made the whole thing click appeared: **contract**.

An API can be understood as a digital contract between software systems. It establishes the rules of communication:

```text
Where do I send the request?
Which HTTP method may I use?
What fields must I provide?
What format must they use?
What response will I receive?
What happens when I violate the rules?
```

For the leads endpoint, part of the contract could be:

```text
GET /api/leads/
Returns: a JSON list of leads
Status: 200 OK

POST /api/leads/
Accepts: name, email, and message as JSON
Returns: the newly created lead
Status: 201 Created

Invalid input
Returns: validation errors
Status: 400 Bad Request
```

This explains why API stability matters. If the backend changes a response field from `email` to `user_email_address` without warning, the frontend may break even if every piece of backend code is working exactly as written. The implementation changed the terms of the agreement.

It also explains API documentation and versioning. Documentation records the terms. A path such as `/api/v2/leads/` can introduce a new agreement without silently rewriting the old one.

The endpoint is the address. The API is the contract. The backend code is the machinery that fulfills the contract.

That may be the cleanest mental model I have found for APIs so far.

## Validators Enforce the Terms

Validators fit naturally into that model. If the contract says a desired start date cannot be in the past, the validator enforces that clause:

```python
def validate_start_date(value):
    if value < timezone.localdate():
        raise ValidationError(
            "Desired start date cannot be in the past."
        )
```

Here, `value` represents the specific field value Django is validating. If the client submits:

```json
{
  "desired_start_date": "2026-09-15"
}
```

Django converts that string to a Python `date` and passes it to the validator. The function compares the submitted date with today's local date. If it is earlier, Django raises a `ValidationError` and rejects the data.

Conceptually, validators and serializers are simple.

Validators decide whether data satisfies the rules.

Serializers translate data between JSON and the Python or database-facing representation Django uses.

In an API understood as a contract, serializers define and translate part of the exchange while validators prevent invalid data from crossing the boundary.

The code can still look more complicated than the concept. That was the uncomfortable part today. I could explain what validation and serialization were doing, then open the sample project and wonder why the implementation seemed to have so many layers.

My response was to trust my process and start typing.

## Defaults With Selective Overrides

One small Python pattern from the tests was:

```python
def valid_lead_data(**overrides):
```

The double asterisk collects any named keyword arguments into a dictionary:

```python
valid_lead_data(
    name="Keith",
    email="keith@example.com",
)
```

Inside the function, `overrides` becomes:

```python
{
    "name": "Keith",
    "email": "keith@example.com",
}
```

That is useful in a test helper that starts with valid defaults:

```python
def valid_lead_data(**overrides):
    data = {
        "name": "Jane Smith",
        "email": "jane@example.com",
        "company": "Smith Consulting",
    }

    data.update(overrides)
    return data
```

Most tests can request ordinary valid data with:

```python
valid_lead_data()
```

A specific test can replace only the field it cares about:

```python
valid_lead_data(email="not-an-email")
```

The author could have called the dictionary `kwargs`, but `overrides` describes its purpose. The helper is saying: here is a complete valid request, but replace any default whose name I provide.

That is a small piece of code, but it makes tests much easier to read. Each test can emphasize the one contract term it is trying to verify.

## Configuration and Choices Belong Near the Model

I also got a clearer picture of two class-related Django patterns.

The first is the model's inner `Meta` class:

```python
class Meta:
    ordering = ["-created_at"]
```

This tells Django to order model objects by `created_at` descending unless a query requests some other order. The minus sign means descending, so the newest lead appears first.

`Meta` does not create a database field. It configures how the model behaves.

The second pattern is a nested choices class:

```python
class Lead(models.Model):
    class Service(models.TextChoices):
        WEBSITE = "website", "Website"
        AUTOMATION = "automation", "Automation"
        CUSTOM_SOFTWARE = "custom_software", "Custom software"
        APP_REPAIR = "app_repair", "Repair an AI-built app"
```

Python allows a class inside another class. Here, `Service` lives in the `Lead` namespace, so I access it as:

```python
Lead.Service.WEBSITE
```

Each choice contains a database value and a human-readable label:

```text
WEBSITE = "website", "Website"
          database    display
```

The model field can then use:

```python
service_needed = models.CharField(
    max_length=30,
    choices=Service.choices,
)
```

Nesting the choices communicates ownership. This is not some generic `Service` floating through the project. It is the set of services allowed for a `Lead`.

It also makes raw-string typos less likely:

```python
Lead.Service.WEBSITE
```

is safer and clearer than repeatedly typing:

```python
"website"
```

The database stores `"website"`, while Django can display `"Website"` to a person. Again, there is a boundary between representations, and the model defines how to cross it.

## The Environment I Was Actually Using

Not every problem today was conceptual.

When I ran:

```bash
python manage.py migrate
```

Python reported:

```text
ModuleNotFoundError: No module named 'django'
```

That failure happened before Django could inspect the migrations. My virtual environment was active, but Django was not installed inside that particular environment.

This was another boundary problem:

```text
installed globally ≠ installed in .venv
installed in another .venv ≠ installed in this .venv
installed in Docker ≠ installed on the host
```

Using:

```bash
python -m pip install -r requirements.txt
```

ties `pip` to the exact Python interpreter invoked by `python`. Commands such as `which python` and `python -m pip --version` can confirm that both paths point into the expected `.venv`.

Then dependency installation found another mismatch. The requirements pinned:

```text
psycopg[binary]==3.2.9
```

but that binary package was not available for my Python version and platform. Pip could see compatible releases beginning at `3.2.10`, so the pinned dependency had to be updated before installation could complete.

None of that had anything to do with whether `makemigrations` should come before `migrate`.

`makemigrations` compares models with existing migration history and creates new migration files. `migrate` applies migration files that already exist. A project can run `migrate` without first running `makemigrations` when the necessary migrations have already been created, including Django's built-in migrations.

My actual problem was earlier in the chain. Django itself could not be imported.

The error was not asking a database question yet. It was asking whether I knew which Python environment I was actually using.

## One Complete Project at a Time

Week nine, day thirty-nine, done.

I continued the strategy I started earlier this week: build a complete project around the day's lesson.

Today it was a lead intake application. That forced me through the whole process instead of showing me validators and serializers as isolated snippets:

```text
blank project
      ↓
Dockerfile and Compose services
      ↓
Django and PostgreSQL
      ↓
models and migrations
      ↓
validators and serializers
      ↓
API requests and responses
      ↓
tests for the contract
```

While watching the setup process from the beginning today, I could mentally keep up. I knew why the containers were being created, where the database fit, why Django needed migrations, and what the serializer was doing between the model and the response.

That tells me this strategy is working.

Building one project a day sounds excessive, but why not? I am already incorporating the lesson. The difference is that I am seeing each concept inside a functioning full-stack application instead of pretending that a validator exists alone in space.

There is something to the muscle memory of writing the code, even when I begin by copying a correct example. Reading a file can create the impression that I understand it. Rewriting it forces me to encounter every nested class, every keyword argument, every indentation level, and every opportunity to accidentally write `values` instead of `value`.

I have gotten that lesson from years of working through freeCodeCamp off and on. Even copying code lets me feel what it is like to write the correct shape. Then I can change it, break it, repair it, and eventually produce it without the example.

I also created a spreadsheet of common business problems that software could solve. Today's lead intake form is not merely an academic Django exercise. It is a recognizable piece of business infrastructure. A company receives a request, validates it, stores it, and returns a predictable response. The details can change, but the architecture will appear again.

That gives this daily project strategy a second purpose. I am practicing the course material, but I am also building a catalog of systems that small and medium-sized businesses might actually pay for. The more familiar I become with their architecture, the easier it will be to build them, customize them, and improve them for a specific situation.

Validators and serializers still look more complicated in code than they sound in English. That is fine. The concept is there. The implementation needs repetitions.

So I will keep building the whole thing.

An API is a technical contract enforced by code. Today I did not just read its clauses. I built enough of the machinery to watch the agreement take effect.
