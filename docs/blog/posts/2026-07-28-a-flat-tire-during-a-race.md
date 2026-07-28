---
date: 2026-07-28
categories:
  - Code Platoon
authors:
  - keith
tags:
  - django
  - docker
  - postgresql
  - javascript
  - codewars
  - code-platoon
---

# A Flat Tire During a Race

Before the monastery's smoking tallow lamps, swore I by the old word *triuwa* that I knew every turn of the maze, though the map shook in my ink-stained hands, he had me lost--- Django. 

<!-- more -->

---

## The Array That Was Always Equal to Itself

The day began with a Codewars problem called “Are the Numbers in Order?”

The function receives an array of integers and returns whether the numbers are in ascending order. Repeated values are allowed because the requirement is only that no number exceeds the number immediately to its right.

My first solution seemed reasonable:

```javascript
function inAscOrder(arr) {
  const ascArr = arr.sort((a, b) => a - b);

  if (ascArr === arr) {
    return true;
  }

  return false;
}
```

Sort the array, compare the sorted result to the original, and return whether they match.

Except that is not what this code does.

The first problem is that `sort()` mutates the original array. It does not create a separate sorted array. After this line:

```javascript
const ascArr = arr.sort((a, b) => a - b);
```

both `ascArr` and `arr` point to the same array in memory. The original ordering is already gone.

The second problem is that `===` does not compare the contents of two arrays. Arrays are objects in JavaScript, so strict equality compares their references. It asks whether both variables point to the same object, not whether the objects contain the same values.

In my code, they do point to the same object. That means `ascArr === arr` is always `true`, including when the input began completely out of order.

It is a compact demonstration of two JavaScript behaviors colliding at once:

```text
sort() changes the original array
             +
=== compares object references
             =
the function always returns true
```

The better solution does not need to sort anything:

```javascript
function inAscOrder(arr) {
  for (let i = 0; i < arr.length - 1; i++) {
    if (arr[i] > arr[i + 1]) {
      return false;
    }
  }

  return true;
}
```

This version checks exactly what the problem asks. For every adjacent pair, is the number on the left greater than the number on the right? The moment the answer is yes, the function returns `false`. If the loop finishes without finding such a pair, the array is in ascending order.

It is also more efficient. Sorting takes `O(n log n)` time and changes the input unless I make a copy first. The loop takes `O(n)` time, uses no additional array, and can stop as soon as it finds a failure.

There is also a clean `every()` version:

```javascript
function inAscOrder(arr) {
  return arr.every((num, i) => i === 0 || arr[i - 1] <= num);
}
```

I like the direct loop here because it maps almost word for word to the definition of the problem. Sometimes the best solution comes from resisting the urge to transform the data and simply checking the condition I was given.

## The Thirty-Thousand-Foot View

Today was the first full day of Django, and I am very glad I followed through on my plan to create a [Creative Project Tracker](https://github.com/KgitWH21/creative-project-tracker-django) ahead of the lecture.

Django is confusing when seeing it for the first time, especially in a fast lecture format. It is not that any single command is impossible to understand. The problem is that there are many separate pieces, and they all have to line up:

```text
virtual environment
        ↓
Django project
        ↓
Django container
        ↓
Docker network
        ↓
PostgreSQL container
        ↓
migrations and database tables
        ↓
fixtures, models, tests, and admin
```

When I don't yet know what the completed system is supposed to look like, every step can feel equally mysterious. I can follow a command without understanding why it belongs at that point in the process. If something fails, I may not know whether I am dealing with Python, Django, Docker, PostgreSQL, networking, or a mistake in the shell.

That is where the prebuilt project helped. It gave me the 30,000-foot view. I knew what everything was working toward, so I did not get as caught up in each individual turn through the maze.

The moment an error appears during a fast lecture is the equivalent of getting a flat tire during a race. I have to stop moving forward and diagnose it while the rest of the material keeps going. I can continue listening, but I cannot keep typing along, and that means I get less out of everything that follows.

Having already seen a complete Django system did not mean I understood all of it. It meant I had a map. When a command appeared, I could place it somewhere on that map.

That made an enormous difference.

## A Private LAN for Containers

One of those pieces was the Docker network:

```bash
docker network create hac-network
```

This creates a private virtual network inside Docker named `hac-network`.

The network exists so the Django container and PostgreSQL container can communicate. Both containers are attached to it when they start:

```text
Django container ── hac-network ── PostgreSQL container
```

Once they share that network, Django can use the PostgreSQL container's name as the database host:

```python
"HOST": "db-container"
```

Docker provides name resolution within the network. It translates `db-container` into the internal IP address of the database container, almost like a small private LAN with its own DNS.

Without that shared network, Django may know that it needs a database at `db-container`, but it has no route to reach a machine by that name. The containers can both be running perfectly and still be unable to communicate.

That helped clarify something important about containers. A container is not just a process with a funny startup command. It has boundaries. Networking has to be configured intentionally, and the hostname in Django's database settings is part of that configuration.

Creating the network does not start either container. It only creates the communication channel they will join later. It also generally needs to be created only once.

## Make It Executable, Then Execute It

The database startup sequence included:

```bash
chmod +x run_db.sh
./run_db.sh
```

These are two separate operations.

The first changes the file's permissions:

```bash
chmod +x run_db.sh
```

`chmod` means change mode, `+x` adds executable permission, and `run_db.sh` is the file being changed. Without that permission, trying to execute the script can produce a `Permission denied` error.

The second command runs it:

```bash
./run_db.sh
```

The `.` means the current directory, `/` separates the directory from the filename, and `run_db.sh` is the script. In plain language: find this script in the current directory and execute it.

In this project, the script builds a PostgreSQL Docker image and starts a container from it:

```bash
docker build -t hac-db-img .

docker run \
  -d \
  --rm \
  --name db-container \
  --network hac-network \
  hac-db-img
```

So the full sequence is:

```text
make the script runnable
        ↓
run the script
        ↓
build the database image
        ↓
start the database container
        ↓
attach it to the shared network
```

Again, none of these pieces is especially complicated by itself. The challenge is understanding the state change at every step. Before `chmod`, the file exists but may not be executable. Before `docker build`, the Dockerfile exists but there is no image. Before `docker run`, the image exists but there is no running container. Before attaching the network, the container exists but Django may not be able to reach it.

Django setup began making more sense once I stopped seeing it as one enormous incantation and started seeing a sequence of state changes.

## Models Are Not Tables Yet

After the containers were running, the next sequence moved inside the Django container:

```bash
docker exec -it django-container bash
python manage.py makemigrations
python manage.py migrate
python manage.py loaddata starter_projects
python manage.py test
exit
```

The first command opens an interactive Bash shell inside the already-running Django container. From that point until `exit`, the commands run in the container rather than directly in my ordinary terminal.

Then comes one of the most important distinctions in Django:

```text
models.py → makemigrations → migration files
migration files → migrate → PostgreSQL tables
```

`python manage.py makemigrations` examines the Django models and creates migration instructions describing the database changes that should happen. It does not make those changes to PostgreSQL yet.

`python manage.py migrate` reads those instructions and applies them. That is the point where tables are actually created or altered in the database.

The difference matters because a model is Python code describing the shape and behavior of data. A migration is a versioned plan for changing the database schema. The database table is the structure PostgreSQL creates after that plan is applied.

They are connected, but they are not the same thing.

After the tables exist, `loaddata` reads a fixture and inserts starter records. Then `test` creates a separate temporary test database, applies the necessary setup, runs the tests, and destroys that temporary database afterward. The development data is not supposed to be modified by the tests.

That sequence now feels less like a collection of Django commands and more like a data pipeline:

```text
define the data
        ↓
describe the schema change
        ↓
apply it to PostgreSQL
        ↓
load starter records
        ↓
verify the behavior separately
```

## Three Doors Into the Same Model

Django Admin initially looked like another system to configure, but it is better understood as another interface to the same system.

Running:

```bash
python manage.py createsuperuser
```

creates an account that can log in to Django's built-in administration site. That user is separate from the PostgreSQL database user and separate from my operating-system account.

The PostgreSQL user lets Django connect to the database. The Django superuser lets a person log in to the Admin interface. My WSL user lets me operate my computer and terminal. They are three identities with three different jobs.

The Admin site then gives me a ready-made interface for viewing and modifying model records. I can create a project, edit fields, search records, apply filters, or delete entries without building a separate front end for all of those operations.

The Django shell provides another route:

```bash
python manage.py shell
```

This opens an interactive Python session with the Django project already configured. Unlike an ordinary Python shell, it knows which settings to use, which apps are installed, and which database to connect to.

From there, I can work directly with the model:

```python
from projects.models import CreativeProject

CreativeProject.objects.all()

project = CreativeProject.objects.get(code="ORS")
project.add_words(500)
project.archive()
```

The React interface, Django Admin, and Django shell are not three separate databases. They are three doors into the same model and the same PostgreSQL data:

```text
React interface ─┐
Django Admin ────┼──> CreativeProject model ──> PostgreSQL
Django shell ────┘
```

That was another part of the 30,000-foot view. Once I understood that all these tools converge on the same model, the setup stopped looking like unrelated machinery.

## A Productive Struggle

By the afternoon, I was able to struggle through almost all of the homework in a productive way.

That distinction matters. There is a kind of struggle where I have no map, every error could originate anywhere, and I am mostly trying random changes. Then there is a struggle where I understand the overall system, can identify the layer I am working in, and can narrow down what must be wrong.

Today moved me closer to the second kind.

I also received the result of my latest assessment: 100.

That was satisfying because I put a lot of work into it, but it also felt like evidence that the learning process is working. Building extra context can take time. Stopping to understand what a Docker network does can take time. Asking why `makemigrations` and `migrate` are separate can take time. None of that always looks like forward motion while I am doing it.

But the context is what lets the individual commands stick.

Week nine, day thirty-eight, is done. Django is still a maze, and I am nowhere close to having every turn memorized. The difference is that I have now seen the maze from above.

The containers need a network. Django needs a host name it can resolve. Models become migration instructions, migrations become tables, fixtures become rows, and Admin and the shell are just different ways of reaching the same data.

That is enough of a map to keep moving—even when I hit the next flat tire.
