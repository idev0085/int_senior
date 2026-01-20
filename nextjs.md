# Next.js - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Fundamentals](#fundamentals)
2. [Core Concepts & Rendering](#core-concepts--rendering)
3. [React 18+ Features](#react-18-features)
4. [App Router vs Pages Router](#app-router-vs-pages-router)
5. [Performance & Optimization](#performance--optimization)
6. [Data Fetching](#data-fetching)
7. [Deployment & Production](#deployment--production)
8. [Architecture Patterns](#architecture-patterns)

---

## Fundamentals

### Q1: What is Next.js?

**Answer:**
Next.js is a React framework for building full-stack web applications. It provides a production-ready setup with built-in optimizations, routing, and rendering strategies.

**Key Features:**
- **File-based routing** - No manual route configuration
- **Multiple rendering modes** - SSG, SSR, ISR, CSR
- **Built-in optimizations** - Images, fonts, scripts
- **API routes** - Backend in the same codebase
- **TypeScript support** - First-class TypeScript integration
- **Zero config** - Works out of the box

**Why Choose Next.js?**

```javascript
// Traditional React SPA
// ❌ Manual routing setup
// ❌ No SSR without complex setup
// ❌ SEO challenges
// ❌ Manual optimization

// Next.js
// ✅ File = route automatically
// ✅ SSR/SSG built-in
// ✅ SEO-friendly by default
// ✅ Automatic code splitting
```

**Example - Simple Next.js App:**
```javascript
// app/page.js - Automatically becomes homepage
export default function Home() {
  return <h1>Welcome to Next.js!</h1>;
}

// app/about/page.js - Automatically becomes /about
export default function About() {
  return <h1>About Us</h1>;
}

// That's it! No router configuration needed.
```

**Use Next.js for:**
- E-commerce sites
- Marketing websites
- Dashboards and admin panels
- Blogs and content sites
- SaaS applications
- Any React app that benefits from SEO

**Don't use Next.js for:**
- Simple static sites (use plain HTML/CSS)
- Apps that don't need SSR/SSG
- Native mobile apps (use React Native)

### Q2: What is Server-Side Rendering (SSR)?

**Answer:**
SSR renders pages on the server for each request, sending fully rendered HTML to the browser.

**How it works:**
```
1. User requests /product/123
2. Server fetches data
3. Server renders React to HTML
4. HTML sent to browser
5. Browser displays content (instant!)
6. JavaScript hydrates (makes interactive)
```

**In Next.js App Router:**
```javascript
// Server Component with dynamic data (SSR by default)
export const dynamic = 'force-dynamic'; // Force SSR

export default async function ProductPage({ params }) {
  // Fetch on every request
  const product = await fetch(`https://api.example.com/products/${params.id}`, {
    cache: 'no-store' // Don't cache
  }).then(r => r.json());
  
  return (
    <div>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
    </div>
  );
}
```

**In Next.js Pages Router:**
```javascript
// pages/product/[id].js
export async function getServerSideProps({ params }) {
  const res = await fetch(`https://api.example.com/products/${params.id}`);
  const product = await res.json();
  
  return {
    props: { product }
  };
}

export default function ProductPage({ product }) {
  return (
    <div>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
    </div>
  );
}
```

**Advantages:**
- ✅ Always up-to-date content
- ✅ SEO-friendly (search engines see full content)
- ✅ Fast initial load
- ✅ Personalized content per user

**Disadvantages:**
- ❌ Slower than static (server processing time)
- ❌ Higher server costs
- ❌ Can't serve from CDN
- ❌ Server must be available

**Use SSR when:**
- Content changes frequently
- Personalized per user
- Real-time data required
- SEO important + dynamic content

### Q3: What is Static Site Generation (SSG)?

**Answer:**
SSG pre-renders pages at build time, generating static HTML files that can be cached and served from CDN.

**How it works:**
```
1. Build time: npm run build
2. Next.js generates HTML for all pages
3. Deploy static files to CDN
4. User requests page → served instantly from CDN
```

**In Next.js App Router:**
```javascript
// app/blog/[slug]/page.js
// Static by default if no dynamic data fetching

// Generate all blog post pages at build time
export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  return posts.map(post => ({
    slug: post.slug
  }));
}

export default async function BlogPost({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(r => r.json());
  
  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
    </article>
  );
}
```

**In Next.js Pages Router:**
```javascript
// pages/blog/[slug].js
export async function getStaticPaths() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  const paths = posts.map(post => ({
    params: { slug: post.slug }
  }));
  
  return {
    paths,
    fallback: false // or 'blocking' or true
  };
}

export async function getStaticProps({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(r => r.json());
  
  return {
    props: { post }
  };
}

export default function BlogPost({ post }) {
  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
    </article>
  );
}
```

**Advantages:**
- ✅ Blazing fast (CDN)
- ✅ Cheap hosting
- ✅ Scales infinitely
- ✅ Works offline
- ✅ SEO-perfect

**Disadvantages:**
- ❌ Build time increases with pages
- ❌ Content is stale until rebuild
- ❌ Not suitable for frequently changing data
- ❌ Can't personalize per user

**Use SSG when:**
- Content rarely changes
- Marketing pages, blogs, docs
- Same content for all users
- Performance is critical

### Q4: What is Incremental Static Regeneration (ISR)?

**Answer:**
ISR combines the best of SSG and SSR - static pages that can be updated after deployment without rebuilding the entire site.

**How it works:**
```
1. Page built statically at build time
2. Served from cache (fast!)
3. After X seconds (revalidation period), next request triggers rebuild
4. Old page served while new one builds in background
5. New page replaces old one in cache
```

**In Next.js App Router:**
```javascript
// app/products/[id]/page.js
export const revalidate = 60; // Revalidate every 60 seconds

export default async function ProductPage({ params }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(r => r.json());
  
  return (
    <div>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
      <p>Stock: {product.stock}</p>
    </div>
  );
}

// Or per-fetch revalidation
export default async function ProductPage({ params }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`, {
    next: { revalidate: 60 } // This specific fetch revalidates every 60s
  }).then(r => r.json());
  
  return <div>{product.name}</div>;
}
```

**In Next.js Pages Router:**
```javascript
// pages/products/[id].js
export async function getStaticProps({ params }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(r => r.json());
  
  return {
    props: { product },
    revalidate: 60 // Revalidate every 60 seconds
  };
}

export async function getStaticPaths() {
  return {
    paths: [],
    fallback: 'blocking' // Generate pages on-demand
  };
}

export default function ProductPage({ product }) {
  return <div>{product.name}</div>;
}
```

**On-Demand Revalidation:**
```javascript
// app/api/revalidate/route.js
import { revalidatePath, revalidateTag } from 'next/cache';

export async function POST(request) {
  const { path, tag } = await request.json();
  
  if (path) {
    revalidatePath(path);
  }
  
  if (tag) {
    revalidateTag(tag);
  }
  
  return Response.json({ revalidated: true });
}

// Trigger revalidation
// POST /api/revalidate
// { "path": "/products/123" }
```

**Advantages:**
- ✅ Fast like SSG (CDN cached)
- ✅ Content stays fresh
- ✅ No full rebuild needed
- ✅ Scales well
- ✅ Automatic or on-demand updates

**Disadvantages:**
- ❌ Slight delay for first user after revalidation
- ❌ More complex than pure SSG
- ❌ Cache invalidation complexity

**Use ISR when:**
- E-commerce product pages
- News articles
- Blog posts that get updated
- Any content that changes periodically
- Large sites (thousands of pages)

**Revalidation Strategies:**
```javascript
// Time-based revalidation
export const revalidate = 3600; // 1 hour

// Tag-based revalidation (better control)
export default async function Page() {
  const data = await fetch('https://api.example.com/data', {
    next: { tags: ['products'] }
  });
  
  // Later: revalidateTag('products')
}

// Path-based revalidation
// revalidatePath('/products/123')

// Revalidate everything
// revalidatePath('/', 'layout')
```

### Q5: What are React Server Components?

**Answer:**
React Server Components are a new type of component that runs **only on the server**, never sent to the client as JavaScript.

**Key Differences:**

| Feature | Server Components | Client Components |
|---------|------------------|-------------------|
| Where runs | Server only | Server + Client |
| JavaScript bundle | Not included | Included |
| Can use hooks | ❌ No | ✅ Yes |
| Can access server directly | ✅ Yes | ❌ No |
| Can use browser APIs | ❌ No | ✅ Yes |
| Event handlers | ❌ No | ✅ Yes |

**Server Component (Default in App Router):**
```javascript
// app/users/page.js
// No 'use client' = Server Component
import { db } from '@/lib/database';

export default async function UsersPage() {
  // Direct database access! No API route needed
  const users = await db.user.findMany();
  
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <UserCard key={user.id} user={user} />
      ))}
    </div>
  );
}
```

**Client Component:**
```javascript
// components/Counter.js
'use client'; // Required!

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

**Composing Server and Client:**
```javascript
// app/page.js (Server Component)
import Counter from '@/components/Counter'; // Client Component
import { db } from '@/lib/database';

export default async function HomePage() {
  const stats = await db.getStats(); // Server-only code
  
  return (
    <div>
      <h1>Stats: {stats.total}</h1>
      {/* Embed client component for interactivity */}
      <Counter initialCount={stats.total} />
    </div>
  );
}
```

**Benefits of Server Components:**
- **Smaller bundles** - Server code not sent to browser
- **Direct backend access** - Database, filesystem, etc.
- **Better security** - API keys, secrets stay on server
- **Improved performance** - Less JavaScript to parse
- **Automatic code splitting** - Per component

**When to use Server Components:**
- Fetching data
- Accessing backend directly
- Rendering static content
- Using large libraries (not sent to client)

**When to use Client Components:**
- Need interactivity (onClick, onChange)
- Use React hooks (useState, useEffect)
- Use browser APIs (localStorage, window)
- Use third-party libraries that need browser

### Q6: What is the difference between Client and Server Components?

**Answer:**
This is covered in Q5, but here's a quick comparison chart:

**Server Components:**
```javascript
// Default in App Router
export default async function ServerComp() {
  const data = await fetchFromDB(); // ✅ Can do this
  return <div>{data}</div>;
}
```

**What you CAN'T do:**
```javascript
// ❌ No hooks
const [state, setState] = useState();
useEffect(() => {}, []);

// ❌ No event handlers  
<button onClick={handleClick}>

// ❌ No browser APIs
localStorage.getItem('key');
window.addEventListener('scroll');
```

**Client Components:**
```javascript
'use client';

export default function ClientComp() {
  const [state, setState] = useState(); // ✅ Hooks work
  
  return (
    <button onClick={() => setState('new')}> {/* ✅ Events work */}
      Click me
    </button>
  );
}
```

**What you CAN'T do:**
```javascript
// ❌ No direct server access
const data = await db.query(); // Won't work

// ❌ Can't access filesystem
const file = fs.readFileSync();

// ❌ Can't use server-only libraries
```

**Best Practice - Start Server, Add Client:**
```javascript
// app/dashboard/page.js (Server)
import ClientWidget from './ClientWidget';

export default async function Dashboard() {
  const data = await fetchData(); // Server
  
  return (
    <div>
      <h1>Dashboard</h1>
      <StaticChart data={data} /> {/* Server Component */}
      <ClientWidget data={data} /> {/* Client Component for interactivity */}
    </div>
  );
}
```

---

## React 18+ Features

### Q7: What is Concurrent Rendering in React 18?

**Answer:**
Concurrent rendering allows React to work on multiple tasks at once and pause/resume work based on priority.

**Before React 18 (Blocking Rendering):**
```
User types → React starts rendering → UI freezes → Render completes → UI updates
(Everything blocks until done)
```

**React 18 (Concurrent Rendering):**
```
User types → React starts rendering → User types again → React pauses, handles input → Continues render
(Urgent updates interrupt non-urgent work)
```

**Key Features:**

**1. Automatic Batching:**
```javascript
// React 17 - Multiple renders
setTimeout(() => {
  setCount(c => c + 1);  // Render 1
  setFlag(f => !f);      // Render 2
  setValue(v => v * 2);  // Render 3
}, 1000);

// React 18 - Single render (automatic)
setTimeout(() => {
  setCount(c => c + 1);  // 
  setFlag(f => !f);      // All batched together!
  setValue(v => v * 2);  // 
}, 1000);
```

**2. Transitions (mark non-urgent updates):**
```javascript
import { useTransition } from 'react';

function SearchPage() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();
  
  const handleSearch = (e) => {
    const value = e.target.value;
    
    // Urgent: Update input immediately (don't block typing)
    setQuery(value);
    
    // Non-urgent: Can be interrupted
    startTransition(() => {
      const searchResults = performExpensiveSearch(value);
      setResults(searchResults);
    });
  };
  
  return (
    <>
      <input value={query} onChange={handleSearch} />
      {isPending && <Spinner />}
      <Results data={results} />
    </>
  );
}
```

**3. Suspense for Data Fetching:**
```javascript
import { Suspense } from 'react';

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <UserProfile /> {/* Can suspend while loading */}
    </Suspense>
  );
}
```

**4. useDeferredValue:**
```javascript
function SearchResults({ query }) {
  // Deferred value lags behind query during fast typing
  const deferredQuery = useDeferredValue(query);
  
  const results = useMemo(() => {
    return expensiveSearch(deferredQuery);
  }, [deferredQuery]);
  
  return (
    <div style={{ opacity: query !== deferredQuery ? 0.5 : 1 }}>
      {results.map(r => <Result key={r.id} {...r} />)}
    </div>
  );
}
```

**Benefits:**
- UI stays responsive during heavy renders
- Better perceived performance
- Smooth animations
- No janky scrolling

### Q8: What is Automatic Batching in React 18?

**Answer:**
Automatic batching groups multiple state updates into a single re-render for better performance.

**React 17 - Limited Batching:**
```javascript
function Component() {
  const [count, setCount] = useState(0);
  const [flag, setFlag] = useState(false);
  
  // ✅ Batched (in event handler)
  function handleClick() {
    setCount(c => c + 1);  // 
    setFlag(f => !f);      // Single render
  }
  
  // ❌ NOT batched (in setTimeout, Promise, etc.)
  function handleAsync() {
    setTimeout(() => {
      setCount(c => c + 1);  // Render 1
      setFlag(f => !f);      // Render 2
    }, 1000);
  }
  
  // ❌ NOT batched (in native event)
  useEffect(() => {
    element.addEventListener('click', () => {
      setCount(c => c + 1);  // Render 1
      setFlag(f => !f);      // Render 2
    });
  }, []);
}
```

**React 18 - Automatic Batching:**
```javascript
function Component() {
  const [count, setCount] = useState(0);
  const [flag, setFlag] = useState(false);
  
  // ✅ Batched everywhere!
  function handleAsync() {
    setTimeout(() => {
      setCount(c => c + 1);  // 
      setFlag(f => !f);      // Single render
    }, 1000);
  }
  
  // ✅ Batched in promises
  async function handlePromise() {
    await fetchData();
    setCount(c => c + 1);  // 
    setFlag(f => !f);      // Single render
  }
  
  // ✅ Batched in native events
  useEffect(() => {
    element.addEventListener('click', () => {
      setCount(c => c + 1);  // 
      setFlag(f => !f);      // Single render
    });
  }, []);
}
```

**Opt-out if needed:**
```javascript
import { flushSync } from 'react-dom';

function handleClick() {
  flushSync(() => {
    setCount(c => c + 1); // Render immediately
  });
  
  flushSync(() => {
    setFlag(f => !f); // Render immediately
  });
  // Two separate renders
}
```

**Benefits:**
- Fewer renders = better performance
- Less wasted work
- Consistent behavior everywhere

### Q9: What is the use() Hook in React?

**Answer:**
The `use()` Hook (React 18.3+) lets you read the value of a Promise or Context.

**Reading Promises:**
```javascript
import { use, Suspense } from 'react';

// Data fetching function that returns a Promise
async function fetchUser(userId) {
  const res = await fetch(`/api/users/${userId}`);
  return res.json();
}

function UserProfile({ userPromise }) {
  // use() unwraps the promise
  const user = use(userPromise);
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}

// Usage with Suspense
function App() {
  const userPromise = fetchUser(123);
  
  return (
    <Suspense fallback={<Loading />}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}
```

**Reading Context:**
```javascript
import { use } from 'react';

const ThemeContext = createContext('light');

function Button() {
  // Alternative to useContext
  const theme = use(ThemeContext);
  
  return <button className={theme}>Click me</button>;
}
```

**Key Difference from other Hooks:**
```javascript
// use() can be called conditionally!
function Component({ promise }) {
  let data;
  
  if (promise) {
    data = use(promise); // ✅ Conditional is OK!
  }
  
  // Other hooks can't be conditional
  if (condition) {
    const [state, setState] = useState(); // ❌ Error!
  }
  
  return <div>{data}</div>;
}
```

**Use cases:**
- Simplified data fetching with Suspense
- Conditional promise resolution
- Alternative to useContext

### Q10: What are the latest features in React 18+?

**Answer:**

**React 18.0 (March 2022):**

**1. Concurrent Rendering**
```javascript
// createRoot enables concurrent features
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

**2. Automatic Batching**
```javascript
// All updates batched, even in async
setTimeout(() => {
  setCount(1);
  setFlag(true);  // Single render
}, 1000);
```

**3. Transitions**
```javascript
const [isPending, startTransition] = useTransition();

startTransition(() => {
  setResults(expensiveOperation()); // Non-urgent
});
```

**4. Suspense for Data Fetching**
```javascript
<Suspense fallback={<Spinner />}>
  <AsyncComponent />
</Suspense>
```

**5. useDeferredValue**
```javascript
const deferredValue = useDeferredValue(value);
```

**6. useId**
```javascript
const id = useId(); // Unique ID for accessibility
```

**7. useSyncExternalStore**
```javascript
// For external store subscriptions
const state = useSyncExternalStore(subscribe, getSnapshot);
```

**React 18.3 (April 2024):**

**8. use() Hook**
```javascript
const user = use(userPromise); // Unwrap promises
const theme = use(ThemeContext); // Read context
```

**React 19 (Coming Soon):**

**9. Server Actions (Stable)**
```javascript
'use server';

export async function createUser(formData) {
  await db.user.create({...});
}
```

**10. Asset Loading**
```javascript
// Preload resources
import { preload, preinit } from 'react-dom';

preload('/font.woff2', { as: 'font' });
preinit('/script.js', { as: 'script' });
```

**11. Document Metadata**
```javascript
function Page() {
  return (
    <>
      <title>My Page</title>
      <meta name="description" content="..." />
      <h1>Content</h1>
    </>
  );
}
```

**12. useFormStatus**
```javascript
import { useFormStatus } from 'react-dom';

function SubmitButton() {
  const { pending } = useFormStatus();
  return <button disabled={pending}>Submit</button>;
}
```

**13. useOptimistic**
```javascript
const [optimisticState, addOptimistic] = useOptimistic(
  currentState,
  (state, newValue) => [...state, newValue]
);
```

**Summary - When to Upgrade:**
- React 18: Required for Next.js 13+ App Router
- React 18.3: If you want use() Hook
- React 19: When stable (Q2 2024+)

---

## Core Concepts & Rendering

### Q11: Explain the different rendering strategies in Next.js and when to use each.

**Answer:**

Think of rendering strategies as different ways to prepare a meal for a restaurant:

**1. Static Site Generation (SSG):**
Like meal prep - cook everything in advance.

```javascript
// Page is built at BUILD TIME
// Perfect for content that rarely changes
export async function generateStaticParams() {
  const posts = await getPosts();
  return posts.map((post) => ({ slug: post.slug }));
}

export default async function BlogPost({ params }) {
  const post = await getPost(params.slug);
  return <Article post={post} />;
}
```

**When to use:**
- Marketing pages
- Blog posts
- Documentation
- Product listings (if they don't change often)

**2. Server-Side Rendering (SSR):**
Like cooking to order - fresh for each customer.

```javascript
// App Router - Server Component by default
export const dynamic = 'force-dynamic'; // Force SSR

export default async function Dashboard() {
  const data = await fetchUserData(); // Fresh data every request
  return <DashboardUI data={data} />;
}
```

**When to use:**
- Personalized content
- Real-time data
- SEO-critical pages with dynamic content
- User dashboards

**3. Incremental Static Regeneration (ISR):**
Like buffet service - prepare in advance, refresh periodically.

```javascript
// Revalidate every 60 seconds
export const revalidate = 60;

export default async function ProductPage({ params }) {
  const product = await getProduct(params.id);
  return <Product data={product} />;
}
```

**When to use:**
- E-commerce product pages
- News articles
- Social media feeds
- Any content that updates periodically

**4. Client-Side Rendering (CSR):**
Like a salad bar - customers build their own meal.

```javascript
'use client'; // Mark as Client Component

export default function InteractiveDashboard() {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    fetchData().then(setData);
  }, []);
  
  return <DashboardUI data={data} />;
}
```

**When to use:**
- Highly interactive features
- Private user data (behind auth)
- Real-time updates (WebSocket)
- Components that need browser APIs

---

### Q2: What is the difference between Server Components and Client Components in Next.js 13+?

**Answer:**

**Server Components (Default):**
Run on the server, HTML sent to browser.

```javascript
// app/users/page.tsx
// No 'use client' = Server Component
import { getUsers } from '@/lib/db';

export default async function UsersPage() {
  const users = await getUsers(); // Direct DB access!
  
  return (
    <div>
      {users.map(user => (
        <UserCard key={user.id} user={user} />
      ))}
    </div>
  );
}
```

**Benefits:**
- Access backend directly (database, filesystem)
- Keep sensitive data on server (API keys)
- Smaller JavaScript bundle
- Better initial page load

**Limitations:**
- No hooks (useState, useEffect)
- No browser APIs
- No event handlers

**Client Components:**
Run in the browser, need interactivity.

```javascript
'use client'; // Required!

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

**Benefits:**
- Full React hooks
- Browser APIs (localStorage, etc.)
- Event handlers
- Third-party libraries

**Best Practice - Compose Them:**
```javascript
// Server Component (page)
import ClientInteractiveForm from './ClientForm';

export default async function Page() {
  const data = await fetchData(); // Server-side
  
  return (
    <div>
      <h1>Static Content</h1>
      {/* Client component for interactivity */}
      <ClientInteractiveForm initialData={data} />
    </div>
  );
}
```

**Golden Rule:** Use Server Components by default, add 'use client' only when needed!

---

## App Router vs Pages Router

### Q3: Compare App Router (app/) vs Pages Router (pages/). When should you migrate?

**Answer:**

**Pages Router (Legacy but stable):**
```
pages/
├── index.js          → /
├── about.js          → /about
├── blog/
│   ├── [slug].js     → /blog/:slug
│   └── index.js      → /blog
└── api/
    └── users.js      → /api/users
```

**App Router (Modern, recommended):**
```
app/
├── page.js           → /
├── layout.js         → Root layout
├── about/
│   └── page.js       → /about
├── blog/
│   ├── [slug]/
│   │   └── page.js   → /blog/:slug
│   └── page.js       → /blog
└── api/
    └── users/
        └── route.js  → /api/users
```

**Key Differences:**

| Feature | Pages Router | App Router |
|---------|-------------|------------|
| Default | Client Components | Server Components |
| Layouts | Manual wrapper components | Built-in layout.js |
| Loading States | Manual | loading.js file |
| Error Handling | \_error.js | error.js file |
| Data Fetching | getServerSideProps, getStaticProps | async components |
| Streaming | Limited | Full support |

**App Router Example:**
```javascript
// app/blog/[slug]/page.js
export default async function BlogPost({ params }) {
  const post = await getPost(params.slug); // So simple!
  return <Article post={post} />;
}

// app/blog/[slug]/loading.js
export default function Loading() {
  return <Spinner />;
}

// app/blog/[slug]/error.js
'use client';
export default function Error({ error, reset }) {
  return <ErrorUI error={error} retry={reset} />;
}
```

**When to Migrate:**
- ✅ New projects: Always use App Router
- ✅ Need better performance
- ✅ Want React Server Components
- ✅ Complex layouts and loading states
- ⚠️ Existing large app: Migrate gradually (both can coexist)
- ❌ Small app that works fine: No rush

---

### Q4: How do layouts work in the App Router and what are best practices?

**Answer:**

Layouts wrap pages and persist across navigation (don't re-render).

**Root Layout (Required):**
```javascript
// app/layout.js
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <Header />
        {children}
        <Footer />
      </body>
    </html>
  );
}
```

**Nested Layouts:**
```javascript
// app/dashboard/layout.js
export default function DashboardLayout({ children }) {
  return (
    <div className="dashboard">
      <Sidebar />
      <main>{children}</main>
    </div>
  );
}

// app/dashboard/page.js → Wrapped by dashboard layout + root layout
```

**Layout Hierarchy:**
```
app/
├── layout.js                    (Root: wraps everything)
├── dashboard/
│   ├── layout.js                (Dashboard: wraps dashboard pages)
│   ├── page.js                  → / + Root + Dashboard layouts
│   └── settings/
│       ├── layout.js            (Settings: wraps settings pages)
│       └── page.js              → Root + Dashboard + Settings layouts
```

**Best Practices:**

**1. Keep Layouts Server Components:**
```javascript
// ✅ Good: Server component layout
export default function Layout({ children }) {
  return <div className="layout">{children}</div>;
}

// ❌ Avoid: Making entire layout client-side
'use client';
export default function Layout({ children }) { ... }
```

**2. Fetch Data in Layouts:**
```javascript
// Layouts can fetch data!
export default async function DashboardLayout({ children }) {
  const user = await getCurrentUser();
  
  return (
    <div>
      <UserProfile user={user} />
      {children}
    </div>
  );
}
```

**3. Use Template for Animations:**
```javascript
// template.js - Remounts on navigation (for animations)
'use client';
import { motion } from 'framer-motion';

export default function Template({ children }) {
  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
    >
      {children}
    </motion.div>
  );
}
```

---

## Performance & Optimization

### Q5: What are the best practices for optimizing Next.js bundle size and performance?

**Answer:**

**1. Automatic Optimizations (Next.js does for you):**
- Code splitting per route
- Image optimization
- Font optimization
- Script optimization

**2. Dynamic Imports (Manual Code Splitting):**
```javascript
// Don't load heavy components upfront
import dynamic from 'next/dynamic';

const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <Spinner />,
  ssr: false // Don't render on server
});

export default function Dashboard() {
  return (
    <div>
      <HeavyChart data={data} />
    </div>
  );
}
```

**3. Image Optimization:**
```javascript
import Image from 'next/image';

// ✅ Good: Automatic optimization
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority // Load immediately for LCP
  placeholder="blur"
  blurDataURL="data:image/..." // Or generate with plaiceholder
/>

// ❌ Bad: Regular img tag
<img src="/hero.jpg" alt="Hero" />
```

**4. Font Optimization:**
```javascript
// app/layout.js
import { Inter } from 'next/font/google';

const inter = Inter({ 
  subsets: ['latin'],
  display: 'swap' // Show fallback while loading
});

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={inter.className}>
      <body>{children}</body>
    </html>
  );
}
```

**5. Analyze Bundle:**
```bash
# Install analyzer
npm install @next/bundle-analyzer

# next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer({
  // config
});

# Run
ANALYZE=true npm run build
```

**6. Use React Server Components:**
```javascript
// Instead of shipping React component to client
'use client';
import { HeavyLibrary } from 'heavy-library'; // 500KB!

// Keep it on server (0KB to client)
import { HeavyLibrary } from 'heavy-library';
export default async function Page() {
  const data = HeavyLibrary.process();
  return <div>{data}</div>;
}
```

**7. Streaming & Suspense:**
```javascript
import { Suspense } from 'react';

export default function Page() {
  return (
    <div>
      <Header /> {/* Instant */}
      <Suspense fallback={<Spinner />}>
        <SlowComponent /> {/* Streams in when ready */}
      </Suspense>
    </div>
  );
}
```

---

### Q6: How do you implement caching strategies in Next.js?

**Answer:**

Next.js has multiple caching layers:

**1. Full Route Cache (Static Pages):**
```javascript
// Cached at build time
export default async function StaticPage() {
  const data = await fetchData();
  return <div>{data}</div>;
}
```

**2. Request Memoization (Automatic):**
```javascript
// Same fetch in one render = deduped automatically
async function Header() {
  const user = await fetch('/api/user').then(r => r.json());
  return <div>{user.name}</div>;
}

async function Sidebar() {
  const user = await fetch('/api/user').then(r => r.json()); // Reuses!
  return <nav>{user.role}</nav>;
}
```

**3. Data Cache (Persistent):**
```javascript
// Default: cached forever
const data = await fetch('https://api.example.com/data');

// Revalidate after 60 seconds
const data = await fetch('https://api.example.com/data', {
  next: { revalidate: 60 }
});

// Never cache
const data = await fetch('https://api.example.com/data', {
  cache: 'no-store'
});

// Revalidate on-demand
const data = await fetch('https://api.example.com/data', {
  next: { tags: ['products'] }
});

// Later, trigger revalidation:
// app/api/revalidate/route.js
import { revalidateTag } from 'next/cache';

export async function POST() {
  revalidateTag('products');
  return Response.json({ revalidated: true });
}
```

**4. Router Cache (Client-side):**
Automatically caches visited routes for 30s.

**5. Redis/Custom Cache:**
```javascript
import { unstable_cache } from 'next/cache';

// Cache expensive computation
const getCachedData = unstable_cache(
  async (id) => {
    return await expensiveOperation(id);
  },
  ['data-cache'], // cache key
  {
    revalidate: 3600, // 1 hour
    tags: ['data']
  }
);

export default async function Page({ params }) {
  const data = await getCachedData(params.id);
  return <div>{data}</div>;
}
```

**Cache Control Strategy:**
```
Homepage → Static (ISR: 60s)
Product List → ISR (10 minutes)
Product Detail → ISR (5 minutes) + on-demand revalidation
User Dashboard → No cache (dynamic)
API Routes → Custom cache headers
```

---

## Data Fetching

### Q7: What are the different data fetching patterns in Next.js and when to use each?

**Answer:**

**1. Server Components (Preferred):**
```javascript
// Direct async/await in components!
export default async function ProductPage({ params }) {
  // Parallel fetching
  const [product, reviews] = await Promise.all([
    getProduct(params.id),
    getReviews(params.id)
  ]);
  
  return (
    <div>
      <ProductDetails product={product} />
      <Reviews reviews={reviews} />
    </div>
  );
}
```

**2. Sequential Fetching (When Needed):**
```javascript
// Second fetch depends on first
export default async function Page({ params }) {
  const user = await getUser(params.id);
  const posts = await getUserPosts(user.id); // Needs user.id
  
  return <UserProfile user={user} posts={posts} />;
}
```

**3. Streaming with Suspense:**
```javascript
import { Suspense } from 'react';

async function FastComponent() {
  const data = await fetchFast();
  return <div>{data}</div>;
}

async function SlowComponent() {
  const data = await fetchSlow(); // Takes 3 seconds
  return <div>{data}</div>;
}

export default function Page() {
  return (
    <div>
      <FastComponent /> {/* Shows immediately */}
      <Suspense fallback={<Spinner />}>
        <SlowComponent /> {/* Streams in later */}
      </Suspense>
    </div>
  );
}
```

**4. Client-Side Fetching (When Needed):**
```javascript
'use client';

import useSWR from 'swr';

export default function LiveData() {
  const { data, error } = useSWR('/api/live', fetcher, {
    refreshInterval: 1000 // Real-time updates
  });
  
  if (error) return <div>Error</div>;
  if (!data) return <Spinner />;
  return <div>{data}</div>;
}
```

**5. Server Actions (Mutations):**
```javascript
// app/actions.js
'use server';

export async function createPost(formData) {
  const title = formData.get('title');
  const content = formData.get('content');
  
  await db.post.create({ title, content });
  revalidatePath('/posts');
}

// app/new-post/page.js
import { createPost } from './actions';

export default function NewPost() {
  return (
    <form action={createPost}>
      <input name="title" />
      <textarea name="content" />
      <button type="submit">Create</button>
    </form>
  );
}
```

**Decision Matrix:**
```
Need real-time? → Client-side (SWR/React Query)
Initial page load important? → Server Component
Need SEO? → Server Component
User interaction/mutation? → Server Action
Complex client state? → Client Component + React Query
```

---

### Q8: How do you handle authentication and protected routes in Next.js?

**Answer:**

**Modern Approach with Middleware:**

**1. Setup Middleware:**
```javascript
// middleware.js
import { NextResponse } from 'next/server';
import { verifyToken } from './lib/auth';

export async function middleware(request) {
  const token = request.cookies.get('auth-token')?.value;
  
  // Protected routes
  if (request.nextUrl.pathname.startsWith('/dashboard')) {
    if (!token || !await verifyToken(token)) {
      return NextResponse.redirect(new URL('/login', request.url));
    }
  }
  
  // Redirect logged-in users from login page
  if (request.nextUrl.pathname === '/login' && token) {
    return NextResponse.redirect(new URL('/dashboard', request.url));
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/login']
};
```

**2. Server-Side Auth Check:**
```javascript
// app/dashboard/page.js
import { getServerSession } from 'next-auth';
import { redirect } from 'next/navigation';

export default async function Dashboard() {
  const session = await getServerSession();
  
  if (!session) {
    redirect('/login');
  }
  
  return <DashboardUI user={session.user} />;
}
```

**3. Using NextAuth.js:**
```javascript
// app/api/auth/[...nextauth]/route.js
import NextAuth from 'next-auth';
import GithubProvider from 'next-auth/providers/github';
import CredentialsProvider from 'next-auth/providers/credentials';

export const authOptions = {
  providers: [
    GithubProvider({
      clientId: process.env.GITHUB_ID,
      clientSecret: process.env.GITHUB_SECRET,
    }),
    CredentialsProvider({
      async authorize(credentials) {
        const user = await verifyCredentials(credentials);
        return user || null;
      }
    })
  ],
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.role = user.role;
      }
      return token;
    },
    async session({ session, token }) {
      session.user.role = token.role;
      return session;
    }
  }
};

const handler = NextAuth(authOptions);
export { handler as GET, handler as POST };

// app/dashboard/page.js
import { getServerSession } from 'next-auth';
import { authOptions } from '../api/auth/[...nextauth]/route';

export default async function Dashboard() {
  const session = await getServerSession(authOptions);
  return <div>Welcome {session.user.name}</div>;
}
```

**4. Client-Side Session:**
```javascript
// app/providers.js
'use client';

import { SessionProvider } from 'next-auth/react';

export function Providers({ children }) {
  return <SessionProvider>{children}</SessionProvider>;
}

// app/layout.js
import { Providers } from './providers';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}

// Any client component
'use client';
import { useSession, signIn, signOut } from 'next-auth/react';

export default function Component() {
  const { data: session, status } = useSession();
  
  if (status === 'loading') return <Spinner />;
  if (status === 'unauthenticated') return <button onClick={() => signIn()}>Sign in</button>;
  
  return (
    <div>
      <p>Welcome {session.user.name}</p>
      <button onClick={() => signOut()}>Sign out</button>
    </div>
  );
}
```

---

## Deployment & Production

### Q9: What are the best practices for deploying and monitoring Next.js in production?

**Answer:**

**1. Environment Variables:**
```javascript
// .env.local (not committed)
DATABASE_URL=postgresql://...
API_SECRET=secret123

// .env.production (for build-time vars)
NEXT_PUBLIC_API_URL=https://api.production.com

// Access in code
const apiUrl = process.env.NEXT_PUBLIC_API_URL; // Client-side
const dbUrl = process.env.DATABASE_URL; // Server-side only
```

**2. Deployment Platforms:**

**Vercel (Easiest):**
```bash
# Connect GitHub repo → Auto deploy on push
# Or use CLI
npx vercel
```

**Docker (Self-hosted):**
```dockerfile
# Dockerfile
FROM node:18-alpine AS base

FROM base AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV production

COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000
CMD ["node", "server.js"]
```

```javascript
// next.config.js
module.exports = {
  output: 'standalone', // For Docker
};
```

**3. Monitoring & Error Tracking:**
```javascript
// Install Sentry
npm install @sentry/nextjs

// sentry.client.config.js
import * as Sentry from '@sentry/nextjs';

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  tracesSampleRate: 1.0,
});

// sentry.server.config.js
import * as Sentry from '@sentry/nextjs';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampleRate: 1.0,
});

// Automatically catches errors in app
```

**4. Performance Monitoring:**
```javascript
// app/layout.js
import { Analytics } from '@vercel/analytics/react';
import { SpeedInsights } from '@vercel/speed-insights/next';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
        <SpeedInsights />
      </body>
    </html>
  );
}
```

**5. Security Headers:**
```javascript
// next.config.js
module.exports = {
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          {
            key: 'X-DNS-Prefetch-Control',
            value: 'on'
          },
          {
            key: 'Strict-Transport-Security',
            value: 'max-age=63072000; includeSubDomains; preload'
          },
          {
            key: 'X-Frame-Options',
            value: 'SAMEORIGIN'
          },
          {
            key: 'X-Content-Type-Options',
            value: 'nosniff'
          },
          {
            key: 'Referrer-Policy',
            value: 'origin-when-cross-origin'
          }
        ]
      }
    ];
  }
};
```

**6. Health Checks:**
```javascript
// app/api/health/route.js
export async function GET() {
  try {
    // Check database connection
    await db.$queryRaw`SELECT 1`;
    
    return Response.json({ 
      status: 'healthy',
      timestamp: new Date().toISOString()
    });
  } catch (error) {
    return Response.json(
      { status: 'unhealthy', error: error.message },
      { status: 503 }
    );
  }
}
```

---

## Architecture Patterns

### Q10: How do you structure a large-scale Next.js application?

**Answer:**

**Folder Structure:**
```
app/
├── (marketing)/          # Route group (doesn't affect URL)
│   ├── layout.js         # Marketing layout
│   ├── page.js           # Homepage
│   ├── about/
│   └── pricing/
├── (app)/                # App route group
│   ├── layout.js         # App layout (with sidebar)
│   ├── dashboard/
│   │   ├── page.js
│   │   ├── loading.js
│   │   └── error.js
│   └── settings/
├── api/                  # API routes
│   ├── users/
│   └── products/
└── _components/          # Shared components (not a route)
    ├── ui/
    └── forms/

lib/                      # Utilities
├── db.js
├── auth.js
└── utils.js

features/                 # Feature-based modules
├── auth/
│   ├── components/
│   ├── actions.js
│   └── utils.js
└── products/
    ├── components/
    ├── actions.js
    └── utils.js
```

**Key Patterns:**

**1. Route Groups for Different Layouts:**
```
(marketing)/    → Marketing layout (header, footer)
(app)/          → App layout (sidebar, dashboard)
(admin)/        → Admin layout (admin nav)
```

**2. Server Actions for Mutations:**
```javascript
// features/posts/actions.js
'use server';

import { revalidatePath } from 'next/cache';
import { db } from '@/lib/db';

export async function createPost(data) {
  const post = await db.post.create(data);
  revalidatePath('/posts');
  return post;
}

export async function updatePost(id, data) {
  const post = await db.post.update({ where: { id }, data });
  revalidatePath(`/posts/${id}`);
  return post;
}
```

**3. Parallel Routes for Complex UIs:**
```
app/
├── @modal/              # Modal slot
│   ├── (..)photo/[id]/  # Intercept route
│   │   └── page.js
│   └── default.js
├── @sidebar/            # Sidebar slot
│   └── page.js
└── layout.js

// layout.js
export default function Layout({ children, modal, sidebar }) {
  return (
    <div>
      {sidebar}
      {children}
      {modal}
    </div>
  );
}
```

**4. API Routes Best Practices:**
```javascript
// app/api/users/route.js
import { NextResponse } from 'next/server';
import { z } from 'zod';

const userSchema = z.object({
  name: z.string().min(1),
  email: z.string().email()
});

export async function POST(request) {
  try {
    const body = await request.json();
    const data = userSchema.parse(body);
    
    const user = await createUser(data);
    
    return NextResponse.json(user, { status: 201 });
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json(
        { error: 'Validation error', issues: error.issues },
        { status: 400 }
      );
    }
    
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

**5. Metadata for SEO:**
```javascript
// Static metadata
export const metadata = {
  title: 'My App',
  description: 'Description'
};

// Dynamic metadata
export async function generateMetadata({ params }) {
  const product = await getProduct(params.id);
  
  return {
    title: product.name,
    description: product.description,
    openGraph: {
      images: [product.image]
    }
  };
}
```

---

## Summary

**Next.js Best Practices:**

1. **Use App Router** for new projects
2. **Server Components by default**, add 'use client' only when needed
3. **Implement proper caching** strategy (ISR, dynamic, static)
4. **Optimize images, fonts, and bundles** automatically
5. **Use Server Actions** for mutations instead of API routes
6. **Implement middleware** for auth and redirects
7. **Structure by features** for large apps
8. **Monitor performance** with analytics and error tracking
9. **Use TypeScript** for type safety
10. **Implement proper SEO** with metadata

**Remember:** Next.js gives you the tools, but you decide the strategy based on your use case!

---

## Pages Router Data Fetching

### Q21: What is getStaticProps and when should you use it?

**Answer:**
`getStaticProps` fetches data at build time for static page generation.

```javascript
// pages/blog/[slug].js
export async function getStaticProps(context) {
  const { params, preview, previewData, locale } = context;
  
  // Fetch data at build time
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(r => r.json());
  
  // Check if post exists
  if (!post) {
    return {
      notFound: true // Show 404 page
    };
  }
  
  return {
    props: {
      post
    },
    revalidate: 60 // ISR: revalidate every 60 seconds
  };
}

export default function BlogPost({ post }) {
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

**Return options:**
```javascript
return {
  props: { data },           // Pass to page component
  revalidate: 60,            // Enable ISR (seconds)
  notFound: true,            // Show 404
  redirect: {                // Redirect to another page
    destination: '/new-url',
    permanent: false
  }
};
```

**When to use:**
- Content rarely changes
- Can be pre-rendered at build time
- Same content for all users
- SEO important

### Q22: What is getServerSideProps and when should you use it?

**Answer:**
`getServerSideProps` fetches data on every request at runtime.

```javascript
// pages/dashboard.js
export async function getServerSideProps(context) {
  const { req, res, params, query } = context;
  
  // Check authentication
  const session = await getSession(context);
  
  if (!session) {
    return {
      redirect: {
        destination: '/login',
        permanent: false
      }
    };
  }
  
  // Fetch user-specific data
  const userData = await fetchUserData(session.userId);
  
  // Set cache headers
  res.setHeader(
    'Cache-Control',
    'public, s-maxage=10, stale-while-revalidate=59'
  );
  
  return {
    props: {
      user: userData
    }
  };
}

export default function Dashboard({ user }) {
  return (
    <div>
      <h1>Welcome, {user.name}</h1>
      <DashboardContent user={user} />
    </div>
  );
}
```

**When to use:**
- Data changes frequently
- Personalized content per user
- Need request object (cookies, headers)
- Real-time data required

**Key differences from getStaticProps:**
- Runs on every request (slower)
- Can't be cached by CDN
- Has access to request/response
- More server resources needed

### Q23: What is getStaticPaths and how does it work?

**Answer:**
`getStaticPaths` specifies which dynamic routes to pre-render at build time.

```javascript
// pages/products/[id].js
export async function getStaticPaths() {
  // Fetch list of all product IDs
  const products = await fetch('https://api.example.com/products')
    .then(r => r.json());
  
  // Generate paths for all products
  const paths = products.map(product => ({
    params: { id: product.id.toString() }
  }));
  
  return {
    paths,
    fallback: 'blocking' // or false, true
  };
}

export async function getStaticProps({ params }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(r => r.json());
  
  return {
    props: { product },
    revalidate: 3600 // 1 hour
  };
}

export default function ProductPage({ product }) {
  return <ProductDetails product={product} />;
}
```

**Fallback options:**

**fallback: false**
```javascript
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: false // Only these paths exist, others = 404
};
```

**fallback: true**
```javascript
return {
  paths: [{ params: { id: '1' } }], // Pre-render only ID 1
  fallback: true // Generate others on-demand
};

// Must handle loading state
export default function ProductPage({ product }) {
  const router = useRouter();
  
  if (router.isFallback) {
    return <LoadingSpinner />;
  }
  
  return <ProductDetails product={product} />;
}
```

**fallback: 'blocking'**
```javascript
return {
  paths: [],
  fallback: 'blocking' // Wait for page generation, no loading state needed
};

// No loading state needed - page waits on server
export default function ProductPage({ product }) {
  return <ProductDetails product={product} />;
}
```

**When to use each:**
- `false`: Small number of pages, all known at build time
- `true`: Large catalog, want fast builds, can show loading
- `'blocking'`: Large catalog, prefer server wait over loading UI

### Q24: What is the difference between Next.js and Create React App?

**Answer:**

| Feature | Next.js | Create React App |
|---------|---------|------------------|
| Rendering | SSR, SSG, ISR, CSR | CSR only |
| Routing | File-based, built-in | Need React Router |
| SEO | Excellent (SSR/SSG) | Poor (CSR) |
| Performance | Optimized by default | Manual optimization |
| Backend | API routes included | Need separate server |
| Image Optimization | Built-in (next/image) | Manual |
| Code Splitting | Automatic | Manual (lazy) |
| TypeScript | Zero config | Zero config |
| Deployment | Optimized (Vercel) | Static hosting |

**Create React App:**
```javascript
// All routes load at once
// JavaScript executes to render content
// Search engines see empty HTML
// Slower initial load
// Need separate backend
```

**Next.js:**
```javascript
// Routes loaded on demand
// HTML sent pre-rendered
// Search engines see full content
// Faster perceived performance
// Backend in same codebase
```

**Choose CRA when:**
- Building internal tools (no SEO needed)
- Everything is behind authentication
- Simple SPA with no server requirements

**Choose Next.js when:**
- SEO is important
- Need fast initial page load
- Want server-side rendering
- Need API routes
- Building production web apps

---

## Routing & Navigation

### Q25: How to create dynamic routes in Next.js?

**Answer:**

**Single dynamic route:**
```javascript
// pages/products/[id].js → /products/1, /products/2
export default function Product({ params }) {
  return <div>Product ID: {params.id}</div>;
}
```

**Multiple dynamic segments:**
```javascript
// pages/blog/[year]/[month]/[slug].js → /blog/2024/01/my-post
export default function BlogPost({ params }) {
  const { year, month, slug } = params;
  return <div>Post: {slug} from {month}/{year}</div>;
}
```

**Catch-all routes:**
```javascript
// pages/docs/[...slug].js → /docs/a, /docs/a/b, /docs/a/b/c
export default function Docs({ params }) {
  const segments = params.slug; // ['a', 'b', 'c']
  return <div>Path: {segments.join('/')}</div>;
}
```

**Optional catch-all:**
```javascript
// pages/shop/[[...slug]].js → /shop, /shop/a, /shop/a/b
export default function Shop({ params }) {
  const segments = params.slug || []; // May be undefined
  return <div>Shop: {segments.join('/')}</div>;
}
```

**App Router:**
```javascript
// app/products/[id]/page.js
export default function ProductPage({ params }) {
  return <div>Product: {params.id}</div>;
}

// app/blog/[...slug]/page.js (catch-all)
export default function BlogPage({ params }) {
  return <div>Path: {params.slug.join('/')}</div>;
}
```

### Q26: What is the Link component and how is it different from anchor tags?

**Answer:**

**Regular anchor tag (❌ Don't use for internal links):**
```javascript
<a href="/about">About</a>
// Problems:
// - Full page reload
// - Loses client-side state
// - Slower navigation
// - No prefetching
```

**Next.js Link (✅ Use for internal navigation):**
```javascript
import Link from 'next/link';

<Link href="/about">About</Link>
// Benefits:
// - Client-side navigation
// - Preserves state
// - Instant navigation
// - Automatic prefetching
```

**Advanced Link usage:**
```javascript
// With query params
<Link href={{ pathname: '/products', query: { category: 'shoes' } }}>
  Shoes
</Link>

// Dynamic route
<Link href={`/products/${product.id}`}>
  {product.name}
</Link>

// External link (use anchor tag)
<a href="https://external.com" target="_blank" rel="noopener noreferrer">
  External Site
</a>

// Disable prefetch
<Link href="/about" prefetch={false}>
  About
</Link>

// Replace instead of push
<Link href="/login" replace>
  Login
</Link>

// Custom child component
<Link href="/profile" legacyBehavior>
  <a className="custom-link">Profile</a>
</Link>
```

**Prefetching behavior:**
```javascript
// Production: Links in viewport are prefetched automatically
// Development: Manual hover/focus triggers prefetch

// Disable globally
// next.config.js
module.exports = {
  experimental: {
    optimizeCssLoading: true,
    prefetch: false // Disable prefetch
  }
};
```

### Q27: What is the useRouter hook?

**Answer:**
`useRouter` provides access to router object for navigation and route information.

```javascript
'use client'; // Required in App Router

import { useRouter, usePathname, useSearchParams } from 'next/navigation';

export default function Component() {
  const router = useRouter();
  const pathname = usePathname(); // /dashboard/settings
  const searchParams = useSearchParams(); // ?tab=profile
  
  // Programmatic navigation
  const navigate = () => {
    router.push('/about'); // Navigate and add to history
    router.replace('/login'); // Navigate and replace history
    router.back(); // Go back
    router.forward(); // Go forward
    router.refresh(); // Refresh current route
  };
  
  // Read route info
  const tab = searchParams.get('tab'); // 'profile'
  const currentPath = pathname; // '/dashboard/settings'
  
  return (
    <div>
      <p>Current path: {pathname}</p>
      <p>Tab: {tab}</p>
      <button onClick={() => router.push('/new-page')}>
        Navigate
      </button>
    </div>
  );
}
```

**Pages Router useRouter:**
```javascript
import { useRouter } from 'next/router';

export default function Page() {
  const router = useRouter();
  
  // Route info
  const { pathname, query, asPath, locale } = router;
  
  // query: { id: '123', tab: 'profile' }
  // pathname: /products/[id]
  // asPath: /products/123?tab=profile
  
  // Navigation
  router.push('/about');
  router.push({
    pathname: '/products/[id]',
    query: { id: '123' }
  });
  
  // Shallow routing (update URL without re-running data fetching)
  router.push('/about?counter=10', undefined, { shallow: true });
  
  // Check if ready
  if (!router.isReady) return <div>Loading...</div>;
  
  return <div>Product: {router.query.id}</div>;
}
```

### Q28: What are API Routes in Next.js?

**Answer:**
API Routes let you build backend APIs inside your Next.js app.

**Pages Router:**
```javascript
// pages/api/users.js → /api/users
export default function handler(req, res) {
  if (req.method === 'GET') {
    const users = [
      { id: 1, name: 'John' },
      { id: 2, name: 'Jane' }
    ];
    res.status(200).json(users);
  } else if (req.method === 'POST') {
    const newUser = req.body;
    // Save to database
    res.status(201).json({ id: 3, ...newUser });
  } else {
    res.status(405).json({ error: 'Method not allowed' });
  }
}
```

**Dynamic API routes:**
```javascript
// pages/api/users/[id].js → /api/users/123
export default async function handler(req, res) {
  const { id } = req.query;
  
  if (req.method === 'GET') {
    const user = await db.user.findUnique({ where: { id } });
    
    if (!user) {
      return res.status(404).json({ error: 'User not found' });
    }
    
    res.status(200).json(user);
  } else if (req.method === 'DELETE') {
    await db.user.delete({ where: { id } });
    res.status(204).end();
  }
}
```

**App Router - Route Handlers:**
```javascript
// app/api/users/route.js → /api/users
import { NextResponse } from 'next/server';

export async function GET(request) {
  const users = await db.user.findMany();
  return NextResponse.json(users);
}

export async function POST(request) {
  const body = await request.json();
  const user = await db.user.create({ data: body });
  return NextResponse.json(user, { status: 201 });
}

// app/api/users/[id]/route.js → /api/users/123
export async function GET(request, { params }) {
  const user = await db.user.findUnique({ 
    where: { id: params.id } 
  });
  
  if (!user) {
    return NextResponse.json(
      { error: 'Not found' },
      { status: 404 }
    );
  }
  
  return NextResponse.json(user);
}

export async function DELETE(request, { params }) {
  await db.user.delete({ where: { id: params.id } });
  return new NextResponse(null, { status: 204 });
}
```

**Advanced features:**
```javascript
// Custom headers
export async function GET() {
  return NextResponse.json({ data: 'value' }, {
    headers: {
      'Cache-Control': 'max-age=3600',
      'X-Custom-Header': 'value'
    }
  });
}

// CORS
export async function OPTIONS(request) {
  return new NextResponse(null, {
    status: 200,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE',
      'Access-Control-Allow-Headers': 'Content-Type'
    }
  });
}

// Cookies
import { cookies } from 'next/headers';

export async function POST(request) {
  cookies().set('session', 'token', { 
    httpOnly: true,
    secure: true,
    maxAge: 3600 
  });
  
  return NextResponse.json({ success: true });
}

// Stream response
export async function GET() {
  const stream = new ReadableStream({
    async start(controller) {
      for (let i = 0; i < 10; i++) {
        controller.enqueue(`Data chunk ${i}\n`);
        await new Promise(r => setTimeout(r, 100));
      }
      controller.close();
    }
  });
  
  return new Response(stream);
}
```

---

## Image Optimization

### Q29: What is next/image and why should you use it?

**Answer:**
`next/image` is Next.js's built-in image optimization component.

**Problems with regular img tag:**
```javascript
// ❌ Regular img
<img src="/hero.jpg" alt="Hero" />

// Problems:
// - No optimization
// - Loads full size image
// - No lazy loading
// - Layout shift (CLS)
// - No modern formats (WebP, AVIF)
// - No responsive images
```

**Next.js Image component:**
```javascript
import Image from 'next/image';

// ✅ Optimized image
<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1200}
  height={600}
  priority // Load immediately for LCP
  placeholder="blur"
  blurDataURL="data:image/..." // Base64 blur
/>

// Benefits:
// ✅ Automatic optimization
// ✅ Lazy loading by default
// ✅ Modern formats (WebP, AVIF)
// ✅ Responsive images
// ✅ Prevents layout shift
// ✅ Serves optimal size
```

**Different layouts:**
```javascript
// Fixed size
<Image
  src="/profile.jpg"
  width={400}
  height={400}
  alt="Profile"
/>

// Fill container (requires parent position: relative)
<div style={{ position: 'relative', width: '100%', height: '400px' }}>
  <Image
    src="/banner.jpg"
    fill
    style={{ objectFit: 'cover' }}
    alt="Banner"
  />
</div>

// Responsive (maintain aspect ratio)
<Image
  src="/product.jpg"
  width={800}
  height={600}
  sizes="(max-width: 768px) 100vw, 50vw"
  alt="Product"
/>
```

**External images:**
```javascript
// next.config.js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'example.com',
        pathname: '/images/**'
      }
    ]
  }
};

// Usage
<Image
  src="https://example.com/images/photo.jpg"
  width={800}
  height={600}
  alt="External"
/>
```

**Priority prop (for LCP):**
```javascript
// Use for above-the-fold images
<Image
  src="/hero.jpg"
  width={1920}
  height={1080}
  priority // Disables lazy loading, loads immediately
  alt="Hero"
/>
```

**Placeholder blur:**
```javascript
import { getPlaiceholder } from 'plaiceholder';

export async function getStaticProps() {
  const { base64, img } = await getPlaiceholder('/image.jpg');
  
  return {
    props: {
      image: { ...img, blurDataURL: base64 }
    }
  };
}

export default function Page({ image }) {
  return (
    <Image
      {...image}
      placeholder="blur"
      blurDataURL={image.blurDataURL}
    />
  );
}
```

**Performance benefits:**
- Automatic format detection (WebP, AVIF)
- Automatic size optimization
- Lazy loading by default
- Prevents Cumulative Layout Shift (CLS)
- Image CDN optimization

---

## Middleware

### Q30: What is Middleware in Next.js and what can you do with it?

**Answer:**
Middleware runs code before a request is completed, allowing you to modify response, redirect, rewrite, or add headers.

```javascript
// middleware.js (root of project)
import { NextResponse } from 'next/server';

export function middleware(request) {
  const { pathname } = request.nextUrl;
  
  // Example 1: Authentication
  const token = request.cookies.get('auth-token');
  
  if (pathname.startsWith('/dashboard') && !token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  // Example 2: Add custom header
  const response = NextResponse.next();
  response.headers.set('X-Custom-Header', 'value');
  
  // Example 3: Rewrite (show different page, keep URL)
  if (pathname === '/old-page') {
    return NextResponse.rewrite(new URL('/new-page', request.url));
  }
  
  // Example 4: Modify request
  request.headers.set('x-pathname', pathname);
  
  return response;
}

// Configure which routes use middleware
export const config = {
  matcher: [
    '/dashboard/:path*',
    '/api/:path*',
    '/((?!_next|api|favicon.ico).*)'
  ]
};
```

**Use Cases:**

**1. Authentication & Authorization:**
```javascript
export function middleware(request) {
  const token = request.cookies.get('auth-token')?.value;
  const { pathname } = request.nextUrl;
  
  // Check if route requires auth
  const protectedRoutes = ['/dashboard', '/profile', '/settings'];
  const isProtected = protectedRoutes.some(route => 
    pathname.startsWith(route)
  );
  
  if (isProtected && !token) {
    const url = new URL('/login', request.url);
    url.searchParams.set('from', pathname);
    return NextResponse.redirect(url);
  }
  
  // Check role-based access
  if (pathname.startsWith('/admin')) {
    const user = await verifyToken(token);
    if (user.role !== 'admin') {
      return NextResponse.redirect(new URL('/unauthorized', request.url));
    }
  }
  
  return NextResponse.next();
}
```

**2. Internationalization:**
```javascript
import { match } from '@formatjs/intl-localematcher';
import Negotiator from 'negotiator';

const locales = ['en', 'fr', 'es'];
const defaultLocale = 'en';

export function middleware(request) {
  const { pathname } = request.nextUrl;
  
  // Check if pathname already has locale
  const pathnameHasLocale = locales.some(
    locale => pathname.startsWith(`/${locale}/`) || pathname === `/${locale}`
  );
  
  if (pathnameHasLocale) return;
  
  // Detect locale from headers
  const headers = { 'accept-language': request.headers.get('accept-language') || '' };
  const languages = new Negotiator({ headers }).languages();
  const locale = match(languages, locales, defaultLocale);
  
  // Redirect to localized URL
  return NextResponse.redirect(
    new URL(`/${locale}${pathname}`, request.url)
  );
}
```

**3. Rate Limiting:**
```javascript
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '10 s')
});

export async function middleware(request) {
  const ip = request.ip || '127.0.0.1';
  const { success, limit, remaining } = await ratelimit.limit(ip);
  
  if (!success) {
    return new NextResponse('Too Many Requests', {
      status: 429,
      headers: {
        'X-RateLimit-Limit': limit.toString(),
        'X-RateLimit-Remaining': remaining.toString()
      }
    });
  }
  
  return NextResponse.next();
}
```

**4. A/B Testing:**
```javascript
export function middleware(request) {
  const variant = request.cookies.get('ab-variant')?.value;
  
  if (!variant) {
    // Assign random variant
    const newVariant = Math.random() > 0.5 ? 'A' : 'B';
    const response = NextResponse.next();
    response.cookies.set('ab-variant', newVariant);
    return response;
  }
  
  // Rewrite based on variant
  if (variant === 'B' && request.nextUrl.pathname === '/landing') {
    return NextResponse.rewrite(new URL('/landing-b', request.url));
  }
  
  return NextResponse.next();
}
```

**5. Feature Flags:**
```javascript
const features = {
  newDashboard: false,
  betaFeature: true
};

export function middleware(request) {
  const { pathname } = request.nextUrl;
  
  if (pathname === '/new-dashboard' && !features.newDashboard) {
    return NextResponse.redirect(new URL('/dashboard', request.url));
  }
  
  return NextResponse.next();
}
```

**Where middleware runs:**
- On Vercel: Edge runtime (ultra-fast, global)
- Self-hosted: Can run on Edge or Node.js runtime

**Limitations:**
- Can't use Node.js APIs (fs, path)
- Limited to Edge runtime APIs
- Response size limit
- Execution time limit

---

## SEO & Metadata

### Q31: How to implement SEO and metadata in Next.js?

**Answer:**

**App Router - Metadata API:**
```javascript
// app/page.js - Static metadata
export const metadata = {
  title: 'My Website',
  description: 'Best website ever',
  keywords: ['next.js', 'react', 'web development'],
  authors: [{ name: 'John Doe' }],
  openGraph: {
    title: 'My Website',
    description: 'Best website ever',
    url: 'https://mysite.com',
    siteName: 'My Website',
    images: [
      {
        url: 'https://mysite.com/og.jpg',
        width: 1200,
        height: 630
      }
    ],
    locale: 'en_US',
    type: 'website'
  },
  twitter: {
    card: 'summary_large_image',
    title: 'My Website',
    description: 'Best website ever',
    images: ['https://mysite.com/twitter.jpg']
  },
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      'max-image-preview': 'large'
    }
  }
};

export default function HomePage() {
  return <div>Home</div>;
}
```

**Dynamic metadata:**
```javascript
// app/products/[id]/page.js
export async function generateMetadata({ params, searchParams }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(r => r.json());
  
  return {
    title: `${product.name} | My Store`,
    description: product.description,
    openGraph: {
      title: product.name,
      description: product.description,
      images: [{ url: product.image }]
    },
    // JSON-LD structured data
    other: {
      'application/ld+json': JSON.stringify({
        '@context': 'https://schema.org',
        '@type': 'Product',
        name: product.name,
        image: product.image,
        description: product.description,
        offers: {
          '@type': 'Offer',
          price: product.price,
          priceCurrency: 'USD'
        }
      })
    }
  };
}

export default function ProductPage({ params }) {
  return <div>Product {params.id}</div>;
}
```

**Pages Router:**
```javascript
import Head from 'next/head';

export default function Page({ product }) {
  return (
    <>
      <Head>
        <title>{product.name} | My Store</title>
        <meta name="description" content={product.description} />
        <meta property="og:title" content={product.name} />
        <meta property="og:description" content={product.description} />
        <meta property="og:image" content={product.image} />
        <meta name="twitter:card" content="summary_large_image" />
        <link rel="canonical" href={`https://mysite.com/products/${product.id}`} />
        
        {/* JSON-LD */}
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={{
            __html: JSON.stringify({
              '@context': 'https://schema.org',
              '@type': 'Product',
              name: product.name,
              image: product.image
            })
          }}
        />
      </Head>
      
      <div>{product.name}</div>
    </>
  );
}
```

**Sitemap generation:**
```javascript
// app/sitemap.js
export default function sitemap() {
  return [
    {
      url: 'https://mysite.com',
      lastModified: new Date(),
      changeFrequency: 'yearly',
      priority: 1
    },
    {
      url: 'https://mysite.com/about',
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.8
    }
  ];
}

// Or dynamic
export default async function sitemap() {
  const products = await fetch('https://api.example.com/products').then(r => r.json());
  
  const productUrls = products.map(product => ({
    url: `https://mysite.com/products/${product.id}`,
    lastModified: product.updatedAt,
    changeFrequency: 'weekly',
    priority: 0.7
  }));
  
  return [
    {
      url: 'https://mysite.com',
      lastModified: new Date(),
      priority: 1
    },
    ...productUrls
  ];
}
```

**Robots.txt:**
```javascript
// app/robots.js
export default function robots() {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: '/private/'
    },
    sitemap: 'https://mysite.com/sitemap.xml'
  };
}
```

---

## Styling in Next.js

### Q32: What are the different ways to style components in Next.js?

**Answer:**

**1. CSS Modules (Recommended):**
```javascript
// components/Button.module.css
.button {
  background: blue;
  color: white;
  padding: 10px 20px;
}

.primary {
  background: green;
}

// components/Button.js
import styles from './Button.module.css';

export default function Button({ primary }) {
  return (
    <button className={`${styles.button} ${primary ? styles.primary : ''}`}>
      Click me
    </button>
  );
}
```

**2. Global CSS:**
```javascript
// styles/globals.css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
}

// app/layout.js (App Router)
import './globals.css';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>{children}</body>
    </html>
  );
}

// pages/_app.js (Pages Router)
import '../styles/globals.css';

export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

**3. Tailwind CSS:**
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

```javascript
// tailwind.config.js
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}'
  ],
  theme: {
    extend: {}
  },
  plugins: []
};

// globals.css
@tailwind base;
@tailwind components;
@tailwind utilities;

// Usage
export default function Button() {
  return (
    <button className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
      Button
    </button>
  );
}
```

**4. Styled Components:**
```bash
npm install styled-components
```

```javascript
// app/registry.js
'use client';

import { useState } from 'react';
import { useServerInsertedHTML } from 'next/navigation';
import { ServerStyleSheet, StyleSheetManager } from 'styled-components';

export default function StyledComponentsRegistry({ children }) {
  const [styledComponentsStyleSheet] = useState(() => new ServerStyleSheet());
  
  useServerInsertedHTML(() => {
    const styles = styledComponentsStyleSheet.getStyleElement();
    styledComponentsStyleSheet.instance.clearTag();
    return <>{styles}</>;
  });
  
  if (typeof window !== 'undefined') return <>{children}</>;
  
  return (
    <StyleSheetManager sheet={styledComponentsStyleSheet.instance}>
      {children}
    </StyleSheetManager>
  );
}

// app/layout.js
import StyledComponentsRegistry from './registry';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <StyledComponentsRegistry>{children}</StyledComponentsRegistry>
      </body>
    </html>
  );
}

// Usage
import styled from 'styled-components';

const Button = styled.button`
  background: ${props => props.primary ? 'blue' : 'gray'};
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  
  &:hover {
    opacity: 0.8;
  }
`;

export default function Component() {
  return <Button primary>Click me</Button>;
}
```

**5. CSS-in-JS (Emotion):**
```javascript
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';

export default function Component() {
  return (
    <div
      css={css`
        background: blue;
        color: white;
        padding: 20px;
        &:hover {
          background: darkblue;
        }
      `}
    >
      Styled with Emotion
    </div>
  );
}
```

**6. Sass/SCSS:**
```bash
npm install sass
```

```javascript
// styles/Home.module.scss
$primary-color: blue;

.container {
  background: $primary-color;
  
  .title {
    color: white;
    font-size: 2rem;
  }
}

// Usage
import styles from './Home.module.scss';

export default function Home() {
  return (
    <div className={styles.container}>
      <h1 className={styles.title}>Title</h1>
    </div>
  );
}
```

**Recommendation:**
- **Small projects**: CSS Modules or Tailwind
- **Design system**: Tailwind with custom config
- **Component library**: Styled Components or Emotion
- **Large teams**: Tailwind (consistency) or CSS Modules (simplicity)

---

## Error Handling

### Q33: How to implement error handling in Next.js?

**Answer:**

**App Router - error.js:**
```javascript
// app/error.js - Catches errors in any route
'use client'; // Error components must be Client Components

export default function Error({ error, reset }) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <p>{error.message}</p>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}

// app/dashboard/error.js - Catches errors in /dashboard
'use client';

export default function DashboardError({ error }) {
  return (
    <div className="error-container">
      <h1>Dashboard Error</h1>
      <p>{error.message}</p>
    </div>
  );
}
```

**Global error:**
```javascript
// app/global-error.js - Catches errors in root layout
'use client';

export default function GlobalError({ error, reset }) {
  return (
    <html>
      <body>
        <h2>Application Error</h2>
        <button onClick={() => reset()}>Try again</button>
      </body>
    </html>
  );
}
```

**Not Found pages:**
```javascript
// app/not-found.js
export default function NotFound() {
  return (
    <div>
      <h2>Not Found</h2>
      <p>Could not find requested resource</p>
    </div>
  );
}

// Programmatically trigger 404
import { notFound } from 'next/navigation';

export default async function Page({ params }) {
  const data = await fetchData(params.id);
  
  if (!data) {
    notFound(); // Shows app/not-found.js
  }
  
  return <div>{data.title}</div>;
}
```

**Pages Router:**
```javascript
// pages/_error.js
function Error({ statusCode }) {
  return (
    <p>
      {statusCode
        ? `An error ${statusCode} occurred on server`
        : 'An error occurred on client'}
    </p>
  );
}

Error.getInitialProps = ({ res, err }) => {
  const statusCode = res ? res.statusCode : err ? err.statusCode : 404;
  return { statusCode };
};

export default Error;

// pages/404.js - Custom 404
export default function Custom404() {
  return <h1>404 - Page Not Found</h1>;
}

// pages/500.js - Custom 500
export default function Custom500() {
  return <h1>500 - Server Error</h1>;
}
```

**Error monitoring:**
```javascript
// app/error.js
'use client';

import * as Sentry from '@sentry/nextjs';
import { useEffect } from 'react';

export default function Error({ error, reset }) {
  useEffect(() => {
    // Log to error reporting service
    Sentry.captureException(error);
  }, [error]);
  
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

---

## Performance Best Practices

### Q34: What are the performance optimization strategies in Next.js?

**Answer:**

**1. Use Static Generation when possible:**
```javascript
// Fastest - pre-rendered at build time
export default async function Page() {
  const data = await fetchData();
  return <Content data={data} />;
}
```

**2. Implement ISR for semi-static content:**
```javascript
export const revalidate = 3600; // Revalidate every hour

export default async function ProductPage() {
  const products = await fetchProducts();
  return <ProductList products={products} />;
}
```

**3. Optimize images:**
```javascript
import Image from 'next/image';

<Image
  src="/hero.jpg"
  width={1200}
  height={600}
  priority // For above-fold images
  placeholder="blur"
  alt="Hero"
/>
```

**4. Lazy load components:**
```javascript
import dynamic from 'next/dynamic';

const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <Spinner />,
  ssr: false // Skip SSR if not needed
});
```

**5. Use streaming and Suspense:**
```javascript
import { Suspense } from 'react';

export default function Page() {
  return (
    <>
      <Header /> {/* Instant */}
      <Suspense fallback={<Skeleton />}>
        <SlowData /> {/* Streams in */}
      </Suspense>
    </>
  );
}
```

**6. Optimize fonts:**
```javascript
import { Inter } from 'next/font/google';

const inter = Inter({ 
  subsets: ['latin'],
  display: 'swap',
  preload: true
});
```

**7. Use Server Components:**
```javascript
// Keep heavy logic on server
export default async function Dashboard() {
  const data = await heavyProcessing(); // On server
  return <DashboardUI data={data} />;
}
```

**8. Implement proper caching:**
```javascript
// Cache API responses
const data = await fetch('https://api.example.com/data', {
  next: { revalidate: 3600 } // Cache for 1 hour
});

// Use unstable_cache for expensive operations
import { unstable_cache } from 'next/cache';

const getCachedData = unstable_cache(
  async (id) => expensiveOperation(id),
  ['data-key'],
  { revalidate: 3600, tags: ['data'] }
);
```

**9. Bundle analysis:**
```bash
npm install @next/bundle-analyzer
```

```javascript
// next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true'
});

module.exports = withBundleAnalyzer({
  // config
});

// Run: ANALYZE=true npm run build
```

**10. Core Web Vitals optimization:**
```javascript
// Largest Contentful Paint (LCP)
- Use priority on hero images
- Preload critical resources
- Optimize server response time

// First Input Delay (FID) / Interaction to Next Paint (INP)
- Use Server Components
- Defer non-critical JS
- Reduce JavaScript execution time

// Cumulative Layout Shift (CLS)
- Specify image dimensions
- Reserve space for dynamic content
- Avoid inserting content above existing content
```

---

## Summary

**Key Takeaways:**

1. **Choose the right rendering strategy** - SSG for static, SSR for dynamic, ISR for middle ground
2. **Optimize everything** - Images, fonts, code splitting, caching
3. **Use App Router** for new projects - Better performance and DX
4. **Server Components first** - Keep heavy logic on server
5. **Implement proper SEO** - Metadata API, sitemaps, structured data
6. **Monitor performance** - Core Web Vitals, bundle size, load times
7. **Error handling** - Graceful errors with error.js
8. **API Routes or Server Actions** - Backend in same codebase
9. **Middleware for cross-cutting concerns** - Auth, i18n, redirects
10. **Test and measure** - Use analytics and profiling tools

---

## Beginner Level Questions

### Q35: What is the difference between pages and app directory?
