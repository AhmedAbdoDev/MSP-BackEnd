# 🧪 Session 01 — Backend Foundations & Networking Basics

### 📊 [Session Slides](https://docs.google.com/presentation/d/10vy9LjxeU1v6Ys3kz3gSL8z-NmOSa11E/edit?usp=drive_link&ouid=110982600669219439801&rtpof=true&sd=true) · 🛠️ [Prerequisites & Setup](./Prerequisites/README.md) · 📚 [Level 1 Home](../README.md)

> *"Every request has a destination, and every response has a contract. Learn to engineer both."*

Welcome to **Session 01** of the **MSP Backend Bootcamp**. In this foundational session, we pull back the curtain on how web applications actually work behind the scenes—tracing how a user's action on a screen travels across networks, locates a server, speaks HTTP/HTTPS, and executes inside **Node.js** and **Express.js**.

---

## 📖 Table of Contents

- [🎯 Session Overview & Prerequisites](#-session-overview--prerequisites)
  - [Learning Objectives](#learning-objectives)
  - [Before the Session](#before-the-session)
- [Part 1 — Backend Foundations & Client-Server Architecture](#part-1--backend-foundations--client-server-architecture)
  - [1. What is Backend Development?](#1-what-is-backend-development)
    - [Practical Analogy: Dining Room vs. Kitchen](#practical-analogy-dining-room-vs-kitchen)
  - [2. Client-Server Model & Request Lifecycle](#2-client-server-model--request-lifecycle)
    - [End-to-End Trace: Checking University Admission Results](#end-to-end-trace-checking-university-admission-results)
- [Part 2 — Networking Fundamentals](#part-2--networking-fundamentals)
  - [3. What is a Network?](#3-what-is-a-network)
  - [4. IP Addresses (Machine Coordinates)](#4-ip-addresses-machine-coordinates)
  - [5. Ports & Sockets (Application Doors)](#5-ports--sockets-application-doors)
  - [6. Transport Protocols: TCP vs. UDP](#6-transport-protocols-tcp-vs-udp)
  - [7. Domain Names & DNS Resolution](#7-domain-names--dns-resolution)
    - [🛠️ Practical Task 01: Local Network Diagnostics](#-practical-task-01-local-network-diagnostics)
  - [8. Self-Study Reference: The OSI 7-Layer Model](#8-self-study-reference-the-osi-7-layer-model)
- [Part 3 — HTTP, HTTPS & Wire Anatomy](#part-3--http-https--wire-anatomy)
  - [9. HTTP vs. HTTPS & TLS Security](#9-http-vs-https--tls-security)
    - [How HTTPS & TLS Protect Web Traffic](#how-https--tls-protect-web-traffic)
  - [10. Anatomy of HTTP/1.1 Requests & Responses](#10-anatomy-of-http11-requests--responses)
  - [11. Common HTTP Methods & Status Codes](#11-common-http-methods--status-codes)
    - [HTTP Methods (Client Intent)](#http-methods-client-intent)
    - [Essential Status Codes (Server Outcome)](#essential-status-codes-server-outcome)
    - [🛠️ Practical Task 02: Inspecting HTTP/1.1 Headers](#-practical-task-02-inspecting-http11-headers)
- [Part 4 — JavaScript Essentials for Backend](#part-4--javascript-essentials-for-backend)
  - [12. Variables, Data Types & Type Coercion](#12-variables-data-types--type-coercion)
  - [13. Conditions, Loops & Functions](#13-conditions-loops--functions)
  - [14. Objects, JSON & Array Methods](#14-objects-json--array-methods)
    - [🛠️ Practical Task 03: Filter & Transform API Data](#-practical-task-03-filter--transform-api-data)
- [Part 5 — Node.js, NPM, Express.js & Postman](#part-5--nodejs-npm-expressjs--postman)
  - [15. What is Node.js? (Browser JS vs. Node.js)](#15-what-is-nodejs-browser-js-vs-nodejs)
  - [16. NPM, `package.json`, `package-lock.json` & Scripts](#16-npm-packagejson-package-lockjson--scripts)
  - [17. Raw Node.js (`node:http`) vs. Express.js](#17-raw-nodejs-nodehttp-vs-expressjs)
  - [18. Building & Testing Your First Express Server](#18-building--testing-your-first-express-server)
    - [🛠️ Practical Task 04: Create `server.js`](#-practical-task-04-create-serverjs)
    - [Smoke-Testing with Postman](#smoke-testing-with-postman)
    - [🛠️ Practical Task 05: Request → Response Trace Challenge](#-practical-task-05-request--response-trace-challenge)
- [📌 Session Recap & Before Session 02](#-session-recap--before-session-02)
  - [Key Takeaways](#key-takeaways)
  - [Before Session 02 (`Async JavaScript & Node.js Core`)](#before-session-02-async-javascript--nodejs-core)

---

## 🎯 Session Overview & Prerequisites

### Learning Objectives

By the end of this session, you will be able to:

1. **Understand the Backend Role:** Explain what the backend is responsible for, distinguish between Client and Server, and trace the full request–response lifecycle.
2. **Reason About Web Networking:** Differentiate between Networks, IP addresses, Ports, TCP/UDP, Domain Names, and DNS resolution.
3. **Read & Speak HTTP/HTTPS:** Understand why HTTPS/TLS is essential in production, dissect HTTP/1.1 requests and responses, and choose the right HTTP methods and status codes.
4. **Run JavaScript on the Server:** Execute JavaScript outside the browser using **Node.js 26**, manage packages with **NPM**, build a minimal **Express.js** server, and verify endpoints using **Postman**.

### Before the Session

Make sure your local development environment is ready by completing the setup steps in the prerequisites guide:

- 👉 **[Open Session 01 Prerequisites](./Prerequisites/README.md)**
- **Required Tools:** **Node.js 26**, **Visual Studio Code**, and **Postman**.

---

# Part 1 — Backend Foundations & Client-Server Architecture

## 1. What is Backend Development?

> **Core Principle:** *The user sees pixels. The backend runs truth.*

**Backend development** is the engineering of the invisible, server-side half of an application. Whenever a user logs in, submits a registration form, or checks exam grades, the frontend interface cannot be trusted to make authoritative decisions on its own.

Every backend application revolves around four core responsibilities:

| Step | Responsibility | What It Does |
| :--- | :--- | :--- |
| **01** | **Receive** | Listens on a network port for incoming HTTP requests from web browsers, mobile apps, or external APIs. |
| **02** | **Validate** | Rejects missing, malformed, or malicious input before running logic (*Rule #1: Never trust the client*). |
| **03** | **Process** | Executes business logic—verifying password hashes, calculating prices, or checking seat eligibility. |
| **04** | **Persist & Respond** | Reads/writes records in a database and sends back a clear HTTP status code + JSON payload. |

### Practical Analogy: Dining Room vs. Kitchen

- **Frontend (Dining Room):** Presents the menu (UI), offers comfortable seating, and collects the customer's order. Customers are never allowed inside the kitchen.
- **Backend (Kitchen & Pantry):** Guards raw ingredients (Database), follows strict safety and recipe rules (Business Logic), and sends out the finished plate (HTTP Response).

---

## 2. Client-Server Model & Request Lifecycle

Web applications split responsibilities between two distinct environments:

| Dimension | Client (Frontend) — *Untrusted Realm* | Server (Backend) — *Trusted Realm* |
| :--- | :--- | :--- |
| **Examples** | Web browsers (Chrome), Mobile apps (iOS/Android), Postman | A Node.js / Express application running continuously on a server machine |
| **Role** | Renders UI, captures clicks/input, sends HTTP requests, and displays responses | Listens for requests, authenticates users, enforces rules, and queries databases |
| **Security Reality** | Anyone can inspect or edit client HTML/JS via DevTools. **Never store DB passwords here**. | Single source of truth. Decides which data each client is authorized to see. |

### End-to-End Trace: Checking University Admission Results


```
┌─────────────┐   1. GET /api/admissions/948201    ┌──────────────────┐
│   Client    │ ────────────────────────────────── ►  Backend Server  │
│  (Browser)  │                                    │  (Node/Express)  │
│             ◄ ────────────────────────────────── │                  │
└─────────────┘   4. 200 OK + JSON Payload         └───────┬──▲───────┘
                                                           │  │
                                            2. Query ID    │  │ 3. Return
                                               948201      │  │    Record
                                                           │  │
                                                    ┌──────▼───────────┐
                                                    │     Database     │
                                                    │ (Candidate Rows) │
                                                    └──────────────────┘
```

1. **User Action:** A student enters Seat ID `948201` and clicks **Check Result**.
2. **HTTP Request:** The browser fires `GET /api/admissions/948201` across the network.
3. **Validation & Query:** The backend verifies that `948201` is a valid 6-digit number, then queries the database for that candidate record.
4. **HTTP Response & Render:** The server returns `200 OK` with `{ "status": "Admitted" }`, and the browser renders the result card.

---

# Part 2 — Networking Fundamentals

## 3. What is a Network?

A **computer network** is a system of two or more computing devices (clients, servers, routers, switches) connected via physical cables (fiber/Ethernet) or wireless signals (Wi-Fi/5G) to exchange data packets using agreed communication protocols.

- **Nodes (Hosts):** End devices that send or receive data (your laptop, phone, or cloud Linux server).
- **Switches & Routers:** Switches connect devices inside a Local Area Network (LAN); routers forward packets across networks on the global Internet (WAN).
- **Data Packets:** Large API responses are broken into small numbered chunks called **packets**, routed independently, and reassembled at the destination.

---

## 4. IP Addresses (Machine Coordinates)

An **Internet Protocol (IP) address** is a unique numerical label assigned to every device on a network—acting like a **building's street address** so routers know which machine should receive a packet.

```text
// IPv4 Format: 32-bit (4 octets, each 0–255)
Public IP:    142.250.180.206   // Reachable across the global Internet
Private IP:   192.168.1.14      // Local Wi-Fi / LAN device address
Loopback IP:  127.0.0.1         // "localhost" — routes traffic back to your own PC

// IPv6 Format: 128-bit Hexadecimal (created to solve IPv4 address exhaustion)
IPv6 Example: 2001:0db8:85a3::8a2e:0370:7334

```

> 💡 **Why `127.0.0.1` (`localhost`) Matters:** During local development, both your browser/Postman (Client) and your Node.js server (Backend) run on the same laptop. `127.0.0.1` lets them talk without leaving your machine.
> 
> 

---

## 5. Ports & Sockets (Application Doors)

A single server machine often runs multiple programs simultaneously (an API server, a database, SSH). While the **IP Address** locates the *building* (machine), a **Port Number (`0`–`65535`)** identifies the exact *apartment door* (running process) that should receive the packet.

```text
Complete Socket Address = IP Address + Port Number  -->  [http://127.0.0.1:3000](http://127.0.0.1:3000)

```

| Port | Service / Protocol | Purpose |
| --- | --- | --- |
| **`80`** | HTTP | Default unencrypted web traffic
| **`443`** | HTTPS | Default TLS-encrypted secure web traffic
| **`22`** | SSH | Secure remote terminal access to servers
| **`3000`** | Node.js / Express | Standard local development port for Node APIs
| **`5432`** | PostgreSQL | Default relational database listener port
| **`27017`** | MongoDB | Default NoSQL document database listener port

(Note: If you see `EADDRINUSE`, another process is already occupying that port.)

---

## 6. Transport Protocols: TCP vs. UDP

Once an IP address locates the machine and a Port selects the application, the **Transport Layer (Layer 4)** decides *how* packets are delivered:

| Feature | TCP (Transmission Control Protocol) | UDP (User Datagram Protocol) |
| --- | --- | --- |
| **Connection** | **Connection-oriented** (3-way handshake: `SYN` → `SYN-ACK` → `ACK`) | **Connectionless** (sends datagrams immediately without a handshake)
| **Reliability** | **Guaranteed** — numbers packets, confirms receipt (`ACK`), and retransmits lost packets in order | **Best-effort** — no acknowledgments, no ordering guarantees, no retransmissions
| **Speed / Latency** | Slightly higher overhead due to reliability checks | Ultra-fast, minimal overhead, lowest latency
| **Use Cases** | **HTTP/HTTPS REST APIs, Node.js/Express servers, SQL databases, file transfers** | **DNS lookups (Port 53), live video/voice calls, online gaming, live telemetry**


---

## 7. Domain Names & DNS Resolution

* **Domain Name:** A human-friendly alias such as `api.msp-tech.club`.


* **DNS (Domain Name System):** The internet's distributed phonebook that translates domain names into numerical IP addresses before any HTTP request can be sent.



```
User types domain ──► 1. Cache Check (Browser → OS → Router)
                          │ (if not cached)
                          ▼
                      2. Recursive DNS Resolver (ISP / 8.8.8.8 / 1.1.1.1)
                          │ returns IP (e.g., 104.21.48.12)
                          ▼
                      3. TCP Handshake on Port 443 ──► 4. HTTP Request Fires
```

### 🛠️ Practical Task 01: Local Network Diagnostics

Run these commands in your terminal to inspect your machine's network configuration:

```bash
# 1. Check your computer's local LAN IP address
$ ipconfig                   # Windows
$ ifconfig                   # macOS / Linux (or: ip a)

# 2. Test network reachability & round-trip time
$ ping -n 3 google.com

# 3. Query DNS directly to resolve a domain to an IP
$ nslookup github.com

# 4. List active listening ports on your machine
$ netstat -an | findstr LISTENING   # Windows
$ lsof -i -P -n | grep LISTEN       # macOS / Linux
```

---

## 8. Self-Study Reference: The OSI 7-Layer Model

(Included in the slide deck as a hidden self-study reference for home reading.)

| Layer | Name | Key Protocols & Concepts |
| --- | --- | --- |
| **L7** | **Application** | HTTP, HTTPS, DNS — where your Node.js / Express API lives
| **L6** | **Presentation** | UTF-8 / JSON encoding and TLS/SSL encryption & decryption
| **L5** | **Session** | Opens, maintains, and closes communication sessions
| **L4** | **Transport** | **TCP & UDP**, segmentation, and **Port Numbers** (`80`, `443`, `3000`)
| **L3** | **Network** | **IP Addresses** (`IPv4` / `IPv6`) and packet routing across routers
| **L2** | **Data Link** | MAC addresses, Ethernet frames, and local Wi-Fi switching
| **L1** | **Physical** | Raw binary bits (`0`s and `1`s) over cables, fiber, or radio waves

---

# Part 3 — HTTP, HTTPS & Wire Anatomy

## 9. HTTP vs. HTTPS & TLS Security

**HTTP (Hypertext Transfer Protocol)** is the stateless request–response contract defining how clients and servers format messages on the web.

* **HTTP (`Port 80`):** Transmits headers, URLs, and JSON bodies as **unencrypted plain text**. Anyone sniffing public Wi-Fi can read passwords and tokens. Safe **only** for local development (`http://localhost:3000`).


* **HTTPS (`Port 443`):** Wraps HTTP inside **TLS (Transport Layer Security)** encryption. Mandatory for all production APIs and websites.



### How HTTPS & TLS Protect Web Traffic

1. **Encryption (Confidentiality):** Scrambles headers, URLs, and JSON bodies into unreadable ciphertext so eavesdroppers cannot read sensitive data.


2. **Data Integrity:** Attaches cryptographic authentication codes (MAC/AEAD) to every packet so any tampering in transit is immediately detected and rejected.


3. **Server Authentication:** Proves the server genuinely owns the domain name before the client transmits credentials.


4. **SSL/TLS Certificates & CAs:** Trusted **Certificate Authorities (CAs)** issue digital X.509 certificates binding a domain name to the server's public key, verified automatically during the TLS handshake.



---

## 10. Anatomy of HTTP/1.1 Requests & Responses

Every HTTP/1.1 message consists of a **Start Line**, **Headers**, an **Empty Line**, and an optional **Body**:

**Outbound Request (`Client → Server`)**

```http
POST /api/v1/students HTTP/1.1
Host: api.msp-backend.dev
Content-Type: application/json
Authorization: Bearer <token>

{
  "name": "Mariam Adel",
  "track": "Backend"
}
```

**Inbound Response (`Server → Client`)**

```http
HTTP/1.1 201 Created
Content-Type: application/json
Date: Thu, 12 Nov 2026 18:50:00 GMT

{
  "id": 56,
  "name": "Mariam Adel",
  "status": "enrolled"
}
```

---

## 11. Common HTTP Methods & Status Codes

### HTTP Methods (Client Intent)

| Method | Action | Request Body? | Example |
| --- | --- | --- | --- |
| **`GET`** | Retrieve / read data safely | No | `GET /api/users`
| **`POST`** | Create a new resource | Yes | `POST /api/users`
| **`PUT`** | Replace an entire existing resource | Yes | `PUT /api/users/5`
| **`PATCH`** | Partially update specific fields | Yes | `PATCH /api/users/5`
| **`DELETE`** | Remove a resource | Optional | `DELETE /api/users/5`


### Essential Status Codes (Server Outcome)

| Category | Code | Meaning |
| --- | --- | --- |
| **2xx Success** | **`200 OK`** | Request succeeded and standard payload returned
| **2xx Success** | **`201 Created`** | New resource successfully created (typically after `POST`)
| **4xx Client Error** | **`400 Bad Request`** | Invalid JSON syntax or missing required fields
| **4xx Client Error** | **`401 Unauthorized`** | Missing or invalid authentication credentials/token
| **4xx Client Error** | **`403 Forbidden`** | Authenticated, but lacks permission for this action
| **4xx Client Error** | **`404 Not Found`** | Endpoint URL or requested resource ID does not exist
| **5xx Server Error** | **`500 Internal Server Error`** | Unhandled exception or crash inside backend code


### 🛠️ Practical Task 02: Inspecting HTTP/1.1 Headers

```bash
# Run curl -I in your terminal to inspect real HTTP headers:
$ curl -I https://api.github.com

// Sample HTTP/1.1 Wire Response Returned by Server:
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 1256
Connection: keep-alive

# Try requesting a path that does not exist:
$ curl -I https://api.github.com/nonexistent-route
// Observe Status Line: HTTP/1.1 404 Not Found
```

---

# Part 4 — JavaScript Essentials for Backend

## 12. Variables, Data Types & Type Coercion

```javascript
// 1. Variables: const (default) vs let (reassignable)
const PORT = 3000;
let requestCount = 0;
requestCount += 1;

// 2. Primitive Data Types
const appName = "MSP-SESSION";   // String
const maxUsers = 100;         // Number
const isActive = true;        // Boolean
const authToken = null;       // Intentional empty
let pendingResult;            // undefined


// BACKEND TRAP: URL query params arrive as Strings!
const queryPage = "2";

// Bug: '+' concatenates strings instead of adding
const wrongNext = queryPage + 1; // "21" (BUG!)

// Fix: Convert explicitly with Number()
const safePage = Number(queryPage);
const nextPage = safePage + 1;   // 3 (CORRECT)

// Always use Strict Equality (===)
console.log("3000" == 3000);  // true  (loose)
console.log("3000" === 3000); // false (strict)
```

---

## 13. Conditions, Loops & Functions

```javascript
// 1. Conditions (Guard Clauses for API Validation)
function checkRole(role, isVerified) {
  if (!isVerified) {
    return "403: Email not verified";
  }
  return role === "admin" ? "Full Access" : "Student";
}

// 2. Loops (Iterating over server ports or records)
const openPorts = [80, 443, 3000];
for (const port of openPorts) {
  console.log(`Listening on port ${port}`);
}

// 3. Standard Function Declaration
function calculatePrice(base, taxRate) {
  return base + base * taxRate;
}

// 4. Arrow Function (Standard in Express routes)
const isValidSeatId = (seatId) => {
  return typeof seatId === "string" && seatId.length === 6;
};

// 5. Implicit Return One-Liner
const isSuccessStatus = (code) => code >= 200 && code < 300;

console.log(isValidSeatId("948201")); // true
```

---

## 14. Objects, JSON & Array Methods

```javascript
// Objects represent database records & JSON payloads
const student = {
  id: 56,
  name: "Tarek Hassan",
  track: "Backend",
  active: true
};

// Object Destructuring (Used constantly in req.body)
const { name, track } = student;

// JSON Serialization (Object <-> Wire String)
const rawJson = JSON.stringify(student);
const parsed  = JSON.parse(rawJson);

// Array Methods
const users = [
  { id: 1, name: "Ahmed", active: true },
  { id: 2, name: "Ali", active: false },
  { id: 3, name: "Omar", active: true }
];

// 1. .filter() -> Keep items matching a condition
const activeUsers = users.filter((u) => u.active);

// 2. .map() -> Transform each item for API response
const activeNames = activeUsers.map((u) => u.name);
console.log(activeNames); // ["Ahmed", "Omar"]

// 3. .find() -> Return the first match (or undefined)
const userTwo = users.find((u) => u.id === 3);

```

### 🛠️ Practical Task 03: Filter & Transform API Data

Create `js_exercise.js`, complete the `TODO` steps, and run `node js_exercise.js` (expected output: `[ 'Youssef: 88', 'Karim: 94' ]`):

```javascript
// Save as js_exercise.js and complete the 3 TODO steps:
const submissions = [
  { student: "Youssef", score: 88, submitted: true },
  { student: "Salma",   score: 45, submitted: false },
  { student: "Karim",   score: 94, submitted: true },
  { student: "Hana",    score: 72, submitted: true }
];

// TODO 1: Use .filter() to keep only students where
//         submitted === true AND score >= 75
const passed = // your code here

// TODO 2: Use .map() to format each item as 'Name: Score'
const summary = // your code here

// TODO 3: Print summary to the terminal
console.log(summary);
```

---

# Part 5 — Node.js, NPM, Express.js & Postman

## 15. What is Node.js? (Browser JS vs. Node.js)

**Node.js** is an open-source **JavaScript runtime environment** (not a new programming language) built on Chrome's V8 engine that allows JavaScript to execute directly on your operating system outside the browser.

| Capability | Browser JavaScript | Node.js Runtime |
| --- | --- | --- |
| **Global Objects** | `window`, `document` (HTML/CSS DOM) | `global`, `process` (no browser DOM)
| **Filesystem Access** | **Prohibited** (sandboxed for user security) | **Full Access** via built-in `fs` module
| **Network Ports** | **Unavailable** (cannot act as a server) | **Full Binding** via `http` / Express on ports like `3000`

Verify your Node.js 26 installation in the terminal:

```bash
node -v   # Expected: v26.x.x
```

---

## 16. NPM, `package.json`, `package-lock.json` & Scripts

* **NPM (Node Package Manager):** Ships with Node.js and installs reusable packages from the npm registry.


* **`package.json`:** The project's manifest/blueprint recording metadata, custom terminal `scripts`, and dependency version ranges.


* **`package-lock.json`:** Locks the exact resolved versions and integrity hashes of every dependency so teammates install identical code.


* **`node_modules/`:** Folder holding downloaded library source code. **Never commit `node_modules/` to Git**—recreate it anytime with `npm install`.



```bash
# 1. Create a folder and move into it
mkdir msp-session-01 && cd msp-session-01

# 2. Create package.json automatically with defaults
npm init -y   # -y skips interactive questions

# 3. Download Express into node_modules & update JSONs
npm install express
```

Adding reusable automation `scripts` inside `package.json`:

```json
{
  "name": "msp-session-01",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "node --watch server.js"
  },
  "dependencies": {
    "express": "^5.3.0"
  }
}
```

* `npm start` — Runs the `"start"` script.


* `npm run dev` — Runs the `"dev"` script using Node.js 26's built-in `--watch` auto-restart flag.



---

## 17. Raw Node.js (`node:http`) vs. Express.js

Before using Express, it helps to see how a raw server works using Node's built-in `node:http` module:

```javascript
// 1. Load Node's built-in 'http' module
const http = require('node:http');

// 2. Create server & define request handler callback
const server = http.createServer((req, res) => {
  if (req.method === 'GET' && req.url === '/health') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    return res.end(JSON.stringify({ status: 'ok' }));
  }
  res.writeHead(404, { 'Content-Type': 'text/plain' });
  res.end('Route Not Found');
});

// 3. Start listening for traffic on Port 3000
server.listen(3000, () => console.log('Listening on 3000'));
```

**Why Express.js?** As endpoints grow, manual `if/else` routing, manual `JSON.stringify()`, and manual body stream parsing become repetitive. **Express.js** wraps `node:http` and introduces clean **Routes** (`HTTP Method + URL Path + Handler`), automatic JSON helpers (`res.status(200).json(...)`), and middleware (`express.json()`):

* **`req` (Request):** Represents the incoming client request (`req.body`, `req.params`, `req.query`, headers).
* **`res` (Response):** Sends the reply back to the client (`res.status(200).json(...)`).

```javascript
// Compare how clean the same endpoint is in Express:
const express = require('express');
const app = express();

// Route: Method (GET) + Path ('/health') + Handler
app.get('/health', (req, res) => {
  res.status(200).json({ status: 'ok' });
});

// Automatic 404 handling for unmatched routes!
app.listen(3000, () => {
  console.log('Express server listening on port 3000');
});
```

---

## 18. Building & Testing Your First Express Server

### 🛠️ Practical Task 04: Create `server.js`

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

// 1. Built-in middleware to parse incoming JSON bodies
app.use(express.json());

// 2. GET Route: Welcome endpoint
app.get('/', (req, res) => {
  res.status(200).json({ message: 'MSP Backend Server Online!' });
});

// 3. POST Route: Receive & echo student registration
app.post('/api/students', (req, res) => {
  const { name, track } = req.body;
  res.status(201).json({ enrolled: true, student: { name, track } });
});

// 4. Bind server to Port 3000
app.listen(PORT, () => console.log(`Running on http://localhost:${PORT}`));
```

Start your server:

```bash
node server.js
```

### Smoke-Testing with Postman

1. **Test `GET /`:** Open a new request tab in Postman → select **`GET`** → enter `http://localhost:3000/` → click **Send** → verify **`200 OK`** and the JSON welcome message.
2. **Test `POST /api/students`:** Select **`POST`** → enter `http://localhost:3000/api/students` → open **Body → raw → JSON** → paste the JSON below → click **Send** → verify **`201 Created`**:



```json
{
  "name": "Nourhan Tarek",
  "track": "Backend"
}
```

### 🛠️ Practical Task 05: Request → Response Trace Challenge

1. Trace out loud every hop from clicking **Send** in Postman on `POST http://localhost:3000/api/students` (`Postman` → `127.0.0.1:3000 OS Socket` → `express.json()` → `app.post` handler → `201 Created` JSON response).


2. **Mini-Challenge:** Add a `GET /api/status` route in `server.js` that returns `{ status: "healthy", uptime: process.uptime() }`, restart your server, and verify **`200 OK`** in Postman.



---

## 📌 Session Recap & Before Session 02

### Key Takeaways

* ✅ **Backend & Client-Server:** The client renders UI and sends requests; the backend validates input, executes business logic, interacts with data, and returns responses.
* ✅ **Networking & Transport:** IP addresses locate machines (`127.0.0.1`), Ports select applications (`3000`), TCP guarantees ordered delivery, and DNS resolves domains to IPs.
* ✅ **HTTP/1.1 & HTTPS:** HTTPS protects traffic via TLS encryption, integrity, authentication, and certificates; APIs communicate via verbs (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`) and status codes (`200`, `201`, `400`, `401`, `403`, `404`, `500`).
* ✅ **Node.js & Express:** Node.js 26 executes JS on the server, NPM manages dependencies via `package.json`, and Express simplifies routing and JSON APIs.



### Before Session 02 (`Async JavaScript & Node.js Core`)

1. **Verify Node.js 26 & NPM Setup:** Confirm `node -v` (`v26.x.x`) and `npm -v` work cleanly on your personal laptop.
2. **Run a JS File via Node:** Execute your completed preparation script from the terminal using `node prep_session02.js`.
3. **Complete the Coding Exercise:** Practice functions, arrays (`.filter`/`.map`), and objects using the `prep_session02.js` exercise from Slide 33.
4. **Be Ready to Explain Out Loud:** Selected members will be called up next session to explain **Browser JS vs. Node.js**, trace a request–response path, or present the **OSI Model** (Slides 12–13).