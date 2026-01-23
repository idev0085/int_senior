# CSS - Top 50 Questions & Answers (Beginner to Advanced)

## Table of Contents
1. [CSS Basics](#css-basics)
2. [Selectors & Specificity](#selectors--specificity)
3. [Box Model & Layout](#box-model--layout)
4. [Flexbox](#flexbox)
5. [Grid](#grid)
6. [Responsive Design](#responsive-design)
7. [Animations & Transitions](#animations--transitions)
8. [Advanced Techniques](#advanced-techniques)
9. [CSS Architecture & Best Practices](#css-architecture--best-practices)
10. [Modern CSS Features](#modern-css-features)
11. [Sass/SCSS](#sassscss)
12. [Most Useful CSS Styles & Utilities](#most-useful-css-styles--utilities)

---

## CSS Basics

### Q1: What is CSS and how do you include it in HTML?

**Answer:**
CSS (Cascading Style Sheets) is used to style and layout web pages. It controls colors, fonts, spacing, positioning, and visual effects.

**Three Ways to Include CSS:**

**1. Inline CSS (Not Recommended):**
```html
<p style="color: blue; font-size: 16px;">Styled text</p>
```

**2. Internal CSS (In `<head>` tag):**
```html
<!DOCTYPE html>
<html>
<head>
    <style>
        p {
            color: blue;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <p>Styled text</p>
</body>
</html>
```

**3. External CSS (Best Practice):**
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <p>Styled text</p>
</body>
</html>
```

```css
/* styles.css */
p {
    color: blue;
    font-size: 16px;
}
```

---

### Q2: What are CSS selectors and how do they work?

**Answer:**

**Basic Selectors:**

```css
/* Element/Type Selector */
p {
    color: blue;
}

/* Class Selector */
.highlight {
    background-color: yellow;
}

/* ID Selector */
#header {
    font-size: 24px;
}

/* Universal Selector */
* {
    margin: 0;
    padding: 0;
}
```

**Usage:**
```html
<p>Blue paragraph</p>
<p class="highlight">Highlighted paragraph</p>
<div id="header">Header</div>
```

**Attribute Selectors:**
```css
/* Has attribute */
[disabled] {
    opacity: 0.5;
}

/* Exact value */
[type="text"] {
    border: 1px solid gray;
}

/* Contains value */
[class*="btn"] {
    cursor: pointer;
}

/* Starts with */
[href^="https"] {
    color: green;
}

/* Ends with */
[src$=".pdf"] {
    background: url('pdf-icon.png');
}
```

**Combinators:**
```css
/* Descendant (any level) */
div p {
    color: blue;
}

/* Direct child */
div > p {
    color: red;
}

/* Adjacent sibling (immediately after) */
h1 + p {
    font-weight: bold;
}

/* General sibling (any sibling after) */
h1 ~ p {
    color: gray;
}
```

**Pseudo-classes:**
```css
/* Link states */
a:link { color: blue; }
a:visited { color: purple; }
a:hover { color: red; }
a:active { color: orange; }

/* Form states */
input:focus { border-color: blue; }
input:disabled { opacity: 0.5; }
input:checked { background: green; }

/* Structural */
li:first-child { font-weight: bold; }
li:last-child { border-bottom: none; }
li:nth-child(odd) { background: #f0f0f0; }
li:nth-child(3n) { color: blue; }

/* Other */
p:not(.special) { color: gray; }
div:empty { display: none; }
```

**Pseudo-elements:**
```css
/* Before and After */
p::before {
    content: "→ ";
    color: blue;
}

p::after {
    content: " ←";
    color: red;
}

/* First letter/line */
p::first-letter {
    font-size: 2em;
    font-weight: bold;
}

p::first-line {
    color: blue;
}

/* Selection */
::selection {
    background: yellow;
    color: black;
}
```

---

### Q3: What is the CSS Box Model?

**Answer:**

Every element is a box with four areas:

```
┌─────────────────────────────────────┐
│          MARGIN (transparent)        │
│  ┌───────────────────────────────┐  │
│  │    BORDER                     │  │
│  │  ┌─────────────────────────┐ │  │
│  │  │    PADDING              │ │  │
│  │  │  ┌───────────────────┐  │ │  │
│  │  │  │    CONTENT        │  │ │  │
│  │  │  │  (width x height) │  │ │  │
│  │  │  └───────────────────┘  │ │  │
│  │  └─────────────────────────┘ │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

**Example:**
```css
.box {
    /* Content */
    width: 200px;
    height: 100px;
    
    /* Padding (inside) */
    padding: 20px;
    
    /* Border */
    border: 5px solid black;
    
    /* Margin (outside) */
    margin: 10px;
}

/* Total width = width + padding-left + padding-right + border-left + border-right
   Total width = 200 + 20 + 20 + 5 + 5 = 250px */
```

**Box-Sizing Property:**
```css
/* Default: content-box */
.box1 {
    box-sizing: content-box;
    width: 200px;
    padding: 20px;
    border: 5px solid black;
    /* Total width = 200 + 20 + 20 + 5 + 5 = 250px */
}

/* Better: border-box */
.box2 {
    box-sizing: border-box;
    width: 200px;
    padding: 20px;
    border: 5px solid black;
    /* Total width = 200px (padding and border included!) */
}

/* Apply to all elements (recommended) */
* {
    box-sizing: border-box;
}
```

---

## Selectors & Specificity

### Q4: What is CSS specificity and how does it work?

**Answer:**

Specificity determines which CSS rule applies when multiple rules target the same element.

**Specificity Hierarchy (Highest to Lowest):**

1. **Inline styles** (1000 points)
2. **IDs** (100 points)
3. **Classes, attributes, pseudo-classes** (10 points)
4. **Elements, pseudo-elements** (1 point)

**Calculation Examples:**
```css
/* Specificity: 0001 (1 element) */
p {
    color: blue;
}

/* Specificity: 0010 (1 class) */
.text {
    color: red;
}

/* Specificity: 0100 (1 ID) */
#main {
    color: green;
}

/* Specificity: 1000 (inline) */
/* <p style="color: purple;">Text</p> */

/* Specificity: 0111 (1 ID + 1 class + 1 element) */
#main .text p {
    color: orange;
}

/* Specificity: 0021 (2 classes + 1 element) */
.container .text p {
    color: pink;
}
```

**Example Conflict:**
```html
<div id="container" class="wrapper">
    <p class="text">What color am I?</p>
</div>
```

```css
/* 0001 - Least specific */
p { color: blue; }

/* 0010 - More specific */
.text { color: red; }

/* 0011 - Even more specific */
.wrapper .text { color: green; }

/* 0111 - Most specific (WINS!) */
#container .wrapper .text { color: purple; }
```

**The `!important` Rule (Nuclear Option):**
```css
p {
    color: blue !important; /* Overrides everything! */
}

#main .text {
    color: red; /* Doesn't apply because !important wins */
}
```

**Best Practices:**
- Avoid `!important` when possible
- Keep specificity low for maintainability
- Use classes over IDs for styling
- Be consistent with selector patterns

---

### Q5: What are CSS variables (Custom Properties)?

**Answer:**

CSS variables allow you to store and reuse values throughout your stylesheet.

**Syntax:**
```css
:root {
    /* Define variables */
    --primary-color: #3498db;
    --secondary-color: #2ecc71;
    --font-size-base: 16px;
    --spacing-unit: 8px;
    --max-width: 1200px;
}

/* Use variables */
.button {
    background-color: var(--primary-color);
    font-size: var(--font-size-base);
    padding: calc(var(--spacing-unit) * 2);
}

.header {
    color: var(--secondary-color);
    max-width: var(--max-width);
}
```

**With Fallback Values:**
```css
.element {
    /* Use fallback if variable not defined */
    color: var(--text-color, black);
    font-size: var(--font-size, 16px);
}
```

**Scoped Variables:**
```css
:root {
    --button-color: blue;
}

.button-primary {
    --button-color: green; /* Override for this component */
    background: var(--button-color);
}
```

**Real-World Example (Theme):**
```css
:root {
    /* Light theme (default) */
    --bg-color: #ffffff;
    --text-color: #333333;
    --border-color: #dddddd;
}

[data-theme="dark"] {
    /* Dark theme */
    --bg-color: #1a1a1a;
    --text-color: #f0f0f0;
    --border-color: #444444;
}

body {
    background-color: var(--bg-color);
    color: var(--text-color);
}

.card {
    border: 1px solid var(--border-color);
}
```

**JavaScript Integration:**
```html
<style>
    :root {
        --theme-color: blue;
    }
    
    .box {
        background: var(--theme-color);
    }
</style>

<script>
    // Get variable value
    const root = document.documentElement;
    const color = getComputedStyle(root).getPropertyValue('--theme-color');
    
    // Set variable value
    root.style.setProperty('--theme-color', 'red');
</script>
```

---

## Box Model & Layout

### Q6: What are the CSS positioning properties?

**Answer:**

**1. Static (Default):**
```css
.static {
    position: static; /* Normal document flow */
    /* top, right, bottom, left have no effect */
}
```

**2. Relative:**
```css
.relative {
    position: relative;
    top: 20px;    /* Move 20px down from original position */
    left: 30px;   /* Move 30px right from original position */
    /* Original space still preserved */
}
```

**3. Absolute:**
```css
.container {
    position: relative; /* Positioning context */
}

.absolute {
    position: absolute;
    top: 0;
    right: 0;
    /* Positioned relative to nearest positioned ancestor */
    /* Removed from document flow */
}
```

**4. Fixed:**
```css
.fixed {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    /* Positioned relative to viewport */
    /* Stays in place when scrolling */
}
```

**5. Sticky:**
```css
.sticky {
    position: sticky;
    top: 0;
    /* Acts like relative until scroll threshold */
    /* Then acts like fixed */
}
```

**Practical Examples:**

**Modal Overlay:**
```css
.overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    z-index: 1000;
}

.modal {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: white;
    padding: 20px;
    z-index: 1001;
}
```

**Sticky Header:**
```css
.header {
    position: sticky;
    top: 0;
    background: white;
    z-index: 100;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
```

**Tooltip:**
```css
.tooltip-container {
    position: relative;
}

.tooltip {
    position: absolute;
    bottom: 100%;
    left: 50%;
    transform: translateX(-50%);
    background: black;
    color: white;
    padding: 5px 10px;
    border-radius: 4px;
    white-space: nowrap;
}
```

---

### Q7: What is the difference between display properties?

**Answer:**

**Display: Block**
```css
.block {
    display: block;
    /* Takes full width available */
    /* Starts on new line */
    /* Can set width and height */
}

/* Block elements: div, p, h1-h6, ul, li, section, etc. */
```

**Display: Inline**
```css
.inline {
    display: inline;
    /* Takes only necessary width */
    /* Doesn't start on new line */
    /* Cannot set width and height */
    /* Vertical margin/padding doesn't work properly */
}

/* Inline elements: span, a, strong, em, img, etc. */
```

**Display: Inline-Block**
```css
.inline-block {
    display: inline-block;
    /* Flows inline like inline elements */
    /* But can set width and height like block */
    /* Best of both worlds */
}
```

**Comparison:**
```html
<style>
    .block { 
        display: block; 
        width: 200px; 
        height: 50px; 
        background: lightblue; 
    }
    .inline { 
        display: inline; 
        width: 200px;  /* Doesn't work! */
        height: 50px;  /* Doesn't work! */
        background: lightgreen; 
    }
    .inline-block { 
        display: inline-block; 
        width: 200px;  /* Works! */
        height: 50px;  /* Works! */
        background: lightcoral; 
    }
</style>

<div class="block">Block 1</div>
<div class="block">Block 2</div>

<span class="inline">Inline 1</span>
<span class="inline">Inline 2</span>
<span class="inline">Inline 3</span>

<div class="inline-block">Inline-Block 1</div>
<div class="inline-block">Inline-Block 2</div>
```

**Display: None**
```css
.hidden {
    display: none;
    /* Completely removed from layout */
    /* No space occupied */
}

.invisible {
    visibility: hidden;
    /* Hidden but space still occupied */
}
```

**Display: Flex and Grid** (covered in separate sections)

---

## Flexbox

### Q8: What is Flexbox and how do you use it?

**Answer:**

Flexbox is a one-dimensional layout method for arranging items in rows or columns.

**Container Properties:**
```css
.container {
    display: flex; /* or inline-flex */
    
    /* Direction */
    flex-direction: row; /* row | row-reverse | column | column-reverse */
    
    /* Wrap */
    flex-wrap: nowrap; /* nowrap | wrap | wrap-reverse */
    
    /* Shorthand for direction + wrap */
    flex-flow: row wrap;
    
    /* Horizontal alignment */
    justify-content: flex-start; /* flex-start | flex-end | center | space-between | space-around | space-evenly */
    
    /* Vertical alignment */
    align-items: stretch; /* stretch | flex-start | flex-end | center | baseline */
    
    /* Multi-line alignment */
    align-content: flex-start; /* flex-start | flex-end | center | space-between | space-around | stretch */
    
    /* Gap between items */
    gap: 20px; /* or row-gap and column-gap */
}
```

**Item Properties:**
```css
.item {
    /* Growth factor */
    flex-grow: 0; /* default */
    
    /* Shrink factor */
    flex-shrink: 1; /* default */
    
    /* Base size */
    flex-basis: auto; /* auto | length | percentage */
    
    /* Shorthand: flex-grow flex-shrink flex-basis */
    flex: 1 1 auto;
    
    /* Override align-items for this item */
    align-self: center; /* auto | flex-start | flex-end | center | baseline | stretch */
    
    /* Order */
    order: 0; /* default */
}
```

**Common Patterns:**

**1. Center Content:**
```css
.center {
    display: flex;
    justify-content: center; /* Horizontal */
    align-items: center;     /* Vertical */
}
```

**2. Equal Width Columns:**
```css
.container {
    display: flex;
}

.item {
    flex: 1; /* All items same width */
}
```

**3. Navigation Bar:**
```css
.nav {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px;
}

.nav-links {
    display: flex;
    gap: 20px;
    list-style: none;
}
```

**4. Card Layout:**
```css
.card-container {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
}

.card {
    flex: 1 1 300px; /* Grow, shrink, min 300px */
    max-width: 400px;
}
```

**5. Holy Grail Layout:**
```html
<style>
    .container {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
    }
    
    .header, .footer {
        background: lightgray;
        padding: 20px;
    }
    
    .main {
        display: flex;
        flex: 1; /* Take remaining space */
    }
    
    .sidebar {
        flex: 0 0 200px; /* Fixed width */
        background: lightblue;
    }
    
    .content {
        flex: 1; /* Take remaining space */
        padding: 20px;
    }
</style>

<div class="container">
    <header class="header">Header</header>
    <div class="main">
        <aside class="sidebar">Sidebar</aside>
        <main class="content">Content</main>
    </div>
    <footer class="footer">Footer</footer>
</div>
```

---

## Grid

### Q9: What is CSS Grid and how does it differ from Flexbox?

**Answer:**

**Grid vs Flexbox:**
- **Flexbox**: One-dimensional (row OR column)
- **Grid**: Two-dimensional (rows AND columns)

**Basic Grid:**
```css
.container {
    display: grid;
    
    /* Define columns */
    grid-template-columns: 200px 200px 200px; /* 3 columns, 200px each */
    /* or */
    grid-template-columns: 1fr 1fr 1fr; /* 3 equal columns */
    /* or */
    grid-template-columns: repeat(3, 1fr); /* Shorthand */
    
    /* Define rows */
    grid-template-rows: 100px 200px; /* 2 rows */
    
    /* Gap between items */
    gap: 20px; /* or row-gap and column-gap */
}
```

**Fractional Units (fr):**
```css
.container {
    display: grid;
    grid-template-columns: 1fr 2fr 1fr;
    /* First column: 1 part */
    /* Second column: 2 parts (twice as wide) */
    /* Third column: 1 part */
}
```

**Auto-fit and Auto-fill:**
```css
.container {
    display: grid;
    
    /* Auto-fit: Creates as many columns as fit, collapses empty */
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    
    /* Auto-fill: Creates as many columns as fit, keeps empty */
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    
    gap: 20px;
}
```

**Grid Template Areas:**
```css
.container {
    display: grid;
    grid-template-columns: 200px 1fr 200px;
    grid-template-rows: auto 1fr auto;
    grid-template-areas:
        "header header header"
        "sidebar content ads"
        "footer footer footer";
    gap: 20px;
    min-height: 100vh;
}

.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.content { grid-area: content; }
.ads { grid-area: ads; }
.footer { grid-area: footer; }
```

**Item Placement:**
```css
.item {
    /* Column placement */
    grid-column-start: 1;
    grid-column-end: 3;
    /* or shorthand */
    grid-column: 1 / 3; /* Span from line 1 to 3 */
    /* or */
    grid-column: 1 / span 2; /* Start at 1, span 2 columns */
    
    /* Row placement */
    grid-row: 2 / 4;
    
    /* Super shorthand */
    grid-area: 2 / 1 / 4 / 3; /* row-start / col-start / row-end / col-end */
}
```

**Alignment:**
```css
.container {
    /* Align items within their cells */
    justify-items: start; /* start | end | center | stretch */
    align-items: start; /* start | end | center | stretch */
    
    /* Align entire grid within container */
    justify-content: center; /* start | end | center | space-between | space-around | space-evenly */
    align-content: center; /* start | end | center | space-between | space-around | space-evenly */
}

.item {
    /* Override for individual item */
    justify-self: center;
    align-self: center;
}
```

**Practical Examples:**

**1. Responsive Image Gallery:**
```css
.gallery {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 15px;
    padding: 20px;
}

.gallery img {
    width: 100%;
    height: 250px;
    object-fit: cover;
    border-radius: 8px;
}
```

**2. Dashboard Layout:**
```css
.dashboard {
    display: grid;
    grid-template-columns: repeat(12, 1fr);
    gap: 20px;
    padding: 20px;
}

.widget-large {
    grid-column: span 8;
}

.widget-small {
    grid-column: span 4;
}

.widget-full {
    grid-column: span 12;
}
```

**3. Magazine Layout:**
```css
.magazine {
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    grid-auto-rows: 200px;
    gap: 10px;
}

.article-1 {
    grid-column: 1 / 4;
    grid-row: 1 / 3;
}

.article-2 {
    grid-column: 4 / 7;
    grid-row: 1 / 2;
}

.article-3 {
    grid-column: 4 / 7;
    grid-row: 2 / 3;
}
```

---

## Responsive Design

### Q10: How do you create responsive designs with media queries?

**Answer:**

**Basic Media Query Syntax:**
```css
/* Mobile-first approach (recommended) */
/* Base styles for mobile */
.container {
    width: 100%;
    padding: 15px;
}

/* Tablet */
@media (min-width: 768px) {
    .container {
        max-width: 750px;
        margin: 0 auto;
    }
}

/* Desktop */
@media (min-width: 1024px) {
    .container {
        max-width: 960px;
    }
}

/* Large Desktop */
@media (min-width: 1200px) {
    .container {
        max-width: 1140px;
    }
}
```

**Common Breakpoints:**
```css
/* Extra small devices (phones, less than 576px) */
/* No media query needed (mobile-first) */

/* Small devices (landscape phones, 576px and up) */
@media (min-width: 576px) { }

/* Medium devices (tablets, 768px and up) */
@media (min-width: 768px) { }

/* Large devices (desktops, 992px and up) */
@media (min-width: 992px) { }

/* Extra large devices (large desktops, 1200px and up) */
@media (min-width: 1200px) { }
```

**Media Query Features:**
```css
/* Width */
@media (max-width: 768px) { }
@media (min-width: 769px) { }
@media (min-width: 768px) and (max-width: 1024px) { }

/* Height */
@media (min-height: 600px) { }

/* Orientation */
@media (orientation: portrait) { }
@media (orientation: landscape) { }

/* Aspect Ratio */
@media (min-aspect-ratio: 16/9) { }

/* Resolution (for high-DPI screens) */
@media (min-resolution: 2dppx) { }

/* Hover capability */
@media (hover: hover) {
    button:hover {
        background: blue;
    }
}

/* Pointer type */
@media (pointer: coarse) {
    /* Touch screens - make buttons bigger */
    button {
        min-height: 44px;
    }
}

/* Prefers color scheme */
@media (prefers-color-scheme: dark) {
    body {
        background: #1a1a1a;
        color: #f0f0f0;
    }
}

/* Prefers reduced motion */
@media (prefers-reduced-motion: reduce) {
    * {
        animation: none !important;
        transition: none !important;
    }
}
```

**Responsive Typography:**
```css
body {
    font-size: 14px;
}

@media (min-width: 768px) {
    body {
        font-size: 16px;
    }
}

@media (min-width: 1200px) {
    body {
        font-size: 18px;
    }
}

/* Fluid typography using clamp() */
h1 {
    font-size: clamp(24px, 5vw, 48px);
    /* min: 24px, preferred: 5% of viewport width, max: 48px */
}
```

**Complete Responsive Example:**
```html
<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    
    body {
        font-family: Arial, sans-serif;
    }
    
    /* Mobile styles */
    .container {
        width: 100%;
        padding: 15px;
    }
    
    .nav {
        display: flex;
        flex-direction: column;
    }
    
    .grid {
        display: grid;
        grid-template-columns: 1fr;
        gap: 15px;
    }
    
    .image {
        width: 100%;
        height: auto;
    }
    
    .hide-mobile {
        display: none;
    }
    
    /* Tablet */
    @media (min-width: 768px) {
        .container {
            max-width: 750px;
            margin: 0 auto;
        }
        
        .nav {
            flex-direction: row;
            justify-content: space-between;
        }
        
        .grid {
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
        }
    }
    
    /* Desktop */
    @media (min-width: 1024px) {
        .container {
            max-width: 960px;
        }
        
        .grid {
            grid-template-columns: repeat(3, 1fr);
        }
        
        .hide-mobile {
            display: block;
        }
    }
    
    /* Large Desktop */
    @media (min-width: 1200px) {
        .container {
            max-width: 1140px;
        }
        
        .grid {
            grid-template-columns: repeat(4, 1fr);
        }
    }
    
    /* Print styles */
    @media print {
        .no-print {
            display: none;
        }
        
        a {
            text-decoration: none;
            color: black;
        }
        
        a::after {
            content: " (" attr(href) ")";
        }
    }
</style>
```

---

### Q11: What are CSS units for fluid/responsive design (vw, vh, rem, em, etc.)?

**Answer:**

CSS units are essential for creating fluid, responsive designs. Understanding the difference between absolute and relative units is crucial.

---

#### **Absolute Units**

**px (Pixels):**
- Fixed size, doesn't scale
- 1px = 1/96th of an inch
- Good for: borders, small fixed elements

```css
.box {
    width: 300px;        /* Fixed width */
    border: 2px solid;   /* Fixed border */
}
```

**Problems with px:**
```css
/* Not responsive - breaks on small screens */
.container {
    width: 1200px;  /* ❌ Too wide for mobile */
    font-size: 16px; /* ❌ Doesn't respect user font size preferences */
}
```

---

#### **Relative Units - Font-Based**

**em:**
- Relative to **parent element's font-size**
- Compounds when nested
- 1em = current font size

```css
/* em Example */
body {
    font-size: 16px;
}

.parent {
    font-size: 20px;  /* 20px */
}

.child {
    font-size: 1.5em; /* 1.5 × 20px = 30px */
    padding: 1em;     /* 1 × 30px = 30px (uses own font-size) */
}

.grandchild {
    font-size: 1.2em; /* 1.2 × 30px = 36px (compounds!) */
}
```

**em Use Cases:**
```css
/* Good for component-level spacing */
.button {
    font-size: 1em;
    padding: 0.5em 1em;    /* Scales with font-size */
    border-radius: 0.25em;
}

/* Component scales together */
.button.large {
    font-size: 1.25em;  /* Everything scales proportionally */
}
```

**rem (Root em):**
- Relative to **root element's (`<html>`) font-size**
- Does NOT compound
- More predictable than em

```css
/* rem Example */
html {
    font-size: 16px; /* Base size (browser default) */
}

.parent {
    font-size: 2rem;    /* 2 × 16px = 32px */
}

.child {
    font-size: 1.5rem;  /* 1.5 × 16px = 24px (NOT 1.5 × 32px) */
    padding: 1rem;      /* 1 × 16px = 16px */
}

.grandchild {
    font-size: 1.2rem;  /* 1.2 × 16px = 19.2px (consistent!) */
}
```

**rem Best Practices:**
```css
/* Set base size on html */
html {
    font-size: 16px;  /* 1rem = 16px */
}

/* Or use percentage for user preferences */
html {
    font-size: 100%; /* Respects user's browser settings */
}

/* Use rem for most spacing and sizing */
.container {
    padding: 2rem;           /* 32px */
    margin-bottom: 3rem;     /* 48px */
}

h1 {
    font-size: 2.5rem;       /* 40px */
    margin-bottom: 1.5rem;   /* 24px */
}

.button {
    padding: 0.75rem 1.5rem; /* 12px 24px */
    font-size: 1rem;         /* 16px */
}
```

**em vs rem Comparison:**
```css
/* em - Compounds (can be unpredictable) */
.nested {
    font-size: 1.2em;
}
.nested .nested {
    font-size: 1.2em; /* 1.2 × 1.2 = 1.44× parent */
}
.nested .nested .nested {
    font-size: 1.2em; /* 1.2 × 1.44 = 1.728× parent */
}

/* rem - Consistent (always relative to root) */
.nested {
    font-size: 1.2rem; /* Always 1.2 × root font-size */
}
.nested .nested {
    font-size: 1.2rem; /* Still 1.2 × root font-size */
}
```

---

#### **Viewport Units**

**vw (Viewport Width):**
- 1vw = 1% of viewport width
- Responsive to browser width

```css
/* vw Examples */
.full-width {
    width: 100vw;  /* Full viewport width */
}

.half-width {
    width: 50vw;   /* Half viewport width */
}

/* Fluid typography */
h1 {
    font-size: 5vw; /* Scales with viewport */
    /* On 1200px screen: 5% × 1200 = 60px */
    /* On 320px screen: 5% × 320 = 16px */
}
```

**vh (Viewport Height):**
- 1vh = 1% of viewport height
- Great for full-screen sections

```css
/* vh Examples */
.hero {
    height: 100vh;  /* Full viewport height */
    min-height: 500px; /* Minimum height fallback */
}

.section {
    min-height: 50vh; /* At least half screen */
}

/* Center content vertically */
.centered {
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
}
```

**vmin & vmax:**
- **vmin**: 1% of smaller viewport dimension (width or height)
- **vmax**: 1% of larger viewport dimension

```css
/* vmin - useful for responsive sizing */
.square {
    width: 50vmin;   /* 50% of smaller dimension */
    height: 50vmin;  /* Always a square */
}

/* Portrait (360×800): 50vmin = 50% × 360 = 180px */
/* Landscape (800×360): 50vmin = 50% × 360 = 180px */

/* vmax - responsive to larger dimension */
.responsive-text {
    font-size: 3vmax;
}
```

**Viewport Unit Caveats:**
```css
/* ⚠️ Problem: Can create very small/large text */
h1 {
    font-size: 5vw;  /* Too small on mobile, too large on desktop */
}

/* ✅ Solution: Use clamp() */
h1 {
    font-size: clamp(1.5rem, 5vw, 3rem);
    /* min: 1.5rem (24px) */
    /* preferred: 5vw (responsive) */
    /* max: 3rem (48px) */
}

/* ⚠️ Problem: 100vw includes scrollbar width */
.full-width {
    width: 100vw; /* Can cause horizontal scroll */
}

/* ✅ Solution: Use width: 100% instead */
.full-width {
    width: 100%;
}
```

---

#### **Percentage (%)**

- Relative to **parent element**
- Different behavior for different properties

```css
/* Width/Height - relative to parent's width/height */
.parent {
    width: 1000px;
}

.child {
    width: 50%;      /* 50% × 1000px = 500px */
    padding: 5%;     /* 5% of parent's WIDTH (50px) */
    margin-top: 10%; /* 10% of parent's WIDTH (100px) */
}

/* Font-size - relative to parent's font-size */
.parent {
    font-size: 20px;
}

.child {
    font-size: 150%; /* 150% × 20px = 30px */
}

/* Transform - relative to element's own size */
.box {
    transform: translateX(50%); /* 50% of element's width */
}
```

---

#### **Fluid Typography & Spacing**

**Using calc():**
```css
/* Fluid spacing that scales between breakpoints */
.container {
    padding: calc(1rem + 2vw);
    /* Small screen (320px): 16px + 6.4px = 22.4px */
    /* Large screen (1920px): 16px + 38.4px = 54.4px */
}

/* Fluid typography */
h1 {
    font-size: calc(1.5rem + 2vw);
}
```

**Using clamp():**
```css
/* Modern approach - best practice */
h1 {
    font-size: clamp(2rem, 4vw + 1rem, 4rem);
    /* min: 2rem (32px) */
    /* preferred: 4vw + 1rem (grows with viewport) */
    /* max: 4rem (64px) */
}

.container {
    padding: clamp(1rem, 3vw, 3rem);
    width: clamp(300px, 90vw, 1200px);
}

/* Fluid spacing scale */
:root {
    --space-xs: clamp(0.5rem, 1vw, 0.75rem);    /* 8-12px */
    --space-sm: clamp(0.75rem, 1.5vw, 1rem);    /* 12-16px */
    --space-md: clamp(1rem, 2vw, 1.5rem);       /* 16-24px */
    --space-lg: clamp(1.5rem, 3vw, 2.5rem);     /* 24-40px */
    --space-xl: clamp(2rem, 5vw, 4rem);         /* 32-64px */
}

.card {
    padding: var(--space-md);
    margin-bottom: var(--space-lg);
}
```

---

#### **Comparison Table**

| Unit | Relative To | Use Case | Example |
|------|-------------|----------|---------|
| **px** | Fixed | Borders, shadows, small details | `border: 1px solid` |
| **em** | Parent font-size | Component-level spacing | `padding: 0.5em` |
| **rem** | Root font-size | Global spacing, typography | `font-size: 1.5rem` |
| **%** | Parent element | Responsive widths, layouts | `width: 50%` |
| **vw** | Viewport width | Full-width elements, fluid type | `width: 100vw` |
| **vh** | Viewport height | Full-height sections | `height: 100vh` |
| **vmin** | Smaller dimension | Responsive squares, text | `width: 50vmin` |
| **vmax** | Larger dimension | Responsive backgrounds | `font-size: 3vmax` |

---

#### **Complete Fluid Design Example**

```css
/* Base setup */
:root {
    /* Fluid font sizes */
    --fs-sm: clamp(0.875rem, 0.8rem + 0.375vw, 1rem);
    --fs-base: clamp(1rem, 0.9rem + 0.5vw, 1.25rem);
    --fs-lg: clamp(1.25rem, 1rem + 1vw, 2rem);
    --fs-xl: clamp(1.5rem, 1.2rem + 1.5vw, 3rem);
    --fs-xxl: clamp(2rem, 1.5rem + 2.5vw, 4rem);
    
    /* Fluid spacing */
    --space-xs: clamp(0.5rem, 0.4rem + 0.5vw, 0.75rem);
    --space-sm: clamp(0.75rem, 0.6rem + 0.75vw, 1.25rem);
    --space-md: clamp(1rem, 0.8rem + 1vw, 2rem);
    --space-lg: clamp(1.5rem, 1rem + 2vw, 3rem);
    --space-xl: clamp(2rem, 1.5rem + 2.5vw, 4rem);
    
    /* Container width */
    --container-width: min(100% - 2rem, 1200px);
}

html {
    font-size: 100%; /* Respects user preferences */
}

body {
    font-size: var(--fs-base);
    line-height: 1.6;
    padding: var(--space-md);
}

/* Container with fluid max-width */
.container {
    width: var(--container-width);
    margin-inline: auto;
    padding-inline: var(--space-md);
}

/* Typography scale */
h1 { font-size: var(--fs-xxl); margin-bottom: var(--space-md); }
h2 { font-size: var(--fs-xl); margin-bottom: var(--space-sm); }
h3 { font-size: var(--fs-lg); margin-bottom: var(--space-sm); }
p  { font-size: var(--fs-base); margin-bottom: var(--space-sm); }

/* Hero section - full viewport */
.hero {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: var(--space-xl) var(--space-md);
}

/* Card with fluid spacing */
.card {
    padding: var(--space-md);
    border-radius: clamp(0.5rem, 0.5vw, 1rem);
    margin-bottom: var(--space-lg);
}

/* Responsive grid */
.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(min(300px, 100%), 1fr));
    gap: var(--space-md);
}

/* Button with proportional sizing */
.button {
    font-size: var(--fs-base);
    padding: 0.75em 1.5em; /* Uses em for proportional scaling */
    border-radius: 0.25em;
}
```

---

#### **Best Practices**

```css
/* ✅ DO: Use rem for most spacing and sizing */
.element {
    padding: 2rem;
    margin: 1.5rem;
    font-size: 1.25rem;
}

/* ✅ DO: Use em for component-level proportions */
.button {
    padding: 0.5em 1em;  /* Scales with button font-size */
}

/* ✅ DO: Use % for layout widths */
.column {
    width: 50%;
}

/* ✅ DO: Use clamp() for fluid sizing */
h1 {
    font-size: clamp(2rem, 5vw, 4rem);
}

/* ✅ DO: Use vh for full-height sections */
.hero {
    min-height: 100vh;
}

/* ❌ DON'T: Use px for everything */
.bad {
    font-size: 16px;
    padding: 20px;
    margin: 30px;
}

/* ❌ DON'T: Use viewport units without constraints */
.bad {
    font-size: 10vw; /* Way too large on big screens */
}

/* ❌ DON'T: Mix too many unit types randomly */
.confusing {
    padding: 10px 1em 2rem 5%;  /* Hard to maintain */
}
```

---

#### **Responsive Typography System**

```css
/* Modern fluid typography system */
:root {
    /* Type scale using clamp() */
    --text-xs: clamp(0.75rem, 0.7rem + 0.25vw, 0.875rem);
    --text-sm: clamp(0.875rem, 0.8rem + 0.375vw, 1rem);
    --text-base: clamp(1rem, 0.9rem + 0.5vw, 1.25rem);
    --text-lg: clamp(1.125rem, 1rem + 0.625vw, 1.5rem);
    --text-xl: clamp(1.25rem, 1.1rem + 0.75vw, 1.875rem);
    --text-2xl: clamp(1.5rem, 1.3rem + 1vw, 2.25rem);
    --text-3xl: clamp(1.875rem, 1.5rem + 1.875vw, 3rem);
    --text-4xl: clamp(2.25rem, 1.75rem + 2.5vw, 3.75rem);
    --text-5xl: clamp(3rem, 2rem + 5vw, 6rem);
}

/* Usage */
.hero-title {
    font-size: var(--text-5xl);
}

.section-title {
    font-size: var(--text-3xl);
}

.body-text {
    font-size: var(--text-base);
}

.caption {
    font-size: var(--text-sm);
}
```

---

## Animations & Transitions

### Q11: What is the difference between transitions and animations?

**Answer:**

**Transitions:**
Smooth change between two states (triggered by state change).

```css
.button {
    background: blue;
    color: white;
    padding: 10px 20px;
    border: none;
    cursor: pointer;
    
    /* Transition properties */
    transition-property: background, transform;
    transition-duration: 0.3s;
    transition-timing-function: ease;
    transition-delay: 0s;
    
    /* Shorthand */
    transition: background 0.3s ease, transform 0.3s ease;
    /* or transition all properties */
    transition: all 0.3s ease;
}

.button:hover {
    background: darkblue;
    transform: scale(1.05);
}
```

**Transition Timing Functions:**
```css
.element {
    /* Pre-defined */
    transition-timing-function: linear;        /* Constant speed */
    transition-timing-function: ease;          /* Slow start, fast middle, slow end */
    transition-timing-function: ease-in;       /* Slow start */
    transition-timing-function: ease-out;      /* Slow end */
    transition-timing-function: ease-in-out;   /* Slow start and end */
    
    /* Custom cubic-bezier */
    transition-timing-function: cubic-bezier(0.68, -0.55, 0.265, 1.55);
    
    /* Steps */
    transition-timing-function: steps(4, end);
}
```

**Animations:**
Complex sequences with keyframes (automatically executed).

```css
/* Define animation */
@keyframes slideIn {
    0% {
        transform: translateX(-100%);
        opacity: 0;
    }
    100% {
        transform: translateX(0);
        opacity: 1;
    }
}

/* Apply animation */
.element {
    animation-name: slideIn;
    animation-duration: 1s;
    animation-timing-function: ease-out;
    animation-delay: 0.5s;
    animation-iteration-count: 1;        /* or infinite */
    animation-direction: normal;         /* normal | reverse | alternate | alternate-reverse */
    animation-fill-mode: forwards;       /* none | forwards | backwards | both */
    animation-play-state: running;       /* running | paused */
    
    /* Shorthand */
    animation: slideIn 1s ease-out 0.5s 1 normal forwards;
}
```

**Complex Animations:**

**1. Loading Spinner:**
```css
@keyframes spin {
    from {
        transform: rotate(0deg);
    }
    to {
        transform: rotate(360deg);
    }
}

.spinner {
    width: 50px;
    height: 50px;
    border: 5px solid #f3f3f3;
    border-top: 5px solid #3498db;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}
```

**2. Pulse Effect:**
```css
@keyframes pulse {
    0% {
        transform: scale(1);
        opacity: 1;
    }
    50% {
        transform: scale(1.1);
        opacity: 0.8;
    }
    100% {
        transform: scale(1);
        opacity: 1;
    }
}

.pulse {
    animation: pulse 2s ease-in-out infinite;
}
```

**3. Bounce:**
```css
@keyframes bounce {
    0%, 20%, 50%, 80%, 100% {
        transform: translateY(0);
    }
    40% {
        transform: translateY(-30px);
    }
    60% {
        transform: translateY(-15px);
    }
}

.bounce {
    animation: bounce 2s infinite;
}
```

**4. Slide and Fade In:**
```css
@keyframes slideInUp {
    from {
        transform: translateY(50px);
        opacity: 0;
    }
    to {
        transform: translateY(0);
        opacity: 1;
    }
}

.animate-in {
    animation: slideInUp 0.6s ease-out forwards;
}
```

**5. Typing Effect:**
```css
@keyframes typing {
    from {
        width: 0;
    }
    to {
        width: 100%;
    }
}

@keyframes blink {
    50% {
        border-color: transparent;
    }
}

.typewriter {
    overflow: hidden;
    border-right: 2px solid;
    white-space: nowrap;
    animation: 
        typing 3s steps(40) 1s 1 normal both,
        blink 0.5s step-end infinite;
}
```

**6. Parallax Scroll Effect:**
```css
.parallax {
    background-image: url('image.jpg');
    background-attachment: fixed;
    background-position: center;
    background-size: cover;
}
```

---

## Advanced Techniques

### Q12: What are CSS transforms and how do you use them?

**Answer:**

Transforms allow you to rotate, scale, skew, or translate an element.

**2D Transforms:**
```css
/* Translate (move) */
.translate {
    transform: translate(50px, 100px);     /* X, Y */
    transform: translateX(50px);           /* Horizontal only */
    transform: translateY(100px);          /* Vertical only */
}

/* Scale (resize) */
.scale {
    transform: scale(1.5);                 /* Both directions */
    transform: scale(2, 0.5);              /* X, Y separately */
    transform: scaleX(2);                  /* Horizontal only */
    transform: scaleY(0.5);                /* Vertical only */
}

/* Rotate */
.rotate {
    transform: rotate(45deg);              /* Clockwise */
    transform: rotate(-45deg);             /* Counter-clockwise */
}

/* Skew (distort) */
.skew {
    transform: skew(10deg, 5deg);          /* X, Y */
    transform: skewX(10deg);               /* Horizontal only */
    transform: skewY(5deg);                /* Vertical only */
}

/* Combine multiple transforms */
.combined {
    transform: translate(50px, 50px) rotate(45deg) scale(1.2);
}

/* Transform origin (pivot point) */
.origin {
    transform-origin: center center;        /* Default */
    transform-origin: top left;
    transform-origin: 50% 50%;
    transform-origin: 100px 50px;
}
```

**3D Transforms:**
```css
/* Enable 3D space */
.container-3d {
    perspective: 1000px;  /* Distance from viewer */
}

/* 3D transforms */
.element-3d {
    transform: rotateX(45deg);             /* Rotate around X axis */
    transform: rotateY(45deg);             /* Rotate around Y axis */
    transform: rotateZ(45deg);             /* Rotate around Z axis */
    transform: translateZ(50px);           /* Move forward/back */
    
    /* Preserve 3D */
    transform-style: preserve-3d;
    
    /* Backface visibility */
    backface-visibility: hidden;           /* Hide when rotated away */
}
```

**Practical Examples:**

**1. Card Flip:**
```css
.flip-card {
    width: 300px;
    height: 200px;
    perspective: 1000px;
}

.flip-card-inner {
    position: relative;
    width: 100%;
    height: 100%;
    transition: transform 0.6s;
    transform-style: preserve-3d;
}

.flip-card:hover .flip-card-inner {
    transform: rotateY(180deg);
}

.flip-card-front,
.flip-card-back {
    position: absolute;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
}

.flip-card-back {
    transform: rotateY(180deg);
}
```

**2. 3D Cube:**
```css
.cube-container {
    width: 200px;
    height: 200px;
    perspective: 1000px;
}

.cube {
    width: 100%;
    height: 100%;
    position: relative;
    transform-style: preserve-3d;
    transform: rotateX(-20deg) rotateY(30deg);
    animation: rotateCube 10s infinite linear;
}

.cube-face {
    position: absolute;
    width: 200px;
    height: 200px;
    border: 2px solid black;
    opacity: 0.8;
}

.front  { transform: translateZ(100px); }
.back   { transform: translateZ(-100px) rotateY(180deg); }
.right  { transform: rotateY(90deg) translateZ(100px); }
.left   { transform: rotateY(-90deg) translateZ(100px); }
.top    { transform: rotateX(90deg) translateZ(100px); }
.bottom { transform: rotateX(-90deg) translateZ(100px); }

@keyframes rotateCube {
    from { transform: rotateX(0) rotateY(0); }
    to { transform: rotateX(360deg) rotateY(360deg); }
}
```

---

### Q13: What are CSS filters and how do you use them?

**Answer:**

CSS filters apply visual effects like blur, brightness, contrast, etc.

**Filter Functions:**
```css
.element {
    /* Blur */
    filter: blur(5px);
    
    /* Brightness (0 = black, 1 = normal, >1 = brighter) */
    filter: brightness(0.5);
    filter: brightness(1.5);
    
    /* Contrast */
    filter: contrast(150%);
    
    /* Grayscale (0 = color, 1 = gray) */
    filter: grayscale(100%);
    
    /* Hue rotate */
    filter: hue-rotate(90deg);
    
    /* Invert */
    filter: invert(100%);
    
    /* Opacity */
    filter: opacity(50%);
    
    /* Saturate */
    filter: saturate(200%);
    
    /* Sepia */
    filter: sepia(100%);
    
    /* Drop shadow */
    filter: drop-shadow(5px 5px 10px rgba(0, 0, 0, 0.5));
    
    /* Combine multiple filters */
    filter: brightness(1.2) contrast(110%) saturate(120%);
}
```

**Practical Examples:**

**1. Image Hover Effects:**
```css
.image-hover {
    transition: filter 0.3s ease;
}

/* Grayscale to color */
.image-hover {
    filter: grayscale(100%);
}
.image-hover:hover {
    filter: grayscale(0%);
}

/* Blur on hover */
.image-blur:hover {
    filter: blur(3px);
}

/* Brightness on hover */
.image-bright:hover {
    filter: brightness(1.3);
}
```

**2. Glassmorphism (Frosted Glass Effect):**
```css
.glass {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px) saturate(180%);
    border: 1px solid rgba(255, 255, 255, 0.2);
    border-radius: 12px;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}
```

**3. Dark Mode Toggle:**
```css
body {
    transition: filter 0.3s ease;
}

body.dark-mode {
    filter: invert(1) hue-rotate(180deg);
}

/* Prevent images from being inverted */
body.dark-mode img {
    filter: invert(1) hue-rotate(180deg);
}
```

**4. Instagram-like Filters:**
```css
/* Vintage */
.filter-vintage {
    filter: sepia(50%) contrast(120%) brightness(90%);
}

/* Cool */
.filter-cool {
    filter: brightness(110%) contrast(110%) hue-rotate(20deg);
}

/* Warm */
.filter-warm {
    filter: brightness(110%) saturate(130%) hue-rotate(-15deg);
}
```

---

## CSS Architecture & Best Practices

### Q14: What are CSS naming conventions and methodologies?

**Answer:**

**BEM (Block Element Modifier):**
```css
/* Block: Standalone component */
.card { }

/* Element: Part of block */
.card__title { }
.card__image { }
.card__button { }

/* Modifier: Variation of block or element */
.card--featured { }
.card--large { }
.card__button--primary { }
.card__button--disabled { }
```

```html
<div class="card card--featured">
    <h2 class="card__title">Title</h2>
    <img class="card__image" src="image.jpg" alt="">
    <button class="card__button card__button--primary">Click</button>
</div>
```

**OOCSS (Object-Oriented CSS):**
```css
/* Separate structure from skin */

/* Structure */
.button {
    display: inline-block;
    padding: 10px 20px;
    border: none;
    cursor: pointer;
}

/* Skin */
.button-primary {
    background: blue;
    color: white;
}

.button-secondary {
    background: gray;
    color: white;
}
```

```html
<button class="button button-primary">Primary</button>
<button class="button button-secondary">Secondary</button>
```

**SMACSS (Scalable and Modular Architecture):**
```css
/* 1. Base rules */
body, p, h1 { }

/* 2. Layout rules */
.l-header { }
.l-sidebar { }
.l-footer { }

/* 3. Module rules */
.card { }
.nav { }
.button { }

/* 4. State rules */
.is-hidden { }
.is-active { }
.is-disabled { }

/* 5. Theme rules */
.theme-dark { }
.theme-light { }
```

**Utility-First (Tailwind-style):**
```css
/* Small, single-purpose classes */
.flex { display: flex; }
.items-center { align-items: center; }
.justify-between { justify-content: space-between; }
.p-4 { padding: 1rem; }
.m-2 { margin: 0.5rem; }
.text-lg { font-size: 1.125rem; }
.font-bold { font-weight: 700; }
.text-blue-500 { color: #3b82f6; }
```

```html
<div class="flex items-center justify-between p-4 m-2">
    <h1 class="text-lg font-bold text-blue-500">Title</h1>
</div>
```

---

### Q15: What are CSS performance best practices?

**Answer:**

**1. Minimize CSS File Size:**
```css
/* ❌ Bad: Redundant code */
.button-1 {
    background: blue;
    color: white;
    padding: 10px;
}
.button-2 {
    background: blue;
    color: white;
    padding: 10px;
}

/* ✅ Good: Reusable classes */
.button {
    background: blue;
    color: white;
    padding: 10px;
}
```

**2. Efficient Selectors:**
```css
/* ❌ Bad: Over-specific */
body div.container ul li a.link { }

/* ✅ Good: Simple selector */
.nav-link { }

/* ❌ Bad: Universal selector */
* {
    margin: 0;
}

/* ✅ Good: Reset specific elements */
body, h1, h2, h3, p, ul, li {
    margin: 0;
}
```

**3. Avoid Expensive Properties:**
```css
/* Expensive (triggers reflow): */
width, height, padding, margin, border
top, right, bottom, left
font-size, line-height

/* Cheap (composite only): */
transform, opacity

/* ✅ Use transform instead of positioning */
/* Bad */
.move {
    left: 100px;
}

/* Good */
.move {
    transform: translateX(100px);
}
```

**4. Use CSS Containment:**
```css
.widget {
    contain: layout style paint;
    /* Tells browser this element is isolated */
}
```

**5. Optimize Critical CSS:**
```html
<head>
    <!-- Inline critical above-the-fold CSS -->
    <style>
        /* Critical styles here */
        .header { display: flex; }
    </style>
    
    <!-- Async load non-critical CSS -->
    <link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
    <noscript><link rel="stylesheet" href="styles.css"></noscript>
</head>
```

**6. Use CSS Nesting (Modern):**
```css
/* Modern CSS with native nesting */
.card {
    padding: 20px;
    
    & .title {
        font-size: 24px;
    }
    
    & .button {
        background: blue;
        
        &:hover {
            background: darkblue;
        }
    }
}
```

---

## Modern CSS Features

### Q16: What are modern CSS features you should know?

**Answer:**

**1. Container Queries:**
```css
/* Style based on container size, not viewport */
.card-container {
    container-type: inline-size;
    container-name: card;
}

@container card (min-width: 400px) {
    .card {
        display: flex;
    }
}
```

**2. :has() Selector (Parent Selector):**
```css
/* Style parent based on children */
.card:has(.featured) {
    border: 3px solid gold;
}

/* Form validation */
.form-group:has(input:invalid) {
    border-color: red;
}

/* Conditional styling */
.article:has(> img) {
    display: grid;
}
```

**3. :is() and :where() Selectors:**
```css
/* Simplify selector lists */
:is(h1, h2, h3, h4, h5, h6) {
    margin-top: 0;
}

/* Instead of: */
h1, h2, h3, h4, h5, h6 {
    margin-top: 0;
}

/* :where() has 0 specificity */
:where(.card, .box) {
    padding: 20px;
}
```

**4. Subgrid:**
```css
.grid {
    display: grid;
    grid-template-columns: 1fr 2fr 1fr;
}

.subgrid-item {
    display: grid;
    grid-template-columns: subgrid; /* Inherit parent grid */
}
```

**5. aspect-ratio:**
```css
.video {
    aspect-ratio: 16 / 9;
    width: 100%;
}

.square {
    aspect-ratio: 1;
}
```

**6. gap (for Flexbox):**
```css
.flex-container {
    display: flex;
    gap: 20px; /* Works in flexbox now! */
}
```

**7. Logical Properties:**
```css
/* Instead of left/right, use start/end (better for i18n) */
.element {
    margin-inline-start: 20px;  /* left in LTR, right in RTL */
    margin-inline-end: 20px;    /* right in LTR, left in RTL */
    margin-block-start: 10px;   /* top */
    margin-block-end: 10px;     /* bottom */
    
    padding-inline: 20px;       /* left and right */
    padding-block: 10px;        /* top and bottom */
}
```

**8. accent-color:**
```css
/* Style native form controls */
input[type="checkbox"],
input[type="radio"],
input[type="range"] {
    accent-color: blue;
}
```

**9. color-mix():**
```css
.element {
    background: color-mix(in srgb, blue 50%, red 50%);
}
```

**10. @layer (Cascade Layers):**
```css
@layer reset, base, components, utilities;

@layer reset {
    * { margin: 0; padding: 0; }
}

@layer base {
    body { font-family: sans-serif; }
}

@layer components {
    .button { padding: 10px; }
}

@layer utilities {
    .hidden { display: none; }
}
```

---

### Q17: How do you create a complete responsive component?

**Answer:**

**Complete Example: Responsive Card Component**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Responsive Card</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        :root {
            --primary-color: #3498db;
            --text-color: #333;
            --border-radius: 8px;
            --spacing: 1rem;
            --shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        
        body {
            font-family: system-ui, -apple-system, sans-serif;
            line-height: 1.6;
            color: var(--text-color);
            padding: 2rem;
            background: #f5f5f5;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
        }
        
        .card-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 2rem;
        }
        
        .card {
            background: white;
            border-radius: var(--border-radius);
            box-shadow: var(--shadow);
            overflow: hidden;
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }
        
        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 12px rgba(0, 0, 0, 0.15);
        }
        
        .card__image {
            width: 100%;
            height: 200px;
            object-fit: cover;
        }
        
        .card__content {
            padding: var(--spacing);
        }
        
        .card__title {
            font-size: 1.25rem;
            margin-bottom: 0.5rem;
            color: var(--primary-color);
        }
        
        .card__description {
            color: #666;
            margin-bottom: 1rem;
        }
        
        .card__footer {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: var(--spacing);
            border-top: 1px solid #eee;
        }
        
        .card__button {
            background: var(--primary-color);
            color: white;
            border: none;
            padding: 0.5rem 1rem;
            border-radius: 4px;
            cursor: pointer;
            transition: background 0.3s ease;
        }
        
        .card__button:hover {
            background: #2980b9;
        }
        
        .card__meta {
            font-size: 0.875rem;
            color: #999;
        }
        
        /* Responsive breakpoints */
        @media (max-width: 768px) {
            .container {
                padding: 1rem;
            }
            
            .card-grid {
                grid-template-columns: 1fr;
                gap: 1rem;
            }
        }
        
        @media (min-width: 769px) and (max-width: 1024px) {
            .card-grid {
                grid-template-columns: repeat(2, 1fr);
            }
        }
        
        /* Dark mode support */
        @media (prefers-color-scheme: dark) {
            :root {
                --text-color: #f0f0f0;
            }
            
            body {
                background: #1a1a1a;
            }
            
            .card {
                background: #2a2a2a;
            }
            
            .card__description {
                color: #ccc;
            }
        }
        
        /* Reduced motion */
        @media (prefers-reduced-motion: reduce) {
            .card {
                transition: none;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 style="margin-bottom: 2rem;">Responsive Cards</h1>
        <div class="card-grid">
            <article class="card">
                <img src="https://via.placeholder.com/400x200" alt="Card image" class="card__image">
                <div class="card__content">
                    <h2 class="card__title">Card Title 1</h2>
                    <p class="card__description">
                        This is a description of the card content. It provides context and information.
                    </p>
                </div>
                <div class="card__footer">
                    <span class="card__meta">2 days ago</span>
                    <button class="card__button">Learn More</button>
                </div>
            </article>
            
            <article class="card">
                <img src="https://via.placeholder.com/400x200" alt="Card image" class="card__image">
                <div class="card__content">
                    <h2 class="card__title">Card Title 2</h2>
                    <p class="card__description">
                        Another card with similar styling but different content.
                    </p>
                </div>
                <div class="card__footer">
                    <span class="card__meta">5 days ago</span>
                    <button class="card__button">Learn More</button>
                </div>
            </article>
            
            <article class="card">
                <img src="https://via.placeholder.com/400x200" alt="Card image" class="card__image">
                <div class="card__content">
                    <h2 class="card__title">Card Title 3</h2>
                    <p class="card__description">
                        Responsive design adapts to different screen sizes automatically.
                    </p>
                </div>
                <div class="card__footer">
                    <span class="card__meta">1 week ago</span>
                    <button class="card__button">Learn More</button>
                </div>
            </article>
        </div>
    </div>
</body>
</html>
```

---

## Summary

**Key Takeaways:**

**Beginner:**
- Understand selectors and specificity
- Master box model and display properties
- Learn basic positioning
- Use colors, fonts, and spacing

**Intermediate:**
- Master Flexbox for 1D layouts
- Use CSS Grid for 2D layouts
- Create responsive designs with media queries
- Implement transitions for smooth interactions

**Advanced:**
- Use CSS variables for maintainability
- Implement complex animations
- Use transforms for visual effects
- Apply filters for image effects
- Follow CSS architecture patterns
- Optimize for performance

**Best Practices:**
1. Use CSS variables for consistency
2. Follow mobile-first approach
3. Keep specificity low
4. Use semantic class names
5. Organize CSS logically
6. Optimize for performance
7. Consider accessibility
8. Test across browsers
9. Use modern CSS features when supported
10. Write maintainable, scalable code

**Remember:** CSS is constantly evolving. Stay updated with new features and best practices!

---

## Sass/SCSS

### Q18: What is Sass and how does it work?

**Answer:**

**Sass (Syntactically Awesome Style Sheets)** is a CSS preprocessor that extends CSS with features like variables, nesting, mixins, functions, and more. It makes CSS more maintainable, reusable, and powerful.

**How It Works:**

```
Sass/SCSS Code → Sass Compiler → Regular CSS → Browser
```

1. Write code in `.scss` or `.sass` files
2. Sass compiler processes the files
3. Outputs standard `.css` files
4. Browser reads the CSS

**Installation:**

```bash
# Using npm
npm install -g sass

# Compile single file
sass input.scss output.css

# Watch for changes
sass --watch input.scss:output.css

# Watch entire directory
sass --watch scss:css
```

**Two Syntaxes:**

**1. SCSS (Sassy CSS) - More Popular:**
```scss
// SCSS syntax - uses braces and semicolons (like CSS)
.button {
    padding: 10px 20px;
    background: blue;
    
    &:hover {
        background: darkblue;
    }
}
```

**2. Sass (Indented Syntax):**
```sass
// Sass syntax - uses indentation (no braces or semicolons)
.button
    padding: 10px 20px
    background: blue
    
    &:hover
        background: darkblue
```

**Note:** SCSS is more commonly used because it's CSS-compatible (any valid CSS is valid SCSS).

---

### Q19: What are Sass variables?

**Answer:**

Sass variables store values that you can reuse throughout your stylesheet.

**Basic Variables:**

```scss
// Define variables
$primary-color: #3498db;
$secondary-color: #2ecc71;
$danger-color: #e74c3c;

$font-main: 'Helvetica', sans-serif;
$font-size-base: 16px;
$font-size-large: 20px;

$spacing-small: 8px;
$spacing-medium: 16px;
$spacing-large: 24px;

$border-radius: 4px;
$transition-speed: 0.3s;

// Use variables
.button {
    font-family: $font-main;
    font-size: $font-size-base;
    padding: $spacing-medium;
    background: $primary-color;
    border-radius: $border-radius;
    transition: all $transition-speed ease;
    
    &:hover {
        background: darken($primary-color, 10%);
    }
}

.alert-success {
    background: $secondary-color;
    padding: $spacing-small;
}
```

**Variable Scope:**

```scss
// Global variable
$global-color: blue;

.container {
    // Local variable (only available inside .container)
    $local-color: red;
    color: $local-color;
    
    .child {
        color: $local-color; // Can access parent's local variable
        background: $global-color; // Can access global variable
    }
}

// Outside container
.other {
    color: $global-color; // ✅ Works
    // color: $local-color; // ❌ Error: undefined variable
}
```

**Default Values:**

```scss
// Define variable with !default (can be overridden)
$primary-color: blue !default;

// If $primary-color is already defined, this won't override it
// If not defined, it will use this value

// Useful for library/framework development
```

**Variable Interpolation:**

```scss
$side: left;
$size: 200;

.box {
    margin-#{$side}: 20px; // Outputs: margin-left: 20px;
    width: #{$size}px; // Outputs: width: 200px;
}

// For selectors and property names
$name: button;
.#{$name}-primary {
    background: blue;
}
// Outputs: .button-primary { background: blue; }
```

**Data Types:**

```scss
// Numbers
$width: 100px;
$opacity: 0.8;
$z-index: 10;

// Strings
$font-family: 'Helvetica';
$image-path: '../images/';

// Colors
$color1: #ff0000;
$color2: rgb(255, 0, 0);
$color3: rgba(255, 0, 0, 0.5);
$color4: hsl(0, 100%, 50%);

// Booleans
$is-rounded: true;

// Null
$shadow: null;

// Lists (space or comma separated)
$font-stack: Helvetica, Arial, sans-serif;
$padding: 10px 20px 10px 20px;

// Maps (key-value pairs)
$colors: (
    primary: blue,
    secondary: green,
    danger: red
);
```

---

### Q19.1: What is Sass nesting and how do you use it?

**Answer:**

Nesting allows you to write CSS selectors in a hierarchical way that mirrors your HTML structure.

**Basic Nesting:**

```scss
// SCSS
.nav {
    background: #333;
    padding: 10px;
    
    ul {
        list-style: none;
        margin: 0;
        
        li {
            display: inline-block;
            margin-right: 20px;
            
            a {
                color: white;
                text-decoration: none;
                
                &:hover {
                    color: #0066cc;
                }
            }
        }
    }
}

// Compiled CSS
.nav {
    background: #333;
    padding: 10px;
}
.nav ul {
    list-style: none;
    margin: 0;
}
.nav ul li {
    display: inline-block;
    margin-right: 20px;
}
.nav ul li a {
    color: white;
    text-decoration: none;
}
.nav ul li a:hover {
    color: #0066cc;
}
```

**Parent Selector (&):**

```scss
// & refers to the parent selector
.button {
    background: blue;
    color: white;
    
    // .button:hover
    &:hover {
        background: darkblue;
    }
    
    // .button:active
    &:active {
        background: navy;
    }
    
    // .button--primary (BEM modifier)
    &--primary {
        background: green;
    }
    
    // .button--secondary
    &--secondary {
        background: gray;
    }
    
    // .button.is-disabled
    &.is-disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }
    
    // body.dark-mode .button
    body.dark-mode & {
        background: white;
        color: black;
    }
}
```

**Nesting Properties:**

```scss
.box {
    // Nesting properties with same prefix
    font: {
        family: Arial;
        size: 16px;
        weight: bold;
    }
    
    margin: {
        top: 10px;
        bottom: 20px;
        left: 15px;
        right: 15px;
    }
    
    border: {
        radius: 5px;
        style: solid;
        width: 2px;
        color: black;
    }
}

// Compiled CSS
.box {
    font-family: Arial;
    font-size: 16px;
    font-weight: bold;
    margin-top: 10px;
    margin-bottom: 20px;
    margin-left: 15px;
    margin-right: 15px;
    border-radius: 5px;
    border-style: solid;
    border-width: 2px;
    border-color: black;
}
```

**Nesting Best Practices:**

```scss
// ❌ Bad: Too deep (harder to maintain and override)
.header {
    .nav {
        ul {
            li {
                a {
                    span {
                        // 6 levels deep!
                    }
                }
            }
        }
    }
}

// ✅ Good: Maximum 3-4 levels
.header {
    .nav-link {
        &:hover {
            // Only 2-3 levels
        }
    }
}
```

**Nested Media Queries:**

```scss
.sidebar {
    width: 300px;
    float: left;
    
    @media (max-width: 768px) {
        width: 100%;
        float: none;
    }
    
    .widget {
        padding: 20px;
        
        @media (max-width: 768px) {
            padding: 10px;
        }
    }
}
```

---

### Q19.2: What are Sass mixins and how do you use them?

**Answer:**

Mixins are reusable blocks of code that can accept arguments, similar to functions in programming.

**Basic Mixins:**

```scss
// Define a mixin
@mixin flex-center {
    display: flex;
    justify-content: center;
    align-items: center;
}

// Use the mixin
.container {
    @include flex-center;
    height: 100vh;
}

// Compiled CSS
.container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}
```

**Mixins with Arguments:**

```scss
// Mixin with required arguments
@mixin box($width, $height, $bg-color) {
    width: $width;
    height: $height;
    background-color: $bg-color;
}

.box1 {
    @include box(200px, 100px, blue);
}

.box2 {
    @include box(300px, 150px, red);
}
```

**Mixins with Default Values:**

```scss
@mixin button($bg-color: blue, $text-color: white, $padding: 10px 20px) {
    background-color: $bg-color;
    color: $text-color;
    padding: $padding;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    
    &:hover {
        background-color: darken($bg-color, 10%);
    }
}

// Use with defaults
.btn1 {
    @include button; // Uses all defaults
}

// Override specific arguments
.btn2 {
    @include button(red); // Only override bg-color
}

.btn3 {
    @include button($bg-color: green, $padding: 15px 30px);
}
```

**Real-World Mixin Examples:**

**1. Responsive Breakpoints:**

```scss
@mixin respond-to($breakpoint) {
    @if $breakpoint == phone {
        @media (max-width: 599px) { @content; }
    }
    @else if $breakpoint == tablet {
        @media (min-width: 600px) and (max-width: 1023px) { @content; }
    }
    @else if $breakpoint == desktop {
        @media (min-width: 1024px) { @content; }
    }
}

.container {
    width: 100%;
    
    @include respond-to(phone) {
        padding: 10px;
    }
    
    @include respond-to(tablet) {
        padding: 20px;
    }
    
    @include respond-to(desktop) {
        padding: 30px;
        max-width: 1200px;
    }
}
```

**2. Vendor Prefixes:**

```scss
@mixin transform($property) {
    -webkit-transform: $property;
    -moz-transform: $property;
    -ms-transform: $property;
    -o-transform: $property;
    transform: $property;
}

@mixin transition($property, $duration: 0.3s, $easing: ease) {
    -webkit-transition: $property $duration $easing;
    -moz-transition: $property $duration $easing;
    -o-transition: $property $duration $easing;
    transition: $property $duration $easing;
}

.box {
    @include transform(rotate(45deg));
    @include transition(all, 0.5s, ease-in-out);
}
```

**3. Flexbox Layout:**

```scss
@mixin flex-container($direction: row, $justify: flex-start, $align: stretch) {
    display: flex;
    flex-direction: $direction;
    justify-content: $justify;
    align-items: $align;
}

.header {
    @include flex-container(row, space-between, center);
}

.sidebar {
    @include flex-container(column, flex-start, stretch);
}
```

**4. CSS Triangle:**

```scss
@mixin triangle($size, $color, $direction) {
    width: 0;
    height: 0;
    border: $size solid transparent;
    
    @if $direction == up {
        border-bottom-color: $color;
    }
    @else if $direction == down {
        border-top-color: $color;
    }
    @else if $direction == left {
        border-right-color: $color;
    }
    @else if $direction == right {
        border-left-color: $color;
    }
}

.arrow-up {
    @include triangle(10px, red, up);
}

.arrow-down {
    @include triangle(15px, blue, down);
}
```

**5. Text Truncation:**

```scss
@mixin truncate($lines: 1) {
    @if $lines == 1 {
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
    }
    @else {
        display: -webkit-box;
        -webkit-line-clamp: $lines;
        -webkit-box-orient: vertical;
        overflow: hidden;
    }
}

.single-line {
    @include truncate; // Single line truncation
}

.multi-line {
    @include truncate(3); // Three line truncation
}
```

**6. Absolute Centering:**

```scss
@mixin absolute-center {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

.modal {
    @include absolute-center;
}
```

**7. Clearfix:**

```scss
@mixin clearfix {
    &::after {
        content: "";
        display: table;
        clear: both;
    }
}

.container {
    @include clearfix;
}
```

---

### Q19.3: What is Sass @extend and inheritance?

**Answer:**

`@extend` allows one selector to inherit styles from another, promoting code reuse.

**Basic @extend:**

```scss
// Base style
.message {
    padding: 15px;
    border: 1px solid transparent;
    border-radius: 4px;
    margin-bottom: 20px;
}

// Extend base style
.message-success {
    @extend .message;
    background-color: #d4edda;
    border-color: #c3e6cb;
    color: #155724;
}

.message-error {
    @extend .message;
    background-color: #f8d7da;
    border-color: #f5c6cb;
    color: #721c24;
}

.message-warning {
    @extend .message;
    background-color: #fff3cd;
    border-color: #ffeeba;
    color: #856404;
}

// Compiled CSS
.message, .message-success, .message-error, .message-warning {
    padding: 15px;
    border: 1px solid transparent;
    border-radius: 4px;
    margin-bottom: 20px;
}

.message-success {
    background-color: #d4edda;
    border-color: #c3e6cb;
    color: #155724;
}

.message-error {
    background-color: #f8d7da;
    border-color: #f5c6cb;
    color: #721c24;
}

.message-warning {
    background-color: #fff3cd;
    border-color: #ffeeba;
    color: #856404;
}
```

**Placeholder Selectors (%):**

Placeholders are like classes but don't appear in the compiled CSS unless extended.

```scss
// Define placeholder (not compiled unless extended)
%button-base {
    display: inline-block;
    padding: 10px 20px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    text-align: center;
    text-decoration: none;
    transition: all 0.3s ease;
}

.btn-primary {
    @extend %button-base;
    background: blue;
    color: white;
    
    &:hover {
        background: darkblue;
    }
}

.btn-secondary {
    @extend %button-base;
    background: gray;
    color: white;
    
    &:hover {
        background: darkgray;
    }
}

// Compiled CSS (notice %button-base becomes a selector list)
.btn-primary, .btn-secondary {
    display: inline-block;
    padding: 10px 20px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    text-align: center;
    text-decoration: none;
    transition: all 0.3s ease;
}

.btn-primary {
    background: blue;
    color: white;
}
.btn-primary:hover {
    background: darkblue;
}

.btn-secondary {
    background: gray;
    color: white;
}
.btn-secondary:hover {
    background: darkgray;
}
```

**@extend vs Mixins:**

```scss
// Using @extend (groups selectors)
%flex-center {
    display: flex;
    justify-content: center;
    align-items: center;
}

.box1 { @extend %flex-center; }
.box2 { @extend %flex-center; }

// Compiled CSS (efficient - shared rule)
.box1, .box2 {
    display: flex;
    justify-content: center;
    align-items: center;
}

// Using mixin (duplicates code)
@mixin flex-center {
    display: flex;
    justify-content: center;
    align-items: center;
}

.box1 { @include flex-center; }
.box2 { @include flex-center; }

// Compiled CSS (less efficient - duplicated)
.box1 {
    display: flex;
    justify-content: center;
    align-items: center;
}
.box2 {
    display: flex;
    justify-content: center;
    align-items: center;
}
```

**When to Use Each:**

```scss
// Use @extend when:
// - No arguments needed
// - Want to group selectors (smaller CSS)
// - Sharing exact same styles

%card-base {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

// Use mixins when:
// - Need arguments/customization
// - Logic/calculations required
// - Used in media queries

@mixin respond($breakpoint) {
    @if $breakpoint == mobile {
        @media (max-width: 767px) { @content; }
    }
}
```

---

### Q19.4: What are Sass partials and imports?

**Answer:**

Partials allow you to split your Sass into multiple files for better organization.

**File Structure:**

```
styles/
├── main.scss           (Main file that imports everything)
├── _variables.scss     (Variables)
├── _mixins.scss        (Mixins)
├── _base.scss          (Base styles)
├── _layout.scss        (Layout)
├── _components.scss    (Components)
└── _utilities.scss     (Utility classes)
```

**Note:** Partial files start with underscore `_` and are not compiled directly.

**_variables.scss:**
```scss
// Colors
$primary-color: #3498db;
$secondary-color: #2ecc71;
$danger-color: #e74c3c;
$text-color: #333;
$bg-color: #f5f5f5;

// Typography
$font-main: 'Helvetica Neue', sans-serif;
$font-size-base: 16px;
$line-height-base: 1.6;

// Spacing
$spacing-xs: 4px;
$spacing-sm: 8px;
$spacing-md: 16px;
$spacing-lg: 24px;
$spacing-xl: 32px;

// Breakpoints
$breakpoint-mobile: 576px;
$breakpoint-tablet: 768px;
$breakpoint-desktop: 1024px;
```

**_mixins.scss:**
```scss
@mixin flex-center {
    display: flex;
    justify-content: center;
    align-items: center;
}

@mixin respond-to($breakpoint) {
    @if $breakpoint == mobile {
        @media (max-width: $breakpoint-mobile) { @content; }
    }
    @else if $breakpoint == tablet {
        @media (max-width: $breakpoint-tablet) { @content; }
    }
    @else if $breakpoint == desktop {
        @media (min-width: $breakpoint-desktop) { @content; }
    }
}

@mixin button-variant($bg-color) {
    background-color: $bg-color;
    color: white;
    
    &:hover {
        background-color: darken($bg-color, 10%);
    }
}
```

**_base.scss:**
```scss
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: $font-main;
    font-size: $font-size-base;
    line-height: $line-height-base;
    color: $text-color;
    background-color: $bg-color;
}

h1, h2, h3, h4, h5, h6 {
    margin-bottom: $spacing-md;
}

p {
    margin-bottom: $spacing-sm;
}

a {
    color: $primary-color;
    text-decoration: none;
    
    &:hover {
        text-decoration: underline;
    }
}
```

**_components.scss:**
```scss
.button {
    padding: $spacing-sm $spacing-md;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: all 0.3s ease;
    
    &--primary {
        @include button-variant($primary-color);
    }
    
    &--secondary {
        @include button-variant($secondary-color);
    }
    
    &--danger {
        @include button-variant($danger-color);
    }
}

.card {
    background: white;
    border-radius: 8px;
    padding: $spacing-lg;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    
    &__title {
        font-size: 1.5rem;
        margin-bottom: $spacing-md;
    }
    
    &__content {
        color: lighten($text-color, 20%);
    }
}
```

**main.scss (Import everything):**
```scss
// 1. Import variables first (other files depend on them)
@import 'variables';

// 2. Import mixins (used by other files)
@import 'mixins';

// 3. Import base styles
@import 'base';

// 4. Import layout
@import 'layout';

// 5. Import components
@import 'components';

// 6. Import utilities (highest specificity)
@import 'utilities';
```

**Modern @use and @forward:**

Sass now recommends `@use` instead of `@import`:

```scss
// _variables.scss
$primary-color: blue;
$secondary-color: green;

// _mixins.scss
@mixin flex-center {
    display: flex;
    justify-content: center;
    align-items: center;
}

// main.scss
@use 'variables';
@use 'mixins';

.container {
    // Access with namespace
    background: variables.$primary-color;
    @include mixins.flex-center;
}

// Or use alias
@use 'variables' as vars;
@use 'mixins' as mx;

.container {
    background: vars.$primary-color;
    @include mx.flex-center;
}

// Or without namespace
@use 'variables' as *;
@use 'mixins' as *;

.container {
    background: $primary-color;
    @include flex-center;
}
```

**@forward (Re-exporting):**

```scss
// _config.scss (central configuration file)
@forward 'variables';
@forward 'mixins';
@forward 'functions';

// main.scss (import once, get everything)
@use 'config' as *;

.button {
    background: $primary-color; // From variables
    @include flex-center;       // From mixins
}
```

---

### Q19.5: What are Sass functions and operators?

**Answer:**

**Built-in Color Functions:**

```scss
$base-color: #3498db;

.element {
    // Lighten / Darken
    background: lighten($base-color, 20%);  // Lighter
    color: darken($base-color, 20%);        // Darker
    
    // Saturate / Desaturate
    border-color: saturate($base-color, 30%);
    outline-color: desaturate($base-color, 30%);
    
    // Adjust hue
    box-shadow: 0 0 10px hue-rotate($base-color, 45deg);
    
    // Transparency
    background: rgba($base-color, 0.5);
    background: transparentize($base-color, 0.5);  // Same as above
    background: opacify($base-color, 0.2);         // Increase opacity
    
    // Mix colors
    color: mix($base-color, red, 50%);  // 50% of each
    
    // Complement
    background: complement($base-color);  // Opposite on color wheel
    
    // Grayscale
    color: grayscale($base-color);
    
    // Invert
    background: invert($base-color);
}
```

**Number Functions:**

```scss
.element {
    // Rounding
    width: round(3.6px);        // 4px
    height: ceil(3.2px);        // 4px
    margin: floor(3.8px);       // 3px
    
    // Absolute value
    padding: abs(-10px);        // 10px
    
    // Min / Max
    width: min(100px, 50%);
    height: max(200px, 10vh);
    
    // Percentage
    width: percentage(0.5);     // 50%
    
    // Random
    opacity: random();          // Random 0-1
    z-index: random(100);       // Random 0-100
}
```

**String Functions:**

```scss
$font: 'Helvetica';

.element {
    // Quote / Unquote
    font-family: quote($font);          // "Helvetica"
    content: unquote("'Hello'");        // 'Hello'
    
    // To upper / lower case
    text-transform: to-upper-case('hello');  // HELLO
    content: to-lower-case('WORLD');         // world
    
    // String length
    $length: str-length('hello');       // 5
    
    // String index
    $index: str-index('hello', 'e');    // 2
    
    // Insert
    content: str-insert('hello', 'x', 3);  // hexllo
}
```

**List Functions:**

```scss
$list: 10px 20px 30px 40px;

.element {
    // Length
    $len: length($list);              // 4
    
    // Get nth item
    padding-left: nth($list, 2);      // 20px
    
    // Append
    $new-list: append($list, 50px);   // 10px 20px 30px 40px 50px
    
    // Join lists
    $list1: 1px 2px;
    $list2: 3px 4px;
    $joined: join($list1, $list2);    // 1px 2px 3px 4px
}
```

**Map Functions:**

```scss
$colors: (
    primary: blue,
    secondary: green,
    danger: red
);

.element {
    // Get value
    background: map-get($colors, primary);  // blue
    
    // Has key
    @if map-has-key($colors, primary) {
        color: white;
    }
    
    // Get keys
    // map-keys($colors) returns: primary, secondary, danger
    
    // Get values
    // map-values($colors) returns: blue, green, red
    
    // Merge maps
    $more-colors: (warning: orange, info: cyan);
    $all-colors: map-merge($colors, $more-colors);
}

// Loop through map
@each $name, $color in $colors {
    .btn-#{$name} {
        background: $color;
    }
}

// Compiled:
// .btn-primary { background: blue; }
// .btn-secondary { background: green; }
// .btn-danger { background: red; }
```

**Math Operators:**

```scss
.element {
    // Addition
    width: 100px + 50px;           // 150px
    
    // Subtraction
    height: 200px - 50px;          // 150px
    
    // Multiplication
    padding: 10px * 2;             // 20px
    
    // Division (use math.div() or wrap in parentheses)
    font-size: (30px / 2);         // 15px
    
    // Modulo
    width: 100px % 30px;           // 10px
    
    // Comparison
    @if 100px > 50px {
        // true
    }
    
    // String concatenation
    content: "Hello" + " " + "World";  // "Hello World"
    
    // Color operations
    color: #010203 + #040506;      // #050709
}
```

**Custom Functions:**

```scss
// Define custom function
@function calculate-rem($px-value, $base: 16px) {
    @return ($px-value / $base) * 1rem;
}

.element {
    font-size: calculate-rem(24px);  // 1.5rem
    padding: calculate-rem(32px);    // 2rem
}

// Function with conditionals
@function contrast-color($color) {
    @if lightness($color) > 50% {
        @return #000;  // Dark text on light background
    }
    @else {
        @return #fff;  // Light text on dark background
    }
}

.button {
    $bg: #3498db;
    background: $bg;
    color: contrast-color($bg);
}

// Function for spacing scale
@function spacing($multiplier) {
    $base: 8px;
    @return $base * $multiplier;
}

.element {
    margin: spacing(2);    // 16px
    padding: spacing(3);   // 24px
}
```

---

### Q19.6: What are Sass control directives?

**Answer:**

Sass includes programming-like control structures for logic and loops.

**@if / @else:**

```scss
$theme: dark;

body {
    @if $theme == dark {
        background: #1a1a1a;
        color: #f0f0f0;
    }
    @else if $theme == light {
        background: #ffffff;
        color: #333333;
    }
    @else {
        background: gray;
        color: white;
    }
}

// Mixin with conditional logic
@mixin button-size($size) {
    @if $size == small {
        padding: 5px 10px;
        font-size: 12px;
    }
    @else if $size == medium {
        padding: 10px 20px;
        font-size: 14px;
    }
    @else if $size == large {
        padding: 15px 30px;
        font-size: 18px;
    }
    @else {
        @warn "Unknown size: #{$size}";
        padding: 10px 20px;
        font-size: 14px;
    }
}

.btn-sm { @include button-size(small); }
.btn-md { @include button-size(medium); }
.btn-lg { @include button-size(large); }
```

**@for Loop:**

```scss
// @for $i from <start> through <end> (inclusive)
@for $i from 1 through 5 {
    .column-#{$i} {
        width: percentage($i / 5);
    }
}

// Compiled:
// .column-1 { width: 20%; }
// .column-2 { width: 40%; }
// .column-3 { width: 60%; }
// .column-4 { width: 80%; }
// .column-5 { width: 100%; }

// @for $i from <start> to <end> (exclusive)
@for $i from 1 to 4 {
    .item-#{$i} {
        z-index: $i;
    }
}

// Compiled:
// .item-1 { z-index: 1; }
// .item-2 { z-index: 2; }
// .item-3 { z-index: 3; }

// Grid system
@for $i from 1 through 12 {
    .col-#{$i} {
        width: percentage($i / 12);
    }
}
```

**@each Loop:**

```scss
// Loop through list
$sizes: small, medium, large;

@each $size in $sizes {
    .button-#{$size} {
        @if $size == small {
            padding: 5px 10px;
        }
        @else if $size == medium {
            padding: 10px 20px;
        }
        @else if $size == large {
            padding: 15px 30px;
        }
    }
}

// Loop through map
$colors: (
    primary: #3498db,
    success: #2ecc71,
    danger: #e74c3c,
    warning: #f39c12,
    info: #00bcd4
);

@each $name, $color in $colors {
    .btn-#{$name} {
        background-color: $color;
        
        &:hover {
            background-color: darken($color, 10%);
        }
    }
    
    .text-#{$name} {
        color: $color;
    }
    
    .bg-#{$name} {
        background-color: $color;
    }
}

// Multiple assignment
$icons: 
    home "\\f015",
    user "\\f007",
    heart "\\f004",
    star "\\f005";

@each $name, $glyph in $icons {
    .icon-#{$name}::before {
        content: $glyph;
        font-family: 'FontAwesome';
    }
}
```

**@while Loop:**

```scss
$i: 1;

@while $i <= 6 {
    .heading-#{$i} {
        font-size: #{24 - ($i * 2)}px;
    }
    $i: $i + 1;
}

// Compiled:
// .heading-1 { font-size: 22px; }
// .heading-2 { font-size: 20px; }
// .heading-3 { font-size: 18px; }
// .heading-4 { font-size: 16px; }
// .heading-5 { font-size: 14px; }
// .heading-6 { font-size: 12px; }
```

**Practical Examples:**

**1. Spacing Utilities:**

```scss
$spacings: (0, 1, 2, 3, 4, 5);
$sides: (top: t, right: r, bottom: b, left: l);

@each $size in $spacings {
    // Margin all sides
    .m-#{$size} {
        margin: #{$size * 0.25}rem;
    }
    
    // Padding all sides
    .p-#{$size} {
        padding: #{$size * 0.25}rem;
    }
    
    // Individual sides
    @each $name, $abbr in $sides {
        .m#{$abbr}-#{$size} {
            margin-#{$name}: #{$size * 0.25}rem;
        }
        .p#{$abbr}-#{$size} {
            padding-#{$name}: #{$size * 0.25}rem;
        }
    }
}

// Generates: .m-0, .m-1, .mt-1, .mr-1, .p-0, .p-1, etc.
```

**2. Responsive Grid:**

```scss
$breakpoints: (
    sm: 576px,
    md: 768px,
    lg: 992px,
    xl: 1200px
);

@each $breakpoint, $width in $breakpoints {
    @media (min-width: $width) {
        @for $i from 1 through 12 {
            .col-#{$breakpoint}-#{$i} {
                width: percentage($i / 12);
            }
        }
    }
}

// Generates: .col-sm-1 through .col-xl-12
```

**3. Loading Animation:**

```scss
@for $i from 1 through 3 {
    .dot-#{$i} {
        animation-delay: #{$i * 0.15}s;
    }
}
```

---

### Q20: What is CSS pseudo-classes and pseudo-elements?

**Answer:**

**Pseudo-classes** - Select elements based on their state (`:hover`, `:focus`, `:active`)

**Pseudo-elements** - Style specific parts of an element (`::before`, `::after`, `::first-letter`)

**Key Difference:**
- Pseudo-classes: single colon `:hover`
- Pseudo-elements: double colon `::before`

```css
/* Pseudo-classes */
a:hover {
    color: blue;
}

input:focus {
    outline: 2px solid blue;
}

button:active {
    transform: scale(0.95);
}

li:nth-child(odd) {
    background: #f0f0f0;
}

p:first-child {
    font-weight: bold;
}

input:disabled {
    opacity: 0.5;
}

/* Pseudo-elements */
p::first-letter {
    font-size: 2em;
    float: left;
}

p::first-line {
    font-weight: bold;
}

.quote::before {
    content: '"';
    font-size: 2em;
}

.quote::after {
    content: '"';
}

/* Create decorative elements */
.card::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 4px;
    height: 100%;
    background: linear-gradient(to bottom, #667eea, #764ba2);
}

/* Selection styling */
::selection {
    background: #667eea;
    color: white;
}
```

**Advanced Pseudo-classes:**

```css
/* :is() - Simplify selectors */
:is(h1, h2, h3):hover {
    color: blue;
}

/* :where() - Zero specificity */
:where(ul, ol) li {
    padding: 0.5rem;
}

/* :not() - Exclude elements */
li:not(:last-child) {
    border-bottom: 1px solid #ddd;
}

/* :has() - Parent selector (modern) */
.card:has(img) {
    display: grid;
}
```

---

### Q21: What is CSS `z-index` and stacking context?

**Answer:**

`z-index` controls the stack order of positioned elements. Higher values appear on top.

**Important Rules:**
1. Only works on **positioned elements** (`position: relative/absolute/fixed/sticky`)
2. Creates a **stacking context**
3. `z-index` is relative to parent stacking context

```css
/* Basic z-index */
.modal-overlay {
    position: fixed;
    z-index: 100;
}

.modal {
    position: fixed;
    z-index: 101;
}

.dropdown {
    position: absolute;
    z-index: 50;
}
```

**Stacking Context Created By:**
```css
/* 1. Positioned with z-index */
.element {
    position: relative;
    z-index: 1;
}

/* 2. Opacity less than 1 */
.element {
    opacity: 0.9;
}

/* 3. Transform */
.element {
    transform: translateZ(0);
}

/* 4. Fixed/Sticky position */
.element {
    position: fixed;
}

/* 5. Filter */
.element {
    filter: blur(5px);
}
```

**Common z-index Scale:**
```css
:root {
    --z-dropdown: 1000;
    --z-sticky: 1020;
    --z-fixed: 1030;
    --z-modal-backdrop: 1040;
    --z-modal: 1050;
    --z-popover: 1060;
    --z-tooltip: 1070;
}
```

---

### Q22: What is CSS `overflow` property?

**Answer:**

Controls what happens when content exceeds an element's dimensions.

```css
/* Values */
.container {
    overflow: visible;  /* Default - content shows outside */
    overflow: hidden;   /* Clip content */
    overflow: scroll;   /* Always show scrollbar */
    overflow: auto;     /* Show scrollbar only when needed */
}

/* Individual axes */
.element {
    overflow-x: auto;
    overflow-y: hidden;
}

/* Clip content smoothly */
.text {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}
/* Result: "This is a very long tex..." */

/* Scrollable container */
.chat-messages {
    max-height: 500px;
    overflow-y: auto;
    overflow-x: hidden;
}

/* Hide scrollbar but keep functionality */
.custom-scroll {
    overflow: auto;
    scrollbar-width: none; /* Firefox */
}

.custom-scroll::-webkit-scrollbar {
    display: none; /* Chrome, Safari, Edge */
}
```

**Custom Scrollbar Styling:**
```css
.custom-scroll::-webkit-scrollbar {
    width: 10px;
}

.custom-scroll::-webkit-scrollbar-track {
    background: #f1f1f1;
}

.custom-scroll::-webkit-scrollbar-thumb {
    background: #888;
    border-radius: 5px;
}

.custom-scroll::-webkit-scrollbar-thumb:hover {
    background: #555;
}
```

---

### Q23: What are CSS combinators?

**Answer:**

Combinators select elements based on their relationship to other elements.

```css
/* Descendant Combinator (space) */
div p {
    color: blue; /* All <p> inside <div> */
}

/* Child Combinator (>) */
ul > li {
    list-style: disc; /* Direct children only */
}

/* Adjacent Sibling (+) */
h2 + p {
    font-weight: bold; /* <p> immediately after <h2> */
}

/* General Sibling (~) */
h2 ~ p {
    color: gray; /* All <p> siblings after <h2> */
}
```

**Practical Examples:**

```css
/* Style first paragraph after heading */
h1 + p {
    font-size: 1.2em;
    margin-top: 0;
}

/* Style list items but not nested lists */
.menu > li > a {
    padding: 1rem;
}

/* Alternate row colors in table */
tr:nth-child(even) {
    background: #f0f0f0;
}

/* Style siblings after checked checkbox */
input[type="checkbox"]:checked ~ label {
    text-decoration: line-through;
    opacity: 0.5;
}
```

---

### Q24: What is the difference between `visibility: hidden` and `display: none`?

**Answer:**

| Property | Space Taken | Accessibility | Events | Animation |
|----------|-------------|---------------|--------|-----------|
| `display: none` | No | Not read by screen readers | No | Can't animate |
| `visibility: hidden` | Yes | Not read by screen readers | No | Can animate |
| `opacity: 0` | Yes | Read by screen readers | Yes | Can animate |

```css
/* display: none - Completely removes from layout */
.hidden {
    display: none;
}

/* visibility: hidden - Invisible but takes space */
.invisible {
    visibility: hidden;
}

/* opacity: 0 - Transparent but interactive */
.transparent {
    opacity: 0;
}

/* Comparison */
.container {
    display: flex;
    gap: 1rem;
}

.box {
    width: 100px;
    height: 100px;
}

/* Box 1: removed from flow */
.box1 {
    display: none; /* Other boxes shift left */
}

/* Box 2: invisible but space remains */
.box2 {
    visibility: hidden; /* Gap remains */
}

/* Box 3: transparent but clickable */
.box3 {
    opacity: 0; /* Can still click */
}
```

**Use Cases:**
```css
/* Toggle visibility with animation */
.modal {
    opacity: 0;
    visibility: hidden;
    transition: opacity 0.3s, visibility 0.3s;
}

.modal.active {
    opacity: 1;
    visibility: visible;
}

/* Collapse with animation */
.accordion-content {
    max-height: 0;
    overflow: hidden;
    transition: max-height 0.3s;
}

.accordion-content.open {
    max-height: 500px;
}
```

---

### Q25: What is CSS `clip-path` property?

**Answer:**

Creates clipping regions to show only parts of an element.

```css
/* Basic shapes */
.circle {
    clip-path: circle(50%);
}

.ellipse {
    clip-path: ellipse(40% 30%);
}

.triangle {
    clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
}

.hexagon {
    clip-path: polygon(25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%, 0% 50%);
}

/* Inset rectangle */
.card {
    clip-path: inset(10px 20px 30px 40px round 10px);
}

/* Animated shapes */
.shape {
    clip-path: polygon(0 0, 100% 0, 100% 100%, 0 100%);
    transition: clip-path 0.5s;
}

.shape:hover {
    clip-path: polygon(20% 0, 80% 0, 100% 100%, 0 100%);
}
```

**Practical Examples:**

```css
/* Diagonal hero section */
.hero {
    clip-path: polygon(0 0, 100% 0, 100% 85%, 0 100%);
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

/* Notched corner card */
.card {
    clip-path: polygon(0 0, calc(100% - 20px) 0, 100% 20px, 100% 100%, 0 100%);
}

/* Reveal animation */
@keyframes reveal {
    from {
        clip-path: inset(0 100% 0 0);
    }
    to {
        clip-path: inset(0 0 0 0);
    }
}

.reveal-text {
    animation: reveal 1s ease-out;
}
```

---

### Q26: What is CSS `object-fit` and `object-position`?

**Answer:**

Controls how images/videos fit within their container.

```css
/* object-fit values */
img {
    width: 300px;
    height: 200px;
}

.fill {
    object-fit: fill; /* Default - stretch to fit */
}

.contain {
    object-fit: contain; /* Fit inside, maintain aspect ratio */
}

.cover {
    object-fit: cover; /* Fill container, crop if needed */
}

.none {
    object-fit: none; /* Original size */
}

.scale-down {
    object-fit: scale-down; /* Smaller of 'none' or 'contain' */
}

/* object-position */
.hero-image {
    object-fit: cover;
    object-position: center top; /* Position within container */
}

.profile-pic {
    object-fit: cover;
    object-position: 50% 20%; /* Focus on face */
}
```

**Practical Example:**

```css
/* Responsive image grid */
.gallery {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 1rem;
}

.gallery img {
    width: 100%;
    height: 250px;
    object-fit: cover;
    object-position: center;
    border-radius: 8px;
}

/* Video background */
.video-bg {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
    z-index: -1;
}

/* Avatar */
.avatar {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    object-fit: cover;
}
```

---

### Q27: What is CSS `aspect-ratio` property?

**Answer:**

Maintains a specific width-to-height ratio for elements.

```css
/* Basic usage */
.video-container {
    aspect-ratio: 16 / 9; /* 16:9 video */
}

.square {
    aspect-ratio: 1 / 1; /* Square */
}

.portrait {
    aspect-ratio: 3 / 4; /* Portrait photo */
}

/* Old method (before aspect-ratio) */
.video-wrapper {
    position: relative;
    padding-bottom: 56.25%; /* 16:9 = 9/16 * 100 */
    height: 0;
}

.video-wrapper iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}

/* Modern method */
.video-wrapper {
    aspect-ratio: 16 / 9;
}

.video-wrapper iframe {
    width: 100%;
    height: 100%;
}
```

**Responsive Cards:**

```css
.card-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 2rem;
}

.card {
    aspect-ratio: 1 / 1;
    background: #f0f0f0;
    border-radius: 12px;
    overflow: hidden;
}

.card img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}
```

---

### Q28: What is CSS `contain` property?

**Answer:**

Tells the browser that an element's contents are independent from the rest of the page, allowing for performance optimizations.

```css
/* Values */
.card {
    contain: none;        /* No containment */
    contain: layout;      /* Layout containment */
    contain: paint;       /* Paint containment */
    contain: size;        /* Size containment */
    contain: style;       /* Style containment */
    contain: content;     /* layout + paint + style */
    contain: strict;      /* layout + paint + size + style */
}

/* Practical usage */
.infinite-scroll-item {
    contain: content; /* Optimize rendering */
}

.widget {
    contain: layout style paint;
}
```

**Performance Optimization:**

```css
/* Long list of items */
.list-item {
    contain: layout paint style;
    /* Browser can optimize repainting */
}

/* Independent widgets */
.dashboard-widget {
    contain: content;
    /* Changes inside don't affect outside */
}
```

---

### Q29: What are CSS logical properties?

**Answer:**

Writing-mode independent properties that adapt to text direction (LTR/RTL).

```css
/* Old way - Physical properties */
.element {
    margin-left: 1rem;
    padding-right: 2rem;
    border-top: 1px solid;
}

/* New way - Logical properties */
.element {
    margin-inline-start: 1rem;   /* Left in LTR, Right in RTL */
    padding-inline-end: 2rem;    /* Right in LTR, Left in RTL */
    border-block-start: 1px solid; /* Top in horizontal text */
}

/* Mapping */
/* Physical → Logical */
/* left/right → inline-start/inline-end */
/* top/bottom → block-start/block-end */
/* width → inline-size */
/* height → block-size */
```

**Complete Example:**

```css
.card {
    /* Size */
    inline-size: 300px;        /* width */
    block-size: 200px;         /* height */
    max-inline-size: 100%;     /* max-width */
    
    /* Spacing */
    margin-block: 1rem;        /* margin-top + margin-bottom */
    margin-inline: 2rem;       /* margin-left + margin-right */
    padding-block-start: 1rem; /* padding-top */
    padding-inline-end: 2rem;  /* padding-right */
    
    /* Borders */
    border-inline-start: 3px solid blue;
    border-block-end: 1px solid gray;
    
    /* Border radius */
    border-start-start-radius: 8px; /* top-left */
    border-start-end-radius: 8px;   /* top-right */
}

/* Shorthand */
.element {
    margin-block: 1rem 2rem;     /* top bottom */
    margin-inline: 1rem 2rem;    /* left right */
    padding-block: 1rem;         /* top & bottom */
    padding-inline: 2rem;        /* left & right */
}
```

**Benefits:**

```css
/* Automatically adapts to RTL */
html[dir="rtl"] .card {
    /* No need to override - logical properties adapt automatically */
}

/* Internationalization-friendly */
.sidebar {
    margin-inline-end: 2rem;
    /* In LTR: margin-right: 2rem */
    /* In RTL: margin-left: 2rem */
}
```

---

### Q30: What is CSS `gap` property?

**Answer:**

Creates spacing between flex/grid items without margins.

```css
/* Flexbox gap */
.flex-container {
    display: flex;
    gap: 1rem; /* Space between all items */
}

.flex-container-xy {
    display: flex;
    row-gap: 1rem;    /* Vertical spacing */
    column-gap: 2rem; /* Horizontal spacing */
}

/* Grid gap */
.grid-container {
    display: grid;
    gap: 2rem 1rem; /* row-gap column-gap */
}

/* Why gap is better than margin */
/* ❌ Old way - requires margin hack */
.flex-old {
    display: flex;
    margin: -0.5rem; /* Negative margin */
}

.flex-old > * {
    margin: 0.5rem; /* Compensate */
}

/* ✅ New way - clean and simple */
.flex-new {
    display: flex;
    gap: 1rem;
}
```

**Practical Examples:**

```css
/* Navigation */
.nav {
    display: flex;
    gap: 2rem;
}

/* Card grid */
.card-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 2rem 1rem;
}

/* Button group */
.button-group {
    display: flex;
    gap: 0.5rem;
}

/* Form layout */
.form {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
}
```

---

### Q31: What is CSS `min()`, `max()`, `clamp()` functions?

**Answer:**

Mathematical functions for responsive sizing.

```css
/* min() - Use the smallest value */
.container {
    width: min(100%, 1200px);
    /* Width is 100% but never exceeds 1200px */
}

/* max() - Use the largest value */
.text {
    font-size: max(16px, 1rem);
    /* Never smaller than 16px */
}

/* clamp() - Bound value between min and max */
.heading {
    font-size: clamp(1.5rem, 5vw, 3rem);
    /* Min: 1.5rem, Preferred: 5vw, Max: 3rem */
}
```

**Practical Examples:**

```css
/* Responsive container without media queries */
.container {
    width: min(90%, 1200px);
    margin-inline: auto;
    /* On small screens: 90% width */
    /* On large screens: max 1200px */
}

/* Fluid typography */
h1 {
    font-size: clamp(2rem, 5vw + 1rem, 5rem);
}

p {
    font-size: clamp(1rem, 0.5vw + 0.8rem, 1.2rem);
    line-height: clamp(1.5, 1.2vw + 1rem, 2);
}

/* Responsive padding */
.section {
    padding: clamp(2rem, 5vw, 8rem) clamp(1rem, 3vw, 4rem);
}

/* Flexible grid */
.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(min(300px, 100%), 1fr));
    gap: clamp(1rem, 3vw, 3rem);
}

/* Maintain readable line length */
.article {
    max-width: min(65ch, 90%);
    /* Ideal: 65 characters, but max 90% on small screens */
}
```

---

### Q32: What is CSS scroll-snap?

**Answer:**

Creates smooth scrolling snap points for carousels and galleries.

```css
/* Container */
.carousel {
    display: flex;
    overflow-x: auto;
    scroll-snap-type: x mandatory; /* Snap horizontally */
    scroll-behavior: smooth;
    gap: 1rem;
}

/* Items */
.carousel-item {
    flex: 0 0 300px;
    scroll-snap-align: start; /* Snap to start of container */
}

/* Values */
scroll-snap-type: x mandatory;  /* Always snap, horizontal */
scroll-snap-type: y proximity;  /* Snap when near, vertical */
scroll-snap-type: both mandatory; /* Both axes */

scroll-snap-align: start;   /* Align to start */
scroll-snap-align: center;  /* Align to center */
scroll-snap-align: end;     /* Align to end */
```

**Complete Carousel Example:**

```css
.slider-container {
    overflow: hidden;
}

.slider {
    display: flex;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    scroll-behavior: smooth;
    -webkit-overflow-scrolling: touch; /* Smooth iOS scrolling */
    scrollbar-width: none; /* Hide scrollbar Firefox */
}

.slider::-webkit-scrollbar {
    display: none; /* Hide scrollbar Chrome/Safari */
}

.slide {
    flex: 0 0 100%;
    scroll-snap-align: start;
    scroll-snap-stop: always; /* Force stop at each slide */
}

/* Full-page sections */
.sections {
    height: 100vh;
    overflow-y: auto;
    scroll-snap-type: y mandatory;
}

.section {
    height: 100vh;
    scroll-snap-align: start;
}
```

**Image Gallery:**

```css
.gallery {
    display: grid;
    grid-auto-flow: column;
    grid-auto-columns: calc(33.333% - 1rem);
    gap: 1rem;
    overflow-x: auto;
    scroll-snap-type: x proximity;
    padding: 1rem;
}

.gallery img {
    scroll-snap-align: center;
    border-radius: 8px;
}
```

---

### Q33: What is CSS `backdrop-filter`?

**Answer:**

Applies filters to the area behind an element (like frosted glass effect).

```css
/* Glass morphism effect */
.glass-card {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.2);
    border-radius: 12px;
    padding: 2rem;
}

/* Available filters */
.element {
    backdrop-filter: blur(10px);
    backdrop-filter: brightness(150%);
    backdrop-filter: contrast(80%);
    backdrop-filter: grayscale(50%);
    backdrop-filter: hue-rotate(90deg);
    backdrop-filter: invert(75%);
    backdrop-filter: opacity(25%);
    backdrop-filter: saturate(200%);
    backdrop-filter: sepia(60%);
    
    /* Multiple filters */
    backdrop-filter: blur(10px) brightness(110%);
}
```

**Complete Glass Morphism Example:**

```css
.glass-navbar {
    position: fixed;
    top: 0;
    width: 100%;
    background: rgba(255, 255, 255, 0.15);
    backdrop-filter: blur(20px) saturate(180%);
    -webkit-backdrop-filter: blur(20px) saturate(180%);
    border-bottom: 1px solid rgba(255, 255, 255, 0.3);
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

.modal-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.3);
    backdrop-filter: blur(5px);
    display: flex;
    align-items: center;
    justify-content: center;
}

.modal {
    background: rgba(255, 255, 255, 0.95);
    backdrop-filter: blur(20px);
    border-radius: 16px;
    padding: 2rem;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
}

/* Dark theme glass */
.glass-dark {
    background: rgba(0, 0, 0, 0.3);
    backdrop-filter: blur(15px) brightness(80%);
    border: 1px solid rgba(255, 255, 255, 0.1);
}
```

---

### Q34: What is CSS `mask` property?

**Answer:**

Creates alpha masks to hide portions of elements.

```css
/* Image mask */
.masked-image {
    mask-image: url('mask.png');
    mask-size: cover;
}

/* Gradient mask */
.fade-out {
    mask-image: linear-gradient(to bottom, black 80%, transparent);
}

/* Multiple masks */
.element {
    mask-image: linear-gradient(to right, transparent, black),
                linear-gradient(to bottom, black, transparent);
    mask-composite: intersect;
}

/* SVG mask */
.svg-mask {
    mask-image: url('#wave-mask');
}
```

**Practical Examples:**

```css
/* Fade text at edges */
.scrollable-text {
    overflow: hidden;
    mask-image: linear-gradient(
        to right,
        transparent,
        black 10%,
        black 90%,
        transparent
    );
}

/* Image reveal on scroll */
.reveal-image {
    mask-image: linear-gradient(to top, black var(--scroll), transparent);
}

/* Circular reveal */
.circle-reveal {
    mask-image: radial-gradient(circle at center, black 50%, transparent 50%);
    mask-size: 0%;
    transition: mask-size 0.5s;
}

.circle-reveal:hover {
    mask-size: 200%;
}

/* Text gradient mask */
.gradient-text {
    background: linear-gradient(45deg, #667eea, #764ba2);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
}
```

---

### Q35: What is CSS `will-change` property?

**Answer:**

Hints to the browser which properties will change, allowing for performance optimizations.

```css
/* Tell browser to optimize for transform */
.animated-element {
    will-change: transform;
}

/* Multiple properties */
.complex-animation {
    will-change: transform, opacity;
}

/* Auto (let browser decide) */
.element {
    will-change: auto;
}
```

**Best Practices:**

```css
/* ❌ Don't use on too many elements */
* {
    will-change: transform; /* BAD - wastes memory */
}

/* ❌ Don't use permanently */
.element {
    will-change: transform; /* BAD - always optimized */
}

/* ✅ Apply before animation, remove after */
.element {
    transition: transform 0.3s;
}

.element:hover {
    will-change: transform;
}

.element:active {
    transform: scale(1.1);
}

/* ✅ Use JavaScript for complex scenarios */
```

```javascript
// Apply will-change before animation
element.addEventListener('mouseenter', () => {
    element.style.willChange = 'transform';
});

// Remove after animation
element.addEventListener('animationend', () => {
    element.style.willChange = 'auto';
});
```

**When to Use:**

```css
/* Smooth scrolling animations */
.scroll-animated {
    will-change: transform;
}

/* Carousel slides */
.carousel-slide {
    will-change: transform, opacity;
}

/* Modal animations */
.modal {
    will-change: transform, opacity;
}

.modal.open {
    transform: scale(1);
    opacity: 1;
}
```

---

### Q36: What is CSS `content-visibility`?

**Answer:**

Allows the browser to skip rendering work for off-screen content.

```css
/* Skip rendering until needed */
.blog-post {
    content-visibility: auto;
    contain-intrinsic-size: 500px; /* Estimated height */
}

/* Values */
.element {
    content-visibility: visible;  /* Default */
    content-visibility: hidden;   /* Skip rendering */
    content-visibility: auto;     /* Skip if off-screen */
}
```

**Performance Optimization:**

```css
/* Long list of items */
.list-item {
    content-visibility: auto;
    contain-intrinsic-size: 200px;
}

/* Blog feed */
.article {
    content-visibility: auto;
    contain-intrinsic-size: 1000px;
}

/* Image gallery */
.gallery-item {
    content-visibility: auto;
    contain-intrinsic-size: 300px 200px; /* width height */
}
```

**Impact:**
- Faster initial page load
- Reduced rendering time
- Better scroll performance
- Lower memory usage

---

### Q37: What are CSS container queries?

**Answer:**

Style elements based on their container's size (not viewport size).

```css
/* Define container */
.sidebar {
    container-type: inline-size;
    container-name: sidebar;
}

/* Query container */
.card {
    display: block;
}

@container sidebar (min-width: 400px) {
    .card {
        display: grid;
        grid-template-columns: 150px 1fr;
    }
}

/* Container types */
.element {
    container-type: size;        /* Query width and height */
    container-type: inline-size; /* Query inline direction (width) */
    container-type: normal;      /* Not a container */
}
```

**Practical Example:**

```css
/* Responsive card that adapts to its container */
.card-container {
    container-type: inline-size;
    container-name: card;
}

.card {
    padding: 1rem;
}

.card h2 {
    font-size: 1rem;
}

/* When container is 300px or larger */
@container card (min-width: 300px) {
    .card {
        display: grid;
        grid-template-columns: 100px 1fr;
        gap: 1rem;
    }
    
    .card h2 {
        font-size: 1.5rem;
    }
}

/* When container is 500px or larger */
@container card (min-width: 500px) {
    .card {
        grid-template-columns: 150px 1fr;
        padding: 2rem;
    }
    
    .card h2 {
        font-size: 2rem;
    }
}
```

**Benefits:**
- Truly reusable components
- Components adapt to their space, not viewport
- Better than media queries for component libraries

---

### Q38: What is CSS `scroll-behavior`?

**Answer:**

Controls the scrolling behavior for scroll actions.

```css
/* Smooth scrolling */
html {
    scroll-behavior: smooth;
}

/* Values */
element {
    scroll-behavior: auto;   /* Instant */
    scroll-behavior: smooth; /* Animated */
}
```

**Practical Usage:**

```css
/* Smooth anchor links */
html {
    scroll-behavior: smooth;
}

/* Link to sections */
<a href="#section2">Go to Section 2</a>

<section id="section2">
    <!-- Content -->
</section>

/* Disable for accessibility */
@media (prefers-reduced-motion: reduce) {
    html {
        scroll-behavior: auto;
    }
}
```

**JavaScript Control:**

```javascript
// Smooth scroll to element
element.scrollIntoView({ behavior: 'smooth' });

// Smooth scroll to position
window.scrollTo({
    top: 1000,
    behavior: 'smooth'
});
```

---

### Q39: What is CSS `mix-blend-mode`?

**Answer:**

Defines how an element's content blends with its background.

```css
/* Blend modes */
.element {
    mix-blend-mode: normal;
    mix-blend-mode: multiply;
    mix-blend-mode: screen;
    mix-blend-mode: overlay;
    mix-blend-mode: darken;
    mix-blend-mode: lighten;
    mix-blend-mode: color-dodge;
    mix-blend-mode: color-burn;
    mix-blend-mode: difference;
    mix-blend-mode: exclusion;
    mix-blend-mode: hue;
    mix-blend-mode: saturation;
    mix-blend-mode: color;
    mix-blend-mode: luminosity;
}
```

**Practical Examples:**

```css
/* Text over image */
.hero h1 {
    mix-blend-mode: difference;
    color: white;
    /* Text color inverts based on background */
}

/* Overlay effect */
.image-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(45deg, #667eea, #764ba2);
    mix-blend-mode: multiply;
}

/* Duotone effect */
.duotone {
    position: relative;
}

.duotone::before {
    content: '';
    position: absolute;
    inset: 0;
    background: #667eea;
    mix-blend-mode: color;
}

.duotone::after {
    content: '';
    position: absolute;
    inset: 0;
    background: #764ba2;
    mix-blend-mode: lighten;
}

/* Loading animation with blend mode */
.loader {
    width: 100px;
    height: 100px;
    background: conic-gradient(#667eea, #764ba2, #667eea);
    mix-blend-mode: difference;
    animation: rotate 2s linear infinite;
}
```

---

### Q40: What are CSS color functions?

**Answer:**

Modern ways to define colors with better control.

```css
/* RGB/RGBA */
.element {
    color: rgb(102, 126, 234);
    background: rgba(102, 126, 234, 0.5);
}

/* HSL/HSLA - Hue, Saturation, Lightness */
.element {
    color: hsl(230, 75%, 65%);
    background: hsla(230, 75%, 65%, 0.5);
}

/* Modern syntax (space-separated) */
.element {
    color: rgb(102 126 234);
    color: rgb(102 126 234 / 0.5);
    color: hsl(230 75% 65%);
    color: hsl(230 75% 65% / 50%);
}

/* HWB - Hue, Whiteness, Blackness */
.element {
    color: hwb(230 20% 8%);
}

/* LAB - Lightness, A, B (perceptually uniform) */
.element {
    color: lab(65% 10 -40);
}

/* LCH - Lightness, Chroma, Hue (perceptually uniform) */
.element {
    color: lch(65% 50 230);
}
```

**Practical Usage:**

```css
/* Generate color palette with HSL */
:root {
    --primary-h: 230;
    --primary-s: 75%;
    --primary-l: 65%;
    
    --primary: hsl(var(--primary-h) var(--primary-s) var(--primary-l));
    --primary-light: hsl(var(--primary-h) var(--primary-s) 75%);
    --primary-dark: hsl(var(--primary-h) var(--primary-s) 45%);
    --primary-alpha: hsl(var(--primary-h) var(--primary-s) var(--primary-l) / 0.5);
}

/* Dynamic colors */
.button {
    --hue: 230;
    background: hsl(var(--hue) 75% 65%);
}

.button:hover {
    --hue: 250;
}

/* Accessible color contrasts with LAB */
.text {
    color: lab(20% 0 0); /* Dark text */
    background: lab(98% 0 0); /* Light background */
}
```

---

### Q41: What is CSS `isolation` property?

**Answer:**

Creates a new stacking context to isolate blend modes.

```css
/* Values */
.element {
    isolation: auto;    /* Default */
    isolation: isolate; /* Create stacking context */
}

/* Use case */
.card {
    isolation: isolate;
}

.card-overlay {
    mix-blend-mode: multiply;
    /* Only blends within .card, not with elements outside */
}
```

**Practical Example:**

```css
/* Without isolation - blend mode affects siblings */
.container {
    background: white;
}

.box1 {
    background: red;
}

.box2 {
    background: blue;
    mix-blend-mode: multiply;
    /* Blends with .box1 and .container background */
}

/* With isolation - contain blend mode */
.container {
    background: white;
}

.box1 {
    isolation: isolate;
    background: red;
}

.box2 {
    background: blue;
    mix-blend-mode: multiply;
    /* Only blends with .box1, not .container */
}
```

---

### Q42: What is CSS `all` property?

**Answer:**

Resets all CSS properties to initial, inherit, or unset values.

```css
/* Values */
.element {
    all: initial;  /* Reset to initial browser values */
    all: inherit;  /* Inherit all from parent */
    all: unset;    /* Combination of initial/inherit */
    all: revert;   /* Revert to user-agent stylesheet */
}

/* Use case - Reset widget styles */
.widget {
    all: initial; /* Remove all inherited styles */
    font-family: Arial, sans-serif;
    color: black;
    /* Now style from scratch */
}

/* Iframe content */
.iframe-container * {
    all: revert;
}
```

**Practical Example:**

```css
/* Third-party widget that shouldn't inherit styles */
.external-widget {
    all: initial;
    display: block;
    font-family: system-ui;
}

/* Reset button styles */
.custom-button {
    all: unset;
    /* Start fresh, then add custom styles */
    display: inline-block;
    padding: 0.5rem 1rem;
    background: #667eea;
    color: white;
    border-radius: 4px;
    cursor: pointer;
}
```

---

### Q43: What are CSS math functions (`calc()`, `min()`, `max()`, `clamp()`)?

**Answer:**

Perform calculations and comparisons in CSS.

```css
/* calc() - Mathematical calculations */
.element {
    width: calc(100% - 50px);
    padding: calc(1rem + 5px);
    height: calc(100vh - 60px); /* Full height minus navbar */
}

/* Nested calc */
.element {
    width: calc(100% / 3 - 1rem * 2);
}

/* min() - Smallest value */
.container {
    width: min(90%, 1200px);
    /* Use 90% or 1200px, whichever is smaller */
}

/* max() - Largest value */
.text {
    font-size: max(1rem, 16px);
    /* Never smaller than 16px */
}

/* clamp() - Bounded value */
.heading {
    font-size: clamp(1.5rem, 5vw, 3rem);
    /* Min: 1.5rem, Preferred: 5vw, Max: 3rem */
}
```

**Complex Examples:**

```css
/* Responsive grid without media queries */
.grid {
    display: grid;
    grid-template-columns: repeat(
        auto-fit,
        minmax(min(300px, 100%), 1fr)
    );
    gap: clamp(1rem, 3vw, 3rem);
}

/* Fluid spacing */
.section {
    padding-block: clamp(2rem, 5vh, 8rem);
    padding-inline: clamp(1rem, 5vw, 4rem);
}

/* Maintain aspect ratio with padding */
.video-container {
    padding-bottom: calc(9 / 16 * 100%); /* 16:9 aspect ratio */
}

/* Responsive font sizing */
:root {
    --font-size-sm: clamp(0.875rem, 0.5vw + 0.75rem, 1rem);
    --font-size-base: clamp(1rem, 0.5vw + 0.85rem, 1.125rem);
    --font-size-lg: clamp(1.25rem, 1vw + 1rem, 1.5rem);
    --font-size-xl: clamp(1.5rem, 2vw + 1rem, 2.5rem);
}
```

---

### Q44: What is CSS `counter` for automatic numbering?

**Answer:**

Create automatic counters for lists, headings, etc.

```css
/* Basic counter */
.list {
    counter-reset: item;
}

.list li::before {
    counter-increment: item;
    content: counter(item) ". ";
}

/* Nested counters */
.outline {
    counter-reset: section;
}

.outline h2 {
    counter-reset: subsection;
}

.outline h2::before {
    counter-increment: section;
    content: counter(section) ". ";
}

.outline h3::before {
    counter-increment: subsection;
    content: counter(section) "." counter(subsection) " ";
}
```

**Practical Examples:**

```css
/* Numbered headings */
article {
    counter-reset: heading;
}

article h2::before {
    counter-increment: heading;
    content: counter(heading) ". ";
    color: #667eea;
    font-weight: bold;
}

/* Custom list styling */
.custom-list {
    list-style: none;
    counter-reset: item;
}

.custom-list li {
    counter-increment: item;
    position: relative;
    padding-left: 3rem;
}

.custom-list li::before {
    content: counter(item);
    position: absolute;
    left: 0;
    width: 2rem;
    height: 2rem;
    background: #667eea;
    color: white;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: bold;
}

/* Different counter styles */
.roman {
    list-style: none;
    counter-reset: item;
}

.roman li::before {
    counter-increment: item;
    content: counter(item, upper-roman) ") ";
}
/* Generates: I) II) III) */

/* counter styles: decimal, upper-roman, lower-roman, 
   upper-alpha, lower-alpha, upper-greek, lower-greek */
```

---

### Q45: What is CSS `@supports` (feature queries)?

**Answer:**

Test if the browser supports specific CSS features.

```css
/* Basic usage */
@supports (display: grid) {
    .container {
        display: grid;
    }
}

/* Fallback for older browsers */
.container {
    display: flex; /* Fallback */
}

@supports (display: grid) {
    .container {
        display: grid; /* Modern browsers */
    }
}

/* Logical operators */
/* AND */
@supports (display: grid) and (gap: 1rem) {
    .grid {
        display: grid;
        gap: 1rem;
    }
}

/* OR */
@supports (display: flex) or (display: grid) {
    .container {
        /* Code for flex or grid support */
    }
}

/* NOT */
@supports not (display: grid) {
    .container {
        display: flex; /* Fallback for no grid support */
    }
}
```

**Practical Examples:**

```css
/* Test for backdrop-filter support */
.glass {
    background: rgba(255, 255, 255, 0.7);
}

@supports (backdrop-filter: blur(10px)) {
    .glass {
        background: rgba(255, 255, 255, 0.1);
        backdrop-filter: blur(10px);
    }
}

/* Test for CSS Grid support */
.layout {
    display: flex;
    flex-wrap: wrap;
}

@supports (display: grid) {
    .layout {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
        gap: 2rem;
    }
}

/* Test for sticky positioning */
.navbar {
    position: relative;
    top: 0;
}

@supports (position: sticky) {
    .navbar {
        position: sticky;
        top: 0;
    }
}

/* Test for CSS custom properties */
@supports (--css: variables) {
    :root {
        --primary: #667eea;
    }
    
    .button {
        background: var(--primary);
    }
}
```

---

### Q46: What is CSS `@layer` (cascade layers)?

**Answer:**

Control the cascade order of CSS rules.

```css
/* Define layers (order matters) */
@layer reset, base, components, utilities;

/* Add rules to layers */
@layer reset {
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
}

@layer base {
    body {
        font-family: system-ui;
        line-height: 1.5;
    }
}

@layer components {
    .button {
        padding: 0.5rem 1rem;
        border-radius: 4px;
    }
}

@layer utilities {
    .text-center {
        text-align: center;
    }
}
```

**Benefits:**

```css
/* Utilities always override components */
@layer components, utilities;

@layer components {
    .button {
        color: blue; /* Lower priority */
    }
}

@layer utilities {
    .text-red {
        color: red; /* Higher priority */
    }
}

/* <button class="button text-red"> will be red */

/* Import into layers */
@import url('reset.css') layer(reset);
@import url('components.css') layer(components);

/* Anonymous layers */
@layer {
    .special {
        color: purple;
    }
}
```

---

### Q47: What is CSS `:has()` selector (parent selector)?

**Answer:**

Select parent elements based on their children (revolutionary!).

```css
/* Select .card that has an image */
.card:has(img) {
    display: grid;
    grid-template-columns: 200px 1fr;
}

/* Select .card without an image */
.card:not(:has(img)) {
    display: block;
}

/* Select form that has invalid input */
form:has(input:invalid) {
    border: 2px solid red;
}

/* Select label that has checked checkbox */
label:has(input[type="checkbox"]:checked) {
    font-weight: bold;
    color: green;
}
```

**Powerful Examples:**

```css
/* Style parent based on child state */
.article:has(h2) {
    padding-top: 2rem;
}

/* Different layouts based on content */
.container:has(> .sidebar) {
    display: grid;
    grid-template-columns: 250px 1fr;
}

.container:not(:has(> .sidebar)) {
    max-width: 800px;
    margin: 0 auto;
}

/* Interactive UI without JavaScript */
.accordion:has(.panel:hover) {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

/* Form validation styling */
.form-group:has(input:focus) label {
    color: #667eea;
    transform: translateY(-1.5rem);
}

.form-group:has(input:invalid:not(:placeholder-shown)) {
    border-color: red;
}

.form-group:has(input:valid:not(:placeholder-shown)) {
    border-color: green;
}

/* Select previous sibling (kind of) */
.item:has(+ .item:hover) {
    opacity: 0.5;
}
```

---

### Q48: What are CSS `@property` custom properties?

**Answer:**

Define custom properties with type checking and default values.

```css
/* Register custom property */
@property --gradient-angle {
    syntax: '<angle>';
    initial-value: 0deg;
    inherits: false;
}

/* Now can animate it! */
.gradient-bg {
    background: linear-gradient(var(--gradient-angle), #667eea, #764ba2);
    animation: rotate 3s linear infinite;
}

@keyframes rotate {
    to {
        --gradient-angle: 360deg;
    }
}

/* Different syntax types */
@property --color {
    syntax: '<color>';
    initial-value: blue;
    inherits: true;
}

@property --spacing {
    syntax: '<length>';
    initial-value: 0px;
    inherits: false;
}

@property --scale {
    syntax: '<number>';
    initial-value: 1;
    inherits: false;
}
```

**Practical Examples:**

```css
/* Animate gradient */
@property --gradient-position {
    syntax: '<percentage>';
    initial-value: 0%;
    inherits: false;
}

.animated-gradient {
    background: linear-gradient(
        90deg,
        #667eea 0%,
        #764ba2 var(--gradient-position),
        #667eea 100%
    );
    animation: gradient-move 3s ease infinite;
}

@keyframes gradient-move {
    0%, 100% { --gradient-position: 0%; }
    50% { --gradient-position: 100%; }
}

/* Animated blur */
@property --blur-amount {
    syntax: '<length>';
    initial-value: 0px;
    inherits: false;
}

.hover-blur {
    filter: blur(var(--blur-amount));
    transition: --blur-amount 0.3s;
}

.hover-blur:hover {
    --blur-amount: 5px;
}
```

---

### Q49: What is CSS `color-scheme` property?

**Answer:**

Indicates which color schemes (light/dark) an element can be rendered with.

```css
/* Support dark mode */
:root {
    color-scheme: light dark;
}

/* Force light mode */
.light-only {
    color-scheme: light;
}

/* Force dark mode */
.dark-only {
    color-scheme: dark;
}

/* Affects form controls, scrollbars, system colors */
html {
    color-scheme: light dark;
}

/* Now <input>, <select> adapt to system theme */
```

**Complete Dark Mode Setup:**

```css
/* Define color schemes */
:root {
    color-scheme: light dark;
    
    /* Light mode colors */
    --bg: white;
    --text: #333;
    --border: #ddd;
}

@media (prefers-color-scheme: dark) {
    :root {
        --bg: #1a1a1a;
        --text: #e0e0e0;
        --border: #444;
    }
}

/* Use colors */
body {
    background: var(--bg);
    color: var(--text);
}

/* Manual toggle */
[data-theme="light"] {
    color-scheme: light;
    --bg: white;
    --text: #333;
}

[data-theme="dark"] {
    color-scheme: dark;
    --bg: #1a1a1a;
    --text: #e0e0e0;
}
```

---

### Q50: What are CSS subgrid and nested grids?

**Answer:**

Allow grid items to inherit parent's grid tracks.

```css
/* Parent grid */
.grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 1rem;
}

/* Child inherits parent's columns */
.card {
    display: grid;
    grid-column: span 2;
    grid-template-columns: subgrid; /* Inherit parent columns */
    gap: 0.5rem;
}

/* Subgrid for rows */
.item {
    display: grid;
    grid-row: span 3;
    grid-template-rows: subgrid;
}
```

**Practical Example:**

```css
/* Product grid with aligned prices */
.products {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 2rem;
}

.product {
    display: grid;
    grid-template-rows: subgrid;
    grid-row: span 4; /* Image, title, description, price */
    gap: 1rem;
}

/* All prices align across cards */
.product-image { /* Row 1 */ }
.product-title { /* Row 2 */ }
.product-desc { /* Row 3 */ }
.product-price { /* Row 4 - aligned! */ }

/* Complex layout with subgrid */
.page {
    display: grid;
    grid-template-columns: 200px 1fr 200px;
    grid-template-rows: auto 1fr auto;
    gap: 1rem;
}

.content {
    display: grid;
    grid-column: 2;
    grid-template-columns: subgrid;
    grid-template-rows: subgrid;
}
```

---

## Most Useful CSS Styles & Utilities

### Reset & Base Styles

```css
/* Modern CSS Reset */
*, *::before, *::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

html {
    font-size: 16px;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

body {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', 
                 Roboto, 'Helvetica Neue', Arial, sans-serif;
    line-height: 1.6;
    color: #333;
    background: #fff;
}

img, picture, video, canvas, svg {
    display: block;
    max-width: 100%;
    height: auto;
}

input, button, textarea, select {
    font: inherit;
}

button {
    cursor: pointer;
}

a {
    color: inherit;
    text-decoration: none;
}

ul, ol {
    list-style: none;
}

/* Remove all animations for people who prefer reduced motion */
@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
        scroll-behavior: auto !important;
    }
}
```

---

### Flexbox Utilities

```css
/* Flex containers */
.flex { display: flex; }
.inline-flex { display: inline-flex; }

/* Direction */
.flex-row { flex-direction: row; }
.flex-col { flex-direction: column; }
.flex-row-reverse { flex-direction: row-reverse; }
.flex-col-reverse { flex-direction: column-reverse; }

/* Wrap */
.flex-wrap { flex-wrap: wrap; }
.flex-nowrap { flex-wrap: nowrap; }

/* Justify (main axis) */
.justify-start { justify-content: flex-start; }
.justify-end { justify-content: flex-end; }
.justify-center { justify-content: center; }
.justify-between { justify-content: space-between; }
.justify-around { justify-content: space-around; }
.justify-evenly { justify-content: space-evenly; }

/* Align items (cross axis) */
.items-start { align-items: flex-start; }
.items-end { align-items: flex-end; }
.items-center { align-items: center; }
.items-baseline { align-items: baseline; }
.items-stretch { align-items: stretch; }

/* Align content (multiple lines) */
.content-start { align-content: flex-start; }
.content-end { align-content: flex-end; }
.content-center { align-content: center; }
.content-between { align-content: space-between; }
.content-around { align-content: space-around; }

/* Gap */
.gap-0 { gap: 0; }
.gap-1 { gap: 0.25rem; }
.gap-2 { gap: 0.5rem; }
.gap-3 { gap: 0.75rem; }
.gap-4 { gap: 1rem; }
.gap-6 { gap: 1.5rem; }
.gap-8 { gap: 2rem; }

/* Flex grow/shrink */
.flex-1 { flex: 1 1 0%; }
.flex-auto { flex: 1 1 auto; }
.flex-none { flex: none; }
.grow { flex-grow: 1; }
.grow-0 { flex-grow: 0; }
.shrink { flex-shrink: 1; }
.shrink-0 { flex-shrink: 0; }
```

---

### Grid Utilities

```css
/* Grid display */
.grid { display: grid; }
.inline-grid { display: inline-grid; }

/* Columns */
.grid-cols-1 { grid-template-columns: repeat(1, minmax(0, 1fr)); }
.grid-cols-2 { grid-template-columns: repeat(2, minmax(0, 1fr)); }
.grid-cols-3 { grid-template-columns: repeat(3, minmax(0, 1fr)); }
.grid-cols-4 { grid-template-columns: repeat(4, minmax(0, 1fr)); }
.grid-cols-6 { grid-template-columns: repeat(6, minmax(0, 1fr)); }
.grid-cols-12 { grid-template-columns: repeat(12, minmax(0, 1fr)); }

/* Rows */
.grid-rows-1 { grid-template-rows: repeat(1, minmax(0, 1fr)); }
.grid-rows-2 { grid-template-rows: repeat(2, minmax(0, 1fr)); }
.grid-rows-3 { grid-template-rows: repeat(3, minmax(0, 1fr)); }
.grid-rows-4 { grid-template-rows: repeat(4, minmax(0, 1fr)); }

/* Column span */
.col-span-1 { grid-column: span 1 / span 1; }
.col-span-2 { grid-column: span 2 / span 2; }
.col-span-3 { grid-column: span 3 / span 3; }
.col-span-4 { grid-column: span 4 / span 4; }
.col-span-full { grid-column: 1 / -1; }

/* Row span */
.row-span-1 { grid-row: span 1 / span 1; }
.row-span-2 { grid-row: span 2 / span 2; }
.row-span-3 { grid-row: span 3 / span 3; }
.row-span-full { grid-row: 1 / -1; }

/* Auto-fit responsive grid */
.grid-auto-fit {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 1rem;
}

/* Auto-fill responsive grid */
.grid-auto-fill {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 1rem;
}
```

---

### Spacing Utilities

```css
/* Margin */
.m-0 { margin: 0; }
.m-1 { margin: 0.25rem; }
.m-2 { margin: 0.5rem; }
.m-3 { margin: 0.75rem; }
.m-4 { margin: 1rem; }
.m-6 { margin: 1.5rem; }
.m-8 { margin: 2rem; }
.m-auto { margin: auto; }

/* Margin directional */
.mt-4 { margin-top: 1rem; }
.mr-4 { margin-right: 1rem; }
.mb-4 { margin-bottom: 1rem; }
.ml-4 { margin-left: 1rem; }
.mx-4 { margin-left: 1rem; margin-right: 1rem; }
.my-4 { margin-top: 1rem; margin-bottom: 1rem; }

/* Padding */
.p-0 { padding: 0; }
.p-1 { padding: 0.25rem; }
.p-2 { padding: 0.5rem; }
.p-3 { padding: 0.75rem; }
.p-4 { padding: 1rem; }
.p-6 { padding: 1.5rem; }
.p-8 { padding: 2rem; }

/* Padding directional */
.pt-4 { padding-top: 1rem; }
.pr-4 { padding-right: 1rem; }
.pb-4 { padding-bottom: 1rem; }
.pl-4 { padding-left: 1rem; }
.px-4 { padding-left: 1rem; padding-right: 1rem; }
.py-4 { padding-top: 1rem; padding-bottom: 1rem; }
```

---

### Typography Utilities

```css
/* Font size */
.text-xs { font-size: 0.75rem; line-height: 1rem; }
.text-sm { font-size: 0.875rem; line-height: 1.25rem; }
.text-base { font-size: 1rem; line-height: 1.5rem; }
.text-lg { font-size: 1.125rem; line-height: 1.75rem; }
.text-xl { font-size: 1.25rem; line-height: 1.75rem; }
.text-2xl { font-size: 1.5rem; line-height: 2rem; }
.text-3xl { font-size: 1.875rem; line-height: 2.25rem; }
.text-4xl { font-size: 2.25rem; line-height: 2.5rem; }

/* Font weight */
.font-thin { font-weight: 100; }
.font-light { font-weight: 300; }
.font-normal { font-weight: 400; }
.font-medium { font-weight: 500; }
.font-semibold { font-weight: 600; }
.font-bold { font-weight: 700; }
.font-black { font-weight: 900; }

/* Text align */
.text-left { text-align: left; }
.text-center { text-align: center; }
.text-right { text-align: right; }
.text-justify { text-align: justify; }

/* Text transform */
.uppercase { text-transform: uppercase; }
.lowercase { text-transform: lowercase; }
.capitalize { text-transform: capitalize; }
.normal-case { text-transform: none; }

/* Text decoration */
.underline { text-decoration: underline; }
.line-through { text-decoration: line-through; }
.no-underline { text-decoration: none; }

/* Text ellipsis */
.truncate {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}

/* Line clamp */
.line-clamp-2 {
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
}

.line-clamp-3 {
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow: hidden;
}
```

---

### Color Utilities

```css
/* Text colors */
.text-white { color: #fff; }
.text-black { color: #000; }
.text-gray-400 { color: #9ca3af; }
.text-gray-600 { color: #4b5563; }
.text-gray-800 { color: #1f2937; }
.text-blue-500 { color: #3b82f6; }
.text-red-500 { color: #ef4444; }
.text-green-500 { color: #10b981; }

/* Background colors */
.bg-white { background-color: #fff; }
.bg-black { background-color: #000; }
.bg-gray-100 { background-color: #f3f4f6; }
.bg-gray-200 { background-color: #e5e7eb; }
.bg-blue-500 { background-color: #3b82f6; }
.bg-red-500 { background-color: #ef4444; }
.bg-green-500 { background-color: #10b981; }

/* Opacity */
.opacity-0 { opacity: 0; }
.opacity-25 { opacity: 0.25; }
.opacity-50 { opacity: 0.5; }
.opacity-75 { opacity: 0.75; }
.opacity-100 { opacity: 1; }
```

---

### Border Utilities

```css
/* Border width */
.border { border-width: 1px; }
.border-2 { border-width: 2px; }
.border-4 { border-width: 4px; }
.border-0 { border-width: 0; }

.border-t { border-top-width: 1px; }
.border-r { border-right-width: 1px; }
.border-b { border-bottom-width: 1px; }
.border-l { border-left-width: 1px; }

/* Border radius */
.rounded-none { border-radius: 0; }
.rounded-sm { border-radius: 0.125rem; }
.rounded { border-radius: 0.25rem; }
.rounded-md { border-radius: 0.375rem; }
.rounded-lg { border-radius: 0.5rem; }
.rounded-xl { border-radius: 0.75rem; }
.rounded-2xl { border-radius: 1rem; }
.rounded-full { border-radius: 9999px; }

/* Border style */
.border-solid { border-style: solid; }
.border-dashed { border-style: dashed; }
.border-dotted { border-style: dotted; }
.border-none { border-style: none; }

/* Border color */
.border-gray-200 { border-color: #e5e7eb; }
.border-gray-300 { border-color: #d1d5db; }
.border-blue-500 { border-color: #3b82f6; }
```

---

### Shadow Utilities

```css
/* Box shadow */
.shadow-sm {
    box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
}

.shadow {
    box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1),
                0 1px 2px 0 rgba(0, 0, 0, 0.06);
}

.shadow-md {
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
                0 2px 4px -1px rgba(0, 0, 0, 0.06);
}

.shadow-lg {
    box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1),
                0 4px 6px -2px rgba(0, 0, 0, 0.05);
}

.shadow-xl {
    box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1),
                0 10px 10px -5px rgba(0, 0, 0, 0.04);
}

.shadow-2xl {
    box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
}

.shadow-none {
    box-shadow: none;
}

/* Inner shadow */
.shadow-inner {
    box-shadow: inset 0 2px 4px 0 rgba(0, 0, 0, 0.06);
}
```

---

### Position & Display Utilities

```css
/* Display */
.block { display: block; }
.inline-block { display: inline-block; }
.inline { display: inline; }
.hidden { display: none; }

/* Position */
.static { position: static; }
.relative { position: relative; }
.absolute { position: absolute; }
.fixed { position: fixed; }
.sticky { position: sticky; }

/* Position values */
.top-0 { top: 0; }
.right-0 { right: 0; }
.bottom-0 { bottom: 0; }
.left-0 { left: 0; }
.inset-0 { top: 0; right: 0; bottom: 0; left: 0; }

/* Z-index */
.z-0 { z-index: 0; }
.z-10 { z-index: 10; }
.z-20 { z-index: 20; }
.z-30 { z-index: 30; }
.z-40 { z-index: 40; }
.z-50 { z-index: 50; }
```

---

### Width & Height Utilities

```css
/* Width */
.w-auto { width: auto; }
.w-full { width: 100%; }
.w-screen { width: 100vw; }
.w-1\/2 { width: 50%; }
.w-1\/3 { width: 33.333333%; }
.w-2\/3 { width: 66.666667%; }
.w-1\/4 { width: 25%; }
.w-3\/4 { width: 75%; }

/* Max width */
.max-w-xs { max-width: 20rem; }
.max-w-sm { max-width: 24rem; }
.max-w-md { max-width: 28rem; }
.max-w-lg { max-width: 32rem; }
.max-w-xl { max-width: 36rem; }
.max-w-2xl { max-width: 42rem; }
.max-w-full { max-width: 100%; }
.max-w-screen { max-width: 100vw; }

/* Height */
.h-auto { height: auto; }
.h-full { height: 100%; }
.h-screen { height: 100vh; }

/* Min/Max height */
.min-h-screen { min-height: 100vh; }
.min-h-full { min-height: 100%; }
.max-h-full { max-height: 100%; }
.max-h-screen { max-height: 100vh; }
```

---

### Common Component Styles

```css
/* Button */
.btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 0.5rem 1rem;
    font-weight: 500;
    border-radius: 0.375rem;
    border: none;
    cursor: pointer;
    transition: all 0.2s;
}

.btn-primary {
    background: #3b82f6;
    color: white;
}

.btn-primary:hover {
    background: #2563eb;
}

/* Card */
.card {
    background: white;
    border-radius: 0.5rem;
    box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
    padding: 1.5rem;
    transition: box-shadow 0.2s;
}

.card:hover {
    box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
}

/* Container */
.container {
    width: 100%;
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 1rem;
}

/* Badge */
.badge {
    display: inline-block;
    padding: 0.25rem 0.75rem;
    font-size: 0.875rem;
    font-weight: 500;
    border-radius: 9999px;
    background: #e5e7eb;
    color: #374151;
}

/* Input */
.input {
    display: block;
    width: 100%;
    padding: 0.5rem 0.75rem;
    font-size: 1rem;
    border: 1px solid #d1d5db;
    border-radius: 0.375rem;
    transition: border-color 0.2s, box-shadow 0.2s;
}

.input:focus {
    outline: none;
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}
```

---

### Animation Utilities

```css
/* Transitions */
.transition {
    transition-property: all;
    transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
    transition-duration: 150ms;
}

.transition-colors {
    transition-property: color, background-color, border-color;
}

.transition-transform {
    transition-property: transform;
}

.transition-opacity {
    transition-property: opacity;
}

/* Duration */
.duration-150 { transition-duration: 150ms; }
.duration-200 { transition-duration: 200ms; }
.duration-300 { transition-duration: 300ms; }
.duration-500 { transition-duration: 500ms; }

/* Transforms */
.scale-0 { transform: scale(0); }
.scale-50 { transform: scale(0.5); }
.scale-75 { transform: scale(0.75); }
.scale-100 { transform: scale(1); }
.scale-110 { transform: scale(1.1); }
.scale-125 { transform: scale(1.25); }

.rotate-45 { transform: rotate(45deg); }
.rotate-90 { transform: rotate(90deg); }
.rotate-180 { transform: rotate(180deg); }

.translate-x-full { transform: translateX(100%); }
.translate-y-full { transform: translateY(100%); }

/* Hover effects */
.hover\:scale-110:hover { transform: scale(1.1); }
.hover\:shadow-lg:hover {
    box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
}
```

---

### Accessibility Utilities

```css
/* Screen reader only */
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border-width: 0;
}

/* Focus visible */
.focus-visible {
    outline: 2px solid transparent;
    outline-offset: 2px;
}

.focus-visible:focus-visible {
    outline: 2px solid #3b82f6;
}

/* Skip to content */
.skip-to-content {
    position: absolute;
    top: -40px;
    left: 0;
    background: #000;
    color: #fff;
    padding: 8px;
    text-decoration: none;
    z-index: 100;
}

.skip-to-content:focus {
    top: 0;
}
```
