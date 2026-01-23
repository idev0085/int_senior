# Scaling, Optimization, Coding Best Practices & Security

## Table of Contents
1. [Application Scaling](#application-scaling)
2. [Performance Optimization](#performance-optimization)
3. [Coding Best Practices](#coding-best-practices)
4. [Security Best Practices](#security-best-practices)

---

## Application Scaling

### Horizontal vs Vertical Scaling

#### Vertical Scaling (Scale Up)
- Adding more power (CPU, RAM) to existing servers
- **Pros**: Simple to implement, no code changes needed
- **Cons**: Hardware limits, single point of failure, downtime during upgrades
- **Use Case**: Monolithic applications, databases with complex transactions

#### Horizontal Scaling (Scale Out)
- Adding more servers/instances to distribute load
- **Pros**: Better fault tolerance, unlimited scaling potential, cost-effective
- **Cons**: Requires load balancing, stateless architecture needed
- **Use Case**: Web applications, microservices, distributed systems

### Scaling Strategies

#### 1. Load Balancing
```
┌─────────┐
│  Load   │
│ Balancer│
└────┬────┘
     │
  ───┼───────────
  │  │  │       │
┌─▼──▼──▼───┐ ┌─▼────┐
│ Server 1  │ │Server│
│ Server 2  │ │  N   │
│ Server 3  │ └──────┘
└───────────┘
```

**Load Balancing Algorithms:**
- Round Robin: Distributes requests evenly
- Least Connections: Routes to server with fewest active connections
- IP Hash: Routes based on client IP address
- Weighted Round Robin: Routes based on server capacity

#### 2. Caching Strategies

**Cache Levels:**
```
Browser Cache → CDN → Application Cache → Database Cache
```

**Caching Patterns:**

- **Cache-Aside (Lazy Loading)**
  ```javascript
  async function getData(key) {
    let data = await cache.get(key);
    if (!data) {
      data = await database.query(key);
      await cache.set(key, data, TTL);
    }
    return data;
  }
  ```

- **Write-Through Cache**
  ```javascript
  async function saveData(key, value) {
    await database.save(key, value);
    await cache.set(key, value);
  }
  ```

- **Write-Behind Cache**
  ```javascript
  async function saveData(key, value) {
    await cache.set(key, value);
    queue.push({ key, value }); // Async write to DB
  }
  ```

#### 3. Database Scaling

**Read Replicas:**
- Master handles writes, replicas handle reads
- Reduces load on primary database
- Eventual consistency consideration

**Sharding:**
- Horizontal partitioning of data
- Distribute data across multiple databases
- Shard by user ID, geographic region, or hash

**Database Indexing:**
```sql
-- Create index on frequently queried columns
CREATE INDEX idx_user_email ON users(email);
CREATE INDEX idx_order_date ON orders(created_at);

-- Composite index for multiple columns
CREATE INDEX idx_user_status_date ON users(status, created_at);
```

#### 4. Microservices Architecture

**Benefits:**
- Independent scaling of services
- Technology diversity
- Fault isolation
- Faster deployments

**Challenges:**
- Distributed system complexity
- Network latency
- Data consistency
- Monitoring and debugging

#### 5. Message Queues & Async Processing

**Use Cases:**
- Decouple services
- Handle traffic spikes
- Background job processing
- Event-driven architecture

**Example with RabbitMQ/Kafka:**
```javascript
// Producer
await messageQueue.publish('order.created', {
  orderId: 123,
  userId: 456,
  items: [...]
});

// Consumer
messageQueue.subscribe('order.created', async (message) => {
  await processOrder(message);
  await sendEmail(message.userId);
});
```

#### 6. Auto-Scaling

**Cloud Auto-Scaling Rules:**
- CPU utilization > 70%: Add instances
- Memory usage > 80%: Add instances
- Request queue length > threshold: Add instances
- CPU utilization < 30%: Remove instances

---

## Performance Optimization

### Frontend Optimization

#### 1. Asset Optimization

**Image Optimization:**
```html
<!-- Responsive images -->
<img srcset="image-320w.jpg 320w,
             image-640w.jpg 640w,
             image-1280w.jpg 1280w"
     sizes="(max-width: 320px) 280px,
            (max-width: 640px) 600px,
            1200px"
     src="image-640w.jpg" alt="Optimized image">

<!-- Modern formats -->
<picture>
  <source srcset="image.webp" type="image/webp">
  <source srcset="image.jpg" type="image/jpeg">
  <img src="image.jpg" alt="Fallback">
</picture>
```

**Code Splitting:**
```javascript
// React lazy loading
const Dashboard = lazy(() => import('./Dashboard'));
const Profile = lazy(() => import('./Profile'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Route path="/dashboard" component={Dashboard} />
      <Route path="/profile" component={Profile} />
    </Suspense>
  );
}
```

#### 2. Network Optimization

**HTTP/2 & HTTP/3:**
- Multiplexing: Multiple requests over single connection
- Server push: Proactively send resources
- Header compression

**Resource Hints:**
```html
<!-- DNS prefetch -->
<link rel="dns-prefetch" href="//api.example.com">

<!-- Preconnect -->
<link rel="preconnect" href="https://cdn.example.com">

<!-- Prefetch -->
<link rel="prefetch" href="/next-page.html">

<!-- Preload critical resources -->
<link rel="preload" href="critical.css" as="style">
<link rel="preload" href="critical.js" as="script">
```

**Compression:**
```javascript
// Enable Gzip/Brotli compression
app.use(compression({
  level: 6,
  threshold: 1024,
  filter: (req, res) => {
    if (req.headers['x-no-compression']) return false;
    return compression.filter(req, res);
  }
}));
```

#### 3. Rendering Optimization

**Virtual Scrolling:**
```javascript
// React Virtualized
import { List } from 'react-virtualized';

<List
  width={300}
  height={600}
  rowCount={items.length}
  rowHeight={50}
  rowRenderer={({ index, key, style }) => (
    <div key={key} style={style}>
      {items[index]}
    </div>
  )}
/>
```

**Debouncing & Throttling:**
```javascript
// Debounce: Execute after delay
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// Throttle: Execute at most once per interval
function throttle(func, interval) {
  let lastTime = 0;
  return function(...args) {
    const now = Date.now();
    if (now - lastTime >= interval) {
      lastTime = now;
      func.apply(this, args);
    }
  };
}

// Usage
const searchInput = debounce(handleSearch, 300);
const scrollHandler = throttle(handleScroll, 100);
```

### Backend Optimization

#### 1. Database Query Optimization

**N+1 Query Problem:**
```javascript
// ❌ Bad: N+1 queries
const users = await User.findAll();
for (const user of users) {
  const posts = await Post.findAll({ where: { userId: user.id } });
}

// ✅ Good: Single query with join
const users = await User.findAll({
  include: [{ model: Post }]
});
```

**Pagination:**
```javascript
// Offset-based pagination
async function getUsers(page = 1, limit = 20) {
  const offset = (page - 1) * limit;
  return await User.findAll({ limit, offset });
}

// Cursor-based pagination (better for large datasets)
async function getUsers(cursor, limit = 20) {
  return await User.findAll({
    where: { id: { [Op.gt]: cursor } },
    limit,
    order: [['id', 'ASC']]
  });
}
```

**Database Connection Pooling:**
```javascript
// PostgreSQL connection pool
const pool = new Pool({
  host: 'localhost',
  database: 'mydb',
  max: 20, // Maximum connections
  min: 5,  // Minimum connections
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000
});
```

#### 2. API Optimization

**Response Compression:**
```javascript
// Compress API responses
app.use(compression());

// Selective field responses
app.get('/users', (req, res) => {
  const fields = req.query.fields?.split(',') || ['id', 'name', 'email'];
  const users = await User.findAll({ attributes: fields });
  res.json(users);
});
```

**Rate Limiting:**
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  message: 'Too many requests, please try again later'
});

app.use('/api/', limiter);
```

#### 3. Caching Implementation

**Redis Caching:**
```javascript
const redis = require('redis');
const client = redis.createClient();

async function getCachedData(key) {
  const cached = await client.get(key);
  if (cached) return JSON.parse(cached);
  
  const data = await fetchFromDatabase(key);
  await client.setEx(key, 3600, JSON.stringify(data)); // 1 hour TTL
  return data;
}
```

**Memoization:**
```javascript
function memoize(fn) {
  const cache = new Map();
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

const expensiveCalculation = memoize((n) => {
  // Complex calculation
  return n * n;
});
```

#### 4. Async Operations

**Promise.all for Parallel Operations:**
```javascript
// ❌ Sequential (slow)
const user = await getUserData(userId);
const posts = await getUserPosts(userId);
const comments = await getUserComments(userId);

// ✅ Parallel (fast)
const [user, posts, comments] = await Promise.all([
  getUserData(userId),
  getUserPosts(userId),
  getUserComments(userId)
]);
```

**Background Jobs:**
```javascript
// Bull Queue for background processing
const Queue = require('bull');
const emailQueue = new Queue('email', redisConfig);

// Add job to queue
emailQueue.add({
  to: 'user@example.com',
  subject: 'Welcome',
  body: 'Welcome to our platform'
});

// Process jobs
emailQueue.process(async (job) => {
  await sendEmail(job.data);
});
```

---

## Coding Best Practices

### 1. SOLID Principles

#### Single Responsibility Principle (SRP)
```javascript
// ❌ Bad: Multiple responsibilities
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
  
  save() { /* database logic */ }
  sendEmail() { /* email logic */ }
  generateReport() { /* reporting logic */ }
}

// ✅ Good: Single responsibility
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
}

class UserRepository {
  save(user) { /* database logic */ }
}

class EmailService {
  sendWelcomeEmail(user) { /* email logic */ }
}

class ReportGenerator {
  generateUserReport(user) { /* reporting logic */ }
}
```

#### Open/Closed Principle (OCP)
```javascript
// ✅ Open for extension, closed for modification
class Shape {
  area() {
    throw new Error('Must implement area method');
  }
}

class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  }
  
  area() {
    return Math.PI * this.radius ** 2;
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }
  
  area() {
    return this.width * this.height;
  }
}
```

#### Liskov Substitution Principle (LSP)
```javascript
// ✅ Derived classes must be substitutable for base classes
class Bird {
  fly() {
    console.log('Flying');
  }
}

// ❌ Bad: Penguin can't fly, violates LSP
class Penguin extends Bird {
  fly() {
    throw new Error('Cannot fly');
  }
}

// ✅ Good: Proper abstraction
class Bird {
  move() {
    console.log('Moving');
  }
}

class FlyingBird extends Bird {
  fly() {
    console.log('Flying');
  }
}

class Penguin extends Bird {
  swim() {
    console.log('Swimming');
  }
}
```

#### Interface Segregation Principle (ISP)
```javascript
// ❌ Bad: Fat interface
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
}

// ✅ Good: Segregated interfaces
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

interface Sleepable {
  sleep(): void;
}

class Human implements Workable, Eatable, Sleepable {
  work() { /* ... */ }
  eat() { /* ... */ }
  sleep() { /* ... */ }
}

class Robot implements Workable {
  work() { /* ... */ }
}
```

#### Dependency Inversion Principle (DIP)
```javascript
// ❌ Bad: High-level module depends on low-level module
class MySQLDatabase {
  connect() { /* ... */ }
  query(sql) { /* ... */ }
}

class UserService {
  constructor() {
    this.db = new MySQLDatabase(); // Tight coupling
  }
}

// ✅ Good: Depend on abstractions
interface Database {
  connect(): void;
  query(sql: string): any;
}

class MySQLDatabase implements Database {
  connect() { /* ... */ }
  query(sql) { /* ... */ }
}

class PostgreSQLDatabase implements Database {
  connect() { /* ... */ }
  query(sql) { /* ... */ }
}

class UserService {
  constructor(private db: Database) {
    // Dependency injection
  }
}
```

### 2. DRY (Don't Repeat Yourself)

```javascript
// ❌ Bad: Repetitive code
function calculateCircleArea(radius) {
  return 3.14159 * radius * radius;
}

function calculateCircleCircumference(radius) {
  return 2 * 3.14159 * radius;
}

// ✅ Good: Centralized constant
const PI = Math.PI;

function calculateCircleArea(radius) {
  return PI * radius ** 2;
}

function calculateCircleCircumference(radius) {
  return 2 * PI * radius;
}
```

### 3. Clean Code Principles

#### Meaningful Names
```javascript
// ❌ Bad
const d = new Date();
const x = user.data;
function calc(a, b) { return a + b; }

// ✅ Good
const currentDate = new Date();
const userData = user.data;
function calculateTotal(price, quantity) {
  return price * quantity;
}
```

#### Small Functions
```javascript
// ❌ Bad: Long function
function processOrder(order) {
  // Validate order (20 lines)
  // Calculate total (15 lines)
  // Apply discounts (25 lines)
  // Process payment (30 lines)
  // Send confirmation (10 lines)
}

// ✅ Good: Small, focused functions
function processOrder(order) {
  validateOrder(order);
  const total = calculateOrderTotal(order);
  const finalAmount = applyDiscounts(total, order.discountCode);
  processPayment(order.paymentMethod, finalAmount);
  sendOrderConfirmation(order.email, order);
}
```

#### Function Arguments
```javascript
// ❌ Bad: Too many arguments
function createUser(name, email, age, address, phone, role, status) {
  // ...
}

// ✅ Good: Object parameter
function createUser(userOptions) {
  const { name, email, age, address, phone, role, status } = userOptions;
  // ...
}
```

#### Error Handling
```javascript
// ❌ Bad: Ignore errors
try {
  await processPayment();
} catch (error) {
  // Silent failure
}

// ✅ Good: Proper error handling
try {
  await processPayment();
} catch (error) {
  logger.error('Payment processing failed', { error, userId });
  await notifyAdmin(error);
  throw new PaymentError('Payment failed, please try again');
}
```

### 4. Design Patterns

#### Singleton Pattern
```javascript
class Database {
  constructor() {
    if (Database.instance) {
      return Database.instance;
    }
    this.connection = this.createConnection();
    Database.instance = this;
  }
  
  createConnection() {
    // Create database connection
  }
}

const db1 = new Database();
const db2 = new Database();
console.log(db1 === db2); // true
```

#### Factory Pattern
```javascript
class UserFactory {
  createUser(type) {
    switch (type) {
      case 'admin':
        return new AdminUser();
      case 'customer':
        return new CustomerUser();
      case 'guest':
        return new GuestUser();
      default:
        throw new Error('Invalid user type');
    }
  }
}
```

#### Observer Pattern
```javascript
class EventEmitter {
  constructor() {
    this.events = {};
  }
  
  on(event, listener) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(listener);
  }
  
  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(listener => listener(data));
    }
  }
}

const emitter = new EventEmitter();
emitter.on('user.created', (user) => {
  console.log('New user:', user);
  sendWelcomeEmail(user);
});
```

#### Strategy Pattern
```javascript
class PaymentStrategy {
  pay(amount) {
    throw new Error('Must implement pay method');
  }
}

class CreditCardPayment extends PaymentStrategy {
  pay(amount) {
    console.log(`Paid ${amount} via Credit Card`);
  }
}

class PayPalPayment extends PaymentStrategy {
  pay(amount) {
    console.log(`Paid ${amount} via PayPal`);
  }
}

class PaymentContext {
  constructor(strategy) {
    this.strategy = strategy;
  }
  
  executePayment(amount) {
    this.strategy.pay(amount);
  }
}
```

### 5. Testing Best Practices

```javascript
// Unit Test Example
describe('UserService', () => {
  let userService;
  let mockDatabase;
  
  beforeEach(() => {
    mockDatabase = {
      query: jest.fn()
    };
    userService = new UserService(mockDatabase);
  });
  
  describe('createUser', () => {
    it('should create user with valid data', async () => {
      const userData = { name: 'John', email: 'john@example.com' };
      mockDatabase.query.mockResolvedValue({ id: 1, ...userData });
      
      const result = await userService.createUser(userData);
      
      expect(result).toHaveProperty('id');
      expect(result.name).toBe('John');
      expect(mockDatabase.query).toHaveBeenCalledTimes(1);
    });
    
    it('should throw error for invalid email', async () => {
      const userData = { name: 'John', email: 'invalid' };
      
      await expect(userService.createUser(userData))
        .rejects.toThrow('Invalid email');
    });
  });
});
```

### 6. SonarQube - Code Quality & Security Analysis

#### What is SonarQube?

SonarQube is a continuous inspection tool that performs automatic reviews with static analysis to detect:
- **Bugs**: Code that is demonstrably wrong or highly likely to yield unexpected behavior
- **Vulnerabilities**: Security issues that could be exploited
- **Code Smells**: Maintainability issues that make code harder to understand and change
- **Security Hotspots**: Security-sensitive code that requires manual review
- **Code Coverage**: Percentage of code covered by tests
- **Code Duplications**: Repeated code blocks
- **Technical Debt**: Estimated time to fix all maintainability issues

#### SonarQube Setup

**Installation (Docker):**
```bash
# Run SonarQube server
docker run -d --name sonarqube \
  -p 9000:9000 \
  -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true \
  sonarqube:latest

# Access at http://localhost:9000
# Default credentials: admin/admin
```

**Project Configuration (sonar-project.properties):**
```properties
# Project identification
sonar.projectKey=my-project
sonar.projectName=My Project
sonar.projectVersion=1.0

# Source code location
sonar.sources=src
sonar.tests=tests

# Exclusions
sonar.exclusions=**/node_modules/**,**/dist/**,**/build/**,**/*.test.js
sonar.test.exclusions=**/*.test.js,**/*.spec.js

# Coverage reports
sonar.javascript.lcov.reportPaths=coverage/lcov.info
sonar.typescript.lcov.reportPaths=coverage/lcov.info

# Language-specific settings
sonar.sourceEncoding=UTF-8
sonar.language=js
```

#### Running SonarQube Scanner

**Using SonarScanner CLI:**
```bash
# Install SonarScanner
npm install -g sonarqube-scanner

# Run analysis
sonar-scanner \
  -Dsonar.projectKey=my-project \
  -Dsonar.sources=src \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=your-token
```

**Using Node.js:**
```javascript
// sonar-scanner.js
const scanner = require('sonarqube-scanner');

scanner(
  {
    serverUrl: 'http://localhost:9000',
    token: process.env.SONAR_TOKEN,
    options: {
      'sonar.projectKey': 'my-project',
      'sonar.projectName': 'My Project',
      'sonar.sources': 'src',
      'sonar.tests': 'tests',
      'sonar.exclusions': '**/node_modules/**,**/dist/**',
      'sonar.javascript.lcov.reportPaths': 'coverage/lcov.info'
    }
  },
  () => process.exit()
);
```

**Package.json Scripts:**
```json
{
  "scripts": {
    "test": "jest --coverage",
    "sonar": "node sonar-scanner.js",
    "test:sonar": "npm test && npm run sonar"
  }
}
```

#### CI/CD Integration

**GitHub Actions:**
```yaml
name: SonarQube Analysis

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  sonarqube:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0  # Shallow clones disabled for better analysis
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests with coverage
        run: npm test -- --coverage
      
      - name: SonarQube Scan
        uses: SonarSource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
      
      - name: Quality Gate Check
        uses: SonarSource/sonarqube-quality-gate-action@master
        timeout-minutes: 5
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

**Jenkins Pipeline:**
```groovy
pipeline {
    agent any
    
    environment {
        SONAR_TOKEN = credentials('sonar-token')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test -- --coverage'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=my-project \
                        -Dsonar.sources=src \
                        -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                    '''
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
```

#### Quality Gates

**Default Quality Gate Conditions:**
```yaml
Conditions:
  - Coverage < 80%: FAILED
  - Duplicated Lines > 3%: FAILED
  - Maintainability Rating worse than A: FAILED
  - Reliability Rating worse than A: FAILED
  - Security Rating worse than A: FAILED
  - Security Hotspots Reviewed < 100%: FAILED
```

**Custom Quality Gate:**
```javascript
// Quality gate configuration via API
const axios = require('axios');

async function createQualityGate() {
  const response = await axios.post(
    'http://localhost:9000/api/qualitygates/create',
    null,
    {
      params: { name: 'Strict Gate' },
      auth: {
        username: process.env.SONAR_TOKEN,
        password: ''
      }
    }
  );
  
  const gateId = response.data.id;
  
  // Add conditions
  await axios.post(
    'http://localhost:9000/api/qualitygates/create_condition',
    null,
    {
      params: {
        gateId,
        metric: 'coverage',
        op: 'LT',
        error: '90'
      },
      auth: {
        username: process.env.SONAR_TOKEN,
        password: ''
      }
    }
  );
}
```

#### SonarQube Metrics Explained

**Reliability (Bugs):**
- **A**: 0 bugs
- **B**: At least 1 minor bug
- **C**: At least 1 major bug
- **D**: At least 1 critical bug
- **E**: At least 1 blocker bug

**Security (Vulnerabilities):**
- **A**: 0 vulnerabilities
- **B**: At least 1 minor vulnerability
- **C**: At least 1 major vulnerability
- **D**: At least 1 critical vulnerability
- **E**: At least 1 blocker vulnerability

**Maintainability (Code Smells):**
- **A**: Technical debt ratio ≤ 5%
- **B**: 6-10%
- **C**: 11-20%
- **D**: 21-50%
- **E**: > 50%

**Technical Debt:**
```
Technical Debt = Cost to fix all code smells
Technical Debt Ratio = (Technical Debt / Development Cost) × 100
```

#### Code Examples with SonarQube Rules

**1. Cognitive Complexity (S3776)**
```javascript
// ❌ Bad: High cognitive complexity
function processOrder(order) {
  if (order.items.length > 0) {
    for (let item of order.items) {
      if (item.quantity > 0) {
        if (item.price > 0) {
          if (order.discount) {
            if (order.discount.type === 'percentage') {
              // Complex nested logic
            } else {
              // More complexity
            }
          }
        }
      }
    }
  }
}

// ✅ Good: Reduced complexity
function processOrder(order) {
  if (!hasValidItems(order)) return;
  
  order.items
    .filter(isValidItem)
    .forEach(item => applyDiscount(item, order.discount));
}

function hasValidItems(order) {
  return order.items.length > 0;
}

function isValidItem(item) {
  return item.quantity > 0 && item.price > 0;
}
```

**2. Functions Should Not Be Too Complex (S1541)**
```javascript
// ❌ Bad: Function too long
function calculateTotal() {
  // 100+ lines of code
}

// ✅ Good: Break into smaller functions
function calculateTotal() {
  const subtotal = calculateSubtotal();
  const tax = calculateTax(subtotal);
  const shipping = calculateShipping();
  const discount = calculateDiscount(subtotal);
  return subtotal + tax + shipping - discount;
}
```

**3. Security Hotspot: SQL Injection (S2077)**
```javascript
// ❌ Bad: SQL injection vulnerability
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ✅ Good: Parameterized query
const query = 'SELECT * FROM users WHERE id = ?';
db.query(query, [userId]);
```

**4. Code Smell: Duplicated Code (S1192)**
```javascript
// ❌ Bad: Magic strings
if (status === 'pending') { }
if (status === 'pending') { }
if (status === 'pending') { }

// ✅ Good: Constants
const ORDER_STATUS = {
  PENDING: 'pending',
  COMPLETED: 'completed',
  CANCELLED: 'cancelled'
};

if (status === ORDER_STATUS.PENDING) { }
```

#### SonarQube Best Practices

1. **Run Analysis Regularly**
   - On every commit (CI/CD)
   - Before merging pull requests
   - Scheduled nightly builds

2. **Set Realistic Quality Gates**
   - Start with achievable thresholds
   - Gradually increase standards
   - Don't block all builds initially

3. **Focus on New Code**
   - "Clean as You Code" approach
   - Ensure new code meets quality standards
   - Gradually refactor legacy code

4. **Review Security Hotspots**
   - Manually review security-sensitive code
   - Mark as safe or fix vulnerabilities
   - Don't ignore security warnings

5. **Monitor Technical Debt**
   - Track debt trends over time
   - Allocate time for debt reduction
   - Balance features vs. quality

6. **Use Branch Analysis**
   - Analyze feature branches
   - Compare against main branch
   - Catch issues before merge

7. **Customize Rules**
   - Disable irrelevant rules
   - Adjust severity levels
   - Add custom rules if needed

#### SonarQube Dashboard Metrics

```
┌─────────────────────────────────────────┐
│         Project Overview                │
├─────────────────────────────────────────┤
│ Bugs: 0                    Rating: A    │
│ Vulnerabilities: 2         Rating: C    │
│ Code Smells: 45            Rating: A    │
│ Coverage: 85.3%            Target: 80%  │
│ Duplications: 1.2%         Target: 3%   │
│ Technical Debt: 2d 4h                   │
└─────────────────────────────────────────┘
```

### 7. Code Review Checklist

- [ ] Code follows style guide and conventions
- [ ] Meaningful variable and function names
- [ ] No code duplication (DRY principle)
- [ ] Functions are small and focused
- [ ] Proper error handling
- [ ] Unit tests included
- [ ] Edge cases handled
- [ ] Security vulnerabilities addressed
- [ ] Performance considerations
- [ ] Documentation/comments for complex logic

---

## Kibana & Elasticsearch - Logging and Monitoring

### What is the ELK Stack?

The **ELK Stack** (now called **Elastic Stack**) consists of:
- **Elasticsearch**: Distributed search and analytics engine
- **Logstash**: Data processing pipeline for ingesting logs
- **Kibana**: Visualization and exploration tool for Elasticsearch data
- **Beats**: Lightweight data shippers (Filebeat, Metricbeat, etc.)

```
┌──────────┐
│  Beats   │ ──┐
└──────────┘   │
               │
┌──────────┐   │    ┌──────────┐    ┌──────────┐    ┌──────────┐
│   App    │ ──┼───→│ Logstash │───→│ Elastic  │───→│  Kibana  │
│  Logs    │   │    │          │    │  Search  │    │          │
└──────────┘   │    └──────────┘    └──────────┘    └──────────┘
               │
┌──────────┐   │
│  Nginx   │ ──┘
└──────────┘
```

### Setting Up ELK Stack

#### Docker Compose Setup

```yaml
# docker-compose.yml
version: '3.8'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.11.0
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - xpack.security.enabled=false
    ports:
      - "9200:9200"
      - "9300:9300"
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    networks:
      - elk

  logstash:
    image: docker.elastic.co/logstash/logstash:8.11.0
    container_name: logstash
    volumes:
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml
      - ./logstash/pipeline:/usr/share/logstash/pipeline
    ports:
      - "5044:5044"
      - "5000:5000/tcp"
      - "5000:5000/udp"
      - "9600:9600"
    environment:
      - "LS_JAVA_OPTS=-Xmx256m -Xms256m"
    networks:
      - elk
    depends_on:
      - elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:8.11.0
    container_name: kibana
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    networks:
      - elk
    depends_on:
      - elasticsearch

  filebeat:
    image: docker.elastic.co/beats/filebeat:8.11.0
    container_name: filebeat
    user: root
    volumes:
      - ./filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - elk
    depends_on:
      - elasticsearch
      - logstash

volumes:
  elasticsearch_data:
    driver: local

networks:
  elk:
    driver: bridge
```

#### Logstash Configuration

**logstash/config/logstash.yml:**
```yaml
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: ["http://elasticsearch:9200"]
```

**logstash/pipeline/logstash.conf:**
```conf
input {
  beats {
    port => 5044
  }
  
  tcp {
    port => 5000
    codec => json
  }
  
  http {
    port => 8080
    codec => json
  }
}

filter {
  # Parse JSON logs
  if [message] =~ /^\{/ {
    json {
      source => "message"
    }
  }
  
  # Parse timestamp
  date {
    match => ["timestamp", "ISO8601"]
    target => "@timestamp"
  }
  
  # Add geoip location for IP addresses
  if [client_ip] {
    geoip {
      source => "client_ip"
      target => "geoip"
    }
  }
  
  # Parse user agent
  if [user_agent] {
    useragent {
      source => "user_agent"
      target => "ua"
    }
  }
  
  # Classify log levels
  if [level] == "error" or [level] == "fatal" {
    mutate {
      add_tag => ["error"]
    }
  }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "app-logs-%{+YYYY.MM.dd}"
  }
  
  # Debug output
  stdout {
    codec => rubydebug
  }
}
```

#### Filebeat Configuration

**filebeat/filebeat.yml:**
```yaml
filebeat.inputs:
  - type: log
    enabled: true
    paths:
      - /var/log/*.log
      - /app/logs/*.log
    fields:
      log_type: application
    fields_under_root: true
    
  - type: docker
    containers.ids: '*'
    processors:
      - add_docker_metadata: ~

processors:
  - add_host_metadata:
      when.not.contains.tags: forwarded
  - add_cloud_metadata: ~
  - add_docker_metadata: ~

output.logstash:
  hosts: ["logstash:5044"]

logging.level: info
logging.to_files: true
logging.files:
  path: /var/log/filebeat
  name: filebeat
  keepfiles: 7
  permissions: 0644
```

### Application Integration

#### Node.js/Express Logging to ELK

**Using Winston with Logstash:**
```javascript
const winston = require('winston');
const LogstashTransport = require('winston-logstash/lib/winston-logstash-latest');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  defaultMeta: { 
    service: 'user-service',
    environment: process.env.NODE_ENV 
  },
  transports: [
    // Console output
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.colorize(),
        winston.format.simple()
      )
    }),
    
    // File output
    new winston.transports.File({ 
      filename: 'logs/error.log', 
      level: 'error' 
    }),
    new winston.transports.File({ 
      filename: 'logs/combined.log' 
    }),
    
    // Logstash output
    new LogstashTransport({
      port: 5000,
      host: 'localhost',
      node_name: 'user-service',
      max_connect_retries: -1
    })
  ]
});

module.exports = logger;
```

**Express Middleware for Request Logging:**
```javascript
const logger = require('./logger');

function requestLogger(req, res, next) {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = Date.now() - start;
    
    logger.info('HTTP Request', {
      method: req.method,
      url: req.url,
      status: res.statusCode,
      duration: `${duration}ms`,
      ip: req.ip,
      user_agent: req.get('user-agent'),
      user_id: req.user?.id,
      request_id: req.id,
      response_time: duration
    });
  });
  
  next();
}

app.use(requestLogger);
```

**Error Logging:**
```javascript
function errorHandler(err, req, res, next) {
  logger.error('Application Error', {
    error: {
      message: err.message,
      stack: err.stack,
      code: err.code
    },
    request: {
      method: req.method,
      url: req.url,
      headers: req.headers,
      body: req.body,
      user_id: req.user?.id
    },
    timestamp: new Date().toISOString()
  });
  
  res.status(err.statusCode || 500).json({
    error: process.env.NODE_ENV === 'production' 
      ? 'Internal Server Error' 
      : err.message
  });
}

app.use(errorHandler);
```

**Business Event Logging:**
```javascript
class OrderService {
  async createOrder(orderData) {
    try {
      const order = await Order.create(orderData);
      
      logger.info('Order Created', {
        event: 'order.created',
        order_id: order.id,
        user_id: orderData.userId,
        total_amount: order.total,
        items_count: order.items.length,
        payment_method: orderData.paymentMethod,
        timestamp: new Date().toISOString()
      });
      
      return order;
    } catch (error) {
      logger.error('Order Creation Failed', {
        event: 'order.creation.failed',
        error: error.message,
        user_id: orderData.userId,
        timestamp: new Date().toISOString()
      });
      throw error;
    }
  }
}
```

#### Structured Logging Best Practices

```javascript
// ❌ Bad: Unstructured logging
logger.info('User John logged in from 192.168.1.1');
logger.error('Payment failed: ' + error.message);

// ✅ Good: Structured logging
logger.info('User Login', {
  event: 'user.login',
  user_id: user.id,
  username: user.username,
  ip_address: req.ip,
  timestamp: new Date().toISOString()
});

logger.error('Payment Failed', {
  event: 'payment.failed',
  error: {
    message: error.message,
    code: error.code,
    stack: error.stack
  },
  payment: {
    order_id: orderId,
    amount: amount,
    method: paymentMethod
  },
  user_id: userId,
  timestamp: new Date().toISOString()
});
```

### Kibana Dashboards & Visualizations

#### Creating Index Pattern

1. Open Kibana (http://localhost:5601)
2. Go to **Management → Stack Management → Index Patterns**
3. Create index pattern: `app-logs-*`
4. Select timestamp field: `@timestamp`

#### Discover - Log Exploration

**KQL (Kibana Query Language) Examples:**
```
# Find all error logs
level: "error"

# Find logs for specific user
user_id: "12345"

# Find slow requests (> 1000ms)
response_time > 1000

# Find 500 errors
status: 500

# Combine conditions
level: "error" AND status: 500 AND url: "/api/*"

# Find orders created in last 24 hours
event: "order.created" AND @timestamp >= now-24h

# Find failed payments
event: "payment.failed" AND amount > 100
```

#### Common Visualizations

**1. Error Rate Over Time (Line Chart)**
```
Metric: Count
Filter: level: "error"
X-Axis: Date Histogram (@timestamp)
Interval: Auto
```

**2. Response Time Distribution (Histogram)**
```
Metric: Average (response_time)
X-Axis: Date Histogram (@timestamp)
Y-Axis: Average response_time
```

**3. Top Error Messages (Data Table)**
```
Metric: Count
Rows: Terms (error.message)
Size: 10
Sort: Descending
```

**4. HTTP Status Code Distribution (Pie Chart)**
```
Metric: Count
Slice: Terms (status)
Size: 10
```

**5. Geographic Distribution (Maps)**
```
Metric: Count
Field: geoip.location
```

**6. Request Rate (Metric)**
```
Metric: Count
Time Range: Last 15 minutes
Refresh: 10 seconds
```

#### Creating a Dashboard

**Example: Application Monitoring Dashboard**

```javascript
// Dashboard panels configuration
{
  "title": "Application Monitoring Dashboard",
  "panels": [
    {
      "title": "Total Requests (15min)",
      "type": "metric",
      "query": "*"
    },
    {
      "title": "Error Rate",
      "type": "line",
      "query": "level: error"
    },
    {
      "title": "Response Time (p95)",
      "type": "line",
      "query": "*",
      "aggregation": "percentiles",
      "field": "response_time",
      "percentile": 95
    },
    {
      "title": "Top Endpoints",
      "type": "table",
      "aggregation": "terms",
      "field": "url.keyword"
    },
    {
      "title": "Status Codes",
      "type": "pie",
      "aggregation": "terms",
      "field": "status"
    },
    {
      "title": "User Activity Map",
      "type": "map",
      "field": "geoip.location"
    }
  ]
}
```

### Alerts & Monitoring

#### Kibana Alerting (Watcher)

**Create Alert for High Error Rate:**
```json
{
  "trigger": {
    "schedule": {
      "interval": "5m"
    }
  },
  "input": {
    "search": {
      "request": {
        "indices": ["app-logs-*"],
        "body": {
          "query": {
            "bool": {
              "filter": [
                {
                  "term": {
                    "level": "error"
                  }
                },
                {
                  "range": {
                    "@timestamp": {
                      "gte": "now-5m"
                    }
                  }
                }
              ]
            }
          }
        }
      }
    }
  },
  "condition": {
    "compare": {
      "ctx.payload.hits.total": {
        "gt": 100
      }
    }
  },
  "actions": {
    "send_email": {
      "email": {
        "to": "team@example.com",
        "subject": "High Error Rate Alert",
        "body": "Error count exceeded 100 in the last 5 minutes"
      }
    },
    "webhook": {
      "webhook": {
        "method": "POST",
        "url": "https://slack.com/api/webhooks/...",
        "body": "{\"text\": \"High error rate detected!\"}"
      }
    }
  }
}
```

**Node.js Alert Integration:**
```javascript
const axios = require('axios');

class AlertService {
  async checkErrorRate() {
    const response = await axios.post(
      'http://localhost:9200/app-logs-*/_search',
      {
        query: {
          bool: {
            filter: [
              { term: { level: 'error' } },
              { range: { '@timestamp': { gte: 'now-5m' } } }
            ]
          }
        },
        size: 0
      }
    );
    
    const errorCount = response.data.hits.total.value;
    
    if (errorCount > 100) {
      await this.sendAlert({
        severity: 'high',
        message: `High error rate: ${errorCount} errors in last 5 minutes`,
        timestamp: new Date()
      });
    }
  }
  
  async sendAlert(alert) {
    // Send to Slack, PagerDuty, email, etc.
    await axios.post('https://hooks.slack.com/services/...', {
      text: `🚨 Alert: ${alert.message}`,
      attachments: [
        {
          color: 'danger',
          fields: [
            { title: 'Severity', value: alert.severity, short: true },
            { title: 'Time', value: alert.timestamp, short: true }
          ]
        }
      ]
    });
  }
}

// Schedule alert checks
setInterval(() => {
  new AlertService().checkErrorRate();
}, 5 * 60 * 1000); // Every 5 minutes
```

### Advanced Elasticsearch Queries

#### Aggregations for Analytics

```javascript
// Get error trends by hour
const errorTrends = await elasticsearch.search({
  index: 'app-logs-*',
  body: {
    query: {
      term: { level: 'error' }
    },
    aggs: {
      errors_over_time: {
        date_histogram: {
          field: '@timestamp',
          interval: 'hour'
        }
      }
    },
    size: 0
  }
});

// Top 10 slowest endpoints
const slowEndpoints = await elasticsearch.search({
  index: 'app-logs-*',
  body: {
    aggs: {
      slow_endpoints: {
        terms: {
          field: 'url.keyword',
          size: 10,
          order: { avg_response_time: 'desc' }
        },
        aggs: {
          avg_response_time: {
            avg: { field: 'response_time' }
          }
        }
      }
    },
    size: 0
  }
});

// User activity analysis
const userActivity = await elasticsearch.search({
  index: 'app-logs-*',
  body: {
    query: {
      range: {
        '@timestamp': { gte: 'now-24h' }
      }
    },
    aggs: {
      unique_users: {
        cardinality: { field: 'user_id' }
      },
      top_users: {
        terms: {
          field: 'user_id',
          size: 10
        },
        aggs: {
          actions: {
            terms: { field: 'event.keyword' }
          }
        }
      }
    },
    size: 0
  }
});
```

### Performance Optimization

#### Index Lifecycle Management (ILM)

```json
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_size": "50GB",
            "max_age": "1d"
          }
        }
      },
      "warm": {
        "min_age": "7d",
        "actions": {
          "shrink": {
            "number_of_shards": 1
          },
          "forcemerge": {
            "max_num_segments": 1
          }
        }
      },
      "cold": {
        "min_age": "30d",
        "actions": {
          "freeze": {}
        }
      },
      "delete": {
        "min_age": "90d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

### Kibana Best Practices

1. **Use Index Patterns Wisely**
   - Create separate indices for different log types
   - Use time-based indices (daily/monthly)
   - Implement ILM for old data

2. **Optimize Queries**
   - Use filters instead of queries when possible
   - Limit time ranges for better performance
   - Use field existence checks

3. **Dashboard Design**
   - Keep dashboards focused (5-10 panels max)
   - Use appropriate visualization types
   - Set reasonable refresh intervals
   - Use filters and variables

4. **Security**
   - Enable authentication
   - Use role-based access control
   - Encrypt data in transit and at rest
   - Audit log access

5. **Monitoring**
   - Monitor cluster health
   - Track index sizes
   - Set up alerts for critical events
   - Regular backup and recovery testing

---

## Debugging & Root Cause Analysis (RCA)

### Debugging Strategies

#### 1. Systematic Debugging Approach

**The Scientific Method for Debugging:**
```
1. Observe: What is the actual behavior?
2. Hypothesize: What could be causing it?
3. Test: Verify or reject the hypothesis
4. Repeat: Continue until root cause found
5. Fix: Implement the solution
6. Verify: Confirm the fix works
```

**Debugging Workflow:**
```
┌─────────────────┐
│  Bug Reported   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Reproduce      │ ◄───────┐
│  the Issue      │         │
└────────┬────────┘         │
         │                  │
         ▼                  │
┌─────────────────┐         │
│  Gather Logs    │         │
│  & Context      │         │
└────────┬────────┘         │
         │                  │
         ▼                  │
┌─────────────────┐         │
│  Analyze &      │         │
│  Hypothesize    │         │
└────────┬────────┘         │
         │                  │
         ▼                  │
┌─────────────────┐         │
│  Test Fix       │         │
└────────┬────────┘         │
         │                  │
    ┌────┴────┐             │
    │  Works? │             │
    └────┬────┘             │
         │                  │
    Yes  │  No              │
         │  └───────────────┘
         ▼
┌─────────────────┐
│  Deploy &       │
│  Monitor        │
└─────────────────┘
```

#### 2. Debugging Techniques

**Console Logging (Strategic):**
```javascript
// ❌ Bad: Random console logs
console.log('here');
console.log('here2');
console.log(data);

// ✅ Good: Meaningful logs
console.log('[UserService] Creating user:', { 
  email: userData.email,
  timestamp: new Date().toISOString() 
});

console.log('[UserService] Database response:', {
  success: true,
  userId: result.id,
  duration: Date.now() - startTime
});

// ✅ Use log levels
console.debug('Detailed debug info');
console.info('User created successfully');
console.warn('Deprecated API used');
console.error('Failed to create user', error);

// ✅ Conditional logging
const DEBUG = process.env.DEBUG === 'true';
if (DEBUG) {
  console.log('Debug info:', complexObject);
}
```

**Debugger Statements:**
```javascript
function calculateDiscount(price, discountCode) {
  // Set breakpoint here
  debugger;
  
  const discount = getDiscountByCode(discountCode);
  const finalPrice = price - (price * discount / 100);
  
  // Inspect variables at this point
  debugger;
  
  return finalPrice;
}
```

**VS Code Debugging Configuration:**
```json
// .vscode/launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Node App",
      "program": "${workspaceFolder}/src/index.js",
      "restart": true,
      "console": "integratedTerminal",
      "env": {
        "NODE_ENV": "development",
        "DEBUG": "*"
      }
    },
    {
      "type": "node",
      "request": "attach",
      "name": "Attach to Process",
      "port": 9229,
      "restart": true
    },
    {
      "type": "chrome",
      "request": "launch",
      "name": "Debug React App",
      "url": "http://localhost:3000",
      "webRoot": "${workspaceFolder}/src",
      "sourceMapPathOverrides": {
        "webpack:///src/*": "${webRoot}/*"
      }
    }
  ]
}
```

**Chrome DevTools Debugging:**
```javascript
// Conditional breakpoints
// Right-click breakpoint → Edit breakpoint → Add condition
// Example: userId === '12345'

// Logpoints (non-breaking console.log)
// Right-click line → Add logpoint
// Example: User {userId} logged in

// Watch expressions
// Add to watch: req.user.role, order.total, etc.

// Call stack analysis
// Examine function call hierarchy

// Network tab debugging
// - Check request/response
// - Timing analysis
// - Headers inspection
```

**Error Boundaries (React):**
```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null, errorInfo: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', {
      error: error.toString(),
      componentStack: errorInfo.componentStack,
      timestamp: new Date().toISOString()
    });
    
    // Send to error tracking service
    logErrorToService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h2>Something went wrong.</h2>
          {process.env.NODE_ENV === 'development' && (
            <details>
              <summary>Error details</summary>
              <pre>{this.state.error?.toString()}</pre>
              <pre>{this.state.errorInfo?.componentStack}</pre>
            </details>
          )}
        </div>
      );
    }

    return this.props.children;
  }
}

// Usage
<ErrorBoundary>
  <App />
</ErrorBoundary>
```

**Assert and Invariants:**
```javascript
function assert(condition, message) {
  if (!condition) {
    throw new Error(`Assertion failed: ${message}`);
  }
}

function invariant(condition, message) {
  if (!condition) {
    throw new Error(`Invariant violation: ${message}`);
  }
}

// Usage
function processPayment(amount, currency) {
  assert(amount > 0, 'Amount must be positive');
  assert(currency, 'Currency is required');
  invariant(['USD', 'EUR', 'GBP'].includes(currency), 
    `Unsupported currency: ${currency}`);
  
  // Process payment
}
```

#### 3. Common Debugging Scenarios

**Memory Leaks:**
```javascript
// ❌ Memory leak: Event listener not removed
class Component {
  componentDidMount() {
    window.addEventListener('resize', this.handleResize);
  }
  // Missing cleanup!
}

// ✅ Proper cleanup
class Component {
  componentDidMount() {
    window.addEventListener('resize', this.handleResize);
  }
  
  componentWillUnmount() {
    window.removeEventListener('resize', this.handleResize);
  }
}

// Detect memory leaks
// Chrome DevTools → Memory → Take Heap Snapshot
// Compare snapshots over time to find retained objects

// Node.js memory debugging
const used = process.memoryUsage();
console.log({
  rss: `${Math.round(used.rss / 1024 / 1024)} MB`,
  heapTotal: `${Math.round(used.heapTotal / 1024 / 1024)} MB`,
  heapUsed: `${Math.round(used.heapUsed / 1024 / 1024)} MB`,
  external: `${Math.round(used.external / 1024 / 1024)} MB`
});
```

**Race Conditions:**
```javascript
// ❌ Race condition
let data;
fetchData().then(result => data = result);
console.log(data); // undefined! Race condition

// ✅ Proper async handling
async function getData() {
  const data = await fetchData();
  console.log(data);
  return data;
}

// ❌ Multiple async operations
async function updateUser(userId, updates) {
  const user = await getUser(userId);
  user.lastLogin = new Date();
  await saveUser(user); // Race condition if called multiple times
}

// ✅ Optimistic locking
async function updateUser(userId, updates) {
  const user = await getUser(userId);
  const version = user.version;
  
  user.lastLogin = new Date();
  user.version = version + 1;
  
  const result = await saveUser(user, { 
    where: { id: userId, version: version } 
  });
  
  if (!result) {
    throw new Error('Update conflict - user was modified');
  }
}
```

**Null/Undefined Errors:**
```javascript
// ❌ Unsafe property access
const userName = user.profile.name; // Error if profile is null

// ✅ Optional chaining
const userName = user?.profile?.name;

// ✅ Nullish coalescing
const displayName = userName ?? 'Anonymous';

// ✅ Default parameters
function greet(name = 'Guest') {
  console.log(`Hello, ${name}`);
}

// ✅ Guard clauses
function processUser(user) {
  if (!user) {
    console.error('User is null or undefined');
    return;
  }
  
  if (!user.profile) {
    console.warn('User profile is missing');
    return;
  }
  
  // Safe to access user.profile now
}
```

**Async/Await Errors:**
```javascript
// ❌ Unhandled promise rejection
async function fetchUser(id) {
  const response = await fetch(`/api/users/${id}`);
  return response.json(); // Could fail!
}

// ✅ Proper error handling
async function fetchUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    
    return await response.json();
  } catch (error) {
    console.error('Failed to fetch user:', { id, error: error.message });
    throw error; // Re-throw or handle appropriately
  }
}

// ❌ Promise.all fails on first error
const [users, posts] = await Promise.all([
  fetchUsers(),
  fetchPosts() // If this fails, everything fails
]);

// ✅ Promise.allSettled for partial success
const results = await Promise.allSettled([
  fetchUsers(),
  fetchPosts()
]);

const users = results[0].status === 'fulfilled' ? results[0].value : [];
const posts = results[1].status === 'fulfilled' ? results[1].value : [];
```

### Root Cause Analysis (RCA)

#### The 5 Whys Technique

**Example: Website is down**
```
Problem: Website is down

Why 1: Why is the website down?
→ The server is not responding

Why 2: Why is the server not responding?
→ The process crashed due to out of memory error

Why 3: Why did the process run out of memory?
→ Memory leak in the image processing service

Why 4: Why is there a memory leak?
→ Images are loaded but never released from cache

Why 5: Why are images not released from cache?
→ Cache cleanup logic was disabled in recent deployment

Root Cause: Cache cleanup disabled
Solution: Re-enable cache cleanup and add monitoring
```

#### RCA Process

**1. Define the Problem:**
```markdown
# Incident Report

**Date**: 2026-01-23 14:30 UTC
**Duration**: 45 minutes
**Impact**: API response time increased from 200ms to 5000ms
**Affected Users**: ~10,000 users
**Severity**: High

## Symptoms
- Slow API responses
- Increased error rate (5%)
- High database CPU usage
- Customer complaints
```

**2. Timeline Construction:**
```markdown
## Timeline

| Time (UTC) | Event | Action Taken |
|------------|-------|--------------|
| 14:30 | Alert triggered: High response time | Team notified |
| 14:32 | Confirmed API slowdown | Started investigation |
| 14:35 | Identified database CPU at 95% | Checked slow query log |
| 14:40 | Found N+1 query in new code | Identified root cause |
| 14:45 | Rolled back deployment | Service recovered |
| 15:00 | Created fix with proper eager loading | - |
| 15:30 | Deployed fix | - |
| 16:00 | Confirmed resolution | Closed incident |
```

**3. Root Cause Identification:**
```markdown
## Root Cause

**Primary Cause**: N+1 query problem introduced in release v2.5.0

**Code Change**:
```javascript
// Before (v2.4.0) - Efficient
const users = await User.findAll({
  include: [{ model: Post }]
});

// After (v2.5.0) - N+1 Problem
const users = await User.findAll();
for (const user of users) {
  user.posts = await Post.findAll({ where: { userId: user.id } });
}
```

**Contributing Factors**:
1. Missing query performance test in CI/CD
2. Code review didn't catch the N+1 pattern
3. No load testing before production deployment
4. Database query monitoring alerts not sensitive enough
```

**4. Impact Analysis:**
```markdown
## Impact Assessment

### User Impact
- 10,000 users experienced slow page loads
- 500 users (5%) encountered timeouts
- Estimated 200 abandoned carts

### Business Impact
- Revenue: ~$5,000 in lost sales
- Reputation: 15 negative social media mentions
- SLA: Breached 99.9% uptime target for the month

### Technical Impact
- Database: 95% CPU utilization
- Application: 20x increase in response time
- Infrastructure: No permanent damage
```

**5. Action Items:**
```markdown
## Action Items

### Immediate (Done)
- [x] Rollback to v2.4.0
- [x] Deploy fix with proper eager loading
- [x] Verify resolution

### Short-term (Within 1 week)
- [ ] Add query performance tests to CI/CD
- [ ] Update code review checklist for N+1 patterns
- [ ] Implement database query monitoring dashboard
- [ ] Add load testing to staging environment

### Long-term (Within 1 month)
- [ ] Implement automatic query analysis in development
- [ ] Add performance budgets to CI/CD
- [ ] Conduct team training on SQL optimization
- [ ] Review and improve alerting thresholds
- [ ] Implement chaos engineering practices

### Owners & Deadlines
| Task | Owner | Deadline |
|------|-------|----------|
| Query performance tests | @dev-team | Jan 30 |
| Code review update | @tech-lead | Jan 25 |
| Monitoring dashboard | @ops-team | Jan 28 |
| Load testing setup | @qa-team | Feb 1 |
```

#### RCA Document Template

```markdown
# Root Cause Analysis Report

## Executive Summary
[Brief overview of incident and resolution]

## Incident Details
- **Date & Time**: 
- **Duration**: 
- **Severity**: 
- **Services Affected**: 
- **Users Affected**: 

## What Happened?
[Detailed description of the incident]

## Timeline
[Chronological sequence of events]

## Root Cause
[Primary cause with technical details]

## Contributing Factors
[Secondary factors that enabled or worsened the issue]

## Impact Analysis
- User Impact
- Business Impact
- Technical Impact

## What Went Well?
[Positive aspects of incident response]

## What Could Be Improved?
[Areas for improvement]

## Action Items
[Specific, measurable, actionable, relevant, time-bound tasks]

## Lessons Learned
[Key takeaways for future prevention]
```

#### Common Root Causes & Solutions

**1. Configuration Errors:**
```javascript
// Problem: Wrong environment variable in production
const API_URL = process.env.API_URL; // Undefined in production!

// Solution: Validate environment variables at startup
function validateEnv() {
  const required = ['API_URL', 'DATABASE_URL', 'JWT_SECRET'];
  const missing = required.filter(key => !process.env[key]);
  
  if (missing.length > 0) {
    throw new Error(`Missing required environment variables: ${missing.join(', ')}`);
  }
}

validateEnv();
```

**2. Dependency Issues:**
```bash
# Problem: Broken dependency after update
npm install package@latest # Breaks existing code

# Solution: Lock dependencies
npm install package@1.2.3 --save-exact

# Use package-lock.json
npm ci # Install exact versions from lock file

# Regular security audits
npm audit
npm audit fix
```

**3. Database Issues:**
```sql
-- Problem: Missing database index causing slow queries
SELECT * FROM orders WHERE user_id = 123; -- Slow!

-- Solution: Add appropriate indexes
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- Monitor slow queries
-- Enable slow query log in MySQL/PostgreSQL
-- Review and optimize regularly
```

**4. Resource Exhaustion:**
```javascript
// Problem: Connection pool exhausted
const pool = new Pool({ max: 10 }); // Only 10 connections

// Solution: Proper connection management
const pool = new Pool({
  max: 20, // Increase pool size
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000
});

// Always release connections
async function queryDatabase(sql) {
  const client = await pool.connect();
  try {
    return await client.query(sql);
  } finally {
    client.release(); // Always release!
  }
}
```

**5. Code Defects:**
```javascript
// Problem: Off-by-one error
for (let i = 0; i <= array.length; i++) { // Bug!
  console.log(array[i]);
}

// Solution: Proper bounds checking
for (let i = 0; i < array.length; i++) {
  console.log(array[i]);
}

// Or use modern iteration
array.forEach(item => console.log(item));
```

### Advanced Debugging Tools

#### 1. Performance Profiling

**Node.js Profiling:**
```bash
# Start app with --inspect flag
node --inspect index.js

# Or for immediate break
node --inspect-brk index.js

# Chrome DevTools: chrome://inspect
# Click "inspect" to open profiler
```

**CPU Profiling:**
```javascript
// Using Node.js built-in profiler
const { performance, PerformanceObserver } = require('perf_hooks');

const obs = new PerformanceObserver((items) => {
  console.log(items.getEntries()[0].duration);
  performance.clearMarks();
});
obs.observe({ entryTypes: ['measure'] });

performance.mark('start');
// ... code to measure
performance.mark('end');
performance.measure('My Operation', 'start', 'end');
```

**Memory Profiling:**
```javascript
// Heap dump
const v8 = require('v8');
const fs = require('fs');

function takeHeapSnapshot() {
  const filename = `heap-${Date.now()}.heapsnapshot`;
  const snapshot = v8.writeHeapSnapshot(filename);
  console.log(`Heap snapshot written to ${snapshot}`);
}

// Call when memory usage is high
takeHeapSnapshot();
```

#### 2. Distributed Tracing

**OpenTelemetry Implementation:**
```javascript
const { NodeTracerProvider } = require('@opentelemetry/sdk-trace-node');
const { registerInstrumentations } = require('@opentelemetry/instrumentation');
const { HttpInstrumentation } = require('@opentelemetry/instrumentation-http');
const { ExpressInstrumentation } = require('@opentelemetry/instrumentation-express');

const provider = new NodeTracerProvider();
provider.register();

registerInstrumentations({
  instrumentations: [
    new HttpInstrumentation(),
    new ExpressInstrumentation(),
  ],
});

// Create spans for custom operations
const tracer = provider.getTracer('my-service');

async function processOrder(orderId) {
  const span = tracer.startSpan('process-order');
  span.setAttribute('order.id', orderId);
  
  try {
    const order = await fetchOrder(orderId);
    span.addEvent('order-fetched');
    
    await validateOrder(order);
    span.addEvent('order-validated');
    
    await chargePayment(order);
    span.addEvent('payment-charged');
    
    span.setStatus({ code: SpanStatusCode.OK });
  } catch (error) {
    span.setStatus({ 
      code: SpanStatusCode.ERROR, 
      message: error.message 
    });
    throw error;
  } finally {
    span.end();
  }
}
```

#### 3. Request Correlation

**Request ID Tracking:**
```javascript
const { v4: uuidv4 } = require('uuid');

// Middleware to add request ID
function requestIdMiddleware(req, res, next) {
  req.id = req.headers['x-request-id'] || uuidv4();
  res.setHeader('X-Request-ID', req.id);
  next();
}

app.use(requestIdMiddleware);

// Use in logs
logger.info('Processing request', { 
  requestId: req.id,
  method: req.method,
  url: req.url 
});

// Pass to downstream services
async function callExternalAPI(req, data) {
  return await axios.post('https://api.example.com/endpoint', data, {
    headers: {
      'X-Request-ID': req.id
    }
  });
}
```

#### 4. Error Tracking Services

**Sentry Integration:**
```javascript
const Sentry = require('@sentry/node');

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 1.0,
});

// Capture errors with context
try {
  await processPayment(order);
} catch (error) {
  Sentry.captureException(error, {
    tags: {
      section: 'payment',
      order_id: order.id
    },
    user: {
      id: order.userId,
      email: order.userEmail
    },
    extra: {
      order: order,
      attemptNumber: retryCount
    }
  });
  throw error;
}

// Set up Express error handler
app.use(Sentry.Handlers.errorHandler());
```

### Debugging Best Practices Checklist

#### Before Debugging
- [ ] Can you reproduce the issue consistently?
- [ ] Do you have access to relevant logs?
- [ ] Is the issue in production or development?
- [ ] What changed recently (code, config, infrastructure)?
- [ ] Are there any alerts or monitoring data?

#### During Debugging
- [ ] Use version control to track changes
- [ ] Document your findings and hypotheses
- [ ] Test one change at a time
- [ ] Keep the original issue in mind (avoid rabbit holes)
- [ ] Use appropriate debugging tools
- [ ] Ask for help when stuck (rubber duck debugging)

#### After Debugging
- [ ] Document the root cause
- [ ] Write tests to prevent regression
- [ ] Update documentation if needed
- [ ] Share findings with the team
- [ ] Implement preventive measures
- [ ] Update monitoring/alerting if needed

### Preventive Measures

**1. Defensive Programming:**
```javascript
function calculateDiscount(price, discountPercent) {
  // Input validation
  if (typeof price !== 'number' || price < 0) {
    throw new Error('Invalid price');
  }
  
  if (typeof discountPercent !== 'number' || 
      discountPercent < 0 || 
      discountPercent > 100) {
    throw new Error('Invalid discount percentage');
  }
  
  // Calculation
  const discount = price * (discountPercent / 100);
  return price - discount;
}
```

**2. Comprehensive Testing:**
```javascript
describe('Payment Processing', () => {
  it('should process valid payment', async () => {
    const result = await processPayment(validPayment);
    expect(result.status).toBe('success');
  });
  
  it('should reject invalid card', async () => {
    await expect(processPayment(invalidCard))
      .rejects.toThrow('Invalid card');
  });
  
  it('should handle network errors', async () => {
    mockPaymentGateway.post.mockRejectedValue(new Error('Network error'));
    await expect(processPayment(validPayment))
      .rejects.toThrow('Payment failed');
  });
});
```

**3. Monitoring & Alerting:**
```javascript
// Application Performance Monitoring
const apm = require('elastic-apm-node').start({
  serviceName: 'my-service',
  serverUrl: process.env.APM_SERVER_URL
});

// Custom metrics
function trackMetric(name, value, tags = {}) {
  apm.setCustomContext({
    metric: { [name]: value },
    tags
  });
}

// Alert on anomalies
async function checkHealthMetrics() {
  const errorRate = await getErrorRate();
  if (errorRate > 0.05) { // 5% error rate
    await sendAlert({
      severity: 'high',
      message: `Error rate is ${errorRate * 100}%`,
      service: 'api'
    });
  }
}
```

---

## Requirements Analysis & Cost Estimation

### Requirements Analysis

#### What is Requirements Analysis?

Requirements analysis is the process of defining user expectations for a new or modified product. It involves:
- **Understanding** stakeholder needs
- **Documenting** functional and non-functional requirements
- **Validating** requirements with stakeholders
- **Managing** requirement changes throughout the project lifecycle

```
┌─────────────┐
│ Stakeholder │
│   Needs     │
└──────┬──────┘
       │
       ▼
┌─────────────┐      ┌──────────────┐
│ Requirements│─────▶│  Analysis &  │
│  Gathering  │      │ Validation   │
└─────────────┘      └──────┬───────┘
                            │
                            ▼
                     ┌──────────────┐
                     │ Requirements │
                     │ Documentation│
                     └──────┬───────┘
                            │
                            ▼
                     ┌──────────────┐
                     │   Design &   │
                     │ Development  │
                     └──────────────┘
```

#### Types of Requirements

**1. Functional Requirements (What the system should do)**
```markdown
## Functional Requirements

### User Authentication
- FR-001: System must allow users to register with email and password
- FR-002: System must validate email format before registration
- FR-003: System must send verification email after registration
- FR-004: Users must verify email within 24 hours
- FR-005: System must allow login with email and password
- FR-006: System must support "Forgot Password" functionality
- FR-007: System must enforce password reset within 1 hour

### Order Management
- FR-101: Users must be able to add items to shopping cart
- FR-102: System must calculate total including tax and shipping
- FR-103: Users must be able to apply discount codes
- FR-104: System must validate discount codes before applying
- FR-105: Users must be able to checkout and complete payment
- FR-106: System must send order confirmation email
```

**2. Non-Functional Requirements (How the system should perform)**
```markdown
## Non-Functional Requirements

### Performance
- NFR-001: API response time must be < 200ms for 95% of requests
- NFR-002: Page load time must be < 2 seconds
- NFR-003: System must support 10,000 concurrent users
- NFR-004: Database queries must execute in < 100ms

### Security
- NFR-101: All data in transit must be encrypted using TLS 1.3
- NFR-102: Passwords must be hashed using bcrypt (min 12 rounds)
- NFR-103: System must implement rate limiting (100 req/min per IP)
- NFR-104: All API endpoints must require authentication except public ones
- NFR-105: System must comply with GDPR and data privacy regulations

### Reliability
- NFR-201: System uptime must be 99.9% (excluding planned maintenance)
- NFR-202: System must have automated backup every 6 hours
- NFR-203: Recovery Point Objective (RPO) must be < 1 hour
- NFR-204: Recovery Time Objective (RTO) must be < 4 hours

### Scalability
- NFR-301: System must scale horizontally to handle increased load
- NFR-302: Database must support read replicas for scaling
- NFR-303: Static assets must be served via CDN

### Usability
- NFR-401: System must be accessible (WCAG 2.1 Level AA)
- NFR-402: System must support mobile devices (responsive design)
- NFR-403: System must support latest versions of Chrome, Firefox, Safari
- NFR-404: Error messages must be user-friendly and actionable

### Maintainability
- NFR-501: Code coverage must be > 80%
- NFR-502: All code must pass linting and static analysis
- NFR-503: API must be versioned for backward compatibility
- NFR-504: System must have comprehensive logging and monitoring
```

#### Requirements Gathering Techniques

**1. Stakeholder Interviews:**
```markdown
## Interview Template

### Stakeholder Information
- Name: [Stakeholder Name]
- Role: [Product Manager / Business Owner / End User]
- Date: [Interview Date]

### Questions
1. What problem are you trying to solve?
2. Who are the primary users?
3. What are the key features needed?
4. What are the success criteria?
5. Are there any constraints (budget, timeline, technology)?
6. What are the must-have vs nice-to-have features?
7. What are the expected user workflows?
8. What are the integration requirements?
9. What are the security/compliance requirements?
10. What is the expected timeline?

### Key Takeaways
[Document insights and requirements]
```

**2. User Stories (Agile Format):**
```markdown
## User Story Template

**As a** [type of user]
**I want** [goal/desire]
**So that** [benefit/value]

### Acceptance Criteria
- [ ] Given [context], when [action], then [expected result]
- [ ] Given [context], when [action], then [expected result]

### Examples

#### User Registration
**As a** new visitor
**I want** to create an account
**So that** I can save my preferences and make purchases

**Acceptance Criteria:**
- [ ] Given I'm on the registration page, when I enter valid email and password, then my account is created
- [ ] Given I'm registering, when I enter an already registered email, then I see "Email already exists" error
- [ ] Given I've registered, when I check my email, then I receive a verification link
- [ ] Given I click the verification link, when I'm redirected to the site, then my account is activated

**Story Points:** 5
**Priority:** High
**Sprint:** Sprint 1

#### Product Search
**As a** customer
**I want** to search for products by name or category
**So that** I can quickly find what I'm looking for

**Acceptance Criteria:**
- [ ] Given I'm on the homepage, when I type in the search box, then I see autocomplete suggestions
- [ ] Given I enter a search term, when I press enter, then I see relevant results
- [ ] Given search results, when I filter by category, then results are filtered accordingly
- [ ] Given no results found, when I search, then I see "No products found" message with suggestions

**Story Points:** 8
**Priority:** High
**Sprint:** Sprint 2
```

**3. Use Cases:**
```markdown
## Use Case: Process Order Payment

**Use Case ID:** UC-001
**Use Case Name:** Process Order Payment
**Actor:** Customer
**Preconditions:** 
- User is logged in
- Shopping cart has items
- User has selected shipping method

**Main Flow:**
1. User clicks "Proceed to Checkout"
2. System displays order summary
3. User selects payment method
4. User enters payment details
5. System validates payment information
6. System processes payment
7. System creates order record
8. System sends confirmation email
9. System displays order confirmation page

**Alternative Flows:**

**3a. Invalid Payment Details**
3a1. System detects invalid card number
3a2. System displays error message
3a3. Return to step 4

**6a. Payment Processing Fails**
6a1. Payment gateway returns error
6a2. System logs error details
6a3. System displays user-friendly error message
6a4. Return to step 4

**6b. Payment Timeout**
6b1. Payment gateway doesn't respond within 30 seconds
6b2. System cancels transaction
6b3. System displays timeout message
6b4. Return to step 3

**Postconditions:**
- Order is created in database
- Payment is processed
- Inventory is updated
- Confirmation email is sent
- User can view order in order history

**Business Rules:**
- BR-001: Payment must be processed within 30 seconds
- BR-002: Failed payments must be logged for audit
- BR-003: Order number must be unique
- BR-004: Inventory must be reserved during checkout
```

**4. Requirements Workshop (Collaborative Sessions):**
```javascript
// Workshop Agenda Template
const workshopAgenda = {
  duration: '3 hours',
  participants: [
    'Product Manager',
    'Tech Lead',
    'UI/UX Designer',
    'Backend Developer',
    'QA Engineer',
    'Business Analyst'
  ],
  agenda: [
    {
      time: '30 min',
      activity: 'Project overview and goals',
      outcome: 'Shared understanding of project vision'
    },
    {
      time: '45 min',
      activity: 'Feature brainstorming',
      outcome: 'List of potential features'
    },
    {
      time: '30 min',
      activity: 'Feature prioritization (MoSCoW)',
      outcome: 'Prioritized feature list'
    },
    {
      time: '45 min',
      activity: 'User workflow mapping',
      outcome: 'Visual user journey maps'
    },
    {
      time: '30 min',
      activity: 'Technical constraints and dependencies',
      outcome: 'Risk and constraint documentation'
    }
  ]
};
```

#### Requirements Prioritization

**MoSCoW Method:**
```markdown
## Feature Prioritization (MoSCoW)

### Must Have (Critical for MVP)
- User authentication (login/register)
- Product catalog and search
- Shopping cart functionality
- Checkout and payment processing
- Order confirmation

### Should Have (Important but not critical)
- User profile management
- Order history
- Product reviews and ratings
- Wishlist functionality
- Email notifications

### Could Have (Nice to have if time permits)
- Social media login
- Product recommendations
- Live chat support
- Loyalty points program
- Advanced filters and sorting

### Won't Have (Out of scope for this release)
- Mobile app
- Multi-language support
- Cryptocurrency payment
- AR product preview
- Subscription model
```

**RICE Scoring:**
```javascript
// RICE = (Reach × Impact × Confidence) / Effort

const features = [
  {
    name: 'User Authentication',
    reach: 1000,      // Users affected per month
    impact: 3,        // 0.25=minimal, 0.5=low, 1=medium, 2=high, 3=massive
    confidence: 100,  // Percentage
    effort: 2,        // Person-months
    rice: null
  },
  {
    name: 'Product Search',
    reach: 800,
    impact: 3,
    confidence: 80,
    effort: 3,
    rice: null
  },
  {
    name: 'Social Login',
    reach: 500,
    impact: 1,
    confidence: 50,
    effort: 2,
    rice: null
  }
];

// Calculate RICE scores
features.forEach(feature => {
  feature.rice = (feature.reach * feature.impact * (feature.confidence / 100)) / feature.effort;
});

// Sort by RICE score (highest priority first)
features.sort((a, b) => b.rice - a.rice);

console.log(features);
/*
Output:
[
  { name: 'User Authentication', rice: 1500 },
  { name: 'Product Search', rice: 640 },
  { name: 'Social Login', rice: 125 }
]
*/
```

#### Requirements Documentation

**Software Requirements Specification (SRS) Template:**
```markdown
# Software Requirements Specification (SRS)

## 1. Introduction

### 1.1 Purpose
This document specifies the requirements for the E-Commerce Platform.

### 1.2 Scope
The system will provide online shopping capabilities including product browsing, 
cart management, checkout, and order tracking.

### 1.3 Definitions, Acronyms, and Abbreviations
- API: Application Programming Interface
- SLA: Service Level Agreement
- MVP: Minimum Viable Product
- NFR: Non-Functional Requirement

### 1.4 References
- [Market Research Document]
- [Competitor Analysis]
- [Technical Architecture Document]

## 2. Overall Description

### 2.1 Product Perspective
Cloud-based e-commerce platform accessible via web browsers.

### 2.2 Product Functions
- User management
- Product catalog
- Shopping cart
- Payment processing
- Order management

### 2.3 User Classes and Characteristics
- **Customers**: End users who browse and purchase products
- **Administrators**: Manage products, orders, and users
- **Support Staff**: Handle customer inquiries

### 2.4 Operating Environment
- Supported Browsers: Chrome 90+, Firefox 88+, Safari 14+
- Mobile: iOS 14+, Android 11+
- Server: Node.js 18+, PostgreSQL 14+

### 2.5 Design and Implementation Constraints
- Must use existing payment gateway (Stripe)
- Must comply with PCI DSS for payment data
- Must support 10,000 concurrent users
- Must be deployed on AWS

## 3. Functional Requirements

[Detailed functional requirements with IDs]

## 4. Non-Functional Requirements

[Detailed non-functional requirements]

## 5. External Interface Requirements

### 5.1 User Interfaces
[UI mockups and descriptions]

### 5.2 Hardware Interfaces
[If applicable]

### 5.3 Software Interfaces
- Payment Gateway: Stripe API v2
- Email Service: SendGrid API v3
- Analytics: Google Analytics

### 5.4 Communication Interfaces
- HTTPS for all communications
- WebSocket for real-time notifications
- REST API for client-server communication

## 6. Other Requirements

### 6.1 Security Requirements
[Detailed security requirements]

### 6.2 Business Rules
[Business logic and rules]

## Appendix A: Data Dictionary
[Database schema and data definitions]

## Appendix B: Analysis Models
[ER diagrams, flowcharts, etc.]
```

### Cost Estimation

#### Estimation Techniques

**1. Story Points (Fibonacci Sequence):**
```javascript
// Story point scale: 1, 2, 3, 5, 8, 13, 21
const storyPointScale = {
  1: 'Very simple, well understood, no risk',
  2: 'Simple, mostly understood, minimal risk',
  3: 'Moderate complexity, some unknowns',
  5: 'Complex, multiple components, some risk',
  8: 'Very complex, significant unknowns',
  13: 'Extremely complex, high risk',
  21: 'Epic - needs to be broken down'
};

const features = [
  { name: 'User login', points: 3 },
  { name: 'Password reset', points: 2 },
  { name: 'User profile', points: 5 },
  { name: 'Product search', points: 8 },
  { name: 'Payment integration', points: 13 },
  { name: 'Order tracking', points: 5 },
  { name: 'Admin dashboard', points: 21 } // Should be broken down
];

const totalPoints = features.reduce((sum, f) => sum + f.points, 0);
console.log(`Total Story Points: ${totalPoints}`);

// Velocity calculation (points completed per sprint)
const sprintVelocity = 20; // Average points per 2-week sprint
const estimatedSprints = Math.ceil(totalPoints / sprintVelocity);
console.log(`Estimated Sprints: ${estimatedSprints}`);
console.log(`Estimated Timeline: ${estimatedSprints * 2} weeks`);
```

**2. T-Shirt Sizing:**
```javascript
const tShirtSizing = {
  XS: { hours: 4, description: 'Extra Small - Quick fix or simple change' },
  S: { hours: 16, description: 'Small - 2 days of work' },
  M: { hours: 40, description: 'Medium - 1 week of work' },
  L: { hours: 80, description: 'Large - 2 weeks of work' },
  XL: { hours: 160, description: 'Extra Large - 1 month of work' },
  XXL: { hours: 320, description: 'Extra Extra Large - Needs breakdown' }
};

const projectFeatures = [
  { feature: 'User Authentication', size: 'M' },
  { feature: 'Product Catalog', size: 'L' },
  { feature: 'Shopping Cart', size: 'M' },
  { feature: 'Payment Processing', size: 'L' },
  { feature: 'Order Management', size: 'L' },
  { feature: 'Admin Panel', size: 'XL' },
  { feature: 'Email Notifications', size: 'S' },
  { feature: 'Search Functionality', size: 'L' }
];

const totalHours = projectFeatures.reduce((sum, f) => {
  return sum + tShirtSizing[f.size].hours;
}, 0);

console.log(`Total Estimated Hours: ${totalHours}`);
console.log(`Total Estimated Days: ${totalHours / 8}`);
console.log(`Total Estimated Weeks: ${totalHours / 40}`);
```

**3. Three-Point Estimation:**
```javascript
// Estimate: (Optimistic + 4×Realistic + Pessimistic) / 6

function threePointEstimate(optimistic, realistic, pessimistic) {
  const estimate = (optimistic + (4 * realistic) + pessimistic) / 6;
  const standardDeviation = (pessimistic - optimistic) / 6;
  
  return {
    estimate: Math.round(estimate),
    stdDev: Math.round(standardDeviation * 10) / 10,
    range: {
      min: Math.round(estimate - standardDeviation),
      max: Math.round(estimate + standardDeviation)
    }
  };
}

const tasks = [
  {
    name: 'User Authentication',
    optimistic: 20,   // Best case (hours)
    realistic: 40,    // Most likely
    pessimistic: 80   // Worst case
  },
  {
    name: 'Payment Integration',
    optimistic: 40,
    realistic: 80,
    pessimistic: 160
  },
  {
    name: 'Product Search',
    optimistic: 30,
    realistic: 60,
    pessimistic: 100
  }
];

tasks.forEach(task => {
  const estimate = threePointEstimate(
    task.optimistic,
    task.realistic,
    task.pessimistic
  );
  
  console.log(`${task.name}:`);
  console.log(`  Estimate: ${estimate.estimate} hours`);
  console.log(`  Range: ${estimate.range.min} - ${estimate.range.max} hours`);
  console.log(`  Std Dev: ±${estimate.stdDev} hours`);
});
```

**4. Analogous Estimation (Historical Data):**
```javascript
// Use data from similar past projects
const historicalProjects = [
  { name: 'Project A', features: 15, actualHours: 600 },
  { name: 'Project B', features: 20, actualHours: 800 },
  { name: 'Project C', features: 12, actualHours: 500 }
];

// Calculate average hours per feature
const avgHoursPerFeature = historicalProjects.reduce((sum, p) => {
  return sum + (p.actualHours / p.features);
}, 0) / historicalProjects.length;

// Estimate new project
const newProject = {
  name: 'E-Commerce Platform',
  features: 18
};

const estimatedHours = newProject.features * avgHoursPerFeature;
const estimatedWeeks = Math.ceil(estimatedHours / 40);

console.log(`Estimated Hours: ${Math.round(estimatedHours)}`);
console.log(`Estimated Weeks: ${estimatedWeeks}`);
```

#### Resource Planning

```javascript
// Team composition and cost calculation
const team = [
  { role: 'Senior Full-Stack Developer', count: 2, hourlyRate: 100 },
  { role: 'Junior Developer', count: 2, hourlyRate: 60 },
  { role: 'UI/UX Designer', count: 1, hourlyRate: 80 },
  { role: 'QA Engineer', count: 1, hourlyRate: 70 },
  { role: 'DevOps Engineer', count: 1, hourlyRate: 90 },
  { role: 'Project Manager', count: 1, hourlyRate: 85 },
  { role: 'Business Analyst', count: 1, hourlyRate: 75 }
];

const projectDuration = {
  weeks: 20,
  hoursPerWeek: 40
};

// Calculate total cost
const totalCost = team.reduce((sum, member) => {
  const memberCost = member.count * 
                     member.hourlyRate * 
                     projectDuration.hoursPerWeek * 
                     projectDuration.weeks;
  return sum + memberCost;
}, 0);

console.log(`Total Labor Cost: $${totalCost.toLocaleString()}`);

// Breakdown by role
team.forEach(member => {
  const roleCost = member.count * 
                   member.hourlyRate * 
                   projectDuration.hoursPerWeek * 
                   projectDuration.weeks;
  console.log(`${member.role}: $${roleCost.toLocaleString()}`);
});
```

#### Complete Project Budget

```javascript
const projectBudget = {
  // Development costs
  labor: {
    developers: 320000,    // 4 developers × 20 weeks
    designer: 64000,       // 1 designer × 20 weeks
    qa: 56000,            // 1 QA × 20 weeks
    devops: 72000,        // 1 DevOps × 20 weeks
    projectManager: 68000, // 1 PM × 20 weeks
    businessAnalyst: 60000 // 1 BA × 20 weeks
  },
  
  // Infrastructure costs
  infrastructure: {
    aws: 5000,              // Cloud hosting (20 weeks)
    domains: 200,           // Domain registration
    ssl: 500,              // SSL certificates
    cdn: 1000,             // CDN service
    monitoring: 800,       // Monitoring tools
    cicd: 1200             // CI/CD tools
  },
  
  // Software licenses
  licenses: {
    development: 5000,     // IDEs, tools
    design: 2000,         // Design software
    projectManagement: 1500, // Jira, etc.
    testing: 2500         // Testing tools
  },
  
  // Third-party services
  services: {
    paymentGateway: 3000,  // Stripe/PayPal setup
    emailService: 1000,    // SendGrid
    sms: 500,             // Twilio
    analytics: 1000,      // Analytics tools
    errorTracking: 800    // Sentry
  },
  
  // Contingency
  contingency: 0,  // Will calculate as percentage
  
  calculateTotal() {
    const labor = Object.values(this.labor).reduce((a, b) => a + b, 0);
    const infra = Object.values(this.infrastructure).reduce((a, b) => a + b, 0);
    const licenses = Object.values(this.licenses).reduce((a, b) => a + b, 0);
    const services = Object.values(this.services).reduce((a, b) => a + b, 0);
    
    const subtotal = labor + infra + licenses + services;
    this.contingency = subtotal * 0.15; // 15% contingency
    
    return {
      labor,
      infrastructure: infra,
      licenses,
      services,
      subtotal,
      contingency: this.contingency,
      total: subtotal + this.contingency
    };
  }
};

const budget = projectBudget.calculateTotal();

console.log('Project Budget Breakdown:');
console.log('========================');
console.log(`Labor: $${budget.labor.toLocaleString()}`);
console.log(`Infrastructure: $${budget.infrastructure.toLocaleString()}`);
console.log(`Licenses: $${budget.licenses.toLocaleString()}`);
console.log(`Services: $${budget.services.toLocaleString()}`);
console.log(`Subtotal: $${budget.subtotal.toLocaleString()}`);
console.log(`Contingency (15%): $${budget.contingency.toLocaleString()}`);
console.log('========================');
console.log(`TOTAL: $${budget.total.toLocaleString()}`);
```

#### ROI Calculation

```javascript
// Return on Investment calculation
const roiCalculation = {
  // Costs
  developmentCost: 640000,
  operationalCostPerYear: 120000,
  
  // Revenue projections
  expectedUsers: {
    year1: 5000,
    year2: 15000,
    year3: 30000
  },
  
  avgRevenuePerUser: 500,
  conversionRate: 0.02, // 2%
  
  calculateROI(years = 3) {
    let totalRevenue = 0;
    let totalCost = this.developmentCost;
    
    for (let year = 1; year <= years; year++) {
      const users = this.expectedUsers[`year${year}`];
      const convertedUsers = users * this.conversionRate;
      const yearRevenue = convertedUsers * this.avgRevenuePerUser;
      
      totalRevenue += yearRevenue;
      totalCost += this.operationalCostPerYear;
      
      console.log(`Year ${year}:`);
      console.log(`  Users: ${users.toLocaleString()}`);
      console.log(`  Converted: ${convertedUsers.toLocaleString()}`);
      console.log(`  Revenue: $${yearRevenue.toLocaleString()}`);
    }
    
    const netProfit = totalRevenue - totalCost;
    const roi = (netProfit / totalCost) * 100;
    const breakEvenPoint = totalCost / (totalRevenue / years);
    
    return {
      totalRevenue,
      totalCost,
      netProfit,
      roi: roi.toFixed(2),
      breakEvenYears: breakEvenPoint.toFixed(2)
    };
  }
};

const roi = roiCalculation.calculateROI(3);

console.log('\nROI Summary (3 years):');
console.log('=====================');
console.log(`Total Revenue: $${roi.totalRevenue.toLocaleString()}`);
console.log(`Total Cost: $${roi.totalCost.toLocaleString()}`);
console.log(`Net Profit: $${roi.netProfit.toLocaleString()}`);
console.log(`ROI: ${roi.roi}%`);
console.log(`Break-even: ${roi.breakEvenYears} years`);
```

#### Risk Assessment & Contingency

```javascript
const riskAssessment = [
  {
    risk: 'Technical complexity underestimated',
    probability: 'High',
    impact: 'High',
    mitigation: 'Add 20% buffer to estimates',
    contingency: 128000
  },
  {
    risk: 'Third-party API changes',
    probability: 'Medium',
    impact: 'Medium',
    mitigation: 'Build abstraction layer',
    contingency: 32000
  },
  {
    risk: 'Key team member leaves',
    probability: 'Low',
    impact: 'High',
    mitigation: 'Knowledge documentation, pair programming',
    contingency: 64000
  },
  {
    risk: 'Scope creep',
    probability: 'High',
    impact: 'High',
    mitigation: 'Strict change management process',
    contingency: 96000
  },
  {
    risk: 'Performance issues at scale',
    probability: 'Medium',
    impact: 'High',
    mitigation: 'Load testing, performance budgets',
    contingency: 48000
  }
];

const totalRiskContingency = riskAssessment.reduce((sum, r) => sum + r.contingency, 0);

console.log('Risk Assessment:');
console.log('===============');
riskAssessment.forEach((risk, index) => {
  console.log(`\n${index + 1}. ${risk.risk}`);
  console.log(`   Probability: ${risk.probability}`);
  console.log(`   Impact: ${risk.impact}`);
  console.log(`   Mitigation: ${risk.mitigation}`);
  console.log(`   Contingency: $${risk.contingency.toLocaleString()}`);
});
console.log(`\nTotal Risk Contingency: $${totalRiskContingency.toLocaleString()}`);
```

#### Estimation Best Practices

**1. Break Down Complex Tasks:**
```javascript
// ❌ Bad: Too large to estimate accurately
const epicTask = {
  name: 'Build entire e-commerce platform',
  estimate: '6 months' // Too vague
};

// ✅ Good: Broken down into estimable chunks
const detailedTasks = [
  { name: 'User authentication system', estimate: 40, unit: 'hours' },
  { name: 'Product catalog API', estimate: 80, unit: 'hours' },
  { name: 'Shopping cart functionality', estimate: 60, unit: 'hours' },
  { name: 'Payment integration', estimate: 100, unit: 'hours' },
  { name: 'Order management system', estimate: 80, unit: 'hours' },
  { name: 'Admin dashboard', estimate: 120, unit: 'hours' },
  { name: 'Email notification system', estimate: 40, unit: 'hours' },
  { name: 'Search and filtering', estimate: 80, unit: 'hours' },
  { name: 'User profile management', estimate: 60, unit: 'hours' },
  { name: 'Testing and QA', estimate: 200, unit: 'hours' }
];

const totalHours = detailedTasks.reduce((sum, task) => sum + task.estimate, 0);
console.log(`Total Estimate: ${totalHours} hours (${totalHours / 160} months)`);
```

**2. Include Non-Development Time:**
```javascript
const completeEstimate = {
  development: 800,        // Actual coding
  codeReview: 80,         // 10% of development
  testing: 160,           // 20% of development
  bugFixing: 120,         // 15% of development
  documentation: 80,      // 10% of development
  meetings: 120,          // Standups, planning, retrospectives
  deployment: 40,         // CI/CD setup, production deployment
  bufferTime: 200,        // 15% buffer for unknowns
  
  getTotal() {
    return Object.values(this).reduce((sum, val) => {
      return typeof val === 'number' ? sum + val : sum;
    }, 0);
  }
};

console.log(`Total Project Hours: ${completeEstimate.getTotal()}`);
```

**3. Track Actual vs Estimated:**
```javascript
// Use for future estimation improvements
const projectTracking = {
  tasks: [
    { name: 'User Auth', estimated: 40, actual: 55, variance: '+37%' },
    { name: 'Product API', estimated: 80, actual: 75, variance: '-6%' },
    { name: 'Shopping Cart', estimated: 60, actual: 70, variance: '+17%' }
  ],
  
  getAccuracy() {
    const totalEstimated = this.tasks.reduce((sum, t) => sum + t.estimated, 0);
    const totalActual = this.tasks.reduce((sum, t) => sum + t.actual, 0);
    const accuracy = (totalEstimated / totalActual) * 100;
    
    return {
      totalEstimated,
      totalActual,
      accuracy: accuracy.toFixed(2) + '%',
      overUnder: totalActual > totalEstimated ? 'over' : 'under'
    };
  }
};

console.log('Estimation Accuracy:', projectTracking.getAccuracy());
```

#### Estimation Checklist

```markdown
## Before Estimating
- [ ] Requirements are clear and documented
- [ ] Technical approach is defined
- [ ] Dependencies are identified
- [ ] Team composition is known
- [ ] Historical data is available (if applicable)

## During Estimation
- [ ] Break down into small, estimable tasks
- [ ] Involve the entire team in estimation
- [ ] Consider all phases (dev, test, deploy, docs)
- [ ] Account for meetings and overhead
- [ ] Include buffer for unknowns (15-20%)
- [ ] Identify risks and add contingency
- [ ] Document assumptions

## After Estimation
- [ ] Review estimates with stakeholders
- [ ] Get buy-in from the team
- [ ] Document estimation rationale
- [ ] Set up tracking mechanism
- [ ] Plan regular estimate reviews
- [ ] Be prepared to re-estimate when needed
```

---

## Agile Methodology

### What is Agile?

Agile is an iterative approach to software development that emphasizes:
- **Flexibility**: Adapting to changing requirements
- **Collaboration**: Close teamwork and communication
- **Customer Focus**: Continuous delivery of value
- **Iteration**: Regular releases with incremental improvements
- **Feedback**: Continuous learning and improvement

```
Traditional Waterfall:
Requirements → Design → Development → Testing → Deployment
(6-12 months)

Agile Approach:
┌─────────────────────────────────┐
│  Sprint 1 (2 weeks)             │
│  Plan → Build → Test → Review   │ ──→ Working Software
└─────────────────────────────────┘
┌─────────────────────────────────┐
│  Sprint 2 (2 weeks)             │
│  Plan → Build → Test → Review   │ ──→ Working Software
└─────────────────────────────────┘
┌─────────────────────────────────┐
│  Sprint 3 (2 weeks)             │
│  Plan → Build → Test → Review   │ ──→ Working Software
└─────────────────────────────────┘
```

### Agile Manifesto

**Core Values:**
1. **Individuals and interactions** over processes and tools
2. **Working software** over comprehensive documentation
3. **Customer collaboration** over contract negotiation
4. **Responding to change** over following a plan

**12 Principles:**
```markdown
1. Customer satisfaction through early and continuous delivery
2. Welcome changing requirements, even late in development
3. Deliver working software frequently (weeks rather than months)
4. Business people and developers work together daily
5. Build projects around motivated individuals
6. Face-to-face conversation is the best form of communication
7. Working software is the primary measure of progress
8. Sustainable development pace
9. Continuous attention to technical excellence
10. Simplicity—maximizing work not done
11. Self-organizing teams
12. Regular reflection and adaptation
```

### Scrum Framework

#### Scrum Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        Product Backlog                       │
│  1. User Authentication (Priority: High)                     │
│  2. Product Catalog (Priority: High)                         │
│  3. Shopping Cart (Priority: Medium)                         │
│  ...                                                         │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
                    ┌────────────────┐
                    │ Sprint Planning│
                    └────────┬───────┘
                             │
                             ▼
┌────────────────────────────────────────────────────────────┐
│                     Sprint Backlog                          │
│  Sprint Goal: Implement user authentication                 │
│  - User registration                                        │
│  - User login                                              │
│  - Password reset                                          │
└────────────────────────────┬───────────────────────────────┘
                             │
                ┌────────────┴────────────┐
                │    Sprint (2 weeks)     │
                │  ┌─────────────────┐   │
                │  │  Daily Standup  │   │
                │  └─────────────────┘   │
                │  Development & Testing  │
                └────────────┬────────────┘
                             │
                ┌────────────┴────────────┐
                │                         │
                ▼                         ▼
        ┌──────────────┐        ┌─────────────────┐
        │    Sprint    │        │     Sprint      │
        │    Review    │        │  Retrospective  │
        └──────┬───────┘        └────────┬────────┘
               │                         │
               └────────────┬────────────┘
                            │
                            ▼
                    ┌───────────────┐
                    │   Increment   │
                    │  (Potentially │
                    │  Releasable)  │
                    └───────────────┘
```

#### Scrum Roles

**1. Product Owner:**
```javascript
const productOwnerResponsibilities = {
  primary: "Maximize value of product and work of Development Team",
  
  responsibilities: [
    'Define product vision and roadmap',
    'Manage and prioritize product backlog',
    'Write user stories and acceptance criteria',
    'Make decisions on features and scope',
    'Accept or reject work results',
    'Engage with stakeholders',
    'Ensure team understands backlog items'
  ],
  
  keySkills: [
    'Business domain knowledge',
    'Stakeholder management',
    'Decision making',
    'Communication',
    'Prioritization'
  ],
  
  // Example: User Story Creation
  createUserStory(feature) {
    return {
      id: 'US-101',
      title: feature.name,
      description: `As a ${feature.userType}, I want ${feature.goal}, so that ${feature.benefit}`,
      acceptanceCriteria: [
        'Given [context], when [action], then [result]',
        'Given [context], when [action], then [result]'
      ],
      priority: feature.priority, // High, Medium, Low
      storyPoints: null, // Set by team
      status: 'Backlog'
    };
  }
};
```

**2. Scrum Master:**
```javascript
const scrumMasterResponsibilities = {
  primary: "Ensure Scrum is understood and enacted",
  
  responsibilities: [
    'Facilitate Scrum events (planning, standup, review, retro)',
    'Remove impediments blocking the team',
    'Shield team from external interruptions',
    'Coach team on Scrum practices',
    'Help Product Owner with backlog management',
    'Foster self-organization',
    'Promote continuous improvement'
  ],
  
  // Example: Impediment Tracking
  impediments: [
    {
      id: 'IMP-001',
      description: 'Blocked by API dependency from another team',
      reportedBy: 'Developer',
      reportedDate: '2026-01-20',
      status: 'In Progress',
      resolution: 'Coordinating with other team\'s Scrum Master',
      priority: 'High'
    },
    {
      id: 'IMP-002',
      description: 'Need access to staging environment',
      reportedBy: 'QA',
      reportedDate: '2026-01-21',
      status: 'Resolved',
      resolution: 'Access granted by DevOps',
      priority: 'Medium'
    }
  ]
};
```

**3. Development Team:**
```javascript
const developmentTeam = {
  characteristics: [
    'Self-organizing',
    'Cross-functional',
    'No sub-teams or hierarchies',
    '3-9 members optimal',
    'Collectively responsible for deliverables'
  ],
  
  responsibilities: [
    'Estimate story points',
    'Commit to sprint goals',
    'Develop working software',
    'Ensure quality (testing, code review)',
    'Collaborate daily',
    'Participate in all Scrum events'
  ],
  
  // Example team composition
  members: [
    { role: 'Senior Full-Stack Developer', count: 2 },
    { role: 'Junior Developer', count: 2 },
    { role: 'QA Engineer', count: 1 },
    { role: 'UI/UX Designer', count: 1 }
  ]
};
```

#### Scrum Artifacts

**1. Product Backlog:**
```javascript
const productBacklog = [
  {
    id: 'US-001',
    type: 'User Story',
    title: 'User Registration',
    description: 'As a new user, I want to create an account',
    priority: 1,
    storyPoints: 8,
    status: 'Ready',
    dependencies: []
  },
  {
    id: 'US-002',
    type: 'User Story',
    title: 'User Login',
    description: 'As a registered user, I want to login',
    priority: 2,
    storyPoints: 5,
    status: 'Ready',
    dependencies: ['US-001']
  },
  {
    id: 'BUG-001',
    type: 'Bug',
    title: 'Fix password reset email',
    description: 'Password reset emails not being sent',
    priority: 1,
    storyPoints: 3,
    status: 'Ready',
    severity: 'High'
  },
  {
    id: 'TECH-001',
    type: 'Technical Debt',
    title: 'Refactor authentication module',
    description: 'Improve code quality and test coverage',
    priority: 3,
    storyPoints: 13,
    status: 'Backlog'
  }
];

// Backlog Refinement
function refineBacklog(backlog) {
  return backlog
    .filter(item => item.status !== 'Done')
    .sort((a, b) => a.priority - b.priority)
    .map(item => {
      // Break down large items
      if (item.storyPoints > 13) {
        return breakDownStory(item);
      }
      return item;
    });
}
```

**2. Sprint Backlog:**
```javascript
const sprintBacklog = {
  sprintNumber: 5,
  sprintGoal: 'Complete user authentication functionality',
  startDate: '2026-01-22',
  endDate: '2026-02-04',
  
  stories: [
    {
      id: 'US-001',
      title: 'User Registration',
      storyPoints: 8,
      tasks: [
        { id: 'T-001', description: 'Create registration API endpoint', hours: 8, assignee: 'John', status: 'Done' },
        { id: 'T-002', description: 'Implement email validation', hours: 4, assignee: 'John', status: 'Done' },
        { id: 'T-003', description: 'Create registration UI form', hours: 8, assignee: 'Sarah', status: 'In Progress' },
        { id: 'T-004', description: 'Write unit tests', hours: 6, assignee: 'Mike', status: 'To Do' },
        { id: 'T-005', description: 'Integration testing', hours: 4, assignee: 'QA', status: 'To Do' }
      ]
    },
    {
      id: 'US-002',
      title: 'User Login',
      storyPoints: 5,
      tasks: [
        { id: 'T-006', description: 'Create login API endpoint', hours: 6, assignee: 'John', status: 'To Do' },
        { id: 'T-007', description: 'JWT token implementation', hours: 8, assignee: 'John', status: 'To Do' },
        { id: 'T-008', description: 'Create login UI', hours: 6, assignee: 'Sarah', status: 'To Do' },
        { id: 'T-009', description: 'Write tests', hours: 4, assignee: 'Mike', status: 'To Do' }
      ]
    }
  ],
  
  // Sprint Burndown
  burndown: [
    { day: 1, remainingHours: 54 },
    { day: 2, remainingHours: 50 },
    { day: 3, remainingHours: 42 },
    { day: 4, remainingHours: 38 },
    { day: 5, remainingHours: 35 }
    // ... continues
  ]
};
```

**3. Increment:**
```javascript
const increment = {
  definition: "Sum of all Product Backlog items completed during Sprint",
  mustMeet: "Definition of Done",
  
  definitionOfDone: [
    'Code is written and follows coding standards',
    'Unit tests written with >80% coverage',
    'Code reviewed and approved',
    'Integration tests pass',
    'Documentation updated',
    'No critical bugs',
    'Deployed to staging environment',
    'Product Owner acceptance'
  ],
  
  // Example increment
  sprint5Increment: {
    completedStories: ['US-001', 'US-002', 'BUG-001'],
    features: [
      'User registration with email verification',
      'User login with JWT authentication',
      'Password reset functionality'
    ],
    deployedTo: 'staging',
    readyForProduction: true,
    releaseNotes: 'Version 1.2.0 - User Authentication Complete'
  }
};
```

#### Scrum Events (Ceremonies)

**1. Sprint Planning:**
```javascript
const sprintPlanning = {
  duration: '4 hours for 2-week sprint',
  participants: ['Product Owner', 'Scrum Master', 'Development Team'],
  
  agenda: {
    part1_whatCanBeDone: {
      duration: '2 hours',
      activities: [
        'Product Owner presents ordered backlog',
        'Team discusses and asks questions',
        'Team forecasts what can be completed',
        'Team creates sprint goal'
      ]
    },
    part2_howToDoIt: {
      duration: '2 hours',
      activities: [
        'Team breaks down stories into tasks',
        'Team estimates tasks in hours',
        'Team self-assigns tasks',
        'Team validates sprint commitment'
      ]
    }
  },
  
  output: {
    sprintGoal: 'Complete user authentication functionality',
    sprintBacklog: ['US-001', 'US-002', 'BUG-001'],
    teamCommitment: 'We commit to delivering these items'
  }
};

// Sprint Planning Meeting Template
const planningTemplate = `
SPRINT PLANNING MEETING

Date: ${new Date().toISOString().split('T')[0]}
Sprint: Sprint 5
Duration: 2 weeks (Jan 22 - Feb 4)

PART 1: WHAT (2 hours)
Sprint Goal: Complete user authentication

Selected Stories:
1. US-001: User Registration (8 pts)
2. US-002: User Login (5 pts)
3. BUG-001: Fix password reset (3 pts)

Total: 16 story points
Team Velocity: 15-18 points/sprint
Capacity: 6 team members × 8 hours × 10 days = 480 hours
Minus: Meetings (40h), buffer (40h) = 400 available hours

PART 2: HOW (2 hours)
[Tasks created and estimated]

COMMITMENTS:
- Team commits to sprint goal
- Product Owner available for questions
- No scope changes during sprint
`;
```

**2. Daily Standup (Daily Scrum):**
```javascript
const dailyStandup = {
  duration: '15 minutes',
  time: '9:00 AM daily',
  location: 'In front of task board / Video call',
  participants: ['Development Team', 'Scrum Master (facilitates)'],
  
  threeQuestions: [
    'What did I do yesterday that helped meet the sprint goal?',
    'What will I do today to help meet the sprint goal?',
    'Do I see any impediments preventing me or the team from meeting the sprint goal?'
  ],
  
  // Example standup
  example: {
    john: {
      yesterday: 'Completed registration API endpoint and email validation',
      today: 'Start working on login API endpoint',
      blockers: 'None'
    },
    sarah: {
      yesterday: 'Started registration UI form',
      today: 'Complete registration form and add validation',
      blockers: 'Waiting for design assets from UX team'
    },
    mike: {
      yesterday: 'Reviewed code and wrote unit tests for API',
      today: 'Continue with unit tests for UI components',
      blockers: 'None'
    }
  },
  
  // Anti-patterns to avoid
  antiPatterns: [
    '❌ Turning into status reporting to manager',
    '❌ Problem-solving discussions (take offline)',
    '❌ Going over 15 minutes',
    '❌ Only addressing Scrum Master',
    '❌ Detailed technical discussions'
  ]
};
```

**3. Sprint Review:**
```javascript
const sprintReview = {
  duration: '2 hours for 2-week sprint',
  participants: ['Product Owner', 'Scrum Master', 'Development Team', 'Stakeholders'],
  
  agenda: [
    'Product Owner explains what was Done vs Not Done',
    'Development Team demonstrates working software',
    'Stakeholders provide feedback',
    'Product Owner discusses current product backlog',
    'Group discusses what to do next',
    'Review timeline, budget, capabilities for next release'
  ],
  
  // Demo checklist
  demoChecklist: [
    '✓ Prepare demo environment',
    '✓ Test all features before demo',
    '✓ Prepare demo script',
    '✓ Show actual working software (not slides)',
    '✓ Focus on user value, not technical details',
    '✓ Collect feedback from stakeholders',
    '✓ Update product backlog based on feedback'
  ],
  
  output: {
    demonstratedFeatures: [
      'User can register with email',
      'Email verification working',
      'User can login and receive JWT token',
      'Session management implemented'
    ],
    feedback: [
      'Add social login options',
      'Improve password strength requirements',
      'Add "Remember me" functionality'
    ],
    backlogUpdates: [
      'New story: Social login integration',
      'Enhanced story: Improve password validation',
      'New story: Remember me feature'
    ]
  }
};
```

**4. Sprint Retrospective:**
```javascript
const sprintRetrospective = {
  duration: '1.5 hours for 2-week sprint',
  participants: ['Development Team', 'Scrum Master', 'Product Owner (optional)'],
  purpose: 'Inspect how last sprint went and create plan for improvements',
  
  format: 'Start-Stop-Continue',
  
  discussion: {
    start: [
      'Start pair programming for complex features',
      'Start automated deployment to staging',
      'Start weekly backlog refinement sessions'
    ],
    stop: [
      'Stop accepting unclear user stories into sprint',
      'Stop context switching between multiple stories',
      'Stop skipping code reviews due to time pressure'
    ],
    continue: [
      'Continue daily standups at 9 AM',
      'Continue thorough testing before demo',
      'Continue knowledge sharing sessions'
    ]
  },
  
  // Alternative formats
  alternativeFormats: {
    madSadGlad: {
      mad: 'Things that frustrate us',
      sad: 'Things that disappoint us',
      glad: 'Things that make us happy'
    },
    
    fourLs: {
      liked: 'What went well',
      learned: 'What we learned',
      lacked: 'What was missing',
      longedFor: 'What we wish we had'
    },
    
    sailboat: {
      wind: 'What helps us move forward',
      anchor: 'What slows us down',
      rocks: 'What are the risks',
      island: 'Where we want to go'
    }
  },
  
  actionItems: [
    {
      id: 'AI-001',
      action: 'Set up automated deployment pipeline',
      owner: 'DevOps team',
      deadline: 'Next sprint',
      status: 'In Progress'
    },
    {
      id: 'AI-002',
      action: 'Create Definition of Ready checklist',
      owner: 'Product Owner + Team',
      deadline: 'Before next planning',
      status: 'To Do'
    }
  ]
};
```

### Kanban Framework

#### Kanban Board

```
┌───────────┬───────────┬───────────┬───────────┬───────────┐
│  Backlog  │   To Do   │ In Progr. │  Review   │   Done    │
│           │           │           │           │           │
│  [Story]  │  [Task]   │  [Task]   │  [Task]   │  [Task]   │
│  [Story]  │  [Task]   │  [Task]   │           │  [Task]   │
│  [Story]  │  [Task]   │  [Task]   │           │  [Task]   │
│  [Bug]    │           │           │           │  [Task]   │
│           │           │           │           │  [Task]   │
│ WIP: ∞    │ WIP: 5    │ WIP: 3    │ WIP: 2    │ WIP: ∞    │
└───────────┴───────────┴───────────┴───────────┴───────────┘

WIP = Work In Progress Limits
```

#### Kanban Principles

```javascript
const kanbanPrinciples = {
  core: [
    'Visualize workflow',
    'Limit work in progress (WIP)',
    'Manage flow',
    'Make policies explicit',
    'Implement feedback loops',
    'Improve collaboratively, evolve experimentally'
  ],
  
  // Example Kanban implementation
  board: {
    columns: [
      { name: 'Backlog', wipLimit: null },
      { name: 'Selected', wipLimit: 10 },
      { name: 'In Development', wipLimit: 3 },
      { name: 'Code Review', wipLimit: 2 },
      { name: 'Testing', wipLimit: 2 },
      { name: 'Done', wipLimit: null }
    ],
    
    cards: [
      {
        id: 'CARD-001',
        title: 'User Authentication',
        type: 'Feature',
        priority: 'High',
        assignee: 'John',
        column: 'In Development',
        blockers: [],
        age: 3 // days in current column
      }
    ]
  },
  
  // Key metrics
  metrics: {
    cycleTime: 'Time from start to finish',
    leadTime: 'Time from request to delivery',
    throughput: 'Items completed per time period',
    wipAge: 'How long items stay in progress'
  }
};
```

### Scrum vs Kanban

```javascript
const comparison = {
  scrum: {
    cadence: 'Fixed length sprints (1-4 weeks)',
    roles: 'Product Owner, Scrum Master, Dev Team',
    changes: 'No changes during sprint',
    commitment: 'Team commits to sprint goal',
    metrics: 'Velocity (story points per sprint)',
    boards: 'Reset after each sprint',
    bestFor: 'Teams starting with Agile, complex projects with clear increments'
  },
  
  kanban: {
    cadence: 'Continuous flow',
    roles: 'No prescribed roles',
    changes: 'Changes can be made anytime',
    commitment: 'No specific commitment',
    metrics: 'Cycle time, lead time, throughput',
    boards: 'Persistent board',
    bestFor: 'Support/maintenance teams, continuous delivery, operations'
  },
  
  scrumban: {
    description: 'Hybrid approach combining Scrum and Kanban',
    features: [
      'Scrum events (planning, review, retro)',
      'Kanban board with WIP limits',
      'Pull system instead of sprint commitment',
      'Continuous flow within sprint timeboxes'
    ]
  }
};
```

### User Stories & Acceptance Criteria

```javascript
// INVEST Criteria for User Stories
const investCriteria = {
  I: 'Independent - Can be developed in any order',
  N: 'Negotiable - Details can be discussed',
  V: 'Valuable - Provides value to user/business',
  E: 'Estimable - Can be estimated',
  S: 'Small - Fits in one sprint',
  T: 'Testable - Has clear acceptance criteria'
};

// Well-written user story
const userStory = {
  id: 'US-042',
  title: 'Product Search',
  
  // User story format
  asA: 'customer',
  iWant: 'to search for products by name',
  soThat: 'I can quickly find what I\'m looking for',
  
  // Acceptance criteria (Given-When-Then)
  acceptanceCriteria: [
    {
      given: 'I am on the homepage',
      when: 'I type "laptop" in the search box',
      then: 'I see a list of laptops matching my search'
    },
    {
      given: 'I have searched for a product',
      when: 'no results are found',
      then: 'I see "No products found" message with search suggestions'
    },
    {
      given: 'I am viewing search results',
      when: 'I click on a product',
      then: 'I am taken to the product detail page'
    }
  ],
  
  // Additional details
  priority: 'High',
  storyPoints: 5,
  assignee: 'Development Team',
  sprint: 'Sprint 6',
  
  // Non-functional requirements
  nfr: [
    'Search must return results in < 500ms',
    'Support fuzzy matching for typos',
    'Handle special characters gracefully'
  ],
  
  // Technical notes
  technicalNotes: `
    - Use Elasticsearch for search functionality
    - Implement pagination (20 results per page)
    - Cache popular searches
  `,
  
  // Dependencies
  dependencies: ['US-010 (Product catalog API)'],
  
  // Definition of Done
  done: [
    '✓ Code written and follows standards',
    '✓ Unit tests with >80% coverage',
    '✓ Integration tests pass',
    '✓ Code reviewed and merged',
    '✓ Deployed to staging',
    '✓ Product Owner acceptance',
    '✓ Documentation updated'
  ]
};
```

### Estimation Techniques

**1. Planning Poker:**
```javascript
const planningPoker = {
  cards: [0, 0.5, 1, 2, 3, 5, 8, 13, 20, 40, 100, '?', '☕'],
  
  process: [
    '1. Product Owner presents user story',
    '2. Team discusses and asks questions',
    '3. Each member selects a card (face down)',
    '4. All reveal cards simultaneously',
    '5. Discuss differences (especially highest and lowest)',
    '6. Re-estimate until consensus reached'
  ],
  
  example: {
    story: 'US-042: Product Search',
    round1: {
      estimates: [5, 8, 5, 13, 8],
      discussion: '13 pointer concerned about Elasticsearch complexity'
    },
    round2: {
      estimates: [8, 8, 8, 8, 8],
      consensus: 8,
      reasoning: 'Team agreed on 8 points after discussing implementation approach'
    }
  }
};
```

**2. T-Shirt Sizing:**
```javascript
const tShirtSizing = {
  sizes: ['XS', 'S', 'M', 'L', 'XL'],
  
  // Quick estimation for backlog items
  examples: {
    XS: ['Fix typo in UI', 'Update documentation'],
    S: ['Add validation to form field', 'Simple API endpoint'],
    M: ['User authentication', 'Product search'],
    L: ['Payment integration', 'Admin dashboard'],
    XL: ['Complete e-commerce platform', 'Migration to new framework']
  },
  
  // Convert to story points later
  conversion: {
    XS: 1,
    S: 3,
    M: 5,
    L: 8,
    XL: 13
  }
};
```

### Velocity Tracking

```javascript
class VelocityTracker {
  constructor() {
    this.sprints = [];
  }
  
  addSprint(sprintData) {
    this.sprints.push(sprintData);
  }
  
  calculateAverageVelocity(lastNSprints = 3) {
    const recentSprints = this.sprints.slice(-lastNSprints);
    const totalPoints = recentSprints.reduce((sum, sprint) => sum + sprint.completed, 0);
    return Math.round(totalPoints / recentSprints.length);
  }
  
  predictCapacity(numberOfSprints) {
    const avgVelocity = this.calculateAverageVelocity();
    return avgVelocity * numberOfSprints;
  }
  
  getMetrics() {
    return {
      averageVelocity: this.calculateAverageVelocity(),
      trend: this.calculateTrend(),
      predictability: this.calculatePredictability()
    };
  }
  
  calculateTrend() {
    // Simple trend: comparing last 3 sprints to previous 3
    const recent = this.sprints.slice(-3);
    const previous = this.sprints.slice(-6, -3);
    
    const recentAvg = recent.reduce((sum, s) => sum + s.completed, 0) / 3;
    const previousAvg = previous.reduce((sum, s) => sum + s.completed, 0) / 3;
    
    return recentAvg > previousAvg ? 'Improving' : 'Declining';
  }
  
  calculatePredictability() {
    // Standard deviation of velocity
    const velocities = this.sprints.map(s => s.completed);
    const avg = velocities.reduce((a, b) => a + b, 0) / velocities.length;
    const variance = velocities.reduce((sum, v) => sum + Math.pow(v - avg, 2), 0) / velocities.length;
    const stdDev = Math.sqrt(variance);
    
    return stdDev < 3 ? 'High' : stdDev < 6 ? 'Medium' : 'Low';
  }
}

// Example usage
const tracker = new VelocityTracker();
tracker.addSprint({ number: 1, committed: 20, completed: 18 });
tracker.addSprint({ number: 2, committed: 20, completed: 22 });
tracker.addSprint({ number: 3, committed: 22, completed: 20 });
tracker.addSprint({ number: 4, committed: 20, completed: 21 });
tracker.addSprint({ number: 5, committed: 21, completed: 23 });

console.log('Average Velocity:', tracker.calculateAverageVelocity()); // Last 3 sprints
console.log('Predicted for 2 sprints:', tracker.predictCapacity(2));
console.log('Metrics:', tracker.getMetrics());
```

### Agile Metrics & KPIs

```javascript
const agileMetrics = {
  velocity: {
    description: 'Story points completed per sprint',
    purpose: 'Capacity planning and predictability',
    target: 'Stable velocity over time'
  },
  
  burndown: {
    description: 'Work remaining over time within sprint',
    purpose: 'Track sprint progress',
    target: 'Smooth downward trend'
  },
  
  burnup: {
    description: 'Work completed over time',
    purpose: 'Show progress toward release goal',
    target: 'Steady upward trend'
  },
  
  cycleTime: {
    description: 'Time from start to finish of work item',
    purpose: 'Measure team efficiency',
    target: 'Lower is better, consistent'
  },
  
  leadTime: {
    description: 'Time from request to delivery',
    purpose: 'Customer wait time',
    target: 'Lower is better'
  },
  
  cumulativeFlow: {
    description: 'Work items in each state over time',
    purpose: 'Identify bottlenecks',
    target: 'Smooth, parallel bands'
  },
  
  defectRate: {
    description: 'Bugs per story point or sprint',
    purpose: 'Quality measurement',
    target: 'Low and stable'
  },
  
  teamHappiness: {
    description: 'Team satisfaction score',
    purpose: 'Team health indicator',
    target: 'High and improving',
    measurement: 'Survey after each sprint (1-5 scale)'
  }
};
```

### Agile Best Practices

```javascript
const bestPractices = {
  teamCollaboration: [
    '✓ Co-located teams when possible',
    '✓ Daily face-to-face communication',
    '✓ Shared workspace (physical or virtual)',
    '✓ Pair programming for complex tasks',
    '✓ Regular knowledge sharing sessions',
    '✓ Cross-functional team members'
  ],
  
  technicalExcellence: [
    '✓ Test-Driven Development (TDD)',
    '✓ Continuous Integration/Deployment',
    '✓ Code reviews for all changes',
    '✓ Automated testing (unit, integration, e2e)',
    '✓ Refactoring and technical debt management',
    '✓ Definition of Done includes quality criteria'
  ],
  
  productOwnership: [
    '✓ Clear product vision and roadmap',
    '✓ Well-defined acceptance criteria',
    '✓ Prioritized backlog',
    '✓ Available to answer questions',
    '✓ Regular backlog refinement',
    '✓ Timely feedback on increments'
  ],
  
  continuousImprovement: [
    '✓ Act on retrospective action items',
    '✓ Experiment with new practices',
    '✓ Measure and track metrics',
    '✓ Regular team health checks',
    '✓ Learn from failures',
    '✓ Celebrate successes'
  ],
  
  stakeholderEngagement: [
    '✓ Regular sprint reviews with demos',
    '✓ Transparent communication',
    '✓ Manage expectations',
    '✓ Gather feedback continuously',
    '✓ Include stakeholders in planning',
    '✓ Show working software early and often'
  ]
};
```

### Common Agile Anti-Patterns

```javascript
const antiPatterns = {
  scrumBut: {
    description: 'We do Scrum, but...',
    examples: [
      '"We do Scrum, but we don\'t do retrospectives" - Missing continuous improvement',
      '"We do Scrum, but our sprints are 6 weeks" - Not iterative enough',
      '"We do Scrum, but Product Owner never attends meetings" - Lack of collaboration'
    ]
  },
  
  waterScrumFall: {
    description: 'Waterfall with Scrum in the middle',
    problem: 'Big upfront design → Scrum implementation → Big bang testing',
    solution: 'Integrate testing and design within sprints'
  },
  
  darkScrum: {
    description: 'Scrum used to pressure developers',
    examples: [
      'Daily standup as status reporting to management',
      'Using velocity to compare teams',
      'Blaming team for not meeting unrealistic commitments',
      'No time allocated for quality or technical debt'
    ]
  },
  
  miniWaterfall: {
    description: 'Each sprint becomes mini waterfall',
    problem: 'Design → Dev → Test in sequence within sprint',
    solution: 'Parallel work, cross-functional collaboration'
  },
  
  noRetro: {
    description: 'Skipping retrospectives',
    problem: 'No continuous improvement',
    solution: 'Make retros mandatory and actionable'
  },
  
  zombieScrum: {
    description: 'Going through motions without delivering value',
    signs: [
      'No stakeholder engagement',
      'No working software demonstrated',
      'No real collaboration',
      'Meetings feel like checkbox exercises'
    ],
    solution: 'Focus on delivering value, engage stakeholders'
  }
};
```

### Agile Transformation Tips

```javascript
const transformationSteps = {
  phase1_assessment: {
    duration: '2-4 weeks',
    activities: [
      'Assess current state',
      'Identify pain points',
      'Get leadership buy-in',
      'Form pilot team',
      'Set clear goals'
    ]
  },
  
  phase2_training: {
    duration: '2-3 weeks',
    activities: [
      'Agile principles training',
      'Scrum/Kanban training',
      'Tool training (Jira, etc.)',
      'Role-specific training',
      'Hands-on workshops'
    ]
  },
  
  phase3_pilot: {
    duration: '2-3 sprints',
    activities: [
      'Run pilot with one team',
      'Establish ceremonies',
      'Set up tooling',
      'Coach team daily',
      'Gather feedback'
    ]
  },
  
  phase4_scale: {
    duration: '3-6 months',
    activities: [
      'Expand to more teams',
      'Establish communities of practice',
      'Continuous coaching',
      'Refine processes',
      'Measure and adapt'
    ]
  },
  
  successFactors: [
    'Executive sponsorship',
    'Start small, scale gradually',
    'Invest in training and coaching',
    'Embrace failure as learning',
    'Focus on value delivery',
    'Be patient and persistent'
  ]
};
```

### Agile Tools & Software

```javascript
const agileTools = {
  projectManagement: [
    {
      name: 'Jira',
      bestFor: 'Scrum and Kanban boards, backlog management',
      features: ['Sprint planning', 'Burndown charts', 'Reporting', 'Integrations']
    },
    {
      name: 'Azure DevOps',
      bestFor: 'Microsoft shops, integrated with dev tools',
      features: ['Boards', 'Pipelines', 'Repos', 'Test Plans']
    },
    {
      name: 'Trello',
      bestFor: 'Simple Kanban boards, small teams',
      features: ['Easy to use', 'Visual boards', 'Power-ups']
    },
    {
      name: 'Monday.com',
      bestFor: 'Flexible workflows, non-technical teams',
      features: ['Customizable', 'Automation', 'Timeline views']
    }
  ],
  
  collaboration: [
    'Slack / Microsoft Teams - Communication',
    'Miro / Mural - Virtual whiteboard',
    'Confluence - Documentation',
    'Zoom / Meet - Video conferencing'
  ],
  
  cicd: [
    'Jenkins - CI/CD automation',
    'GitHub Actions - Integrated with GitHub',
    'GitLab CI/CD - Integrated with GitLab',
    'CircleCI - Cloud-based CI/CD'
  ]
};
```

---

## Generative AI & AI-Assisted Development

### What is Generative AI?

Generative AI refers to artificial intelligence systems that can create new content, including:
- **Code generation**: Writing functions, classes, and complete applications
- **Text generation**: Documentation, comments, explanations
- **Problem solving**: Debugging, optimization suggestions, architecture design
- **Testing**: Test case generation, edge case identification
- **Refactoring**: Code improvement suggestions

```
┌────────────────┐
│  Developer     │
│  Prompt/Query  │
└────────┬───────┘
         │
         ▼
┌────────────────┐       ┌──────────────┐
│   AI Model     │◄──────│  Training    │
│  (GPT, Codex)  │       │     Data     │
└────────┬───────┘       └──────────────┘
         │
         ▼
┌────────────────┐
│   Generated    │
│  Code/Content  │
└────────┬───────┘
         │
         ▼
┌────────────────┐
│   Developer    │
│  Review/Edit   │
└────────────────┘
```

### GitHub Copilot

#### What is GitHub Copilot?

GitHub Copilot is an AI pair programmer that:
- Suggests code completions in real-time
- Generates entire functions from comments
- Provides multiple solution alternatives
- Learns from context in your codebase
- Supports 40+ programming languages

#### Setting Up GitHub Copilot

**Installation (VS Code):**
```bash
# 1. Install the extension
# Go to Extensions → Search "GitHub Copilot"
# Or install via CLI
code --install-extension GitHub.copilot

# 2. Authenticate with GitHub
# Sign in when prompted
# Requires GitHub Copilot subscription
```

**Configuration (.vscode/settings.json):**
```json
{
  "github.copilot.enable": {
    "*": true,
    "yaml": false,
    "plaintext": false
  },
  "github.copilot.advanced": {
    "debug.overrideEngine": "gpt-4",
    "inlineSuggestCount": 3
  },
  "editor.inlineSuggest.enabled": true,
  "editor.suggestSelection": "first"
}
```

#### Using Copilot Effectively

**1. Function Generation from Comments:**
```javascript
// ✅ Good: Descriptive comment for Copilot
// Function to validate email format using regex
// Returns true if valid, false otherwise
function validateEmail(email) {
  // Copilot will suggest implementation
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
}

// Function to calculate compound interest
// Parameters: principal (initial amount), rate (annual %), time (years), n (compounds per year)
// Returns: final amount
function calculateCompoundInterest(principal, rate, time, n) {
  // Copilot generates: A = P(1 + r/n)^(nt)
  return principal * Math.pow(1 + rate / (n * 100), n * time);
}

// Async function to fetch user data from API with error handling
// Retries up to 3 times on failure
async function fetchUserWithRetry(userId, retries = 3) {
  // Copilot will suggest retry logic
  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(`/api/users/${userId}`);
      if (!response.ok) throw new Error('API error');
      return await response.json();
    } catch (error) {
      if (i === retries - 1) throw error;
      await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
    }
  }
}
```

**2. Test Generation:**
```javascript
// Copilot can generate tests from function signatures
function calculateDiscount(price, discountPercent) {
  if (price < 0 || discountPercent < 0 || discountPercent > 100) {
    throw new Error('Invalid input');
  }
  return price * (1 - discountPercent / 100);
}

// Copilot suggests:
describe('calculateDiscount', () => {
  it('should calculate discount correctly', () => {
    expect(calculateDiscount(100, 10)).toBe(90);
    expect(calculateDiscount(50, 20)).toBe(40);
  });
  
  it('should handle 0% discount', () => {
    expect(calculateDiscount(100, 0)).toBe(100);
  });
  
  it('should handle 100% discount', () => {
    expect(calculateDiscount(100, 100)).toBe(0);
  });
  
  it('should throw error for negative price', () => {
    expect(() => calculateDiscount(-100, 10)).toThrow('Invalid input');
  });
  
  it('should throw error for invalid discount', () => {
    expect(() => calculateDiscount(100, 150)).toThrow('Invalid input');
  });
});
```

**3. Code Refactoring:**
```javascript
// Original code (before Copilot suggestion)
function processUsers(users) {
  let result = [];
  for (let i = 0; i < users.length; i++) {
    if (users[i].age >= 18 && users[i].active === true) {
      result.push({
        name: users[i].name,
        email: users[i].email
      });
    }
  }
  return result;
}

// Copilot suggests refactored version:
function processUsers(users) {
  return users
    .filter(user => user.age >= 18 && user.active)
    .map(({ name, email }) => ({ name, email }));
}
```

**4. Documentation Generation:**
```javascript
/**
 * Copilot can generate JSDoc comments
 * Type the /** above a function and press Enter
 */

function mergeArrays(arr1, arr2, removeDuplicates = false) {
  const merged = [...arr1, ...arr2];
  return removeDuplicates ? [...new Set(merged)] : merged;
}

// Copilot generates:
/**
 * Merges two arrays into one
 * @param {Array} arr1 - First array to merge
 * @param {Array} arr2 - Second array to merge
 * @param {boolean} removeDuplicates - Whether to remove duplicate values
 * @returns {Array} Merged array
 * @example
 * mergeArrays([1, 2], [3, 4]) // [1, 2, 3, 4]
 * mergeArrays([1, 2], [2, 3], true) // [1, 2, 3]
 */
```

**5. Algorithm Implementation:**
```javascript
// Binary search implementation
// Copilot can generate classic algorithms from comments

// Implement binary search algorithm
// Returns index of target in sorted array, -1 if not found
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  
  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    
    if (arr[mid] === target) {
      return mid;
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
  
  return -1;
}

// Implement quicksort algorithm
function quickSort(arr) {
  if (arr.length <= 1) return arr;
  
  const pivot = arr[Math.floor(arr.length / 2)];
  const left = arr.filter(x => x < pivot);
  const middle = arr.filter(x => x === pivot);
  const right = arr.filter(x => x > pivot);
  
  return [...quickSort(left), ...middle, ...quickSort(right)];
}
```

### Prompt Engineering for Code Generation

#### Effective Prompts

**❌ Poor Prompts:**
```javascript
// "make function" - Too vague
// "sort data" - Lacks context
// "fix bug" - No details
```

**✅ Good Prompts:**
```javascript
// Create a function that validates a credit card number using Luhn algorithm
// The function should accept a string, remove spaces and dashes, and return boolean

// Implement rate limiting middleware for Express.js
// Allow maximum 100 requests per 15 minutes per IP address
// Return 429 status code when limit exceeded

// Write a React custom hook for debounced search
// Hook should accept search callback and delay in milliseconds
// Should cancel previous searches when new input arrives

// Create a SQL query to find top 10 customers by total purchase amount
// Join customers, orders, and order_items tables
// Filter for orders in the last 6 months
// Group by customer and sort by total descending
```

#### Prompt Patterns

**1. Role-Based Prompts:**
```javascript
// Act as a senior software architect
// Design a microservices architecture for an e-commerce platform
// Include API gateway, authentication service, product catalog, and order service

// Act as a security expert
// Review this authentication code and suggest security improvements
// Focus on password hashing, session management, and input validation
```

**2. Step-by-Step Instructions:**
```javascript
// Create a function that:
// 1. Accepts an array of objects with 'name' and 'score' properties
// 2. Filters out objects with score less than 50
// 3. Sorts remaining objects by score descending
// 4. Returns only the top 5 objects
// 5. Include TypeScript type definitions

function getTopScorers(students: Array<{name: string, score: number}>): Array<{name: string, score: number}> {
  return students
    .filter(student => student.score >= 50)
    .sort((a, b) => b.score - a.score)
    .slice(0, 5);
}
```

**3. Example-Driven Prompts:**
```javascript
// Create a function similar to lodash's _.groupBy
// Example usage:
// groupBy([{type: 'fruit', name: 'apple'}, {type: 'vegetable', name: 'carrot'}], 'type')
// Returns: { fruit: [{...}], vegetable: [{...}] }

function groupBy<T>(array: T[], key: keyof T): Record<string, T[]> {
  return array.reduce((result, item) => {
    const groupKey = String(item[key]);
    if (!result[groupKey]) {
      result[groupKey] = [];
    }
    result[groupKey].push(item);
    return result;
  }, {} as Record<string, T[]>);
}
```

**4. Constraint-Based Prompts:**
```javascript
// Implement pagination utility
// Constraints:
// - Must work with any data source (array, database)
// - Support page size between 10-100 items
// - Return metadata (totalPages, currentPage, hasNext, hasPrev)
// - Use TypeScript generics

interface PaginationResult<T> {
  data: T[];
  metadata: {
    totalItems: number;
    totalPages: number;
    currentPage: number;
    pageSize: number;
    hasNext: boolean;
    hasPrev: boolean;
  };
}

function paginate<T>(
  items: T[],
  page: number = 1,
  pageSize: number = 20
): PaginationResult<T> {
  // Validate constraints
  pageSize = Math.max(10, Math.min(100, pageSize));
  
  const totalItems = items.length;
  const totalPages = Math.ceil(totalItems / pageSize);
  const currentPage = Math.max(1, Math.min(page, totalPages));
  const startIndex = (currentPage - 1) * pageSize;
  const endIndex = startIndex + pageSize;
  
  return {
    data: items.slice(startIndex, endIndex),
    metadata: {
      totalItems,
      totalPages,
      currentPage,
      pageSize,
      hasNext: currentPage < totalPages,
      hasPrev: currentPage > 1
    }
  };
}
```

### AI-Assisted Development Workflow

#### 1. Requirements to Code

```javascript
// AI-assisted development flow
const developmentWorkflow = {
  phase1_requirements: {
    input: "User story: As a user, I want to search products by name and filter by category",
    aiAssist: "Generate API endpoint design and data models"
  },
  
  phase2_design: {
    // AI generates:
    apiEndpoint: "GET /api/products/search?q={query}&category={category}&page={page}",
    dataModel: {
      Product: {
        id: "string",
        name: "string",
        category: "string",
        price: "number",
        description: "string",
        imageUrl: "string"
      }
    }
  },
  
  phase3_implementation: {
    // AI generates controller
    controller: `
      async searchProducts(req, res) {
        const { q, category, page = 1 } = req.query;
        const pageSize = 20;
        
        const query = Product.find();
        
        if (q) {
          query.where('name').regex(new RegExp(q, 'i'));
        }
        
        if (category) {
          query.where('category').equals(category);
        }
        
        const total = await Product.countDocuments(query);
        const products = await query
          .skip((page - 1) * pageSize)
          .limit(pageSize)
          .exec();
        
        res.json({
          data: products,
          pagination: {
            page: parseInt(page),
            pageSize,
            total,
            totalPages: Math.ceil(total / pageSize)
          }
        });
      }
    `
  },
  
  phase4_testing: {
    // AI generates tests
    tests: `
      describe('Product Search', () => {
        it('should search by name', async () => {
          const res = await request(app)
            .get('/api/products/search?q=laptop')
            .expect(200);
          
          expect(res.body.data).toBeInstanceOf(Array);
          expect(res.body.data[0].name).toContain('laptop');
        });
        
        it('should filter by category', async () => {
          const res = await request(app)
            .get('/api/products/search?category=electronics')
            .expect(200);
          
          expect(res.body.data.every(p => p.category === 'electronics')).toBe(true);
        });
      });
    `
  }
};
```

#### 2. Code Review with AI

```javascript
// Use AI to review code for issues
class AICodeReviewer {
  async reviewCode(code) {
    const issues = [];
    
    // AI can identify:
    // - Security vulnerabilities
    // - Performance issues
    // - Code smells
    // - Best practice violations
    
    return {
      security: [
        {
          line: 15,
          severity: 'high',
          issue: 'SQL injection vulnerability',
          suggestion: 'Use parameterized queries'
        }
      ],
      performance: [
        {
          line: 42,
          severity: 'medium',
          issue: 'N+1 query problem',
          suggestion: 'Use eager loading with joins'
        }
      ],
      codeQuality: [
        {
          line: 88,
          severity: 'low',
          issue: 'Function too long (>50 lines)',
          suggestion: 'Break into smaller functions'
        }
      ]
    };
  }
}
```

#### 3. Debugging with AI

```javascript
// AI-assisted debugging workflow

// 1. Paste error message
const error = `
TypeError: Cannot read property 'name' of undefined
    at getUserName (app.js:42)
    at processRequest (app.js:120)
`;

// 2. AI analyzes and suggests fixes
const aiSuggestion = {
  problem: "Attempting to access 'name' property on undefined object",
  likelyCause: "User object is undefined or null",
  suggestedFix: `
    function getUserName(user) {
      // Add null check
      if (!user) {
        return 'Anonymous';
      }
      return user.name || 'Unknown';
      
      // Or use optional chaining
      return user?.name ?? 'Anonymous';
    }
  `,
  prevention: "Add input validation and guard clauses"
};

// 3. AI suggests tests to prevent regression
const aiGeneratedTest = `
  describe('getUserName', () => {
    it('should return name when user exists', () => {
      expect(getUserName({ name: 'John' })).toBe('John');
    });
    
    it('should return default when user is undefined', () => {
      expect(getUserName(undefined)).toBe('Anonymous');
    });
    
    it('should return default when user is null', () => {
      expect(getUserName(null)).toBe('Anonymous');
    });
    
    it('should return Unknown when name is missing', () => {
      expect(getUserName({})).toBe('Unknown');
    });
  });
`;
```

### Other AI Development Tools

#### 1. ChatGPT / Claude for Development

```javascript
// Use cases for conversational AI

const aiUseCases = [
  {
    task: 'Architecture Design',
    prompt: 'Design a scalable microservices architecture for a food delivery app',
    output: 'Detailed architecture with services, APIs, data flow'
  },
  {
    task: 'Algorithm Explanation',
    prompt: 'Explain how the A* pathfinding algorithm works with code example',
    output: 'Step-by-step explanation with implementation'
  },
  {
    task: 'Code Translation',
    prompt: 'Convert this Python code to JavaScript',
    output: 'Equivalent JavaScript implementation'
  },
  {
    task: 'Optimization',
    prompt: 'Optimize this SQL query for better performance',
    output: 'Optimized query with indexes and execution plan'
  },
  {
    task: 'Documentation',
    prompt: 'Write API documentation for this Express router',
    output: 'OpenAPI/Swagger documentation'
  }
];
```

#### 2. Tabnine

```javascript
// Tabnine: AI code completion
// Features:
// - Whole-line and full-function completions
// - Learns from your codebase
// - Works offline
// - Supports team training

// Example usage
function calculateTotalPrice(items) {
  // Tabnine suggests based on your codebase patterns
  return items.reduce((total, item) => {
    return total + (item.price * item.quantity);
  }, 0);
}
```

#### 3. Amazon CodeWhisperer

```javascript
// AWS-focused code suggestions
// Specialized in AWS SDK usage

// Type comment:
// Create S3 client and upload file with error handling

const AWS = require('aws-sdk');
const fs = require('fs');

async function uploadToS3(bucketName, key, filePath) {
  const s3 = new AWS.S3();
  const fileContent = fs.readFileSync(filePath);
  
  const params = {
    Bucket: bucketName,
    Key: key,
    Body: fileContent
  };
  
  try {
    const result = await s3.upload(params).promise();
    console.log('Upload successful:', result.Location);
    return result;
  } catch (error) {
    console.error('Upload failed:', error);
    throw error;
  }
}
```

### Best Practices for AI-Assisted Development

#### 1. Always Review AI-Generated Code

```javascript
// ❌ Don't: Blindly accept AI suggestions
// AI might generate working code that has issues

// AI-generated code (needs review)
function divideNumbers(a, b) {
  return a / b; // Missing division by zero check!
}

// ✅ Do: Review and improve
function divideNumbers(a, b) {
  if (b === 0) {
    throw new Error('Division by zero');
  }
  if (typeof a !== 'number' || typeof b !== 'number') {
    throw new Error('Invalid input: both arguments must be numbers');
  }
  return a / b;
}
```

#### 2. Verify Security Implications

```javascript
// ❌ AI might generate insecure code
function login(username, password) {
  const query = `SELECT * FROM users WHERE username='${username}' AND password='${password}'`;
  return db.query(query); // SQL injection vulnerability!
}

// ✅ Always check for security issues
function login(username, password) {
  const query = 'SELECT * FROM users WHERE username = ? AND password_hash = ?';
  const passwordHash = bcrypt.hashSync(password, 10);
  return db.query(query, [username, passwordHash]);
}
```

#### 3. Test AI-Generated Code

```javascript
// Always write tests for AI-generated code
describe('AI-Generated Function Tests', () => {
  // Test happy path
  it('should work with valid inputs', () => {
    expect(aiGeneratedFunction(validInput)).toBe(expectedOutput);
  });
  
  // Test edge cases
  it('should handle empty input', () => {
    expect(aiGeneratedFunction('')).toBe(defaultValue);
  });
  
  it('should handle null/undefined', () => {
    expect(() => aiGeneratedFunction(null)).toThrow();
  });
  
  // Test error conditions
  it('should handle errors gracefully', () => {
    expect(() => aiGeneratedFunction(invalidInput)).toThrow(Error);
  });
});
```

#### 4. Understand the Generated Code

```javascript
// ❌ Don't use code you don't understand
// If AI generates complex code, make sure you understand it

// ✅ Ask AI to explain
// "Explain this code line by line"
// "What is the time complexity of this algorithm?"
// "What are the potential edge cases?"

function complexAlgorithm(data) {
  // Make sure you understand:
  // - What this does
  // - Why it works
  // - What could go wrong
  // - How to maintain it
}
```

#### 5. Use AI as a Learning Tool

```javascript
// Use AI to improve your skills

// Ask AI:
// "What's a better way to write this code?"
// "Explain the design pattern used here"
// "What are the pros and cons of this approach?"
// "Show me best practices for error handling in Node.js"

// Example learning interaction:
const myCode = `
  for (let i = 0; i < array.length; i++) {
    if (array[i] % 2 === 0) {
      result.push(array[i]);
    }
  }
`;

// AI explains better approach:
const improvedCode = `
  // More readable and functional approach
  const result = array.filter(num => num % 2 === 0);
  
  // Why this is better:
  // 1. More declarative and readable
  // 2. Less error-prone (no manual index management)
  // 3. Immutable (doesn't modify original array)
  // 4. Chainable with other array methods
`;
```

### AI Development Workflow Example

```javascript
// Complete workflow using AI assistance

// STEP 1: Requirements Analysis with AI
const aiRequirementsAnalysis = {
  input: "Build a REST API for a blog platform",
  aiOutput: {
    entities: ['User', 'Post', 'Comment', 'Category'],
    endpoints: [
      'POST /api/users/register',
      'POST /api/users/login',
      'GET /api/posts',
      'POST /api/posts',
      'GET /api/posts/:id',
      'PUT /api/posts/:id',
      'DELETE /api/posts/:id',
      'POST /api/posts/:id/comments'
    ],
    techStack: {
      backend: 'Node.js + Express',
      database: 'PostgreSQL',
      auth: 'JWT',
      validation: 'Joi'
    }
  }
};

// STEP 2: Generate Data Models with AI
// Prompt: "Create Sequelize models for User, Post, Comment"

const UserModel = `
  const User = sequelize.define('User', {
    id: { type: DataTypes.UUID, defaultValue: DataTypes.UUIDV4, primaryKey: true },
    username: { type: DataTypes.STRING, unique: true, allowNull: false },
    email: { type: DataTypes.STRING, unique: true, allowNull: false },
    passwordHash: { type: DataTypes.STRING, allowNull: false }
  });
`;

// STEP 3: Generate Controllers with AI
// Prompt: "Create Express controller for blog posts CRUD operations"

const PostController = `
  class PostController {
    async create(req, res) {
      try {
        const { title, content, categoryId } = req.body;
        const post = await Post.create({
          title,
          content,
          categoryId,
          userId: req.user.id
        });
        res.status(201).json(post);
      } catch (error) {
        res.status(400).json({ error: error.message });
      }
    }
    
    async getAll(req, res) {
      const { page = 1, limit = 10 } = req.query;
      const posts = await Post.findAndCountAll({
        limit,
        offset: (page - 1) * limit,
        include: [User, Category]
      });
      res.json(posts);
    }
  }
`;

// STEP 4: Generate Tests with AI
// Prompt: "Create Jest tests for PostController"

const PostTests = `
  describe('PostController', () => {
    describe('create', () => {
      it('should create post with valid data', async () => {
        const res = await request(app)
          .post('/api/posts')
          .set('Authorization', \`Bearer \${token}\`)
          .send({ title: 'Test', content: 'Content' })
          .expect(201);
        
        expect(res.body).toHaveProperty('id');
      });
    });
  });
`;

// STEP 5: Generate Documentation with AI
// Prompt: "Create OpenAPI documentation for blog API"

const apiDocumentation = `
  /**
   * @swagger
   * /api/posts:
   *   post:
   *     summary: Create a new blog post
   *     tags: [Posts]
   *     security:
   *       - bearerAuth: []
   *     requestBody:
   *       required: true
   *       content:
   *         application/json:
   *           schema:
   *             type: object
   *             properties:
   *               title: { type: string }
   *               content: { type: string }
   */
`;
```

### AI Ethics and Limitations

#### Important Considerations

```javascript
const aiLimitations = {
  codeOwnership: {
    issue: "Who owns AI-generated code?",
    guideline: "Review your company's policy on AI-generated code",
    action: "Document that AI was used in development"
  },
  
  licensing: {
    issue: "AI might generate code similar to copyrighted code",
    guideline: "Review generated code for licensing issues",
    action: "Use tools to check for code similarity"
  },
  
  security: {
    issue: "AI might suggest vulnerable code",
    guideline: "Always review for security issues",
    action: "Run security scans on AI-generated code"
  },
  
  privacy: {
    issue: "Don't share sensitive code with AI tools",
    guideline: "Avoid pasting proprietary or customer data",
    action: "Use sanitized examples when asking for help"
  },
  
  accuracy: {
    issue: "AI can make mistakes or hallucinate",
    guideline: "Verify all AI suggestions",
    action: "Test thoroughly and understand the code"
  },
  
  bias: {
    issue: "AI might perpetuate biased patterns from training data",
    guideline: "Review for inclusive and accessible code",
    action: "Apply human judgment and ethics"
  }
};
```

### AI Development Checklist

```markdown
## Before Using AI Tools
- [ ] Understand your company's AI usage policy
- [ ] Review licensing and code ownership guidelines
- [ ] Set up AI tools with appropriate permissions
- [ ] Understand privacy implications

## During Development with AI
- [ ] Provide clear, detailed prompts
- [ ] Review all AI-generated code
- [ ] Verify security implications
- [ ] Check for licensing issues
- [ ] Test generated code thoroughly
- [ ] Document AI assistance used

## After AI-Assisted Development
- [ ] Ensure you understand all generated code
- [ ] Refactor for maintainability
- [ ] Add comprehensive tests
- [ ] Update documentation
- [ ] Review with team members
- [ ] Monitor for issues in production
```

---

## Security Best Practices

### 1. Authentication & Authorization

#### Secure Password Hashing
```javascript
const bcrypt = require('bcrypt');

// ✅ Hash password before storing
async function hashPassword(password) {
  const saltRounds = 12;
  return await bcrypt.hash(password, saltRounds);
}

// ✅ Verify password
async function verifyPassword(password, hashedPassword) {
  return await bcrypt.compare(password, hashedPassword);
}

// ❌ Never store plain text passwords
// ❌ Never use MD5 or SHA1 for passwords
```

#### JWT Token Security
```javascript
const jwt = require('jsonwebtoken');

// ✅ Generate token with expiration
function generateToken(userId) {
  return jwt.sign(
    { userId },
    process.env.JWT_SECRET,
    { expiresIn: '15m' } // Short-lived access token
  );
}

// ✅ Verify token
function verifyToken(token) {
  try {
    return jwt.verify(token, process.env.JWT_SECRET);
  } catch (error) {
    throw new Error('Invalid token');
  }
}

// ✅ Implement refresh tokens
function generateRefreshToken(userId) {
  return jwt.sign(
    { userId, type: 'refresh' },
    process.env.REFRESH_TOKEN_SECRET,
    { expiresIn: '7d' }
  );
}
```

#### OAuth 2.0 / OpenID Connect
```javascript
// Use established libraries
const passport = require('passport');
const GoogleStrategy = require('passport-google-oauth20').Strategy;

passport.use(new GoogleStrategy({
    clientID: process.env.GOOGLE_CLIENT_ID,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    callbackURL: '/auth/google/callback'
  },
  (accessToken, refreshToken, profile, done) => {
    // Handle user authentication
    User.findOrCreate({ googleId: profile.id }, (err, user) => {
      return done(err, user);
    });
  }
));
```

### 2. Input Validation & Sanitization

#### Prevent SQL Injection
```javascript
// ❌ Bad: Vulnerable to SQL injection
const query = `SELECT * FROM users WHERE email = '${userInput}'`;

// ✅ Good: Use parameterized queries
const query = 'SELECT * FROM users WHERE email = ?';
db.query(query, [userInput]);

// ✅ Good: Use ORM with prepared statements
const user = await User.findOne({ where: { email: userInput } });
```

#### Prevent XSS (Cross-Site Scripting)
```javascript
// ✅ Sanitize user input
const DOMPurify = require('isomorphic-dompurify');

function sanitizeInput(userInput) {
  return DOMPurify.sanitize(userInput);
}

// ✅ Escape HTML in templates
// React automatically escapes by default
<div>{userInput}</div>

// ❌ Dangerous: dangerouslySetInnerHTML
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ If needed, sanitize first
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userInput) }} />
```

#### Input Validation
```javascript
const Joi = require('joi');

// ✅ Validate all inputs
const userSchema = Joi.object({
  username: Joi.string().alphanum().min(3).max(30).required(),
  email: Joi.string().email().required(),
  password: Joi.string().min(8).pattern(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/).required(),
  age: Joi.number().integer().min(18).max(120)
});

function validateUser(userData) {
  const { error, value } = userSchema.validate(userData);
  if (error) {
    throw new Error(`Validation error: ${error.details[0].message}`);
  }
  return value;
}
```

### 3. HTTPS & Secure Communication

```javascript
// ✅ Enforce HTTPS
app.use((req, res, next) => {
  if (!req.secure && process.env.NODE_ENV === 'production') {
    return res.redirect(`https://${req.headers.host}${req.url}`);
  }
  next();
});

// ✅ Strict Transport Security
app.use(helmet.hsts({
  maxAge: 31536000,
  includeSubDomains: true,
  preload: true
}));
```

### 4. CSRF Protection

```javascript
const csrf = require('csurf');
const csrfProtection = csrf({ cookie: true });

// ✅ Add CSRF token to forms
app.get('/form', csrfProtection, (req, res) => {
  res.render('form', { csrfToken: req.csrfToken() });
});

app.post('/form', csrfProtection, (req, res) => {
  // Process form - CSRF token validated automatically
});

// ✅ Include token in AJAX requests
fetch('/api/data', {
  method: 'POST',
  headers: {
    'CSRF-Token': csrfToken,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(data)
});
```

### 5. CORS Configuration

```javascript
const cors = require('cors');

// ❌ Bad: Allow all origins
app.use(cors({ origin: '*' }));

// ✅ Good: Whitelist specific origins
const whitelist = ['https://example.com', 'https://app.example.com'];
app.use(cors({
  origin: (origin, callback) => {
    if (whitelist.includes(origin) || !origin) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization']
}));
```

### 6. Security Headers

```javascript
const helmet = require('helmet');

app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'", "https://fonts.googleapis.com"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", "data:", "https:"],
      connectSrc: ["'self'", "https://api.example.com"],
      fontSrc: ["'self'", "https://fonts.gstatic.com"],
      objectSrc: ["'none'"],
      upgradeInsecureRequests: []
    }
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true
  },
  frameguard: { action: 'deny' },
  noSniff: true,
  xssFilter: true
}));
```

### 7. Environment Variables & Secrets

```javascript
// ✅ Use environment variables for secrets
require('dotenv').config();

const config = {
  database: {
    host: process.env.DB_HOST,
    password: process.env.DB_PASSWORD
  },
  jwt: {
    secret: process.env.JWT_SECRET
  },
  api: {
    key: process.env.API_KEY
  }
};

// ❌ Never commit .env file
// ✅ Add to .gitignore
/*
.env
.env.local
.env.production
*/

// ✅ Use secret management services
// - AWS Secrets Manager
// - Azure Key Vault
// - HashiCorp Vault
```

### 8. Rate Limiting & DDoS Protection

```javascript
const rateLimit = require('express-rate-limit');

// ✅ General API rate limiting
const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100,
  message: 'Too many requests from this IP'
});

app.use('/api/', apiLimiter);

// ✅ Strict rate limiting for authentication
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5, // 5 attempts per 15 minutes
  skipSuccessfulRequests: true
});

app.post('/login', authLimiter, loginController);

// ✅ Account lockout after failed attempts
let failedAttempts = {};

async function loginAttempt(username, password) {
  const attempts = failedAttempts[username] || 0;
  
  if (attempts >= 5) {
    throw new Error('Account locked. Try again in 15 minutes');
  }
  
  const user = await authenticate(username, password);
  
  if (!user) {
    failedAttempts[username] = attempts + 1;
    throw new Error('Invalid credentials');
  }
  
  delete failedAttempts[username];
  return user;
}
```

### 9. Dependency Security

```bash
# ✅ Regular security audits
npm audit
npm audit fix

# ✅ Use automated tools
npm install -g snyk
snyk test
snyk monitor

# ✅ Keep dependencies updated
npm outdated
npm update
```

```javascript
// package.json
{
  "scripts": {
    "audit": "npm audit",
    "audit:fix": "npm audit fix",
    "check:vulnerabilities": "snyk test"
  }
}
```

### 10. Logging & Monitoring

```javascript
const winston = require('winston');

// ✅ Comprehensive logging
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

// ✅ Log security events
function logSecurityEvent(event, details) {
  logger.warn('Security Event', {
    event,
    details,
    timestamp: new Date(),
    ip: details.ip,
    userId: details.userId
  });
}

// Monitor for:
// - Failed login attempts
// - Unauthorized access attempts
// - Unusual API usage patterns
// - Data access anomalies
```

### 11. Data Encryption

```javascript
const crypto = require('crypto');

// ✅ Encrypt sensitive data at rest
function encrypt(text, secretKey) {
  const iv = crypto.randomBytes(16);
  const cipher = crypto.createCipheriv('aes-256-cbc', Buffer.from(secretKey), iv);
  let encrypted = cipher.update(text);
  encrypted = Buffer.concat([encrypted, cipher.final()]);
  return iv.toString('hex') + ':' + encrypted.toString('hex');
}

function decrypt(text, secretKey) {
  const parts = text.split(':');
  const iv = Buffer.from(parts.shift(), 'hex');
  const encrypted = Buffer.from(parts.join(':'), 'hex');
  const decipher = crypto.createDecipheriv('aes-256-cbc', Buffer.from(secretKey), iv);
  let decrypted = decipher.update(encrypted);
  decrypted = Buffer.concat([decrypted, decipher.final()]);
  return decrypted.toString();
}

// ✅ Use TLS for data in transit
// ✅ Encrypt database backups
// ✅ Encrypt sensitive fields in database
```

### 12. File Upload Security

```javascript
const multer = require('multer');
const path = require('path');

// ✅ Validate file type and size
const upload = multer({
  storage: multer.diskStorage({
    destination: './uploads/',
    filename: (req, file, cb) => {
      // Generate unique filename
      const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
      cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
    }
  }),
  limits: {
    fileSize: 5 * 1024 * 1024 // 5MB
  },
  fileFilter: (req, file, cb) => {
    const allowedTypes = ['image/jpeg', 'image/png', 'application/pdf'];
    if (allowedTypes.includes(file.mimetype)) {
      cb(null, true);
    } else {
      cb(new Error('Invalid file type'));
    }
  }
});

// ✅ Scan uploaded files for malware
// ✅ Store uploads outside web root
// ✅ Use CDN for serving user uploads
```

### 13. API Security Best Practices

```javascript
// ✅ API versioning
app.use('/api/v1', v1Routes);
app.use('/api/v2', v2Routes);

// ✅ Authentication middleware
function authenticateToken(req, res, next) {
  const token = req.headers['authorization']?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'Access token required' });
  }
  
  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) return res.status(403).json({ error: 'Invalid token' });
    req.user = user;
    next();
  });
}

// ✅ Role-based access control
function authorize(...roles) {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Insufficient permissions' });
    }
    next();
  };
}

app.get('/admin', authenticateToken, authorize('admin'), adminController);
```

### 14. Security Checklist

#### Development Phase
- [ ] Input validation on all user inputs
- [ ] Parameterized queries for database operations
- [ ] Proper authentication and authorization
- [ ] Secure password hashing (bcrypt, argon2)
- [ ] CSRF protection implemented
- [ ] XSS protection implemented
- [ ] SQL injection prevention
- [ ] Security headers configured (Helmet.js)
- [ ] HTTPS enforced in production
- [ ] Environment variables for secrets
- [ ] Rate limiting implemented
- [ ] File upload validation
- [ ] Error messages don't leak sensitive info

#### Deployment Phase
- [ ] Dependencies audit (npm audit, Snyk)
- [ ] Remove development dependencies
- [ ] Disable debug mode
- [ ] Configure CORS properly
- [ ] Set up logging and monitoring
- [ ] Regular security updates scheduled
- [ ] Backup and disaster recovery plan
- [ ] Penetration testing completed
- [ ] Security scanning (SAST, DAST)
- [ ] SSL/TLS certificates configured

#### Ongoing Maintenance
- [ ] Regular dependency updates
- [ ] Security patch management
- [ ] Log analysis for anomalies
- [ ] Access control reviews
- [ ] Incident response plan
- [ ] Security training for team

---

## Additional Resources

### Tools & Libraries

**Security:**
- OWASP Top 10: https://owasp.org/www-project-top-ten/
- Snyk: Dependency vulnerability scanning
- Helmet.js: Security headers
- bcrypt: Password hashing
- jsonwebtoken: JWT implementation

**Performance:**
- Lighthouse: Performance auditing
- WebPageTest: Performance testing
- New Relic / Datadog: APM tools
- Redis: Caching layer
- CDN: CloudFlare, AWS CloudFront

**Testing:**
- Jest: Unit testing
- Cypress: E2E testing
- Artillery: Load testing
- SonarQube: Code quality

**Monitoring:**
- Prometheus + Grafana: Metrics
- ELK Stack: Logging
- Sentry: Error tracking
- PagerDuty: Incident management

---

## Conclusion

Building scalable, optimized, and secure applications requires a holistic approach:

1. **Scaling**: Design for horizontal scalability, use load balancing, caching, and async processing
2. **Optimization**: Focus on both frontend and backend performance, database queries, and network efficiency
3. **Code Quality**: Follow SOLID principles, write clean code, use design patterns, and maintain comprehensive tests
4. **Security**: Implement defense in depth - authentication, authorization, input validation, encryption, and continuous monitoring

Remember: Security and performance should be built into the development process from day one, not added as an afterthought.
