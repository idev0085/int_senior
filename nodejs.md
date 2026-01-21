# Node.js - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Why Node.js?](#why-nodejs)
2. [Core Concepts](#core-concepts)
3. [Performance & Scalability](#performance--scalability)
4. [Security](#security)
5. [Architecture & Design Patterns](#architecture--design-patterns)
6. [Database & Data Management](#database--data-management)
7. [Deployment & DevOps](#deployment--devops)

---

## Why Node.js?

### Q0: Why should you use Node.js? What problems does it solve?

**Answer:**

Node.js is a **JavaScript runtime built on Chrome's V8 engine** that lets you run JavaScript on the server side. Here's why it's revolutionary:

---

#### What is Node.js?

**Simple Definition:**
Node.js = JavaScript + V8 Engine + Event Loop + C++ Bindings

```
┌─────────────────────────────────┐
│   Your JavaScript Code          │
├─────────────────────────────────┤
│   Node.js APIs (fs, http, etc)  │
├─────────────────────────────────┤
│   V8 JavaScript Engine          │
├─────────────────────────────────┤
│   libuv (Event Loop, I/O)       │
├─────────────────────────────────┤
│   Operating System              │
└─────────────────────────────────┘
```

**Key Components:**
- **V8 Engine**: Compiles JavaScript to machine code (super fast)
- **libuv**: Cross-platform asynchronous I/O library
- **Event Loop**: Non-blocking operations
- **npm**: Largest package ecosystem in the world

---

#### Why Use Node.js? (Top Reasons)

**1. JavaScript Everywhere (Full-Stack JS)**

```javascript
// Same language on frontend and backend!

// Frontend (React)
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(r => r.json())
      .then(setUser);
  }, [userId]);
  
  return <div>{user?.name}</div>;
}

// Backend (Node.js)
app.get('/api/users/:userId', async (req, res) => {
  const user = await User.findById(req.params.userId);
  res.json(user);
});
```

**Benefits:**
- ✅ One language for entire stack
- ✅ Share code between frontend/backend
- ✅ Easier team collaboration
- ✅ Faster development
- ✅ Unified data models and validation

**2. Non-Blocking I/O (Perfect for I/O-Heavy Apps)**

```javascript
// Traditional blocking approach (PHP, Python, Ruby)
// Each request = new thread/process
const user = database.query('SELECT * FROM users'); // WAITS
const posts = database.query('SELECT * FROM posts'); // WAITS
return render(user, posts);

// Node.js non-blocking approach
// Single thread handles thousands of connections
async function handler() {
  // Both queries run in parallel!
  const [user, posts] = await Promise.all([
    database.query('SELECT * FROM users'),
    database.query('SELECT * FROM posts')
  ]);
  return render(user, posts);
}
```

**Why This Matters:**
```
Traditional Server (Apache/PHP):
- 1000 concurrent connections = 1000 threads = ~1GB RAM

Node.js:
- 1000 concurrent connections = 1 thread = ~100MB RAM
- 10x more connections with same resources!
```

**3. Real-Time Applications (WebSockets, Streaming)**

```javascript
// Real-time chat with WebSocket
const io = require('socket.io')(server);

io.on('connection', (socket) => {
  console.log('User connected');
  
  socket.on('message', (msg) => {
    // Broadcast to all connected clients instantly
    io.emit('message', msg);
  });
});

// Streaming data processing
const { pipeline } = require('stream');
const fs = require('fs');

// Process huge files without loading into memory
pipeline(
  fs.createReadStream('huge-file.csv'),
  parseCSV(),
  transformData(),
  fs.createWriteStream('output.json'),
  (err) => {
    if (err) console.error('Pipeline failed', err);
    else console.log('Pipeline succeeded');
  }
);
```

**4. Massive Ecosystem (npm)**

```bash
# npm has over 2 million packages!
npm install express     # Web framework
npm install mongoose    # MongoDB ODM
npm install socket.io   # Real-time communication
npm install jest        # Testing framework
npm install axios       # HTTP client
# ... and 1,999,995 more!
```

**5. High Performance**

```javascript
// V8 engine compiles JS to machine code
// Benchmark: Simple HTTP server

// Node.js can handle:
// - 50,000+ requests/second on single core
// - 1,000,000+ WebSocket connections on single server

const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200);
  res.end('Hello World');
});

server.listen(3000);
// This simple server can handle tens of thousands req/sec!
```

**6. Easy Scalability**

```javascript
// Horizontal scaling with clustering
const cluster = require('cluster');
const os = require('os');

if (cluster.isMaster) {
  // Create worker for each CPU core
  for (let i = 0; i < os.cpus().length; i++) {
    cluster.fork();
  }
} else {
  // Each worker runs the same server
  require('./server');
}

// Microservices architecture
// Each service can be independently scaled
```

**7. Corporate Backing & Community**

- **Created by**: Ryan Dahl (2009)
- **Maintained by**: OpenJS Foundation
- **Backed by**: Google, Microsoft, IBM, Netflix, PayPal
- **GitHub Stars**: 100k+
- **Weekly npm downloads**: 10+ billion

---

#### When to Use Node.js? (Perfect Use Cases)

**✅ BEST For:**

**1. Real-Time Applications**
```javascript
// Chat applications
// Live collaboration (Google Docs-like)
// Gaming servers
// Live streaming
// Trading platforms

io.on('connection', (socket) => {
  socket.on('documentChange', (change) => {
    // Broadcast to all collaborators instantly
    socket.broadcast.emit('documentUpdate', change);
  });
});
```

**2. RESTful APIs & Microservices**
```javascript
// Fast API development
const express = require('express');
const app = express();

app.get('/api/products', async (req, res) => {
  const products = await Product.find();
  res.json(products);
});

app.listen(3000);
// Deploy multiple microservices easily
```

**3. Single Page Applications (SPAs)**
```javascript
// Perfect backend for React, Vue, Angular
// Server-side rendering (Next.js, Nuxt)
// Static site generation

// Next.js example
export async function getServerSideProps() {
  const data = await fetchData();
  return { props: { data } };
}
```

**4. Streaming Applications**
```javascript
// Video streaming (Netflix uses Node.js!)
// File uploads/downloads
// Data processing pipelines

app.get('/video', (req, res) => {
  const stream = fs.createReadStream('movie.mp4');
  stream.pipe(res);
});
```

**5. IoT & Data-Intensive Apps**
```javascript
// Handle thousands of IoT device connections
// Process sensor data in real-time
// Lightweight and fast

mqtt.on('message', (topic, message) => {
  // Process sensor data from thousands of devices
  processSensorData(message);
});
```

**6. Command-Line Tools**
```javascript
// Build CLI tools with Node.js
#!/usr/bin/env node

const program = require('commander');

program
  .version('1.0.0')
  .option('-n, --name <name>', 'Your name')
  .action((options) => {
    console.log(`Hello, ${options.name}!`);
  });

program.parse(process.argv);
```

---

#### When NOT to Use Node.js? (Wrong Use Cases)

**❌ AVOID For:**

**1. CPU-Intensive Operations**

```javascript
// ❌ Bad: Blocks the event loop
app.get('/fibonacci', (req, res) => {
  const result = fibonacci(40); // Blocks everything!
  res.json({ result });
});

// ✅ Better: Use Worker Threads
const { Worker } = require('worker_threads');

app.get('/fibonacci', (req, res) => {
  const worker = new Worker('./fibonacci-worker.js');
  worker.on('message', (result) => {
    res.json({ result });
  });
  worker.postMessage(40);
});

// ✅ Best: Use different language
// Python, Go, Rust, C++ are better for heavy computation
```

**2. Complex Matrix Operations**
```javascript
// ❌ Not ideal for:
// - Machine learning training
// - Video encoding
// - Image processing (heavy)
// - Scientific computing
// - 3D rendering

// Use: Python (NumPy), Go, Rust instead
```

**3. Applications Requiring Threads**
```javascript
// Node.js is single-threaded by default
// Not suitable for:
// - Heavy parallel processing
// - Multi-threaded games
// - CPU-bound batch jobs

// Better alternatives:
// - Go (goroutines)
// - Java (threads)
// - C++ (threads)
```

---

#### Node.js vs Other Technologies

**Node.js vs Python (Django/Flask):**

| Feature | Node.js | Python |
|---------|---------|--------|
| **Speed** | Faster (V8 engine) | Slower (interpreted) |
| **Concurrency** | Event-driven, non-blocking | Multi-threading, blocking |
| **Use Case** | APIs, real-time apps | Data science, ML, scripting |
| **Package Manager** | npm (2M packages) | pip (400K packages) |
| **Syntax** | JavaScript | Python |
| **Learning Curve** | Moderate | Easy |
| **Best For** | Web apps, APIs | ML, data analysis, automation |

```javascript
// Node.js async by default
const users = await User.find();
const posts = await Post.find();

# Python requires explicit async
users = await User.find()
posts = await Post.find()
```

**Node.js vs Java (Spring Boot):**

| Feature | Node.js | Java |
|---------|---------|------|
| **Startup Time** | Fast (~100ms) | Slow (~10s) |
| **Memory** | Low (50-200MB) | High (500MB+) |
| **Development Speed** | Fast | Moderate |
| **Type Safety** | Optional (TypeScript) | Built-in |
| **Enterprise Support** | Good | Excellent |
| **Best For** | Microservices, APIs | Large enterprise apps |

**Node.js vs Go:**

| Feature | Node.js | Go |
|---------|---------|-----|
| **Concurrency** | Event loop | Goroutines |
| **Performance** | Very fast | Faster |
| **Ease of Use** | Easy (JavaScript) | Moderate |
| **Ecosystem** | Massive (npm) | Growing |
| **Use Case** | Web apps | System tools, APIs |
| **Best For** | Full-stack JS | High-performance backend |

```javascript
// Node.js
app.get('/users', async (req, res) => {
  const users = await User.find();
  res.json(users);
});

// Go
func getUsers(w http.ResponseWriter, r *http.Request) {
    users, _ := findUsers()
    json.NewEncoder(w).Encode(users)
}
```

**Node.js vs PHP:**

| Feature | Node.js | PHP |
|---------|---------|-----|
| **Async** | Native | Limited (8.0+) |
| **Performance** | Better | Good |
| **Real-time** | Excellent | Poor |
| **Hosting** | Any VPS | Shared hosting friendly |
| **Learning Curve** | Moderate | Easy |
| **Best For** | Modern apps | CMS, WordPress |

---

#### Real-World Success Stories

**1. Netflix**
- **Challenge**: Slow startup time (40+ seconds)
- **Solution**: Migrated to Node.js
- **Result**: 
  - Startup time reduced to <1 second
  - 70% reduction in code
  - Handles millions of streams

**2. PayPal**
- **Challenge**: Slow Java-based platform
- **Solution**: Rebuilt with Node.js
- **Result**:
  - 2x faster page responses
  - 35% decrease in response time
  - Built in 1/3 the time with fewer developers

**3. Uber**
- **Challenge**: Need to process millions of requests in real-time
- **Solution**: Microservices with Node.js
- **Result**:
  - Handles 2+ million RPM
  - Real-time location tracking
  - Fast API responses

**4. LinkedIn**
- **Challenge**: Ruby on Rails scalability issues
- **Solution**: Migrated mobile backend to Node.js
- **Result**:
  - 10x improvement in performance
  - 2-10x reduction in servers
  - Better user experience

**5. NASA**
- **Challenge**: Data scattered across multiple locations
- **Solution**: Node.js microservices
- **Result**:
  - Unified data access
  - 300% improvement in access time
  - Safer astronaut missions

---

#### Companies Using Node.js

**Tech Giants:**
- Netflix, PayPal, LinkedIn, Uber, eBay
- NASA, Twitter, Medium, Trello
- GoDaddy, Walmart, Mozilla

**Why They Chose Node.js:**
1. Handle millions of concurrent connections
2. Real-time data processing
3. Microservices architecture
4. Fast development cycles
5. JavaScript everywhere

---

#### Key Advantages Summary

**Technical Benefits:**
```javascript
// 1. Async/Non-blocking by default
const [users, posts, comments] = await Promise.all([
  getUsers(),
  getPosts(),
  getComments()
]); // All run in parallel!

// 2. Event-driven architecture
emitter.on('userCreated', (user) => {
  sendWelcomeEmail(user);
  logActivity(user);
  updateAnalytics(user);
});

// 3. Streaming support
fs.createReadStream('large-file.txt')
  .pipe(transformer)
  .pipe(fs.createWriteStream('output.txt'));

// 4. Single-threaded but highly concurrent
// Handles 10,000+ concurrent connections easily

// 5. Fast development
// npm install any-package
// No compilation needed (JIT with V8)
```

**Business Benefits:**
- ✅ **Cost-effective**: Less servers needed
- ✅ **Fast time-to-market**: Rapid development
- ✅ **Talent pool**: JavaScript developers everywhere
- ✅ **Code reuse**: Share code between frontend/backend
- ✅ **Community**: Huge ecosystem and support

---

#### Common Misconceptions

**❌ Myth 1: "Node.js is single-threaded, so it's slow"**
```javascript
// ✅ Truth: Single-threaded event loop, but:
// - I/O operations are async (non-blocking)
// - Can use Worker Threads for CPU work
// - Can handle more connections than multi-threaded servers

// Example: 1 Node.js process > 10 PHP processes
```

**❌ Myth 2: "Node.js is only for small apps"**
```javascript
// ✅ Truth: Used by Netflix, PayPal, NASA
// - Microservices scale independently
// - Clustering for multi-core support
// - Proven at massive scale
```

**❌ Myth 3: "Callback hell makes code unreadable"**
```javascript
// ❌ Old way (2012)
getData(function(data) {
  processData(data, function(result) {
    saveResult(result, function() {
      console.log('Done!');
    });
  });
});

// ✅ Modern way (2024+)
const data = await getData();
const result = await processData(data);
await saveResult(result);
console.log('Done!');
```

**❌ Myth 4: "No types = bad for large projects"**
```typescript
// ✅ Truth: Use TypeScript!
interface User {
  id: number;
  name: string;
  email: string;
}

async function getUser(id: number): Promise<User> {
  return await User.findById(id);
}
// Full type safety + JavaScript ecosystem
```

---

#### Quick Decision Guide

**Choose Node.js if you need:**
```
✅ Real-time features (chat, live updates)
✅ RESTful APIs
✅ Microservices
✅ Single Page Applications backend
✅ Data streaming
✅ High I/O operations
✅ JavaScript everywhere
✅ Fast development
✅ Large developer community
```

**Choose something else if you need:**
```
❌ Heavy CPU operations (use Go, Rust, C++)
❌ Machine learning (use Python)
❌ Enterprise with strong typing (use Java, C#)
❌ Systems programming (use Rust, C++)
❌ Extreme performance (use Go, Rust)
```

---

#### Getting Started Example

**Complete REST API in 20 lines:**

```javascript
// app.js
const express = require('express');
const app = express();

app.use(express.json());

// In-memory database
let users = [{ id: 1, name: 'John' }];

// Get all users
app.get('/users', (req, res) => {
  res.json(users);
});

// Create user
app.post('/users', (req, res) => {
  const user = { id: Date.now(), ...req.body };
  users.push(user);
  res.status(201).json(user);
});

app.listen(3000, () => {
  console.log('API running on http://localhost:3000');
});
```

**Run it:**
```bash
npm install express
node app.js

# Test it
curl http://localhost:3000/users
curl -X POST http://localhost:3000/users -H "Content-Type: application/json" -d '{"name":"Jane"}'
```

---

#### Summary: Why Node.js?

**The Bottom Line:**

Node.js is perfect when you need:
1. **JavaScript everywhere** (frontend + backend)
2. **High I/O throughput** (thousands of connections)
3. **Real-time features** (WebSockets, streaming)
4. **Fast development** (huge npm ecosystem)
5. **Modern async patterns** (Promise, async/await)
6. **Scalable microservices** (easy to deploy and scale)

**Choose Node.js for**: Web apps, APIs, real-time apps, microservices, IoT
**Avoid Node.js for**: Heavy computation, CPU-intensive tasks, complex ML

**Remember:**
> "Node.js doesn't make things possible that weren't possible before. It makes things easy that were hard before." - Ryan Dahl (Creator of Node.js)

---

## Core Concepts

### Q1: Explain the Event Loop in Node.js. How does it handle asynchronous operations?

**Answer:**

The Event Loop is the core mechanism that makes JavaScript **single-threaded yet asynchronous**. It's what allows Node.js to handle thousands of concurrent operations without creating multiple threads.

---

#### What is the Event Loop?

**Definition:**
The Event Loop is a continuous process that monitors the **Call Stack** and **Task Queues**. When the Call Stack is empty, it takes tasks from the queues and executes them.

**Think of it like a restaurant:**
- **Call Stack** = Chef cooking (one dish at a time, single-threaded)
- **Task Queue** = Orders waiting to be cooked
- **Event Loop** = Manager checking if chef is free, then giving next order
- **Web APIs** = Kitchen equipment (oven, microwave) that can work independently

---

#### JavaScript Runtime Components

```
┌─────────────────────────────────────────────────────────┐
│                    JavaScript Engine                     │
│  ┌──────────────┐           ┌─────────────────────────┐ │
│  │  Call Stack  │           │        Heap             │ │
│  │              │           │  (Memory Allocation)    │ │
│  │  ┌────────┐  │           └─────────────────────────┘ │
│  │  │ main() │  │                                         │
│  │  └────────┘  │                                         │
│  └──────────────┘                                         │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                      Event Loop                          │
└─────────────────────────────────────────────────────────┘
                           │
        ┌──────────────────┼──────────────────┐
        ▼                  ▼                  ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   Web APIs   │  │  Microtask   │  │  Macrotask   │
│              │  │    Queue     │  │    Queue     │
│ • setTimeout │  │              │  │              │
│ • fetch()    │  │ • Promises   │  │ • setTimeout │
│ • fs.readFile│  │ • queueMicro │  │ • setInterval│
└──────────────┘  └──────────────┘  └──────────────┘
```

---

#### How the Event Loop Works

**Step-by-Step Process:**

```javascript
console.log('1. Start');              // STEP 1: Add to Call Stack → Execute

setTimeout(() => {                    // STEP 2: Web API handles timer
  console.log('5. Timeout');          // → Goes to Macrotask Queue after 0ms
}, 0);

Promise.resolve().then(() => {        // STEP 3: Promise resolves immediately
  console.log('4. Promise');          // → Goes to Microtask Queue
});

console.log('2. End');                // STEP 4: Add to Call Stack → Execute

// Now Call Stack is empty!
// Event Loop checks queues:
// 1. Microtask Queue first → executes Promise (4.)
// 2. Macrotask Queue next → executes setTimeout (5.)

// OUTPUT ORDER:
// 1. Start
// 2. End
// 4. Promise
// 5. Timeout
```

**Detailed Execution Flow:**

```javascript
// INITIAL STATE
Call Stack: [main()]
Microtask Queue: []
Macrotask Queue: []

// STEP 1: console.log('1. Start')
Call Stack: [main(), console.log]
Output: "1. Start"
Call Stack: [main()]

// STEP 2: setTimeout(callback, 0)
Call Stack: [main(), setTimeout]
// Timer registered with Web API
Call Stack: [main()]
// After 0ms: Macrotask Queue: [timeout callback]

// STEP 3: Promise.resolve().then(callback)
Call Stack: [main(), Promise.resolve().then]
// Promise resolves immediately
Microtask Queue: [promise callback]
Call Stack: [main()]

// STEP 4: console.log('2. End')
Call Stack: [main(), console.log]
Output: "2. End"
Call Stack: [main()]

// STEP 5: main() finishes
Call Stack: []

// EVENT LOOP CHECKS QUEUES:

// STEP 6: Process Microtask Queue (PRIORITY!)
Call Stack: [promise callback]
Output: "4. Promise"
Call Stack: []

// STEP 7: Process Macrotask Queue
Call Stack: [timeout callback]
Output: "5. Timeout"
Call Stack: []

// DONE!
```

---

#### Microtasks vs Macrotasks

**Microtasks (Higher Priority):**
- ✅ Executed immediately after current script
- ✅ Run before any Macrotask
- ✅ All Microtasks run before next Macrotask

**Examples:**
```javascript
// Microtasks
Promise.resolve().then(callback)
Promise.reject().catch(callback)
queueMicrotask(callback)
async/await
MutationObserver (Browser only)
process.nextTick (Node.js - highest priority!)
```

**Macrotasks (Lower Priority):**
- ⏰ Executed one per Event Loop iteration
- ⏰ Includes timers, I/O, UI rendering

**Examples:**
```javascript
// Macrotasks
setTimeout(callback, delay)
setInterval(callback, delay)
setImmediate(callback)  // Node.js only
I/O operations (fs.readFile, http requests)
UI rendering (Browser only)
```

**Priority Order:**

```javascript
// Demonstrate priority
console.log('1. Sync');

setTimeout(() => console.log('5. Timeout'), 0);

Promise.resolve().then(() => console.log('3. Promise'));

queueMicrotask(() => console.log('4. queueMicrotask'));

process.nextTick(() => console.log('2. nextTick')); // Node.js only

console.log('6. Sync End');

// OUTPUT:
// 1. Sync
// 6. Sync End
// 2. nextTick (Node.js - highest priority microtask)
// 3. Promise
// 4. queueMicrotask
// 5. Timeout
```

---

#### Node.js Event Loop Phases

Node.js has a more detailed Event Loop with **6 phases**:

```
   ┌───────────────────────────┐
┌─>│        1. timers          │  setTimeout, setInterval
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │  2. pending callbacks     │  I/O callbacks deferred
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │   3. idle, prepare        │  Internal use only
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       4. poll             │  ← Most important!
│  │   • Retrieve new I/O      │     Retrieve I/O events
│  │   • Execute I/O callbacks │     Execute callbacks
│  │   • Wait for events       │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       5. check            │  setImmediate callbacks
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │   6. close callbacks      │  socket.on('close')
│  └───────────────────────────┘
└────────────────────────────────
│
▼
Between each phase: Microtask Queue is checked!
```

**Phase Details:**

```javascript
const fs = require('fs');

// 1. TIMERS - setTimeout/setInterval callbacks
setTimeout(() => console.log('timer'), 0);

// 2. PENDING CALLBACKS - I/O operations
fs.readFile('file.txt', () => {
  console.log('I/O callback');
  
  // 5. CHECK - setImmediate (after I/O)
  setImmediate(() => console.log('immediate in I/O'));
  
  // 1. TIMERS - setTimeout (next loop)
  setTimeout(() => console.log('timeout in I/O'), 0);
});

// 5. CHECK - setImmediate
setImmediate(() => console.log('immediate'));

// OUTPUT (typically):
// timer
// immediate
// I/O callback
// immediate in I/O
// timeout in I/O
```

---

#### setImmediate vs setTimeout vs process.nextTick

**Differences:**

```javascript
// TEST: Which executes first?

setTimeout(() => console.log('1. setTimeout 0'), 0);
setImmediate(() => console.log('2. setImmediate'));
process.nextTick(() => console.log('3. nextTick'));
Promise.resolve().then(() => console.log('4. Promise'));

// OUTPUT:
// 3. nextTick      (highest priority microtask)
// 4. Promise       (microtask)
// 1. setTimeout 0  (depends on system timer precision)
// 2. setImmediate  (depends on system timer precision)

// NOTE: setTimeout 0 vs setImmediate order is non-deterministic
// EXCEPT when inside I/O cycle - then setImmediate is always first
```

**Inside I/O Callback:**

```javascript
const fs = require('fs');

fs.readFile('file.txt', () => {
  setTimeout(() => console.log('1. timeout'), 0);
  setImmediate(() => console.log('2. immediate'));
});

// OUTPUT (guaranteed):
// 2. immediate  (CHECK phase - after POLL phase)
// 1. timeout    (TIMERS phase - next loop)
```

**Use Cases:**

```javascript
// Use process.nextTick for:
// - Executing callback ASAP (before I/O)
// - Allowing constructors to complete before emitting events
process.nextTick(() => {
  console.log('Execute this ASAP');
});

// Use setImmediate for:
// - Deferring execution until after I/O events
// - Breaking up long-running operations
setImmediate(() => {
  console.log('Execute after I/O events');
});

// Use setTimeout for:
// - Delaying execution by specific time
// - Rate limiting
setTimeout(() => {
  console.log('Execute after delay');
}, 1000);
```

---

#### Common Event Loop Patterns

**1. Blocking vs Non-Blocking:**

```javascript
// ❌ BAD: Blocks Event Loop
function computePrimes(max) {
  const primes = [];
  for (let i = 2; i < max; i++) {
    let isPrime = true;
    for (let j = 2; j < i; j++) {
      if (i % j === 0) {
        isPrime = false;
        break;
      }
    }
    if (isPrime) primes.push(i);
  }
  return primes;
}

// This blocks ALL other operations!
const primes = computePrimes(1000000);

// ✅ GOOD: Use Worker Threads for CPU-intensive tasks
const { Worker } = require('worker_threads');

function computePrimesAsync(max) {
  return new Promise((resolve, reject) => {
    const worker = new Worker('./prime-worker.js');
    worker.postMessage(max);
    worker.on('message', resolve);
    worker.on('error', reject);
  });
}

// Other operations continue!
computePrimesAsync(1000000).then(primes => {
  console.log('Primes computed:', primes.length);
});
```

**2. Recursive setTimeout (Better than setInterval):**

```javascript
// ❌ setInterval: Doesn't wait for async operation
setInterval(async () => {
  await fetchData(); // Takes 2 seconds
  // If fetchData is slow, intervals stack up!
}, 1000);

// ✅ Recursive setTimeout: Waits for completion
async function poll() {
  try {
    await fetchData();
    setTimeout(poll, 1000); // Schedule next after completion
  } catch (err) {
    console.error(err);
    setTimeout(poll, 5000); // Retry after error
  }
}
poll();
```

**3. Avoiding Microtask Queue Starvation:**

```javascript
// ❌ BAD: Starves macrotask queue
function recursiveMicrotask() {
  Promise.resolve().then(() => {
    console.log('Microtask');
    recursiveMicrotask(); // Never allows macrotasks to run!
  });
}

// ✅ GOOD: Give macrotasks a chance
function recursiveMacrotask() {
  console.log('Macrotask');
  setImmediate(recursiveMacrotask); // Allows other tasks between
}
```

---

#### Event Loop Visualization

**Complete Example:**

```javascript
console.log('🔵 1. Script Start');

setTimeout(() => {
  console.log('🟠 8. setTimeout 1');
  
  Promise.resolve().then(() => {
    console.log('🟢 9. Promise in setTimeout');
  });
}, 0);

setTimeout(() => {
  console.log('🟠 10. setTimeout 2');
}, 0);

Promise.resolve()
  .then(() => {
    console.log('🟢 4. Promise 1');
    return Promise.resolve();
  })
  .then(() => {
    console.log('🟢 5. Promise 2');
  });

Promise.resolve().then(() => {
  console.log('🟢 6. Promise 3');
  
  setTimeout(() => {
    console.log('🟠 11. setTimeout in Promise');
  }, 0);
});

async function asyncFunc() {
  console.log('🔵 2. Async Start');
  await Promise.resolve();
  console.log('🟢 7. After await');
}

asyncFunc();

console.log('🔵 3. Script End');

// EXECUTION ORDER:
// 🔵 1. Script Start         (Sync)
// 🔵 2. Async Start          (Sync before await)
// 🔵 3. Script End           (Sync)
// --- Call Stack Empty → Check Microtask Queue ---
// 🟢 4. Promise 1            (Microtask)
// 🟢 5. Promise 2            (Microtask)
// 🟢 6. Promise 3            (Microtask)
// 🟢 7. After await          (Microtask)
// --- Microtask Queue Empty → Check Macrotask Queue ---
// 🟠 8. setTimeout 1         (Macrotask)
// --- Check Microtask Queue (created by setTimeout) ---
// 🟢 9. Promise in setTimeout (Microtask)
// --- Continue Macrotask Queue ---
// 🟠 10. setTimeout 2        (Macrotask)
// 🟠 11. setTimeout in Promise (Macrotask)
```

---

#### Debugging the Event Loop

**1. Detect Blocking Code:**

```javascript
// Monitor event loop lag
const start = Date.now();

setInterval(() => {
  const lag = Date.now() - start - 1000;
  if (lag > 100) {
    console.warn(`⚠️ Event Loop Lag: ${lag}ms`);
  }
}, 1000);

// If lag is high, something is blocking!
```

**2. Measure Async Performance:**

```javascript
const { performance } = require('perf_hooks');

async function measureAsync() {
  const start = performance.now();
  
  await someAsyncOperation();
  
  const end = performance.now();
  console.log(`Operation took: ${end - start}ms`);
}
```

**3. Use Node.js Built-in Diagnostics:**

```javascript
// Check event loop utilization (Node.js 14+)
const { performance } = require('perf_hooks');

setInterval(() => {
  const utilization = performance.eventLoopUtilization();
  console.log('Event Loop Utilization:', {
    active: (utilization.utilization * 100).toFixed(2) + '%',
    idle: ((1 - utilization.utilization) * 100).toFixed(2) + '%'
  });
}, 5000);
```

---

#### Common Pitfalls

**1. Blocking the Event Loop:**

```javascript
// ❌ BAD: Synchronous heavy computation
app.get('/heavy', (req, res) => {
  const result = heavySync(); // BLOCKS all other requests!
  res.json(result);
});

// ✅ GOOD: Use worker threads
app.get('/heavy', async (req, res) => {
  const result = await heavyAsync(); // Other requests continue
  res.json(result);
});
```

**2. Unhandled Promise Rejections:**

```javascript
// ❌ BAD: Silent failure
Promise.reject('Error'); // Might not be caught!

// ✅ GOOD: Always handle rejections
Promise.reject('Error').catch(err => console.error(err));

// ✅ BETTER: Global handler
process.on('unhandledRejection', (reason, promise) => {
  console.error('Unhandled Rejection:', reason);
});
```

**3. Memory Leaks with Timers:**

```javascript
// ❌ BAD: Timer not cleared
function startPolling() {
  setInterval(() => {
    fetchData(); // Runs forever!
  }, 1000);
}

// ✅ GOOD: Clear timer when done
function startPolling() {
  const timerId = setInterval(() => {
    fetchData();
  }, 1000);
  
  return () => clearInterval(timerId); // Return cleanup function
}

const stopPolling = startPolling();
// Later...
stopPolling(); // Clean up
```

---

#### Best Practices

**1. Keep Operations Non-Blocking:**

```javascript
// ✅ Use async APIs
const fs = require('fs').promises;
const data = await fs.readFile('file.txt', 'utf8');

// ❌ Avoid sync APIs in production
const data = fs.readFileSync('file.txt', 'utf8');
```

**2. Use Appropriate Task Scheduling:**

```javascript
// For immediate execution (before I/O):
process.nextTick(callback);

// For execution after I/O:
setImmediate(callback);

// For delayed execution:
setTimeout(callback, delay);

// For promise-based:
Promise.resolve().then(callback);
```

**3. Avoid Deep Recursion:**

```javascript
// ❌ BAD: Stack overflow
function recursive(n) {
  if (n === 0) return;
  recursive(n - 1);
}
recursive(100000); // Stack overflow!

// ✅ GOOD: Use setImmediate to yield
function recursive(n) {
  if (n === 0) return;
  setImmediate(() => recursive(n - 1));
}
recursive(100000); // Works fine!
```

---

#### Summary

**Key Concepts:**

| Concept | Description |
|---------|-------------|
| **Call Stack** | Where code executes (LIFO - Last In, First Out) |
| **Event Loop** | Monitors stack and queues, moves tasks to stack |
| **Microtask Queue** | High priority (Promises, process.nextTick) |
| **Macrotask Queue** | Lower priority (setTimeout, I/O, setImmediate) |
| **Web APIs** | Handle async operations (timers, I/O, fetch) |

**Execution Priority:**

```
1. Call Stack (Synchronous code)
2. Microtask Queue (process.nextTick → Promises)
3. Macrotask Queue (setTimeout → setImmediate)
4. Repeat
```

**Remember:**
- ✅ JavaScript is single-threaded
- ✅ Event Loop enables concurrency
- ✅ Microtasks run before Macrotasks
- ✅ Never block the Event Loop
- ✅ Use Worker Threads for CPU-intensive tasks
- ✅ Always handle Promise rejections

**Quick Test:**

```javascript
// What's the output order?
console.log('A');
setTimeout(() => console.log('B'), 0);
Promise.resolve().then(() => console.log('C'));
console.log('D');

// Answer: A, D, C, B
// (Sync → Sync → Microtask → Macrotask)
```

---

### Q1.2: What is Promise Chaining in JavaScript?

**Answer:**

**Promise chaining** is a technique to execute multiple asynchronous operations **in sequence**, where each operation starts after the previous one completes. It's cleaner than nested callbacks and avoids "callback hell".

---

#### What are Promises?

**Definition:**
A **Promise** is an object representing the eventual completion (or failure) of an asynchronous operation and its resulting value.

**Promise States:**

```javascript
// Three states of a Promise
const promise = new Promise((resolve, reject) => {
  // PENDING → initial state
  
  setTimeout(() => {
    const success = true;
    
    if (success) {
      resolve('Success!');  // → FULFILLED
    } else {
      reject('Error!');     // → REJECTED
    }
  }, 1000);
});

// PENDING: Neither fulfilled nor rejected
// FULFILLED: Operation completed successfully
// REJECTED: Operation failed
```

**Visual Representation:**

```
┌─────────┐
│ PENDING │  Initial state
└────┬────┘
     │
     ├──────────────┬──────────────┐
     │              │              │
     ▼              ▼              ▼
┌──────────┐  ┌──────────┐  ┌──────────┐
│FULFILLED │  │ REJECTED │  │  PENDING │
│(Success) │  │ (Error)  │  │(Timeout) │
└──────────┘  └──────────┘  └──────────┘
     │              │
     ▼              ▼
  .then()       .catch()
```

---

#### Basic Promise Usage

**Creating a Promise:**

```javascript
// Simple Promise
const myPromise = new Promise((resolve, reject) => {
  const success = true;
  
  if (success) {
    resolve('Operation successful!');
  } else {
    reject('Operation failed!');
  }
});

// Using the Promise
myPromise
  .then(result => {
    console.log(result); // "Operation successful!"
  })
  .catch(error => {
    console.error(error);
  });
```

**Real-World Example - Fetch User:**

```javascript
function fetchUser(userId) {
  return new Promise((resolve, reject) => {
    // Simulate API call
    setTimeout(() => {
      if (userId > 0) {
        resolve({ id: userId, name: 'John Doe' });
      } else {
        reject(new Error('Invalid user ID'));
      }
    }, 1000);
  });
}

// Usage
fetchUser(1)
  .then(user => console.log('User:', user))
  .catch(error => console.error('Error:', error.message));
```

---

#### Promise Chaining Basics

**Why Chain Promises?**

```javascript
// ❌ BAD: Callback Hell (Pyramid of Doom)
getUserId(username, (err, userId) => {
  if (err) return handleError(err);
  
  getUserData(userId, (err, userData) => {
    if (err) return handleError(err);
    
    getUserPosts(userId, (err, posts) => {
      if (err) return handleError(err);
      
      getPostComments(posts[0].id, (err, comments) => {
        if (err) return handleError(err);
        console.log(comments);
      });
    });
  });
});

// ✅ GOOD: Promise Chaining (Linear and Readable)
getUserId(username)
  .then(userId => getUserData(userId))
  .then(userData => getUserPosts(userData.id))
  .then(posts => getPostComments(posts[0].id))
  .then(comments => console.log(comments))
  .catch(error => handleError(error));
```

**How Chaining Works:**

```javascript
// Each .then() returns a new Promise
const promise1 = fetch('/api/user/1');                    // Promise<Response>

const promise2 = promise1.then(res => res.json());        // Promise<User>

const promise3 = promise2.then(user => user.posts);       // Promise<Posts[]>

const promise4 = promise3.then(posts => posts.length);    // Promise<number>

// Chained version (same thing)
fetch('/api/user/1')
  .then(res => res.json())           // Return value becomes next .then()'s input
  .then(user => user.posts)
  .then(posts => posts.length)
  .then(count => console.log(count))
  .catch(err => console.error(err));
```

---

#### Promise Chaining Rules

**Rule 1: Always Return**

```javascript
// ❌ BAD: Not returning the Promise
fetchUser(1)
  .then(user => {
    fetchPosts(user.id); // Missing return!
  })
  .then(posts => {
    console.log(posts); // undefined! Previous .then() didn't return
  });

// ✅ GOOD: Return the Promise
fetchUser(1)
  .then(user => {
    return fetchPosts(user.id); // Return the Promise
  })
  .then(posts => {
    console.log(posts); // Works correctly!
  });

// ✅ BETTER: Implicit return with arrow function
fetchUser(1)
  .then(user => fetchPosts(user.id)) // Implicit return
  .then(posts => console.log(posts));
```

**Rule 2: Return Values Pass to Next .then()**

```javascript
Promise.resolve(5)
  .then(num => {
    console.log(num); // 5
    return num * 2;   // Return value
  })
  .then(num => {
    console.log(num); // 10 (value from previous .then())
    return num + 3;
  })
  .then(num => {
    console.log(num); // 13
  });
```

**Rule 3: Errors Propagate Down the Chain**

```javascript
fetchUser(1)
  .then(user => fetchPosts(user.id))
  .then(posts => fetchComments(posts[0].id))
  .then(comments => {
    throw new Error('Something went wrong!');
  })
  .then(result => {
    console.log('This will NOT run'); // Skipped!
  })
  .catch(error => {
    console.error('Caught:', error.message); // Catches error from any .then()
  });
```

---

#### Real-World Example: Complete User Flow

```javascript
// API functions that return Promises
function login(username, password) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (password === 'correct') {
        resolve({ token: 'abc123', userId: 1 });
      } else {
        reject(new Error('Invalid credentials'));
      }
    }, 500);
  });
}

function fetchUserProfile(token, userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (token) {
        resolve({ id: userId, name: 'John', email: 'john@example.com' });
      } else {
        reject(new Error('Unauthorized'));
      }
    }, 500);
  });
}

function fetchUserPosts(userId) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve([
        { id: 1, title: 'Post 1', userId },
        { id: 2, title: 'Post 2', userId }
      ]);
    }, 500);
  });
}

function enrichPostsWithComments(posts) {
  return new Promise((resolve) => {
    setTimeout(() => {
      const enriched = posts.map(post => ({
        ...post,
        comments: ['Comment 1', 'Comment 2']
      }));
      resolve(enriched);
    }, 500);
  });
}

// Promise Chain
login('john', 'correct')
  .then(authData => {
    console.log('✅ Logged in with token:', authData.token);
    return fetchUserProfile(authData.token, authData.userId);
  })
  .then(profile => {
    console.log('✅ Profile loaded:', profile.name);
    return fetchUserPosts(profile.id);
  })
  .then(posts => {
    console.log('✅ Posts loaded:', posts.length);
    return enrichPostsWithComments(posts);
  })
  .then(enrichedPosts => {
    console.log('✅ Complete data:', enrichedPosts);
  })
  .catch(error => {
    console.error('❌ Error:', error.message);
  })
  .finally(() => {
    console.log('🏁 Process completed');
  });

// OUTPUT:
// ✅ Logged in with token: abc123
// ✅ Profile loaded: John
// ✅ Posts loaded: 2
// ✅ Complete data: [{ id: 1, title: 'Post 1', comments: [...] }, ...]
// 🏁 Process completed
```

---

#### Advanced Chaining Patterns

**1. Passing Multiple Values:**

```javascript
// ❌ Problem: Can't access previous values
fetchUser(1)
  .then(user => fetchPosts(user.id))
  .then(posts => {
    // How do I access user here? 😕
  });

// ✅ Solution 1: Return object with accumulated data
fetchUser(1)
  .then(user => {
    return fetchPosts(user.id)
      .then(posts => ({ user, posts })); // Return both
  })
  .then(({ user, posts }) => {
    console.log('User:', user.name);
    console.log('Posts:', posts.length);
  });

// ✅ Solution 2: Use Promise.all()
fetchUser(1)
  .then(user => {
    return Promise.all([
      user,                    // Pass user forward
      fetchPosts(user.id)      // Fetch posts
    ]);
  })
  .then(([user, posts]) => {
    console.log('User:', user.name);
    console.log('Posts:', posts.length);
  });

// ✅ Solution 3: Use closure (variable in outer scope)
let user;
fetchUser(1)
  .then(u => {
    user = u; // Store in outer scope
    return fetchPosts(user.id);
  })
  .then(posts => {
    console.log('User:', user.name); // Access from closure
    console.log('Posts:', posts.length);
  });
```

**2. Conditional Chaining:**

```javascript
function processUser(userId) {
  return fetchUser(userId)
    .then(user => {
      if (user.isPremium) {
        return fetchPremiumContent(user.id); // Premium path
      } else {
        return fetchFreeContent(user.id);    // Free path
      }
    })
    .then(content => {
      console.log('Content:', content);
    });
}
```

**3. Nested vs Flat Chaining:**

```javascript
// ❌ BAD: Nested Promises (defeats the purpose!)
fetchUser(1)
  .then(user => {
    fetchPosts(user.id)
      .then(posts => {
        fetchComments(posts[0].id)
          .then(comments => {
            console.log(comments); // Back to callback hell!
          });
      });
  });

// ✅ GOOD: Flat chain
fetchUser(1)
  .then(user => fetchPosts(user.id))
  .then(posts => fetchComments(posts[0].id))
  .then(comments => console.log(comments));
```

**4. Chaining with Transformation:**

```javascript
fetch('/api/users')
  .then(response => {
    console.log('Status:', response.status);
    return response.json(); // Transform Response → JSON
  })
  .then(data => {
    console.log('Data:', data);
    return data.users; // Extract users array
  })
  .then(users => {
    console.log('Users:', users.length);
    return users.filter(u => u.active); // Filter active users
  })
  .then(activeUsers => {
    console.log('Active users:', activeUsers.length);
    return activeUsers.map(u => u.email); // Extract emails
  })
  .then(emails => {
    console.log('Emails:', emails);
  });
```

---

#### Error Handling in Chains

**Single .catch() at End:**

```javascript
// Catches errors from any step
fetchUser(1)
  .then(user => fetchPosts(user.id))        // Error here? → goes to .catch()
  .then(posts => fetchComments(posts[0].id)) // Error here? → goes to .catch()
  .then(comments => console.log(comments))   // Error here? → goes to .catch()
  .catch(error => {
    console.error('Error at some step:', error);
  });
```

**Multiple .catch() for Different Steps:**

```javascript
fetchUser(1)
  .catch(error => {
    console.error('User fetch failed:', error);
    return { id: 0, name: 'Guest' }; // Provide default user
  })
  .then(user => fetchPosts(user.id))
  .catch(error => {
    console.error('Posts fetch failed:', error);
    return []; // Provide default posts
  })
  .then(posts => console.log('Posts:', posts.length));
```

**Recovering from Errors:**

```javascript
fetchUser(1)
  .catch(error => {
    console.warn('Primary fetch failed, trying backup...');
    return fetchUserFromBackup(1); // Recover with fallback
  })
  .then(user => {
    console.log('User loaded:', user);
  })
  .catch(error => {
    console.error('Both attempts failed:', error);
  });
```

**finally() - Always Executes:**

```javascript
let isLoading = true;

fetchData()
  .then(data => console.log(data))
  .catch(error => console.error(error))
  .finally(() => {
    isLoading = false; // Cleanup (runs regardless of success/failure)
    console.log('Loading complete');
  });
```

---

#### Promise.all() - Parallel Operations

**When to Use:**
Run multiple independent operations **in parallel** and wait for all to complete.

```javascript
// Sequential (SLOW): 3 seconds total
const user = await fetchUser(1);        // 1 second
const posts = await fetchPosts(1);      // 1 second
const friends = await fetchFriends(1);  // 1 second

// Parallel (FAST): 1 second total
const [user, posts, friends] = await Promise.all([
  fetchUser(1),      // All 3 run simultaneously
  fetchPosts(1),
  fetchFriends(1)
]);

// With .then()
Promise.all([
  fetchUser(1),
  fetchPosts(1),
  fetchFriends(1)
])
  .then(([user, posts, friends]) => {
    console.log('All loaded:', { user, posts, friends });
  })
  .catch(error => {
    // If ANY promise fails, entire Promise.all() fails
    console.error('One of the requests failed:', error);
  });
```

**Real Example - Dashboard Load:**

```javascript
function loadDashboard(userId) {
  return Promise.all([
    fetchUserProfile(userId),
    fetchUserPosts(userId),
    fetchUserFriends(userId),
    fetchNotifications(userId),
    fetchSettings(userId)
  ])
    .then(([profile, posts, friends, notifications, settings]) => {
      return {
        profile,
        posts,
        friends,
        notifications,
        settings
      };
    })
    .catch(error => {
      console.error('Dashboard load failed:', error);
      throw error;
    });
}

loadDashboard(1)
  .then(dashboard => console.log('Dashboard ready:', dashboard));
```

---

#### Promise.race() - First to Complete

**When to Use:**
Use the result of whichever promise resolves/rejects **first**.

```javascript
// Timeout pattern
const timeout = new Promise((_, reject) => {
  setTimeout(() => reject(new Error('Timeout!')), 5000);
});

const fetchData = fetch('/api/data').then(res => res.json());

Promise.race([fetchData, timeout])
  .then(data => console.log('Data loaded:', data))
  .catch(error => console.error('Error or timeout:', error));

// Real example: Multiple servers
Promise.race([
  fetch('https://server1.com/api/data'),
  fetch('https://server2.com/api/data'),
  fetch('https://server3.com/api/data')
])
  .then(response => response.json())
  .then(data => console.log('Fastest server responded:', data));
```

---

#### Promise.allSettled() - Wait for All (Ignore Failures)

**When to Use:**
Wait for all promises to complete, regardless of success/failure.

```javascript
Promise.allSettled([
  fetchUser(1),      // Success
  fetchPosts(999),   // Fails
  fetchFriends(1)    // Success
])
  .then(results => {
    results.forEach((result, index) => {
      if (result.status === 'fulfilled') {
        console.log(`Promise ${index} succeeded:`, result.value);
      } else {
        console.log(`Promise ${index} failed:`, result.reason);
      }
    });
  });

// OUTPUT:
// Promise 0 succeeded: { id: 1, name: 'John' }
// Promise 1 failed: Error: User not found
// Promise 2 succeeded: [{ id: 2, name: 'Jane' }, ...]
```

---

#### Promise.any() - First Successful

**When to Use:**
Get the first promise that **succeeds** (ignores failures until all fail).

```javascript
// Try multiple APIs, use first successful response
Promise.any([
  fetch('https://api1.com/data'),  // Fails
  fetch('https://api2.com/data'),  // Succeeds first!
  fetch('https://api3.com/data')   // Succeeds but slower
])
  .then(response => response.json())
  .then(data => console.log('Got data from first working API:', data))
  .catch(error => {
    console.error('All APIs failed:', error);
  });
```

---

#### Converting Callbacks to Promises

**util.promisify (Node.js):**

```javascript
const util = require('util');
const fs = require('fs');

// Convert callback-based function to Promise-based
const readFilePromise = util.promisify(fs.readFile);

// Now use with Promises
readFilePromise('file.txt', 'utf8')
  .then(data => console.log(data))
  .catch(error => console.error(error));

// Or with async/await
async function readFile() {
  const data = await readFilePromise('file.txt', 'utf8');
  console.log(data);
}
```

**Manual Wrapper:**

```javascript
// Old callback-based function
function fetchUserCallback(id, callback) {
  setTimeout(() => {
    if (id > 0) {
      callback(null, { id, name: 'John' });
    } else {
      callback(new Error('Invalid ID'));
    }
  }, 1000);
}

// Wrap in Promise
function fetchUser(id) {
  return new Promise((resolve, reject) => {
    fetchUserCallback(id, (error, user) => {
      if (error) {
        reject(error);
      } else {
        resolve(user);
      }
    });
  });
}

// Now use with Promises!
fetchUser(1)
  .then(user => console.log(user))
  .catch(error => console.error(error));
```

---

#### Async/Await - Modern Promise Syntax

**Promises vs Async/Await:**

```javascript
// With Promises (chaining)
fetchUser(1)
  .then(user => fetchPosts(user.id))
  .then(posts => fetchComments(posts[0].id))
  .then(comments => console.log(comments))
  .catch(error => console.error(error));

// With Async/Await (cleaner!)
async function loadData() {
  try {
    const user = await fetchUser(1);
    const posts = await fetchPosts(user.id);
    const comments = await fetchComments(posts[0].id);
    console.log(comments);
  } catch (error) {
    console.error(error);
  }
}

loadData();
```

**When to Use Each:**

```javascript
// ✅ Use Promises when:
// - Simple linear chain
// - Functional style preferred
// - No complex error handling needed

// ✅ Use Async/Await when:
// - Complex logic (loops, conditions)
// - Better error handling with try/catch
// - More readable for procedural code
```

---

#### Common Mistakes

**1. Forgetting to Return:**

```javascript
// ❌ BAD
fetchUser(1)
  .then(user => {
    fetchPosts(user.id); // Forgot return!
  })
  .then(posts => {
    console.log(posts); // undefined
  });

// ✅ GOOD
fetchUser(1)
  .then(user => fetchPosts(user.id)) // Implicit return
  .then(posts => console.log(posts));
```

**2. Not Catching Errors:**

```javascript
// ❌ BAD: Unhandled rejection
fetchUser(1)
  .then(user => console.log(user));
// If fetchUser fails → Unhandled rejection!

// ✅ GOOD
fetchUser(1)
  .then(user => console.log(user))
  .catch(error => console.error(error));
```

**3. Creating Unnecessary Promises:**

```javascript
// ❌ BAD: Unnecessary wrapping
function fetchUser(id) {
  return new Promise((resolve) => {
    resolve(fetch(`/api/users/${id}`)); // fetch already returns Promise!
  });
}

// ✅ GOOD: Just return the Promise
function fetchUser(id) {
  return fetch(`/api/users/${id}`);
}
```

**4. Mixing Patterns:**

```javascript
// ❌ BAD: Mixing callbacks and Promises
fetchUser(1, (err, user) => {  // Callback style
  if (err) return console.error(err);
  
  fetchPosts(user.id)          // Promise style
    .then(posts => console.log(posts));
});

// ✅ GOOD: Use one pattern consistently
fetchUser(1)
  .then(user => fetchPosts(user.id))
  .then(posts => console.log(posts))
  .catch(error => console.error(error));
```

---

#### Best Practices

**1. Always Handle Errors:**

```javascript
// ✅ With .catch()
promise.then(handleSuccess).catch(handleError);

// ✅ With try/catch (async/await)
try {
  const result = await promise;
} catch (error) {
  handleError(error);
}
```

**2. Keep Chains Flat:**

```javascript
// ✅ Flat is better
a().then(b).then(c).then(d);

// ❌ Avoid nesting
a().then(() => {
  b().then(() => {
    c().then(() => {
      d();
    });
  });
});
```

**3. Use Promise.all() for Parallel:**

```javascript
// ✅ Parallel (fast)
const [a, b, c] = await Promise.all([
  fetchA(),
  fetchB(),
  fetchC()
]);

// ❌ Sequential (slow)
const a = await fetchA();
const b = await fetchB();
const c = await fetchC();
```

**4. Return Promises from Functions:**

```javascript
// ✅ Good: Can be chained
function loadUser(id) {
  return fetchUser(id)
    .then(user => enrichUserData(user));
}

loadUser(1).then(user => console.log(user));
```

---

#### Summary

**Key Concepts:**

| Concept | Description |
|---------|-------------|
| **Promise** | Object representing eventual completion/failure |
| **Chaining** | Link multiple .then() calls sequentially |
| **.then()** | Handles successful result |
| **.catch()** | Handles errors |
| **.finally()** | Runs regardless of outcome |
| **Promise.all()** | Wait for all promises (parallel) |
| **Promise.race()** | First to complete wins |
| **Promise.allSettled()** | Wait for all, ignore failures |
| **Promise.any()** | First successful promise |

**Chaining Rules:**
1. ✅ Always return from .then()
2. ✅ Return values pass to next .then()
3. ✅ Errors propagate to .catch()
4. ✅ Keep chains flat (avoid nesting)
5. ✅ Use Promise.all() for parallel operations

**Quick Reference:**

```javascript
// Basic chain
fetch('/api/user/1')
  .then(res => res.json())
  .then(user => console.log(user))
  .catch(err => console.error(err))
  .finally(() => console.log('Done'));

// Async/await equivalent
try {
  const res = await fetch('/api/user/1');
  const user = await res.json();
  console.log(user);
} catch (err) {
  console.error(err);
} finally {
  console.log('Done');
}
```

**Remember:**
- ✅ Promises avoid callback hell
- ✅ Chaining makes async code readable
- ✅ Always handle errors
- ✅ Use Promise.all() for parallel operations
- ✅ Modern code prefers async/await over .then()

---

### Q1.25: What is the difference between Blocking and Non-Blocking in Node.js?

**Answer:**

**Blocking** operations stop the execution of code until the operation completes (synchronous), while **Non-Blocking** operations allow code to continue executing without waiting (asynchronous). This is fundamental to Node.js's high-performance architecture.
- Like waiting in line at a bank teller - you can't do anything else
- Uses `Sync` methods: `readFileSync()`, `writeFileSync()`

**Non-Blocking (Asynchronous):**
- Continues execution immediately, handles result later via callback/promise
- Like ordering at a restaurant - you're free to do other things while food is prepared
- Uses async methods: `readFile()`, `writeFile()`, or `async/await`

---

#### Visual Comparison

```javascript
// ❌ BLOCKING - Everything stops until file is read
console.log('1. Start');
const data = fs.readFileSync('file.txt', 'utf8'); // Blocks here!
console.log('2. File content:', data);
console.log('3. End');

// Output (in order):
// 1. Start
// 2. File content: [file contents]
// 3. End

// ✅ NON-BLOCKING - Code continues, callback runs when ready
console.log('1. Start');
fs.readFile('file.txt', 'utf8', (err, data) => {
  console.log('3. File content:', data); // Runs when ready
});
console.log('2. End'); // Runs immediately!

// Output (different order!):
// 1. Start
// 2. End
// 3. File content: [file contents]
```

---

#### Real-World Examples

**1. File System Operations**

```javascript
const fs = require('fs');

// ❌ BLOCKING - Freezes entire server
app.get('/data', (req, res) => {
  const data = fs.readFileSync('large-file.txt', 'utf8'); // 😱 Server frozen!
  res.send(data);
});

// ✅ NON-BLOCKING - Server continues handling other requests
app.get('/data', (req, res) => {
  fs.readFile('large-file.txt', 'utf8', (err, data) => {
    if (err) return res.status(500).send('Error');
    res.send(data);
  });
});

// ✅ BEST - Modern async/await
app.get('/data', async (req, res) => {
  try {
    const data = await fs.promises.readFile('large-file.txt', 'utf8');
    res.send(data);
  } catch (err) {
    res.status(500).send('Error');
  }
});
```

**2. Database Queries**

```javascript
// ❌ BLOCKING - Not how Node.js works!
// (Most DB drivers don't even offer sync versions)
const user = db.getUserSync(123); // Doesn't exist!
console.log(user);

// ✅ NON-BLOCKING - Callback approach
db.getUser(123, (err, user) => {
  if (err) throw err;
  console.log(user);
});

// ✅ BEST - Promise/async approach
async function getUser(id) {
  const user = await db.getUser(id);
  console.log(user);
  return user;
}
```

**3. HTTP Requests**

```javascript
const https = require('https');

// ❌ BLOCKING (doesn't exist in Node.js http module)
// const response = https.getSync('https://api.example.com/data');

// ✅ NON-BLOCKING - Callback
https.get('https://api.example.com/data', (res) => {
  let data = '';
  res.on('data', chunk => data += chunk);
  res.on('end', () => console.log(JSON.parse(data)));
});

// ✅ BEST - Modern with fetch (Node 18+)
async function fetchData() {
  const response = await fetch('https://api.example.com/data');
  const data = await response.json();
  console.log(data);
}
```

---

#### Performance Impact

**Blocking Code Performance:**

```javascript
const fs = require('fs');
const http = require('http');

// ❌ TERRIBLE - Blocking server
http.createServer((req, res) => {
  // This blocks ALL requests!
  const data = fs.readFileSync('large-file.txt'); // Takes 100ms
  res.end(data);
}).listen(3000);

// Concurrent requests: 1000
// Time for each: 100ms
// Total time: 1000 * 100ms = 100 seconds 😱
```

**Non-Blocking Code Performance:**

```javascript
// ✅ EXCELLENT - Non-blocking server
http.createServer((req, res) => {
  // Doesn't block! Other requests continue
  fs.readFile('large-file.txt', (err, data) => {
    res.end(data);
  });
}).listen(3000);

// Concurrent requests: 1000
// Time for each: 100ms
// Total time: ~100ms (all handled concurrently!) ⚡
```

---

#### Complete Comparison Table

| Aspect | Blocking (Sync) | Non-Blocking (Async) |
|--------|----------------|----------------------|
| **Execution** | Stops until complete | Continues immediately |
| **Event Loop** | Blocks event loop | Doesn't block |
| **Other Requests** | Must wait | Handled concurrently |
| **Memory** | Lower (sequential) | Higher (many operations) |
| **Use Case** | Scripts, CLI tools | Web servers, APIs |
| **Method Names** | `*Sync()` | No Sync suffix |
| **Error Handling** | try/catch | Callbacks, promises |
| **Code Style** | Sequential, simple | Callbacks, promises |
| **Performance** | Poor for I/O | Excellent for I/O |
| **Concurrency** | None | High |

---

#### When to Use Blocking vs Non-Blocking

**✅ Use Blocking (Sync) When:**

1. **CLI Scripts** - One-time execution
```javascript
#!/usr/bin/env node
const fs = require('fs');

// OK for CLI - runs once then exits
const config = JSON.parse(fs.readFileSync('config.json', 'utf8'));
console.log('Config loaded:', config);
```

2. **Initialization Code** - Before server starts
```javascript
const express = require('express');
const fs = require('fs');

// OK - runs once at startup
const sslKey = fs.readFileSync('./ssl/key.pem');
const sslCert = fs.readFileSync('./ssl/cert.pem');

const app = express();
// ... setup routes

https.createServer({ key: sslKey, cert: sslCert }, app).listen(443);
```

3. **Simple Scripts** - No concurrency needed
```javascript
// build.js - runs once
const fs = require('fs');

const files = fs.readdirSync('./src');
files.forEach(file => {
  const content = fs.readFileSync(`./src/${file}`, 'utf8');
  const processed = processContent(content);
  fs.writeFileSync(`./dist/${file}`, processed);
});

console.log('Build complete!');
```

**✅ Use Non-Blocking (Async) When:**

1. **Web Servers** - Handle multiple requests
```javascript
// Every request must be non-blocking!
app.get('/users/:id', async (req, res) => {
  const user = await db.getUser(req.params.id);
  res.json(user);
});
```

2. **Real-Time Applications** - WebSockets, chat, etc.
```javascript
io.on('connection', async (socket) => {
  const messages = await db.getRecentMessages();
  socket.emit('messages', messages);
});
```

3. **API Calls** - External services
```javascript
async function getUserData(userId) {
  const [user, posts, friends] = await Promise.all([
    api.getUser(userId),
    api.getPosts(userId),
    api.getFriends(userId)
  ]);
  return { user, posts, friends };
}
```

4. **Background Jobs** - Long-running tasks
```javascript
async function processQueue() {
  while (true) {
    const job = await queue.getNextJob();
    await processJob(job);
  }
}
```

---

#### Common Pitfalls

**1. Accidentally Blocking the Event Loop**

```javascript
// ❌ BAD - Blocks event loop with computation
app.get('/calculate', (req, res) => {
  let sum = 0;
  for (let i = 0; i < 10000000000; i++) {
    sum += i;
  }
  res.json({ sum });
});

// ✅ GOOD - Use worker threads for CPU-intensive tasks
const { Worker } = require('worker_threads');

app.get('/calculate', (req, res) => {
  const worker = new Worker('./calculate-worker.js');
  worker.on('message', (sum) => res.json({ sum }));
});
```

**2. Mixing Sync and Async**

```javascript
// ❌ BAD - Inconsistent
function readConfig(callback) {
  const data = fs.readFileSync('config.json'); // Sync!
  callback(null, JSON.parse(data)); // Async callback!
}

// ✅ GOOD - Consistent async
function readConfig(callback) {
  fs.readFile('config.json', 'utf8', (err, data) => {
    if (err) return callback(err);
    callback(null, JSON.parse(data));
  });
}

// ✅ BEST - Promise-based
async function readConfig() {
  const data = await fs.promises.readFile('config.json', 'utf8');
  return JSON.parse(data);
}
```

**3. Forgetting Error Handling**

```javascript
// ❌ BAD - Crashes on error
fs.readFile('file.txt', 'utf8', (err, data) => {
  console.log(data.toUpperCase()); // Crashes if err!
});

// ✅ GOOD - Proper error handling
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) {
    console.error('Error reading file:', err);
    return;
  }
  console.log(data.toUpperCase());
});

// ✅ BEST - Try/catch with async/await
async function readFile() {
  try {
    const data = await fs.promises.readFile('file.txt', 'utf8');
    console.log(data.toUpperCase());
  } catch (err) {
    console.error('Error reading file:', err);
  }
}
```

**4. Callback Hell (Pyramid of Doom)**

```javascript
// ❌ BAD - Nested callbacks
fs.readFile('file1.txt', (err, data1) => {
  if (err) throw err;
  fs.readFile('file2.txt', (err, data2) => {
    if (err) throw err;
    fs.readFile('file3.txt', (err, data3) => {
      if (err) throw err;
      console.log(data1, data2, data3);
    });
  });
});

// ✅ GOOD - Promises
const fs = require('fs').promises;

fs.readFile('file1.txt', 'utf8')
  .then(data1 => fs.readFile('file2.txt', 'utf8'))
  .then(data2 => fs.readFile('file3.txt', 'utf8'))
  .then(data3 => console.log(data1, data2, data3))
  .catch(err => console.error(err));

// ✅ BEST - Async/await
async function readFiles() {
  try {
    const data1 = await fs.readFile('file1.txt', 'utf8');
    const data2 = await fs.readFile('file2.txt', 'utf8');
    const data3 = await fs.readFile('file3.txt', 'utf8');
    console.log(data1, data2, data3);
  } catch (err) {
    console.error(err);
  }
}

// ✅ PARALLEL - Even better!
async function readFilesParallel() {
  try {
    const [data1, data2, data3] = await Promise.all([
      fs.readFile('file1.txt', 'utf8'),
      fs.readFile('file2.txt', 'utf8'),
      fs.readFile('file3.txt', 'utf8')
    ]);
    console.log(data1, data2, data3);
  } catch (err) {
    console.error(err);
  }
}
```

---

#### Testing Blocking vs Non-Blocking

**Demonstrate the Difference:**

```javascript
const fs = require('fs');

console.log('=== BLOCKING EXAMPLE ===');
console.log('1. Start');
const dataSync = fs.readFileSync('package.json', 'utf8');
console.log('2. File read (blocking completed)');
console.log('3. End');

console.log('\n=== NON-BLOCKING EXAMPLE ===');
console.log('1. Start');
fs.readFile('package.json', 'utf8', (err, dataAsync) => {
  console.log('3. File read (callback executed)');
});
console.log('2. End (continued before file read!)');

// Output:
// === BLOCKING EXAMPLE ===
// 1. Start
// 2. File read (blocking completed)
// 3. End
//
// === NON-BLOCKING EXAMPLE ===
// 1. Start
// 2. End (continued before file read!)
// 3. File read (callback executed)
```

**Performance Benchmark:**

```javascript
const fs = require('fs');
const { performance } = require('perf_hooks');

// Blocking test
console.log('Testing blocking...');
const startBlocking = performance.now();
for (let i = 0; i < 100; i++) {
  fs.readFileSync('package.json'); // Blocks each time
}
const endBlocking = performance.now();
console.log(`Blocking: ${(endBlocking - startBlocking).toFixed(2)}ms`);

// Non-blocking test
console.log('\nTesting non-blocking...');
const startNonBlocking = performance.now();
let completed = 0;

for (let i = 0; i < 100; i++) {
  fs.readFile('package.json', (err, data) => {
    completed++;
    if (completed === 100) {
      const endNonBlocking = performance.now();
      console.log(`Non-blocking: ${(endNonBlocking - startNonBlocking).toFixed(2)}ms`);
    }
  });
}

// Typical results:
// Blocking: 150ms (sequential)
// Non-blocking: 20ms (concurrent!)
```

---

#### Best Practices

**1. Default to Non-Blocking**
```javascript
// ✅ Always prefer async in servers
const fs = require('fs').promises;

app.get('/data', async (req, res) => {
  const data = await fs.readFile('data.json', 'utf8');
  res.json(JSON.parse(data));
});
```

**2. Use Modern async/await**
```javascript
// ✅ Cleaner than callbacks
async function processUser(userId) {
  const user = await db.getUser(userId);
  const posts = await db.getPosts(userId);
  return { user, posts };
}
```

**3. Parallelize Independent Operations**
```javascript
// ❌ Slow - Sequential
const user = await getUser(id);
const posts = await getPosts(id);
const friends = await getFriends(id);

// ✅ Fast - Parallel
const [user, posts, friends] = await Promise.all([
  getUser(id),
  getPosts(id),
  getFriends(id)
]);
```

**4. Only Use Sync in Initialization**
```javascript
// ✅ OK - Runs once at startup
const config = JSON.parse(fs.readFileSync('./config.json', 'utf8'));

app.listen(3000, () => {
  console.log('Server started with config:', config);
});
```

**5. Handle Errors Properly**
```javascript
// ✅ Always handle errors
async function safeOperation() {
  try {
    return await riskyOperation();
  } catch (error) {
    logger.error('Operation failed:', error);
    throw new AppError('Operation failed', 500);
  }
}
```

---

#### Summary

**Key Takeaways:**

| Concept | Description |
|---------|-------------|
| **Blocking** | Stops execution until complete (`*Sync()` methods) |
| **Non-Blocking** | Continues execution, handles result via callback/promise |
| **Event Loop** | Blocked by sync operations, free with async |
| **Performance** | Sync = sequential, Async = concurrent |
| **Use Sync When** | CLI scripts, initialization, simple one-time tasks |
| **Use Async When** | Web servers, APIs, real-time apps, I/O operations |
| **Best Practice** | Default to async, use `async/await`, parallelize with `Promise.all()` |

**Quick Decision Guide:**

```
Is this a web server/API? 
  ├─ YES → Use async (always!)
  └─ NO → Is it a one-time script?
           ├─ YES → Sync is OK
           └─ NO → Still use async for best practice
```

**Remember:**
- 🚫 **Never** block the event loop in production servers
- ✅ Use `async/await` for cleaner code
- ⚡ Parallelize independent operations with `Promise.all()`
- 🎯 Blocking is OK for CLI tools and initialization
- 💡 Node.js shines with non-blocking I/O

---

### Q1.5: What is Node.js REPL (Read, Eval, Print, Loop)?

**Answer:**

REPL is an **interactive shell** that allows you to execute JavaScript code line by line and see immediate results. It's like a JavaScript playground built into Node.js.

---

#### What is REPL?

**REPL stands for:**
- **R**ead: Read user input
- **E**val: Evaluate the JavaScript code
- **P**rint: Print the result
- **L**oop: Loop back and wait for next input

```
     ┌─────────────┐
     │   Read      │ ← User types JavaScript
     └──────┬──────┘
            │
     ┌──────▼──────┐
     │   Eval      │ ← Execute the code
     └──────┬──────┘
            │
     ┌──────▼──────┐
     │   Print     │ ← Show the result
     └──────┬──────┘
            │
     └──────┘ Loop back to Read
```

---

#### Starting REPL

**Basic Start:**

```bash
# Just type 'node' without any file
node

# You'll see:
Welcome to Node.js v20.11.0.
Type ".help" for more information.
> _
```

**The `>` prompt means REPL is ready for input!**

---

#### Basic Usage

**Simple Expressions:**

```javascript
> 2 + 2
4

> 'Hello' + ' ' + 'World'
'Hello World'

> Math.sqrt(16)
4

> new Date()
2024-01-21T10:30:00.000Z

> [1, 2, 3].map(x => x * 2)
[ 2, 4, 6 ]
```

**Variables:**

```javascript
> let name = 'John'
undefined

> name
'John'

> const age = 25
undefined

> `${name} is ${age} years old`
'John is 25 years old'

> let numbers = [1, 2, 3, 4, 5]
undefined

> numbers.reduce((sum, n) => sum + n, 0)
15
```

**Functions:**

```javascript
> function greet(name) {
...   return `Hello, ${name}!`;
... }
undefined

> greet('Alice')
'Hello, Alice!'

> const square = (x) => x * x
undefined

> square(5)
25
```

**Multi-line Code:**

```javascript
> function fibonacci(n) {
...   if (n <= 1) return n;
...   return fibonacci(n - 1) + fibonacci(n - 2);
... }
undefined

> fibonacci(10)
55

> for (let i = 0; i < 3; i++) {
...   console.log(`Count: ${i}`);
... }
Count: 0
Count: 1
Count: 2
undefined
```

---

#### Special REPL Commands

**Dot Commands:**

```javascript
// .help - Show all commands
> .help
.break    Sometimes you get stuck in a place you can't get out... 
.clear    Alias for .break
.editor   Enter editor mode
.exit     Exit the REPL
.help     Print this help message
.load     Load JS from a file into the REPL session
.save     Save all evaluated commands to a file

// .exit - Exit REPL
> .exit
$ # Back to terminal

// .clear - Clear REPL context (reset all variables)
> let x = 10
undefined
> x
10
> .clear
Clearing context...
> x
ReferenceError: x is not defined

// .save - Save session to file
> let data = [1, 2, 3]
undefined
> .save session.js
Session saved to: session.js

// .load - Load JavaScript file
> .load session.js
let data = [1, 2, 3]

> data
[ 1, 2, 3 ]

// .editor - Multi-line editor mode
> .editor
// Entering editor mode (Ctrl+D to finish, Ctrl+C to cancel)
function complexFunction() {
  const a = 10;
  const b = 20;
  return a + b;
}
// Press Ctrl+D
undefined

> complexFunction()
30
```

---

#### Special Variables in REPL

**`_` (underscore) - Last Expression Result:**

```javascript
> 5 + 5
10

> _
10

> _ * 2
20

> _
20

> Math.sqrt(_)
4.47213595499958

> _
4.47213595499958
```

**`_error` - Last Error:**

```javascript
> throw new Error('Oops!')
Error: Oops!

> _error
Error: Oops!
    at REPL:1:7

> _error.message
'Oops!'
```

---

#### Using Node.js Modules in REPL

**Built-in Modules:**

```javascript
> const fs = require('fs')
undefined

> fs.readdirSync('.')
[ 'file1.js', 'file2.js', 'package.json' ]

> const path = require('path')
undefined

> path.join('/users', 'john', 'documents')
'/users/john/documents'

> const os = require('os')
undefined

> os.cpus().length
8

> os.platform()
'linux'

> const http = require('http')
undefined

> // Create server in REPL!
> const server = http.createServer((req, res) => {
...   res.end('Hello from REPL!');
... })
undefined

> server.listen(3000)
Server {
  ...
}

> // Server is now running on port 3000!
```

**External Packages (if installed):**

```javascript
// First install: npm install lodash

> const _ = require('lodash')
undefined

> _.chunk([1, 2, 3, 4, 5], 2)
[ [ 1, 2 ], [ 3, 4 ], [ 5 ] ]

> _.capitalize('hello world')
'Hello world'
```

---

#### Async/Await in REPL

**Top-Level Await (Node.js 14+):**

```javascript
> const delay = (ms) => new Promise(resolve => setTimeout(resolve, ms))
undefined

> await delay(2000)
undefined
// Waits 2 seconds before returning

> const fetch = require('node-fetch')
undefined

> const response = await fetch('https://api.github.com/users/github')
undefined

> const data = await response.json()
undefined

> data.name
'GitHub'

> data.public_repos
175
```

**Without Top-Level Await:**

```javascript
// Wrap in async function
> (async () => {
...   const result = await someAsyncFunction();
...   console.log(result);
... })()
Promise { <pending> }
// Result printed after promise resolves
```

---

#### Practical REPL Use Cases

**1. Quick Testing:**

```javascript
// Test regex patterns
> /\d{3}-\d{3}-\d{4}/.test('555-123-4567')
true

> /\d{3}-\d{3}-\d{4}/.test('invalid')
false

// Test date formatting
> new Date().toISOString()
'2024-01-21T10:30:00.000Z'

> new Date().toLocaleString('en-US', { dateStyle: 'full' })
'Sunday, January 21, 2024'
```

**2. API Exploration:**

```javascript
// Test APIs quickly
> const https = require('https')
undefined

> https.get('https://api.ipify.org?format=json', (res) => {
...   let data = '';
...   res.on('data', chunk => data += chunk);
...   res.on('end', () => console.log(JSON.parse(data)));
... })
{ ip: '123.45.67.89' }
```

**3. Data Manipulation:**

```javascript
> const data = [
...   { name: 'Alice', age: 25 },
...   { name: 'Bob', age: 30 },
...   { name: 'Charlie', age: 35 }
... ]
undefined

> data.filter(person => person.age > 25)
[ { name: 'Bob', age: 30 }, { name: 'Charlie', age: 35 } ]

> data.map(p => p.name)
[ 'Alice', 'Bob', 'Charlie' ]

> data.reduce((sum, p) => sum + p.age, 0) / data.length
30
```

**4. Learning & Experimentation:**

```javascript
// Test JavaScript features
> const obj = { a: 1, b: 2, c: 3 }
undefined

> const { a, ...rest } = obj
undefined

> a
1

> rest
{ b: 2, c: 3 }

// Test array methods
> [1, 2, 3].flatMap(x => [x, x * 2])
[ 1, 2, 2, 4, 3, 6 ]
```

**5. File System Operations:**

```javascript
> const fs = require('fs')
undefined

> fs.writeFileSync('test.txt', 'Hello from REPL!')
undefined

> fs.readFileSync('test.txt', 'utf8')
'Hello from REPL!'

> fs.existsSync('test.txt')
true

> fs.unlinkSync('test.txt')
undefined
```

---

#### Programmatic REPL

**Create Custom REPL:**

```javascript
// custom-repl.js
const repl = require('repl');

// Start custom REPL
const myRepl = repl.start({
  prompt: 'myApp > ',
  useColors: true
});

// Add custom commands
myRepl.defineCommand('hello', {
  help: 'Say hello',
  action(name) {
    this.clearBufferedCommand();
    console.log(`Hello, ${name || 'World'}!`);
    this.displayPrompt();
  }
});

myRepl.defineCommand('time', {
  help: 'Show current time',
  action() {
    this.clearBufferedCommand();
    console.log(new Date().toLocaleString());
    this.displayPrompt();
  }
});

// Add predefined variables
myRepl.context.db = {
  users: [
    { id: 1, name: 'John' },
    { id: 2, name: 'Jane' }
  ]
};

myRepl.context.helper = {
  sum: (a, b) => a + b,
  multiply: (a, b) => a * b
};
```

**Usage:**

```bash
node custom-repl.js

myApp > .hello Alice
Hello, Alice!

myApp > .time
1/21/2024, 10:30:00 AM

myApp > db.users
[ { id: 1, name: 'John' }, { id: 2, name: 'Jane' } ]

myApp > helper.sum(5, 10)
15
```

---

#### Advanced REPL Features

**1. Custom Evaluation:**

```javascript
const repl = require('repl');

const myRepl = repl.start({
  prompt: '> ',
  eval: (cmd, context, filename, callback) => {
    // Custom evaluation logic
    try {
      const result = eval(cmd);
      callback(null, result);
    } catch (error) {
      callback(error);
    }
  }
});
```

**2. Custom Writer (Format Output):**

```javascript
const repl = require('repl');

const myRepl = repl.start({
  prompt: '> ',
  writer: (output) => {
    // Custom output formatting
    if (typeof output === 'object') {
      return JSON.stringify(output, null, 2);
    }
    return output;
  }
});
```

**3. Tab Completion:**

```javascript
const repl = require('repl');

const myRepl = repl.start({
  prompt: '> ',
  completer: (line) => {
    const completions = ['help', 'users', 'products', 'orders'];
    const hits = completions.filter((c) => c.startsWith(line));
    return [hits.length ? hits : completions, line];
  }
});
```

---

#### REPL History

**Automatic History:**

```javascript
// REPL automatically saves history to:
// ~/.node_repl_history

// Access previous commands with arrow keys:
// ↑ (up arrow) - Previous command
// ↓ (down arrow) - Next command

// Search history:
// Ctrl+R - Reverse search
// Type to search, Enter to execute
```

**Custom History File:**

```javascript
const repl = require('repl');
const path = require('path');

const myRepl = repl.start({
  prompt: '> ',
  historyFile: path.join(__dirname, '.custom_history'),
  historySize: 1000
});
```

---

#### REPL Shortcuts

**Keyboard Shortcuts:**

```
Ctrl+C     - Cancel current input / Exit (if empty line)
Ctrl+D     - Exit REPL
Tab        - Auto-complete
↑/↓        - Navigate history
Ctrl+L     - Clear screen
Ctrl+U     - Delete line before cursor
Ctrl+K     - Delete line after cursor
Ctrl+A     - Move cursor to start of line
Ctrl+E     - Move cursor to end of line
Ctrl+R     - Search history (reverse)
```

---

#### Common REPL Patterns

**1. Testing Code Snippets:**

```javascript
> // Test a function before using in code
> const validateEmail = (email) => {
...   return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
... }
undefined

> validateEmail('test@example.com')
true

> validateEmail('invalid.email')
false
```

**2. Debugging Values:**

```javascript
> const obj = { nested: { value: 42 } }
undefined

> obj.nested.value
42

> JSON.stringify(obj, null, 2)
'{
  "nested": {
    "value": 42
  }
}'
```

**3. Quick Calculations:**

```javascript
> // Calculate percentage
> (250 / 1000) * 100
25

> // Convert bytes to MB
> 5242880 / 1024 / 1024
5
```

**4. String Manipulation:**

```javascript
> const str = 'Hello World'
undefined

> str.toLowerCase()
'hello world'

> str.split(' ').reverse().join(' ')
'World Hello'

> str.replace(/World/, 'Node.js')
'Hello Node.js'
```

---

#### REPL Configuration Options

**All Options:**

```javascript
const repl = require('repl');

const options = {
  prompt: 'custom> ',           // Custom prompt
  input: process.stdin,          // Input stream
  output: process.stdout,        // Output stream
  terminal: true,                // Terminal mode
  eval: customEval,              // Custom evaluator
  writer: customWriter,          // Custom output formatter
  useColors: true,               // Enable colors
  useGlobal: false,              // Use global scope
  ignoreUndefined: false,        // Hide undefined results
  replMode: repl.REPL_MODE_STRICT, // Strict mode
  breakEvalOnSigint: true,       // Break on Ctrl+C
  historySize: 1000,             // History size
  historyFile: '.repl_history'   // History file path
};

const myRepl = repl.start(options);
```

---

#### Real-World REPL Example

**Database Debugging REPL:**

```javascript
// db-repl.js
const repl = require('repl');
const mongoose = require('mongoose');

// Connect to MongoDB
mongoose.connect('mongodb://localhost/mydb');

const User = mongoose.model('User', {
  name: String,
  email: String,
  age: Number
});

// Start REPL
const replServer = repl.start({
  prompt: 'db> ',
  useColors: true
});

// Add models to context
replServer.context.User = User;

// Add helper functions
replServer.context.findUsers = async (query) => {
  return await User.find(query);
};

replServer.context.createUser = async (data) => {
  const user = new User(data);
  return await user.save();
};

// Custom commands
replServer.defineCommand('users', {
  help: 'List all users',
  async action() {
    this.clearBufferedCommand();
    const users = await User.find();
    console.log(users);
    this.displayPrompt();
  }
});

console.log('Database REPL ready!');
console.log('Available: User, findUsers(), createUser()');
console.log('Commands: .users');
```

**Usage:**

```bash
node db-repl.js

db> await User.find()
[ { name: 'John', email: 'john@example.com' }, ... ]

db> await createUser({ name: 'Alice', email: 'alice@example.com', age: 25 })
{ _id: ..., name: 'Alice', ... }

db> .users
[ List of all users ]
```

---

#### REPL Best Practices

**1. Use for Quick Testing:**
```javascript
// ✅ Test regex, functions, APIs quickly
> /\d+/.test('abc123')
true
```

**2. Explore APIs:**
```javascript
// ✅ Learn new modules
> const util = require('util')
> util.
// Tab to see all methods
```

**3. Debug Values:**
```javascript
// ✅ Inspect complex objects
> const obj = { a: 1, b: { c: 2 } }
> obj.b.c
2
```

**4. Prototype Code:**
```javascript
// ✅ Try ideas before writing files
> const isEven = n => n % 2 === 0
> [1,2,3,4].filter(isEven)
[ 2, 4 ]
```

**5. Don't Use for Production:**
```javascript
// ❌ Don't run production servers from REPL
// ✅ Use REPL for development and testing only
```

---

#### Troubleshooting REPL

**1. Stuck in Multi-line Mode:**
```javascript
> function test() {
... // Forgot to close bracket
... // Type .break or Ctrl+C to exit
.break
>
```

**2. Clear Context:**
```javascript
> let messy = 'data'
> // Too many variables defined
> .clear
Clearing context...
> // Fresh start!
```

**3. Exit REPL:**
```javascript
> .exit
// Or press Ctrl+D
// Or press Ctrl+C twice
```

---

#### Summary

**REPL Key Points:**

1. **Interactive JavaScript Shell** - Execute code line by line
2. **Quick Testing** - Test functions, regex, APIs instantly
3. **Learning Tool** - Explore JavaScript features
4. **Debugging** - Inspect values and test logic
5. **Customizable** - Create custom REPLs for your apps

**Common Commands:**
```javascript
.help      // Show help
.exit      // Exit REPL
.clear     // Clear context
.save      // Save session
.load      // Load file
.editor    // Multi-line editor
_          // Last result
```

**Use REPL for:**
- ✅ Quick testing and prototyping
- ✅ Learning JavaScript/Node.js
- ✅ Debugging values
- ✅ API exploration
- ✅ Quick calculations

**Don't use REPL for:**
- ❌ Production code
- ❌ Long-running applications
- ❌ Complex multi-file projects

**Quick Start:**
```bash
# Start REPL
node

# Try something
> console.log('Hello REPL!')
Hello REPL!

# Exit
> .exit
```

---

### Q1.7: What are Global Objects in Node.js?

**Answer:**

Global objects in Node.js are **built-in objects** available in all modules without requiring `require()` or `import`. They provide essential functionality and information about the current execution environment.

---

#### What are Global Objects?

**Definition:**
Global objects are predefined variables and functions that are accessible everywhere in your Node.js application, in any file or module.

**Key Difference from Browser:**
```javascript
// ❌ In Node.js, these DON'T exist (browser-only):
window        // No window object
document      // No DOM
alert()       // No alert function
localStorage  // No local storage

// ✅ In Node.js, these ARE available:
global        // Equivalent to window (but avoid using)
process       // Process information
__dirname     // Current directory
__filename    // Current file path
require()     // Module system
module        // Current module
exports       // Module exports
```

---

#### Essential Global Objects

**1. `global` - Global Namespace**

```javascript
// The global object (like window in browser)
console.log(global);

// Attaching to global (❌ generally avoid this!)
global.myGlobalVar = 'Available everywhere';

// Accessing from another file
console.log(global.myGlobalVar); // 'Available everywhere'

// ⚠️ Better approach: Use modules instead
// export/require for sharing between files
```

**Why Avoid `global`:**
```javascript
// ❌ BAD: Polluting global namespace
global.config = { api: 'https://api.example.com' };

// ✅ GOOD: Use modules
// config.js
module.exports = { api: 'https://api.example.com' };

// app.js
const config = require('./config');
console.log(config.api);
```

---

**2. `process` - Process Information**

Most important global object for Node.js applications.

**Process Information:**
```javascript
// Node.js version
console.log(process.version);        // v20.10.0

// Platform
console.log(process.platform);       // linux, darwin, win32

// Architecture
console.log(process.arch);           // x64, arm64

// Process ID
console.log(process.pid);            // 12345

// Current working directory
console.log(process.cwd());          // /home/user/project

// Memory usage
console.log(process.memoryUsage());
// {
//   rss: 30597120,
//   heapTotal: 6787072,
//   heapUsed: 4464976,
//   external: 1234567
// }

// Uptime in seconds
console.log(process.uptime());       // 45.123
```

**Environment Variables:**
```javascript
// Access environment variables
console.log(process.env.NODE_ENV);   // development, production
console.log(process.env.PORT);       // 3000
console.log(process.env.DATABASE_URL);

// Set environment variable (current process only)
process.env.MY_VAR = 'value';

// Common pattern
const isDevelopment = process.env.NODE_ENV !== 'production';

if (isDevelopment) {
  console.log('Running in development mode');
}
```

**Command-Line Arguments:**
```javascript
// node app.js --name John --age 25

console.log(process.argv);
// [
//   '/usr/bin/node',           // process.argv[0] - node executable
//   '/path/to/app.js',         // process.argv[1] - script file
//   '--name',                  // process.argv[2]
//   'John',                    // process.argv[3]
//   '--age',                   // process.argv[4]
//   '25'                       // process.argv[5]
// ]

// Parse arguments
const args = process.argv.slice(2); // Skip first 2 elements

// Manual parsing
const name = process.argv[3];
const age = process.argv[5];
console.log(`Name: ${name}, Age: ${age}`);

// Better: Use library like yargs or commander
```

**Exit Process:**
```javascript
// Exit with success
process.exit(0);

// Exit with error
process.exit(1);

// Exit with custom code
process.exit(127);

// Before exit
process.on('exit', (code) => {
  console.log(`Process exiting with code: ${code}`);
});

// Graceful shutdown
process.on('SIGTERM', () => {
  console.log('SIGTERM received, closing server...');
  server.close(() => {
    console.log('Server closed');
    process.exit(0);
  });
});

process.on('SIGINT', () => {
  console.log('SIGINT received (Ctrl+C)');
  process.exit(0);
});
```

**Standard Streams:**
```javascript
// stdin - Standard Input
process.stdin.on('data', (data) => {
  console.log('You typed:', data.toString());
});

// stdout - Standard Output
process.stdout.write('Hello World\n');

// stderr - Standard Error
process.stderr.write('Error occurred\n');

// Example: Interactive CLI
const readline = require('readline');
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.question('What is your name? ', (answer) => {
  console.log(`Hello ${answer}!`);
  rl.close();
});
```

**Change Directory:**
```javascript
console.log('Starting directory:', process.cwd());

try {
  process.chdir('/tmp');
  console.log('New directory:', process.cwd());
} catch (err) {
  console.error('Failed to change directory:', err);
}
```

---

**3. `__dirname` & `__filename` - File Paths**

**Critical:** These are module-scoped, not truly global (available in CommonJS, not in ES modules).

```javascript
// __filename - Absolute path of current file
console.log(__filename);
// /home/user/project/app.js

// __dirname - Directory of current file
console.log(__dirname);
// /home/user/project

// Common use: Build file paths
const path = require('path');
const fs = require('fs');

const dataPath = path.join(__dirname, 'data', 'users.json');
console.log(dataPath);
// /home/user/project/data/users.json

// Read file relative to current script
const configPath = path.join(__dirname, 'config.json');
const config = JSON.parse(fs.readFileSync(configPath, 'utf8'));

// Serve static files
const express = require('express');
const app = express();
app.use(express.static(path.join(__dirname, 'public')));
```

**ES Modules Alternative:**
```javascript
// In ES modules (.mjs or "type": "module")
import { fileURLToPath } from 'url';
import { dirname } from 'path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

console.log(__filename);
console.log(__dirname);
```

---

**4. `console` - Logging**

```javascript
// Basic logging
console.log('Message');                      // Standard output
console.error('Error occurred');             // Standard error
console.warn('Warning');                     // Standard error
console.info('Information');                 // Standard output
console.debug('Debug info');                 // Standard output

// Formatted output
const user = { name: 'John', age: 30 };
console.log('User:', user);                  // User: { name: 'John', age: 30 }

// Multiple arguments
console.log('Name:', user.name, 'Age:', user.age);

// String substitution
console.log('User: %s, Age: %d', user.name, user.age);

// Table (great for arrays)
const users = [
  { id: 1, name: 'John', role: 'admin' },
  { id: 2, name: 'Jane', role: 'user' }
];
console.table(users);

// Timing
console.time('operation');
// ... some code ...
console.timeEnd('operation');                // operation: 123.456ms

// Count
for (let i = 0; i < 5; i++) {
  console.count('loop');                     // loop: 1, loop: 2, ...
}
console.countReset('loop');

// Stack trace
console.trace('Trace point');

// Assertions
console.assert(1 === 1, 'This will not print');
console.assert(1 === 2, 'This will print an error');

// Clear (in terminal)
console.clear();

// Grouping
console.group('User Details');
console.log('Name:', user.name);
console.log('Age:', user.age);
console.groupEnd();
```

---

**5. `Buffer` - Binary Data**

```javascript
// Create buffer from string
const buf1 = Buffer.from('Hello World');
console.log(buf1);                           // <Buffer 48 65 6c 6c 6f ...>

// Create empty buffer
const buf2 = Buffer.alloc(10);               // 10 bytes, filled with 0
const buf3 = Buffer.allocUnsafe(10);         // 10 bytes, uninitialized (faster)

// Convert to string
console.log(buf1.toString());                // Hello World
console.log(buf1.toString('hex'));           // 48656c6c6f20576f726c64
console.log(buf1.toString('base64'));        // SGVsbG8gV29ybGQ=

// Buffer operations
buf2.write('Node.js');
console.log(buf2.toString());                // Node.js

// Length
console.log(buf1.length);                    // 11 (bytes)

// Concatenate
const buf4 = Buffer.concat([buf1, buf2]);

// Compare
console.log(Buffer.compare(buf1, buf2));     // -1, 0, or 1

// Check if Buffer
console.log(Buffer.isBuffer(buf1));          // true
console.log(Buffer.isBuffer('string'));      // false
```

---

**6. `setTimeout`, `setInterval`, `setImmediate`**

**setTimeout - Delay Execution:**
```javascript
// Execute after delay
setTimeout(() => {
  console.log('Executed after 1 second');
}, 1000);

// With arguments
setTimeout((name, age) => {
  console.log(`Name: ${name}, Age: ${age}`);
}, 1000, 'John', 30);

// Clear timeout
const timeoutId = setTimeout(() => {
  console.log('This will not run');
}, 5000);

clearTimeout(timeoutId);
```

**setInterval - Repeat Execution:**
```javascript
// Execute every interval
const intervalId = setInterval(() => {
  console.log('Executed every 1 second');
}, 1000);

// Clear interval
setTimeout(() => {
  clearInterval(intervalId);
  console.log('Interval cleared');
}, 5000);

// Common pattern: Polling
let attempts = 0;
const maxAttempts = 5;

const pollInterval = setInterval(async () => {
  attempts++;
  const ready = await checkIfReady();
  
  if (ready || attempts >= maxAttempts) {
    clearInterval(pollInterval);
    console.log(ready ? 'Ready!' : 'Timeout');
  }
}, 1000);
```

**setImmediate - Execute After I/O:**
```javascript
// Execute on next iteration of event loop
console.log('1. Start');

setImmediate(() => {
  console.log('3. Immediate');
});

console.log('2. End');

// OUTPUT:
// 1. Start
// 2. End
// 3. Immediate

// Clear immediate
const immediateId = setImmediate(() => {
  console.log('This will not run');
});
clearImmediate(immediateId);
```

---

**7. `require()` & `module` - Module System**

**require() - Import Modules:**
```javascript
// Core modules
const fs = require('fs');
const path = require('path');
const http = require('http');

// npm packages
const express = require('express');
const axios = require('axios');

// Local modules (relative path)
const myModule = require('./myModule');
const utils = require('./utils/helpers');
const config = require('../config');

// JSON files
const package = require('./package.json');
const data = require('./data.json');

// Destructuring
const { readFile, writeFile } = require('fs').promises;
const { join, resolve } = require('path');
```

**module & exports:**
```javascript
// Current module information
console.log(module);
// {
//   id: '.',
//   path: '/home/user/project',
//   exports: {},
//   parent: null,
//   filename: '/home/user/project/app.js',
//   loaded: false,
//   children: [],
//   paths: [ ... ]
// }

// Check if module is main (entry point)
if (require.main === module) {
  console.log('This file was run directly');
} else {
  console.log('This file was required by another module');
}

// Exporting
module.exports = {
  name: 'MyModule',
  version: '1.0.0'
};

// Or use exports shorthand
exports.myFunction = () => {};
exports.myVariable = 123;

// Note: exports is reference to module.exports
// ❌ Don't reassign exports
exports = { data: 'value' };  // Won't work!

// ✅ Reassign module.exports
module.exports = { data: 'value' };  // Works!
```

**require.cache - Module Cache:**
```javascript
// Modules are cached after first load
const module1 = require('./myModule');
const module2 = require('./myModule');  // Same instance!

console.log(module1 === module2);  // true

// Clear cache (for testing/hot reload)
delete require.cache[require.resolve('./myModule')];

// Force reload
const freshModule = require('./myModule');
```

---

**8. Other Global Objects**

**URL & URLSearchParams:**
```javascript
// Parse URL
const myURL = new URL('https://example.com:8080/path?name=John&age=25#section');

console.log(myURL.protocol);    // https:
console.log(myURL.hostname);    // example.com
console.log(myURL.port);        // 8080
console.log(myURL.pathname);    // /path
console.log(myURL.search);      // ?name=John&age=25
console.log(myURL.hash);        // #section

// Search params
console.log(myURL.searchParams.get('name'));  // John
myURL.searchParams.set('city', 'NYC');
console.log(myURL.toString());
```

**TextEncoder & TextDecoder:**
```javascript
// Encode string to bytes
const encoder = new TextEncoder();
const bytes = encoder.encode('Hello World');
console.log(bytes);  // Uint8Array [72, 101, 108, 108, 111, ...]

// Decode bytes to string
const decoder = new TextDecoder();
const text = decoder.decode(bytes);
console.log(text);   // Hello World
```

**AbortController (Node.js 15+):**
```javascript
// Cancel async operations
const controller = new AbortController();
const signal = controller.signal;

// Abort after 5 seconds
setTimeout(() => controller.abort(), 5000);

// Use with fetch
fetch('https://api.example.com/data', { signal })
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => {
    if (err.name === 'AbortError') {
      console.log('Request was aborted');
    }
  });
```

---

#### Timers - Complete Reference

**Comparison:**

| Function | Description | Execution Order |
|----------|-------------|-----------------|
| **setTimeout** | Execute after delay | Macrotask (timers phase) |
| **setInterval** | Repeat every interval | Macrotask (timers phase) |
| **setImmediate** | Execute after I/O | Macrotask (check phase) |
| **process.nextTick** | Execute ASAP | Microtask (highest priority) |

**Example:**
```javascript
console.log('1. Start');

setTimeout(() => console.log('5. setTimeout'), 0);
setImmediate(() => console.log('6. setImmediate'));
process.nextTick(() => console.log('3. nextTick'));
Promise.resolve().then(() => console.log('4. Promise'));

console.log('2. End');

// OUTPUT:
// 1. Start
// 2. End
// 3. nextTick (highest priority microtask)
// 4. Promise (microtask)
// 5. setTimeout (macrotask - timers)
// 6. setImmediate (macrotask - check)
```

---

#### Checking for Globals

**List All Globals:**
```javascript
// View all global properties
console.log(Object.keys(global));

// Check if variable is global
console.log('Buffer' in global);        // true
console.log('process' in global);       // true
console.log('myVar' in global);         // false
```

**Detecting Environment:**
```javascript
// Check if running in Node.js
if (typeof process !== 'undefined' && process.versions && process.versions.node) {
  console.log('Running in Node.js');
}

// Check if running in browser
if (typeof window !== 'undefined') {
  console.log('Running in browser');
}

// Universal check
const isNode = typeof process === 'object' && 
               process.versions && 
               process.versions.node;

const isBrowser = typeof window === 'object';
```

---

#### Best Practices

**1. Avoid Polluting Global:**
```javascript
// ❌ BAD: Global pollution
global.myConfig = { api: 'https://api.example.com' };

// ✅ GOOD: Use modules
// config.js
module.exports = { api: 'https://api.example.com' };
```

**2. Use process.env for Configuration:**
```javascript
// ✅ Environment-based config
const config = {
  port: process.env.PORT || 3000,
  dbUrl: process.env.DATABASE_URL,
  env: process.env.NODE_ENV || 'development'
};
```

**3. Always Clear Timers:**
```javascript
// ✅ Prevent memory leaks
const timerId = setTimeout(() => {}, 1000);
// When no longer needed:
clearTimeout(timerId);

const intervalId = setInterval(() => {}, 1000);
// When no longer needed:
clearInterval(intervalId);
```

**4. Use __dirname for File Paths:**
```javascript
// ✅ Reliable file paths
const path = require('path');
const configPath = path.join(__dirname, 'config.json');

// ❌ Avoid relative to process.cwd() (can change!)
const configPath = './config.json';
```

**5. Handle Process Signals:**
```javascript
// ✅ Graceful shutdown
process.on('SIGTERM', () => {
  console.log('SIGTERM received');
  server.close(() => process.exit(0));
});

process.on('SIGINT', () => {
  console.log('SIGINT received');
  process.exit(0);
});
```

---

#### Common Patterns

**Environment-Based Behavior:**
```javascript
const isDevelopment = process.env.NODE_ENV !== 'production';

if (isDevelopment) {
  // Development only
  console.log('Debug mode enabled');
  require('dotenv').config();
}

// Production optimizations
if (process.env.NODE_ENV === 'production') {
  // Enable caching, minification, etc.
}
```

**CLI Arguments Parsing:**
```javascript
// Simple parser
const args = {};
process.argv.slice(2).forEach((arg, i, arr) => {
  if (arg.startsWith('--')) {
    const key = arg.slice(2);
    const value = arr[i + 1] && !arr[i + 1].startsWith('--') 
                  ? arr[i + 1] 
                  : true;
    args[key] = value;
  }
});

// node app.js --name John --age 25 --verbose
// args = { name: 'John', age: '25', verbose: true }
```

**Periodic Health Check:**
```javascript
setInterval(() => {
  const mem = process.memoryUsage();
  const heapUsedMB = (mem.heapUsed / 1024 / 1024).toFixed(2);
  
  console.log(`Memory: ${heapUsedMB} MB`);
  
  if (heapUsedMB > 500) {
    console.warn('High memory usage!');
  }
}, 30000); // Every 30 seconds
```

---

#### Summary

**Most Important Global Objects:**

```javascript
// Process & Environment
process.env.NODE_ENV
process.argv
process.exit()
process.cwd()

// File System
__dirname
__filename

// Timers
setTimeout()
setInterval()
setImmediate()

// Module System
require()
module.exports
exports

// Data Handling
Buffer
console

// Web Standards
URL
URLSearchParams
TextEncoder/TextDecoder
AbortController
```

**Key Differences: Node.js vs Browser:**

| Global | Node.js | Browser |
|--------|---------|---------|
| **process** | ✅ Yes | ❌ No |
| **Buffer** | ✅ Yes | ❌ No |
| **__dirname** | ✅ Yes | ❌ No |
| **require()** | ✅ Yes | ❌ No |
| **window** | ❌ No | ✅ Yes |
| **document** | ❌ No | ✅ Yes |
| **setTimeout** | ✅ Yes | ✅ Yes |
| **console** | ✅ Yes | ✅ Yes |

**Remember:**
- ✅ Use `process` for environment and process info
- ✅ Use `__dirname` for reliable file paths
- ✅ Avoid polluting `global` namespace
- ✅ Always clear timers when done
- ✅ Handle process signals for graceful shutdown
- ✅ Use modules instead of globals for sharing data

---

### Q1.8: What is the Child Process Module in Node.js?

**Answer:**

The **child_process** module allows you to spawn child processes and execute external commands from your Node.js application. It's essential for running CPU-intensive tasks, executing shell commands, and parallel processing without blocking the event loop.

---

#### Why Use Child Processes?

**Problem: Blocking the Event Loop**

```javascript
// ❌ BAD: CPU-intensive task blocks everything
app.get('/process', (req, res) => {
  const result = heavyComputation(); // Blocks for 5 seconds!
  res.json({ result });
});

// All other requests wait 5 seconds! 😱
```

**Solution: Offload to Child Process**

```javascript
// ✅ GOOD: Run in separate process
const { fork } = require('child_process');

app.get('/process', (req, res) => {
  const child = fork('./worker.js');
  
  child.on('message', (result) => {
    res.json({ result });
  });
  
  child.send({ data: req.body });
});

// Main process stays responsive! 🎉
```

---

#### When to Use Child Processes

**Use Cases:**

✅ **CPU-Intensive Operations:**
- Image/video processing
- Data compression
- Cryptographic operations
- Large file parsing
- Machine learning inference

✅ **External Commands:**
- Running shell scripts
- Executing system utilities
- Running other programming languages
- System administration tasks

✅ **Parallel Processing:**
- Batch processing
- Multi-core utilization
- Concurrent tasks

✅ **Isolation:**
- Sandboxing untrusted code
- Running different Node.js versions
- Memory isolation

---

#### The Four Methods

The `child_process` module provides **4 main methods** for creating child processes:

| Method | Use Case | Output | IPC | Best For |
|--------|----------|--------|-----|----------|
| **spawn()** | Stream large output | Stream | Optional | Long-running commands, large output |
| **exec()** | Run shell commands | Buffered | No | Short commands, shell features |
| **execFile()** | Execute files | Buffered | No | Running executables directly |
| **fork()** | Run Node.js scripts | Stream | ✅ Yes | Node.js child processes, IPC |

---

#### 1. spawn() - Streaming Output

**When to Use:**
- Large output (doesn't buffer, streams instead)
- Long-running processes
- Real-time output needed

**Basic Usage:**

```javascript
const { spawn } = require('child_process');

// Spawn a process
const ls = spawn('ls', ['-lh', '/usr']);

// Listen to stdout
ls.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`);
});

// Listen to stderr
ls.stderr.on('data', (data) => {
  console.error(`stderr: ${data}`);
});

// Process exit
ls.on('close', (code) => {
  console.log(`Process exited with code ${code}`);
});

// Error handling
ls.on('error', (error) => {
  console.error(`Failed to start: ${error}`);
});
```

**Advanced Example: Git Clone with Progress**

```javascript
const { spawn } = require('child_process');

function gitClone(repoUrl, destination) {
  return new Promise((resolve, reject) => {
    const git = spawn('git', ['clone', repoUrl, destination]);
    
    // Git outputs progress to stderr
    git.stderr.on('data', (data) => {
      const progress = data.toString();
      console.log(progress); // Show clone progress
    });
    
    git.on('close', (code) => {
      if (code === 0) {
        resolve('Clone successful');
      } else {
        reject(new Error(`Git clone failed with code ${code}`));
      }
    });
    
    git.on('error', reject);
  });
}

// Usage
gitClone('https://github.com/user/repo.git', './repo')
  .then(msg => console.log(msg))
  .catch(err => console.error(err));
```

**Spawn Options:**

```javascript
const child = spawn('command', ['arg1', 'arg2'], {
  cwd: '/path/to/working/directory',  // Working directory
  env: { ...process.env, CUSTOM: 'value' }, // Environment variables
  stdio: 'inherit',                   // Inherit stdio from parent
  detached: true,                     // Run independently
  shell: true,                        // Run in shell (enables pipes, redirects)
  timeout: 5000,                      // Kill after 5 seconds
  maxBuffer: 1024 * 1024             // Max buffer size (1MB)
});
```

---

#### 2. exec() - Execute Shell Commands

**When to Use:**
- Short commands with small output
- Need shell features (pipes, redirects, globs)
- Quick system commands

**Basic Usage:**

```javascript
const { exec } = require('child_process');

// Simple command
exec('ls -la', (error, stdout, stderr) => {
  if (error) {
    console.error(`Error: ${error.message}`);
    return;
  }
  if (stderr) {
    console.error(`stderr: ${stderr}`);
    return;
  }
  console.log(`stdout: ${stdout}`);
});
```

**Promise Wrapper:**

```javascript
const { exec } = require('child_process');
const util = require('util');
const execPromise = util.promisify(exec);

async function runCommand(command) {
  try {
    const { stdout, stderr } = await execPromise(command);
    return stdout;
  } catch (error) {
    throw new Error(`Command failed: ${error.message}`);
  }
}

// Usage
(async () => {
  try {
    const output = await runCommand('echo "Hello World"');
    console.log(output);
  } catch (error) {
    console.error(error);
  }
})();
```

**Real-World Examples:**

```javascript
const { exec } = require('child_process');
const { promisify } = require('util');
const execPromise = promisify(exec);

// 1. Get system info
async function getSystemInfo() {
  const { stdout } = await execPromise('uname -a');
  return stdout.trim();
}

// 2. Check disk space
async function getDiskSpace() {
  const { stdout } = await execPromise('df -h');
  return stdout;
}

// 3. Kill process by port
async function killProcessOnPort(port) {
  try {
    // Find process ID
    const { stdout } = await execPromise(
      `lsof -ti:${port}`
    );
    const pid = stdout.trim();
    
    if (pid) {
      // Kill the process
      await execPromise(`kill -9 ${pid}`);
      console.log(`Killed process ${pid} on port ${port}`);
    }
  } catch (error) {
    console.log(`No process found on port ${port}`);
  }
}

// 4. Compress files
async function compressFiles(source, destination) {
  const { stdout } = await execPromise(
    `tar -czf ${destination} ${source}`
  );
  return stdout;
}

// 5. Git operations
async function gitCommitAndPush(message) {
  await execPromise('git add .');
  await execPromise(`git commit -m "${message}"`);
  await execPromise('git push origin main');
  return 'Pushed successfully';
}
```

**Using Shell Features:**

```javascript
const { exec } = require('child_process');

// Pipes and redirects work because exec uses shell
exec('cat file.txt | grep "error" > errors.txt', (error, stdout) => {
  if (error) {
    console.error(error);
    return;
  }
  console.log('Filtered errors to errors.txt');
});

// Wildcards
exec('ls *.js', (error, stdout) => {
  console.log(stdout);
});

// Multiple commands
exec('npm install && npm test', (error, stdout) => {
  console.log(stdout);
});
```

**exec() Options:**

```javascript
exec('command', {
  cwd: process.cwd(),                 // Working directory
  env: process.env,                   // Environment variables
  shell: '/bin/sh',                   // Shell to use
  timeout: 0,                         // Max execution time (0 = no timeout)
  maxBuffer: 1024 * 1024,            // Max stdout/stderr buffer (1MB)
  killSignal: 'SIGTERM',             // Signal to kill process
  encoding: 'utf8'                    // Output encoding
}, callback);
```

---

#### 3. execFile() - Execute Files Directly

**When to Use:**
- Execute binary files directly (no shell overhead)
- More secure (no shell injection)
- Faster than exec()

**Basic Usage:**

```javascript
const { execFile } = require('child_process');

// Execute a file directly (no shell)
execFile('node', ['--version'], (error, stdout, stderr) => {
  if (error) {
    console.error(error);
    return;
  }
  console.log(`Node.js version: ${stdout}`);
});
```

**Security Comparison:**

```javascript
const { exec, execFile } = require('child_process');

// ❌ DANGEROUS: Shell injection vulnerability
const userInput = 'file.txt; rm -rf /'; // Malicious input!
exec(`cat ${userInput}`, (error, stdout) => {
  // This would execute: cat file.txt; rm -rf /
  // 💀 All files deleted!
});

// ✅ SAFE: No shell, arguments are separate
execFile('cat', [userInput], (error, stdout) => {
  // This tries to read a file literally named "file.txt; rm -rf /"
  // Much safer!
});
```

**Real-World Example:**

```javascript
const { execFile } = require('child_process');
const { promisify } = require('util');
const execFilePromise = promisify(execFile);

// Run Python script
async function runPythonScript(scriptPath, args = []) {
  try {
    const { stdout, stderr } = await execFilePromise('python3', [
      scriptPath,
      ...args
    ]);
    
    if (stderr) {
      console.warn('Warnings:', stderr);
    }
    
    return stdout;
  } catch (error) {
    throw new Error(`Python script failed: ${error.message}`);
  }
}

// Usage
(async () => {
  const result = await runPythonScript('./process.py', ['--input', 'data.csv']);
  console.log(result);
})();
```

**execFile() vs exec():**

| Feature | exec() | execFile() |
|---------|--------|------------|
| Uses Shell | ✅ Yes | ❌ No |
| Shell Features | ✅ Yes (pipes, etc.) | ❌ No |
| Security | ⚠️ Shell injection risk | ✅ Safer |
| Performance | Slower | ✅ Faster |
| Arguments | String | Array |

---

#### 4. fork() - Run Node.js Scripts with IPC

**When to Use:**
- Run Node.js child processes
- Need two-way communication (IPC)
- Worker processes
- Parallel Node.js tasks

**Basic Usage:**

```javascript
// parent.js
const { fork } = require('child_process');

const child = fork('./child.js');

// Send message to child
child.send({ type: 'task', data: [1, 2, 3, 4, 5] });

// Receive message from child
child.on('message', (message) => {
  console.log('Result from child:', message);
});

// Handle child exit
child.on('exit', (code) => {
  console.log(`Child exited with code ${code}`);
});
```

```javascript
// child.js
process.on('message', (message) => {
  console.log('Child received:', message);
  
  // Do work
  const result = message.data.reduce((a, b) => a + b, 0);
  
  // Send result back
  process.send({ result });
});
```

**Real-World Example: Image Processing**

```javascript
// server.js
const express = require('express');
const { fork } = require('child_process');
const app = express();

app.post('/process-image', (req, res) => {
  // Fork a child process for heavy image processing
  const child = fork('./imageProcessor.js');
  
  // Send image data to child
  child.send({
    imageUrl: req.body.imageUrl,
    operations: ['resize', 'compress', 'watermark']
  });
  
  // Receive processed image
  child.on('message', (message) => {
    if (message.error) {
      res.status(500).json({ error: message.error });
    } else {
      res.json({ processedImage: message.result });
    }
    child.kill(); // Clean up
  });
  
  // Timeout after 30 seconds
  setTimeout(() => {
    child.kill();
    res.status(408).json({ error: 'Processing timeout' });
  }, 30000);
});

app.listen(3000);
```

```javascript
// imageProcessor.js
const sharp = require('sharp'); // Image processing library

process.on('message', async (message) => {
  try {
    const { imageUrl, operations } = message;
    
    // Heavy image processing here
    let image = sharp(imageUrl);
    
    if (operations.includes('resize')) {
      image = image.resize(800, 600);
    }
    
    if (operations.includes('compress')) {
      image = image.jpeg({ quality: 80 });
    }
    
    const buffer = await image.toBuffer();
    
    // Send result back to parent
    process.send({
      result: buffer.toString('base64')
    });
  } catch (error) {
    process.send({ error: error.message });
  }
});
```

**Worker Pool Pattern:**

```javascript
// workerPool.js
const { fork } = require('child_process');

class WorkerPool {
  constructor(workerScript, poolSize = 4) {
    this.workerScript = workerScript;
    this.poolSize = poolSize;
    this.workers = [];
    this.queue = [];
    
    // Create worker pool
    for (let i = 0; i < poolSize; i++) {
      this.createWorker();
    }
  }
  
  createWorker() {
    const worker = fork(this.workerScript);
    worker.busy = false;
    
    worker.on('message', (message) => {
      // Worker finished task
      worker.busy = false;
      worker.callback(null, message);
      
      // Process next task in queue
      this.processQueue();
    });
    
    worker.on('exit', () => {
      // Replace dead worker
      const index = this.workers.indexOf(worker);
      if (index !== -1) {
        this.workers.splice(index, 1);
        this.createWorker();
      }
    });
    
    this.workers.push(worker);
  }
  
  exec(data) {
    return new Promise((resolve, reject) => {
      this.queue.push({ data, resolve, reject });
      this.processQueue();
    });
  }
  
  processQueue() {
    if (this.queue.length === 0) return;
    
    // Find available worker
    const worker = this.workers.find(w => !w.busy);
    if (!worker) return;
    
    // Get next task
    const task = this.queue.shift();
    
    worker.busy = true;
    worker.callback = task.resolve;
    worker.send(task.data);
  }
  
  destroy() {
    this.workers.forEach(worker => worker.kill());
  }
}

// Usage
const pool = new WorkerPool('./worker.js', 4);

async function processTasks() {
  const tasks = Array.from({ length: 100 }, (_, i) => i);
  
  const results = await Promise.all(
    tasks.map(task => pool.exec({ task }))
  );
  
  console.log('All tasks completed:', results.length);
  pool.destroy();
}

processTasks();
```

---

#### Communication Between Processes (IPC)

**Inter-Process Communication (IPC)** allows parent and child to exchange messages.

**Basic IPC:**

```javascript
// parent.js
const { fork } = require('child_process');
const child = fork('./child.js');

// Parent → Child
child.send({ command: 'start', data: 'Hello' });

// Child → Parent
child.on('message', (msg) => {
  console.log('From child:', msg);
});
```

```javascript
// child.js

// Child → Parent
process.send({ status: 'ready' });

// Parent → Child
process.on('message', (msg) => {
  console.log('From parent:', msg);
  
  // Do work and respond
  process.send({ status: 'done', result: 42 });
});
```

**Complex Data Transfer:**

```javascript
// parent.js
const { fork } = require('child_process');
const child = fork('./child.js');

// Send complex object
child.send({
  type: 'process',
  data: {
    users: [{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }],
    operation: 'filter',
    criteria: { active: true }
  },
  timestamp: Date.now()
});

child.on('message', ({ result, stats }) => {
  console.log('Processed:', result);
  console.log('Stats:', stats);
});
```

**Bidirectional Communication:**

```javascript
// parent.js
const { fork } = require('child_process');

class ChildWorker {
  constructor(scriptPath) {
    this.child = fork(scriptPath);
    this.callbacks = new Map();
    this.messageId = 0;
    
    this.child.on('message', (message) => {
      const callback = this.callbacks.get(message.id);
      if (callback) {
        callback(message.data);
        this.callbacks.delete(message.id);
      }
    });
  }
  
  async call(method, ...args) {
    return new Promise((resolve) => {
      const id = this.messageId++;
      this.callbacks.set(id, resolve);
      this.child.send({ id, method, args });
    });
  }
  
  destroy() {
    this.child.kill();
  }
}

// Usage
const worker = new ChildWorker('./worker.js');

(async () => {
  const sum = await worker.call('add', 5, 3);
  console.log('Sum:', sum); // 8
  
  const product = await worker.call('multiply', 4, 7);
  console.log('Product:', product); // 28
  
  worker.destroy();
})();
```

```javascript
// worker.js
const methods = {
  add: (a, b) => a + b,
  multiply: (a, b) => a * b,
  divide: (a, b) => a / b
};

process.on('message', ({ id, method, args }) => {
  const result = methods[method](...args);
  process.send({ id, data: result });
});
```

---

#### Handling stdout, stderr, and exit codes

**Reading Output:**

```javascript
const { spawn } = require('child_process');

const child = spawn('ls', ['-la']);

// Collect stdout
let output = '';
child.stdout.on('data', (data) => {
  output += data.toString();
});

// Collect stderr
let errors = '';
child.stderr.on('data', (data) => {
  errors += data.toString();
});

// Handle completion
child.on('close', (code, signal) => {
  console.log(`Exit code: ${code}`);
  console.log(`Signal: ${signal}`);
  
  if (code === 0) {
    console.log('Success:', output);
  } else {
    console.error('Failed:', errors);
  }
});
```

**Exit Codes:**

```javascript
const { spawn } = require('child_process');

function runCommand(command, args) {
  return new Promise((resolve, reject) => {
    const child = spawn(command, args);
    
    child.on('exit', (code, signal) => {
      if (code === 0) {
        resolve('Success');
      } else if (signal) {
        reject(new Error(`Killed by signal ${signal}`));
      } else {
        reject(new Error(`Exited with code ${code}`));
      }
    });
    
    child.on('error', reject);
  });
}

// Usage
runCommand('npm', ['test'])
  .then(() => console.log('Tests passed'))
  .catch(err => console.error('Tests failed:', err));
```

**Common Exit Codes:**

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Misuse of shell command |
| 126 | Command cannot execute |
| 127 | Command not found |
| 128 + N | Fatal error signal N |
| 130 | Terminated by Ctrl+C (SIGINT) |
| 137 | Killed by SIGKILL |
| 143 | Terminated by SIGTERM |

---

#### Error Handling

**Comprehensive Error Handling:**

```javascript
const { spawn } = require('child_process');

function safeSpawn(command, args = [], options = {}) {
  return new Promise((resolve, reject) => {
    let stdout = '';
    let stderr = '';
    
    const child = spawn(command, args, options);
    
    // Collect output
    if (child.stdout) {
      child.stdout.on('data', (data) => {
        stdout += data.toString();
      });
    }
    
    if (child.stderr) {
      child.stderr.on('data', (data) => {
        stderr += data.toString();
      });
    }
    
    // Handle errors
    child.on('error', (error) => {
      reject({
        type: 'spawn_error',
        message: error.message,
        error
      });
    });
    
    // Handle exit
    child.on('exit', (code, signal) => {
      if (code === 0) {
        resolve({ stdout, stderr, code });
      } else {
        reject({
          type: 'exit_error',
          message: `Process exited with code ${code}`,
          code,
          signal,
          stdout,
          stderr
        });
      }
    });
    
    // Timeout
    if (options.timeout) {
      setTimeout(() => {
        child.kill();
        reject({
          type: 'timeout',
          message: `Process timeout after ${options.timeout}ms`
        });
      }, options.timeout);
    }
  });
}

// Usage
try {
  const result = await safeSpawn('npm', ['test'], { timeout: 30000 });
  console.log('Success:', result.stdout);
} catch (error) {
  switch (error.type) {
    case 'spawn_error':
      console.error('Failed to start:', error.message);
      break;
    case 'exit_error':
      console.error('Process failed:', error.stderr);
      break;
    case 'timeout':
      console.error('Process timed out');
      break;
  }
}
```

**Error Types:**

```javascript
const { spawn } = require('child_process');

const child = spawn('nonexistent-command');

// 1. Spawn Error (command not found)
child.on('error', (error) => {
  console.error('Spawn error:', error);
  // ENOENT: command not found
});

// 2. Exit Error (command failed)
child.on('exit', (code, signal) => {
  if (code !== 0) {
    console.error(`Exit error: code ${code}`);
  }
});

// 3. Stream Error
child.stdout.on('error', (error) => {
  console.error('Stream error:', error);
});
```

---

#### stdio Configuration

**stdio Options:**

```javascript
const { spawn } = require('child_process');

// 1. Inherit (share parent's stdio)
spawn('command', [], { stdio: 'inherit' });

// 2. Pipe (default, create streams)
spawn('command', [], { stdio: 'pipe' });

// 3. Ignore (discard output)
spawn('command', [], { stdio: 'ignore' });

// 4. Custom configuration
spawn('command', [], {
  stdio: [
    'pipe',   // stdin
    'pipe',   // stdout
    'pipe',   // stderr
    'pipe',   // custom fd 3
    'pipe'    // custom fd 4
  ]
});

// 5. Mix and match
spawn('command', [], {
  stdio: [
    'inherit',  // stdin from parent
    'pipe',     // stdout to stream
    'ignore'    // discard stderr
  ]
});
```

**Real-World Example: Quiet Mode**

```javascript
const { spawn } = require('child_process');

function installPackage(packageName, quiet = false) {
  const npm = spawn('npm', ['install', packageName], {
    stdio: quiet ? 'ignore' : 'inherit'
  });
  
  return new Promise((resolve, reject) => {
    npm.on('exit', (code) => {
      if (code === 0) {
        resolve();
      } else {
        reject(new Error(`npm install failed`));
      }
    });
  });
}

// Quiet install
await installPackage('express', true);

// Verbose install
await installPackage('react', false);
```

---

#### Advanced Patterns

**1. Process Pool with Queue:**

```javascript
const { fork } = require('child_process');

class ProcessPool {
  constructor(script, size = 4) {
    this.workers = Array.from({ length: size }, () => {
      const worker = fork(script);
      worker.busy = false;
      return worker;
    });
    this.queue = [];
  }
  
  async execute(data) {
    const worker = this.workers.find(w => !w.busy);
    
    if (worker) {
      return this.runTask(worker, data);
    }
    
    // Queue if all workers busy
    return new Promise((resolve) => {
      this.queue.push({ data, resolve });
    });
  }
  
  runTask(worker, data) {
    return new Promise((resolve) => {
      worker.busy = true;
      
      worker.once('message', (result) => {
        worker.busy = false;
        resolve(result);
        
        // Process queue
        if (this.queue.length > 0) {
          const next = this.queue.shift();
          this.runTask(worker, next.data).then(next.resolve);
        }
      });
      
      worker.send(data);
    });
  }
}

const pool = new ProcessPool('./worker.js', 4);

// Execute 100 tasks with 4 workers
const tasks = Array.from({ length: 100 }, (_, i) => i);
const results = await Promise.all(
  tasks.map(task => pool.execute({ task }))
);
```

**2. Graceful Shutdown:**

```javascript
const { fork } = require('child_process');

class GracefulChild {
  constructor(scriptPath) {
    this.child = fork(scriptPath);
    this.stopping = false;
  }
  
  async stop(timeout = 5000) {
    if (this.stopping) return;
    this.stopping = true;
    
    return new Promise((resolve) => {
      // Send shutdown signal
      this.child.send({ type: 'shutdown' });
      
      // Wait for graceful exit
      const timer = setTimeout(() => {
        console.warn('Force killing child process');
        this.child.kill('SIGKILL');
        resolve();
      }, timeout);
      
      this.child.once('exit', () => {
        clearTimeout(timer);
        resolve();
      });
    });
  }
}

// Usage
const child = new GracefulChild('./worker.js');

process.on('SIGTERM', async () => {
  await child.stop();
  process.exit(0);
});
```

**3. Restart on Crash:**

```javascript
const { fork } = require('child_process');

class ResilientChild {
  constructor(scriptPath, maxRestarts = 5) {
    this.scriptPath = scriptPath;
    this.maxRestarts = maxRestarts;
    this.restarts = 0;
    this.start();
  }
  
  start() {
    this.child = fork(this.scriptPath);
    
    this.child.on('exit', (code) => {
      if (code !== 0 && this.restarts < this.maxRestarts) {
        this.restarts++;
        console.log(`Restarting child (${this.restarts}/${this.maxRestarts})`);
        setTimeout(() => this.start(), 1000);
      } else if (this.restarts >= this.maxRestarts) {
        console.error('Max restarts reached, giving up');
      }
    });
  }
  
  send(message) {
    if (this.child) {
      this.child.send(message);
    }
  }
}

const child = new ResilientChild('./worker.js', 5);
```

**4. Streaming Large Data:**

```javascript
const { spawn } = require('child_process');
const fs = require('fs');

// Process large file with streaming
function processLargeFile(inputFile, outputFile) {
  return new Promise((resolve, reject) => {
    // Read input file
    const input = fs.createReadStream(inputFile);
    
    // Spawn processor
    const processor = spawn('process-command', ['--option']);
    
    // Write output file
    const output = fs.createWriteStream(outputFile);
    
    // Pipe: input → processor → output
    input.pipe(processor.stdin);
    processor.stdout.pipe(output);
    
    // Handle errors
    input.on('error', reject);
    processor.on('error', reject);
    output.on('error', reject);
    
    // Handle completion
    output.on('finish', resolve);
  });
}

// Usage
await processLargeFile('input.txt', 'output.txt');
```

---

#### Best Practices

**1. Always Handle Errors:**

```javascript
const { spawn } = require('child_process');

// ✅ GOOD: Comprehensive error handling
const child = spawn('command');

child.on('error', (error) => {
  console.error('Failed to start:', error);
});

child.on('exit', (code, signal) => {
  if (code !== 0) {
    console.error(`Process failed with code ${code}`);
  }
});

child.stdout.on('error', (error) => {
  console.error('Stream error:', error);
});
```

**2. Clean Up Child Processes:**

```javascript
const { spawn } = require('child_process');

const children = [];

function createChild() {
  const child = spawn('long-running-command');
  children.push(child);
  return child;
}

// Cleanup on exit
process.on('exit', () => {
  children.forEach(child => child.kill());
});

process.on('SIGTERM', () => {
  children.forEach(child => child.kill());
  process.exit(0);
});
```

**3. Use Appropriate Method:**

```javascript
// ❌ BAD: exec() for large output (buffers all in memory)
exec('cat large-file.txt', (error, stdout) => {
  // Memory problem!
});

// ✅ GOOD: spawn() for large output (streams)
const cat = spawn('cat', ['large-file.txt']);
cat.stdout.pipe(process.stdout);

// ❌ BAD: spawn() for simple command
spawn('echo', ['hello']).stdout.pipe(process.stdout);

// ✅ GOOD: exec() for simple command
exec('echo hello', (error, stdout) => {
  console.log(stdout);
});
```

**4. Validate User Input:**

```javascript
const { execFile } = require('child_process');

// ✅ GOOD: Validate and use execFile
function processFile(filename) {
  // Validate input
  if (!/^[a-zA-Z0-9_-]+\.txt$/.test(filename)) {
    throw new Error('Invalid filename');
  }
  
  // Use execFile (no shell)
  return new Promise((resolve, reject) => {
    execFile('cat', [filename], (error, stdout) => {
      if (error) reject(error);
      else resolve(stdout);
    });
  });
}
```

**5. Set Timeouts:**

```javascript
const { spawn } = require('child_process');

function runWithTimeout(command, args, timeout) {
  return new Promise((resolve, reject) => {
    const child = spawn(command, args);
    
    const timer = setTimeout(() => {
      child.kill();
      reject(new Error('Process timeout'));
    }, timeout);
    
    child.on('exit', (code) => {
      clearTimeout(timer);
      if (code === 0) resolve();
      else reject(new Error(`Exit code ${code}`));
    });
  });
}

// Usage
await runWithTimeout('npm', ['test'], 30000); // 30 second timeout
```

**6. Use Detached Processes Carefully:**

```javascript
const { spawn } = require('child_process');

// Start detached process (won't die with parent)
const child = spawn('long-running-daemon', [], {
  detached: true,
  stdio: 'ignore'
});

// Unreference so parent can exit
child.unref();

// The daemon continues running after parent exits
```

---

#### Performance Considerations

**1. spawn() vs exec():**

```javascript
// ✅ spawn() - Better for large output (streams)
const spawn = require('child_process').spawn;
const child = spawn('find', ['/']);
child.stdout.pipe(process.stdout); // Streaming

// ⚠️ exec() - Buffers all output (memory limit)
const exec = require('child_process').exec;
exec('find /', (error, stdout) => {
  // May exceed maxBuffer and crash!
});
```

**2. Process Pool:**

```javascript
// ✅ Reuse processes instead of creating new ones
const pool = new ProcessPool('./worker.js', 4);

for (let i = 0; i < 1000; i++) {
  await pool.execute({ task: i });
}

// ❌ Creating 1000 processes is expensive!
for (let i = 0; i < 1000; i++) {
  const child = fork('./worker.js');
  child.send({ task: i });
}
```

**3. Batching:**

```javascript
// ✅ Send batch of tasks to one process
child.send({
  tasks: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
});

// ❌ One task per message (more overhead)
for (let i = 1; i <= 10; i++) {
  child.send({ task: i });
}
```

---

#### Common Pitfalls

**1. Not Handling Errors:**

```javascript
// ❌ BAD: No error handling
const child = spawn('command');
child.stdout.pipe(process.stdout);
// If command doesn't exist, app crashes!

// ✅ GOOD: Always handle errors
const child = spawn('command');
child.on('error', (error) => {
  console.error('Error:', error);
});
```

**2. Memory Leaks:**

```javascript
// ❌ BAD: Not cleaning up
const children = [];
setInterval(() => {
  children.push(spawn('command'));
}, 1000);
// Memory leak! Processes accumulate

// ✅ GOOD: Clean up finished processes
const children = new Set();
setInterval(() => {
  const child = spawn('command');
  children.add(child);
  
  child.on('exit', () => {
    children.delete(child);
  });
}, 1000);
```

**3. Shell Injection:**

```javascript
const userInput = req.query.file;

// ❌ DANGEROUS: Shell injection
exec(`cat ${userInput}`, callback);
// userInput: "file.txt; rm -rf /"

// ✅ SAFE: Use execFile or validate input
execFile('cat', [userInput], callback);
```

**4. Ignoring Exit Codes:**

```javascript
// ❌ BAD: Assuming success
exec('npm test', (error, stdout) => {
  console.log(stdout);
  // Might have failed!
});

// ✅ GOOD: Check exit code
exec('npm test', (error, stdout, stderr) => {
  if (error) {
    console.error('Tests failed:', error.code);
    return;
  }
  console.log('Tests passed');
});
```

---

#### Summary

**Quick Reference:**

**When to Use Each Method:**

```javascript
// Large output, real-time streaming
spawn('command', ['arg']);

// Short commands, shell features
exec('command arg');

// Execute binaries securely
execFile('binary', ['arg']);

// Node.js workers with IPC
fork('./script.js');
```

**Key Methods:**

```javascript
const { spawn, exec, execFile, fork } = require('child_process');

// spawn() - Streaming
const child = spawn('ls', ['-la']);
child.stdout.on('data', console.log);

// exec() - Buffered, shell
exec('ls -la', (err, stdout) => console.log(stdout));

// execFile() - No shell, secure
execFile('ls', ['-la'], (err, stdout) => console.log(stdout));

// fork() - Node.js with IPC
const worker = fork('./worker.js');
worker.send({ task: 'process' });
worker.on('message', console.log);
```

**Best Practices:**
- ✅ Always handle `error` and `exit` events
- ✅ Use `spawn()` for large output (streaming)
- ✅ Use `exec()` for simple shell commands
- ✅ Use `execFile()` for security (no shell)
- ✅ Use `fork()` for Node.js child processes
- ✅ Clean up child processes on exit
- ✅ Set timeouts for long-running processes
- ✅ Validate user input before executing
- ✅ Use process pools for many tasks
- ✅ Implement graceful shutdown

**Common Use Cases:**
- 🖼️ Image/video processing
- 📊 Data processing
- 🔐 Cryptographic operations
- 🧪 Running tests in isolation
- 🔧 System administration tasks
- 🌐 Running shell scripts
- ⚡ Parallel processing
- 🏗️ Build tools

---

### Q1.75: What are the Core Modules in Node.js?

**Answer:**

Core modules are **built-in modules** that come with Node.js - no installation required. They provide fundamental functionality for file system access, networking, streams, encryption, and more.

---

#### What are Core Modules?

**Definition:**
Core modules are native Node.js modules maintained by the Node.js team. They're compiled into the Node.js binary and available immediately.

**Key Characteristics:**
- ✅ No `npm install` needed
- ✅ Always available in any Node.js environment
- ✅ High performance (written in C/C++)
- ✅ Well-tested and stable
- ✅ Part of Node.js specification

**How to Use:**
```javascript
// Just require them - no installation needed!
const fs = require('fs');
const http = require('http');
const path = require('path');
```

---

#### Complete List of Core Modules

**Essential Core Modules (Most Used):**

| Module | Purpose | Common Use Cases |
|--------|---------|------------------|
| **fs** | File System | Read/write files, directories |
| **http** | HTTP Server/Client | Web servers, API calls |
| **path** | Path Utilities | File path manipulation |
| **os** | Operating System | System information |
| **events** | Event Emitter | Custom events |
| **stream** | Streaming Data | Large data processing |
| **buffer** | Binary Data | Binary data handling |
| **crypto** | Cryptography | Hashing, encryption |
| **util** | Utilities | Helper functions |
| **url** | URL Parsing | Parse and format URLs |

**Additional Core Modules:**

| Module | Purpose |
|--------|---------|
| **https** | HTTPS Server/Client |
| **net** | TCP/IPC Networking |
| **dns** | DNS Resolution |
| **child_process** | Spawn Child Processes |
| **cluster** | Multi-Process Clustering |
| **process** | Process Information |
| **readline** | Read Line-by-Line |
| **zlib** | Compression |
| **timers** | Timing Functions |
| **tty** | Terminal/TTY |
| **dgram** | UDP Sockets |
| **querystring** | Query String Parsing |
| **assert** | Testing Assertions |
| **vm** | Virtual Machine |
| **worker_threads** | Worker Threads |
| **perf_hooks** | Performance Measurement |

---

#### 1. fs (File System)

**Purpose:** Read and write files, manage directories

**Common Methods:**

```javascript
const fs = require('fs');

// Read file (synchronous)
const data = fs.readFileSync('file.txt', 'utf8');
console.log(data);

// Read file (asynchronous - better!)
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});

// Read file (promise-based - modern!)
const fsPromises = require('fs').promises;

async function readFile() {
  try {
    const data = await fsPromises.readFile('file.txt', 'utf8');
    console.log(data);
  } catch (error) {
    console.error('Error:', error);
  }
}

// Write file
fs.writeFileSync('output.txt', 'Hello World!');

// Append to file
fs.appendFileSync('log.txt', 'New log entry\n');

// Check if file exists
if (fs.existsSync('file.txt')) {
  console.log('File exists!');
}

// Get file stats
const stats = fs.statSync('file.txt');
console.log('Size:', stats.size);
console.log('Is file:', stats.isFile());
console.log('Is directory:', stats.isDirectory());

// List directory contents
const files = fs.readdirSync('.');
console.log('Files:', files);

// Create directory
fs.mkdirSync('newDir', { recursive: true });

// Delete file
fs.unlinkSync('temp.txt');

// Delete directory
fs.rmdirSync('tempDir', { recursive: true });

// Watch file for changes
fs.watch('file.txt', (eventType, filename) => {
  console.log(`${filename} changed: ${eventType}`);
});
```

**Real-World Example:**

```javascript
const fs = require('fs').promises;
const path = require('path');

// Read all JSON files in a directory
async function readAllJsonFiles(dirPath) {
  try {
    const files = await fs.readdir(dirPath);
    const jsonFiles = files.filter(f => f.endsWith('.json'));
    
    const contents = await Promise.all(
      jsonFiles.map(async (file) => {
        const filePath = path.join(dirPath, file);
        const data = await fs.readFile(filePath, 'utf8');
        return { file, data: JSON.parse(data) };
      })
    );
    
    return contents;
  } catch (error) {
    console.error('Error reading files:', error);
    throw error;
  }
}

// Usage
readAllJsonFiles('./data')
  .then(files => console.log('Loaded files:', files))
  .catch(err => console.error(err));
```

---

#### 2. http / https

**Purpose:** Create HTTP/HTTPS servers and make HTTP requests

**HTTP Server:**

```javascript
const http = require('http');

// Create basic server
const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World!');
});

server.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});

// Handle different routes
const server2 = http.createServer((req, res) => {
  const { method, url } = req;
  
  if (url === '/') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ message: 'Home' }));
  } else if (url === '/api/users') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ users: ['John', 'Jane'] }));
  } else {
    res.writeHead(404);
    res.end('Not Found');
  }
});
```

**HTTP Client (Make Requests):**

```javascript
const https = require('https');

// GET request
https.get('https://api.github.com/users/github', (res) => {
  let data = '';
  
  res.on('data', chunk => data += chunk);
  
  res.on('end', () => {
    console.log(JSON.parse(data));
  });
}).on('error', (err) => {
  console.error('Error:', err);
});

// POST request
const https = require('https');
const data = JSON.stringify({ name: 'John' });

const options = {
  hostname: 'api.example.com',
  port: 443,
  path: '/users',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': data.length
  }
};

const req = https.request(options, (res) => {
  console.log('Status:', res.statusCode);
  
  res.on('data', (chunk) => {
    console.log(chunk.toString());
  });
});

req.on('error', (error) => {
  console.error(error);
});

req.write(data);
req.end();
```

---

#### 3. path

**Purpose:** Handle and transform file paths

```javascript
const path = require('path');

// Join paths (handles separators correctly)
const fullPath = path.join('/users', 'john', 'documents', 'file.txt');
console.log(fullPath); // /users/john/documents/file.txt

// Resolve absolute path
const absolute = path.resolve('file.txt');
console.log(absolute); // /current/working/directory/file.txt

// Get directory name
console.log(path.dirname('/users/john/file.txt')); // /users/john

// Get file name
console.log(path.basename('/users/john/file.txt')); // file.txt

// Get file extension
console.log(path.extname('file.txt')); // .txt

// Parse path into components
const parsed = path.parse('/users/john/file.txt');
console.log(parsed);
// {
//   root: '/',
//   dir: '/users/john',
//   base: 'file.txt',
//   ext: '.txt',
//   name: 'file'
// }

// Build path from components
const built = path.format({
  dir: '/users/john',
  base: 'file.txt'
});
console.log(built); // /users/john/file.txt

// Normalize path (remove .., ., etc.)
console.log(path.normalize('/users/john/../jane/./file.txt'));
// /users/jane/file.txt

// Get relative path
console.log(path.relative('/users/john', '/users/jane/file.txt'));
// ../jane/file.txt

// Check if path is absolute
console.log(path.isAbsolute('/users/john')); // true
console.log(path.isAbsolute('file.txt')); // false

// Platform-specific separator
console.log(path.sep); // '/' on Unix, '\' on Windows
```

**Cross-Platform Paths:**

```javascript
// ✅ Good: Works on all platforms
const filePath = path.join(__dirname, 'data', 'users.json');

// ❌ Bad: Only works on Unix
const filePath = __dirname + '/data/users.json';

// ❌ Bad: Only works on Windows
const filePath = __dirname + '\\data\\users.json';
```

---

#### 4. os

**Purpose:** Get operating system information

```javascript
const os = require('os');

// CPU information
console.log('CPUs:', os.cpus());
console.log('CPU count:', os.cpus().length);

// Memory information
console.log('Total memory:', os.totalmem());
console.log('Free memory:', os.freemem());
console.log('Memory usage:', (1 - os.freemem() / os.totalmem()) * 100 + '%');

// OS information
console.log('Platform:', os.platform()); // linux, darwin, win32
console.log('OS Type:', os.type()); // Linux, Darwin, Windows_NT
console.log('OS Release:', os.release());
console.log('Architecture:', os.arch()); // x64, arm, etc.

// User information
console.log('Username:', os.userInfo().username);
console.log('Home directory:', os.homedir());
console.log('Temp directory:', os.tmpdir());

// System uptime
console.log('Uptime:', os.uptime(), 'seconds');

// Network interfaces
console.log('Network:', os.networkInterfaces());

// Hostname
console.log('Hostname:', os.hostname());

// End of line marker
console.log('EOL:', JSON.stringify(os.EOL)); // "\n" or "\r\n"
```

**Practical Example:**

```javascript
const os = require('os');

function getSystemInfo() {
  return {
    platform: os.platform(),
    architecture: os.arch(),
    cpus: os.cpus().length,
    totalMemory: `${(os.totalmem() / 1024 / 1024 / 1024).toFixed(2)} GB`,
    freeMemory: `${(os.freemem() / 1024 / 1024 / 1024).toFixed(2)} GB`,
    uptime: `${(os.uptime() / 3600).toFixed(2)} hours`,
    hostname: os.hostname(),
    username: os.userInfo().username
  };
}

console.log('System Info:', getSystemInfo());
```

---

#### 5. events (EventEmitter)

**Purpose:** Create and handle custom events

```javascript
const EventEmitter = require('events');

// Create event emitter
const emitter = new EventEmitter();

// Register event listener
emitter.on('userCreated', (user) => {
  console.log('New user:', user.name);
});

// Register multiple listeners
emitter.on('userCreated', (user) => {
  console.log('Sending welcome email to:', user.email);
});

// Emit event
emitter.emit('userCreated', { name: 'John', email: 'john@example.com' });

// One-time listener
emitter.once('appStarted', () => {
  console.log('App started! (fires only once)');
});

emitter.emit('appStarted');
emitter.emit('appStarted'); // Won't fire

// Remove listener
const handler = () => console.log('Event fired');
emitter.on('test', handler);
emitter.removeListener('test', handler);

// Get listener count
console.log('Listener count:', emitter.listenerCount('userCreated'));
```

**Custom EventEmitter Class:**

```javascript
const EventEmitter = require('events');

class Logger extends EventEmitter {
  log(message) {
    console.log(`[LOG] ${message}`);
    this.emit('logged', { message, timestamp: Date.now() });
  }
  
  error(message) {
    console.error(`[ERROR] ${message}`);
    this.emit('error', { message, timestamp: Date.now() });
  }
}

// Usage
const logger = new Logger();

logger.on('logged', (data) => {
  // Save to database
  console.log('Saving log:', data);
});

logger.on('error', (data) => {
  // Send alert
  console.log('Sending alert:', data);
});

logger.log('Application started');
logger.error('Something went wrong');
```

---

#### 6. stream

**Purpose:** Handle streaming data efficiently

```javascript
const { Readable, Writable, Transform, pipeline } = require('stream');

// Create readable stream
const readable = new Readable({
  read() {
    this.push('Hello ');
    this.push('World!');
    this.push(null); // End stream
  }
});

// Create writable stream
const writable = new Writable({
  write(chunk, encoding, callback) {
    console.log('Received:', chunk.toString());
    callback();
  }
});

// Pipe streams
readable.pipe(writable);

// Transform stream (modify data)
const upperCaseTransform = new Transform({
  transform(chunk, encoding, callback) {
    this.push(chunk.toString().toUpperCase());
    callback();
  }
});

// Pipeline (better than pipe)
const fs = require('fs');

pipeline(
  fs.createReadStream('input.txt'),
  upperCaseTransform,
  fs.createWriteStream('output.txt'),
  (err) => {
    if (err) {
      console.error('Pipeline failed:', err);
    } else {
      console.log('Pipeline succeeded');
    }
  }
);
```

---

#### 7. buffer

**Purpose:** Handle binary data

```javascript
// Create buffer from string
const buf1 = Buffer.from('Hello World', 'utf8');
console.log(buf1); // <Buffer 48 65 6c 6c 6f 20 57 6f 72 6c 64>
console.log(buf1.toString()); // Hello World

// Create buffer with size
const buf2 = Buffer.alloc(10); // Creates 10-byte buffer filled with zeros
const buf3 = Buffer.allocUnsafe(10); // Faster but contains old data

// Write to buffer
buf2.write('Node.js');
console.log(buf2.toString()); // Node.js

// Buffer from array
const buf4 = Buffer.from([1, 2, 3, 4, 5]);
console.log(buf4); // <Buffer 01 02 03 04 05>

// Convert to JSON
console.log(buf1.toJSON());
// { type: 'Buffer', data: [ 72, 101, 108, 108, 111, ... ] }

// Concatenate buffers
const buf5 = Buffer.concat([buf1, buf2]);

// Compare buffers
console.log(buf1.equals(buf2)); // false
console.log(Buffer.compare(buf1, buf2)); // -1, 0, or 1

// Slice buffer
const slice = buf1.slice(0, 5);
console.log(slice.toString()); // Hello

// Buffer length
console.log(buf1.length); // 11 (bytes)

// Encoding/Decoding
const base64 = buf1.toString('base64');
console.log(base64); // SGVsbG8gV29ybGQ=

const hex = buf1.toString('hex');
console.log(hex); // 48656c6c6f20576f726c64
```

---

#### 8. crypto

**Purpose:** Cryptographic functions (hashing, encryption)

```javascript
const crypto = require('crypto');

// 1. Create hash (MD5, SHA256, etc.)
const hash = crypto.createHash('sha256');
hash.update('password123');
const hashed = hash.digest('hex');
console.log('SHA256:', hashed);

// Shorthand
const hash2 = crypto.createHash('sha256')
  .update('password123')
  .digest('hex');

// 2. Generate random bytes
const randomBytes = crypto.randomBytes(16).toString('hex');
console.log('Random:', randomBytes);

// 3. HMAC (Hash-based Message Authentication Code)
const hmac = crypto.createHmac('sha256', 'secret-key');
hmac.update('message');
const signature = hmac.digest('hex');
console.log('HMAC:', signature);

// 4. Encryption/Decryption (AES)
const algorithm = 'aes-256-cbc';
const key = crypto.randomBytes(32);
const iv = crypto.randomBytes(16);

// Encrypt
function encrypt(text) {
  const cipher = crypto.createCipheriv(algorithm, key, iv);
  let encrypted = cipher.update(text, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  return encrypted;
}

// Decrypt
function decrypt(encrypted) {
  const decipher = crypto.createDecipheriv(algorithm, key, iv);
  let decrypted = decipher.update(encrypted, 'hex', 'utf8');
  decrypted += decipher.final('utf8');
  return decrypted;
}

const encrypted = encrypt('Secret message');
console.log('Encrypted:', encrypted);

const decrypted = decrypt(encrypted);
console.log('Decrypted:', decrypted);

// 5. Password hashing with scrypt (recommended)
const password = 'myPassword123';
const salt = crypto.randomBytes(16).toString('hex');

crypto.scrypt(password, salt, 64, (err, derivedKey) => {
  if (err) throw err;
  console.log('Hashed password:', derivedKey.toString('hex'));
});

// 6. UUID generation
const uuid = crypto.randomUUID();
console.log('UUID:', uuid); // e.g., 9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d
```

**Password Hashing Example:**

```javascript
const crypto = require('crypto');
const { promisify } = require('util');

const scrypt = promisify(crypto.scrypt);

async function hashPassword(password) {
  const salt = crypto.randomBytes(16).toString('hex');
  const derivedKey = await scrypt(password, salt, 64);
  return `${salt}:${derivedKey.toString('hex')}`;
}

async function verifyPassword(password, hash) {
  const [salt, key] = hash.split(':');
  const derivedKey = await scrypt(password, salt, 64);
  return key === derivedKey.toString('hex');
}

// Usage
(async () => {
  const hashedPassword = await hashPassword('myPassword123');
  console.log('Hashed:', hashedPassword);
  
  const isValid = await verifyPassword('myPassword123', hashedPassword);
  console.log('Password valid:', isValid); // true
  
  const isInvalid = await verifyPassword('wrongPassword', hashedPassword);
  console.log('Wrong password:', isInvalid); // false
})();
```

---

#### 9. util

**Purpose:** Utility functions

```javascript
const util = require('util');

// 1. Promisify (convert callback to promise)
const fs = require('fs');
const readFile = util.promisify(fs.readFile);

async function readData() {
  const data = await readFile('file.txt', 'utf8');
  console.log(data);
}

// 2. Format strings (like printf)
const formatted = util.format('%s:%s', 'foo', 'bar');
console.log(formatted); // foo:bar

const formatted2 = util.format('%d + %d = %d', 5, 10, 15);
console.log(formatted2); // 5 + 10 = 15

// 3. Inspect objects (debugging)
const obj = { name: 'John', nested: { age: 25 } };
console.log(util.inspect(obj, { colors: true, depth: null }));

// 4. Check types
console.log(util.types.isDate(new Date())); // true
console.log(util.types.isPromise(Promise.resolve())); // true
console.log(util.types.isRegExp(/test/)); // true

// 5. Deprecate functions
const deprecatedFunction = util.deprecate(
  () => console.log('Old function'),
  'deprecatedFunction() is deprecated. Use newFunction() instead.'
);

deprecatedFunction(); // Shows deprecation warning

// 6. Inherit (deprecated but still used)
function Base() {}
Base.prototype.method = function() {};

function Child() {}
util.inherits(Child, Base);

// 7. TextEncoder/TextDecoder
const encoder = new util.TextEncoder();
const encoded = encoder.encode('Hello');
console.log(encoded); // Uint8Array

const decoder = new util.TextDecoder();
const decoded = decoder.decode(encoded);
console.log(decoded); // Hello
```

---

#### 10. url

**Purpose:** Parse and manipulate URLs

```javascript
const url = require('url');

// Parse URL
const myUrl = new URL('https://example.com:8080/path?name=John&age=25#section');

console.log('Protocol:', myUrl.protocol); // https:
console.log('Host:', myUrl.host); // example.com:8080
console.log('Hostname:', myUrl.hostname); // example.com
console.log('Port:', myUrl.port); // 8080
console.log('Pathname:', myUrl.pathname); // /path
console.log('Search:', myUrl.search); // ?name=John&age=25
console.log('Hash:', myUrl.hash); // #section

// Get search params
console.log('Name:', myUrl.searchParams.get('name')); // John
console.log('Age:', myUrl.searchParams.get('age')); // 25

// Modify URL
myUrl.searchParams.set('city', 'NYC');
myUrl.searchParams.append('tag', 'important');
myUrl.searchParams.delete('age');

console.log('Updated URL:', myUrl.toString());
// https://example.com:8080/path?name=John&city=NYC&tag=important#section

// Format URL
const formattedUrl = url.format({
  protocol: 'https',
  hostname: 'example.com',
  pathname: '/users',
  query: { id: 123, role: 'admin' }
});
console.log(formattedUrl); // https://example.com/users?id=123&role=admin

// Parse query string
const querystring = require('querystring');
const params = querystring.parse('name=John&age=25&city=NYC');
console.log(params); // { name: 'John', age: '25', city: 'NYC' }

// Stringify object to query string
const qs = querystring.stringify({ name: 'John', age: 25 });
console.log(qs); // name=John&age=25
```

---

#### 11. child_process

**Purpose:** Spawn child processes, execute shell commands

```javascript
const { exec, execSync, spawn, fork } = require('child_process');

// 1. exec - Execute command (buffered output)
exec('ls -la', (error, stdout, stderr) => {
  if (error) {
    console.error('Error:', error);
    return;
  }
  console.log('Output:', stdout);
});

// 2. execSync - Synchronous version
const output = execSync('echo "Hello"').toString();
console.log(output); // Hello

// 3. spawn - Stream output (better for large output)
const ls = spawn('ls', ['-la']);

ls.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`);
});

ls.stderr.on('data', (data) => {
  console.error(`stderr: ${data}`);
});

ls.on('close', (code) => {
  console.log(`Process exited with code ${code}`);
});

// 4. fork - Run another Node.js script
// child.js
if (process.send) {
  process.send({ message: 'Hello from child' });
}

process.on('message', (msg) => {
  console.log('Child received:', msg);
});

// parent.js
const child = fork('./child.js');

child.on('message', (msg) => {
  console.log('Parent received:', msg);
});

child.send({ message: 'Hello from parent' });
```

---

#### 12. cluster

**Purpose:** Create multi-process applications

```javascript
const cluster = require('cluster');
const http = require('http');
const os = require('os');

if (cluster.isMaster) {
  const numCPUs = os.cpus().length;
  
  console.log(`Master ${process.pid} is running`);
  console.log(`Forking ${numCPUs} workers...`);
  
  // Fork workers
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
  
  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} died`);
    console.log('Starting a new worker...');
    cluster.fork();
  });
} else {
  // Workers share the same port
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Worker ${process.pid} handled request\n`);
  }).listen(3000);
  
  console.log(`Worker ${process.pid} started`);
}
```

---

#### Quick Reference: When to Use Each Module

**File Operations:** Use `fs` and `path`
```javascript
const fs = require('fs').promises;
const path = require('path');
const filePath = path.join(__dirname, 'data', 'file.txt');
const data = await fs.readFile(filePath, 'utf8');
```

**Web Server:** Use `http` or `https`
```javascript
const http = require('http');
const server = http.createServer((req, res) => {
  res.end('Hello World');
});
```

**System Info:** Use `os` and `process`
```javascript
const os = require('os');
console.log('Platform:', os.platform());
console.log('Node version:', process.version);
```

**Custom Events:** Use `events`
```javascript
const EventEmitter = require('events');
const emitter = new EventEmitter();
emitter.on('event', () => console.log('Event fired!'));
```

**Security:** Use `crypto`
```javascript
const crypto = require('crypto');
const hash = crypto.createHash('sha256').update('data').digest('hex');
```

**Large Data:** Use `stream`
```javascript
const fs = require('fs');
fs.createReadStream('large-file.txt')
  .pipe(process.stdout);
```

---

#### Best Practices

**1. Use Modern APIs:**
```javascript
// ✅ Good: Use promises
const fs = require('fs').promises;
const data = await fs.readFile('file.txt', 'utf8');

// ❌ Avoid: Callbacks when promises available
fs.readFile('file.txt', 'utf8', (err, data) => {});
```

**2. Use Async Methods:**
```javascript
// ✅ Good: Non-blocking
fs.readFile('file.txt', (err, data) => {});

// ❌ Bad: Blocks event loop
const data = fs.readFileSync('file.txt');
```

**3. Use Streams for Large Data:**
```javascript
// ✅ Good: Memory efficient
fs.createReadStream('huge-file.txt').pipe(res);

// ❌ Bad: Loads entire file into memory
const data = fs.readFileSync('huge-file.txt');
res.end(data);
```

**4. Handle Errors:**
```javascript
// ✅ Good: Error handling
try {
  const data = await fs.readFile('file.txt', 'utf8');
} catch (error) {
  console.error('Error reading file:', error);
}

// ❌ Bad: No error handling
const data = await fs.readFile('file.txt', 'utf8');
```

---

#### Summary

**Most Important Core Modules:**

```javascript
// Top 5 most used
const fs = require('fs');        // File operations
const http = require('http');    // Web servers
const path = require('path');    // Path utilities
const os = require('os');        // System info
const crypto = require('crypto'); // Security

// Essential for advanced use
const events = require('events');        // Custom events
const stream = require('stream');        // Streaming data
const child_process = require('child_process'); // Execute commands
const cluster = require('cluster');      // Multi-process
const util = require('util');           // Utilities
```

**Remember:**
- ✅ Core modules are built-in (no npm install)
- ✅ Always available in any Node.js environment
- ✅ Use async versions for better performance
- ✅ Handle errors properly
- ✅ Use streams for large data
- ✅ Check Node.js docs for latest APIs

**Quick Test:**
```javascript
// Test if module is core
const module = require('module');
console.log(module.builtinModules);
// ['fs', 'http', 'path', 'os', 'crypto', ...]
```

---

### Q1.76: What is the Assert Module in Node.js?

**Answer:**

The **assert** module provides assertion functions for testing and validation. It's primarily used for writing unit tests and ensuring code correctness by verifying assumptions.

---

#### What is Assert?

**Purpose:**
Assert functions throw errors when conditions are not met, helping you catch bugs early during development and testing.

**When to Use:**
- ✅ Writing unit tests
- ✅ Validating function inputs
- ✅ Testing invariants
- ✅ Development-time checks

**Basic Concept:**

```javascript
const assert = require('assert');

// If condition is false, throws AssertionError
assert(true);  // ✅ Pass
assert(false); // ❌ Throws error

// Like saying: "I assert this must be true"
```

---

#### Two Modes: Legacy vs Strict

```javascript
// Legacy mode (loose equality)
const assert = require('assert');

// Strict mode (recommended - uses === and deep equality)
const assert = require('assert').strict;
// or
const assert = require('assert/strict');
```

**Difference:**

```javascript
const assert = require('assert');
const strictAssert = require('assert').strict;

// Legacy uses == (loose equality)
assert.equal(1, '1');     // ✅ Pass (1 == '1')
assert.equal(1, true);    // ✅ Pass (1 == true)

// Strict uses === (strict equality)
strictAssert.equal(1, '1');  // ❌ Fail (1 !== '1')
strictAssert.equal(1, 1);    // ✅ Pass

// Always use strict mode!
```

---

#### Basic Assertions

**1. assert() - Truthy Check:**

```javascript
const assert = require('assert').strict;

// Pass if value is truthy
assert(true);
assert(1);
assert('hello');
assert({});
assert([]);

// Fail if value is falsy
assert(false);  // ❌ Throws
assert(0);      // ❌ Throws
assert('');     // ❌ Throws
assert(null);   // ❌ Throws
assert(undefined); // ❌ Throws

// With custom message
assert(value, 'Value must be truthy');
```

**2. assert.ok() - Same as assert():**

```javascript
const assert = require('assert').strict;

assert.ok(true);  // ✅ Pass
assert.ok(false); // ❌ Fail

// Useful for readable tests
assert.ok(user.isAdmin, 'User must be admin');
assert.ok(age >= 18, 'Must be 18 or older');
```

---

#### Equality Assertions

**1. assert.equal() - Strict Equality (===):**

```javascript
const assert = require('assert').strict;

// Primitive equality
assert.equal(1, 1);           // ✅ Pass
assert.equal('hello', 'hello'); // ✅ Pass
assert.equal(true, true);     // ✅ Pass

// Fails on type mismatch
assert.equal(1, '1');         // ❌ Fail
assert.equal(true, 1);        // ❌ Fail

// Reference equality for objects
const obj1 = { a: 1 };
const obj2 = { a: 1 };
const obj3 = obj1;

assert.equal(obj1, obj3);     // ✅ Pass (same reference)
assert.equal(obj1, obj2);     // ❌ Fail (different objects)
```

**2. assert.notEqual() - Not Equal (op !== value):**

```javascript
const assert = require('assert').strict;

assert.notEqual(1, 2);        // ✅ Pass
assert.notEqual('a', 'b');    // ✅ Pass
assert.notEqual(1, '1');      // ✅ Pass

assert.notEqual(1, 1);        // ❌ Fail
```

**3. assert.deepEqual() - Deep Equality:**

```javascript
const assert = require('assert').strict;

// Deep comparison of objects/arrays
const obj1 = { a: 1, b: { c: 2 } };
const obj2 = { a: 1, b: { c: 2 } };
const obj3 = { a: 1, b: { c: 3 } };

assert.deepEqual(obj1, obj2);  // ✅ Pass
assert.deepEqual(obj1, obj3);  // ❌ Fail

// Arrays
assert.deepEqual([1, 2, 3], [1, 2, 3]); // ✅ Pass
assert.deepEqual([1, 2], [1, 2, 3]);    // ❌ Fail

// Nested structures
assert.deepEqual(
  { users: [{ id: 1, name: 'Alice' }] },
  { users: [{ id: 1, name: 'Alice' }] }
); // ✅ Pass
```

**4. assert.notDeepEqual() - Not Deep Equal:**

```javascript
const assert = require('assert').strict;

assert.notDeepEqual({ a: 1 }, { a: 2 }); // ✅ Pass
assert.notDeepEqual([1, 2], [1, 3]);     // ✅ Pass
assert.notDeepEqual({ a: 1 }, { a: 1 }); // ❌ Fail
```

**5. assert.deepStrictEqual() - Deep Strict Equality:**

```javascript
const assert = require('assert').strict;

// More strict than deepEqual
const obj1 = { a: 1 };
const obj2 = { a: '1' };

assert.deepEqual(obj1, obj2);       // Depends on mode
assert.deepStrictEqual(obj1, obj2); // ❌ Always fails (1 !== '1')

// Best practice: Use this for object comparison
```

---

#### Comparison Assertions

**1. Greater Than / Less Than:**

```javascript
const assert = require('assert').strict;

// assert doesn't have built-in >, <, >=, <= 
// Use custom assertions:

function assertGreaterThan(actual, expected, message) {
  assert(actual > expected, message || `${actual} > ${expected}`);
}

function assertLessThan(actual, expected, message) {
  assert(actual < expected, message || `${actual} < ${expected}`);
}

// Usage
assertGreaterThan(10, 5);  // ✅ Pass
assertLessThan(3, 7);      // ✅ Pass
```

---

#### Type Assertions

**1. Check Types:**

```javascript
const assert = require('assert').strict;

function assertType(value, type, message) {
  assert(typeof value === type, message);
}

assertType(42, 'number');      // ✅ Pass
assertType('hello', 'string'); // ✅ Pass
assertType(true, 'boolean');   // ✅ Pass
assertType({}, 'object');      // ✅ Pass
assertType([], 'object');      // ✅ Pass (arrays are objects)

// Instance checks
assert([] instanceof Array);        // ✅ Pass
assert({} instanceof Object);       // ✅ Pass
assert(new Date() instanceof Date); // ✅ Pass
```

---

#### Error Assertions

**1. assert.throws() - Expects Error:**

```javascript
const assert = require('assert').strict;

// Function must throw
assert.throws(() => {
  throw new Error('Boom!');
}); // ✅ Pass

assert.throws(() => {
  // No error thrown
}); // ❌ Fail

// Check error message
assert.throws(
  () => { throw new Error('Not found'); },
  /Not found/
); // ✅ Pass

// Check error type
assert.throws(
  () => { throw new TypeError('Wrong type'); },
  TypeError
); // ✅ Pass

// Check error properties
assert.throws(
  () => {
    const err = new Error('Custom error');
    err.code = 'ERR_CUSTOM';
    throw err;
  },
  (err) => {
    assert.equal(err.code, 'ERR_CUSTOM');
    return true;
  }
);
```

**2. assert.doesNotThrow() - Must Not Throw:**

```javascript
const assert = require('assert').strict;

// Function must not throw
assert.doesNotThrow(() => {
  const x = 1 + 1;
}); // ✅ Pass

assert.doesNotThrow(() => {
  throw new Error('Oops');
}); // ❌ Fail
```

**3. assert.rejects() - Async Rejection (Promise):**

```javascript
const assert = require('assert').strict;

// Promise must reject
await assert.rejects(
  async () => {
    throw new Error('Async error');
  }
); // ✅ Pass

await assert.rejects(
  async () => {
    return 'success';
  }
); // ❌ Fail

// Check rejection reason
await assert.rejects(
  async () => {
    throw new Error('Database error');
  },
  /Database error/
);
```

**4. assert.doesNotReject() - Async Success:**

```javascript
const assert = require('assert').strict;

// Promise must resolve
await assert.doesNotReject(
  async () => {
    return 'success';
  }
); // ✅ Pass

await assert.doesNotReject(
  async () => {
    throw new Error('Failed');
  }
); // ❌ Fail
```

---

#### Assertion Error

**Understanding AssertionError:**

```javascript
const assert = require('assert').strict;

try {
  assert.equal(1, 2, 'Numbers must match');
} catch (error) {
  console.log(error.name);     // AssertionError
  console.log(error.message);  // Numbers must match
  console.log(error.actual);   // 1
  console.log(error.expected); // 2
  console.log(error.operator); // strictEqual
}
```

---

#### Real-World Testing Examples

**1. Testing a Function:**

```javascript
const assert = require('assert').strict;

function add(a, b) {
  return a + b;
}

// Test cases
function testAdd() {
  assert.equal(add(2, 3), 5, 'Should add two positive numbers');
  assert.equal(add(-1, 1), 0, 'Should handle negative numbers');
  assert.equal(add(0, 0), 0, 'Should handle zeros');
  
  console.log('✅ All add() tests passed');
}

testAdd();
```

**2. Testing Object Properties:**

```javascript
const assert = require('assert').strict;

function createUser(name, age) {
  return { name, age, createdAt: new Date() };
}

// Test
const user = createUser('Alice', 30);

assert.equal(user.name, 'Alice');
assert.equal(user.age, 30);
assert(user.createdAt instanceof Date);
assert.ok(user.createdAt.getTime() <= Date.now());

console.log('✅ User creation test passed');
```

**3. Testing Async Functions:**

```javascript
const assert = require('assert').strict;

async function fetchUser(id) {
  if (id <= 0) throw new Error('Invalid ID');
  return { id, name: 'User ' + id };
}

async function testFetchUser() {
  // Test success
  const user = await fetchUser(1);
  assert.deepStrictEqual(user, { id: 1, name: 'User 1' });
  
  // Test error
  await assert.rejects(
    () => fetchUser(0),
    /Invalid ID/
  );
  
  console.log('✅ Fetch user tests passed');
}

testFetchUser();
```

**4. Testing Arrays:**

```javascript
const assert = require('assert').strict;

function filterEven(numbers) {
  return numbers.filter(n => n % 2 === 0);
}

// Test
const result = filterEven([1, 2, 3, 4, 5, 6]);
assert.deepStrictEqual(result, [2, 4, 6]);

const empty = filterEven([1, 3, 5]);
assert.deepStrictEqual(empty, []);

console.log('✅ Filter tests passed');
```

**5. Complete Test Suite:**

```javascript
const assert = require('assert').strict;

class Calculator {
  add(a, b) { return a + b; }
  subtract(a, b) { return a - b; }
  multiply(a, b) { return a * b; }
  divide(a, b) {
    if (b === 0) throw new Error('Division by zero');
    return a / b;
  }
}

function runTests() {
  const calc = new Calculator();
  
  // Addition tests
  assert.equal(calc.add(2, 3), 5);
  assert.equal(calc.add(-1, 1), 0);
  
  // Subtraction tests
  assert.equal(calc.subtract(5, 3), 2);
  assert.equal(calc.subtract(0, 5), -5);
  
  // Multiplication tests
  assert.equal(calc.multiply(3, 4), 12);
  assert.equal(calc.multiply(0, 100), 0);
  
  // Division tests
  assert.equal(calc.divide(10, 2), 5);
  assert.throws(() => calc.divide(10, 0), /Division by zero/);
  
  console.log('✅ All calculator tests passed!');
}

runTests();
```

---

#### Assert vs Testing Frameworks

**Assert Module:**

```javascript
const assert = require('assert').strict;

// Manual test organization
function testMyFunction() {
  // Arrange
  const input = 'test';
  
  // Act
  const result = myFunction(input);
  
  // Assert
  assert.equal(result, expected);
}

testMyFunction();
```

**Testing Framework (Mocha/Jest):**

```javascript
// Better structure, async support, test runners
describe('myFunction', () => {
  it('should return expected result', () => {
    const result = myFunction('test');
    assert.equal(result, expected);
  });
  
  it('should handle edge cases', () => {
    assert.throws(() => myFunction(null));
  });
});
```

**When to Use Each:**

| Feature | Assert | Testing Framework |
|---------|--------|-------------------|
| Learning/Simple tests | ✅ Good | ⚠️ Overkill |
| Large projects | ⚠️ Limited | ✅ Better |
| Test organization | ❌ Manual | ✅ Built-in |
| Test runners | ❌ No | ✅ Yes |
| Async testing | ⚠️ Basic | ✅ Advanced |
| Mocking | ❌ No | ✅ Yes |
| Coverage | ❌ No | ✅ Yes |

---

#### Best Practices

**1. Always Use Strict Mode:**

```javascript
// ✅ GOOD: Strict mode
const assert = require('assert').strict;

// ❌ BAD: Legacy mode (loose equality)
const assert = require('assert');
```

**2. Provide Descriptive Messages:**

```javascript
// ❌ BAD: No context
assert.equal(result, expected);

// ✅ GOOD: Clear message
assert.equal(
  result,
  expected,
  'User name should match the input'
);
```

**3. Use Appropriate Assertions:**

```javascript
// ❌ BAD: Wrong assertion for objects
assert.equal({ a: 1 }, { a: 1 }); // Fails (reference check)

// ✅ GOOD: Deep equality for objects
assert.deepStrictEqual({ a: 1 }, { a: 1 });

// ❌ BAD: Checking error with try/catch
try {
  dangerousFunction();
  assert.fail('Should have thrown');
} catch (err) {
  assert(err.message.includes('error'));
}

// ✅ GOOD: Use assert.throws
assert.throws(
  () => dangerousFunction(),
  /error/
);
```

**4. Test Edge Cases:**

```javascript
function divide(a, b) {
  return a / b;
}

// Test normal cases AND edge cases
assert.equal(divide(10, 2), 5);      // Normal
assert.equal(divide(0, 5), 0);       // Zero numerator
assert(isNaN(divide(5, 0)));         // Division by zero
assert.equal(divide(-10, 2), -5);    // Negative numbers
assert.equal(divide(1, 3), 0.333...); // Floating point
```

**5. Keep Tests Independent:**

```javascript
// ❌ BAD: Tests depend on each other
let counter = 0;

function test1() {
  counter++;
  assert.equal(counter, 1);
}

function test2() {
  counter++; // Depends on test1!
  assert.equal(counter, 2);
}

// ✅ GOOD: Independent tests
function test1() {
  let counter = 0;
  counter++;
  assert.equal(counter, 1);
}

function test2() {
  let counter = 0;
  counter++;
  assert.equal(counter, 1); // Same test, independent
}
```

---

#### Common Patterns

**1. Custom Assertion Helpers:**

```javascript
const assert = require('assert').strict;

class TestHelpers {
  static assertArray(value, message) {
    assert(Array.isArray(value), message || 'Value must be an array');
  }
  
  static assertNotEmpty(value, message) {
    assert(value && value.length > 0, message || 'Value must not be empty');
  }
  
  static assertBetween(value, min, max, message) {
    assert(
      value >= min && value <= max,
      message || `Value must be between ${min} and ${max}`
    );
  }
}

// Usage
TestHelpers.assertArray([1, 2, 3]);
TestHelpers.assertNotEmpty('hello');
TestHelpers.assertBetween(5, 0, 10);
```

**2. Test Runner Pattern:**

```javascript
const assert = require('assert').strict;

class TestRunner {
  constructor() {
    this.tests = [];
    this.passed = 0;
    this.failed = 0;
  }
  
  test(name, fn) {
    this.tests.push({ name, fn });
  }
  
  async run() {
    console.log(`Running ${this.tests.length} tests...\n`);
    
    for (const { name, fn } of this.tests) {
      try {
        await fn();
        this.passed++;
        console.log(`✅ ${name}`);
      } catch (error) {
        this.failed++;
        console.log(`❌ ${name}`);
        console.log(`   ${error.message}\n`);
      }
    }
    
    console.log(`\n${this.passed} passed, ${this.failed} failed`);
  }
}

// Usage
const runner = new TestRunner();

runner.test('Addition works', () => {
  assert.equal(2 + 2, 4);
});

runner.test('Subtraction works', () => {
  assert.equal(5 - 3, 2);
});

runner.test('Division by zero throws', () => {
  assert.throws(() => {
    if (2 / 0 === Infinity) throw new Error('Division by zero');
  });
});

runner.run();
```

---

#### Summary

**Quick Reference:**

```javascript
const assert = require('assert').strict;

// Basic assertions
assert(value);                           // Truthy check
assert.ok(value);                        // Same as assert()

// Equality
assert.equal(actual, expected);          // Strict equality (===)
assert.notEqual(actual, expected);       // Not equal (!==)
assert.deepStrictEqual(obj1, obj2);      // Deep comparison
assert.notDeepStrictEqual(obj1, obj2);   // Not deep equal

// Errors
assert.throws(() => fn());               // Must throw
assert.doesNotThrow(() => fn());         // Must not throw
await assert.rejects(async () => fn()); // Promise must reject
await assert.doesNotReject(asyncFn());   // Promise must resolve

// Custom message
assert.equal(actual, expected, 'Custom error message');
```

**Key Points:**
- ✅ Always use strict mode: `require('assert').strict`
- ✅ Use `deepStrictEqual` for objects/arrays
- ✅ Provide descriptive error messages
- ✅ Use `assert.throws` for error testing
- ✅ Use `assert.rejects` for async errors
- ✅ Keep tests independent
- ✅ Test edge cases and error conditions
- ✅ Consider using a testing framework for larger projects

**When to Use:**
- ✅ Unit testing
- ✅ Input validation during development
- ✅ Simple test scripts
- ✅ Learning testing concepts
- ⚠️ For production validation, use custom error classes
- ⚠️ For large projects, consider Mocha/Jest/Vitest

---

### Q1.77: What is the DNS Module in Node.js?

**Answer:**

The **dns** module provides functions to perform Domain Name System (DNS) lookups and name resolution. It translates human-readable domain names (like `google.com`) into IP addresses (like `142.250.185.46`).

---

#### What is DNS?

**DNS (Domain Name System):**
- Translates domain names to IP addresses
- Like a phone book for the internet
- `www.google.com` → `172.217.164.36`

**Why Use the DNS Module:**
- ✅ Resolve domain names to IP addresses
- ✅ Reverse lookups (IP to domain)
- ✅ Get MX records (mail servers)
- ✅ Verify domain existence
- ✅ Network diagnostics

---

####Two DNS APIs

**1. dns.promises (Modern, Recommended):**

```javascript
const dns = require('dns').promises;

// Modern async/await
async function lookup() {
  const result = await dns.lookup('google.com');
  console.log(result);
}
```

**2. Callback API (Legacy):**

```javascript
const dns = require('dns');

// Old callback style
dns.lookup('google.com', (err, address) => {
  if (err) throw err;
  console.log(address);
});
```

---

#### Basic DNS Lookups

**1. dns.lookup() - Get IP Address:**

```javascript
const dns = require('dns').promises;

async function getIP(domain) {
  try {
    const { address, family } = await dns.lookup(domain);
    console.log(`${domain} → ${address} (IPv${family})`);
    return address;
  } catch (error) {
    console.error(`Failed to resolve ${domain}:`, error.message);
  }
}

// Usage
await getIP('google.com');
// google.com → 142.250.185.46 (IPv4)

await getIP('nodejs.org');
// nodejs.org → 104.20.23.46 (IPv4)
```

**2. dns.resolve4() / dns.resolve6() - Get All IPs:**

```javascript
const dns = require('dns').promises;

async function getAllIPs(domain) {
  try {
    // Get IPv4 addresses
    const ipv4 = await dns.resolve4(domain);
    console.log('IPv4:', ipv4);
    
    // Get IPv6 addresses
    const ipv6 = await dns.resolve6(domain);
    console.log('IPv6:', ipv6);
  } catch (error) {
    console.error('Error:', error.message);
  }
}

// Usage
await getAllIPs('google.com');
// IPv4: ['142.250.185.46', '142.250.185.78', ...]
// IPv6: ['2607:f8b0:4004:c07::71', ...]
```

---

#### Reverse DNS Lookup

**Get Domain from IP:**

```javascript
const dns = require('dns').promises;

async function reverseLookup(ip) {
  try {
    const hostnames = await dns.reverse(ip);
    console.log(`${ip} → ${hostnames.join(', ')}`);
    return hostnames;
  } catch (error) {
    console.error(`Reverse lookup failed for ${ip}:`, error.message);
  }
}

// Usage
await reverseLookup('8.8.8.8');
// 8.8.8.8 → dns.google

await reverseLookup('1.1.1.1');
// 1.1.1.1 → one.one.one.one
```

---

#### DNS Record Types

**1. A Records (IPv4 Addresses):**

```javascript
const dns = require('dns').promises;

const addresses = await dns.resolve('example.com', 'A');
console.log(addresses);
// ['93.184.216.34']
```

**2. AAAA Records (IPv6 Addresses):**

```javascript
const addresses = await dns.resolve('example.com', 'AAAA');
console.log(addresses);
// ['2606:2800:220:1:248:1893:25c8:1946']
```

**3. MX Records (Mail Servers):**

```javascript
const dns = require('dns').promises;

async function getMailServers(domain) {
  try {
    const mx = await dns.resolveMx(domain);
    
    // Sort by priority (lower = higher priority)
    mx.sort((a, b) => a.priority - b.priority);
    
    console.log(`Mail servers for ${domain}:`);
    mx.forEach(({ exchange, priority }) => {
      console.log(`  Priority ${priority}: ${exchange}`);
    });
    
    return mx;
  } catch (error) {
    console.error('Error:', error.message);
  }
}

// Usage
await getMailServers('gmail.com');
// Mail servers for gmail.com:
//   Priority 5: gmail-smtp-in.l.google.com
//   Priority 10: alt1.gmail-smtp-in.l.google.com
//   Priority 20: alt2.gmail-smtp-in.l.google.com
```

**4. TXT Records (Text Information):**

```javascript
const dns = require('dns').promises;

async function getTxtRecords(domain) {
  const records = await dns.resolveTxt(domain);
  console.log(`TXT records for ${domain}:`);
  records.forEach(record => {
    console.log(`  ${record.join('')}`);
  });
}

// Usage
await getTxtRecords('google.com');
// TXT records for google.com:
//   v=spf1 include:_spf.google.com ~all
//   facebook-domain-verification=...
```

**5. NS Records (Name Servers):**

```javascript
const dns = require('dns').promises;

const nameservers = await dns.resolveNs('google.com');
console.log('Name servers:', nameservers);
// ['ns1.google.com', 'ns2.google.com', 'ns3.google.com', 'ns4.google.com']
```

**6. CNAME Records (Canonical Names / Aliases):**

```javascript
const dns = require('dns').promises;

async function getCNAME(domain) {
  try {
    const cname = await dns.resolveCname(domain);
    console.log(`${domain} is an alias for ${cname}`);
  } catch (error) {
    if (error.code === 'ENODATA') {
      console.log(`${domain} is not an alias`);
    } else {
      console.error('Error:', error.message);
    }
  }
}

// Usage
await getCNAME('www.github.com');
// www.github.com is an alias for github.com
```

**7. SOA Records (Start of Authority):**

```javascript
const dns = require('dns').promises;

const soa = await dns.resolveSoa('google.com');
console.log(soa);
// {
//   nsname: 'ns1.google.com',
//   hostmaster: 'dns-admin.google.com',
//   serial: 123456789,
//   refresh: 900,
//   retry: 900,
//   expire: 1800,
//   minttl: 60
// }
```

---

#### Complete DNS Information

**Get All DNS Records:**

```javascript
const dns = require('dns').promises;

async function getDNSInfo(domain) {
  console.log(`\n📋 DNS Information for ${domain}\n`);
  
  try {
    // A Records (IPv4)
    try {
      const ipv4 = await dns.resolve4(domain);
      console.log('IPv4 Addresses:', ipv4);
    } catch (e) {
      console.log('IPv4: None');
    }
    
    // AAAA Records (IPv6)
    try {
      const ipv6 = await dns.resolve6(domain);
      console.log('IPv6 Addresses:', ipv6);
    } catch (e) {
      console.log('IPv6: None');
    }
    
    // MX Records
    try {
      const mx = await dns.resolveMx(domain);
      console.log('Mail Servers:', mx.map(m => `${m.exchange} (${m.priority})`));
    } catch (e) {
      console.log('Mail Servers: None');
    }
    
    // NS Records
    try {
      const ns = await dns.resolveNs(domain);
      console.log('Name Servers:', ns);
    } catch (e) {
      console.log('Name Servers: None');
    }
    
    // TXT Records
    try {
      const txt = await dns.resolveTxt(domain);
      console.log('TXT Records:', txt.length);
    } catch (e) {
      console.log('TXT Records: None');
    }
    
    // CNAME
    try {
      const cname = await dns.resolveCname(domain);
      console.log('CNAME:', cname);
    } catch (e) {
      console.log('CNAME: Not an alias');
    }
    
  } catch (error) {
    console.error('Error:', error.message);
  }
}

// Usage
await getDNSInfo('google.com');
```

---

#### DNS Resolver Options

**Custom DNS Server:**

```javascript
const dns = require('dns').promises;

// Create custom resolver
const resolver = new dns.Resolver();

// Use Google's DNS servers
resolver.setServers([
  '8.8.8.8',       // Google DNS
  '8.8.4.4',       // Google DNS Secondary
  '1.1.1.1',       // Cloudflare DNS
  '1.0.0.1'        // Cloudflare DNS Secondary
]);

// Use custom resolver
const addresses = await resolver.resolve4('example.com');
console.log(addresses);
```

**Get Current DNS Servers:**

```javascript
const dns = require('dns');

const servers = dns.getServers();
console.log('DNS Servers:', servers);
// ['192.168.1.1', '8.8.8.8']
```

**Set Custom DNS Servers:**

```javascript
const dns = require('dns');

dns.setServers([
  '8.8.8.8',   // Google DNS
  '1.1.1.1'    // Cloudflare DNS
]);

console.log('Updated servers:', dns.getServers());
```

---

#### Real-World Examples

**1. Check if Domain Exists:**

```javascript
const dns = require('dns').promises;

async function domainExists(domain) {
  try {
    await dns.lookup(domain);
    return true;
  } catch (error) {
    if (error.code === 'ENOTFOUND') {
      return false;
    }
    throw error;
  }
}

// Usage
console.log(await domainExists('google.com'));    // true
console.log(await domainExists('doesnotexist123.com')); // false
```

**2. Find Fastest DNS Server:**

```javascript
const dns = require('dns').promises;

async function findFastestDNS(domain) {
  const servers = [
    { name: 'Google', ip: '8.8.8.8' },
    { name: 'Cloudflare', ip: '1.1.1.1' },
    { name: 'OpenDNS', ip: '208.67.222.222' },
    { name: 'Quad9', ip: '9.9.9.9' }
  ];
  
  const results = [];
  
  for (const server of servers) {
    const resolver = new dns.Resolver();
    resolver.setServers([server.ip]);
    
    const start = Date.now();
    try {
      await resolver.resolve4(domain);
      const time = Date.now() - start;
      results.push({ ...server, time });
    } catch (error) {
      results.push({ ...server, time: Infinity, error: error.message });
    }
  }
  
  // Sort by speed
  results.sort((a, b) => a.time - b.time);
  
  console.log('DNS Server Speed Test:\n');
  results.forEach(({ name, ip, time }) => {
    if (time === Infinity) {
      console.log(`❌ ${name} (${ip}): Failed`);
    } else {
      console.log(`✅ ${name} (${ip}): ${time}ms`);
    }
  });
  
  return results[0];
}

// Usage
const fastest = await findFastestDNS('google.com');
console.log(`\nFastest: ${fastest.name} (${fastest.time}ms)`);
```

**3. Email Server Validator:**

```javascript
const dns = require('dns').promises;

async function validateEmailDomain(email) {
  const domain = email.split('@')[1];
  
  if (!domain) {
    return { valid: false, reason: 'Invalid email format' };
  }
  
  try {
    // Check if domain exists
    await dns.lookup(domain);
    
    // Check if domain has mail servers
    const mx = await dns.resolveMx(domain);
    
    if (mx.length === 0) {
      return { valid: false, reason: 'No mail servers found' };
    }
    
    return {
      valid: true,
      domain,
      mailServers: mx.map(m => m.exchange)
    };
  } catch (error) {
    return { valid: false, reason: error.message };
  }
}

// Usage
console.log(await validateEmailDomain('user@gmail.com'));
// { valid: true, domain: 'gmail.com', mailServers: ['gmail-smtp-in.l.google.com', ...] }

console.log(await validateEmailDomain('user@fakédomain123.com'));
// { valid: false, reason: 'Domain not found' }
```

**4. Load Balancer DNS Check:**

```javascript
const dns = require('dns').promises;

async function getLoadBalancedServers(domain) {
  try {
    const addresses = await dns.resolve4(domain);
    
    console.log(`${domain} load balances across ${addresses.length} servers:`);
    addresses.forEach((ip, index) => {
      console.log(`  Server ${index + 1}: ${ip}`);
    });
    
    return addresses;
  } catch (error) {
    console.error('Error:', error.message);
  }
}

// Usage
await getLoadBalancedServers('google.com');
// google.com load balances across 6 servers:
//   Server 1: 142.250.185.46
//   Server 2: 142.250.185.78
//   ...
```

---

#### Error Handling

**Common DNS Errors:**

```javascript
const dns = require('dns').promises;

async function safeLookup(domain) {
  try {
    const result = await dns.lookup(domain);
    return result;
  } catch (error) {
    switch (error.code) {
      case 'ENOTFOUND':
        console.error(`Domain not found: ${domain}`);
        break;
      case 'ENODATA':
        console.error(`No data returned for: ${domain}`);
        break;
      case 'ETIMEOUT':
        console.error(`DNS lookup timed out for: ${domain}`);
        break;
      case 'ESERVFAIL':
        console.error(`DNS server failure for: ${domain}`);
        break;
      case 'EREFUSED':
        console.error(`DNS server refused query for: ${domain}`);
        break;
      default:
        console.error(`DNS error for ${domain}:`, error.message);
    }
    return null;
  }
}
```

---

#### dns.lookup() vs dns.resolve()

**Key Differences:**

| Feature | dns.lookup() | dns.resolve4/6() |
|---------|--------------|------------------|
| **Method** | Uses OS resolver | Direct DNS query |
| **Caching** | Uses OS cache | No caching |
| **Speed** | Usually faster | Slower (fresh lookup) |
| **IP Version** | Returns 1 IP | Returns all IPs |
| **/etc/hosts** | ✅ Respects | ❌ Ignores |
| **Use Case** | Most apps | DNS diagnostics |

**Example:**

```javascript
const dns = require('dns').promises;

// lookup() - Uses OS, returns 1 IP
const { address } = await dns.lookup('google.com');
console.log('lookup:', address);
// 142.250.185.46 (one IP)

// resolve4() - Direct DNS, returns all IPs
const addresses = await dns.resolve4('google.com');
console.log('resolve4:', addresses);
// ['142.250.185.46', '142.250.185.78', ...] (multiple IPs)
```

**When to Use Each:**

```javascript
// ✅ Use lookup() for:
// - Normal application requests
// - Connect to services
// - Most common use cases
const { address } = await dns.lookup('example.com');

// ✅ Use resolve4/6() for:
// - DNS diagnostics
// - Need all IPs
// - Bypass OS cache
// - DNS record testing
const addresses = await dns.resolve4('example.com');
```

---

#### Best Practices

**1. Handle Errors Gracefully:**

```javascript
const dns = require('dns').promises;

async function safeResolve(domain) {
  try {
    return await dns.lookup(domain);
  } catch (error) {
    console.error(`Failed to resolve ${domain}:`, error.code);
    return null;
  }
}
```

**2. Use Timeouts:**

```javascript
const dns = require('dns').promises;

async function lookupWithTimeout(domain, timeout = 5000) {
  const controller = new AbortController();
  const timer = setTimeout(() => controller.abort(), timeout);
  
  try {
    const result = await dns.lookup(domain);
    clearTimeout(timer);
    return result;
  } catch (error) {
    clearTimeout(timer);
    if (error.name === 'AbortError') {
      throw new Error('DNS lookup timeout');
    }
    throw error;
  }
}
```

**3. Cache Results:**

```javascript
const dns = require('dns').promises;

class DNSCache {
  constructor(ttl = 300000) { // 5 minutes default
    this.cache = new Map();
    this.ttl = ttl;
  }
  
  async lookup(domain) {
    const cached = this.cache.get(domain);
    
    if (cached && Date.now() - cached.timestamp < this.ttl) {
      return cached.result;
    }
    
    const result = await dns.lookup(domain);
    this.cache.set(domain, { result, timestamp: Date.now() });
    
    return result;
  }
  
  clear() {
    this.cache.clear();
  }
}

// Usage
const cache = new DNSCache();
const result1 = await cache.lookup('google.com'); // Fresh lookup
const result2 = await cache.lookup('google.com'); // From cache
```

**4. Use Specific DNS Servers:**

```javascript
const dns = require('dns').promises;

// Use reliable DNS servers
const resolver = new dns.Resolver();
resolver.setServers([
  '8.8.8.8',   // Google DNS
  '1.1.1.1',   // Cloudflare DNS
]);

const result = await resolver.resolve4('example.com');
```

---

#### Performance Tips

**1. Parallel Lookups:**

```javascript
const dns = require('dns').promises;

// ❌ BAD: Sequential lookups
async function sequential(domains) {
  const results = [];
  for (const domain of domains) {
    results.push(await dns.lookup(domain));
  }
  return results;
}

// ✅ GOOD: Parallel lookups
async function parallel(domains) {
  return Promise.all(
    domains.map(domain => dns.lookup(domain))
  );
}

// Much faster!
const domains = ['google.com', 'github.com', 'nodejs.org'];
const results = await parallel(domains);
```

**2. Use Promise.allSettled for Fault Tolerance:**

```javascript
const dns = require('dns').promises;

async function lookupMany(domains) {
  const promises = domains.map(async domain => {
    try {
      const result = await dns.lookup(domain);
      return { domain, ...result, success: true };
    } catch (error) {
      return { domain, error: error.message, success: false };
    }
  });
  
  return Promise.all(promises);
}

// Usage
const results = await lookupMany(['google.com', 'invalid.domain', 'github.com']);
results.forEach(({ domain, address, success }) => {
  if (success) {
    console.log(`✅ ${domain}: ${address}`);
  } else {
    console.log(`❌ ${domain}: Failed`);
  }
});
```

---

#### Summary

**Quick Reference:**

```javascript
const dns = require('dns').promises;

// Basic lookup
const { address } = await dns.lookup('google.com');

// Get all IPv4 addresses
const ipv4 = await dns.resolve4('google.com');

// Get all IPv6 addresses
const ipv6 = await dns.resolve6('google.com');

// Reverse lookup
const hostnames = await dns.reverse('8.8.8.8');

// Mail servers
const mx = await dns.resolveMx('gmail.com');

// Name servers
const ns = await dns.resolveNs('google.com');

// TXT records
const txt = await dns.resolveTxt('google.com');

// Custom DNS server
const resolver = new dns.Resolver();
resolver.setServers(['8.8.8.8']);
const result = await resolver.resolve4('example.com');
```

**Key Points:**
- ✅ Use `dns.promises` for async/await
- ✅ Use `dns.lookup()` for most applications
- ✅ Use `dns.resolve4/6()` for DNS diagnostics
- ✅ Always handle errors (ENOTFOUND, ETIMEOUT)
- ✅ Cache results to improve performance
- ✅ Use parallel lookups for multiple domains
- ✅ Set custom DNS servers for reliability
- ✅ Implement timeouts for DNS queries

**Common Use Cases:**
- ✅ Validate email domains
- ✅ Check domain availability
- ✅ Get mail server information
- ✅ Network diagnostics
- ✅ Load balancer discovery
- ✅ DNS health checks
- ✅ Reverse IP lookups

---

### Q2: What are Streams in Node.js and when should you use them?

**Answer:**

Streams are like drinking water from a fountain vs. filling a bucket first.

**Benefits:**
- Memory efficient (process chunks, not entire file)
- Time efficient (start processing before everything loads)

**Types of Streams:**

**1. Readable (Reading data):**
```javascript
const fs = require('fs');

// ❌ Bad: Load entire file into memory (1GB file = 1GB RAM)
const data = fs.readFileSync('huge-file.txt');
console.log(data);

// ✅ Good: Process in chunks (always ~64KB RAM)
const stream = fs.createReadStream('huge-file.txt', {
  encoding: 'utf8',
  highWaterMark: 64 * 1024 // 64KB chunks
});

stream.on('data', (chunk) => {
  console.log(`Received ${chunk.length} bytes`);
  // Process chunk
});

stream.on('end', () => {
  console.log('Finished reading');
});

stream.on('error', (error) => {
  console.error('Error:', error);
});
```

**2. Writable (Writing data):**
```javascript
const writable = fs.createWriteStream('output.txt');

writable.write('First line\n');
writable.write('Second line\n');
writable.end('Last line\n'); // Signals end

writable.on('finish', () => {
  console.log('All data written');
});
```

**3. Duplex (Both read and write):**
```javascript
const { Duplex } = require('stream');

const duplexStream = new Duplex({
  read(size) {
    this.push('data from read');
    this.push(null); // End of stream
  },
  write(chunk, encoding, callback) {
    console.log('Writing:', chunk.toString());
    callback();
  }
});
```

**4. Transform (Modify data while streaming):**
```javascript
const { Transform } = require('stream');

// Convert text to uppercase while streaming
const upperCaseTransform = new Transform({
  transform(chunk, encoding, callback) {
    this.push(chunk.toString().toUpperCase());
    callback();
  }
});

// Pipeline: Read → Transform → Write
fs.createReadStream('input.txt')
  .pipe(upperCaseTransform)
  .pipe(fs.createWriteStream('output.txt'));
```

**Real-World Use Cases:**

**File Upload:**
```javascript
const express = require('express');
const multer = require('multer');
const { pipeline } = require('stream/promises');

app.post('/upload', multer().single('file'), async (req, res) => {
  try {
    // Stream file directly to S3 (no temp storage needed)
    await pipeline(
      req.file.stream,
      uploadToS3Stream('my-bucket', 'file.pdf')
    );
    res.json({ success: true });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

**CSV Processing:**
```javascript
const csv = require('csv-parser');

fs.createReadStream('huge-data.csv')
  .pipe(csv())
  .pipe(new Transform({
    objectMode: true,
    transform(row, encoding, callback) {
      // Process each row
      const processed = processRow(row);
      this.push(processed);
      callback();
    }
  }))
  .pipe(writeToDatabase());
```

---

### Q2.25: What is a Web Server? (Introduction)

**Answer:**

A **web server** is a software application that accepts HTTP requests from clients (browsers, mobile apps, APIs) and returns HTTP responses. It's the foundation of the internet, handling billions of requests daily.

---

#### What is a Web Server?

**Definition:**
A web server is software that:
1. **Listens** for incoming network requests on a specific port (usually 80 for HTTP, 443 for HTTPS)
2. **Processes** the request (routing, authentication, database queries)
3. **Responds** with data (HTML, JSON, files, images)

**Think of it like a restaurant:**
- **Client (Browser)** = Customer ordering food
- **Request (HTTP Request)** = Order placed
- **Web Server** = Kitchen that prepares food
- **Response (HTTP Response)** = Food delivered to customer

---

#### How Web Servers Work

**Basic Flow:**

```
┌─────────┐           ┌─────────────┐           ┌──────────┐
│ Browser │  Request  │ Web Server  │   Query   │ Database │
│ Client  │ ───────>  │   Node.js   │ <──────>  │  MySQL   │
│         │ <─────── │             │           │          │
└─────────┘  Response └─────────────┘           └──────────┘
```

**Step-by-Step Process:**

1. **Client makes request:**
   ```
   GET /users/123 HTTP/1.1
   Host: api.example.com
   User-Agent: Mozilla/5.0
   ```

2. **Server receives and processes:**
   - Parse URL: `/users/123`
   - Extract route: `GET /users/:id`
   - Match route handler
   - Execute business logic
   - Query database if needed

3. **Server sends response:**
   ```
   HTTP/1.1 200 OK
   Content-Type: application/json
   Content-Length: 85
   
   {"id": 123, "name": "John Doe", "email": "john@example.com"}
   ```

4. **Client receives and renders:**
   - Parse JSON
   - Update UI
   - Display to user

---

#### HTTP Protocol Basics

**What is HTTP?**
**HTTP (HyperText Transfer Protocol)** is the foundation of data communication on the web. It's a request-response protocol between client and server.

**HTTP Request Structure:**

```
[METHOD] [URL] [HTTP VERSION]
[HEADERS]
[BLANK LINE]
[BODY]
```

**Example:**
```http
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer token123
Content-Length: 58

{"name": "John Doe", "email": "john@example.com"}
```

**HTTP Response Structure:**

```
[HTTP VERSION] [STATUS CODE] [STATUS MESSAGE]
[HEADERS]
[BLANK LINE]
[BODY]
```

**Example:**
```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/users/123
Content-Length: 85

{"id": 123, "name": "John Doe", "email": "john@example.com"}
```

---

#### HTTP Methods (Verbs)

| Method | Purpose | Has Body | Idempotent | Safe |
|--------|---------|----------|------------|------|
| **GET** | Retrieve data | No | ✅ Yes | ✅ Yes |
| **POST** | Create new resource | Yes | ❌ No | ❌ No |
| **PUT** | Update/Replace resource | Yes | ✅ Yes | ❌ No |
| **PATCH** | Partial update | Yes | ❌ No | ❌ No |
| **DELETE** | Remove resource | No | ✅ Yes | ❌ No |
| **HEAD** | Get headers only | No | ✅ Yes | ✅ Yes |
| **OPTIONS** | Get supported methods | No | ✅ Yes | ✅ Yes |

**Examples:**

```javascript
// GET - Retrieve data
GET /api/users/123

// POST - Create new user
POST /api/users
Body: {"name": "John", "email": "john@example.com"}

// PUT - Replace entire user
PUT /api/users/123
Body: {"name": "John Doe", "email": "john@example.com", "age": 30}

// PATCH - Update specific fields
PATCH /api/users/123
Body: {"age": 31}

// DELETE - Remove user
DELETE /api/users/123
```

---

#### HTTP Status Codes

**1xx - Informational:**
- `100 Continue` - Continue with request
- `101 Switching Protocols` - Switching to WebSocket

**2xx - Success:**
- `200 OK` - Request succeeded
- `201 Created` - Resource created
- `204 No Content` - Success but no response body
- `206 Partial Content` - Partial response (streaming)

**3xx - Redirection:**
- `301 Moved Permanently` - Resource moved permanently
- `302 Found` - Temporary redirect
- `304 Not Modified` - Use cached version

**4xx - Client Errors:**
- `400 Bad Request` - Invalid request syntax
- `401 Unauthorized` - Authentication required
- `403 Forbidden` - No permission
- `404 Not Found` - Resource doesn't exist
- `405 Method Not Allowed` - Wrong HTTP method
- `409 Conflict` - Conflict with current state
- `422 Unprocessable Entity` - Validation failed
- `429 Too Many Requests` - Rate limit exceeded

**5xx - Server Errors:**
- `500 Internal Server Error` - Generic server error
- `501 Not Implemented` - Feature not supported
- `502 Bad Gateway` - Invalid response from upstream
- `503 Service Unavailable` - Server overloaded
- `504 Gateway Timeout` - Upstream timeout

**Usage Example:**

```javascript
const http = require('http');

http.createServer((req, res) => {
  if (req.url === '/users' && req.method === 'GET') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ users: [] }));
  } else if (req.url === '/users' && req.method === 'POST') {
    res.writeHead(201, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ id: 1, created: true }));
  } else if (req.method === 'DELETE') {
    res.writeHead(204); // No content
    res.end();
  } else if (req.url.startsWith('/admin')) {
    res.writeHead(403, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ error: 'Forbidden' }));
  } else {
    res.writeHead(404, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ error: 'Not Found' }));
  }
}).listen(3000);
```

---

#### HTTP Headers

**Common Request Headers:**

```javascript
// Authentication
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

// Content negotiation
Accept: application/json
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br

// Content type
Content-Type: application/json
Content-Length: 1234

// Caching
Cache-Control: no-cache
If-None-Match: "686897696a7c876b7e"

// Client info
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Referer: https://example.com/page

// Custom headers
X-API-Key: abc123
X-Request-ID: uuid-1234
```

**Common Response Headers:**

```javascript
// Content info
Content-Type: application/json; charset=utf-8
Content-Length: 1234
Content-Encoding: gzip

// Caching
Cache-Control: public, max-age=3600
ETag: "686897696a7c876b7e"
Expires: Wed, 21 Jan 2026 07:28:00 GMT

// Security
Strict-Transport-Security: max-age=31536000
X-Content-Type-Options: nosniff
X-Frame-Options: DENY

// CORS
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization

// Location (for redirects/created resources)
Location: /api/users/123

// Rate limiting
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1642752000
```

**Setting Headers in Node.js:**

```javascript
const http = require('http');

http.createServer((req, res) => {
  // Method 1: writeHead (sets status + headers)
  res.writeHead(200, {
    'Content-Type': 'application/json',
    'Cache-Control': 'no-cache',
    'X-Custom-Header': 'value'
  });
  
  // Method 2: setHeader (individual headers)
  res.setHeader('Content-Type', 'application/json');
  res.setHeader('Cache-Control', 'no-cache');
  
  // Method 3: Express (more convenient)
  // res.set('Content-Type', 'application/json');
  // res.set('Cache-Control', 'no-cache');
  
  res.end(JSON.stringify({ message: 'Hello' }));
}).listen(3000);
```

---

#### Types of Web Servers

**1. Static Web Server:**
Serves files directly from disk (HTML, CSS, JS, images).

```javascript
const http = require('http');
const fs = require('fs');
const path = require('path');

http.createServer((req, res) => {
  const filePath = path.join(__dirname, 'public', req.url);
  
  fs.readFile(filePath, (err, data) => {
    if (err) {
      res.writeHead(404);
      res.end('File not found');
    } else {
      res.writeHead(200);
      res.end(data);
    }
  });
}).listen(3000);
```

**2. Dynamic Web Server:**
Generates content on-the-fly based on request.

```javascript
const http = require('http');

http.createServer((req, res) => {
  const data = {
    message: 'Hello',
    timestamp: new Date().toISOString(),
    random: Math.random()
  };
  
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify(data));
}).listen(3000);
```

**3. API Server (RESTful):**
Provides data through REST endpoints.

```javascript
const express = require('express');
const app = express();

// Database simulation
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' }
];

// REST endpoints
app.get('/api/users', (req, res) => {
  res.json(users);
});

app.get('/api/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  user ? res.json(user) : res.status(404).json({ error: 'Not found' });
});

app.post('/api/users', (req, res) => {
  const user = { id: users.length + 1, name: req.body.name };
  users.push(user);
  res.status(201).json(user);
});

app.listen(3000);
```

**4. Proxy/Reverse Proxy:**
Forwards requests to other servers.

```javascript
const http = require('http');
const httpProxy = require('http-proxy');

const proxy = httpProxy.createProxyServer({});

http.createServer((req, res) => {
  // Forward to backend server
  proxy.web(req, res, { target: 'http://localhost:4000' });
}).listen(3000);
```

**5. WebSocket Server:**
Real-time bidirectional communication.

```javascript
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 3000 });

wss.on('connection', (ws) => {
  ws.on('message', (message) => {
    // Broadcast to all clients
    wss.clients.forEach(client => {
      if (client.readyState === WebSocket.OPEN) {
        client.send(message);
      }
    });
  });
});
```

---

#### Popular Web Server Software

**1. Apache HTTP Server:**
- Most popular traditional web server
- Configuration-based (`.htaccess`)
- Multi-threaded
- Great for serving static files

**2. Nginx:**
- High-performance, lightweight
- Event-driven architecture
- Often used as reverse proxy
- Handles static files efficiently

**3. Node.js (Built-in http module):**
- JavaScript-based
- Event-driven, non-blocking
- Great for APIs and real-time apps
- Single-threaded with event loop

**4. Express.js (Node.js framework):**
- Most popular Node.js framework
- Minimal and flexible
- Rich ecosystem of middleware

**5. Tomcat (Java):**
- Java servlet container
- Enterprise applications

**6. IIS (Windows):**
- Microsoft's web server
- Integrated with Windows Server

---

#### Node.js as a Web Server

**Why Node.js for Web Servers?**

✅ **JavaScript Everywhere:** Same language for frontend and backend
✅ **Non-Blocking I/O:** Handles thousands of concurrent connections
✅ **Fast Development:** npm ecosystem with millions of packages
✅ **Real-Time:** Built for WebSockets and real-time apps
✅ **Microservices:** Perfect for API-first architecture
✅ **Scalable:** Horizontal scaling with clustering

**Basic Node.js Web Server:**

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  // req = IncomingMessage (readable stream)
  // res = ServerResponse (writable stream)
  
  console.log(`${req.method} ${req.url}`);
  
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World\n');
});

server.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

**With Express (Recommended):**

```javascript
const express = require('express');
const app = express();

// Middleware
app.use(express.json());
app.use(express.static('public'));

// Routes
app.get('/', (req, res) => {
  res.send('Hello World');
});

app.get('/api/users', (req, res) => {
  res.json({ users: [] });
});

app.post('/api/users', (req, res) => {
  const user = req.body;
  res.status(201).json({ id: 1, ...user });
});

// Start server
app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

---

#### Request-Response Cycle

**Complete Flow in Node.js:**

```javascript
const express = require('express');
const app = express();

// 1. Request comes in
app.use((req, res, next) => {
  console.log('1. Request received:', req.method, req.url);
  next();
});

// 2. Parse JSON body
app.use(express.json());

// 3. Authentication middleware
app.use((req, res, next) => {
  console.log('2. Authenticating...');
  const token = req.headers.authorization;
  if (!token && req.url.startsWith('/api/private')) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  next();
});

// 4. Route handler
app.get('/api/users', async (req, res) => {
  console.log('3. Fetching users from database...');
  
  try {
    const users = await db.getUsers();
    console.log('4. Sending response...');
    res.json(users);
  } catch (error) {
    console.log('4. Error occurred');
    res.status(500).json({ error: 'Internal server error' });
  }
});

// 5. Response sent
app.use((req, res, next) => {
  console.log('5. Response sent');
});

app.listen(3000);
```

---

#### Web Server Architecture Patterns

**1. Single Server:**
```
Client → Web Server → Database
```
Simple but doesn't scale.

**2. Load Balanced:**
```
                  ┌─ Web Server 1 ─┐
Client → Load Balancer ─ Web Server 2 ─┤→ Database
                  └─ Web Server 3 ─┘
```
Horizontal scaling for high traffic.

**3. Microservices:**
```
               ┌─ User Service → User DB
Client → API Gateway ─ Order Service → Order DB
               └─ Payment Service → Payment DB
```
Separate services for different features.

**4. Serverless:**
```
Client → API Gateway → Lambda Functions → Database
```
Auto-scaling, pay-per-request.

---

#### Performance Considerations

**1. Concurrency Model:**

```javascript
// Traditional (Thread-per-request) - PHP, Java
// 1000 connections = 1000 threads = ~1GB RAM

// Node.js (Event Loop)
// 1000 connections = 1 thread = ~100MB RAM

const server = http.createServer((req, res) => {
  // This doesn't block other requests!
  db.query('SELECT * FROM users', (err, users) => {
    res.json(users);
  });
});
```

**2. Clustering (Multi-Core):**

```javascript
const cluster = require('cluster');
const http = require('http');
const os = require('os');

if (cluster.isMaster) {
  // Fork workers for each CPU core
  for (let i = 0; i < os.cpus().length; i++) {
    cluster.fork();
  }
} else {
  // Workers share the same port
  http.createServer((req, res) => {
    res.end('Hello from worker ' + process.pid);
  }).listen(3000);
}
```

**3. Caching:**

```javascript
const cache = new Map();

app.get('/api/users', async (req, res) => {
  // Check cache first
  if (cache.has('users')) {
    return res.json(cache.get('users'));
  }
  
  // Fetch from database
  const users = await db.getUsers();
  
  // Cache for 1 minute
  cache.set('users', users);
  setTimeout(() => cache.delete('users'), 60000);
  
  res.json(users);
});
```

---

#### Security Basics

**1. HTTPS (SSL/TLS):**

```javascript
const https = require('https');
const fs = require('fs');

const options = {
  key: fs.readFileSync('private-key.pem'),
  cert: fs.readFileSync('certificate.pem')
};

https.createServer(options, (req, res) => {
  res.end('Secure connection!');
}).listen(443);
```

**2. Common Security Headers:**

```javascript
app.use((req, res, next) => {
  // Prevent XSS attacks
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  
  // HTTPS only
  res.setHeader('Strict-Transport-Security', 'max-age=31536000');
  
  // Content Security Policy
  res.setHeader('Content-Security-Policy', "default-src 'self'");
  
  next();
});
```

**3. Rate Limiting:**

```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});

app.use('/api/', limiter);
```

---

#### Summary

**Key Concepts:**

| Concept | Description |
|---------|-------------|
| **Web Server** | Software that handles HTTP requests and responses |
| **HTTP** | Protocol for client-server communication |
| **Request** | Client asks server for resource |
| **Response** | Server sends data back to client |
| **Port** | Network endpoint (80 for HTTP, 443 for HTTPS) |
| **Status Code** | Indicates request success/failure (200, 404, 500) |
| **Headers** | Metadata about request/response |
| **Body** | Actual content/data |

**Node.js Advantages:**
- ✅ Non-blocking I/O (high concurrency)
- ✅ JavaScript everywhere
- ✅ Rich ecosystem (npm)
- ✅ Great for APIs and real-time apps

**Quick Start:**

```javascript
// Simplest web server
const http = require('http');
http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World');
}).listen(3000);

// With Express (recommended)
const express = require('express');
const app = express();
app.get('/', (req, res) => res.send('Hello World'));
app.listen(3000);
```

**Next Steps:**
- Learn Express.js framework
- Understand routing and middleware
- Study REST API design
- Explore authentication (JWT, OAuth)
- Learn database integration

---

### Q2.5: Can You Create a Node.js Server Without Express?

**Answer:**

**Yes! Node.js has built-in `http` and `https` modules that can create web servers without any framework.**

---

#### Why Use Built-in HTTP Module?

**Advantages:**
- ✅ Zero dependencies (no npm packages needed)
- ✅ Smaller bundle size
- ✅ More control over server behavior
- ✅ Better understanding of how HTTP works
- ✅ Faster startup time
- ✅ No framework overhead

**When to Use:**
- Simple APIs or microservices
- Learning HTTP fundamentals
- Performance-critical applications
- Minimal dependency requirements
- Custom server logic needed

**When to Use Express Instead:**
- Need middleware ecosystem
- Complex routing requirements
- Rapid development needed
- Large team with Express experience
- Want community plugins

---

#### Basic HTTP Server (Vanilla Node.js)

**Simplest Server:**

```javascript
// server.js
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello World!');
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}/`);
});
```

**Run:**
```bash
node server.js
```

---

#### Handling Different Routes

**Manual Routing:**

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  const { method, url } = req;
  
  // Set common headers
  res.setHeader('Content-Type', 'application/json');
  
  // Route: GET /
  if (method === 'GET' && url === '/') {
    res.writeHead(200);
    res.end(JSON.stringify({ message: 'Welcome to API' }));
  }
  
  // Route: GET /api/users
  else if (method === 'GET' && url === '/api/users') {
    res.writeHead(200);
    res.end(JSON.stringify({
      users: [
        { id: 1, name: 'John' },
        { id: 2, name: 'Jane' }
      ]
    }));
  }
  
  // Route: POST /api/users
  else if (method === 'POST' && url === '/api/users') {
    let body = '';
    
    req.on('data', chunk => {
      body += chunk.toString();
    });
    
    req.on('end', () => {
      try {
        const data = JSON.parse(body);
        res.writeHead(201);
        res.end(JSON.stringify({
          message: 'User created',
          user: data
        }));
      } catch (error) {
        res.writeHead(400);
        res.end(JSON.stringify({ error: 'Invalid JSON' }));
      }
    });
  }
  
  // Route: 404 Not Found
  else {
    res.writeHead(404);
    res.end(JSON.stringify({ error: 'Route not found' }));
  }
});

server.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

---

#### Handling URL Parameters

**Query Parameters & Path Parameters:**

```javascript
const http = require('http');
const url = require('url');

const server = http.createServer((req, res) => {
  const parsedUrl = url.parse(req.url, true);
  const pathname = parsedUrl.pathname;
  const query = parsedUrl.query;
  
  res.setHeader('Content-Type', 'application/json');
  
  // GET /api/users?role=admin&limit=10
  if (pathname === '/api/users') {
    const role = query.role || 'all';
    const limit = parseInt(query.limit) || 10;
    
    res.writeHead(200);
    res.end(JSON.stringify({
      filters: { role, limit },
      users: [] // Your data here
    }));
  }
  
  // GET /api/users/:id (e.g., /api/users/123)
  else if (pathname.startsWith('/api/users/')) {
    const userId = pathname.split('/')[3];
    
    res.writeHead(200);
    res.end(JSON.stringify({
      user: { id: userId, name: 'User ' + userId }
    }));
  }
  
  else {
    res.writeHead(404);
    res.end(JSON.stringify({ error: 'Not found' }));
  }
});

server.listen(3000);
```

---

#### Advanced Routing with RegEx

**Pattern Matching:**

```javascript
const http = require('http');
const url = require('url');

// Define routes with regex patterns
const routes = [
  {
    pattern: /^\/$/,
    method: 'GET',
    handler: (req, res) => {
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ message: 'Home' }));
    }
  },
  {
    pattern: /^\/api\/users$/,
    method: 'GET',
    handler: (req, res) => {
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ users: [] }));
    }
  },
  {
    pattern: /^\/api\/users\/(\d+)$/,
    method: 'GET',
    handler: (req, res, matches) => {
      const userId = matches[1];
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ user: { id: userId } }));
    }
  }
];

const server = http.createServer((req, res) => {
  const { pathname } = url.parse(req.url);
  const method = req.method;
  
  // Find matching route
  for (const route of routes) {
    const matches = pathname.match(route.pattern);
    
    if (matches && route.method === method) {
      return route.handler(req, res, matches);
    }
  }
  
  // 404 if no route matches
  res.writeHead(404, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({ error: 'Not found' }));
});

server.listen(3000);
```

---

#### Middleware Pattern (Without Express)

**Creating Middleware Chain:**

```javascript
const http = require('http');

// Middleware function type
function middleware(req, res, next) {
  // Do something
  next(); // Call next middleware
}

// Logging middleware
function logger(req, res, next) {
  console.log(`${req.method} ${req.url} - ${new Date().toISOString()}`);
  next();
}

// CORS middleware
function cors(req, res, next) {
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
  next();
}

// Auth middleware
function authenticate(req, res, next) {
  const token = req.headers.authorization;
  
  if (!token) {
    res.writeHead(401, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ error: 'No token provided' }));
    return;
  }
  
  // Verify token (simplified)
  req.user = { id: 1, name: 'User' };
  next();
}

// Middleware runner
function runMiddleware(middlewares, req, res, finalHandler) {
  let index = 0;
  
  function next() {
    if (index >= middlewares.length) {
      return finalHandler(req, res);
    }
    
    const middleware = middlewares[index++];
    middleware(req, res, next);
  }
  
  next();
}

// Create server with middleware
const server = http.createServer((req, res) => {
  const middlewares = [logger, cors];
  
  // Add auth for protected routes
  if (req.url.startsWith('/api/protected')) {
    middlewares.push(authenticate);
  }
  
  runMiddleware(middlewares, req, res, (req, res) => {
    // Final route handler
    if (req.url === '/api/protected/data') {
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({
        message: 'Protected data',
        user: req.user
      }));
    } else {
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ message: 'Public route' }));
    }
  });
});

server.listen(3000);
```

---

#### Request Body Parsing

**Handling POST/PUT Data:**

```javascript
const http = require('http');

// Parse JSON body
function parseBody(req) {
  return new Promise((resolve, reject) => {
    let body = '';
    
    req.on('data', chunk => {
      body += chunk.toString();
    });
    
    req.on('end', () => {
      try {
        const data = body ? JSON.parse(body) : {};
        resolve(data);
      } catch (error) {
        reject(new Error('Invalid JSON'));
      }
    });
    
    req.on('error', reject);
  });
}

const server = http.createServer(async (req, res) => {
  res.setHeader('Content-Type', 'application/json');
  
  if (req.method === 'POST' && req.url === '/api/users') {
    try {
      const body = await parseBody(req);
      
      // Validate body
      if (!body.name || !body.email) {
        res.writeHead(400);
        res.end(JSON.stringify({
          error: 'Name and email are required'
        }));
        return;
      }
      
      // Process data
      const user = {
        id: Date.now(),
        name: body.name,
        email: body.email,
        createdAt: new Date().toISOString()
      };
      
      res.writeHead(201);
      res.end(JSON.stringify({ user }));
    } catch (error) {
      res.writeHead(400);
      res.end(JSON.stringify({ error: error.message }));
    }
  } else {
    res.writeHead(404);
    res.end(JSON.stringify({ error: 'Not found' }));
  }
});

server.listen(3000);
```

---

#### File Upload Without Express

**Handling Multipart Form Data:**

```javascript
const http = require('http');
const fs = require('fs');
const path = require('path');

function parseMultipart(req, boundary) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    
    req.on('data', chunk => chunks.push(chunk));
    
    req.on('end', () => {
      const buffer = Buffer.concat(chunks);
      const parts = buffer.toString().split(`--${boundary}`);
      const files = [];
      
      parts.forEach(part => {
        const match = part.match(/filename="(.+?)"/);
        if (match) {
          const filename = match[1];
          const dataStart = part.indexOf('\r\n\r\n') + 4;
          const dataEnd = part.lastIndexOf('\r\n');
          const fileData = part.slice(dataStart, dataEnd);
          
          files.push({ filename, data: fileData });
        }
      });
      
      resolve(files);
    });
    
    req.on('error', reject);
  });
}

const server = http.createServer(async (req, res) => {
  if (req.method === 'POST' && req.url === '/upload') {
    const contentType = req.headers['content-type'];
    const boundary = contentType.split('boundary=')[1];
    
    try {
      const files = await parseMultipart(req, boundary);
      
      // Save files
      for (const file of files) {
        const filepath = path.join(__dirname, 'uploads', file.filename);
        fs.writeFileSync(filepath, file.data);
      }
      
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({
        message: 'Files uploaded',
        count: files.length
      }));
    } catch (error) {
      res.writeHead(500, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ error: error.message }));
    }
  } else {
    res.writeHead(404);
    res.end('Not found');
  }
});

server.listen(3000);
```

---

#### Serving Static Files

**File Server:**

```javascript
const http = require('http');
const fs = require('fs');
const path = require('path');

// MIME types
const mimeTypes = {
  '.html': 'text/html',
  '.css': 'text/css',
  '.js': 'text/javascript',
  '.json': 'application/json',
  '.png': 'image/png',
  '.jpg': 'image/jpeg',
  '.svg': 'image/svg+xml',
  '.pdf': 'application/pdf'
};

const server = http.createServer((req, res) => {
  // Prevent directory traversal
  let filepath = path.join(__dirname, 'public', req.url);
  
  // Default to index.html
  if (filepath.endsWith('/')) {
    filepath = path.join(filepath, 'index.html');
  }
  
  const ext = path.extname(filepath);
  const contentType = mimeTypes[ext] || 'application/octet-stream';
  
  fs.readFile(filepath, (err, content) => {
    if (err) {
      if (err.code === 'ENOENT') {
        res.writeHead(404, { 'Content-Type': 'text/html' });
        res.end('<h1>404 - File Not Found</h1>');
      } else {
        res.writeHead(500);
        res.end(`Server Error: ${err.code}`);
      }
    } else {
      res.writeHead(200, { 'Content-Type': contentType });
      res.end(content);
    }
  });
});

server.listen(3000);
```

**With Streaming (Better for Large Files):**

```javascript
const http = require('http');
const fs = require('fs');
const path = require('path');

const server = http.createServer((req, res) => {
  const filepath = path.join(__dirname, 'public', req.url);
  
  // Check if file exists
  fs.stat(filepath, (err, stats) => {
    if (err) {
      res.writeHead(404);
      res.end('Not found');
      return;
    }
    
    if (stats.isDirectory()) {
      res.writeHead(403);
      res.end('Forbidden');
      return;
    }
    
    // Stream the file
    const ext = path.extname(filepath);
    const contentType = mimeTypes[ext] || 'application/octet-stream';
    
    res.writeHead(200, {
      'Content-Type': contentType,
      'Content-Length': stats.size
    });
    
    const stream = fs.createReadStream(filepath);
    stream.pipe(res);
    
    stream.on('error', (error) => {
      res.writeHead(500);
      res.end('Server error');
    });
  });
});

server.listen(3000);
```

---

#### HTTPS Server

**Creating Secure Server:**

```javascript
const https = require('https');
const fs = require('fs');

// SSL certificate options
const options = {
  key: fs.readFileSync('path/to/private-key.pem'),
  cert: fs.readFileSync('path/to/certificate.pem')
};

const server = https.createServer(options, (req, res) => {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({ message: 'Secure connection!' }));
});

server.listen(443, () => {
  console.log('HTTPS server running on port 443');
});
```

**Self-Signed Certificate for Development:**

```bash
# Generate self-signed certificate
openssl req -nodes -new -x509 -keyout server.key -out server.cert
```

```javascript
const https = require('https');
const fs = require('fs');

const options = {
  key: fs.readFileSync('server.key'),
  cert: fs.readFileSync('server.cert')
};

const server = https.createServer(options, (req, res) => {
  res.writeHead(200);
  res.end('Hello HTTPS!');
});

server.listen(8443);
```

---

#### Complete REST API Example

**Full CRUD API Without Express:**

```javascript
const http = require('http');
const url = require('url');

// In-memory database
let users = [
  { id: 1, name: 'John Doe', email: 'john@example.com' },
  { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
];

let nextId = 3;

// Parse JSON body
async function parseBody(req) {
  return new Promise((resolve, reject) => {
    let body = '';
    req.on('data', chunk => body += chunk);
    req.on('end', () => {
      try {
        resolve(body ? JSON.parse(body) : {});
      } catch (err) {
        reject(new Error('Invalid JSON'));
      }
    });
  });
}

// Route handlers
const routes = {
  // GET /api/users - List all users
  'GET:/api/users': async (req, res) => {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ users }));
  },
  
  // GET /api/users/:id - Get user by ID
  'GET:/api/users/:id': async (req, res, params) => {
    const user = users.find(u => u.id === parseInt(params.id));
    
    if (!user) {
      res.writeHead(404, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ error: 'User not found' }));
      return;
    }
    
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ user }));
  },
  
  // POST /api/users - Create new user
  'POST:/api/users': async (req, res) => {
    try {
      const body = await parseBody(req);
      
      if (!body.name || !body.email) {
        res.writeHead(400, { 'Content-Type': 'application/json' });
        res.end(JSON.stringify({ error: 'Name and email required' }));
        return;
      }
      
      const user = {
        id: nextId++,
        name: body.name,
        email: body.email
      };
      
      users.push(user);
      
      res.writeHead(201, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ user }));
    } catch (error) {
      res.writeHead(400, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ error: error.message }));
    }
  },
  
  // PUT /api/users/:id - Update user
  'PUT:/api/users/:id': async (req, res, params) => {
    try {
      const body = await parseBody(req);
      const userId = parseInt(params.id);
      const userIndex = users.findIndex(u => u.id === userId);
      
      if (userIndex === -1) {
        res.writeHead(404, { 'Content-Type': 'application/json' });
        res.end(JSON.stringify({ error: 'User not found' }));
        return;
      }
      
      users[userIndex] = {
        ...users[userIndex],
        name: body.name || users[userIndex].name,
        email: body.email || users[userIndex].email
      };
      
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ user: users[userIndex] }));
    } catch (error) {
      res.writeHead(400, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ error: error.message }));
    }
  },
  
  // DELETE /api/users/:id - Delete user
  'DELETE:/api/users/:id': async (req, res, params) => {
    const userId = parseInt(params.id);
    const userIndex = users.findIndex(u => u.id === userId);
    
    if (userIndex === -1) {
      res.writeHead(404, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ error: 'User not found' }));
      return;
    }
    
    users.splice(userIndex, 1);
    
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ message: 'User deleted' }));
  }
};

// Router
function router(req, res) {
  const parsedUrl = url.parse(req.url, true);
  const method = req.method;
  const pathname = parsedUrl.pathname;
  
  // Try exact match first
  const exactKey = `${method}:${pathname}`;
  if (routes[exactKey]) {
    return routes[exactKey](req, res);
  }
  
  // Try pattern match (for :id parameters)
  for (const key in routes) {
    const [routeMethod, routePath] = key.split(':');
    
    if (routeMethod !== method) continue;
    
    // Convert route pattern to regex
    const pattern = routePath.replace(/:\w+/g, '(\\d+)');
    const regex = new RegExp(`^${pattern}$`);
    const match = pathname.match(regex);
    
    if (match) {
      const params = {};
      const paramNames = routePath.match(/:\w+/g);
      
      if (paramNames) {
        paramNames.forEach((name, index) => {
          params[name.slice(1)] = match[index + 1];
        });
      }
      
      return routes[key](req, res, params);
    }
  }
  
  // 404 - Not found
  res.writeHead(404, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({ error: 'Route not found' }));
}

// Create server
const server = http.createServer(router);

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
  console.log('Available routes:');
  console.log('  GET    /api/users');
  console.log('  GET    /api/users/:id');
  console.log('  POST   /api/users');
  console.log('  PUT    /api/users/:id');
  console.log('  DELETE /api/users/:id');
});
```

**Test the API:**

```bash
# Get all users
curl http://localhost:3000/api/users

# Get user by ID
curl http://localhost:3000/api/users/1

# Create user
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Alice","email":"alice@example.com"}'

# Update user
curl -X PUT http://localhost:3000/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{"name":"John Updated"}'

# Delete user
curl -X DELETE http://localhost:3000/api/users/1
```

---

#### Error Handling

**Centralized Error Handler:**

```javascript
const http = require('http');

class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
  }
}

function errorHandler(err, res) {
  console.error(err);
  
  const statusCode = err.statusCode || 500;
  const message = err.message || 'Internal Server Error';
  
  res.writeHead(statusCode, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({
    error: message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
  }));
}

const server = http.createServer(async (req, res) => {
  try {
    if (req.url === '/api/users') {
      // Simulate database error
      throw new AppError('Database connection failed', 503);
    }
    
    if (req.url === '/api/forbidden') {
      throw new AppError('Access denied', 403);
    }
    
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ message: 'Success' }));
  } catch (error) {
    errorHandler(error, res);
  }
});

server.listen(3000);
```

---

#### Performance Optimization

**1. Clustering for Multi-Core:**

```javascript
const http = require('http');
const cluster = require('cluster');
const os = require('os');

if (cluster.isMaster) {
  const numCPUs = os.cpus().length;
  
  console.log(`Master ${process.pid} starting ${numCPUs} workers`);
  
  // Fork workers
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
  
  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} died. Restarting...`);
    cluster.fork();
  });
} else {
  // Worker processes
  const server = http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Handled by worker ${process.pid}`);
  });
  
  server.listen(3000);
  console.log(`Worker ${process.pid} started`);
}
```

**2. Response Compression:**

```javascript
const http = require('http');
const zlib = require('zlib');

const server = http.createServer((req, res) => {
  const data = JSON.stringify({ message: 'Hello'.repeat(1000) });
  
  // Check if client accepts gzip
  const acceptEncoding = req.headers['accept-encoding'] || '';
  
  if (acceptEncoding.includes('gzip')) {
    res.writeHead(200, {
      'Content-Type': 'application/json',
      'Content-Encoding': 'gzip'
    });
    
    zlib.gzip(data, (err, compressed) => {
      if (err) {
        res.writeHead(500);
        res.end('Compression error');
      } else {
        res.end(compressed);
      }
    });
  } else {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(data);
  }
});

server.listen(3000);
```

**3. Keep-Alive Connections:**

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  // Enable keep-alive
  res.setHeader('Connection', 'keep-alive');
  res.setHeader('Keep-Alive', 'timeout=5, max=1000');
  
  res.writeHead(200);
  res.end('Hello World');
});

// Configure server keep-alive
server.keepAliveTimeout = 5000; // 5 seconds
server.headersTimeout = 6000; // Must be higher than keepAliveTimeout

server.listen(3000);
```

---

#### Comparison: Vanilla vs Express

**Vanilla Node.js:**

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  if (req.method === 'GET' && req.url === '/api/users') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ users: [] }));
  } else {
    res.writeHead(404);
    res.end('Not found');
  }
});

server.listen(3000);
```

**Express Equivalent:**

```javascript
const express = require('express');
const app = express();

app.get('/api/users', (req, res) => {
  res.json({ users: [] });
});

app.listen(3000);
```

**Comparison:**

| Feature | Vanilla Node.js | Express |
|---------|----------------|---------|
| Dependencies | 0 | 30+ packages |
| Bundle Size | ~0 KB | ~200 KB |
| Learning Curve | Steeper | Easier |
| Routing | Manual | Built-in |
| Middleware | Custom | Ecosystem |
| Performance | Slightly faster | Very fast |
| Development Speed | Slower | Faster |
| Community Support | Large | Massive |
| Use Case | Simple APIs, learning | Production apps |

---

#### When to Use Each Approach

**Use Vanilla Node.js when:**
- ✅ Building microservices with minimal dependencies
- ✅ Learning HTTP fundamentals
- ✅ Maximum performance needed
- ✅ Tiny container images required
- ✅ Custom server logic needed

**Use Express when:**
- ✅ Building production web applications
- ✅ Need middleware ecosystem
- ✅ Team familiar with Express
- ✅ Rapid development required
- ✅ Complex routing needs

---

#### Summary

**Key Takeaways:**

1. **Yes, you can build servers without Express** using Node.js built-in `http`/`https` modules

2. **Vanilla Node.js is:**
   - More verbose
   - Zero dependencies
   - Slightly faster
   - More control

3. **Common patterns you need to implement:**
   - Routing (manual URL matching)
   - Body parsing (stream data collection)
   - Middleware (function chaining)
   - Error handling (try-catch + error handler)

4. **Real-world use cases:**
   - Microservices
   - Learning projects
   - Minimal APIs
   - Performance-critical services

5. **Best practices:**
   - Use streaming for large files
   - Implement clustering for multi-core
   - Enable compression
   - Handle errors properly
   - Use async/await for clean code

**Quick Start Template:**

```javascript
const http = require('http');

async function parseBody(req) {
  return new Promise((resolve, reject) => {
    let body = '';
    req.on('data', chunk => body += chunk);
    req.on('end', () => resolve(body ? JSON.parse(body) : {}));
    req.on('error', reject);
  });
}

const server = http.createServer(async (req, res) => {
  try {
    res.setHeader('Content-Type', 'application/json');
    
    if (req.method === 'GET' && req.url === '/') {
      res.writeHead(200);
      res.end(JSON.stringify({ message: 'API is running' }));
    } else {
      res.writeHead(404);
      res.end(JSON.stringify({ error: 'Not found' }));
    }
  } catch (error) {
    res.writeHead(500);
    res.end(JSON.stringify({ error: error.message }));
  }
});

server.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

---

### Q2.75: How do you debug Node.js applications?

**Answer:**

Node.js provides **multiple debugging approaches** ranging from simple console logging to advanced Chrome DevTools integration. The right tool depends on your use case - from quick debugging to production troubleshooting.

---

#### 1. Console Debugging (Simplest)

**Basic Console Methods:**

```javascript
// Standard logging
console.log('User data:', user);

// Error logging
console.error('Error occurred:', error);

// Warning
console.warn('Deprecated API used');

// Info
console.info('Server started on port 3000');

// Debugging
console.debug('Debug info:', debugData);

// Table format (great for arrays/objects)
const users = [
  { id: 1, name: 'John', role: 'admin' },
  { id: 2, name: 'Jane', role: 'user' }
];
console.table(users);

// Timing
console.time('database-query');
await db.query('SELECT * FROM users');
console.timeEnd('database-query'); // database-query: 45.231ms

// Count occurrences
for (let i = 0; i < 5; i++) {
  console.count('loop'); // loop: 1, loop: 2, ...
}

// Stack trace
console.trace('Execution path');

// Group logs
console.group('User Details');
console.log('Name:', user.name);
console.log('Email:', user.email);
console.groupEnd();

// Assert
console.assert(user.age >= 18, 'User must be 18+');
```

**Advanced Console Techniques:**

```javascript
// Custom formatting with colors (using util.inspect)
const util = require('util');

const data = { name: 'John', nested: { value: 123 } };
console.log(util.inspect(data, { colors: true, depth: null }));

// Pretty print JSON
console.log(JSON.stringify(data, null, 2));

// Conditional logging
const DEBUG = process.env.NODE_ENV !== 'production';
const log = (...args) => DEBUG && console.log(...args);

log('This only shows in development');

// Custom logger
class Logger {
  constructor(prefix) {
    this.prefix = prefix;
  }
  
  log(...args) {
    console.log(`[${this.prefix}]`, new Date().toISOString(), ...args);
  }
  
  error(...args) {
    console.error(`[${this.prefix}] ERROR:`, new Date().toISOString(), ...args);
  }
}

const logger = new Logger('API');
logger.log('Request received');
logger.error('Database connection failed');
```

---

#### 1.5. Console Colors and Formatting

**Why Use Colors?**
- ✅ Better readability in terminal output
- ✅ Distinguish log levels (error=red, success=green, warning=yellow)
- ✅ Improve debugging experience
- ✅ Make CLI tools more user-friendly

---

**Method 1: ANSI Escape Codes (No Dependencies)**

```javascript
// Basic colors
console.log('\x1b[31m%s\x1b[0m', 'Red text');      // Red
console.log('\x1b[32m%s\x1b[0m', 'Green text');    // Green
console.log('\x1b[33m%s\x1b[0m', 'Yellow text');   // Yellow
console.log('\x1b[34m%s\x1b[0m', 'Blue text');     // Blue
console.log('\x1b[35m%s\x1b[0m', 'Magenta text');  // Magenta
console.log('\x1b[36m%s\x1b[0m', 'Cyan text');     // Cyan

// Background colors
console.log('\x1b[41m%s\x1b[0m', 'Red background');
console.log('\x1b[42m%s\x1b[0m', 'Green background');

// Text styles
console.log('\x1b[1m%s\x1b[0m', 'Bold text');
console.log('\x1b[2m%s\x1b[0m', 'Dim text');
console.log('\x1b[3m%s\x1b[0m', 'Italic text');
console.log('\x1b[4m%s\x1b[0m', 'Underlined text');

// Combined
console.log('\x1b[1m\x1b[31m%s\x1b[0m', 'Bold Red text');
console.log('\x1b[4m\x1b[36m%s\x1b[0m', 'Underlined Cyan text');
```

**ANSI Color Codes Reference:**

```javascript
// Helper object
const colors = {
  // Text colors
  reset: '\x1b[0m',
  bright: '\x1b[1m',
  dim: '\x1b[2m',
  underscore: '\x1b[4m',
  blink: '\x1b[5m',
  reverse: '\x1b[7m',
  hidden: '\x1b[8m',
  
  // Foreground colors
  black: '\x1b[30m',
  red: '\x1b[31m',
  green: '\x1b[32m',
  yellow: '\x1b[33m',
  blue: '\x1b[34m',
  magenta: '\x1b[35m',
  cyan: '\x1b[36m',
  white: '\x1b[37m',
  
  // Background colors
  bgBlack: '\x1b[40m',
  bgRed: '\x1b[41m',
  bgGreen: '\x1b[42m',
  bgYellow: '\x1b[43m',
  bgBlue: '\x1b[44m',
  bgMagenta: '\x1b[45m',
  bgCyan: '\x1b[46m',
  bgWhite: '\x1b[47m'
};

// Usage
console.log(`${colors.red}Error: Something went wrong${colors.reset}`);
console.log(`${colors.green}Success: Operation completed${colors.reset}`);
console.log(`${colors.yellow}Warning: Deprecated API${colors.reset}`);
console.log(`${colors.bright}${colors.blue}Info: Processing...${colors.reset}`);

// Example: Logger with colors
class ColorLogger {
  error(message) {
    console.log(`${colors.red}[ERROR]${colors.reset} ${message}`);
  }
  
  success(message) {
    console.log(`${colors.green}[SUCCESS]${colors.reset} ${message}`);
  }
  
  warning(message) {
    console.log(`${colors.yellow}[WARNING]${colors.reset} ${message}`);
  }
  
  info(message) {
    console.log(`${colors.cyan}[INFO]${colors.reset} ${message}`);
  }
  
  debug(message) {
    console.log(`${colors.dim}[DEBUG]${colors.reset} ${message}`);
  }
}

const logger = new ColorLogger();
logger.error('Database connection failed');
logger.success('User created successfully');
logger.warning('API rate limit approaching');
logger.info('Server started on port 3000');
logger.debug('Request headers: {...}');
```

---

**Method 2: chalk (Most Popular Library)**

```bash
npm install chalk
```

```javascript
const chalk = require('chalk');

// Basic colors
console.log(chalk.red('Red text'));
console.log(chalk.green('Green text'));
console.log(chalk.blue('Blue text'));
console.log(chalk.yellow('Yellow text'));

// Background colors
console.log(chalk.bgRed('Red background'));
console.log(chalk.bgGreen.black('Black text on green background'));

// Text styles
console.log(chalk.bold('Bold text'));
console.log(chalk.italic('Italic text'));
console.log(chalk.underline('Underlined text'));
console.log(chalk.strikethrough('Strikethrough text'));

// Combine styles
console.log(chalk.bold.red('Bold red text'));
console.log(chalk.bgBlue.white.bold('Bold white on blue'));
console.log(chalk.red.bgWhite.bold('Bold red on white'));

// Template literals
console.log(chalk`
  CPU: {red ${cpu}%}
  RAM: {green ${ram}%}
  DISK: {yellow ${disk}%}
`);

// Conditional colors
const level = 'error';
const message = 'Something went wrong';

console.log(
  level === 'error' ? chalk.red(message) :
  level === 'warn' ? chalk.yellow(message) :
  chalk.green(message)
);

// Real-world example: Express logger
app.use((req, res, next) => {
  const method = req.method;
  const url = req.url;
  const status = res.statusCode;
  
  const methodColor = 
    method === 'GET' ? chalk.green :
    method === 'POST' ? chalk.blue :
    method === 'PUT' ? chalk.yellow :
    method === 'DELETE' ? chalk.red :
    chalk.white;
  
  const statusColor = 
    status < 300 ? chalk.green :
    status < 400 ? chalk.cyan :
    status < 500 ? chalk.yellow :
    chalk.red;
  
  console.log(
    methodColor(method.padEnd(7)),
    chalk.dim(url),
    statusColor(status)
  );
  
  next();
});
```

---

**Method 3: colors (Alternative Library)**

```bash
npm install colors
```

```javascript
const colors = require('colors');

// Text colors
console.log('Red text'.red);
console.log('Green text'.green);
console.log('Yellow text'.yellow);
console.log('Blue text'.blue);

// Background colors
console.log('Red background'.bgRed);
console.log('Green background'.bgGreen);

// Styles
console.log('Bold text'.bold);
console.log('Italic text'.italic);
console.log('Underlined text'.underline);

// Combine
console.log('Bold red text'.bold.red);
console.log('Underlined blue'.underline.blue);

// Themes
colors.setTheme({
  silly: 'rainbow',
  input: 'grey',
  verbose: 'cyan',
  prompt: 'grey',
  info: 'green',
  data: 'grey',
  help: 'cyan',
  warn: 'yellow',
  debug: 'blue',
  error: 'red'
});

console.log('Info message'.info);
console.log('Error message'.error);
console.log('Warning message'.warn);
```

---

**Method 4: kleur (Lightweight Alternative)**

```bash
npm install kleur
```

```javascript
const { red, green, yellow, blue, bold, underline } = require('kleur');

console.log(red('Red text'));
console.log(green('Green text'));
console.log(bold(blue('Bold blue text')));
console.log(underline(yellow('Underlined yellow')));

// Chainable
console.log(red().bold().underline('Bold underlined red'));
```

---

**Method 5: util.inspect with colors**

```javascript
const util = require('util');

// Automatic coloring for objects
const obj = {
  name: 'John',
  age: 30,
  active: true,
  tags: ['developer', 'nodejs']
};

console.log(util.inspect(obj, { colors: true }));

// Custom depth and colors
console.log(util.inspect(obj, { 
  colors: true, 
  depth: null,  // Infinite depth
  showHidden: false 
}));
```

---

**Practical Examples**

**1. Status Logger:**

```javascript
const chalk = require('chalk');

function logStatus(status, message) {
  const timestamp = new Date().toISOString();
  
  const statusColors = {
    success: chalk.green.bold,
    error: chalk.red.bold,
    warning: chalk.yellow.bold,
    info: chalk.blue.bold,
    debug: chalk.gray
  };
  
  const icon = {
    success: '✓',
    error: '✗',
    warning: '⚠',
    info: 'ℹ',
    debug: '○'
  };
  
  const color = statusColors[status] || chalk.white;
  
  console.log(
    chalk.dim(timestamp),
    color(`[${status.toUpperCase()}]`),
    color(icon[status]),
    message
  );
}

logStatus('success', 'User created successfully');
logStatus('error', 'Database connection failed');
logStatus('warning', 'API key expiring soon');
logStatus('info', 'Server started on port 3000');
logStatus('debug', 'Request processed in 45ms');
```

**2. Progress Bar:**

```javascript
const chalk = require('chalk');

function progressBar(current, total, label = '') {
  const percentage = Math.round((current / total) * 100);
  const width = 40;
  const filled = Math.round((width * current) / total);
  const empty = width - filled;
  
  const bar = 
    chalk.green('█'.repeat(filled)) + 
    chalk.gray('░'.repeat(empty));
  
  const percentColor = 
    percentage < 33 ? chalk.red :
    percentage < 66 ? chalk.yellow :
    chalk.green;
  
  process.stdout.write(
    `\r${label} ${bar} ${percentColor(percentage + '%')} (${current}/${total})`
  );
  
  if (current === total) {
    console.log(chalk.green(' ✓ Complete!'));
  }
}

// Usage
let current = 0;
const total = 100;

const interval = setInterval(() => {
  current += 10;
  progressBar(current, total, 'Downloading');
  
  if (current >= total) {
    clearInterval(interval);
  }
}, 200);
```

**3. Table with Colors:**

```javascript
const chalk = require('chalk');

function colorTable(data) {
  console.log(
    chalk.bold.cyan('ID').padEnd(10),
    chalk.bold.cyan('Name').padEnd(20),
    chalk.bold.cyan('Status').padEnd(15),
    chalk.bold.cyan('Score')
  );
  
  console.log(chalk.dim('─'.repeat(60)));
  
  data.forEach(row => {
    const statusColor = 
      row.status === 'active' ? chalk.green :
      row.status === 'pending' ? chalk.yellow :
      chalk.red;
    
    const scoreColor = 
      row.score >= 80 ? chalk.green.bold :
      row.score >= 50 ? chalk.yellow :
      chalk.red;
    
    console.log(
      chalk.white(row.id.toString().padEnd(10)),
      chalk.white(row.name.padEnd(20)),
      statusColor(row.status.padEnd(15)),
      scoreColor(row.score.toString())
    );
  });
}

// Usage
colorTable([
  { id: 1, name: 'John Doe', status: 'active', score: 95 },
  { id: 2, name: 'Jane Smith', status: 'pending', score: 67 },
  { id: 3, name: 'Bob Johnson', status: 'inactive', score: 42 }
]);
```

**4. Diff Display:**

```javascript
const chalk = require('chalk');

function showDiff(before, after) {
  console.log(chalk.red('- Before:'));
  console.log(chalk.red(JSON.stringify(before, null, 2)));
  console.log();
  console.log(chalk.green('+ After:'));
  console.log(chalk.green(JSON.stringify(after, null, 2)));
}

// Usage
showDiff(
  { name: 'John', age: 30 },
  { name: 'John', age: 31, active: true }
);
```

**5. Syntax Highlighting:**

```javascript
const chalk = require('chalk');

function highlightCode(code) {
  return code
    .replace(/(function|const|let|var|return|if|else)/g, chalk.magenta('$1'))
    .replace(/(['"])(.*?)\1/g, chalk.green('$1$2$1'))
    .replace(/(\d+)/g, chalk.yellow('$1'))
    .replace(/(\/\/.*$)/gm, chalk.gray('$1'));
}

const code = `
function greet(name) {
  const message = "Hello, " + name;
  return message;
}
`;

console.log(highlightCode(code));
```

---

**Cross-Platform Considerations**

```javascript
// Check if terminal supports colors
const chalk = require('chalk');

if (chalk.supportsColor) {
  console.log(chalk.green('Colors are supported!'));
} else {
  console.log('Colors are not supported');
}

// Disable colors in CI/CD environments
const isCI = process.env.CI === 'true';
const useColors = !isCI;

const log = (message, color = 'white') => {
  if (useColors && chalk[color]) {
    console.log(chalk[color](message));
  } else {
    console.log(message);
  }
};

log('This respects environment', 'green');
```

**Windows Support:**

```javascript
// Enable ANSI colors on Windows 10+
if (process.platform === 'win32') {
  // Windows Terminal supports colors by default
  // Older cmd.exe might need this:
  const isColorSupported = process.stdout.isTTY && 
                           process.stdout.hasColors &&
                           process.stdout.hasColors();
  
  if (!isColorSupported) {
    console.log('Colors might not work on this Windows terminal');
  }
}
```

---

**Performance Considerations**

```javascript
// ❌ Slow: Creating color objects repeatedly
for (let i = 0; i < 10000; i++) {
  console.log(chalk.red(`Item ${i}`));
}

// ✅ Faster: Reuse color functions
const red = chalk.red;
for (let i = 0; i < 10000; i++) {
  console.log(red(`Item ${i}`));
}

// ✅ Best: Disable colors in production if not needed
const logger = {
  log: process.env.NODE_ENV === 'production' 
    ? console.log 
    : (msg) => console.log(chalk.blue(msg))
};
```

---

**Comparison Table**

| Method | Size | Speed | Features | Windows |
|--------|------|-------|----------|---------|
| **ANSI Codes** | 0 KB | Fastest | Basic | ✅ (Win10+) |
| **chalk** | ~15 KB | Fast | Rich, Auto-detect | ✅ |
| **colors** | ~11 KB | Fast | String extension | ✅ |
| **kleur** | ~2 KB | Fastest | Lightweight | ✅ |
| **util.inspect** | 0 KB | Fast | Objects only | ✅ |

---

**Best Practices**

```javascript
// 1. Use consistent color scheme
const theme = {
  error: chalk.red.bold,
  success: chalk.green,
  warning: chalk.yellow,
  info: chalk.blue,
  debug: chalk.gray
};

// 2. Make colors optional
const logger = {
  error: (msg) => console.error(colors.error(`[ERROR] ${msg}`)),
  success: (msg) => console.log(colors.success(`[SUCCESS] ${msg}`))
};

// 3. Respect NO_COLOR environment variable
const useColors = !process.env.NO_COLOR;

// 4. Don't log colored text to files
const isFile = !process.stdout.isTTY;
const log = isFile ? console.log : (msg) => console.log(chalk.blue(msg));

// 5. Use semantic colors
// ✅ Good
console.log(chalk.red('Error: Connection failed'));
console.log(chalk.green('Success: User created'));

// ❌ Bad (confusing)
console.log(chalk.green('Error: Connection failed'));
```

---

**Summary**

**Quick Start (No Dependencies):**
```javascript
const colors = {
  red: '\x1b[31m',
  green: '\x1b[32m',
  yellow: '\x1b[33m',
  blue: '\x1b[34m',
  reset: '\x1b[0m'
};

console.log(`${colors.red}Error${colors.reset}: Something failed`);
console.log(`${colors.green}Success${colors.reset}: All good!`);
```

**Best Choice (With Dependencies):**
```javascript
const chalk = require('chalk');

console.log(chalk.red('Error: Something failed'));
console.log(chalk.green('Success: All good!'));
console.log(chalk.yellow('Warning: Deprecated'));
console.log(chalk.blue('Info: Processing...'));
```

**Key Takeaways:**
- ✅ Use ANSI codes for zero dependencies
- ✅ Use chalk for feature-rich applications
- ✅ Use kleur for minimal bundle size
- ✅ Always reset colors after use
- ✅ Check terminal support before using colors
- ✅ Use consistent color scheme (red=error, green=success, etc.)
- ✅ Respect `NO_COLOR` environment variable

---

#### 2. Built-in Node.js Debugger

**Start Debugger:**

```bash
# Method 1: Use inspect flag
node inspect app.js

# Method 2: Use --inspect flag (recommended)
node --inspect app.js

# Method 3: Inspect with break at start
node --inspect-brk app.js

# Custom port
node --inspect=0.0.0.0:9229 app.js
```

**Debugger Commands:**

```javascript
// Add breakpoint in code
debugger; // Execution pauses here when debugger is attached

// Example
async function processUser(userId) {
  const user = await db.getUser(userId);
  debugger; // Pause and inspect variables
  
  const processed = transform(user);
  debugger; // Pause again
  
  return processed;
}
```

**REPL Debugger Commands:**

```bash
# When using: node inspect app.js
c         # Continue execution
n         # Step to next line
s         # Step into function
o         # Step out of function
pause     # Pause execution
repl      # Enter REPL to inspect variables
exec expr # Execute expression
watch(expr) # Watch expression
list(5)   # Show 5 lines of code
backtrace # Show call stack
setBreakpoint(line) # Set breakpoint
clearBreakpoint(line) # Clear breakpoint
```

**Example Debug Session:**

```javascript
// app.js
function add(a, b) {
  debugger; // Breakpoint
  return a + b;
}

function multiply(x, y) {
  const sum = add(x, y);
  return sum * 2;
}

console.log(multiply(5, 10));

// Run: node inspect app.js
// Commands:
// c        -> continues to debugger statement
// repl     -> opens REPL
// a        -> shows value of 'a'
// b        -> shows value of 'b'
// n        -> step to next line
```

---

#### 3. Chrome DevTools (Most Powerful)

**Setup:**

```bash
# Start with --inspect
node --inspect app.js

# Or break at start
node --inspect-brk app.js

# Output:
# Debugger listening on ws://127.0.0.1:9229/uuid
# For help, see: https://nodejs.org/en/docs/inspector
```

**Connect:**

1. Open Chrome browser
2. Navigate to `chrome://inspect`
3. Click "inspect" under your Node.js process
4. DevTools opens with full debugging features

**Features Available:**

- ✅ Set breakpoints by clicking line numbers
- ✅ Step through code (F10, F11, F8)
- ✅ Watch expressions
- ✅ Call stack inspection
- ✅ Scope variables
- ✅ Console access
- ✅ CPU profiling
- ✅ Memory profiling
- ✅ Network inspection

**Example with Express:**

```javascript
const express = require('express');
const app = express();

app.get('/users/:id', async (req, res) => {
  const userId = req.params.id;
  debugger; // Chrome DevTools will pause here
  
  const user = await db.getUser(userId);
  debugger; // Pause again to inspect user object
  
  res.json(user);
});

app.listen(3000);

// Run: node --inspect app.js
// Open chrome://inspect
// Make request: curl http://localhost:3000/users/123
// Debugger pauses, inspect variables in DevTools
```

---

#### 4. VS Code Debugging (Most Convenient)

**Simple Configuration (.vscode/launch.json):**

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch Program",
      "skipFiles": ["<node_internals>/**"],
      "program": "${workspaceFolder}/app.js"
    }
  ]
}
```

**Advanced Configurations:**

```json
{
  "version": "0.2.0",
  "configurations": [
    // Launch current file
    {
      "type": "node",
      "request": "launch",
      "name": "Launch Current File",
      "program": "${file}",
      "skipFiles": ["<node_internals>/**"]
    },
    
    // Attach to running process
    {
      "type": "node",
      "request": "attach",
      "name": "Attach to Process",
      "port": 9229,
      "restart": true
    },
    
    // Debug with nodemon
    {
      "type": "node",
      "request": "launch",
      "name": "nodemon",
      "runtimeExecutable": "nodemon",
      "program": "${workspaceFolder}/app.js",
      "restart": true,
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen"
    },
    
    // Debug tests
    {
      "type": "node",
      "request": "launch",
      "name": "Jest Tests",
      "program": "${workspaceFolder}/node_modules/.bin/jest",
      "args": ["--runInBand"],
      "console": "integratedTerminal",
      "internalConsoleOptions": "neverOpen"
    },
    
    // Debug with environment variables
    {
      "type": "node",
      "request": "launch",
      "name": "Launch with Env",
      "program": "${workspaceFolder}/app.js",
      "env": {
        "NODE_ENV": "development",
        "DEBUG": "*"
      }
    },
    
    // Debug TypeScript
    {
      "type": "node",
      "request": "launch",
      "name": "TypeScript",
      "program": "${workspaceFolder}/src/app.ts",
      "preLaunchTask": "tsc: build - tsconfig.json",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"]
    }
  ]
}
```

**VS Code Debugging Features:**

```javascript
// Set breakpoints: Click left of line number
// Conditional breakpoint: Right-click breakpoint
// Logpoint: Right-click line number -> Add Logpoint

app.get('/api/users/:id', async (req, res) => {
  const userId = req.params.id; // Breakpoint here
  
  // Conditional breakpoint: userId === '123'
  // Logpoint: User ID: {userId}
  
  const user = await db.getUser(userId);
  res.json(user);
});

// Debug shortcuts:
// F5 - Start debugging
// F9 - Toggle breakpoint
// F10 - Step over
// F11 - Step into
// Shift+F11 - Step out
// Ctrl+Shift+F5 - Restart
// Shift+F5 - Stop
```

---

#### 5. Debug Module (Popular npm Package)

**Installation:**

```bash
npm install debug
```

**Usage:**

```javascript
const debug = require('debug');

// Create namespaced debuggers
const debugApp = debug('app:server');
const debugDB = debug('app:database');
const debugAuth = debug('app:auth');

// Use them
debugApp('Server starting on port %d', 3000);
debugDB('Connecting to database: %s', dbUrl);
debugAuth('User %s logged in', username);

// Enable in terminal:
// DEBUG=app:* node app.js        -> All app namespaces
// DEBUG=app:server node app.js   -> Only server
// DEBUG=app:*,-app:auth node app.js -> All except auth
// DEBUG=* node app.js            -> Everything
```

**Real-World Example:**

```javascript
// logger.js
const debug = require('debug');

module.exports = {
  server: debug('myapp:server'),
  db: debug('myapp:db'),
  api: debug('myapp:api'),
  error: debug('myapp:error')
};

// app.js
const { server, db, api, error } = require('./logger');

app.listen(3000, () => {
  server('Server started on port 3000');
});

app.get('/users', async (req, res) => {
  api('GET /users - Query: %O', req.query);
  
  try {
    db('Fetching users from database');
    const users = await db.getUsers();
    db('Found %d users', users.length);
    
    res.json(users);
  } catch (err) {
    error('Failed to fetch users: %O', err);
    res.status(500).json({ error: 'Internal error' });
  }
});

// Run with:
// DEBUG=myapp:* node app.js
```

---

#### 6. Memory Leak Debugging

**Detect Memory Leaks:**

```javascript
// Check memory usage
setInterval(() => {
  const used = process.memoryUsage();
  console.log({
    rss: `${Math.round(used.rss / 1024 / 1024)} MB`,
    heapTotal: `${Math.round(used.heapTotal / 1024 / 1024)} MB`,
    heapUsed: `${Math.round(used.heapUsed / 1024 / 1024)} MB`,
    external: `${Math.round(used.external / 1024 / 1024)} MB`
  });
}, 5000);

// Common memory leak causes:
// ❌ Global variables growing
global.users = [];
app.post('/user', (req, res) => {
  global.users.push(req.body); // LEAK!
});

// ❌ Event listeners not removed
const EventEmitter = require('events');
const emitter = new EventEmitter();

function handleRequest(req, res) {
  emitter.on('data', (data) => { // LEAK! Never removed
    res.write(data);
  });
}

// ✅ Remove listeners
function handleRequest(req, res) {
  const handler = (data) => res.write(data);
  emitter.on('data', handler);
  res.on('close', () => emitter.removeListener('data', handler));
}

// ❌ Closures holding references
function createHandler() {
  const largeData = new Array(1000000).fill('data'); // 1M items
  
  return (req, res) => {
    // largeData is captured in closure, never released
    res.json({ message: 'ok' });
  };
}
```

**Heap Snapshots:**

```bash
# Take heap snapshot
node --inspect app.js

# In Chrome DevTools Memory tab:
# 1. Take Snapshot
# 2. Load data/make requests
# 3. Take another Snapshot
# 4. Compare snapshots
```

**Heap Dump to File:**

```javascript
const v8 = require('v8');
const fs = require('fs');

function writeHeapSnapshot() {
  const filename = `heap-${Date.now()}.heapsnapshot`;
  v8.writeHeapSnapshot(filename);
  console.log(`Heap snapshot written to ${filename}`);
}

// Trigger on signal
process.on('SIGUSR2', writeHeapSnapshot);

// Or on endpoint
app.get('/debug/heap', (req, res) => {
  writeHeapSnapshot();
  res.json({ message: 'Heap snapshot created' });
});

// Trigger: kill -SIGUSR2 <pid>
```

---

#### 7. Performance Debugging

**CPU Profiling:**

```bash
# Start with profiling
node --prof app.js

# Generate load
ab -n 10000 -c 100 http://localhost:3000/

# Process profile
node --prof-process isolate-*-v8.log > profile.txt

# View profile.txt for hotspots
```

**Performance Hooks:**

```javascript
const { performance, PerformanceObserver } = require('perf_hooks');

// Measure function execution
async function slowFunction() {
  performance.mark('start');
  
  await heavyOperation();
  
  performance.mark('end');
  performance.measure('slowFunction', 'start', 'end');
}

// Observe measurements
const obs = new PerformanceObserver((items) => {
  items.getEntries().forEach((entry) => {
    console.log(`${entry.name}: ${entry.duration}ms`);
  });
});

obs.observe({ entryTypes: ['measure'] });

// Monitor event loop lag
const start = Date.now();
setInterval(() => {
  const lag = Date.now() - start - 1000;
  if (lag > 100) {
    console.warn(`Event loop lag: ${lag}ms`);
  }
}, 1000);
```

**Async Hooks (Advanced):**

```javascript
const async_hooks = require('async_hooks');
const fs = require('fs');

// Track async operations
const hook = async_hooks.createHook({
  init(asyncId, type, triggerAsyncId) {
    fs.writeSync(1, `Init: ${type}(${asyncId})\n`);
  },
  before(asyncId) {
    fs.writeSync(1, `Before: ${asyncId}\n`);
  },
  after(asyncId) {
    fs.writeSync(1, `After: ${asyncId}\n`);
  },
  destroy(asyncId) {
    fs.writeSync(1, `Destroy: ${asyncId}\n`);
  }
});

hook.enable();

// Now all async operations are tracked
```

---

#### 8. Production Debugging

**Logging in Production:**

```javascript
// Use proper logging library
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}

// Use it
logger.info('Server started', { port: 3000 });
logger.error('Database error', { error: err.message, stack: err.stack });
```

**APM Tools:**

```javascript
// New Relic
require('newrelic');

// Datadog
const tracer = require('dd-trace').init();

// Application Insights
const appInsights = require('applicationinsights');
appInsights.setup('your-instrumentation-key').start();

// These automatically instrument your app
```

**Remote Debugging (SSH Tunnel):**

```bash
# On server
node --inspect=127.0.0.1:9229 app.js

# On local machine (create SSH tunnel)
ssh -L 9229:127.0.0.1:9229 user@server

# Now connect Chrome DevTools to localhost:9229
```

**Post-Mortem Debugging:**

```javascript
// Generate core dump on crash
process.on('uncaughtException', (err) => {
  console.error('Uncaught exception:', err);
  
  // Write heap snapshot
  const v8 = require('v8');
  v8.writeHeapSnapshot(`crash-${Date.now()}.heapsnapshot`);
  
  // Exit
  process.exit(1);
});

// Or use llnode for core dumps
// npm install -g llnode
// llnode /path/to/node /path/to/core
```

---

#### 9. Common Debugging Scenarios

**Debugging Async/Await:**

```javascript
// Problem: Silent failures
async function fetchUser(id) {
  const user = await db.getUser(id); // Might throw
  return user;
}

// Solution: Add try-catch
async function fetchUser(id) {
  try {
    const user = await db.getUser(id);
    console.log('User fetched:', user);
    return user;
  } catch (error) {
    console.error('Error fetching user:', error);
    throw error;
  }
}

// Better: Use debugger
async function fetchUser(id) {
  debugger; // Pause before fetch
  const user = await db.getUser(id);
  debugger; // Pause after fetch, inspect user
  return user;
}
```

**Debugging Promises:**

```javascript
// Problem: Unhandled rejections
Promise.reject('Error'); // Silent failure!

// Solution: Always catch
Promise.reject('Error')
  .catch(err => console.error('Caught:', err));

// Better: Global handler
process.on('unhandledRejection', (reason, promise) => {
  console.error('Unhandled Rejection at:', promise, 'reason:', reason);
});

// Best: Use async/await with try-catch
async function main() {
  try {
    await riskyOperation();
  } catch (err) {
    console.error('Error:', err);
  }
}
```

**Debugging Callback Hell:**

```javascript
// Hard to debug
getData((err, data) => {
  if (err) return handleError(err);
  processData(data, (err, result) => {
    if (err) return handleError(err);
    saveResult(result, (err) => {
      if (err) return handleError(err);
      console.log('Done');
    });
  });
});

// Easy to debug with async/await
async function process() {
  try {
    const data = await getData();
    debugger; // Inspect data
    
    const result = await processData(data);
    debugger; // Inspect result
    
    await saveResult(result);
    console.log('Done');
  } catch (err) {
    handleError(err);
  }
}
```

**Debugging Event Emitters:**

```javascript
const EventEmitter = require('events');
const emitter = new EventEmitter();

// Add listener
emitter.on('data', (data) => {
  console.log('Data received:', data);
});

// Debug: Check listeners
console.log('Listener count:', emitter.listenerCount('data'));
console.log('Event names:', emitter.eventNames());

// Debug: Trace all events
const originalEmit = emitter.emit;
emitter.emit = function(event, ...args) {
  console.log(`Event emitted: ${event}`, args);
  return originalEmit.apply(this, [event, ...args]);
};

// Detect memory leaks from listeners
emitter.setMaxListeners(10); // Default is 10
emitter.on('warning', (warning) => {
  console.warn('EventEmitter warning:', warning);
});
```

---

#### 10. Debugging Tools Comparison

| Tool | Use Case | Pros | Cons |
|------|----------|------|------|
| **console.log** | Quick debugging | Simple, fast | Clutters code |
| **Node Debugger** | CLI debugging | Built-in | Limited features |
| **Chrome DevTools** | Visual debugging | Powerful UI | Requires Chrome |
| **VS Code** | Development | Integrated | IDE-specific |
| **debug module** | Namespaced logging | Production-safe | Requires setup |
| **Winston/Pino** | Production logs | Professional | Learning curve |
| **APM Tools** | Production monitoring | Comprehensive | Expensive |

---

#### Best Practices

**1. Use Appropriate Tools:**

```javascript
// Development: console.log, VS Code debugger
if (process.env.NODE_ENV === 'development') {
  console.log('Debug info:', data);
}

// Production: Proper logging
const logger = require('./logger');
logger.info('User action', { userId, action });
```

**2. Remove Debugger Statements:**

```javascript
// ❌ Don't commit
debugger;

// ✅ Use conditional
if (process.env.DEBUG) {
  debugger;
}
```

**3. Add Context to Logs:**

```javascript
// ❌ Bad
console.log(user);

// ✅ Good
console.log('User loaded:', { 
  userId: user.id, 
  timestamp: new Date(), 
  route: req.path 
});
```

**4. Use Source Maps (TypeScript):**

```json
// tsconfig.json
{
  "compilerOptions": {
    "sourceMap": true
  }
}
```

**5. Handle Errors Properly:**

```javascript
// ✅ Always log errors with context
try {
  await operation();
} catch (error) {
  logger.error('Operation failed', {
    error: error.message,
    stack: error.stack,
    userId,
    timestamp: new Date()
  });
  throw error;
}
```

---

#### Quick Reference

**Start Debugging:**

```bash
# Console
node app.js

# Built-in debugger
node inspect app.js

# Chrome DevTools
node --inspect app.js
# Open chrome://inspect

# VS Code
# Press F5 (with launch.json configured)

# With environment variable
DEBUG=* node app.js
```

**Breakpoints:**

```javascript
// Code breakpoint
debugger;

// VS Code: Click line number
// Chrome: Click line number in Sources tab
// Conditional: Right-click breakpoint
```

**Useful Environment Variables:**

```bash
NODE_ENV=development
DEBUG=app:*
NODE_OPTIONS=--inspect
NODE_OPTIONS=--max-old-space-size=4096
```

---

#### Summary

**Debugging Levels:**

1. **Quick Debug**: `console.log()` - Fast, simple
2. **Development**: VS Code debugger - Breakpoints, step through
3. **Deep Debug**: Chrome DevTools - Profiling, memory
4. **Production**: Winston/Pino + APM - Proper logging, monitoring

**Key Takeaways:**

- ✅ Use `console.log()` for quick debugging
- ✅ Use VS Code debugger during development
- ✅ Use Chrome DevTools for performance issues
- ✅ Use proper logging (Winston/Pino) in production
- ✅ Never commit `debugger` statements
- ✅ Always handle errors and log with context
- ✅ Use APM tools for production monitoring

**Most Common Workflow:**

```javascript
// 1. Add console.log
console.log('Data:', data);

// 2. If complex, add debugger
debugger;

// 3. If still unclear, use VS Code debugger
// Set breakpoint, press F5

// 4. If production issue, check logs
logger.error('Error', { context });
```

---

## Performance & Scalability

### Q2.9: How to Restart Node.js Server Automatically with Nodemon and PM2?

**Answer:**

Automatically restarting your Node.js server on file changes is essential for efficient development (Nodemon) and production deployments (PM2). This eliminates manual server restarts and improves productivity.

---

#### Why Auto-Restart?

**Problems with Manual Restart:**
```bash
# Manual workflow (tedious!)
node app.js
# Make code changes
Ctrl+C  # Stop server
node app.js  # Restart server
# Repeat 100 times per day... 😫
```

**Benefits of Auto-Restart:**
- ✅ Saves time during development
- ✅ Immediate feedback on code changes
- ✅ Automatic crash recovery (production)
- ✅ Zero-downtime deployments (PM2)
- ✅ Better developer experience

---

#### Development: Nodemon

**What is Nodemon?**
Nodemon monitors your files and automatically restarts the server when changes are detected. Perfect for development.

**Installation:**

```bash
# Global installation (recommended for CLI)
npm install -g nodemon

# Local installation (project-specific)
npm install --save-dev nodemon

# Verify installation
nodemon --version
```

---

#### Basic Nodemon Usage

**1. Replace `node` with `nodemon`:**

```bash
# Instead of:
node app.js

# Use:
nodemon app.js

# Output:
# [nodemon] 3.0.1
# [nodemon] to restart at any time, enter `rs`
# [nodemon] watching path(s): *.*
# [nodemon] watching extensions: js,mjs,json
# [nodemon] starting `node app.js`
# Server running on port 3000
```

**2. With TypeScript:**

```bash
# Install ts-node
npm install --save-dev ts-node typescript

# Run TypeScript files
nodemon --exec ts-node src/app.ts

# Or shorter:
nodemon src/app.ts
```

**3. Watch Specific Files:**

```bash
# Watch only .js files
nodemon --watch src --ext js app.js

# Watch multiple extensions
nodemon --ext js,json,ts app.js

# Watch specific directories
nodemon --watch src --watch config app.js
```

**4. Ignore Files/Directories:**

```bash
# Ignore node_modules and logs
nodemon --ignore node_modules --ignore logs app.js

# Ignore specific files
nodemon --ignore '*.test.js' app.js
```

---

#### Nodemon Configuration File

**Create `nodemon.json` in project root:**

```json
{
  "watch": ["src", "config"],
  "ext": "js,json,ts",
  "ignore": ["src/**/*.test.js", "node_modules"],
  "exec": "node",
  "env": {
    "NODE_ENV": "development",
    "DEBUG": "*"
  },
  "delay": 2000,
  "verbose": true,
  "restartable": "rs"
}
```

**Configuration Options Explained:**

```json
{
  "watch": ["src"],              // Directories to watch
  "ext": "js,json,ts",           // File extensions to monitor
  "ignore": ["*.test.js"],       // Files to ignore
  "exec": "node --inspect",      // Command to execute
  "delay": 1000,                 // Wait 1s before restarting
  "verbose": true,               // Show detailed logs
  "restartable": "rs",           // Type 'rs' to manually restart
  "env": {                       // Environment variables
    "NODE_ENV": "development"
  },
  "events": {                    // Custom commands
    "restart": "echo 'Restarting...'",
    "crash": "echo 'App crashed!'"
  },
  "colours": true,               // Colored output
  "signal": "SIGTERM",           // Signal to send to process
  "stdin": false,                // Don't listen to stdin
  "runOnChangeOnly": false,      // Run on startup or only on changes
  "quiet": false                 // Suppress nodemon messages
}
```

---

#### Advanced Nodemon Usage

**1. Different Commands for Different Environments:**

```json
// nodemon.json
{
  "watch": ["src"],
  "ext": "js,ts",
  "ignore": ["**/*.test.ts"],
  "exec": "ts-node src/app.ts",
  "env": {
    "NODE_ENV": "development",
    "PORT": "3000"
  }
}
```

**2. Using package.json Scripts:**

```json
{
  "name": "my-app",
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js",
    "dev:debug": "nodemon --inspect app.js",
    "dev:ts": "nodemon --exec ts-node src/app.ts",
    "dev:watch": "nodemon --watch src --ext js,ts app.js"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

**Run with:**
```bash
npm run dev
npm run dev:debug
npm run dev:ts
```

**3. With ES Modules:**

```json
// package.json
{
  "type": "module",
  "scripts": {
    "dev": "nodemon app.js"
  }
}
```

```javascript
// app.js (ES modules work automatically)
import express from 'express';
const app = express();
```

**4. Execute Custom Commands:**

```bash
# Run with Babel
nodemon --exec babel-node src/app.js

# Run with custom flags
nodemon --exec "node --experimental-modules --es-module-specifier-resolution=node" app.js

# Multiple commands
nodemon --exec "npm run build && node dist/app.js"
```

**5. Events and Hooks:**

```json
// nodemon.json
{
  "events": {
    "restart": "echo '\\033[33mRestarting server...\\033[0m'",
    "crash": "echo '\\033[31mServer crashed!\\033[0m'",
    "start": "echo '\\033[32mServer started!\\033[0m'"
  }
}
```

---

#### Production: PM2

**What is PM2?**
PM2 is a **production process manager** for Node.js with built-in load balancing, auto-restart, clustering, monitoring, and zero-downtime deployments.

**Key Features:**
- ✅ Auto-restart on crash
- ✅ Cluster mode (utilize all CPU cores)
- ✅ Load balancing
- ✅ Zero-downtime reload
- ✅ Startup scripts (auto-start on boot)
- ✅ Monitoring and logs
- ✅ Process management

**Installation:**

```bash
# Global installation (required)
npm install -g pm2

# Verify
pm2 --version
```

---

#### Basic PM2 Usage

**1. Start Application:**

```bash
# Start app
pm2 start app.js

# Start with custom name
pm2 start app.js --name "my-app"

# Start with watch mode (auto-restart on file changes)
pm2 start app.js --watch

# Start multiple instances (cluster mode)
pm2 start app.js -i 4  # 4 instances

# Use all CPU cores
pm2 start app.js -i max
```

**2. Process Management:**

```bash
# List all processes
pm2 list
pm2 ls

# Show detailed info
pm2 show my-app
pm2 info my-app

# Monitor in real-time
pm2 monit

# Stop process
pm2 stop my-app
pm2 stop all

# Restart process
pm2 restart my-app
pm2 restart all

# Reload (zero-downtime)
pm2 reload my-app

# Delete process
pm2 delete my-app
pm2 delete all

# Reset restart count
pm2 reset my-app
```

**3. Logs:**

```bash
# View logs
pm2 logs

# View logs for specific app
pm2 logs my-app

# View only errors
pm2 logs --err

# Clear logs
pm2 flush

# View old logs
pm2 logs --lines 1000
```

---

#### PM2 Ecosystem File

**Create `ecosystem.config.js`:**

```javascript
module.exports = {
  apps: [
    {
      name: 'my-app',
      script: './app.js',
      instances: 'max',              // Use all CPU cores
      exec_mode: 'cluster',          // Cluster mode
      watch: false,                  // Don't watch in production
      max_memory_restart: '500M',    // Restart if memory exceeds 500MB
      env: {
        NODE_ENV: 'development',
        PORT: 3000
      },
      env_production: {
        NODE_ENV: 'production',
        PORT: 8080
      },
      error_file: './logs/err.log',
      out_file: './logs/out.log',
      log_date_format: 'YYYY-MM-DD HH:mm:ss',
      merge_logs: true,
      autorestart: true,
      max_restarts: 10,
      min_uptime: '10s',
      listen_timeout: 3000,
      kill_timeout: 5000,
      wait_ready: false,
      shutdown_with_message: false
    }
  ]
};
```

**Start with ecosystem file:**

```bash
# Development
pm2 start ecosystem.config.js

# Production
pm2 start ecosystem.config.js --env production

# Update running app
pm2 reload ecosystem.config.js

# Stop all apps in ecosystem
pm2 delete ecosystem.config.js
```

---

#### Advanced PM2 Configuration

**1. Multiple Applications:**

```javascript
// ecosystem.config.js
module.exports = {
  apps: [
    {
      name: 'api',
      script: './api/server.js',
      instances: 4,
      exec_mode: 'cluster',
      env: { PORT: 3000 }
    },
    {
      name: 'worker',
      script: './worker/index.js',
      instances: 2,
      exec_mode: 'cluster',
      cron_restart: '0 0 * * *'  // Restart daily at midnight
    },
    {
      name: 'websocket',
      script: './websocket/server.js',
      instances: 1,
      exec_mode: 'fork'
    }
  ]
};
```

**2. Watch Mode (Development):**

```javascript
module.exports = {
  apps: [{
    name: 'dev-app',
    script: './app.js',
    watch: true,
    watch_delay: 1000,              // Wait 1s before restart
    ignore_watch: [
      'node_modules',
      'logs',
      '*.log',
      'public'
    ],
    watch_options: {
      followSymlinks: false
    }
  }]
};
```

**3. Auto-Restart Strategies:**

```javascript
module.exports = {
  apps: [{
    name: 'my-app',
    script: './app.js',
    
    // Memory-based restart
    max_memory_restart: '500M',
    
    // Time-based restart
    cron_restart: '0 0 * * *',      // Daily at midnight
    
    // Restart on crashes
    autorestart: true,
    max_restarts: 10,                // Max 10 restarts
    min_uptime: '10s',               // Must run 10s to count as success
    
    // Exponential backoff
    restart_delay: 4000,             // Initial delay 4s
    exp_backoff_restart_delay: 100   // Increase by 100ms each time
  }]
};
```

**4. Load Balancing:**

```javascript
module.exports = {
  apps: [{
    name: 'api',
    script: './app.js',
    instances: 4,                    // 4 instances
    exec_mode: 'cluster',            // Cluster mode for load balancing
    instance_var: 'INSTANCE_ID',     // Each instance gets unique ID
    listen_timeout: 3000,
    kill_timeout: 5000
  }]
};
```

---

#### PM2 Cluster Mode

**How Clustering Works:**

```javascript
// app.js - No changes needed!
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send(`Handled by process ${process.pid}`);
});

app.listen(3000, () => {
  console.log(`Worker ${process.pid} started`);
});

// PM2 automatically creates multiple instances
// and load balances between them!
```

**Start in Cluster Mode:**

```bash
# Use all CPU cores
pm2 start app.js -i max

# Use specific number of cores
pm2 start app.js -i 4

# Scale up/down
pm2 scale my-app 8   # Scale to 8 instances
pm2 scale my-app +2  # Add 2 instances
pm2 scale my-app -1  # Remove 1 instance
```

---

#### PM2 Monitoring

**1. Built-in Monitoring:**

```bash
# Real-time monitoring
pm2 monit

# Shows:
# - CPU usage
# - Memory usage
# - Process list
# - Logs
```

**2. Web Dashboard (PM2 Plus):**

```bash
# Link to PM2 Plus (cloud monitoring)
pm2 link <secret_key> <public_key>

# Monitor multiple servers
# View metrics, logs, exceptions
# Get alerts
```

**3. Programmatic Monitoring:**

```javascript
const pm2 = require('pm2');

pm2.connect((err) => {
  if (err) throw err;
  
  pm2.list((err, processes) => {
    console.log('Running processes:', processes.length);
    
    processes.forEach(proc => {
      console.log(`${proc.name}: ${proc.pm2_env.status}`);
      console.log(`Memory: ${proc.monit.memory / 1024 / 1024} MB`);
      console.log(`CPU: ${proc.monit.cpu}%`);
    });
    
    pm2.disconnect();
  });
});
```

---

#### Startup Scripts (Auto-Start on Boot)

**Generate Startup Script:**

```bash
# Generate startup script for your OS
pm2 startup

# Output will show command to run, e.g.:
# sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u username --hp /home/username

# Run the command shown
sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u username --hp /home/username

# Start your apps
pm2 start app.js

# Save the process list
pm2 save

# Now PM2 will auto-start your apps on system reboot!

# Disable startup
pm2 unstartup systemd
```

**Update Saved Apps:**

```bash
# Make changes to running apps
pm2 start new-app.js
pm2 delete old-app

# Save current state
pm2 save

# Now reboots will use new configuration
```

---

#### Zero-Downtime Deployments

**1. Graceful Reload:**

```javascript
// app.js
const express = require('express');
const app = express();

// Your routes...
app.get('/', (req, res) => res.send('Hello'));

const server = app.listen(3000);

// Handle SIGINT signal
process.on('SIGINT', () => {
  console.log('SIGINT received: closing server gracefully');
  
  server.close(() => {
    console.log('Server closed. Exiting process.');
    process.exit(0);
  });
  
  // Force exit after 10s
  setTimeout(() => {
    console.error('Forcing exit');
    process.exit(1);
  }, 10000);
});

// PM2 uses this for zero-downtime reload
```

**Deploy with zero downtime:**

```bash
# Reload all processes (zero-downtime)
pm2 reload all

# Reload specific app
pm2 reload my-app

# Restart (brief downtime)
pm2 restart my-app
```

**2. Deployment Script:**

```bash
#!/bin/bash
# deploy.sh

echo "🚀 Deploying application..."

# Pull latest code
git pull origin main

# Install dependencies
npm install --production

# Run migrations
npm run migrate

# Reload with zero downtime
pm2 reload ecosystem.config.js --env production

echo "✅ Deployment complete!"
```

---

#### Comparing Nodemon vs PM2

| Feature | Nodemon | PM2 |
|---------|---------|-----|
| **Purpose** | Development | Production |
| **Auto-restart** | On file changes | On crash/manual |
| **Clustering** | ❌ No | ✅ Yes |
| **Load Balancing** | ❌ No | ✅ Yes |
| **Monitoring** | ❌ Basic | ✅ Advanced |
| **Logs** | ✅ Console | ✅ File + rotation |
| **Startup Scripts** | ❌ No | ✅ Yes |
| **Zero-downtime** | ❌ No | ✅ Yes |
| **Memory Limit** | ❌ No | ✅ Yes |
| **CPU Usage** | Low | Varies |
| **Use Case** | Local development | Production servers |

---

#### Combined Workflow

**Development (Nodemon):**

```json
// package.json
{
  "scripts": {
    "dev": "nodemon app.js",
    "dev:debug": "nodemon --inspect app.js"
  }
}
```

**Staging/Production (PM2):**

```json
// package.json
{
  "scripts": {
    "start": "pm2 start ecosystem.config.js --env production",
    "stop": "pm2 stop all",
    "restart": "pm2 reload all",
    "logs": "pm2 logs",
    "monit": "pm2 monit"
  }
}
```

**Complete Setup:**

```bash
# Development
npm run dev

# Production deployment
npm run start
pm2 save
pm2 startup
```

---

#### Best Practices

**1. Development (Nodemon):**

```javascript
// nodemon.json
{
  "watch": ["src"],
  "ext": "js,ts,json",
  "ignore": ["**/*.test.js", "node_modules", "dist"],
  "delay": 1000,
  "env": {
    "NODE_ENV": "development",
    "DEBUG": "app:*"
  }
}
```

**2. Production (PM2):**

```javascript
// ecosystem.config.js
module.exports = {
  apps: [{
    name: 'api',
    script: './dist/app.js',
    instances: 'max',
    exec_mode: 'cluster',
    max_memory_restart: '500M',
    env_production: {
      NODE_ENV: 'production'
    },
    error_file: './logs/error.log',
    out_file: './logs/out.log',
    log_date_format: 'YYYY-MM-DD HH:mm:ss Z',
    merge_logs: true
  }]
};
```

**3. Graceful Shutdown:**

```javascript
// Proper cleanup before restart
process.on('SIGINT', gracefulShutdown);
process.on('SIGTERM', gracefulShutdown);

function gracefulShutdown() {
  console.log('Shutting down gracefully...');
  
  // Stop accepting new connections
  server.close(() => {
    // Close database connections
    db.close(() => {
      // Close Redis connections
      redis.quit(() => {
        console.log('Shutdown complete');
        process.exit(0);
      });
    });
  });
  
  // Force exit after timeout
  setTimeout(() => {
    console.error('Forcing shutdown');
    process.exit(1);
  }, 10000);
}
```

---

#### Troubleshooting

**Nodemon Issues:**

```bash
# Nodemon not detecting changes
nodemon --legacy-watch app.js

# Too many file watchers
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Clear nodemon cache
rm -rf .nodemon

# Verbose output
nodemon --verbose app.js
```

**PM2 Issues:**

```bash
# PM2 not starting
pm2 kill          # Kill PM2 daemon
pm2 resurrect     # Restore saved processes

# Clear logs
pm2 flush

# Check process status
pm2 status
pm2 show app-name

# Reset process
pm2 reset app-name

# Update PM2
npm install -g pm2@latest
pm2 update
```

---

#### Summary

**Quick Reference:**

**Development - Nodemon:**
```bash
# Install
npm install -g nodemon

# Basic usage
nodemon app.js

# With config
nodemon.json + npm run dev

# Watch specific files
nodemon --watch src --ext js,ts app.js
```

**Production - PM2:**
```bash
# Install
npm install -g pm2

# Start
pm2 start app.js -i max

# Manage
pm2 list | stop | restart | reload | delete

# Monitor
pm2 monit | logs

# Auto-start
pm2 startup
pm2 save
```

**Key Takeaways:**
- ✅ Use **Nodemon** for development (auto-restart on file changes)
- ✅ Use **PM2** for production (clustering, monitoring, auto-restart on crash)
- ✅ Configure using `nodemon.json` and `ecosystem.config.js`
- ✅ PM2 cluster mode utilizes all CPU cores
- ✅ PM2 provides zero-downtime deployments
- ✅ Always implement graceful shutdown handlers
- ✅ Use `pm2 save` and `pm2 startup` for auto-start on boot

---

### Q3: How do you optimize Node.js application performance?

**Answer:**

**1. Use Worker Threads for CPU-Intensive Tasks:**
```javascript
// ❌ Bad: Blocks Event Loop
app.get('/process', (req, res) => {
  const result = heavyCPUTask(req.body.data); // BLOCKS!
  res.json({ result });
});

// ✅ Good: Use Worker Thread
const { Worker } = require('worker_threads');

app.get('/process', (req, res) => {
  const worker = new Worker('./worker.js', {
    workerData: req.body.data
  });
  
  worker.on('message', (result) => {
    res.json({ result });
  });
  
  worker.on('error', (error) => {
    res.status(500).json({ error: error.message });
  });
});

// worker.js
const { parentPort, workerData } = require('worker_threads');
const result = heavyCPUTask(workerData);
parentPort.postMessage(result);
```

**2. Clustering (Use All CPU Cores):**
```javascript
const cluster = require('cluster');
const os = require('os');
const express = require('express');

if (cluster.isMaster) {
  const numCPUs = os.cpus().length;
  console.log(`Master process starting ${numCPUs} workers`);
  
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
  
  cluster.on('exit', (worker) => {
    console.log(`Worker ${worker.process.pid} died, starting new one`);
    cluster.fork();
  });
} else {
  const app = express();
  
  app.get('/', (req, res) => {
    res.send(`Handled by worker ${process.pid}`);
  });
  
  app.listen(3000);
  console.log(`Worker ${process.pid} started`);
}
```

**3. Caching:**
```javascript
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 600 }); // 10 minutes

app.get('/api/users/:id', async (req, res) => {
  const cacheKey = `user_${req.params.id}`;
  
  // Check cache first
  let user = cache.get(cacheKey);
  
  if (!user) {
    // Cache miss - fetch from database
    user = await db.user.findById(req.params.id);
    cache.set(cacheKey, user);
  }
  
  res.json(user);
});
```

**4. Database Query Optimization:**
```javascript
// ❌ Bad: N+1 Query Problem
const users = await User.findAll();
for (const user of users) {
  user.posts = await Post.findAll({ where: { userId: user.id } });
}

// ✅ Good: Eager Loading
const users = await User.findAll({
  include: [{ model: Post }]
});

// ✅ Good: Indexing
// In migration
await queryInterface.addIndex('users', ['email']);
await queryInterface.addIndex('posts', ['userId', 'createdAt']);
```

**5. Connection Pooling:**
```javascript
const { Pool } = require('pg');

// ✅ Use connection pool
const pool = new Pool({
  host: 'localhost',
  database: 'mydb',
  max: 20, // Maximum connections
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000
});

app.get('/users', async (req, res) => {
  const client = await pool.connect();
  try {
    const result = await client.query('SELECT * FROM users');
    res.json(result.rows);
  } finally {
    client.release(); // Return to pool
  }
});
```

**6. Use Compression:**
```javascript
const compression = require('compression');
const express = require('express');

const app = express();
app.use(compression()); // Gzip responses
```

**7. Optimize JSON Operations:**
```javascript
// Use fast-json-stringify for consistent JSON structure
const fastJson = require('fast-json-stringify');

const stringify = fastJson({
  type: 'object',
  properties: {
    id: { type: 'number' },
    name: { type: 'string' },
    email: { type: 'string' }
  }
});

app.get('/users', async (req, res) => {
  const users = await getUsers();
  res.send(stringify(users)); // 2-3x faster than JSON.stringify
});
```

---

### Q4: How do you handle concurrency and prevent race conditions in Node.js?

**Answer:**

**1. Use Locks/Mutexes:**
```javascript
const { Mutex } = require('async-mutex');
const mutex = new Mutex();

let balance = 1000;

async function withdraw(amount) {
  // Wait for lock
  const release = await mutex.acquire();
  
  try {
    if (balance >= amount) {
      // Simulate async operation
      await new Promise(resolve => setTimeout(resolve, 100));
      balance -= amount;
      return { success: true, newBalance: balance };
    }
    return { success: false, message: 'Insufficient funds' };
  } finally {
    release(); // Always release lock
  }
}

// Multiple concurrent withdrawals - no race condition!
Promise.all([
  withdraw(600),
  withdraw(500)
]);
```

**2. Atomic Operations with Redis:**
```javascript
const redis = require('redis');
const client = redis.createClient();

// ❌ Bad: Race condition
async function incrementCounter() {
  const value = await client.get('counter');
  await client.set('counter', parseInt(value) + 1);
}

// ✅ Good: Atomic operation
async function incrementCounterAtomic() {
  await client.incr('counter'); // Atomic in Redis
}

// ✅ Good: Lua script for complex atomic operations
async function transferFunds(fromId, toId, amount) {
  const script = `
    local balance = redis.call('GET', KEYS[1])
    if tonumber(balance) >= tonumber(ARGV[1]) then
      redis.call('DECRBY', KEYS[1], ARGV[1])
      redis.call('INCRBY', KEYS[2], ARGV[1])
      return 1
    end
    return 0
  `;
  
  return client.eval(script, 2, 
    `account:${fromId}`, `account:${toId}`, amount);
}
```

**3. Database Transactions:**
```javascript
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

async function transferMoney(fromUserId, toUserId, amount) {
  return await prisma.$transaction(async (tx) => {
    // Withdraw from sender
    const sender = await tx.account.update({
      where: { userId: fromUserId },
      data: { balance: { decrement: amount } }
    });
    
    if (sender.balance < 0) {
      throw new Error('Insufficient funds');
    }
    
    // Deposit to receiver
    await tx.account.update({
      where: { userId: toUserId },
      data: { balance: { increment: amount } }
    });
    
    return { success: true };
  });
}
```

**4. Optimistic Locking:**
```javascript
// Add version field to model
const user = await User.findByPk(userId);
const currentVersion = user.version;

// Try to update with version check
const [updated] = await User.update(
  { 
    name: 'New Name',
    version: currentVersion + 1
  },
  {
    where: { 
      id: userId,
      version: currentVersion // Only update if version matches
    }
  }
);

if (updated === 0) {
  throw new Error('Update conflict - record was modified by another process');
}
```

**5. Queue for Sequential Processing:**
```javascript
const Bull = require('bull');
const queue = new Bull('email-queue');

// Producer: Add jobs
app.post('/send-email', async (req, res) => {
  await queue.add('send', {
    to: req.body.email,
    subject: 'Hello',
    body: 'World'
  });
  res.json({ queued: true });
});

// Consumer: Process sequentially
queue.process('send', async (job) => {
  await sendEmail(job.data);
});
```

---

## Security

### Q5: What are the critical security best practices for Node.js applications?

**Answer:**

**1. Input Validation & Sanitization:**
```javascript
const Joi = require('joi');
const express = require('express');

const userSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().min(8).required(),
  age: Joi.number().integer().min(13).max(120)
});

app.post('/register', async (req, res) => {
  try {
    // Validate input
    const { error, value } = userSchema.validate(req.body);
    if (error) {
      return res.status(400).json({ error: error.details[0].message });
    }
    
    // Use validated value
    const user = await createUser(value);
    res.json(user);
  } catch (error) {
    res.status(500).json({ error: 'Server error' });
  }
});
```

**2. SQL Injection Prevention:**
```javascript
// ❌ Bad: SQL Injection vulnerability
app.get('/user', (req, res) => {
  const query = `SELECT * FROM users WHERE id = ${req.query.id}`;
  db.query(query); // DANGEROUS!
});

// ✅ Good: Parameterized queries
app.get('/user', async (req, res) => {
  const query = 'SELECT * FROM users WHERE id = $1';
  const result = await db.query(query, [req.query.id]);
  res.json(result.rows[0]);
});

// ✅ Good: ORM
const user = await User.findByPk(req.query.id);
```

**3. Password Security:**
```javascript
const bcrypt = require('bcrypt');

// Registration
async function registerUser(email, password) {
  const saltRounds = 12;
  const hashedPassword = await bcrypt.hash(password, saltRounds);
  
  return await User.create({
    email,
    password: hashedPassword
  });
}

// Login
async function loginUser(email, password) {
  const user = await User.findOne({ where: { email } });
  if (!user) {
    throw new Error('Invalid credentials');
  }
  
  const valid = await bcrypt.compare(password, user.password);
  if (!valid) {
    throw new Error('Invalid credentials');
  }
  
  return generateToken(user);
}
```

**4. JWT Token Security:**
```javascript
const jwt = require('jsonwebtoken');

// Generate token
function generateToken(user) {
  return jwt.sign(
    { userId: user.id, email: user.email },
    process.env.JWT_SECRET,
    { expiresIn: '1h' } // Short-lived tokens
  );
}

// Verify token middleware
function authenticateToken(req, res, next) {
  const token = req.headers['authorization']?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'Access denied' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(403).json({ error: 'Invalid token' });
  }
}

// Use middleware
app.get('/protected', authenticateToken, (req, res) => {
  res.json({ message: 'Secret data', user: req.user });
});
```

**5. Rate Limiting:**
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  message: 'Too many requests, please try again later'
});

app.use('/api/', limiter);

// Stricter limits for auth endpoints
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5
});

app.post('/api/login', authLimiter, loginHandler);
```

**6. Security Headers:**
```javascript
const helmet = require('helmet');

app.use(helmet()); // Sets multiple security headers

// Or configure individually
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    scriptSrc: ["'self'"],
    imgSrc: ["'self'", 'data:', 'https:']
  }
}));
```

**7. Environment Variables:**
```javascript
// ❌ Never commit secrets
const API_KEY = 'secret123';

// ✅ Use environment variables
require('dotenv').config();

const config = {
  dbUrl: process.env.DATABASE_URL,
  apiKey: process.env.API_KEY,
  jwtSecret: process.env.JWT_SECRET
};

// Validate required env vars on startup
if (!config.jwtSecret) {
  throw new Error('JWT_SECRET is required');
}
```

**8. CORS Configuration:**
```javascript
const cors = require('cors');

// ❌ Bad: Allow all origins
app.use(cors());

// ✅ Good: Whitelist specific origins
const corsOptions = {
  origin: ['https://yourdomain.com', 'https://app.yourdomain.com'],
  credentials: true,
  optionsSuccessStatus: 200
};

app.use(cors(corsOptions));
```

---

## Architecture & Design Patterns

### Q6: How do you structure a large-scale Node.js application?

**Answer:**

**Layered Architecture:**

```
src/
├── api/
│   ├── routes/           # Express routes
│   │   ├── users.js
│   │   └── products.js
│   ├── middleware/       # Custom middleware
│   │   ├── auth.js
│   │   ├── validation.js
│   │   └── errorHandler.js
│   └── controllers/      # Request handlers
│       ├── userController.js
│       └── productController.js
├── services/             # Business logic
│   ├── userService.js
│   └── productService.js
├── repositories/         # Data access layer
│   ├── userRepository.js
│   └── productRepository.js
├── models/               # Database models
│   ├── User.js
│   └── Product.js
├── utils/                # Helper functions
│   ├── logger.js
│   └── validators.js
├── config/               # Configuration
│   ├── database.js
│   └── environment.js
└── app.js                # App setup
```

**Example Implementation:**

**1. Controller (Handle HTTP):**
```javascript
// api/controllers/userController.js
const userService = require('../../services/userService');

class UserController {
  async getUser(req, res, next) {
    try {
      const user = await userService.getUserById(req.params.id);
      res.json(user);
    } catch (error) {
      next(error); // Pass to error handler
    }
  }
  
  async createUser(req, res, next) {
    try {
      const user = await userService.createUser(req.body);
      res.status(201).json(user);
    } catch (error) {
      next(error);
    }
  }
}

module.exports = new UserController();
```

**2. Service (Business Logic):**
```javascript
// services/userService.js
const userRepository = require('../repositories/userRepository');
const emailService = require('./emailService');
const bcrypt = require('bcrypt');

class UserService {
  async getUserById(id) {
    const user = await userRepository.findById(id);
    if (!user) {
      throw new Error('User not found');
    }
    return user;
  }
  
  async createUser(userData) {
    // Validation
    if (await userRepository.existsByEmail(userData.email)) {
      throw new Error('Email already exists');
    }
    
    // Hash password
    userData.password = await bcrypt.hash(userData.password, 12);
    
    // Create user
    const user = await userRepository.create(userData);
    
    // Send welcome email
    await emailService.sendWelcomeEmail(user.email);
    
    return user;
  }
}

module.exports = new UserService();
```

**3. Repository (Data Access):**
```javascript
// repositories/userRepository.js
const { User } = require('../models');

class UserRepository {
  async findById(id) {
    return await User.findByPk(id, {
      attributes: { exclude: ['password'] }
    });
  }
  
  async existsByEmail(email) {
    const count = await User.count({ where: { email } });
    return count > 0;
  }
  
  async create(userData) {
    return await User.create(userData);
  }
  
  async update(id, userData) {
    await User.update(userData, { where: { id } });
    return this.findById(id);
  }
  
  async delete(id) {
    return await User.destroy({ where: { id } });
  }
}

module.exports = new UserRepository();
```

**4. Routes:**
```javascript
// api/routes/users.js
const express = require('express');
const router = express.Router();
const userController = require('../controllers/userController');
const { authenticate } = require('../middleware/auth');
const { validateUser } = require('../middleware/validation');

router.get('/:id', authenticate, userController.getUser);
router.post('/', validateUser, userController.createUser);

module.exports = router;
```

**5. Error Handling:**
```javascript
// api/middleware/errorHandler.js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
  }
}

function errorHandler(err, req, res, next) {
  // Log error
  logger.error(err);
  
  // Operational error (expected)
  if (err.isOperational) {
    return res.status(err.statusCode).json({
      error: err.message
    });
  }
  
  // Programming error (unexpected)
  res.status(500).json({
    error: 'Something went wrong'
  });
}

module.exports = { AppError, errorHandler };
```

---

### Q7: Explain microservices architecture in Node.js and communication patterns.

**Answer:**

**Microservices Benefits:**
- Independent deployment
- Technology diversity
- Fault isolation
- Scalability per service

**Communication Patterns:**

**1. REST API (Synchronous):**
```javascript
// User Service
const express = require('express');
const app = express();

app.get('/users/:id', async (req, res) => {
  const user = await User.findByPk(req.params.id);
  res.json(user);
});

// Order Service (calls User Service)
const axios = require('axios');

async function createOrder(userId, items) {
  // Call User Service
  const userResponse = await axios.get(
    `http://user-service/users/${userId}`
  );
  
  const order = await Order.create({
    userId,
    items,
    userEmail: userResponse.data.email
  });
  
  return order;
}
```

**2. Message Queue (Asynchronous):**
```javascript
// User Service - Publisher
const amqp = require('amqplib');

async function publishUserCreated(user) {
  const connection = await amqp.connect('amqp://localhost');
  const channel = await connection.createChannel();
  
  await channel.assertQueue('user.created');
  channel.sendToQueue('user.created', Buffer.from(JSON.stringify(user)));
  
  console.log('Published user.created event');
  await channel.close();
  await connection.close();
}

// Email Service - Consumer
async function consumeUserCreatedEvents() {
  const connection = await amqp.connect('amqp://localhost');
  const channel = await connection.createChannel();
  
  await channel.assertQueue('user.created');
  
  channel.consume('user.created', async (msg) => {
    const user = JSON.parse(msg.content.toString());
    await sendWelcomeEmail(user.email);
    channel.ack(msg);
  });
}
```

**3. Event-Driven (RabbitMQ/Kafka):**
```javascript
// Event Bus
class EventBus {
  constructor() {
    this.subscribers = {};
  }
  
  subscribe(event, callback) {
    if (!this.subscribers[event]) {
      this.subscribers[event] = [];
    }
    this.subscribers[event].push(callback);
  }
  
  publish(event, data) {
    if (this.subscribers[event]) {
      this.subscribers[event].forEach(callback => callback(data));
    }
  }
}

// Service A
eventBus.subscribe('order.created', async (order) => {
  await processPayment(order);
});

// Service B
eventBus.subscribe('order.created', async (order) => {
  await updateInventory(order);
});

// Publisher
eventBus.publish('order.created', orderData);
```

**4. Service Mesh (with Consul/Kubernetes):**
```javascript
// Service Discovery
const consul = require('consul')();

// Register service
await consul.agent.service.register({
  name: 'user-service',
  address: 'localhost',
  port: 3001,
  check: {
    http: 'http://localhost:3001/health',
    interval: '10s'
  }
});

// Discover service
const services = await consul.catalog.service.nodes('user-service');
const service = services[Math.floor(Math.random() * services.length)];
const url = `http://${service.ServiceAddress}:${service.ServicePort}`;
```

**Best Practices:**
- Use API Gateway for external clients
- Implement Circuit Breaker pattern
- Use correlation IDs for tracing
- Implement health checks
- Use container orchestration (Kubernetes)

---

## Database & Data Management

### Q8: How do you handle database transactions and maintain data consistency?

**Answer:**

**1. ACID Transactions:**
```javascript
const { Sequelize } = require('sequelize');
const sequelize = new Sequelize(/* config */);

async function transferFunds(fromId, toId, amount) {
  const t = await sequelize.transaction();
  
  try {
    // Deduct from sender
    const sender = await Account.findByPk(fromId, { 
      transaction: t,
      lock: t.LOCK.UPDATE // Row-level lock
    });
    
    if (sender.balance < amount) {
      throw new Error('Insufficient funds');
    }
    
    await sender.update(
      { balance: sender.balance - amount },
      { transaction: t }
    );
    
    // Add to receiver
    const receiver = await Account.findByPk(toId, { 
      transaction: t,
      lock: t.LOCK.UPDATE
    });
    
    await receiver.update(
      { balance: receiver.balance + amount },
      { transaction: t }
    );
    
    // Commit transaction
    await t.commit();
    return { success: true };
  } catch (error) {
    // Rollback on error
    await t.rollback();
    throw error;
  }
}
```

**2. Saga Pattern (Distributed Transactions):**
```javascript
// Order Saga
class OrderSaga {
  async execute(orderData) {
    const saga = [];
    
    try {
      // Step 1: Create order
      const order = await orderService.create(orderData);
      saga.push(() => orderService.delete(order.id));
      
      // Step 2: Reserve inventory
      await inventoryService.reserve(order.items);
      saga.push(() => inventoryService.release(order.items));
      
      // Step 3: Process payment
      const payment = await paymentService.charge(order.total);
      saga.push(() => paymentService.refund(payment.id));
      
      // Step 4: Send confirmation
      await notificationService.send(order.userId, 'Order confirmed');
      
      return { success: true, order };
    } catch (error) {
      // Compensate: Execute rollback functions in reverse
      for (const compensate of saga.reverse()) {
        await compensate();
      }
      throw error;
    }
  }
}
```

**3. Two-Phase Commit:**
```javascript
class TwoPhaseCommit {
  async execute(participants, operation) {
    // Phase 1: Prepare
    const prepared = [];
    
    for (const participant of participants) {
      const result = await participant.prepare(operation);
      if (!result.canCommit) {
        // Abort
        await this.abort(prepared);
        throw new Error('Participant cannot commit');
      }
      prepared.push(participant);
    }
    
    // Phase 2: Commit
    for (const participant of prepared) {
      await participant.commit(operation);
    }
  }
  
  async abort(participants) {
    for (const participant of participants) {
      await participant.abort();
    }
  }
}
```

---

## Deployment & DevOps

### Q9: What are the best practices for deploying Node.js applications to production?

**Answer:**

**1. Environment Configuration:**
```javascript
// config/index.js
const env = process.env.NODE_ENV || 'development';

const config = {
  development: {
    port: 3000,
    db: {
      host: 'localhost',
      database: 'myapp_dev'
    },
    logLevel: 'debug'
  },
  production: {
    port: process.env.PORT,
    db: {
      host: process.env.DB_HOST,
      database: process.env.DB_NAME
    },
    logLevel: 'error'
  }
};

module.exports = config[env];
```

**2. Process Manager (PM2):**
```javascript
// ecosystem.config.js
module.exports = {
  apps: [{
    name: 'api',
    script: './server.js',
    instances: 'max', // Use all CPU cores
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production'
    },
    error_file: './logs/err.log',
    out_file: './logs/out.log',
    merge_logs: true,
    max_memory_restart: '1G'
  }]
};

// Start
// pm2 start ecosystem.config.js
```

**3. Docker:**
```dockerfile
# Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine

WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .

# Non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001
USER nodejs

EXPOSE 3000
CMD ["node", "server.js"]
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  api:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://postgres:password@db:5432/myapp
    depends_on:
      - db
    restart: unless-stopped
  
  db:
    image: postgres:15
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: password
      POSTGRES_DB: myapp

volumes:
  postgres_data:
```

**4. Health Checks:**
```javascript
// Health check endpoint
app.get('/health', async (req, res) => {
  try {
    // Check database
    await sequelize.authenticate();
    
    // Check Redis
    await redis.ping();
    
    // Check external services
    const apiHealth = await checkExternalAPI();
    
    res.json({
      status: 'healthy',
      uptime: process.uptime(),
      timestamp: Date.now(),
      checks: {
        database: 'ok',
        redis: 'ok',
        externalAPI: apiHealth ? 'ok' : 'degraded'
      }
    });
  } catch (error) {
    res.status(503).json({
      status: 'unhealthy',
      error: error.message
    });
  }
});
```

**5. Logging:**
```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: { service: 'api' },
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}

// Use in code
logger.info('Server started', { port: 3000 });
logger.error('Database connection failed', { error: err.message });
```

**6. Graceful Shutdown:**
```javascript
const server = app.listen(3000);

// Handle shutdown signals
process.on('SIGTERM', gracefulShutdown);
process.on('SIGINT', gracefulShutdown);

async function gracefulShutdown() {
  logger.info('Received shutdown signal');
  
  // Stop accepting new connections
  server.close(() => {
    logger.info('HTTP server closed');
  });
  
  // Close database connections
  await sequelize.close();
  
  // Close Redis connections
  await redis.quit();
  
  // Exit
  process.exit(0);
}
```

---

## Summary

**Node.js Best Practices:**

1. **Performance**: Clustering, worker threads, caching, connection pooling
2. **Security**: Input validation, parameterized queries, rate limiting, helmet
3. **Architecture**: Layered structure, microservices, event-driven
4. **Database**: Transactions, connection pooling, indexing
5. **Deployment**: Docker, PM2, health checks, graceful shutdown
6. **Monitoring**: Logging (Winston), APM tools, error tracking
7. **Testing**: Unit tests, integration tests, E2E tests
8. **Code Quality**: ESLint, Prettier, TypeScript, code reviews

**Remember:** Node.js excels at I/O-heavy operations. For CPU-intensive tasks, use worker threads or separate services!
