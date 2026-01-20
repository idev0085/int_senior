# Node.js - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Core Concepts](#core-concepts)
2. [Performance & Scalability](#performance--scalability)
3. [Security](#security)
4. [Architecture & Design Patterns](#architecture--design-patterns)
5. [Database & Data Management](#database--data-management)
6. [Deployment & DevOps](#deployment--devops)

---

## Core Concepts

### Q1: Explain the Event Loop in Node.js. How does it handle asynchronous operations?

**Answer:**

The Event Loop is like a restaurant manager coordinating orders.

**Simple Analogy:**
- **Call Stack**: Chef cooking (one dish at a time)
- **Task Queue**: Orders waiting to be cooked
- **Event Loop**: Manager checking if chef is free, then giving next order

**How it Works:**

```javascript
console.log('1. Start'); // Synchronous - executes immediately

setTimeout(() => {
  console.log('3. Timeout'); // Async - goes to Task Queue
}, 0);

Promise.resolve().then(() => {
  console.log('2. Promise'); // Async - goes to Microtask Queue
});

console.log('4. End'); // Synchronous - executes immediately

// Output: 1. Start → 4. End → 2. Promise → 3. Timeout
```

**Why this order?**
1. Synchronous code runs first (Call Stack)
2. Microtasks (Promises) run before Macrotasks (setTimeout)
3. Event Loop checks queues only when Call Stack is empty

**Phases of Event Loop:**
```
   ┌───────────────────────────┐
┌─>│           timers          │  setTimeout/setInterval
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │  I/O callbacks
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │  Internal use
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │           poll            │  Retrieve new I/O events
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │           check           │  setImmediate
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │      close callbacks      │  socket.on('close')
│  └───────────────────────────┘
└──────────────────────────────────
```

**Common Pitfall:**
```javascript
// ❌ Blocking the Event Loop
function blockFor5Seconds() {
  const start = Date.now();
  while (Date.now() - start < 5000) {} // BLOCKS everything!
}

// ✅ Non-blocking alternative
async function dontBlock() {
  await new Promise(resolve => setTimeout(resolve, 5000));
}
```

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

## Performance & Scalability

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
