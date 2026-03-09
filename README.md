# 📗 Node.js & Express.js — Complete Concepts Guide

> A comprehensive, beginner-to-advanced reference covering every Node.js and Express.js concept with detailed explanations, real-world code examples, and best practices.

---

## 📚 Table of Contents

### 🟢 Node.js Core
1. [Introduction to Node.js](#1-introduction-to-nodejs)
2. [Node.js Architecture & Event Loop](#2-nodejs-architecture--event-loop)
3. [Modules System (CommonJS & ESM)](#3-modules-system-commonjs--esm)
4. [Built-in Core Modules](#4-built-in-core-modules)
5. [File System (fs)](#5-file-system-fs)
6. [Path Module](#6-path-module)
7. [OS Module](#7-os-module)
8. [HTTP Module (Raw Server)](#8-http-module-raw-server)
9. [Streams & Buffers](#9-streams--buffers)
10. [Events & EventEmitter](#10-events--eventemitter)
11. [Child Processes](#11-child-processes)
12. [Worker Threads](#12-worker-threads)
13. [Timers & Scheduling](#13-timers--scheduling)
14. [Process Object](#14-process-object)
15. [npm & Package Management](#15-npm--package-management)
16. [Environment Variables](#16-environment-variables)
17. [Error Handling in Node.js](#17-error-handling-in-nodejs)
18. [Debugging Node.js](#18-debugging-nodejs)

### 🔵 Express.js Core
19. [Introduction to Express.js](#19-introduction-to-expressjs)
20. [Routing](#20-routing)
21. [Middleware](#21-middleware)
22. [Request Object (req)](#22-request-object-req)
23. [Response Object (res)](#23-response-object-res)
24. [Static Files](#24-static-files)
25. [Template Engines](#25-template-engines)
26. [Error Handling in Express](#26-error-handling-in-express)
27. [Router & Modular Routes](#27-router--modular-routes)
28. [Body Parsing](#28-body-parsing)
29. [Cookies & Sessions](#29-cookies--sessions)
30. [Authentication & Authorization](#30-authentication--authorization)
31. [File Uploads](#31-file-uploads)
32. [CORS](#32-cors)
33. [Rate Limiting & Security](#33-rate-limiting--security)
34. [Database Integration](#34-database-integration)
35. [REST API Design & Best Practices](#35-rest-api-design--best-practices)
36. [WebSockets with Express](#36-websockets-with-express)
37. [Testing Node.js & Express](#37-testing-nodejs--express)
38. [Deployment & Production](#38-deployment--production)

---

## 1. Introduction to Node.js

### What is Node.js?

Node.js is a **free, open-source, cross-platform JavaScript runtime environment** that allows you to run JavaScript code outside of a browser. It was created by **Ryan Dahl in 2009** and is built on **Google's V8 JavaScript engine**.

```
Traditional Model (Multi-threaded, Blocking):
  Request 1 ──▶ Thread 1 ──▶ [waits for DB] ──▶ Response
  Request 2 ──▶ Thread 2 ──▶ [waits for DB] ──▶ Response
  Request 3 ──▶ Thread 3 ──▶ [waits for DB] ──▶ Response

Node.js Model (Single-threaded, Non-blocking):
  Request 1 ──▶ ──▶ [sends DB query, moves on] ──▶ ...callback
  Request 2 ──▶ ──▶ [sends DB query, moves on] ──▶ ...callback
  Request 3 ──▶ ──▶ [sends DB query, moves on] ──▶ ...callback
  All handled on ONE thread via Event Loop!
```

### Key Characteristics

| Feature | Description |
|---|---|
| **Asynchronous** | Non-blocking I/O operations |
| **Event-driven** | Uses events and callbacks |
| **Single-threaded** | One thread, handles concurrency via event loop |
| **Cross-platform** | Runs on Windows, Mac, Linux |
| **npm Ecosystem** | 2M+ packages via npm registry |
| **V8 Engine** | Same engine that powers Chrome |

### Installation & Version Management

```bash
# Install Node.js from nodejs.org
# Or use Node Version Manager (NVM) — recommended:

# Install NVM:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Install a specific Node.js version:
nvm install 20          # install Node 20 (LTS)
nvm install --lts       # install latest LTS
nvm use 20              # switch to Node 20
nvm alias default 20    # set as default

# Check version:
node --version          # v20.x.x
npm --version           # 10.x.x

# Run a JavaScript file:
node app.js

# Run with flags:
node --inspect app.js           # enable debugger
node --watch app.js             # watch mode (Node 18+)
node --env-file=.env app.js     # load env file (Node 20+)

# Interactive REPL (Read-Eval-Print Loop):
node                    # opens REPL
> 2 + 2                 # 4
> .help                 # show REPL commands
> .exit                 # exit REPL
```

### Your First Node.js Program

```javascript
// hello.js

// Node.js has no 'window' object — it's NOT a browser
// But it has 'global' (like window in browser)

console.log("Hello, Node.js!");
console.log("Node version:", process.version);
console.log("Platform:", process.platform);
console.log("Working directory:", process.cwd());

// Built-in global objects:
console.log(typeof window);     // "undefined" — no browser!
console.log(typeof global);     // "object" — Node's global
console.log(typeof process);    // "object" — Node's process info
console.log(typeof __dirname);  // "string" — current directory
console.log(typeof __filename); // "string" — current file path
console.log(typeof require);    // "function" — import modules
```

```bash
node hello.js
# Hello, Node.js!
# Node version: v20.x.x
# Platform: linux
# Working directory: /home/user/project
```

---

## 2. Node.js Architecture & Event Loop

### How Node.js Works Internally

```
┌─────────────────────────────────────────────────────┐
│                   NODE.JS PROCESS                   │
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │              V8 ENGINE (JavaScript)          │   │
│  │  Parses + Compiles + Executes JS code        │   │
│  └──────────────────────────────────────────────┘   │
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │           NODE.JS BINDINGS (C++)             │   │
│  │  Bridges JS ↔ OS (file, network, etc.)       │   │
│  └──────────────────────────────────────────────┘   │
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │              LIBUV (C library)               │   │
│  │  Event Loop + Thread Pool + Async I/O        │   │
│  └──────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

### The Event Loop — Phase by Phase

```
Node.js Event Loop — 6 Phases:

   ┌─────────────────────────────┐
   │           timers            │  ← setTimeout, setInterval callbacks
   └──────────────┬──────────────┘
                  │
   ┌──────────────▼──────────────┐
   │       pending callbacks     │  ← I/O callbacks deferred to next loop
   └──────────────┬──────────────┘
                  │
   ┌──────────────▼──────────────┐
   │         idle, prepare       │  ← internal use only
   └──────────────┬──────────────┘
                  │
   ┌──────────────▼──────────────┐
   │             poll            │  ← retrieve new I/O events ⭐
   └──────────────┬──────────────┘
                  │
   ┌──────────────▼──────────────┐
   │             check           │  ← setImmediate callbacks
   └──────────────┬──────────────┘
                  │
   ┌──────────────▼──────────────┐
   │        close callbacks      │  ← socket.on('close', ...)
   └──────────────┬──────────────┘
                  │
   ┌──────────────▼──────────────┐
   │   nextTick + microtasks     │  ← process.nextTick(), Promises
   └─────────────────────────────┘
   (runs between EVERY phase!)
```

### Event Loop Execution Order

```javascript
// Demonstrates the execution order of different async mechanisms

console.log("1 - Script start");   // synchronous

// setTimeout — goes to 'timers' phase (delay >= 0ms)
setTimeout(() => console.log("5 - setTimeout"), 0);

// setImmediate — goes to 'check' phase (after I/O)
setImmediate(() => console.log("4 - setImmediate"));

// process.nextTick — runs after current operation, before I/O phases
process.nextTick(() => console.log("3 - nextTick"));

// Promise microtask — runs after nextTick queue
Promise.resolve().then(() => console.log("3.5 - Promise"));

console.log("2 - Script end");     // synchronous

// Output order:
// 1 - Script start
// 2 - Script end
// 3 - nextTick           ← nextTick queue (before microtasks)
// 3.5 - Promise          ← microtask queue
// 4 - setImmediate       ← check phase
// 5 - setTimeout         ← timers phase
```

### Thread Pool (libuv)

```javascript
// libuv provides a thread pool (default 4 threads) for:
// - File I/O operations
// - DNS lookups
// - Some crypto operations
// - Custom C++ addons

const os = require("os");
const crypto = require("crypto");

// Increase thread pool size for CPU-intensive operations:
process.env.UV_THREADPOOL_SIZE = os.cpus().length; // set before requiring modules

// These operations use the thread pool:
const fs = require("fs");
const start = Date.now();

// 4 concurrent operations — fit in thread pool:
for (let i = 0; i < 4; i++) {
  crypto.pbkdf2("password", "salt", 100000, 64, "sha512", () => {
    console.log(`Hash ${i + 1} done in ${Date.now() - start}ms`);
  });
}

// With UV_THREADPOOL_SIZE=4 (default), all 4 run in parallel (~same time)
// With UV_THREADPOOL_SIZE=1, they'd run sequentially (~4x longer)
```

### Blocking vs Non-Blocking

```javascript
const fs = require("fs");

// ❌ BLOCKING — freezes event loop!
// No other requests can be handled while this runs:
const data = fs.readFileSync("large-file.txt", "utf8"); // blocks
console.log("File content:", data.length, "chars");
// Server is frozen until file is read!

// ✅ NON-BLOCKING — event loop keeps running:
fs.readFile("large-file.txt", "utf8", (err, data) => {
  if (err) throw err;
  console.log("File content:", data.length, "chars");
  // This runs when file is ready — event loop free meanwhile!
});
console.log("This runs WHILE file is being read"); // runs first!

// ✅ Even better — use promises:
const fsPromises = require("fs").promises;

async function readFileSafe() {
  try {
    const data = await fsPromises.readFile("large-file.txt", "utf8");
    console.log("File content:", data.length, "chars");
  } catch (err) {
    console.error("Error reading file:", err.message);
  }
}
```

---

## 3. Modules System (CommonJS & ESM)

### CommonJS (CJS) — Traditional Node.js

```javascript
// ─── math.js ──────────────────────────────────────────────
// Every file is its own module with its own scope!

// Local variables — NOT global, invisible to other files:
const PI = 3.14159;

// Export a single function:
function add(a, b) {
  return a + b;
}

// Export multiple things via module.exports:
module.exports = {
  PI,
  add,
  subtract: (a, b) => a - b,
  multiply: (a, b) => a * b
};

// ─── OR export individually with exports shorthand ─────────
// exports is a reference to module.exports

exports.add      = (a, b) => a + b;
exports.subtract = (a, b) => a - b;
// ⚠️ exports = { ... } breaks the reference — use module.exports for this!

// ─── app.js ───────────────────────────────────────────────

// require() — synchronous, runs the module file once, caches result:
const math = require("./math");           // relative path
const path = require("path");              // built-in module
const express = require("express");        // npm package (from node_modules)
const config = require("./config.json");  // can import JSON too!

console.log(math.add(2, 3));   // 5
console.log(math.PI);          // 3.14159

// Destructure on import:
const { add, subtract } = require("./math");
console.log(add(10, 5));       // 15
console.log(subtract(10, 5));  // 5

// How require() works — step by step:
// 1. Resolves the path (./math → ./math.js or ./math/index.js)
// 2. Checks cache (require.cache) — returns cached if found
// 3. Creates new Module object
// 4. Wraps file content in Module Wrapper Function
// 5. Executes the wrapped code
// 6. Returns module.exports
// 7. Caches the result

// The Module Wrapper (what Node.js wraps every module in):
(function(exports, require, module, __filename, __dirname) {
  // Your module code lives here
  // This is why __dirname, __filename, require, module, exports exist!
});
```

### ES Modules (ESM) — Modern Standard

```javascript
// To use ESM in Node.js:
// Option 1: rename file to .mjs
// Option 2: add "type": "module" to package.json

// ─── math.mjs ─────────────────────────────────────────────

// Named exports:
export const PI = 3.14159;
export function add(a, b)      { return a + b; }
export function subtract(a, b) { return a - b; }

// Default export:
export default class Calculator {
  add(a, b) { return a + b; }
}

// ─── app.mjs ──────────────────────────────────────────────

// Named imports:
import { PI, add, subtract } from "./math.mjs";

// Default import:
import Calculator from "./math.mjs";

// Import all:
import * as MathLib from "./math.mjs";

// Import built-in modules (with node: prefix — recommended):
import { readFile } from "node:fs/promises";
import { join }     from "node:path";
import { createServer } from "node:http";

// Dynamic import (lazy loading — works in both CJS and ESM):
const { add: addFn } = await import("./math.mjs");

// ESM features NOT in CJS:
// ✅ Top-level await
const data = await readFile("file.txt", "utf8"); // works at top level!

// ✅ Tree-shakeable (bundlers can remove unused exports)
// ✅ Strict mode by default
// ✅ No __dirname or __filename — use import.meta instead:
import { fileURLToPath } from "node:url";
import { dirname }       from "node:path";

const __filename = fileURLToPath(import.meta.url);
const __dirname  = dirname(__filename);
const __url      = import.meta.url; // file:///path/to/file.mjs
```

### CJS vs ESM Comparison

```javascript
// CJS                             // ESM
require("./module")                // import { x } from "./module.js"
module.exports = x                 // export default x
exports.x = x                     // export const x = ...
const x = require("./m")          // import x from "./m.js"  
No top-level await                 // await at top level ✅
__dirname available                // use import.meta.url
Sync loading                       // Async loading
No tree-shaking                    // Tree-shakeable

// ─── Interop — CJS in ESM project ─────────────────────────
// CJS modules can be imported in ESM with default import:
import cjsModule from "./old-module.cjs"; // cjsModule = module.exports

// ─── Interop — ESM in CJS project ─────────────────────────
// Must use dynamic import():
async function loadEsmModule() {
  const { add } = await import("./esm-module.mjs");
  return add(1, 2);
}
```

### Module Caching

```javascript
// Node.js caches modules after first require()
// Subsequent require() calls return the SAME instance

// counter.js:
let count = 0;
module.exports = {
  increment: () => ++count,
  getCount: () => count
};

// app.js:
const c1 = require("./counter");
const c2 = require("./counter"); // same cached instance!

c1.increment(); // count = 1
c2.increment(); // count = 2 (same object!)
console.log(c1 === c2);          // true
console.log(c1.getCount());      // 2 (shared state!)

// Clear cache (useful in testing):
delete require.cache[require.resolve("./counter")];
const c3 = require("./counter"); // fresh instance
console.log(c3.getCount());      // 0

// View all cached modules:
console.log(Object.keys(require.cache));
```

---

## 4. Built-in Core Modules

### Overview of Core Modules

```javascript
// Node.js includes many built-in modules — no npm install needed!

// File system:
const fs       = require("fs");           // sync/callback file ops
const fsP      = require("fs/promises");  // promise-based file ops

// Networking:
const http     = require("http");         // HTTP server/client
const https    = require("https");        // HTTPS server/client
const net      = require("net");          // TCP server/client
const dns      = require("dns");          // DNS lookups

// Streams & Buffers:
const stream   = require("stream");       // stream primitives
const readline = require("readline");     // line-by-line reading

// Utilities:
const path     = require("path");         // path manipulation
const os       = require("os");           // operating system info
const util     = require("util");         // utility functions
const url      = require("url");          // URL parsing
const querystring = require("querystring"); // query string parsing

// Crypto & Security:
const crypto   = require("crypto");       // encryption, hashing
const tls      = require("tls");          // TLS/SSL

// Process & System:
const child_process = require("child_process"); // spawn processes
const cluster  = require("cluster");      // multi-core clustering
const worker_threads = require("worker_threads"); // parallel threads

// Events:
const events   = require("events");       // EventEmitter base class

// Buffers & Data:
const buffer   = require("buffer");       // Buffer class
const zlib     = require("zlib");         // gzip/deflate compression

// Testing (Node 18+):
const assert   = require("assert");       // assertion testing
const test     = require("node:test");    // built-in test runner
```

---

## 5. File System (fs)

### Reading Files

```javascript
const fs   = require("fs");
const fsP  = require("fs/promises");
const path = require("path");

// ─── Synchronous (blocks event loop — avoid in servers!) ───
try {
  // Read as string:
  const text = fs.readFileSync("readme.txt", "utf8");
  console.log(text);

  // Read as Buffer (binary):
  const buffer = fs.readFileSync("image.png");
  console.log(buffer.length, "bytes");
} catch (err) {
  console.error("Error:", err.message);
}

// ─── Callback (old style) ─────────────────────────────────
fs.readFile("readme.txt", "utf8", (err, data) => {
  if (err) {
    console.error("Error:", err.message);
    return;
  }
  console.log(data);
});

// ─── Promises (modern — preferred) ────────────────────────
async function readFiles() {
  try {
    // Read a text file:
    const text = await fsP.readFile("readme.txt", "utf8");
    console.log(text);

    // Read and parse JSON:
    const rawJson = await fsP.readFile("config.json", "utf8");
    const config  = JSON.parse(rawJson);
    console.log(config);

    // Read binary:
    const buffer  = await fsP.readFile("image.png");
    console.log("Image size:", buffer.length, "bytes");

    // Read directory contents:
    const files   = await fsP.readdir("./src");
    console.log("Files:", files);

    // Read with options:
    const details = await fsP.readdir("./src", { withFileTypes: true });
    details.forEach(dirent => {
      if (dirent.isFile())      console.log("File:", dirent.name);
      if (dirent.isDirectory()) console.log("Dir:",  dirent.name);
    });
  } catch (err) {
    console.error("Error:", err.code, err.message);
  }
}
```

### Writing Files

```javascript
const fsP = require("fs/promises");

async function writeExamples() {
  // ─── Write file (creates or overwrites) ──────────────────
  await fsP.writeFile("output.txt", "Hello, World!\n", "utf8");
  await fsP.writeFile("output.json", JSON.stringify({ key: "value" }, null, 2));

  // ─── Append to file ───────────────────────────────────────
  await fsP.appendFile("log.txt", `${new Date().toISOString()} - Event logged\n`);

  // ─── Atomic write (write to temp, then rename) ────────────
  // Safer — prevents data corruption if write fails midway:
  const tmpFile = "output.tmp";
  await fsP.writeFile(tmpFile, "Important data");
  await fsP.rename(tmpFile, "output.txt"); // atomic on same filesystem

  // ─── Write with options ───────────────────────────────────
  await fsP.writeFile("output.txt", "content", {
    encoding: "utf8",
    flag: "a",   // a=append, w=write (default), wx=write-exclusive
    mode: 0o644  // file permissions (octal)
  });

  // ─── Write stream (efficient for large data) ──────────────
  const writeStream = require("fs").createWriteStream("large-output.txt");
  for (let i = 0; i < 100000; i++) {
    writeStream.write(`Line ${i}: data data data\n`);
  }
  writeStream.end();
  writeStream.on("finish", () => console.log("Write complete!"));
  writeStream.on("error", (err) => console.error(err));
}
```

### File & Directory Operations

```javascript
const fsP = require("fs/promises");
const path = require("path");

async function fileOps() {
  // ─── Check if file/dir exists ─────────────────────────────
  try {
    await fsP.access("file.txt", require("fs").constants.F_OK);
    console.log("File exists");
  } catch {
    console.log("File does not exist");
  }

  // ─── Get file stats ───────────────────────────────────────
  const stats = await fsP.stat("file.txt");
  console.log("Size:", stats.size, "bytes");
  console.log("Created:", stats.birthtime);
  console.log("Modified:", stats.mtime);
  console.log("Is file?", stats.isFile());
  console.log("Is dir?", stats.isDirectory());
  console.log("Permissions:", stats.mode.toString(8));

  // ─── Create directory ─────────────────────────────────────
  await fsP.mkdir("new-folder");                          // single dir
  await fsP.mkdir("nested/path/dir", { recursive: true }); // nested dirs

  // ─── Remove file ──────────────────────────────────────────
  await fsP.unlink("file-to-delete.txt");

  // ─── Remove directory ─────────────────────────────────────
  await fsP.rmdir("empty-dir");
  await fsP.rm("any-dir", { recursive: true, force: true }); // Node 14+

  // ─── Copy file ────────────────────────────────────────────
  await fsP.copyFile("source.txt", "destination.txt");

  // ─── Move / Rename ────────────────────────────────────────
  await fsP.rename("old-name.txt", "new-name.txt");
  await fsP.rename("./old-dir/file.txt", "./new-dir/file.txt");

  // ─── Watch file changes ───────────────────────────────────
  const watcher = fsP.watch("config.json", { signal: new AbortController().signal });
  for await (const event of watcher) {
    console.log("Change detected:", event.eventType, event.filename);
  }
}

// ─── Recursive directory operations ───────────────────────
async function copyDir(src, dest) {
  await fsP.mkdir(dest, { recursive: true });
  const entries = await fsP.readdir(src, { withFileTypes: true });

  for (const entry of entries) {
    const srcPath  = path.join(src, entry.name);
    const destPath = path.join(dest, entry.name);

    if (entry.isDirectory()) {
      await copyDir(srcPath, destPath);   // recurse
    } else {
      await fsP.copyFile(srcPath, destPath);
    }
  }
}

// Walk directory tree:
async function* walkDir(dir) {
  const entries = await fsP.readdir(dir, { withFileTypes: true });
  for (const entry of entries) {
    const fullPath = path.join(dir, entry.name);
    if (entry.isDirectory()) {
      yield* walkDir(fullPath); // recurse — generator delegation
    } else {
      yield fullPath;
    }
  }
}

// Usage:
for await (const file of walkDir("./src")) {
  console.log(file);
}
```

---

## 6. Path Module

```javascript
const path = require("path");

// ─── Core methods ─────────────────────────────────────────

// join — combine path segments (handles OS separators):
path.join("/users", "alice", "documents", "file.txt");
// → "/users/alice/documents/file.txt" (Linux/Mac)
// → "\\users\\alice\\documents\\file.txt" (Windows)

path.join(__dirname, "views", "index.html"); // relative to current file

// resolve — creates ABSOLUTE path from segments:
path.resolve("src", "utils", "helper.js");
// → "/current/working/dir/src/utils/helper.js"

path.resolve("/absolute", "relative");
// → "/absolute/relative"

// dirname — directory portion of path:
path.dirname("/users/alice/file.txt");  // "/users/alice"
path.dirname(__filename);               // directory of current file

// basename — filename portion:
path.basename("/users/alice/file.txt");         // "file.txt"
path.basename("/users/alice/file.txt", ".txt"); // "file" (removes ext)

// extname — file extension:
path.extname("file.txt");      // ".txt"
path.extname("archive.tar.gz"); // ".gz"
path.extname("no-extension");  // ""

// parse — break path into parts:
const parsed = path.parse("/home/alice/documents/file.txt");
// {
//   root: '/',
//   dir: '/home/alice/documents',
//   base: 'file.txt',
//   ext: '.txt',
//   name: 'file'
// }

// format — build path from parts:
path.format({
  dir: "/home/alice",
  name: "file",
  ext: ".txt"
}); // "/home/alice/file.txt"

// normalize — clean up path:
path.normalize("/users//alice/../bob/./file.txt");
// → "/users/bob/file.txt"

// relative — relative path between two paths:
path.relative("/home/alice", "/home/alice/docs/file.txt");
// → "docs/file.txt"

path.relative("/home/alice/src", "/home/bob/dest");
// → "../../bob/dest"

// ─── Platform-specific ────────────────────────────────────
path.sep;       // "/" on Unix, "\" on Windows
path.delimiter; // ":" on Unix, ";" on Windows (for PATH env var)
path.posix.join("/users", "file");    // always unix style
path.win32.join("C:\\Users", "file"); // always windows style

// ─── Practical examples ───────────────────────────────────
const { join, dirname, resolve } = require("path");

// Build paths relative to current file (works no matter where you run):
const rootDir     = join(__dirname, "..", "..");
const configPath  = join(__dirname, "config", "default.json");
const uploadsDir  = join(__dirname, "public", "uploads");
const viewsDir    = resolve(__dirname, "views");

console.log("Root:", rootDir);
console.log("Config:", configPath);
```

---

## 7. OS Module

```javascript
const os = require("os");

// System information:
console.log("OS Platform:", os.platform());    // "linux", "darwin", "win32"
console.log("OS Type:",     os.type());        // "Linux", "Darwin", "Windows_NT"
console.log("OS Version:",  os.release());     // "5.15.0-..."
console.log("OS Arch:",     os.arch());        // "x64", "arm64"

// CPU information:
const cpus = os.cpus();
console.log("CPU Count:", cpus.length);
console.log("CPU Model:", cpus[0].model);
console.log("CPU Speed:", cpus[0].speed, "MHz");

// Memory:
const totalMem  = os.totalmem();
const freeMem   = os.freemem();
const usedMem   = totalMem - freeMem;
console.log("Total RAM:", (totalMem / 1024 ** 3).toFixed(2), "GB");
console.log("Free RAM:",  (freeMem  / 1024 ** 3).toFixed(2), "GB");
console.log("Used RAM:",  (usedMem  / 1024 ** 3).toFixed(2), "GB");

// Network interfaces:
const networkInterfaces = os.networkInterfaces();
Object.entries(networkInterfaces).forEach(([name, addrs]) => {
  addrs.forEach(addr => {
    if (addr.family === "IPv4" && !addr.internal) {
      console.log(`${name}: ${addr.address}`);
    }
  });
});

// User and directories:
console.log("Home dir:",   os.homedir());       // "/home/alice"
console.log("Temp dir:",   os.tmpdir());        // "/tmp"
console.log("Hostname:",   os.hostname());      // "server-01"
console.log("Username:",   os.userInfo().username);

// Uptime:
const uptime = os.uptime();
console.log("Uptime:", Math.floor(uptime / 3600), "hours");

// Load average (Unix only):
const [avg1, avg5, avg15] = os.loadavg();
console.log(`Load: ${avg1.toFixed(2)} (1m) ${avg5.toFixed(2)} (5m)`);

// Practical: set thread pool size:
process.env.UV_THREADPOOL_SIZE = String(os.cpus().length);
```

---

## 8. HTTP Module (Raw Server)

### Creating an HTTP Server

```javascript
const http = require("http");
const url  = require("url");

// ─── Basic server ─────────────────────────────────────────
const server = http.createServer((req, res) => {
  // req = IncomingMessage (readable stream)
  // res = ServerResponse (writable stream)

  console.log(`${req.method} ${req.url}`);

  // Parse URL:
  const parsedUrl = url.parse(req.url, true); // true = parse query string
  const pathname  = parsedUrl.pathname;        // "/users"
  const query     = parsedUrl.query;           // { page: "1" }

  // Set response headers:
  res.setHeader("Content-Type", "application/json");
  res.setHeader("X-Powered-By", "Node.js");

  // Route handling:
  if (req.method === "GET" && pathname === "/") {
    res.writeHead(200);
    res.end(JSON.stringify({ message: "Hello, World!" }));

  } else if (req.method === "GET" && pathname === "/users") {
    res.writeHead(200);
    res.end(JSON.stringify({ users: [{ id: 1, name: "Alice" }] }));

  } else if (req.method === "POST" && pathname === "/users") {
    let body = "";

    // Read request body chunks:
    req.on("data", chunk => {
      body += chunk.toString();

      // Prevent too-large payloads:
      if (body.length > 1e6) {
        req.destroy();
        res.writeHead(413, { "Content-Type": "text/plain" });
        res.end("Payload Too Large");
      }
    });

    req.on("end", () => {
      try {
        const data = JSON.parse(body);
        res.writeHead(201);
        res.end(JSON.stringify({ created: true, user: data }));
      } catch {
        res.writeHead(400);
        res.end(JSON.stringify({ error: "Invalid JSON" }));
      }
    });

  } else {
    res.writeHead(404);
    res.end(JSON.stringify({ error: "Not found" }));
  }
});

// Start listening:
server.listen(3000, "0.0.0.0", () => {
  console.log("Server running at http://localhost:3000");
});

// Handle errors:
server.on("error", (err) => {
  if (err.code === "EADDRINUSE") {
    console.error("Port 3000 is already in use");
  } else {
    console.error("Server error:", err);
  }
});

// Graceful shutdown:
process.on("SIGTERM", () => {
  console.log("Shutting down gracefully...");
  server.close(() => {
    console.log("Server closed");
    process.exit(0);
  });
});
```

### HTTP Client (Making Requests)

```javascript
const http  = require("http");
const https = require("https");

// ─── Simple GET request ───────────────────────────────────
function httpGet(url) {
  return new Promise((resolve, reject) => {
    const protocol = url.startsWith("https") ? https : http;

    const req = protocol.get(url, (res) => {
      let data = "";

      res.on("data", chunk => { data += chunk; });
      res.on("end",  ()    => resolve({ status: res.statusCode, data }));
    });

    req.on("error", reject);
    req.setTimeout(5000, () => {
      req.destroy(new Error("Request timed out"));
    });
  });
}

// ─── POST request ─────────────────────────────────────────
function httpPost(hostname, path, body) {
  const bodyStr = JSON.stringify(body);

  return new Promise((resolve, reject) => {
    const req = https.request(
      {
        hostname,
        path,
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "Content-Length": Buffer.byteLength(bodyStr)
        }
      },
      (res) => {
        let data = "";
        res.on("data",  chunk => { data += chunk; });
        res.on("end",   ()    => resolve(JSON.parse(data)));
        res.on("error", reject);
      }
    );

    req.on("error", reject);
    req.write(bodyStr);
    req.end();
  });
}

// ─── Using fetch (Node 18+) ───────────────────────────────
// Native fetch is available in Node 18+ without any libraries:
const response = await fetch("https://jsonplaceholder.typicode.com/users");
const users    = await response.json();
console.log(users.length, "users found");

// POST with fetch:
const res = await fetch("https://api.example.com/users", {
  method: "POST",
  headers: { "Content-Type": "application/json", "Authorization": "Bearer token" },
  body: JSON.stringify({ name: "Alice", email: "alice@example.com" })
});
if (!res.ok) throw new Error(`HTTP ${res.status}`);
const created = await res.json();
```

---

## 9. Streams & Buffers

### Understanding Buffers

```javascript
// Buffer — fixed-size chunk of raw binary data
// Lives outside V8 heap — for handling binary data efficiently

// ─── Creating Buffers ─────────────────────────────────────

// From string:
const buf1 = Buffer.from("Hello, World!", "utf8");
const buf2 = Buffer.from("SGVsbG8=", "base64");

// From array:
const buf3 = Buffer.from([72, 101, 108, 108, 111]); // ASCII codes for "Hello"

// Allocate uninitialized (fast but contains garbage data):
const buf4 = Buffer.allocUnsafe(256);

// Allocate zero-filled (safe):
const buf5 = Buffer.alloc(256);       // 256 zero bytes
const buf6 = Buffer.alloc(256, 0xff); // 256 bytes of 0xff

// ─── Reading Buffers ──────────────────────────────────────
console.log(buf1.toString("utf8"));   // "Hello, World!"
console.log(buf1.toString("hex"));    // "48656c6c6f..."
console.log(buf1.toString("base64")); // "SGVsbG8sIFdvcmxkIQ=="

// Access individual bytes:
console.log(buf1[0]);                 // 72 (ASCII for 'H')
console.log(buf1.length);            // 13

// Slice buffer (returns a view, not copy):
const slice = buf1.slice(0, 5);      // "Hello"

// ─── Writing to Buffer ────────────────────────────────────
const writeBuf = Buffer.alloc(4);
writeBuf.writeUInt32BE(12345678, 0); // write 32-bit int at offset 0
console.log(writeBuf.readUInt32BE(0)); // 12345678

// ─── Concatenate buffers ──────────────────────────────────
const combined = Buffer.concat([buf1, buf2]);

// ─── Buffer comparison ────────────────────────────────────
Buffer.compare(buf1, buf2); // -1, 0, or 1
buf1.equals(Buffer.from("Hello, World!")); // true

// ─── Convert between Buffer and other types ───────────────
// Buffer → JSON:
console.log(JSON.stringify(buf1)); // {"type":"Buffer","data":[72,101,...]}

// String → Base64:
const base64 = Buffer.from("binary data").toString("base64");

// Base64 → String:
const decoded = Buffer.from(base64, "base64").toString("utf8");
```

### Streams

```javascript
const fs     = require("fs");
const zlib   = require("zlib");
const { Transform, Writable, Readable, pipeline } = require("stream");
const { promisify } = require("util");
const pipelineAsync = promisify(pipeline);

// ─── Types of Streams ─────────────────────────────────────
// Readable  — data comes OUT (fs.createReadStream, http request)
// Writable  — data goes IN  (fs.createWriteStream, http response)
// Duplex    — both readable AND writable (TCP socket, net.Socket)
// Transform — duplex that transforms data (zlib, crypto cipher)

// ─── Readable Stream ──────────────────────────────────────
const readable = fs.createReadStream("large-file.txt", {
  encoding:  "utf8",
  highWaterMark: 64 * 1024 // chunk size: 64KB (default 16KB)
});

// Event-based consumption:
readable.on("data",  (chunk) => console.log("Got chunk:", chunk.length, "chars"));
readable.on("end",   ()      => console.log("Done reading"));
readable.on("error", (err)   => console.error("Read error:", err));

// ─── Writable Stream ──────────────────────────────────────
const writable = fs.createWriteStream("output.txt");

writable.write("Hello,");
writable.write(" World!\n");
writable.end("Goodbye!\n");  // flush and close

writable.on("finish", () => console.log("Write complete"));
writable.on("error",  (err) => console.error("Write error:", err));

// ─── Piping (connect readable → writable) ─────────────────
// Efficiently copy file (no memory issue — chunks flow through):
fs.createReadStream("source.txt")
  .pipe(fs.createWriteStream("destination.txt"))
  .on("finish", () => console.log("Copy complete"));

// Multi-pipe (transform in between):
// Compress file: read → gzip → write
fs.createReadStream("file.txt")
  .pipe(zlib.createGzip())                           // transform
  .pipe(fs.createWriteStream("file.txt.gz"))         // write
  .on("finish", () => console.log("Compressed!"));

// ─── Pipeline (better than pipe — handles errors) ─────────
// Using promisified pipeline:
async function compressFile(input, output) {
  await pipelineAsync(
    fs.createReadStream(input),
    zlib.createGzip(),
    fs.createWriteStream(output)
  );
  console.log("Compression complete");
}

// ─── Custom Transform Stream ──────────────────────────────
class UpperCaseTransform extends Transform {
  _transform(chunk, encoding, callback) {
    // Transform the chunk:
    this.push(chunk.toString().toUpperCase());
    callback(); // signal done
  }
}

class LineCounter extends Transform {
  constructor() {
    super();
    this.count = 0;
  }
  _transform(chunk, encoding, callback) {
    this.count += (chunk.toString().match(/\n/g) || []).length;
    this.push(chunk); // pass through unchanged
    callback();
  }
  _flush(callback) {
    console.log(`Total lines: ${this.count}`);
    callback();
  }
}

// Use transform in pipeline:
await pipelineAsync(
  fs.createReadStream("input.txt"),
  new LineCounter(),
  new UpperCaseTransform(),
  fs.createWriteStream("upper-output.txt")
);

// ─── Custom Readable Stream ────────────────────────────────
class CounterStream extends Readable {
  constructor(options) {
    super({ ...options, objectMode: true });
    this.current = 0;
    this.max     = options?.max ?? 10;
  }

  _read() {
    if (this.current >= this.max) {
      this.push(null); // signal end of stream
    } else {
      this.push(this.current++);
    }
  }
}

const counter = new CounterStream({ max: 5 });
for await (const num of counter) {
  console.log(num); // 0, 1, 2, 3, 4
}

// ─── Backpressure ─────────────────────────────────────────
// Backpressure = slow writer signals fast reader to pause

const writeStream = fs.createWriteStream("output.txt");
const readStream  = fs.createReadStream("large-file.txt");

readStream.on("data", (chunk) => {
  const canContinue = writeStream.write(chunk);
  if (!canContinue) {
    readStream.pause();                           // pause reading!
    writeStream.once("drain", () => {
      readStream.resume();                        // resume when writer drains
    });
  }
});
// Note: pipe() handles backpressure automatically!
```

---

## 10. Events & EventEmitter

### EventEmitter Basics

```javascript
const EventEmitter = require("events");

// ─── Basic usage ──────────────────────────────────────────
const emitter = new EventEmitter();

// Register listener:
emitter.on("message", (text, from) => {
  console.log(`[${from}]: ${text}`);
});

// Register one-time listener:
emitter.once("connect", () => {
  console.log("Connected! (fires only once)");
});

// Emit events:
emitter.emit("message", "Hello!", "Alice");  // fires handler
emitter.emit("connect");                      // fires once, then removes
emitter.emit("connect");                      // does nothing — already removed

// Remove listener:
function handleData(data) {
  console.log("Data:", data);
}
emitter.on("data", handleData);
emitter.removeListener("data", handleData);   // same as .off()
emitter.off("data", handleData);              // alias

// Remove all listeners:
emitter.removeAllListeners("data");           // for specific event
emitter.removeAllListeners();                 // all events

// ─── Listener management ──────────────────────────────────
emitter.listenerCount("message");             // number of listeners
emitter.listeners("message");                 // array of listener functions
emitter.eventNames();                         // all event names

// Default max listeners (15) — increase if needed:
emitter.setMaxListeners(50);
EventEmitter.defaultMaxListeners = 20;        // global default

// ─── Prepend listener (fires FIRST) ───────────────────────
emitter.prependListener("data", (d) => console.log("First:", d));
emitter.prependOnceListener("data", (d) => console.log("Once First:", d));
```

### Building a Custom Event-Driven Class

```javascript
const EventEmitter = require("events");
const fsP          = require("fs/promises");

class FileWatcher extends EventEmitter {
  #interval = null;
  #watching = new Map();

  constructor(options = {}) {
    super();
    this.pollInterval = options.pollInterval ?? 1000;
  }

  async watch(filePath) {
    try {
      const stats = await fsP.stat(filePath);
      this.#watching.set(filePath, stats.mtime.getTime());
      this.emit("watching", filePath);
    } catch (err) {
      this.emit("error", new Error(`Cannot watch ${filePath}: ${err.message}`));
      return;
    }

    if (!this.#interval) {
      this.#interval = setInterval(() => this.#poll(), this.pollInterval);
    }
  }

  async #poll() {
    for (const [filePath, lastMtime] of this.#watching) {
      try {
        const stats   = await fsP.stat(filePath);
        const newMtime = stats.mtime.getTime();

        if (newMtime !== lastMtime) {
          this.#watching.set(filePath, newMtime);
          this.emit("change", filePath, stats);
        }
      } catch {
        this.#watching.delete(filePath);
        this.emit("unlink", filePath);
      }
    }
  }

  unwatch(filePath) {
    this.#watching.delete(filePath);
    if (this.#watching.size === 0) {
      clearInterval(this.#interval);
      this.#interval = null;
    }
  }

  close() {
    clearInterval(this.#interval);
    this.#watching.clear();
    this.emit("close");
    this.removeAllListeners();
  }
}

// Usage:
const watcher = new FileWatcher({ pollInterval: 500 });

watcher.on("watching", (path) => console.log(`Watching: ${path}`));
watcher.on("change",   (path, stats) => console.log(`Changed: ${path} at ${stats.mtime}`));
watcher.on("unlink",   (path) => console.log(`Deleted: ${path}`));
watcher.on("error",    (err)  => console.error(`Error: ${err.message}`));

await watcher.watch("./config.json");
await watcher.watch("./app.js");
```

### Error Events

```javascript
const EventEmitter = require("events");

const emitter = new EventEmitter();

// Special 'error' event — MUST be handled or Node throws!
emitter.on("error", (err) => {
  console.error("Handled error:", err.message);
});

// If no 'error' listener, this CRASHES the process:
emitter.emit("error", new Error("Something went wrong"));

// Capture error event listener:
process.on("uncaughtException", (err) => {
  console.error("Uncaught Exception:", err);
  process.exit(1); // must exit — process may be in unstable state
});

process.on("unhandledRejection", (reason, promise) => {
  console.error("Unhandled Promise Rejection:", reason);
  // Handle the rejection here or exit
});

// EventEmitter with async error handling:
class AsyncEmitter extends EventEmitter {
  async asyncEmit(event, ...args) {
    const listeners = this.listeners(event);
    for (const listener of listeners) {
      try {
        await listener(...args);
      } catch (err) {
        this.emit("error", err);
      }
    }
  }
}
```

---

## 11. Child Processes

```javascript
const { exec, execFile, spawn, fork } = require("child_process");
const { promisify } = require("util");
const execAsync     = promisify(exec);

// ─── exec — run shell command, buffer output ───────────────
// Good for simple commands with small output:
exec("ls -la", (err, stdout, stderr) => {
  if (err) { console.error("Error:", err); return; }
  console.log("Output:", stdout);
  if (stderr) console.error("Stderr:", stderr);
});

// Async version:
const { stdout, stderr } = await execAsync("git log --oneline -10");
console.log(stdout);

// ─── execFile — run executable file directly (safer) ──────
execFile("node", ["--version"], (err, stdout) => {
  console.log("Node version:", stdout.trim());
});

// ─── spawn — run command, stream output (large output) ────
// Good for long-running processes:
const ls = spawn("ls", ["-la", "/usr/bin"], {
  cwd:   "/",          // working directory
  env:   process.env,  // environment variables
  shell: false,        // don't use shell (safer)
  stdio: "pipe"        // pipe stdin/stdout/stderr
});

ls.stdout.on("data", (data) => process.stdout.write(data));
ls.stderr.on("data", (data) => process.stderr.write(data));
ls.on("close", (code) => console.log(`Exited with code ${code}`));
ls.on("error", (err)  => console.error("Failed to start:", err));

// Real example — run a Python script:
const python = spawn("python3", ["script.py", "--arg", "value"]);
python.stdout.setEncoding("utf8");
python.stdout.on("data", (data) => console.log("Python output:", data));

// ─── fork — spawn another Node.js process ─────────────────
// Has built-in IPC (Inter-Process Communication) channel

// parent.js:
const child = fork("./worker.js", [], {
  silent: false, // false = share parent's stdio
  env: { ...process.env, WORKER_ID: "1" }
});

// Send message to child:
child.send({ type: "START", data: [1, 2, 3, 4, 5] });

// Receive messages from child:
child.on("message", (msg) => {
  console.log("Result from child:", msg);
});

child.on("exit", (code) => {
  console.log("Child process exited with code:", code);
});

// worker.js:
process.on("message", (msg) => {
  console.log("Worker received:", msg);
  const result = msg.data.reduce((a, b) => a + b, 0);
  process.send({ type: "RESULT", result });
  process.exit(0);
});

// ─── Killing processes ────────────────────────────────────
const proc = spawn("long-running-script.sh");
setTimeout(() => {
  proc.kill("SIGTERM");  // graceful shutdown signal
  // proc.kill("SIGKILL"); // force kill
}, 5000);
```

---

## 12. Worker Threads

```javascript
const { Worker, isMainThread, parentPort, workerData, threadId } = require("worker_threads");
const os = require("os");

// ─── Why Worker Threads? ──────────────────────────────────
// Child processes: separate memory, heavy, for CPU isolation
// Worker Threads:  shared memory possible, lightweight, for CPU work

// ─── main.js ──────────────────────────────────────────────
if (isMainThread) {
  // Create a thread pool:
  const numThreads = os.cpus().length;
  const tasks      = [1000000, 2000000, 3000000, 4000000];
  const results    = [];

  const workerPromise = (data) => new Promise((resolve, reject) => {
    const worker = new Worker(__filename, {
      workerData: { limit: data }
    });

    worker.on("message",  resolve);
    worker.on("error",    reject);
    worker.on("exit", (code) => {
      if (code !== 0) reject(new Error(`Worker exited with code ${code}`));
    });
  });

  // Run all tasks in parallel:
  const start   = Date.now();
  const outputs = await Promise.all(tasks.map(workerPromise));
  console.log(`All done in ${Date.now() - start}ms:`, outputs);

} else {
  // Worker thread code:
  const { limit } = workerData;

  // CPU-intensive computation:
  let sum = 0;
  for (let i = 0; i < limit; i++) {
    sum += Math.sqrt(i);
  }

  // Send result back to main thread:
  parentPort.postMessage({
    threadId,
    result: sum.toFixed(2),
    limit
  });
}

// ─── Shared Memory with SharedArrayBuffer ─────────────────
// Main thread:
const sharedBuffer = new SharedArrayBuffer(Int32Array.BYTES_PER_ELEMENT * 10);
const sharedArray  = new Int32Array(sharedBuffer);

const worker2 = new Worker("./worker.js", {
  workerData: { sharedBuffer }
});

// Both main thread and worker can read/write sharedArray
// Use Atomics for thread-safe operations:
Atomics.store(sharedArray, 0, 42);      // atomic write
Atomics.add(sharedArray, 1, 1);         // atomic increment
Atomics.load(sharedArray, 0);           // atomic read
Atomics.wait(sharedArray, 0, 42);       // wait until value changes
Atomics.notify(sharedArray, 0, 1);      // wake up waiting threads

// ─── Message Channel ──────────────────────────────────────
const { MessageChannel } = require("worker_threads");
const { port1, port2 }   = new MessageChannel();

port1.on("message", (msg) => console.log("Port1 received:", msg));
port2.postMessage({ hello: "world" }); // sends to port1
```

---

## 13. Timers & Scheduling

```javascript
// ─── setTimeout — run once after delay ───────────────────
const id1 = setTimeout(() => {
  console.log("Runs after 2 seconds");
}, 2000);

// Cancel before it fires:
clearTimeout(id1);

// ─── setInterval — run repeatedly ─────────────────────────
let count = 0;
const id2 = setInterval(() => {
  count++;
  console.log(`Tick ${count}`);
  if (count >= 5) clearInterval(id2); // stop after 5 ticks
}, 1000);

// ─── setImmediate — run after I/O callbacks ───────────────
setImmediate(() => {
  console.log("Runs after I/O, before setTimeout");
});

// ─── process.nextTick — run before I/O, before Promises ──
process.nextTick(() => {
  console.log("Runs before ANYTHING else async");
});

// ─── queueMicrotask — run after nextTick ──────────────────
queueMicrotask(() => {
  console.log("Microtask — after nextTick");
});

// ─── Execution order demonstration ────────────────────────
console.log("1 sync");
setTimeout(()    => console.log("6 setTimeout"),  0);
setImmediate(()  => console.log("5 setImmediate"));
process.nextTick(()  => console.log("3 nextTick"));
queueMicrotask(()    => console.log("4 microtask"));
Promise.resolve().then(() => console.log("4.5 promise"));
console.log("2 sync");

// Output: 1 sync → 2 sync → 3 nextTick → 4 microtask → 4.5 promise → 5 setImmediate → 6 setTimeout

// ─── Reliable interval with drift correction ──────────────
function setIntervalAccurate(fn, interval) {
  let startTime  = Date.now();
  let expected   = startTime + interval;

  function tick() {
    fn();
    const drift = Date.now() - expected;
    expected   += interval;
    setTimeout(tick, Math.max(0, interval - drift));
  }

  return setTimeout(tick, interval);
}

// ─── Debounce — delay execution until calls stop ──────────
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}

// ─── Throttle — limit to one execution per interval ───────
function throttle(fn, interval) {
  let lastRun = 0;
  return function (...args) {
    const now = Date.now();
    if (now - lastRun >= interval) {
      lastRun = now;
      return fn.apply(this, args);
    }
  };
}

// ─── sleep / delay utility ────────────────────────────────
const sleep = (ms) => new Promise(resolve => setTimeout(resolve, ms));

async function main() {
  console.log("Start");
  await sleep(2000); // wait 2 seconds
  console.log("After 2s delay");
}
```

---

## 14. Process Object

```javascript
// process is a global object — no require() needed

// ─── Process information ───────────────────────────────────
console.log(process.version);      // "v20.x.x"
console.log(process.versions);     // versions of node, v8, uv, etc.
console.log(process.platform);     // "linux", "darwin", "win32"
console.log(process.arch);         // "x64", "arm64"
console.log(process.pid);          // process ID number
console.log(process.ppid);         // parent process ID
console.log(process.title);        // process name
console.log(process.execPath);     // path to node executable
console.log(process.argv);         // command-line arguments
console.log(process.env);          // environment variables (object)
console.log(process.cwd());        // current working directory
console.log(process.uptime());     // seconds since process started
console.log(process.memoryUsage()); // { rss, heapTotal, heapUsed, external }

// ─── Command-line arguments ────────────────────────────────
// node app.js --port 3000 --debug
//   process.argv[0] = "/usr/bin/node"
//   process.argv[1] = "/app/app.js"
//   process.argv[2] = "--port"
//   process.argv[3] = "3000"
//   process.argv[4] = "--debug"

function parseArgs() {
  const args = {};
  const argv = process.argv.slice(2);
  for (let i = 0; i < argv.length; i++) {
    if (argv[i].startsWith("--")) {
      const key = argv[i].slice(2);
      args[key] = argv[i + 1]?.startsWith("--") ? true : argv[++i] ?? true;
    }
  }
  return args;
}

const { port = 3000, debug = false } = parseArgs();

// ─── Environment variables ────────────────────────────────
process.env.NODE_ENV === "production"; // check environment
process.env.PORT = "3000";             // set (string only!)

// ─── Standard I/O streams ────────────────────────────────
process.stdin.setEncoding("utf8");
process.stdin.on("data", (data) => {
  process.stdout.write(`Echo: ${data}`);
});
process.stderr.write("Error message\n");

// ─── Exit ─────────────────────────────────────────────────
process.exit(0);   // success
process.exit(1);   // error / failure

// ─── Signals & Graceful Shutdown ──────────────────────────
let server; // your HTTP server

process.on("SIGTERM", async () => {
  console.log("SIGTERM received — shutting down gracefully...");
  server.close(async () => {
    // Close database connections:
    await db.disconnect();
    // Clear intervals:
    clearInterval(healthCheckInterval);
    console.log("Cleanup complete — exiting");
    process.exit(0);
  });
  // Force exit if graceful shutdown takes too long:
  setTimeout(() => {
    console.error("Forced exit after 10s");
    process.exit(1);
  }, 10000);
});

process.on("SIGINT",  () => process.emit("SIGTERM")); // Ctrl+C
process.on("SIGHUP",  () => console.log("Reload config"));

// ─── Uncaught Errors ──────────────────────────────────────
process.on("uncaughtException", (err, origin) => {
  console.error(`Uncaught Exception at: ${origin}`);
  console.error(err);
  // MUST restart — process may be in unstable state:
  process.exit(1);
});

process.on("unhandledRejection", (reason, promise) => {
  console.error("Unhandled Promise Rejection:", reason);
  // In Node 15+, this crashes the process by default
});

// ─── Memory usage monitoring ──────────────────────────────
setInterval(() => {
  const mem = process.memoryUsage();
  console.log({
    rss:      `${(mem.rss / 1024 ** 2).toFixed(1)} MB`,   // Resident Set Size
    heapTotal:`${(mem.heapTotal / 1024 ** 2).toFixed(1)} MB`,
    heapUsed: `${(mem.heapUsed / 1024 ** 2).toFixed(1)} MB`,
    external: `${(mem.external / 1024 ** 2).toFixed(1)} MB`
  });
}, 30000);
```

---

## 15. npm & Package Management

```bash
# ─── Initialize project ───────────────────────────────────
npm init            # interactive setup
npm init -y         # accept all defaults

# ─── Install packages ─────────────────────────────────────
npm install express                     # production dependency
npm install --save-dev nodemon jest     # dev dependency (short: -D)
npm install -g typescript               # global install

npm install express@4.18.0             # specific version
npm install express@latest             # latest version
npm install github:expressjs/express   # from GitHub

# ─── Uninstall ────────────────────────────────────────────
npm uninstall express
npm uninstall -g typescript

# ─── Update packages ──────────────────────────────────────
npm update                  # update all within semver range
npm update express          # update specific package
npm outdated               # show outdated packages

# ─── Scripts ──────────────────────────────────────────────
# Run scripts defined in package.json:
npm run start
npm run build
npm run test
npm start       # shortcut for npm run start
npm test        # shortcut for npm run test

# ─── Package inspection ───────────────────────────────────
npm list                    # local packages
npm list -g                 # global packages
npm info express            # package information
npm docs express            # open docs in browser

# ─── Security ─────────────────────────────────────────────
npm audit               # check for vulnerabilities
npm audit fix           # auto-fix vulnerabilities
npm audit fix --force   # force major version updates
```

### package.json in Detail

```json
{
  "name": "my-node-app",
  "version": "1.0.0",
  "description": "A Node.js application",
  "author": "Alice <alice@example.com>",
  "license": "MIT",
  "private": true,

  "main": "src/index.js",      // entry point for CJS
  "module": "src/index.mjs",   // entry for ESM bundlers
  "exports": {                  // modern export maps (Node 12+)
    ".": "./src/index.js",
    "./utils": "./src/utils.js"
  },
  "type": "module",             // "module" for ESM, "commonjs" (default) for CJS

  "scripts": {
    "start":       "node src/index.js",
    "dev":         "nodemon src/index.js",
    "build":       "tsc",
    "test":        "jest --coverage",
    "test:watch":  "jest --watch",
    "lint":        "eslint src/**/*.js",
    "format":      "prettier --write .",
    "prestart":    "npm run build",       // runs before 'start'
    "postinstall": "node scripts/setup.js" // runs after npm install
  },

  "dependencies": {
    "express":       "^4.18.0",    // ^= compatible with 4.x.x
    "mongoose":      "~7.0.0",     // ~= compatible with 7.0.x
    "dotenv":        "16.0.0",     // exact version
    "jsonwebtoken":  ">=9.0.0",    // >= any version 9+
    "bcryptjs":      "*"           // * = any version (avoid!)
  },

  "devDependencies": {
    "nodemon":  "^3.0.0",
    "jest":     "^29.0.0",
    "eslint":   "^8.0.0",
    "prettier": "^3.0.0"
  },

  "engines": {
    "node": ">=18.0.0",     // required Node.js version
    "npm":  ">=9.0.0"
  },

  "keywords": ["node", "express", "api"],

  "repository": {
    "type": "git",
    "url": "https://github.com/user/repo.git"
  }
}
```

### npx & Other Tools

```bash
# npx — run packages without installing globally:
npx create-react-app my-app
npx eslint --init
npx prettier --write .

# npm workspaces (monorepo):
# package.json:
# "workspaces": ["packages/*"]
npm install --workspace=packages/api lodash

# pnpm — faster alternative to npm:
pnpm install
pnpm add express
pnpm dlx create-react-app my-app   # like npx

# Yarn:
yarn install
yarn add express
yarn add -D nodemon
```

---

## 16. Environment Variables

### Using dotenv

```bash
# Install dotenv:
npm install dotenv
```

```bash
# .env file — NEVER commit this to git!
NODE_ENV=development
PORT=3000
DATABASE_URL=mongodb://localhost:27017/myapp
JWT_SECRET=super-secret-key-change-this-in-production
REDIS_URL=redis://localhost:6379
API_KEY=abc123xyz

# .env.example — DO commit this (template with no real values):
NODE_ENV=development
PORT=3000
DATABASE_URL=
JWT_SECRET=
REDIS_URL=
API_KEY=
```

```javascript
// Load environment variables — do this FIRST, before any other code:
require("dotenv").config();
// Or for ESM:
import "dotenv/config";

// With custom path:
require("dotenv").config({ path: "./.env.production" });

// ─── config.js — centralized configuration ───────────────
const config = {
  env:      process.env.NODE_ENV || "development",
  port:     parseInt(process.env.PORT || "3000", 10),
  isProduction: process.env.NODE_ENV === "production",
  isDevelopment: process.env.NODE_ENV === "development",

  database: {
    url:      process.env.DATABASE_URL || "mongodb://localhost:27017/myapp",
    options: {
      maxPoolSize: parseInt(process.env.DB_POOL_SIZE || "10", 10)
    }
  },

  jwt: {
    secret:    process.env.JWT_SECRET,
    expiresIn: process.env.JWT_EXPIRES_IN || "7d"
  },

  redis: {
    url: process.env.REDIS_URL
  },

  // Validate required variables:
  validate() {
    const required = ["DATABASE_URL", "JWT_SECRET"];
    const missing  = required.filter(key => !process.env[key]);
    if (missing.length > 0) {
      throw new Error(`Missing required environment variables: ${missing.join(", ")}`);
    }
    return this;
  }
};

// Validate on startup:
config.validate();

module.exports = config;
```

---

## 17. Error Handling in Node.js

### Sync vs Async Errors

```javascript
// ─── Synchronous errors — use try/catch ───────────────────
try {
  JSON.parse("invalid json");
  // or
  throw new Error("Something went wrong");
} catch (err) {
  console.error("Caught:", err.message);
} finally {
  console.log("Always runs");
}

// ─── Async errors with callbacks ──────────────────────────
const fs = require("fs");

fs.readFile("nonexistent.txt", (err, data) => {
  if (err) {
    // err.code   = "ENOENT" (error code)
    // err.path   = "nonexistent.txt"
    // err.message = "ENOENT: no such file..."
    console.error("Error:", err.code, err.message);
    return;
  }
  console.log(data);
});

// ─── Async/Await errors ───────────────────────────────────
async function fetchData() {
  try {
    const response = await fetch("https://api.example.com/data");
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    return await response.json();
  } catch (err) {
    if (err.code === "ECONNREFUSED") {
      console.error("Cannot connect to server");
    } else if (err.code === "ETIMEDOUT") {
      console.error("Request timed out");
    } else {
      throw err; // re-throw unknown errors
    }
  }
}

// ─── Custom Error Classes ─────────────────────────────────
class AppError extends Error {
  constructor(message, statusCode = 500, code = "INTERNAL_ERROR") {
    super(message);
    this.name       = this.constructor.name;
    this.statusCode = statusCode;
    this.code       = code;
    this.isOperational = true; // vs programming errors
    Error.captureStackTrace(this, this.constructor);
  }
}

class ValidationError extends AppError {
  constructor(message, fields = {}) {
    super(message, 400, "VALIDATION_ERROR");
    this.fields = fields;
  }
}

class NotFoundError extends AppError {
  constructor(resource, id) {
    super(`${resource} with id '${id}' not found`, 404, "NOT_FOUND");
    this.resource = resource;
  }
}

class UnauthorizedError extends AppError {
  constructor(msg = "Authentication required") {
    super(msg, 401, "UNAUTHORIZED");
  }
}

class ForbiddenError extends AppError {
  constructor(msg = "Access denied") {
    super(msg, 403, "FORBIDDEN");
  }
}

// ─── Global error handlers ────────────────────────────────
process.on("uncaughtException", (err) => {
  console.error("UNCAUGHT EXCEPTION:", err);
  // Log, notify, then exit:
  process.exit(1);
});

process.on("unhandledRejection", (reason) => {
  console.error("UNHANDLED REJECTION:", reason);
  // In Node 15+, crashes by default. Recommended: exit
  process.exit(1);
});
```

---

## 18. Debugging Node.js

```bash
# ─── Built-in debugger ────────────────────────────────────
node inspect app.js               # start debug session
# Commands: n (next), s (step in), o (step out), c (continue), repl

# ─── Chrome DevTools (recommended) ───────────────────────
node --inspect app.js             # start with inspector
node --inspect-brk app.js         # pause at first line
# Then open: chrome://inspect in Chrome

# ─── VS Code debugging ────────────────────────────────────
# .vscode/launch.json:
{
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Node App",
      "program": "${workspaceFolder}/src/index.js",
      "envFile": "${workspaceFolder}/.env",
      "runtimeArgs": ["--inspect"]
    }
  ]
}

# ─── Watch mode (Node 18+) ────────────────────────────────
node --watch app.js               # restart on file change
node --watch-path=src app.js      # watch specific directory
```

```javascript
// ─── Console debugging ────────────────────────────────────
console.log("Basic log");
console.info("Info message");
console.warn("Warning!");
console.error("Error!", new Error("stack trace"));
console.debug("Debug info");     // only shown with --inspect

// Pretty-print objects:
console.log("User:", JSON.stringify(user, null, 2));
console.dir(user, { depth: null, colors: true });
console.table([{ name: "Alice", age: 30 }, { name: "Bob", age: 25 }]);

// Timing:
console.time("db-query");
await db.query("SELECT * FROM users");
console.timeEnd("db-query");   // "db-query: 45.3ms"
console.timeLog("db-query");   // log without ending

// Count:
function route() {
  console.count("request");   // "request: 1", "request: 2", ...
}
console.countReset("request");

// Stack trace:
console.trace("Where was this called?");

// Groups:
console.group("Database");
console.log("Query executed");
console.log("Result: 42 rows");
console.groupEnd();

// ─── util.inspect ────────────────────────────────────────
const util = require("util");
const deep = { a: { b: { c: { d: "deep!" } } } };
console.log(util.inspect(deep, { depth: null, colors: true, compact: false }));

// ─── Debugging async issues ───────────────────────────────
// AsyncLocalStorage — trace async operations:
const { AsyncLocalStorage } = require("async_hooks");
const requestStore = new AsyncLocalStorage();

// In request handler:
requestStore.run({ requestId: crypto.randomUUID() }, async () => {
  await doAsyncWork();    // any async work in this context
  const store = requestStore.getStore();
  console.log("Request ID:", store.requestId); // still accessible!
});
```

---

## 19. Introduction to Express.js

### What is Express?

Express.js is a **minimal, fast, and unopinionated web framework for Node.js**. It provides a thin layer of fundamental web application features without obscuring Node.js features.

```bash
# Create project and install Express:
mkdir my-api && cd my-api
npm init -y
npm install express
npm install --save-dev nodemon
```

### Minimal Express Server

```javascript
// app.js
const express = require("express");
const app     = express();
const PORT    = process.env.PORT || 3000;

// Built-in middleware to parse JSON request bodies:
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// A simple route:
app.get("/", (req, res) => {
  res.json({ message: "Hello from Express!" });
});

// Start the server:
const server = app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});

// Export for testing:
module.exports = { app, server };
```

### Express Application Object

```javascript
const express = require("express");
const app     = express();

// ─── Application settings ────────────────────────────────
app.set("view engine", "ejs");           // set template engine
app.set("views", "./views");             // directory for templates
app.set("trust proxy", 1);              // trust first proxy (for HTTPS behind nginx)
app.set("x-powered-by", false);         // hide "X-Powered-By: Express" header
app.set("env", "production");            // environment

// Read a setting:
app.get("env");           // "production"
app.get("view engine");   // "ejs"

// ─── Enable / Disable ────────────────────────────────────
app.enable("trust proxy");
app.disable("x-powered-by");
app.enabled("trust proxy"); // true
app.disabled("x-powered-by"); // true

// ─── Locals — available in all templates ────────────────
app.locals.appName    = "My Express App";
app.locals.version    = "1.0.0";
app.locals.formatDate = (date) => date.toLocaleDateString();
```

---

## 20. Routing

### Basic Routing

```javascript
const express = require("express");
const app     = express();

// HTTP method + path + handler:
// app.METHOD(PATH, HANDLER)

app.get("/",      (req, res) => res.send("GET /"));
app.post("/",     (req, res) => res.send("POST /"));
app.put("/",      (req, res) => res.send("PUT /"));
app.patch("/",    (req, res) => res.send("PATCH /"));
app.delete("/",   (req, res) => res.send("DELETE /"));
app.options("/",  (req, res) => res.send("OPTIONS /"));
app.head("/",     (req, res) => res.end());            // no body

// All HTTP methods:
app.all("/secret", (req, res) => {
  console.log(`${req.method} /secret`);
  res.send("Secret route — any method");
});
```

### Route Parameters

```javascript
// Named parameters — captured in req.params:
app.get("/users/:id", (req, res) => {
  const { id } = req.params;
  res.json({ userId: id });
});
// GET /users/42  →  { userId: "42" }
// GET /users/abc →  { userId: "abc" }

// Multiple parameters:
app.get("/users/:userId/posts/:postId", (req, res) => {
  const { userId, postId } = req.params;
  res.json({ userId, postId });
});
// GET /users/5/posts/99  →  { userId: "5", postId: "99" }

// Optional parameters with ?:
app.get("/users/:id?", (req, res) => {
  const { id } = req.params;
  if (id) {
    res.json({ user: { id } });
  } else {
    res.json({ users: [] });
  }
});

// Wildcard — matches anything:
app.get("/files/*", (req, res) => {
  const filePath = req.params[0];   // everything after /files/
  res.json({ path: filePath });
});
// GET /files/images/logo.png  →  { path: "images/logo.png" }

// Regex in route (use sparingly):
app.get(/\/users\/(\d+)/, (req, res) => {
  // Only matches numeric IDs
  res.json({ id: req.params[0] });
});

// Parameter validation with app.param():
app.param("userId", (req, res, next, id) => {
  // Validate & fetch user for ALL routes with :userId:
  if (!/^\d+$/.test(id)) {
    return res.status(400).json({ error: "userId must be a number" });
  }
  req.userId = parseInt(id, 10);
  // Could also fetch user from DB here:
  // req.user = await User.findById(id);
  next();
});

app.get("/users/:userId",       (req, res) => res.json({ id: req.userId }));
app.get("/users/:userId/posts", (req, res) => res.json({ userId: req.userId }));
// Both routes benefit from the param validation above!
```

### Query Strings

```javascript
// Query params are accessed via req.query:
// GET /search?q=node&page=2&sort=desc&tags=js&tags=ts

app.get("/search", (req, res) => {
  const {
    q     = "",
    page  = "1",
    sort  = "asc",
    tags  = []         // array if multiple values
  } = req.query;

  res.json({
    query:    q,
    page:     parseInt(page, 10),
    sort,
    tags:     Array.isArray(tags) ? tags : [tags]
  });
});

// Filtering with query params:
app.get("/products", async (req, res) => {
  const {
    category,
    minPrice,
    maxPrice,
    inStock,
    page  = "1",
    limit = "20",
    sortBy = "createdAt",
    order  = "desc"
  } = req.query;

  // Build filter object:
  const filter = {};
  if (category)                  filter.category = category;
  if (minPrice || maxPrice) {
    filter.price = {};
    if (minPrice) filter.price.$gte = parseFloat(minPrice);
    if (maxPrice) filter.price.$lte = parseFloat(maxPrice);
  }
  if (inStock !== undefined) filter.inStock = inStock === "true";

  const products = await Product.find(filter)
    .sort({ [sortBy]: order === "desc" ? -1 : 1 })
    .skip((parseInt(page) - 1) * parseInt(limit))
    .limit(parseInt(limit));

  res.json({ products, page: parseInt(page), limit: parseInt(limit) });
});
```

### Route Chaining

```javascript
// Chain multiple handlers on the same route:

// Using .route() for clean chaining:
app.route("/users")
  .get(async (req, res) => {
    const users = await User.find();
    res.json(users);
  })
  .post(async (req, res) => {
    const user = await User.create(req.body);
    res.status(201).json(user);
  });

app.route("/users/:id")
  .get(async (req, res) => {
    const user = await User.findById(req.params.id);
    if (!user) return res.status(404).json({ error: "Not found" });
    res.json(user);
  })
  .put(async (req, res) => {
    const user = await User.findByIdAndUpdate(req.params.id, req.body, { new: true });
    res.json(user);
  })
  .patch(async (req, res) => {
    const user = await User.findByIdAndUpdate(req.params.id, { $set: req.body }, { new: true });
    res.json(user);
  })
  .delete(async (req, res) => {
    await User.findByIdAndDelete(req.params.id);
    res.status(204).end();
  });

// Multiple handlers (middleware chaining) on a route:
const validate     = (req, res, next) => { /* validate */ next(); };
const authenticate = (req, res, next) => { /* auth check */ next(); };
const authorize    = (req, res, next) => { /* role check */ next(); };

app.post("/admin/users",
  authenticate,           // runs first
  authorize,              // runs second
  validate,               // runs third
  async (req, res) => {   // final handler
    const user = await User.create(req.body);
    res.status(201).json(user);
  }
);
```

---

## 21. Middleware

### What is Middleware?

Middleware functions are functions that have access to the `req`, `res`, and `next` objects. They can:
- Execute any code
- Modify `req` and `res` objects
- End the request-response cycle
- Call `next()` to pass control to the next middleware

```
Request → [Middleware 1] → [Middleware 2] → [Route Handler] → Response
               ↓                  ↓                ↓
           next()            next()             res.send()
```

### Types of Middleware

```javascript
const express = require("express");
const app     = express();

// ─── 1. Application-level middleware ─────────────────────
// Applies to ALL routes (no path) or specific paths:

// Logger for every request:
app.use((req, res, next) => {
  const start = Date.now();
  const { method, url, ip } = req;

  // Intercept res.end to capture status code after response:
  const originalEnd = res.end.bind(res);
  res.end = function (...args) {
    const duration = Date.now() - start;
    console.log(`${method} ${url} ${res.statusCode} ${duration}ms [${ip}]`);
    return originalEnd(...args);
  };

  next(); // pass control to next middleware
});

// Only for /api routes:
app.use("/api", (req, res, next) => {
  res.setHeader("X-API-Version", "1.0");
  next();
});

// ─── 2. Router-level middleware ───────────────────────────
const router = express.Router();

router.use((req, res, next) => {
  console.log("Router middleware — runs for all router routes");
  next();
});

// ─── 3. Error-handling middleware ─────────────────────────
// MUST have 4 parameters: (err, req, res, next)
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.statusCode || 500).json({
    error: err.message || "Internal Server Error"
  });
});

// ─── 4. Built-in middleware ───────────────────────────────
app.use(express.json({ limit: "10mb" }));        // parse JSON bodies
app.use(express.urlencoded({ extended: true })); // parse form data
app.use(express.static("public"));               // serve static files
app.use(express.raw({ type: "application/octet-stream" })); // raw buffer

// ─── 5. Third-party middleware ────────────────────────────
const cors        = require("cors");
const helmet      = require("helmet");
const morgan      = require("morgan");
const compression = require("compression");

app.use(cors());
app.use(helmet());
app.use(morgan("combined"));  // HTTP request logger
app.use(compression());       // gzip responses
```

### Writing Custom Middleware

```javascript
// ─── Request ID middleware ────────────────────────────────
const { randomUUID } = require("crypto");

function requestId(req, res, next) {
  req.id = req.headers["x-request-id"] || randomUUID();
  res.setHeader("X-Request-Id", req.id);
  next();
}
app.use(requestId);

// ─── Request logger middleware ────────────────────────────
function logger(options = {}) {
  const { level = "info", format = "combined" } = options;

  return function (req, res, next) {
    const start = Date.now();

    res.on("finish", () => {
      const log = {
        requestId: req.id,
        method:    req.method,
        url:       req.originalUrl,
        status:    res.statusCode,
        duration:  `${Date.now() - start}ms`,
        ip:        req.ip,
        userAgent: req.get("User-Agent")
      };
      console[level](JSON.stringify(log));
    });

    next();
  };
}
app.use(logger({ level: "info" }));

// ─── Rate limiter middleware ──────────────────────────────
function rateLimiter({ windowMs = 60000, max = 100 } = {}) {
  const requests = new Map();

  return function (req, res, next) {
    const key = req.ip;
    const now = Date.now();

    if (!requests.has(key)) {
      requests.set(key, { count: 1, resetAt: now + windowMs });
      return next();
    }

    const record = requests.get(key);

    // Reset window if expired:
    if (now > record.resetAt) {
      record.count   = 1;
      record.resetAt = now + windowMs;
      return next();
    }

    record.count++;

    if (record.count > max) {
      res.setHeader("Retry-After", Math.ceil((record.resetAt - now) / 1000));
      return res.status(429).json({
        error: "Too many requests",
        retryAfter: record.resetAt
      });
    }

    res.setHeader("X-RateLimit-Limit",     max);
    res.setHeader("X-RateLimit-Remaining", max - record.count);
    next();
  };
}
app.use("/api", rateLimiter({ windowMs: 60000, max: 100 }));

// ─── Auth middleware ──────────────────────────────────────
const jwt = require("jsonwebtoken");

async function authenticate(req, res, next) {
  try {
    // Get token from header or cookie:
    const authHeader = req.headers["authorization"];
    const token      = authHeader?.startsWith("Bearer ")
      ? authHeader.slice(7)
      : req.cookies?.token;

    if (!token) {
      return res.status(401).json({ error: "No token provided" });
    }

    // Verify token:
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user      = decoded;
    next();
  } catch (err) {
    if (err.name === "TokenExpiredError") {
      return res.status(401).json({ error: "Token expired" });
    }
    return res.status(401).json({ error: "Invalid token" });
  }
}

// ─── Role authorization middleware ───────────────────────
function authorize(...roles) {
  return function (req, res, next) {
    if (!req.user) {
      return res.status(401).json({ error: "Not authenticated" });
    }
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({
        error: `Access denied. Required roles: ${roles.join(", ")}`
      });
    }
    next();
  };
}

// Usage:
app.get("/admin/dashboard", authenticate, authorize("admin"), (req, res) => {
  res.json({ data: "Admin only data" });
});

// ─── Async middleware wrapper ─────────────────────────────
// Wrap async handlers to catch errors automatically:
function asyncHandler(fn) {
  return function (req, res, next) {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
}

// Without wrapper — must try/catch every handler:
app.get("/users/:id", async (req, res, next) => {
  try {
    const user = await User.findById(req.params.id);
    res.json(user);
  } catch (err) {
    next(err); // pass to error handler
  }
});

// With wrapper — cleaner:
app.get("/users/:id", asyncHandler(async (req, res) => {
  const user = await User.findById(req.params.id);
  if (!user) throw new NotFoundError("User", req.params.id);
  res.json(user);
}));

// ─── Validate request body middleware ─────────────────────
function validateBody(schema) {
  return function (req, res, next) {
    const { error, value } = schema.validate(req.body, { abortEarly: false });
    if (error) {
      const fields = {};
      error.details.forEach(d => {
        const key = d.path.join(".");
        fields[key] = d.message;
      });
      return res.status(400).json({ error: "Validation failed", fields });
    }
    req.body = value; // use validated & sanitized value
    next();
  };
}
```

### Middleware Order Matters

```javascript
// Middleware executes in the ORDER it is defined!

app.use(express.json());          // 1st — parse body
app.use(requestId);               // 2nd — add request ID
app.use(logger());                // 3rd — log request
app.use(authenticate);            // 4th — auth check

app.get("/users", getUsers);      // 5th — route handler

// After routes — error handler:
app.use(notFoundHandler);         // 404 handler
app.use(errorHandler);            // global error handler

// ─── Common middleware stack ──────────────────────────────
const helmet      = require("helmet");
const cors        = require("cors");
const compression = require("compression");
const morgan      = require("morgan");

// Security & utility first:
app.use(helmet());          // security headers
app.use(cors(corsOptions)); // CORS
app.use(compression());     // gzip responses
app.use(morgan("dev"));     // logging

// Then parsing:
app.use(express.json({ limit: "10mb" }));
app.use(express.urlencoded({ extended: true, limit: "10mb" }));

// Then custom:
app.use(requestId);

// Then routes:
app.use("/api/v1", apiRouter);

// Then error handlers (last!):
app.use(notFoundMiddleware);
app.use(errorMiddleware);
```

---

## 22. Request Object (req)

```javascript
app.post("/api/users/:id/posts", (req, res) => {

  // ─── URL & Route info ─────────────────────────────────
  req.method;          // "POST"
  req.url;             // "/api/users/42/posts?sort=desc"
  req.originalUrl;     // "/api/users/42/posts?sort=desc" (unchanged by router)
  req.path;            // "/42/posts" (relative to where router is mounted)
  req.hostname;        // "example.com"
  req.protocol;        // "https"
  req.secure;          // true if HTTPS
  req.subdomains;      // ["api"] for "api.example.com"
  req.baseUrl;         // "/api/users" (path of router)

  // ─── Route parameters ─────────────────────────────────
  req.params.id;       // "42" (from :id)
  req.params;          // { id: "42" }

  // ─── Query string ─────────────────────────────────────
  req.query.sort;      // "desc"
  req.query;           // { sort: "desc" }

  // ─── Request body ─────────────────────────────────────
  // (requires express.json() or express.urlencoded() middleware)
  req.body;            // parsed request body (object, string, buffer)
  req.body.username;   // access body fields

  // ─── Headers ──────────────────────────────────────────
  req.headers;                            // all headers (lowercase keys)
  req.get("Content-Type");               // "application/json"
  req.get("Authorization");              // "Bearer token..."
  req.headers["x-custom-header"];        // custom headers
  req.accepts("application/json");       // content negotiation
  req.acceptsCharsets("utf-8");
  req.acceptsLanguages("en", "fr");      // returns best match

  // ─── Client info ──────────────────────────────────────
  req.ip;              // "192.168.1.1" (client IP, respects trust proxy)
  req.ips;             // ["192.168.1.1", "10.0.0.1"] (proxy chain)
  req.get("User-Agent"); // browser/client info
  req.xhr;             // true if X-Requested-With: XMLHttpRequest

  // ─── Cookies ──────────────────────────────────────────
  // (requires cookie-parser middleware)
  req.cookies.sessionId;    // unsigned cookie
  req.signedCookies.token;  // signed cookie

  // ─── File uploads ─────────────────────────────────────
  // (requires multer middleware)
  req.file;            // single uploaded file
  req.files;           // multiple uploaded files

  // ─── Custom properties (added by middleware) ──────────
  req.user;            // added by auth middleware
  req.id;              // added by requestId middleware
  req.db;              // added by database middleware

  // ─── Content type check ───────────────────────────────
  req.is("application/json"); // true if Content-Type matches
  req.is("text/*");           // wildcard match

  res.json({ received: req.body });
});
```

---

## 23. Response Object (res)

```javascript
app.get("/demo", (req, res) => {

  // ─── Status code ──────────────────────────────────────
  res.status(200);                 // set status code (chainable)
  res.statusCode = 404;            // alternative

  // ─── Headers ──────────────────────────────────────────
  res.set("X-Custom-Header", "value");          // set header
  res.set({ "X-A": "1", "X-B": "2" });         // set multiple
  res.setHeader("Content-Type", "text/plain");  // alias
  res.append("Set-Cookie", "token=abc");        // append (doesn't overwrite)
  res.removeHeader("X-Powered-By");
  res.get("Content-Type");                      // read header
  res.type("json");                             // set Content-Type shorthand
  res.type("text/html");
  res.type("png");

  // ─── Sending responses ────────────────────────────────

  // Send any type (auto-sets Content-Type):
  res.send("Hello World");                      // text/html
  res.send(Buffer.from("binary data"));         // application/octet-stream
  res.send({ key: "value" });                   // application/json

  // Send JSON (always sets Content-Type: application/json):
  res.json({ message: "Success", data: [] });
  res.status(201).json({ created: true });

  // Send JSONP:
  res.jsonp({ callback: "fn", data: {} });

  // Send file:
  res.sendFile("/absolute/path/to/file.pdf");
  res.sendFile("image.png", { root: __dirname + "/public" });

  // Download file:
  res.download("/path/to/report.pdf");
  res.download("/path/to/file.csv", "custom-name.csv"); // custom filename

  // Send status only (no body):
  res.sendStatus(204);  // "204 No Content"
  res.sendStatus(404);  // "404 Not Found"
  res.end();            // end without body

  // Redirect:
  res.redirect("/new-location");                // 302 temporary
  res.redirect(301, "/permanent-location");     // 301 permanent
  res.redirect("back");                         // redirect to Referer

  // Render template:
  res.render("index", { title: "Home", user: req.user });

  // ─── Streaming responses ──────────────────────────────
  res.setHeader("Content-Type", "text/event-stream");
  res.setHeader("Cache-Control", "no-cache");
  res.setHeader("Connection", "keep-alive");

  // Server-sent events:
  let count = 0;
  const interval = setInterval(() => {
    res.write(`data: ${JSON.stringify({ count: count++ })}\n\n`);
    if (count >= 10) {
      clearInterval(interval);
      res.end();
    }
  }, 1000);

  req.on("close", () => clearInterval(interval));

  // ─── Cookies ──────────────────────────────────────────
  res.cookie("sessionId", "abc123", {
    httpOnly: true,          // no JS access (XSS protection)
    secure:   true,          // HTTPS only
    sameSite: "strict",      // CSRF protection
    maxAge:   7 * 24 * 3600 * 1000, // 7 days in ms
    path:     "/",
    domain:   ".example.com"
  });
  res.clearCookie("sessionId");

  // ─── Locals (available in templates) ─────────────────
  res.locals.user    = req.user;
  res.locals.message = "Welcome!";
  res.render("dashboard");

  // ─── Vary header (caching) ────────────────────────────
  res.vary("Accept-Encoding");
  res.vary("Accept-Language");

  // ─── Format — content negotiation ────────────────────
  res.format({
    "text/html": () => res.render("users"),
    "application/json": () => res.json(users),
    "default": () => res.status(406).send("Not Acceptable")
  });
});
```

---

## 24. Static Files

```javascript
const express = require("express");
const path    = require("path");
const app     = express();

// ─── Serve entire directory as static ────────────────────
// Files in "public/" are served from root:
// public/index.html  → GET /index.html
// public/css/main.css → GET /css/main.css
// public/images/logo.png → GET /images/logo.png

app.use(express.static("public"));

// With absolute path (recommended):
app.use(express.static(path.join(__dirname, "public")));

// ─── Serve from a specific URL prefix ────────────────────
// /static/css/main.css → reads from public/css/main.css
app.use("/static", express.static(path.join(__dirname, "public")));

// Serve multiple directories:
app.use(express.static(path.join(__dirname, "public")));
app.use(express.static(path.join(__dirname, "uploads")));

// ─── Static files with options ────────────────────────────
app.use(express.static("public", {
  maxAge:       "1d",           // cache for 1 day
  etag:         true,           // enable ETag headers
  lastModified: true,           // enable Last-Modified headers
  index:        "index.html",   // directory index file
  dotfiles:     "ignore",       // ignore/allow/deny dotfiles
  redirect:     false,          // redirect trailing slash
  setHeaders:   function (res, path) {
    if (path.endsWith(".html")) {
      res.set("Cache-Control", "no-cache");
    }
    if (path.endsWith(".js") || path.endsWith(".css")) {
      res.set("Cache-Control", "public, max-age=31536000, immutable");
    }
  }
}));

// ─── Serve SPA (Single Page App) ─────────────────────────
// Serve static files, fallback to index.html for unknown routes:
app.use(express.static(path.join(__dirname, "client/build")));

app.get("*", (req, res) => {
  // Only for non-API routes:
  if (!req.path.startsWith("/api")) {
    res.sendFile(path.join(__dirname, "client/build", "index.html"));
  }
});
```

---

## 25. Template Engines

### EJS (Embedded JavaScript)

```bash
npm install ejs
```

```javascript
// app.js:
app.set("view engine", "ejs");
app.set("views", path.join(__dirname, "views"));

app.get("/users", async (req, res) => {
  const users = await User.find();
  res.render("users/index", {
    title:    "Users",
    users,
    currentUser: req.user,
    formatDate: (d) => d.toLocaleDateString()
  });
});
```

```html
<!-- views/users/index.ejs -->
<!DOCTYPE html>
<html>
<head>
  <title><%= title %></title>  <!-- escaped output -->
</head>
<body>
  <!-- Include a partial: -->
  <%- include("../partials/header", { title }) %>

  <h1><%= title %></h1>

  <% if (users.length === 0) { %>
    <p>No users found.</p>
  <% } else { %>
    <ul>
      <% users.forEach(user => { %>
        <li>
          <%= user.name %>                    <!-- escaped -->
          <%- user.bioHtml %>                 <!-- raw HTML — unescaped! -->
          <span><%= formatDate(user.createdAt) %></span>
        </li>
      <% }) %>
    </ul>
  <% } %>

  <!-- Conditional:  -->
  <% if (currentUser?.role === "admin") { %>
    <a href="/admin">Admin Panel</a>
  <% } %>
</body>
</html>
```

### Pug (formerly Jade)

```bash
npm install pug
```

```javascript
app.set("view engine", "pug");
app.set("views", "./views");

app.get("/", (req, res) => {
  res.render("index", { title: "Home", user: req.user });
});
```

```pug
//- views/index.pug
doctype html
html(lang="en")
  head
    title= title
    link(rel="stylesheet", href="/css/main.css")

  body
    include partials/nav

    main
      h1 Welcome, #{user.name}!

      if user.isAdmin
        a(href="/admin") Admin Panel

      ul
        each item in items
          li
            a(href=`/items/${item.id}`)= item.name
            span.price $#{item.price.toFixed(2)}

      p.
        This is a long paragraph
        that spans multiple lines.
```

---

## 26. Error Handling in Express

### The Error Handling Flow

```javascript
const express = require("express");
const app     = express();

// ─── Custom Error Classes ────────────────────────────────
class AppError extends Error {
  constructor(message, statusCode = 500, code = "INTERNAL_ERROR") {
    super(message);
    this.statusCode    = statusCode;
    this.code          = code;
    this.isOperational = true;
    Error.captureStackTrace(this, this.constructor);
  }
}

class NotFoundError      extends AppError {
  constructor(r, id) { super(`${r} '${id}' not found`, 404, "NOT_FOUND"); }
}
class ValidationError    extends AppError {
  constructor(msg, fields = {}) {
    super(msg, 400, "VALIDATION_ERROR");
    this.fields = fields;
  }
}
class UnauthorizedError  extends AppError {
  constructor(msg = "Unauthorized") { super(msg, 401, "UNAUTHORIZED"); }
}
class ForbiddenError     extends AppError {
  constructor(msg = "Forbidden")    { super(msg, 403, "FORBIDDEN"); }
}
class ConflictError      extends AppError {
  constructor(msg) { super(msg, 409, "CONFLICT"); }
}

// ─── Async handler wrapper ────────────────────────────────
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

// ─── Routes that throw errors ────────────────────────────
app.get("/users/:id", asyncHandler(async (req, res) => {
  const user = await User.findById(req.params.id);
  if (!user) throw new NotFoundError("User", req.params.id);
  res.json(user);
}));

app.post("/users", asyncHandler(async (req, res) => {
  const { email } = req.body;
  const existing  = await User.findOne({ email });
  if (existing)   throw new ConflictError("Email already registered");
  const user      = await User.create(req.body);
  res.status(201).json(user);
}));

// ─── 404 Handler (MUST be after all routes) ──────────────
app.use((req, res, next) => {
  next(new AppError(`Route ${req.method} ${req.path} not found`, 404, "ROUTE_NOT_FOUND"));
});

// ─── Global Error Handler (MUST have 4 params) ───────────
app.use((err, req, res, next) => {
  // Log error:
  if (err.statusCode >= 500) {
    console.error({
      message:   err.message,
      stack:     err.stack,
      requestId: req.id,
      url:       req.originalUrl,
      method:    req.method
    });
  }

  // Handle specific error types:
  let error = err;

  // Mongoose validation error:
  if (err.name === "ValidationError") {
    const fields = Object.keys(err.errors).reduce((acc, key) => {
      acc[key] = err.errors[key].message;
      return acc;
    }, {});
    error = new ValidationError("Validation failed", fields);
  }

  // Mongoose duplicate key:
  if (err.code === 11000) {
    const field = Object.keys(err.keyValue)[0];
    error = new ConflictError(`${field} already exists`);
  }

  // Mongoose cast error (invalid ObjectId):
  if (err.name === "CastError" && err.kind === "ObjectId") {
    error = new NotFoundError(err.path, err.value);
  }

  // JWT errors:
  if (err.name === "JsonWebTokenError")  error = new UnauthorizedError("Invalid token");
  if (err.name === "TokenExpiredError")  error = new UnauthorizedError("Token expired");

  // Build response:
  const statusCode = error.statusCode || 500;
  const response   = {
    success: false,
    error: {
      code:    error.code    || "INTERNAL_ERROR",
      message: error.message || "Something went wrong"
    }
  };

  // Add fields for validation errors:
  if (error.fields) response.error.fields = error.fields;

  // Add stack trace in development:
  if (process.env.NODE_ENV === "development") {
    response.error.stack = err.stack;
  }

  res.status(statusCode).json(response);
});
```

---

## 27. Router & Modular Routes

### Express Router

```javascript
// ─── routes/users.js ─────────────────────────────────────
const express    = require("express");
const router     = express.Router();
const { authenticate, authorize } = require("../middleware/auth");
const { asyncHandler } = require("../middleware/asyncHandler");
const userController   = require("../controllers/userController");
const { validateCreateUser, validateUpdateUser } = require("../validators/user");

// Router-level middleware (applies to all routes in this router):
router.use(authenticate);

// GET /api/v1/users
router.get("/",         authorize("admin"), asyncHandler(userController.getAll));

// GET /api/v1/users/:id
router.get("/:id",      asyncHandler(userController.getOne));

// POST /api/v1/users
router.post("/",        authorize("admin"), validateCreateUser, asyncHandler(userController.create));

// PUT /api/v1/users/:id
router.put("/:id",      authorize("admin"), validateUpdateUser, asyncHandler(userController.update));

// PATCH /api/v1/users/:id
router.patch("/:id",    asyncHandler(userController.partialUpdate));

// DELETE /api/v1/users/:id
router.delete("/:id",   authorize("admin"), asyncHandler(userController.delete));

// Nested route:
// GET /api/v1/users/:userId/posts
router.get("/:userId/posts", asyncHandler(userController.getPosts));

module.exports = router;

// ─── routes/index.js — main router aggregator ────────────
const express     = require("express");
const router      = express.Router();
const usersRouter = require("./users");
const postsRouter = require("./posts");
const authRouter  = require("./auth");

router.use("/auth",  authRouter);
router.use("/users", usersRouter);
router.use("/posts", postsRouter);

// Health check — public:
router.get("/health", (req, res) => {
  res.json({ status: "ok", timestamp: new Date().toISOString() });
});

module.exports = router;

// ─── app.js ───────────────────────────────────────────────
const apiRouter = require("./routes");
app.use("/api/v1", apiRouter);
// Now: /api/v1/users, /api/v1/posts, etc.
```

### MVC Controller Pattern

```javascript
// ─── controllers/userController.js ───────────────────────
const User     = require("../models/User");
const { NotFoundError, ConflictError } = require("../errors");

const userController = {

  async getAll(req, res) {
    const {
      page  = 1,
      limit = 20,
      sort  = "createdAt",
      order = "desc",
      ...filters
    } = req.query;

    const skip    = (parseInt(page) - 1) * parseInt(limit);
    const total   = await User.countDocuments(filters);
    const users   = await User.find(filters)
      .select("-password")
      .sort({ [sort]: order === "desc" ? -1 : 1 })
      .skip(skip)
      .limit(parseInt(limit));

    res.json({
      success: true,
      data:    users,
      meta: {
        total,
        page:    parseInt(page),
        limit:   parseInt(limit),
        pages:   Math.ceil(total / parseInt(limit))
      }
    });
  },

  async getOne(req, res) {
    const user = await User.findById(req.params.id).select("-password");
    if (!user) throw new NotFoundError("User", req.params.id);
    res.json({ success: true, data: user });
  },

  async create(req, res) {
    const { email } = req.body;
    if (await User.findOne({ email })) {
      throw new ConflictError("Email already registered");
    }
    const user = await User.create(req.body);
    const { password: _, ...safeUser } = user.toObject();
    res.status(201).json({ success: true, data: safeUser });
  },

  async update(req, res) {
    const user = await User.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true, runValidators: true }
    ).select("-password");
    if (!user) throw new NotFoundError("User", req.params.id);
    res.json({ success: true, data: user });
  },

  async partialUpdate(req, res) {
    const user = await User.findByIdAndUpdate(
      req.params.id,
      { $set: req.body },
      { new: true, runValidators: true }
    ).select("-password");
    if (!user) throw new NotFoundError("User", req.params.id);
    res.json({ success: true, data: user });
  },

  async delete(req, res) {
    const user = await User.findByIdAndDelete(req.params.id);
    if (!user) throw new NotFoundError("User", req.params.id);
    res.status(204).end();
  },

  async getPosts(req, res) {
    const { userId } = req.params;
    const user = await User.findById(userId);
    if (!user) throw new NotFoundError("User", userId);
    const posts = await Post.find({ author: userId })
      .sort({ createdAt: -1 });
    res.json({ success: true, data: posts });
  }
};

module.exports = userController;
```

---

## 28. Body Parsing

```javascript
const express    = require("express");
const app        = express();

// ─── JSON body parsing ───────────────────────────────────
app.use(express.json({
  limit:    "10mb",         // max body size
  strict:   true,           // only accept arrays and objects
  type:     "application/json",
  reviver:  (key, value) => {
    // Custom JSON parsing:
    if (key === "date") return new Date(value);
    return value;
  }
}));

// ─── URL-encoded form data (HTML forms) ───────────────────
// Content-Type: application/x-www-form-urlencoded
app.use(express.urlencoded({
  extended: true,   // true = use qs library (nested objects)
  limit:    "5mb",
  parameterLimit: 1000
}));

// extended: true  allows: ?user[name]=Alice&user[age]=30
// extended: false only allows simple key=value pairs

// ─── Raw body (for webhooks, binary data) ─────────────────
app.use("/webhooks", express.raw({
  type: "application/octet-stream",
  limit: "1mb"
}));

// For Stripe webhooks (needs raw body for signature verification):
app.post("/webhooks/stripe",
  express.raw({ type: "application/json" }),
  (req, res) => {
    const signature = req.headers["stripe-signature"];
    const event     = stripe.webhooks.constructEvent(
      req.body,          // raw Buffer
      signature,
      process.env.STRIPE_WEBHOOK_SECRET
    );
    // Handle event...
    res.json({ received: true });
  }
);

// ─── Text body ────────────────────────────────────────────
app.use(express.text({
  type: "text/plain",
  limit: "1mb"
}));

// ─── Manual body parsing (streams) ────────────────────────
app.post("/upload-stream", async (req, res) => {
  const chunks = [];
  for await (const chunk of req) {
    chunks.push(chunk);
    if (Buffer.concat(chunks).length > 5 * 1024 * 1024) {
      req.destroy();
      return res.status(413).json({ error: "Body too large" });
    }
  }
  const body = Buffer.concat(chunks).toString("utf8");
  res.json({ received: body.length, "chars": body.substring(0, 100) });
});

// ─── Validation with Joi ──────────────────────────────────
const Joi = require("joi");

const createUserSchema = Joi.object({
  name:     Joi.string().min(2).max(100).required(),
  email:    Joi.string().email().lowercase().required(),
  password: Joi.string().min(8).pattern(/^(?=.*[A-Z])(?=.*\d)/).required()
             .messages({ "string.pattern.base": "Password needs uppercase and number" }),
  age:      Joi.number().integer().min(13).max(120),
  role:     Joi.string().valid("user", "admin").default("user")
});

function validate(schema) {
  return async (req, res, next) => {
    try {
      const { error, value } = schema.validate(req.body, {
        abortEarly:  false,   // collect ALL errors
        stripUnknown: true,   // remove unknown fields
        convert:     true     // convert types (string → number)
      });

      if (error) {
        const fields = {};
        error.details.forEach(d => { fields[d.path.join(".")] = d.message; });
        return res.status(400).json({ error: "Validation failed", fields });
      }

      req.body = value;
      next();
    } catch (err) {
      next(err);
    }
  };
}

app.post("/users", validate(createUserSchema), asyncHandler(async (req, res) => {
  const user = await User.create(req.body);
  res.status(201).json(user);
}));
```

---

## 29. Cookies & Sessions

### Cookies

```bash
npm install cookie-parser
```

```javascript
const express      = require("express");
const cookieParser = require("cookie-parser");
const app          = express();

// Mount cookie parser (with optional secret for signed cookies):
app.use(cookieParser(process.env.COOKIE_SECRET));

app.get("/set-cookie", (req, res) => {
  // Simple cookie:
  res.cookie("name", "Alice");

  // Cookie with options:
  res.cookie("sessionId", "abc123xyz", {
    httpOnly: true,            // not accessible via JS (XSS protection)
    secure:   true,            // HTTPS only
    sameSite: "strict",        // "strict" | "lax" | "none" (CSRF protection)
    maxAge:   7 * 24 * 3600 * 1000,  // 7 days in milliseconds
    expires:  new Date(Date.now() + 7 * 24 * 3600 * 1000),
    path:     "/",             // available on all paths
    domain:   ".example.com"   // available on subdomains
  });

  // Signed cookie (tamper-proof):
  res.cookie("userId", "user_42", { signed: true });

  res.json({ message: "Cookies set!" });
});

app.get("/read-cookies", (req, res) => {
  // Read cookies:
  const name      = req.cookies.name;          // "Alice"
  const sessionId = req.cookies.sessionId;     // "abc123xyz"
  const userId    = req.signedCookies.userId;  // "user_42" (or false if tampered)

  res.json({ name, sessionId, userId });
});

app.get("/delete-cookie", (req, res) => {
  res.clearCookie("name");
  res.clearCookie("sessionId", { path: "/" });  // options must match original!
  res.json({ message: "Cookies cleared!" });
});
```

### Sessions with express-session

```bash
npm install express-session connect-mongo
```

```javascript
const session   = require("express-session");
const MongoStore = require("connect-mongo");

app.use(session({
  secret:            process.env.SESSION_SECRET, // sign session ID cookie
  name:              "sessionId",               // cookie name
  resave:            false,    // don't save if session unchanged
  saveUninitialized: false,    // don't save empty sessions

  // Store sessions in MongoDB (instead of default MemoryStore):
  store: MongoStore.create({
    mongoUrl:   process.env.DATABASE_URL,
    ttl:        7 * 24 * 3600,   // session TTL: 7 days
    autoRemove: "native"         // use MongoDB TTL index
  }),

  cookie: {
    httpOnly: true,
    secure:   process.env.NODE_ENV === "production",
    sameSite: "lax",
    maxAge:   7 * 24 * 3600 * 1000  // 7 days
  }
}));

// ─── Using sessions ───────────────────────────────────────

app.post("/login", asyncHandler(async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email }).select("+password");
  if (!user || !await user.comparePassword(password)) {
    throw new UnauthorizedError("Invalid credentials");
  }

  // Store user info in session:
  req.session.userId   = user._id;
  req.session.userRole = user.role;
  req.session.loginAt  = Date.now();

  // Regenerate session ID after login (prevent session fixation):
  req.session.regenerate((err) => {
    if (err) return next(err);
    req.session.userId   = user._id;
    req.session.userRole = user.role;
    res.json({ message: "Logged in", user: user.toPublicJSON() });
  });
}));

app.post("/logout", (req, res) => {
  req.session.destroy((err) => {
    if (err) return res.status(500).json({ error: "Logout failed" });
    res.clearCookie("sessionId");
    res.json({ message: "Logged out" });
  });
});

// Session middleware:
function requireSession(req, res, next) {
  if (!req.session?.userId) {
    return res.status(401).json({ error: "Please log in" });
  }
  next();
}

app.get("/profile", requireSession, asyncHandler(async (req, res) => {
  const user = await User.findById(req.session.userId);
  res.json(user);
}));
```

---

## 30. Authentication & Authorization

### JWT Authentication

```bash
npm install jsonwebtoken bcryptjs
```

```javascript
const jwt     = require("jsonwebtoken");
const bcrypt  = require("bcryptjs");
const { promisify } = require("util");
const signJWT = promisify(jwt.sign);
const verifyJWT = promisify(jwt.verify);

// ─── Password Hashing ─────────────────────────────────────
async function hashPassword(password) {
  const salt   = await bcrypt.genSalt(12); // 12 rounds
  return bcrypt.hash(password, salt);
}

async function comparePassword(plain, hashed) {
  return bcrypt.compare(plain, hashed);
}

// ─── JWT Utilities ────────────────────────────────────────
function generateTokens(userId, role) {
  const payload = { userId, role };

  const accessToken = jwt.sign(
    payload,
    process.env.JWT_ACCESS_SECRET,
    { expiresIn: "15m", issuer: "myapp", audience: "myapp-users" }
  );

  const refreshToken = jwt.sign(
    { userId },
    process.env.JWT_REFRESH_SECRET,
    { expiresIn: "7d" }
  );

  return { accessToken, refreshToken };
}

function sendTokens(res, tokens) {
  // Access token in JSON body:
  // Refresh token in httpOnly cookie:
  res.cookie("refreshToken", tokens.refreshToken, {
    httpOnly: true,
    secure:   process.env.NODE_ENV === "production",
    sameSite: "strict",
    maxAge:   7 * 24 * 3600 * 1000
  });
  return tokens.accessToken;
}

// ─── Auth Controller ──────────────────────────────────────
const authController = {

  async register(req, res) {
    const { name, email, password } = req.body;

    if (await User.findOne({ email })) {
      throw new ConflictError("Email already registered");
    }

    const hashed = await hashPassword(password);
    const user   = await User.create({ name, email, password: hashed });

    const tokens      = generateTokens(user._id, user.role);
    const accessToken = sendTokens(res, tokens);

    res.status(201).json({
      success: true,
      accessToken,
      user:    user.toPublicJSON()
    });
  },

  async login(req, res) {
    const { email, password } = req.body;
    const user = await User.findOne({ email }).select("+password");

    if (!user || !await comparePassword(password, user.password)) {
      throw new UnauthorizedError("Invalid email or password");
    }

    if (!user.isActive) {
      throw new ForbiddenError("Account is deactivated");
    }

    // Update last login:
    await User.findByIdAndUpdate(user._id, { lastLoginAt: new Date() });

    const tokens      = generateTokens(user._id, user.role);
    const accessToken = sendTokens(res, tokens);

    res.json({ success: true, accessToken, user: user.toPublicJSON() });
  },

  async refresh(req, res) {
    const refreshToken = req.cookies?.refreshToken;
    if (!refreshToken) throw new UnauthorizedError("No refresh token");

    let decoded;
    try {
      decoded = jwt.verify(refreshToken, process.env.JWT_REFRESH_SECRET);
    } catch {
      throw new UnauthorizedError("Invalid or expired refresh token");
    }

    const user = await User.findById(decoded.userId);
    if (!user) throw new UnauthorizedError("User not found");

    const tokens      = generateTokens(user._id, user.role);
    const accessToken = sendTokens(res, tokens);

    res.json({ success: true, accessToken });
  },

  async logout(req, res) {
    res.clearCookie("refreshToken");
    // Add token to blocklist if using token revocation...
    res.json({ success: true, message: "Logged out" });
  }
};

// ─── Auth Middleware ──────────────────────────────────────
async function authenticate(req, res, next) {
  try {
    const authHeader  = req.headers["authorization"];
    const tokenFromCookie = req.cookies?.accessToken;
    const token = authHeader?.startsWith("Bearer ")
      ? authHeader.slice(7)
      : tokenFromCookie;

    if (!token) throw new UnauthorizedError("Authentication required");

    const decoded = jwt.verify(token, process.env.JWT_ACCESS_SECRET, {
      issuer:   "myapp",
      audience: "myapp-users"
    });

    const user = await User.findById(decoded.userId).select("-password");
    if (!user) throw new UnauthorizedError("User not found");

    req.user  = user;
    req.token = token;
    next();
  } catch (err) {
    if (err instanceof AppError) return next(err);
    if (err.name === "TokenExpiredError") return next(new UnauthorizedError("Token expired"));
    next(new UnauthorizedError("Invalid token"));
  }
}

function authorize(...allowedRoles) {
  return (req, res, next) => {
    if (!req.user) return next(new UnauthorizedError());
    if (!allowedRoles.includes(req.user.role)) {
      return next(new ForbiddenError(
        `Role '${req.user.role}' not allowed. Required: ${allowedRoles.join(" or ")}`
      ));
    }
    next();
  };
}

// ─── Auth Routes ──────────────────────────────────────────
const authRouter = express.Router();

authRouter.post("/register", validateBody(registerSchema), asyncHandler(authController.register));
authRouter.post("/login",    validateBody(loginSchema),    asyncHandler(authController.login));
authRouter.post("/refresh",  asyncHandler(authController.refresh));
authRouter.post("/logout",   authenticate, asyncHandler(authController.logout));
authRouter.get("/me",        authenticate, (req, res) => res.json({ user: req.user }));
```

---

## 31. File Uploads

```bash
npm install multer
```

```javascript
const multer  = require("multer");
const path    = require("path");
const fs      = require("fs");
const sharp   = require("sharp"); // for image processing

// ─── Memory storage (files in RAM) ───────────────────────
const memoryStorage = multer.memoryStorage();

// ─── Disk storage (files on disk) ─────────────────────────
const diskStorage = multer.diskStorage({
  destination: function (req, file, cb) {
    const uploadDir = path.join(__dirname, "uploads", req.user?.id ?? "temp");
    fs.mkdirSync(uploadDir, { recursive: true });
    cb(null, uploadDir);
  },
  filename: function (req, file, cb) {
    const uniqueSuffix = `${Date.now()}-${Math.round(Math.random() * 1E9)}`;
    const ext          = path.extname(file.originalname);
    cb(null, `${file.fieldname}-${uniqueSuffix}${ext}`);
  }
});

// ─── File filter ──────────────────────────────────────────
function imageFilter(req, file, cb) {
  const allowedMimes = ["image/jpeg", "image/png", "image/gif", "image/webp"];
  if (!allowedMimes.includes(file.mimetype)) {
    cb(new Error("Only image files allowed (jpeg, png, gif, webp)"), false);
  } else {
    cb(null, true);
  }
}

// ─── Configure multer ─────────────────────────────────────
const upload = multer({
  storage:  diskStorage,
  limits: {
    fileSize:  5 * 1024 * 1024,  // 5MB max per file
    files:     10,               // max 10 files
    fields:    20,               // max 20 non-file fields
    fieldSize: 2 * 1024 * 1024   // max 2MB per field
  },
  fileFilter: imageFilter
});

// ─── Single file upload ───────────────────────────────────
app.post(
  "/upload/avatar",
  authenticate,
  upload.single("avatar"),        // field name = "avatar"
  asyncHandler(async (req, res) => {
    if (!req.file) throw new Error("No file uploaded");

    // req.file = {
    //   fieldname:    "avatar",
    //   originalname: "photo.jpg",
    //   encoding:     "7bit",
    //   mimetype:     "image/jpeg",
    //   destination:  "./uploads/user_42",
    //   filename:     "avatar-1234567890.jpg",
    //   path:         "./uploads/user_42/avatar-1234567890.jpg",
    //   size:         12345    // bytes
    // }

    // Process image with sharp:
    const processedPath = req.file.path.replace(/\.\w+$/, "-processed.webp");
    await sharp(req.file.path)
      .resize(200, 200, { fit: "cover" })
      .webp({ quality: 85 })
      .toFile(processedPath);

    // Delete original:
    fs.unlinkSync(req.file.path);

    // Update user avatar URL in DB:
    const avatarUrl = `/uploads/${req.user.id}/${path.basename(processedPath)}`;
    await User.findByIdAndUpdate(req.user.id, { avatarUrl });

    res.json({ success: true, avatarUrl });
  })
);

// ─── Multiple files upload ────────────────────────────────
app.post(
  "/upload/gallery",
  authenticate,
  upload.array("photos", 10),    // field "photos", max 10 files
  asyncHandler(async (req, res) => {
    const files = req.files; // array of file objects

    const processed = await Promise.all(files.map(async (file) => {
      const outputPath = file.path.replace(/\.\w+$/, ".webp");
      await sharp(file.path).webp({ quality: 85 }).toFile(outputPath);
      fs.unlinkSync(file.path);
      return {
        original: file.originalname,
        url:      `/uploads/${req.user.id}/${path.basename(outputPath)}`,
        size:     file.size
      };
    }));

    res.json({ success: true, files: processed });
  })
);

// ─── Mixed fields + files ─────────────────────────────────
app.post(
  "/upload/product",
  authenticate,
  upload.fields([
    { name: "thumbnail",  maxCount: 1 },
    { name: "images",     maxCount: 5 },
    { name: "documents",  maxCount: 3 }
  ]),
  asyncHandler(async (req, res) => {
    const { thumbnail, images, documents } = req.files;
    const { name, description, price }    = req.body;

    // Process...
    res.json({ success: true, name, files: { thumbnail, images, documents } });
  })
);

// ─── Error handling for multer ────────────────────────────
app.use((err, req, res, next) => {
  if (err instanceof multer.MulterError) {
    if (err.code === "LIMIT_FILE_SIZE")  return res.status(413).json({ error: "File too large" });
    if (err.code === "LIMIT_FILE_COUNT") return res.status(400).json({ error: "Too many files" });
    if (err.code === "LIMIT_UNEXPECTED_FILE") return res.status(400).json({ error: "Unexpected file field" });
  }
  next(err);
});
```

---

## 32. CORS

```bash
npm install cors
```

```javascript
const cors = require("cors");

// ─── Allow all origins (development only!) ────────────────
app.use(cors());

// ─── Specific origin ──────────────────────────────────────
app.use(cors({
  origin: "https://example.com"
}));

// ─── Multiple origins ─────────────────────────────────────
const allowedOrigins = [
  "https://example.com",
  "https://www.example.com",
  "https://app.example.com",
  process.env.NODE_ENV === "development" ? "http://localhost:3000" : null
].filter(Boolean);

app.use(cors({
  origin: function (origin, callback) {
    // Allow requests with no origin (curl, Postman, mobile apps):
    if (!origin) return callback(null, true);

    if (allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error(`CORS: Origin '${origin}' not allowed`));
    }
  },
  methods:          ["GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS"],
  allowedHeaders:   ["Content-Type", "Authorization", "X-Request-Id"],
  exposedHeaders:   ["X-Total-Count", "X-RateLimit-Remaining"],
  credentials:      true,        // allow cookies cross-origin
  maxAge:           86400,       // preflight cache: 24 hours (seconds)
  optionsSuccessStatus: 204      // some browsers need 204 for OPTIONS
}));

// ─── CORS per route ───────────────────────────────────────
const publicCors  = cors({ origin: "*" });
const privateCors = cors({ origin: allowedOrigins, credentials: true });

app.get("/public-data",  publicCors,  (req, res) => res.json({ public: true }));
app.get("/private-data", privateCors, (req, res) => res.json({ private: true }));

// ─── Preflight — handle OPTIONS requests ─────────────────
// cors() automatically handles OPTIONS, but you can be explicit:
app.options("*", cors(corsOptions)); // enable pre-flight for all routes

// ─── Manual CORS (without library) ───────────────────────
app.use((req, res, next) => {
  const origin = req.headers.origin;
  if (allowedOrigins.includes(origin)) {
    res.setHeader("Access-Control-Allow-Origin", origin);
    res.setHeader("Vary", "Origin");               // important for caching!
  }
  res.setHeader("Access-Control-Allow-Methods",    "GET,POST,PUT,PATCH,DELETE,OPTIONS");
  res.setHeader("Access-Control-Allow-Headers",    "Content-Type,Authorization");
  res.setHeader("Access-Control-Allow-Credentials","true");
  res.setHeader("Access-Control-Max-Age",          "86400");

  if (req.method === "OPTIONS") {
    return res.status(204).end(); // respond to preflight
  }
  next();
});
```

---

## 33. Rate Limiting & Security

### Rate Limiting

```bash
npm install express-rate-limit rate-limit-redis ioredis helmet
```

```javascript
const rateLimit   = require("express-rate-limit");
const helmet      = require("helmet");

// ─── Basic rate limiter ───────────────────────────────────
const limiter = rateLimit({
  windowMs:       15 * 60 * 1000,  // 15 minutes
  max:            100,              // max requests per window
  standardHeaders: true,           // Return RateLimit-* headers
  legacyHeaders:   false,          // Disable X-RateLimit-* headers
  message:        { error: "Too many requests, try again later" },
  keyGenerator:   (req) => req.ip  // default: IP-based
});

// Apply globally:
app.use(limiter);

// ─── Different limits per route ───────────────────────────
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max:      10,               // only 10 attempts per 15 min
  message:  { error: "Too many login attempts. Try again in 15 minutes." },
  skipSuccessfulRequests: true   // don't count successful logins
});

const createAccountLimiter = rateLimit({
  windowMs: 60 * 60 * 1000,  // 1 hour
  max:      5,                // only 5 registrations per hour
  message:  { error: "Too many accounts created. Try again in an hour." }
});

app.post("/auth/login",    authLimiter,          authController.login);
app.post("/auth/register", createAccountLimiter, authController.register);

// ─── Rate limiter with Redis (for distributed systems) ────
const { RedisStore } = require("rate-limit-redis");
const Redis          = require("ioredis");

const redisClient = new Redis(process.env.REDIS_URL);

const distributedLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max:      100,
  store:    new RedisStore({
    sendCommand: (...args) => redisClient.call(...args)
  })
});

// ─── Helmet — security headers ────────────────────────────
app.use(helmet({
  // Content Security Policy:
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc:  ["'self'", "'nonce-{nonce}'"],
      styleSrc:   ["'self'", "https://fonts.googleapis.com"],
      imgSrc:     ["'self'", "data:", "https://res.cloudinary.com"],
      fontSrc:    ["'self'", "https://fonts.gstatic.com"],
      connectSrc: ["'self'", "https://api.example.com"],
      frameSrc:   ["'none'"],
      objectSrc:  ["'none'"]
    }
  },
  crossOriginEmbedderPolicy: false,  // may break some features
  hsts: {
    maxAge:            31536000,     // 1 year
    includeSubDomains: true,
    preload:           true
  }
}));

// What helmet sets:
// X-Content-Type-Options: nosniff
// X-Frame-Options: SAMEORIGIN
// X-XSS-Protection: 0  (modern browsers don't need this)
// Strict-Transport-Security: max-age=...
// Content-Security-Policy: ...
// Referrer-Policy: no-referrer
// X-DNS-Prefetch-Control: off
// Cross-Origin-Opener-Policy: same-origin
```

### Security Best Practices

```javascript
const mongoSanitize = require("express-mongo-sanitize");
const xss           = require("xss-clean");
const hpp           = require("hpp"); // HTTP parameter pollution

// ─── Prevent NoSQL injection ──────────────────────────────
// Removes $ and . from user input (MongoDB operators):
app.use(mongoSanitize());

// ─── Prevent XSS ──────────────────────────────────────────
// Sanitizes HTML from user input:
app.use(xss());

// ─── Prevent HTTP Parameter Pollution ────────────────────
app.use(hpp({
  whitelist: ["sort", "fields", "page", "limit"] // allow these to be arrays
}));

// ─── Remove sensitive headers ─────────────────────────────
app.disable("x-powered-by");   // hide "X-Powered-By: Express"

// ─── CSRF protection (for cookie-based auth) ──────────────
const csrf = require("csurf");

const csrfProtection = csrf({
  cookie: {
    httpOnly: true,
    secure:   process.env.NODE_ENV === "production",
    sameSite: "strict"
  }
});

// Render form with CSRF token:
app.get("/form", csrfProtection, (req, res) => {
  res.render("form", { csrfToken: req.csrfToken() });
});

// Validate CSRF token on submission:
app.post("/submit", csrfProtection, (req, res) => {
  res.json({ message: "Form submitted successfully!" });
});

// ─── Input sanitization ───────────────────────────────────
function sanitizeString(str) {
  return str
    .trim()
    .replace(/[<>]/g, "")           // remove HTML tags
    .substring(0, 1000);            // limit length
}

function sanitizeObject(obj) {
  const sanitized = {};
  for (const [key, value] of Object.entries(obj)) {
    if (typeof value === "string") {
      sanitized[key] = sanitizeString(value);
    } else if (typeof value === "object" && value !== null) {
      sanitized[key] = sanitizeObject(value);
    } else {
      sanitized[key] = value;
    }
  }
  return sanitized;
}
```

---

## 34. Database Integration

### MongoDB with Mongoose

```bash
npm install mongoose
```

```javascript
// ─── db.js — database connection ─────────────────────────
const mongoose = require("mongoose");

async function connectDB(url = process.env.DATABASE_URL) {
  try {
    await mongoose.connect(url, {
      maxPoolSize:       10,     // connection pool size
      serverSelectionTimeoutMS: 5000,
      socketTimeoutMS:   45000,
      family:            4       // use IPv4
    });

    console.log("✅ Connected to MongoDB");

    mongoose.connection.on("error",      (err) => console.error("DB error:", err));
    mongoose.connection.on("disconnected", ()  => console.warn("DB disconnected"));
    mongoose.connection.on("reconnected",  ()  => console.log("DB reconnected"));
  } catch (err) {
    console.error("❌ DB connection failed:", err.message);
    process.exit(1);
  }
}

async function disconnectDB() {
  await mongoose.disconnect();
  console.log("DB disconnected");
}

module.exports = { connectDB, disconnectDB };

// ─── models/User.js ───────────────────────────────────────
const mongoose = require("mongoose");
const bcrypt   = require("bcryptjs");

const userSchema = new mongoose.Schema(
  {
    name: {
      type:     String,
      required: [true, "Name is required"],
      trim:     true,
      minlength: [2,  "Name must be at least 2 characters"],
      maxlength: [50, "Name cannot exceed 50 characters"]
    },
    email: {
      type:     String,
      required: [true, "Email is required"],
      unique:   true,
      lowercase: true,
      trim:     true,
      match:    [/^\S+@\S+\.\S+$/, "Invalid email format"]
    },
    password: {
      type:     String,
      required: [true, "Password is required"],
      minlength: 8,
      select:   false          // exclude from queries by default
    },
    role: {
      type:    String,
      enum:    ["user", "admin", "moderator"],
      default: "user"
    },
    isActive:    { type: Boolean, default: true },
    avatarUrl:   { type: String },
    lastLoginAt: { type: Date },
    profile: {
      bio:      { type: String, maxlength: 500 },
      location: { type: String },
      website:  { type: String }
    }
  },
  {
    timestamps: true,         // adds createdAt, updatedAt
    versionKey: false,        // removes __v
    toJSON:     { virtuals: true },
    toObject:   { virtuals: true }
  }
);

// ─── Indexes ──────────────────────────────────────────────
userSchema.index({ email: 1 }, { unique: true });
userSchema.index({ createdAt: -1 });
userSchema.index({ role: 1, isActive: 1 });

// ─── Virtuals ─────────────────────────────────────────────
userSchema.virtual("fullUrl").get(function () {
  return `https://example.com/users/${this._id}`;
});

// ─── Pre-save hooks ───────────────────────────────────────
userSchema.pre("save", async function (next) {
  if (!this.isModified("password")) return next();
  const salt    = await bcrypt.genSalt(12);
  this.password = await bcrypt.hash(this.password, salt);
  next();
});

// ─── Instance methods ─────────────────────────────────────
userSchema.methods.comparePassword = async function (plain) {
  return bcrypt.compare(plain, this.password);
};

userSchema.methods.toPublicJSON = function () {
  const obj = this.toObject();
  delete obj.password;
  delete obj.__v;
  return obj;
};

// ─── Static methods ───────────────────────────────────────
userSchema.statics.findByEmail = function (email) {
  return this.findOne({ email: email.toLowerCase() });
};

userSchema.statics.findActiveUsers = function () {
  return this.find({ isActive: true }).select("-password");
};

const User = mongoose.model("User", userSchema);
module.exports = User;

// ─── CRUD Operations ──────────────────────────────────────
// Create:
const user = await User.create({ name: "Alice", email: "alice@mail.com", password: "Secret123" });

// Read:
const all     = await User.find();
const active  = await User.find({ isActive: true });
const one     = await User.findById("64abc...");
const byEmail = await User.findByEmail("alice@mail.com");

// With projection (select fields):
const names   = await User.find().select("name email -_id");

// With population (join):
const posts   = await Post.find().populate("author", "name email");

// Pagination:
const page    = 1, limit = 20;
const users   = await User.find()
  .sort({ createdAt: -1 })
  .skip((page - 1) * limit)
  .limit(limit);

// Count:
const total = await User.countDocuments({ isActive: true });

// Update:
const updated = await User.findByIdAndUpdate(
  id,
  { $set: { name: "Bob" } },
  { new: true, runValidators: true }  // new=return updated doc
);

// Delete:
await User.findByIdAndDelete(id);
await User.deleteMany({ isActive: false });
```

### PostgreSQL with pg

```bash
npm install pg
```

```javascript
const { Pool } = require("pg");

// ─── Connection pool ──────────────────────────────────────
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  max:              10,    // max connections in pool
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
  ssl: process.env.NODE_ENV === "production"
    ? { rejectUnauthorized: false }
    : false
});

pool.on("error", (err) => console.error("DB pool error:", err));

// ─── Query helper ─────────────────────────────────────────
async function query(text, params) {
  const client = await pool.connect();
  try {
    const start  = Date.now();
    const result = await client.query(text, params);
    const duration = Date.now() - start;
    if (duration > 1000) console.warn("Slow query:", text, duration + "ms");
    return result;
  } finally {
    client.release();
  }
}

// ─── Transaction helper ───────────────────────────────────
async function withTransaction(callback) {
  const client = await pool.connect();
  try {
    await client.query("BEGIN");
    const result = await callback(client);
    await client.query("COMMIT");
    return result;
  } catch (err) {
    await client.query("ROLLBACK");
    throw err;
  } finally {
    client.release();
  }
}

// ─── Usage ────────────────────────────────────────────────
// Get all users:
const { rows: users } = await query(
  "SELECT id, name, email, created_at FROM users WHERE is_active = $1 ORDER BY created_at DESC LIMIT $2 OFFSET $3",
  [true, 20, 0]
);

// Get one user:
const { rows: [user] } = await query(
  "SELECT * FROM users WHERE id = $1",
  [userId]
);

// Create user:
const { rows: [created] } = await query(
  "INSERT INTO users (name, email, password_hash) VALUES ($1, $2, $3) RETURNING *",
  [name, email, hashedPassword]
);

// Update:
const { rows: [updated] } = await query(
  "UPDATE users SET name = $1, updated_at = NOW() WHERE id = $2 RETURNING *",
  [name, id]
);

// Transaction example:
const result = await withTransaction(async (client) => {
  const { rows: [order] } = await client.query(
    "INSERT INTO orders (user_id, total) VALUES ($1, $2) RETURNING *",
    [userId, total]
  );

  for (const item of items) {
    await client.query(
      "INSERT INTO order_items (order_id, product_id, quantity, price) VALUES ($1, $2, $3, $4)",
      [order.id, item.productId, item.quantity, item.price]
    );

    await client.query(
      "UPDATE products SET stock = stock - $1 WHERE id = $2 AND stock >= $1",
      [item.quantity, item.productId]
    );
  }

  return order;
});
```

---

## 35. REST API Design & Best Practices

### Project Structure

```
my-api/
├── src/
│   ├── config/
│   │   ├── database.js        # DB connection
│   │   ├── redis.js           # Redis connection
│   │   └── index.js           # All config (env vars)
│   ├── controllers/
│   │   ├── authController.js
│   │   ├── userController.js
│   │   └── postController.js
│   ├── middleware/
│   │   ├── authenticate.js    # JWT auth
│   │   ├── authorize.js       # Role checks
│   │   ├── asyncHandler.js    # Async wrapper
│   │   ├── errorHandler.js    # Global error handler
│   │   ├── logger.js          # Request logging
│   │   ├── rateLimiter.js
│   │   └── validate.js        # Body validation
│   ├── models/
│   │   ├── User.js
│   │   └── Post.js
│   ├── routes/
│   │   ├── index.js           # Aggregate all routes
│   │   ├── auth.js
│   │   ├── users.js
│   │   └── posts.js
│   ├── services/
│   │   ├── emailService.js    # Business logic
│   │   ├── paymentService.js
│   │   └── cacheService.js
│   ├── utils/
│   │   ├── errors.js          # Custom error classes
│   │   ├── helpers.js
│   │   └── validators.js      # Joi schemas
│   ├── app.js                 # Express app setup
│   └── server.js              # HTTP server & startup
├── tests/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── .env
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

### Complete app.js

```javascript
// src/app.js
const express      = require("express");
const cors         = require("cors");
const helmet       = require("helmet");
const compression  = require("compression");
const morgan       = require("morgan");
const cookieParser = require("cookie-parser");
const mongoSanitize = require("express-mongo-sanitize");
const path         = require("path");

const routes       = require("./routes");
const errorHandler = require("./middleware/errorHandler");
const requestId    = require("./middleware/requestId");
const { AppError } = require("./utils/errors");

const app = express();

// ─── Trust proxy (for nginx/load balancer) ────────────────
app.set("trust proxy", 1);

// ─── Security middleware ──────────────────────────────────
app.use(helmet());
app.use(cors({
  origin:      process.env.ALLOWED_ORIGINS?.split(",") ?? "*",
  credentials: true,
  methods:     ["GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS"]
}));
app.options("*", cors());

// ─── Compression ─────────────────────────────────────────
app.use(compression());

// ─── Logging ─────────────────────────────────────────────
if (process.env.NODE_ENV !== "test") {
  app.use(morgan(process.env.NODE_ENV === "development" ? "dev" : "combined"));
}

// ─── Body parsing ─────────────────────────────────────────
app.use(express.json({ limit: "10mb" }));
app.use(express.urlencoded({ extended: true, limit: "10mb" }));
app.use(cookieParser(process.env.COOKIE_SECRET));

// ─── Data sanitization ───────────────────────────────────
app.use(mongoSanitize());

// ─── Request ID ───────────────────────────────────────────
app.use(requestId);

// ─── Static files ─────────────────────────────────────────
app.use("/uploads", express.static(path.join(__dirname, "..", "uploads")));

// ─── Health check ─────────────────────────────────────────
app.get("/health", (req, res) => {
  res.json({
    status: "healthy",
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
    memory: process.memoryUsage()
  });
});

// ─── API Routes ───────────────────────────────────────────
app.use("/api/v1", routes);

// ─── 404 handler ──────────────────────────────────────────
app.use((req, res, next) => {
  next(new AppError(`Cannot ${req.method} ${req.path}`, 404, "NOT_FOUND"));
});

// ─── Global error handler ─────────────────────────────────
app.use(errorHandler);

module.exports = app;
```

### Complete server.js

```javascript
// src/server.js
require("dotenv").config();
const app          = require("./app");
const { connectDB } = require("./config/database");

const PORT = parseInt(process.env.PORT || "3000", 10);
const HOST = process.env.HOST || "0.0.0.0";

async function startServer() {
  try {
    // Connect to database first:
    await connectDB();

    const server = app.listen(PORT, HOST, () => {
      console.log(`🚀 Server running at http://${HOST}:${PORT}`);
      console.log(`📍 Environment: ${process.env.NODE_ENV}`);
      console.log(`📖 Docs: http://${HOST}:${PORT}/api/v1/docs`);
    });

    // Handle server errors:
    server.on("error", (err) => {
      if (err.code === "EADDRINUSE") {
        console.error(`❌ Port ${PORT} is already in use`);
        process.exit(1);
      }
      throw err;
    });

    // Graceful shutdown:
    const shutdown = async (signal) => {
      console.log(`\n${signal} received — shutting down gracefully...`);

      server.close(async () => {
        console.log("HTTP server closed");

        try {
          await mongoose.disconnect();
          console.log("Database disconnected");
        } catch {}

        process.exit(0);
      });

      // Force exit after 30 seconds:
      setTimeout(() => {
        console.error("Forced shutdown after 30s");
        process.exit(1);
      }, 30000);
    };

    process.on("SIGTERM", () => shutdown("SIGTERM"));
    process.on("SIGINT",  () => shutdown("SIGINT"));

    return server;
  } catch (err) {
    console.error("❌ Failed to start server:", err);
    process.exit(1);
  }
}

startServer();
```

### REST API Response Standards

```javascript
// Standard response format:
// Success:
{
  "success": true,
  "data": { ... },           // or array for lists
  "meta": {                  // for paginated lists
    "total": 100,
    "page": 1,
    "limit": 20,
    "pages": 5
  }
}

// Error:
{
  "success": false,
  "error": {
    "code": "NOT_FOUND",
    "message": "User 'abc' not found",
    "fields": { "email": "Invalid email" }   // for validation errors
  }
}

// Response helper:
class ApiResponse {
  static success(res, data, statusCode = 200, meta = null) {
    const response = { success: true, data };
    if (meta) response.meta = meta;
    return res.status(statusCode).json(response);
  }

  static created(res, data) {
    return this.success(res, data, 201);
  }

  static noContent(res) {
    return res.status(204).end();
  }

  static paginated(res, items, { total, page, limit }) {
    return this.success(res, items, 200, {
      total,
      page:  parseInt(page),
      limit: parseInt(limit),
      pages: Math.ceil(total / limit)
    });
  }
}

// Usage:
app.get("/users", asyncHandler(async (req, res) => {
  const { page = 1, limit = 20 } = req.query;
  const total = await User.countDocuments();
  const users = await User.find()
    .skip((page - 1) * limit).limit(parseInt(limit));
  ApiResponse.paginated(res, users, { total, page, limit });
}));

app.post("/users", asyncHandler(async (req, res) => {
  const user = await User.create(req.body);
  ApiResponse.created(res, user);
}));

app.delete("/users/:id", asyncHandler(async (req, res) => {
  await User.findByIdAndDelete(req.params.id);
  ApiResponse.noContent(res);
}));
```

---

## 36. WebSockets with Express

```bash
npm install socket.io
```

```javascript
const express    = require("express");
const http       = require("http");
const { Server } = require("socket.io");

const app    = express();
const server = http.createServer(app);
const io     = new Server(server, {
  cors: {
    origin:      process.env.CLIENT_URL || "http://localhost:3000",
    methods:     ["GET", "POST"],
    credentials: true
  },
  pingTimeout:  60000,   // disconnect after 60s without pong
  pingInterval: 25000    // send ping every 25s
});

// ─── Auth middleware for WebSocket ───────────────────────
io.use(async (socket, next) => {
  try {
    const token = socket.handshake.auth?.token
               || socket.handshake.headers?.authorization?.split(" ")[1];

    if (!token) return next(new Error("Authentication required"));

    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    const user    = await User.findById(decoded.userId);
    if (!user)    return next(new Error("User not found"));

    socket.user = user;
    next();
  } catch {
    next(new Error("Invalid token"));
  }
});

// ─── Namespace: /chat ─────────────────────────────────────
const chatNamespace = io.of("/chat");

chatNamespace.use(/* auth middleware */);

chatNamespace.on("connection", (socket) => {
  const { user } = socket;
  console.log(`User ${user.name} connected — socket: ${socket.id}`);

  // Join a room:
  socket.on("join:room", async ({ roomId }) => {
    await socket.join(roomId);
    socket.to(roomId).emit("user:joined", { user: user.toPublicJSON() });
    console.log(`${user.name} joined room ${roomId}`);
  });

  // Leave a room:
  socket.on("leave:room", async ({ roomId }) => {
    await socket.leave(roomId);
    socket.to(roomId).emit("user:left", { userId: user._id });
  });

  // Send a message:
  socket.on("message:send", async ({ roomId, content }) => {
    try {
      if (!content?.trim()) return;

      // Save to DB:
      const message = await Message.create({
        roomId,
        author:  user._id,
        content: content.trim()
      });

      await message.populate("author", "name avatarUrl");

      // Broadcast to room:
      chatNamespace.to(roomId).emit("message:new", {
        id:        message._id,
        content:   message.content,
        author:    message.author,
        createdAt: message.createdAt
      });
    } catch (err) {
      socket.emit("error", { message: "Failed to send message" });
    }
  });

  // Typing indicator:
  socket.on("typing:start", ({ roomId }) => {
    socket.to(roomId).emit("typing:start", { user: user.name });
  });

  socket.on("typing:stop", ({ roomId }) => {
    socket.to(roomId).emit("typing:stop", { user: user.name });
  });

  // Disconnect:
  socket.on("disconnect", (reason) => {
    console.log(`${user.name} disconnected: ${reason}`);
    // Notify all rooms this user was in:
    socket.rooms.forEach(roomId => {
      if (roomId !== socket.id) {
        chatNamespace.to(roomId).emit("user:offline", { userId: user._id });
      }
    });
  });
});

// ─── Emit from REST endpoint ──────────────────────────────
app.post("/api/notifications", authenticate, asyncHandler(async (req, res) => {
  const { userId, message } = req.body;

  // Emit to specific user's socket:
  chatNamespace.to(`user:${userId}`).emit("notification", { message });

  // Emit to all connected users:
  io.emit("announcement", { message });

  res.json({ sent: true });
}));

server.listen(3000, () => console.log("Server + WebSocket on port 3000"));
```

---

## 37. Testing Node.js & Express

```bash
npm install --save-dev jest supertest @types/jest
```

```javascript
// ─── Unit test: userController ────────────────────────────
// tests/unit/userController.test.js

const userController = require("../../src/controllers/userController");
const User           = require("../../src/models/User");

jest.mock("../../src/models/User");

describe("userController.getOne", () => {
  let req, res, next;

  beforeEach(() => {
    req  = { params: { id: "user_123" } };
    res  = { json: jest.fn(), status: jest.fn().mockReturnThis() };
    next = jest.fn();
  });

  it("returns user when found", async () => {
    const mockUser = { _id: "user_123", name: "Alice", email: "a@mail.com" };
    User.findById.mockResolvedValue(mockUser);

    await userController.getOne(req, res, next);

    expect(User.findById).toHaveBeenCalledWith("user_123");
    expect(res.json).toHaveBeenCalledWith({ success: true, data: mockUser });
  });

  it("calls next with NotFoundError when user missing", async () => {
    User.findById.mockResolvedValue(null);

    await userController.getOne(req, res, next);

    expect(next).toHaveBeenCalled();
    const err = next.mock.calls[0][0];
    expect(err.statusCode).toBe(404);
  });

  it("calls next with error on DB failure", async () => {
    User.findById.mockRejectedValue(new Error("DB error"));

    await userController.getOne(req, res, next);

    expect(next).toHaveBeenCalledWith(expect.any(Error));
  });
});

// ─── Integration test: API routes ─────────────────────────
// tests/integration/users.test.js

const request  = require("supertest");
const mongoose = require("mongoose");
const { app }  = require("../../src/app");
const User     = require("../../src/models/User");

const MONGO_URI = process.env.TEST_DATABASE_URL || "mongodb://localhost:27017/myapp_test";

beforeAll(async () => await mongoose.connect(MONGO_URI));
afterAll(async () => { await mongoose.connection.dropDatabase(); await mongoose.disconnect(); });
afterEach(async () => await User.deleteMany({}));

let authToken;
let testUser;

beforeEach(async () => {
  testUser = await User.create({
    name:     "Alice",
    email:    "alice@test.com",
    password: "Password123",
    role:     "admin"
  });

  const res  = await request(app)
    .post("/api/v1/auth/login")
    .send({ email: "alice@test.com", password: "Password123" });

  authToken = res.body.accessToken;
});

describe("GET /api/v1/users", () => {
  it("returns 401 without auth", async () => {
    const res = await request(app).get("/api/v1/users");
    expect(res.status).toBe(401);
  });

  it("returns users list for admin", async () => {
    const res = await request(app)
      .get("/api/v1/users")
      .set("Authorization", `Bearer ${authToken}`);

    expect(res.status).toBe(200);
    expect(res.body.success).toBe(true);
    expect(Array.isArray(res.body.data)).toBe(true);
    expect(res.body.meta).toHaveProperty("total");
  });
});

describe("POST /api/v1/users", () => {
  it("creates a user", async () => {
    const res = await request(app)
      .post("/api/v1/users")
      .set("Authorization", `Bearer ${authToken}`)
      .send({ name: "Bob", email: "bob@test.com", password: "Password123" });

    expect(res.status).toBe(201);
    expect(res.body.data.email).toBe("bob@test.com");
    expect(res.body.data.password).toBeUndefined();  // never expose password!
  });

  it("returns 400 for invalid data", async () => {
    const res = await request(app)
      .post("/api/v1/users")
      .set("Authorization", `Bearer ${authToken}`)
      .send({ name: "B", email: "not-an-email" }); // invalid

    expect(res.status).toBe(400);
    expect(res.body.error.fields).toHaveProperty("email");
  });

  it("returns 409 for duplicate email", async () => {
    await request(app)
      .post("/api/v1/users")
      .set("Authorization", `Bearer ${authToken}`)
      .send({ name: "Bob", email: "bob@test.com", password: "Password123" });

    const res = await request(app)
      .post("/api/v1/users")
      .set("Authorization", `Bearer ${authToken}`)
      .send({ name: "Bob2", email: "bob@test.com", password: "Password123" });

    expect(res.status).toBe(409);
  });
});

// ─── package.json scripts ─────────────────────────────────
// "test":         "jest --forceExit",
// "test:watch":   "jest --watch",
// "test:coverage":"jest --coverage --forceExit"
```

---

## 38. Deployment & Production

### PM2 Process Manager

```bash
npm install -g pm2

# Start app:
pm2 start src/server.js --name "my-api"

# Cluster mode (uses all CPU cores):
pm2 start src/server.js --name "my-api" -i max

# Ecosystem file (recommended):
pm2 start ecosystem.config.js

# Monitor:
pm2 monit

# Logs:
pm2 logs my-api
pm2 logs --lines 100

# Restart / Reload / Stop:
pm2 restart my-api
pm2 reload my-api    # zero-downtime reload
pm2 stop my-api
pm2 delete my-api

# Auto-start on boot:
pm2 startup
pm2 save
```

```javascript
// ecosystem.config.js
module.exports = {
  apps: [{
    name:          "my-api",
    script:        "src/server.js",
    instances:     "max",         // use all CPU cores
    exec_mode:     "cluster",     // cluster mode
    watch:         false,         // don't watch in production
    max_memory_restart: "1G",     // restart if exceeds 1GB
    env: {
      NODE_ENV:  "development",
      PORT:      3000
    },
    env_production: {
      NODE_ENV:  "production",
      PORT:      8080
    },
    log_date_format: "YYYY-MM-DD HH:mm:ss",
    error_file:  "logs/err.log",
    out_file:    "logs/out.log",
    merge_logs:  true
  }]
};
```

### Docker

```dockerfile
# Dockerfile
FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

FROM node:20-alpine AS production
WORKDIR /app

# Create non-root user:
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001

COPY --from=base /app/node_modules ./node_modules
COPY --chown=nodejs:nodejs . .

USER nodejs
EXPOSE 3000
ENV NODE_ENV=production

HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1

CMD ["node", "src/server.js"]
```

```yaml
# docker-compose.yml
version: "3.9"

services:
  api:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=mongodb://mongo:27017/myapp
      - REDIS_URL=redis://redis:6379
    depends_on:
      mongo:
        condition: service_healthy
      redis:
        condition: service_healthy
    restart: unless-stopped
    volumes:
      - ./uploads:/app/uploads

  mongo:
    image: mongo:7
    volumes:
      - mongo_data:/data/db
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  mongo_data:
  redis_data:
```

### Production Checklist

```javascript
// ─── Production checklist ─────────────────────────────────

// 1. Environment:
// ✅ NODE_ENV=production
// ✅ All secrets in environment variables, never in code
// ✅ .env not committed to git
// ✅ Different .env for each environment

// 2. Security:
// ✅ helmet() for security headers
// ✅ cors() with specific origins
// ✅ Rate limiting on all routes
// ✅ Input validation & sanitization
// ✅ HTTPS only (TLS termination at nginx/load balancer)
// ✅ Passwords hashed with bcrypt
// ✅ JWT secrets are long & random
// ✅ No sensitive data in logs

// 3. Performance:
// ✅ compression() for gzip
// ✅ DB indexes on frequently queried fields
// ✅ Connection pooling for DB
// ✅ Redis for caching & sessions
// ✅ Cluster mode (PM2 or Node cluster)
// ✅ Static files served by nginx (not Express)

// 4. Reliability:
// ✅ Graceful shutdown (close server, disconnect DB)
// ✅ Health check endpoint (/health)
// ✅ process.on("unhandledRejection") handler
// ✅ Auto-restart with PM2 or systemd
// ✅ Logging to files (not just console)

// 5. Monitoring:
// ✅ Error tracking (Sentry, etc.)
// ✅ APM (New Relic, Datadog)
// ✅ Log aggregation (ELK stack, CloudWatch)
// ✅ Uptime monitoring

// ─── Generate strong secrets ──────────────────────────────
const crypto = require("crypto");
console.log(crypto.randomBytes(64).toString("hex")); // use for JWT_SECRET
```

---

## 📊 Quick Reference Cheat Sheet

```
NODE.JS CORE:
  require()          load module (CJS)
  module.exports     export from module
  process.env        environment variables
  process.argv       CLI arguments
  __dirname          current directory
  __filename         current file path
  Buffer.from()      create binary buffer
  EventEmitter       event-driven base class
  stream.pipeline()  safe stream piping

ASYNC PATTERNS:
  callbacks          old style, error-first
  Promises           .then().catch().finally()
  async/await        modern, readable
  Promise.all()      parallel, fail-fast
  Promise.allSettled() parallel, never fails

EXPRESS CORE:
  app.use()          mount middleware / router
  app.get/post/...   define route
  app.route()        chain methods on route
  express.Router()   create modular router
  req.params         route parameters (:id)
  req.query          query string (?key=val)
  req.body           parsed request body
  req.headers        request headers
  req.user           custom (added by auth middleware)
  res.json()         send JSON response
  res.status()       set HTTP status code
  res.send()         send any response
  res.redirect()     redirect to URL
  res.render()       render template
  next()             pass to next middleware
  next(err)          pass to error handler

HTTP STATUS CODES:
  200 OK             success
  201 Created        resource created
  204 No Content     success, no body
  400 Bad Request    invalid input
  401 Unauthorized   not authenticated
  403 Forbidden      not authorized
  404 Not Found      resource not found
  409 Conflict       duplicate / conflict
  422 Unprocessable  validation error
  429 Too Many       rate limited
  500 Server Error   unexpected error

MIDDLEWARE ORDER:
  1. helmet, cors, compression
  2. body parsers (json, urlencoded)
  3. cookies, sessions
  4. request ID, logging
  5. static files
  6. routes
  7. 404 handler
  8. error handler (last!)
```

---

*📗 Node.js & Express.js Complete Reference*
*Happy Building! 🚀*
