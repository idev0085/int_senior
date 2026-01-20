# Next.js - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Core Concepts & Rendering](#core-concepts--rendering)
2. [App Router vs Pages Router](#app-router-vs-pages-router)
3. [Performance & Optimization](#performance--optimization)
4. [Data Fetching](#data-fetching)
5. [Deployment & Production](#deployment--production)
6. [Architecture Patterns](#architecture-patterns)

---

## Core Concepts & Rendering

### Q1: Explain the different rendering strategies in Next.js and when to use each.

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
