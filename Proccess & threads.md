See also:
- **[Process Wikipedia](https://en.wikipedia.org/wiki/Process_(computing))**
- [[Multithreading]]

- A **process** is an instance of a running program.
- It’s like a **container** that holds everything needed for a program to run:
    - Its **own virtual address space** (memory)
    - **Code** (instructions)
    - **Data** (variables, heap, stack)
    - **System resources** (open files, network sockets)
    - Its **own state** or **[[Execution context]]** (registers, program counter, etc.)
- Processes are **independent** from each other. They have isolated memory spaces — so one process can’t accidentally (or intentionally) modify another’s memory.
- In OS terms, a process is the unit of resource allocation and scheduling.
⭐**Analogy:** Think of a process as a **self-contained ship** with its own crew, supplies, and maps.

## What is a **Thread**?

- A **thread** is the smallest unit of CPU execution inside a process.
- A process can have **one or more threads**.
- Threads within the **same process share**:
    - The **same virtual address space** (memory)
    - File descriptors, global variables, heap
- But each thread has its own [[Execution context]]:
    - **CPU registers**
    - **Program counter**
    - **Stack** (for function calls and local variables)
- Threads allow a program to do **multiple things at the same time** (concurrently), like downloading a file while updating the UI.
- Because threads share memory, they are **lighter-weight** than processes but require careful synchronization to avoid conflicts.

**Analogy:** Threads are like **crew members** on the ship working simultaneously on different tasks, sharing the ship’s resources.

## Summary Table

| Aspect            | Process                        | Thread                   |
| ----------------- | ------------------------------ | ------------------------ |
| Memory            | Own virtual address space      | Shared within process    |
| Execution context | Own registers, PC, stack       | Own registers, PC, stack |
| Resource sharing  | No (isolated)                  | Yes (shared resources)   |
| Scheduling unit   | Yes                            | Yes                      |
| Creation overhead | High                           | Low                      |
| Communication     | Via IPC (pipes, sockets, etc.) | Via shared memory (fast) |

---
# Real-life example of a multi-threaded process

- **Web browsers (like Chrome, Firefox):**
    - Each tab can be a separate process, but inside a process, there are multiple threads handling UI, networking, rendering, JavaScript execution, etc.
- **Word processors (e.g., Microsoft Word):**
    - One thread handles user input, another autosaves in background, another spellchecks, etc.
- **Servers (e.g., web servers like Nginx, Apache, NOT node & express):**
    - Multiple threads handle incoming requests in parallel.
### ❓ **Does a Node.js (Express) server use multiple threads if more than one request comes in at the same time?**

**Short Answer:**  
**No**, not in the way traditional multi-threaded servers (like Java or Apache) do.  
Node.js is **single-threaded** in terms of handling JavaScript execution, but it can handle **multiple concurrent requests** efficiently through its **event-driven, non-blocking I/O model**, and it **does use threads internally** for certain operations via the **libuv thread pool**.

---

### 🔍 **Deeper Explanation**

#### 1. **Single-Threaded Event Loop**

- Node.js runs JavaScript code in a **single thread** using an **event loop**.
    
- When you define routes and controllers in Express, they're executed on the **main thread**.
    
- The event loop handles incoming requests one at a time in the sense that it picks them off a queue and invokes your handler code.
    

#### 2. **Non-Blocking I/O and Concurrency**

- I/O operations (like file system access, database calls, or network requests) are handled **asynchronously**.
    
- When you perform something like `fs.readFile()` or make an HTTP call inside a route handler, Node **delegates that to a background thread** from the **libuv thread pool**.
    
- This allows Node.js to **process other requests while waiting** for those I/O operations to complete.
    

#### 3. **libuv Thread Pool (Behind the Scenes)**

- libuv is the C library Node uses under the hood for non-blocking I/O.
    
- It uses a **thread pool** (default 4 threads) for things like:
    
    - File system operations
        
    - DNS lookups
        
    - Some crypto functions
        
- So while your JavaScript is single-threaded, some **blocking tasks are offloaded** to this pool.
    

#### 4. **Multiple Requests at the Same Time?**

- Yes, if two users hit your Express route at the same time, Node.js:
    
    - Queues both requests
        
    - Starts processing the first
        
    - If the first hits a non-blocking async operation (e.g., DB call), Node continues processing the second
        
- This is **concurrent**, but not multi-threaded in the traditional sense.
    

---

### 🧠 Example

```js
app.get('/slow', async (req, res) => {
  const result = await someSlowDbCall(); // async, non-blocking
  res.send(result);
});
```

- If 10 users hit `/slow`, Node.js won’t spin 10 threads.
- It’ll start the first one, offload the DB call, and keep handling others.
- The DB work might run in a separate thread (handled by the DB driver or libuv), but your Express code is still single-threaded.

## ⭐ But if the route is compute heavy synchronous code then:-

### 🧩 Recap: Node.js Execution Model

- **Main thread (Event Loop)** → runs your JavaScript code (Express routes, callbacks, etc.).
    
- **libuv thread pool (default 4 threads)** → used for _I/O-bound_ async operations (like file I/O, DNS lookup, crypto hashing, etc.).
    
- **Event-driven model** → when something is async, Node.js offloads it and keeps serving other requests.
    

---

### 💥 The Problem: CPU-bound, Synchronous Computation

Now, if you have something like this:
```js
app.get('/heavy', (req, res) => {
  // Synchronous CPU-bound work
  const result = doHeavyMathCalculations(); 
  res.send(result);
});
```

and `doHeavyMathCalculations()` is **purely synchronous** and **CPU-heavy** (say, calculating primes, fractals, AI inference, etc.), then:

- That entire computation runs **on the main event loop thread**.
    
- While that’s happening, Node **cannot** process any other incoming requests.
    
- The libuv worker threads **don’t help** here — because your computation is **not an async operation** that’s handed to libuv.
    
- So yes, your server becomes **blocked** until the calculation finishes.
    

❗❗❗⭐ This means that even if 100 users hit `/heavy`, only one will be processed at a time. Others will just **wait** for the event loop to free up.

---
### 🧠 Why libuv threads don’t help here

The libuv thread pool only handles **specific** asynchronous operations — mostly I/O or tasks that have been explicitly implemented as async in Node’s C++ core or native addons.

If you write a plain JS function like:

`function compute() {   for (let i = 0; i < 1e10; i++) Math.sqrt(i); }`

— this executes **entirely on the main thread**.  
libuv isn’t even aware of it.

---
### 🧰 Solutions: How to Handle CPU-Intensive Work

1. **Worker Threads (Built-in since Node 10.5+)**
    
    - These let you run JavaScript in **separate threads**.
        
    - Perfect for CPU-bound work.
```js
const { Worker } = require('node:worker_threads');

app.get('/heavy', (req, res) => {
  const worker = new Worker('./heavyWorker.js');
  worker.once('message', result => res.send(result));
  worker.postMessage('start');
});
```

1. **Child Processes**
    
    - Run your heavy work in a separate Node process.
        
    - Simpler than threads, but with more memory overhead.
        
2. **Cluster Mode**
    
    - Run multiple Node processes (workers) that share the same port.
        
    - Distributes load across CPU cores.
        
    - Still, each process has one event loop — so heavy work in one will block that worker.
        
3. **Offload to External Service**
    
    - Offload the computation to another microservice or queue worker, and just return a pending response.

### ❗ Important note

- Adding the `async` keyword **does not** make a function run in a separate thread.
- Example:
```js
async function doHeavySyncWork() {
  for (let i = 0; i < 1e10; i++) Math.sqrt(i);
}
```
Even though it’s `async`, this still runs **entirely on the main thread**, **synchronously**, until the loop finishes.  
All the `async` keyword does is make the function return a **Promise**.  
Node **does not** offload it to libuv threads unless:

- The function internally calls something _natively asynchronous_, such as:
    
    - `fs.readFile()`
        
    - `dns.lookup()`
        
    - `crypto.pbkdf2()`
        
    - a database driver’s async query
        
- or a custom native addon that delegates to the thread pool.
    

So Node doesn’t “offload everything async automatically” — it offloads _specific built-in async operations_ that are implemented in C++ and registered with libuv.

## Important distinction: Worker Threads vs Child Processes vs libuv Threads

|Feature|libuv Thread Pool|Worker Threads|Child Processes|
|---|---|---|---|
|**Created by**|Node’s internal C++ layer|Your JS code (via `worker_threads`)|Your JS code (via `child_process`)|
|**Purpose**|Handle async _I/O_ and _native_ tasks|Run _JavaScript_ code in parallel threads|Run _separate Node processes_|
|**Memory Space**|Shared (same process)|Shared memory possible (via `SharedArrayBuffer`)|Completely separate memory|
|**Communication**|Internal, hidden from you|Via `postMessage` & events|Via inter-process messages (IPC)|
|**Overhead**|Very low|Moderate|Higher (new process + memory)|
|**Ideal For**|Async I/O (file, crypto, DNS, etc.)|CPU-bound JS computations|Running isolated scripts, services, or tasks|

So in short:

- **libuv threads** → built-in, invisible, for async _I/O_.
    
- **Worker threads** → your way to get _real parallelism_ in JS.
    
- **Child processes** → full isolation, best when tasks are independent or need fault isolation.

---

### 🛠️ Want True Multithreading?

If you want true parallel execution in Node.js, you can use:

- **Worker Threads**: A Node.js module (`worker_threads`) for actual JS multithreading.
- **Child Processes / Clustering**: Forking processes to utilize multiple CPU cores.

---

### ✅ Summary

|Feature|Node.js (Express) Server|
|---|---|
|JavaScript execution model|Single-threaded|
|Handles concurrent requests|Yes|
|True multithreading|No (not by default)|
|Uses background threads|Yes (libuv thread pool)|
|Can be scaled to use threads|Yes (via worker threads)|

---
# What are CPU cores exactly?

- A **CPU core** is an independent processing unit inside a physical CPU chip.
- Each core can execute its own thread/instruction stream **independently** at the same time.
- Modern CPUs have multiple cores (2, 4, 8, 16, or more) to do **true parallelism** — multiple threads/processes running simultaneously.
- Think of CPU cores as **separate workers** in a factory. More cores = more workers = more work done in parallel.

### ❗Important Distinction: Concurrency v/s Parallelism
See [[Multithreading]]
