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

## Quick Reference: SSR, SSG, and ISR

### Overview of Next.js Rendering Strategies

**SSR (Server-Side Rendering)**, **SSG (Static Site Generation)**, and **ISR (Incremental Static Regeneration)** are the three core rendering strategies in Next.js that determine when and how your pages are rendered.

### SSR (Server-Side Rendering)

**What it is:** HTML is generated on the server for each request, providing fresh data on every page load.

**How it works:**
1. User requests a page
2. Server fetches required data
3. Server renders React components to HTML
4. HTML is sent to browser (content visible immediately)
5. JavaScript hydrates the page (makes it interactive)

**Pages Router Implementation:**
```javascript
export async function getServerSideProps(context) {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  
  return { props: { data } };
}

export default function Page({ data }) {
  return <div>{data.title}</div>;
}
```

**App Router Implementation:**
```javascript
// Force dynamic rendering (SSR)
export const dynamic = 'force-dynamic';

export default async function Page() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'no-store' // Don't cache, fetch fresh every time
  });
  const data = await res.json();
  
  return <div>{data.title}</div>;
}
```

**Use SSR when:**
- Content changes frequently
- Need real-time or personalized data
- SEO is important AND content is dynamic
- User-specific information (dashboards, profiles)

**Pros:** Always up-to-date, SEO-friendly, fast initial render
**Cons:** Slower than static, higher server costs, requires available server

---

### SSG (Static Site Generation)

**What it is:** HTML is pre-generated at build time, creating static files that can be served from a CDN.

**How it works:**
1. During `npm run build`, Next.js generates HTML for all pages
2. Static HTML files are deployed to CDN
3. User requests trigger instant delivery from CDN
4. No server-side processing needed per request

**Pages Router Implementation:**
```javascript
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  
  return { props: { data } };
}

// For dynamic routes
export async function getStaticPaths() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  return {
    paths: posts.map(post => ({ params: { id: post.id } })),
    fallback: false
  };
}

export default function Page({ data }) {
  return <div>{data.title}</div>;
}
```

**App Router Implementation:**
```javascript
// Static by default - pages are generated at build time
export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  return posts.map(post => ({
    id: post.id
  }));
}

export default async function Page({ params }) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);
  const data = await res.json();
  
  return <div>{data.title}</div>;
}
```

**Use SSG when:**
- Content doesn't change often
- Marketing pages, blogs, documentation
- Same content for all users
- Maximum performance is critical

**Pros:** Blazing fast, cheap hosting, scales infinitely, perfect SEO
**Cons:** Stale until rebuild, build time increases with page count, not suitable for dynamic content

---

### ISR (Incremental Static Regeneration)

**What it is:** Static pages that can be updated after build time without rebuilding the entire site, combining the speed of static with the freshness of dynamic.

**How it works:**
1. Page is initially built statically at build time
2. Page is served from cache (fast like SSG)
3. After revalidation period expires, next request triggers background regeneration
4. Stale page continues to be served while new version generates
5. Once ready, new page replaces old one in cache
6. Subsequent requests get the updated version

**Pages Router Implementation:**
```javascript
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  
  return {
    props: { data },
    revalidate: 60 // Regenerate page every 60 seconds
  };
}

export default function Page({ data }) {
  return <div>{data.title}</div>;
}
```

**App Router Implementation:**
```javascript
// Set revalidation time for entire page
export const revalidate = 60; // Revalidate every 60 seconds

export default async function Page() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  
  return <div>{data.title}</div>;
}

// Or per-fetch revalidation
export default async function Page() {
  const res = await fetch('https://api.example.com/data', {
    next: { revalidate: 60 } // This specific fetch revalidates every 60s
  });
  const data = await res.json();
  
  return <div>{data.title}</div>;
}
```

**Use ISR when:**
- Content updates periodically but not constantly
- E-commerce product pages
- News articles that update throughout the day
- Need balance between performance and freshness

**Pros:** Fast like static, stays reasonably fresh, no full rebuilds needed, handles traffic spikes
**Cons:** Potential for stale data within revalidation window, slightly more complex caching behavior

---

### Comparison Table

| Feature | SSR | SSG | ISR |
|---------|-----|-----|-----|
| **Generation Time** | On each request | At build time | At build time + periodic updates |
| **Data Freshness** | Always fresh | Stale until rebuild | Fresh after revalidation period |
| **Performance** | Slower (server processing) | Fastest (CDN) | Fast (CDN with updates) |
| **Server Required** | Yes | No | Yes (for regeneration) |
| **SEO** | ✅ Excellent | ✅ Excellent | ✅ Excellent |
| **Cost** | Higher (server compute) | Lower (static hosting) | Medium (CDN + regeneration) |
| **Best For** | Real-time, personalized | Rarely changing content | Periodically updated content |

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

---

### Q1.1: What are the key features of Next.js?

**Answer:**
Next.js is a React framework that extends React's capabilities to build server-rendered and statically generated web applications. Here are its key features:

#### 1. Server-Side Rendering (SSR)
Improves SEO and initial load performance by rendering pages on the server. Content is already available for search engines to index, and users see a fully rendered page on the first load.

```javascript
// App Router - SSR by default for Server Components
export const dynamic = 'force-dynamic';

export default async function Page() {
  const data = await fetch('https://api.example.com/data', {
    cache: 'no-store'
  }).then(r => r.json());
  
  return <div>{data.content}</div>;
}
```

**Benefits:**
- ✅ Improved SEO - content visible to search engines
- ✅ Faster initial page load - no JavaScript required for initial render
- ✅ Better performance on low-powered devices

#### 2. Static Site Generation (SSG)
Pre-renders pages at build time, making them super fast to load. Ideal for content that changes infrequently, like blog posts or landing pages.

```javascript
// App Router - Static by default
export default async function BlogPost({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.id}`)
    .then(r => r.json());
  
  return <article>{post.content}</article>;
}

export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  return posts.map(post => ({ id: post.id }));
}
```

**Benefits:**
- ✅ Lightning-fast page loads
- ✅ Can be served from CDN
- ✅ Lower server costs

#### 3. Automatic Code Splitting
Breaks down your application code into smaller bundles, improving load times by only loading the code needed for the current page.

```javascript
// Automatic code splitting per route
// app/home/page.js - Only loads code for home page
export default function Home() {
  return <div>Home</div>;
}

// app/about/page.js - Only loads code for about page
export default function About() {
  return <div>About</div>;
}

// Dynamic imports for component-level splitting
import dynamic from 'next/dynamic';

const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <p>Loading...</p>
});
```

**Benefits:**
- ✅ Smaller initial bundle sizes
- ✅ Faster page loads
- ✅ Better performance metrics

#### 4. Data Fetching
Next.js offers multiple ways to fetch data, allowing you to choose the most appropriate method for your specific needs.

```javascript
// Pages Router - Static data fetching
export async function getStaticProps() {
  const data = await fetch('https://api.example.com/data').then(r => r.json());
  return { props: { data } };
}

// Pages Router - Server-side data fetching
export async function getServerSideProps() {
  const data = await fetch('https://api.example.com/data').then(r => r.json());
  return { props: { data } };
}

// App Router - Flexible fetching with caching control
export default async function Page() {
  // Static (cached)
  const staticData = await fetch('https://api.example.com/static');
  
  // Dynamic (no cache)
  const dynamicData = await fetch('https://api.example.com/dynamic', {
    cache: 'no-store'
  });
  
  // Revalidated (ISR)
  const revalidatedData = await fetch('https://api.example.com/revalidated', {
    next: { revalidate: 60 }
  });
  
  return <div>...</div>;
}
```

**Methods:**
- `getStaticProps` - Fetch at build time (Pages Router)
- `getServerSideProps` - Fetch on each request (Pages Router)
- `fetch` with caching options - Flexible approach (App Router)

#### 5. File-Based Routing
Routing is simplified in Next.js. It automatically creates routes based on the file structure of your pages directory, making it easy to manage your application's URL structure.

```javascript
// Pages Router
pages/
├── index.js          → /
├── about.js          → /about
├── blog/
│   ├── index.js      → /blog
│   └── [slug].js     → /blog/:slug
└── users/
    └── [id].js       → /users/:id

// App Router
app/
├── page.js           → /
├── about/
│   └── page.js       → /about
├── blog/
│   ├── page.js       → /blog
│   └── [slug]/
│       └── page.js   → /blog/:slug
└── users/
    └── [id]/
        └── page.js   → /users/:id
```

**Benefits:**
- ✅ No manual route configuration
- ✅ Intuitive file structure
- ✅ Dynamic routes with brackets notation

#### 6. Image Optimization
Next.js automatically optimizes images, including resizing and compressing them, for faster loading times and improved SEO.

```javascript
import Image from 'next/image';

export default function Page() {
  return (
    <Image
      src="/profile.jpg"
      alt="Profile"
      width={500}
      height={500}
      priority // Load immediately for LCP
    />
  );
}
```

**Automatic optimizations:**
- ✅ Lazy loading by default
- ✅ Automatic WebP/AVIF conversion
- ✅ Responsive images for different screen sizes
- ✅ Blur placeholder while loading
- ✅ Prevents Cumulative Layout Shift (CLS)

#### 7. Built-in CSS and JavaScript Bundling
Next.js takes care of bundling and optimizing your CSS and JavaScript code, streamlining the development process.

```javascript
// CSS Modules - Scoped CSS
import styles from './page.module.css';

export default function Page() {
  return <div className={styles.container}>Content</div>;
}

// Global CSS
import './globals.css'; // In app/layout.js

// CSS-in-JS (with any library)
import styled from 'styled-components';

const Button = styled.button`
  background: blue;
  color: white;
`;
```

**Features:**
- ✅ CSS Modules support
- ✅ Sass/SCSS support
- ✅ CSS-in-JS compatibility
- ✅ Automatic vendor prefixing
- ✅ Minification in production

---

#### 7.1. How to Add Stylesheets in Next.js

Next.js supports multiple ways to style your application, each with its own use cases and benefits.

---

##### Method 1: Global CSS

Import global CSS files in your root layout or page.

**App Router:**
```javascript
// app/layout.tsx
import './globals.css';

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

**Pages Router:**
```javascript
// pages/_app.js
import '../styles/globals.css';

export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

**globals.css:**
```css
/* app/globals.css */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen;
  line-height: 1.6;
  color: #333;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}
```

**✅ Use global CSS for:**
- CSS resets
- Typography
- Global utility classes
- Theme variables

**⚠️ Important:**
- Global CSS can only be imported in `app/layout.tsx` (App Router) or `pages/_app.js` (Pages Router)
- Cannot import global CSS in component files

---

##### Method 2: CSS Modules

Scoped CSS that's automatically namespaced to avoid conflicts.

```javascript
// components/Button.tsx
import styles from './Button.module.css';

export function Button({ children, variant = 'primary' }) {
  return (
    <button className={`${styles.button} ${styles[variant]}`}>
      {children}
    </button>
  );
}
```

**Button.module.css:**
```css
/* components/Button.module.css */
.button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s ease;
}

.primary {
  background-color: #0070f3;
  color: white;
}

.primary:hover {
  background-color: #0051cc;
}

.secondary {
  background-color: #eaeaea;
  color: #333;
}

.secondary:hover {
  background-color: #d4d4d4;
}
```

**Multiple classes:**
```javascript
import styles from './Card.module.css';

export function Card({ featured, children }) {
  return (
    <div className={`${styles.card} ${featured ? styles.featured : ''}`}>
      {children}
    </div>
  );
}
```

**✅ Benefits:**
- Automatic unique class names
- No naming conflicts
- Component-scoped styles
- Type-safe with TypeScript
- Can be imported anywhere

---

##### Method 3: Sass/SCSS

Next.js has built-in Sass support.

**Installation:**
```bash
npm install sass
# or
yarn add sass
# or
pnpm add sass
```

**Usage:**
```javascript
// app/page.tsx
import './page.scss';
// or
import styles from './page.module.scss';

export default function Page() {
  return <div className={styles.container}>Content</div>;
}
```

**page.module.scss:**
```scss
// app/page.module.scss
$primary-color: #0070f3;
$spacing-unit: 8px;

.container {
  padding: $spacing-unit * 2;
  
  .header {
    color: $primary-color;
    font-size: 2rem;
    
    &:hover {
      opacity: 0.8;
    }
  }
  
  .content {
    margin-top: $spacing-unit * 3;
    
    p {
      line-height: 1.6;
    }
  }
}
```

**With variables and mixins:**
```scss
// styles/variables.scss
$primary: #0070f3;
$secondary: #ff4081;
$breakpoint-mobile: 768px;

@mixin mobile {
  @media (max-width: $breakpoint-mobile) {
    @content;
  }
}

// components/Hero.module.scss
@import '../styles/variables';

.hero {
  background: $primary;
  padding: 60px 20px;
  
  @include mobile {
    padding: 30px 15px;
  }
}
```

---

##### Method 4: CSS-in-JS (Styled Components, Emotion)

**Styled Components:**

**Installation:**
```bash
npm install styled-components
```

**Setup (App Router):**
```javascript
// app/registry.tsx
'use client';

import React, { useState } from 'react';
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

// app/layout.tsx
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
```

**Usage:**
```javascript
'use client';

import styled from 'styled-components';

const Button = styled.button`
  background: ${props => props.primary ? '#0070f3' : '#eaeaea'};
  color: ${props => props.primary ? 'white' : '#333'};
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: all 0.3s ease;

  &:hover {
    opacity: 0.8;
  }
`;

const Container = styled.div`
  max-width: 1200px;
  margin: 0 auto;
  padding: ${props => props.padding || '20px'};
`;

export default function Page() {
  return (
    <Container padding="40px">
      <h1>Styled Components</h1>
      <Button primary>Primary Button</Button>
      <Button>Secondary Button</Button>
    </Container>
  );
}
```

---

##### Method 5: Tailwind CSS

**Installation:**
```bash
npx create-next-app@latest --tailwind
# or manually:
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**Configuration:**
```javascript
// tailwind.config.js
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        primary: '#0070f3',
        secondary: '#ff4081',
      },
    },
  },
  plugins: [],
};
```

**globals.css:**
```css
/* app/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .btn-primary {
    @apply bg-primary text-white px-4 py-2 rounded hover:opacity-80 transition;
  }
}
```

**Usage:**
```javascript
export default function Page() {
  return (
    <div className="max-w-7xl mx-auto px-4 py-8">
      <h1 className="text-4xl font-bold text-gray-900 mb-4">
        Welcome to Next.js
      </h1>
      <button className="btn-primary">
        Get Started
      </button>
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mt-8">
        <div className="p-6 bg-white rounded-lg shadow-md">
          <h2 className="text-xl font-semibold mb-2">Feature 1</h2>
          <p className="text-gray-600">Description</p>
        </div>
      </div>
    </div>
  );
}
```

---

##### Method 6: Inline Styles

Use JavaScript objects for dynamic styles.

```javascript
export default function Page() {
  const styles = {
    container: {
      maxWidth: '1200px',
      margin: '0 auto',
      padding: '20px',
    },
    heading: {
      fontSize: '2rem',
      color: '#333',
      marginBottom: '1rem',
    },
  };

  return (
    <div style={styles.container}>
      <h1 style={styles.heading}>Hello World</h1>
    </div>
  );
}

// Dynamic styles
export function Button({ primary, children }) {
  return (
    <button
      style={{
        background: primary ? '#0070f3' : '#eaeaea',
        color: primary ? 'white' : '#333',
        padding: '10px 20px',
        border: 'none',
        borderRadius: '5px',
        cursor: 'pointer',
      }}
    >
      {children}
    </button>
  );
}
```

**⚠️ Limitations:**
- No pseudo-classes (`:hover`, `:focus`)
- No media queries
- Less performant for large applications
- Verbose syntax

---

##### Method 7: External Stylesheets

Import stylesheets from `public` folder or CDN.

```javascript
// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <head>
        {/* From CDN */}
        <link
          rel="stylesheet"
          href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css"
        />
        
        {/* From public folder */}
        <link rel="stylesheet" href="/styles/custom.css" />
      </head>
      <body>{children}</body>
    </html>
  );
}
```

**Using next/head (Pages Router):**
```javascript
import Head from 'next/head';

export default function Page() {
  return (
    <>
      <Head>
        <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto" />
      </Head>
      <div>Content</div>
    </>
  );
}
```

---

##### Method 8: CSS Variables (Custom Properties)

Define CSS variables for theming.

```css
/* app/globals.css */
:root {
  --color-primary: #0070f3;
  --color-secondary: #ff4081;
  --color-background: #ffffff;
  --color-text: #333333;
  --spacing-unit: 8px;
  --border-radius: 8px;
  --font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto;
}

[data-theme='dark'] {
  --color-primary: #4d94ff;
  --color-background: #1a1a1a;
  --color-text: #ffffff;
}

body {
  background-color: var(--color-background);
  color: var(--color-text);
  font-family: var(--font-family);
}

.button {
  background-color: var(--color-primary);
  padding: calc(var(--spacing-unit) * 2);
  border-radius: var(--border-radius);
}
```

**Usage with theme toggle:**
```javascript
'use client';

import { useState } from 'react';
import './globals.css';

export default function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    const newTheme = theme === 'light' ? 'dark' : 'light';
    setTheme(newTheme);
    document.documentElement.setAttribute('data-theme', newTheme);
  };

  return (
    <div>
      <button onClick={toggleTheme}>Toggle Theme</button>
      {children}
    </div>
  );
}
```

---

##### Comparison Table

| Method | Scoping | Performance | Learning Curve | Use Case |
|--------|---------|-------------|----------------|----------|
| **Global CSS** | Global | Excellent | Easy | Resets, base styles |
| **CSS Modules** | Component | Excellent | Easy | Component styles |
| **Sass/SCSS** | Global/Component | Excellent | Medium | Complex styles, variables |
| **CSS-in-JS** | Component | Good | Medium-Hard | Dynamic styles, theming |
| **Tailwind CSS** | Utility | Excellent | Medium | Rapid development |
| **Inline Styles** | Element | Good | Easy | Simple dynamic styles |
| **CSS Variables** | Global | Excellent | Easy | Theming, design tokens |

---

##### Best Practices

**1. Prefer CSS Modules for components:**
```javascript
// ✅ Good - Component-scoped styles
import styles from './Button.module.css';

export function Button() {
  return <button className={styles.button}>Click me</button>;
}
```

**2. Use global CSS sparingly:**
```css
/* ✅ Good - Only for global resets and base styles */
* {
  box-sizing: border-box;
}

body {
  font-family: system-ui;
}
```

**3. Organize styles by feature:**
```
app/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   └── Button.module.css
│   └── Card/
│       ├── Card.tsx
│       └── Card.module.css
├── styles/
│   ├── globals.css
│   ├── variables.css
│   └── utilities.css
```

**4. Use CSS variables for theming:**
```css
/* ✅ Good - Reusable theme tokens */
:root {
  --color-primary: #0070f3;
  --spacing-md: 16px;
}

.button {
  background: var(--color-primary);
  padding: var(--spacing-md);
}
```

**5. Avoid deep nesting:**
```scss
// ❌ Bad - Too deep
.container {
  .header {
    .nav {
      .item {
        .link {
          color: blue;
        }
      }
    }
  }
}

// ✅ Good - Flat structure
.nav-link {
  color: blue;
}
```

---

##### Font Optimization

Next.js provides built-in font optimization:

```javascript
// app/layout.tsx
import { Inter, Roboto_Mono } from 'next/font/google';

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-inter',
});

const robotoMono = Roboto_Mono({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-roboto-mono',
});

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={`${inter.variable} ${robotoMono.variable}`}>
      <body className={inter.className}>{children}</body>
    </html>
  );
}
```

**globals.css:**
```css
body {
  font-family: var(--font-inter), system-ui;
}

code {
  font-family: var(--font-roboto-mono), monospace;
}
```

---

##### Custom PostCSS Configuration

```javascript
// postcss.config.js
module.exports = {
  plugins: {
    'postcss-import': {},
    'tailwindcss/nesting': {},
    tailwindcss: {},
    autoprefixer: {},
    ...(process.env.NODE_ENV === 'production' ? { cssnano: {} } : {})
  },
};
```

---

#### 7.2. How to Use Dynamic Imports in Next.js

Dynamic imports allow you to load JavaScript modules and React components on-demand, reducing initial bundle size and improving performance.

---

##### What Are Dynamic Imports?

Dynamic imports load code only when needed, rather than including everything in the initial bundle.

**Traditional Static Import:**
```javascript
// ❌ Loaded immediately, always included in bundle
import HeavyComponent from './HeavyComponent';
import { largeLibrary } from 'large-library';

export default function Page() {
  return <HeavyComponent />;
}
```

**Dynamic Import:**
```javascript
// ✅ Loaded only when needed
import dynamic from 'next/dynamic';

const HeavyComponent = dynamic(() => import('./HeavyComponent'));

export default function Page() {
  return <HeavyComponent />;
}
```

---

##### Why Use Dynamic Imports?

**1. Reduce Initial Bundle Size:**
```javascript
// Before: 500KB initial bundle
import Chart from 'chart.js'; // 150KB
import Editor from 'rich-editor'; // 200KB

// After: 150KB initial bundle (350KB loaded on-demand)
const Chart = dynamic(() => import('chart.js'));
const Editor = dynamic(() => import('rich-editor'));
```

**2. Improve First Load Performance:**
- Faster Time to Interactive (TTI)
- Better Core Web Vitals scores
- Improved user experience

**3. Conditional Loading:**
```javascript
// Load only for specific users or conditions
const AdminPanel = dynamic(() => import('./AdminPanel'));

export default function Dashboard({ user }) {
  return (
    <div>
      <h1>Dashboard</h1>
      {user.isAdmin && <AdminPanel />}
    </div>
  );
}
```

---

##### Basic Dynamic Import

**Component-level dynamic import:**

```javascript
import dynamic from 'next/dynamic';

const DynamicComponent = dynamic(() => import('./Component'));

export default function Page() {
  return (
    <div>
      <h1>My Page</h1>
      <DynamicComponent />
    </div>
  );
}
```

---

##### Dynamic Import with Loading State

**Show loading indicator while component loads:**

```javascript
import dynamic from 'next/dynamic';

const DynamicComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <div>Loading...</div>
});

export default function Page() {
  return (
    <div>
      <h1>Page Title</h1>
      <DynamicComponent />
    </div>
  );
}
```

**Custom loading component:**

```javascript
const LoadingSpinner = () => (
  <div className="flex justify-center items-center p-8">
    <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-blue-500"></div>
  </div>
);

const Chart = dynamic(() => import('./Chart'), {
  loading: () => <LoadingSpinner />
});
```

---

##### Disable SSR for Client-Only Components

Some components rely on browser APIs and can't render on the server.

```javascript
import dynamic from 'next/dynamic';

const ClientOnlyComponent = dynamic(() => import('./ClientOnlyComponent'), {
  ssr: false
});

export default function Page() {
  return (
    <div>
      <h1>Page with Client-Only Component</h1>
      <ClientOnlyComponent />
    </div>
  );
}
```

**Example - Component using window:**

```javascript
// components/BrowserComponent.tsx
'use client';

export default function BrowserComponent() {
  const width = window.innerWidth; // Requires browser
  
  return <div>Window width: {width}px</div>;
}

// app/page.tsx
import dynamic from 'next/dynamic';

const BrowserComponent = dynamic(() => import('@/components/BrowserComponent'), {
  ssr: false, // Don't render on server
  loading: () => <p>Loading...</p>
});

export default function Page() {
  return <BrowserComponent />;
}
```

---

##### Dynamic Import with Named Exports

**If your component uses named exports:**

```javascript
// components/Charts.tsx
export function LineChart() {
  return <div>Line Chart</div>;
}

export function BarChart() {
  return <div>Bar Chart</div>;
}

// app/page.tsx
import dynamic from 'next/dynamic';

const LineChart = dynamic(() => 
  import('@/components/Charts').then(mod => mod.LineChart)
);

const BarChart = dynamic(() => 
  import('@/components/Charts').then(mod => mod.BarChart)
);

export default function Page() {
  return (
    <div>
      <LineChart />
      <BarChart />
    </div>
  );
}
```

---

##### Dynamic Import Multiple Components

```javascript
import dynamic from 'next/dynamic';

const components = {
  LineChart: dynamic(() => import('./charts/LineChart')),
  BarChart: dynamic(() => import('./charts/BarChart')),
  PieChart: dynamic(() => import('./charts/PieChart')),
};

export default function Dashboard({ chartType }) {
  const Chart = components[chartType];
  
  return (
    <div>
      <h1>Dashboard</h1>
      {Chart && <Chart />}
    </div>
  );
}
```

---

##### Dynamic Import with Props

```javascript
import dynamic from 'next/dynamic';

const DynamicComponent = dynamic(() => import('./Component'));

export default function Page() {
  return (
    <DynamicComponent 
      title="Hello"
      count={42}
      onAction={() => console.log('Action!')}
    />
  );
}
```

---

##### Lazy Loading Based on User Interaction

**Load component only when user clicks:**

```javascript
'use client';

import { useState } from 'react';
import dynamic from 'next/dynamic';

const HeavyModal = dynamic(() => import('./HeavyModal'));

export default function Page() {
  const [showModal, setShowModal] = useState(false);

  return (
    <div>
      <button onClick={() => setShowModal(true)}>
        Open Modal
      </button>
      
      {showModal && <HeavyModal onClose={() => setShowModal(false)} />}
    </div>
  );
}
```

---

##### Dynamic Import External Libraries

**Load heavy libraries only when needed:**

```javascript
'use client';

import { useState } from 'react';
import dynamic from 'next/dynamic';

export default function Editor() {
  const [Editor, setEditor] = useState(null);

  const loadEditor = async () => {
    // Dynamically import Monaco Editor (large library)
    const monaco = await import('@monaco-editor/react');
    setEditor(() => monaco.default);
  };

  return (
    <div>
      {!Editor && (
        <button onClick={loadEditor}>Load Editor</button>
      )}
      
      {Editor && <Editor height="500px" defaultLanguage="javascript" />}
    </div>
  );
}
```

---

##### Dynamic Import with Suspense (React 18+)

**Modern approach using React Suspense:**

```javascript
import { Suspense, lazy } from 'react';

const LazyComponent = lazy(() => import('./Component'));

export default function Page() {
  return (
    <div>
      <h1>My Page</h1>
      
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
    </div>
  );
}
```

**Multiple lazy components:**

```javascript
import { Suspense, lazy } from 'react';

const Chart = lazy(() => import('./Chart'));
const Table = lazy(() => import('./Table'));
const Map = lazy(() => import('./Map'));

export default function Dashboard() {
  return (
    <div className="grid grid-cols-3 gap-4">
      <Suspense fallback={<div>Loading chart...</div>}>
        <Chart />
      </Suspense>
      
      <Suspense fallback={<div>Loading table...</div>}>
        <Table />
      </Suspense>
      
      <Suspense fallback={<div>Loading map...</div>}>
        <Map />
      </Suspense>
    </div>
  );
}
```

---

##### Route-Based Code Splitting

Next.js automatically code-splits by route:

```javascript
// app/page.tsx - Only loads code for homepage
export default function Home() {
  return <h1>Home</h1>;
}

// app/about/page.tsx - Only loads code for about page
export default function About() {
  return <h1>About</h1>;
}

// app/dashboard/page.tsx - Only loads code for dashboard
export default function Dashboard() {
  return <h1>Dashboard</h1>;
}
```

**Each route is a separate bundle:**
```
Route                  Bundle Size
/                      → home.js (50KB)
/about                 → about.js (30KB)
/dashboard             → dashboard.js (120KB)
```

---

##### Real-World Example - E-commerce Product Page

```javascript
'use client';

import { useState } from 'react';
import dynamic from 'next/dynamic';
import Image from 'next/image';

// Lazy load heavy components
const Reviews = dynamic(() => import('./Reviews'), {
  loading: () => <div>Loading reviews...</div>
});

const RelatedProducts = dynamic(() => import('./RelatedProducts'), {
  loading: () => <div>Loading related products...</div>
});

const ProductVideo = dynamic(() => import('./ProductVideo'), {
  ssr: false,
  loading: () => <div>Loading video player...</div>
});

export default function ProductPage({ product }) {
  const [showReviews, setShowReviews] = useState(false);
  const [showVideo, setShowVideo] = useState(false);

  return (
    <div>
      {/* Always loaded - critical content */}
      <div className="product-header">
        <Image 
          src={product.image} 
          alt={product.name}
          width={600}
          height={600}
          priority
        />
        <h1>{product.name}</h1>
        <p>${product.price}</p>
        <button className="btn-primary">Add to Cart</button>
      </div>

      {/* Lazy loaded - less critical */}
      <div className="product-details">
        <button onClick={() => setShowVideo(true)}>
          Watch Product Video
        </button>
        {showVideo && <ProductVideo url={product.videoUrl} />}
      </div>

      <div className="product-reviews">
        <button onClick={() => setShowReviews(!showReviews)}>
          {showReviews ? 'Hide' : 'Show'} Reviews
        </button>
        {showReviews && <Reviews productId={product.id} />}
      </div>

      {/* Always lazy loaded - below the fold */}
      <RelatedProducts categoryId={product.categoryId} />
    </div>
  );
}
```

---

##### Performance Comparison

**Without Dynamic Imports:**
```
Initial Bundle: 450KB
First Load JS: 450KB
Time to Interactive: 3.2s
```

**With Dynamic Imports:**
```
Initial Bundle: 180KB
First Load JS: 180KB
Additional Chunks: Loaded on-demand (270KB total)
Time to Interactive: 1.4s ✨
```

---

##### Best Practices

**1. Dynamic import heavy components:**

```javascript
// ✅ Good - Heavy chart library loaded on-demand
const Chart = dynamic(() => import('recharts'));

// ❌ Bad - Small component, overhead not worth it
const Button = dynamic(() => import('./Button'));
```

**2. Use loading states:**

```javascript
// ✅ Good - User knows something is loading
const Editor = dynamic(() => import('./Editor'), {
  loading: () => <LoadingSpinner />
});

// ⚠️ OK but not ideal - No feedback during load
const Editor = dynamic(() => import('./Editor'));
```

**3. Disable SSR for browser-dependent code:**

```javascript
// ✅ Good - Component needs window/document
const ClientComponent = dynamic(() => import('./ClientComponent'), {
  ssr: false
});

// ❌ Bad - Will cause hydration errors
const ClientComponent = dynamic(() => import('./ClientComponent'));
```

**4. Group related dynamic imports:**

```javascript
// ✅ Good - Related components in one file
// components/Charts.tsx - Export multiple charts
const Charts = dynamic(() => import('./Charts'));

// ❌ Less efficient - Multiple small chunks
const Chart1 = dynamic(() => import('./Chart1'));
const Chart2 = dynamic(() => import('./Chart2'));
const Chart3 = dynamic(() => import('./Chart3'));
```

**5. Measure impact:**

```javascript
// Use Next.js Bundle Analyzer
npm install @next/bundle-analyzer

// next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer({
  // Your config
});

// Run: ANALYZE=true npm run build
```

---

##### Common Use Cases

**1. Code Editors:**
```javascript
const CodeEditor = dynamic(() => import('@monaco-editor/react'), {
  ssr: false,
  loading: () => <div>Loading editor...</div>
});
```

**2. Rich Text Editors:**
```javascript
const RichTextEditor = dynamic(() => import('react-quill'), {
  ssr: false,
  loading: () => <div>Loading editor...</div>
});
```

**3. Charts and Data Visualization:**
```javascript
const Chart = dynamic(() => import('react-chartjs-2'), {
  loading: () => <div>Loading chart...</div>
});
```

**4. Maps:**
```javascript
const Map = dynamic(() => import('react-leaflet').then(mod => mod.MapContainer), {
  ssr: false,
  loading: () => <div>Loading map...</div>
});
```

**5. Modals and Overlays:**
```javascript
const Modal = dynamic(() => import('./Modal'));

export default function Page() {
  const [show, setShow] = useState(false);
  
  return (
    <>
      <button onClick={() => setShow(true)}>Open</button>
      {show && <Modal onClose={() => setShow(false)} />}
    </>
  );
}
```

**6. Admin Panels:**
```javascript
const AdminPanel = dynamic(() => import('./AdminPanel'));

export default function Dashboard({ user }) {
  if (!user.isAdmin) return <div>Access Denied</div>;
  
  return <AdminPanel />;
}
```

---

##### Troubleshooting

**1. Component not loading:**
```javascript
// ❌ Wrong - Default export not found
const Component = dynamic(() => import('./Component'));

// ✅ Correct - Access named export
const Component = dynamic(() => 
  import('./Component').then(mod => mod.ComponentName)
);
```

**2. Hydration errors with SSR:**
```javascript
// ❌ Wrong - Server/client mismatch
const Component = dynamic(() => import('./Component'));

// ✅ Correct - Disable SSR
const Component = dynamic(() => import('./Component'), {
  ssr: false
});
```

**3. Loading state flashing:**
```javascript
// ⚠️ Can cause layout shift
const Component = dynamic(() => import('./Component'), {
  loading: () => <div>Loading...</div>
});

// ✅ Better - Reserve space
const Component = dynamic(() => import('./Component'), {
  loading: () => <div className="h-96">Loading...</div>
});
```

---

##### Dynamic Import vs Static Import

| Feature | Static Import | Dynamic Import |
|---------|--------------|----------------|
| **Loading** | Build time | Runtime |
| **Bundle** | Always included | Separate chunk |
| **Initial Size** | Larger | Smaller |
| **When to load** | Immediately | On-demand |
| **SSR** | Always | Configurable |
| **Performance** | Good for critical | Better for non-critical |
| **Syntax** | `import X from 'x'` | `dynamic(() => import('x'))` |

---

##### Next.js Bundle Analysis

**See what's in your bundle:**

```bash
# Install
npm install @next/bundle-analyzer

# Configure
// next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer({
  // Your Next.js config
});

# Run analysis
ANALYZE=true npm run build
```

**Identify candidates for dynamic import:**
- Components > 50KB
- Third-party libraries
- Below-the-fold content
- Conditionally rendered components
- Admin-only features

---

#### 8. API Routes
Next.js allows you to create serverless functions directly within your application using API routes. This lets you add backend functionality to your React application without needing a separate server.

```javascript
// Pages Router - pages/api/users.js
export default function handler(req, res) {
  if (req.method === 'GET') {
    res.status(200).json({ users: ['Alice', 'Bob'] });
  } else if (req.method === 'POST') {
    const { name } = req.body;
    res.status(201).json({ message: `User ${name} created` });
  }
}

// App Router - app/api/users/route.js
export async function GET(request) {
  return Response.json({ users: ['Alice', 'Bob'] });
}

export async function POST(request) {
  const { name } = await request.json();
  return Response.json({ message: `User ${name} created` }, { status: 201 });
}
```

**Use cases:**
- ✅ Handle form submissions
- ✅ Integrate with databases
- ✅ Create REST or GraphQL APIs
- ✅ Proxy external API requests
- ✅ Authentication endpoints

#### 9. TypeScript Support
First-class TypeScript support with zero configuration.

```typescript
// Next.js automatically detects TypeScript
interface PageProps {
  params: { id: string };
  searchParams: { sort?: string };
}

export default async function Page({ params, searchParams }: PageProps) {
  const data: User = await fetchUser(params.id);
  return <div>{data.name}</div>;
}
```

#### 10. Middleware
Run code before a request is completed, enabling powerful features like authentication, redirects, and rewrites.

```javascript
// middleware.js
import { NextResponse } from 'next/server';

export function middleware(request) {
  // Check authentication
  const token = request.cookies.get('token');
  
  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: '/dashboard/:path*',
};
```

---

### Q1.2: What are Import Aliases and Path Mapping in Next.js?

**Answer:**

Import aliases (also called path aliases or absolute imports) allow you to use clean import paths instead of relative paths with multiple `../` in your Next.js project.

**Problem with Relative Imports:**

```javascript
// ❌ Messy relative imports
import { Button } from '../../../components/ui/Button';
import { formatDate } from '../../../../lib/utils/date';
import { User } from '../../../types/user';
import { db } from '../../../lib/database';
```

**Solution with Import Aliases:**

```javascript
// ✅ Clean absolute imports with aliases
import { Button } from '@/components/ui/Button';
import { formatDate } from '@/lib/utils/date';
import { User } from '@/types/user';
import { db } from '@/lib/database';
```

---

#### Setting Up Import Aliases

**1. TypeScript Projects (tsconfig.json):**

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"],           // Maps @/ to root directory
      "@/components/*": ["components/*"],
      "@/lib/*": ["lib/*"],
      "@/utils/*": ["lib/utils/*"],
      "@/types/*": ["types/*"],
      "@/styles/*": ["styles/*"],
      "@/public/*": ["public/*"]
    }
  }
}
```

**2. JavaScript Projects (jsconfig.json):**

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

**Next.js Default Configuration:**

When you create a new Next.js project with `create-next-app`, it automatically sets up the `@/` alias:

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

---

### Q1.3: How Does Routing Work in Next.js?

**Answer:**

Next.js uses **file-system based routing**, meaning the file and folder structure in your project automatically determines your application's routes. There are two routing systems depending on your Next.js version:

---

#### Pages Router (Next.js 12 and earlier)

**Basic Structure:**

```
pages/
├── index.js          → /
├── about.js          → /about
├── contact.js        → /contact
└── blog/
    ├── index.js      → /blog
    └── post.js       → /blog/post
```

**Creating Routes:**

```javascript
// pages/index.js → Homepage (/)
export default function Home() {
  return <h1>Home Page</h1>;
}

// pages/about.js → About page (/about)
export default function About() {
  return <h1>About Page</h1>;
}

// pages/blog/index.js → Blog home (/blog)
export default function Blog() {
  return <h1>Blog</h1>;
}
```

---

#### App Router (Next.js 13+)

The App Router introduces a new convention where each route is a folder containing a `page.js` file.

**Basic Structure:**

```
app/
├── page.js           → /
├── about/
│   └── page.js       → /about
├── contact/
│   └── page.js       → /contact
└── blog/
    ├── page.js       → /blog
    └── post/
        └── page.js   → /blog/post
```

**Creating Routes:**

```javascript
// app/page.js → Homepage (/)
export default function Home() {
  return <h1>Home Page</h1>;
}

// app/about/page.js → About page (/about)
export default function About() {
  return <h1>About Page</h1>;
}

// app/blog/page.js → Blog home (/blog)
export default function Blog() {
  return <h1>Blog</h1>;
}
```

---

#### Dynamic Routes

**Pages Router:**

```
pages/
├── blog/
│   └── [slug].js     → /blog/:slug
├── products/
│   └── [id].js       → /products/:id
└── users/
    └── [username].js → /users/:username
```

```javascript
// pages/blog/[slug].js
import { useRouter } from 'next/router';

export default function BlogPost() {
  const router = useRouter();
  const { slug } = router.query;
  
  return <h1>Post: {slug}</h1>;
}

// Accessing: /blog/hello-world → slug = "hello-world"
```

**App Router:**

```
app/
├── blog/
│   └── [slug]/
│       └── page.js   → /blog/:slug
├── products/
│   └── [id]/
│       └── page.js   → /products/:id
└── users/
    └── [username]/
        └── page.js   → /users/:username
```

```javascript
// app/blog/[slug]/page.js
export default function BlogPost({ params }) {
  return <h1>Post: {params.slug}</h1>;
}

// Accessing: /blog/hello-world → params.slug = "hello-world"
```

---

#### Catch-All Routes

**Match multiple segments:**

**Pages Router:**

```
pages/
└── docs/
    └── [...slug].js  → /docs/*, /docs/a/b/c
```

```javascript
// pages/docs/[...slug].js
import { useRouter } from 'next/router';

export default function Docs() {
  const router = useRouter();
  const { slug } = router.query;
  
  // /docs/a/b/c → slug = ['a', 'b', 'c']
  return <div>Path: {slug?.join('/')}</div>;
}
```

**App Router:**

```
app/
└── docs/
    └── [...slug]/
        └── page.js   → /docs/*, /docs/a/b/c
```

```javascript
// app/docs/[...slug]/page.js
export default function Docs({ params }) {
  // /docs/a/b/c → params.slug = ['a', 'b', 'c']
  return <div>Path: {params.slug.join('/')}</div>;
}
```

---

#### Optional Catch-All Routes

**Match route with or without segments:**

**Pages Router:**

```
pages/
└── shop/
    └── [[...slug]].js  → /shop, /shop/category, /shop/category/item
```

```javascript
// pages/shop/[[...slug]].js
import { useRouter } from 'next/router';

export default function Shop() {
  const router = useRouter();
  const { slug } = router.query;
  
  // /shop → slug = undefined
  // /shop/electronics → slug = ['electronics']
  // /shop/electronics/phones → slug = ['electronics', 'phones']
  
  return <div>Shop: {slug?.join('/') || 'Home'}</div>;
}
```

**App Router:**

```
app/
└── shop/
    └── [[...slug]]/
        └── page.js   → /shop, /shop/category, /shop/category/item
```

```javascript
// app/shop/[[...slug]]/page.js
export default function Shop({ params }) {
  const slug = params.slug || [];
  
  return <div>Shop: {slug.join('/') || 'Home'}</div>;
}
```

---

#### Route Groups (App Router Only)

**Organize routes without affecting URL:**

```
app/
├── (marketing)/      ← Not in URL
│   ├── about/
│   │   └── page.js   → /about
│   └── contact/
│       └── page.js   → /contact
├── (shop)/           ← Not in URL
│   ├── products/
│   │   └── page.js   → /products
│   └── cart/
│       └── page.js   → /cart
└── page.js           → /
```

**Use cases:**
- Organize code by feature
- Apply different layouts to groups
- Create multiple root layouts

```javascript
// app/(marketing)/layout.js
export default function MarketingLayout({ children }) {
  return (
    <div className="marketing-layout">
      <nav>Marketing Nav</nav>
      {children}
    </div>
  );
}

// app/(shop)/layout.js
export default function ShopLayout({ children }) {
  return (
    <div className="shop-layout">
      <nav>Shop Nav</nav>
      {children}
    </div>
  );
}
```

---

#### Parallel Routes (App Router Only)

**Render multiple pages in the same layout:**

```
app/
├── @dashboard/
│   └── page.js
├── @analytics/
│   └── page.js
└── layout.js
```

```javascript
// app/layout.js
export default function Layout({ dashboard, analytics }) {
  return (
    <div>
      <div className="dashboard-section">{dashboard}</div>
      <div className="analytics-section">{analytics}</div>
    </div>
  );
}
```

---

#### Intercepting Routes (App Router Only)

**Intercept navigation to show different content:**

```
app/
├── feed/
│   └── page.js
├── photos/
│   └── [id]/
│       └── page.js
└── @modal/
    └── (.)photos/
        └── [id]/
            └── page.js
```

**Convention:**
- `(.)` - same level
- `(..)` - one level up
- `(..)(..)` - two levels up
- `(...)` - root

```javascript
// app/@modal/(.)photos/[id]/page.js
export default function PhotoModal({ params }) {
  return (
    <div className="modal">
      <h1>Photo {params.id} in modal</h1>
    </div>
  );
}
```

---

#### Navigation

**Pages Router:**

```javascript
import Link from 'next/link';
import { useRouter } from 'next/router';

export default function Navigation() {
  const router = useRouter();
  
  return (
    <>
      {/* Link component */}
      <Link href="/about">About</Link>
      <Link href="/blog/hello">Blog Post</Link>
      
      {/* Programmatic navigation */}
      <button onClick={() => router.push('/dashboard')}>
        Go to Dashboard
      </button>
      
      {/* With query params */}
      <Link href={{ pathname: '/search', query: { q: 'nextjs' } }}>
        Search
      </Link>
    </>
  );
}
```

**App Router:**

```javascript
'use client';

import Link from 'next/link';
import { useRouter } from 'next/navigation';

export default function Navigation() {
  const router = useRouter();
  
  return (
    <>
      {/* Link component */}
      <Link href="/about">About</Link>
      <Link href="/blog/hello">Blog Post</Link>
      
      {/* Programmatic navigation */}
      <button onClick={() => router.push('/dashboard')}>
        Go to Dashboard
      </button>
      
      {/* With replace (no history entry) */}
      <button onClick={() => router.replace('/login')}>
        Login
      </button>
    </>
  );
}
```

---

#### Special Files in App Router

```
app/
├── layout.js         # Shared UI for segment and children
├── page.js           # Unique UI for route
├── loading.js        # Loading UI (Suspense boundary)
├── error.js          # Error UI (Error boundary)
├── not-found.js      # Not found UI
├── template.js       # Re-rendered layout
└── route.js          # API endpoint
```

**Example structure:**

```javascript
// app/dashboard/layout.js
export default function DashboardLayout({ children }) {
  return (
    <div className="dashboard">
      <nav>Dashboard Nav</nav>
      {children}
    </div>
  );
}

// app/dashboard/loading.js
export default function Loading() {
  return <div>Loading dashboard...</div>;
}

// app/dashboard/error.js
'use client';

export default function Error({ error, reset }) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}

// app/dashboard/page.js
export default function Dashboard() {
  return <h1>Dashboard Page</h1>;
}
```

---

#### Layouts vs Templates

**Layouts:**
- Preserve state across navigation
- Don't re-render on navigation

```javascript
// app/layout.js
export default function Layout({ children }) {
  return (
    <div>
      <Header />
      {children}
    </div>
  );
}
```

**Templates:**
- Create new instance on navigation
- Do re-render on navigation

```javascript
// app/template.js
export default function Template({ children }) {
  return (
    <div>
      <AnimatedWrapper>{children}</AnimatedWrapper>
    </div>
  );
}
```

---

#### Route Handlers (API Routes)

**Pages Router:**

```
pages/
└── api/
    ├── users.js      → /api/users
    └── posts/
        └── [id].js   → /api/posts/:id
```

```javascript
// pages/api/users.js
export default function handler(req, res) {
  if (req.method === 'GET') {
    res.status(200).json({ users: [] });
  } else if (req.method === 'POST') {
    res.status(201).json({ message: 'User created' });
  }
}
```

**App Router:**

```
app/
└── api/
    ├── users/
    │   └── route.js  → /api/users
    └── posts/
        └── [id]/
            └── route.js → /api/posts/:id
```

```javascript
// app/api/users/route.js
export async function GET(request) {
  return Response.json({ users: [] });
}

export async function POST(request) {
  const data = await request.json();
  return Response.json({ message: 'User created' }, { status: 201 });
}
```

---

#### Middleware

**Run code before request completes:**

```javascript
// middleware.js (root level)
import { NextResponse } from 'next/server';

export function middleware(request) {
  // Check auth
  const token = request.cookies.get('token');
  
  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/admin/:path*'],
};
```

---

#### Redirects and Rewrites

**next.config.js:**

```javascript
module.exports = {
  async redirects() {
    return [
      {
        source: '/old-blog/:slug',
        destination: '/blog/:slug',
        permanent: true,
      },
    ];
  },
  async rewrites() {
    return [
      {
        source: '/api/:path*',
        destination: 'https://external-api.com/:path*',
      },
    ];
  },
};
```

---

#### Best Practices

**1. Use Link component for navigation:**

```javascript
// ✅ Good - Client-side navigation
import Link from 'next/link';

<Link href="/about">About</Link>

// ❌ Bad - Full page reload
<a href="/about">About</a>
```

**2. Prefetching:**

```javascript
// Automatically prefetches on Link hover (production)
<Link href="/dashboard" prefetch={true}>
  Dashboard
</Link>

// Disable prefetch
<Link href="/heavy-page" prefetch={false}>
  Heavy Page
</Link>
```

**3. Organize by feature (App Router):**

```
app/
├── (auth)/
│   ├── login/
│   └── register/
├── (dashboard)/
│   ├── profile/
│   └── settings/
└── (marketing)/
    ├── about/
    └── pricing/
```

**4. Co-locate related files:**

```
app/
└── dashboard/
    ├── page.js
    ├── loading.js
    ├── error.js
    ├── layout.js
    ├── components/
    │   └── DashboardWidget.js
    └── utils/
        └── helpers.js
```

---

#### Comparison: Pages vs App Router

| Feature | Pages Router | App Router |
|---------|-------------|------------|
| **File Convention** | `pages/about.js` | `app/about/page.js` |
| **Dynamic Routes** | `[id].js` | `[id]/page.js` |
| **Layouts** | `_app.js` only | Multiple nested layouts |
| **Loading UI** | Manual | `loading.js` |
| **Error Handling** | Manual | `error.js` |
| **Server Components** | ❌ No | ✅ Yes |
| **Streaming** | ❌ No | ✅ Yes |
| **Route Groups** | ❌ No | ✅ Yes |
| **Parallel Routes** | ❌ No | ✅ Yes |

---

### Q1.4: How to Get the Current Route in Next.js?

**Answer:**

Getting the current route depends on whether you're using the Pages Router or App Router, and whether you're in a Client Component or Server Component.

---

#### Pages Router - Getting Current Route

**1. Using `useRouter` Hook:**

```javascript
'use client';

import { useRouter } from 'next/router';

export default function Navigation() {
  const router = useRouter();
  
  console.log('Current pathname:', router.pathname);     // /blog/[slug]
  console.log('Current route:', router.route);          // /blog/[slug]
  console.log('Actual path:', router.asPath);           // /blog/hello-world
  console.log('Query params:', router.query);           // { slug: 'hello-world' }
  console.log('Base path:', router.basePath);           // '' or '/docs'
  console.log('Locale:', router.locale);                // 'en' or undefined
  
  return (
    <nav>
      <a className={router.pathname === '/' ? 'active' : ''}>
        Home
      </a>
      <a className={router.pathname === '/about' ? 'active' : ''}>
        About
      </a>
    </nav>
  );
}
```

**Key Properties:**

```javascript
import { useRouter } from 'next/router';

function Component() {
  const router = useRouter();
  
  return (
    <div>
      {/* pathname: Route pattern */}
      <p>Route: {router.pathname}</p>
      {/* Example: /products/[id] */}
      
      {/* asPath: Actual URL path */}
      <p>Path: {router.asPath}</p>
      {/* Example: /products/123?color=red */}
      
      {/* query: All params and query strings */}
      <p>Params: {JSON.stringify(router.query)}</p>
      {/* Example: { id: '123', color: 'red' } */}
    </div>
  );
}
```

**2. Using `withRouter` HOC:**

```javascript
import { withRouter } from 'next/router';

function MyComponent({ router }) {
  return (
    <div>
      <h1>Current Page: {router.pathname}</h1>
    </div>
  );
}

export default withRouter(MyComponent);
```

**3. In getServerSideProps:**

```javascript
export async function getServerSideProps(context) {
  console.log('Request URL:', context.req.url);
  console.log('Resolved URL:', context.resolvedUrl);
  console.log('Query:', context.query);
  console.log('Params:', context.params);
  
  return { props: {} };
}
```

---

#### App Router - Getting Current Route

**1. Using `usePathname` Hook (Client Component):**

```javascript
'use client';

import { usePathname } from 'next/navigation';

export default function Navigation() {
  const pathname = usePathname();
  
  console.log('Current pathname:', pathname); // /blog/hello-world
  
  return (
    <nav>
      <a className={pathname === '/' ? 'active' : ''}>
        Home
      </a>
      <a className={pathname === '/about' ? 'active' : ''}>
        About
      </a>
      <a className={pathname.startsWith('/blog') ? 'active' : ''}>
        Blog
      </a>
    </nav>
  );
}
```

**2. Using `useSearchParams` Hook (Client Component):**

```javascript
'use client';

import { useSearchParams } from 'next/navigation';

export default function SearchPage() {
  const searchParams = useSearchParams();
  
  const query = searchParams.get('q');
  const category = searchParams.get('category');
  const page = searchParams.get('page') || '1';
  
  console.log('Query:', query);           // 'nextjs'
  console.log('Category:', category);     // 'tutorials'
  console.log('Page:', page);            // '2'
  
  // Get all params
  const allParams = Object.fromEntries(searchParams.entries());
  console.log('All params:', allParams);
  
  return (
    <div>
      <h1>Search: {query}</h1>
      <p>Category: {category}</p>
      <p>Page: {page}</p>
    </div>
  );
}
```

**3. Using `useParams` Hook (Client Component):**

```javascript
'use client';

import { useParams } from 'next/navigation';

export default function ProductPage() {
  const params = useParams();
  
  console.log('Dynamic params:', params);
  // For /products/[id] → { id: '123' }
  // For /blog/[category]/[slug] → { category: 'tech', slug: 'hello' }
  
  return (
    <div>
      <h1>Product ID: {params.id}</h1>
    </div>
  );
}
```

**4. In Server Components:**

```javascript
// app/products/[id]/page.js
export default function ProductPage({ params, searchParams }) {
  console.log('Params:', params);           // { id: '123' }
  console.log('Search params:', searchParams); // { color: 'red', size: 'L' }
  
  return (
    <div>
      <h1>Product {params.id}</h1>
      <p>Color: {searchParams.color}</p>
      <p>Size: {searchParams.size}</p>
    </div>
  );
}
```

**5. Using headers() for Full URL (Server Component):**

```javascript
import { headers } from 'next/headers';

export default function Page() {
  const headersList = headers();
  const pathname = headersList.get('x-pathname');
  const fullUrl = headersList.get('x-url');
  
  return (
    <div>
      <h1>Current Path: {pathname}</h1>
    </div>
  );
}
```

---

#### Complete Comparison Table

| Method | Pages Router | App Router | Where to Use |
|--------|-------------|------------|--------------|
| **Get pathname** | `useRouter().pathname` | `usePathname()` | Client Component |
| **Get query params** | `useRouter().query` | `useSearchParams()` | Client Component |
| **Get dynamic params** | `useRouter().query` | `useParams()` | Client Component |
| **Full path with query** | `useRouter().asPath` | `pathname + searchParams` | Client Component |
| **Server-side** | `context.req.url` | `params, searchParams` props | Server Component/SSR |
| **Check active route** | `router.pathname === '/path'` | `pathname === '/path'` | Client Component |

---

#### Real-World Examples

**1. Active Link Component (Pages Router):**

```javascript
'use client';

import Link from 'next/link';
import { useRouter } from 'next/router';

export function NavLink({ href, children }) {
  const router = useRouter();
  const isActive = router.pathname === href;
  
  return (
    <Link 
      href={href}
      className={isActive ? 'text-blue-600 font-bold' : 'text-gray-600'}
    >
      {children}
    </Link>
  );
}

// Usage
<NavLink href="/">Home</NavLink>
<NavLink href="/about">About</NavLink>
```

**2. Active Link Component (App Router):**

```javascript
'use client';

import Link from 'next/link';
import { usePathname } from 'next/navigation';

export function NavLink({ href, children }) {
  const pathname = usePathname();
  const isActive = pathname === href;
  
  return (
    <Link 
      href={href}
      className={isActive ? 'text-blue-600 font-bold' : 'text-gray-600'}
    >
      {children}
    </Link>
  );
}

// Usage
<NavLink href="/">Home</NavLink>
<NavLink href="/about">About</NavLink>
```

**3. Breadcrumbs (App Router):**

```javascript
'use client';

import { usePathname } from 'next/navigation';
import Link from 'next/link';

export function Breadcrumbs() {
  const pathname = usePathname();
  const segments = pathname.split('/').filter(Boolean);
  
  return (
    <nav className="breadcrumbs">
      <Link href="/">Home</Link>
      {segments.map((segment, index) => {
        const href = `/${segments.slice(0, index + 1).join('/')}`;
        const isLast = index === segments.length - 1;
        
        return (
          <span key={href}>
            <span className="separator"> / </span>
            {isLast ? (
              <span className="current">{segment}</span>
            ) : (
              <Link href={href}>{segment}</Link>
            )}
          </span>
        );
      })}
    </nav>
  );
}

// For URL: /products/electronics/phones
// Renders: Home / products / electronics / phones
```

**4. Search with URL Params (App Router):**

```javascript
'use client';

import { useSearchParams, useRouter } from 'next/navigation';

export function SearchBar() {
  const searchParams = useSearchParams();
  const router = useRouter();
  const currentQuery = searchParams.get('q') || '';
  
  const handleSearch = (query) => {
    const params = new URLSearchParams(searchParams);
    
    if (query) {
      params.set('q', query);
    } else {
      params.delete('q');
    }
    
    router.push(`/search?${params.toString()}`);
  };
  
  return (
    <input
      type="search"
      defaultValue={currentQuery}
      onChange={(e) => handleSearch(e.target.value)}
      placeholder="Search..."
    />
  );
}
```

**5. Pagination (App Router):**

```javascript
'use client';

import { useSearchParams, usePathname, useRouter } from 'next/navigation';

export function Pagination({ totalPages }) {
  const searchParams = useSearchParams();
  const pathname = usePathname();
  const router = useRouter();
  
  const currentPage = Number(searchParams.get('page')) || 1;
  
  const goToPage = (page) => {
    const params = new URLSearchParams(searchParams);
    params.set('page', page.toString());
    router.push(`${pathname}?${params.toString()}`);
  };
  
  return (
    <div className="pagination">
      <button 
        disabled={currentPage === 1}
        onClick={() => goToPage(currentPage - 1)}
      >
        Previous
      </button>
      
      <span>Page {currentPage} of {totalPages}</span>
      
      <button 
        disabled={currentPage === totalPages}
        onClick={() => goToPage(currentPage + 1)}
      >
        Next
      </button>
    </div>
  );
}
```

**6. Route Guard (Pages Router):**

```javascript
'use client';

import { useRouter } from 'next/router';
import { useEffect } from 'react';

export function ProtectedRoute({ children, user }) {
  const router = useRouter();
  
  useEffect(() => {
    if (!user) {
      // Redirect to login, preserve return URL
      router.push(`/login?returnUrl=${router.asPath}`);
    }
  }, [user, router]);
  
  if (!user) {
    return <div>Loading...</div>;
  }
  
  return children;
}
```

**7. Track Page Views (App Router):**

```javascript
'use client';

import { usePathname, useSearchParams } from 'next/navigation';
import { useEffect } from 'react';

export function Analytics() {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  
  useEffect(() => {
    const url = pathname + (searchParams.toString() ? `?${searchParams}` : '');
    
    // Track page view
    console.log('Page view:', url);
    // analytics.track(url);
  }, [pathname, searchParams]);
  
  return null;
}

// In layout.js
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <Analytics />
        {children}
      </body>
    </html>
  );
}
```

---

#### Common Patterns

**1. Check if on specific route:**

```javascript
// Pages Router
const router = useRouter();
const isHome = router.pathname === '/';
const isBlog = router.pathname.startsWith('/blog');
const isProduct = router.pathname === '/products/[id]';

// App Router
const pathname = usePathname();
const isHome = pathname === '/';
const isBlog = pathname.startsWith('/blog');
const isProduct = pathname.includes('/products/');
```

**2. Get specific query parameter:**

```javascript
// Pages Router
const router = useRouter();
const { id, category } = router.query;

// App Router
const searchParams = useSearchParams();
const id = searchParams.get('id');
const category = searchParams.get('category');
```

**3. Build URL with preserved params:**

```javascript
// App Router
const searchParams = useSearchParams();

function updateParam(key, value) {
  const params = new URLSearchParams(searchParams);
  params.set(key, value);
  return `?${params.toString()}`;
}

// Usage
<Link href={`/products${updateParam('sort', 'price')}`}>
  Sort by Price
</Link>
```

**4. Remove query parameter:**

```javascript
// App Router
const searchParams = useSearchParams();
const router = useRouter();
const pathname = usePathname();

function removeParam(key) {
  const params = new URLSearchParams(searchParams);
  params.delete(key);
  const query = params.toString();
  router.push(`${pathname}${query ? `?${query}` : ''}`);
}
```

---

#### Best Practices

**1. Use appropriate hooks:**

```javascript
// ✅ Good - App Router
'use client';
import { usePathname } from 'next/navigation';

// ❌ Bad - Wrong import
import { useRouter } from 'next/router'; // Pages Router API
```

**2. Memoize route checks:**

```javascript
'use client';

import { usePathname } from 'next/navigation';
import { useMemo } from 'react';

export function Navigation() {
  const pathname = usePathname();
  
  const isActive = useMemo(() => ({
    home: pathname === '/',
    blog: pathname.startsWith('/blog'),
    about: pathname === '/about',
  }), [pathname]);
  
  return (
    <nav>
      <Link className={isActive.home ? 'active' : ''}>Home</Link>
      <Link className={isActive.blog ? 'active' : ''}>Blog</Link>
      <Link className={isActive.about ? 'active' : ''}>About</Link>
    </nav>
  );
}
```

**3. Handle SSR safely:**

```javascript
// ✅ Good - Check if window exists
'use client';

import { usePathname } from 'next/navigation';

export function Component() {
  const pathname = usePathname();
  
  // Client-side only code
  if (typeof window !== 'undefined') {
    localStorage.setItem('lastPath', pathname);
  }
  
  return <div>Current: {pathname}</div>;
}
```

**4. Use Server Components when possible:**

```javascript
// ✅ Good - Server Component
export default function Page({ params, searchParams }) {
  // No hooks needed, props automatically provided
  return <div>Product {params.id}</div>;
}

// ❌ Less efficient - Client Component
'use client';
import { useParams } from 'next/navigation';

export default function Page() {
  const params = useParams();
  return <div>Product {params.id}</div>;
}
```

---

#### Migration Guide

**From Pages Router to App Router:**

```javascript
// Before (Pages Router)
import { useRouter } from 'next/router';

function Component() {
  const router = useRouter();
  const pathname = router.pathname;
  const query = router.query;
  const asPath = router.asPath;
}

// After (App Router)
'use client';

import { usePathname, useSearchParams, useParams } from 'next/navigation';

function Component() {
  const pathname = usePathname();
  const searchParams = useSearchParams();
  const params = useParams();
  
  const query = Object.fromEntries(searchParams.entries());
  const asPath = pathname + (searchParams.toString() ? `?${searchParams}` : '');
}
```

---

### Q1.5: What is getServerSideProps() and How Does It Work?

**Answer:**

`getServerSideProps()` is a Next.js function (Pages Router) that enables Server-Side Rendering (SSR). It runs on every request, fetches data on the server, and passes it as props to your page component.

---

#### What is getServerSideProps()?

`getServerSideProps()` is a special Next.js function that:
- Runs **only on the server** (never in the browser)
- Executes on **every request** (not at build time)
- Allows you to fetch data and pass it to your page as props
- Makes your page SEO-friendly by rendering complete HTML on the server

**Key Characteristics:**
- ✅ Runs on every page request
- ✅ Has access to request context (cookies, headers, query params)
- ✅ Can't be used in Client Components (Pages Router only)
- ✅ Code never sent to the client (safe for API keys, database queries)
- ✅ Perfect for dynamic, personalized content

---

#### Basic Syntax

```javascript
// pages/profile.js
export async function getServerSideProps(context) {
  // Fetch data from API or database
  const res = await fetch('https://api.example.com/user');
  const user = await res.json();
  
  // Pass data to page via props
  return {
    props: {
      user,
    },
  };
}

export default function Profile({ user }) {
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

---

#### When to Use getServerSideProps()

**✅ Use getServerSideProps when:**
- Data changes frequently and must be up-to-date
- Content is personalized per user
- You need request-time information (cookies, headers)
- SEO is critical AND content is dynamic
- Building dashboards with real-time data

**❌ Don't use getServerSideProps when:**
- Data doesn't change often (use `getStaticProps` with ISR)
- SEO isn't important (use client-side fetching)
- You're using App Router (use Server Components instead)

**Examples:**
```javascript
// ✅ Good use cases
- User dashboard with personal data
- Real-time stock prices
- Personalized product recommendations
- Admin panels with live data
- Pages requiring authentication

// ❌ Better alternatives exist
- Blog posts → Use getStaticProps with ISR
- Static marketing pages → Use getStaticProps
- Client-only data → Use useEffect + fetch
```

---

#### The Context Object

`getServerSideProps` receives a `context` object with useful information:

```javascript
export async function getServerSideProps(context) {
  const {
    req,           // HTTP request object
    res,           // HTTP response object
    params,        // Route parameters (dynamic routes)
    query,         // Query string parameters
    preview,       // Preview mode boolean
    previewData,   // Preview mode data
    resolvedUrl,   // Resolved URL path
    locale,        // Active locale (i18n)
    locales,       // All locales (i18n)
    defaultLocale, // Default locale (i18n)
  } = context;
  
  return { props: {} };
}
```

---

#### Working with Request and Response

**Access cookies:**

```javascript
export async function getServerSideProps(context) {
  const { req } = context;
  
  // Get cookie
  const token = req.cookies.token;
  
  if (!token) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    };
  }
  
  // Fetch user data with token
  const user = await fetchUser(token);
  
  return { props: { user } };
}
```

**Access headers:**

```javascript
export async function getServerSideProps(context) {
  const { req } = context;
  
  const userAgent = req.headers['user-agent'];
  const ip = req.headers['x-forwarded-for'] || req.socket.remoteAddress;
  
  return {
    props: {
      userAgent,
      ip,
    },
  };
}
```

**Set response headers:**

```javascript
export async function getServerSideProps(context) {
  const { res } = context;
  
  // Set custom headers
  res.setHeader('Cache-Control', 'public, s-maxage=10, stale-while-revalidate=59');
  
  const data = await fetchData();
  
  return { props: { data } };
}
```

---

#### Dynamic Routes and Params

```javascript
// pages/products/[id].js
export async function getServerSideProps(context) {
  const { params } = context;
  const { id } = params;
  
  const product = await fetch(`https://api.example.com/products/${id}`)
    .then(res => res.json());
  
  return {
    props: {
      product,
    },
  };
}

export default function Product({ product }) {
  return (
    <div>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
    </div>
  );
}
```

---

#### Query Parameters

```javascript
// pages/search.js
export async function getServerSideProps(context) {
  const { query } = context;
  
  // URL: /search?q=laptop&category=electronics&page=2
  const searchQuery = query.q || '';
  const category = query.category || 'all';
  const page = parseInt(query.page) || 1;
  
  const results = await fetch(
    `https://api.example.com/search?q=${searchQuery}&category=${category}&page=${page}`
  ).then(res => res.json());
  
  return {
    props: {
      results,
      searchQuery,
      category,
      page,
    },
  };
}

export default function Search({ results, searchQuery, category, page }) {
  return (
    <div>
      <h1>Search Results for: {searchQuery}</h1>
      <p>Category: {category} | Page: {page}</p>
      <ul>
        {results.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

---

#### Return Values

`getServerSideProps` can return different objects:

**1. Props (most common):**

```javascript
export async function getServerSideProps() {
  const data = await fetchData();
  
  return {
    props: {
      data,
    },
  };
}
```

**2. Redirect:**

```javascript
export async function getServerSideProps(context) {
  const { req } = context;
  const token = req.cookies.token;
  
  if (!token) {
    return {
      redirect: {
        destination: '/login',
        permanent: false, // 307 redirect
      },
    };
  }
  
  return { props: {} };
}
```

**3. Not Found (404):**

```javascript
export async function getServerSideProps(context) {
  const { params } = context;
  
  const post = await fetch(`https://api.example.com/posts/${params.id}`)
    .then(res => res.json())
    .catch(() => null);
  
  if (!post) {
    return {
      notFound: true, // Shows 404 page
    };
  }
  
  return {
    props: { post },
  };
}
```

---

#### Real-World Examples

**1. User Dashboard (Authentication Required):**

```javascript
// pages/dashboard.js
import { parseCookies } from 'nookies';

export async function getServerSideProps(context) {
  const cookies = parseCookies(context);
  const token = cookies.token;
  
  // Check authentication
  if (!token) {
    return {
      redirect: {
        destination: '/login?returnUrl=/dashboard',
        permanent: false,
      },
    };
  }
  
  try {
    // Fetch user data
    const user = await fetch('https://api.example.com/me', {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    }).then(res => {
      if (!res.ok) throw new Error('Unauthorized');
      return res.json();
    });
    
    // Fetch dashboard data
    const [stats, recentActivity] = await Promise.all([
      fetch('https://api.example.com/stats', {
        headers: { Authorization: `Bearer ${token}` },
      }).then(res => res.json()),
      fetch('https://api.example.com/activity', {
        headers: { Authorization: `Bearer ${token}` },
      }).then(res => res.json()),
    ]);
    
    return {
      props: {
        user,
        stats,
        recentActivity,
      },
    };
  } catch (error) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    };
  }
}

export default function Dashboard({ user, stats, recentActivity }) {
  return (
    <div>
      <h1>Welcome, {user.name}!</h1>
      <div className="stats">
        <div>Total Sales: ${stats.totalSales}</div>
        <div>Orders: {stats.orders}</div>
        <div>Customers: {stats.customers}</div>
      </div>
      <div className="activity">
        <h2>Recent Activity</h2>
        <ul>
          {recentActivity.map(activity => (
            <li key={activity.id}>{activity.message}</li>
          ))}
        </ul>
      </div>
    </div>
  );
}
```

**2. E-commerce Product Page:**

```javascript
// pages/products/[slug].js
export async function getServerSideProps(context) {
  const { params, req } = context;
  const { slug } = params;
  
  // Fetch product
  const product = await fetch(`https://api.example.com/products/${slug}`)
    .then(res => res.json())
    .catch(() => null);
  
  if (!product) {
    return { notFound: true };
  }
  
  // Get user's location for pricing
  const country = req.headers['cf-ipcountry'] || 'US';
  
  // Get personalized recommendations
  const userId = req.cookies.userId;
  const recommendations = userId
    ? await fetch(`https://api.example.com/recommendations?userId=${userId}&productId=${product.id}`)
        .then(res => res.json())
    : [];
  
  return {
    props: {
      product,
      country,
      recommendations,
    },
  };
}

export default function Product({ product, country, recommendations }) {
  return (
    <div>
      <h1>{product.name}</h1>
      <p className="price">
        {product.prices[country]} {product.currency}
      </p>
      <p>{product.description}</p>
      
      {recommendations.length > 0 && (
        <div className="recommendations">
          <h2>You might also like</h2>
          <div className="products">
            {recommendations.map(rec => (
              <ProductCard key={rec.id} product={rec} />
            ))}
          </div>
        </div>
      )}
    </div>
  );
}
```

**3. Blog Post with View Counter:**

```javascript
// pages/blog/[slug].js
import { db } from '@/lib/database';

export async function getServerSideProps(context) {
  const { params, req, res } = context;
  const { slug } = params;
  
  // Set cache headers (cache for 60 seconds)
  res.setHeader(
    'Cache-Control',
    'public, s-maxage=60, stale-while-revalidate=120'
  );
  
  // Fetch post
  const post = await db.post.findUnique({
    where: { slug },
    include: {
      author: true,
      comments: {
        orderBy: { createdAt: 'desc' },
        take: 10,
      },
    },
  });
  
  if (!post) {
    return { notFound: true };
  }
  
  // Increment view count (don't await - fire and forget)
  db.post.update({
    where: { id: post.id },
    data: { views: { increment: 1 } },
  }).catch(console.error);
  
  return {
    props: {
      post: JSON.parse(JSON.stringify(post)), // Serialize dates
    },
  };
}

export default function BlogPost({ post }) {
  return (
    <article>
      <h1>{post.title}</h1>
      <div className="meta">
        <span>By {post.author.name}</span>
        <span>{post.views} views</span>
        <span>{new Date(post.createdAt).toLocaleDateString()}</span>
      </div>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
      
      <div className="comments">
        <h2>Comments ({post.comments.length})</h2>
        {post.comments.map(comment => (
          <div key={comment.id}>
            <strong>{comment.author}</strong>
            <p>{comment.text}</p>
          </div>
        ))}
      </div>
    </article>
  );
}
```

**4. Admin Panel with Role-Based Access:**

```javascript
// pages/admin/users.js
import { verifyToken, hasRole } from '@/lib/auth';

export async function getServerSideProps(context) {
  const { req } = context;
  const token = req.cookies.authToken;
  
  // Verify authentication
  let user;
  try {
    user = await verifyToken(token);
  } catch (error) {
    return {
      redirect: {
        destination: '/login?returnUrl=/admin/users',
        permanent: false,
      },
    };
  }
  
  // Check authorization
  if (!hasRole(user, 'admin')) {
    return {
      redirect: {
        destination: '/unauthorized',
        permanent: false,
      },
    };
  }
  
  // Fetch users
  const { page = 1, search = '' } = context.query;
  const limit = 20;
  const offset = (page - 1) * limit;
  
  const [users, totalCount] = await Promise.all([
    db.user.findMany({
      where: {
        name: { contains: search, mode: 'insensitive' },
      },
      skip: offset,
      take: limit,
      orderBy: { createdAt: 'desc' },
    }),
    db.user.count({
      where: {
        name: { contains: search, mode: 'insensitive' },
      },
    }),
  ]);
  
  return {
    props: {
      users: JSON.parse(JSON.stringify(users)),
      totalCount,
      currentPage: parseInt(page),
      totalPages: Math.ceil(totalCount / limit),
    },
  };
}

export default function AdminUsers({ users, currentPage, totalPages }) {
  return (
    <div className="admin-panel">
      <h1>User Management</h1>
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Email</th>
            <th>Role</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          {users.map(user => (
            <tr key={user.id}>
              <td>{user.id}</td>
              <td>{user.name}</td>
              <td>{user.email}</td>
              <td>{user.role}</td>
              <td>
                <button>Edit</button>
                <button>Delete</button>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
      
      <div className="pagination">
        <button disabled={currentPage === 1}>Previous</button>
        <span>Page {currentPage} of {totalPages}</span>
        <button disabled={currentPage === totalPages}>Next</button>
      </div>
    </div>
  );
}
```

---

#### Error Handling

**Proper error handling:**

```javascript
export async function getServerSideProps(context) {
  const { params } = context;
  
  try {
    const data = await fetch(`https://api.example.com/data/${params.id}`)
      .then(res => {
        if (!res.ok) {
          if (res.status === 404) throw new Error('NOT_FOUND');
          throw new Error('FETCH_ERROR');
        }
        return res.json();
      });
    
    return {
      props: { data },
    };
  } catch (error) {
    console.error('Error in getServerSideProps:', error);
    
    if (error.message === 'NOT_FOUND') {
      return { notFound: true };
    }
    
    // Return error state
    return {
      props: {
        error: 'Failed to load data',
      },
    };
  }
}

export default function Page({ data, error }) {
  if (error) {
    return <div className="error">{error}</div>;
  }
  
  return <div>{data.title}</div>;
}
```

---

#### Performance Optimization

**1. Parallel Data Fetching:**

```javascript
// ❌ Bad - Sequential (slow)
export async function getServerSideProps() {
  const user = await fetchUser();
  const posts = await fetchPosts();
  const comments = await fetchComments();
  
  return { props: { user, posts, comments } };
}

// ✅ Good - Parallel (fast)
export async function getServerSideProps() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(),
    fetchPosts(),
    fetchComments(),
  ]);
  
  return { props: { user, posts, comments } };
}
```

**2. Caching with Headers:**

```javascript
export async function getServerSideProps(context) {
  const { res } = context;
  
  // Cache for 60 seconds, serve stale for 120 seconds while revalidating
  res.setHeader(
    'Cache-Control',
    'public, s-maxage=60, stale-while-revalidate=120'
  );
  
  const data = await fetchData();
  
  return { props: { data } };
}
```

**3. Conditional Data Fetching:**

```javascript
export async function getServerSideProps(context) {
  const { req, query } = context;
  const userId = req.cookies.userId;
  
  // Only fetch user-specific data if logged in
  const userData = userId ? await fetchUserData(userId) : null;
  
  // Always fetch public data
  const publicData = await fetchPublicData();
  
  return {
    props: {
      userData,
      publicData,
    },
  };
}
```

---

#### Common Patterns

**1. Authentication Check:**

```javascript
export async function getServerSideProps(context) {
  const { req } = context;
  const session = await getSession(context);
  
  if (!session) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    };
  }
  
  return {
    props: { user: session.user },
  };
}
```

**2. API Key Protection:**

```javascript
export async function getServerSideProps() {
  // API key is safe here - never sent to client
  const data = await fetch('https://api.example.com/data', {
    headers: {
      'Authorization': `Bearer ${process.env.SECRET_API_KEY}`,
    },
  }).then(res => res.json());
  
  return { props: { data } };
}
```

**3. Database Queries:**

```javascript
import { db } from '@/lib/database';

export async function getServerSideProps() {
  const users = await db.user.findMany({
    where: { active: true },
    select: {
      id: true,
      name: true,
      email: true,
    },
  });
  
  return {
    props: {
      users: JSON.parse(JSON.stringify(users)), // Serialize dates
    },
  };
}
```

**4. Internationalization (i18n):**

```javascript
export async function getServerSideProps(context) {
  const { locale } = context;
  
  const translations = await import(`@/locales/${locale}.json`);
  
  return {
    props: {
      translations: translations.default,
    },
  };
}
```

---

#### Best Practices

**1. Keep it fast:**

```javascript
// ✅ Good - Quick queries
export async function getServerSideProps() {
  const data = await db.post.findMany({
    take: 10,
    select: { id: true, title: true }, // Only select needed fields
  });
  
  return { props: { data } };
}

// ❌ Bad - Slow queries
export async function getServerSideProps() {
  const data = await db.post.findMany({
    include: { comments: { include: { author: true } } }, // Too much data
  });
  
  return { props: { data } };
}
```

**2. Serialize dates properly:**

```javascript
// ✅ Good - Serialize dates
export async function getServerSideProps() {
  const data = await fetchData();
  
  return {
    props: {
      data: JSON.parse(JSON.stringify(data)), // Converts dates to strings
    },
  };
}

// ❌ Bad - Runtime error
export async function getServerSideProps() {
  const data = await fetchData();
  
  return {
    props: { data }, // Dates can't be serialized
  };
}
```

**3. Use type safety:**

```typescript
import { GetServerSideProps } from 'next';

interface PageProps {
  user: {
    id: string;
    name: string;
    email: string;
  };
}

export const getServerSideProps: GetServerSideProps<PageProps> = async (context) => {
  const user = await fetchUser();
  
  return {
    props: { user },
  };
};

export default function Page({ user }: PageProps) {
  return <div>{user.name}</div>;
}
```

**4. Handle errors gracefully:**

```javascript
export async function getServerSideProps() {
  try {
    const data = await fetchData();
    return { props: { data } };
  } catch (error) {
    console.error('Error:', error);
    return {
      props: {
        error: 'Failed to load data',
      },
    };
  }
}
```

---

#### Comparison with Other Data Fetching Methods

| Feature | getServerSideProps | getStaticProps | Client-side Fetch |
|---------|-------------------|----------------|-------------------|
| **When runs** | Every request | Build time | After page load |
| **Performance** | Slower (server processing) | Fastest (pre-built) | Medium (after hydration) |
| **SEO** | ✅ Excellent | ✅ Excellent | ❌ Poor |
| **Data freshness** | Always fresh | Stale (until rebuild) | Fresh (after load) |
| **Use case** | Dynamic, personalized | Static, rarely changes | Client-only data |
| **Blocking** | Yes (waits for data) | No (pre-built) | No (shows loading) |

---

#### When to Use Each Method

**Use getServerSideProps:**
```javascript
// ✅ User-specific data
- User dashboard
- Shopping cart
- Personalized recommendations

// ✅ Real-time data
- Stock prices
- Live scores
- Current weather

// ✅ Request-dependent data
- Geolocation-based content
- A/B testing
- Analytics tracking
```

**Use getStaticProps + ISR:**
```javascript
// ✅ Content that updates periodically
- Blog posts
- Product catalogs
- News articles

export async function getStaticProps() {
  return {
    props: { data },
    revalidate: 60, // Regenerate every 60 seconds
  };
}
```

**Use client-side fetching:**
```javascript
// ✅ Non-SEO critical data
- User comments
- Like counts
- Real-time updates

'use client';
import { useEffect, useState } from 'react';

export default function Page() {
  const [data, setData] = useState(null);
  
  useEffect(() => {
    fetch('/api/data')
      .then(res => res.json())
      .then(setData);
  }, []);
}
```

---

#### Migration to App Router

**Pages Router:**
```javascript
// pages/products/[id].js
export async function getServerSideProps(context) {
  const product = await fetchProduct(context.params.id);
  return { props: { product } };
}

export default function Product({ product }) {
  return <div>{product.name}</div>;
}
```

**App Router (Server Component):**
```javascript
// app/products/[id]/page.js
export default async function Product({ params }) {
  const product = await fetchProduct(params.id);
  return <div>{product.name}</div>;
}
```

**Benefits of App Router:**
- No need for getServerSideProps
- Simpler syntax
- Better streaming support
- Automatic request deduplication
- Improved caching control

---

#### Troubleshooting

**1. Serialization errors:**
```javascript
// ❌ Error: can't serialize Date objects
return { props: { date: new Date() } };

// ✅ Fix: convert to string
return { props: { date: new Date().toISOString() } };
```

**2. Undefined params:**
```javascript
// ❌ Error: params is undefined
export async function getServerSideProps({ params }) {
  console.log(params.id); // undefined on non-dynamic routes
}

// ✅ Fix: check if params exist
export async function getServerSideProps({ params }) {
  const id = params?.id || 'default';
}
```

**3. CORS issues:**
```javascript
// ❌ CORS error with external API
const data = await fetch('https://api.example.com/data');

// ✅ Fix: API requests are server-side, no CORS
const data = await fetch('https://api.example.com/data', {
  headers: {
    'User-Agent': 'Next.js',
  },
});
```

**4. Slow performance:**
```javascript
// ❌ Slow - sequential fetching
const user = await fetchUser();
const posts = await fetchPosts();

// ✅ Fast - parallel fetching
const [user, posts] = await Promise.all([
  fetchUser(),
  fetchPosts(),
]);
```

---

---

### Q1.6: What is getStaticPaths() and How Does It Work?

**Answer:**

`getStaticPaths()` is a Next.js function (Pages Router) that works with `getStaticProps()` to pre-generate dynamic routes at build time. It tells Next.js which dynamic route paths to render as static HTML pages.

---

#### What is getStaticPaths()?

`getStaticPaths()` is used to:
- Define which dynamic routes should be pre-rendered at build time
- Generate static pages for routes with dynamic segments (like `[id].js` or `[slug].js`)
- Enable Static Site Generation (SSG) for dynamic routes
- Control fallback behavior for routes not generated at build time

**Key Characteristics:**
- ✅ Only works with `getStaticProps()` (not with `getServerSideProps()`)
- ✅ Required for dynamic routes using SSG
- ✅ Runs only at build time (not on every request)
- ✅ Only runs on the server (never in the browser)
- ✅ Can be used with catch-all routes

---

#### Basic Syntax

```javascript
// pages/posts/[id].js
export async function getStaticPaths() {
  // Fetch list of all post IDs
  const posts = await fetch('https://api.example.com/posts')
    .then(res => res.json());
  
  // Generate paths for all posts
  const paths = posts.map(post => ({
    params: { id: post.id.toString() }
  }));
  
  return {
    paths,
    fallback: false
  };
}

export async function getStaticProps({ params }) {
  // Fetch data for specific post
  const post = await fetch(`https://api.example.com/posts/${params.id}`)
    .then(res => res.json());
  
  return {
    props: { post }
  };
}

export default function Post({ post }) {
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

---

#### Return Object Structure

`getStaticPaths()` must return an object with two properties:

**1. paths:**
Array of objects, each with a `params` key matching the dynamic route parameters.

```javascript
return {
  paths: [
    { params: { id: '1' } },    // Generates /posts/1
    { params: { id: '2' } },    // Generates /posts/2
    { params: { id: '3' } },    // Generates /posts/3
  ],
  fallback: false
};
```

**2. fallback:**
Controls behavior for paths not returned by `getStaticPaths()`.

---

#### Fallback Options

**`fallback: false`** (Default, safest option)

```javascript
export async function getStaticPaths() {
  const paths = [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ];
  
  return {
    paths,
    fallback: false // Only these paths exist, 404 for others
  };
}
```

**Behavior:**
- Only paths returned are pre-rendered
- Any other path returns 404
- ✅ Best for small number of pages
- ✅ All pages ready at build time

**Use when:**
- Limited number of pages (dozens, not thousands)
- All paths known at build time
- Want to ensure all pages are pre-generated

---

**`fallback: true`** (On-demand generation)

```javascript
export async function getStaticPaths() {
  // Only pre-render most popular posts
  const popularPosts = await fetchPopularPosts();
  
  const paths = popularPosts.map(post => ({
    params: { id: post.id.toString() }
  }));
  
  return {
    paths,
    fallback: true // Generate others on first request
  };
}

export default function Post({ post }) {
  const router = useRouter();
  
  // Show loading state while page generates
  if (router.isFallback) {
    return <div>Loading...</div>;
  }
  
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

**Behavior:**
- Paths not in `paths` array generate on first request
- Shows loading state (check `router.isFallback`)
- Generated page cached for subsequent requests
- Returns 404 if data doesn't exist

**Use when:**
- Large number of pages (thousands)
- Most pages rarely accessed
- Want fast builds
- Can show loading state to users

---

**`fallback: 'blocking'`** (SSR-like behavior)

```javascript
export async function getStaticPaths() {
  // Pre-render only critical pages
  const criticalPaths = await fetchCriticalPages();
  
  return {
    paths: criticalPaths.map(path => ({
      params: { id: path.id.toString() }
    })),
    fallback: 'blocking' // Wait for generation, no loading state needed
  };
}

export default function Post({ post }) {
  // No need to check router.isFallback
  // Page always has data when rendered
  
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

**Behavior:**
- Paths not in `paths` array generate on first request
- User waits (like SSR) - no loading state shown
- Generated page cached for subsequent requests
- No hydration mismatch issues

**Use when:**
- Large number of pages
- Can't show loading state (SEO concerns)
- Want simpler code (no `isFallback` checks)
- Acceptable to make users wait briefly

---

#### Comparison of Fallback Options

| Feature | `false` | `true` | `'blocking'` |
|---------|---------|--------|--------------|
| **Pre-render all?** | Yes | No | No |
| **Build time** | Slower (all pages) | Faster (some pages) | Faster (some pages) |
| **First request** | Instant (cached) | Shows loading | Waits for generation |
| **Subsequent requests** | Instant (cached) | Instant (cached) | Instant (cached) |
| **404 for missing** | ✅ Yes | ✅ Yes | ✅ Yes |
| **Need isFallback check** | ❌ No | ✅ Yes | ❌ No |
| **SEO** | ✅ Perfect | ⚠️ Loading state | ✅ Perfect |
| **Best for** | Small sites | Large sites, loading OK | Large sites, no loading |

---

#### Dynamic Routes Examples

**Single Dynamic Parameter:**

```javascript
// pages/posts/[id].js
export async function getStaticPaths() {
  const posts = await fetch('https://api.example.com/posts')
    .then(res => res.json());
  
  return {
    paths: posts.map(post => ({
      params: { id: post.id.toString() }
    })),
    fallback: false
  };
}

export async function getStaticProps({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.id}`)
    .then(res => res.json());
  
  return { props: { post } };
}
```

---

**Multiple Dynamic Parameters:**

```javascript
// pages/blog/[category]/[slug].js
export async function getStaticPaths() {
  const posts = await fetch('https://api.example.com/posts')
    .then(res => res.json());
  
  return {
    paths: posts.map(post => ({
      params: {
        category: post.category,
        slug: post.slug
      }
    })),
    fallback: false
  };
}

export async function getStaticProps({ params }) {
  const { category, slug } = params;
  
  const post = await fetch(
    `https://api.example.com/posts?category=${category}&slug=${slug}`
  ).then(res => res.json());
  
  return { props: { post } };
}

export default function BlogPost({ post }) {
  return (
    <article>
      <span className="category">{post.category}</span>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

---

**Catch-All Routes:**

```javascript
// pages/docs/[...slug].js
export async function getStaticPaths() {
  const docs = [
    { slug: ['getting-started'] },
    { slug: ['api', 'reference'] },
    { slug: ['guides', 'deployment', 'vercel'] }
  ];
  
  return {
    paths: docs.map(doc => ({
      params: { slug: doc.slug }
    })),
    fallback: false
  };
}

export async function getStaticProps({ params }) {
  const { slug } = params; // Array: ['api', 'reference']
  const path = slug.join('/');
  
  const doc = await fetch(`https://api.example.com/docs/${path}`)
    .then(res => res.json());
  
  return { props: { doc } };
}

export default function Doc({ doc }) {
  return (
    <article>
      <h1>{doc.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: doc.content }} />
    </article>
  );
}
```

---

**Optional Catch-All Routes:**

```javascript
// pages/shop/[[...slug]].js
export async function getStaticPaths() {
  return {
    paths: [
      { params: { slug: [] } },              // /shop
      { params: { slug: ['electronics'] } }, // /shop/electronics
      { params: { slug: ['electronics', 'phones'] } } // /shop/electronics/phones
    ],
    fallback: 'blocking'
  };
}

export async function getStaticProps({ params }) {
  const slug = params.slug || [];
  const category = slug.join('/');
  
  const products = await fetch(
    `https://api.example.com/products?category=${category}`
  ).then(res => res.json());
  
  return {
    props: { products, category: category || 'all' }
  };
}

export default function Shop({ products, category }) {
  return (
    <div>
      <h1>Category: {category}</h1>
      <div className="products">
        {products.map(product => (
          <ProductCard key={product.id} product={product} />
        ))}
      </div>
    </div>
  );
}
```

---

#### Real-World Examples

**1. Blog with ISR (Incremental Static Regeneration):**

```javascript
// pages/blog/[slug].js
export async function getStaticPaths() {
  // Only pre-render most recent 50 posts
  const recentPosts = await fetch('https://api.example.com/posts?limit=50')
    .then(res => res.json());
  
  return {
    paths: recentPosts.map(post => ({
      params: { slug: post.slug }
    })),
    fallback: 'blocking' // Generate older posts on-demand
  };
}

export async function getStaticProps({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(res => res.json())
    .catch(() => null);
  
  if (!post) {
    return { notFound: true };
  }
  
  return {
    props: { post },
    revalidate: 60 // Regenerate every 60 seconds
  };
}

export default function BlogPost({ post }) {
  return (
    <article>
      <h1>{post.title}</h1>
      <time>{new Date(post.publishedAt).toLocaleDateString()}</time>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}
```

---

**2. E-commerce Product Pages:**

```javascript
// pages/products/[id].js
export async function getStaticPaths() {
  // Pre-render only featured/popular products
  const featuredProducts = await fetch(
    'https://api.example.com/products?featured=true'
  ).then(res => res.json());
  
  return {
    paths: featuredProducts.map(product => ({
      params: { id: product.id.toString() }
    })),
    fallback: 'blocking' // Generate other products on-demand
  };
}

export async function getStaticProps({ params }) {
  const [product, reviews, relatedProducts] = await Promise.all([
    fetch(`https://api.example.com/products/${params.id}`).then(r => r.json()),
    fetch(`https://api.example.com/reviews?productId=${params.id}`).then(r => r.json()),
    fetch(`https://api.example.com/products/${params.id}/related`).then(r => r.json())
  ]);
  
  if (!product) {
    return { notFound: true };
  }
  
  return {
    props: {
      product,
      reviews,
      relatedProducts
    },
    revalidate: 300 // Revalidate every 5 minutes
  };
}

export default function Product({ product, reviews, relatedProducts }) {
  return (
    <div className="product-page">
      <div className="product-info">
        <h1>{product.name}</h1>
        <p className="price">${product.price}</p>
        <p>{product.description}</p>
        <button className="add-to-cart">Add to Cart</button>
      </div>
      
      <div className="reviews">
        <h2>Customer Reviews ({reviews.length})</h2>
        {reviews.map(review => (
          <div key={review.id} className="review">
            <p>⭐ {review.rating}/5</p>
            <p>{review.comment}</p>
          </div>
        ))}
      </div>
      
      <div className="related-products">
        <h2>You might also like</h2>
        <div className="products-grid">
          {relatedProducts.map(product => (
            <ProductCard key={product.id} product={product} />
          ))}
        </div>
      </div>
    </div>
  );
}
```

---

**3. Documentation Site with Nested Routes:**

```javascript
// pages/docs/[...slug].js
export async function getStaticPaths() {
  // Fetch all documentation pages
  const allDocs = await fetch('https://api.example.com/docs/sitemap')
    .then(res => res.json());
  
  const paths = allDocs.map(doc => ({
    params: {
      slug: doc.path.split('/') // 'api/auth/login' → ['api', 'auth', 'login']
    }
  }));
  
  return {
    paths,
    fallback: false // 404 for non-existent docs
  };
}

export async function getStaticProps({ params }) {
  const { slug } = params;
  const path = slug.join('/');
  
  const [doc, navigation] = await Promise.all([
    fetch(`https://api.example.com/docs/${path}`).then(r => r.json()),
    fetch('https://api.example.com/docs/navigation').then(r => r.json())
  ]);
  
  if (!doc) {
    return { notFound: true };
  }
  
  return {
    props: {
      doc,
      navigation
    }
  };
}

export default function DocPage({ doc, navigation }) {
  return (
    <div className="docs-layout">
      <aside className="sidebar">
        <Navigation items={navigation} />
      </aside>
      
      <main className="content">
        <h1>{doc.title}</h1>
        <div dangerouslySetInnerHTML={{ __html: doc.content }} />
        
        <div className="pagination">
          {doc.prev && <Link href={doc.prev.path}>← {doc.prev.title}</Link>}
          {doc.next && <Link href={doc.next.path}>{doc.next.title} →</Link>}
        </div>
      </main>
    </div>
  );
}
```

---

**4. User Profiles with Fallback:**

```javascript
// pages/users/[username].js
export async function getStaticPaths() {
  // Pre-render profiles of top 100 users
  const topUsers = await fetch('https://api.example.com/users/top?limit=100')
    .then(res => res.json());
  
  return {
    paths: topUsers.map(user => ({
      params: { username: user.username }
    })),
    fallback: true // Generate other profiles on-demand
  };
}

export async function getStaticProps({ params }) {
  const user = await fetch(`https://api.example.com/users/${params.username}`)
    .then(res => {
      if (!res.ok) return null;
      return res.json();
    })
    .catch(() => null);
  
  if (!user) {
    return { notFound: true };
  }
  
  return {
    props: { user },
    revalidate: 60 // Update profile every minute
  };
}

export default function UserProfile({ user }) {
  const router = useRouter();
  
  // Show loading state while page generates
  if (router.isFallback) {
    return (
      <div className="loading">
        <h1>Loading profile...</h1>
        <div className="spinner"></div>
      </div>
    );
  }
  
  return (
    <div className="profile">
      <img src={user.avatar} alt={user.name} />
      <h1>{user.name}</h1>
      <p>@{user.username}</p>
      <p>{user.bio}</p>
      
      <div className="stats">
        <div>
          <strong>{user.followers}</strong>
          <span>Followers</span>
        </div>
        <div>
          <strong>{user.following}</strong>
          <span>Following</span>
        </div>
        <div>
          <strong>{user.posts}</strong>
          <span>Posts</span>
        </div>
      </div>
    </div>
  );
}
```

---

#### Working with External APIs

**Fetching from REST API:**

```javascript
export async function getStaticPaths() {
  const response = await fetch('https://jsonplaceholder.typicode.com/posts');
  const posts = await response.json();
  
  return {
    paths: posts.map(post => ({
      params: { id: post.id.toString() }
    })),
    fallback: 'blocking'
  };
}
```

**Fetching from Database:**

```javascript
import { db } from '@/lib/database';

export async function getStaticPaths() {
  const products = await db.product.findMany({
    select: { id: true }
  });
  
  return {
    paths: products.map(product => ({
      params: { id: product.id.toString() }
    })),
    fallback: 'blocking'
  };
}
```

**Fetching from CMS:**

```javascript
import { client } from '@/lib/contentful';

export async function getStaticPaths() {
  const response = await client.getEntries({
    content_type: 'blogPost'
  });
  
  return {
    paths: response.items.map(item => ({
      params: { slug: item.fields.slug }
    })),
    fallback: 'blocking'
  };
}
```

---

#### Best Practices

**1. Limit pre-rendered pages for large datasets:**

```javascript
// ✅ Good - Only pre-render popular pages
export async function getStaticPaths() {
  const popularPosts = await fetch('https://api.example.com/posts?popular=true')
    .then(res => res.json());
  
  return {
    paths: popularPosts.map(post => ({
      params: { id: post.id.toString() }
    })),
    fallback: 'blocking' // Others generated on-demand
  };
}

// ❌ Bad - Pre-render all 100,000 posts (slow build)
export async function getStaticPaths() {
  const allPosts = await fetch('https://api.example.com/posts')
    .then(res => res.json());
  
  return {
    paths: allPosts.map(post => ({
      params: { id: post.id.toString() }
    })),
    fallback: false
  };
}
```

---

**2. Always convert params to strings:**

```javascript
// ✅ Good - Params are strings
return {
  paths: posts.map(post => ({
    params: { id: post.id.toString() } // Convert number to string
  })),
  fallback: false
};

// ❌ Bad - Numbers cause issues
return {
  paths: posts.map(post => ({
    params: { id: post.id } // Don't pass numbers
  })),
  fallback: false
};
```

---

**3. Handle errors gracefully:**

```javascript
export async function getStaticPaths() {
  try {
    const posts = await fetch('https://api.example.com/posts')
      .then(res => {
        if (!res.ok) throw new Error('Failed to fetch');
        return res.json();
      });
    
    return {
      paths: posts.map(post => ({
        params: { id: post.id.toString() }
      })),
      fallback: 'blocking'
    };
  } catch (error) {
    console.error('Error in getStaticPaths:', error);
    
    // Return empty paths, use fallback to generate on-demand
    return {
      paths: [],
      fallback: 'blocking'
    };
  }
}
```

---

**4. Use ISR for frequently changing data:**

```javascript
export async function getStaticPaths() {
  const paths = await generatePaths();
  
  return {
    paths,
    fallback: 'blocking'
  };
}

export async function getStaticProps({ params }) {
  const data = await fetchData(params.id);
  
  return {
    props: { data },
    revalidate: 60 // Regenerate every minute
  };
}
```

---

**5. Use TypeScript for type safety:**

```typescript
import { GetStaticPaths, GetStaticProps } from 'next';

interface Post {
  id: string;
  title: string;
  content: string;
}

interface PathParams {
  id: string;
}

export const getStaticPaths: GetStaticPaths<PathParams> = async () => {
  const posts = await fetch('https://api.example.com/posts')
    .then(res => res.json()) as Post[];
  
  return {
    paths: posts.map(post => ({
      params: { id: post.id }
    })),
    fallback: 'blocking'
  };
};

export const getStaticProps: GetStaticProps<{ post: Post }, PathParams> = async ({ params }) => {
  const post = await fetch(`https://api.example.com/posts/${params!.id}`)
    .then(res => res.json()) as Post;
  
  return {
    props: { post }
  };
};
```

---

#### Common Patterns

**1. Pagination:**

```javascript
// pages/posts/page/[page].js
export async function getStaticPaths() {
  const totalPosts = await fetch('https://api.example.com/posts/count')
    .then(res => res.json());
  
  const postsPerPage = 10;
  const totalPages = Math.ceil(totalPosts / postsPerPage);
  
  return {
    paths: Array.from({ length: totalPages }, (_, i) => ({
      params: { page: (i + 1).toString() }
    })),
    fallback: false
  };
}

export async function getStaticProps({ params }) {
  const page = parseInt(params.page);
  const postsPerPage = 10;
  const offset = (page - 1) * postsPerPage;
  
  const posts = await fetch(
    `https://api.example.com/posts?limit=${postsPerPage}&offset=${offset}`
  ).then(res => res.json());
  
  return {
    props: {
      posts,
      currentPage: page,
      totalPages: Math.ceil(totalPosts / postsPerPage)
    }
  };
}
```

---

**2. Localization (i18n):**

```javascript
// pages/[locale]/blog/[slug].js
export async function getStaticPaths() {
  const locales = ['en', 'es', 'fr', 'de'];
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  const paths = [];
  
  // Generate paths for each locale
  for (const locale of locales) {
    for (const post of posts) {
      paths.push({
        params: {
          locale,
          slug: post.slug
        }
      });
    }
  }
  
  return {
    paths,
    fallback: 'blocking'
  };
}

export async function getStaticProps({ params }) {
  const { locale, slug } = params;
  
  const post = await fetch(
    `https://api.example.com/posts/${slug}?locale=${locale}`
  ).then(res => res.json());
  
  return {
    props: { post, locale }
  };
}
```

---

**3. Category + Slug:**

```javascript
// pages/[category]/[slug].js
export async function getStaticPaths() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  return {
    paths: posts.map(post => ({
      params: {
        category: post.category,
        slug: post.slug
      }
    })),
    fallback: 'blocking'
  };
}

export async function getStaticProps({ params }) {
  const { category, slug } = params;
  
  const post = await fetch(
    `https://api.example.com/posts?category=${category}&slug=${slug}`
  ).then(res => res.json());
  
  return { props: { post } };
}
```

---

#### Performance Optimization

**1. Parallel fetching in getStaticPaths:**

```javascript
// ❌ Bad - Sequential (slow)
export async function getStaticPaths() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  const products = await fetch('https://api.example.com/products').then(r => r.json());
  
  return { paths: [...postPaths, ...productPaths], fallback: false };
}

// ✅ Good - Parallel (fast)
export async function getStaticPaths() {
  const [posts, products] = await Promise.all([
    fetch('https://api.example.com/posts').then(r => r.json()),
    fetch('https://api.example.com/products').then(r => r.json())
  ]);
  
  return { paths: [...postPaths, ...productPaths], fallback: false };
}
```

---

**2. Cache API responses:**

```javascript
// lib/cache.js
const cache = new Map();

export async function fetchWithCache(url, ttl = 60000) {
  const cached = cache.get(url);
  
  if (cached && Date.now() - cached.timestamp < ttl) {
    return cached.data;
  }
  
  const data = await fetch(url).then(r => r.json());
  cache.set(url, { data, timestamp: Date.now() });
  
  return data;
}

// pages/posts/[id].js
import { fetchWithCache } from '@/lib/cache';

export async function getStaticPaths() {
  const posts = await fetchWithCache('https://api.example.com/posts');
  
  return {
    paths: posts.map(post => ({
      params: { id: post.id.toString() }
    })),
    fallback: 'blocking'
  };
}
```

---

#### Troubleshooting

**1. Build fails with too many paths:**

```javascript
// Problem: Trying to generate 100,000 pages
export async function getStaticPaths() {
  const allPosts = await fetchAllPosts(); // 100,000 posts
  
  return {
    paths: allPosts.map(post => ({ params: { id: post.id } })),
    fallback: false // Build takes hours!
  };
}

// Solution: Use fallback
export async function getStaticPaths() {
  const popularPosts = await fetchPopularPosts(); // Top 100
  
  return {
    paths: popularPosts.map(post => ({ params: { id: post.id } })),
    fallback: 'blocking' // Generate others on-demand
  };
}
```

---

**2. Params type mismatch:**

```javascript
// ❌ Error: Expected string, got number
return {
  paths: [
    { params: { id: 1 } },
    { params: { id: 2 } }
  ],
  fallback: false
};

// ✅ Fix: Convert to strings
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: false
};
```

---

**3. Missing params key:**

```javascript
// ❌ Error: Missing params
return {
  paths: [
    { id: '1' },
    { id: '2' }
  ],
  fallback: false
};

// ✅ Fix: Wrap in params
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: false
};
```

---

**4. Fallback behavior not working:**

```javascript
// Problem: 404 even with fallback: true
export async function getStaticProps({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.id}`)
    .then(res => res.json());
  
  return { props: { post } }; // Missing notFound check!
}

// Solution: Handle missing data
export async function getStaticProps({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.id}`)
    .then(res => {
      if (!res.ok) return null;
      return res.json();
    });
  
  if (!post) {
    return { notFound: true }; // Properly return 404
  }
  
  return { props: { post } };
}
```

---

#### Migration to App Router

**Pages Router:**
```javascript
// pages/posts/[id].js
export async function getStaticPaths() {
  const posts = await fetchPosts();
  
  return {
    paths: posts.map(post => ({
      params: { id: post.id }
    })),
    fallback: 'blocking'
  };
}

export async function getStaticProps({ params }) {
  const post = await fetchPost(params.id);
  return { props: { post } };
}

export default function Post({ post }) {
  return <article>{post.title}</article>;
}
```

**App Router:**
```javascript
// app/posts/[id]/page.js
export async function generateStaticParams() {
  const posts = await fetchPosts();
  
  return posts.map(post => ({
    id: post.id
  }));
}

export default async function Post({ params }) {
  const post = await fetchPost(params.id);
  return <article>{post.title}</article>;
}
```

**Key differences:**
- `getStaticPaths()` → `generateStaticParams()`
- Return params directly (no `paths` wrapper)
- No `fallback` option (controlled via `dynamicParams`)
- Component can be async (no need for `getStaticProps`)

---

### Q1.7: What is generateMetadata() and How Does It Work?

**Answer:**

`generateMetadata()` is a Next.js App Router function that allows you to generate dynamic metadata (like page titles, descriptions, Open Graph tags, Twitter cards, etc.) for your pages. It enables SEO-friendly, customizable meta tags based on route data.

---

#### What is generateMetadata()?

`generateMetadata()` is used to:
- Generate dynamic page titles and descriptions
- Create Open Graph and Twitter card metadata
- Set favicon and theme colors
- Define canonical URLs and alternate language versions
- Control search engine indexing and crawling
- Provide structured data for social media previews

**Key Characteristics:**
- ✅ App Router only (not available in Pages Router)
- ✅ Runs on the server (never in the browser)
- ✅ Can be async (fetch data for metadata)
- ✅ Type-safe with TypeScript
- ✅ Automatically merges with parent metadata
- ✅ Supports both static and dynamic values

---

#### Basic Syntax

**Static Metadata (Simple):**

```javascript
// app/page.js
export const metadata = {
  title: 'Home Page',
  description: 'Welcome to our website',
};

export default function Home() {
  return <h1>Home</h1>;
}
```

**Dynamic Metadata (Function):**

```javascript
// app/blog/[slug]/page.js
export async function generateMetadata({ params }) {
  // Fetch post data
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(res => res.json());
  
  return {
    title: post.title,
    description: post.excerpt,
  };
}

export default async function BlogPost({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(res => res.json());
  
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

---

#### Metadata Object Properties

**Complete list of available properties:**

```typescript
export const metadata = {
  // Basic Metadata
  title: 'Page Title',
  description: 'Page description',
  keywords: ['next.js', 'react', 'javascript'],
  
  // Authors
  authors: [
    { name: 'John Doe', url: 'https://johndoe.com' }
  ],
  
  // Creator/Publisher
  creator: 'John Doe',
  publisher: 'Acme Corp',
  
  // Application Name
  applicationName: 'My App',
  
  // Referrer
  referrer: 'origin-when-cross-origin',
  
  // Robots (SEO)
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      'max-video-preview': -1,
      'max-image-preview': 'large',
      'max-snippet': -1,
    },
  },
  
  // Alternate URLs
  alternates: {
    canonical: 'https://example.com/page',
    languages: {
      'en-US': 'https://example.com/en-US',
      'es-ES': 'https://example.com/es-ES',
    },
  },
  
  // Icons
  icons: {
    icon: '/favicon.ico',
    shortcut: '/shortcut-icon.png',
    apple: '/apple-icon.png',
  },
  
  // Open Graph
  openGraph: {
    title: 'Open Graph Title',
    description: 'Open Graph Description',
    url: 'https://example.com',
    siteName: 'Site Name',
    images: [
      {
        url: 'https://example.com/og-image.jpg',
        width: 1200,
        height: 630,
        alt: 'Og Image Alt',
      }
    ],
    locale: 'en_US',
    type: 'website',
  },
  
  // Twitter
  twitter: {
    card: 'summary_large_image',
    title: 'Twitter Title',
    description: 'Twitter Description',
    creator: '@username',
    images: ['https://example.com/twitter-image.jpg'],
  },
  
  // Verification
  verification: {
    google: 'google-site-verification-code',
    yandex: 'yandex-verification-code',
    yahoo: 'yahoo-verification-code',
  },
  
  // Category
  category: 'technology',
};
```

---

#### Title Configuration

**Simple String:**

```javascript
export const metadata = {
  title: 'My Page Title',
};
```

**Title Template (for child pages):**

```javascript
// app/layout.js (root)
export const metadata = {
  title: {
    default: 'My Website',
    template: '%s | My Website', // %s is replaced with child title
  },
};

// app/blog/page.js
export const metadata = {
  title: 'Blog', // Becomes "Blog | My Website"
};

// app/blog/[slug]/page.js
export const metadata = {
  title: 'Post Title', // Becomes "Post Title | My Website"
};
```

**Absolute Title (ignore template):**

```javascript
export const metadata = {
  title: {
    absolute: 'This exact title', // Won't use parent template
  },
};
```

---

#### Dynamic Metadata with generateMetadata()

**Basic Example:**

```javascript
// app/products/[id]/page.js
export async function generateMetadata({ params, searchParams }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(res => res.json());
  
  return {
    title: product.name,
    description: product.description,
    openGraph: {
      images: [product.image],
    },
  };
}

export default async function Product({ params }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(res => res.json());
  
  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
    </div>
  );
}
```

---

#### Function Parameters

`generateMetadata()` receives two parameters:

```typescript
export async function generateMetadata(
  { params, searchParams }: {
    params: { [key: string]: string }
    searchParams: { [key: string]: string | string[] | undefined }
  },
  parent: ResolvingMetadata
) {
  // params: Dynamic route parameters
  // searchParams: URL query parameters
  // parent: Metadata from parent segments
  
  return {
    title: 'Page Title',
  };
}
```

**Using params:**

```javascript
// app/blog/[category]/[slug]/page.js
export async function generateMetadata({ params }) {
  const { category, slug } = params;
  
  const post = await fetch(
    `https://api.example.com/posts?category=${category}&slug=${slug}`
  ).then(res => res.json());
  
  return {
    title: post.title,
    description: post.excerpt,
  };
}
```

**Using searchParams:**

```javascript
// app/search/page.js
export async function generateMetadata({ searchParams }) {
  const query = searchParams.q || 'all';
  const category = searchParams.category || 'general';
  
  return {
    title: `Search results for "${query}" in ${category}`,
    description: `Find ${query} in our ${category} section`,
  };
}
```

**Using parent metadata:**

```javascript
export async function generateMetadata({ params }, parent) {
  // Read parent metadata
  const parentTitle = (await parent).title?.absolute || 'Default';
  const parentImages = (await parent).openGraph?.images || [];
  
  const post = await fetch(`https://api.example.com/posts/${params.id}`)
    .then(res => res.json());
  
  return {
    title: post.title,
    openGraph: {
      images: [post.image, ...parentImages], // Merge with parent images
    },
  };
}
```

---

#### Real-World Examples

**1. Blog Post with Complete SEO:**

```javascript
// app/blog/[slug]/page.js
export async function generateMetadata({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(res => res.json());
  
  if (!post) {
    return {
      title: 'Post Not Found',
    };
  }
  
  return {
    title: post.title,
    description: post.excerpt,
    keywords: post.tags,
    authors: [{ name: post.author.name, url: post.author.url }],
    
    openGraph: {
      title: post.title,
      description: post.excerpt,
      type: 'article',
      publishedTime: post.publishedAt,
      authors: [post.author.name],
      images: [
        {
          url: post.coverImage,
          width: 1200,
          height: 630,
          alt: post.title,
        }
      ],
    },
    
    twitter: {
      card: 'summary_large_image',
      title: post.title,
      description: post.excerpt,
      creator: `@${post.author.twitter}`,
      images: [post.coverImage],
    },
    
    alternates: {
      canonical: `https://example.com/blog/${params.slug}`,
    },
  };
}

export default async function BlogPost({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(res => res.json());
  
  return (
    <article>
      <h1>{post.title}</h1>
      <time>{new Date(post.publishedAt).toLocaleDateString()}</time>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}
```

---

**2. E-commerce Product Page:**

```javascript
// app/products/[id]/page.js
export async function generateMetadata({ params }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(res => res.json());
  
  const inStock = product.inventory > 0;
  const priceRange = product.variants
    ? `$${Math.min(...product.variants.map(v => v.price))} - $${Math.max(...product.variants.map(v => v.price))}`
    : `$${product.price}`;
  
  return {
    title: `${product.name} - Buy Online`,
    description: `${product.description.substring(0, 160)}... ${inStock ? 'In Stock' : 'Out of Stock'}. ${priceRange}`,
    keywords: [product.category, product.brand, ...product.tags],
    
    openGraph: {
      title: product.name,
      description: product.description,
      type: 'product',
      images: product.images.map(img => ({
        url: img.url,
        width: 800,
        height: 600,
        alt: img.alt || product.name,
      })),
      siteName: 'Your Store',
    },
    
    twitter: {
      card: 'summary_large_image',
      title: product.name,
      description: product.description,
      images: [product.images[0]?.url],
    },
    
    robots: {
      index: inStock, // Don't index out-of-stock products
      follow: true,
    },
    
    alternates: {
      canonical: `https://yourstore.com/products/${params.id}`,
    },
  };
}

export default async function Product({ params }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(res => res.json());
  
  return (
    <div className="product-page">
      <h1>{product.name}</h1>
      <p className="price">${product.price}</p>
      <p>{product.description}</p>
      <button>Add to Cart</button>
    </div>
  );
}
```

---

**3. User Profile with Dynamic Images:**

```javascript
// app/users/[username]/page.js
export async function generateMetadata({ params }) {
  const user = await fetch(`https://api.example.com/users/${params.username}`)
    .then(res => res.json());
  
  if (!user) {
    return {
      title: 'User Not Found',
      robots: {
        index: false,
        follow: false,
      },
    };
  }
  
  return {
    title: `${user.name} (@${user.username})`,
    description: user.bio || `Check out ${user.name}'s profile`,
    
    openGraph: {
      title: user.name,
      description: user.bio,
      type: 'profile',
      username: user.username,
      images: [
        {
          url: user.avatar,
          width: 400,
          height: 400,
          alt: `${user.name}'s avatar`,
        }
      ],
    },
    
    twitter: {
      card: 'summary',
      title: `${user.name} (@${user.username})`,
      description: user.bio,
      creator: `@${user.username}`,
      images: [user.avatar],
    },
    
    alternates: {
      canonical: `https://example.com/users/${params.username}`,
    },
  };
}

export default async function UserProfile({ params }) {
  const user = await fetch(`https://api.example.com/users/${params.username}`)
    .then(res => res.json());
  
  return (
    <div className="profile">
      <img src={user.avatar} alt={user.name} />
      <h1>{user.name}</h1>
      <p>@{user.username}</p>
      <p>{user.bio}</p>
    </div>
  );
}
```

---

**4. Documentation Page with Localization:**

```javascript
// app/[locale]/docs/[...slug]/page.js
export async function generateMetadata({ params }) {
  const locale = params.locale;
  const path = params.slug.join('/');
  
  const doc = await fetch(
    `https://api.example.com/docs/${path}?locale=${locale}`
  ).then(res => res.json());
  
  const locales = {
    en: 'en_US',
    es: 'es_ES',
    fr: 'fr_FR',
    de: 'de_DE',
  };
  
  return {
    title: doc.title,
    description: doc.description,
    
    alternates: {
      canonical: `https://example.com/${locale}/docs/${path}`,
      languages: {
        'en': `https://example.com/en/docs/${path}`,
        'es': `https://example.com/es/docs/${path}`,
        'fr': `https://example.com/fr/docs/${path}`,
        'de': `https://example.com/de/docs/${path}`,
      },
    },
    
    openGraph: {
      title: doc.title,
      description: doc.description,
      type: 'article',
      locale: locales[locale],
      alternateLocale: Object.values(locales).filter(l => l !== locales[locale]),
    },
  };
}

export default async function DocPage({ params }) {
  const locale = params.locale;
  const path = params.slug.join('/');
  
  const doc = await fetch(
    `https://api.example.com/docs/${path}?locale=${locale}`
  ).then(res => res.json());
  
  return (
    <article>
      <h1>{doc.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: doc.content }} />
    </article>
  );
}
```

---

#### Metadata Inheritance and Merging

Metadata automatically merges with parent layouts:

```javascript
// app/layout.js (root)
export const metadata = {
  title: {
    template: '%s | My Site',
    default: 'My Site',
  },
  openGraph: {
    siteName: 'My Site',
    type: 'website',
  },
};

// app/blog/layout.js
export const metadata = {
  openGraph: {
    type: 'article', // Overrides parent type
  },
};

// app/blog/[slug]/page.js
export async function generateMetadata({ params }) {
  const post = await fetchPost(params.slug);
  
  return {
    title: post.title, // Uses template from root: "Post Title | My Site"
    openGraph: {
      title: post.title,
      // Inherits siteName from root and type from blog layout
      images: [post.image],
    },
  };
}
```

---

#### File-Based Metadata

**Special files for metadata:**

```
app/
├── favicon.ico          # Favicon
├── icon.png             # App icon
├── apple-icon.png       # Apple touch icon
├── opengraph-image.jpg  # OG image
├── twitter-image.jpg    # Twitter image
├── robots.txt           # Robots file
├── sitemap.xml          # Sitemap
└── manifest.json        # Web app manifest
```

**Dynamic icons:**

```javascript
// app/icon.tsx
import { ImageResponse } from 'next/og';

export const size = { width: 32, height: 32 };
export const contentType = 'image/png';

export default function Icon() {
  return new ImageResponse(
    (
      <div
        style={{
          fontSize: 24,
          background: 'black',
          width: '100%',
          height: '100%',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          color: 'white',
        }}
      >
        A
      </div>
    ),
    {
      ...size,
    }
  );
}
```

**Dynamic Open Graph images:**

```javascript
// app/blog/[slug]/opengraph-image.tsx
import { ImageResponse } from 'next/og';

export const size = { width: 1200, height: 630 };
export const contentType = 'image/png';

export default async function Image({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(res => res.json());
  
  return new ImageResponse(
    (
      <div
        style={{
          fontSize: 60,
          background: 'linear-gradient(to bottom, #667eea 0%, #764ba2 100%)',
          width: '100%',
          height: '100%',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          color: 'white',
          padding: '40px',
        }}
      >
        {post.title}
      </div>
    ),
    {
      ...size,
    }
  );
}
```

---

#### JSON-LD Structured Data

**Add structured data for rich results:**

```javascript
export async function generateMetadata({ params }) {
  const product = await fetchProduct(params.id);
  
  const jsonLd = {
    '@context': 'https://schema.org',
    '@type': 'Product',
    name: product.name,
    image: product.images,
    description: product.description,
    brand: {
      '@type': 'Brand',
      name: product.brand,
    },
    offers: {
      '@type': 'Offer',
      url: `https://example.com/products/${params.id}`,
      priceCurrency: 'USD',
      price: product.price,
      availability: product.inStock ? 'https://schema.org/InStock' : 'https://schema.org/OutOfStock',
    },
  };
  
  return {
    title: product.name,
    description: product.description,
    other: {
      'application/ld+json': JSON.stringify(jsonLd),
    },
  };
}

// Or add directly in component
export default async function Product({ params }) {
  const product = await fetchProduct(params.id);
  
  const jsonLd = {
    '@context': 'https://schema.org',
    '@type': 'Product',
    name: product.name,
    description: product.description,
  };
  
  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />
      <div>{product.name}</div>
    </>
  );
}
```

---

#### Best Practices

**1. Keep titles under 60 characters:**

```javascript
// ✅ Good - 55 characters
export const metadata = {
  title: 'How to Build Fast Websites with Next.js',
};

// ❌ Bad - 80 characters (truncated in search results)
export const metadata = {
  title: 'The Complete Ultimate Guide to Building Lightning Fast Websites Using Next.js Framework',
};
```

---

**2. Keep descriptions under 160 characters:**

```javascript
// ✅ Good - 158 characters
export const metadata = {
  description: 'Learn how to build fast, SEO-friendly websites with Next.js. Step-by-step tutorials, best practices, and real-world examples.',
};

// ❌ Bad - Too long
export const metadata = {
  description: 'This is an extremely comprehensive guide that covers everything you could possibly want to know about building websites with Next.js including all features, optimizations, and deployment strategies...',
};
```

---

**3. Always provide Open Graph images:**

```javascript
// ✅ Good - Proper OG image
export const metadata = {
  openGraph: {
    images: [
      {
        url: 'https://example.com/og-image.jpg',
        width: 1200,
        height: 630,
        alt: 'Page title',
      }
    ],
  },
};

// ❌ Bad - No image or wrong size
export const metadata = {
  openGraph: {
    images: ['https://example.com/small-logo.png'], // 200x200 too small
  },
};
```

---

**4. Handle errors gracefully:**

```javascript
export async function generateMetadata({ params }) {
  try {
    const post = await fetch(`https://api.example.com/posts/${params.slug}`)
      .then(res => {
        if (!res.ok) throw new Error('Not found');
        return res.json();
      });
    
    return {
      title: post.title,
      description: post.excerpt,
    };
  } catch (error) {
    return {
      title: 'Post Not Found',
      description: 'The requested post could not be found.',
      robots: {
        index: false,
        follow: false,
      },
    };
  }
}
```

---

**5. Use TypeScript for type safety:**

```typescript
import { Metadata } from 'next';

export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await fetchPost(params.slug);
  
  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.coverImage],
    },
  };
}
```

---

**6. Reuse data fetching:**

```javascript
// ✅ Good - Share fetched data
async function getPost(slug) {
  const res = await fetch(`https://api.example.com/posts/${slug}`, {
    next: { revalidate: 3600 } // Cache for 1 hour
  });
  return res.json();
}

export async function generateMetadata({ params }) {
  const post = await getPost(params.slug); // Cached
  
  return {
    title: post.title,
    description: post.excerpt,
  };
}

export default async function Post({ params }) {
  const post = await getPost(params.slug); // Uses same cache
  
  return <article>{post.title}</article>;
}

// ❌ Bad - Duplicate fetches
export async function generateMetadata({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(r => r.json());
  
  return { title: post.title };
}

export default async function Post({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(r => r.json()); // Fetched again!
  
  return <article>{post.title}</article>;
}
```

---

#### Common Patterns

**1. Dynamic sitemap:**

```javascript
// app/sitemap.ts
export default async function sitemap() {
  const posts = await fetch('https://api.example.com/posts')
    .then(res => res.json());
  
  return [
    {
      url: 'https://example.com',
      lastModified: new Date(),
    },
    ...posts.map(post => ({
      url: `https://example.com/blog/${post.slug}`,
      lastModified: new Date(post.updatedAt),
    })),
  ];
}
```

---

**2. Dynamic robots.txt:**

```javascript
// app/robots.ts
export default function robots() {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: ['/private/', '/admin/'],
    },
    sitemap: 'https://example.com/sitemap.xml',
  };
}
```

---

**3. Conditional metadata:**

```javascript
export async function generateMetadata({ params, searchParams }) {
  const isPreview = searchParams.preview === 'true';
  
  if (isPreview) {
    return {
      title: 'Preview Mode',
      robots: {
        index: false,
        follow: false,
      },
    };
  }
  
  const post = await fetchPost(params.slug);
  
  return {
    title: post.title,
    description: post.excerpt,
  };
}
```

---

#### Troubleshooting

**1. Metadata not showing:**

```javascript
// ❌ Wrong - Client Component can't use generateMetadata
'use client';

export async function generateMetadata() {
  // This won't work!
}

// ✅ Correct - Server Component
export async function generateMetadata() {
  // This works!
}
```

---

**2. Images not loading:**

```javascript
// ❌ Wrong - Relative URL
openGraph: {
  images: ['/image.jpg'],
}

// ✅ Correct - Absolute URL
openGraph: {
  images: ['https://example.com/image.jpg'],
}
```

---

**3. Title template not working:**

```javascript
// ❌ Wrong - String instead of object
export const metadata = {
  title: '%s | My Site', // Won't work as template
};

// ✅ Correct - Use template property
export const metadata = {
  title: {
    template: '%s | My Site',
    default: 'My Site',
  },
};
```

---

#### Comparison with Pages Router

**Pages Router (using next-seo or Head):**

```javascript
import Head from 'next/head';

export default function Page({ post }) {
  return (
    <>
      <Head>
        <title>{post.title}</title>
        <meta name="description" content={post.excerpt} />
        <meta property="og:title" content={post.title} />
        <meta property="og:image" content={post.image} />
      </Head>
      <article>{post.title}</article>
    </>
  );
}
```

**App Router (using generateMetadata):**

```javascript
export async function generateMetadata({ params }) {
  const post = await fetchPost(params.slug);
  
  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      images: [post.image],
    },
  };
}

export default async function Page({ params }) {
  const post = await fetchPost(params.slug);
  return <article>{post.title}</article>;
}
```

**Benefits of App Router approach:**
- Type-safe with TypeScript
- Automatic merging with parent metadata
- Data fetching deduplication
- Better developer experience
- Cleaner separation of concerns

---

### Q1.8: What are the Different Data Fetching Methods in Next.js?

**Answer:**

Next.js provides multiple data fetching methods to suit different use cases. The method you choose depends on when you need the data (build time, request time, or client-side) and your rendering strategy.

---

#### Overview of Data Fetching Methods

Next.js offers different approaches depending on which router you're using:

**Pages Router Methods:**
- `getStaticProps` - Fetch data at build time (SSG)
- `getServerSideProps` - Fetch data on each request (SSR)
- `getStaticPaths` - Generate dynamic routes at build time
- `getInitialProps` - Legacy method (not recommended)
- Client-side fetching - Fetch data in the browser

**App Router Methods:**
- Server Components with `fetch()` - Default data fetching
- Client Components with hooks - Client-side fetching
- Route Handlers - API endpoints for data fetching
- Server Actions - Mutations and form handling

---

#### Method 1: getStaticProps (Pages Router - SSG)

**What it is:** Fetches data at build time during `npm run build`. The page is pre-rendered as static HTML and served from CDN.

**When to use:**
- Content doesn't change often
- Same content for all users
- SEO is important
- Maximum performance needed

**Basic Syntax:**

```javascript
// pages/blog/index.js
export async function getStaticProps() {
  // Fetch data at build time
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  return {
    props: {
      posts,
    },
    // Optional: Revalidate every 60 seconds (ISR)
    revalidate: 60,
  };
}

export default function Blog({ posts }) {
  return (
    <div>
      <h1>Blog Posts</h1>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}
```

**Return Object Properties:**

```javascript
export async function getStaticProps(context) {
  return {
    props: {
      // Data to pass to component
      data: {},
    },
    
    // Optional: Regenerate page every N seconds (ISR)
    revalidate: 60,
    
    // Optional: Return 404 if data not found
    notFound: true,
    
    // Optional: Redirect to another page
    redirect: {
      destination: '/other-page',
      permanent: false,
    },
  };
}
```

**With TypeScript:**

```typescript
import { GetStaticProps } from 'next';

interface Post {
  id: string;
  title: string;
  content: string;
}

interface BlogProps {
  posts: Post[];
}

export const getStaticProps: GetStaticProps<BlogProps> = async () => {
  const posts = await fetchPosts();
  
  return {
    props: { posts },
  };
};

export default function Blog({ posts }: BlogProps) {
  return <div>{/* ... */}</div>;
}
```

**Real-World Example - Blog with Categories:**

```javascript
// pages/blog/[category]/index.js
export async function getStaticProps({ params }) {
  const { category } = params;
  
  // Fetch posts for this category
  const res = await fetch(`https://api.example.com/posts?category=${category}`);
  const posts = await res.json();
  
  // Return 404 if category has no posts
  if (posts.length === 0) {
    return {
      notFound: true,
    };
  }
  
  return {
    props: {
      category,
      posts,
    },
    revalidate: 300, // Revalidate every 5 minutes
  };
}

export async function getStaticPaths() {
  // Generate paths for all categories
  const categories = ['technology', 'design', 'business'];
  
  return {
    paths: categories.map(category => ({
      params: { category },
    })),
    fallback: 'blocking',
  };
}

export default function CategoryBlog({ category, posts }) {
  return (
    <div>
      <h1>{category} Posts</h1>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}
```

**Pros:**
- ✅ Blazing fast - served from CDN
- ✅ Perfect SEO
- ✅ No server needed at runtime
- ✅ Can use ISR for updates

**Cons:**
- ❌ Data can become stale
- ❌ Build time increases with pages
- ❌ Not suitable for personalized content

---

#### Method 2: getServerSideProps (Pages Router - SSR)

**What it is:** Fetches data on each request on the server. The page is rendered fresh for every user.

**When to use:**
- Data changes frequently
- Need real-time data
- User-specific/personalized content
- Authentication required

**Basic Syntax:**

```javascript
// pages/dashboard.js
export async function getServerSideProps(context) {
  // Runs on every request
  const res = await fetch('https://api.example.com/user/dashboard');
  const data = await res.json();

  return {
    props: {
      data,
    },
  };
}

export default function Dashboard({ data }) {
  return (
    <div>
      <h1>Dashboard</h1>
      <p>Welcome, {data.user.name}</p>
      <div>Balance: ${data.balance}</div>
    </div>
  );
}
```

**Context Object:**

```javascript
export async function getServerSideProps(context) {
  const {
    req,           // HTTP request object
    res,           // HTTP response object
    params,        // Route parameters
    query,         // Query string parameters
    preview,       // Preview mode boolean
    previewData,   // Preview mode data
    resolvedUrl,   // Normalized request URL
    locale,        // Active locale
    locales,       // All locales
    defaultLocale, // Default locale
  } = context;

  return {
    props: {},
  };
}
```

**Real-World Example - User Dashboard:**

```javascript
// pages/dashboard.js
export async function getServerSideProps({ req, res }) {
  // Get token from cookies
  const token = req.cookies.token;

  if (!token) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    };
  }

  try {
    // Fetch user data with authentication
    const userRes = await fetch('https://api.example.com/user', {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });

    if (!userRes.ok) {
      throw new Error('Unauthorized');
    }

    const user = await userRes.json();

    // Fetch dashboard data
    const [ordersRes, statsRes] = await Promise.all([
      fetch(`https://api.example.com/users/${user.id}/orders`),
      fetch(`https://api.example.com/users/${user.id}/stats`),
    ]);

    const orders = await ordersRes.json();
    const stats = await statsRes.json();

    // Set cache headers
    res.setHeader(
      'Cache-Control',
      'private, s-maxage=10, stale-while-revalidate=59'
    );

    return {
      props: {
        user,
        orders,
        stats,
      },
    };
  } catch (error) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    };
  }
}

export default function Dashboard({ user, orders, stats }) {
  return (
    <div>
      <h1>Welcome, {user.name}</h1>
      
      <div className="stats">
        <div>Total Orders: {stats.totalOrders}</div>
        <div>Total Spent: ${stats.totalSpent}</div>
      </div>

      <div className="recent-orders">
        <h2>Recent Orders</h2>
        {orders.map(order => (
          <div key={order.id}>
            <p>Order #{order.id}</p>
            <p>Status: {order.status}</p>
            <p>Total: ${order.total}</p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

**Pros:**
- ✅ Always fresh data
- ✅ Access to request context (cookies, headers)
- ✅ Can handle authentication
- ✅ Good for personalized content

**Cons:**
- ❌ Slower than static
- ❌ Higher server costs
- ❌ Requires server at runtime
- ❌ Can't use CDN caching effectively

---

#### Method 3: Client-Side Data Fetching (Pages Router)

**What it is:** Fetch data in the browser after the page has loaded, using React hooks or data fetching libraries.

**When to use:**
- Data doesn't need to be SEO-friendly
- User-specific data after login
- Real-time updates
- Interactive features (search, filters)

**Basic Approach with useEffect:**

```javascript
'use client';

import { useState, useEffect } from 'react';

export default function Posts() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch('https://api.example.com/posts')
      .then(res => res.json())
      .then(data => {
        setPosts(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
        </article>
      ))}
    </div>
  );
}
```

**Using SWR (Recommended):**

```javascript
'use client';

import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

export default function Profile() {
  const { data, error, isLoading } = useSWR('/api/user', fetcher);

  if (error) return <div>Failed to load</div>;
  if (isLoading) return <div>Loading...</div>;

  return (
    <div>
      <h1>Hello {data.name}!</h1>
      <p>Email: {data.email}</p>
    </div>
  );
}
```

**Using React Query:**

```javascript
'use client';

import { useQuery } from '@tanstack/react-query';

export default function Posts() {
  const { data, error, isLoading } = useQuery({
    queryKey: ['posts'],
    queryFn: async () => {
      const res = await fetch('https://api.example.com/posts');
      return res.json();
    },
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div>
      {data.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
        </article>
      ))}
    </div>
  );
}
```

**Real-World Example - Search with Debouncing:**

```javascript
'use client';

import { useState, useEffect } from 'react';
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

export default function SearchProducts() {
  const [query, setQuery] = useState('');
  const [debouncedQuery, setDebouncedQuery] = useState('');

  // Debounce search query
  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedQuery(query);
    }, 500);

    return () => clearTimeout(timer);
  }, [query]);

  // Fetch products with SWR
  const { data: products, isLoading } = useSWR(
    debouncedQuery ? `/api/products/search?q=${debouncedQuery}` : null,
    fetcher
  );

  return (
    <div>
      <input
        type="search"
        placeholder="Search products..."
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />

      {isLoading && <div>Searching...</div>}

      {products && (
        <div>
          <p>Found {products.length} products</p>
          {products.map(product => (
            <div key={product.id}>
              <h3>{product.name}</h3>
              <p>${product.price}</p>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}
```

**Pros:**
- ✅ No impact on initial page load
- ✅ Great for interactive features
- ✅ Automatic caching and revalidation (with SWR/React Query)
- ✅ Real-time updates

**Cons:**
- ❌ Not SEO-friendly
- ❌ Content not available immediately
- ❌ Requires JavaScript
- ❌ Loading states needed

---

#### Method 4: App Router - Server Components (Default)

**What it is:** In the App Router, Server Components can fetch data directly using `async/await` with enhanced `fetch()`.

**When to use:**
- Default choice for App Router
- Need server-side data fetching
- Want automatic request deduplication
- SEO is important

**Basic Syntax:**

```javascript
// app/posts/page.js
export default async function PostsPage() {
  // Fetch data in Server Component
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  return (
    <div>
      <h1>Posts</h1>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}
```

**Caching Options:**

```javascript
// app/page.js

// 1. Static (cached indefinitely) - DEFAULT
export default async function Page() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  return <div>{data.title}</div>;
}

// 2. Revalidate (ISR) - Regenerate every N seconds
export default async function Page() {
  const res = await fetch('https://api.example.com/data', {
    next: { revalidate: 60 } // Revalidate every 60 seconds
  });
  const data = await res.json();
  return <div>{data.title}</div>;
}

// 3. Dynamic (no cache) - Fetch on every request
export default async function Page() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'no-store' // Don't cache, always fresh
  });
  const data = await res.json();
  return <div>{data.title}</div>;
}

// 4. Force dynamic rendering for entire route
export const dynamic = 'force-dynamic';

export default async function Page() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  return <div>{data.title}</div>;
}
```

**Route Segment Config:**

```javascript
// app/dashboard/page.js

// Control caching behavior for entire route
export const dynamic = 'force-dynamic'; // 'auto' | 'force-dynamic' | 'error' | 'force-static'
export const dynamicParams = true; // true | false
export const revalidate = 60; // false | 0 | number
export const fetchCache = 'auto'; // 'auto' | 'default-cache' | 'only-cache' | 'force-cache' | 'force-no-store' | 'default-no-store' | 'only-no-store'
export const runtime = 'nodejs'; // 'nodejs' | 'edge'
export const preferredRegion = 'auto'; // 'auto' | 'global' | 'home' | string | string[]

export default async function Dashboard() {
  const data = await fetchDashboardData();
  return <div>{/* ... */}</div>;
}
```

**Real-World Example - Product Page:**

```javascript
// app/products/[id]/page.js
import { notFound } from 'next/navigation';

export default async function ProductPage({ params }) {
  // Fetch product data
  const product = await fetch(
    `https://api.example.com/products/${params.id}`,
    { next: { revalidate: 300 } } // Revalidate every 5 minutes
  ).then(res => {
    if (!res.ok) return null;
    return res.json();
  });

  // Show 404 if product not found
  if (!product) {
    notFound();
  }

  // Fetch related data in parallel
  const [reviews, relatedProducts] = await Promise.all([
    fetch(`https://api.example.com/products/${params.id}/reviews`)
      .then(res => res.json()),
    fetch(`https://api.example.com/products/${params.id}/related`)
      .then(res => res.json()),
  ]);

  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
      <p>${product.price}</p>

      <div>
        <h2>Reviews</h2>
        {reviews.map(review => (
          <div key={review.id}>
            <p>{review.comment}</p>
            <p>Rating: {review.rating}/5</p>
          </div>
        ))}
      </div>

      <div>
        <h2>Related Products</h2>
        {relatedProducts.map(item => (
          <div key={item.id}>{item.name}</div>
        ))}
      </div>
    </div>
  );
}

// Generate static params for popular products
export async function generateStaticParams() {
  const products = await fetch('https://api.example.com/products/popular')
    .then(res => res.json());

  return products.map(product => ({
    id: product.id.toString(),
  }));
}
```

**Parallel Data Fetching:**

```javascript
// app/dashboard/page.js
export default async function Dashboard() {
  // ✅ Good - Parallel fetching
  const [user, orders, stats] = await Promise.all([
    fetch('https://api.example.com/user').then(r => r.json()),
    fetch('https://api.example.com/orders').then(r => r.json()),
    fetch('https://api.example.com/stats').then(r => r.json()),
  ]);

  return (
    <div>
      <h1>Hello, {user.name}</h1>
      <div>Orders: {orders.length}</div>
      <div>Total: ${stats.total}</div>
    </div>
  );
}

// ❌ Bad - Sequential fetching (slower)
export default async function Dashboard() {
  const user = await fetch('https://api.example.com/user').then(r => r.json());
  const orders = await fetch('https://api.example.com/orders').then(r => r.json());
  const stats = await fetch('https://api.example.com/stats').then(r => r.json());
  
  // This takes 3x longer than parallel!
}
```

**Streaming with Suspense:**

```javascript
// app/dashboard/page.js
import { Suspense } from 'react';

async function UserData() {
  const user = await fetch('https://api.example.com/user')
    .then(r => r.json());
  return <div>Hello, {user.name}</div>;
}

async function Orders() {
  const orders = await fetch('https://api.example.com/orders')
    .then(r => r.json());
  return (
    <div>
      {orders.map(order => (
        <div key={order.id}>Order #{order.id}</div>
      ))}
    </div>
  );
}

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      
      <Suspense fallback={<div>Loading user...</div>}>
        <UserData />
      </Suspense>

      <Suspense fallback={<div>Loading orders...</div>}>
        <Orders />
      </Suspense>
    </div>
  );
}
```

**Pros:**
- ✅ No client-side JavaScript needed
- ✅ Automatic request deduplication
- ✅ Fine-grained caching control
- ✅ Can stream with Suspense
- ✅ Better performance

**Cons:**
- ❌ Can't use React hooks
- ❌ No client-side interactivity in Server Components
- ❌ Learning curve for new patterns

---

#### Method 5: App Router - Client Components

**What it is:** Client Components in the App Router work similarly to Pages Router, fetching data on the client side.

**When to use:**
- Need client-side interactivity
- Real-time updates
- User input dependent fetching

**Basic Syntax:**

```javascript
'use client';

import { useState, useEffect } from 'react';

export default function ClientComponent() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api/data')
      .then(res => res.json())
      .then(setData);
  }, []);

  if (!data) return <div>Loading...</div>;

  return <div>{data.message}</div>;
}
```

**With SWR:**

```javascript
'use client';

import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

export default function Profile() {
  const { data, error, isLoading, mutate } = useSWR('/api/user/profile', fetcher, {
    refreshInterval: 5000, // Refresh every 5 seconds
    revalidateOnFocus: true,
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error</div>;

  return (
    <div>
      <h1>{data.name}</h1>
      <button onClick={() => mutate()}>Refresh</button>
    </div>
  );
}
```

---

#### Method 6: Route Handlers (API Routes)

**What it is:** Create API endpoints that can be called from anywhere (client or server).

**Pages Router:**

```javascript
// pages/api/posts.js
export default async function handler(req, res) {
  if (req.method === 'GET') {
    const posts = await fetchPosts();
    res.status(200).json(posts);
  } else if (req.method === 'POST') {
    const { title, content } = req.body;
    const post = await createPost({ title, content });
    res.status(201).json(post);
  } else {
    res.status(405).json({ error: 'Method not allowed' });
  }
}
```

**App Router:**

```javascript
// app/api/posts/route.js
import { NextResponse } from 'next/server';

export async function GET(request) {
  const posts = await fetchPosts();
  return NextResponse.json(posts);
}

export async function POST(request) {
  const { title, content } = await request.json();
  const post = await createPost({ title, content });
  return NextResponse.json(post, { status: 201 });
}
```

**Dynamic Route Handlers:**

```javascript
// app/api/posts/[id]/route.js
export async function GET(request, { params }) {
  const post = await fetchPost(params.id);
  
  if (!post) {
    return NextResponse.json({ error: 'Not found' }, { status: 404 });
  }
  
  return NextResponse.json(post);
}

export async function PUT(request, { params }) {
  const data = await request.json();
  const post = await updatePost(params.id, data);
  return NextResponse.json(post);
}

export async function DELETE(request, { params }) {
  await deletePost(params.id);
  return NextResponse.json({ message: 'Deleted' });
}
```

---

#### Method 7: Incremental Static Regeneration (ISR)

**What it is:** Static pages that can be updated after build time without rebuilding the entire site.

**Pages Router:**

```javascript
// pages/products/[id].js
export async function getStaticProps({ params }) {
  const product = await fetchProduct(params.id);

  return {
    props: { product },
    revalidate: 60, // Regenerate page every 60 seconds
  };
}

export async function getStaticPaths() {
  return {
    paths: [], // Generate on-demand
    fallback: 'blocking',
  };
}

export default function Product({ product }) {
  return <div>{product.name}</div>;
}
```

**App Router:**

```javascript
// app/products/[id]/page.js
export const revalidate = 60; // Revalidate every 60 seconds

export default async function Product({ params }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(r => r.json());

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

// Usage:
// POST /api/revalidate
// { "path": "/products/123" }
```

**Tagged Caching:**

```javascript
// app/products/[id]/page.js
export default async function Product({ params }) {
  const product = await fetch(
    `https://api.example.com/products/${params.id}`,
    {
      next: {
        tags: ['products', `product-${params.id}`],
        revalidate: 3600,
      },
    }
  ).then(r => r.json());

  return <div>{product.name}</div>;
}

// Revalidate specific product:
// revalidateTag('product-123');

// Revalidate all products:
// revalidateTag('products');
```

---

#### Comparison Table

| Method | When Data Fetches | SEO | Performance | Use Case |
|--------|------------------|-----|-------------|----------|
| **getStaticProps** | Build time | ✅ Excellent | ⚡ Fastest | Blogs, marketing pages |
| **getServerSideProps** | Each request | ✅ Excellent | 🐌 Slower | Personalized content |
| **Client-side** | Browser | ❌ Poor | ⚡ After load | Interactive features |
| **Server Components** | Request/Build | ✅ Excellent | ⚡ Fast | Default App Router |
| **ISR** | Build + periodic | ✅ Excellent | ⚡ Fast | E-commerce, news |
| **Route Handlers** | On-demand | N/A | Varies | API endpoints |

---

#### Decision Tree

```
Do you need the data for SEO?
│
├─ Yes
│  │
│  ├─ Does the data change frequently?
│  │  │
│  │  ├─ No → Use getStaticProps (Pages) or Server Components (App)
│  │  │
│  │  └─ Yes
│  │     │
│  │     ├─ Can it be cached for a few seconds/minutes?
│  │     │  └─ Yes → Use ISR (revalidate)
│  │     │
│  │     └─ No, needs to be real-time
│  │        └─ Use getServerSideProps (Pages) or dynamic Server Components (App)
│  │
│  └─ Is it user-specific/personalized?
│     └─ Yes → Use getServerSideProps or Client-side fetching
│
└─ No (not for SEO)
   │
   ├─ Fetch after user interaction?
   │  └─ Yes → Use Client-side fetching (useEffect, SWR, React Query)
   │
   └─ Need it on initial load but not for SEO?
      └─ Use Client-side fetching or Server Components
```

---

#### Best Practices

**1. Prefer Server Components (App Router):**

```javascript
// ✅ Good - Server Component (default)
export default async function Page() {
  const data = await fetch('https://api.example.com/data').then(r => r.json());
  return <div>{data.title}</div>;
}

// ❌ Unnecessary - Client Component for static data
'use client';
import { useEffect, useState } from 'react';

export default function Page() {
  const [data, setData] = useState(null);
  useEffect(() => {
    fetch('https://api.example.com/data')
      .then(r => r.json())
      .then(setData);
  }, []);
  return <div>{data?.title}</div>;
}
```

**2. Use Parallel Fetching:**

```javascript
// ✅ Good - Parallel (fast)
const [user, posts, comments] = await Promise.all([
  fetchUser(),
  fetchPosts(),
  fetchComments(),
]);

// ❌ Bad - Sequential (slow)
const user = await fetchUser();
const posts = await fetchPosts();
const comments = await fetchComments();
```

**3. Choose the Right Caching Strategy:**

```javascript
// Static content (blog posts)
fetch('https://api.example.com/posts', {
  cache: 'force-cache' // Cache forever
});

// Frequently updated (stock prices)
fetch('https://api.example.com/stocks', {
  cache: 'no-store' // Never cache
});

// Periodically updated (product catalog)
fetch('https://api.example.com/products', {
  next: { revalidate: 300 } // Cache for 5 minutes
});
```

**4. Use SWR for Client-Side:**

```javascript
// ✅ Good - SWR handles caching, revalidation, error handling
'use client';
import useSWR from 'swr';

export default function Profile() {
  const { data, error } = useSWR('/api/user', fetcher);
  // ...
}

// ❌ Manual approach - more code, no automatic features
'use client';
import { useEffect, useState } from 'react';

export default function Profile() {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch('/api/user')
      .then(r => r.json())
      .then(setData)
      .catch(setError);
  }, []);
  // ...
}
```

**5. Handle Loading and Error States:**

```javascript
// App Router with Suspense
export default function Page() {
  return (
    <Suspense fallback={<Loading />}>
      <DataComponent />
    </Suspense>
  );
}

// Pages Router with getServerSideProps
export async function getServerSideProps() {
  try {
    const data = await fetchData();
    return { props: { data } };
  } catch (error) {
    return {
      notFound: true, // or redirect to error page
    };
  }
}
```

**6. Implement Error Boundaries:**

```javascript
// app/error.js
'use client';

export default function Error({ error, reset }) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <p>{error.message}</p>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

**7. Use TypeScript for Type Safety:**

```typescript
interface Post {
  id: string;
  title: string;
  content: string;
}

export default async function Blog() {
  const posts: Post[] = await fetch('https://api.example.com/posts')
    .then(r => r.json());

  return (
    <div>
      {posts.map(post => (
        <article key={post.id}>{post.title}</article>
      ))}
    </div>
  );
}
```

**8. Monitor Performance:**

```javascript
// Track fetch performance
export default async function Page() {
  const start = Date.now();
  
  const data = await fetch('https://api.example.com/data')
    .then(r => r.json());
  
  const duration = Date.now() - start;
  console.log(`Fetch took ${duration}ms`);
  
  return <div>{data.title}</div>;
}
```

---

#### Real-World Example - E-commerce Product Listing

**Hybrid Approach (Pages Router):**

```javascript
// pages/products/[category].js

// Static generation for popular categories
export async function getStaticProps({ params }) {
  // Fetch products at build time
  const products = await fetch(
    `https://api.example.com/products?category=${params.category}`
  ).then(r => r.json());

  return {
    props: { products, category: params.category },
    revalidate: 300, // ISR: Regenerate every 5 minutes
  };
}

export async function getStaticPaths() {
  // Pre-generate only popular categories
  const popularCategories = ['electronics', 'clothing', 'books'];
  
  return {
    paths: popularCategories.map(cat => ({ params: { category: cat } })),
    fallback: 'blocking', // Generate other categories on-demand
  };
}

export default function CategoryPage({ products, category }) {
  return (
    <div>
      <h1>{category}</h1>
      
      {/* Static product grid */}
      <ProductGrid products={products} />
      
      {/* Client-side filters */}
      <ProductFilters category={category} />
      
      {/* Client-side recommendations */}
      <RecommendedProducts />
    </div>
  );
}

// Client-side filtering component
function ProductFilters({ category }) {
  'use client';
  
  const [filters, setFilters] = useState({});
  const { data: filteredProducts } = useSWR(
    `/api/products/filter?category=${category}&${new URLSearchParams(filters)}`,
    fetcher
  );

  return (
    <div>
      {/* Filter controls */}
      <FilterControls onChange={setFilters} />
      
      {/* Filtered results */}
      {filteredProducts && <ProductList products={filteredProducts} />}
    </div>
  );
}
```

**App Router Approach:**

```javascript
// app/products/[category]/page.js

// ISR: Revalidate every 5 minutes
export const revalidate = 300;

// Generate static params for popular categories
export async function generateStaticParams() {
  return [
    { category: 'electronics' },
    { category: 'clothing' },
    { category: 'books' },
  ];
}

export default async function CategoryPage({ params, searchParams }) {
  // Server-side: Fetch products
  const products = await fetch(
    `https://api.example.com/products?category=${params.category}`,
    { next: { revalidate: 300, tags: ['products'] } }
  ).then(r => r.json());

  // Server-side: Apply server filters
  const filters = searchParams.filters ? JSON.parse(searchParams.filters) : {};
  
  return (
    <div>
      <h1>{params.category}</h1>
      
      {/* Server Component: Static content */}
      <ProductGrid products={products} />
      
      {/* Client Component: Interactive filters */}
      <ClientFilters category={params.category} initialFilters={filters} />
    </div>
  );
}

// app/products/[category]/ClientFilters.js
'use client';

import { useState } from 'react';
import { useRouter, useSearchParams } from 'next/navigation';

export default function ClientFilters({ category, initialFilters }) {
  const router = useRouter();
  const searchParams = useSearchParams();
  const [filters, setFilters] = useState(initialFilters);

  const applyFilters = (newFilters) => {
    setFilters(newFilters);
    const params = new URLSearchParams(searchParams);
    params.set('filters', JSON.stringify(newFilters));
    router.push(`/products/${category}?${params.toString()}`);
  };

  return (
    <div>
      {/* Filter UI */}
      <button onClick={() => applyFilters({ price: 'low-to-high' })}>
        Sort by Price
      </button>
    </div>
  );
}
```

---

#### Troubleshooting Common Issues

**1. Data not updating:**

```javascript
// Problem: Using default cache
const data = await fetch('https://api.example.com/data');

// Solution: Disable cache for dynamic data
const data = await fetch('https://api.example.com/data', {
  cache: 'no-store'
});

// Or use revalidation
const data = await fetch('https://api.example.com/data', {
  next: { revalidate: 0 }
});
```

**2. Hydration errors:**

```javascript
// Problem: Server and client rendering different content
export default function Page() {
  return <div>{new Date().toISOString()}</div>; // Different on server/client
}

// Solution: Use client-side rendering for dynamic content
'use client';
import { useState, useEffect } from 'react';

export default function Page() {
  const [date, setDate] = useState(null);
  
  useEffect(() => {
    setDate(new Date().toISOString());
  }, []);

  return <div>{date || 'Loading...'}</div>;
}
```

**3. Slow page loads:**

```javascript
// Problem: Sequential fetching
async function Page() {
  const user = await fetchUser();
  const posts = await fetchPosts(); // Waits for user
  const comments = await fetchComments(); // Waits for posts
}

// Solution: Parallel fetching
async function Page() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(),
    fetchPosts(),
    fetchComments(),
  ]);
}
```

**4. CORS issues with API routes:**

```javascript
// app/api/data/route.js
export async function GET(request) {
  // Add CORS headers
  return Response.json(data, {
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, OPTIONS',
      'Access-Control-Allow-Headers': 'Content-Type',
    },
  });
}
```

---

### Q1.9: What is generateStaticParams() and How Does It Work?

**Answer:**

`generateStaticParams()` is a function in Next.js App Router that generates static routes at build time for dynamic segments. It's the modern replacement for `getStaticPaths()` from the Pages Router.

---

#### What is generateStaticParams?

**Purpose:**
- Pre-render dynamic routes at build time
- Generate static HTML for known parameter values
- Improve performance by creating pages ahead of time
- Enable Static Site Generation (SSG) for dynamic routes

**When to use:**
- Blog posts, articles, documentation
- Product pages with known IDs
- Category pages
- User profiles (for popular users)
- Any dynamic route where you can predict the parameters

---

#### Basic Syntax

```typescript
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  // Fetch list of posts
  const posts = await fetch('https://api.example.com/posts').then(res => res.json());
  
  // Return array of params
  return posts.map((post) => ({
    slug: post.slug,
  }));
}

export default async function BlogPost({ params }: { params: { slug: string } }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(res => res.json());
  
  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
    </article>
  );
}
```

---

#### How It Works

**Build Process:**

```
1. Build time (npm run build)
   ↓
2. generateStaticParams() executes
   ↓
3. Returns array of params: [{ slug: 'post-1' }, { slug: 'post-2' }, ...]
   ↓
4. Next.js generates HTML for each:
   - /blog/post-1
   - /blog/post-2
   - /blog/post-3
   ↓
5. Static HTML files deployed to CDN
   ↓
6. User visits /blog/post-1 → Instant load from CDN ⚡
```

---

#### Return Value

**Must return an array of objects:**

```typescript
// Single parameter
export async function generateStaticParams() {
  return [
    { slug: 'post-1' },
    { slug: 'post-2' },
    { slug: 'post-3' },
  ];
}

// Multiple parameters
export async function generateStaticParams() {
  return [
    { category: 'tech', slug: 'nextjs-guide' },
    { category: 'design', slug: 'figma-tips' },
    { category: 'tech', slug: 'react-hooks' },
  ];
}

// From API
export async function generateStaticParams() {
  const products = await fetch('https://api.example.com/products')
    .then(res => res.json());
  
  return products.map(product => ({
    id: product.id.toString(), // Must be string!
  }));
}
```

---

#### Real-World Example 1: Blog with Categories

```typescript
// app/blog/[category]/[slug]/page.tsx

interface Post {
  category: string;
  slug: string;
  title: string;
  content: string;
}

// Generate all blog post routes
export async function generateStaticParams() {
  // Fetch all posts from API/database
  const posts: Post[] = await fetch('https://api.example.com/posts')
    .then(res => res.json());
  
  // Return category + slug for each post
  return posts.map(post => ({
    category: post.category,
    slug: post.slug,
  }));
}

export default async function BlogPost({ 
  params 
}: { 
  params: { category: string; slug: string } 
}) {
  const post = await fetch(
    `https://api.example.com/posts/${params.category}/${params.slug}`
  ).then(res => res.json());
  
  return (
    <article>
      <div className="category">{params.category}</div>
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}

// Generate metadata for SEO
export async function generateMetadata({ params }: { params: { slug: string } }) {
  const post = await fetch(
    `https://api.example.com/posts/${params.category}/${params.slug}`
  ).then(res => res.json());
  
  return {
    title: post.title,
    description: post.excerpt,
  };
}
```

---

#### Real-World Example 2: E-commerce Product Pages

```typescript
// app/products/[id]/page.tsx

interface Product {
  id: string;
  name: string;
  price: number;
  description: string;
  image: string;
}

// Pre-generate only popular products
export async function generateStaticParams() {
  // Strategy: Only pre-render top 100 products
  const popularProducts = await fetch(
    'https://api.example.com/products/popular?limit=100'
  ).then(res => res.json());
  
  return popularProducts.map((product: Product) => ({
    id: product.id.toString(),
  }));
}

// Enable on-demand generation for other products
export const dynamicParams = true; // Important!

export default async function ProductPage({ 
  params 
}: { 
  params: { id: string } 
}) {
  const product: Product = await fetch(
    `https://api.example.com/products/${params.id}`
  ).then(res => res.json());
  
  if (!product) {
    notFound(); // Show 404 page
  }
  
  return (
    <div className="product">
      <img src={product.image} alt={product.name} />
      <h1>{product.name}</h1>
      <p className="price">${product.price}</p>
      <p>{product.description}</p>
      <button>Add to Cart</button>
    </div>
  );
}

// ISR: Revalidate every 5 minutes
export const revalidate = 300;
```

---

#### Real-World Example 3: Documentation with Nested Routes

```typescript
// app/docs/[...slug]/page.tsx

interface DocPage {
  slug: string[];
  title: string;
  content: string;
}

// Generate all documentation pages
export async function generateStaticParams() {
  const docs: DocPage[] = await fetch('https://api.example.com/docs')
    .then(res => res.json());
  
  // Handle nested routes like /docs/getting-started/installation
  return docs.map(doc => ({
    slug: doc.slug, // slug is already an array like ['getting-started', 'installation']
  }));
}

export default async function DocPage({ 
  params 
}: { 
  params: { slug: string[] } 
}) {
  const path = params.slug.join('/');
  const doc = await fetch(`https://api.example.com/docs/${path}`)
    .then(res => res.json());
  
  return (
    <article className="prose">
      <h1>{doc.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: doc.content }} />
    </article>
  );
}
```

---

#### Real-World Example 4: Multi-Language Support

```typescript
// app/[lang]/products/[id]/page.tsx

const languages = ['en', 'es', 'fr', 'de'];

// Generate pages for all products in all languages
export async function generateStaticParams() {
  const products = await fetch('https://api.example.com/products')
    .then(res => res.json());
  
  // Create params for each language + product combination
  const params = [];
  
  for (const lang of languages) {
    for (const product of products) {
      params.push({
        lang,
        id: product.id.toString(),
      });
    }
  }
  
  return params;
}

export default async function ProductPage({ 
  params 
}: { 
  params: { lang: string; id: string } 
}) {
  const product = await fetch(
    `https://api.example.com/products/${params.id}?lang=${params.lang}`
  ).then(res => res.json());
  
  return (
    <div>
      <h1>{product.name}</h1>
      <p>{product.description}</p>
    </div>
  );
}
```

---

#### Configuration Options

**1. dynamicParams (Control fallback behavior):**

```typescript
// app/products/[id]/page.tsx

// Allow on-demand generation for paths not returned by generateStaticParams
export const dynamicParams = true; // Default

// Show 404 for paths not in generateStaticParams
export const dynamicParams = false;

export async function generateStaticParams() {
  // Only generate these 3 products
  return [
    { id: '1' },
    { id: '2' },
    { id: '3' },
  ];
}

// With dynamicParams = true:
// /products/1 → Pre-rendered ✅
// /products/999 → Generated on first request ✅

// With dynamicParams = false:
// /products/1 → Pre-rendered ✅
// /products/999 → 404 Not Found ❌
```

---

#### Combining with ISR (Incremental Static Regeneration)

```typescript
// app/blog/[slug]/page.tsx

// Pre-generate popular posts
export async function generateStaticParams() {
  const popularPosts = await fetch('https://api.example.com/posts/popular')
    .then(res => res.json());
  
  return popularPosts.map(post => ({
    slug: post.slug,
  }));
}

// Revalidate every 60 seconds
export const revalidate = 60;

// Allow other posts to be generated on-demand
export const dynamicParams = true;

export default async function BlogPost({ params }: { params: { slug: string } }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(res => res.json());
  
  return <article>{post.content}</article>;
}
```

---

#### Parallel Route Segments

**Generate params for parent and child routes:**

```typescript
// app/blog/[category]/page.tsx
export async function generateStaticParams() {
  return [
    { category: 'tech' },
    { category: 'design' },
  ];
}

// app/blog/[category]/[slug]/page.tsx
export async function generateStaticParams() {
  // Get all categories first
  const categories = ['tech', 'design'];
  const params = [];
  
  for (const category of categories) {
    const posts = await fetch(`https://api.example.com/posts?category=${category}`)
      .then(res => res.json());
    
    for (const post of posts) {
      params.push({
        category,
        slug: post.slug,
      });
    }
  }
  
  return params;
}
```

**Optimized approach - Use parent params:**

```typescript
// app/blog/[category]/[slug]/page.tsx
export async function generateStaticParams({ 
  params: { category } 
}: { 
  params: { category: string } 
}) {
  // generateStaticParams receives parent params!
  const posts = await fetch(`https://api.example.com/posts?category=${category}`)
    .then(res => res.json());
  
  return posts.map(post => ({
    slug: post.slug,
  }));
}
```

---

#### Fetching from Database

```typescript
// app/users/[id]/page.tsx
import { db } from '@/lib/database';

export async function generateStaticParams() {
  // Query database directly
  const users = await db.user.findMany({
    select: { id: true },
    take: 1000, // Limit to 1000 users
  });
  
  return users.map(user => ({
    id: user.id.toString(),
  }));
}

export default async function UserProfile({ 
  params 
}: { 
  params: { id: string } 
}) {
  const user = await db.user.findUnique({
    where: { id: parseInt(params.id) },
  });
  
  if (!user) {
    notFound();
  }
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

---

#### Performance Optimization

**1. Limit Static Generation:**

```typescript
// Don't pre-generate ALL products (slow build)
export async function generateStaticParams() {
  // ❌ Bad: Generate 10,000 pages
  const allProducts = await fetch('https://api.example.com/products')
    .then(res => res.json());
  
  // ✅ Good: Generate only top 100
  const popularProducts = await fetch('https://api.example.com/products?popular=true&limit=100')
    .then(res => res.json());
  
  return popularProducts.map(p => ({ id: p.id }));
}

// Allow others to be generated on-demand
export const dynamicParams = true;
```

**2. Parallel Data Fetching:**

```typescript
export async function generateStaticParams() {
  // ✅ Good: Fetch all categories in parallel
  const [techPosts, designPosts, businessPosts] = await Promise.all([
    fetch('https://api.example.com/posts?category=tech').then(r => r.json()),
    fetch('https://api.example.com/posts?category=design').then(r => r.json()),
    fetch('https://api.example.com/posts?category=business').then(r => r.json()),
  ]);
  
  return [
    ...techPosts.map(p => ({ category: 'tech', slug: p.slug })),
    ...designPosts.map(p => ({ category: 'design', slug: p.slug })),
    ...businessPosts.map(p => ({ category: 'business', slug: p.slug })),
  ];
}
```

---

#### Comparison: getStaticPaths vs generateStaticParams

| Feature | getStaticPaths (Pages) | generateStaticParams (App) |
|---------|----------------------|---------------------------|
| **Router** | Pages Router | App Router |
| **Return Format** | `{ paths, fallback }` | Array of params |
| **Fallback Control** | `fallback: true/false/'blocking'` | `dynamicParams: true/false` |
| **Function Type** | Regular function | Can be async |
| **Nested Routes** | Separate for each level | Can receive parent params |
| **TypeScript** | Manual typing | Better inference |

**Pages Router (getStaticPaths):**

```typescript
// pages/blog/[slug].tsx
export async function getStaticPaths() {
  const posts = await fetchPosts();
  
  return {
    paths: posts.map(post => ({
      params: { slug: post.slug },
    })),
    fallback: 'blocking',
  };
}

export async function getStaticProps({ params }) {
  const post = await fetchPost(params.slug);
  return { props: { post } };
}

export default function BlogPost({ post }) {
  return <article>{post.content}</article>;
}
```

**App Router (generateStaticParams):**

```typescript
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await fetchPosts();
  
  return posts.map(post => ({
    slug: post.slug,
  }));
}

export const dynamicParams = true; // Equivalent to fallback: 'blocking'

export default async function BlogPost({ params }) {
  const post = await fetchPost(params.slug);
  return <article>{post.content}</article>;
}
```

---

#### Best Practices

**1. Type Safety with TypeScript:**

```typescript
interface Params {
  id: string;
}

export async function generateStaticParams(): Promise<Params[]> {
  const products = await fetchProducts();
  
  return products.map(product => ({
    id: product.id.toString(), // Ensure it's a string
  }));
}

export default async function Page({ params }: { params: Params }) {
  // params.id is typed as string
}
```

**2. Handle Errors Gracefully:**

```typescript
export async function generateStaticParams() {
  try {
    const posts = await fetch('https://api.example.com/posts')
      .then(res => {
        if (!res.ok) throw new Error('Failed to fetch');
        return res.json();
      });
    
    return posts.map(post => ({ slug: post.slug }));
  } catch (error) {
    console.error('Error generating static params:', error);
    return []; // Return empty array on error
  }
}
```

**3. Cache API Responses:**

```typescript
export async function generateStaticParams() {
  // Cache the fetch during build
  const posts = await fetch('https://api.example.com/posts', {
    next: { revalidate: 3600 }, // Cache for 1 hour during build
  }).then(res => res.json());
  
  return posts.map(post => ({ slug: post.slug }));
}
```

**4. Use Environment Variables:**

```typescript
export async function generateStaticParams() {
  const API_URL = process.env.NEXT_PUBLIC_API_URL;
  
  const posts = await fetch(`${API_URL}/posts`)
    .then(res => res.json());
  
  return posts.map(post => ({ slug: post.slug }));
}
```

**5. Validate Returned Data:**

```typescript
export async function generateStaticParams() {
  const posts = await fetchPosts();
  
  // Filter out invalid entries
  return posts
    .filter(post => post.slug && typeof post.slug === 'string')
    .map(post => ({
      slug: post.slug,
    }));
}
```

**6. Limit Build Time:**

```typescript
export async function generateStaticParams() {
  // For development: generate fewer pages
  const limit = process.env.NODE_ENV === 'development' ? 10 : 1000;
  
  const posts = await fetch(`https://api.example.com/posts?limit=${limit}`)
    .then(res => res.json());
  
  return posts.map(post => ({ slug: post.slug }));
}
```

---

#### Common Patterns

**1. Pagination:**

```typescript
// app/blog/page/[number]/page.tsx
export async function generateStaticParams() {
  const totalPosts = await fetch('https://api.example.com/posts/count')
    .then(res => res.json());
  
  const postsPerPage = 10;
  const totalPages = Math.ceil(totalPosts / postsPerPage);
  
  return Array.from({ length: totalPages }, (_, i) => ({
    number: (i + 1).toString(),
  }));
}
```

**2. Internationalization:**

```typescript
// app/[lang]/blog/[slug]/page.tsx
const locales = ['en', 'es', 'fr', 'de', 'ja'];

export async function generateStaticParams() {
  const posts = await fetchPosts();
  const params = [];
  
  for (const locale of locales) {
    for (const post of posts) {
      params.push({
        lang: locale,
        slug: post.slug,
      });
    }
  }
  
  return params;
}
```

**3. Date-based Routes:**

```typescript
// app/blog/[year]/[month]/[day]/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await fetchPosts();
  
  return posts.map(post => {
    const date = new Date(post.publishedAt);
    return {
      year: date.getFullYear().toString(),
      month: (date.getMonth() + 1).toString().padStart(2, '0'),
      day: date.getDate().toString().padStart(2, '0'),
      slug: post.slug,
    };
  });
}
```

---

#### Troubleshooting

**1. Build taking too long:**

```typescript
// Problem: Generating too many pages
export async function generateStaticParams() {
  const all = await fetchAll(); // 100,000 items!
  return all.map(item => ({ id: item.id }));
}

// Solution: Limit and use on-demand generation
export async function generateStaticParams() {
  const popular = await fetchPopular(100); // Only 100 items
  return popular.map(item => ({ id: item.id }));
}

export const dynamicParams = true; // Generate others on-demand
```

**2. Parameters not matching:**

```typescript
// Problem: Params don't match route segment names
// File: app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  return [{ id: '1' }]; // ❌ Wrong! Should be 'slug', not 'id'
}

// Solution: Match the segment name
export async function generateStaticParams() {
  return [{ slug: 'my-post' }]; // ✅ Correct!
}
```

**3. Dynamic params not working:**

```typescript
// Problem: 404 for non-pre-generated paths
// Solution: Enable dynamicParams
export const dynamicParams = true;

export async function generateStaticParams() {
  return [{ id: '1' }, { id: '2' }];
}

// Now /products/999 will work (generated on-demand)
```

**4. Type errors:**

```typescript
// Problem: Type mismatch
export async function generateStaticParams() {
  return [{ id: 123 }]; // ❌ id should be string
}

// Solution: Convert to string
export async function generateStaticParams() {
  return [{ id: '123' }]; // ✅ Correct
}
```

---

#### Advanced Usage

**1. Conditional Generation:**

```typescript
export async function generateStaticParams() {
  // Only pre-generate in production
  if (process.env.NODE_ENV !== 'production') {
    return [{ slug: 'example' }]; // Just one page for dev
  }
  
  const posts = await fetchAllPosts();
  return posts.map(post => ({ slug: post.slug }));
}
```

**2. Incremental Generation:**

```typescript
// Generate popular pages at build time
export async function generateStaticParams() {
  const popular = await fetchPopularProducts();
  return popular.map(p => ({ id: p.id }));
}

// Allow others to be generated
export const dynamicParams = true;

// Revalidate every hour
export const revalidate = 3600;

// New pages are added to the static pool automatically!
```

**3. With Metadata:**

```typescript
export async function generateStaticParams() {
  const posts = await fetchPosts();
  return posts.map(post => ({ slug: post.slug }));
}

export async function generateMetadata({ params }) {
  const post = await fetchPost(params.slug);
  
  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      images: [post.coverImage],
    },
  };
}

export default async function Page({ params }) {
  const post = await fetchPost(params.slug);
  return <article>{post.content}</article>;
}
```

---

#### Summary

**Key Points:**
- `generateStaticParams()` is for App Router (replaces `getStaticPaths`)
- Returns an array of parameter objects
- Pre-generates pages at build time for better performance
- Use `dynamicParams` to control fallback behavior
- Can be combined with ISR for incremental updates
- Receives parent params for nested routes
- Best for blogs, documentation, e-commerce, content sites

**Decision Flow:**
```
Do you have dynamic routes?
│
├─ Yes
│  │
│  ├─ Can you predict the parameters?
│  │  │
│  │  ├─ Yes → Use generateStaticParams
│  │  │
│  │  └─ No → Use dynamicParams = true (on-demand generation)
│  │
│  └─ Should all pages be pre-generated?
│     │
│     ├─ Yes → Set dynamicParams = false
│     │
│     └─ No → Keep dynamicParams = true (default)
│
└─ No → No need for generateStaticParams
```

---

### Q1.10: What is NextResponse and How to Use It?

**Answer:**

`NextResponse` is a utility class in Next.js that extends the native Web Response API with convenient methods for working with responses in Route Handlers (API routes) and Middleware.

---

#### What is NextResponse?

**Purpose:**
- Create HTTP responses in Route Handlers
- Manipulate responses in Middleware
- Set cookies, headers, and redirects
- Return JSON, text, or stream responses
- Type-safe response creation

**Import:**
```typescript
import { NextResponse } from 'next/server';
```

---

#### Basic Usage in Route Handlers

**1. JSON Response:**

```typescript
// app/api/users/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
  const users = await fetchUsers();
  
  return NextResponse.json(users);
}
```

**With status code:**

```typescript
export async function POST(request: Request) {
  const data = await request.json();
  const user = await createUser(data);
  
  return NextResponse.json(user, { status: 201 });
}
```

**2. Error Responses:**

```typescript
export async function GET(request: Request) {
  try {
    const data = await fetchData();
    return NextResponse.json(data);
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to fetch data' },
      { status: 500 }
    );
  }
}
```

**3. Redirect:**

```typescript
export async function GET(request: Request) {
  // Check authentication
  const isAuthenticated = await checkAuth(request);
  
  if (!isAuthenticated) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  return NextResponse.json({ message: 'Authenticated' });
}
```

---

#### NextResponse Methods

**1. NextResponse.json()**

Create a JSON response:

```typescript
// Basic JSON response
NextResponse.json({ message: 'Success' });

// With status code
NextResponse.json({ error: 'Not found' }, { status: 404 });

// With custom headers
NextResponse.json(
  { data: 'value' },
  {
    status: 200,
    headers: {
      'Content-Type': 'application/json',
      'X-Custom-Header': 'custom-value',
    },
  }
);
```

**2. NextResponse.redirect()**

Redirect to another URL:

```typescript
import { NextResponse } from 'next/server';

// Temporary redirect (307)
NextResponse.redirect(new URL('/new-location', request.url));

// Permanent redirect (308)
NextResponse.redirect(new URL('/new-location', request.url), {
  status: 308,
});

// External redirect
NextResponse.redirect('https://external-site.com');
```

**3. NextResponse.rewrite()**

Rewrite the URL (change destination without changing visible URL):

```typescript
import { NextResponse } from 'next/server';

// Rewrite to different path
NextResponse.rewrite(new URL('/internal-path', request.url));

// Rewrite to different domain
NextResponse.rewrite('https://api.example.com/data');
```

**4. NextResponse.next()**

Continue to next middleware or route:

```typescript
// In middleware
export function middleware(request: Request) {
  const response = NextResponse.next();
  
  // Add headers but continue
  response.headers.set('X-Custom-Header', 'value');
  
  return response;
}
```

---

#### Working with Headers

**Setting Headers:**

```typescript
export async function GET() {
  const data = await fetchData();
  
  return NextResponse.json(data, {
    headers: {
      'Cache-Control': 'public, s-maxage=60, stale-while-revalidate=30',
      'X-Custom-Header': 'custom-value',
      'Access-Control-Allow-Origin': '*',
    },
  });
}
```

**Modifying Response Headers:**

```typescript
export async function GET() {
  const response = NextResponse.json({ data: 'value' });
  
  response.headers.set('X-Custom-Header', 'custom');
  response.headers.append('Set-Cookie', 'token=abc');
  response.headers.delete('X-Powered-By');
  
  return response;
}
```

---

#### Working with Cookies

**Setting Cookies:**

```typescript
import { NextResponse } from 'next/server';

export async function POST(request: Request) {
  const response = NextResponse.json({ message: 'Success' });
  
  // Simple cookie
  response.cookies.set('token', 'abc123');
  
  // Cookie with options
  response.cookies.set('session', 'xyz789', {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'lax',
    maxAge: 60 * 60 * 24 * 7, // 1 week
    path: '/',
  });
  
  return response;
}
```

**Reading Cookies:**

```typescript
export async function GET(request: Request) {
  const token = request.cookies.get('token');
  
  if (!token) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }
  
  return NextResponse.json({ token: token.value });
}
```

**Deleting Cookies:**

```typescript
export async function POST() {
  const response = NextResponse.json({ message: 'Logged out' });
  
  // Delete cookie
  response.cookies.delete('token');
  
  // Or set with maxAge: 0
  response.cookies.set('token', '', { maxAge: 0 });
  
  return response;
}
```

---

#### Real-World Example 1: Authentication API

```typescript
// app/api/auth/login/route.ts
import { NextResponse } from 'next/server';
import { signJWT } from '@/lib/jwt';
import { verifyPassword } from '@/lib/auth';

export async function POST(request: Request) {
  try {
    const { email, password } = await request.json();
    
    // Validate input
    if (!email || !password) {
      return NextResponse.json(
        { error: 'Email and password are required' },
        { status: 400 }
      );
    }
    
    // Verify credentials
    const user = await verifyPassword(email, password);
    
    if (!user) {
      return NextResponse.json(
        { error: 'Invalid credentials' },
        { status: 401 }
      );
    }
    
    // Create JWT token
    const token = await signJWT({ userId: user.id });
    
    // Create response
    const response = NextResponse.json(
      {
        message: 'Login successful',
        user: {
          id: user.id,
          email: user.email,
          name: user.name,
        },
      },
      { status: 200 }
    );
    
    // Set secure HTTP-only cookie
    response.cookies.set('auth-token', token, {
      httpOnly: true,
      secure: process.env.NODE_ENV === 'production',
      sameSite: 'strict',
      maxAge: 60 * 60 * 24 * 7, // 1 week
      path: '/',
    });
    
    return response;
  } catch (error) {
    console.error('Login error:', error);
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}

// app/api/auth/logout/route.ts
export async function POST() {
  const response = NextResponse.json({ message: 'Logout successful' });
  
  // Clear auth cookie
  response.cookies.delete('auth-token');
  
  return response;
}
```

---

#### Real-World Example 2: CORS-Enabled API

```typescript
// app/api/public/data/route.ts
import { NextResponse } from 'next/server';

// Handle preflight requests
export async function OPTIONS() {
  return NextResponse.json({}, {
    status: 200,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
      'Access-Control-Allow-Headers': 'Content-Type, Authorization',
      'Access-Control-Max-Age': '86400',
    },
  });
}

export async function GET(request: Request) {
  const data = await fetchPublicData();
  
  return NextResponse.json(data, {
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE',
      'Cache-Control': 'public, s-maxage=60',
    },
  });
}

export async function POST(request: Request) {
  try {
    const body = await request.json();
    const result = await processData(body);
    
    return NextResponse.json(result, {
      status: 201,
      headers: {
        'Access-Control-Allow-Origin': '*',
      },
    });
  } catch (error) {
    return NextResponse.json(
      { error: 'Invalid request' },
      {
        status: 400,
        headers: {
          'Access-Control-Allow-Origin': '*',
        },
      }
    );
  }
}
```

---

#### Real-World Example 3: Rate-Limited API

```typescript
// app/api/limited/route.ts
import { NextResponse } from 'next/server';
import { rateLimit } from '@/lib/rate-limit';

const limiter = rateLimit({
  interval: 60 * 1000, // 1 minute
  uniqueTokenPerInterval: 500,
});

export async function GET(request: Request) {
  const ip = request.headers.get('x-forwarded-for') || 'unknown';
  
  try {
    await limiter.check(ip, 10); // 10 requests per minute
  } catch {
    return NextResponse.json(
      { error: 'Rate limit exceeded' },
      {
        status: 429,
        headers: {
          'X-RateLimit-Limit': '10',
          'X-RateLimit-Remaining': '0',
          'Retry-After': '60',
        },
      }
    );
  }
  
  const data = await fetchData();
  
  return NextResponse.json(data, {
    headers: {
      'X-RateLimit-Limit': '10',
      'X-RateLimit-Remaining': '9',
    },
  });
}
```

---

#### Using NextResponse in Middleware

**Basic Middleware:**

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  // Log request
  console.log('Request:', request.method, request.url);
  
  // Continue to next middleware/route
  return NextResponse.next();
}

export const config = {
  matcher: '/api/:path*',
};
```

**Authentication Middleware:**

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { verifyToken } from '@/lib/jwt';

export async function middleware(request: NextRequest) {
  const token = request.cookies.get('auth-token');
  
  // Protected routes
  if (request.nextUrl.pathname.startsWith('/dashboard')) {
    if (!token) {
      return NextResponse.redirect(new URL('/login', request.url));
    }
    
    try {
      await verifyToken(token.value);
      return NextResponse.next();
    } catch (error) {
      return NextResponse.redirect(new URL('/login', request.url));
    }
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/profile/:path*'],
};
```

**Adding Headers in Middleware:**

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const response = NextResponse.next();
  
  // Add security headers
  response.headers.set('X-Frame-Options', 'DENY');
  response.headers.set('X-Content-Type-Options', 'nosniff');
  response.headers.set('Referrer-Policy', 'strict-origin-when-cross-origin');
  response.headers.set(
    'Content-Security-Policy',
    "default-src 'self'; script-src 'self' 'unsafe-inline'"
  );
  
  return response;
}
```

**Geolocation Redirect:**

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const country = request.geo?.country || 'US';
  const url = request.nextUrl;
  
  // Redirect based on country
  if (url.pathname === '/') {
    if (country === 'GB') {
      return NextResponse.redirect(new URL('/en-gb', request.url));
    } else if (country === 'FR') {
      return NextResponse.redirect(new URL('/fr', request.url));
    }
  }
  
  return NextResponse.next();
}
```

**Rewrite Based on Host:**

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const hostname = request.headers.get('host') || '';
  
  // Rewrite for subdomain
  if (hostname.startsWith('blog.')) {
    return NextResponse.rewrite(new URL('/blog', request.url));
  }
  
  if (hostname.startsWith('docs.')) {
    return NextResponse.rewrite(new URL('/documentation', request.url));
  }
  
  return NextResponse.next();
}
```

---

#### Response Types

**1. JSON Response:**

```typescript
NextResponse.json({ key: 'value' });
```

**2. Text Response:**

```typescript
new NextResponse('Plain text response', {
  headers: { 'Content-Type': 'text/plain' },
});
```

**3. HTML Response:**

```typescript
new NextResponse('<html><body>Hello</body></html>', {
  headers: { 'Content-Type': 'text/html' },
});
```

**4. Stream Response:**

```typescript
const stream = new ReadableStream({
  start(controller) {
    controller.enqueue('chunk 1');
    controller.enqueue('chunk 2');
    controller.close();
  },
});

new NextResponse(stream);
```

**5. File Download:**

```typescript
export async function GET() {
  const file = await readFile('path/to/file.pdf');
  
  return new NextResponse(file, {
    headers: {
      'Content-Type': 'application/pdf',
      'Content-Disposition': 'attachment; filename="download.pdf"',
    },
  });
}
```

---

#### Advanced Patterns

**1. Conditional Responses:**

```typescript
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const format = searchParams.get('format');
  
  const data = await fetchData();
  
  if (format === 'xml') {
    return new NextResponse(convertToXML(data), {
      headers: { 'Content-Type': 'application/xml' },
    });
  }
  
  if (format === 'csv') {
    return new NextResponse(convertToCSV(data), {
      headers: {
        'Content-Type': 'text/csv',
        'Content-Disposition': 'attachment; filename="data.csv"',
      },
    });
  }
  
  return NextResponse.json(data);
}
```

**2. Response Caching:**

```typescript
export async function GET() {
  const data = await fetchData();
  
  return NextResponse.json(data, {
    headers: {
      // Cache for 1 hour
      'Cache-Control': 'public, s-maxage=3600, stale-while-revalidate=60',
      // Add ETag for conditional requests
      'ETag': generateETag(data),
    },
  });
}
```

**3. Pagination with Headers:**

```typescript
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const page = parseInt(searchParams.get('page') || '1');
  const limit = parseInt(searchParams.get('limit') || '10');
  
  const { data, total } = await fetchPaginated(page, limit);
  
  return NextResponse.json(data, {
    headers: {
      'X-Total-Count': total.toString(),
      'X-Page': page.toString(),
      'X-Per-Page': limit.toString(),
      'X-Total-Pages': Math.ceil(total / limit).toString(),
      'Link': generateLinkHeader(request.url, page, limit, total),
    },
  });
}
```

**4. API Versioning:**

```typescript
export async function GET(request: Request) {
  const version = request.headers.get('api-version') || '1';
  
  if (version === '2') {
    const data = await fetchDataV2();
    return NextResponse.json(data, {
      headers: { 'API-Version': '2' },
    });
  }
  
  const data = await fetchDataV1();
  return NextResponse.json(data, {
    headers: { 'API-Version': '1' },
  });
}
```

---

#### Error Handling

**Structured Error Responses:**

```typescript
class APIError extends Error {
  constructor(
    public statusCode: number,
    message: string,
    public code?: string
  ) {
    super(message);
  }
}

export async function GET(request: Request) {
  try {
    const data = await fetchData();
    return NextResponse.json(data);
  } catch (error) {
    if (error instanceof APIError) {
      return NextResponse.json(
        {
          error: {
            message: error.message,
            code: error.code,
            timestamp: new Date().toISOString(),
          },
        },
        { status: error.statusCode }
      );
    }
    
    return NextResponse.json(
      {
        error: {
          message: 'Internal server error',
          code: 'INTERNAL_ERROR',
        },
      },
      { status: 500 }
    );
  }
}
```

---

#### Type Safety with TypeScript

```typescript
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

interface User {
  id: string;
  name: string;
  email: string;
}

interface ApiResponse<T> {
  data?: T;
  error?: string;
  message?: string;
}

export async function GET(
  request: NextRequest
): Promise<NextResponse<ApiResponse<User[]>>> {
  try {
    const users = await fetchUsers();
    
    return NextResponse.json({ data: users });
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to fetch users' },
      { status: 500 }
    );
  }
}
```

---

#### Best Practices

**1. Always Set Appropriate Status Codes:**

```typescript
// ✅ Good: Correct status codes
return NextResponse.json(user, { status: 201 }); // Created
return NextResponse.json({ error: 'Not found' }, { status: 404 });
return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });

// ❌ Bad: Always using 200
return NextResponse.json({ error: 'Not found' }); // Still 200!
```

**2. Use Type-Safe Responses:**

```typescript
// ✅ Good: Type-safe response
interface ApiResponse {
  success: boolean;
  data?: any;
  error?: string;
}

export async function GET(): Promise<NextResponse<ApiResponse>> {
  return NextResponse.json({ success: true, data: {} });
}

// ❌ Bad: No types
export async function GET() {
  return NextResponse.json({ whatever: 'data' });
}
```

**3. Set Security Headers:**

```typescript
// ✅ Good: Security headers
return NextResponse.json(data, {
  headers: {
    'X-Content-Type-Options': 'nosniff',
    'X-Frame-Options': 'DENY',
    'Content-Security-Policy': "default-src 'self'",
  },
});
```

**4. Handle CORS Properly:**

```typescript
// ✅ Good: Explicit CORS configuration
const allowedOrigins = ['https://example.com'];

export async function GET(request: Request) {
  const origin = request.headers.get('origin');
  
  const headers: HeadersInit = {};
  
  if (origin && allowedOrigins.includes(origin)) {
    headers['Access-Control-Allow-Origin'] = origin;
    headers['Access-Control-Allow-Credentials'] = 'true';
  }
  
  return NextResponse.json(data, { headers });
}

// ❌ Bad: Allow all origins in production
headers['Access-Control-Allow-Origin'] = '*';
```

**5. Use Proper Cookie Options:**

```typescript
// ✅ Good: Secure cookies
response.cookies.set('token', value, {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production',
  sameSite: 'strict',
  maxAge: 60 * 60 * 24,
  path: '/',
});

// ❌ Bad: Insecure cookies
response.cookies.set('token', value); // No security options
```

---

#### Common Patterns

**API Response Wrapper:**

```typescript
// lib/api-response.ts
export function successResponse<T>(data: T, status = 200) {
  return NextResponse.json(
    {
      success: true,
      data,
      timestamp: new Date().toISOString(),
    },
    { status }
  );
}

export function errorResponse(
  message: string,
  status = 500,
  code?: string
) {
  return NextResponse.json(
    {
      success: false,
      error: { message, code },
      timestamp: new Date().toISOString(),
    },
    { status }
  );
}

// Usage
export async function GET() {
  try {
    const data = await fetchData();
    return successResponse(data);
  } catch (error) {
    return errorResponse('Failed to fetch', 500, 'FETCH_ERROR');
  }
}
```

---

#### Troubleshooting

**1. Headers not showing up:**

```typescript
// Problem: Setting headers after creating response
const response = NextResponse.json(data);
response.headers.set('X-Custom', 'value'); // ✅ This works

// But this doesn't work:
return NextResponse.json(data).headers.set('X-Custom', 'value'); // ❌
```

**2. Cookies not being set:**

```typescript
// Problem: Wrong cookie options
response.cookies.set('token', 'value', {
  httpOnly: true,
  secure: true, // ❌ Fails on localhost
});

// Solution: Conditional secure
response.cookies.set('token', 'value', {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production', // ✅
});
```

**3. CORS errors:**

```typescript
// Problem: Missing preflight handler
export async function GET() {
  return NextResponse.json(data, {
    headers: { 'Access-Control-Allow-Origin': '*' },
  });
}

// Solution: Add OPTIONS handler
export async function OPTIONS() {
  return NextResponse.json({}, {
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, OPTIONS',
      'Access-Control-Allow-Headers': 'Content-Type',
    },
  });
}
```

---

#### Summary

**Key Points:**
- `NextResponse` extends native Web Response API
- Use `.json()` for JSON responses with status codes
- Use `.redirect()` for redirects, `.rewrite()` for rewrites
- Set cookies with `.cookies.set()` with security options
- Add headers for CORS, caching, security
- Use in Route Handlers and Middleware
- Type-safe with TypeScript
- Always set appropriate HTTP status codes

**Common Use Cases:**
- API endpoints returning JSON
- Authentication with cookies
- CORS-enabled public APIs
- Rate-limited APIs
- File downloads
- Redirects and rewrites in middleware
- Security headers in middleware

---

### Q1.11: What is NextRequest and How to Use It?

**Answer:**

`NextRequest` is a utility class in Next.js that extends the native Web Request API with additional convenient methods and properties for accessing request data in Route Handlers and Middleware.

---

#### What is NextRequest?

**Purpose:**
- Access request headers, cookies, and URL information
- Get geolocation data
- Access client IP address
- Parse URL and search parameters
- Type-safe request handling
- Enhanced request methods for middleware

**Import:**
```typescript
import { NextRequest } from 'next/server';
```

---

#### Basic Usage in Route Handlers

**1. Accessing Request Data:**

```typescript
// app/api/example/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  // URL information
  const url = request.nextUrl;
  const pathname = url.pathname;
  const searchParams = url.searchParams;
  
  // Headers
  const userAgent = request.headers.get('user-agent');
  const authorization = request.headers.get('authorization');
  
  // Cookies
  const token = request.cookies.get('auth-token');
  
  return NextResponse.json({
    pathname,
    query: Object.fromEntries(searchParams),
    userAgent,
    hasToken: !!token,
  });
}
```

**2. Reading Request Body:**

```typescript
export async function POST(request: NextRequest) {
  // JSON body
  const body = await request.json();
  
  // Form data
  // const formData = await request.formData();
  
  // Plain text
  // const text = await request.text();
  
  return NextResponse.json({ 
    received: body,
    timestamp: Date.now()
  });
}
```

**3. Search Parameters:**

```typescript
// GET /api/search?q=nextjs&page=2&limit=10
export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;
  
  const query = searchParams.get('q');          // 'nextjs'
  const page = searchParams.get('page') || '1';  // '2'
  const limit = searchParams.get('limit') || '10'; // '10'
  
  const results = await searchDatabase({
    query,
    page: parseInt(page),
    limit: parseInt(limit)
  });
  
  return NextResponse.json(results);
}
```

---

#### NextRequest Properties

**1. nextUrl (Enhanced URL)**

```typescript
export async function GET(request: NextRequest) {
  const { nextUrl } = request;
  
  // URL parts
  console.log(nextUrl.href);      // Full URL: https://example.com/api/users?page=2
  console.log(nextUrl.origin);    // https://example.com
  console.log(nextUrl.pathname);  // /api/users
  console.log(nextUrl.search);    // ?page=2
  console.log(nextUrl.host);      // example.com
  console.log(nextUrl.protocol);  // https:
  
  // Search params
  const page = nextUrl.searchParams.get('page');
  
  return NextResponse.json({ page });
}
```

**2. geo (Geolocation Data)**

```typescript
export async function GET(request: NextRequest) {
  const geo = request.geo;
  
  return NextResponse.json({
    country: geo?.country,    // 'US'
    region: geo?.region,      // 'CA'
    city: geo?.city,          // 'San Francisco'
    latitude: geo?.latitude,  // 37.7749
    longitude: geo?.longitude // -122.4194
  });
}
```

**3. ip (Client IP Address)**

```typescript
export async function GET(request: NextRequest) {
  const ip = request.ip || 'unknown';
  
  // Check rate limiting by IP
  const isRateLimited = await checkRateLimit(ip);
  
  if (isRateLimited) {
    return NextResponse.json(
      { error: 'Rate limit exceeded' },
      { status: 429 }
    );
  }
  
  return NextResponse.json({ ip });
}
```

**4. cookies (Cookie Management)**

```typescript
export async function GET(request: NextRequest) {
  // Get single cookie
  const token = request.cookies.get('auth-token');
  console.log(token?.value);
  
  // Get all cookies
  const allCookies = request.cookies.getAll();
  console.log(allCookies); // [{ name: 'token', value: '...' }, ...]
  
  // Check if cookie exists
  const hasToken = request.cookies.has('auth-token');
  
  return NextResponse.json({ hasToken });
}
```

**5. headers (Request Headers)**

```typescript
export async function GET(request: NextRequest) {
  // Get single header
  const contentType = request.headers.get('content-type');
  const authorization = request.headers.get('authorization');
  const userAgent = request.headers.get('user-agent');
  const referer = request.headers.get('referer');
  
  // Custom headers
  const apiKey = request.headers.get('x-api-key');
  const clientVersion = request.headers.get('x-client-version');
  
  return NextResponse.json({
    contentType,
    hasAuth: !!authorization,
    userAgent,
  });
}
```

---

#### Real-World Example 1: Authentication Middleware

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';
import { verifyJWT } from '@/lib/jwt';

export async function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;
  
  // Skip auth for public routes
  const publicRoutes = ['/login', '/signup', '/'];
  if (publicRoutes.includes(pathname)) {
    return NextResponse.next();
  }
  
  // Check auth token
  const token = request.cookies.get('auth-token');
  
  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  try {
    // Verify token
    const payload = await verifyJWT(token.value);
    
    // Add user info to headers (accessible in route handlers)
    const response = NextResponse.next();
    response.headers.set('x-user-id', payload.userId);
    response.headers.set('x-user-role', payload.role);
    
    return response;
  } catch (error) {
    // Invalid token - redirect to login
    const response = NextResponse.redirect(new URL('/login', request.url));
    response.cookies.delete('auth-token');
    return response;
  }
}

export const config = {
  matcher: [
    '/dashboard/:path*',
    '/profile/:path*',
    '/api/protected/:path*'
  ]
};
```

---

#### Real-World Example 2: API Route with Full Request Handling

```typescript
// app/api/products/search/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { searchProducts } from '@/lib/database';

export async function GET(request: NextRequest) {
  try {
    // 1. Extract search parameters
    const searchParams = request.nextUrl.searchParams;
    const query = searchParams.get('q');
    const category = searchParams.get('category');
    const minPrice = searchParams.get('minPrice');
    const maxPrice = searchParams.get('maxPrice');
    const page = parseInt(searchParams.get('page') || '1');
    const limit = parseInt(searchParams.get('limit') || '20');
    
    // 2. Validate required parameters
    if (!query || query.trim().length === 0) {
      return NextResponse.json(
        { error: 'Search query is required' },
        { status: 400 }
      );
    }
    
    // 3. Check authentication (optional search enhancement)
    const token = request.cookies.get('auth-token');
    const userId = token ? await getUserIdFromToken(token.value) : null;
    
    // 4. Get user preferences (for personalized results)
    const userAgent = request.headers.get('user-agent');
    const isMobile = /mobile/i.test(userAgent || '');
    
    // 5. Build search filters
    const filters = {
      query: query.trim(),
      category: category || undefined,
      priceRange: minPrice && maxPrice ? {
        min: parseFloat(minPrice),
        max: parseFloat(maxPrice)
      } : undefined,
      userId,
      isMobile
    };
    
    // 6. Perform search
    const { products, total } = await searchProducts(filters, {
      page,
      limit
    });
    
    // 7. Return results with pagination info
    return NextResponse.json({
      products,
      pagination: {
        page,
        limit,
        total,
        totalPages: Math.ceil(total / limit),
        hasMore: page * limit < total
      },
      query: filters.query
    });
    
  } catch (error) {
    console.error('Search error:', error);
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}

// Helper function
async function getUserIdFromToken(token: string): Promise<string | null> {
  try {
    const payload = await verifyJWT(token);
    return payload.userId;
  } catch {
    return null;
  }
}
```

---

#### Real-World Example 3: Geolocation-Based Content

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;
  const geo = request.geo;
  
  // Redirect based on country
  if (pathname === '/') {
    const country = geo?.country || 'US';
    
    const countryRoutes: Record<string, string> = {
      'US': '/en-us',
      'GB': '/en-gb',
      'FR': '/fr',
      'DE': '/de',
      'JP': '/ja',
      'CN': '/zh',
    };
    
    const localePath = countryRoutes[country] || '/en-us';
    
    // Only redirect if not already on localized path
    if (!pathname.startsWith(localePath)) {
      return NextResponse.redirect(new URL(localePath, request.url));
    }
  }
  
  // Add geo info to response headers
  const response = NextResponse.next();
  response.headers.set('x-country', geo?.country || 'unknown');
  response.headers.set('x-city', geo?.city || 'unknown');
  
  return response;
}

export const config = {
  matcher: '/'
};
```

---

#### Using NextRequest in Middleware

**Basic Middleware:**

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const { pathname, searchParams } = request.nextUrl;
  
  console.log(`[${request.method}] ${pathname}`);
  console.log('Query:', Object.fromEntries(searchParams));
  console.log('IP:', request.ip);
  console.log('User-Agent:', request.headers.get('user-agent'));
  
  return NextResponse.next();
}
```

**URL Rewriting:**

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const url = request.nextUrl.clone();
  const hostname = request.headers.get('host') || '';
  
  // Subdomain routing: blog.example.com → example.com/blog
  if (hostname.startsWith('blog.')) {
    url.pathname = `/blog${url.pathname}`;
    return NextResponse.rewrite(url);
  }
  
  // API versioning: /api/v1/users → /api/users?version=1
  if (url.pathname.startsWith('/api/v1/')) {
    url.pathname = url.pathname.replace('/api/v1/', '/api/');
    url.searchParams.set('version', '1');
    return NextResponse.rewrite(url);
  }
  
  return NextResponse.next();
}
```

**A/B Testing:**

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;
  
  // Only A/B test on home page
  if (pathname !== '/') {
    return NextResponse.next();
  }
  
  // Check if user already has variant cookie
  let variant = request.cookies.get('ab-test-variant')?.value;
  
  if (!variant) {
    // Assign random variant (50/50 split)
    variant = Math.random() < 0.5 ? 'A' : 'B';
  }
  
  // Rewrite to variant page
  const url = request.nextUrl.clone();
  url.pathname = `/variant-${variant}`;
  
  const response = NextResponse.rewrite(url);
  
  // Set cookie for 30 days
  response.cookies.set('ab-test-variant', variant, {
    maxAge: 60 * 60 * 24 * 30,
    path: '/',
  });
  
  return response;
}
```

**Rate Limiting:**

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';
import { RateLimiter } from '@/lib/rate-limiter';

const limiter = new RateLimiter({
  interval: 60 * 1000, // 1 minute
  limit: 100, // 100 requests per minute
});

export async function middleware(request: NextRequest) {
  const ip = request.ip || 'unknown';
  const { pathname } = request.nextUrl;
  
  // Apply rate limiting only to API routes
  if (pathname.startsWith('/api/')) {
    const { success, limit, remaining, reset } = await limiter.check(ip);
    
    if (!success) {
      return NextResponse.json(
        { error: 'Too many requests' },
        {
          status: 429,
          headers: {
            'X-RateLimit-Limit': limit.toString(),
            'X-RateLimit-Remaining': '0',
            'X-RateLimit-Reset': reset.toString(),
            'Retry-After': Math.ceil((reset - Date.now()) / 1000).toString(),
          }
        }
      );
    }
    
    // Add rate limit headers to successful responses
    const response = NextResponse.next();
    response.headers.set('X-RateLimit-Limit', limit.toString());
    response.headers.set('X-RateLimit-Remaining', remaining.toString());
    response.headers.set('X-RateLimit-Reset', reset.toString());
    
    return response;
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: '/api/:path*'
};
```

---

#### Advanced URL Manipulation

**Modifying Search Parameters:**

```typescript
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const url = request.nextUrl.clone();
  
  // Add default parameters
  if (!url.searchParams.has('version')) {
    url.searchParams.set('version', '1.0');
  }
  
  // Remove sensitive parameters from URL
  url.searchParams.delete('api_key');
  url.searchParams.delete('token');
  
  // Redirect to cleaned URL
  if (url.search !== request.nextUrl.search) {
    return NextResponse.redirect(url);
  }
  
  return NextResponse.next();
}
```

**Dynamic Routing:**

```typescript
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const url = request.nextUrl.clone();
  const pathname = url.pathname;
  
  // Short URL redirects: /p/abc123 → /products/abc123
  if (pathname.startsWith('/p/')) {
    const productId = pathname.replace('/p/', '');
    return NextResponse.redirect(new URL(`/products/${productId}`, request.url));
  }
  
  // Legacy URL redirects: /old-blog/... → /blog/...
  if (pathname.startsWith('/old-blog/')) {
    url.pathname = pathname.replace('/old-blog/', '/blog/');
    return NextResponse.redirect(url, { status: 301 }); // Permanent
  }
  
  return NextResponse.next();
}
```

---

#### Type Safety with TypeScript

**Typed Request Handler:**

```typescript
import { NextRequest, NextResponse } from 'next/server';

interface SearchParams {
  q: string;
  page?: string;
  limit?: string;
  category?: string;
}

interface ProductSearchResult {
  products: Product[];
  total: number;
  page: number;
}

export async function GET(
  request: NextRequest
): Promise<NextResponse<ProductSearchResult | { error: string }>> {
  const searchParams = request.nextUrl.searchParams;
  
  const params: SearchParams = {
    q: searchParams.get('q') || '',
    page: searchParams.get('page') || undefined,
    limit: searchParams.get('limit') || undefined,
    category: searchParams.get('category') || undefined,
  };
  
  if (!params.q) {
    return NextResponse.json(
      { error: 'Query parameter required' },
      { status: 400 }
    );
  }
  
  const results = await searchProducts(params);
  return NextResponse.json(results);
}
```

**Custom Request Extensions:**

```typescript
import { NextRequest } from 'next/server';

// Extend NextRequest with custom properties
interface AuthenticatedRequest extends NextRequest {
  user?: {
    id: string;
    email: string;
    role: string;
  };
}

// Middleware that adds user to request
export async function authenticateRequest(
  request: NextRequest
): Promise<AuthenticatedRequest> {
  const token = request.cookies.get('auth-token');
  
  if (token) {
    const user = await verifyToken(token.value);
    return Object.assign(request, { user }) as AuthenticatedRequest;
  }
  
  return request as AuthenticatedRequest;
}

// Usage in route handler
export async function GET(request: NextRequest) {
  const authRequest = await authenticateRequest(request);
  
  if (!authRequest.user) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }
  
  return NextResponse.json({ user: authRequest.user });
}
```

---

#### Request Method Checking

```typescript
export async function handler(request: NextRequest) {
  const { method } = request;
  
  switch (method) {
    case 'GET':
      return handleGet(request);
    case 'POST':
      return handlePost(request);
    case 'PUT':
      return handlePut(request);
    case 'DELETE':
      return handleDelete(request);
    case 'PATCH':
      return handlePatch(request);
    default:
      return NextResponse.json(
        { error: `Method ${method} not allowed` },
        { status: 405 }
      );
  }
}
```

---

#### Reading Different Content Types

**1. JSON:**

```typescript
export async function POST(request: NextRequest) {
  const data = await request.json();
  return NextResponse.json({ received: data });
}
```

**2. Form Data:**

```typescript
export async function POST(request: NextRequest) {
  const formData = await request.formData();
  
  const name = formData.get('name') as string;
  const email = formData.get('email') as string;
  const file = formData.get('file') as File;
  
  // Process file
  const buffer = await file.arrayBuffer();
  
  return NextResponse.json({ 
    name, 
    email, 
    fileSize: buffer.byteLength 
  });
}
```

**3. Plain Text:**

```typescript
export async function POST(request: NextRequest) {
  const text = await request.text();
  return NextResponse.json({ received: text });
}
```

**4. Array Buffer:**

```typescript
export async function POST(request: NextRequest) {
  const buffer = await request.arrayBuffer();
  const bytes = new Uint8Array(buffer);
  
  return NextResponse.json({ 
    size: bytes.length,
    type: request.headers.get('content-type')
  });
}
```

**5. Blob:**

```typescript
export async function POST(request: NextRequest) {
  const blob = await request.blob();
  
  return NextResponse.json({ 
    size: blob.size,
    type: blob.type 
  });
}
```

---

#### Best Practices

**1. Always Validate Input:**

```typescript
// ✅ Good: Validate and sanitize
export async function POST(request: NextRequest) {
  const body = await request.json();
  
  if (!body.email || !isValidEmail(body.email)) {
    return NextResponse.json(
      { error: 'Valid email required' },
      { status: 400 }
    );
  }
  
  const sanitizedEmail = body.email.toLowerCase().trim();
  // Process sanitized data...
}

// ❌ Bad: Trust user input
export async function POST(request: NextRequest) {
  const body = await request.json();
  await db.user.create({ email: body.email }); // Dangerous!
}
```

**2. Check Request Method:**

```typescript
// ✅ Good: Explicit method handling
export async function POST(request: NextRequest) {
  if (request.method !== 'POST') {
    return NextResponse.json(
      { error: 'Method not allowed' },
      { status: 405 }
    );
  }
  // Handle POST...
}

// Or use separate exports (App Router style)
export async function GET(request: NextRequest) { /* ... */ }
export async function POST(request: NextRequest) { /* ... */ }
```

**3. Handle Errors Gracefully:**

```typescript
// ✅ Good: Comprehensive error handling
export async function POST(request: NextRequest) {
  try {
    const body = await request.json();
    // Process...
  } catch (error) {
    if (error instanceof SyntaxError) {
      return NextResponse.json(
        { error: 'Invalid JSON' },
        { status: 400 }
      );
    }
    
    console.error('Unexpected error:', error);
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

**4. Use Type Guards:**

```typescript
// ✅ Good: Type safety
interface CreateUserBody {
  email: string;
  name: string;
  age: number;
}

function isValidUserBody(body: unknown): body is CreateUserBody {
  return (
    typeof body === 'object' &&
    body !== null &&
    'email' in body &&
    'name' in body &&
    'age' in body &&
    typeof (body as CreateUserBody).email === 'string' &&
    typeof (body as CreateUserBody).name === 'string' &&
    typeof (body as CreateUserBody).age === 'number'
  );
}

export async function POST(request: NextRequest) {
  const body = await request.json();
  
  if (!isValidUserBody(body)) {
    return NextResponse.json(
      { error: 'Invalid request body' },
      { status: 400 }
    );
  }
  
  // TypeScript knows body is CreateUserBody here
  await createUser(body);
}
```

**5. Clone URL for Modifications:**

```typescript
// ✅ Good: Clone before modifying
export function middleware(request: NextRequest) {
  const url = request.nextUrl.clone();
  url.searchParams.set('timestamp', Date.now().toString());
  return NextResponse.rewrite(url);
}

// ❌ Bad: Modify original
export function middleware(request: NextRequest) {
  request.nextUrl.searchParams.set('timestamp', Date.now().toString());
  return NextResponse.rewrite(request.nextUrl); // May cause issues
}
```

---

#### Common Patterns

**1. Request Logger:**

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const start = Date.now();
  const { method, nextUrl } = request;
  
  // Log request
  console.log(`→ ${method} ${nextUrl.pathname}`, {
    query: Object.fromEntries(nextUrl.searchParams),
    ip: request.ip,
    userAgent: request.headers.get('user-agent')?.slice(0, 50)
  });
  
  const response = NextResponse.next();
  
  // Log response time
  console.log(`← ${method} ${nextUrl.pathname} (${Date.now() - start}ms)`);
  
  return response;
}
```

**2. Request ID Generator:**

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { nanoid } from 'nanoid';

export function middleware(request: NextRequest) {
  const requestId = nanoid();
  
  const response = NextResponse.next();
  response.headers.set('x-request-id', requestId);
  
  // Can be used for logging/tracing
  console.log(`[${requestId}] ${request.method} ${request.nextUrl.pathname}`);
  
  return response;
}
```

**3. CORS Handler:**

```typescript
function corsMiddleware(request: NextRequest) {
  const origin = request.headers.get('origin') || '';
  const allowedOrigins = [
    'https://example.com',
    'https://app.example.com'
  ];
  
  const response = NextResponse.next();
  
  if (allowedOrigins.includes(origin)) {
    response.headers.set('Access-Control-Allow-Origin', origin);
    response.headers.set('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
    response.headers.set('Access-Control-Allow-Headers', 'Content-Type, Authorization');
    response.headers.set('Access-Control-Max-Age', '86400');
  }
  
  return response;
}
```

---

#### Troubleshooting

**1. Request body already consumed:**

```typescript
// Problem: Can't read body twice
export async function POST(request: NextRequest) {
  const body1 = await request.json(); // ✅ Works
  const body2 = await request.json(); // ❌ Error: body already consumed
}

// Solution: Store the result
export async function POST(request: NextRequest) {
  const body = await request.json();
  
  // Use body multiple times
  validateBody(body);
  processBody(body);
}
```

**2. Search params not updating:**

```typescript
// Problem: Modifying request.nextUrl directly
export function middleware(request: NextRequest) {
  request.nextUrl.searchParams.set('key', 'value');
  return NextResponse.rewrite(request.nextUrl); // May not work
}

// Solution: Clone the URL
export function middleware(request: NextRequest) {
  const url = request.nextUrl.clone();
  url.searchParams.set('key', 'value');
  return NextResponse.rewrite(url); // ✅ Works
}
```

**3. Geolocation data missing:**

```typescript
// Problem: Geo data only available on certain platforms
export function middleware(request: NextRequest) {
  const country = request.geo.country; // ❌ Might be undefined
}

// Solution: Check and provide fallback
export function middleware(request: NextRequest) {
  const country = request.geo?.country || 'US'; // ✅ Safe
  
  // Or check if running on compatible platform
  if (request.geo) {
    console.log('Geo data available:', request.geo);
  } else {
    console.log('Geo data not available (probably local dev)');
  }
}
```

**4. IP address is undefined:**

```typescript
// Problem: IP not available in development
export function middleware(request: NextRequest) {
  const ip = request.ip; // undefined in local dev
}

// Solution: Provide fallback
export function middleware(request: NextRequest) {
  const ip = 
    request.ip || 
    request.headers.get('x-forwarded-for') || 
    request.headers.get('x-real-ip') ||
    '127.0.0.1';
}
```

---

#### NextRequest vs Request

**Standard Request (Web API):**
```typescript
export async function GET(request: Request) {
  // Standard Web API only
  const url = new URL(request.url);
  const headers = request.headers;
  const method = request.method;
  
  // No NextRequest features
  // request.geo ❌
  // request.ip ❌
  // request.cookies ❌
  // request.nextUrl ❌
}
```

**NextRequest (Enhanced):**
```typescript
export async function GET(request: NextRequest) {
  // All standard features plus Next.js enhancements
  const { nextUrl, geo, ip, cookies } = request;
  
  // Enhanced URL handling
  const searchParams = nextUrl.searchParams;
  
  // Easy cookie access
  const token = cookies.get('auth-token');
  
  // Geolocation
  const country = geo?.country;
  
  // Client IP
  const clientIp = ip;
}
```

---

#### Summary

**Key Points:**
- `NextRequest` extends standard Web Request with Next.js features
- Provides easy access to cookies, headers, URL, and geolocation
- Essential for middleware and advanced route handlers
- Type-safe with TypeScript
- Use `request.nextUrl` for URL manipulation
- Use `request.cookies` for cookie access
- Use `request.geo` for geolocation (when available)
- Use `request.ip` for client IP address
- Always clone `nextUrl` before modifying

**Common Use Cases:**
- Authentication middleware
- Rate limiting by IP
- Geolocation-based redirects
- A/B testing
- Request logging
- URL rewriting/routing
- API parameter parsing
- CORS handling
- Security headers

**NextRequest Properties:**
```typescript
interface NextRequest {
  nextUrl: NextURL;           // Enhanced URL with searchParams
  geo?: {                     // Geolocation data (platform-dependent)
    country?: string;
    region?: string;
    city?: string;
    latitude?: string;
    longitude?: string;
  };
  ip?: string;                // Client IP address
  cookies: RequestCookies;    // Cookie management
  headers: Headers;           // Request headers
  method: string;             // HTTP method
  url: string;                // Full URL
  
  // Methods
  json(): Promise<any>;
  formData(): Promise<FormData>;
  text(): Promise<string>;
  arrayBuffer(): Promise<ArrayBuffer>;
  blob(): Promise<Blob>;
}
```

---

#### Common Import Alias Patterns

**1. Single Root Alias (`@/`):**

```javascript
// tsconfig.json or jsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"]
    }
  }
}

// Usage
import Header from '@/components/Header';
import { formatPrice } from '@/lib/utils';
import type { User } from '@/types/user';
```

**2. Multiple Named Aliases:**

```javascript
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@components/*": ["components/*"],
      "@lib/*": ["lib/*"],
      "@utils/*": ["lib/utils/*"],
      "@hooks/*": ["hooks/*"],
      "@styles/*": ["styles/*"],
      "@types/*": ["types/*"],
      "@config/*": ["config/*"],
      "@api/*": ["app/api/*"]
    }
  }
}

// Usage
import Header from '@components/Header';
import { db } from '@lib/database';
import { formatDate } from '@utils/date';
import { useAuth } from '@hooks/useAuth';
import type { User } from '@types/user';
```

**3. Tilde (`~`) Alias (Alternative):**

```javascript
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "~/*": ["./*"]
    }
  }
}

// Usage
import Header from '~/components/Header';
import { db } from '~/lib/database';
```

---

#### Real-World Project Structure with Aliases

```
my-next-app/
├── app/
│   ├── api/
│   ├── (auth)/
│   └── page.tsx
├── components/
│   ├── ui/
│   │   ├── Button.tsx
│   │   └── Input.tsx
│   ├── forms/
│   └── layout/
│       └── Header.tsx
├── lib/
│   ├── database.ts
│   ├── auth.ts
│   └── utils/
│       ├── date.ts
│       └── format.ts
├── hooks/
│   ├── useAuth.ts
│   └── useUser.ts
├── types/
│   ├── user.ts
│   └── product.ts
└── tsconfig.json
```

**Configuration:**

```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"],
      "@/components/*": ["components/*"],
      "@/lib/*": ["lib/*"],
      "@/hooks/*": ["hooks/*"],
      "@/types/*": ["types/*"],
      "@/app/*": ["app/*"]
    }
  }
}
```

**Usage Examples:**

```javascript
// app/dashboard/page.tsx
import { Button } from '@/components/ui/Button';
import { Header } from '@/components/layout/Header';
import { db } from '@/lib/database';
import { formatDate } from '@/lib/utils/date';
import { useAuth } from '@/hooks/useAuth';
import type { User } from '@/types/user';

export default async function DashboardPage() {
  const users = await db.user.findMany();
  
  return (
    <>
      <Header />
      <main>
        {users.map(user => (
          <div key={user.id}>
            <h2>{user.name}</h2>
            <p>Joined: {formatDate(user.createdAt)}</p>
          </div>
        ))}
      </main>
    </>
  );
}
```

---

#### Benefits of Import Aliases

**1. Cleaner Code:**

```javascript
// Before: Hard to read
import { Button } from '../../../components/ui/Button';
import { Input } from '../../../components/ui/Input';
import { db } from '../../../lib/database';

// After: Clear and concise
import { Button } from '@/components/ui/Button';
import { Input } from '@/components/ui/Input';
import { db } from '@/lib/database';
```

**2. Easier Refactoring:**

When you move a file, you don't need to update all relative imports:

```javascript
// With relative imports - breaks when file moves!
import { utils } from '../../lib/utils';

// With aliases - works regardless of file location
import { utils } from '@/lib/utils';
```

**3. Better IntelliSense:**

Modern IDEs provide better autocomplete with absolute imports:

```javascript
// Type '@/' and get suggestions for all directories
import { } from '@/
//               ↑ Shows: components/, lib/, hooks/, types/, etc.
```

**4. Consistent Imports:**

Everyone on the team uses the same import pattern:

```javascript
// ✅ Consistent
import { Header } from '@/components/Header';
import { Footer } from '@/components/Footer';

// ❌ Inconsistent
import { Header } from '../components/Header';
import { Footer } from '../../components/Footer';
```

---

#### Advanced Path Mapping

**Mapping Subdirectories:**

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/components/*": ["components/*"],
      "@/ui/*": ["components/ui/*"],        // Direct access to ui folder
      "@/forms/*": ["components/forms/*"],  // Direct access to forms
      "@/icons/*": ["components/icons/*"],
      "@/lib/*": ["lib/*"],
      "@/utils/*": ["lib/utils/*"],         // Shortcut to utils
      "@/db/*": ["lib/database/*"],
      "@/api/*": ["app/api/*"]
    }
  }
}
```

**Usage:**

```javascript
// Without shortcuts
import { Button } from '@/components/ui/Button';
import { formatDate } from '@/lib/utils/date';

// With shortcuts
import { Button } from '@/ui/Button';
import { formatDate } from '@/utils/date';
```

---

#### Troubleshooting

**1. Aliases Not Working:**

```bash
# Restart TypeScript server in VS Code
# Command Palette: TypeScript: Restart TS Server

# Or restart dev server
npm run dev
```

**2. Module Not Found Error:**

Ensure `baseUrl` is set:

```json
{
  "compilerOptions": {
    "baseUrl": ".",  // ⭐ Required!
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

**3. Import Not Resolving:**

Check your paths match your folder structure:

```json
// If your components are in 'src/components'
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],  // Note: src/*
      "@/components/*": ["src/components/*"]
    }
  }
}
```

---

#### Best Practices

**1. Use `@/` as Default:**

```javascript
// ✅ Standard Next.js convention
import { Component } from '@/components/Component';

// ❌ Avoid custom symbols without good reason
import { Component } from '$/components/Component';
```

**2. Keep It Simple:**

```json
// ✅ Simple and sufficient
{
  "paths": {
    "@/*": ["./*"]
  }
}

// ❌ Over-complicated
{
  "paths": {
    "@/*": ["./*"],
    "@comp/*": ["components/*"],
    "@c/*": ["components/*"],
    "@ui/*": ["components/ui/*"],
    "@u/*": ["components/ui/*"]
    // Too many aliases!
  }
}
```

**3. Document Your Aliases:**

```javascript
// README.md
// Import Aliases:
// @/components/* - React components
// @/lib/* - Utility functions and libraries
// @/types/* - TypeScript type definitions
// @/hooks/* - Custom React hooks
```

**4. Use Aliases Consistently:**

```javascript
// ✅ Pick one style
import { Button } from '@/components/ui/Button';
import { Input } from '@/components/ui/Input';

// ❌ Don't mix styles
import { Button } from '@/components/ui/Button';
import { Input } from '../components/ui/Input';
```

---

#### VS Code IntelliSense Setup

For better autocomplete, create a `jsconfig.json` even in TypeScript projects:

```json
// jsconfig.json (for VS Code)
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"]
    }
  },
  "exclude": ["node_modules", ".next"]
}
```

---

#### Example: Complete Next.js Project with Aliases

```javascript
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2017",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}

// app/page.tsx
import { Button } from '@/components/ui/Button';
import { Hero } from '@/components/sections/Hero';
import { db } from '@/lib/database';
import { formatPrice } from '@/lib/utils/format';
import type { Product } from '@/types/product';

export default async function HomePage() {
  const products: Product[] = await db.product.findMany();
  
  return (
    <>
      <Hero />
      <section>
        {products.map(product => (
          <div key={product.id}>
            <h3>{product.name}</h3>
            <p>{formatPrice(product.price)}</p>
            <Button>Buy Now</Button>
          </div>
        ))}
      </section>
    </>
  );
}

// components/ui/Button.tsx
import type { ButtonProps } from '@/types/components';
import styles from '@/styles/button.module.css';

export function Button({ children, ...props }: ButtonProps) {
  return (
    <button className={styles.button} {...props}>
      {children}
    </button>
  );
}
```

---

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

### Q4.1: What are the Different Forms of Pre-rendering in Next.js?

**Answer:**

Pre-rendering is the process of generating HTML for a page in advance, rather than having it all done by client-side JavaScript. Next.js offers multiple pre-rendering strategies, each suited for different use cases.

---

#### Overview of Pre-rendering Types

| Type | When HTML Generated | Data Freshness | Performance | Use Case |
|------|---------------------|----------------|-------------|----------|
| **Static Generation (SSG)** | Build time | Stale until rebuild | ⚡ Fastest | Blogs, marketing pages |
| **Incremental Static Regeneration (ISR)** | Build time + periodic | Stale until revalidation | ⚡ Very fast | E-commerce, news |
| **Server-Side Rendering (SSR)** | Per request | ✅ Always fresh | 🐌 Slower | User dashboards |
| **Client-Side Rendering (CSR)** | In browser | ✅ Real-time | 🐌 Slowest initial load | Private data, interactive |
| **Streaming SSR** | Per request (streaming) | ✅ Always fresh | ⚡ Fast perceived | Complex pages |

---

#### 1. Static Site Generation (SSG)

**What it is:**
HTML is generated at **build time** and reused for each request. The fastest form of pre-rendering.

**App Router (Default Behavior):**

```typescript
// app/blog/[slug]/page.tsx
// Static by default if using fetch with cache

export async function generateStaticParams() {
  // Generate all blog post paths at build time
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  return posts.map((post) => ({
    slug: post.slug,
  }));
}

export default async function BlogPost({ params }: { params: { slug: string } }) {
  // This fetch is cached by default (force-cache)
  const post = await fetch(`https://api.example.com/posts/${params.slug}`).then(r => r.json());
  
  return (
    <article>
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}
```

**Pages Router:**

```typescript
// pages/blog/[slug].tsx
export async function getStaticPaths() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  const paths = posts.map((post: any) => ({
    params: { slug: post.slug }
  }));
  
  return {
    paths,
    fallback: false // or true, 'blocking'
  };
}

export async function getStaticProps({ params }: any) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`).then(r => r.json());
  
  return {
    props: { post }
  };
}

export default function BlogPost({ post }: any) {
  return (
    <article>
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}
```

**When to use SSG:**
- Content doesn't change often
- Same content for all users
- SEO is critical
- Performance is top priority
- Examples: blogs, documentation, marketing pages

**Pros:**
- ✅ Blazing fast (served from CDN)
- ✅ Minimal server load
- ✅ Perfect SEO
- ✅ Can be hosted anywhere (static hosting)
- ✅ Scales infinitely

**Cons:**
- ❌ Content can become stale
- ❌ Build time increases with page count
- ❌ Requires rebuild to update content
- ❌ Can't personalize content per user

---

#### 2. Incremental Static Regeneration (ISR)

**What it is:**
Static pages that can be **updated after deployment** without rebuilding the entire site. Best of both SSG and SSR.

**How it works:**
```
1. Page generated at build time
2. Served from cache (fast!)
3. After revalidation period, next request triggers background regeneration
4. Old page served while new one generates
5. New page replaces old in cache
```

**App Router - Time-based Revalidation:**

```typescript
// app/products/[id]/page.tsx
// Revalidate every 60 seconds
export const revalidate = 60;

export default async function ProductPage({ params }: { params: { id: string } }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`).then(r => r.json());
  
  return (
    <div>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
      <p>Stock: {product.stock}</p>
    </div>
  );
}
```

**App Router - Per-Fetch Revalidation:**

```typescript
export default async function ProductPage({ params }: { params: { id: string } }) {
  // This specific fetch revalidates every 30 seconds
  const product = await fetch(`https://api.example.com/products/${params.id}`, {
    next: { revalidate: 30 }
  }).then(r => r.json());
  
  // This fetch revalidates every 10 seconds
  const reviews = await fetch(`https://api.example.com/products/${params.id}/reviews`, {
    next: { revalidate: 10 }
  }).then(r => r.json());
  
  return (
    <div>
      <h1>{product.name}</h1>
      <div>Reviews: {reviews.length}</div>
    </div>
  );
}
```

**App Router - Tag-based Revalidation:**

```typescript
// app/products/[id]/page.tsx
export default async function ProductPage({ params }: { params: { id: string } }) {
  // Tag this fetch for targeted revalidation
  const product = await fetch(`https://api.example.com/products/${params.id}`, {
    next: { tags: ['products', `product-${params.id}`] }
  }).then(r => r.json());
  
  return <div>{product.name}</div>;
}

// app/api/revalidate/route.ts
import { revalidateTag } from 'next/cache';
import { NextRequest } from 'next/server';

export async function POST(request: NextRequest) {
  const { tag } = await request.json();
  
  // Revalidate all pages tagged with 'products'
  revalidateTag(tag);
  
  return Response.json({ revalidated: true, now: Date.now() });
}

// Trigger: POST /api/revalidate { "tag": "products" }
```

**Pages Router - ISR:**

```typescript
// pages/products/[id].tsx
export async function getStaticProps({ params }: any) {
  const product = await fetch(`https://api.example.com/products/${params.id}`).then(r => r.json());
  
  return {
    props: { product },
    revalidate: 60 // Revalidate every 60 seconds
  };
}

export async function getStaticPaths() {
  // Pre-generate only popular products
  const popularProducts = await fetch('https://api.example.com/products/popular').then(r => r.json());
  
  const paths = popularProducts.map((p: any) => ({
    params: { id: p.id }
  }));
  
  return {
    paths,
    fallback: 'blocking' // Generate other pages on-demand
  };
}

export default function ProductPage({ product }: any) {
  return <div>{product.name}</div>;
}
```

**On-Demand Revalidation (Pages Router):**

```typescript
// pages/api/revalidate.ts
import { NextApiRequest, NextApiResponse } from 'next';

export default async function handler(req: NextApiRequest, res: NextApiResponse) {
  // Check secret to confirm this is a valid request
  if (req.query.secret !== process.env.REVALIDATE_SECRET) {
    return res.status(401).json({ message: 'Invalid token' });
  }
  
  try {
    // Revalidate specific path
    await res.revalidate('/products/123');
    return res.json({ revalidated: true });
  } catch (err) {
    return res.status(500).send('Error revalidating');
  }
}

// Trigger: GET /api/revalidate?secret=TOKEN
```

**When to use ISR:**
- Content updates periodically (not real-time)
- E-commerce product pages
- News articles
- Social media posts
- Large sites with thousands of pages

**Pros:**
- ✅ Fast like SSG (CDN cached)
- ✅ Content stays reasonably fresh
- ✅ No full rebuild needed
- ✅ Scales well
- ✅ On-demand updates possible

**Cons:**
- ❌ First user after revalidation sees stale content briefly
- ❌ More complex than pure SSG
- ❌ Requires hosting that supports ISR (Vercel, self-hosted Node.js)

---

#### 3. Server-Side Rendering (SSR)

**What it is:**
HTML is generated on the **server for each request**. Always fresh data, but slower than static.

**App Router - Force Dynamic:**

```typescript
// app/dashboard/page.tsx
// Force this page to be dynamic (SSR)
export const dynamic = 'force-dynamic';

export default async function Dashboard() {
  // Fetched on every request
  const user = await getCurrentUser();
  const stats = await fetch('https://api.example.com/stats', {
    cache: 'no-store' // Don't cache this fetch
  }).then(r => r.json());
  
  return (
    <div>
      <h1>Welcome, {user.name}</h1>
      <div>Active users: {stats.activeUsers}</div>
    </div>
  );
}
```

**App Router - Dynamic with Cookies/Headers:**

```typescript
import { cookies, headers } from 'next/headers';

export default async function Page() {
  // Using cookies() or headers() automatically makes the page dynamic
  const cookieStore = cookies();
  const token = cookieStore.get('auth-token');
  
  const headersList = headers();
  const userAgent = headersList.get('user-agent');
  
  return <div>User Agent: {userAgent}</div>;
}
```

**Pages Router - SSR:**

```typescript
// pages/dashboard.tsx
export async function getServerSideProps(context: any) {
  // Runs on every request
  const { req, res } = context;
  
  const user = await getUserFromCookie(req.cookies.token);
  
  if (!user) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      }
    };
  }
  
  const stats = await fetch('https://api.example.com/stats').then(r => r.json());
  
  return {
    props: {
      user,
      stats
    }
  };
}

export default function Dashboard({ user, stats }: any) {
  return (
    <div>
      <h1>Welcome, {user.name}</h1>
      <div>Active users: {stats.activeUsers}</div>
    </div>
  );
}
```

**When to use SSR:**
- Content must be real-time
- Personalized per user
- Authentication required
- SEO important + dynamic content
- Examples: user dashboards, admin panels, personalized feeds

**Pros:**
- ✅ Always up-to-date content
- ✅ Full SEO support
- ✅ Personalized per request
- ✅ Access to request context (cookies, headers)

**Cons:**
- ❌ Slower than static (server processing time)
- ❌ Higher server costs
- ❌ Can't serve from CDN edge (only origin)
- ❌ Server must always be available
- ❌ Potential bottleneck under high load

---

#### 4. Client-Side Rendering (CSR)

**What it is:**
HTML is minimal, data fetched in the **browser after page loads**. No pre-rendering.

**App Router - Client Component:**

```typescript
// app/analytics/page.tsx
'use client';

import { useState, useEffect } from 'react';

export default function AnalyticsPage() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetch('/api/analytics')
      .then(r => r.json())
      .then(data => {
        setData(data);
        setLoading(false);
      });
  }, []);
  
  if (loading) return <div>Loading...</div>;
  
  return (
    <div>
      <h1>Analytics Dashboard</h1>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  );
}
```

**Using SWR (Recommended for CSR):**

```typescript
'use client';

import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then(r => r.json());

export default function ProfilePage() {
  const { data, error, isLoading } = useSWR('/api/user', fetcher, {
    refreshInterval: 3000, // Refresh every 3 seconds
    revalidateOnFocus: true
  });
  
  if (error) return <div>Failed to load</div>;
  if (isLoading) return <div>Loading...</div>;
  
  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.email}</p>
    </div>
  );
}
```

**Using React Query:**

```typescript
'use client';

import { useQuery } from '@tanstack/react-query';

export default function PostsPage() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['posts'],
    queryFn: async () => {
      const response = await fetch('/api/posts');
      return response.json();
    },
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
  
  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error loading posts</div>;
  
  return (
    <div>
      {data.map((post: any) => (
        <article key={post.id}>
          <h2>{post.title}</h2>
        </article>
      ))}
    </div>
  );
}
```

**When to use CSR:**
- SEO not important (private pages)
- Highly interactive features
- Real-time data (WebSocket)
- User-specific data behind authentication
- Examples: dashboards, admin panels, chat apps

**Pros:**
- ✅ Real-time data updates
- ✅ Rich interactivity
- ✅ Reduces server load
- ✅ Better for private/authenticated content

**Cons:**
- ❌ No SEO (content not in initial HTML)
- ❌ Slower perceived performance
- ❌ Blank screen while loading
- ❌ Requires JavaScript enabled

---

#### 5. Streaming SSR (React 18+)

**What it is:**
Server-side rendering that **streams HTML** to the browser as it's generated. Faster time to first byte.

**App Router with Suspense:**

```typescript
// app/dashboard/page.tsx
import { Suspense } from 'react';

// Fast component - renders immediately
function UserInfo({ userId }: { userId: string }) {
  const user = getUserFromCache(userId); // Fast
  return <div>Welcome, {user.name}</div>;
}

// Slow component - streams later
async function ActivityFeed({ userId }: { userId: string }) {
  const activities = await fetchActivities(userId); // Slow API
  
  return (
    <ul>
      {activities.map((activity: any) => (
        <li key={activity.id}>{activity.message}</li>
      ))}
    </ul>
  );
}

export default function Dashboard({ params }: { params: { userId: string } }) {
  return (
    <div>
      {/* Renders immediately */}
      <UserInfo userId={params.userId} />
      
      {/* Streams in when ready */}
      <Suspense fallback={<div>Loading activity...</div>}>
        <ActivityFeed userId={params.userId} />
      </Suspense>
    </div>
  );
}
```

**Multiple Suspense Boundaries:**

```typescript
import { Suspense } from 'react';

export default function ComplexPage() {
  return (
    <div>
      <h1>Dashboard</h1>
      
      {/* Each section streams independently */}
      <Suspense fallback={<Skeleton />}>
        <RecentOrders />
      </Suspense>
      
      <Suspense fallback={<Skeleton />}>
        <Analytics />
      </Suspense>
      
      <Suspense fallback={<Skeleton />}>
        <Notifications />
      </Suspense>
    </div>
  );
}
```

**When to use Streaming SSR:**
- Complex pages with multiple data sources
- Some data fast, some slow
- Want to show content progressively
- Improve perceived performance

**Pros:**
- ✅ Faster time to first byte (TTFB)
- ✅ Progressive rendering
- ✅ Better user experience
- ✅ SEO-friendly (all content eventually rendered)

**Cons:**
- ❌ More complex to implement
- ❌ Requires React 18+
- ❌ Not all hosting supports streaming

---

#### 6. Hybrid Rendering (Mixing Strategies)

**Combining Multiple Strategies:**

```typescript
// app/product/[id]/page.tsx
import { Suspense } from 'react';

// ISR for product info
export const revalidate = 300; // 5 minutes

// Static product data
async function ProductInfo({ id }: { id: string }) {
  const product = await fetch(`https://api.example.com/products/${id}`, {
    next: { revalidate: 300 }
  }).then(r => r.json());
  
  return (
    <div>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
    </div>
  );
}

// Client-side for real-time stock
function StockStatus({ productId }: { productId: string }) {
  'use client';
  
  const { data } = useSWR(`/api/products/${productId}/stock`, fetcher, {
    refreshInterval: 5000 // Update every 5 seconds
  });
  
  return <div>In Stock: {data?.stock || '...'}</div>;
}

// SSR for personalized recommendations
async function Recommendations({ userId }: { userId: string }) {
  const recommendations = await fetch(
    `https://api.example.com/recommendations/${userId}`,
    { cache: 'no-store' } // Always fresh
  ).then(r => r.json());
  
  return (
    <div>
      <h3>Recommended for you</h3>
      {recommendations.map((item: any) => (
        <div key={item.id}>{item.name}</div>
      ))}
    </div>
  );
}

export default async function ProductPage({ 
  params 
}: { 
  params: { id: string } 
}) {
  return (
    <div>
      {/* ISR - Cached product info */}
      <ProductInfo id={params.id} />
      
      {/* Client-side - Real-time stock */}
      <StockStatus productId={params.id} />
      
      {/* SSR - Personalized recommendations */}
      <Suspense fallback={<div>Loading recommendations...</div>}>
        <Recommendations userId="current-user-id" />
      </Suspense>
    </div>
  );
}
```

---

#### Choosing the Right Strategy

**Decision Tree:**

```
Is SEO important?
│
├─ No → Consider Client-Side Rendering (CSR)
│  └─ Is data real-time? → Yes: CSR with SWR/React Query
│     └─ No: Still CSR, but maybe reconsider
│
└─ Yes (SEO important)
   │
   ├─ Does content change per user?
   │  │
   │  ├─ Yes → Server-Side Rendering (SSR)
   │  │  └─ Is it slow? → Add Streaming with Suspense
   │  │
   │  └─ No → Continue
   │
   ├─ How often does content change?
   │  │
   │  ├─ Never/Rarely → Static Generation (SSG)
   │  │
   │  ├─ Periodically → Incremental Static Regeneration (ISR)
   │  │  └─ Need instant updates? → Add on-demand revalidation
   │  │
   │  └─ Constantly → Server-Side Rendering (SSR)
   │
   └─ How many pages?
      │
      ├─ Few (< 1000) → SSG or ISR
      │
      └─ Many (> 1000) → ISR with limited static generation
```

**Performance Comparison:**

```
Speed (Fastest to Slowest):
1. SSG (Static) - ~10ms
2. ISR (Cached) - ~15ms
3. ISR (Regenerating) - ~200ms
4. Streaming SSR - ~300ms
5. SSR (Dynamic) - ~500ms
6. CSR (Client-side) - ~1000ms+ (includes data fetching)

SEO Score:
1. SSG - Perfect ⭐⭐⭐⭐⭐
2. ISR - Perfect ⭐⭐⭐⭐⭐
3. SSR - Perfect ⭐⭐⭐⭐⭐
4. Streaming SSR - Perfect ⭐⭐⭐⭐⭐
5. CSR - Poor ⭐ (only initial HTML)

Data Freshness:
1. SSR - Always fresh ⭐⭐⭐⭐⭐
2. Streaming SSR - Always fresh ⭐⭐⭐⭐⭐
3. CSR - Real-time ⭐⭐⭐⭐⭐
4. ISR - Periodic updates ⭐⭐⭐
5. SSG - Stale until rebuild ⭐

Cost/Server Load:
1. SSG - Minimal 💰
2. ISR - Low 💰💰
3. Streaming SSR - Medium 💰💰💰
4. SSR - High 💰💰💰💰
5. CSR - Minimal (client pays) 💰
```

---

#### Real-World Examples

**E-commerce Site:**

```typescript
// Homepage - SSG (rarely changes)
// app/page.tsx
export default async function HomePage() {
  const featured = await fetch('https://api.example.com/products/featured').then(r => r.json());
  return <FeaturedProducts products={featured} />;
}

// Category pages - ISR (updates periodically)
// app/category/[slug]/page.tsx
export const revalidate = 600; // 10 minutes

// Product pages - ISR + CSR hybrid
// app/products/[id]/page.tsx
export const revalidate = 300; // 5 minutes

export default async function ProductPage({ params }: any) {
  const product = await fetch(`https://api.example.com/products/${params.id}`).then(r => r.json());
  
  return (
    <div>
      <ProductInfo product={product} />
      <ClientStockChecker productId={params.id} /> {/* Real-time stock */}
    </div>
  );
}

// User cart - SSR (personalized)
// app/cart/page.tsx
export const dynamic = 'force-dynamic';

export default async function CartPage() {
  const user = await getCurrentUser();
  const cart = await getUserCart(user.id);
  return <CartView items={cart.items} />;
}

// Checkout - SSR (sensitive data)
// app/checkout/page.tsx
export const dynamic = 'force-dynamic';
```

**News Website:**

```typescript
// Homepage - ISR (updates frequently)
// app/page.tsx
export const revalidate = 60; // 1 minute

// Article pages - ISR + on-demand revalidation
// app/articles/[slug]/page.tsx
export const revalidate = 300;

export default async function Article({ params }: any) {
  const article = await fetch(`https://api.example.com/articles/${params.slug}`, {
    next: { tags: ['articles', `article-${params.slug}`] }
  }).then(r => r.json());
  
  return <ArticleView article={article} />;
}

// Breaking news - SSR (real-time)
// app/live/page.tsx
export const dynamic = 'force-dynamic';

// Comments - CSR (real-time, not SEO critical)
// components/Comments.tsx
'use client';
export default function Comments({ articleId }: any) {
  const { data } = useSWR(`/api/articles/${articleId}/comments`, fetcher, {
    refreshInterval: 10000
  });
  return <CommentsList comments={data} />;
}
```

**SaaS Dashboard:**

```typescript
// Marketing pages - SSG
// app/page.tsx, app/pricing/page.tsx, app/about/page.tsx

// Documentation - SSG
// app/docs/[...slug]/page.tsx

// User dashboard - SSR + Streaming
// app/dashboard/page.tsx
export const dynamic = 'force-dynamic';

export default function Dashboard() {
  return (
    <div>
      <Header /> {/* Fast, renders immediately */}
      
      <Suspense fallback={<Skeleton />}>
        <UserStats /> {/* Slow query, streams later */}
      </Suspense>
      
      <Suspense fallback={<Skeleton />}>
        <RecentActivity /> {/* Another slow query */}
      </Suspense>
    </div>
  );
}

// Admin panel - SSR (sensitive data)
// app/admin/page.tsx
export const dynamic = 'force-dynamic';

// Real-time analytics - CSR
// app/analytics/page.tsx
'use client';
// Use WebSocket or polling
```

---

#### Summary

**Quick Reference:**

| Strategy | Build Time | Request Time | Data Fresh | SEO | Use For |
|----------|-----------|--------------|-----------|-----|---------|
| **SSG** | ✅ Generated | ❌ None | ❌ Stale | ✅ Yes | Blogs, docs |
| **ISR** | ✅ Generated | ⚡ Minimal | 🟡 Periodic | ✅ Yes | E-commerce, news |
| **SSR** | ❌ None | 🔴 Full render | ✅ Fresh | ✅ Yes | Dashboards, personalized |
| **Streaming SSR** | ❌ None | 🟡 Partial | ✅ Fresh | ✅ Yes | Complex pages |
| **CSR** | ❌ None | ❌ None | ✅ Real-time | ❌ No | Private data, interactive |

**Best Practices:**

1. **Default to Static:** Start with SSG/ISR, only use SSR when necessary
2. **Combine Strategies:** Use hybrid approaches for optimal performance
3. **Use Suspense:** For streaming SSR with partial hydration
4. **Cache Strategically:** Use appropriate revalidation periods
5. **Monitor Performance:** Measure real-world impact of each strategy
6. **Consider CDN:** Static content can be globally distributed
7. **Think SEO:** Ensure critical content is pre-rendered
8. **Optimize Data Fetching:** Minimize waterfalls, use parallel fetching

---

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

### Q5.1: What are Core Web Vitals and how do you optimize them in Next.js?

**Answer:**
Core Web Vitals are a set of metrics defined by Google to measure user experience. The three main metrics are:

#### 1. LCP (Largest Contentful Paint)

**What it is:**
LCP measures loading performance. It marks the time when the largest content element (image, video, or text block) becomes visible in the viewport.

**Scoring:**
- **Good**: 0-2.5 seconds ✅
- **Needs Improvement**: 2.5-4.0 seconds ⚠️
- **Poor**: > 4.0 seconds ❌

**What counts as LCP:**
- `<img>` elements
- `<image>` elements inside `<svg>`
- `<video>` elements (poster image)
- Background images loaded via CSS `url()`
- Block-level text elements

**Optimizing LCP in Next.js:**

```javascript
import Image from 'next/image';

export default function Hero() {
  return (
    <Image
      src="/hero.jpg"
      alt="Hero image"
      width={1920}
      height={1080}
      priority // ⭐ CRITICAL: Loads immediately, prevents lazy-loading
      // This tells Next.js this image is the LCP element
    />
  );
}
```

**Best practices for LCP:**

```javascript
// 1. Preload critical resources
export const metadata = {
  other: {
    'link': [
      { rel: 'preload', href: '/hero.jpg', as: 'image' }
    ]
  }
};

// 2. Use proper image sizes
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1920}
  height={1080}
  priority
  quality={85} // Balance quality vs file size
  sizes="(max-width: 768px) 100vw, 1920px" // Responsive sizes
/>

// 3. Optimize fonts
import { Inter } from 'next/font/google';

const inter = Inter({ 
  subsets: ['latin'],
  display: 'swap', // Show fallback while loading
  preload: true
});

// 4. Use Server-Side Rendering for above-the-fold content
export default async function Page() {
  const heroData = await fetchHeroData(); // Rendered on server
  
  return (
    <div>
      <Hero data={heroData} /> {/* LCP element ready immediately */}
      <Suspense fallback={<Spinner />}>
        <BelowFoldContent /> {/* Can load later */}
      </Suspense>
    </div>
  );
}
```

**Common LCP issues and fixes:**

| Issue | Solution |
|-------|----------|
| Large images | Use Next.js Image component with `priority` |
| Slow server response | Use SSG/ISR instead of SSR, optimize API |
| Render-blocking resources | Minimize CSS, use font-display: swap |
| Client-side rendering | Use Server Components for LCP content |

---

#### 2. FID (First Input Delay) / INP (Interaction to Next Paint)

**What it is:**
- **FID** (being replaced): Time from user interaction to browser response
- **INP** (new metric): Measures responsiveness throughout page lifetime

**Scoring (INP):**
- **Good**: 0-200ms ✅
- **Needs Improvement**: 200-500ms ⚠️
- **Poor**: > 500ms ❌

**Optimizing FID/INP in Next.js:**

```javascript
// 1. Minimize JavaScript execution
'use client';
import { useState, useTransition } from 'react';

export default function SearchPage() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();
  
  const handleSearch = (e) => {
    const value = e.target.value;
    
    // Urgent: Update input immediately (responsive!)
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

// 2. Code splitting for heavy components
import dynamic from 'next/dynamic';

const HeavyInteractiveComponent = dynamic(
  () => import('./HeavyComponent'),
  { ssr: false } // Don't block initial render
);

// 3. Debounce expensive operations
import { useDeferredValue } from 'react';

function SearchResults({ query }) {
  const deferredQuery = useDeferredValue(query);
  const results = useSearch(deferredQuery);
  
  return <ResultsList results={results} />;
}
```

---

#### 3. CLS (Cumulative Layout Shift)

**What it is:**
CLS measures visual stability. It tracks unexpected layout shifts during page load.

**Scoring:**
- **Good**: < 0.1 ✅
- **Needs Improvement**: 0.1-0.25 ⚠️
- **Poor**: > 0.25 ❌

**Optimizing CLS in Next.js:**

```javascript
// 1. ALWAYS set width/height for images
import Image from 'next/image';

// ✅ Good: No layout shift
<Image
  src="/profile.jpg"
  alt="Profile"
  width={400}
  height={400}
/>

// ❌ Bad: Causes layout shift
<img src="/profile.jpg" alt="Profile" />

// 2. Use blur placeholder
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1920}
  height={1080}
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,/9j/4AAQ..." // Low quality placeholder
/>

// 3. Reserve space for dynamic content
<div style={{ minHeight: '200px' }}>
  <Suspense fallback={<div style={{ height: '200px' }}>Loading...</div>}>
    <DynamicContent />
  </Suspense>
</div>

// 4. Optimize fonts
import { Inter } from 'next/font/google';

const inter = Inter({ 
  subsets: ['latin'],
  display: 'swap', // Prevents invisible text (FOIT)
  adjustFontFallback: true // Matches fallback font metrics
});

// 5. Avoid inserting content above existing content
// ❌ Bad
function BadComponent() {
  const [banner, setBanner] = useState(null);
  
  useEffect(() => {
    // This inserts content, pushing everything down!
    setBanner(<div>Important message!</div>);
  }, []);
  
  return (
    <>
      {banner}
      <main>Content</main>
    </>
  );
}

// ✅ Good
function GoodComponent() {
  const [banner, setBanner] = useState(null);
  
  return (
    <>
      <div style={{ minHeight: banner ? 'auto' : '60px' }}>
        {banner}
      </div>
      <main>Content</main>
    </>
  );
}
```

---

#### Monitoring Core Web Vitals

**1. Using Vercel Analytics:**
```javascript
// app/layout.js
import { Analytics } from '@vercel/analytics/react';
import { SpeedInsights } from '@vercel/speed-insights/next';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics /> {/* Tracks Core Web Vitals */}
        <SpeedInsights /> {/* Real user monitoring */}
      </body>
    </html>
  );
}
```

**2. Custom Web Vitals Tracking:**
```javascript
// app/layout.js
import { sendToAnalytics } from './analytics';

export function reportWebVitals(metric) {
  switch (metric.name) {
    case 'LCP':
      console.log('LCP:', metric.value);
      break;
    case 'FID':
      console.log('FID:', metric.value);
      break;
    case 'CLS':
      console.log('CLS:', metric.value);
      break;
    case 'INP':
      console.log('INP:', metric.value);
      break;
  }
  
  // Send to analytics service
  sendToAnalytics(metric);
}
```

**3. Lighthouse CI:**
```yaml
# .github/workflows/lighthouse.yml
name: Lighthouse CI
on: [push]
jobs:
  lighthouse:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Lighthouse CI
        uses: treosh/lighthouse-ci-action@v9
        with:
          urls: |
            https://example.com/
            https://example.com/about
          uploadArtifacts: true
```

**Summary - Quick Optimization Checklist:**

**For LCP:**
- [ ] Use `priority` prop on hero images
- [ ] Optimize image sizes and formats
- [ ] Use font-display: swap
- [ ] Minimize server response time
- [ ] Use SSG/ISR when possible

**For INP/FID:**
- [ ] Minimize JavaScript bundle
- [ ] Use code splitting
- [ ] Implement useTransition for heavy updates
- [ ] Defer non-critical scripts
- [ ] Use Server Components

**For CLS:**
- [ ] Set width/height on all images
- [ ] Use blur placeholders
- [ ] Reserve space for dynamic content
- [ ] Optimize font loading
- [ ] Avoid injecting content above viewport

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

### Q6.0.5: How to Reset Next.js Development Cache?

**Answer:**

Next.js caches various files and data during development to improve performance. Sometimes you need to clear these caches when experiencing unexpected behavior, stale data, or build issues.

---

#### Understanding Next.js Cache Locations

Next.js stores cache in several places:

**1. `.next/` Directory:**
- Contains all build outputs
- Includes cached pages, chunks, and compiled files
- Located at project root

**2. `node_modules/.cache/`:**
- Stores SWC compilation cache
- Babel cache (if using Babel)
- Other build tool caches

**3. Browser Cache:**
- Client-side route cache
- Prefetched data
- Service worker cache

**4. Server Memory Cache:**
- Runtime request memoization
- Development server cache

---

#### Method 1: Delete .next Folder (Most Common)

**Manual Deletion:**

```bash
# Windows (PowerShell)
Remove-Item -Recurse -Force .next

# macOS/Linux
rm -rf .next

# Then restart dev server
npm run dev
```

**Using npm scripts:**

```json
// package.json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "clean": "rm -rf .next",
    "clean:win": "if exist .next rmdir /s /q .next",
    "dev:clean": "npm run clean && npm run dev"
  }
}
```

**Usage:**
```bash
# Clean and restart
npm run dev:clean

# Or manually
npm run clean
npm run dev
```

---

#### Method 2: Clear All Caches (Deep Clean)

**Full Cache Clear:**

```bash
# Windows (PowerShell)
Remove-Item -Recurse -Force .next
Remove-Item -Recurse -Force node_modules/.cache

# macOS/Linux
rm -rf .next
rm -rf node_modules/.cache
rm -rf .swc

# Restart dev server
npm run dev
```

**Complete Reset Script:**

```json
// package.json
{
  "scripts": {
    "clean:all": "rm -rf .next node_modules/.cache .swc",
    "clean:all:win": "if exist .next rmdir /s /q .next && if exist node_modules\\.cache rmdir /s /q node_modules\\.cache",
    "reset": "npm run clean:all && npm run dev"
  }
}
```

---

#### Method 3: Clear Specific Caches

**1. Clear Only Build Cache:**
```bash
# Keeps node_modules cache intact
rm -rf .next
```

**2. Clear SWC Cache:**
```bash
# Next.js 12+ uses SWC compiler
rm -rf .next/cache
rm -rf node_modules/.cache
```

**3. Clear TypeScript Build Info:**
```bash
# If using TypeScript
rm tsconfig.tsbuildinfo
```

**4. Clear Turbopack Cache (Next.js 14+):**
```bash
# If using --turbo flag
rm -rf .next/cache/webpack
```

---

#### Method 4: Programmatic Cache Clearing

**Create a cache clearing utility:**

```javascript
// scripts/clear-cache.js
const fs = require('fs');
const path = require('path');

const cacheDirs = [
  '.next',
  'node_modules/.cache',
  '.swc',
  '.turbo'
];

function clearCache() {
  console.log('🧹 Clearing Next.js caches...\n');
  
  cacheDirs.forEach(dir => {
    const fullPath = path.join(process.cwd(), dir);
    
    if (fs.existsSync(fullPath)) {
      try {
        fs.rmSync(fullPath, { recursive: true, force: true });
        console.log(`✅ Cleared: ${dir}`);
      } catch (error) {
        console.error(`❌ Failed to clear ${dir}:`, error.message);
      }
    } else {
      console.log(`⏭️  Skipped: ${dir} (doesn't exist)`);
    }
  });
  
  console.log('\n✨ Cache clearing complete!\n');
}

clearCache();
```

**Add to package.json:**

```json
{
  "scripts": {
    "clear-cache": "node scripts/clear-cache.js",
    "dev:fresh": "npm run clear-cache && npm run dev"
  }
}
```

**Usage:**
```bash
npm run clear-cache
npm run dev:fresh
```

---

#### Method 5: Browser Cache Clearing

**Clear Browser-Side Cache:**

**Manual (Chrome/Edge):**
1. Open DevTools (F12)
2. Right-click refresh button
3. Select "Empty Cache and Hard Reload"

**Programmatic:**

```javascript
// app/layout.tsx
// Add cache-busting headers in development
export const metadata = {
  other: {
    'Cache-Control': process.env.NODE_ENV === 'development' 
      ? 'no-store, no-cache, must-revalidate' 
      : undefined
  }
};
```

**Service Worker Clearing:**

```javascript
// public/sw.js or app/service-worker.js
self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames.map((cacheName) => {
          return caches.delete(cacheName);
        })
      );
    })
  );
});
```

---

#### Method 6: Clear Runtime Data Cache

**Clear Next.js Data Cache:**

```typescript
// app/actions/clear-cache.ts
'use server';

import { revalidatePath, revalidateTag } from 'next/cache';

export async function clearAllCache() {
  // Revalidate all paths
  revalidatePath('/', 'layout');
  
  return { success: true, message: 'Cache cleared' };
}

export async function clearSpecificCache(path: string) {
  revalidatePath(path);
  return { success: true, message: `Cache cleared for ${path}` };
}

export async function clearTagCache(tag: string) {
  revalidateTag(tag);
  return { success: true, message: `Cache cleared for tag: ${tag}` };
}
```

**Usage in Component:**

```typescript
// app/admin/cache-control.tsx
'use client';

import { clearAllCache, clearSpecificCache, clearTagCache } from '../actions/clear-cache';

export default function CacheControl() {
  const handleClearAll = async () => {
    const result = await clearAllCache();
    alert(result.message);
  };
  
  const handleClearPath = async () => {
    const path = prompt('Enter path to clear:');
    if (path) {
      const result = await clearSpecificCache(path);
      alert(result.message);
    }
  };
  
  const handleClearTag = async () => {
    const tag = prompt('Enter tag to clear:');
    if (tag) {
      const result = await clearTagCache(tag);
      alert(result.message);
    }
  };
  
  return (
    <div>
      <h2>Cache Control</h2>
      <button onClick={handleClearAll}>Clear All Cache</button>
      <button onClick={handleClearPath}>Clear Specific Path</button>
      <button onClick={handleClearTag}>Clear Tag Cache</button>
    </div>
  );
}
```

---

#### Method 7: Restart Development Server with Clean Flag

**Using Environment Variables:**

```json
// package.json
{
  "scripts": {
    "dev": "next dev",
    "dev:clean": "NEXT_CLEAN=true next dev"
  }
}
```

**Custom Server with Cache Clearing:**

```javascript
// server.js
const next = require('next');
const { exec } = require('child_process');

const dev = process.env.NODE_ENV !== 'production';
const cleanCache = process.env.NEXT_CLEAN === 'true';

if (cleanCache && dev) {
  console.log('🧹 Cleaning cache before start...');
  exec('rm -rf .next', (error) => {
    if (error) {
      console.error('Cache clear failed:', error);
    } else {
      console.log('✅ Cache cleared successfully');
    }
    startServer();
  });
} else {
  startServer();
}

function startServer() {
  const app = next({ dev });
  const handle = app.getRequestHandler();
  
  app.prepare().then(() => {
    // Your server setup...
  });
}
```

---

#### When to Clear Each Cache

**Clear `.next/` when:**
- ✅ Pages not updating after code changes
- ✅ Strange build errors
- ✅ Switching between branches
- ✅ After updating Next.js version
- ✅ CSS/styling issues not resolving
- ✅ TypeScript types not updating

**Clear `node_modules/.cache/` when:**
- ✅ SWC compilation errors
- ✅ Babel transformation issues
- ✅ After updating dependencies
- ✅ Compiler hanging or very slow

**Clear browser cache when:**
- ✅ Client-side navigation issues
- ✅ Old assets loading
- ✅ Service worker problems
- ✅ Stale API responses

**Clear runtime cache when:**
- ✅ Stale data showing in production
- ✅ ISR pages not updating
- ✅ Tagged cache not invalidating

---

#### Common Issues Solved by Cache Clearing

**1. Changes Not Reflecting:**

```bash
# Problem: Code changes don't appear in browser
# Solution: Clear .next and restart

rm -rf .next
npm run dev
```

**2. Module Resolution Errors:**

```bash
# Problem: "Module not found" after installing package
# Solution: Clear all caches and reinstall

rm -rf .next node_modules/.cache node_modules
npm install
npm run dev
```

**3. TypeScript Errors Persisting:**

```bash
# Problem: TS errors remain after fixing
# Solution: Clear TypeScript build info

rm tsconfig.tsbuildinfo
rm -rf .next
npm run dev
```

**4. Build Hanging or Very Slow:**

```bash
# Problem: Build process hangs
# Solution: Clear SWC cache

rm -rf .next/cache
rm -rf node_modules/.cache
npm run dev
```

**5. CSS Not Updating:**

```bash
# Problem: Style changes not applying
# Solution: Clear .next and hard refresh browser

rm -rf .next
# Then in browser: Ctrl+Shift+R (hard refresh)
```

**6. Environment Variables Not Loading:**

```bash
# Problem: New .env variables not recognized
# Solution: Restart dev server (no cache clear needed)

# Stop server (Ctrl+C)
npm run dev
# Note: .env changes always require server restart
```

---

#### Automated Cache Management

**Pre-commit Hook (Clear Before Commits):**

```javascript
// .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

# Clear cache before commit (optional)
rm -rf .next/cache

echo "✅ Cache cleared"
```

**Post-checkout Hook (Clear After Branch Switch):**

```bash
# .husky/post-checkout
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

# Clear cache after checking out branches
rm -rf .next
rm -rf node_modules/.cache

echo "✅ Cache cleared after branch switch"
```

**Install Husky:**
```bash
npm install -D husky
npx husky install
npx husky add .husky/post-checkout "rm -rf .next"
```

---

#### Cache Clearing Utilities

**Cross-Platform Script (Works on Windows, Mac, Linux):**

```javascript
// scripts/clear-cache-cross-platform.js
const fs = require('fs');
const path = require('path');

function deleteFolderRecursive(folderPath) {
  if (fs.existsSync(folderPath)) {
    fs.readdirSync(folderPath).forEach((file) => {
      const curPath = path.join(folderPath, file);
      
      if (fs.lstatSync(curPath).isDirectory()) {
        deleteFolderRecursive(curPath);
      } else {
        fs.unlinkSync(curPath);
      }
    });
    
    fs.rmdirSync(folderPath);
  }
}

const cacheDirs = [
  path.join(process.cwd(), '.next'),
  path.join(process.cwd(), 'node_modules', '.cache'),
  path.join(process.cwd(), '.swc'),
];

console.log('🧹 Clearing Next.js caches...\n');

cacheDirs.forEach((dir) => {
  try {
    if (fs.existsSync(dir)) {
      deleteFolderRecursive(dir);
      console.log(`✅ Cleared: ${path.relative(process.cwd(), dir)}`);
    } else {
      console.log(`⏭️  Skipped: ${path.relative(process.cwd(), dir)} (not found)`);
    }
  } catch (error) {
    console.error(`❌ Error clearing ${dir}:`, error.message);
  }
});

console.log('\n✨ Done!\n');
```

**Add to package.json:**
```json
{
  "scripts": {
    "clear": "node scripts/clear-cache-cross-platform.js"
  }
}
```

---

#### Development vs Production Cache

**Development Cache:**
```javascript
// Stored in .next directory
// Cleared with: rm -rf .next

// Development caches:
// - Hot Module Replacement state
// - Webpack build cache
// - SWC compilation cache
// - Fast Refresh state
```

**Production Cache:**
```javascript
// Stored in hosting platform (Vercel, etc.)
// Cleared via:

// 1. Manual revalidation
import { revalidatePath } from 'next/cache';
revalidatePath('/path');

// 2. On-demand ISR
fetch('/api/revalidate?path=/path');

// 3. Deploy new version
// (Clears all cache automatically)
```

---

#### Debugging Cache Issues

**1. Check What's Cached:**

```javascript
// next.config.js - Enable build output analysis
module.exports = {
  // Show detailed build output
  // This helps identify what's being cached
};
```

**2. Disable Caching Temporarily:**

```javascript
// next.config.js
module.exports = {
  // Disable all caching for debugging
  generateBuildId: async () => {
    return 'development-build-' + Date.now();
  },
};
```

**3. Monitor Cache Behavior:**

```javascript
// middleware.ts
import { NextResponse } from 'next/server';

export function middleware(request) {
  const response = NextResponse.next();
  
  // Add debug headers
  response.headers.set('X-Cache-Status', 'MISS');
  response.headers.set('X-Cache-Time', new Date().toISOString());
  
  return response;
}
```

---

#### Best Practices

**1. Regular Cache Clearing:**
```bash
# Clear cache after major changes
npm run clean && npm run dev

# Clear cache after dependency updates
rm -rf .next node_modules/.cache
npm run dev
```

**2. Automated Cache Management:**
```json
// package.json
{
  "scripts": {
    "predev": "npm run clean",
    "clean": "rm -rf .next",
    "dev": "next dev"
  }
}
```

**3. Don't Clear Too Often:**
```bash
# ❌ Avoid: Clearing on every start (slows development)
"dev": "rm -rf .next && next dev"

# ✅ Better: Clear only when needed
"dev": "next dev"
"dev:clean": "rm -rf .next && next dev"
```

**4. Use Selective Clearing:**
```bash
# Only clear what's necessary
rm -rf .next/cache        # Just cache
rm -rf .next              # Everything
```

---

#### Quick Reference

**Most Common Commands:**

```bash
# Quick clean (most issues)
rm -rf .next && npm run dev

# Deep clean (persistent issues)
rm -rf .next node_modules/.cache && npm run dev

# Nuclear option (all caches)
rm -rf .next node_modules/.cache node_modules && npm install && npm run dev

# Browser cache
# Chrome: Ctrl+Shift+R (Windows) or Cmd+Shift+R (Mac)

# Production cache
# Trigger revalidation via API or redeploy
```

**Troubleshooting Decision Tree:**

```
Issue: Changes not showing
├─ Try: Hard refresh browser (Ctrl+Shift+R)
├─ Still broken? Clear .next: rm -rf .next
├─ Still broken? Clear all caches: rm -rf .next node_modules/.cache
└─ Still broken? Reinstall: rm -rf node_modules && npm install

Issue: Build errors
├─ Try: Clear .next: rm -rf .next
├─ Still broken? Clear TypeScript cache: rm tsconfig.tsbuildinfo
└─ Still broken? Reinstall dependencies

Issue: Slow build
├─ Try: Clear SWC cache: rm -rf node_modules/.cache
└─ Still slow? Clear everything and rebuild

Issue: Stale data (production)
├─ Try: Revalidate path: revalidatePath('/path')
├─ Still stale? Revalidate tag: revalidateTag('tag')
└─ Still stale? Redeploy application
```

---

#### Platform-Specific Cache Locations

**Vercel:**
```bash
# Vercel maintains its own cache
# Clear via:
# 1. Vercel dashboard → Settings → Clear Cache
# 2. Redeploy (invalidates previous cache)
# 3. Use revalidation APIs
```

**Netlify:**
```bash
# Clear build cache
netlify build --clear-cache

# Or in netlify.toml
[build]
  ignore = "git diff --quiet HEAD^ HEAD ./"
```

**Docker:**
```dockerfile
# Don't cache .next in Docker
# .dockerignore
.next
node_modules/.cache
```

---

#### Summary

**When to Clear Cache:**

| Symptom | Cache to Clear | Command |
|---------|---------------|---------|
| Changes not showing | `.next` | `rm -rf .next` |
| Build errors | `.next` + SWC | `rm -rf .next node_modules/.cache` |
| TS errors | TypeScript | `rm tsconfig.tsbuildinfo` |
| Slow builds | All caches | `rm -rf .next node_modules/.cache` |
| Module errors | Full reset | `rm -rf node_modules && npm install` |
| Stale data (prod) | Runtime cache | `revalidatePath()` or redeploy |
| CSS not updating | `.next` + browser | `rm -rf .next` + hard refresh |

**Quick Scripts:**
```json
{
  "clean": "rm -rf .next",
  "clean:all": "rm -rf .next node_modules/.cache",
  "clean:full": "rm -rf .next node_modules && npm install",
  "dev:clean": "npm run clean && npm run dev"
}
```

**Remember:**
- Cache improves performance - don't clear unnecessarily
- Always restart dev server after clearing cache
- Browser hard refresh often sufficient for client issues
- Use revalidation APIs for production cache issues

---

### Q6.1: How to Ignore or Disable ESLint in Next.js?

**Answer:**

Next.js includes ESLint by default. Here's how to ignore, disable, or customize ESLint rules in your Next.js project.

---

#### 1. Completely Disable ESLint

**During Build (not recommended for production):**

```javascript
// next.config.js
module.exports = {
  eslint: {
    // Warning: This allows production builds to complete even if
    // your project has ESLint errors.
    ignoreDuringBuilds: true,
  },
}
```

**Remove ESLint Entirely:**

```bash
# Remove ESLint packages
npm uninstall eslint eslint-config-next

# Delete ESLint config
rm .eslintrc.json
```

---

#### 2. Ignore Specific Files and Directories

**Using .eslintignore File:**

```bash
# .eslintignore (create in project root)

# Dependencies
node_modules/
.next/
out/

# Build outputs
build/
dist/

# Environment files
.env
.env.local
.env.*.local

# Generated files
*.generated.ts
*.generated.js

# Specific files
public/service-worker.js
app/legacy-code.js

# Directories
scripts/
tools/
**/migrations/

# Pattern matching
**/*.config.js
**/*.test.js
**/*.spec.js
```

**Project Structure Example:**

```
my-next-app/
├── .eslintignore       ← ESLint ignore file
├── .eslintrc.json      ← ESLint config
├── app/
│   ├── api/
│   └── page.tsx
├── scripts/            ← Ignored (in .eslintignore)
├── public/
│   └── sw.js          ← Ignored (in .eslintignore)
└── next.config.js
```

---

#### 3. Ignore Specific ESLint Rules

**For Entire Project:**

```json
// .eslintrc.json
{
  "extends": "next/core-web-vitals",
  "rules": {
    // Disable specific rules
    "@next/next/no-html-link-for-pages": "off",
    "react/no-unescaped-entities": "off",
    "@next/next/no-img-element": "off",
    "react-hooks/exhaustive-deps": "off",
    
    // Change severity: "off" | "warn" | "error"
    "no-console": "warn",
    "no-unused-vars": "warn",
    
    // Configure rule options
    "indent": ["error", 2],
    "quotes": ["error", "single"],
    "semi": ["error", "always"]
  }
}
```

**Common Next.js Rules to Customize:**

```json
{
  "rules": {
    // Allow <a> instead of <Link> for external links
    "@next/next/no-html-link-for-pages": "off",
    
    // Allow <img> instead of next/image
    "@next/next/no-img-element": "off",
    
    // Allow missing alt text on images
    "jsx-a11y/alt-text": "off",
    
    // Allow unescaped entities like apostrophes
    "react/no-unescaped-entities": "off",
    
    // Disable hook dependencies warning
    "react-hooks/exhaustive-deps": "off",
    
    // Allow any in TypeScript
    "@typescript-eslint/no-explicit-any": "off",
    
    // Allow unused variables starting with _
    "@typescript-eslint/no-unused-vars": [
      "warn",
      { 
        "argsIgnorePattern": "^_",
        "varsIgnorePattern": "^_"
      }
    ]
  }
}
```

---

#### 4. Ignore Specific Lines or Blocks

**Disable for Next Line:**

```javascript
// eslint-disable-next-line
const data = await fetch(url);

// Disable specific rule for next line
// eslint-disable-next-line @next/next/no-img-element
<img src="/logo.png" alt="Logo" />

// Disable multiple rules
// eslint-disable-next-line no-console, no-unused-vars
console.log('Debug:', unusedVar);
```

**Disable for Entire File:**

```javascript
/* eslint-disable */
// All ESLint rules disabled for this file
const Component = () => {
  return <img src="/logo.png" />; // No warnings
};
/* eslint-enable */
```

**Disable Specific Rule for Entire File:**

```javascript
/* eslint-disable @next/next/no-img-element */
// Only this rule disabled for entire file

export default function Page() {
  return (
    <div>
      <img src="/hero.jpg" alt="Hero" />
      <img src="/logo.png" alt="Logo" />
    </div>
  );
}
```

**Disable for Block of Code:**

```javascript
function MyComponent() {
  /* eslint-disable react-hooks/exhaustive-deps */
  useEffect(() => {
    // Complex effect without all dependencies listed
    fetchData(id);
  }, []); // ESLint won't complain about missing 'id'
  /* eslint-enable react-hooks/exhaustive-deps */
  
  return <div>Content</div>;
}
```

---

#### 5. Configure ESLint for Different Environments

**Separate Rules for App and Pages Router:**

```json
// .eslintrc.json
{
  "extends": "next/core-web-vitals",
  "overrides": [
    {
      "files": ["app/**/*.{js,jsx,ts,tsx}"],
      "rules": {
        // Rules specific to App Router
        "react-hooks/rules-of-hooks": "error"
      }
    },
    {
      "files": ["pages/**/*.{js,jsx,ts,tsx}"],
      "rules": {
        // Rules specific to Pages Router
        "@next/next/no-html-link-for-pages": ["error", "pages/"]
      }
    },
    {
      "files": ["**/*.test.{js,jsx,ts,tsx}", "**/*.spec.{js,jsx,ts,tsx}"],
      "rules": {
        // Relaxed rules for tests
        "@typescript-eslint/no-explicit-any": "off",
        "no-console": "off"
      }
    }
  ]
}
```

---

#### 6. Ignore During Development Only

**VS Code Settings:**

```json
// .vscode/settings.json
{
  "eslint.enable": true,
  "eslint.alwaysShowStatus": true,
  
  // Ignore specific file patterns in editor
  "eslint.workingDirectories": [
    { "mode": "auto" }
  ],
  
  // Validate specific file types only
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ],
  
  // Don't run ESLint on save (if you want)
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": false
  }
}
```

**Temporarily Disable in Terminal:**

```bash
# Run build without ESLint
ESLINT_NO_DEV_ERRORS=true npm run dev

# Build without ESLint checks
npm run build -- --no-lint

# Or use environment variable
cross-env ESLINT_NO_DEV_ERRORS=true npm run dev
```

---

#### 7. Selective ESLint Checking

**Run ESLint on Specific Directories:**

```json
// package.json
{
  "scripts": {
    "lint": "next lint",
    "lint:app": "next lint --dir app",
    "lint:pages": "next lint --dir pages",
    "lint:components": "next lint --dir components",
    "lint:fix": "next lint --fix"
  }
}
```

**Custom ESLint Script:**

```bash
# Run ESLint only on staged files (with husky/lint-staged)
npm install --save-dev husky lint-staged

# package.json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "git add"
    ]
  }
}
```

---

#### 8. Ignore Based on File Patterns

**Advanced .eslintrc.json Configuration:**

```json
{
  "extends": "next/core-web-vitals",
  "ignorePatterns": [
    // Ignore patterns (alternative to .eslintignore)
    "node_modules/",
    ".next/",
    "out/",
    "public/",
    "*.config.js",
    "**/*.generated.ts"
  ],
  "rules": {
    // Global rules
  },
  "overrides": [
    {
      // Ignore rules for specific file patterns
      "files": ["**/legacy/**/*.js"],
      "rules": {
        // Disable all rules for legacy code
        "@typescript-eslint/no-unused-vars": "off",
        "no-console": "off"
      }
    }
  ]
}
```

---

#### 9. Real-World Examples

**Example 1: Ignore Third-Party Code:**

```javascript
// lib/external-library.js
/* eslint-disable */
// Third-party code that we don't control
export function externalFunction() {
  var oldStyleVar = 'legacy';
  console.log(oldStyleVar);
}
/* eslint-enable */
```

**Example 2: Allow Console in Development:**

```javascript
// utils/logger.ts
export function logDebug(message: string) {
  if (process.env.NODE_ENV === 'development') {
    // eslint-disable-next-line no-console
    console.log('[DEBUG]:', message);
  }
}
```

**Example 3: Ignore Generated API Types:**

```json
// .eslintignore
# Generated API client
src/api/generated/
types/api.generated.ts

# Prisma generated client
prisma/generated/
```

**Example 4: Allow Dynamic Require:**

```javascript
// next.config.js
/* eslint-disable @typescript-eslint/no-var-requires */
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer({
  // config
});
```

**Example 5: Temporarily Disable During Migration:**

```typescript
// app/old-component.tsx
/* eslint-disable @typescript-eslint/no-explicit-any */
/* eslint-disable react-hooks/exhaustive-deps */

// TODO: Refactor this component to use proper types
export default function OldComponent({ data }: any) {
  // Legacy code during migration
  useEffect(() => {
    processData(data);
  }, []);
  
  return <div>{data.value}</div>;
}
```

---

#### 10. Best Practices

**✅ Do:**

1. **Use .eslintignore for generated files:**
```bash
# .eslintignore
.next/
out/
node_modules/
**/*.generated.ts
```

2. **Disable specific rules with comments and explanation:**
```javascript
// We need to use img here for performance reasons in this specific case
// Next/Image causes layout shift with this third-party widget
// eslint-disable-next-line @next/next/no-img-element
<img src={dynamicUrl} alt="Widget" />
```

3. **Use overrides for different contexts:**
```json
{
  "overrides": [
    {
      "files": ["**/*.test.ts"],
      "rules": {
        "@typescript-eslint/no-explicit-any": "off"
      }
    }
  ]
}
```

4. **Gradually fix instead of disabling:**
```json
{
  "rules": {
    // Change from "error" to "warn" while fixing
    "no-unused-vars": "warn"
  }
}
```

**❌ Don't:**

1. **Don't disable ESLint globally in production:**
```javascript
// ❌ Bad: Disables all error checking
module.exports = {
  eslint: {
    ignoreDuringBuilds: true,
  },
}
```

2. **Don't disable entire files without reason:**
```javascript
/* eslint-disable */
// ❌ Bad: No explanation why entire file is disabled
```

3. **Don't ignore all type checking:**
```json
{
  "rules": {
    // ❌ Bad: Defeats purpose of TypeScript
    "@typescript-eslint/no-explicit-any": "off",
    "@typescript-eslint/ban-ts-comment": "off"
  }
}
```

4. **Don't disable security rules:**
```json
{
  "rules": {
    // ❌ Bad: Security risk
    "react/no-danger": "off",
    "react/no-danger-with-children": "off"
  }
}
```

---

#### 11. Common ESLint Errors and Solutions

**Error: "next/image does not support alt text"**

```javascript
// ❌ Error
<Image src="/logo.png" width={100} height={100} />

// ✅ Solution 1: Add alt text
<Image src="/logo.png" alt="Company Logo" width={100} height={100} />

// ✅ Solution 2: Decorative image
<Image src="/bg.png" alt="" width={100} height={100} />

// ✅ Solution 3: Disable if necessary
// eslint-disable-next-line jsx-a11y/alt-text
<Image src="/logo.png" width={100} height={100} />
```

**Error: "Do not use <img>. Use Image from 'next/image' instead"**

```javascript
// ❌ Error
<img src="/hero.jpg" alt="Hero" />

// ✅ Solution 1: Use Next.js Image
import Image from 'next/image';
<Image src="/hero.jpg" alt="Hero" width={800} height={600} />

// ✅ Solution 2: External image that needs img
// eslint-disable-next-line @next/next/no-img-element
<img src={externalUrl} alt="External content" />
```

**Error: "Do not use <a>. Use Link from 'next/link' instead"**

```javascript
// ❌ Error
<a href="/about">About</a>

// ✅ Solution 1: Use Link
import Link from 'next/link';
<Link href="/about">About</Link>

// ✅ Solution 2: External link
<a href="https://external.com" target="_blank" rel="noopener noreferrer">
  External
</a>
```

**Error: "React Hook useEffect has a missing dependency"**

```javascript
// ❌ Error
useEffect(() => {
  fetchData(id);
}, []);

// ✅ Solution 1: Add dependency
useEffect(() => {
  fetchData(id);
}, [id]);

// ✅ Solution 2: Intentionally omit (with comment)
useEffect(() => {
  // Only run once on mount, id is stable
  fetchData(id);
  // eslint-disable-next-line react-hooks/exhaustive-deps
}, []);
```

---

#### 12. ESLint Configuration Templates

**Minimal Configuration (Relaxed):**

```json
// .eslintrc.json
{
  "extends": "next/core-web-vitals",
  "rules": {
    "@next/next/no-img-element": "off",
    "react/no-unescaped-entities": "off",
    "no-console": "warn"
  }
}
```

**Strict Configuration (Production):**

```json
// .eslintrc.json
{
  "extends": [
    "next/core-web-vitals",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "no-console": "error",
    "no-debugger": "error",
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/no-unused-vars": [
      "error",
      { "argsIgnorePattern": "^_" }
    ],
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

**Balanced Configuration (Recommended):**

```json
// .eslintrc.json
{
  "extends": "next/core-web-vitals",
  "rules": {
    // Warnings for common issues
    "no-console": "warn",
    "@typescript-eslint/no-unused-vars": "warn",
    "react-hooks/exhaustive-deps": "warn",
    
    // Turn off overly strict rules
    "@next/next/no-img-element": "off",
    "react/no-unescaped-entities": "off",
    
    // Keep important checks
    "no-debugger": "error",
    "react-hooks/rules-of-hooks": "error"
  }
}
```

---

#### Summary

**Quick Reference:**

| Task | Method |
|------|--------|
| Disable for build | `eslint: { ignoreDuringBuilds: true }` in next.config.js |
| Ignore files | Create `.eslintignore` file |
| Disable rule globally | Configure in `.eslintrc.json` rules |
| Disable next line | `// eslint-disable-next-line` |
| Disable entire file | `/* eslint-disable */` at top |
| Disable specific rule | `/* eslint-disable rule-name */` |
| Ignore directory | Add to `.eslintignore` or `ignorePatterns` |
| Run on specific dir | `next lint --dir folder-name` |

**Common Commands:**

```bash
# Run ESLint
npm run lint

# Fix auto-fixable issues
npm run lint -- --fix

# Lint specific directory
npm run lint -- --dir app

# Lint specific file
npx eslint path/to/file.js
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

## Custom Server

### Q8.1: What is a Next.js Custom Server and when should you use it?

**Answer:**

A Custom Server allows you to programmatically start Next.js using your own HTTP server (like Express, Fastify, etc.) instead of the default `next start` command. This gives you full control over the server layer.

---

#### Understanding Custom Servers

**Default Next.js Server:**
```bash
# Standard way - Next.js handles everything
npm run dev    # Development
npm run build && npm start  # Production
```

**Custom Server:**
```javascript
// server.js - You control the server
const express = require('express');
const next = require('next');

const dev = process.env.NODE_ENV !== 'production';
const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  const server = express();
  
  // Custom routes/middleware before Next.js
  server.get('/api/custom', (req, res) => {
    res.json({ message: 'Custom API endpoint' });
  });
  
  // Let Next.js handle all other routes
  server.all('*', (req, res) => {
    return handle(req, res);
  });
  
  server.listen(3000, (err) => {
    if (err) throw err;
    console.log('> Ready on http://localhost:3000');
  });
});
```

---

#### When to Use a Custom Server

**✅ Use Custom Server When:**

1. **Need custom routing logic not possible with Next.js routing**
   - Pattern matching beyond dynamic routes
   - Complex URL rewriting rules
   - Legacy URL compatibility

2. **Integrating with existing Express/Koa/Fastify app**
   - Adding Next.js to existing backend
   - Sharing middleware between Next.js and other routes

3. **Custom WebSocket server**
   - Real-time features (chat, notifications)
   - Sharing port with Next.js

4. **Advanced middleware requirements**
   - Complex authentication flows
   - Custom logging/monitoring
   - Request transformation

5. **Server-side features not in Next.js**
   - GraphQL server (Apollo, etc.)
   - Custom proxy logic
   - Advanced caching strategies

**❌ Avoid Custom Server When:**

1. **Default Next.js routing works** - It's optimized and maintained
2. **Deploying to Vercel** - Custom servers don't work on Vercel
3. **Want automatic optimizations** - Some features disabled with custom server
4. **Need serverless deployment** - Custom servers require traditional hosting

---

#### Important Limitations

**Features Disabled with Custom Server:**

| Feature | Works? | Alternative |
|---------|--------|-------------|
| Automatic Static Optimization | ❌ No | Use ISR manually |
| Serverless Functions | ❌ No | Deploy to Node.js server |
| Edge Runtime | ❌ No | Use standard middleware |
| Automatic HTTPS (Vercel) | ❌ No | Configure manually |
| Image Optimization (external) | ⚠️ Limited | Configure image loader |

**Trade-offs:**
- ✅ More control over server
- ✅ Custom routing/middleware
- ✅ WebSocket support
- ❌ Can't deploy to Vercel
- ❌ Lose some automatic optimizations
- ❌ More complex setup and maintenance
- ❌ Manual scaling configuration

---

#### Setting Up Custom Server with Express

**1. Install Dependencies:**
```bash
npm install express
```

**2. Create server.js:**
```javascript
// server.js
const express = require('express');
const next = require('next');

const port = parseInt(process.env.PORT, 10) || 3000;
const dev = process.env.NODE_ENV !== 'production';
const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  const server = express();
  
  // Parse JSON bodies
  server.use(express.json());
  
  // Custom API routes
  server.get('/api/health', (req, res) => {
    res.json({ status: 'ok', timestamp: Date.now() });
  });
  
  // Custom route with parameters
  server.get('/blog/:year/:month/:slug', (req, res) => {
    const { year, month, slug } = req.params;
    const queryParams = { year, month, slug };
    
    // Pass to Next.js page
    app.render(req, res, '/blog/post', queryParams);
  });
  
  // Redirect old URLs
  server.get('/old-blog/*', (req, res) => {
    res.redirect(301, `/blog${req.path.replace('/old-blog', '')}`);
  });
  
  // All other routes handled by Next.js
  server.all('*', (req, res) => {
    return handle(req, res);
  });
  
  server.listen(port, (err) => {
    if (err) throw err;
    console.log(`> Ready on http://localhost:${port}`);
  });
});
```

**3. Update package.json:**
```json
{
  "scripts": {
    "dev": "node server.js",
    "build": "next build",
    "start": "NODE_ENV=production node server.js"
  }
}
```

**4. Run:**
```bash
npm run dev
```

---

#### Custom Server with TypeScript

**1. Install TypeScript Dependencies:**
```bash
npm install --save-dev @types/node @types/express ts-node
```

**2. Create server.ts:**
```typescript
// server.ts
import express, { Request, Response } from 'express';
import next from 'next';

const port = parseInt(process.env.PORT || '3000', 10);
const dev = process.env.NODE_ENV !== 'production';
const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  const server = express();
  
  server.use(express.json());
  
  // Typed custom route
  server.get('/api/user/:id', (req: Request, res: Response) => {
    const userId = parseInt(req.params.id, 10);
    
    if (isNaN(userId)) {
      return res.status(400).json({ error: 'Invalid user ID' });
    }
    
    res.json({ id: userId, name: 'John Doe' });
  });
  
  // Handle all other routes with Next.js
  server.all('*', (req: Request, res: Response) => {
    return handle(req, res);
  });
  
  server.listen(port, () => {
    console.log(`> Ready on http://localhost:${port}`);
  });
});
```

**3. Update package.json:**
```json
{
  "scripts": {
    "dev": "ts-node --project tsconfig.server.json server.ts",
    "build": "next build",
    "start": "NODE_ENV=production ts-node --project tsconfig.server.json server.ts"
  }
}
```

**4. Create tsconfig.server.json:**
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "module": "commonjs",
    "outDir": "dist",
    "noEmit": false
  },
  "include": ["server.ts"]
}
```

---

#### Real-World Example: WebSocket Chat Server

```javascript
// server.js
const express = require('express');
const next = require('next');
const { createServer } = require('http');
const { Server } = require('socket.io');

const port = parseInt(process.env.PORT, 10) || 3000;
const dev = process.env.NODE_ENV !== 'production';
const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  const server = express();
  const httpServer = createServer(server);
  
  // Socket.IO setup
  const io = new Server(httpServer, {
    cors: {
      origin: dev ? 'http://localhost:3000' : 'https://yourdomain.com',
      methods: ['GET', 'POST']
    }
  });
  
  // WebSocket connection handling
  io.on('connection', (socket) => {
    console.log('User connected:', socket.id);
    
    socket.on('chat:message', (data) => {
      // Broadcast to all clients
      io.emit('chat:message', {
        id: Date.now(),
        user: data.user,
        message: data.message,
        timestamp: new Date().toISOString()
      });
    });
    
    socket.on('disconnect', () => {
      console.log('User disconnected:', socket.id);
    });
  });
  
  // REST API endpoint
  server.get('/api/chat/history', (req, res) => {
    // Fetch from database
    res.json({ messages: [] });
  });
  
  // Next.js routes
  server.all('*', (req, res) => {
    return handle(req, res);
  });
  
  httpServer.listen(port, () => {
    console.log(`> Ready on http://localhost:${port}`);
  });
});
```

**Client-side (React component):**
```typescript
// app/chat/page.tsx
'use client';

import { useEffect, useState } from 'react';
import io, { Socket } from 'socket.io-client';

export default function ChatPage() {
  const [socket, setSocket] = useState<Socket | null>(null);
  const [messages, setMessages] = useState<any[]>([]);
  const [input, setInput] = useState('');
  
  useEffect(() => {
    // Connect to WebSocket server
    const socketInstance = io();
    
    socketInstance.on('connect', () => {
      console.log('Connected to WebSocket');
    });
    
    socketInstance.on('chat:message', (message) => {
      setMessages((prev) => [...prev, message]);
    });
    
    setSocket(socketInstance);
    
    return () => {
      socketInstance.disconnect();
    };
  }, []);
  
  const sendMessage = () => {
    if (!socket || !input.trim()) return;
    
    socket.emit('chat:message', {
      user: 'User',
      message: input
    });
    
    setInput('');
  };
  
  return (
    <div>
      <div className="messages">
        {messages.map((msg) => (
          <div key={msg.id}>
            <strong>{msg.user}:</strong> {msg.message}
          </div>
        ))}
      </div>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && sendMessage()}
      />
      <button onClick={sendMessage}>Send</button>
    </div>
  );
}
```

---

#### Custom Server with Fastify

**Fastify is faster and more modern than Express:**

```javascript
// server.js
const Fastify = require('fastify');
const next = require('next');

const port = parseInt(process.env.PORT, 10) || 3000;
const dev = process.env.NODE_ENV !== 'production';

const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  const server = Fastify({
    logger: true
  });
  
  // Custom Fastify route
  server.get('/api/health', async (request, reply) => {
    return { status: 'ok', timestamp: Date.now() };
  });
  
  // Route with validation
  server.get('/api/user/:id', {
    schema: {
      params: {
        type: 'object',
        properties: {
          id: { type: 'number' }
        }
      }
    }
  }, async (request, reply) => {
    const { id } = request.params;
    return { id, name: 'John Doe' };
  });
  
  // Handle Next.js
  server.all('/*', async (request, reply) => {
    return handle(request.raw, reply.raw).then(() => {
      reply.sent = true;
    });
  });
  
  server.listen({ port, host: '0.0.0.0' }, (err) => {
    if (err) throw err;
    console.log(`> Ready on http://localhost:${port}`);
  });
});
```

---

#### Advanced: GraphQL Server Integration

```javascript
// server.js
const express = require('express');
const next = require('next');
const { ApolloServer } = require('@apollo/server');
const { expressMiddleware } = require('@apollo/server/express4');
const { typeDefs } = require('./graphql/schema');
const { resolvers } = require('./graphql/resolvers');

const port = parseInt(process.env.PORT, 10) || 3000;
const dev = process.env.NODE_ENV !== 'production';
const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(async () => {
  const server = express();
  
  // Apollo Server setup
  const apolloServer = new ApolloServer({
    typeDefs,
    resolvers,
  });
  
  await apolloServer.start();
  
  // GraphQL endpoint
  server.use(
    '/api/graphql',
    express.json(),
    expressMiddleware(apolloServer, {
      context: async ({ req }) => ({
        token: req.headers.authorization,
      }),
    })
  );
  
  // Next.js routes
  server.all('*', (req, res) => {
    return handle(req, res);
  });
  
  server.listen(port, () => {
    console.log(`> Ready on http://localhost:${port}`);
    console.log(`> GraphQL endpoint: http://localhost:${port}/api/graphql`);
  });
});
```

---

#### Custom Middleware Examples

**1. Request Logging:**
```javascript
server.use((req, res, next) => {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = Date.now() - start;
    console.log(`${req.method} ${req.url} - ${res.statusCode} - ${duration}ms`);
  });
  
  next();
});
```

**2. Authentication:**
```javascript
const jwt = require('jsonwebtoken');

const authMiddleware = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
};

// Protected route
server.get('/api/protected', authMiddleware, (req, res) => {
  res.json({ user: req.user });
});
```

**3. Rate Limiting:**
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP'
});

server.use('/api/', limiter);
```

**4. CORS Configuration:**
```javascript
const cors = require('cors');

server.use(cors({
  origin: process.env.ALLOWED_ORIGINS?.split(',') || '*',
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization']
}));
```

---

#### Production Deployment

**1. Dockerfile for Custom Server:**
```dockerfile
# Dockerfile
FROM node:18-alpine AS base

# Install dependencies only when needed
FROM base AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

# Build Next.js
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Production image
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# Copy built application
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/server.js ./

USER nextjs

EXPOSE 3000

ENV PORT 3000

CMD ["node", "server.js"]
```

**2. next.config.js for Standalone:**
```javascript
// next.config.js
module.exports = {
  output: 'standalone', // Creates a minimal server
  experimental: {
    // Additional optimizations
  }
};
```

**3. PM2 Configuration:**
```javascript
// ecosystem.config.js
module.exports = {
  apps: [{
    name: 'next-app',
    script: './server.js',
    instances: 'max', // Use all CPU cores
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production',
      PORT: 3000
    },
    error_file: './logs/err.log',
    out_file: './logs/out.log',
    log_date_format: 'YYYY-MM-DD HH:mm:ss Z'
  }]
};
```

**Start with PM2:**
```bash
# Install PM2
npm install -g pm2

# Start app
pm2 start ecosystem.config.js

# Monitor
pm2 monit

# View logs
pm2 logs

# Restart
pm2 restart next-app
```

---

#### Testing Custom Server

**1. Unit Test (Jest):**
```javascript
// server.test.js
const request = require('supertest');
const express = require('express');

// Mock Next.js
jest.mock('next', () => {
  return jest.fn(() => ({
    prepare: jest.fn(() => Promise.resolve()),
    getRequestHandler: jest.fn(() => (req, res) => {
      res.send('Next.js page');
    })
  }));
});

describe('Custom Server', () => {
  let app;
  
  beforeAll(async () => {
    // Setup server
    const next = require('next');
    const nextApp = next({ dev: false });
    await nextApp.prepare();
    
    app = express();
    
    app.get('/api/health', (req, res) => {
      res.json({ status: 'ok' });
    });
  });
  
  test('Health endpoint returns ok', async () => {
    const response = await request(app).get('/api/health');
    expect(response.status).toBe(200);
    expect(response.body).toEqual({ status: 'ok' });
  });
});
```

**2. Integration Test:**
```javascript
// integration.test.js
const request = require('supertest');

describe('Custom Server Integration', () => {
  const baseURL = 'http://localhost:3000';
  
  test('Custom API route works', async () => {
    const response = await request(baseURL)
      .get('/api/custom')
      .expect(200);
    
    expect(response.body).toHaveProperty('message');
  });
  
  test('Next.js page renders', async () => {
    const response = await request(baseURL)
      .get('/')
      .expect(200);
    
    expect(response.text).toContain('<!DOCTYPE html>');
  });
});
```

---

#### Common Issues and Solutions

**1. Port Already in Use:**
```javascript
// server.js
const server = express();

server.listen(port, (err) => {
  if (err) {
    if (err.code === 'EADDRINUSE') {
      console.error(`Port ${port} is already in use`);
      process.exit(1);
    }
    throw err;
  }
  console.log(`> Ready on http://localhost:${port}`);
});
```

**2. Hot Reload Not Working:**
```javascript
// For development, ensure Next.js handles hot reload
if (dev) {
  app.prepare().then(() => {
    // Your server setup
  });
}
```

**3. Static Files Not Serving:**
```javascript
// Ensure public folder is accessible
server.use(express.static('public'));

// Or let Next.js handle it
server.all('*', (req, res) => {
  return handle(req, res);
});
```

**4. 404 on Custom Routes:**
```javascript
// Make sure custom routes come BEFORE Next.js handler
server.get('/api/custom', (req, res) => {
  // Custom handler
});

// This must be last
server.all('*', (req, res) => {
  return handle(req, res);
});
```

---

#### Best Practices

**1. Separate Concerns:**
```javascript
// routes/api.js
module.exports = (server) => {
  server.get('/api/users', async (req, res) => {
    // Handler
  });
  
  server.get('/api/posts', async (req, res) => {
    // Handler
  });
};

// server.js
const apiRoutes = require('./routes/api');

app.prepare().then(() => {
  const server = express();
  
  apiRoutes(server);
  
  server.all('*', (req, res) => handle(req, res));
  
  server.listen(port);
});
```

**2. Error Handling:**
```javascript
server.use((err, req, res, next) => {
  console.error(err.stack);
  
  res.status(500).json({
    error: process.env.NODE_ENV === 'production' 
      ? 'Internal Server Error' 
      : err.message
  });
});
```

**3. Graceful Shutdown:**
```javascript
const gracefulShutdown = () => {
  console.log('Received shutdown signal, closing server...');
  
  server.close(() => {
    console.log('HTTP server closed');
    process.exit(0);
  });
  
  // Force close after 10 seconds
  setTimeout(() => {
    console.error('Forcing shutdown...');
    process.exit(1);
  }, 10000);
};

process.on('SIGTERM', gracefulShutdown);
process.on('SIGINT', gracefulShutdown);
```

**4. Health Checks:**
```javascript
server.get('/health', (req, res) => {
  // Check database, cache, etc.
  const health = {
    uptime: process.uptime(),
    timestamp: Date.now(),
    status: 'ok'
  };
  
  res.json(health);
});
```

---

#### Migration from Default Server

**Before (Default Next.js):**
```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  }
}
```

**After (Custom Server):**
```json
{
  "scripts": {
    "dev": "node server.js",
    "build": "next build",
    "start": "NODE_ENV=production node server.js"
  }
}
```

**Checklist:**
- [ ] Install Express/Fastify
- [ ] Create server.js
- [ ] Update package.json scripts
- [ ] Test all routes work
- [ ] Update deployment configuration
- [ ] Update health checks/monitoring
- [ ] Test WebSocket/custom features
- [ ] Document custom routes

---

#### Summary

**Quick Decision Guide:**

```
Do you need custom server?
│
├─ Need WebSockets? → Yes, custom server
├─ Complex routing not possible in Next.js? → Yes, custom server
├─ Integrating with existing Express app? → Yes, custom server
├─ Want to deploy to Vercel? → No, use default
├─ Need automatic optimizations? → No, use default
└─ Simple Next.js app? → No, use default
```

**Key Takeaways:**

1. **Custom Server = More Control, Less Automation**
   - You manage the server lifecycle
   - You configure deployment
   - You handle scaling

2. **When to Use:**
   - WebSockets (Socket.IO, etc.)
   - Custom routing logic
   - Integrating with existing backend
   - GraphQL server

3. **When NOT to Use:**
   - Deploying to Vercel
   - Want serverless
   - Simple use case
   - Need automatic optimizations

4. **Best Practices:**
   - Keep server code separate
   - Use TypeScript for type safety
   - Implement health checks
   - Handle errors gracefully
   - Test thoroughly
   - Document custom routes

**Common Stacks:**
- Express + Next.js (Most popular)
- Fastify + Next.js (Faster)
- Koa + Next.js (Lightweight)
- Custom Node HTTP + Next.js (Minimal)

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

### Q10: What is an enterprise-level folder structure for a Next.js e-commerce application?

**Answer:**

For an enterprise-level Next.js e-commerce application, here's a comprehensive folder structure that follows best practices for scalability, maintainability, and team collaboration:

```
ecommerce-app/
├── app/                                    # Next.js App Router
│   ├── (auth)/                            # Route group for authentication
│   │   ├── login/
│   │   │   └── page.tsx
│   │   ├── register/
│   │   │   └── page.tsx
│   │   ├── forgot-password/
│   │   │   └── page.tsx
│   │   └── layout.tsx                     # Auth-specific layout
│   │
│   ├── (shop)/                            # Route group for main shop
│   │   ├── page.tsx                       # Homepage
│   │   ├── products/
│   │   │   ├── page.tsx                   # Product listing
│   │   │   ├── [slug]/
│   │   │   │   ├── page.tsx               # Product detail
│   │   │   │   ├── loading.tsx
│   │   │   │   └── error.tsx
│   │   │   └── category/
│   │   │       └── [category]/
│   │   │           └── page.tsx
│   │   ├── cart/
│   │   │   └── page.tsx
│   │   ├── checkout/
│   │   │   ├── page.tsx
│   │   │   ├── shipping/
│   │   │   │   └── page.tsx
│   │   │   ├── payment/
│   │   │   │   └── page.tsx
│   │   │   └── confirmation/
│   │   │       └── page.tsx
│   │   └── layout.tsx                     # Main shop layout
│   │
│   ├── (dashboard)/                       # Route group for user dashboard
│   │   ├── dashboard/
│   │   │   ├── page.tsx                   # Dashboard home
│   │   │   ├── orders/
│   │   │   │   ├── page.tsx
│   │   │   │   └── [orderId]/
│   │   │   │       └── page.tsx
│   │   │   ├── wishlist/
│   │   │   │   └── page.tsx
│   │   │   ├── profile/
│   │   │   │   └── page.tsx
│   │   │   ├── addresses/
│   │   │   │   └── page.tsx
│   │   │   └── settings/
│   │   │       └── page.tsx
│   │   └── layout.tsx                     # Dashboard layout with sidebar
│   │
│   ├── (admin)/                           # Route group for admin panel
│   │   ├── admin/
│   │   │   ├── page.tsx
│   │   │   ├── products/
│   │   │   │   ├── page.tsx
│   │   │   │   ├── new/
│   │   │   │   │   └── page.tsx
│   │   │   │   └── [id]/
│   │   │   │       └── edit/
│   │   │   │           └── page.tsx
│   │   │   ├── orders/
│   │   │   │   ├── page.tsx
│   │   │   │   └── [orderId]/
│   │   │   │       └── page.tsx
│   │   │   ├── customers/
│   │   │   │   └── page.tsx
│   │   │   ├── inventory/
│   │   │   │   └── page.tsx
│   │   │   ├── analytics/
│   │   │   │   └── page.tsx
│   │   │   └── settings/
│   │   │       └── page.tsx
│   │   └── layout.tsx                     # Admin layout
│   │
│   ├── api/                               # API routes
│   │   ├── auth/
│   │   │   ├── [...nextauth]/
│   │   │   │   └── route.ts               # NextAuth.js
│   │   │   ├── register/
│   │   │   │   └── route.ts
│   │   │   └── logout/
│   │   │       └── route.ts
│   │   ├── products/
│   │   │   ├── route.ts                   # GET, POST products
│   │   │   ├── [id]/
│   │   │   │   └── route.ts               # GET, PUT, DELETE product
│   │   │   └── search/
│   │   │       └── route.ts
│   │   ├── cart/
│   │   │   ├── route.ts
│   │   │   └── [id]/
│   │   │       └── route.ts
│   │   ├── orders/
│   │   │   ├── route.ts
│   │   │   └── [id]/
│   │   │       └── route.ts
│   │   ├── payments/
│   │   │   ├── stripe/
│   │   │   │   └── webhook/
│   │   │   │       └── route.ts
│   │   │   └── create-intent/
│   │   │       └── route.ts
│   │   ├── webhooks/
│   │   │   ├── inventory/
│   │   │   │   └── route.ts
│   │   │   └── shipping/
│   │   │       └── route.ts
│   │   └── admin/
│   │       └── [...]/
│   │
│   ├── layout.tsx                         # Root layout
│   ├── loading.tsx                        # Global loading
│   ├── error.tsx                          # Global error
│   ├── not-found.tsx                      # 404 page
│   └── global.css                         # Global styles
│
├── components/                            # Reusable components
│   ├── ui/                                # Base UI components
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.test.tsx
│   │   │   ├── Button.stories.tsx
│   │   │   └── index.ts
│   │   ├── Input/
│   │   ├── Card/
│   │   ├── Modal/
│   │   ├── Dropdown/
│   │   ├── Badge/
│   │   ├── Spinner/
│   │   └── index.ts                       # Barrel export
│   │
│   ├── layout/                            # Layout components
│   │   ├── Header/
│   │   │   ├── Header.tsx
│   │   │   ├── SearchBar.tsx
│   │   │   ├── CartIcon.tsx
│   │   │   └── UserMenu.tsx
│   │   ├── Footer/
│   │   ├── Sidebar/
│   │   ├── Navigation/
│   │   └── Breadcrumbs/
│   │
│   ├── product/                           # Product-specific components
│   │   ├── ProductCard/
│   │   ├── ProductGrid/
│   │   ├── ProductDetail/
│   │   ├── ProductGallery/
│   │   ├── ProductReviews/
│   │   ├── RelatedProducts/
│   │   └── AddToCartButton/
│   │
│   ├── cart/                              # Cart components
│   │   ├── CartItem/
│   │   ├── CartSummary/
│   │   ├── CartDrawer/
│   │   └── MiniCart/
│   │
│   ├── checkout/                          # Checkout components
│   │   ├── CheckoutForm/
│   │   ├── ShippingForm/
│   │   ├── PaymentForm/
│   │   ├── OrderSummary/
│   │   └── CheckoutStepper/
│   │
│   ├── forms/                             # Form components
│   │   ├── LoginForm/
│   │   ├── RegisterForm/
│   │   ├── AddressForm/
│   │   ├── ReviewForm/
│   │   └── FormField/
│   │
│   ├── dashboard/                         # Dashboard components
│   │   ├── OrderHistory/
│   │   ├── OrderCard/
│   │   ├── DashboardStats/
│   │   └── ProfileSettings/
│   │
│   ├── admin/                             # Admin components
│   │   ├── DataTable/
│   │   ├── StatsCard/
│   │   ├── ProductForm/
│   │   ├── OrderManager/
│   │   └── InventoryManager/
│   │
│   └── shared/                            # Shared components
│       ├── ErrorBoundary/
│       ├── LoadingState/
│       ├── EmptyState/
│       ├── Pagination/
│       └── SearchBar/
│
├── lib/                                   # Core business logic & utilities
│   ├── api/                               # API client functions
│   │   ├── products.ts
│   │   ├── cart.ts
│   │   ├── orders.ts
│   │   ├── users.ts
│   │   ├── payments.ts
│   │   └── client.ts                      # Base API client
│   │
│   ├── database/                          # Database layer
│   │   ├── prisma.ts                      # Prisma client instance
│   │   ├── repositories/                  # Repository pattern
│   │   │   ├── ProductRepository.ts
│   │   │   ├── OrderRepository.ts
│   │   │   ├── UserRepository.ts
│   │   │   ├── CartRepository.ts
│   │   │   └── ReviewRepository.ts
│   │   ├── migrations/
│   │   └── seeds/
│   │       ├── products.ts
│   │       └── categories.ts
│   │
│   ├── services/                          # Business logic services
│   │   ├── ProductService.ts
│   │   ├── OrderService.ts
│   │   ├── CartService.ts
│   │   ├── PaymentService.ts
│   │   ├── ShippingService.ts
│   │   ├── EmailService.ts
│   │   ├── NotificationService.ts
│   │   ├── InventoryService.ts
│   │   └── RecommendationService.ts
│   │
│   ├── auth/                              # Authentication
│   │   ├── nextauth.ts                    # NextAuth config
│   │   ├── authOptions.ts
│   │   ├── middleware.ts
│   │   └── permissions.ts
│   │
│   ├── payments/                          # Payment integrations
│   │   ├── stripe.ts
│   │   ├── paypal.ts
│   │   └── interfaces.ts
│   │
│   ├── utils/                             # Utility functions
│   │   ├── format.ts                      # Formatters (price, date, etc.)
│   │   ├── validation.ts                  # Validators
│   │   ├── constants.ts
│   │   ├── helpers.ts
│   │   ├── logger.ts
│   │   └── error-handler.ts
│   │
│   ├── hooks/                             # Server-side hooks & utilities
│   │   └── rate-limiter.ts
│   │
│   └── cache/                             # Caching layer
│       ├── redis.ts
│       └── strategies.ts
│
├── hooks/                                 # Custom React hooks (client-side)
│   ├── useCart.ts
│   ├── useAuth.ts
│   ├── useProducts.ts
│   ├── useOrders.ts
│   ├── useLocalStorage.ts
│   ├── useDebounce.ts
│   ├── useIntersectionObserver.ts
│   ├── useMediaQuery.ts
│   └── useToast.ts
│
├── context/                               # React Context providers
│   ├── CartContext.tsx
│   ├── AuthContext.tsx
│   ├── ThemeContext.tsx
│   └── ToastContext.tsx
│
├── store/                                 # State management (Zustand/Redux)
│   ├── slices/
│   │   ├── cartSlice.ts
│   │   ├── userSlice.ts
│   │   ├── productSlice.ts
│   │   └── uiSlice.ts
│   ├── store.ts
│   └── hooks.ts
│
├── types/                                 # TypeScript type definitions
│   ├── models/                            # Database models
│   │   ├── Product.ts
│   │   ├── Order.ts
│   │   ├── User.ts
│   │   ├── Cart.ts
│   │   ├── Review.ts
│   │   └── Category.ts
│   ├── api/                               # API types
│   │   ├── requests.ts
│   │   ├── responses.ts
│   │   └── errors.ts
│   ├── components/                        # Component prop types
│   │   └── index.ts
│   └── global.d.ts                        # Global type declarations
│
├── config/                                # Configuration files
│   ├── site.ts                            # Site metadata
│   ├── navigation.ts                      # Navigation structure
│   ├── env.ts                             # Environment variables (validated)
│   ├── seo.ts                             # SEO configuration
│   ├── payment.ts                         # Payment provider config
│   └── features.ts                        # Feature flags
│
├── middleware.ts                          # Next.js middleware
│
├── prisma/                                # Prisma ORM
│   ├── schema.prisma
│   ├── migrations/
│   └── seed.ts
│
├── public/                                # Static assets
│   ├── images/
│   │   ├── products/
│   │   ├── categories/
│   │   ├── banners/
│   │   └── logos/
│   ├── icons/
│   ├── fonts/
│   └── robots.txt
│
├── styles/                                # Global styles
│   ├── globals.css
│   ├── themes/
│   │   ├── light.css
│   │   └── dark.css
│   └── variables.css
│
├── tests/                                 # Test files
│   ├── unit/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── utils/
│   ├── integration/
│   │   ├── api/
│   │   └── services/
│   ├── e2e/
│   │   ├── checkout.spec.ts
│   │   ├── product-search.spec.ts
│   │   └── user-auth.spec.ts
│   ├── fixtures/
│   └── helpers/
│
├── scripts/                               # Utility scripts
│   ├── seed-db.ts
│   ├── migrate.ts
│   ├── generate-sitemap.ts
│   └── optimize-images.ts
│
├── docs/                                  # Documentation
│   ├── API.md
│   ├── ARCHITECTURE.md
│   ├── DEPLOYMENT.md
│   └── CONTRIBUTING.md
│
├── .github/                               # GitHub specific
│   └── workflows/
│       ├── ci.yml
│       ├── deploy.yml
│       └── tests.yml
│
├── .env.local                             # Local environment variables
├── .env.example                           # Example environment variables
├── .eslintrc.json                         # ESLint config
├── .prettierrc                            # Prettier config
├── next.config.js                         # Next.js config
├── tsconfig.json                          # TypeScript config
├── tailwind.config.ts                     # Tailwind config
├── package.json
└── README.md
```

---

#### Key Architecture Principles

**1. Separation of Concerns**

Components, services, repositories, and hooks each have distinct responsibilities:

```typescript
// Components: Pure UI, no business logic
// components/product/ProductCard.tsx
import { formatPrice } from '@/lib/utils/format';
import type { Product } from '@/types/models/Product';

export function ProductCard({ product }: { product: Product }) {
  return (
    <div>
      <h3>{product.name}</h3>
      <p>{formatPrice(product.price)}</p>
    </div>
  );
}

// Services: Business logic and API calls
// lib/services/ProductService.ts
import { ProductRepository } from '@/lib/database/repositories/ProductRepository';
import { cache } from 'react';

export class ProductService {
  static getBySlug = cache(async (slug: string) => {
    return ProductRepository.findBySlug(slug);
  });
  
  static async updateInventory(productId: string, quantity: number) {
    // Business logic validation
    if (quantity < 0) throw new Error('Invalid quantity');
    return ProductRepository.updateStock(productId, quantity);
  }
}

// Repositories: Database operations
// lib/database/repositories/ProductRepository.ts
import { prisma } from '@/lib/database/prisma';

export class ProductRepository {
  static async findBySlug(slug: string) {
    return prisma.product.findUnique({
      where: { slug },
      include: {
        category: true,
        reviews: true,
        variants: true
      }
    });
  }
}

// Hooks: Reusable stateful logic
// hooks/useCart.ts
'use client';
import { useState, useEffect } from 'react';

export function useCart() {
  const [items, setItems] = useState([]);
  
  const addItem = (product) => {
    setItems([...items, product]);
  };
  
  return { items, addItem };
}
```

---

**2. Import Aliases Configuration**

```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"],
      "@/components/*": ["components/*"],
      "@/lib/*": ["lib/*"],
      "@/hooks/*": ["hooks/*"],
      "@/types/*": ["types/*"],
      "@/config/*": ["config/*"],
      "@/store/*": ["store/*"],
      "@/styles/*": ["styles/*"]
    }
  }
}
```

---

**3. Example Usage - Product Page**

```typescript
// app/(shop)/products/[slug]/page.tsx
import { ProductGallery } from '@/components/product/ProductGallery';
import { AddToCartButton } from '@/components/product/AddToCartButton';
import { RelatedProducts } from '@/components/product/RelatedProducts';
import { ProductService } from '@/lib/services/ProductService';
import { formatPrice } from '@/lib/utils/format';
import type { Product } from '@/types/models/Product';

export default async function ProductPage({ params }: { params: { slug: string } }) {
  const product = await ProductService.getBySlug(params.slug);
  
  if (!product) {
    notFound();
  }
  
  return (
    <div>
      <ProductGallery images={product.images} />
      <div>
        <h1>{product.name}</h1>
        <p>{product.description}</p>
        <p className="price">{formatPrice(product.price)}</p>
        <AddToCartButton productId={product.id} />
      </div>
      <RelatedProducts categoryId={product.categoryId} />
    </div>
  );
}
```

---

**4. Service Layer Pattern**

```typescript
// lib/services/OrderService.ts
import { OrderRepository } from '@/lib/database/repositories/OrderRepository';
import { PaymentService } from './PaymentService';
import { EmailService } from './EmailService';
import { InventoryService } from './InventoryService';

export class OrderService {
  static async createOrder(userId: string, items: CartItem[]) {
    // Business logic
    const total = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
    
    // Check inventory
    for (const item of items) {
      const available = await InventoryService.checkStock(item.productId, item.quantity);
      if (!available) {
        throw new Error(`Insufficient stock for ${item.productName}`);
      }
    }
    
    // Create order
    const order = await OrderRepository.create({
      userId,
      items,
      total,
      status: 'pending'
    });
    
    // Process payment
    const payment = await PaymentService.charge(order.id, total);
    
    if (payment.success) {
      // Update inventory
      await InventoryService.decrementStock(items);
      
      // Send confirmation email
      await EmailService.sendOrderConfirmation(order);
      
      // Update order status
      await OrderRepository.updateStatus(order.id, 'confirmed');
    }
    
    return order;
  }
}
```

---

**5. Repository Pattern**

```typescript
// lib/database/repositories/OrderRepository.ts
import { prisma } from '@/lib/database/prisma';
import type { Order, CreateOrderInput } from '@/types/models/Order';

export class OrderRepository {
  static async create(data: CreateOrderInput): Promise<Order> {
    return prisma.order.create({
      data: {
        userId: data.userId,
        total: data.total,
        status: data.status,
        items: {
          create: data.items
        }
      },
      include: {
        items: true,
        user: true
      }
    });
  }
  
  static async findById(id: string): Promise<Order | null> {
    return prisma.order.findUnique({
      where: { id },
      include: {
        items: {
          include: {
            product: true
          }
        },
        user: true
      }
    });
  }
  
  static async findByUser(userId: string): Promise<Order[]> {
    return prisma.order.findMany({
      where: { userId },
      include: {
        items: true
      },
      orderBy: {
        createdAt: 'desc'
      }
    });
  }
  
  static async updateStatus(id: string, status: string): Promise<Order> {
    return prisma.order.update({
      where: { id },
      data: { status }
    });
  }
}
```

---

**6. API Routes with Services**

```typescript
// app/api/orders/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { OrderService } from '@/lib/services/OrderService';
import { auth } from '@/lib/auth/nextauth';

export async function POST(request: NextRequest) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    const { items } = await request.json();
    
    const order = await OrderService.createOrder(session.user.id, items);
    
    return NextResponse.json({ order }, { status: 201 });
  } catch (error) {
    console.error('Order creation failed:', error);
    return NextResponse.json(
      { error: error.message || 'Failed to create order' },
      { status: 500 }
    );
  }
}

export async function GET(request: NextRequest) {
  try {
    const session = await auth();
    
    if (!session?.user) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    const orders = await OrderService.getUserOrders(session.user.id);
    
    return NextResponse.json({ orders });
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to fetch orders' },
      { status: 500 }
    );
  }
}
```

---

**7. Type Safety**

```typescript
// types/models/Product.ts
export interface Product {
  id: string;
  name: string;
  slug: string;
  description: string;
  price: number;
  stock: number;
  images: string[];
  categoryId: string;
  category?: Category;
  variants?: ProductVariant[];
  reviews?: Review[];
  createdAt: Date;
  updatedAt: Date;
}

export interface CreateProductInput {
  name: string;
  slug: string;
  description: string;
  price: number;
  stock: number;
  categoryId: string;
  images: string[];
}

// types/api/requests.ts
export interface CreateOrderRequest {
  items: {
    productId: string;
    quantity: number;
  }[];
  shippingAddress: Address;
  paymentMethod: string;
}

// types/api/responses.ts
export interface OrderResponse {
  order: Order;
  paymentIntent?: string;
}

export interface ErrorResponse {
  error: string;
  code?: string;
  details?: any;
}
```

---

**8. Configuration Management**

```typescript
// config/site.ts
export const siteConfig = {
  name: 'Acme Store',
  description: 'Modern e-commerce platform',
  url: process.env.NEXT_PUBLIC_SITE_URL || 'http://localhost:3000',
  logo: '/images/logo.png',
  links: {
    twitter: 'https://twitter.com/acmestore',
    github: 'https://github.com/acmestore'
  }
};

// config/navigation.ts
export const mainNav = [
  {
    title: 'Products',
    href: '/products'
  },
  {
    title: 'Categories',
    href: '/categories',
    items: [
      { title: 'Electronics', href: '/categories/electronics' },
      { title: 'Clothing', href: '/categories/clothing' },
      { title: 'Books', href: '/categories/books' }
    ]
  },
  {
    title: 'About',
    href: '/about'
  }
];

// config/env.ts - Environment variable validation
import { z } from 'zod';

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  NEXTAUTH_SECRET: z.string().min(32),
  NEXTAUTH_URL: z.string().url(),
  STRIPE_SECRET_KEY: z.string(),
  STRIPE_WEBHOOK_SECRET: z.string(),
  EMAIL_SERVER: z.string(),
  EMAIL_FROM: z.string().email()
});

export const env = envSchema.parse(process.env);
```

---

**9. Testing Structure**

```typescript
// tests/unit/services/ProductService.test.ts
import { describe, it, expect, vi } from 'vitest';
import { ProductService } from '@/lib/services/ProductService';
import { ProductRepository } from '@/lib/database/repositories/ProductRepository';

vi.mock('@/lib/database/repositories/ProductRepository');

describe('ProductService', () => {
  it('should fetch product by slug', async () => {
    const mockProduct = {
      id: '1',
      name: 'Test Product',
      slug: 'test-product',
      price: 99.99
    };
    
    vi.mocked(ProductRepository.findBySlug).mockResolvedValue(mockProduct);
    
    const result = await ProductService.getBySlug('test-product');
    
    expect(result).toEqual(mockProduct);
    expect(ProductRepository.findBySlug).toHaveBeenCalledWith('test-product');
  });
});

// tests/e2e/checkout.spec.ts
import { test, expect } from '@playwright/test';

test('complete checkout flow', async ({ page }) => {
  await page.goto('/products');
  await page.click('text=Add to Cart');
  await page.click('text=Checkout');
  
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="address"]', '123 Main St');
  await page.click('text=Complete Order');
  
  await expect(page).toHaveURL('/checkout/confirmation');
  await expect(page.locator('text=Order Confirmed')).toBeVisible();
});
```

---

**10. Benefits of This Structure**

✅ **Scalability**: Easy to add new features without affecting existing code  
✅ **Maintainability**: Clear separation of concerns makes code easy to understand  
✅ **Testability**: Each layer (components, services, repositories) can be tested independently  
✅ **Team Collaboration**: Multiple developers can work on different features without conflicts  
✅ **Type Safety**: Full TypeScript coverage prevents runtime errors  
✅ **Performance**: Optimized for Next.js caching strategies and React Server Components  
✅ **Code Reusability**: Shared components and utilities reduce duplication  
✅ **Security**: Sensitive logic stays on server, proper separation of client/server code  

---

### Q11: How do you structure a large-scale Next.js application?

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

---

### Q29.1: How do you import and use images in Next.js?

**Answer:**

Next.js provides multiple ways to import and use images depending on your use case:

#### 1. Static Image Imports (Recommended)

Import images directly as JavaScript modules. Next.js automatically determines width, height, and generates blur placeholders.

```javascript
import Image from 'next/image';
import heroImage from '@/public/hero.jpg';
import profilePic from '../public/profile.png';

export default function Page() {
  return (
    <div>
      {/* Static import - width/height automatically determined */}
      <Image
        src={heroImage}
        alt="Hero image"
        placeholder="blur" // Automatic blur-up while loading
        priority // Load immediately for above-the-fold content
      />
      
      {/* Can override dimensions */}
      <Image
        src={profilePic}
        alt="Profile"
        width={400}
        height={400}
      />
    </div>
  );
}
```

**Benefits of static imports:**
- ✅ Automatic width/height detection
- ✅ Built-in blur placeholder
- ✅ Type safety
- ✅ Automatic optimization at build time
- ✅ No configuration needed

---

#### 2. Public Folder Images (String Path)

Place images in the `public` folder and reference them with a string path.

```javascript
import Image from 'next/image';

export default function Page() {
  return (
    <div>
      {/* Image in public/images/banner.jpg */}
      <Image
        src="/images/banner.jpg"
        alt="Banner"
        width={1920}
        height={1080}
      />
      
      {/* Image in public/logo.png */}
      <Image
        src="/logo.png"
        alt="Logo"
        width={200}
        height={100}
      />
    </div>
  );
}
```

**Project structure:**
```
my-app/
├── public/
│   ├── logo.png
│   ├── favicon.ico
│   └── images/
│       ├── banner.jpg
│       ├── hero.png
│       └── products/
│           ├── product1.jpg
│           └── product2.jpg
└── app/
    └── page.js
```

**Important notes:**
- ⚠️ Must provide width and height
- ⚠️ No automatic blur placeholder
- ✅ Accessible via `/filename.ext`
- ✅ Good for SEO (sitemap, robots.txt)

---

#### 3. Remote/External Images

Import images from external URLs (CDN, API, cloud storage).

**Step 1: Configure allowed domains**

```javascript
// next.config.js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'images.unsplash.com',
        pathname: '/**'
      },
      {
        protocol: 'https',
        hostname: 'res.cloudinary.com',
        pathname: '/your-account/**'
      },
      {
        protocol: 'https',
        hostname: 'cdn.example.com',
        port: '',
        pathname: '/uploads/**'
      }
    ],
    // Legacy format (still supported)
    domains: ['example.com', 'cdn.example.com']
  }
};
```

**Step 2: Use external URLs**

```javascript
import Image from 'next/image';

export default function Page() {
  return (
    <div>
      <Image
        src="https://images.unsplash.com/photo-123456789"
        alt="Photo from Unsplash"
        width={800}
        height={600}
      />
      
      <Image
        src="https://res.cloudinary.com/demo/image/upload/sample.jpg"
        alt="Cloudinary image"
        width={1200}
        height={800}
      />
    </div>
  );
}
```

**Dynamic external images:**

```javascript
// Fetch from API
export default async function ProductPage({ params }) {
  const product = await fetch(`/api/products/${params.id}`).then(r => r.json());
  
  return (
    <Image
      src={product.imageUrl} // https://cdn.example.com/products/123.jpg
      alt={product.name}
      width={600}
      height={600}
    />
  );
}
```

---

#### 4. Dynamic Imports with Import Aliases

Using TypeScript/JavaScript path aliases for cleaner imports:

```javascript
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"],
      "@/images/*": ["public/images/*"],
      "@/assets/*": ["assets/*"]
    }
  }
}

// Usage
import Image from 'next/image';
import heroImg from '@/public/hero.jpg';
import logo from '@/public/logo.png';

export default function Page() {
  return (
    <>
      <Image src={heroImg} alt="Hero" />
      <Image src={logo} alt="Logo" width={150} height={50} />
    </>
  );
}
```

---

#### 5. SVG Images

**Method 1: As Image Component**
```javascript
import Image from 'next/image';

export default function Page() {
  return (
    <Image
      src="/icon.svg"
      alt="Icon"
      width={24}
      height={24}
    />
  );
}
```

**Method 2: Direct SVG Import (Inline)**
```javascript
// Install SVGR
// npm install @svgr/webpack

// next.config.js
module.exports = {
  webpack(config) {
    config.module.rules.push({
      test: /\.svg$/,
      use: ['@svgr/webpack']
    });
    return config;
  }
};

// Usage - SVG as React component
import Logo from '@/public/logo.svg';

export default function Page() {
  return (
    <div>
      <Logo width={200} height={100} />
    </div>
  );
}
```

---

#### 6. Responsive Images with Different Sources

```javascript
import Image from 'next/image';
import heroDesktop from '@/public/hero-desktop.jpg';
import heroMobile from '@/public/hero-mobile.jpg';

export default function ResponsiveHero() {
  return (
    <div>
      {/* Desktop */}
      <div className="hidden md:block">
        <Image
          src={heroDesktop}
          alt="Hero"
          priority
        />
      </div>
      
      {/* Mobile */}
      <div className="block md:hidden">
        <Image
          src={heroMobile}
          alt="Hero"
          priority
        />
      </div>
    </div>
  );
}
```

**Using sizes prop for responsive:**
```javascript
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1920}
  height={1080}
  sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw"
  // Browser loads appropriate size based on viewport
/>
```

---

#### 7. Image Arrays and Galleries

```javascript
import Image from 'next/image';
import img1 from '@/public/gallery/1.jpg';
import img2 from '@/public/gallery/2.jpg';
import img3 from '@/public/gallery/3.jpg';

const images = [img1, img2, img3];

export default function Gallery() {
  return (
    <div className="grid grid-cols-3 gap-4">
      {images.map((img, index) => (
        <Image
          key={index}
          src={img}
          alt={`Gallery image ${index + 1}`}
          placeholder="blur"
        />
      ))}
    </div>
  );
}
```

**Dynamic gallery from API:**
```javascript
export default async function ProductGallery({ productId }) {
  const images = await fetch(`/api/products/${productId}/images`)
    .then(r => r.json());
  
  return (
    <div className="grid grid-cols-3 gap-4">
      {images.map((img) => (
        <Image
          key={img.id}
          src={img.url}
          alt={img.alt}
          width={400}
          height={400}
        />
      ))}
    </div>
  );
}
```

---

#### 8. Background Images

**Method 1: Using fill prop**
```javascript
import Image from 'next/image';
import bgImage from '@/public/background.jpg';

export default function Hero() {
  return (
    <div className="relative w-full h-screen">
      <Image
        src={bgImage}
        alt="Background"
        fill
        style={{ objectFit: 'cover' }}
        priority
      />
      <div className="relative z-10">
        <h1>Content on top</h1>
      </div>
    </div>
  );
}
```

**Method 2: Using CSS**
```javascript
// For simple backgrounds without optimization
export default function Hero() {
  return (
    <div
      style={{
        backgroundImage: 'url(/background.jpg)',
        backgroundSize: 'cover',
        backgroundPosition: 'center'
      }}
      className="w-full h-screen"
    >
      <h1>Content</h1>
    </div>
  );
}
```

---

#### 9. Image with Fallback

```javascript
'use client';
import Image from 'next/image';
import { useState } from 'react';

export default function ImageWithFallback({ src, fallbackSrc, ...props }) {
  const [imgSrc, setImgSrc] = useState(src);
  
  return (
    <Image
      {...props}
      src={imgSrc}
      onError={() => setImgSrc(fallbackSrc)}
    />
  );
}

// Usage
<ImageWithFallback
  src="/user-avatar.jpg"
  fallbackSrc="/default-avatar.png"
  alt="User avatar"
  width={100}
  height={100}
/>
```

---

#### 10. Import Best Practices

**✅ DO:**
```javascript
// Use static imports for local images
import heroImage from '@/public/hero.jpg';

// Use path aliases for cleaner imports
import logo from '@/public/images/logo.png';

// Always provide alt text
<Image src={heroImage} alt="Descriptive alt text" />

// Use priority for above-the-fold images
<Image src={heroImage} alt="Hero" priority />

// Use placeholder="blur" for static imports
<Image src={heroImage} alt="Hero" placeholder="blur" />
```

**❌ DON'T:**
```javascript
// Don't use relative paths for imports
import heroImage from '../../../public/hero.jpg'; // ❌

// Don't forget alt text
<Image src={heroImage} /> // ❌

// Don't use Image for all images - use img for icons sometimes
<Image src="/icon.svg" width={16} height={16} /> // ❌ Overkill for tiny icons

// Don't forget to configure remote domains
<Image src="https://example.com/image.jpg" /> // ❌ Will error without config
```

---

#### 11. Common Import Patterns

**Pattern 1: Organized by feature**
```
public/
├── images/
│   ├── products/
│   │   ├── product-1.jpg
│   │   └── product-2.jpg
│   ├── blog/
│   │   ├── post-1.jpg
│   │   └── post-2.jpg
│   └── common/
│       ├── logo.png
│       └── hero.jpg
```

```javascript
import Image from 'next/image';

// Import and use
<Image src="/images/products/product-1.jpg" width={500} height={500} alt="Product 1" />
<Image src="/images/blog/post-1.jpg" width={800} height={600} alt="Blog post" />
<Image src="/images/common/logo.png" width={200} height={100} alt="Logo" />
```

**Pattern 2: Constants file for images**
```javascript
// lib/constants/images.ts
export const IMAGES = {
  logo: '/images/logo.png',
  hero: '/images/hero.jpg',
  defaultAvatar: '/images/default-avatar.png',
  products: {
    placeholder: '/images/product-placeholder.jpg'
  }
};

// Usage
import Image from 'next/image';
import { IMAGES } from '@/lib/constants/images';

<Image src={IMAGES.logo} width={200} height={100} alt="Logo" />
<Image src={IMAGES.hero} width={1920} height={1080} alt="Hero" />
```

**Pattern 3: Image helper function**
```typescript
// lib/utils/images.ts
export function getImageUrl(path: string, size?: 'sm' | 'md' | 'lg') {
  const baseUrl = process.env.NEXT_PUBLIC_CDN_URL || '';
  const sizeParam = size ? `?size=${size}` : '';
  return `${baseUrl}${path}${sizeParam}`;
}

// Usage
import Image from 'next/image';
import { getImageUrl } from '@/lib/utils/images';

<Image
  src={getImageUrl('/products/chair.jpg', 'lg')}
  width={800}
  height={800}
  alt="Chair"
/>
```

---

#### Summary - Image Import Methods

| Method | Use Case | Pros | Cons |
|--------|----------|------|------|
| **Static Import** | Local images, known at build time | Auto width/height, blur placeholder | Increases bundle size slightly |
| **Public Folder** | Static assets, SEO files | Simple, predictable URLs | Manual width/height, no auto blur |
| **Remote URLs** | CDN, API, external sources | Dynamic, scalable | Requires domain config |
| **Dynamic Import** | Conditional loading | Reduces initial bundle | More complex |
| **SVG Import** | Icons, logos | Inline, styleable | Requires webpack config |

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
