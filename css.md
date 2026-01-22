# CSS - Beginner to Advanced Level Q&A

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
