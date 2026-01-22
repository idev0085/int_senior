# HTML - Beginner to Advanced Level Q&A

## Table of Contents
1. [HTML Basics](#html-basics)
2. [HTML Elements & Structure](#html-elements--structure)
3. [Forms & Input](#forms--input)
4. [Semantic HTML](#semantic-html)
5. [HTML5 Features](#html5-features)
6. [Accessibility](#accessibility)
7. [SEO & Meta Tags](#seo--meta-tags)
8. [Advanced Topics](#advanced-topics)

---

## HTML Basics

### Q1: What is HTML and why is it important?

**Answer:**
HTML (HyperText Markup Language) is the standard markup language for creating web pages. It describes the structure and content of a webpage.

**Simple Analogy:**
If a website is a house:
- **HTML** = The structure (walls, rooms, doors)
- **CSS** = The decoration (paint, furniture, style)
- **JavaScript** = The functionality (lights, plumbing, electronics)

**Basic HTML Document:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My First Page</title>
</head>
<body>
    <h1>Hello, World!</h1>
    <p>This is my first HTML page.</p>
</body>
</html>
```

**Key Points:**
- `<!DOCTYPE html>`: Tells browser this is HTML5
- `<html>`: Root element
- `<head>`: Metadata (not visible on page)
- `<body>`: Visible content

---

### Q2: What is the difference between HTML elements, tags, and attributes?

**Answer:**

**Element:** The complete structure (opening tag + content + closing tag)
```html
<p>This is an element</p>
```

**Tag:** The opening `<p>` or closing `</p>` markers
```html
<p>    ← Opening tag
</p>   ← Closing tag
```

**Attribute:** Additional information about an element
```html
<img src="photo.jpg" alt="A photo" width="300">
     ↑              ↑             ↑
   attribute    attribute     attribute
```

**Self-Closing Tags (No closing tag needed):**
```html
<img src="image.jpg" alt="Image">
<br>
<hr>
<input type="text">
<meta charset="UTF-8">
<link rel="stylesheet" href="style.css">
```

---

### Q3: What are the most important HTML tags for beginners?

**Answer:**

**Text Content:**
```html
<!-- Headings (h1 is most important, h6 is least) -->
<h1>Main Heading</h1>
<h2>Subheading</h2>
<h3>Smaller heading</h3>

<!-- Paragraphs -->
<p>A paragraph of text.</p>

<!-- Line break -->
<br>

<!-- Horizontal rule -->
<hr>

<!-- Text formatting -->
<strong>Bold text</strong>
<em>Italic text</em>
<u>Underlined text</u>
<mark>Highlighted text</mark>
<small>Small text</small>
```

**Links and Images:**
```html
<!-- Link -->
<a href="https://example.com">Click here</a>

<!-- Image -->
<img src="photo.jpg" alt="Description of image">
```

**Lists:**
```html
<!-- Unordered list (bullets) -->
<ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>

<!-- Ordered list (numbers) -->
<ol>
    <li>First</li>
    <li>Second</li>
    <li>Third</li>
</ol>
```

**Containers:**
```html
<!-- Generic block container -->
<div>
    <p>Content inside a div</p>
</div>

<!-- Generic inline container -->
<span>Inline content</span>
```

---

## HTML Elements & Structure

### Q4: What is the difference between block-level and inline elements?

**Answer:**

**Block-Level Elements:**
- Start on a new line
- Take up full width available
- Can contain other block and inline elements

```html
<div>This is a block element</div>
<p>This is also a block element</p>
<h1>Block element</h1>

<!-- Visual representation: -->
┌─────────────────────────────┐
│ <div>Block element</div>    │
└─────────────────────────────┘
┌─────────────────────────────┐
│ <p>Block element</p>        │
└─────────────────────────────┘
```

**Common Block Elements:**
- `<div>`, `<p>`, `<h1>`-`<h6>`
- `<ul>`, `<ol>`, `<li>`
- `<section>`, `<article>`, `<header>`, `<footer>`
- `<form>`, `<table>`

**Inline Elements:**
- Don't start on a new line
- Only take up necessary width
- Cannot contain block elements

```html
<span>Inline</span> <a href="#">Link</a> <strong>Bold</strong>

<!-- Visual representation: -->
┌──────────┬──────┬──────┐
│ Inline   │ Link │ Bold │
└──────────┴──────┴──────┘
```

**Common Inline Elements:**
- `<span>`, `<a>`, `<img>`
- `<strong>`, `<em>`, `<code>`
- `<input>`, `<button>`, `<label>`

**Example:**
```html
<div>
    This is a block element with <span>inline content</span> inside.
    <a href="#">This link</a> is also inline.
</div>
```

---

### Q5: How do you create a proper HTML page structure?

**Answer:**

**Complete HTML5 Structure:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <!-- Character encoding -->
    <meta charset="UTF-8">
    
    <!-- Responsive viewport -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- SEO -->
    <meta name="description" content="Page description for search engines">
    <meta name="keywords" content="html, tutorial, web development">
    <meta name="author" content="Your Name">
    
    <!-- Page title (shown in browser tab) -->
    <title>Page Title - Website Name</title>
    
    <!-- Favicon -->
    <link rel="icon" type="image/png" href="favicon.png">
    
    <!-- External CSS -->
    <link rel="stylesheet" href="styles.css">
    
    <!-- Internal CSS (if needed) -->
    <style>
        body { font-family: Arial, sans-serif; }
    </style>
</head>
<body>
    <!-- Header -->
    <header>
        <nav>
            <ul>
                <li><a href="#home">Home</a></li>
                <li><a href="#about">About</a></li>
                <li><a href="#contact">Contact</a></li>
            </ul>
        </nav>
    </header>
    
    <!-- Main content -->
    <main>
        <article>
            <h1>Main Heading</h1>
            <p>Content goes here...</p>
        </article>
        
        <aside>
            <h2>Sidebar</h2>
            <p>Additional information...</p>
        </aside>
    </main>
    
    <!-- Footer -->
    <footer>
        <p>&copy; 2026 Your Website. All rights reserved.</p>
    </footer>
    
    <!-- External JavaScript -->
    <script src="script.js"></script>
</body>
</html>
```

---

### Q6: What are HTML comments and why use them?

**Answer:**

**Syntax:**
```html
<!-- This is a comment -->

<!-- 
    Multi-line comment
    Can span multiple lines
    Not visible in browser
-->
```

**Use Cases:**

**1. Documentation:**
```html
<!-- Navigation Section -->
<nav>
    <ul>
        <li><a href="#home">Home</a></li>
    </ul>
</nav>

<!-- Main Content Area -->
<main>
    <!-- User posts will be displayed here -->
    <div id="posts"></div>
</main>
```

**2. Temporary Removal (Debugging):**
```html
<!-- Temporarily disabled
<div class="advertisement">
    <img src="ad.jpg" alt="Advertisement">
</div>
-->
```

**3. Notes for Developers:**
```html
<!-- TODO: Add responsive image srcset -->
<img src="image.jpg" alt="Product">

<!-- FIXME: Update copyright year automatically -->
<footer>&copy; 2026 Company</footer>
```

**Important:** Comments are visible in page source! Never put sensitive information in comments.

---

## Forms & Input

### Q7: How do you create an HTML form?

**Answer:**

**Basic Form Structure:**
```html
<form action="/submit" method="POST">
    <!-- Form fields go here -->
    <button type="submit">Submit</button>
</form>
```

**Form Attributes:**
- `action`: URL where form data is sent
- `method`: HTTP method (`GET` or `POST`)
- `name`: Form identifier
- `enctype`: Encoding type (for file uploads: `multipart/form-data`)

**Complete Form Example:**
```html
<form action="/register" method="POST" id="registrationForm">
    <!-- Text Input -->
    <label for="username">Username:</label>
    <input type="text" id="username" name="username" required>
    
    <!-- Email Input -->
    <label for="email">Email:</label>
    <input type="email" id="email" name="email" required>
    
    <!-- Password Input -->
    <label for="password">Password:</label>
    <input type="password" id="password" name="password" 
           minlength="8" required>
    
    <!-- Number Input -->
    <label for="age">Age:</label>
    <input type="number" id="age" name="age" 
           min="18" max="100">
    
    <!-- Date Input -->
    <label for="birthday">Birthday:</label>
    <input type="date" id="birthday" name="birthday">
    
    <!-- Radio Buttons -->
    <fieldset>
        <legend>Gender:</legend>
        <label>
            <input type="radio" name="gender" value="male"> Male
        </label>
        <label>
            <input type="radio" name="gender" value="female"> Female
        </label>
        <label>
            <input type="radio" name="gender" value="other"> Other
        </label>
    </fieldset>
    
    <!-- Checkboxes -->
    <fieldset>
        <legend>Interests:</legend>
        <label>
            <input type="checkbox" name="interests" value="coding"> Coding
        </label>
        <label>
            <input type="checkbox" name="interests" value="sports"> Sports
        </label>
        <label>
            <input type="checkbox" name="interests" value="music"> Music
        </label>
    </fieldset>
    
    <!-- Select Dropdown -->
    <label for="country">Country:</label>
    <select id="country" name="country">
        <option value="">Select a country</option>
        <option value="us">United States</option>
        <option value="uk">United Kingdom</option>
        <option value="ca">Canada</option>
    </select>
    
    <!-- Textarea -->
    <label for="bio">Bio:</label>
    <textarea id="bio" name="bio" rows="5" cols="50" 
              placeholder="Tell us about yourself..."></textarea>
    
    <!-- File Upload -->
    <label for="avatar">Profile Picture:</label>
    <input type="file" id="avatar" name="avatar" 
           accept="image/png, image/jpeg">
    
    <!-- Hidden Input -->
    <input type="hidden" name="userId" value="12345">
    
    <!-- Submit Button -->
    <button type="submit">Register</button>
    
    <!-- Reset Button -->
    <button type="reset">Clear Form</button>
</form>
```

---

### Q8: What are the different input types in HTML5?

**Answer:**

**Text Inputs:**
```html
<!-- Plain text -->
<input type="text" placeholder="Enter text">

<!-- Email (validates email format) -->
<input type="email" placeholder="email@example.com">

<!-- Password (hidden characters) -->
<input type="password" placeholder="Password">

<!-- Search -->
<input type="search" placeholder="Search...">

<!-- URL -->
<input type="url" placeholder="https://example.com">

<!-- Telephone -->
<input type="tel" placeholder="123-456-7890">
```

**Number Inputs:**
```html
<!-- Number -->
<input type="number" min="0" max="100" step="5" value="10">

<!-- Range (slider) -->
<input type="range" min="0" max="100" value="50">
```

**Date & Time:**
```html
<!-- Date -->
<input type="date" min="2024-01-01" max="2026-12-31">

<!-- Time -->
<input type="time">

<!-- Date and Time -->
<input type="datetime-local">

<!-- Month -->
<input type="month">

<!-- Week -->
<input type="week">
```

**Selection:**
```html
<!-- Radio (single selection) -->
<input type="radio" name="option" value="1">
<input type="radio" name="option" value="2">

<!-- Checkbox (multiple selection) -->
<input type="checkbox" name="feature1" value="1">
<input type="checkbox" name="feature2" value="2">
```

**Other:**
```html
<!-- Color Picker -->
<input type="color" value="#ff0000">

<!-- File Upload -->
<input type="file" accept=".pdf,.doc" multiple>

<!-- Hidden -->
<input type="hidden" name="token" value="abc123">

<!-- Submit Button -->
<input type="submit" value="Submit Form">

<!-- Reset Button -->
<input type="reset" value="Clear">

<!-- Button -->
<input type="button" value="Click Me">
```

**HTML5 Validation:**
```html
<input type="email" required>
<input type="text" pattern="[A-Za-z]{3,}" title="At least 3 letters">
<input type="number" min="1" max="10">
<input type="text" minlength="5" maxlength="20">
```

---

## Semantic HTML

### Q9: What is Semantic HTML and why is it important?

**Answer:**

Semantic HTML uses elements that clearly describe their meaning to both the browser and the developer.

**Non-Semantic (Bad):**
```html
<div id="header">
    <div id="nav">
        <div class="link">Home</div>
        <div class="link">About</div>
    </div>
</div>
<div id="main">
    <div class="post">
        <div class="title">Article Title</div>
        <div class="content">Article content...</div>
    </div>
</div>
<div id="footer">
    <div>Copyright 2026</div>
</div>
```

**Semantic (Good):**
```html
<header>
    <nav>
        <a href="#home">Home</a>
        <a href="#about">About</a>
    </nav>
</header>
<main>
    <article>
        <h1>Article Title</h1>
        <p>Article content...</p>
    </article>
</main>
<footer>
    <p>Copyright 2026</p>
</footer>
```

**Benefits:**
1. **SEO**: Search engines understand content better
2. **Accessibility**: Screen readers can navigate properly
3. **Maintainability**: Code is easier to read and update
4. **Consistency**: Team members understand structure instantly

---

### Q10: What are the main semantic HTML5 elements?

**Answer:**

**Page Structure:**
```html
<header>
    <!-- Site header: logo, navigation, etc. -->
    <nav>
        <!-- Main navigation links -->
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/about">About</a></li>
        </ul>
    </nav>
</header>

<main>
    <!-- Main content of the page (only one per page) -->
    
    <article>
        <!-- Self-contained content (blog post, news article) -->
        <header>
            <h1>Article Title</h1>
            <time datetime="2026-01-21">January 21, 2026</time>
        </header>
        
        <section>
            <!-- Thematic grouping of content -->
            <h2>Section Title</h2>
            <p>Content...</p>
        </section>
        
        <aside>
            <!-- Related but separate content (sidebar) -->
            <h3>Related Articles</h3>
            <ul>
                <li><a href="#">Article 1</a></li>
            </ul>
        </aside>
    </article>
    
    <section>
        <!-- Another section of content -->
        <h2>Comments</h2>
        <div>Comment content...</div>
    </section>
</main>

<footer>
    <!-- Site footer: copyright, links, contact -->
    <p>&copy; 2026 Company Name</p>
    <address>
        <!-- Contact information -->
        <a href="mailto:info@example.com">info@example.com</a>
    </address>
</footer>
```

**Content Elements:**
```html
<!-- Figure with caption -->
<figure>
    <img src="chart.png" alt="Sales Chart">
    <figcaption>Sales data for Q1 2026</figcaption>
</figure>

<!-- Marked/highlighted text -->
<p>The <mark>highlighted text</mark> is important.</p>

<!-- Time/date -->
<time datetime="2026-01-21T14:30">2:30 PM on January 21, 2026</time>

<!-- Progress bar -->
<progress value="70" max="100">70%</progress>

<!-- Details/Summary (collapsible) -->
<details>
    <summary>Click to expand</summary>
    <p>Hidden content that can be revealed.</p>
</details>
```

---

## HTML5 Features

### Q11: What are HTML5 data attributes?

**Answer:**

Data attributes allow you to store custom data on HTML elements.

**Syntax:** `data-*` where * can be any name

**Basic Usage:**
```html
<div data-user-id="12345" 
     data-user-role="admin"
     data-created-date="2026-01-21">
    User Profile
</div>

<button data-action="delete" 
        data-item-id="789"
        data-confirm="Are you sure?">
    Delete
</button>

<article data-category="technology" 
         data-author="John Doe"
         data-views="1500">
    Article content...
</article>
```

**Accessing with JavaScript:**
```html
<div id="user" data-id="123" data-name="Alice"></div>

<script>
    const user = document.getElementById('user');
    
    // Access data attributes
    console.log(user.dataset.id);    // "123"
    console.log(user.dataset.name);  // "Alice"
    
    // Set data attributes
    user.dataset.email = "alice@example.com";
    user.dataset.isActive = "true";
    
    // Remove data attribute
    delete user.dataset.name;
</script>
```

**CSS Styling with Data Attributes:**
```html
<div data-status="active">Active User</div>
<div data-status="inactive">Inactive User</div>

<style>
    [data-status="active"] {
        color: green;
    }
    
    [data-status="inactive"] {
        color: gray;
    }
    
    /* Use attribute value in CSS */
    div::after {
        content: " (" attr(data-status) ")";
    }
</style>
```

**Real-World Examples:**

**1. Product Cards:**
```html
<div class="product" 
     data-product-id="SKU123"
     data-price="29.99"
     data-stock="15"
     data-category="electronics">
    <h3>Product Name</h3>
    <button onclick="addToCart(this)">Add to Cart</button>
</div>

<script>
    function addToCart(button) {
        const product = button.parentElement;
        const id = product.dataset.productId;
        const price = product.dataset.price;
        // Add to cart logic
    }
</script>
```

**2. Tabs:**
```html
<div class="tabs">
    <button data-tab="home">Home</button>
    <button data-tab="profile">Profile</button>
    <button data-tab="settings">Settings</button>
</div>

<div data-tab-content="home">Home Content</div>
<div data-tab-content="profile" style="display:none">Profile Content</div>
<div data-tab-content="settings" style="display:none">Settings Content</div>
```

---

### Q12: What is the Canvas element and how do you use it?

**Answer:**

The `<canvas>` element is used to draw graphics using JavaScript.

**Basic Setup:**
```html
<canvas id="myCanvas" width="400" height="300"></canvas>

<script>
    const canvas = document.getElementById('myCanvas');
    const ctx = canvas.getContext('2d');
    
    // Drawing code goes here
</script>
```

**Drawing Shapes:**
```html
<canvas id="shapes" width="500" height="400"></canvas>

<script>
    const canvas = document.getElementById('shapes');
    const ctx = canvas.getContext('2d');
    
    // Rectangle
    ctx.fillStyle = 'blue';
    ctx.fillRect(50, 50, 100, 80);  // x, y, width, height
    
    // Stroke rectangle
    ctx.strokeStyle = 'red';
    ctx.lineWidth = 3;
    ctx.strokeRect(200, 50, 100, 80);
    
    // Circle
    ctx.beginPath();
    ctx.arc(100, 250, 50, 0, 2 * Math.PI);  // x, y, radius, startAngle, endAngle
    ctx.fillStyle = 'green';
    ctx.fill();
    
    // Line
    ctx.beginPath();
    ctx.moveTo(200, 200);
    ctx.lineTo(300, 300);
    ctx.strokeStyle = 'black';
    ctx.lineWidth = 2;
    ctx.stroke();
    
    // Text
    ctx.font = '30px Arial';
    ctx.fillStyle = 'purple';
    ctx.fillText('Hello Canvas!', 50, 350);
</script>
```

**Advanced: Animation:**
```html
<canvas id="animation" width="600" height="400"></canvas>

<script>
    const canvas = document.getElementById('animation');
    const ctx = canvas.getContext('2d');
    
    let x = 0;
    let y = 200;
    let dx = 2;  // Speed
    
    function animate() {
        // Clear canvas
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        // Draw ball
        ctx.beginPath();
        ctx.arc(x, y, 20, 0, 2 * Math.PI);
        ctx.fillStyle = 'red';
        ctx.fill();
        
        // Update position
        x += dx;
        
        // Bounce at edges
        if (x > canvas.width - 20 || x < 20) {
            dx = -dx;
        }
        
        // Continue animation
        requestAnimationFrame(animate);
    }
    
    animate();
</script>
```

**Use Cases:**
- Games
- Data visualizations
- Image editing
- Animations
- Charts and graphs

---

### Q13: What is the difference between LocalStorage and SessionStorage?

**Answer:**

Both are HTML5 Web Storage APIs for storing data in the browser.

**Comparison:**

| Feature | LocalStorage | SessionStorage |
|---------|-------------|----------------|
| Lifetime | Until manually cleared | Until tab/window closes |
| Scope | Same origin (all tabs) | Single tab/window |
| Capacity | ~5-10MB | ~5-10MB |
| Expiration | Never | On tab close |

**LocalStorage:**
```html
<script>
    // Save data
    localStorage.setItem('username', 'Alice');
    localStorage.setItem('userId', '12345');
    
    // Save object (must stringify)
    const user = { name: 'Alice', age: 30 };
    localStorage.setItem('user', JSON.stringify(user));
    
    // Get data
    const username = localStorage.getItem('username');
    console.log(username);  // "Alice"
    
    // Get and parse object
    const userObj = JSON.parse(localStorage.getItem('user'));
    console.log(userObj.name);  // "Alice"
    
    // Remove item
    localStorage.removeItem('username');
    
    // Clear all
    localStorage.clear();
    
    // Check if key exists
    if (localStorage.getItem('username')) {
        console.log('Username exists');
    }
    
    // Get all keys
    for (let i = 0; i < localStorage.length; i++) {
        const key = localStorage.key(i);
        const value = localStorage.getItem(key);
        console.log(key, value);
    }
</script>
```

**SessionStorage:**
```html
<script>
    // Same API as localStorage
    sessionStorage.setItem('tempData', 'value');
    const data = sessionStorage.getItem('tempData');
    sessionStorage.removeItem('tempData');
    sessionStorage.clear();
</script>
```

**Real-World Examples:**

**1. Remember User Preferences (LocalStorage):**
```html
<button onclick="toggleTheme()">Toggle Dark Mode</button>

<script>
    // Load saved theme
    const savedTheme = localStorage.getItem('theme') || 'light';
    document.body.className = savedTheme;
    
    function toggleTheme() {
        const currentTheme = document.body.className;
        const newTheme = currentTheme === 'light' ? 'dark' : 'light';
        
        document.body.className = newTheme;
        localStorage.setItem('theme', newTheme);
    }
</script>
```

**2. Shopping Cart (LocalStorage):**
```html
<script>
    function addToCart(productId, name, price) {
        let cart = JSON.parse(localStorage.getItem('cart') || '[]');
        
        cart.push({ id: productId, name, price });
        localStorage.setItem('cart', JSON.stringify(cart));
        
        updateCartDisplay();
    }
    
    function getCart() {
        return JSON.parse(localStorage.getItem('cart') || '[]');
    }
    
    function clearCart() {
        localStorage.removeItem('cart');
    }
</script>
```

**3. Form Data Persistence (SessionStorage):**
```html
<form>
    <input type="text" id="name" oninput="saveFormData()">
    <textarea id="message" oninput="saveFormData()"></textarea>
</form>

<script>
    // Restore form data on page load
    window.onload = function() {
        document.getElementById('name').value = 
            sessionStorage.getItem('formName') || '';
        document.getElementById('message').value = 
            sessionStorage.getItem('formMessage') || '';
    };
    
    function saveFormData() {
        sessionStorage.setItem('formName', 
            document.getElementById('name').value);
        sessionStorage.setItem('formMessage', 
            document.getElementById('message').value);
    }
</script>
```

---

## Accessibility

### Q13.1: What is Web Accessibility?

**Answer:**

Web accessibility means designing and developing websites, tools, and technologies so that people with disabilities can use them. It ensures that everyone, regardless of their abilities, can perceive, understand, navigate, interact with, and contribute to the web.

**Why Accessibility Matters:**

1. **Inclusive Design** - Makes web content usable by everyone
2. **Legal Requirement** - Many countries have laws requiring web accessibility (ADA, Section 508, WCAG)
3. **Business Benefits** - Reaches wider audience, improves SEO, enhances user experience
4. **Ethical Responsibility** - Digital content should be accessible to all

**Who Benefits from Accessibility?**

- **Visual Disabilities**: Blind, low vision, color blindness
- **Hearing Disabilities**: Deaf or hard of hearing
- **Motor Disabilities**: Limited hand mobility, tremors
- **Cognitive Disabilities**: Learning difficulties, memory issues
- **Temporary Disabilities**: Broken arm, eye surgery recovery
- **Situational Limitations**: Bright sunlight (can't see screen), loud environment (can't hear audio)
- **Everyone**: Clear navigation, good contrast, and proper structure benefit all users

---

### Q13.2: What are the WCAG Guidelines?

**Answer:**

WCAG (Web Content Accessibility Guidelines) are international standards for web accessibility. The guidelines are organized around four principles (POUR):

#### 1. Perceivable
Information must be presentable to users in ways they can perceive.

```html
<!-- ✅ Good: Alt text for images -->
<img src="chart.png" alt="Sales increased 30% in Q4 2023">

<!-- ❌ Bad: Missing alt text -->
<img src="chart.png">

<!-- ✅ Good: Captions for video -->
<video controls>
    <source src="video.mp4" type="video/mp4">
    <track kind="captions" src="captions.vtt" srclang="en" label="English">
</video>

<!-- ✅ Good: Sufficient color contrast -->
<p style="color: #333; background: #fff;">High contrast text</p>

<!-- ❌ Bad: Poor contrast -->
<p style="color: #ccc; background: #fff;">Low contrast text</p>
```

#### 2. Operable
User interface components must be operable by all users.

```html
<!-- ✅ Good: Keyboard accessible -->
<button onclick="submitForm()">Submit</button>
<a href="/page" tabindex="0">Link</a>

<!-- ❌ Bad: Only mouse accessible -->
<div onclick="submitForm()">Submit</div>

<!-- ✅ Good: Skip navigation link -->
<a href="#main-content" class="skip-link">Skip to main content</a>
<nav><!-- navigation --></nav>
<main id="main-content"><!-- content --></main>

<!-- ✅ Good: Descriptive link text -->
<a href="/report.pdf">Download 2023 Annual Report (PDF, 2MB)</a>

<!-- ❌ Bad: Non-descriptive link -->
<a href="/report.pdf">Click here</a>
```

#### 3. Understandable
Information and operation of the user interface must be understandable.

```html
<!-- ✅ Good: Language specified -->
<html lang="en">
    <p>This is English text.</p>
    <p lang="es">Este es texto en español.</p>
</html>

<!-- ✅ Good: Clear error messages -->
<form>
    <label for="email">Email:</label>
    <input type="email" id="email" required 
           aria-describedby="email-error">
    <span id="email-error" role="alert">
        Please enter a valid email address (e.g., name@example.com)
    </span>
</form>

<!-- ✅ Good: Consistent navigation -->
<nav>
    <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/about">About</a></li>
        <li><a href="/contact">Contact</a></li>
    </ul>
</nav>
```

#### 4. Robust
Content must be robust enough to work with various technologies, including assistive technologies.

```html
<!-- ✅ Good: Valid HTML -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Accessible Page</title>
</head>
<body>
    <main>
        <h1>Main Heading</h1>
        <p>Content here</p>
    </main>
</body>
</html>

<!-- ✅ Good: ARIA used correctly -->
<button aria-label="Close menu" aria-expanded="true">
    ×
</button>
```

**WCAG Conformance Levels:**

- **Level A**: Minimum level (basic accessibility)
- **Level AA**: Mid-range (addresses major barriers) - **Most commonly required**
- **Level AAA**: Highest level (enhanced accessibility)

---

### Q13.3: How do you make HTML accessible?

**Answer:**

**1. Use Semantic HTML Elements:**

```html
<!-- ✅ Good: Semantic structure -->
<header>
    <nav>
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/about">About</a></li>
        </ul>
    </nav>
</header>

<main>
    <article>
        <h1>Article Title</h1>
        <p>Article content...</p>
    </article>
</main>

<footer>
    <p>&copy; 2024 Company Name</p>
</footer>

<!-- ❌ Bad: Generic divs -->
<div class="header">
    <div class="nav">
        <div class="menu-item">Home</div>
    </div>
</div>
```

**2. Provide Alternative Text for Images:**

```html
<!-- ✅ Informative image -->
<img src="graph.png" alt="Bar graph showing 25% increase in sales">

<!-- ✅ Decorative image -->
<img src="decoration.png" alt="">

<!-- ✅ Complex image with detailed description -->
<img src="complex-diagram.png" 
     alt="System architecture diagram"
     aria-describedby="diagram-description">
<p id="diagram-description">
    The diagram shows three servers connecting to a load balancer...
</p>

<!-- ✅ Icon with text -->
<button>
    <img src="save-icon.png" alt="">
    Save
</button>

<!-- ✅ Icon without text -->
<button aria-label="Save document">
    <img src="save-icon.png" alt="">
</button>
```

**3. Ensure Keyboard Accessibility:**

```html
<!-- ✅ Good: Native interactive elements -->
<button onclick="doAction()">Click me</button>
<a href="/page">Go to page</a>
<input type="text" id="name">

<!-- ❌ Bad: Non-interactive element with click handler -->
<div onclick="doAction()">Click me</div>

<!-- ✅ Fix: Add keyboard support -->
<div role="button" 
     tabindex="0" 
     onclick="doAction()"
     onkeydown="if(event.key === 'Enter' || event.key === ' ') doAction()">
    Click me
</div>

<!-- ✅ Good: Tab order control -->
<input type="text" tabindex="1">
<input type="text" tabindex="2">
<button tabindex="3">Submit</button>
```

**4. Use Proper Form Labels:**

```html
<!-- ✅ Good: Associated label -->
<label for="username">Username:</label>
<input type="text" id="username" name="username">

<!-- ✅ Good: Implicit label -->
<label>
    Email:
    <input type="email" name="email">
</label>

<!-- ✅ Good: Fieldset for radio groups -->
<fieldset>
    <legend>Choose your subscription:</legend>
    <label>
        <input type="radio" name="plan" value="free">
        Free
    </label>
    <label>
        <input type="radio" name="plan" value="pro">
        Pro
    </label>
</fieldset>

<!-- ✅ Good: Required field indication -->
<label for="password">
    Password <span aria-label="required">*</span>
</label>
<input type="password" 
       id="password" 
       required 
       aria-required="true">
```

**5. Provide Sufficient Color Contrast:**

```html
<!-- ✅ Good: High contrast (4.5:1 minimum for normal text) -->
<p style="color: #000; background: #fff;">Black on white</p>
<p style="color: #333; background: #fff;">Dark gray on white</p>

<!-- ❌ Bad: Low contrast -->
<p style="color: #aaa; background: #fff;">Light gray on white</p>

<!-- ✅ Good: Don't rely on color alone -->
<span style="color: red; font-weight: bold;">* Required field</span>
<span class="error-icon">⚠️</span>

<!-- ❌ Bad: Color only -->
<span style="color: red;">Required field</span>
```

**6. Create Accessible Tables:**

```html
<!-- ✅ Good: Proper table structure -->
<table>
    <caption>Monthly Sales Report</caption>
    <thead>
        <tr>
            <th scope="col">Month</th>
            <th scope="col">Sales</th>
            <th scope="col">Growth</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">January</th>
            <td>$10,000</td>
            <td>5%</td>
        </tr>
        <tr>
            <th scope="row">February</th>
            <td>$12,000</td>
            <td>20%</td>
        </tr>
    </tbody>
</table>
```

**7. Use Headings Properly:**

```html
<!-- ✅ Good: Logical heading hierarchy -->
<h1>Main Page Title</h1>
    <h2>Section 1</h2>
        <h3>Subsection 1.1</h3>
        <h3>Subsection 1.2</h3>
    <h2>Section 2</h2>
        <h3>Subsection 2.1</h3>

<!-- ❌ Bad: Skipping levels -->
<h1>Main Title</h1>
    <h3>Subsection</h3> <!-- Skipped h2 -->
        <h5>Sub-subsection</h5> <!-- Skipped h4 -->
```

**8. Provide Captions and Transcripts:**

```html
<!-- ✅ Video with captions -->
<video controls>
    <source src="video.mp4" type="video/mp4">
    <track kind="captions" 
           src="captions-en.vtt" 
           srclang="en" 
           label="English">
    <track kind="captions" 
           src="captions-es.vtt" 
           srclang="es" 
           label="Spanish">
</video>

<!-- ✅ Audio with transcript -->
<audio controls src="podcast.mp3"></audio>
<details>
    <summary>Read transcript</summary>
    <p>Transcript of the audio content goes here...</p>
</details>
```

**9. Make Modals and Dialogs Accessible:**

```html
<div role="dialog" 
     aria-labelledby="dialog-title"
     aria-describedby="dialog-description"
     aria-modal="true">
    
    <h2 id="dialog-title">Confirm Action</h2>
    <p id="dialog-description">
        Are you sure you want to delete this item?
    </p>
    
    <button autofocus>Confirm</button>
    <button>Cancel</button>
</div>

<script>
// Trap focus inside modal
// Restore focus when modal closes
// Close on Escape key
</script>
```

**10. Test with Assistive Technologies:**

```html
<!-- Test checklist: -->
<!-- ✓ Screen reader (NVDA, JAWS, VoiceOver) -->
<!-- ✓ Keyboard only navigation (no mouse) -->
<!-- ✓ Browser zoom (200% minimum) -->
<!-- ✓ Color contrast checker -->
<!-- ✓ Automated tools (axe, WAVE, Lighthouse) -->
```

**Common Accessibility Mistakes to Avoid:**

```html
<!-- ❌ Empty links -->
<a href="/page"></a>

<!-- ✅ Fix -->
<a href="/page">Go to Page</a>

<!-- ❌ Placeholder as label -->
<input type="text" placeholder="Enter name">

<!-- ✅ Fix -->
<label for="name">Name:</label>
<input type="text" id="name" placeholder="e.g., John Doe">

<!-- ❌ Auto-playing media -->
<video autoplay src="video.mp4"></video>

<!-- ✅ Fix: User control -->
<video controls src="video.mp4"></video>

<!-- ❌ Time-sensitive content without control -->
<div class="carousel" data-auto-rotate="3000"></div>

<!-- ✅ Fix: Pause button -->
<div class="carousel">
    <button aria-label="Pause carousel">Pause</button>
</div>
```

---

### Q14: What is ARIA and why is it important?

**Answer:**

ARIA (Accessible Rich Internet Applications) provides ways to make web content accessible to people with disabilities.

**Basic ARIA Attributes:**

**1. Roles:**
```html
<!-- Define what an element does -->
<div role="button">Click me</div>
<div role="navigation">Navigation links</div>
<div role="alert">Important message!</div>
<div role="dialog">Modal dialog</div>
<div role="progressbar">Loading...</div>
```

**2. States:**
```html
<!-- Current state of element -->
<button aria-pressed="true">Toggle Button</button>
<input aria-invalid="true" aria-required="true">
<div aria-expanded="false">Collapsed section</div>
<div aria-hidden="true">Hidden from screen readers</div>
<button aria-disabled="true">Disabled Button</button>
```

**3. Properties:**
```html
<!-- Describe element -->
<button aria-label="Close dialog">×</button>
<img src="icon.png" aria-label="Settings icon">
<div aria-describedby="help-text">Input field</div>
<p id="help-text">Enter your email address</p>

<input aria-labelledby="username-label">
<label id="username-label">Username</label>
```

**Accessible Form:**
```html
<form role="form" aria-labelledby="form-title">
    <h2 id="form-title">Registration Form</h2>
    
    <!-- Text input with label and error -->
    <div>
        <label for="email">Email:</label>
        <input type="email" 
               id="email" 
               name="email"
               aria-required="true"
               aria-invalid="false"
               aria-describedby="email-error">
        <span id="email-error" 
              role="alert" 
              aria-live="polite"
              style="display: none;">
            Please enter a valid email
        </span>
    </div>
    
    <!-- Checkbox with label -->
    <div>
        <input type="checkbox" 
               id="terms" 
               aria-labelledby="terms-label">
        <label id="terms-label" for="terms">
            I agree to the terms and conditions
        </label>
    </div>
    
    <!-- Submit button -->
    <button type="submit" 
            aria-label="Submit registration form">
        Register
    </button>
</form>
```

**Accessible Modal:**
```html
<div role="dialog" 
     aria-labelledby="modal-title"
     aria-describedby="modal-description"
     aria-modal="true">
    
    <h2 id="modal-title">Confirm Action</h2>
    <p id="modal-description">
        Are you sure you want to delete this item?
    </p>
    
    <button aria-label="Confirm delete">Yes</button>
    <button aria-label="Cancel delete">No</button>
</div>
```

**Live Regions (Dynamic Content):**
```html
<!-- Announce changes to screen readers -->
<div aria-live="polite" aria-atomic="true">
    Status: <span id="status">Idle</span>
</div>

<!-- Urgent announcements -->
<div role="alert" aria-live="assertive">
    Error: Connection lost!
</div>

<script>
    // Update will be announced
    document.getElementById('status').textContent = 'Loading...';
</script>
```

---

### Q15: What are the best practices for accessible HTML?

**Answer:**

**1. Use Semantic HTML:**
```html
<!-- ✅ Good: Semantic -->
<nav>
    <ul>
        <li><a href="/">Home</a></li>
    </ul>
</nav>

<!-- ❌ Bad: Non-semantic -->
<div class="nav">
    <div class="link" onclick="navigate()">Home</div>
</div>
```

**2. Always Use Alt Text for Images:**
```html
<!-- ✅ Good: Descriptive alt text -->
<img src="dog.jpg" alt="Golden retriever playing in park">

<!-- ✅ Good: Decorative image -->
<img src="decoration.png" alt="">

<!-- ❌ Bad: Missing alt -->
<img src="photo.jpg">

<!-- ❌ Bad: Useless alt -->
<img src="photo.jpg" alt="image">
```

**3. Proper Form Labels:**
```html
<!-- ✅ Good: Explicit label -->
<label for="username">Username:</label>
<input type="text" id="username" name="username">

<!-- ✅ Good: Implicit label -->
<label>
    Email:
    <input type="email" name="email">
</label>

<!-- ❌ Bad: No label -->
<input type="text" placeholder="Enter name">
```

**4. Keyboard Navigation:**
```html
<!-- ✅ Good: Focusable and keyboard accessible -->
<button onclick="handleClick()">Click Me</button>

<!-- ✅ Good: Custom element with keyboard support -->
<div role="button" 
     tabindex="0" 
     onclick="handleClick()"
     onkeypress="if(event.key==='Enter') handleClick()">
    Custom Button
</div>

<!-- ❌ Bad: Not keyboard accessible -->
<div onclick="handleClick()">Click Me</div>
```

**5. Heading Hierarchy:**
```html
<!-- ✅ Good: Proper hierarchy -->
<h1>Main Title</h1>
    <h2>Section 1</h2>
        <h3>Subsection 1.1</h3>
        <h3>Subsection 1.2</h3>
    <h2>Section 2</h2>

<!-- ❌ Bad: Skipping levels -->
<h1>Main Title</h1>
    <h3>Section 1</h3>  <!-- Skipped h2 -->
```

**6. Link Text:**
```html
<!-- ✅ Good: Descriptive link text -->
<a href="report.pdf">Download Q1 2026 Financial Report (PDF, 2MB)</a>

<!-- ❌ Bad: Generic link text -->
<a href="report.pdf">Click here</a>
<a href="more.html">Read more</a>
```

**7. Color Contrast:**
```html
<!-- Ensure sufficient contrast (WCAG AA: 4.5:1 for normal text) -->
<style>
    /* ✅ Good: High contrast */
    .text { color: #000000; background: #FFFFFF; }
    
    /* ❌ Bad: Low contrast */
    .text { color: #CCCCCC; background: #FFFFFF; }
</style>
```

**8. Skip Navigation Links:**
```html
<!-- Allow keyboard users to skip repetitive content -->
<a href="#main-content" class="skip-link">Skip to main content</a>

<nav>
    <!-- Navigation links -->
</nav>

<main id="main-content">
    <!-- Main content -->
</main>

<style>
    .skip-link {
        position: absolute;
        top: -40px;
        left: 0;
    }
    .skip-link:focus {
        top: 0;
    }
</style>
```

---

### Q15.1: How to Create Accessible Navigation Menus?

**Answer:**

**1. Simple Navigation:**

```html
<nav aria-label="Main navigation">
    <ul role="menubar">
        <li role="none">
            <a href="/" role="menuitem">Home</a>
        </li>
        <li role="none">
            <a href="/about" role="menuitem">About</a>
        </li>
        <li role="none">
            <a href="/contact" role="menuitem">Contact</a>
        </li>
    </ul>
</nav>

<style>
    nav ul {
        list-style: none;
        padding: 0;
        display: flex;
        gap: 20px;
    }
    
    nav a:focus {
        outline: 2px solid #0066cc;
        outline-offset: 2px;
    }
</style>
```

**2. Dropdown Menu with Keyboard Support:**

```html
<nav aria-label="Main navigation">
    <ul class="menu">
        <li>
            <button aria-expanded="false" 
                    aria-haspopup="true"
                    aria-controls="dropdown-products">
                Products
            </button>
            <ul id="dropdown-products" hidden>
                <li><a href="/product1">Product 1</a></li>
                <li><a href="/product2">Product 2</a></li>
                <li><a href="/product3">Product 3</a></li>
            </ul>
        </li>
        <li><a href="/about">About</a></li>
    </ul>
</nav>

<script>
document.querySelectorAll('[aria-haspopup="true"]').forEach(button => {
    button.addEventListener('click', function() {
        const expanded = this.getAttribute('aria-expanded') === 'true';
        const dropdown = document.getElementById(this.getAttribute('aria-controls'));
        
        // Toggle dropdown
        this.setAttribute('aria-expanded', !expanded);
        dropdown.hidden = expanded;
    });
    
    // Keyboard navigation
    button.addEventListener('keydown', function(e) {
        const dropdown = document.getElementById(this.getAttribute('aria-controls'));
        const items = dropdown.querySelectorAll('a');
        
        if (e.key === 'ArrowDown') {
            e.preventDefault();
            this.setAttribute('aria-expanded', 'true');
            dropdown.hidden = false;
            items[0].focus();
        }
    });
});

// Close dropdown when clicking outside
document.addEventListener('click', function(e) {
    if (!e.target.closest('[aria-haspopup]')) {
        document.querySelectorAll('[aria-haspopup]').forEach(button => {
            button.setAttribute('aria-expanded', 'false');
            const dropdown = document.getElementById(button.getAttribute('aria-controls'));
            if (dropdown) dropdown.hidden = true;
        });
    }
});
</script>
```

**3. Mobile Responsive Menu:**

```html
<nav aria-label="Main navigation">
    <button id="menu-toggle" 
            aria-expanded="false"
            aria-controls="main-menu"
            aria-label="Toggle navigation menu">
        <span class="hamburger"></span>
    </button>
    
    <ul id="main-menu" hidden>
        <li><a href="/">Home</a></li>
        <li><a href="/about">About</a></li>
        <li><a href="/services">Services</a></li>
        <li><a href="/contact">Contact</a></li>
    </ul>
</nav>

<style>
    @media (max-width: 768px) {
        #main-menu {
            position: absolute;
            top: 60px;
            left: 0;
            right: 0;
            background: white;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        
        #main-menu[hidden] {
            display: none;
        }
    }
    
    @media (min-width: 769px) {
        #menu-toggle {
            display: none;
        }
        
        #main-menu[hidden] {
            display: flex !important;
        }
    }
</style>

<script>
document.getElementById('menu-toggle').addEventListener('click', function() {
    const menu = document.getElementById('main-menu');
    const expanded = this.getAttribute('aria-expanded') === 'true';
    
    this.setAttribute('aria-expanded', !expanded);
    menu.hidden = expanded;
});
</script>
```

---

### Q15.2: How to Make Interactive Components Accessible?

**Answer:**

**1. Accessible Tabs:**

```html
<div class="tabs">
    <div role="tablist" aria-label="Content sections">
        <button role="tab" 
                aria-selected="true" 
                aria-controls="panel-1" 
                id="tab-1">
            Tab 1
        </button>
        <button role="tab" 
                aria-selected="false" 
                aria-controls="panel-2" 
                id="tab-2" 
                tabindex="-1">
            Tab 2
        </button>
        <button role="tab" 
                aria-selected="false" 
                aria-controls="panel-3" 
                id="tab-3" 
                tabindex="-1">
            Tab 3
        </button>
    </div>
    
    <div role="tabpanel" 
         id="panel-1" 
         aria-labelledby="tab-1" 
         tabindex="0">
        <h2>Panel 1 Content</h2>
        <p>Content for the first tab...</p>
    </div>
    
    <div role="tabpanel" 
         id="panel-2" 
         aria-labelledby="tab-2" 
         tabindex="0" 
         hidden>
        <h2>Panel 2 Content</h2>
        <p>Content for the second tab...</p>
    </div>
    
    <div role="tabpanel" 
         id="panel-3" 
         aria-labelledby="tab-3" 
         tabindex="0" 
         hidden>
        <h2>Panel 3 Content</h2>
        <p>Content for the third tab...</p>
    </div>
</div>

<script>
const tabs = document.querySelectorAll('[role="tab"]');
const panels = document.querySelectorAll('[role="tabpanel"]');

tabs.forEach((tab, index) => {
    tab.addEventListener('click', function() {
        // Deactivate all tabs
        tabs.forEach(t => {
            t.setAttribute('aria-selected', 'false');
            t.setAttribute('tabindex', '-1');
        });
        
        // Hide all panels
        panels.forEach(p => p.hidden = true);
        
        // Activate clicked tab
        this.setAttribute('aria-selected', 'true');
        this.setAttribute('tabindex', '0');
        
        // Show corresponding panel
        const panel = document.getElementById(this.getAttribute('aria-controls'));
        panel.hidden = false;
    });
    
    // Keyboard navigation
    tab.addEventListener('keydown', function(e) {
        let newIndex;
        
        if (e.key === 'ArrowRight') {
            newIndex = (index + 1) % tabs.length;
        } else if (e.key === 'ArrowLeft') {
            newIndex = (index - 1 + tabs.length) % tabs.length;
        } else if (e.key === 'Home') {
            newIndex = 0;
        } else if (e.key === 'End') {
            newIndex = tabs.length - 1;
        }
        
        if (newIndex !== undefined) {
            e.preventDefault();
            tabs[newIndex].click();
            tabs[newIndex].focus();
        }
    });
});
</script>
```

**2. Accessible Accordion:**

```html
<div class="accordion">
    <h3>
        <button aria-expanded="false" 
                aria-controls="section-1" 
                id="accordion-button-1">
            Section 1 Heading
        </button>
    </h3>
    <div id="section-1" 
         role="region" 
         aria-labelledby="accordion-button-1" 
         hidden>
        <p>Content for section 1...</p>
    </div>
    
    <h3>
        <button aria-expanded="false" 
                aria-controls="section-2" 
                id="accordion-button-2">
            Section 2 Heading
        </button>
    </h3>
    <div id="section-2" 
         role="region" 
         aria-labelledby="accordion-button-2" 
         hidden>
        <p>Content for section 2...</p>
    </div>
</div>

<script>
document.querySelectorAll('.accordion button').forEach(button => {
    button.addEventListener('click', function() {
        const expanded = this.getAttribute('aria-expanded') === 'true';
        const content = document.getElementById(this.getAttribute('aria-controls'));
        
        // Toggle current section
        this.setAttribute('aria-expanded', !expanded);
        content.hidden = expanded;
    });
});
</script>
```

**3. Accessible Modal Dialog:**

```html
<button id="open-modal">Open Dialog</button>

<div role="dialog" 
     aria-labelledby="dialog-title"
     aria-describedby="dialog-description"
     aria-modal="true"
     id="modal"
     class="modal"
     hidden>
    
    <div class="modal-content">
        <h2 id="dialog-title">Dialog Title</h2>
        <p id="dialog-description">
            This is the dialog content with important information.
        </p>
        
        <label for="modal-input">Name:</label>
        <input type="text" id="modal-input">
        
        <div class="modal-actions">
            <button id="confirm-btn">Confirm</button>
            <button id="cancel-btn">Cancel</button>
        </div>
    </div>
</div>

<style>
.modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.7);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
}

.modal[hidden] {
    display: none;
}

.modal-content {
    background: white;
    padding: 30px;
    border-radius: 8px;
    max-width: 500px;
    width: 90%;
}

.modal-actions {
    margin-top: 20px;
    display: flex;
    gap: 10px;
    justify-content: flex-end;
}
</style>

<script>
const modal = document.getElementById('modal');
const openBtn = document.getElementById('open-modal');
const confirmBtn = document.getElementById('confirm-btn');
const cancelBtn = document.getElementById('cancel-btn');
let previousFocus;

function openModal() {
    previousFocus = document.activeElement;
    modal.hidden = false;
    
    // Focus first focusable element
    const firstFocusable = modal.querySelector('input, button');
    firstFocusable.focus();
    
    // Trap focus inside modal
    modal.addEventListener('keydown', trapFocus);
}

function closeModal() {
    modal.hidden = true;
    modal.removeEventListener('keydown', trapFocus);
    
    // Restore focus
    if (previousFocus) {
        previousFocus.focus();
    }
}

function trapFocus(e) {
    if (e.key === 'Escape') {
        closeModal();
        return;
    }
    
    if (e.key === 'Tab') {
        const focusableElements = modal.querySelectorAll(
            'button, input, select, textarea, a[href]'
        );
        const firstElement = focusableElements[0];
        const lastElement = focusableElements[focusableElements.length - 1];
        
        if (e.shiftKey && document.activeElement === firstElement) {
            e.preventDefault();
            lastElement.focus();
        } else if (!e.shiftKey && document.activeElement === lastElement) {
            e.preventDefault();
            firstElement.focus();
        }
    }
}

openBtn.addEventListener('click', openModal);
confirmBtn.addEventListener('click', closeModal);
cancelBtn.addEventListener('click', closeModal);

// Close on backdrop click
modal.addEventListener('click', function(e) {
    if (e.target === modal) {
        closeModal();
    }
});
</script>
```

**4. Accessible Tooltip:**

```html
<button aria-describedby="tooltip" id="info-button">
    Information
    <span class="tooltip" id="tooltip" role="tooltip">
        This provides additional helpful information
    </span>
</button>

<style>
.tooltip {
    position: absolute;
    background: #333;
    color: white;
    padding: 5px 10px;
    border-radius: 4px;
    font-size: 14px;
    white-space: nowrap;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.2s;
    z-index: 1000;
}

#info-button:hover .tooltip,
#info-button:focus .tooltip {
    opacity: 1;
}
</style>
```

**5. Accessible Toggle Switch:**

```html
<div class="toggle-container">
    <span id="toggle-label">Enable notifications</span>
    <button role="switch" 
            aria-checked="false" 
            aria-labelledby="toggle-label"
            class="toggle-switch">
        <span class="toggle-slider"></span>
    </button>
</div>

<style>
.toggle-container {
    display: flex;
    align-items: center;
    gap: 10px;
}

.toggle-switch {
    position: relative;
    width: 50px;
    height: 24px;
    background: #ccc;
    border: none;
    border-radius: 12px;
    cursor: pointer;
    transition: background 0.2s;
}

.toggle-switch[aria-checked="true"] {
    background: #4CAF50;
}

.toggle-slider {
    position: absolute;
    top: 2px;
    left: 2px;
    width: 20px;
    height: 20px;
    background: white;
    border-radius: 50%;
    transition: transform 0.2s;
}

.toggle-switch[aria-checked="true"] .toggle-slider {
    transform: translateX(26px);
}

.toggle-switch:focus {
    outline: 2px solid #0066cc;
    outline-offset: 2px;
}
</style>

<script>
document.querySelectorAll('[role="switch"]').forEach(toggle => {
    toggle.addEventListener('click', function() {
        const checked = this.getAttribute('aria-checked') === 'true';
        this.setAttribute('aria-checked', !checked);
    });
    
    toggle.addEventListener('keydown', function(e) {
        if (e.key === ' ' || e.key === 'Enter') {
            e.preventDefault();
            this.click();
        }
    });
});
</script>
```

---

### Q15.3: How to Test for Accessibility?

**Answer:**

**1. Automated Testing Tools:**

```html
<!-- Include axe-core for automated testing -->
<script src="https://cdn.jsdelivr.net/npm/axe-core@4.7.0/axe.min.js"></script>
<script>
// Run accessibility audit
axe.run()
    .then(results => {
        if (results.violations.length) {
            console.error('Accessibility violations:', results.violations);
        } else {
            console.log('No accessibility violations found!');
        }
    })
    .catch(err => console.error('Error running axe:', err));
</script>
```

**2. Manual Testing Checklist:**

```html
<!-- Keyboard Testing Checklist -->
<!--
✓ Can you navigate to all interactive elements using Tab?
✓ Can you operate all controls with keyboard only?
✓ Is the focus indicator visible?
✓ Does Tab order make logical sense?
✓ Can you close modals/menus with Escape?
✓ Are skip links working?
-->

<!-- Screen Reader Testing -->
<!--
✓ Test with NVDA (Windows), JAWS (Windows), VoiceOver (Mac/iOS), TalkBack (Android)
✓ Are images described properly?
✓ Do form fields have proper labels?
✓ Are headings in logical order?
✓ Are landmarks announced correctly?
✓ Is dynamic content announced?
-->

<!-- Visual Testing -->
<!--
✓ Zoom to 200% - is content still usable?
✓ Check color contrast (4.5:1 for normal text, 3:1 for large text)
✓ Test with grayscale - is information conveyed without color?
✓ Test with different viewport sizes
✓ Check for text spacing issues
-->
```

**3. Browser DevTools Audits:**

```javascript
// Run Lighthouse accessibility audit programmatically
// Chrome DevTools → Lighthouse → Accessibility
// Or use lighthouse CLI:
// npx lighthouse https://example.com --only-categories=accessibility
```

**4. Accessibility Testing Script:**

```html
<script>
// Simple accessibility checker
function checkAccessibility() {
    const issues = [];
    
    // Check for images without alt text
    document.querySelectorAll('img:not([alt])').forEach(img => {
        issues.push(`Image missing alt text: ${img.src}`);
    });
    
    // Check for empty links
    document.querySelectorAll('a:empty').forEach(link => {
        issues.push(`Empty link found: ${link.href}`);
    });
    
    // Check for inputs without labels
    document.querySelectorAll('input:not([aria-label]):not([aria-labelledby])').forEach(input => {
        if (!input.id || !document.querySelector(`label[for="${input.id}"]`)) {
            issues.push(`Input without label: ${input.name || input.type}`);
        }
    });
    
    // Check for buttons without text
    document.querySelectorAll('button').forEach(button => {
        if (!button.textContent.trim() && !button.getAttribute('aria-label')) {
            issues.push('Button without accessible name');
        }
    });
    
    // Check heading hierarchy
    const headings = document.querySelectorAll('h1, h2, h3, h4, h5, h6');
    let lastLevel = 0;
    headings.forEach(heading => {
        const level = parseInt(heading.tagName[1]);
        if (level - lastLevel > 1) {
            issues.push(`Heading hierarchy skipped from h${lastLevel} to h${level}`);
        }
        lastLevel = level;
    });
    
    // Report results
    if (issues.length) {
        console.error('Accessibility Issues Found:', issues);
    } else {
        console.log('No obvious accessibility issues found!');
    }
    
    return issues;
}

// Run on page load
window.addEventListener('load', checkAccessibility);
</script>
```

**5. WCAG Compliance Checklist:**

```html
<!-- Level A (Minimum) -->
<!--
✓ Non-text content has text alternative
✓ Captions for audio/video
✓ Color not used as only visual means
✓ Audio control available
✓ Keyboard accessible
✓ Enough time to read content
✓ No seizure-inducing flashing
✓ Skip navigation links
✓ Page titled properly
✓ Logical focus order
✓ Link purpose clear from text
✓ Language of page specified
✓ On focus doesn't change context
✓ Consistent navigation
✓ Error identification
✓ Labels or instructions for inputs
✓ Valid HTML (parse without errors)
-->

<!-- Level AA (Mid-range) - Most Common -->
<!--
✓ Captions for live audio
✓ Audio descriptions for video
✓ Color contrast ratio 4.5:1 (3:1 for large text)
✓ Text can be resized 200%
✓ Images of text avoided (when possible)
✓ Multiple ways to find pages
✓ Headings and labels descriptive
✓ Visible keyboard focus
✓ On input doesn't change context
✓ Consistent identification
✓ Error suggestions provided
✓ Error prevention for legal/financial
-->

<!-- Level AAA (Enhanced) -->
<!--
✓ Sign language for audio
✓ Extended audio descriptions
✓ Audio-only alternative
✓ Color contrast ratio 7:1 (4.5:1 for large)
✓ No background audio
✓ Text spacing customizable
✓ Content on hover/focus controllable
✓ Section headings used
✓ Unusual words explained
✓ Abbreviations explained
✓ Reading level (lower secondary education)
✓ Context-sensitive help available
-->
</html>
```

---

### Q15.4: Common Accessibility Patterns and Solutions

**Answer:**

**1. Accessible Data Tables:**

```html
<!-- Complex table with proper structure -->
<table>
    <caption>
        Quarterly Sales Report 2026
        <details>
            <summary>Table description</summary>
            <p>This table shows sales figures for each region across quarters...</p>
        </details>
    </caption>
    
    <thead>
        <tr>
            <th scope="col">Region</th>
            <th scope="col">Q1</th>
            <th scope="col">Q2</th>
            <th scope="col">Q3</th>
            <th scope="col">Q4</th>
            <th scope="col">Total</th>
        </tr>
    </thead>
    
    <tbody>
        <tr>
            <th scope="row">North</th>
            <td>$10,000</td>
            <td>$12,000</td>
            <td>$15,000</td>
            <td>$18,000</td>
            <th scope="row">$55,000</th>
        </tr>
        <tr>
            <th scope="row">South</th>
            <td>$8,000</td>
            <td>$9,000</td>
            <td>$11,000</td>
            <td>$13,000</td>
            <th scope="row">$41,000</th>
        </tr>
    </tbody>
    
    <tfoot>
        <tr>
            <th scope="row">Grand Total</th>
            <td>$18,000</td>
            <td>$21,000</td>
            <td>$26,000</td>
            <td>$31,000</td>
            <th scope="row">$96,000</th>
        </tr>
    </tfoot>
</table>

<!-- Sortable table with accessibility -->
<table>
    <thead>
        <tr>
            <th scope="col">
                <button aria-sort="ascending" onclick="sort('name')">
                    Name
                    <span aria-hidden="true">↑</span>
                </button>
            </th>
            <th scope="col">
                <button aria-sort="none" onclick="sort('age')">
                    Age
                    <span aria-hidden="true"></span>
                </button>
            </th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Alice</td>
            <td>30</td>
        </tr>
    </tbody>
</table>
```

**2. Accessible Forms with Error Handling:**

```html
<form novalidate>
    <div class="form-group">
        <label for="email">
            Email Address
            <span aria-label="required">*</span>
        </label>
        <input type="email" 
               id="email" 
               name="email"
               required
               aria-required="true"
               aria-invalid="false"
               aria-describedby="email-desc email-error">
        <small id="email-desc">We'll never share your email</small>
        <span id="email-error" 
              role="alert" 
              class="error-message" 
              aria-live="polite"></span>
    </div>
    
    <fieldset>
        <legend>
            Subscription Plan
            <span aria-label="required">*</span>
        </legend>
        <div role="radiogroup" aria-required="true">
            <label>
                <input type="radio" 
                       name="plan" 
                       value="free" 
                       checked
                       aria-describedby="plan-desc">
                Free Plan
            </label>
            <label>
                <input type="radio" 
                       name="plan" 
                       value="pro"
                       aria-describedby="plan-desc">
                Pro Plan ($9.99/month)
            </label>
        </div>
        <small id="plan-desc">Choose the plan that works for you</small>
    </fieldset>
    
    <div class="form-group">
        <label for="password">
            Password
            <span aria-label="required">*</span>
        </label>
        <input type="password" 
               id="password" 
               name="password"
               required
               minlength="8"
               aria-required="true"
               aria-invalid="false"
               aria-describedby="password-requirements password-error">
        <ul id="password-requirements">
            <li>At least 8 characters</li>
            <li>One uppercase letter</li>
            <li>One number</li>
        </ul>
        <span id="password-error" 
              role="alert" 
              class="error-message" 
              aria-live="polite"></span>
    </div>
    
    <button type="submit">Register</button>
</form>

<script>
const form = document.querySelector('form');
form.addEventListener('submit', function(e) {
    e.preventDefault();
    
    let isValid = true;
    
    // Validate email
    const emailInput = document.getElementById('email');
    const emailError = document.getElementById('email-error');
    
    if (!emailInput.value) {
        emailInput.setAttribute('aria-invalid', 'true');
        emailError.textContent = 'Email is required';
        isValid = false;
    } else if (!emailInput.validity.valid) {
        emailInput.setAttribute('aria-invalid', 'true');
        emailError.textContent = 'Please enter a valid email address';
        isValid = false;
    } else {
        emailInput.setAttribute('aria-invalid', 'false');
        emailError.textContent = '';
    }
    
    // Validate password
    const passwordInput = document.getElementById('password');
    const passwordError = document.getElementById('password-error');
    
    if (!passwordInput.value) {
        passwordInput.setAttribute('aria-invalid', 'true');
        passwordError.textContent = 'Password is required';
        isValid = false;
    } else if (passwordInput.value.length < 8) {
        passwordInput.setAttribute('aria-invalid', 'true');
        passwordError.textContent = 'Password must be at least 8 characters';
        isValid = false;
    } else {
        passwordInput.setAttribute('aria-invalid', 'false');
        passwordError.textContent = '';
    }
    
    if (isValid) {
        // Submit form
        console.log('Form is valid!');
    } else {
        // Focus first invalid field
        const firstInvalid = form.querySelector('[aria-invalid="true"]');
        if (firstInvalid) {
            firstInvalid.focus();
        }
    }
});

// Real-time validation feedback
form.querySelectorAll('input').forEach(input => {
    input.addEventListener('blur', function() {
        // Trigger validation on blur
        form.dispatchEvent(new Event('submit'));
    });
});
</script>
```

**3. Accessible Carousel/Slider:**

```html
<section aria-roledescription="carousel" aria-label="Featured Products">
    <div class="carousel-controls">
        <button aria-label="Previous slide" onclick="prevSlide()">
            ← Previous
        </button>
        <button aria-label="Pause auto-play" 
                aria-pressed="false" 
                id="pause-btn"
                onclick="toggleAutoPlay()">
            Pause
        </button>
        <button aria-label="Next slide" onclick="nextSlide()">
            Next →
        </button>
    </div>
    
    <div class="carousel-slides">
        <article class="slide" 
                 role="group" 
                 aria-roledescription="slide"
                 aria-label="Slide 1 of 3">
            <img src="product1.jpg" alt="Product 1 description">
            <h3>Product 1</h3>
            <p>Description...</p>
        </article>
        
        <article class="slide" 
                 role="group" 
                 aria-roledescription="slide"
                 aria-label="Slide 2 of 3"
                 hidden>
            <img src="product2.jpg" alt="Product 2 description">
            <h3>Product 2</h3>
            <p>Description...</p>
        </article>
        
        <article class="slide" 
                 role="group" 
                 aria-roledescription="slide"
                 aria-label="Slide 3 of 3"
                 hidden>
            <img src="product3.jpg" alt="Product 3 description">
            <h3>Product 3</h3>
            <p>Description...</p>
        </article>
    </div>
    
    <div class="carousel-indicators" role="group" aria-label="Slide navigation">
        <button aria-label="Go to slide 1" 
                aria-current="true"
                onclick="goToSlide(0)"></button>
        <button aria-label="Go to slide 2" 
                onclick="goToSlide(1)"></button>
        <button aria-label="Go to slide 3" 
                onclick="goToSlide(2)"></button>
    </div>
</section>

<script>
let currentSlide = 0;
let autoPlayInterval;
let isAutoPlaying = true;

function showSlide(index) {
    const slides = document.querySelectorAll('.slide');
    const indicators = document.querySelectorAll('.carousel-indicators button');
    
    slides.forEach((slide, i) => {
        slide.hidden = i !== index;
    });
    
    indicators.forEach((indicator, i) => {
        indicator.setAttribute('aria-current', i === index);
    });
    
    currentSlide = index;
    
    // Announce slide change to screen readers
    const liveRegion = document.getElementById('carousel-live-region');
    if (liveRegion) {
        liveRegion.textContent = `Slide ${index + 1} of ${slides.length}`;
    }
}

function nextSlide() {
    const slides = document.querySelectorAll('.slide');
    showSlide((currentSlide + 1) % slides.length);
}

function prevSlide() {
    const slides = document.querySelectorAll('.slide');
    showSlide((currentSlide - 1 + slides.length) % slides.length);
}

function goToSlide(index) {
    showSlide(index);
    stopAutoPlay();
}

function toggleAutoPlay() {
    const btn = document.getElementById('pause-btn');
    if (isAutoPlaying) {
        stopAutoPlay();
        btn.setAttribute('aria-pressed', 'true');
        btn.textContent = 'Play';
        btn.setAttribute('aria-label', 'Resume auto-play');
    } else {
        startAutoPlay();
        btn.setAttribute('aria-pressed', 'false');
        btn.textContent = 'Pause';
        btn.setAttribute('aria-label', 'Pause auto-play');
    }
}

function startAutoPlay() {
    autoPlayInterval = setInterval(nextSlide, 5000);
    isAutoPlaying = true;
}

function stopAutoPlay() {
    clearInterval(autoPlayInterval);
    isAutoPlaying = false;
}

// Pause on hover/focus
document.querySelector('[aria-roledescription="carousel"]')
    .addEventListener('mouseenter', stopAutoPlay);

document.querySelector('[aria-roledescription="carousel"]')
    .addEventListener('mouseleave', function() {
        if (document.getElementById('pause-btn').getAttribute('aria-pressed') === 'false') {
            startAutoPlay();
        }
    });

// Add live region for announcements
const liveRegion = document.createElement('div');
liveRegion.id = 'carousel-live-region';
liveRegion.setAttribute('aria-live', 'polite');
liveRegion.setAttribute('aria-atomic', 'true');
liveRegion.style.position = 'absolute';
liveRegion.style.left = '-10000px';
document.body.appendChild(liveRegion);

// Start auto-play
startAutoPlay();
</script>
```

---



## SEO & Meta Tags

### Q16: What are the essential meta tags for SEO?

**Answer:**

**Complete SEO-Friendly Head Section:**
```html
<head>
    <!-- Character Encoding -->
    <meta charset="UTF-8">
    
    <!-- Viewport for Responsive Design -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- Title (50-60 characters) -->
    <title>Page Title - Brand Name | Max 60 Characters</title>
    
    <!-- Meta Description (150-160 characters) -->
    <meta name="description" content="Concise description of page content that appears in search results. Keep under 160 characters.">
    
    <!-- Keywords (less important now) -->
    <meta name="keywords" content="html, seo, meta tags, web development">
    
    <!-- Author -->
    <meta name="author" content="Your Name or Company">
    
    <!-- Robots -->
    <meta name="robots" content="index, follow">
    <!-- Options: index/noindex, follow/nofollow, noarchive, nosnippet -->
    
    <!-- Canonical URL (prevent duplicate content) -->
    <link rel="canonical" href="https://example.com/page">
    
    <!-- Language -->
    <meta http-equiv="content-language" content="en-US">
    <link rel="alternate" hreflang="en" href="https://example.com/en">
    <link rel="alternate" hreflang="es" href="https://example.com/es">
    
    <!-- Open Graph (Facebook, LinkedIn) -->
    <meta property="og:type" content="website">
    <meta property="og:title" content="Page Title">
    <meta property="og:description" content="Page description for social media">
    <meta property="og:image" content="https://example.com/image.jpg">
    <meta property="og:url" content="https://example.com/page">
    <meta property="og:site_name" content="Site Name">
    
    <!-- Twitter Card -->
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:site" content="@username">
    <meta name="twitter:title" content="Page Title">
    <meta name="twitter:description" content="Page description for Twitter">
    <meta name="twitter:image" content="https://example.com/image.jpg">
    
    <!-- Favicon -->
    <link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
    <link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
    
    <!-- Theme Color (mobile browsers) -->
    <meta name="theme-color" content="#4285f4">
    
    <!-- iOS Web App -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black">
    
    <!-- Schema.org Structured Data -->
    <script type="application/ld+json">
    {
      "@context": "https://schema.org",
      "@type": "Organization",
      "name": "Company Name",
      "url": "https://example.com",
      "logo": "https://example.com/logo.png",
      "contactPoint": {
        "@type": "ContactPoint",
        "telephone": "+1-555-555-5555",
        "contactType": "Customer Service"
      }
    }
    </script>
</head>
```

**Article/Blog Post Example:**
```html
<head>
    <title>How to Learn HTML - Complete Guide 2026</title>
    <meta name="description" content="Comprehensive HTML tutorial for beginners. Learn HTML fundamentals, semantic markup, forms, and modern HTML5 features with code examples.">
    
    <meta property="og:type" content="article">
    <meta property="og:title" content="How to Learn HTML - Complete Guide 2026">
    <meta property="article:author" content="John Doe">
    <meta property="article:published_time" content="2026-01-21T10:00:00Z">
    <meta property="article:tag" content="HTML">
    <meta property="article:tag" content="Web Development">
    
    <script type="application/ld+json">
    {
      "@context": "https://schema.org",
      "@type": "Article",
      "headline": "How to Learn HTML - Complete Guide 2026",
      "author": {
        "@type": "Person",
        "name": "John Doe"
      },
      "datePublished": "2026-01-21",
      "image": "https://example.com/article-image.jpg"
    }
    </script>
</head>
```

---

## Advanced Topics

### Q17: What are Web Components?

**Answer:**

Web Components are reusable custom HTML elements with encapsulated functionality.

**Three Main Technologies:**

**1. Custom Elements:**
```html
<script>
class UserCard extends HTMLElement {
    constructor() {
        super();
        // Element created
    }
    
    connectedCallback() {
        // Element added to page
        const name = this.getAttribute('name') || 'Guest';
        const email = this.getAttribute('email') || '';
        
        this.innerHTML = `
            <div class="user-card">
                <h3>${name}</h3>
                <p>${email}</p>
            </div>
        `;
    }
    
    disconnectedCallback() {
        // Element removed from page
    }
    
    attributeChangedCallback(name, oldValue, newValue) {
        // Attribute changed
        if (name === 'name') {
            this.querySelector('h3').textContent = newValue;
        }
    }
    
    static get observedAttributes() {
        return ['name', 'email'];
    }
}

// Register custom element
customElements.define('user-card', UserCard);
</script>

<!-- Use custom element -->
<user-card name="Alice" email="alice@example.com"></user-card>
<user-card name="Bob" email="bob@example.com"></user-card>
```

**2. Shadow DOM (Encapsulation):**
```html
<script>
class StyledButton extends HTMLElement {
    constructor() {
        super();
        
        // Attach shadow root
        const shadow = this.attachShadow({ mode: 'open' });
        
        // Create button
        const button = document.createElement('button');
        button.textContent = this.getAttribute('text') || 'Click';
        
        // Styles only affect this component
        const style = document.createElement('style');
        style.textContent = `
            button {
                background: #4CAF50;
                color: white;
                border: none;
                padding: 10px 20px;
                border-radius: 4px;
                cursor: pointer;
            }
            button:hover {
                background: #45a049;
            }
        `;
        
        // Add to shadow DOM
        shadow.appendChild(style);
        shadow.appendChild(button);
    }
}

customElements.define('styled-button', StyledButton);
</script>

<styled-button text="Click Me"></styled-button>
<!-- Styles don't leak to rest of page -->
```

**3. HTML Templates:**
```html
<!-- Define template -->
<template id="product-template">
    <style>
        .product {
            border: 1px solid #ddd;
            padding: 20px;
            margin: 10px;
        }
    </style>
    <div class="product">
        <img src="" alt="">
        <h3 class="name"></h3>
        <p class="price"></p>
        <button>Add to Cart</button>
    </div>
</template>

<script>
class ProductCard extends HTMLElement {
    constructor() {
        super();
        const template = document.getElementById('product-template');
        const templateContent = template.content;
        
        const shadowRoot = this.attachShadow({ mode: 'open' });
        shadowRoot.appendChild(templateContent.cloneNode(true));
        
        // Set content
        shadowRoot.querySelector('.name').textContent = 
            this.getAttribute('name');
        shadowRoot.querySelector('.price').textContent = 
            this.getAttribute('price');
        shadowRoot.querySelector('img').src = 
            this.getAttribute('image');
    }
}

customElements.define('product-card', ProductCard);
</script>

<!-- Use component -->
<product-card 
    name="Laptop" 
    price="$999" 
    image="laptop.jpg">
</product-card>
```

---

### Q18: What are HTML5 APIs for advanced functionality?

**Answer:**

**1. Geolocation API:**
```html
<button onclick="getLocation()">Get My Location</button>
<div id="location"></div>

<script>
function getLocation() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
            (position) => {
                const lat = position.coords.latitude;
                const lon = position.coords.longitude;
                document.getElementById('location').innerHTML = 
                    `Latitude: ${lat}<br>Longitude: ${lon}`;
            },
            (error) => {
                console.error('Error:', error.message);
            }
        );
    } else {
        alert('Geolocation not supported');
    }
}
</script>
```

**2. Drag and Drop API:**
```html
<div id="drag-item" draggable="true">Drag me</div>
<div id="drop-zone">Drop here</div>

<style>
    #drag-item {
        width: 100px;
        height: 100px;
        background: lightblue;
        cursor: move;
    }
    #drop-zone {
        width: 200px;
        height: 200px;
        border: 2px dashed #ccc;
        margin-top: 20px;
    }
    .drag-over {
        background: lightyellow;
    }
</style>

<script>
const dragItem = document.getElementById('drag-item');
const dropZone = document.getElementById('drop-zone');

// Drag start
dragItem.addEventListener('dragstart', (e) => {
    e.dataTransfer.effectAllowed = 'move';
    e.dataTransfer.setData('text/html', e.target.innerHTML);
});

// Allow drop
dropZone.addEventListener('dragover', (e) => {
    e.preventDefault();
    dropZone.classList.add('drag-over');
});

// Drag leave
dropZone.addEventListener('dragleave', () => {
    dropZone.classList.remove('drag-over');
});

// Drop
dropZone.addEventListener('drop', (e) => {
    e.preventDefault();
    dropZone.classList.remove('drag-over');
    const data = e.dataTransfer.getData('text/html');
    dropZone.innerHTML = data;
});
</script>
```

**3. History API:**
```html
<script>
// Add to browser history
function navigateTo(page) {
    history.pushState({ page: page }, page, `/${page}`);
    loadContent(page);
}

// Handle back/forward buttons
window.addEventListener('popstate', (event) => {
    if (event.state) {
        loadContent(event.state.page);
    }
});

// Replace current history entry
history.replaceState({ page: 'home' }, 'home', '/home');
</script>
```

**4. Notification API:**
```html
<button onclick="notifyMe()">Notify Me</button>

<script>
function notifyMe() {
    if (!('Notification' in window)) {
        alert('Notifications not supported');
        return;
    }
    
    Notification.requestPermission().then(permission => {
        if (permission === 'granted') {
            new Notification('Hello!', {
                body: 'This is a notification',
                icon: 'icon.png',
                tag: 'unique-notification-id'
            });
        }
    });
}
</script>
```

**5. Web Workers (Background Threads):**
```html
<!-- main.html -->
<script>
const worker = new Worker('worker.js');

// Send message to worker
worker.postMessage({ data: [1, 2, 3, 4, 5] });

// Receive message from worker
worker.onmessage = (event) => {
    console.log('Result from worker:', event.data);
};
</script>

<!-- worker.js -->
<script>
self.onmessage = (event) => {
    const numbers = event.data.data;
    const sum = numbers.reduce((a, b) => a + b, 0);
    
    // Send result back
    self.postMessage(sum);
};
</script>
```

---

### Q19: What are the best practices for HTML performance?

**Answer:**

**1. Minimize HTML Size:**
```html
<!-- ❌ Bad: Excessive whitespace -->
<div>
    <p>
        Text content
    </p>
</div>

<!-- ✅ Good: Minified (in production) -->
<div><p>Text content</p></div>
```

**2. Load CSS in Head, JS Before </body>:**
```html
<!DOCTYPE html>
<html>
<head>
    <!-- ✅ CSS in head (blocks rendering, but needed) -->
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <!-- Content -->
    
    <!-- ✅ JS at bottom (doesn't block content) -->
    <script src="script.js"></script>
</body>
</html>
```

**3. Async/Defer for Scripts:**
```html
<!-- Regular: Blocks parsing -->
<script src="script.js"></script>

<!-- Async: Downloads in parallel, executes when ready -->
<script src="script.js" async></script>

<!-- Defer: Downloads in parallel, executes after parsing -->
<script src="script.js" defer></script>

<!-- Use defer for most scripts -->
<script src="jquery.js" defer></script>
<script src="app.js" defer></script>
```

**4. Lazy Load Images:**
```html
<!-- Native lazy loading -->
<img src="image.jpg" alt="Description" loading="lazy">

<!-- Above the fold: eager loading -->
<img src="hero.jpg" alt="Hero" loading="eager">

<!-- Responsive images -->
<img src="small.jpg"
     srcset="small.jpg 400w, medium.jpg 800w, large.jpg 1200w"
     sizes="(max-width: 600px) 400px, (max-width: 900px) 800px, 1200px"
     alt="Responsive image"
     loading="lazy">
```

**5. Preload Critical Resources:**
```html
<head>
    <!-- Preload critical CSS -->
    <link rel="preload" href="critical.css" as="style">
    
    <!-- Preload fonts -->
    <link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>
    
    <!-- DNS prefetch for external domains -->
    <link rel="dns-prefetch" href="https://fonts.googleapis.com">
    
    <!-- Preconnect to external domains -->
    <link rel="preconnect" href="https://api.example.com">
    
    <!-- Prefetch next page -->
    <link rel="prefetch" href="next-page.html">
</head>
```

**6. Optimize Images:**
```html
<!-- Use appropriate format -->
<picture>
    <source type="image/webp" srcset="image.webp">
    <source type="image/jpeg" srcset="image.jpg">
    <img src="image.jpg" alt="Fallback">
</picture>

<!-- Specify dimensions to prevent layout shift -->
<img src="image.jpg" alt="Product" width="300" height="200">
```

**7. Use Semantic HTML (better parsing):**
```html
<!-- ✅ Semantic: Faster parsing -->
<nav>
    <ul>
        <li><a href="/">Home</a></li>
    </ul>
</nav>

<!-- ❌ Non-semantic: Slower parsing -->
<div class="nav">
    <div class="list">
        <div class="item"><a href="/">Home</a></div>
    </div>
</div>
```

---

### Q20: How do you create a responsive HTML page?

**Answer:**

**1. Viewport Meta Tag:**
```html
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
```

**2. Responsive Images:**
```html
<!-- Flexible image -->
<img src="image.jpg" alt="Description" style="max-width: 100%; height: auto;">

<!-- Art direction (different images for different screens) -->
<picture>
    <source media="(min-width: 1200px)" srcset="large.jpg">
    <source media="(min-width: 768px)" srcset="medium.jpg">
    <img src="small.jpg" alt="Responsive image">
</picture>

<!-- Resolution switching -->
<img src="small.jpg"
     srcset="small.jpg 400w,
             medium.jpg 800w,
             large.jpg 1200w"
     sizes="(max-width: 600px) 100vw,
            (max-width: 900px) 50vw,
            33vw"
     alt="Image">
```

**3. Responsive Layout Structure:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Responsive Page</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
        }
        
        /* Mobile-first approach */
        .container {
            width: 100%;
            padding: 0 15px;
            margin: 0 auto;
        }
        
        /* Tablet */
        @media (min-width: 768px) {
            .container {
                max-width: 750px;
            }
        }
        
        /* Desktop */
        @media (min-width: 1200px) {
            .container {
                max-width: 1140px;
            }
        }
        
        /* Flexible grid */
        .row {
            display: flex;
            flex-wrap: wrap;
            margin: 0 -15px;
        }
        
        .col {
            flex: 1;
            padding: 0 15px;
            min-width: 100%;
        }
        
        @media (min-width: 768px) {
            .col {
                min-width: 50%;
            }
        }
        
        @media (min-width: 1200px) {
            .col {
                min-width: 33.333%;
            }
        }
        
        /* Navigation */
        nav {
            background: #333;
            color: white;
        }
        
        nav ul {
            list-style: none;
            display: flex;
            flex-direction: column;
        }
        
        @media (min-width: 768px) {
            nav ul {
                flex-direction: row;
            }
        }
        
        nav a {
            display: block;
            padding: 15px;
            color: white;
            text-decoration: none;
        }
        
        /* Responsive table */
        table {
            width: 100%;
            border-collapse: collapse;
        }
        
        @media (max-width: 767px) {
            /* Stack table rows on mobile */
            table, thead, tbody, th, td, tr {
                display: block;
            }
            
            thead tr {
                display: none;
            }
            
            td {
                text-align: right;
                padding-left: 50%;
                position: relative;
            }
            
            td::before {
                content: attr(data-label);
                position: absolute;
                left: 0;
                width: 50%;
                padding-left: 15px;
                font-weight: bold;
                text-align: left;
            }
        }
    </style>
</head>
<body>
    <nav>
        <ul>
            <li><a href="#home">Home</a></li>
            <li><a href="#about">About</a></li>
            <li><a href="#contact">Contact</a></li>
        </ul>
    </nav>
    
    <div class="container">
        <h1>Responsive Page</h1>
        
        <div class="row">
            <div class="col">
                <h2>Column 1</h2>
                <p>Content...</p>
            </div>
            <div class="col">
                <h2>Column 2</h2>
                <p>Content...</p>
            </div>
            <div class="col">
                <h2>Column 3</h2>
                <p>Content...</p>
            </div>
        </div>
        
        <table>
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Age</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td data-label="Name">Alice</td>
                    <td data-label="Email">alice@example.com</td>
                    <td data-label="Age">25</td>
                </tr>
            </tbody>
        </table>
    </div>
</body>
</html>
```

**4. Responsive Video:**
```html
<style>
    .video-container {
        position: relative;
        padding-bottom: 56.25%; /* 16:9 aspect ratio */
        height: 0;
        overflow: hidden;
    }
    
    .video-container iframe,
    .video-container video {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
    }
</style>

<div class="video-container">
    <iframe src="https://www.youtube.com/embed/VIDEO_ID" 
            frameborder="0" 
            allowfullscreen></iframe>
</div>
```

---

## Summary

**Key Takeaways:**

**Beginner Level:**
- Understand HTML structure and syntax
- Know basic elements (headings, paragraphs, links, images)
- Create simple forms
- Use lists and tables

**Intermediate Level:**
- Write semantic HTML
- Implement forms with validation
- Use data attributes
- Understand block vs inline elements

**Advanced Level:**
- Create accessible HTML (ARIA)
- Optimize for SEO with meta tags
- Use HTML5 APIs (Canvas, Storage, Geolocation)
- Build Web Components
- Implement responsive design
- Optimize performance

**Best Practices:**
1. Always use semantic HTML
2. Include alt text for images
3. Validate your HTML
4. Keep code clean and organized
5. Think about accessibility
6. Optimize for performance
7. Make it responsive
8. Test across browsers

**Remember:** HTML is the foundation of the web. Master it well, and you'll have a solid base for all web development!
