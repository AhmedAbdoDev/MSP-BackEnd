# ⚡ Session 02 — Asynchronous JavaScript & Node.js Core Runtime

### 📊 [Session Slides](https://docs.google.com/presentation/d/1zoTfNITltNeD8gwYutPAtk-N18a2Fz9-/edit?usp=drive_link&ouid=110982600669219439801&rtpof=true&sd=true) · 📖 [Extra Resources](https://docs.google.com/document/d/119c5jUTjKje42vfeFgQp-YGYbcBd47HgDK873XHKl8I/edit?usp=drive_link) · 📚 [Level 1 Home](../README.md)

> *"Never make the main thread sit and wait. Hand off the slow work, keep serving requests, and react when the bell rings."*

Welcome to **Session 02** of the **MSP Backend Bootcamp**. In this session, we trace the engineering evolution of **Asynchronous JavaScript** — from `Callbacks` to `Promises` and `async/await` — and open the hood of **Node.js** to explore its runtime architecture, **V8**, **libuv**, and the **Event Loop**.

## 📖 Table of Contents

- [🎯 Session Overview & Recap](#-session-overview--recap)
  - [Learning Objectives](#learning-objectives)
  - [Last Session Member Presentations](#last-session-member-presentations)
- [Part 1 — Why Asynchronous? (Blocking vs. Non-Blocking)](#part-1--why-asynchronous-blocking-vs-non-blocking)
  - [1. JavaScript Does One Thing at a Time](#1-javascript-does-one-thing-at-a-time)
    - [Practical Analogy: One Waiter, Many Tables](#practical-analogy-one-waiter-many-tables)
  - [2. Blocking vs. Non-Blocking: Time Difference](#2-blocking-vs-non-blocking-time-difference)
- [Part 2 — Callbacks & Callback Hell](#part-2--callbacks--callback-hell)
  - [3. Callbacks: The First Answer](#3-callbacks-the-first-answer)
  - [4. Callback Hell (Pyramid of Doom) & Root Causes](#4-callback-hell-pyramid-of-doom--root-causes)
    - [The Four Root Causes Behind Callback Pain](#the-four-root-causes-behind-callback-pain)
- [Part 3 — Promises](#part-3--promises)
  - [5. Promises: A Receipt for a Future Value](#5-promises-a-receipt-for-a-future-value)
    - [Practical Analogy: The Restaurant Buzzer](#practical-analogy-the-restaurant-buzzer)
    - [How Promises Fixed Callback Pain](#how-promises-fixed-callback-pain)
  - [6. Creating, Consuming & Chaining Promises](#6-creating-consuming--chaining-promises)
  - [7. Combining Promises: `all`, `allSettled`, `race`, `any`](#7-combining-promises-all-allsettled-race-any)
  - [8. Where Promise Chains Still Hurt](#8-where-promise-chains-still-hurt)
- [Part 4 — `async / await` & Error Handling](#part-4--async--await--error-handling)
  - [9. `async / await`: Promises Written Like Sync Code](#9-async--await-promises-written-like-sync-code)
    - [Four Rules of `async / await`](#four-rules-of-async--await)
  - [10. Error Handling: `.catch()` vs. `try / catch`](#10-error-handling-catch-vs-try--catch)
  - [11. Sequential vs. Parallel `await`](#11-sequential-vs-parallel-await)
    - [🛠️ Hands-On 01: Promisify and Parallelize](#️-hands-on-01-promisify-and-parallelize)
  - [12. The Async Evolution in One Picture](#12-the-async-evolution-in-one-picture)
- [Part 5 — Inside Node.js Runtime & V8](#part-5--inside-nodejs-runtime--v8)
  - [13. What Is a Runtime Environment? (Browser vs. Node.js)](#13-what-is-a-runtime-environment-browser-vs-nodejs)
  - [14. V8: How JavaScript Gets Executed](#14-v8-how-javascript-gets-executed)
- [Part 6 — Node.js Architecture, `libuv` & Core Modules](#part-6--nodejs-architecture-libuv--core-modules)
  - [15. Node.js Architecture, `libuv` & The Thread Pool](#15-nodejs-architecture-libuv--the-thread-pool)
    - [Tracing `fs.readFile()` Under the Hood](#tracing-fsreadfile-under-the-hood)
  - [16. Node.js Core Modules: The Built-in Toolbox](#16-nodejs-core-modules-the-built-in-toolbox)
- [Part 7 — The Event Loop, Microtasks & `EventEmitter`](#part-7--the-event-loop-microtasks--eventemitter)
  - [17. The Event Loop: Who Runs What, and When](#17-the-event-loop-who-runs-what-and-when)
  - [18. The Six Phases of the Node.js Event Loop](#18-the-six-phases-of-the-nodejs-event-loop)
  - [19. Execution Priority in Node.js](#19-execution-priority-in-nodejs)
  - [20. `EventEmitter`: Events Behind Everything](#20-eventemitter-events-behind-everything)
  - [21. What `await` Really Does Under the Hood](#21-what-await-really-does-under-the-hood)
    - [🧠 Interactive Checkpoint: Predict the Output](#-interactive-checkpoint-predict-the-output)
    - [🛠️ Hands-On 02: Block the Event Loop](#️-hands-on-02-block-the-event-loop)
- [Part 8 — Async Pitfalls & `worker_threads`](#part-8--async-pitfalls--worker_threads)
  - [22. Four Async Mistakes to Avoid](#22-four-async-mistakes-to-avoid)
  - [23. CPU-Heavy Work: `worker_threads`](#23-cpu-heavy-work-worker_threads)
    - [🛠️ Hands-On Lab: Read a File Three Ways (Slide 34)](#️-hands-on-lab-read-a-file-three-ways-slide-34)
    - [Solution for Task 03 (Sequential vs. Parallel File Reading)](#solution-for-task-03-sequential-vs-parallel-file-reading)
- [📌 Session Recap, Practical Task & Next Session](#-session-recap-practical-task--next-session)
  - [Key Takeaways](#key-takeaways)
  - [🛠️ Practical Task (Tech): CLI Order Processor](#️-practical-task-tech-cli-order-processor)
    - [Setup Commands](#setup-commands)
    - [Requirements](#requirements)
  - [🎤 Presentation Task (Recap): Async Evolution](#-presentation-task-recap-async-evolution)

## 🎯 Session Overview & Recap

### Learning Objectives

By the end of this session, you will be able to:

1. **Explain the Evolution of Async JS:** Understand why `async/await` exists and what specific problem each earlier step (`Callbacks` and `Promises`) solved.

2. **Predict Execution Order:** Read any asynchronous Node.js code and accurately predict the exact order in which statements execute.

3. **Understand Node.js Internals:** Distinguish between a JavaScript engine (**V8**) and a runtime environment (**Node.js**), and explain how **libuv**, the **Thread Pool**, and the **Event Loop** work together.

4. **Avoid Event Loop Blocking:** Identify synchronous bottlenecks and offload CPU-heavy computations using `worker_threads`.

### Last Session Member Presentations

At the start of Session 02, two members presented 5-minute recaps covering Session 01 topics:

1. **Task 01 — Node.js vs. Browser & Request Flow:** Comparing purposes, global objects (`window` vs. `globalThis`), APIs (`DOM` vs. `fs`/`http`), and tracing a request across `Client → DNS → TCP/TLS → HTTP → Load Balancer → Node.js Server → Controller + DB → Response`.

2. **Task 02 — The OSI 7-Layer Model:** Walking through Layers 1 to 7 (`Physical` up to `Application`) and mapping where `IP` (L3), `TCP` (L4), and `HTTP` (L7) operate.

# Part 1 — Why Asynchronous? (Blocking vs. Non-Blocking)

## 1. JavaScript Does One Thing at a Time

> **Core Rule:** *Never make the JS thread sit and wait. Start the slow work, keep going, and react when it finishes.*

JavaScript executes on a **single thread** with **one Call Stack**. Every line waits for the previous line to finish. If one operation is slow (reading from disk, querying a database, or making a network request), everything behind it freezes (**Synchronous = Blocking**).

### Practical Analogy: One Waiter, Many Tables

* **Blocking Waiter (Sync):** Takes an order from Table 1, then stands idle at the kitchen door until the dish is cooked. All other tables wait in frustration.

* **Non-Blocking Waiter (Async):** Hands the order ticket to the kitchen, immediately serves Tables 2 and 3, and delivers Table 1's food when the kitchen bell rings.

* **Mapping:** `Waiter` = JS Main Thread | `Kitchen` = OS / `libuv` Thread Pool | `Bell` = Callback / Promise.

```javascript
// blocking.js (SYNC - Bad for servers)
const fs = require('node:fs');

console.log('A: start');
const users = fs.readFileSync('users.json'); // FROZEN here until file finishes reading
console.log('B: got users');
console.log('C: all other requests waited!');
```

## 2. Blocking vs. Non-Blocking: Time Difference

Because disk and network I/O are orders of magnitude slower than the CPU, overlapping wait times dramatically increases server throughput on the exact same hardware:

```text
SYNCHRONOUS // ONE AFTER ANOTHER (Total: 9 time units)
REQ A: [JS][--- waiting (I/O) ---]
REQ B:                            [JS][--- waiting (I/O) ---]
REQ C:                                                       [JS][--- waiting (I/O) ---]

ASYNCHRONOUS // WAITING OVERLAPS (Total: 5 time units)
REQ A: [JS][--- waiting (I/O) ---]
REQ B:     [JS][--- waiting (I/O) ---]
REQ C:         [JS][--- waiting (I/O) ---]
```

*(During I/O waiting periods, the JS thread is completely free to accept and process other incoming requests.)*

# Part 2 — Callbacks & Callback Hell

## 3. Callbacks: The First Answer

A **callback** is a function passed as an argument to an asynchronous operation, telling Node.js: *"Start this work in the background, return immediately, and invoke this function when finished."*

* **Error-First Convention:** In Node.js callbacks, the first parameter is always `err` (or `null` if successful), and the data comes second: `(err, result)`.

```javascript
// callback.js
const fs = require('node:fs');

console.log('1. start');

fs.readFile('user.json', 'utf8', (err, data) => {
  if (err) return console.error(err); // Error-first check
  console.log('3. file ready:', data);
});

console.log('2. keep working...');

// Output Order:
// 1. start -> 2. keep working... -> 3. file ready
```

## 4. Callback Hell (Pyramid of Doom) & Root Causes

While callbacks solved blocking, chaining dependent asynchronous operations caused code to grow sideways into a **Pyramid of Doom**:

```javascript
// pyramid.js (Callback Hell)
getUser(id, (err, user) => {
  if (err) return handle(err);
  getOrders(user.id, (err, orders) => {
    if (err) return handle(err);
    getItems(orders[0].id, (err, items) => {
      if (err) return handle(err);
      getPrice(items[0].id, (err, price) => {
        if (err) return handle(err);
        console.log(price); // Finally!
      });
    });
  });
});
```

### The Four Root Causes Behind Callback Pain

| # | Pain Point | Why It Hurts |
| :--- | :--- | :--- |
| **01** | **Nesting** | Execution order is tied to indentation; complexity grows horizontally with every step. |
| **02** | **Error Handling** | Standard `try / catch` cannot catch errors thrown inside a callback running later; every level needs manual `if (err)` checks. |
| **03** | **Trust (Inversion of Control)** | You hand your callback to external code without guarantees it won't be called twice, never, or too early. |
| **04** | **Composition** | Waiting for multiple parallel tasks requires manual counters and state flags. |

# Part 3 — Promises

## 5. Promises: A Receipt for a Future Value

A **Promise** is a standard JavaScript object representing the eventual completion (or failure) of an asynchronous operation.

### Practical Analogy: The Restaurant Buzzer

You pay at the counter and receive a plastic buzzer (`Pending`). Later, it either buzzes green with your meal (`Fulfilled` via `resolve(value)`), or buzzes red because an ingredient ran out (`Rejected` via `reject(reason)`). Once settled, a Promise's state **never changes**.

### How Promises Fixed Callback Pain

1. **Flat Chaining:** `.then()` returns a new Promise, flattening nested pyramids into a vertical sequence.

2. **Centralized Errors:** A rejection travels down the chain until a single `.catch()` handles it.

3. **Predictable Trust:** Settles once only, and handlers always run asynchronously.

4. **Built-in Composition:** `Promise.all`, `allSettled`, `race`, and `any` combine multiple operations cleanly.

## 6. Creating, Consuming & Chaining Promises

```javascript
// promise_basic.js — Creating and Consuming
const wait = (ms) => new Promise((resolve, reject) => {
  if (ms < 0) return reject(new Error('negative'));
  setTimeout(() => resolve(`done after ${ms}ms`), ms);
});

wait(500)
  .then((msg) => console.log(msg))
  .catch((err) => console.error(err.message))
  .finally(() => console.log('cleanup'));

// chain.js — Flattening Callback Hell
getUser(id)
  .then((user)   => getOrders(user.id))
  .then((orders) => getItems(orders[0].id))
  .then((items)  => getPrice(items[0].id))
  .then((price)  => console.log(price))
  .catch(handle); // ONE place for all errors
```

> ⚠️ **Golden Rule:** Always `return` the inner Promise inside `.then()`. If you forget `return`, the chain will not wait and the next step receives `undefined`.

## 7. Combining Promises: `all`, `allSettled`, `race`, `any`

| Method | Behavior | Best Use Case |
| :--- | :--- | :--- |
| **`Promise.all([...])`** | Fulfils with an array of all results. Rejects immediately if **any one** rejects (*fail-fast*). | When you need every result to proceed. |
| **`Promise.allSettled([...])`** | Always fulfils after all settle, returning `{ status, value \| reason }` for each. | When you want a full report even if some fail. |
| **`Promise.race([...])`** | Settles as soon as the **first** promise settles (fulfilled or rejected). | Enforcing request timeouts. |
| **`Promise.any([...])`** | Fulfils with the **first successful** promise; rejects only if **all** reject. | Querying redundant mirrors/backups. |

```javascript
// combine.js
const [user, posts] = await Promise.all([getUser(1), getPosts(1)]);

const results = await Promise.allSettled([a(), b(), c()]);
results.forEach((r) => console.log(r.status)); // 'fulfilled' | 'rejected'

const data = await Promise.race([fetchData(), timeout(3000)]);
```

## 8. Where Promise Chains Still Hurt

Despite their advantages, `.then()` chains still become awkward when:

* **Shared Variables:** Step 3 needs a variable from Step 1, forcing nested `.then()` blocks back into the code.

* **Control Flow:** Writing `if / else` branches and loops inside `.then()` chains feels unnatural.

* **Debugging:** Stack traces across long chains are harder to read than top-to-bottom synchronous code.

# Part 4 — `async / await` & Error Handling

## 9. `async / await`: Promises Written Like Sync Code

Introduced in ES2017, `async / await` is **syntactic sugar built directly on top of Promises** — allowing asynchronous code to read top-to-bottom while remaining 100% non-blocking.

```javascript
// async_await.js
async function showPrice(id) {
  try {
    const user   = await getUser(id);
    const orders = await getOrders(user.id);
    const items  = await getItems(orders[0].id);
    const price  = await getPrice(items[0].id);
    console.log(price);
  } catch (err) {
    handle(err); // ONE try/catch for everything
  } finally {
    console.log('cleanup');
  }
}

showPrice(7); // Always returns a Promise
```

### Four Rules of `async / await`

1. **`async` returns a Promise:** Any value returned from an `async` function is automatically wrapped in a resolved Promise.

2. **`await` pauses the function only:** It suspends execution inside that specific `async` function while the JS main thread continues serving other requests.

3. **`try / catch` works again:** Rejected Promises throw like standard synchronous exceptions.

4. **Scope rule:** Use `await` inside `async` functions (or at the top level of ES modules).

## 10. Error Handling: `.catch()` vs. `try / catch`

```javascript
// promise_style.js (.catch)
fetchUser(id)
  .then(render)
  .catch((err) => {
    console.error(err.message);
    throw err; // Rethrow if caller still needs to know it failed
  })
  .finally(() => hideSpinner());

// async_style.js (try / catch)
async function load(id) {
  try {
    const user = await fetchUser(id);
    return render(user);
  } catch (err) {
    console.error(err.message);
    throw err;
  } finally {
    hideSpinner();
  }
}
```

* **Use Real `Error` Objects:** Throw/reject with `new Error(...)` or custom subclasses (`class NotFoundError extends Error`) to preserve stack traces.

* **Global Safety Net:** Register `process.on('unhandledRejection', log)` to log unexpected uncaught rejections before exiting cleanly.

## 11. Sequential vs. Parallel `await`

Only `await` operations sequentially when Step B genuinely depends on the output of Step A:

```javascript
// slow.js — BAD (2 seconds total for independent tasks)
const a = await fetchA(); // 1 s
const b = await fetchB(); // 1 s

// fast.js — GOOD (1 second total using Promise.all)
const [a, b] = await Promise.all([
  fetchA(),
  fetchB(),
]);
```

> ⚠️ **Related Trap:** `array.forEach(async ...)` does **not** wait for promises to finish. Use `for...of` for sequential loops, or `await Promise.all(array.map(...))` for parallel execution.

### 🛠️ Hands-On 01: Promisify and Parallelize

1. **Task 01 (Measure Sequential):** Create `parallel.js` using `promisify(setTimeout)` to simulate `fakeFetch('A', 800)` and `fakeFetch('B', 800)` sequentially. Measure with `console.time` (~1.6s).

2. **Task 02 (Parallelize):** Refactor to use `Promise.all` (~0.8s).

3. **Task 03 (Compare Failure Modes):** Force `B` to throw an error and compare `Promise.all` vs. `Promise.allSettled`.

4. **Bonus (Timeout):** Race `fakeFetch('A', 2000)` against a `1000ms` timeout using `Promise.race`.

```javascript
const { promisify } = require('node:util');
const sleep = promisify(setTimeout);

async function fakeFetch(name, ms, fail = false) {
  await sleep(ms);
  if (fail) throw new Error(`${name} failed`);
  return `${name} done in ${ms}ms`;
}

async function main() {
  console.time('parallel');
  const results = await Promise.allSettled([
    fakeFetch('A', 800),
    fakeFetch('B', 800, true),
    fakeFetch('C', 800),
  ]);
  results.forEach((r) => console.log(r.value ?? r.reason.message));
  console.timeEnd('parallel'); // ~800ms
}

main();

// Bonus: Timeout with Promise.race
const timeout = (ms) => new Promise((_, reject) =>
  setTimeout(() => reject(new Error('timeout')), ms)
);
// await Promise.race([fakeFetch('A', 2000), timeout(1000)]);
```

## 12. The Async Evolution in One Picture

| Era | Milestone | Problem Solved |
| :--- | :--- | :--- |
| **2009** | **Callbacks** | Node.js ships with error-first callbacks $\rightarrow$ *Solved blocking, caused nesting & scattered errors.* |
| **2015** | **Promises** | ES2015 adds native `Promise` $\rightarrow$ *Solved nesting with `.then()` chains & centralized `.catch()`.* |
| **2017** | **`async / await`** | ES2017 syntax (Node 7.6+) $\rightarrow$ *Reads like sync code while keeping Promises underneath.* |
| **Today** | **Promise-First APIs** | `node:fs/promises`, `util.promisify`, and built-in global `fetch` $\rightarrow$ *`await` everywhere.* |

# Part 5 — Inside Node.js Runtime & V8

## 13. What Is a Runtime Environment? (Browser vs. Node.js)

> **Key Insight:** `Promise` is part of the JavaScript language, whereas `setTimeout`, `fs`, `http`, and `console` are provided by the **Runtime Environment** surrounding the engine.

```text
JS Engine (V8)  +  Runtime APIs (fs, http, timers)  +  Event Loop (libuv)  =  Runtime Environment
```

| Feature | Browser Runtime | Node.js Runtime |
| :--- | :--- | :--- |
| **JS Engine** | V8 (Chrome) | V8 |
| **Unique APIs** | `DOM`, `localStorage`, `window` | `fs`, `http`, `os`, `process`, `crypto` |
| **Event Loop** | Browser Event Loop | `libuv` |
| **Global Object** | `window` / `globalThis` | `global` / `globalThis` |

## 14. V8: How JavaScript Gets Executed

1. **Source:** Your `.js` text file.

2. **Parser:** Builds an **AST** (Abstract Syntax Tree).

3. **Ignition (Interpreter):** Quickly compiles AST into **Bytecode** and starts running immediately.

4. **TurboFan (Optimizing Compiler):** Identifies frequently executed **"Hot Code"** and compiles it into highly optimized **Machine Code**.

* **Memory Heap:** Dynamic memory pool where objects, arrays, and closures live (cleaned automatically by the **Garbage Collector**).

* **Call Stack:** LIFO (Last-In, First-Out) execution stack. Because V8 has **no timers, files, or sockets**, it hands off waiting I/O tasks to Node.js.

# Part 6 — Node.js Architecture, `libuv` & Core Modules

## 15. Node.js Architecture, `libuv` & The Thread Pool

```text
┌───────────────────────────────────────────────────────────┐
│        Your Application (app.js · Express · npm)          │
├───────────────────────────────────────────────────────────┤
│   Node.js Core Modules (fs · http · path · events · os)   │
├───────────────────────────────────────────────────────────┤
│              C++ Bindings (JS <-> Native Glue)            │
├─────────────────────────────┬─────────────────────────────┤
│      V8 (Executes JS)       │  libuv (Event Loop + Pool)  │
├─────────────────────────────┴─────────────────────────────┤
│    Native Libraries (OpenSSL · zlib · llhttp · c-ares)    │
├───────────────────────────────────────────────────────────┤
│        Operating System (File System · Sockets · OS)      │
└───────────────────────────────────────────────────────────┘
```

### Tracing `fs.readFile()` Under the Hood

1. Your JS calls `fs.readFile` in the Node core module.

2. C++ Bindings pass the request down to **libuv**.

3. `libuv` delegates the disk operation to a background **Thread Pool** thread.

4. Your JS Main Thread immediately moves on — it is never blocked.

5. Once finished, `libuv` queues your callback for the **Event Loop**.

> 💡 **Is Node.js Single-Threaded?** Your JavaScript executes on **1 Main Thread**, but `libuv` maintains a **Thread Pool** (default **4 threads**, configurable via `UV_THREADPOOL_SIZE`) for blocking OS operations (`fs`, `dns.lookup`, `crypto`, `zlib`). **CPU-heavy JS code is NOT offloaded** to this pool.

## 16. Node.js Core Modules: The Built-in Toolbox

Built-in modules require no `npm install`. Always import them using the explicit `node:` protocol prefix:

| Module | Purpose | Example |
| :--- | :--- | :--- |
| **`node:fs`** | Read and write files (`fs/promises` for async/await) | `await fsp.readFile('a.txt', 'utf8')` |
| **`node:path`** | Build cross-platform file paths safely | `path.join(__dirname, 'a.txt')` |
| **`node:http`** | Create raw HTTP servers and clients | `http.createServer(handler)` |
| **`node:events`** | Publish/subscribe event architecture (`EventEmitter`) | `emitter.on('data', fn)` |
| **`node:os`** | Inspect CPU cores, memory, and OS platform | `os.cpus().length` |
| **`node:stream`** | Process large files in chunks without filling RAM | `fs.createReadStream(file)` |
| **`node:crypto`** | Hashing, encryption, and secure UUID generation | `crypto.randomUUID()` |
| **`node:util`** | Utilities like converting callbacks to promises | `util.promisify(fn)` |

# Part 7 — The Event Loop, Microtasks & `EventEmitter`

## 17. The Event Loop: Who Runs What, and When

```javascript
// Conceptual Event Loop Cycle:
while (true) {
  runSyncCode();
  drainMicrotasks(); // 1. process.nextTick  2. Promise callbacks
  takeNextTask();    // Next ready callback from Event Loop phases
}
```

> **The Golden Rule:** The Event Loop **only** moves a queued callback onto the Call Stack when the **Call Stack is completely EMPTY**.

## 18. The Six Phases of the Node.js Event Loop

Each tick of `libuv`'s Event Loop iterates through six ordered phases:

1. **01 — Timers:** Executes expired `setTimeout()` and `setInterval()` callbacks.

2. **02 — Pending Callbacks:** Runs deferred OS-level system callbacks (e.g., TCP errors).

3. **03 — Idle / Prepare:** Internal housekeeping used by Node.js.

4. **04 — Poll:** Retrieves new I/O events (finished file reads, incoming network requests) and executes their callbacks.

5. **05 — Check:** Executes `setImmediate()` callbacks.

6. **06 — Close Callbacks:** Runs cleanup close handlers (e.g., `socket.on('close')`).

> **Crucial Behavior:** Between **every single callback**, Node.js drains the `process.nextTick` queue first, followed by the `Promise` microtask queue, before advancing to the next phase.
>
> *Interview Tip:* In the main module, `setTimeout(fn, 0)` vs. `setImmediate(fn)` order is non-deterministic. Inside an I/O callback (Poll phase), `setImmediate` **always** runs first because Check (Phase 05) immediately follows Poll (Phase 04).

## 19. Execution Priority in Node.js

From highest priority to lowest priority (CommonJS scripts):

1. **Synchronous Code** (runs immediately to completion)

2. **`process.nextTick()`** (Node-specific VIP queue)

3. **Promise Microtasks** (`.then`, `.catch`, `.finally`, and code after `await`)

4. **Event Loop Phases** (`Timers`, `I/O Poll`, `Check / setImmediate`)

```javascript
// order.js
console.log('1 sync');
process.nextTick(() => console.log('2 nextTick'));
Promise.resolve().then(() => console.log('3 promise'));
setTimeout(() => console.log('4 timeout'), 0);
console.log('1b sync');

// Exact Output:
// 1 sync -> 1b sync -> 2 nextTick -> 3 promise -> 4 timeout
```

## 20. `EventEmitter`: Events Behind Everything

HTTP servers, streams, and `process` all inherit from `EventEmitter`:

```javascript
// emitter.js
const { EventEmitter, once } = require('node:events');
const orders = new EventEmitter();

orders.on('placed', (id) => console.log('kitchen got', id));
orders.on('placed', (id) => console.log('billing got', id));

orders.emit('placed', 42);
console.log('emit is synchronous!');

// Bridge Events with async/await:
// const [id] = await once(orders, 'placed');
```

* **Watch Out:** `emit()` runs all registered listeners **synchronously** in registration order before returning.

* **Always Listen for `'error'`:** An unhandled `'error'` event crashes the Node.js process immediately.

## 21. What `await` Really Does Under the Hood

```javascript
// WHAT YOU WRITE:                 // WHAT THE RUNTIME DOES:
async function f() {               function f() {
  console.log('1');                  console.log('1');
  await task();                      return task().then(() => {
  console.log('3');                    console.log('3'); // Queued as Microtask
}                                    });
f();                               }
console.log('2');                  f();
                                   console.log('2');
// Output: 1 -> 2 -> 3
```

### 🧠 Interactive Checkpoint: Predict the Output

1. **Predict the output order (`A..F`):**

   ```javascript
   async function f() { console.log('B'); await null; console.log('D'); }
   console.log('A'); f();
   Promise.resolve().then(() => console.log('E'));
   setTimeout(() => console.log('F'), 0);
   console.log('C');
   ```

   * **Answer:** `A B C D E F` (`A, B, C` are sync; `await null` queues `D` as a microtask before `E`; `F` runs last in Timers).

2. **Is Node.js single-threaded?** JS runs on 1 thread, while `libuv` uses OS async I/O and a 4-thread pool in the background.

3. **What happens if a route runs `while (true) {}`?** The entire server freezes because the Event Loop never gets an empty Call Stack.

### 🛠️ Hands-On 02: Block the Event Loop

```javascript
// server.js
const http = require('node:http');

http.createServer((req, res) => {
  if (req.url === '/block') {
    const end = Date.now() + 5000;
    while (Date.now() < end) {} // Busy loop: blocks main thread for 5s
    return res.end('blocked 5s\n');
  }
  if (req.url === '/async') {
    return setTimeout(() => res.end('async 5s\n'), 5000);
  }
  res.end('hello\n');
}).listen(3000);
```

1. Request `curl localhost:3000/block` in Terminal 1, then immediately request `curl localhost:3000/` in Terminal 2 $\rightarrow$ `hello` freezes for 5 seconds.

2. Repeat with `/async` and `/` $\rightarrow$ `hello` responds **instantly** because `setTimeout` does not block the main thread.

# Part 8 — Async Pitfalls & `worker_threads`

## 22. Four Async Mistakes to Avoid

| Pitfall | Problem | Fix |
| :--- | :--- | :--- |
| **01. Blocking the Event Loop** | Using `readFileSync`, giant loops, or huge `JSON.parse` inside request handlers freezes all users. | Use async APIs; offload CPU work to `worker_threads`. |
| **02. Forgotten `await`** | Variable holds a pending `Promise` instead of data, and rejections escape `try/catch`. | Always `await` or `return` promises. |
| **03. `forEach` with `async`** | `forEach` fires callbacks without waiting for them to settle. | Use `for...of` (sequential) or `await Promise.all(arr.map(...))` (parallel). |
| **04. Unhandled Rejections** | In Node 15+, an unhandled promise rejection terminates the process. | Wrap `await` in `try/catch` or attach `.catch()`. |

## 23. CPU-Heavy Work: `worker_threads`

For **CPU-bound tasks** (image processing, heavy math, large parsing), `worker_threads` spawns an isolated JavaScript thread with its own V8 instance and Event Loop, communicating with the main thread via messages:

```javascript
// main.js
const { Worker } = require('node:worker_threads');
const w = new Worker('./heavy.js', { workerData: 40 });
w.on('message', (result) => console.log('Result:', result));
w.on('error', (err) => console.error(err));

// heavy.js
const { parentPort, workerData } = require('node:worker_threads');
const fib = (n) => (n < 2 ? n : fib(n - 1) + fib(n - 2));
parentPort.postMessage(fib(workerData));
```

### 🛠️ Hands-On Lab: Read a File Three Ways (Slide 34)

Create `notes.txt` and run `node read_three_ways.js` to compare Callbacks, Promises, and `async/await` side-by-side:

```javascript
// read_three_ways.js
const fs  = require('node:fs');
const fsp = require('node:fs/promises');

// 1. Callback
fs.readFile('notes.txt', 'utf8', (err, text) => {
  if (err) return console.error(err.message);
  console.log('callback:', text.length);
});

// 2. Promise
fsp.readFile('notes.txt', 'utf8')
  .then((text) => console.log('promise:', text.length))
  .catch((err) => console.error(err.message));

// 3. async / await
async function main() {
  try {
    const text = await fsp.readFile('notes.txt', 'utf8');
    console.log('await:', text.length);
  } catch (err) {
    console.error(err.message);
  }
}
main();
```

* **Tasks:** (1) Run and observe output order. (2) Rename `notes.txt` to trigger errors and compare error reporting. (3) Read 3 files in parallel using `Promise.all` and compare timing against sequential `await` using `console.time`.

#### Solution for Task 03 (Sequential vs. Parallel File Reading)

```javascript
// compare_files.js
const fsp = require('node:fs/promises');

async function compareReadSpeed() {
  const files = ['file1.txt', 'file2.txt', 'file3.txt'];

  // 1. Sequential: waits for each file before starting the next
  console.time('Sequential Read');
  const d1 = await fsp.readFile(files[0], 'utf8');
  const d2 = await fsp.readFile(files[1], 'utf8');
  const d3 = await fsp.readFile(files[2], 'utf8');
  console.timeEnd('Sequential Read');

  // 2. Parallel: dispatches all 3 reads to libuv Thread Pool simultaneously
  console.time('Parallel Read');
  const [p1, p2, p3] = await Promise.all(
    files.map((file) => fsp.readFile(file, 'utf8'))
  );
  console.timeEnd('Parallel Read');

  console.log('Total characters:', p1.length + p2.length + p3.length);
}

compareReadSpeed().catch(console.error);
```

## 📌 Session Recap, Practical Task & Next Session

### Key Takeaways

* ✅ **Never Block:** JavaScript has one main thread; hand off slow I/O work and react when it completes.

* ✅ **Async Evolution:** `Callbacks` solved blocking $\rightarrow$ `Promises` flattened pyramids & unified errors $\rightarrow$ `async/await` made Promises read like synchronous code.

* ✅ **Node.js Core:** `Node.js` = `V8 Engine` + `Runtime APIs` + `libuv (Event Loop & Thread Pool)`.

* ✅ **Execution Order:** `Synchronous Code` $\rightarrow$ `process.nextTick` $\rightarrow$ `Promise Microtasks` $\rightarrow$ `Event Loop Phases (Timers → Poll → Check)`.

### 🛠️ Practical Task (Tech): CLI Order Processor

Build a standalone Node.js script (`orders.js`) that simulates a non-blocking restaurant order system (**no HTTP server required**).

#### Setup Commands

```bash
mkdir order-system && cd order-system
code orders.js
node orders.js
code orders_log.txt
```

#### Requirements

1. **Create 3 Async Step Functions** (using `setTimeout` + `new Promise` or `util.promisify`):

   * `validateOrder(orderId)`: Takes `500ms`. Resolves if `orderId` is valid, or **rejects with an `Error` object** if `orderId` is missing or negative (`<= 0`).

   * `prepareFood(orderId)`: Takes `1500ms` and resolves when the meal is ready.

   * `packOrder(orderId)`: Takes `700ms` and resolves when the order is packed.

2. **Sequential Processing (`processSingleOrder(orderId)`):**

   * Execute the 3 steps sequentially using `async/await` wrapped in `try/catch`.

   * Measure and log the total execution time using `console.time` and `console.timeEnd`.

3. **Parallel Batch Processing (`processBatch(orderIds)`):**

   * Accept an array of order IDs (e.g., `[101, -5, 103]`).

   * Process all orders **concurrently** using `Promise.allSettled` so that one invalid order (`-5`) does not stop the rest.

   * Print a summary of fulfilled/rejected orders and the total batch execution time.

4. **File Logging (`node:fs/promises`):**

   * Save the final batch report to `orders_log.txt` using `writeFile` or `appendFile`.

* **Deliverable:** Send your `orders.js` file privately before **Tuesday at 12:00 AM**.

### 🎤 Presentation Task (Recap): Async Evolution

At the start of **Session 03**, one volunteer will deliver a **5-minute live explanation** covering the evolution of asynchronous JavaScript:

* **Stage 1 — Callbacks:** What they are, the nesting problem (`Callback Hell`), and why error handling hurt.

* **Stage 2 — Promises:** How `.then()` chaining and single `.catch()` solved callback pain.

* **Stage 3 — `async / await`:** How it makes async code read like synchronous code without blocking the thread.