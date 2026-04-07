# Web Development with Python and Flask — Complete Study Guide

> **Audience:** Beginners — no prior web development experience required.
> This guide combines three sets of course notes into a single, self-contained reference.
> Read it from top to bottom, then return to individual sections as you build projects.

---

## Master Table of Contents

### Part I — HTTP and RESTful APIs
- [What is an API?](#1-what-is-an-api)
- [The Client-Server Model](#2-the-clientserver-model)
- [HTTP: The Language of the Web](#3-http-the-language-of-the-web)
- [HTTP Methods](#4-http-methods)
- [HTTP Status Codes](#5-http-status-codes)
- [JSON: The Data Format for APIs](#6-json-the-data-format-for-apis)
- [URLs and Resources](#7-urls-and-resources)
- [REST: A Design Pattern for APIs](#8-rest-a-design-pattern-for-apis)
- [Error Handling in APIs](#9-error-handling-in-apis)
- [Tools for Working with APIs](#10-tools-for-working-with-apis)
- [Putting It All Together](#11-putting-it-all-together)

### Part II — How Flask Runs Your Application: WSGI
- [The Big Picture](#1-the-big-picture)
- [What Happens When a Request Arrives](#2-what-happens-when-a-request-arrives)
- [The Development Server (Werkzeug)](#3-the-development-server-werkzeug)
- [WSGI: The Standard Behind the Scenes](#4-wsgi-the-standard-behind-the-scenes)
- [Development vs. Production](#5-development-vs-production)

### Part III — Flask Introduction
- [Why Flask?](#1-why-flask)
- [What is Flask?](#2-what-is-flask-the-microframework-concept)
- [Flask Concepts Overview](#3-flask-concepts-overview)
- [Project Layouts](#4-project-layouts)
- [The Application Object](#5-the-application-object)
- [Views](#6-views)
- [Routes](#7-routes)
- [Blueprints](#8-blueprints)
- [Models](#9-models)
- [Templates](#10-templates)

---

---

# Part I — HTTP and RESTful APIs

## Table of Contents

1. [What is an API?](#1-what-is-an-api)
2. [The Client–Server Model](#2-the-clientserver-model)
   - [Statelessness](#statelessness)
3. [HTTP: The Language of the Web](#3-http-the-language-of-the-web)
   - [3.1 HTTP Request Structure](#31-http-request-structure)
   - [3.2 HTTP Response Structure](#32-http-response-structure)
   - [3.3 Common Request Headers](#33-common-request-headers)
4. [HTTP Methods](#4-http-methods)
   - [Safe vs. Idempotent](#safe-vs-idempotent-explained)
   - [When to Use Each Method](#when-to-use-each-method)
5. [HTTP Status Codes](#5-http-status-codes)
   - [Status Code Categories](#status-code-categories)
   - [Codes You Will Encounter Most](#codes-you-will-encounter-most)
6. [JSON: The Data Format for APIs](#6-json-the-data-format-for-apis)
   - [Python ↔ JSON Comparison](#python--json-comparison)
   - [Content-Type Header](#content-type-header)
7. [URLs and Resources](#7-urls-and-resources)
   - [URL Anatomy](#url-anatomy)
   - [Collections vs. Individual Resources](#collections-vs-individual-resources)
8. [REST: A Design Pattern for APIs](#8-rest-a-design-pattern-for-apis)
   - [REST Principles](#rest-principles-the-ones-that-matter-now)
   - [CRUD and REST](#crud-and-rest)
   - [A Complete Conversation](#a-complete-conversation)
9. [Error Handling in APIs](#9-error-handling-in-apis)
10. [Tools for Working with APIs](#10-tools-for-working-with-apis)
    - [Bruno (VS Code Extension)](#bruno-vs-code-extension)
    - [curl (Command-Line Tool)](#curl-command-line-tool)
    - [Python requests Library](#python-requests-library)
    - [Python httpx Library](#python-httpx-library)
11. [Putting It All Together](#11-putting-it-all-together)
    - [Real-World Example: Server Monitoring](#real-world-example-server-monitoring)
12. [Summary Table](#summary-table)
13. [References](#references)

---

## 1. What is an API?

### Plain-English Definition

An **API** (Application Programming Interface) is a formal agreement — a contract — between two pieces of software that defines exactly how they can communicate. It answers the questions: *What can I ask for? How do I ask? What will I get back?*

You have already used APIs in Python without knowing it. When you call a function like `len(my_list)`, you are using an API: `len` is the agreed-upon name, `my_list` is the agreed-upon input, and the integer count is the agreed-upon output. You do not need to know *how* Python counts the items internally — the contract is all you need.

### From Local Functions to Web APIs

A **web API** extends this same idea across a network. Instead of calling a function directly inside your program, your program sends a formatted message over the internet to a remote server. The server processes the message and sends a structured reply back.

This makes it possible for a Python script running on your laptop to ask a server running in a data center thousands of miles away: *"Give me the current weather for New York."* The server sends back the data; your script reads it.

### Why Web APIs Matter

Web APIs are the backbone of modern software. Here are concrete examples of what they power:

- **Mobile apps** — When you open Instagram, the app sends an API request to Instagram's servers: *"Give me the latest posts for this user."* The server responds with post data, and the app displays it.
- **Single-page web apps** — Gmail never fully reloads the page. Instead, it continuously sends API requests in the background: *"Any new emails?"* The page updates without a full refresh.
- **Monitoring tools** — A monitoring agent can send a `GET /metrics` request to each server every 30 seconds to collect CPU, memory, and disk usage data.
- **Automation scripts** — A script can create, update, or delete records in a system (say, adding a new user to a database) entirely through API calls, with no graphical interface needed.

### Diagram: Multiple Clients, One API Server

```
+------------------+
|   Mobile App     |---+
+------------------+   |
                        |   HTTP    +------------------+     +------------+
+------------------+   +---------> |                  |     |            |
|  Browser App     |-------------> |   Web API Server |<--> |  Database  |
+------------------+   +---------> |                  |     |            |
                        |           +------------------+     +------------+
+------------------+   |
|  Python Script   |---+
+------------------+   |
                        |
+------------------+   |
| Monitoring Agent |---+
+------------------+
```

Every arrow going into the server is an HTTP request. Every arrow coming back is an HTTP response. All clients speak the same language (HTTP) and get back the same kinds of data (usually JSON).

---

## 2. The Client–Server Model

### What It Is

Almost all web communication follows the **client–server model**. This model divides the world into two roles:

- **Client** — The program that *initiates* a request. This could be a web browser, a mobile app, a Python script, or an API testing tool.
- **Server** — The program that *listens* for requests, processes them, and sends back a response. It never starts the conversation.

The key rule: **the client always speaks first.** The server cannot push information to the client unless the client has asked for it (with standard HTTP — there are more advanced patterns like WebSockets, but those are beyond this course).

### How a Request–Response Cycle Works

```
  CLIENT                                    SERVER
    |                                          |
    |  --- "Give me user #3" (Request) ------> |
    |                                          |
    |                               [Process request,
    |                                look up user #3
    |                                in the database]
    |                                          |
    |  <-- "Here is user #3" (Response) ------ |
    |                                          |
```

1. The client constructs a request and sends it.
2. The server receives it, figures out what to do, and executes that logic.
3. The server constructs a response and sends it back.
4. The connection is complete. The server immediately "forgets" about it.

### Statelessness

**HTTP is stateless.** This is one of the most important — and most confusing — ideas in web development. "Stateless" means the server does not remember anything about previous requests.

Each request is treated as if it arrived from a brand-new, anonymous stranger.

**Example:** Suppose you send `GET /users/3` to retrieve a user, and then immediately send `GET /users/3` again. The server processes each request identically — from scratch. It has no memory of the first request.

**Why is this a feature, not a bug?**

Statelessness makes servers dramatically simpler and more scalable:
- The server does not need to maintain a table of "who is currently logged in."
- Any server in a cluster can handle any request — they are all interchangeable.
- If a server crashes and restarts, nothing is lost, because there was no "session" to lose.

**The trade-off:** Since the server remembers nothing, *the client* is responsible for including all necessary context in every request. If an API requires authentication, you must send your credentials (e.g., an API key) with *every single request*, not just the first one.

> **What My Professor Didn't Explain:** You may wonder, "But websites remember that I'm logged in — isn't that state?" Yes, but that's an application-level illusion built on top of stateless HTTP. Typically, the server issues a **token** (a long random string) after you log in. Your browser stores that token and sends it with every subsequent request. The server looks up the token in its database each time — it's not "remembering" you; it's doing a fresh lookup on every request.

---

## 3. HTTP: The Language of the Web

### What HTTP Is and Why It Exists

**HTTP** (Hypertext Transfer Protocol) is the standardized language that clients and servers use to communicate. "Protocol" just means "an agreed set of rules for exchanging messages." Every URL you visit in a browser, every API call from a Python script, and every image that loads on a web page travels as an HTTP message.

Think of HTTP as an international diplomatic language. As long as both parties speak HTTP, it does not matter whether the client is a browser on Windows, a Python script on Linux, or an app on an iPhone — they can all talk to the same server.

**HTTPS** is HTTP with encryption added on top (via TLS). The `S` stands for "Secure." Anything sent over HTTPS is scrambled so third parties cannot read it in transit. In production, always use HTTPS. In local development, HTTP is fine.

---

### 3.1 HTTP Request Structure

Every HTTP request has the same four-part structure, regardless of what it is asking for:

```
+------------------------------------------------------+
|  Request Line   |  Method + Path + Protocol Version  |
+------------------------------------------------------+
|  Headers        |  Key: Value metadata               |
|                 |  Key: Value metadata               |
+------------------------------------------------------+
|  (Blank Line)   |  Signals end of headers            |
+------------------------------------------------------+
|  Body           |  Optional data sent to the server  |
|  (optional)     |                                    |
+------------------------------------------------------+
```

**Annotated Real Example — Creating a New User:**

```http
POST /users HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{
  "username": "alice",
  "email": "alice@example.com"
}
```

Let's break this down line by line:

| Line | What It Is | What It Means |
|------|------------|---------------|
| `POST /users HTTP/1.1` | **Request line** | *"I want to create something (`POST`) at the `/users` address, using HTTP version 1.1"* |
| `Host: localhost:8080` | **Header** | *"I'm talking to the server at `localhost` on port `8080`"* |
| `Content-Type: application/json` | **Header** | *"The body I'm sending is formatted as JSON"* |
| *(blank line)* | **Separator** | Marks the end of headers — mandatory |
| `{ "username": ... }` | **Body** | The actual data to create the new user |

> **Beginner gotcha:** The blank line between the headers and the body is not optional — it is part of the HTTP standard. Without it, a server may refuse or misparse the request.

---

### 3.2 HTTP Response Structure

When the server processes a request, it sends back a response in a parallel four-part structure:

```
+------------------------------------------------------+
|  Status Line    |  Protocol Version + Code + Reason  |
+------------------------------------------------------+
|  Headers        |  Key: Value metadata               |
+------------------------------------------------------+
|  (Blank Line)   |  Signals end of headers            |
+------------------------------------------------------+
|  Body           |  Data returned by the server       |
+------------------------------------------------------+
```

**Annotated Real Example — Server Confirms User Was Created:**

```http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 42,
  "username": "alice",
  "email": "alice@example.com"
}
```

| Line | What It Is | What It Means |
|------|------------|---------------|
| `HTTP/1.1 201 Created` | **Status line** | *"HTTP 1.1 response; the operation succeeded and a resource was created (code 201)"* |
| `Content-Type: application/json` | **Header** | *"The body I'm returning is JSON — parse it accordingly"* |
| *(blank line)* | **Separator** | Marks end of headers |
| `{ "id": 42, ... }` | **Body** | The newly created user, now including the server-assigned `id` of `42` |

**Notice:** The response body includes an `id` field that the *client* did not send. The server assigned this ID automatically (e.g., auto-incremented in the database). This is standard practice — the server owns ID assignment.

---

### 3.3 Common Request Headers

Headers are metadata — extra context that travels alongside the request but is not part of the main data body. Think of them like a shipping label on a package: they tell the receiver important things about what's inside and who sent it.

| Header | Purpose | Example Value |
|--------|---------|---------------|
| `Host` | Which server or virtual host the request is for. Required by HTTP/1.1. | `localhost:8080` |
| `Content-Type` | The format of the *request* body. Tells the server how to parse what you sent. | `application/json` |
| `Accept` | The format(s) the client is willing to receive in the *response*. | `application/json` |
| `User-Agent` | Identifies the client software (browser name/version, library name, etc.) | `python-requests/2.31` |

**Why `Content-Type` matters:** If you send a JSON body but forget to set `Content-Type: application/json`, the server may not know it is JSON and may reject or misparse it — even if the data looks perfectly valid to you.

> **What My Professor Didn't Explain:** Headers are case-insensitive by the HTTP specification (`content-type` and `Content-Type` mean the same thing), but the conventional style is Title-Case. Different libraries handle this differently — Python's `requests` library normalizes header names automatically.

---

## 4. HTTP Methods

### What HTTP Methods Are

HTTP defines a set of **methods** (also called **verbs**) that indicate *what action* the client wants to perform. The URL identifies *what resource* the action targets. Together, they form a complete instruction.

**Analogy:** If URLs are nouns (the things you work with), HTTP methods are the verbs (what you do with them).
- `GET /tasks` → *"Read the list of tasks"*
- `DELETE /tasks/3` → *"Remove task number 3"*

The four methods you will use in this course:

| Method | Purpose | Sends a Body? | Safe? | Idempotent? |
|--------|---------|---------------|-------|-------------|
| **GET** | Retrieve data | No | Yes | Yes |
| **POST** | Create a new resource | Yes | No | No |
| **PUT** | Replace/update an existing resource | Yes | No | Yes |
| **DELETE** | Remove a resource | No | No | Yes |

### Safe vs. Idempotent — Explained

These two terms appear in every HTTP reference and confuse many beginners.

**Safe** means the request does not modify anything on the server. A safe request is read-only. `GET` is safe — calling `GET /users` a thousand times does not change any data.

**Idempotent** means sending the same request multiple times produces the same result as sending it once. The outcome is the same no matter how many times you repeat it.

- `DELETE /tasks/5` is idempotent: whether you send it once or ten times, task 5 ends up deleted. (After the first deletion, subsequent requests may return `404 Not Found`, but the *state of the data* is the same.)
- `PUT /tasks/5` with `{"title": "New Title"}` is idempotent: setting the title to "New Title" five times in a row still results in the title being "New Title."
- `POST /tasks` is **not** idempotent: sending it three times creates three separate tasks.

**Why do these properties matter?**

In distributed systems and unreliable networks, requests sometimes fail or get retried automatically. If a network glitch causes your client to retry a `GET` request, no harm done — it's safe and idempotent. If it retries a `POST`, you might accidentally create duplicate records. Knowing these properties helps you design robust systems.

### Diagram: How Each Method Affects the Database

```
+--------------------+                  +------------------+
|  GET /tasks/1      | - - - - read - ->|                  |
| (no change)        |                  |                  |
+--------------------+                  |                  |
                                        |    Database      |
+--------------------+                  |                  |
|  POST /tasks       | ---adds row----> |                  |
| (new task)         |                  |                  |
+--------------------+                  |                  |
                                        |                  |
+--------------------+                  |                  |
|  PUT /tasks/1      | -modifies row--> |                  |
| (update task)      |                  |                  |
+--------------------+                  |                  |
                                        |                  |
+--------------------+                  |                  |
|  DELETE /tasks/1   | -removes row---> |                  |
| (delete task)      |                  +------------------+
+--------------------+
```

### When to Use Each Method

| I want to... | Method | Target URL | Notes |
|--------------|--------|------------|-------|
| List all tasks | `GET` | `/tasks` | Returns a JSON array of task objects |
| View one specific task | `GET` | `/tasks/3` | The `3` is the task's ID |
| Add a new task | `POST` | `/tasks` | Include the new task data as a JSON body |
| Replace/update a task | `PUT` | `/tasks/3` | Include the full updated task as a JSON body |
| Delete a task | `DELETE` | `/tasks/3` | No body needed — the ID in the URL is enough |

> **Warning — Common Misconception:** A browser's address bar only ever sends `GET` requests. If you type `http://localhost:8080/tasks` into your browser, you are making a GET request. You *cannot* send POST, PUT, or DELETE requests from the address bar alone. To send those, you need a tool like Bruno, `curl`, or Python code.

---

## 5. HTTP Status Codes

### What Status Codes Are

Every HTTP response begins with a three-digit **status code** that instantly communicates whether the request succeeded, failed, or requires further action. Think of it as the server's one-number summary: *"Here is what happened."*

Status codes are the first thing you check when debugging an API call. Before reading the response body, always check the status code.

### Status Code Categories

The first digit of the code tells you the category:

| Range | Category | Plain-English Meaning |
|-------|----------|-----------------------|
| **2xx** | Success | Everything worked as expected |
| **3xx** | Redirection | The resource has moved; the client should follow a new URL |
| **4xx** | Client Error | The request was wrong — the client needs to fix something |
| **5xx** | Server Error | The request was fine, but the server failed to process it |

### Flowchart: Reading a Status Code

```
Receive a response
        |
        v
+-------------------+
| Look at first digit|
+-------------------+
        |
   +---------+---------+---------+
   |         |         |         |
   v         v         v         v
  2xx       3xx       4xx       5xx
   |         |         |         |
Success  Redirect   Client    Server
It worked  Follow   Error:    Error:
          new URL   fix your  server
                   request   crashed
```

### Codes You Will Encounter Most

| Code | Name | When You'll See It | What to Do |
|------|------|-------------------|------------|
| `200` | OK | A successful GET or PUT — data is returned in the body | Read the response body |
| `201` | Created | A successful POST — a new resource was created | Read the body to get the new resource (including its ID) |
| `204` | No Content | Success, but there's nothing to return — common after DELETE | No body to read; the operation succeeded |
| `400` | Bad Request | The request is malformed — missing fields, wrong format | Fix the request (check headers, body format, required fields) |
| `404` | Not Found | The resource does not exist at this URL | Check the URL and/or ID — the item may have been deleted |
| `405` | Method Not Allowed | The URL exists, but it doesn't support this HTTP method | Check which methods the endpoint supports |
| `409` | Conflict | The request conflicts with existing data (e.g., duplicate username) | Change the conflicting value |
| `500` | Internal Server Error | Something crashed on the server | This is a server bug — check server logs |

**Real-World Example:**

You send `POST /users` with `{"username": "alice", "email": "alice@example.com"}`, but a user named "alice" already exists in the database. The server returns:

```http
HTTP/1.1 409 Conflict
Content-Type: application/json

{
  "error": "Username already exists"
}
```

The `409` tells you immediately: *this is a conflict problem, not a malformed request (400) and not a server crash (500).* The JSON body tells you exactly what conflicted.

> **What My Professor Didn't Explain:** Status codes are defined by the HTTP standard (RFC 7231), not invented by each server. This means every HTTP-aware tool in the world understands the same codes. Libraries like Python's `requests` can automatically raise exceptions for 4xx/5xx responses using `response.raise_for_status()`.

---

## 6. JSON: The Data Format for APIs

### What JSON Is and Why APIs Use It

**JSON** (JavaScript Object Notation) is the standard format for sending and receiving data through web APIs. Despite the name, JSON has nothing to do with JavaScript from a practical standpoint — it is a language-independent text format that virtually every programming language can read and write.

APIs need a standard data format because the client and server may be written in completely different languages (a Python server talking to a Swift iOS app, for example). JSON is the common ground both sides agree on.

### Why JSON Instead of Something Else?

- **Human-readable:** JSON is plain text you can read in your terminal.
- **Lightweight:** It is more compact than XML (the older alternative).
- **Universal:** Every major programming language has a built-in JSON library.
- **Directly maps to Python:** JSON's structure is nearly identical to Python's built-in data types.

### Python ↔ JSON Comparison

If you know Python dictionaries and lists, you already understand 95% of JSON.

| Python Type | JSON Equivalent | Important Differences |
|-------------|----------------|----------------------|
| `dict` | `object` `{}` | JSON keys **must** be strings in double quotes — `{"name": "alice"}`, not `{name: "alice"}` |
| `list` | `array` `[]` | Same square-bracket syntax |
| `str` | `string` `""` | JSON **always** uses double quotes — single quotes are not valid JSON |
| `int`, `float` | `number` | Same syntax — `42`, `3.14` |
| `True` / `False` | `true` / `false` | JSON uses **lowercase** — `true`, not `True` |
| `None` | `null` | Different keyword — `null`, not `None` |

**Example of a JSON object that represents a task:**

```json
{
  "id": 1,
  "title": "Complete Python assignment",
  "is_done": false,
  "assignee": "Sarah Chen",
  "tags": ["homework", "python", "urgent"]
}
```

Notice:
- All keys are in **double quotes**: `"id"`, `"title"`, etc.
- The boolean is **lowercase**: `false`, not `False`
- The array of tags uses the same `[]` syntax as Python lists
- String values are in **double quotes**: `"Sarah Chen"`

**Common Beginner Mistake — Invalid JSON:**

```json
{
  'username': 'alice',   // ERROR: single quotes not allowed in JSON
  is_done: false,        // ERROR: key must be a quoted string
  score: None            // ERROR: Python None is JSON null
}
```

**Valid JSON version:**

```json
{
  "username": "alice",
  "is_done": false,
  "score": null
}
```

### Content-Type Header

When a client sends JSON in the request body, it **must** include this header:

```
Content-Type: application/json
```

This header is a label that tells the server: *"Interpret my body as JSON."* Without it, the server may try to parse your JSON body as a plain text form, fail, and return a `400 Bad Request` error.

Similarly, when a server responds with JSON, it sets `Content-Type: application/json` so the client knows how to parse the response body.

**Think of it this way:** The body is the package; the `Content-Type` header is the label on the package that says what's inside.

> **Warning:** This is one of the most common mistakes beginners make. If you get an unexpected `400` error when sending a POST request, the first thing to check is whether you included `Content-Type: application/json`.

---

## 7. URLs and Resources

### What a URL Identifies

A **URL** (Uniform Resource Locator) is the address of a specific *resource* on the web. A resource is any piece of data that can be named and retrieved — a user account, a task, an image, a document.

In REST APIs (covered in the next section), URLs are how you tell the server *which thing* you want to work with. The HTTP method tells it *what to do*.

### URL Anatomy

```
http://localhost:8080/users/3?include_email=true
|____|  |_________| |______|  |__________________|
  |          |         |               |
scheme      host      path        query string
```

| Part | Purpose | Example |
|------|---------|---------|
| **Scheme** | The protocol to use for this connection | `http` or `https` |
| **Host** | The server's address and optional port number | `localhost:8080` — this means "my own machine, port 8080" |
| **Path** | Identifies which resource on that server | `/users/3` — "the user whose ID is 3" |
| **Query string** | Optional extra parameters, added after a `?`, as `key=value` pairs | `include_email=true` |

**What is a port?** A port is like an apartment number. A server at `localhost` is the building; the port (`8080`) is the specific apartment. Web browsers default to port `80` for HTTP and port `443` for HTTPS — which is why you don't see a port number in normal website URLs.

**What is `localhost`?** `localhost` is a special hostname that always means "this computer." When you run a Flask server on your own machine and visit `http://localhost:8080`, you are connecting to a server running on *your own* laptop.

### Query Strings

Query strings pass extra filtering or configuration options to an endpoint without changing the resource being identified. They always start with `?` and separate multiple parameters with `&`.

**Examples:**
- `GET /tasks?status=done` — get only completed tasks
- `GET /users?page=2&limit=10` — get the second page of users, 10 per page
- `GET /users/3?include_email=true` — get user 3, and also include their email in the response

### Collections vs. Individual Resources

REST APIs consistently use a two-level URL pattern: a **collection** (all items of a type) and an **individual resource** (one specific item, identified by ID).

| URL | Represents | Typical Action |
|-----|-----------|----------------|
| `/users` | The **collection** of all users | `GET` to list them all; `POST` to add a new one |
| `/users/3` | A **single** user (the one with ID 3) | `GET` to read, `PUT` to update, `DELETE` to remove |
| `/tasks` | The **collection** of all tasks | `GET` to list; `POST` to create |
| `/tasks/7` | A **single** task (ID 7) | `GET`, `PUT`, or `DELETE` |

### Diagram: URL Tree Structure

```
http://localhost:8080
        |
        +---------- /users
        |               |------- /users/1
        |               |------- /users/2
        |
        +---------- /tasks
        |               |------- /tasks/1
        |               |------- /tasks/5
        |
        +---------- /tags
                        |------- /tags/1
                        |------- /tags/3
```

Each "branch" of the URL tree is a collection. Each "leaf" is an individual resource with a unique ID.

> **What My Professor Didn't Explain:** The IDs in URLs (like `/users/3`) are typically the primary key from the database. When you create a new resource with POST, the server assigns an ID and returns it. You then use that ID in all future requests for that specific resource.

---

## 8. REST: A Design Pattern for APIs

### What REST Is (and Is Not)

**REST** (Representational State Transfer) is a set of *conventions* — not a technology, not a library, not a protocol — for designing web APIs in a consistent, predictable way.

REST was defined in a PhD dissertation by Roy Fielding in 2000. It has since become the dominant style for web APIs because it leverages what HTTP already provides instead of inventing something new on top of it.

An API that follows REST conventions is called a **RESTful API**.

**The key insight of REST:** HTTP already has verbs (methods), addresses (URLs), and status codes. REST says: *use them correctly and consistently.* Don't invent your own system.

### REST Principles — The Ones That Matter Now

**1. Resources are nouns**

URLs should identify *things*, not *actions*. Name your URLs after the data, not the operation.

```
WRONG (action-based, not RESTful):
  /getTasks
  /createTask
  /deleteTask?id=5
  /updateTaskTitle

RIGHT (resource-based, RESTful):
  /tasks          (the collection of tasks)
  /tasks/5        (a specific task)
```

The action is communicated by the HTTP method, not the URL.

**2. HTTP methods are verbs**

- The *URL* identifies the resource: `which thing`
- The *HTTP method* specifies the action: `what to do with it`

**3. Use standard status codes**

Do not invent a custom error system like `{"status": "fail", "code": 99}`. Use the HTTP status codes the standard already provides — `200`, `201`, `400`, `404`, `409`, `500`, etc.

**4. Stateless**

Every request is self-contained. The server does not store session information between requests. (Covered in detail in Section 2.)

**5. JSON responses**

Return data in JSON — a standard, machine-readable, human-readable format.

---

### CRUD and REST

Most data-driven applications need to perform four basic operations on resources. These are known as **CRUD** — an acronym that is used universally across web development, databases, and software engineering:

| CRUD Operation | Meaning | HTTP Method | URL Pattern | Request Body? | Typical Response Code |
|----------------|---------|-------------|-------------|---------------|-----------------------|
| **C**reate | Add a new item | `POST` | `/tasks` | Yes — the new item's data | `201 Created` |
| **R**ead | Retrieve existing data | `GET` | `/tasks` or `/tasks/3` | No | `200 OK` |
| **U**pdate | Modify an existing item | `PUT` | `/tasks/3` | Yes — the updated data | `200 OK` |
| **D**elete | Remove an item | `DELETE` | `/tasks/3` | No | `200 OK` or `204 No Content` |

### Diagram: CRUD → REST Mapping

```
  CRUD              HTTP Method      URL             Response Code
  ------            -----------      ----            -------------
  Create   -------> POST      -----> /tasks   -----> 201 Created
  Read     -------> GET       -----> /tasks/3 -----> 200 OK
  Update   -------> PUT       -----> /tasks/3 -----> 200 OK
  Delete   -------> DELETE    -----> /tasks/3 -----> 200 OK (or 204)
```

---

### A Complete Conversation

The following diagram shows the entire lifecycle of a resource — creating it, reading it, updating it, deleting it, and then verifying it is gone:

```
CLIENT                                          SERVER
  |                                               |
  |  POST /users                                  |
  |  {"username": "alice", "email": "alice@..."}  |
  | --------------------------------------------> |
  |                                               | [Create user in DB, assign ID=42]
  |  201 Created                                  |
  |  {"id": 42, "username": "alice", ...}         |
  | <-------------------------------------------- |
  |                                               |
  |  GET /users/42                                |
  | --------------------------------------------> |
  |                                               | [Look up user 42 in DB]
  |  200 OK                                       |
  |  {"id": 42, "username": "alice", ...}         |
  | <-------------------------------------------- |
  |                                               |
  |  PUT /users/42                                |
  |  {"username": "alice_updated", ...}           |
  | --------------------------------------------> |
  |                                               | [Update user 42 in DB]
  |  200 OK                                       |
  |  {"id": 42, "username": "alice_updated", ...} |
  | <-------------------------------------------- |
  |                                               |
  |  DELETE /users/42                             |
  | --------------------------------------------> |
  |                                               | [Remove user 42 from DB]
  |  200 OK                                       |
  |  {"message": "User deleted"}                  |
  | <-------------------------------------------- |
  |                                               |
  |  GET /users/42   (try to retrieve deleted)    |
  | --------------------------------------------> |
  |                                               | [User 42 not found in DB]
  |  404 Not Found                                |
  |  {"error": "User 42 not found"}               |
  | <-------------------------------------------- |
```

**Walk-through:**

1. **POST** creates the user. The server assigns ID 42 and returns the full user object.
2. **GET /users/42** reads that user back. The server finds it and returns it with `200 OK`.
3. **PUT /users/42** updates the username. The server replaces the record and returns the updated version.
4. **DELETE /users/42** removes the user. The server deletes the record and confirms with `200 OK`.
5. **GET /users/42** (after deletion) fails with `404 Not Found` — the resource no longer exists.

This pattern is the same regardless of whether you are working with users, tasks, products, or any other resource.

---

## 9. Error Handling in APIs

### Why Good Error Responses Matter

When an API call fails, the client needs to know two things: *what went wrong* and *whose fault it is*. A well-designed API communicates both.

- **Status code** → tells the client the *category* of the problem (client mistake vs. server mistake)
- **JSON body** → tells the client the *specific* details

An API that just returns `400` with an empty body is almost useless — the developer cannot tell if the problem is a missing field, a wrong data type, or something else entirely.

### Error Response Reference

| Scenario | Status Code | Example Response Body |
|----------|-------------|----------------------|
| Request body is missing entirely | `400 Bad Request` | `{"error": "Request body must be JSON"}` |
| A required field is missing | `400 Bad Request` | `{"error": "Username and email are required"}` |
| The resource ID doesn't exist | `404 Not Found` | `{"error": "User 42 not found"}` |
| A value conflicts with existing data | `409 Conflict` | `{"error": "Username already exists"}` |
| Something crashed on the server | `500 Internal Server Error` | `{"error": "Internal server error"}` |

### How to Read an Error Response

**Step 1:** Look at the first digit of the status code.
- `4xx` → Your request is the problem. Read the error body carefully and fix your code or data.
- `5xx` → The server is the problem. Nothing you can fix on the client side — check the server logs.

**Step 2:** Read the JSON error body for the specific message.

**Real example of debugging with error responses:**

You try to create a user:

```http
POST /users HTTP/1.1
Content-Type: application/json

{"username": "alice"}
```

You get back:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{"error": "Username and email are required"}
```

The `400` tells you: *your request was malformed.* The body tells you: *the `email` field is missing.* Fix: add `"email": "alice@example.com"` to the body.

> **Best Practice:** When you write your own Flask API, always return a helpful JSON error body alongside every non-200 status code. Imagine you are the developer on the receiving end — what information would help you fix the problem fastest?

---

## 10. Tools for Working with APIs

### Why You Need API Tools

You cannot use a browser alone to test an API. Browsers only send `GET` requests. To send `POST`, `PUT`, or `DELETE` requests — or to set custom headers or a JSON body — you need a dedicated tool.

Here are the three main tools you will use in this course:

---

### Bruno (VS Code Extension)

**What it is:** Bruno is an open-source API client that lives inside VS Code as an extension. It provides a graphical interface for crafting and sending HTTP requests without writing code.

**Why use Bruno:** It is visual, easy to use, and stores your saved requests as plain text files in your project folder — which means they can be version-controlled with Git and shared with your team.

**Installation:**

Run this command in your terminal:

```powershell
code --install-extension bruno-api-client.bruno
```

Or open VS Code, go to the Extensions panel (Ctrl+Shift+X), and search for "Bruno."

**Workflow — Step by Step:**

1. Open the Bruno sidebar in VS Code (click the Bruno icon in the Activity Bar).
2. Create a **Collection** — a named folder that groups related requests (e.g., "Tasks API").
3. Inside the collection, add a **New Request**:
   - Set the HTTP method (GET, POST, PUT, DELETE) from the dropdown.
   - Enter the full URL (e.g., `http://localhost:8080/tasks`).
   - Add headers if needed (e.g., `Content-Type: application/json`).
   - For POST and PUT, switch to the **Body** tab and enter your JSON.
4. Click **Send**.
5. Inspect the response: status code, headers, and JSON body all appear in the panel.

**Why Bruno stores files as plain text:** Unlike some API clients that store requests in a proprietary binary database, Bruno saves each request as a `.bru` file. This means your API test collection travels with your code in Git — team members can check out the repo and immediately have all the same test requests.

> **Docs:** [https://docs.usebruno.com/introduction/what-is-bruno](https://docs.usebruno.com/introduction/what-is-bruno)

---

### `curl` (Command-Line Tool)

**What it is:** `curl` is a command-line program for sending HTTP requests. It is pre-installed on Windows (PowerShell), macOS, and Linux — no installation required.

**Why use `curl`:** It is the fastest way to test a quick request without opening a GUI. It works on any machine, in any terminal, making it a universal skill for every developer.

#### Syntax Breakdown

Every `curl` command follows this basic pattern:

```
curl [flags] <URL>
```

**Flags** modify the behavior. The most important ones:

| Flag | Purpose | Example |
|------|---------|---------|
| `-X` | Set the HTTP method (default is GET) | `-X POST` |
| `-H` | Add a request header | `-H "Content-Type: application/json"` |
| `-d` | Send a request body | `-d '{"username": "alice"}'` |
| `-s` | Silent mode — hide the progress bar | `-s` |
| `-i` | Include response headers in the output | `-i` |
| `-v` | Verbose — show the full request and response | `-v` |

#### Complete `curl` Examples

**GET — Retrieve all tasks:**

```powershell
curl http://localhost:8080/tasks
```

*This sends `GET /tasks`. The server responds with a JSON array of tasks.*

**GET — Pretty-print JSON output (easier to read):**

```powershell
curl -s http://localhost:8080/tasks | python -m json.tool
```

*The `-s` hides the progress bar. The `|` pipes the raw JSON output into Python's JSON formatter, which adds proper indentation.*

**POST — Create a new user:**

```powershell
curl -X POST http://localhost:8080/users `
  -H "Content-Type: application/json" `
  -d '{"username": "alice", "email": "alice@example.com"}'
```

*`-X POST` sets the method. `-H` adds the Content-Type header so the server knows the body is JSON. `-d` provides the JSON body. (The backtick `` ` `` continues the command on the next line in PowerShell.)*

**PUT — Update an existing task:**

```powershell
curl -X PUT http://localhost:8080/tasks/5 `
  -H "Content-Type: application/json" `
  -d '{"title": "Updated title", "assignee_id": 1, "details": "..."}'
```

*`PUT` to the specific task URL (`/tasks/5`) with the full updated data in the body.*

**DELETE — Remove a task:**

```powershell
curl -X DELETE http://localhost:8080/tasks/5
```

*No body needed — the ID in the URL is sufficient.*

**GET — Show response headers too:**

```powershell
curl -i http://localhost:8080/tasks
```

*The `-i` flag includes the response status line and headers before the body — useful when you want to see the status code and Content-Type.*

#### Common Beginner Mistakes with `curl`

- **Forgetting `-X POST`:** If you include `-d` without `-X POST`, curl still defaults to POST (because `-d` implies a body), but it's clearer to always be explicit with `-X`.
- **Forgetting `Content-Type` header:** If your server expects JSON and you don't set `-H "Content-Type: application/json"`, the server may reject your body or misparse it.
- **Quote style on Windows:** In PowerShell, use double quotes around the `-d` argument and escape inner quotes, or use single quotes where PowerShell allows. If you get parse errors, experiment with quote styles.

> **Docs:** [https://curl.se/docs/manpage.html](https://curl.se/docs/manpage.html)

---

### Python `requests` Library

**What it is:** The `requests` library lets you make HTTP calls from Python code. It is the standard way to build programs that *consume* APIs — for example, a script that fetches data from a third-party API or a monitoring agent that polls your own server.

**Installation:**

```
uv add requests
```

**Why use `requests` instead of `curl` or Bruno?** When you want to automate API calls — loop through a list of items, process responses programmatically, handle errors in code, schedule regular requests — you need Python code. `curl` and Bruno are for manual testing; `requests` is for production code.

#### GET — Retrieve Data

```python
import requests

# Send a GET request
response = requests.get("http://localhost:8080/tasks")

# Check the status code
print(response.status_code)  # Expected: 200

# Parse the JSON body into a Python list
tasks = response.json()
print(tasks)
# Example output: [{"id": 1, "title": "Buy groceries", ...}, ...]
```

**Step by step:**
1. `requests.get(url)` — sends a `GET` request to the URL and waits for the response.
2. `response.status_code` — the integer status code (`200`, `404`, etc.).
3. `response.json()` — parses the response body as JSON and returns a Python `dict` or `list`. If the body is `[{...}, {...}]`, you get a Python list.

#### POST — Create Data

```python
import requests

new_task = {"title": "Review notes", "assignee_id": 1}

# The json= parameter automatically:
#   1. Serializes the dict to a JSON string
#   2. Sets Content-Type: application/json header
response = requests.post("http://localhost:8080/tasks", json=new_task)

print(response.status_code)   # Expected: 201
created = response.json()     # The newly created task, with server-assigned ID
print(created)
# Example output: {"id": 7, "title": "Review notes", "assignee_id": 1, ...}
```

> **Important:** Always use `json=` instead of `data=` when sending JSON. The `json=` parameter automatically sets the `Content-Type: application/json` header *and* serializes the dict to JSON for you. Using `data=` sends form-encoded data, not JSON.

#### PUT — Update Data

```python
import requests

updated = {"title": "Updated title", "assignee_id": 1, "details": "..."}
response = requests.put("http://localhost:8080/tasks/5", json=updated)

print(response.status_code)   # Expected: 200
print(response.json())        # The updated task
```

#### DELETE — Remove Data

```python
import requests

response = requests.delete("http://localhost:8080/tasks/5")
print(response.status_code)   # Expected: 200 or 204
```

#### Response Object Reference

The `response` object returned by any `requests` call gives you everything the server sent back:

| Attribute / Method | Type | What It Gives You |
|--------------------|------|-------------------|
| `response.status_code` | `int` | The numeric HTTP status code (`200`, `404`, etc.) |
| `response.json()` | `dict` or `list` | The response body parsed from JSON into Python |
| `response.text` | `str` | The raw response body as a plain string (unprocessed) |
| `response.headers` | dict-like | All response headers; access like `response.headers["Content-Type"]` |

#### Checking for Errors in Code

```python
import requests

response = requests.get("http://localhost:8080/tasks/999")

if response.status_code == 200:
    task = response.json()
    print("Found task:", task)
elif response.status_code == 404:
    error = response.json()
    print("Not found:", error["error"])
else:
    print("Unexpected status:", response.status_code)
```

> **Best Practice:** Always check `response.status_code` before calling `response.json()`. If the server returned an error, the body may contain an error message rather than the data you expected.

---

### Python `httpx` Library

**What it is:** `httpx` is a modern alternative to `requests`. The interface is nearly identical, so everything you learned about `requests` applies directly. The main advantage is that `httpx` supports **asynchronous** programming (useful for building high-performance applications), while `requests` does not.

**Installation:**

```
uv add httpx
```

**Synchronous usage (same as `requests`):**

```python
import httpx

# GET
response = httpx.get("http://localhost:8080/users")
users = response.json()
print(users)

# POST
response = httpx.post(
    "http://localhost:8080/users",
    json={"username": "bob", "email": "bob@example.com"}
)
print(response.status_code)   # Expected: 201
```

**When to use `httpx` vs `requests`:**
- If you are writing a simple script or learning the basics → use `requests` (it has more tutorials and examples online).
- If you are building an async web application (e.g., with FastAPI) → use `httpx`.
- For this course, both are interchangeable.

> **What My Professor Didn't Explain:** "Asynchronous" means a program can start an HTTP request and then do other work while waiting for the response, instead of sitting idle. This matters in high-throughput servers that handle thousands of simultaneous requests. For beginner scripts that run sequentially, synchronous code is simpler and completely adequate.

---

## 11. Putting It All Together

### The Big Picture

Every concept in this guide connects to form one complete picture. Here is how a single API request flows from start to finish:

```
+------------------------------------------+
|              CLIENT                       |
|  (Bruno, curl, Python script, browser)    |
+------------------------------------------+
              |
              | HTTP Request
              | Method + URL + Headers + optional Body
              |
              v
+------------------------------------------+
|              API SERVER                   |
|                                          |
|  +----------+     +------------------+  |
|  |  Router  | --> | Request Handler  |  |
|  |          |     | (Business Logic) |  |
|  | Match URL|     |                  |  |
|  | + method |     |  reads/writes    |  |
|  | to handler     |  data            |  |
|  +----------+     +--------+---------+  |
|                            |            |
|                   +--------v---------+  |
|                   |    Database      |  |
|                   +------------------+  |
|                                          |
+------------------------------------------+
              |
              | HTTP Response
              | Status Code + Headers + JSON Body
              |
              v
+------------------------------------------+
|              CLIENT                       |
|  Reads status code, parses JSON body     |
+------------------------------------------+
```

**Step-by-step:**

1. The **client** constructs an HTTP request: a method (`GET`, `POST`, etc.), a URL (`/tasks/3`), headers (`Content-Type: application/json`), and optionally a JSON body.
2. The request travels over the network to the **server** (in development: `localhost`).
3. The server's **router** inspects the method and URL and directs the request to the appropriate **handler function** (in Flask, this is the function decorated with `@app.route`).
4. The **handler** contains the business logic: validate input, read from or write to the **database**, construct a response.
5. The handler returns an **HTTP response**: a status code, headers, and a JSON body.
6. The response travels back to the **client**, which reads the status code and parses the JSON.

---

### Real-World Example: Server Monitoring

In a server monitoring system, a small **agent** program runs on each machine being monitored. The agent exposes one or more API endpoints that report system metrics (CPU usage, memory usage, disk space, etc.).

A central **monitor script** polls each agent at regular intervals using HTTP:

```
MONITOR SCRIPT                        AGENT API (Flask on the target machine)
      |                                              |
      |  GET /metrics                                |
      | -------------------------------------------> |
      |                                              | [Read CPU, memory from OS]
      |  200 OK                                      |
      |  {                                           |
      |    "cpu_percent": 14.3,                      |
      |    "memory_percent": 61.2,                   |
      |    "disk_percent": 45.0                      |
      |  }                                           |
      | <------------------------------------------- |
      |                                              |
```

**What the monitor script does with the response:**
- If `status_code == 200`: extract the metrics and store them (log them, display them, alert if thresholds are exceeded).
- If the request fails (connection refused, timeout): mark that server as offline and trigger an alert.

This is the same pattern used by production monitoring systems like Prometheus and Datadog — just at a much larger scale. You are learning the foundational architecture that powers real infrastructure.

**Simplified Python monitor script:**

```python
import requests
import time

SERVERS = [
    "http://server-a:8080",
    "http://server-b:8080",
]

while True:
    for server in SERVERS:
        try:
            response = requests.get(f"{server}/metrics", timeout=5)
            if response.status_code == 200:
                metrics = response.json()
                print(f"{server}: CPU={metrics['cpu_percent']}%")
            else:
                print(f"{server}: Unexpected status {response.status_code}")
        except requests.exceptions.ConnectionError:
            print(f"{server}: OFFLINE — connection refused")
        except requests.exceptions.Timeout:
            print(f"{server}: OFFLINE — request timed out")

    time.sleep(30)  # Wait 30 seconds before polling again
```

This script uses every concept from this guide: HTTP methods, status codes, JSON parsing, and the `requests` library.

---

## Summary Table

| Concept | Key Point | Where to Learn More |
|---------|-----------|---------------------|
| **API** | A formal contract between two software components for how they communicate | Section 1 |
| **Client–Server** | The client always initiates; the server always responds | Section 2 |
| **Stateless** | The server remembers nothing between requests — all context must be in every request | Section 2 |
| **HTTP** | The standard protocol (language of rules) for web communication | Section 3 |
| **HTTP Request** | Method + URL + headers + optional body | Section 3.1 |
| **HTTP Response** | Status code + headers + body | Section 3.2 |
| **HTTP Methods** | GET (read), POST (create), PUT (update), DELETE (remove) | Section 4 |
| **Safe** | A method that does not change server data (only GET) | Section 4 |
| **Idempotent** | Repeating the request has the same effect as doing it once | Section 4 |
| **Status Codes** | 2xx = success, 4xx = client error, 5xx = server error | Section 5 |
| **JSON** | The standard text format for API data; similar to Python dicts and lists | Section 6 |
| **Content-Type** | Header that labels the format of the request or response body | Sections 3.3, 6 |
| **URL** | Address of a specific resource on a server | Section 7 |
| **Query String** | Optional `?key=value` parameters at the end of a URL | Section 7 |
| **REST** | Conventions: noun URLs, HTTP verbs for actions, standard status codes, stateless | Section 8 |
| **CRUD** | The four basic data operations: Create → POST, Read → GET, Update → PUT, Delete → DELETE | Section 8 |
| **Error Handling** | Return meaningful status codes *and* a JSON body with error details | Section 9 |
| **Bruno** | VS Code GUI for manually crafting and testing API requests | Section 10 |
| **curl** | Command-line tool for making HTTP requests from the terminal | Section 10 |
| **requests** | Python library for making HTTP calls from code (consuming APIs) | Section 10 |
| **httpx** | Modern Python library — same interface as `requests`, adds async support | Section 10 |

---

## References

- [HTTP Overview — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- [HTTP Methods — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods)
- [HTTP Status Codes — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status)
- [REST API Tutorial](https://restfulapi.net/)
- [JSON Introduction — MDN Web Docs](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/JSON)
- [Bruno Documentation](https://docs.usebruno.com/introduction/what-is-bruno)
- [`curl` Manual](https://curl.se/docs/manpage.html)
- [Python `requests` Library](https://docs.python-requests.org/)
- [Python `httpx` Library](https://www.python-httpx.org/)
- [Flask Quickstart](https://flask.palletsprojects.com/en/stable/quickstart/)
- [HTTP Methods — RFC 7231](https://tools.ietf.org/html/rfc7231#section-4)


---

---

# Part II — How Flask Runs Your Application: WSGI

## Table of Contents

1. [The Big Picture](#1-the-big-picture)
2. [What Happens When a Request Arrives](#2-what-happens-when-a-request-arrives)
3. [The Development Server (Werkzeug)](#3-the-development-server-werkzeug)
   - 3.1 [Starting the Server](#31-starting-the-server)
   - 3.2 [What "Listening on a Port" Actually Means](#32-what-listening-on-a-port-actually-means)
   - 3.3 [Terminal Output When the Server Starts](#33-terminal-output-when-the-server-starts)
   - 3.4 [What the Development Server Gives You](#34-what-the-development-server-gives-you)
   - 3.5 [What the Development Server Is NOT](#35-what-the-development-server-is-not)
   - 3.6 [What "Single-Threaded" Means and Why It Matters](#36-what-single-threaded-means-and-why-it-matters)
4. [WSGI: The Standard Behind the Scenes](#4-wsgi-the-standard-behind-the-scenes)
   - 4.1 [What Problem Does WSGI Solve?](#41-what-problem-does-wsgi-solve)
   - 4.2 [The WSGI Contract (What It Actually Looks Like)](#42-the-wsgi-contract-what-it-actually-looks-like)
   - 4.3 [Where Flask Fits](#43-where-flask-fits)
5. [Development vs. Production](#5-development-vs-production)
   - 5.1 [The Production Stack Explained](#51-the-production-stack-explained)
   - 5.2 [What a Reverse Proxy (Nginx) Actually Does](#52-what-a-reverse-proxy-nginx-actually-does)
   - 5.3 [Comparison Table](#53-comparison-table)
6. [Summary](#6-summary)
7. [References](#7-references)

---

## 1. The Big Picture

### What problem are we solving?

You write a Python function. A browser (or `curl`, or Bruno) sends an HTTP request
over the internet. Somehow, that raw network message has to become a Python function
call — and then your function's return value has to become an HTTP response that travels
back over the network.

**That translation work is what the web server and WSGI are for.**

When you run a Flask application, several layers of software cooperate:

```
+----------------------------+
|  Client                    |
|  (Browser, Bruno, curl)    |
+----------------------------+
            |
            |   HTTP Request (text over TCP/IP)
            v
+----------------------------+
|  Development Server        |
|  (Werkzeug)                |
|                            |
|  - Listens on a port       |
|  - Parses raw HTTP text    |
|  - Speaks the WSGI         |
|    interface               |
+----------------------------+
            |
            |   Python function call (WSGI)
            v
+----------------------------+
|  Flask Framework           |
|                            |
|  - Routes URL to function  |
|  - Converts dicts to JSON  |
|  - Manages errors          |
+----------------------------+
            |
            |   Calls your view function
            v
+----------------------------+
|  Your View Functions       |  <-- THE ONLY PART YOU WRITE
|                            |
|  - Query the database      |
|  - Build and return data   |
+----------------------------+
```

**Key idea:** You write Python functions. Every layer above handles the machinery of
the web — listening for connections, parsing raw HTTP text, routing URLs, converting
data formats, and sending responses back over the network.

---

## 2. What Happens When a Request Arrives

### Step-by-step walkthrough: `GET /users/3`

Recall from your HTTP notes (`110_http_review.md`) that an HTTP request is a text
message with a request line, headers, and an optional body. Let's trace what happens
to that message after it reaches your machine:

```
Client                   Werkzeug (Dev Server)         Flask            Your View Function
  |                              |                        |                     |
  |  GET /users/3                |                        |                     |
  |  HTTP/1.1                    |                        |                     |
  |  Host: localhost:5000        |                        |                     |
  |----------------------------->|                        |                     |
  |                              |                        |                     |
  |                  [Parse HTTP text: extract           |                     |
  |                   method=GET, path=/users/3,         |                     |
  |                   headers, body=empty]               |                     |
  |                              |                        |                     |
  |                              |  WSGI call: environ    |                     |
  |                              |  + start_response      |                     |
  |                              |----------------------->|                     |
  |                              |                        |                     |
  |                              |              [Match URL /users/<int:id>     |
  |                              |               to registered route]          |
  |                              |                        |                     |
  |                              |                        | call get_user(id=3) |
  |                              |                        |-------------------->|
  |                              |                        |                     |
  |                              |                        |   [Query database,  |
  |                              |                        |    build dict]      |
  |                              |                        |                     |
  |                              |                        |  return {"id": 3,   |
  |                              |                        |  "username":"alice"}|
  |                              |                        |<--------------------|
  |                              |                        |                     |
  |                              |  [Convert dict to JSON |                     |
  |                              |   HTTP response object]|                     |
  |                              |<-----------------------|                     |
  |                              |                        |                     |
  |  HTTP/1.1 200 OK             |                        |                     |
  |  Content-Type: application/json                       |                     |
  |                              |                        |                     |
  |  {"id": 3, "username": "alice"}                       |                     |
  |<-----------------------------|                        |                     |
```

### What each layer is responsible for

| Layer | Responsibility | Do you write this? |
|-------|---------------|-------------------|
| **Client** | Sends HTTP requests, displays responses | No (Bruno, curl, browser) |
| **Development server (Werkzeug)** | Listens on a port, parses raw HTTP text, sends responses | No (Werkzeug does this) |
| **Flask framework** | Matches URLs to functions, handles JSON conversion, manages errors | No (Flask does this) |
| **Your view functions** | Business logic — query database, process data, return results | **Yes — this is your job** |

As a beginner, **you only write the bottom layer: view functions.** Everything else is
handled by software that Flask and Werkzeug provide.

---

## 3. The Development Server (Werkzeug)

### 3.1 Starting the Server

When you run either of these commands:

```bash
uv run flask --app app run --debug
```

```bash
uv run python run.py
```

You are actually starting **Werkzeug** — a development web server that is bundled
with Flask. Both commands start the same Werkzeug server. The only difference is
*how* you invoke it: via the Flask CLI, or via a Python entry-point file (`run.py`).

> **What is Werkzeug?** It is a Python library (also called a "toolkit") that
> provides the low-level plumbing for web applications: parsing HTTP requests,
> building HTTP responses, and running a development server. Flask is built on top
> of Werkzeug. You rarely interact with Werkzeug directly — Flask wraps it for you.

---

### 3.2 What "Listening on a Port" Actually Means

When the documentation says the server "listens on port 5000," what does that
actually mean?

**A port is a numbered channel on your computer** for receiving a specific type of
network traffic. Your computer has 65,535 possible ports. Different programs claim
different port numbers:

- Port `80` — standard HTTP (websites)
- Port `443` — standard HTTPS (encrypted websites)
- Port `5000` — Flask's default development port
- Port `8080` — another common development port

**When Werkzeug starts, it:**

1. Asks the operating system (OS): "Give me exclusive access to port 5000."
2. The OS creates a **socket** — a special file-like object that represents the
   network connection endpoint.
3. Werkzeug then enters a loop, waiting for incoming TCP connections on that socket.
4. When a client connects, it reads the raw bytes that arrive, interprets them as
   an HTTP request (text), and processes it.

**Why `localhost:5000`?**

- `localhost` means "this computer" — it resolves to the IP address `127.0.0.1`,
  which loops back to your own machine. Traffic sent to `localhost` never leaves
  your computer.
- `:5000` is the port number.
- Combined: "connect to the process running on *this machine* at port 5000."

**What happens at the OS/network level:**

```
Your OS / Network Stack
+-----------------------------------------+
|                                         |
|  Port 5000  <-- Werkzeug is here        |
|  Port 443   <-- HTTPS (browser uses)    |
|  Port 22    <-- SSH (if enabled)        |
|                                         |
+-----------------------------------------+
         ^
         | TCP connection from curl/Bruno/browser
         |
         | (stays on your machine because
         |  the address is localhost)
```

> **What My Professor Didn't Explain**
>
> When you type `http://localhost:5000/users` in a browser, this is what happens
> at the OS level:
> 1. The browser resolves `localhost` to `127.0.0.1`.
> 2. The OS opens a TCP connection to `127.0.0.1` on port 5000.
> 3. If a process (Werkzeug) is listening on port 5000, the connection succeeds.
> 4. If nothing is listening (the server isn't running), you get a
>    "Connection refused" error. That error means there is no process bound to
>    that port — not a permissions problem or a network problem.

---

### 3.3 Terminal Output When the Server Starts

When you run `uv run flask --app app run --debug`, you will see output like this
in your terminal:

```
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment.
Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 123-456-789
```

Then, every time a request arrives, a new line is printed:

```
127.0.0.1 - - [15/Jan/2025 10:32:01] "GET /users/3 HTTP/1.1" 200 -
127.0.0.1 - - [15/Jan/2025 10:32:04] "POST /users HTTP/1.1" 201 -
127.0.0.1 - - [15/Jan/2025 10:32:09] "GET /users/99 HTTP/1.1" 404 -
```

**Reading the request log line:**

```
127.0.0.1  -  -  [15/Jan/2025 10:32:01]  "GET /users/3 HTTP/1.1"  200  -
^              ^  ^                       ^                          ^    ^
Client IP      |  |                       Full request line          |    Response size
               |  Timestamp                                         Status code
               (auth fields, not used here)
```

- The **client IP** `127.0.0.1` confirms the request came from your own machine
  (e.g., from Bruno or a browser on the same computer).
- The **request line** matches exactly what you learned in the HTTP notes: method +
  path + version.
- The **status code** (`200`, `201`, `404`) lets you instantly see whether each
  request succeeded or failed.

> **Tip:** Keep this terminal window visible while you develop. The request log is
> one of the most useful debugging tools you have.

---

### 3.4 What the Development Server Gives You

Werkzeug's development server includes three features that make local development
much faster:

- **Auto-reload:** When you save a Python file, the server automatically detects the
  change and restarts itself. You never have to manually stop and re-run the server
  during development. (Note: it does *not* auto-reload for changes to non-Python
  files like templates by default.)

- **Interactive error pages:** If your code raises an unhandled exception, instead of
  seeing a generic `500 Internal Server Error`, the browser shows a detailed error
  page with the full Python traceback — including the exact line that failed — and
  even an interactive debugger you can use to inspect variables. **This only works in
  debug mode** (the `--debug` flag).

- **Request logging:** Every incoming HTTP request is printed to the terminal (see
  Section 3.3), so you always know what requests are being made and whether they
  succeeded.

---

### 3.5 What the Development Server Is NOT

The development server is designed purely for convenience during local development.
It is **not** suitable for real-world use:

- **Not secure:** Debug mode exposes a browser-based Python debugger. If someone
  could reach your server, they could execute arbitrary code on your machine.
- **Not fast:** Werkzeug is not optimized for performance.
- **Not robust:** It does not handle errors, crashes, or restarts gracefully in the
  way a production system requires.

> **Warning:** Flask itself reminds you of this every time you start the server:
> `WARNING: This is a development server. Do not use it in a production deployment.`
> This warning is serious. Never run the Werkzeug dev server on a machine that is
> accessible from the internet.

This course uses the development server exclusively. Production deployment is a
separate, more advanced topic.

---

### 3.6 What "Single-Threaded" Means and Why It Matters

The development server handles **one request at a time** (it is single-threaded by
default). To understand why this is a limitation, you need to understand what a
**thread** is.

**A thread is an independent line of execution** — a sequence of instructions that
your CPU can run. A process (like your Python program) can have multiple threads,
each doing work simultaneously.

**Single-threaded means only one thing happens at a time:**

```
Timeline with ONE thread (single-threaded Werkzeug):

Request A arrives ---[processing A takes 2 seconds]---> Response A sent
                                                          |
                                             Request B finally starts
                                           ---[processing B]---> Response B
```

**Multi-threaded means multiple requests are handled in parallel:**

```
Timeline with MULTIPLE threads (production server):

Request A arrives --> [Thread 1 handles A] --> Response A sent
Request B arrives --> [Thread 2 handles B] --> Response B sent  (no wait!)
Request C arrives --> [Thread 3 handles C] --> Response C sent  (no wait!)
```

**Why this is fine for development but not for production:**

When you are developing alone, only you are sending requests — so only one request
at a time is completely acceptable. But a real web application might receive hundreds
or thousands of simultaneous requests. If the server processes them one at a time,
every user after the first has to wait in a queue. Production servers use multiple
threads (or processes, or async I/O) to handle many requests simultaneously.

> **What My Professor Didn't Explain**
>
> You can actually make the Werkzeug dev server multi-threaded by passing
> `threaded=True`:
> ```python
> app.run(debug=True, threaded=True)
> ```
> But this is still not suitable for production — it just removes an artificial
> limitation for local testing when you have multiple browser tabs making requests.

---

## 4. WSGI: The Standard Behind the Scenes

### 4.1 What Problem Does WSGI Solve?

Before WSGI existed, every Python web framework had to be tightly coupled to a
specific web server. If you wrote a Django application, it might only work with
Apache. If you wrote a Flask application, it might only work with one particular
server.

**WSGI solves this by defining a universal contract** — a standardized way for *any*
Python web framework to talk to *any* Python-compatible web server.

Think of it like a power outlet: the outlet (WSGI interface) has a standardized shape.
Any device that follows the standard (any WSGI server) can power any compatible
appliance (any WSGI app, like Flask or Django). You do not need a different outlet
for every device.

```
WITHOUT WSGI (old world):              WITH WSGI (standardized):

Apache ---[special glue]---> Django    Werkzeug --|
Apache ---[different glue]-> Flask                |--(WSGI)--> Flask
Apache ---[more glue]------> Pyramid   Gunicorn --|
                                                  |
Nginx  ---[special glue]---> Django    Waitress --|--(WSGI)--> Django
Nginx  ---[different glue]-> Flask                |
                                       uWSGI -----|--(WSGI)--> Pyramid
```

**WSGI is defined in [PEP 3333](https://peps.python.org/pep-3333/)** — a Python
Enhancement Proposal, which is how the Python community formally defines language
standards.

---

### 4.2 The WSGI Contract (What It Actually Looks Like)

Your professor says you do not need to understand WSGI to write Flask applications —
and that is true for this course. But seeing the actual shape of the WSGI contract
demystifies a lot of confusing error messages and documentation.

**The WSGI interface requires that a Python web application be a callable** (a
function, or an object with a `__call__` method) that accepts exactly two arguments:

1. **`environ`** — a Python dictionary containing everything about the incoming HTTP
   request: the method, path, headers, query string, body, and dozens of other fields
   from the server environment.
2. **`start_response`** — a callback function the application must call to set the
   HTTP status code and response headers before returning the body.

**The minimal WSGI application** (this is *not* Flask code — this is the raw
standard):

```python
def my_wsgi_app(environ, start_response):
    # 1. Read request information from environ
    method = environ["REQUEST_METHOD"]   # e.g. "GET"
    path   = environ["PATH_INFO"]        # e.g. "/users/3"

    # 2. Call start_response to set status + headers
    status = "200 OK"
    headers = [("Content-Type", "application/json")]
    start_response(status, headers)

    # 3. Return the response body as an iterable of byte strings
    return [b'{"id": 3, "username": "alice"}']
```

This is a fully functional (if trivial) WSGI application. **Flask is simply a very
sophisticated wrapper around this exact pattern** — it reads `environ`, calls
`start_response`, and returns a body, but it does all of this automatically based on
your route definitions and view functions.

**What the WSGI server (Werkzeug) does on its side:**

```python
# Simplified — this is conceptually what Werkzeug does:

import socket

server_socket = socket.socket()          # 1. Create a socket
server_socket.bind(("127.0.0.1", 5000)) # 2. Claim port 5000
server_socket.listen()                  # 3. Start listening

while True:
    conn, addr = server_socket.accept() # 4. Wait for a client connection
    raw_http = conn.recv(4096)          # 5. Read raw HTTP text
    environ = parse_http(raw_http)      # 6. Build the environ dict

    response_body = my_wsgi_app(        # 7. Call the Flask app (WSGI callable)
        environ, start_response
    )

    raw_response = build_http_response( # 8. Serialize back to raw HTTP text
        response_body
    )
    conn.sendall(raw_response)          # 9. Send back to client
```

> **What My Professor Didn't Explain**
>
> When you see errors mentioning `WSGIRequestHandler`, `wsgi.input`, or
> `werkzeug.serving`, these are all internal components of this WSGI machinery.
> The error traceback is showing you the layers of code *above* your view function.
> Your bug is almost always in your view function (the bottom of the traceback),
> not in the WSGI plumbing.

---

### 4.3 Where Flask Fits

Flask is a **WSGI application**. The `app` object you create with `Flask(__name__)`
is the WSGI callable — it implements the two-argument interface described above.

```python
from flask import Flask

app = Flask(__name__)   # app is a WSGI-compatible callable

@app.route("/users/<int:user_id>")
def get_user(user_id):
    return {"id": user_id, "username": "alice"}

# Flask translates:
# - environ["REQUEST_METHOD"] + environ["PATH_INFO"]  -->  get_user(user_id=3)
# - return {"id": 3, "username": "alice"}             -->  JSON bytes + headers
```

The `@app.route(...)` decorator registers your function with Flask's internal URL
router. When a request arrives, Flask looks through all registered routes, finds a
match, calls your function, and converts the return value to a proper HTTP response.

**The chain of responsibility:**

```
WSGI Server (Werkzeug)         WSGI App (Flask)          Your Code
       |                              |                       |
       | -- calls app(environ,   --> |                       |
       |           start_response)   | -- routes URL    --> |
       |                              |    calls view fn     |
       |                              |                       |
       |                              | <-- return value  -- |
       | <-- returns response body -- |                       |
       |                              |                       |
       | -- sends HTTP response       |                       |
         to client                    |                       |
```

---

## 5. Development vs. Production

In this course, you will always use the development server. But understanding what
changes in production will help you understand *why* the development setup is built
the way it is.

### 5.1 The Production Stack Explained

**Development (this course):**

```
Client (Browser / Bruno / curl)
        |
        | HTTP
        v
Werkzeug Development Server
        |
        | WSGI
        v
Your Flask Application
```

**Production (beyond this course):**

```
Client (Browsers from around the world)
        |
        | HTTPS (encrypted HTTP)
        v
Reverse Proxy (Nginx)
        |
        | HTTP (internal, within the server)
        v
Production WSGI Server (Gunicorn)
        |
        | WSGI
        v
Your Flask Application   (same code as in development!)
```

Notice that **your Flask application code does not change**. The only thing that
changes is the server infrastructure that runs it.

---

### 5.2 What a Reverse Proxy (Nginx) Actually Does

A **reverse proxy** is a server that sits in front of your application server and
acts as an intermediary. The client never talks directly to Gunicorn — it talks to
Nginx, which forwards the request to Gunicorn.

**Why does production need one?** Several reasons:

1. **TLS/HTTPS termination:** Encrypting traffic requires certificates and complex
   cryptographic code. Nginx handles this so your Python application does not have
   to. By the time the request reaches Gunicorn, it is already decrypted.

2. **Serving static files efficiently:** Images, CSS, and JavaScript files are served
   directly by Nginx without ever touching Python. Nginx is 10-100x faster at serving
   static files than any Python server.

3. **Load balancing:** Nginx can distribute incoming requests across multiple Gunicorn
   processes (even on multiple machines), so no single process is overwhelmed.

4. **Security buffer:** Nginx can reject malformed requests, enforce rate limits, and
   block known malicious patterns before they reach your Python code.

5. **Port 80/443:** Standard HTTP/HTTPS runs on ports 80 and 443. On Linux, only the
   `root` user can bind to ports below 1024. Nginx runs as root (for a moment) and
   then drops privileges, acting as the single entry point, while Gunicorn runs on a
   higher-numbered port (e.g., 8000) without needing root access.

```
Internet
   |
   | HTTPS request on port 443
   v
+---------------------------+
|  Nginx (Reverse Proxy)    |
|                           |
|  - Handles TLS/HTTPS      |
|  - Serves static files    |
|  - Enforces rate limits   |
|  - Load balances          |
+---------------------------+
   |
   | HTTP request on port 8000 (internal)
   v
+---------------------------+
|  Gunicorn                 |
|  (Production WSGI Server) |
|                           |
|  - 4 worker processes     |
|  - Each handles requests  |
|    simultaneously         |
+---------------------------+
   |
   | WSGI call
   v
+---------------------------+
|  Your Flask App           |
|  (same code you wrote)    |
+---------------------------+
```

> **What My Professor Didn't Explain**
>
> Gunicorn is invoked with a command like `gunicorn -w 4 "app:app"`:
> - `-w 4` means "start 4 worker processes" — this is what makes a production server
>   handle multiple simultaneous requests. Each worker is an independent Python
>   process that can handle one request at a time.
> - `"app:app"` means "from the file `app.py`, load the object named `app`" — which
>   is your Flask WSGI callable.

---

### 5.3 Comparison Table

| Aspect | Development | Production |
|--------|-------------|------------|
| **Server** | Werkzeug (built-in to Flask) | Gunicorn, Waitress, uWSGI, etc. |
| **Reverse proxy** | None | Nginx or Apache |
| **Concurrency** | One request at a time | Many simultaneous requests (multiple workers) |
| **Auto-reload** | Yes — saves time during development | No — stability matters more than convenience |
| **Error display** | Detailed traceback in browser | Generic error page to user; details logged to file |
| **HTTPS** | No (plain HTTP, local only) | Yes — handled by Nginx |
| **Static files** | Served by Python/Flask (slow, but fine locally) | Served by Nginx (fast) |
| **Start command** | `uv run flask run --debug` | `gunicorn -w 4 "app:app"` |
| **Your Flask code** | **Identical** | **Identical** |

The last row is the most important. WSGI's standardization means you write your
Flask application once and it runs everywhere — from your laptop's development server
to a production machine serving thousands of users.

---

## 6. Summary

| Concept | Key Point |
|---------|-----------|
| **Development server** | Werkzeug — handles HTTP parsing, listens on a port, auto-reloads, shows error pages |
| **Your code** | View functions — the only layer you write; everything else is handled for you |
| **Flask** | Matches URLs to your functions, converts dicts to JSON responses, manages errors |
| **"Listening on a port"** | The OS assigns the server exclusive access to a numbered channel (e.g., 5000); clients connect to that channel |
| **`localhost:5000`** | `localhost` = this machine only (`127.0.0.1`); `5000` = the port Werkzeug is bound to |
| **Single-threaded** | The dev server processes one request at a time — fine for solo development, unacceptable for production |
| **WSGI** | A Python standard (PEP 3333) that defines how any web server calls any Python app; Flask implements it so you don't have to |
| **WSGI callable** | A function that accepts `(environ, start_response)` — Flask's `app` object *is* this callable |
| **`environ`** | A dict the WSGI server fills with all HTTP request details: method, path, headers, body |
| **Werkzeug** | The WSGI server bundled with Flask; used only for development |
| **Gunicorn** | A production WSGI server — runs multiple worker processes to handle many simultaneous requests |
| **Nginx** | A reverse proxy used in production — handles HTTPS, static files, and load balancing in front of Gunicorn |
| **Production vs. development** | Same Flask code; different (faster, more robust, more secure) server infrastructure |

---

## 7. References

- [Flask Quickstart — A Minimal Application](https://flask.palletsprojects.com/en/stable/quickstart/)
- [Flask Development Server](https://flask.palletsprojects.com/en/stable/server/)
- [Werkzeug Documentation](https://werkzeug.palletsprojects.com/)
- [PEP 3333 — Python WSGI Specification](https://peps.python.org/pep-3333/) (for reference only)
- [Gunicorn Documentation](https://gunicorn.org/)
- [Nginx Beginner's Guide](https://nginx.org/en/docs/beginners_guide.html)
- [MDN — How the Web Works](https://developer.mozilla.org/en-US/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works)
- [Real Python — Flask by Example](https://realpython.com/flask-by-example-part-1-project-setup/)


---

---

# Part III — Flask Introduction

## Table of Contents

1. [Why Flask?](#1-why-flask)
2. [What is Flask? (The Microframework Concept)](#2-what-is-flask-the-microframework-concept)
3. [Flask Concepts Overview](#3-flask-concepts-overview)
4. [Project Layouts](#4-project-layouts)
   - [The Simplest App (Monolithic)](#41-the-simplest-app-monolithic)
   - [A Modular App (Using Blueprints)](#42-a-modular-app-using-blueprints)
5. [The Application Object](#5-the-application-object)
   - [What `Flask(__name__)` Actually Does](#51-what-flask__name__-actually-does)
   - [How `@app.route()` Works Under the Hood](#52-how-approute-works-under-the-hood)
   - [Why `if __name__ == '__main__':` Exists](#53-why-if-__name__--__main__-exists)
   - [Running the App — All Three Methods](#54-running-the-app--all-three-methods)
   - [Terminal Output When the Dev Server Starts](#55-terminal-output-when-the-dev-server-starts)
   - [The Application Factory Pattern (`create_app`)](#56-the-application-factory-pattern-create_app)
   - [Accessing the App Inside Routes: `current_app`](#57-accessing-the-app-inside-routes-current_app)
6. [Views](#6-views)
   - [The `request` Object — Reading Incoming Data](#61-the-request-object--reading-incoming-data)
   - [Returning HTTP Status Codes](#62-returning-http-status-codes)
   - [Using `abort()` to Stop Early](#63-using-abort-to-stop-early)
   - [Function-Based Views](#64-function-based-views)
   - [Class-Based Views (`MethodView`)](#65-class-based-views-methodview)
   - [View Execution Flow (ASCII Diagram)](#66-view-execution-flow-ascii-diagram)
7. [Routes](#7-routes)
   - [What is an Endpoint?](#71-what-is-an-endpoint)
   - [Static Routes](#72-static-routes)
   - [Dynamic Routes and URL Converters](#73-dynamic-routes-and-url-converters)
   - [HTTP Methods on a Route](#74-http-methods-on-a-route)
   - [Route Matching Process (ASCII Diagram)](#75-route-matching-process-ascii-diagram)
   - [URL Building with `url_for()`](#76-url-building-with-url_for)
8. [Blueprints](#8-blueprints)
   - [The Monolithic File Problem](#81-the-monolithic-file-problem)
   - [Namespace Collisions](#82-namespace-collisions)
   - [Creating and Registering a Blueprint](#83-creating-and-registering-a-blueprint)
   - [What `url_prefix` Does Concretely](#84-what-url_prefix-does-concretely)
   - [Blueprint as a Package (The Real-World Pattern)](#85-blueprint-as-a-package-the-real-world-pattern)
   - [Relative Imports — Connecting Files Inside a Package](#86-relative-imports--connecting-files-inside-a-package)
   - [Circular Imports and Import Order in `__init__.py`](#87-circular-imports-and-import-order-in-__init__py)
   - [Blueprint Registration and Request Handling (ASCII Diagram)](#88-blueprint-registration-and-request-handling-ascii-diagram)
   - [Application Lifecycle with Blueprints (ASCII Diagram)](#89-application-lifecycle-with-blueprints-ascii-diagram)
9. [Models (Brief Overview)](#9-models-brief-overview)
10. [Templates](#10-templates)
    - [What is `render_template`?](#101-what-is-render_template)
    - [Jinja2 Syntax](#102-jinja2-syntax)
    - [Template Inheritance](#103-template-inheritance)
    - [Dictionary Unpacking for Context](#104-dictionary-unpacking-for-context)
    - [Template Rendering Process (ASCII Diagram)](#105-template-rendering-process-ascii-diagram)
11. [The Full Request/Response Cycle](#11-the-full-requestresponse-cycle)
    - [Full Cycle ASCII Diagram](#111-full-cycle-ascii-diagram)
12. [Additional Resources](#12-additional-resources)

---

## 1. Why Flask?

Python has many libraries for building web applications and APIs. This course uses [Flask](http://flask.pocoo.org/) for these reasons:

- **Simple and beginner-friendly.** The minimum working Flask application is five lines of Python. You do not need to configure dozens of files before seeing results.
- **Excellent documentation.** The [Flask Mega-Tutorial by Miguel Grinberg](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) is one of the most thorough beginner-to-advanced resources for any Python web framework.
- **It teaches fundamentals.** Because Flask does not make decisions for you (unlike larger frameworks), you learn *why* each piece exists rather than just accepting defaults.
- **It is widely used in industry** for JSON REST APIs, internal tools, and microservices.

---

## 2. What is Flask? (The Microframework Concept)

### What "microframework" means

A **framework** is code that provides structure for building an application — it handles common tasks so you do not have to. A **microframework** is a framework that deliberately includes only the essentials and leaves everything else optional.

Flask gives you:
- HTTP request parsing (reading what the client sent)
- URL routing (deciding which Python function runs for each URL)
- HTTP response building (sending data back to the client)
- A built-in development server
- A Jinja2 templating engine (for rendering HTML)
- Automatic JSON serialization when you return a Python dictionary

Flask does **not** give you (by default):
- A database layer
- An authentication system
- An admin interface
- Form validation

You add these via third-party extensions when you need them. This is the "micro" philosophy: start small, add only what your project needs.

### Flask and the concepts you already know

Flask is built directly on top of concepts from your previous guides:

- **HTTP (Guide 110):** Every request that arrives at Flask is an HTTP request. The methods (`GET`, `POST`, `PUT`, `DELETE`), status codes (`200`, `404`, `201`), headers, and JSON bodies you studied in Guide 110 are the same data Flask reads and writes.
- **WSGI (Guide 111):** Flask implements the WSGI standard. When a web server (like Gunicorn or Werkzeug's dev server) receives an HTTP request, it calls Flask through the WSGI interface. Flask processes the request and returns a WSGI-compatible response. You do not interact with WSGI directly — Flask handles it — but knowing it exists explains *why* Flask works with any WSGI-compatible server.
- **Werkzeug:** Flask is built on [Werkzeug](https://werkzeug.palletsprojects.com/), a WSGI utility library. Werkzeug does the low-level HTTP parsing (reading raw bytes from the network and turning them into Python objects). Flask builds a developer-friendly API on top of Werkzeug.

**In short:** Werkzeug reads HTTP bytes → Flask organizes those bytes into Python objects → your code runs → Flask turns your Python response back into HTTP bytes → Werkzeug sends them over the network.

---

## 3. Flask Concepts Overview

Before diving into each concept, here is a map of the six building blocks Flask uses. Every Flask application is assembled from these parts:

| Concept | What it is | Where it lives |
|---|---|---|
| **Application** | The central `Flask` object. The entry point for everything. | `app.py` or `app/__init__.py` |
| **Models** | Python classes that define your data structure. | `models.py` |
| **Views** | Python functions (or classes) that handle HTTP requests and return responses. | `routes.py` or any file with route decorators |
| **Routes** | URL patterns that map to view functions. | Defined with `@app.route()` or `@bp.route()` |
| **Blueprints** | Reusable modules that group related routes, views, and resources. | Subdirectories like `app/api/` |
| **Templates** | Jinja2 HTML files for rendering dynamic web pages. | A `templates/` folder |

---

## 4. Project Layouts

There are two common ways to organize a Flask project. You will start with the simpler one and grow into the modular one as your application expands.

### 4.1 The Simplest App (Monolithic)

For learning or very small projects, everything lives in a single file:

```text
simple_app/
├── app.py              # Application + Models + Routes + Views — all in one file
└── pyproject.toml      # Project dependencies (managed by uv)
```

**When to use this:** When you are learning, prototyping, or building something with fewer than ~5 routes. Once you have more than one "feature area" (e.g., user management AND product management), you should move to the modular layout.

**What `app.py` does in this layout:**
- Creates the Flask application instance (`app = Flask(__name__)`)
- Defines any data models
- Registers routes with `@app.route()` decorators
- Defines the view functions below each decorator

### 4.2 A Modular App (Using Blueprints)

As the application grows, you separate concerns into different files and folders:

```text
modular_app/
├── app/
│   ├── __init__.py       # Application factory (creates and configures the Flask app)
│   ├── models.py         # All database model classes
│   ├── api/              # Blueprint: handles JSON API routes
│   │   ├── __init__.py   # Creates the api Blueprint instance
│   │   └── routes.py     # All routes and views for /api/...
│   └── web/              # Blueprint: handles HTML page routes
│       ├── __init__.py   # Creates the web Blueprint instance
│       ├── routes.py     # All routes and views for web pages
│       └── templates/    # HTML templates specific to this blueprint
│           └── index.html
├── run.py                # Entry point: creates the app and starts the dev server
└── pyproject.toml        # Project dependencies
```

**File responsibilities in detail:**

- **`run.py`**: The entry script. It imports `create_app` from your `app` package, calls it to get a configured Flask instance, and starts the development server. It is the only file you run directly.
- **`app/__init__.py`**: The application factory. This is where `Flask(...)` is called, blueprints are registered, and any extensions (database, login manager, etc.) are initialized.
- **`app/models.py`**: All your database model classes live here, separate from routing logic.
- **`app/api/__init__.py`**: Creates the `api` Blueprint object (`api_bp = Blueprint(...)`). Also imports the `routes` module to register route decorators.
- **`app/api/routes.py`**: All the view functions for the API, decorated with `@api_bp.route(...)`.
- **`app/web/templates/`**: HTML templates that belong specifically to the `web` blueprint.

> **Note:** A blueprint physically maps to an entire directory. The directory, its `__init__.py`, its `routes.py`, and its `templates/` folder together form one blueprint.

---

## 5. The Application Object

### 5.1 What `Flask(__name__)` Actually Does

The first thing any Flask app does is create a `Flask` object:

```python
from flask import Flask

app = Flask(__name__)
```

This single line does several important things. Let's break it apart.

#### What is `__name__`?

`__name__` is a built-in Python variable that Python automatically sets in every module. Its value depends on *how* the module is being run:

| How the file is used | Value of `__name__` |
|---|---|
| Run directly: `python app.py` | `'__main__'` |
| Imported by another file: `import app` | `'app'` |
| Imported as part of a package: `from mypackage import app` | `'mypackage.app'` |

**Why does Flask need `__name__`?**

Flask uses the value of `__name__` to figure out where to look for resources that belong to your application — specifically, the `templates/` folder and the `static/` folder. Flask needs to know the root directory of your application, and it discovers this from the module name you pass in.

- If you pass `__name__` and the file is `app.py`, Flask knows to look for `templates/` in the same directory as `app.py`.
- If you pass something incorrect (like a hardcoded string that is wrong), Flask might look in the wrong directory and fail to find your templates.

**Rule:** Always pass `__name__` to `Flask(...)`. Never hardcode a module name here.

#### What the `Flask` constructor does

When you call `Flask(__name__)`, it:
1. Records the module name (to find templates and static files)
2. Creates an empty URL routing table (routes will be added next)
3. Sets up default configuration (debug mode off, secret key empty, etc.)
4. Prepares internal machinery for the request/response cycle

The result is stored in `app` — a Python object that represents your entire web application.

### 5.2 How `@app.route()` Works Under the Hood

You will see this pattern constantly in Flask:

```python
@app.route('/api/status')
def status():
    return {"status": "ok"}
```

If you have not worked with decorators before, this syntax might look magical. Here is what is actually happening.

#### What a decorator is

A **decorator** is a function that takes another function as input and returns a modified version of it. The `@` syntax is just shorthand for applying a decorator. These two blocks are identical:

```python
# Using @ syntax (the normal way)
@app.route('/api/status')
def status():
    return {"status": "ok"}

# Equivalent without @ syntax
def status():
    return {"status": "ok"}
status = app.route('/api/status')(status)
```

#### What `app.route()` does with your function

`app.route('/api/status')` does the following:
1. Takes the URL string `'/api/status'` as an argument.
2. Returns a decorator function.
3. That decorator is applied to `status()`.
4. Internally, Flask calls `app.add_url_rule('/api/status', 'status', status)`, which registers the mapping: URL `/api/status` → function `status`.

Flask stores this mapping in an internal routing table. When a request arrives for `/api/status`, Flask looks up the table and calls the matching function.

**The routing table after registering two routes looks like:**

```
URL Pattern          | Endpoint Name | View Function
---------------------|---------------|---------------
/api/status          | status        | status()
/api/data            | get_data      | get_data()
```

> **What you professor didn't explain:** The endpoint name (second column) defaults to the function's name. This name is what you pass to `url_for()` when you want to build a URL without hardcoding it. With blueprints, the endpoint is prefixed: `'api.status'` instead of just `'status'`.

### 5.3 Why `if __name__ == '__main__':` Exists

You will often see this at the bottom of `app.py`:

```python
if __name__ == '__main__':
    app.run(debug=True)
```

**The problem this solves:**

Suppose another file wants to `import app` to use the `app` object (for testing, for example). Without the guard, importing `app.py` would immediately start the web server — which is not what you want when you are just importing.

The guard prevents this. It says: "only call `app.run()` if this file is being run *directly* by Python, not imported by another module."

```
File is run directly:   __name__ == '__main__'  → True  → server starts
File is imported:       __name__ == 'app'        → False → server does NOT start
```

**When to use it:** In simple single-file apps. In modular apps with an application factory (`create_app()`), this pattern moves to `run.py`.

### 5.4 Running the App — All Three Methods

There are three ways to start a Flask application. Each is appropriate for a different situation.

#### Method 1: `uv run python app.py` — Basic, Not Recommended for Growth

When your file has `if __name__ == '__main__': app.run(debug=True)`, you can run:

```bash
uv run python app.py
```

- **`uv run`:** Executes the following command inside your project's virtual environment (defined by `pyproject.toml`). This ensures you use the right Python version and installed packages.
- **`python app.py`:** Runs your file as a Python script.
- Flask starts a development server at `http://127.0.0.1:5000`.

**Limitation:** As your project grows and you adopt an application factory, there is no longer a single `app.py` to run directly. This method does not scale to modular apps.

#### Method 2: `uv run flask --app app run --debug` — Recommended

The modern, standard approach:

```bash
uv run flask --app app run --debug
```

Breaking down each piece:

| Part | What it does |
|---|---|
| `uv run` | Runs the command inside the project's managed virtual environment |
| `flask` | Invokes the Flask CLI (command-line interface) |
| `--app app` | Tells Flask to find the application in a module named `app`. Flask looks for a variable named `app` or a function named `create_app()` inside `app.py` or `app/__init__.py` |
| `run` | Tells the Flask CLI to start the development server |
| `--debug` | Enables two features: (1) **auto-reload** — the server restarts when you save code changes, (2) **interactive debugger** — if your code throws an exception, you see a detailed traceback in the browser |

> **Common beginner mistake:** Using `--debug` in production. The interactive debugger allows arbitrary Python code execution in the browser — a massive security hole. Only use `--debug` on your local machine during development.

> **Shortcut:** If your file is named `app.py` or `wsgi.py`, Flask finds it automatically. You can omit `--app app` in that case.

#### Method 3: `uv run python run.py` — Modular Applications

When you use the application factory pattern with a `run.py` entry script:

```python
# run.py
from app import create_app

app = create_app()

if __name__ == '__main__':
    app.run(debug=True)
```

```bash
uv run python run.py
```

This is equivalent to Method 1 but works with the modular layout. You can also use the Flask CLI with a modular app:

```bash
uv run flask --app app run --debug
# Flask finds create_app() inside app/__init__.py automatically
```

> **Production note:** In production, you replace the dev server with a production-grade WSGI server like [Gunicorn](https://gunicorn.org/): `uv run gunicorn -w 4 "run:app"`. The `run:app` syntax means "look in `run.py`, use the variable named `app`." The `--debug` flag is never used in production.

### 5.5 Terminal Output When the Dev Server Starts

When you run the Flask development server, you will see output like this in your terminal:

```
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment.
Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 123-456-789
```

**What each line means:**

- `Serving Flask app 'app'` — Flask found your application in the module named `app`.
- `Debug mode: on` — Auto-reload and the interactive debugger are active.
- `WARNING: This is a development server...` — A reminder not to use this server in production.
- `Running on http://127.0.0.1:5000` — The address and port your server is listening on. `127.0.0.1` means "localhost" (only your own machine can reach it). Port `5000` is Flask's default.
- `Restarting with stat` — The reloader is watching your files for changes.
- `Debugger PIN` — A PIN code you would need to use the interactive browser debugger.

When a request comes in, you will also see a log line like:

```
127.0.0.1 - - [15/Jan/2025 10:32:45] "GET /api/status HTTP/1.1" 200 -
```

This shows: the client IP, the timestamp, the HTTP method and path, the HTTP version, and the status code returned.

### 5.6 The Application Factory Pattern (`create_app`)

**The problem with a global `app` object:**

In a simple app, you create `app = Flask(__name__)` at the top of the file. Every other file can import it. This works, but it creates problems:

1. You cannot easily create different configurations for testing vs. production (the app is created once at import time, not when you need it).
2. Blueprints that import `app` create circular import issues (Blueprint module imports `app`, but `app/__init__.py` imports the Blueprint module — each is waiting for the other).

**The solution — the application factory:**

Instead of creating the app at module level, you create it inside a function called `create_app()`. Nothing is created until you *call* the function.

```python
# app/__init__.py
from flask import Flask

def create_app(config=None):
    """Application factory function. Call this to create a configured Flask app."""
    # 1. Create the Flask instance
    app = Flask(__name__)

    # 2. Load configuration
    app.config["APP_NAME"] = "My Service"
    app.config["DEBUG"] = False
    if config:
        app.config.update(config)

    # 3. Register blueprints
    #    Imports are done HERE (inside the function) to avoid circular imports
    from .api import api_bp
    app.register_blueprint(api_bp)

    # 4. Return the configured app
    return app
```

**Benefits:**
- You can call `create_app({"TESTING": True})` in tests to get a fresh test instance.
- Blueprints are imported inside the function, after `app` is created, avoiding circular imports.
- Multiple instances with different configs can coexist (rarely needed, but possible).

**How Flask CLI finds `create_app`:**

When you run `uv run flask --app app run`, Flask imports the `app` module and looks for either a variable named `app` or a callable named `create_app`. If it finds `create_app`, it calls it to get the application instance.

### 5.7 Accessing the App Inside Routes: `current_app`

In the factory pattern, the `app` variable is local to the `create_app()` function. It is not a global variable that your route files can import. So how do your routes access app-level configuration?

Flask provides a special object called **`current_app`** for this purpose.

```python
from flask import current_app
```

`current_app` is a **context proxy** — it is not the `app` object itself, but a reference that always points to the app that is currently handling the active HTTP request. When Flask receives a request, it pushes an "application context" that includes the current app, and `current_app` reads from that context.

**Example usage:**

```python
# app/__init__.py — Store a value in app.config during setup
def create_app():
    app = Flask(__name__)
    app.config["DATABASE_URL"] = "postgresql://localhost/mydb"
    from .api import api_bp
    app.register_blueprint(api_bp)
    return app
```

```python
# app/api/routes.py — Read it back inside a route
from flask import current_app
from . import api_bp

@api_bp.route("/debug-info")
def debug_info():
    db_url = current_app.config["DATABASE_URL"]
    app_name = current_app.config["APP_NAME"]
    return {"database": db_url, "app": app_name}
```

`app.config` is a dictionary. You can store any Python object in it: strings, numbers, database connection objects, service clients — anything your routes need to share.

> **Warning:** `current_app` only works while Flask is handling a request (or while you are in an application context). Accessing `current_app` at module import time (outside a view function) will raise a `RuntimeError: Working outside of application context`. Always use it inside a view function or inside a `with app.app_context():` block.

---

## 6. Views

A **view** is a Python function (or class) that handles one type of HTTP request and returns a response. Views are where your application's logic lives.

- A view reads data from the incoming request (URL parameters, JSON body, headers).
- It does the work: queries a database, validates input, performs calculations.
- It builds a response: a dictionary (auto-converted to JSON), HTML, a status code, etc.
- It returns the response to Flask, which sends it to the client.

### 6.1 The `request` Object — Reading Incoming Data

Inside any view function, Flask makes the `request` object available. You import it from flask:

```python
from flask import request
```

This object contains everything about the incoming HTTP request. The three most common uses are:

#### `request.args` — Query String Parameters

Query string parameters appear after a `?` in the URL. For example, the URL `GET /api/messages?page=2&per_page=10` has two query string parameters.

```python
@app.route('/api/messages')
def get_messages():
    # URL: GET /api/messages?page=2&per_page=10
    page = request.args.get('page', 1)       # Returns '2' (string), defaults to 1
    per_page = request.args.get('per_page', 20)  # Returns '10' (string), defaults to 20
    page = int(page)
    per_page = int(per_page)
    # ... fetch messages with pagination ...
    return {"page": page, "per_page": per_page, "messages": []}
```

> **Gotcha:** `request.args` values are always strings, even if the URL contains a number. You must convert them yourself with `int()` or `float()`. Use `request.args.get('key', default)` instead of `request.args['key']` to avoid a `KeyError` when the parameter is missing.

#### `request.get_json()` — Parsed JSON Body

When a client sends a `POST` or `PUT` request with a JSON body (e.g., `Content-Type: application/json`), you read it with:

```python
@app.route('/api/messages', methods=['POST'])
def create_message():
    # Client sent: POST /api/messages
    # Body: {"title": "Hello", "content": "World"}
    data = request.get_json()

    if data is None:
        return {"error": "Request body must be valid JSON"}, 400

    title = data.get('title')
    content = data.get('content')

    if not title or not content:
        return {"error": "title and content are required"}, 400

    # ... save to database ...
    return {"status": "created", "title": title}, 201
```

> **Difference from `request.args`:**
> - `request.args`: reads parameters from the **URL** (e.g., `?page=2`). No body needed. Works with `GET` requests.
> - `request.get_json()`: reads the **request body**, parsed as JSON. Used when the client sends data in the body of `POST`/`PUT` requests.

> **Gotcha:** `request.get_json()` returns `None` if the request body is empty, malformed JSON, or if the `Content-Type` header is not `application/json`. Always check for `None` before using the result.

#### `request.method` — Which HTTP Method Was Used

```python
@app.route('/api/messages', methods=['GET', 'POST'])
def messages():
    if request.method == 'POST':
        data = request.get_json()
        # ... create a message ...
        return {"status": "created"}, 201
    else:
        # GET
        return {"messages": []}
```

> **Better approach:** Use `MethodView` (covered in section 6.5) instead of `if request.method == ...` branching. It makes the code much cleaner.

### 6.2 Returning HTTP Status Codes

From your HTTP guide (Guide 110), you know that every HTTP response has a status code that tells the client what happened. By default, Flask returns status `200 OK`. To return a different code, provide it as the second value in a tuple:

```python
# Returns 200 (default — no need to specify)
return {"status": "ok"}

# Returns 201 Created — use when a new resource was successfully created
return {"status": "created", "id": new_id}, 201

# Returns 400 Bad Request — use when the client sent invalid data
return {"error": "Missing required field: username"}, 400

# Returns 404 Not Found — use when the requested resource does not exist
return {"error": "User not found"}, 404

# Returns 403 Forbidden — use when the client is authenticated but not allowed
return {"error": "You do not have permission to delete this"}, 403
```

**The pattern:** `return response_body, status_code`

The response body is typically a Python dictionary (Flask automatically converts it to JSON). The status code is an integer.

> **Why status codes matter:** A well-designed API always returns the appropriate status code. If you create a resource and return `200` instead of `201`, clients that rely on status codes (like test suites or other services) will behave incorrectly. Use the correct code — it is part of the HTTP contract.

### 6.3 Using `abort()` to Stop Early

Sometimes you want to immediately stop processing a request and return an error — for example, if a required record does not exist. Flask provides `abort()` for this:

```python
from flask import abort

@app.route('/api/users/<int:user_id>')
def get_user(user_id):
    user = find_user(user_id)  # Returns None if not found

    if user is None:
        abort(404)  # Immediately stops execution. Returns a 404 response.
        # No code below this line runs.

    return {"id": user.id, "name": user.name}
```

`abort()` raises a special exception that Flask catches and converts into an HTTP error response. Code after `abort()` never runs.

**`abort()` vs. `return {...}, 404`:**

Both work. The difference is intent and flow:
- `return {"error": "..."}, 404` — you are constructing a custom error response body.
- `abort(404)` — you want Flask to immediately stop and send its default error response. Good for quick guards where you do not need a custom message.

### 6.4 Function-Based Views

The simplest kind of view: a plain Python function with a route decorator.

```python
from flask import request

@app.route('/api/messages')
def get_messages():
    """
    Handles GET /api/messages.
    Returns a JSON object containing a list of messages.
    """
    # In a real app, you would fetch these from a database
    messages_data = [
        {"id": 1, "text": "Hello World!"},
        {"id": 2, "text": "Learning Flask is fun."}
    ]

    # You can read query parameters to filter or paginate
    # Example: GET /api/messages?limit=1
    limit = request.args.get('limit')
    if limit:
        messages_data = messages_data[:int(limit)]

    # Return a dict — Flask auto-converts it to JSON with status 200
    return {"messages": messages_data, "count": len(messages_data)}
```

**What happens step by step when a client calls `GET /api/messages`:**
1. Flask receives the HTTP request.
2. Flask looks up `/api/messages` in its routing table.
3. Flask calls `get_messages()`.
4. `get_messages()` builds a Python dictionary.
5. Flask converts the dictionary to a JSON string and sets `Content-Type: application/json`.
6. Flask sends an HTTP response with status `200 OK` and the JSON body.
7. The client receives the JSON.

### 6.5 Class-Based Views (`MethodView`)

Function-based views work fine for simple cases, but for a REST API with multiple methods (`GET`, `POST`, `PUT`, `DELETE`), a single function with `if request.method == 'POST':` branching gets messy fast.

`MethodView` solves this by automatically dispatching requests to different methods based on the HTTP method. You define one method per HTTP verb:

```python
from flask.views import MethodView
from flask import request, abort

class UserAPI(MethodView):
    """Handles all HTTP methods for the /users/ and /users/<id> endpoints."""

    def get(self, user_id):
        """Handles GET /users/ (list) and GET /users/<id> (single)."""
        if user_id is None:
            # List all users
            users = [{"id": 1, "name": "Alice"}, {"id": 2, "name": "Bob"}]
            return {"users": users, "count": len(users)}
        else:
            # Return a single user
            user = find_user(user_id)
            if user is None:
                abort(404)
            return {"id": user.id, "name": user.name}

    def post(self):
        """Handles POST /users/ — create a new user."""
        data = request.get_json()
        if not data or 'name' not in data:
            return {"error": "name is required"}, 400
        # ... save to database ...
        new_id = 42  # would come from DB
        return {"status": "created", "id": new_id}, 201

    def put(self, user_id):
        """Handles PUT /users/<id> — update an existing user."""
        user = find_user(user_id)
        if user is None:
            abort(404)
        data = request.get_json()
        # ... update user ...
        return {"status": "updated", "id": user_id}

    def delete(self, user_id):
        """Handles DELETE /users/<id> — remove a user."""
        user = find_user(user_id)
        if user is None:
            abort(404)
        # ... delete from database ...
        return {"status": "deleted", "id": user_id}
```

**Registering a `MethodView` with routes:**

`MethodView` cannot use the `@app.route()` decorator directly (because one class handles multiple URLs). Instead, you use `add_url_rule`:

```python
# Convert the class to a view function with a name
user_view = UserAPI.as_view('user_api')

# Route: GET /users/ — list all (user_id=None because of defaults)
app.add_url_rule('/users/',
                 defaults={'user_id': None},
                 view_func=user_view,
                 methods=['GET'])

# Route: POST /users/ — create new
app.add_url_rule('/users/',
                 view_func=user_view,
                 methods=['POST'])

# Route: GET, PUT, DELETE /users/<id> — single user operations
app.add_url_rule('/users/<int:user_id>',
                 view_func=user_view,
                 methods=['GET', 'PUT', 'DELETE'])
```

> **Why `defaults={'user_id': None}`?** The `GET /users/` route does not have a `user_id` in the URL, but the `get()` method has a `user_id` parameter. Setting `defaults={'user_id': None}` tells Flask to call `get(user_id=None)` for that URL, so your method can check `if user_id is None` to decide whether to return a list or a single item.

> **With Blueprints:** Replace `app.add_url_rule(...)` with `my_blueprint.add_url_rule(...)`. Everything else is identical.

**Why use `MethodView`?**
- Each HTTP verb gets its own clean method. No `if/elif` branching.
- You can inherit from a `MethodView` subclass to share common behavior (like authentication checking) across multiple view classes.
- The code reads like a description of the API: `get`, `post`, `put`, `delete`.

### 6.6 View Execution Flow (ASCII Diagram)

```
HTTP Request arrives
        |
        v
+------------------+
| Flask URL Router |  (matches URL to a registered view function)
+------------------+
        |
        v
+---------------------------+
|    View Function          |
|  1. Read request.args     |  <-- query params from URL (?page=2)
|  2. Read request.get_json |  <-- JSON body from POST/PUT
|  3. Execute business logic|  <-- query database, validate, etc.
|  4. Build response dict   |  <-- {"status": "ok", "data": [...]}
+---------------------------+
        |
        v
+---------------------------+
| Flask Response Serializer |  (converts dict to JSON string)
+---------------------------+
        |
        v
HTTP Response sent to client
(JSON body, status code, headers)
```

---

## 7. Routes

**Routes** are the mapping between URL patterns and view functions. A route is how Flask knows which Python function to call when a specific URL is requested.

Think of it like a telephone switchboard: the incoming call (HTTP request) arrives, the switchboard operator (Flask router) looks at the number dialed (the URL and HTTP method), and connects the call to the right extension (view function).

### 7.1 What is an Endpoint?

The word **endpoint** has two related meanings in Flask:

**Meaning 1 (REST/HTTP):** An endpoint is a specific URL + HTTP method combination that a client can call to perform one operation. For example:
- `GET /api/users` — retrieve a list of users
- `POST /api/users` — create a new user
- `GET /api/users/42` — retrieve user with ID 42
- `DELETE /api/users/42` — delete user with ID 42

All four are different endpoints, even though two of them share the URL `/api/users/42`.

**Meaning 2 (Flask internal):** Endpoint also refers to the *name* Flask uses internally to identify a route. By default, the endpoint name is the function's name. You use endpoint names with `url_for()` to generate URLs without hardcoding them.

```python
@app.route('/api/users')
def list_users():       # <-- endpoint name is 'list_users'
    ...

# Generate the URL for this endpoint:
url_for('list_users')  # returns '/api/users'
```

With blueprints, the endpoint name is prefixed with the blueprint name:
```python
url_for('api.list_users')  # 'api' is the blueprint name
```

### 7.2 Static Routes

A **static route** has a fixed URL — no variables. It always maps to the same function.

```python
@app.route('/api/status')
def status():
    return {"status": "ok", "version": "1.0"}

@app.route('/api/config')
def config():
    return {"max_items_per_page": 100, "allowed_formats": ["json"]}
```

When is a static route appropriate? When the URL does not need to identify a specific resource. `/api/status` does not identify *which* status — it is just a fixed health-check endpoint.

### 7.3 Dynamic Routes and URL Converters

A **dynamic route** captures part of the URL as a variable and passes it to the view function as a parameter.

```python
@app.route('/api/users/<username>')
def get_user_by_name(username):
    # username is a string captured from the URL
    # GET /api/users/alice  →  username = 'alice'
    return {"username": username}
```

The `<username>` part is a **URL variable**. Flask extracts the value at that position in the URL and passes it as a keyword argument to the function.

#### URL Converters

By default, URL variables are strings. You can add a **converter** to automatically parse and validate the type:

| Converter | Syntax | What it matches | Python type | Example URL | Value passed |
|---|---|---|---|---|---|
| (none) | `<name>` | Any text without `/` | `str` | `/users/alice` | `'alice'` |
| `int` | `<int:id>` | Positive integers only | `int` | `/users/42` | `42` |
| `float` | `<float:price>` | Decimal numbers | `float` | `/prices/9.99` | `9.99` |
| `path` | `<path:subpath>` | Text including `/` | `str` | `/files/img/logo.png` | `'img/logo.png'` |
| `uuid` | `<uuid:item_id>` | UUID format strings | `UUID` | `/items/550e8400-...` | UUID object |

```python
# int converter: /api/messages/42  →  message_id = 42 (integer, not string '42')
@app.route('/api/messages/<int:message_id>')
def get_message(message_id):
    # message_id is already an int — no need for int(message_id)
    if message_id < 1:
        abort(400)
    return {"id": message_id, "content": "Sample content"}
```

> **Why converters matter:** Without `<int:message_id>`, the URL `/api/messages/hello` would match and pass the string `'hello'` to your function — which might crash if your code expects a number. With `<int:message_id>`, Flask rejects any URL where that segment is not a valid integer, returning `404` automatically.

> **Common mistake:** Writing `/api/messages/<message_id>` (without the `int:` prefix) and then trying to use `message_id` as an integer. It is a string — you must convert it, or use the converter.

### 7.4 HTTP Methods on a Route

By default, a route only accepts `GET` requests. To accept other methods, specify them in the `methods` argument:

```python
@app.route('/api/messages', methods=['GET', 'POST'])
def messages():
    if request.method == 'POST':
        data = request.get_json()
        # ... create a new message ...
        return {"status": "created", "data": data}, 201
    # GET
    return {"messages": [{"id": 1, "text": "Hello"}]}
```

If a client sends a `DELETE` request to this URL, Flask automatically returns `405 Method Not Allowed` — you do not have to write that check yourself.

**Matching HTTP methods to CRUD operations (from your REST/HTTP notes):**

| HTTP Method | CRUD Operation | Typical use |
|---|---|---|
| `GET` | Read | Retrieve a resource or list |
| `POST` | Create | Create a new resource |
| `PUT` / `PATCH` | Update | Update an existing resource |
| `DELETE` | Delete | Remove a resource |

### 7.5 Route Matching Process (ASCII Diagram)

When Flask receives a request, it goes through this decision process:

```
Incoming HTTP Request: GET /api/messages/42
                |
                v
        +-------------------+
        | Parse request URL |
        | Extract: path     |
        | and method        |
        +-------------------+
                |
                v
        +-------------------------+
        | Scan registered routes  |
        |  /api/status            |
        |  /api/messages          |
        |  /api/messages/<int:id> | <-- matches! id=42
        +-------------------------+
                |
        +-------+------+
        |              |
      Match?         No match
        |              |
        v              v
  Is method        Return 404
  allowed?         Not Found
     |
  +--+--+
  |     |
 Yes    No
  |     |
  v     v
Call  Return 405
view  Method Not
func  Allowed
  |
  v
Execute get_message(message_id=42)
  |
  v
Return HTTP response
```

### 7.6 URL Building with `url_for()`

Instead of hardcoding URLs in your templates and code, Flask provides `url_for()` to generate URLs from endpoint names. This is important because if you later change a URL pattern (e.g., from `/users/` to `/api/v2/users/`), you only change it in one place — the route definition — and `url_for()` everywhere else updates automatically.

```python
from flask import url_for

# Inside a view function or template:
url_for('list_users')          # Returns '/api/users'
url_for('get_message', message_id=42)  # Returns '/api/messages/42'

# With a blueprint (blueprint name is the prefix):
url_for('api.list_users')      # Returns '/api/users'
url_for('api.get_message', message_id=42)  # Returns '/api/messages/42'
```

**ASCII sequence for `url_for('api.about')`:**

```
Template/Code               Flask Router             Result
     |                           |
     |  url_for('api.about')     |
     |-------------------------->|
     |                           |
     |            Lookup endpoint 'api.about'
     |            Found: @api_bp.route('/about')
     |            Blueprint prefix: '/api'
     |            Full URL: '/api/about'
     |                           |
     |<--------------------------|
     |       '/api/about'        |
```

---

## 8. Blueprints

### 8.1 The Monolithic File Problem

Imagine you are building a web app that has user accounts, a blog, an API, and an admin panel. If all of this lives in `app.py`:

```python
# app.py — this file would be thousands of lines long
from flask import Flask
app = Flask(__name__)

@app.route('/login')
def login(): ...

@app.route('/logout')
def logout(): ...

@app.route('/blog/posts')
def list_posts(): ...

@app.route('/blog/posts/<int:post_id>')
def get_post(post_id): ...

@app.route('/api/users')
def api_users(): ...

# ... 50 more routes ...
```

**Problems:**
- The file becomes thousands of lines long and impossible to navigate.
- If two developers are working on different features, they will constantly have merge conflicts in the same file.
- You cannot reuse a feature (like authentication) in another project without copying dozens of entangled functions.

**Blueprints are the solution.** They let you organize each feature area into its own directory with its own files.

### 8.2 Namespace Collisions

Without blueprints, if two different parts of your application define a function with the same name, you get a conflict:

```python
# Without blueprints — these two functions clash!
def index():  # For the web homepage
    return render_template('home.html')

def index():  # For the API — OVERRIDES the first one!
    return {"status": "ok"}
```

**With blueprints**, each blueprint has its own namespace. Both can have a function named `index`, and Flask refers to them as `web.index` and `api.index` — no conflict.

### 8.3 Creating and Registering a Blueprint

Creating a blueprint is very similar to creating a Flask app, but you use `Blueprint` instead of `Flask`:

```python
# api.py (or app/api/__init__.py in the modular layout)
from flask import Blueprint

api_bp = Blueprint(
    "api",           # Name of the blueprint (used for namespacing endpoint names)
    __name__,        # Module name (same as Flask(__name__) — helps Flask find resources)
    url_prefix="/api"  # All routes in this blueprint will be prefixed with /api
)

@api_bp.route("/status")
def status():
    return {"status": "ok"}

@api_bp.route("/about")
def about():
    return {"description": "RESTful API built with Flask Blueprints"}
```

Then register it with the main Flask app:

```python
# app/__init__.py or app.py
from flask import Flask
from .api import api_bp  # or however you import it

def create_app():
    app = Flask(__name__)
    app.register_blueprint(api_bp)  # Mount all the blueprint's routes onto the app
    return app
```

After registration, the routes `GET /api/status` and `GET /api/about` are active.

**Key facts about Blueprint creation:**

- **First argument (`"api"`):** The blueprint's name. This becomes the prefix for endpoint names: `api.status`, `api.about`. Choose a short, descriptive, unique name.
- **Second argument (`__name__`):** Same purpose as in `Flask(__name__)` — Flask uses it to find templates and static files that belong to this blueprint.
- **`url_prefix`:** All routes defined on this blueprint will automatically have `/api` prepended. A route registered as `@api_bp.route("/status")` becomes accessible at `/api/status`.

### 8.4 What `url_prefix` Does Concretely

`url_prefix="/api"` means every route you define in this blueprint gets `/api` prepended automatically.

**Without prefix:**
```python
api_bp = Blueprint("api", __name__)
@api_bp.route("/status")   # accessible at /status
@api_bp.route("/users")    # accessible at /users
```

**With prefix:**
```python
api_bp = Blueprint("api", __name__, url_prefix="/api")
@api_bp.route("/status")   # accessible at /api/status
@api_bp.route("/users")    # accessible at /api/users
```

This is useful when you have multiple blueprints with different prefixes:

```python
# All these blueprints can have a route named /users
# without any conflict, because the prefixes differ:
api_bp   = Blueprint("api",   __name__, url_prefix="/api")    # → /api/users
admin_bp = Blueprint("admin", __name__, url_prefix="/admin")  # → /admin/users
web_bp   = Blueprint("web",   __name__, url_prefix="/")       # → /users
```

You can also override the prefix when registering:

```python
app.register_blueprint(api_bp, url_prefix="/v2/api")
# Now all api_bp routes are under /v2/api/...
```

### 8.5 Blueprint as a Package (The Real-World Pattern)

In production Flask projects, each blueprint is a **Python package** — a directory with an `__init__.py` file. The blueprint instance is defined in `__init__.py` and the route functions live in `routes.py`.

```text
app/
├── __init__.py          (application factory)
└── api/
    ├── __init__.py      (defines api_bp)
    └── routes.py        (defines route functions)
```

This pattern separates concerns cleanly:
- `__init__.py` is the blueprint's "public interface" — it creates the blueprint and exposes it.
- `routes.py` contains the implementation — all the view functions.

### 8.6 Relative Imports — Connecting Files Inside a Package

When your project is organized into packages, the files inside those packages need to import from each other. Python provides **relative imports** for this — imports that use a dot (`.`) to mean "look inside the current package" rather than searching the entire Python path.

#### Why not just use absolute imports?

You might want to write:
```python
# WRONG: This fails inside a package
from api import api_bp
from api.routes import status
```

These fail because Python interprets `api` as a top-level module on the system path — it does not know you mean the `api/` directory next to the current file. You would need the full path from the project root: `from app.api import api_bp`, which is fragile (it depends on where the project is installed).

**Relative imports** use a dot to say "start from the package this file is in":

| Syntax | Meaning | Example |
|---|---|---|
| `from . import name` | Import `name` from the **current package** | `from . import routes` |
| `from .module import name` | Import `name` from a module inside the current package | `from .api import api_bp` |

The single dot (`.`) means "the package this file lives in." Think of it like a relative file path: just as `./routes.py` means "the `routes.py` next to me," `from . import routes` means "import the `routes` module that sits in the same package directory."

#### How the three files connect via relative imports

```text
app/
├── __init__.py       uses:  from .api import api_bp
└── api/
    ├── __init__.py   uses:  from . import routes
    └── routes.py     uses:  from . import api_bp
```

**File 1 — `app/__init__.py` (application factory):**
```python
from .api import api_bp
# Meaning: "Inside the app/ package, go into the api/ sub-package,
#           and import the api_bp object from its __init__.py."
```

**File 2 — `app/api/__init__.py` (blueprint definition):**
```python
from . import routes
# Meaning: "Inside the api/ package, import the routes module
#           (the routes.py file that sits next to me)."
```

**File 3 — `app/api/routes.py` (view functions):**
```python
from . import api_bp
# Meaning: "Inside the api/ package, import api_bp from __init__.py
#           (the file that defines this package's public interface)."
```

> **Rule of thumb:** Inside a package, always use relative imports (starting with a dot) to import from files in the same package. Use absolute imports for external libraries (`from flask import Flask`).

### 8.7 Circular Imports and Import Order in `__init__.py`

This is one of the most confusing parts of Flask for beginners. Read it carefully.

#### What is a circular import?

A **circular import** happens when File A imports from File B, and File B also imports from File A. Python processes imports sequentially — when it tries to import File B while importing File A, File A is not finished yet. Python may see a partially-defined module, causing `ImportError` or `AttributeError`.

#### Why this happens in the blueprint package pattern

In `app/api/__init__.py`, you need to:
1. Create `api_bp = Blueprint(...)` — this needs to happen first.
2. Import `routes.py` — this triggers the route decorators to register themselves.

In `app/api/routes.py`, you need:
1. Import `api_bp` from the current package (`from . import api_bp`).

**The circular dependency:**
- `__init__.py` creates `api_bp` then imports `routes`.
- `routes.py` imports `api_bp` from `__init__.py`.

But when Python is executing `__init__.py` and hits `from . import routes`, it starts executing `routes.py`. `routes.py` then does `from . import api_bp`, which tries to read from `__init__.py` — but `__init__.py` is not finished yet!

#### The solution: order matters

The fix is simple but **critical**: `from . import routes` must come **after** `api_bp = Blueprint(...)` in `__init__.py`. By the time Python starts executing `routes.py`, `api_bp` already exists in the partially-executed `__init__.py`, so `from . import api_bp` in `routes.py` succeeds.

```python
# app/api/__init__.py
from flask import Blueprint

# STEP 1: Define api_bp FIRST
api_bp = Blueprint("api", __name__, url_prefix="/api")

# STEP 2: ONLY NOW import routes (which needs api_bp to already exist)
from . import routes  # noqa: E402, F401
# ^-- noqa comments suppress linter warnings about "import not at top of file"
#     and "imported but unused" — both are intentional here
```

**Why does this work?**
- When `from . import routes` executes, Python starts reading `routes.py`.
- `routes.py` does `from . import api_bp`.
- Python looks at `__init__.py` — even though it is not fully done, `api_bp` has already been assigned (it was in Step 1).
- `api_bp` is available. No error.
- `routes.py` finishes loading, with all `@api_bp.route()` decorators registered.
- `__init__.py` continues (nothing left to do).

> **Warning:** If you move `api_bp = Blueprint(...)` below `from . import routes`, `routes.py` will try to import `api_bp` before it exists, causing `ImportError: cannot import name 'api_bp'`.

### 8.8 Blueprint Registration and Request Handling (ASCII Diagram)

#### Phase 1: Application Startup

```
app.register_blueprint(api_bp)
         |
         v
  Blueprint reads all registered routes:
  +----------------------------------+
  | @api_bp.route('/')     -> index  |
  | @api_bp.route('/about')-> about  |
  | @api_bp.route('/msgs') -> msgs   |
  +----------------------------------+
         |
         v
  Apply url_prefix ('/api') to each:
  +---------------------------------------------+
  | /api/       → endpoint: 'api.index'  → index |
  | /api/about  → endpoint: 'api.about'  → about |
  | /api/msgs   → endpoint: 'api.msgs'   → msgs  |
  +---------------------------------------------+
         |
         v
  Routes added to Flask app's routing table.
  Blueprint registration complete.
```

#### Phase 2: Request Handling

```
Client: GET /api/msgs
         |
         v
Flask routing table lookup: /api/msgs → api.msgs → messages()
         |
         v
messages() executes
         |
         v
Returns dict: {"messages": [...], "count": 1}
         |
         v
Flask serializes to JSON, sets Content-Type: application/json
         |
         v
HTTP 200 OK response sent to client
```

### 8.9 Application Lifecycle with Blueprints (ASCII Diagram)

```
Python runs: uv run flask --app app run --debug
         |
         v
Flask calls create_app()
         |
         +---> app = Flask(__name__)
         |
         +---> app.config["KEY"] = "value"   (load configuration)
         |
         +---> from .api import api_bp        (import blueprint)
         |       |
         |       +--> api/__init__.py runs:
         |              api_bp = Blueprint("api", __name__, url_prefix="/api")
         |              from . import routes  (loads route decorators)
         |
         +---> app.register_blueprint(api_bp) (mount routes onto app)
         |
         +---> return app
         |
         v
Flask dev server starts, listening on http://127.0.0.1:5000

+-------------------------------+
|   Request Handling Loop       |
|                               |
|  HTTP Request arrives         |
|       |                       |
|       v                       |
|  Match route in routing table |
|       |                       |
|       v                       |
|  Execute view function        |
|       |                       |
|       v                       |
|  Send HTTP response           |
|       |                       |
|  (repeat for each request)    |
+-------------------------------+
         |
         v
CTRL+C pressed → server stops
```

---

## 9. Models (Brief Overview)

**Models** represent the data layer of your application. A model is a Python class that describes the structure of a piece of data — its fields and their types.

```python
# app/models.py
class User:
    def __init__(self, id, name, email):
        self.id = id
        self.name = name
        self.email = email
```

In a real application, models are not plain Python classes — they are connected to a database through an **ORM (Object-Relational Mapper)**. An ORM lets you:
- Define a Python class that maps to a database table.
- Create, read, update, and delete records using Python objects instead of raw SQL.
- Flask does not include an ORM, but integrates cleanly with libraries like Peewee and SQLAlchemy.

**In the project layout**, `models.py` is kept separate from routes and views. This is intentional — the data definition should not be mixed with the HTTP handling logic.

> **Full coverage:** Model definition, database setup, and CRUD operations using an ORM are covered in the companion guide *Flask with Database Persistence: Peewee ORM*.

---

## 10. Templates

Templates are for when your Flask application serves **HTML pages** (not just JSON). Instead of building an HTML string inside a view function (tedious and error-prone), you write an HTML file with placeholders, and Flask fills in the values at request time.

> **For this course:** If you are building a pure JSON API, templates are optional. If you are building web pages that users visit in a browser, templates are essential.

### 10.1 What is `render_template`?

`render_template` is a Flask function that:
1. Loads an HTML file from your `templates/` folder.
2. Passes variables from your view to the template.
3. Uses the Jinja2 engine to fill in all the placeholders and execute template logic.
4. Returns the final HTML string as the HTTP response body.

**Basic usage:**

```python
# app.py (or routes.py)
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def homepage():
    # render_template finds templates/index.html and fills in the variables
    return render_template('index.html', username="Alice", item_count=5)
```

```html
<!-- templates/index.html -->
<!DOCTYPE html>
<html>
<body>
    <h1>Hello, {{ username }}!</h1>
    <p>You have {{ item_count }} items.</p>
</body>
</html>
```

**What the client receives:**
```html
<!DOCTYPE html>
<html>
<body>
    <h1>Hello, Alice!</h1>
    <p>You have 5 items.</p>
</body>
</html>
```

**Where Flask looks for templates:**
- If your app has no blueprints, Flask looks in `templates/` relative to `app.py`.
- If your blueprint has `template_folder='templates'` set, Flask also looks in that blueprint's own `templates/` directory.
- The file name in `render_template('index.html', ...)` must match exactly (case-sensitive on Linux).

### 10.2 Jinja2 Syntax

Flask uses [Jinja2](https://jinja.palletsprojects.com/) as its templating engine. Jinja2 adds two categories of special syntax to regular HTML:

#### Variables: `{{ }}`

Double curly braces output the value of a variable:

```html
<p>Welcome, {{ username }}!</p>
<p>Total: {{ price * quantity }}</p>
<p>Name uppercase: {{ name | upper }}</p>
```

- `{{ username }}` — renders the value of `username` from the view context.
- `{{ price * quantity }}` — Jinja2 can evaluate simple Python expressions.
- `{{ name | upper }}` — the `|` applies a **filter** (`upper` converts to uppercase). Other filters include `lower`, `length`, `round`, `default`.

#### Control Structures: `{% %}`

Percent-style tags contain logic that is executed but not printed:

**Loops:**
```html
<ul>
  {% for item in items %}
    <li>{{ item.name }} — ${{ item.price }}</li>
  {% endfor %}
</ul>
```

**Conditionals:**
```html
{% if user.is_admin %}
  <a href="/admin">Admin Panel</a>
{% elif user.is_logged_in %}
  <a href="/dashboard">Dashboard</a>
{% else %}
  <a href="/login">Log In</a>
{% endif %}
```

**Blocks (for template inheritance — covered next):**
```html
{% block content %}
  <!-- child templates override this area -->
{% endblock %}
```

> **Key difference:** `{{ }}` outputs a value. `{% %}` executes logic (no output by itself).

### 10.3 Template Inheritance

Copying the same `<head>`, `<nav>`, and `<footer>` into every HTML file is repetitive and makes updates difficult. Jinja2's **template inheritance** solves this with a base template that defines the shared structure, and child templates that fill in only the parts that change.

#### Step 1: Create a base template

```html
<!-- templates/base.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <!-- This block lets child templates change the title -->
    <title>{% block title %}My App{% endblock %} - Message Board</title>
    <link rel="stylesheet" href="/static/style.css">
</head>
<body>
    <nav>
        <a href="{{ url_for('web.home') }}">Home</a>
        <a href="{{ url_for('web.messages') }}">Messages</a>
    </nav>

    <main>
        <!-- This block is where child templates inject their content -->
        {% block content %}{% endblock %}
    </main>

    <footer>
        <p>&copy; 2025 Message Board</p>
    </footer>
</body>
</html>
```

A `{% block name %}...{% endblock %}` tag defines a **slot** that child templates can fill in or override.

#### Step 2: Create a child template

```html
<!-- templates/messages.html -->
{% extends 'base.html' %}

{% block title %}Messages{% endblock %}

{% block content %}
<h2>All Messages</h2>

{% if messages %}
  {% for message in messages %}
  <article>
      <h3>{{ message.title }}</h3>
      <p>By {{ message.author }} on {{ message.date }}</p>
      <p>{{ message.content }}</p>
  </article>
  {% endfor %}
{% else %}
  <p>No messages yet. Be the first to post!</p>
{% endif %}
{% endblock %}
```

- `{% extends 'base.html' %}` — tells Jinja2 "use `base.html` as the parent template."
- `{% block title %}Messages{% endblock %}` — overrides the `title` block from `base.html`. The page title becomes "Messages - Message Board".
- `{% block content %}...{% endblock %}` — fills in the main content area.

Everything outside `{% block %}` tags in the child template is ignored — only the blocks are used to replace slots in the base template.

#### Step 3: Render the child template from a view

```python
@web_bp.route('/messages')
def messages():
    all_messages = Message.select()  # Fetch from database
    return render_template('messages.html', messages=all_messages)
```

Flask loads `messages.html`, sees `{% extends 'base.html' %}`, loads `base.html`, merges the blocks, and returns the complete HTML.

**The student receives HTML like:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Messages - Message Board</title>
    ...
</head>
<body>
    <nav>...</nav>
    <main>
        <h2>All Messages</h2>
        <article>
            <h3>Hello World</h3>
            ...
        </article>
    </main>
    <footer>...</footer>
</body>
</html>
```

### 10.4 Dictionary Unpacking for Context

When passing many variables to `render_template`, the call can become long and hard to read:

```python
# Hard to read — especially with many variables
return render_template('profile.html',
                       user=user,
                       post_count=post_count,
                       follower_count=follower_count,
                       is_own_profile=True,
                       recent_posts=recent_posts)
```

**Better approach — build a context dictionary and unpack it:**

```python
context = {
    "user": user,
    "post_count": post_count,
    "follower_count": follower_count,
    "is_own_profile": True,
    "recent_posts": recent_posts
}
return render_template('profile.html', **context)
```

The `**context` syntax unpacks the dictionary into keyword arguments — it is exactly equivalent to listing each key-value pair as `key=value`. This is standard Python, not Flask-specific.

**Benefits:**
- The context dictionary can be built incrementally with `context['key'] = value`.
- It is easier to add or remove variables.
- The `render_template` call stays short regardless of how many variables are passed.

### 10.5 Template Rendering Process (ASCII Diagram)

```
view function calls render_template('messages.html', messages=data)
         |
         v
Jinja2 loads templates/messages.html
         |
         v
Sees: {% extends 'base.html' %}
         |
         v
Jinja2 loads templates/base.html
         |
         v
Jinja2 identifies blocks in base.html:
  {% block title %} ... {% endblock %}
  {% block content %} ... {% endblock %}
         |
         v
Jinja2 finds matching blocks in messages.html:
  {% block title %}Messages{% endblock %}
  {% block content %}... for loop ...{% endblock %}
         |
         v
Merges: base.html structure + child's block content
         |
         v
Processes variables and logic:
  {{ message.title }} → actual title string
  {% for message in messages %} → repeats HTML for each message
  {{ url_for('web.home') }} → generates '/home' URL
         |
         v
Final HTML string returned to view function
         |
         v
Flask sends HTML as HTTP response body
(Content-Type: text/html)
```

---

## 11. The Full Request/Response Cycle

Now that you have studied each component, here is how they all fit together when a client makes a single HTTP request to a running Flask application.

**Scenario:** A client sends `GET /api/messages` to your modular Flask app.

**Layered architecture (outer to inner):**
```
Client (browser, curl, Bruno)
    ↕  HTTP over TCP/IP
Werkzeug Dev Server  (Guide 111: WSGI)
    ↕  WSGI interface
Flask Application (app/__init__.py)
    ↕  route matching
Blueprint (app/api/__init__.py + routes.py)
    ↕  function call
View Function (get_messages in routes.py)
    ↕  Python dict
Flask Response Serializer
    ↕  JSON string
Back up through Werkzeug to Client
```

### 11.1 Full Cycle ASCII Diagram

```
CLIENT                    FLASK APP                   YOUR CODE
  |                           |                           |
  | GET /api/messages         |                           |
  |-------------------------->|                           |
  |                           |                           |
  |                    [Werkzeug receives HTTP bytes]     |
  |                    [Parses into Python request obj]   |
  |                           |                           |
  |                    [Flask URL router]                 |
  |                    [Scans routing table]              |
  |                    [Match: /api/messages]             |
  |                    [Blueprint: api]                   |
  |                    [Endpoint: api.get_messages]       |
  |                           |                           |
  |                           |  call get_messages()     |
  |                           |------------------------->|
  |                           |                           |
  |                           |              [Read request.args]
  |                           |              [e.g., ?page=2]
  |                           |              [Query database]
  |                           |              [Build dict]
  |                           |                           |
  |                           |  return {"messages": [...]}
  |                           |<-------------------------|
  |                           |                           |
  |                    [Flask converts dict to JSON]      |
  |                    [Sets Content-Type: application/json]
  |                    [Sets Status: 200 OK]              |
  |                           |                           |
  | HTTP 200 OK               |                           |
  | Content-Type: application/json                        |
  | Body: {"messages": [...]} |                           |
  |<--------------------------|                           |
  |                           |                           |
```

**Step-by-step walkthrough:**

1. **Client sends request:** `GET /api/messages HTTP/1.1` with appropriate headers.
2. **Werkzeug receives bytes:** The WSGI dev server reads raw TCP data and creates a WSGI environment dict (Guide 111).
3. **Flask receives WSGI call:** The Flask app object is called by Werkzeug per the WSGI interface.
4. **URL routing:** Flask compares the request path `/api/messages` against every registered route in the routing table. It finds a match registered by the `api` blueprint.
5. **Blueprint lookup:** Flask identifies which blueprint owns this endpoint (`api`) and retrieves the endpoint name (`api.get_messages`).
6. **View function called:** Flask calls `get_messages()`, passing any URL variables as arguments.
7. **View executes:** The function reads `request.args` or `request.get_json()`, queries the database, and builds a Python dictionary response.
8. **Serialization:** Flask detects that the return value is a Python dictionary and calls `json.dumps()` on it. It sets the `Content-Type` header to `application/json`.
9. **Response assembled:** Flask creates an HTTP response with status `200 OK`, the JSON body, and headers.
10. **Werkzeug sends bytes:** Werkzeug converts the WSGI response back to HTTP bytes and sends them over the network.
11. **Client receives JSON:** The client sees the HTTP response and parses the JSON body.

---

## 12. Additional Resources

All resources below are the ones referenced by your professor, presented here for convenient access:

- [Flask Official Documentation](https://flask.palletsprojects.com/) — the authoritative reference for every Flask feature.
- [Flask Mega-Tutorial by Miguel Grinberg](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) — the most comprehensive beginner-to-advanced Flask tutorial available. Highly recommended.
- [Flask Tutorial (official)](https://flask.palletsprojects.com/en/stable/tutorial/) — the official step-by-step guide from the Flask team.
- [Flask Patterns](https://flask.palletsprojects.com/en/stable/patterns/) — common design patterns for Flask applications.
- [Flask Blueprints — Real Python](https://realpython.com/flask-blueprint/) — a thorough article specifically on blueprints with worked examples.
- [Flask Application Context](https://flask.palletsprojects.com/en/stable/appcontext/) — explains `current_app` and when application context is available.
- [Jinja2 Template Designer Documentation](https://jinja.palletsprojects.com/en/stable/templates/) — complete reference for Jinja2 syntax.
- [Jinja Templates — Real Python](https://realpython.com/primer-on-jinja-templating/) — beginner-friendly primer on Jinja2.
- [Real Python Flask Tutorials](https://realpython.com/tutorials/flask/) — a collection of Flask tutorials from basic to advanced.
- [Corey Schafer — Flask Tutorials (YouTube)](https://youtu.be/529LYDgRTgQ?si=jt5FyCVKTY99QvuX) — video series walking through Flask from scratch.
- [Flask Tutorial in VS Code](https://code.visualstudio.com/docs/python/tutorial-flask) — if you use VS Code as your editor.
- [Werkzeug Documentation](https://werkzeug.palletsprojects.com/en/stable/) — reference for the WSGI library Flask is built on (relevant to Guide 111).
- [Python Web Applications: The Basics of WSGI — SitePoint](https://www.sitepoint.com/python-web-applications-the-basics-of-wsgi/) — explains the WSGI layer between web servers and Python apps.

---

*Guide 112 — Flask Introduction. Builds on Guide 110 (HTTP, REST, JSON) and Guide 111 (WSGI, Werkzeug).*

