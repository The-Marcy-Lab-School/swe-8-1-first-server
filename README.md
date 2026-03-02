# Your First Server Application

- [Setup](#setup)
- [Short Response Questions](#short-response-questions)
- [From Scratch](#from-scratch)
  - [Starter Code](#starter-code)
  - [Grading](#grading)
  - [Step 1 — Create the Server](#step-1--create-the-server)
  - [Step 2 — Add Routes](#step-2--add-routes)
    - [`GET /`](#get-)
    - [`GET /api/joke`](#get-apijoke)
    - [`GET /api/rollDie`](#get-apirolldie)
    - [Fallback — All Other Routes](#fallback--all-other-routes)
  - [Step 3 — Log Every Request](#step-3--log-every-request)
  - [Step 4 — Test with `curl`](#step-4--test-with-curl)
    - [Test each route](#test-each-route)
    - [View response headers](#view-response-headers)

## Setup

For guidance on setting up and submitting this assignment, refer to the Marcy Lab School Docs How-To guide for [Working with Short Response and Coding Assignments](https://marcylabschool.gitbook.io/marcy-lab-school-docs/how-tos/working-with-assignments#how-to-work-on-assignments).

After cloning your repository, make sure to run:

```sh
git checkout -b draft
```

**No `npm install` is required.** The `node:http` module used in this assignment is built into Node — you don't need to install anything to use it.

> **Optional:** If you want your server to restart automatically when you save changes, you can install `nodemon`:
> ```sh
> npm install --save-dev nodemon
> npx nodemon server.js
> ```



## Short Response Questions

Short response questions can be found in the `short-response.md` file. Write your responses directly in that file. Do not forget to complete this part of the assignment. There are three questions covering the topics:

1. Server Basics
2. req and res
3. Routing

## From Scratch

### Starter Code

You will be given a blank repo for this one! You are building a server from scratch using Node's built-in `node:http` module — no npm packages required.

### Grading

Instead of automated tests, your grade on this assignment will be determined by the number of tasks you are able to complete. Tasks appear as a checkbox, like this:

- [ ] example of an incomplete task
- [x] example of a completed task

Feel free to mark these tasks as complete/incomplete as you go. Your instructor may modify your tasks as complete/incomplete when grading.

This assignment has 11 requirements:
- 4 server setup requirements
- 2 request logging requirements
- 5 routing requirements

**Server Setup Requirements**

- [ ] A `server.js` file exists at the root of the repository
- [ ] `server.js` uses `require('node:http')` — no npm packages
- [ ] `http.createServer()` is used to create the server
- [ ] The server listens on port `8080`

**Request Logging Requirements**

- [ ] Every incoming request logs the HTTP method, URL, and timestamp to the console
- [ ] After logging, the server continues to process and respond to the request

**Routing Requirements**

- [ ] `GET /` responds with status `200` and a plain-text welcome message
- [ ] `GET /api/joke` responds with status `200` and a JSON object containing `setup` and `punchline` fields
- [ ] `GET /api/rollDie` responds with status `200` and a JSON object containing a `rolls` array with a random die roll (e.g. `{ rolls: [5] }`)
- [ ] `GET /api/rollDie` uses a `?quantity=` query parameter to roll multiple dice (defaults to 1 if missing or invalid)
- [ ] All unmatched routes respond with status `404` and a JSON error object

---

### Step 1 — Create the Server

> ✅ You will know that you've completed this step when you can run `node server.js` and see `Server listening on http://localhost:8080` printed in your terminal.

Create a `server.js` file at the root of your repository.

Inside it, use Node's built-in `node:http` module to create a server and start listening on port `8080`. You do not need to install anything — `node:http` is part of Node itself.

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  // All request handling goes here
});

server.listen(8080, () => {
  console.log('Server listening on http://localhost:8080');
});
```

`http.createServer()` accepts a **request listener** — a callback that is invoked once for every incoming request, regardless of the URL or method. Every piece of logic you write for handling requests will live inside this callback.

Once your server is running, try visiting `http://localhost:8080` in your browser. The browser will hang waiting for a response — that's expected! You haven't told the server how to respond yet. That's next.

---

### Step 2 — Add Routes

> ✅ You will know that you've completed this step when you can visit each endpoint in your browser and receive the correct response.

With `node:http`, **routing** means inspecting `req.method` and `req.url` inside the request listener and using `if/else` statements to decide how to respond.

Two key methods on `res` send the response back:
- **`res.writeHead(statusCode, headers)`** — sets the HTTP status code and response headers
- **`res.end(body)`** — sends the response body (must be a string) and closes the connection

> **Important:** Always use `return` after `res.end()` so that no other code in the callback runs after the response has been sent.

Your server needs to handle three routes plus a fallback:

#### `GET /`
- Status: `200`
- Content-Type: `text/plain`
- Body: A welcome message of your choosing

#### `GET /api/joke`
- Status: `200`
- Content-Type: `application/json`
- Body: `{ "setup": "...", "punchline": "..." }` with a joke of your choice

#### `GET /api/rollDie`
- Status: `200`
- Content-Type: `application/json`
- Body: `{ "rolls": [...] }` — an array of random integers between 1 and 6

This endpoint should read a `?quantity=` query parameter to determine how many dice to roll. If the parameter is missing or not a valid positive number, default to rolling **one die**.

To parse the query string from `req.url`, use the built-in `URL` constructor:

```js
const { pathname, searchParams } = new URL(req.url, 'http://localhost:8080');
const quantity = parseInt(searchParams.get('quantity'));
```

Example responses:
- `/api/rollDie` → `{ "rolls": [4] }`
- `/api/rollDie?quantity=3` → `{ "rolls": [5, 2, 3] }`
- `/api/rollDie?quantity=foo` → `{ "rolls": [1] }`

#### Fallback — All Other Routes
- Status: `404`
- Content-Type: `application/json`
- Body: `{ "error": "Not found" }`

---

### Step 3 — Log Every Request

> ✅ You will know that you've completed this step when every request you send prints a line to the server terminal showing the method, URL, and timestamp.

At the top of the `http.createServer()` callback — before any routing logic — add a `console.log` that prints the HTTP method, URL, and current timestamp for every incoming request.

Example terminal output:

```
GET / 2024-01-15T10:30:00.000Z
GET /api/joke 2024-01-15T10:30:05.123Z
GET /api/rollDie?quantity=3 2024-01-15T10:30:10.456Z
GET /doesnotexist 2024-01-15T10:30:15.789Z
```

> **Tip:** Use `new Date().toISOString()` to get a formatted timestamp.

After adding this, restart your server and send a few requests. Keep an eye on your terminal and watch the requests appear in real time!

---

### Step 4 — Test with `curl`

> ✅ You will know that you've completed this step when you can run each `curl` command below and receive the expected response.

With your server running, open a **second terminal tab** and use `curl` to test each of your endpoints. Unlike a browser, `curl` lets you see exactly what your server sends back — including the status code and headers.

#### Test each route

```sh
# Welcome message
curl http://localhost:8080/

# Joke endpoint
curl http://localhost:8080/api/joke

# Roll one die
curl http://localhost:8080/api/rollDie

# Roll three dice
curl "http://localhost:8080/api/rollDie?quantity=3"

# Invalid quantity (should roll one die)
curl "http://localhost:8080/api/rollDie?quantity=abc"

# Unknown route (should get 404)
curl http://localhost:8080/doesnotexist
```

#### View response headers

Use the `-i` flag to see the full response — status line, headers, and body:

```sh
curl -i http://localhost:8080/api/joke
```

You should see something like:

```
HTTP/1.1 200 OK
Content-Type: application/json
Date: ...

{"setup":"...","punchline":"..."}
```

This is the raw HTTP response — the same bytes your browser receives before it does any rendering.
