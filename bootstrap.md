# Bootstrap Framework - 200 Basic Level Questions & Answers

---

## 📱 Visual Guide to Bootstrap Components

This guide includes complete HTML examples for each topic to help you understand how Bootstrap components look and work.

### Browser Display Preview Legend
- **Complete HTML Examples**: Full working code that you can copy and paste into an HTML file
- **What you see**: Description of how the component appears in the browser
- **Visual Components**: Includes colors, spacing, and interactive elements
- **Responsive Design**: Shows how components adapt to different screen sizes

### Key Things to Remember
1. Always include the Bootstrap CDN or CSS file in `<head>`
2. Include Bootstrap JS bundle before closing `</body>`
3. Use container classes to wrap content
4. Test examples on different screen sizes
5. Copy complete examples and open them in your browser to see results

---

## Table of Contents
1. [Introduction & Basics (1-20)](#introduction--basics)
2. [Grid System (21-40)](#grid-system)
3. [Typography (41-60)](#typography)
4. [Colors & Backgrounds (61-80)](#colors--backgrounds)
5. [Components (81-120)](#components)
6. [Forms (121-140)](#forms)
7. [Utilities (141-160)](#utilities)
8. [Responsive Design (161-180)](#responsive-design)
9. [JavaScript Components (181-200)](#javascript-components)

---

## Introduction & Basics

### 1. What is Bootstrap?
Bootstrap is a free, open-source CSS framework for building responsive and mobile-first web applications. It provides pre-built components and utilities that speed up web development.

### 2. What are the main advantages of Bootstrap?
- Easy to use and learn
- Responsive design built-in
- Pre-built components
- Cross-browser compatibility
- Mobile-first approach
- Large community support
- Reduces development time
- Consistent design

### 3. What are the disadvantages of Bootstrap?
- All Bootstrap sites look similar
- Bulky CSS file size
- Limited customization without modifying source
- Requires jQuery for some components (Bootstrap 4)
- Learning curve for beginners

### 4. What version of Bootstrap should I use?
Bootstrap 5 is the latest and recommended version. It dropped jQuery dependency, improved flexibility, and enhanced customization. Previous versions (3, 4) are still supported but Bootstrap 5 is preferred for new projects.

### 5. How do you include Bootstrap in your project?
```html
<!-- Via CDN -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

<!-- Via npm -->
npm install bootstrap
```

**Complete HTML Example:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bootstrap Setup</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h1 class="text-center text-primary">Welcome to Bootstrap</h1>
        <p class="text-center">Bootstrap is now included in your project!</p>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Centered heading in blue color
- Centered paragraph text
- Page with proper spacing and styling
- Ready to use Bootstrap components

### 6. What is a Container in Bootstrap?
A container is a Bootstrap element that wraps content and provides padding and responsive behavior. There are two types: `.container` (fixed width) and `.container-fluid` (full width).

### 7. What is the mobile-first approach?
Mobile-first means designing for mobile devices first, then progressively enhancing the design for larger screens. Bootstrap uses this approach with mobile styles as default and media queries for larger screens.

### 8. What are breakpoints in Bootstrap?
Breakpoints are predefined screen size thresholds used for responsive design. Bootstrap 5 has: `xs` (default), `sm` (576px), `md` (768px), `lg` (992px), `xl` (1200px), `xxl` (1400px).

### 9. What is the Bootstrap grid system?
The grid system is a 12-column layout system that helps create responsive designs. It uses rows and columns to structure content and automatically adjust based on screen size.

### 10. What are the required HTML5 elements in Bootstrap?
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bootstrap Page</title>
</head>
<body>
    <!-- Content here -->
</body>
</html>
```

### 11. What is the viewport meta tag?
The viewport meta tag tells the browser how to render the page on different devices. `<meta name="viewport" content="width=device-width, initial-scale=1.0">` ensures responsive design works properly.

### 12. How do you create a basic Bootstrap page?
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container">
        <h1>Hello Bootstrap</h1>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**Complete Example with More Content:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>My Bootstrap Page</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <nav class="navbar navbar-light bg-light">
        <div class="container">
            <span class="navbar-brand mb-0 h1">MyApp</span>
        </div>
    </nav>
    
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-8">
                <h1>Welcome to Bootstrap</h1>
                <p>This is a basic Bootstrap page with a navbar and grid layout.</p>
            </div>
            <div class="col-md-4">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title">Sidebar</h5>
                        <p class="card-text">Additional content goes here</p>
                    </div>
                </div>
            </div>
        </div>
    </div>
    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Navbar at the top with "MyApp" text
- Page title and description
- Two-column layout (8 columns main content, 4 columns sidebar)
- Card component in the sidebar
- Proper spacing and responsive design

### 13. What is the difference between .container and .container-fluid?
- `.container`: Has fixed width and centered with left/right margins
- `.container-fluid`: Takes full width of the viewport (100%)

### 14. What are utility classes in Bootstrap?
Utility classes are single-purpose classes that apply specific styles. Examples: `.m-2` (margin), `.p-3` (padding), `.text-center` (text alignment), `.d-flex` (display flex).

### 15. How do you use custom CSS with Bootstrap?
Create a separate CSS file after Bootstrap link:
```html
<link href="bootstrap.css" rel="stylesheet">
<link href="custom.css" rel="stylesheet">
```

### 16. What is SCSS in Bootstrap?
Bootstrap uses SCSS (Sass CSS) for its source code. It allows variables, nesting, and mixins which make customization easier than plain CSS.

### 17. How do you customize Bootstrap variables?
```scss
// Override Bootstrap's default variables before importing Bootstrap
$primary: #007bff;
$secondary: #6c757d;
$success: #28a745;

@import "../../node_modules/bootstrap/scss/bootstrap";
```

### 18. What does "Normalize.css" do in Bootstrap?
Normalize.css is included in Bootstrap to make browsers render elements consistently. It fixes common bugs and improves cross-browser compatibility.

### 19. What is the Bootstrap CDN?
Content Delivery Network (CDN) is a fast way to include Bootstrap without downloading it. It uses: `cdn.jsdelivr.net`, `cdnjs.com`, or `maxcdn.bootstrapcdn.com`.

### 20. Can you use Bootstrap without CDN?
Yes, download Bootstrap files from `getbootstrap.com` and include them locally in your project using `<link>` and `<script>` tags.

---

## Grid System

### 21. How does the Bootstrap grid system work?
The grid divides the page into 12 equal columns. You use `.row` for rows and `.col` for columns. Content automatically adjusts based on screen size using responsive classes.

### 22. What is a row in Bootstrap?
A row is a horizontal group of columns. Use `.row` class to create rows. Rows must be inside containers and columns must be inside rows.

### 23. What is a column in Bootstrap?
A column is a vertical division of space. Use `.col` or `.col-{number}` to create columns. Multiple columns can exist in one row.

### 24. What does .col mean?
`.col` automatically divides available space equally among all columns in a row. Three `.col` in a row = each gets 25% width (12÷3=4 columns each).

### 25. How do you specify column width?
Use `.col-{number}` where number is 1-12. Example: `.col-4` takes 4 out of 12 columns (33.33% width).

### 26. What is responsive grid notation?
Use `.col-{breakpoint}-{number}` to set column width at specific breakpoints. Example: `.col-md-6` means 6 columns on medium screens and up.

### 27. What are common grid column classes?
- `.col-sm-6` - 50% width on small screens and up
- `.col-md-4` - 33.33% width on medium screens and up
- `.col-lg-3` - 25% width on large screens and up
- `.col-xl-2` - 20% width on extra-large screens

### 28. How do you create equal-width columns?
```html
<div class="row">
    <div class="col">Column 1</div>
    <div class="col">Column 2</div>
    <div class="col">Column 3</div>
</div>
```

**Complete Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2>Equal Width Columns</h2>
        <div class="row">
            <div class="col bg-light border p-3">
                <h5>Column 1</h5>
                <p>Each column takes equal width</p>
            </div>
            <div class="col bg-light border p-3">
                <h5>Column 2</h5>
                <p>Responsive on all screens</p>
            </div>
            <div class="col bg-light border p-3">
                <h5>Column 3</h5>
                <p>Stacks on mobile devices</p>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Three columns with equal width (each 33.33%)
- Light gray background with borders
- Text describing each column
- On mobile: stacks into single column
- On desktop: displays side by side

### 29. What is the .row-cols class?
`.row-cols-{number}` sets the number of columns in a row. Example: `.row-cols-3` creates 3 equal-width columns.

### 30. How do you create unequal column widths?
```html
<div class="row">
    <div class="col-md-8">Wide column</div>
    <div class="col-md-4">Narrow column</div>
</div>
```

**Complete Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2>Unequal Column Widths</h2>
        
        <h5 class="mt-4">8 Column + 4 Column Layout:</h5>
        <div class="row mb-4">
            <div class="col-md-8 bg-light border p-3">
                <h5>Main Content (8 columns - 66%)</h5>
                <p>This is the main content area taking up 8 out of 12 columns.</p>
                <p>On mobile, this stacks above the sidebar.</p>
            </div>
            <div class="col-md-4 bg-light border p-3">
                <h5>Sidebar (4 columns - 33%)</h5>
                <p>This is the sidebar taking up 4 columns.</p>
            </div>
        </div>
        
        <h5>9 Column + 3 Column Layout:</h5>
        <div class="row mb-4">
            <div class="col-md-9 bg-info text-white border p-3">
                <h5>Main (9 columns - 75%)</h5>
                <p>Larger main content area</p>
            </div>
            <div class="col-md-3 bg-success text-white border p-3">
                <h5>Side (3 columns - 25%)</h5>
                <p>Smaller sidebar</p>
            </div>
        </div>
        
        <h5>5 Column + 7 Column Layout:</h5>
        <div class="row mb-4">
            <div class="col-md-5 bg-warning border p-3">
                <h5>Section 1 (5 columns)</h5>
                <p>Custom column ratio</p>
            </div>
            <div class="col-md-7 bg-danger text-white border p-3">
                <h5>Section 2 (7 columns)</h5>
                <p>Unequal distribution</p>
            </div>
        </div>
        
        <h5>Responsive Unequal Columns:</h5>
        <div class="row">
            <div class="col-12 col-md-7 bg-primary text-white border p-3 mb-2 mb-md-0">
                <h5>Main Content</h5>
                <p>Full width on mobile, 7 columns on desktop</p>
            </div>
            <div class="col-12 col-md-5 bg-secondary text-white border p-3">
                <h5>Secondary Content</h5>
                <p>Full width on mobile, 5 columns on desktop</p>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- First layout: Main content takes 8 columns (66%), sidebar takes 4 columns (33%)
- Second layout: 9 columns and 3 columns ratio (75% / 25%)
- Third layout: 5 columns and 7 columns (custom ratio)
- Fourth layout: On mobile - full width stacking, on desktop - side by side
- Different background colors to show column divisions
- Responsive: Columns stack on small screens

### 31. What does auto layout mean in Bootstrap grid?
Auto layout automatically sizes columns based on content width. Use `.col-auto` for columns that size based on content.

### 32. How do you nest columns in Bootstrap?
```html
<div class="row">
    <div class="col-md-8">
        <div class="row">
            <div class="col-md-6">Nested 1</div>
            <div class="col-md-6">Nested 2</div>
        </div>
    </div>
</div>
```

### 33. What is gutters in Bootstrap grid?
Gutters are the gaps between columns created by padding. Default gutter is 24px. Use `.g-0` to remove gutters.

### 34. How do you adjust gutter size?
Use `.g-{number}` or `.g{direction}-{number}`:
- `.g-0` - No gutter
- `.g-2` - Small gutter
- `.g-4` - Medium gutter
- `.gx-3` - Horizontal gutter only

### 35. What is column offsetting?
Offsetting moves columns to the right. Use `.offset-{number}` or `.offset-{breakpoint}-{number}`:
```html
<div class="col-md-4 offset-md-2">Moved right by 2 columns</div>
```

### 36. How do you reorder columns?
Use `.order-{number}` (1-5) or use flexbox utilities:
```html
<div class="col order-3">Third</div>
<div class="col order-1">First</div>
<div class="col order-2">Second</div>
```

### 37. What are alignment utilities in grid?
- `.align-items-start` - Align top
- `.align-items-center` - Align middle
- `.align-items-end` - Align bottom
- `.justify-content-start` - Align left
- `.justify-content-center` - Align center
- `.justify-content-end` - Align right

### 38. How do you hide columns on certain breakpoints?
Use display utilities:
```html
<div class="col-md-6 d-none d-md-block">Hidden on small screens</div>
<div class="col-md-6 d-md-none">Hidden on medium screens and up</div>
```

### 39. What is .col-auto in Bootstrap?
`.col-auto` creates a column that only takes up as much space as needed for its content.

### 40. How do you make a grid column full width?
Use `.col-12` or `.col-{breakpoint}-12` to make a column span all 12 columns.

---

## Typography

### 41. What are heading tags in Bootstrap?
Bootstrap styles `<h1>` to `<h6>` tags. All headings have margin-bottom of 0.5rem and use `$headings-font-weight: 500`.

### 42. What are heading classes in Bootstrap?
Use `.h1` to `.h6` classes to style non-heading elements as headings:
```html
<p class="h1">This paragraph looks like an H1</p>
```

### 43. What is `.display-{number}` class?
Display classes (`.display-1` to `.display-6`) create larger headings. `.display-1` is the largest and is used for hero sections.

### 44. What is a lead paragraph in Bootstrap?
Use `.lead` class to make a paragraph stand out:
```html
<p class="lead">This paragraph is emphasized</p>
```

### 45. How do you mark text in Bootstrap?
Use `<mark>` tag to highlight text:
```html
<p>This is <mark>highlighted</mark> text</p>
```

### 46. What is the `.text-muted` class?
`.text-muted` makes text gray/light to de-emphasize it. Used for secondary information.

### 47. What is `.small` class in Bootstrap?
`.small` reduces font size to 85% of parent font size.

### 48. How do you create abbreviations in Bootstrap?
Use `<abbr>` with `.initialism` class:
```html
<abbr title="Hypertext Markup Language" class="initialism">HTML</abbr>
```

### 49. How do you create blockquotes in Bootstrap?
```html
<blockquote class="blockquote">
    <p>Quote here</p>
    <footer class="blockquote-footer">Quote source</footer>
</blockquote>
```

### 50. What is `.list-unstyled` class?
`.list-unstyled` removes default list styling (bullets and indentation) from `<ul>` or `<ol>`.

### 51. What is `.list-inline` with `.list-inline-item`?
```html
<ul class="list-inline">
    <li class="list-inline-item">Item 1</li>
    <li class="list-inline-item">Item 2</li>
</ul>
```
Displays list items horizontally instead of vertically.

### 52. What is description list in Bootstrap?
Bootstrap styles `<dl>`, `<dt>` (term), and `<dd>` (definition) tags with proper spacing and alignment.

### 53. How do you align text in Bootstrap?
Use `.text-start`, `.text-center`, `.text-end`, `.text-justify` for horizontal alignment.

### 54. What is `.text-nowrap` class?
`.text-nowrap` prevents text from wrapping to next line. Useful for keeping text on single line.

### 55. How do you truncate text in Bootstrap?
```html
<p class="text-truncate">Long text that will be cut off...</p>
```

### 56. What is `.text-break` class?
`.text-break` breaks long words and prevents overflow. Useful for URLs and long strings.

### 57. How do you transform text case in Bootstrap?
- `.text-lowercase` - Convert to lowercase
- `.text-uppercase` - Convert to uppercase
- `.text-capitalize` - Capitalize first letter

### 58. What is `.font-monospace` class?
`.font-monospace` applies monospace font (like code). Default is SFMono-Regular, Menlo, Monaco, Consolas.

### 59. How do you make text bold or italic?
```html
<strong>Bold text</strong>
<em>Italic text</em>
<u>Underlined text</u>
<s>Strikethrough text</s>
```

### 60. What is `.initials` in Bootstrap?
Actually it's `.initialism` - used for abbreviations to display them in small caps.

---

## Colors & Backgrounds

### 61. What are Bootstrap color utilities?
Bootstrap provides utility classes for text color and background color based on theme colors: primary, secondary, success, danger, warning, info, light, dark.

### 62. How do you set text color in Bootstrap?
Use `.text-{color}`:
```html
<p class="text-primary">Primary text</p>
<p class="text-success">Success text</p>
<p class="text-danger">Danger text</p>
```

**Complete Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Text Color Examples</h2>
        
        <h5>Theme Colors:</h5>
        <p class="text-primary">Primary text (Blue)</p>
        <p class="text-secondary">Secondary text (Gray)</p>
        <p class="text-success">Success text (Green)</p>
        <p class="text-danger">Danger text (Red)</p>
        <p class="text-warning">Warning text (Yellow)</p>
        <p class="text-info">Info text (Cyan)</p>
        <p class="text-light">Light text (Light Gray)</p>
        <p class="text-dark">Dark text (Dark Gray)</p>
        
        <h5 class="mt-4">Special Text Colors:</h5>
        <p class="text-muted">Muted text (Faded gray)</p>
        <p class="text-body">Body text (Default color)</p>
        <p class="text-emphasis">Emphasis text (Darker)</p>
        
        <h5 class="mt-4">Background with Text Color:</h5>
        <p class="bg-primary text-white p-2">Primary background with white text</p>
        <p class="bg-success text-white p-2">Success background with white text</p>
        <p class="bg-danger text-white p-2">Danger background with white text</p>
        <p class="bg-warning text-dark p-2">Warning background with dark text</p>
        <p class="bg-info text-white p-2">Info background with white text</p>
        
        <h5 class="mt-4">Opacity Variations (Bootstrap 5):</h5>
        <p class="text-primary-50">50% opacity primary</p>
        <p class="text-success-75">75% opacity success</p>
        <p class="text-danger-25">25% opacity danger</p>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Primary text in blue
- Secondary text in gray
- Success text in green
- Danger text in red
- Warning text in yellow/orange
- Info text in cyan
- Light text in light gray
- Dark text in dark gray
- Muted text appears faded
- Text colors are consistent with theme
- Good contrast for readability

### 63. What color values do Bootstrap colors have by default?
- `primary`: #0d6efd (blue)
- `secondary`: #6c757d (gray)
- `success`: #198754 (green)
- `danger`: #dc3545 (red)
- `warning`: #ffc107 (yellow)
- `info`: #0dcaf0 (cyan)
- `light`: #f8f9fa (light gray)
- `dark`: #212529 (dark gray)

### 64. How do you set background color in Bootstrap?
Use `.bg-{color}`:
```html
<div class="bg-primary">Primary background</div>
<div class="bg-success">Success background</div>
<div class="bg-danger">Danger background</div>
```

### 65. What is `.text-muted` color?
`.text-muted` is a lighter gray color (#6c757d) used to de-emphasize text.

### 66. What is `.text-white` and `.text-dark`?
`.text-white` makes text white (used on dark backgrounds), `.text-dark` makes text dark.

### 67. How do you create colored links?
```html
<a href="#" class="link-primary">Primary link</a>
<a href="#" class="link-danger">Danger link</a>
```

### 68. What is opacity in Bootstrap colors?
Bootstrap color utilities support opacity variations:
```html
<p class="text-primary-50">50% opacity primary</p>
<p class="text-danger-75">75% opacity danger</p>
```

### 69. What are gradient backgrounds in Bootstrap?
Use `.bg-gradient` with `.bg-{color}` for gradient effect:
```html
<div class="bg-primary bg-gradient">Gradient background</div>
```

### 70. How do you use custom colors in Bootstrap?
Define custom CSS variables in SCSS:
```scss
$custom-color: #ff6b6b;
.text-custom {
    color: $custom-color;
}
```

### 71. What is `.border` class?
`.border` adds a 1px solid border to element. Use `.border-{color}` to set border color.

### 72. How do you style borders in Bootstrap?
- `.border` - Add border
- `.border-0` - Remove all borders
- `.border-top` - Top border only
- `.border-danger` - Danger colored border

### 73. What is `.rounded` class?
`.rounded` adds border-radius. Variations:
- `.rounded-0` - No rounding
- `.rounded-1` - Small rounding
- `.rounded-pill` - Fully rounded (border-radius: 9999px)

### 74. What is `.shadow` class?
`.shadow` adds box-shadow to element. Variations:
- `.shadow-sm` - Small shadow
- `.shadow` - Medium shadow
- `.shadow-lg` - Large shadow

### 75. What are opacity utilities in Bootstrap?
`.opacity-{number}` sets opacity (0-100):
```html
<div class="opacity-25">25% opacity</div>
<div class="opacity-50">50% opacity</div>
<div class="opacity-75">75% opacity</div>
```

### 76. How do you use RGBa colors in Bootstrap?
Bootstrap CSS variables use CSS custom properties for colors allowing easy opacity adjustments.

### 77. What is `.bg-body`?
`.bg-body` sets background to body background color (usually white or off-white).

### 78. What are color aliases in Bootstrap?
Color aliases map to base colors:
- `.text-body` - Body text color
- `.text-muted` - Muted text color
- `.text-emphasis` - Emphasis text color

### 79. How do you combine text and background colors?
```html
<div class="bg-danger text-white p-3">Danger alert</div>
```

### 80. What is the `.link-opacity` class?
`.link-opacity-{number}` sets link opacity without changing color.

---

## Components

### 81. What are Bootstrap components?
Bootstrap components are pre-built, reusable UI elements like buttons, navbars, cards, modals, etc.

### 82. How do you create a button in Bootstrap?
```html
<button class="btn btn-primary">Button</button>
<a href="#" class="btn btn-success">Link Button</a>
```

**Complete Example with Multiple Button Styles:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Button Examples</h2>
        
        <h5>Solid Buttons:</h5>
        <div class="mb-3">
            <button class="btn btn-primary">Primary</button>
            <button class="btn btn-secondary">Secondary</button>
            <button class="btn btn-success">Success</button>
            <button class="btn btn-danger">Danger</button>
            <button class="btn btn-warning">Warning</button>
            <button class="btn btn-info">Info</button>
            <button class="btn btn-light">Light</button>
            <button class="btn btn-dark">Dark</button>
        </div>
        
        <h5>Outline Buttons:</h5>
        <div class="mb-3">
            <button class="btn btn-outline-primary">Primary</button>
            <button class="btn btn-outline-secondary">Secondary</button>
            <button class="btn btn-outline-success">Success</button>
            <button class="btn btn-outline-danger">Danger</button>
        </div>
        
        <h5>Button Sizes:</h5>
        <div>
            <button class="btn btn-primary btn-lg">Large Button</button>
            <button class="btn btn-primary">Regular Button</button>
            <button class="btn btn-primary btn-sm">Small Button</button>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Multiple button colors (primary, secondary, success, danger, warning, info, light, dark)
- Solid buttons with colored background and white text
- Outline buttons with colored border and text, transparent background
- Three button sizes: Large, Regular, Small
- All buttons are clickable and styled with Bootstrap

### 83. What are button styles in Bootstrap?
- `.btn-primary` - Primary action
- `.btn-secondary` - Secondary action
- `.btn-success` - Success action
- `.btn-danger` - Dangerous action
- `.btn-warning` - Warning action
- `.btn-info` - Info
- `.btn-light` - Light
- `.btn-dark` - Dark

### 84. What is `.btn-outline-{color}`?
`.btn-outline-{color}` creates a button with outline style (colored text and border, transparent background):
```html
<button class="btn btn-outline-primary">Outline Button</button>
```

### 85. How do you create button sizes?
- `.btn-lg` - Large button
- `.btn` - Regular button (default)
- `.btn-sm` - Small button

### 86. What is `.btn-block`?
`.btn-block` makes button full width of parent container.

### 87. How do you create a disabled button?
```html
<button class="btn btn-primary" disabled>Disabled Button</button>
```

### 88. What are button groups?
Button groups combine related buttons together:
```html
<div class="btn-group">
    <button class="btn btn-primary">Left</button>
    <button class="btn btn-primary">Middle</button>
    <button class="btn btn-primary">Right</button>
</div>
```

### 89. What is a badge in Bootstrap?
A badge is a small count or label component:
```html
<span class="badge bg-primary">5</span>
<span class="badge bg-danger">Important</span>
```

**Complete Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Badge Examples</h2>
        
        <h5>Basic Badges:</h5>
        <p>
            <span class="badge bg-primary">Primary</span>
            <span class="badge bg-secondary">Secondary</span>
            <span class="badge bg-success">Success</span>
            <span class="badge bg-danger">Danger</span>
            <span class="badge bg-warning text-dark">Warning</span>
            <span class="badge bg-info">Info</span>
            <span class="badge bg-light text-dark">Light</span>
            <span class="badge bg-dark">Dark</span>
        </p>
        
        <h5>Pills (Rounded Badges):</h5>
        <p>
            <span class="badge rounded-pill bg-primary">New</span>
            <span class="badge rounded-pill bg-success">Approved</span>
            <span class="badge rounded-pill bg-danger">Rejected</span>
            <span class="badge rounded-pill bg-warning text-dark">Pending</span>
        </p>
        
        <h5>Badges in Headings:</h5>
        <h1>Heading <span class="badge bg-secondary">v5.0</span></h1>
        <h2>Another Heading <span class="badge bg-info">New</span></h2>
        
        <h5>Badges with Numbers:</h5>
        <p>
            <span class="badge bg-primary">5</span>
            <span class="badge bg-success">99+</span>
            <span class="badge bg-danger">3</span>
        </p>
        
        <h5>Badges in List Items:</h5>
        <ul class="list-group mt-3">
            <li class="list-group-item">
                Inbox 
                <span class="badge bg-primary rounded-pill float-end">5</span>
            </li>
            <li class="list-group-item">
                Important 
                <span class="badge bg-danger rounded-pill float-end">2</span>
            </li>
            <li class="list-group-item">
                Archived 
                <span class="badge bg-success rounded-pill float-end">12</span>
            </li>
        </ul>
        
        <h5 class="mt-4">Status Badges:</h5>
        <p>
            <span class="badge bg-success">Active</span>
            <span class="badge bg-secondary">Inactive</span>
            <span class="badge bg-danger">Error</span>
            <span class="badge bg-warning text-dark">Warning</span>
        </p>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Badges are small inline labels with background colors
- Rectangular badges for standard display
- Rounded/pill badges (fully rounded corners)
- Different color themes (Primary, Secondary, Success, Danger, etc.)
- Badges with numbers and text
- Used in headings to show version or status
- Badges in list items showing counts (inbox, messages, etc.)
- Great for notifications, status indicators, and tags

### 90. How do you create an alert in Bootstrap?
```html
<div class="alert alert-primary">Alert message</div>
<div class="alert alert-danger alert-dismissible fade show">
    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
    Message
</div>
```

**Complete Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Alert Examples</h2>
        
        <div class="alert alert-primary" role="alert">
            <strong>Primary Alert!</strong> This is a primary information message.
        </div>
        
        <div class="alert alert-success" role="alert">
            <strong>Success!</strong> Your action was completed successfully.
        </div>
        
        <div class="alert alert-warning" role="alert">
            <strong>Warning!</strong> Please be careful with this action.
        </div>
        
        <div class="alert alert-danger" role="alert">
            <strong>Error!</strong> Something went wrong. Please try again.
        </div>
        
        <div class="alert alert-info alert-dismissible fade show" role="alert">
            <strong>Info:</strong> You can dismiss this alert by clicking the X button.
            <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </div>
        
        <div class="alert alert-danger alert-dismissible fade show" role="alert">
            <strong>Dismissible Alert!</strong> Click the X button to close this alert.
            <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Primary alert: Blue background with bold text
- Success alert: Green background indicating successful action
- Warning alert: Yellow/orange background for caution
- Danger alert: Red background for errors
- Info alert: Cyan background for information
- Dismissible alerts: Have an X button to close them
- Each alert has different color and styling

### 91. What is a breadcrumb in Bootstrap?
Breadcrumbs show navigation hierarchy:
```html
<nav aria-label="breadcrumb">
    <ol class="breadcrumb">
        <li class="breadcrumb-item"><a href="#">Home</a></li>
        <li class="breadcrumb-item active">Current</li>
    </ol>
</nav>
```

### 92. How do you create a card in Bootstrap?
```html
<div class="card">
    <div class="card-body">
        <h5 class="card-title">Title</h5>
        <p class="card-text">Content</p>
    </div>
</div>
```

**Complete Example with Multiple Cards:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Card Examples</h2>
        
        <div class="row">
            <div class="col-md-4 mb-4">
                <div class="card">
                    <img src="https://via.placeholder.com/300x200" class="card-img-top" alt="Card image">
                    <div class="card-body">
                        <h5 class="card-title">Card Title 1</h5>
                        <p class="card-text">This is a basic card with an image on top.</p>
                        <a href="#" class="btn btn-primary">Learn More</a>
                    </div>
                </div>
            </div>
            
            <div class="col-md-4 mb-4">
                <div class="card">
                    <div class="card-header">
                        <h5 class="card-title mb-0">Card with Header</h5>
                    </div>
                    <div class="card-body">
                        <p class="card-text">Cards can have headers and footers.</p>
                    </div>
                    <div class="card-footer text-muted">
                        Footer text here
                    </div>
                </div>
            </div>
            
            <div class="col-md-4 mb-4">
                <div class="card bg-light">
                    <div class="card-body">
                        <h5 class="card-title">Colored Card</h5>
                        <p class="card-text">Cards can have background colors.</p>
                        <span class="badge bg-primary">Tag 1</span>
                        <span class="badge bg-success">Tag 2</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Three cards displayed in a row
- First card: Has image at top, title, text, and button
- Second card: Has header, body, and footer sections
- Third card: Light background with title, text, and badge tags
- Cards stack vertically on mobile devices
- Professional layout for content organization

### 93. What are card parts?
- `.card` - Container
- `.card-header` - Header section
- `.card-body` - Main content area
- `.card-footer` - Footer section
- `.card-img-top` - Image on top
- `.card-title` - Title
- `.card-text` - Paragraph text

### 94. How do you create a dropdown menu?
```html
<div class="dropdown">
    <button class="btn btn-primary dropdown-toggle" data-bs-toggle="dropdown">
        Dropdown
    </button>
    <ul class="dropdown-menu">
        <li><a class="dropdown-item" href="#">Item 1</a></li>
        <li><a class="dropdown-item" href="#">Item 2</a></li>
    </ul>
</div>
```

**Complete Example with Different Dropdown Types:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Dropdown Examples</h2>
        
        <h5>Basic Dropdown:</h5>
        <div class="dropdown mb-4">
            <button class="btn btn-primary dropdown-toggle" data-bs-toggle="dropdown">
                Basic Dropdown
            </button>
            <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="#">Action</a></li>
                <li><a class="dropdown-item" href="#">Another action</a></li>
                <li><a class="dropdown-item" href="#">Something else here</a></li>
            </ul>
        </div>
        
        <h5>Dropdown with Divider:</h5>
        <div class="dropdown mb-4">
            <button class="btn btn-success dropdown-toggle" data-bs-toggle="dropdown">
                Menu Options
            </button>
            <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="#">Edit</a></li>
                <li><a class="dropdown-item" href="#">Delete</a></li>
                <li><hr class="dropdown-divider"></li>
                <li><a class="dropdown-item" href="#">Report</a></li>
            </ul>
        </div>
        
        <h5>Dropdown with Headers:</h5>
        <div class="dropdown mb-4">
            <button class="btn btn-info dropdown-toggle" data-bs-toggle="dropdown">
                Categories
            </button>
            <ul class="dropdown-menu">
                <li><h6 class="dropdown-header">Office Suite</h6></li>
                <li><a class="dropdown-item" href="#">Word</a></li>
                <li><a class="dropdown-item" href="#">Excel</a></li>
                <li><hr class="dropdown-divider"></li>
                <li><h6 class="dropdown-header">Creative</h6></li>
                <li><a class="dropdown-item" href="#">Photoshop</a></li>
                <li><a class="dropdown-item" href="#">Illustrator</a></li>
            </ul>
        </div>
        
        <h5>Disabled Items in Dropdown:</h5>
        <div class="dropdown mb-4">
            <button class="btn btn-warning dropdown-toggle" data-bs-toggle="dropdown">
                Settings
            </button>
            <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="#">Profile</a></li>
                <li><a class="dropdown-item" href="#">Preferences</a></li>
                <li><a class="dropdown-item disabled" href="#">Advanced (Disabled)</a></li>
            </ul>
        </div>
        
        <h5>Dropdown Button Group:</h5>
        <div class="btn-group mb-4">
            <button type="button" class="btn btn-danger">Left</button>
            <button type="button" class="btn btn-danger dropdown-toggle dropdown-toggle-split" data-bs-toggle="dropdown">
                <span class="visually-hidden">Toggle</span>
            </button>
            <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="#">Action</a></li>
                <li><a class="dropdown-item" href="#">Another</a></li>
            </ul>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Basic dropdown: Click button to show menu items
- Dropdown with divider: Separates related items with a line
- Dropdown with headers: Organize items into categories
- Disabled items: Items that can't be clicked (grayed out)
- Split dropdown: Button and arrow are separate
- Menu items appear below the button
- Click outside to close the dropdown
- Smooth animation when opening/closing

### 95. How do you create a navbar in Bootstrap?
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container">
        <a class="navbar-brand" href="#">Logo</a>
        <button class="navbar-toggler" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item"><a class="nav-link" href="#">Home</a></li>
                <li class="nav-item"><a class="nav-link" href="#">About</a></li>
            </ul>
        </div>
    </div>
</nav>
```

**Complete Example with Different Navbar Styles:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <!-- Light Navbar -->
    <nav class="navbar navbar-expand-lg navbar-light bg-light border-bottom">
        <div class="container">
            <a class="navbar-brand fw-bold" href="#">MyApp</a>
            <button class="navbar-toggler" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ms-auto">
                    <li class="nav-item"><a class="nav-link active" href="#">Home</a></li>
                    <li class="nav-item"><a class="nav-link" href="#">Features</a></li>
                    <li class="nav-item"><a class="nav-link" href="#">Pricing</a></li>
                    <li class="nav-item"><a class="nav-link" href="#">Contact</a></li>
                </ul>
            </div>
        </div>
    </nav>
    
    <!-- Dark Navbar -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark mb-5">
        <div class="container">
            <a class="navbar-brand fw-bold" href="#">DarkApp</a>
            <button class="navbar-toggler" data-bs-toggle="collapse" data-bs-target="#navbarNav2">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav2">
                <ul class="navbar-nav ms-auto">
                    <li class="nav-item"><a class="nav-link active" href="#">Home</a></li>
                    <li class="nav-item"><a class="nav-link" href="#">Features</a></li>
                    <li class="nav-item"><a class="nav-link" href="#">About</a></li>
                    <li class="nav-item"><a class="nav-link" href="#">Sign In</a></li>
                </ul>
            </div>
        </div>
    </nav>
    
    <div class="container mt-5">
        <p>The navbars above collapse on mobile screens. Click the hamburger menu on small screens.</p>
    </div>
    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Light navbar with gray background
- Dark navbar with dark background and white text
- "MyApp" and "DarkApp" brand names on left
- Navigation links (Home, Features, Pricing/About, Contact/Sign In) on right
- Hamburger menu button that appears on mobile screens
- Active link is highlighted
- On mobile: Menu items hide and show via hamburger button
- On desktop: All menu items visible horizontally

### 96. What is `.navbar-brand`?
`.navbar-brand` is used for logo or brand name in navbar. Usually an `<a>` or `<span>` element.

### 97. What is a modal in Bootstrap?
A modal is a dialog box overlay:
```html
<div class="modal fade" id="myModal">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Title</h5>
            </div>
            <div class="modal-body">Content</div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
            </div>
        </div>
    </div>
</div>
```

**Complete Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Modal Examples</h2>
        
        <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#basicModal">
            Open Basic Modal
        </button>
        <button class="btn btn-success" data-bs-toggle="modal" data-bs-target="#confirmModal">
            Confirmation Modal
        </button>
    </div>
    
    <!-- Basic Modal -->
    <div class="modal fade" id="basicModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Welcome!</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <p>This is a basic Bootstrap modal.</p>
                    <p>You can add any content here - text, forms, images, etc.</p>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                    <button type="button" class="btn btn-primary">Save Changes</button>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Confirmation Modal -->
    <div class="modal fade" id="confirmModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header bg-danger text-white">
                    <h5 class="modal-title">Confirm Action</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <p><strong>Are you sure?</strong></p>
                    <p>This action cannot be undone.</p>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                    <button type="button" class="btn btn-danger">Delete</button>
                </div>
            </div>
        </div>
    </div>
    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Two buttons that trigger modals when clicked
- Basic modal: Contains a title, message, and action buttons
- Confirmation modal: Has red header indicating a dangerous action
- Modal has a backdrop (darkened background)
- Close button (X) in the header
- Footer with Cancel/Close and action buttons
- When modal opens: Page content becomes slightly dimmed
- Click outside modal or X button to close it

### 98. How do you trigger a modal?
```html
<button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#myModal">
    Open Modal
</button>
```

### 99. What is a pagination in Bootstrap?
```html
<ul class="pagination">
    <li class="page-item"><a class="page-link" href="#">Previous</a></li>
    <li class="page-item"><a class="page-link" href="#">1</a></li>
    <li class="page-item active"><span class="page-link">2</span></li>
    <li class="page-item"><a class="page-link" href="#">Next</a></li>
</ul>
```

**Complete Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Pagination Examples</h2>
        
        <h5>Basic Pagination:</h5>
        <nav class="mb-4">
            <ul class="pagination">
                <li class="page-item disabled">
                    <a class="page-link" href="#">Previous</a>
                </li>
                <li class="page-item active"><span class="page-link">1</span></li>
                <li class="page-item"><a class="page-link" href="#">2</a></li>
                <li class="page-item"><a class="page-link" href="#">3</a></li>
                <li class="page-item"><a class="page-link" href="#">Next</a></li>
            </ul>
        </nav>
        
        <h5>Pagination with Full Set:</h5>
        <nav class="mb-4">
            <ul class="pagination">
                <li class="page-item">
                    <a class="page-link" href="#" aria-label="Previous">
                        <span aria-hidden="true">&laquo;</span>
                    </a>
                </li>
                <li class="page-item"><a class="page-link" href="#">1</a></li>
                <li class="page-item"><a class="page-link" href="#">2</a></li>
                <li class="page-item"><a class="page-link" href="#">3</a></li>
                <li class="page-item"><a class="page-link" href="#">4</a></li>
                <li class="page-item"><a class="page-link" href="#">5</a></li>
                <li class="page-item">
                    <a class="page-link" href="#" aria-label="Next">
                        <span aria-hidden="true">&raquo;</span>
                    </a>
                </li>
            </ul>
        </nav>
        
        <h5>Large Pagination:</h5>
        <nav class="mb-4">
            <ul class="pagination pagination-lg">
                <li class="page-item disabled"><a class="page-link" href="#">Previous</a></li>
                <li class="page-item active"><span class="page-link">1</span></li>
                <li class="page-item"><a class="page-link" href="#">2</a></li>
                <li class="page-item"><a class="page-link" href="#">3</a></li>
                <li class="page-item"><a class="page-link" href="#">Next</a></li>
            </ul>
        </nav>
        
        <h5>Small Pagination:</h5>
        <nav class="mb-4">
            <ul class="pagination pagination-sm">
                <li class="page-item disabled"><a class="page-link" href="#">Previous</a></li>
                <li class="page-item active"><span class="page-link">1</span></li>
                <li class="page-item"><a class="page-link" href="#">2</a></li>
                <li class="page-item"><a class="page-link" href="#">3</a></li>
                <li class="page-item"><a class="page-link" href="#">Next</a></li>
            </ul>
        </nav>
        
        <h5>Centered Pagination:</h5>
        <nav class="d-flex justify-content-center mb-4">
            <ul class="pagination">
                <li class="page-item disabled"><a class="page-link" href="#">Prev</a></li>
                <li class="page-item active"><span class="page-link">1</span></li>
                <li class="page-item"><a class="page-link" href="#">2</a></li>
                <li class="page-item"><a class="page-link" href="#">3</a></li>
                <li class="page-item"><a class="page-link" href="#">Next</a></li>
            </ul>
        </nav>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Basic pagination with Previous/Next and page numbers
- Current page highlighted in blue (active state)
- Previous button disabled (grayed out) when on first page
- Large pagination with bigger buttons
- Small pagination with compact size
- Centered pagination using flexbox
- Arrow symbols (&laquo; and &raquo;) for Previous/Next
- Numbered buttons for direct page access
- Good for navigating large lists or search results

### 100. How do you create a progress bar in Bootstrap?
```html
<div class="progress">
    <div class="progress-bar" role="progressbar" style="width: 25%"></div>
</div>
```

**Complete Example with Different Progress Bars:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Progress Bar Examples</h2>
        
        <h5>Basic Progress Bars:</h5>
        <div class="mb-3">
            <p>25% Complete</p>
            <div class="progress">
                <div class="progress-bar" role="progressbar" style="width: 25%"></div>
            </div>
        </div>
        
        <div class="mb-3">
            <p>50% Complete</p>
            <div class="progress">
                <div class="progress-bar" role="progressbar" style="width: 50%"></div>
            </div>
        </div>
        
        <div class="mb-3">
            <p>75% Complete</p>
            <div class="progress">
                <div class="progress-bar" role="progressbar" style="width: 75%"></div>
            </div>
        </div>
        
        <div class="mb-4">
            <p>100% Complete</p>
            <div class="progress">
                <div class="progress-bar" role="progressbar" style="width: 100%"></div>
            </div>
        </div>
        
        <h5>Colored Progress Bars:</h5>
        <div class="mb-3">
            <p>Success (75%)</p>
            <div class="progress">
                <div class="progress-bar bg-success" role="progressbar" style="width: 75%"></div>
            </div>
        </div>
        
        <div class="mb-3">
            <p>Info (60%)</p>
            <div class="progress">
                <div class="progress-bar bg-info" role="progressbar" style="width: 60%"></div>
            </div>
        </div>
        
        <div class="mb-3">
            <p>Warning (45%)</p>
            <div class="progress">
                <div class="progress-bar bg-warning" role="progressbar" style="width: 45%"></div>
            </div>
        </div>
        
        <div class="mb-4">
            <p>Danger (30%)</p>
            <div class="progress">
                <div class="progress-bar bg-danger" role="progressbar" style="width: 30%"></div>
            </div>
        </div>
        
        <h5>Progress Bar with Label:</h5>
        <div class="mb-3">
            <div class="progress">
                <div class="progress-bar" role="progressbar" style="width: 68%">68%</div>
            </div>
        </div>
        
        <h5>Striped Progress Bar:</h5>
        <div class="mb-3">
            <div class="progress">
                <div class="progress-bar progress-bar-striped" role="progressbar" style="width: 80%"></div>
            </div>
        </div>
        
        <h5>Animated Progress Bar:</h5>
        <div class="mb-3">
            <div class="progress">
                <div class="progress-bar progress-bar-striped progress-bar-animated" role="progressbar" style="width: 90%"></div>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Progress bars at different percentages (25%, 50%, 75%, 100%)
- Default blue color for progress bars
- Different color variants: Success (green), Info (cyan), Warning (yellow), Danger (red)
- Progress bar with percentage label displayed inside
- Striped pattern (diagonal lines)
- Animated striped progress bar (lines move from left to right)
- Each bar has a background container and filled portion
- Responsive and easy to customize

### 101. What is a spinner in Bootstrap?
```html
<div class="spinner-border"></div>
<div class="spinner-grow"></div>
```

### 102. How do you create a table in Bootstrap?
```html
<table class="table table-striped table-hover">
    <thead>
        <tr>
            <th>Header 1</th>
            <th>Header 2</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Data 1</td>
            <td>Data 2</td>
        </tr>
    </tbody>
</table>
```

**Complete Example with Multiple Table Styles:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Table Examples</h2>
        
        <h5>Basic Striped & Hover Table:</h5>
        <table class="table table-striped table-hover mb-5">
            <thead class="table-dark">
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Status</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>1</td>
                    <td>John Doe</td>
                    <td>john@example.com</td>
                    <td><span class="badge bg-success">Active</span></td>
                </tr>
                <tr>
                    <td>2</td>
                    <td>Jane Smith</td>
                    <td>jane@example.com</td>
                    <td><span class="badge bg-success">Active</span></td>
                </tr>
                <tr>
                    <td>3</td>
                    <td>Bob Johnson</td>
                    <td>bob@example.com</td>
                    <td><span class="badge bg-warning">Pending</span></td>
                </tr>
            </tbody>
        </table>
        
        <h5>Bordered Table:</h5>
        <table class="table table-bordered table-hover mb-5">
            <thead class="table-light">
                <tr>
                    <th>Product</th>
                    <th>Price</th>
                    <th>Quantity</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Laptop</td>
                    <td>$999</td>
                    <td>5</td>
                </tr>
                <tr>
                    <td>Mouse</td>
                    <td>$25</td>
                    <td>15</td>
                </tr>
                <tr>
                    <td>Keyboard</td>
                    <td>$79</td>
                    <td>8</td>
                </tr>
            </tbody>
        </table>
        
        <h5>Dark Table:</h5>
        <table class="table table-dark table-striped">
            <thead>
                <tr>
                    <th>Feature</th>
                    <th>Description</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Responsive</td>
                    <td>Works on all screen sizes</td>
                </tr>
                <tr>
                    <td>Accessible</td>
                    <td>WCAG compliant design</td>
                </tr>
                <tr>
                    <td>Lightweight</td>
                    <td>Minimal CSS overhead</td>
                </tr>
            </tbody>
        </table>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- First table: Dark header with white text, striped rows (alternating colors), hover effect
- Second table: Borders around all cells, light header, responsive design
- Third table: Dark background with white text
- Striped rows: Alternating row colors for better readability
- Hover effect: Rows highlight when you mouse over them
- Badge badges show different statuses in different colors
- Tables are responsive and scroll horizontally on small screens

### 103. What are table styles in Bootstrap?
- `.table` - Basic styling
- `.table-striped` - Alternating row colors
- `.table-hover` - Highlight row on hover
- `.table-bordered` - Borders on all cells
- `.table-borderless` - No borders
- `.table-dark` - Dark background

### 104. What is a tooltip in Bootstrap?
```html
<button type="button" class="btn btn-primary" data-bs-toggle="tooltip" title="Tooltip text">
    Hover me
</button>
```

### 105. What is a popover in Bootstrap?
Similar to tooltip but with title and larger content area.

### 106. How do you create a toast in Bootstrap?
```html
<div class="toast">
    <div class="toast-header">
        <strong>Notification</strong>
    </div>
    <div class="toast-body">
        Message here
    </div>
</div>
```

### 107. What is a list group in Bootstrap?
```html
<ul class="list-group">
    <li class="list-group-item">Item 1</li>
    <li class="list-group-item">Item 2</li>
    <li class="list-group-item active">Item 3</li>
</ul>
```

### 108. What is a scrollspy in Bootstrap?
Scrollspy automatically highlights navigation items as user scrolls through page sections.

### 109. What are collapse/accordion in Bootstrap?
Collapsible content that expands/collapses when clicked:
```html
<div class="accordion">
    <div class="accordion-item">
        <h2 class="accordion-header">
            <button class="accordion-button" data-bs-toggle="collapse" data-bs-target="#item1">
                Item 1
            </button>
        </h2>
        <div id="item1" class="accordion-collapse collapse">
            <div class="accordion-body">Content</div>
        </div>
    </div>
</div>
```

**Complete Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Accordion Example</h2>
        
        <div class="accordion" id="faqAccordion">
            <div class="accordion-item">
                <h2 class="accordion-header">
                    <button class="accordion-button" type="button" data-bs-toggle="collapse" data-bs-target="#faq1">
                        What is Bootstrap?
                    </button>
                </h2>
                <div id="faq1" class="accordion-collapse collapse show" data-bs-parent="#faqAccordion">
                    <div class="accordion-body">
                        Bootstrap is a popular CSS framework that provides pre-built components and utilities 
                        for creating responsive web applications quickly. It includes buttons, forms, modals, navbars, and much more.
                    </div>
                </div>
            </div>
            
            <div class="accordion-item">
                <h2 class="accordion-header">
                    <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#faq2">
                        Is Bootstrap free?
                    </button>
                </h2>
                <div id="faq2" class="accordion-collapse collapse" data-bs-parent="#faqAccordion">
                    <div class="accordion-body">
                        Yes, Bootstrap is completely free and open-source. You can download it from getbootstrap.com 
                        or include it via CDN without any cost.
                    </div>
                </div>
            </div>
            
            <div class="accordion-item">
                <h2 class="accordion-header">
                    <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#faq3">
                        Can I customize Bootstrap?
                    </button>
                </h2>
                <div id="faq3" class="accordion-collapse collapse" data-bs-parent="#faqAccordion">
                    <div class="accordion-body">
                        Absolutely! You can customize Bootstrap in several ways: override CSS with your own styles, 
                        use SCSS variables for customization, or modify the source code before building.
                    </div>
                </div>
            </div>
            
            <div class="accordion-item">
                <h2 class="accordion-header">
                    <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#faq4">
                        What version should I use?
                    </button>
                </h2>
                <div id="faq4" class="accordion-collapse collapse" data-bs-parent="#faqAccordion">
                    <div class="accordion-body">
                        Bootstrap 5 is the latest and recommended version. It has no jQuery dependency, 
                        improved customization, and better features than Bootstrap 4.
                    </div>
                </div>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Accordion with 4 collapsible sections
- Each section has a question as the header
- Click header to expand/collapse the section
- Only one section can be open at a time (default behavior)
- First section is expanded by default (`.show` class)
- Other sections are collapsed and expand when clicked
- Smooth animation when opening/closing
- Good for FAQs and organizing content

### 110. What is a carousel in Bootstrap?
```html
<div id="carouselExample" class="carousel slide">
    <div class="carousel-inner">
        <div class="carousel-item active">
            <img src="image1.jpg" alt="Slide 1">
        </div>
        <div class="carousel-item">
            <img src="image2.jpg" alt="Slide 2">
        </div>
    </div>
    <button class="carousel-control-prev" data-bs-target="#carouselExample" data-bs-slide="prev"></button>
    <button class="carousel-control-next" data-bs-target="#carouselExample" data-bs-slide="next"></button>
</div>
```

**Complete Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Image Carousel</h2>
        
        <div id="imageCarousel" class="carousel slide" data-bs-ride="carousel">
            <div class="carousel-indicators">
                <button type="button" data-bs-target="#imageCarousel" data-bs-slide-to="0" class="active"></button>
                <button type="button" data-bs-target="#imageCarousel" data-bs-slide-to="1"></button>
                <button type="button" data-bs-target="#imageCarousel" data-bs-slide-to="2"></button>
                <button type="button" data-bs-target="#imageCarousel" data-bs-slide-to="3"></button>
            </div>
            
            <div class="carousel-inner">
                <div class="carousel-item active">
                    <img src="https://via.placeholder.com/800x400?text=Slide+1" class="d-block w-100" alt="Slide 1">
                    <div class="carousel-caption d-none d-md-block">
                        <h5>First Slide</h5>
                        <p>Welcome to the carousel</p>
                    </div>
                </div>
                
                <div class="carousel-item">
                    <img src="https://via.placeholder.com/800x400?text=Slide+2" class="d-block w-100" alt="Slide 2">
                    <div class="carousel-caption d-none d-md-block">
                        <h5>Second Slide</h5>
                        <p>Swipe or click to navigate</p>
                    </div>
                </div>
                
                <div class="carousel-item">
                    <img src="https://via.placeholder.com/800x400?text=Slide+3" class="d-block w-100" alt="Slide 3">
                    <div class="carousel-caption d-none d-md-block">
                        <h5>Third Slide</h5>
                        <p>Carousels are responsive</p>
                    </div>
                </div>
                
                <div class="carousel-item">
                    <img src="https://via.placeholder.com/800x400?text=Slide+4" class="d-block w-100" alt="Slide 4">
                    <div class="carousel-caption d-none d-md-block">
                        <h5>Fourth Slide</h5>
                        <p>Perfect for image galleries</p>
                    </div>
                </div>
            </div>
            
            <button class="carousel-control-prev" type="button" data-bs-target="#imageCarousel" data-bs-slide="prev">
                <span class="carousel-control-prev-icon"></span>
            </button>
            <button class="carousel-control-next" type="button" data-bs-target="#imageCarousel" data-bs-slide="next">
                <span class="carousel-control-next-icon"></span>
            </button>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Large image area showing one slide at a time
- Carousel indicators (dots) at the top - click to jump to specific slide
- Left and right arrow buttons to navigate
- Captions/titles on each slide (hidden on mobile, visible on desktop)
- Automatic rotation through slides
- Smooth transitions between images
- Responsive: images scale to fit container
- Can be used for image galleries, testimonials, or banners

### 111. What is a nav component in Bootstrap?
```html
<ul class="nav">
    <li class="nav-item">
        <a class="nav-link active" href="#">Active</a>
    </li>
    <li class="nav-item">
        <a class="nav-link" href="#">Link</a>
    </li>
</ul>
```

### 112. What is `.nav-tabs` in Bootstrap?
`.nav-tabs` styles nav links with tabs appearance.

### 113. What is `.nav-pills` in Bootstrap?
`.nav-pills` styles nav links as pills (more rounded).

### 114. How do you create an image in Bootstrap?
```html
<img src="image.jpg" class="img-fluid" alt="Description">
<img src="image.jpg" class="rounded" alt="Rounded image">
```

### 115. What is `.img-fluid`?
`.img-fluid` makes image responsive (max-width: 100%, height: auto).

### 116. What is `.figure` in Bootstrap?
```html
<figure class="figure">
    <img src="image.jpg" class="figure-img img-fluid rounded" alt="Caption">
    <figcaption class="figure-caption">Image caption</figcaption>
</figure>
```

### 117. How do you create a badge with background color?
```html
<span class="badge bg-primary">New</span>
<span class="badge bg-success rounded-pill">Success</span>
```

### 118. What is a jumbotron (now called hero) in Bootstrap?
In Bootstrap 5, use `.hero` or just `.display-4` with `.py-5` for large hero section.

### 119. What is an input group in Bootstrap?
```html
<div class="input-group">
    <span class="input-group-text">@</span>
    <input type="text" class="form-control" placeholder="Username">
</div>
```

### 120. How do you create a button with icon?
```html
<button class="btn btn-primary">
    <i class="bi bi-download"></i> Download
</button>
```

---

## Forms

### 121. How do you create a form in Bootstrap?
```html
<form>
    <div class="mb-3">
        <label for="email" class="form-label">Email:</label>
        <input type="email" class="form-control" id="email">
    </div>
</form>
```

**Complete Form Example:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-6 mx-auto">
                <h2 class="mb-4">Registration Form</h2>
                
                <form>
                    <div class="mb-3">
                        <label for="name" class="form-label">Full Name</label>
                        <input type="text" class="form-control" id="name" placeholder="Enter your name">
                    </div>
                    
                    <div class="mb-3">
                        <label for="email" class="form-label">Email Address</label>
                        <input type="email" class="form-control" id="email" placeholder="your@example.com">
                    </div>
                    
                    <div class="mb-3">
                        <label for="password" class="form-label">Password</label>
                        <input type="password" class="form-control" id="password" placeholder="Enter password">
                        <small class="form-text text-muted">Must be 8+ characters</small>
                    </div>
                    
                    <div class="mb-3">
                        <label for="country" class="form-label">Country</label>
                        <select class="form-select" id="country">
                            <option selected>Choose a country...</option>
                            <option>United States</option>
                            <option>Canada</option>
                            <option>United Kingdom</option>
                            <option>India</option>
                        </select>
                    </div>
                    
                    <div class="mb-3">
                        <label for="bio" class="form-label">Biography</label>
                        <textarea class="form-control" id="bio" rows="3" placeholder="Tell us about yourself..."></textarea>
                    </div>
                    
                    <div class="mb-3">
                        <div class="form-check">
                            <input type="checkbox" class="form-check-input" id="terms">
                            <label class="form-check-label" for="terms">
                                I agree to the terms and conditions
                            </label>
                        </div>
                        <div class="form-check">
                            <input type="checkbox" class="form-check-input" id="newsletter">
                            <label class="form-check-label" for="newsletter">
                                Subscribe to our newsletter
                            </label>
                        </div>
                    </div>
                    
                    <div class="mb-3">
                        <label class="form-label">Gender</label>
                        <div class="form-check">
                            <input type="radio" class="form-check-input" name="gender" id="male" value="male">
                            <label class="form-check-label" for="male">Male</label>
                        </div>
                        <div class="form-check">
                            <input type="radio" class="form-check-input" name="gender" id="female" value="female">
                            <label class="form-check-label" for="female">Female</label>
                        </div>
                    </div>
                    
                    <button type="submit" class="btn btn-primary w-100 mb-2">Register</button>
                    <button type="reset" class="btn btn-secondary w-100">Clear Form</button>
                </form>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- Form centered with max-width on medium screens
- Text inputs for Name and Email
- Password input field with help text
- Dropdown select for country
- Large textarea for biography
- Checkboxes for terms and newsletter subscription
- Radio buttons for gender selection
- Register button (primary blue) and Clear button (secondary gray)
- Form fields have proper spacing with `.mb-3` margin
- Labels are clearly associated with inputs
- Form is responsive and mobile-friendly

### 122. What are form control classes?
- `.form-control` - Text input styling
- `.form-control-lg` - Large input
- `.form-control-sm` - Small input
- `.form-control-plaintext` - Plain text style

### 123. What is `.form-label`?
`.form-label` styles label elements with proper spacing and font.

### 124. How do you create form groups?
```html
<div class="form-group">
    <label for="name">Name:</label>
    <input type="text" class="form-control" id="name">
</div>
```

### 125. What is `.mb-3` class?
`.mb-3` adds margin-bottom (1rem). Used to space form elements vertically.

### 126. How do you create checkboxes in Bootstrap?
```html
<div class="form-check">
    <input type="checkbox" class="form-check-input" id="check1">
    <label class="form-check-label" for="check1">Option 1</label>
</div>
```

### 127. How do you create radio buttons in Bootstrap?
```html
<div class="form-check">
    <input type="radio" class="form-check-input" name="options" id="radio1">
    <label class="form-check-label" for="radio1">Option 1</label>
</div>
```

### 128. How do you create select dropdowns in Bootstrap?
```html
<select class="form-select">
    <option>Choose...</option>
    <option>Option 1</option>
    <option>Option 2</option>
</select>
```

### 129. How do you create textarea in Bootstrap?
```html
<textarea class="form-control" rows="3" placeholder="Enter text..."></textarea>
```

### 130. What is form validation in Bootstrap?
Bootstrap provides `.is-valid` and `.is-invalid` classes for validation:
```html
<input type="email" class="form-control is-valid">
<input type="email" class="form-control is-invalid">
<div class="invalid-feedback">Error message</div>
```

### 131. What is `.form-text` class?
`.form-text` adds help text below form controls:
```html
<input type="password" class="form-control">
<small class="form-text text-muted">Must be 8+ characters</small>
```

### 132. How do you create inline forms?
Use `.row` and `.col` to align form elements horizontally:
```html
<form class="row">
    <div class="col-auto">
        <input type="text" class="form-control" placeholder="Email">
    </div>
    <div class="col-auto">
        <button class="btn btn-primary">Submit</button>
    </div>
</form>
```

### 133. What is `.form-floating`?
`.form-floating` creates floating labels that animate above input on focus:
```html
<div class="form-floating">
    <input type="email" class="form-control" id="email" placeholder="Email">
    <label for="email">Email address</label>
</div>
```

### 134. How do you disable form controls?
```html
<input type="text" class="form-control" disabled>
<input type="text" class="form-control" readonly>
```

### 135. What is `.form-switch`?
`.form-switch` creates toggle switch instead of checkbox:
```html
<div class="form-check form-switch">
    <input type="checkbox" class="form-check-input" id="switch">
    <label class="form-check-label" for="switch">Toggle me</label>
</div>
```

### 136. How do you size input groups?
```html
<div class="input-group input-group-lg">
    <span class="input-group-text">$</span>
    <input type="text" class="form-control">
</div>
```

### 137. What is `.form-control-color`?
`.form-control-color` creates color picker input:
```html
<input type="color" class="form-control form-control-color" value="#563d7c">
```

### 138. How do you create range sliders in Bootstrap?
```html
<input type="range" class="form-range" min="0" max="100">
```

### 139. What is form datalist?
```html
<input class="form-control" list="datalistOptions" placeholder="Search...">
<datalist id="datalistOptions">
    <option value="Option 1">
    <option value="Option 2">
</datalist>
```

### 140. How do you create responsive forms?
Use grid classes for responsive layouts:
```html
<div class="row g-3">
    <div class="col-md-6">
        <input type="text" class="form-control">
    </div>
    <div class="col-md-6">
        <input type="email" class="form-control">
    </div>
</div>
```

---

## Utilities

### 141. What are margin utilities in Bootstrap?
`.m{direction}-{size}` sets margins:
- `.m-0` to `.m-5` - All margins
- `.mt-2` - Margin top
- `.mb-3` - Margin bottom
- `.ms-4` - Margin start (left)
- `.me-5` - Margin end (right)
- `.mx-auto` - Horizontal auto margin (center)

### 142. What are padding utilities in Bootstrap?
`.p{direction}-{size}` sets padding (same as margin):
- `.p-2` - All padding
- `.pt-3` - Padding top
- `.pb-4` - Padding bottom
- `.px-3` - Horizontal padding

### 143. What is `.d-flex` class?
`.d-flex` sets display: flex, enabling flexbox layout:
```html
<div class="d-flex justify-content-center align-items-center">
    Centered content
</div>
```

**Complete Example with Flexbox:**
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h2 class="mb-4">Flexbox Examples</h2>
        
        <h5>Center Content Horizontally and Vertically:</h5>
        <div class="d-flex justify-content-center align-items-center bg-light p-5 mb-4" style="height: 200px;">
            <div class="text-center">
                <h4>Centered Box</h4>
                <p>This content is perfectly centered</p>
            </div>
        </div>
        
        <h5>Space Between Items:</h5>
        <div class="d-flex justify-content-between bg-light p-3 mb-4">
            <div>Item 1</div>
            <div>Item 2</div>
            <div>Item 3</div>
        </div>
        
        <h5>Space Around Items:</h5>
        <div class="d-flex justify-content-around bg-light p-3 mb-4">
            <button class="btn btn-primary">Button 1</button>
            <button class="btn btn-success">Button 2</button>
            <button class="btn btn-danger">Button 3</button>
        </div>
        
        <h5>Vertical Stack (flex-column):</h5>
        <div class="d-flex flex-column gap-2 mb-4">
            <div class="bg-primary text-white p-3">Item 1</div>
            <div class="bg-success text-white p-3">Item 2</div>
            <div class="bg-danger text-white p-3">Item 3</div>
        </div>
        
        <h5>Horizontal with Gap:</h5>
        <div class="d-flex gap-3">
            <div class="card flex-grow-1">
                <div class="card-body">Card 1</div>
            </div>
            <div class="card flex-grow-1">
                <div class="card-body">Card 2</div>
            </div>
            <div class="card flex-grow-1">
                <div class="card-body">Card 3</div>
            </div>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**What you see:**
- First box: Content centered both horizontally and vertically
- Second row: Items with space distributed between them (Item 1 left, Item 3 right)
- Third row: Buttons with equal space around each
- Fourth section: Items stacked vertically with gaps between them
- Last row: Cards that grow equally to fill available space
- Flexbox is responsive and works on all screen sizes

### 144. What is `.justify-content-{value}`?
Controls flex main axis alignment:
- `.justify-content-start` - Left align
- `.justify-content-center` - Center
- `.justify-content-end` - Right align
- `.justify-content-between` - Space between
- `.justify-content-around` - Space around

### 145. What is `.align-items-{value}`?
Controls flex cross axis alignment:
- `.align-items-start` - Top align
- `.align-items-center` - Middle align
- `.align-items-end` - Bottom align

### 146. What is `.flex-wrap`?
`.flex-wrap` allows flex items to wrap to next line. `.flex-nowrap` prevents wrapping.

### 147. What is `.flex-direction`?
- `.flex-row` - Horizontal (default)
- `.flex-column` - Vertical
- `.flex-row-reverse` - Reverse horizontal
- `.flex-column-reverse` - Reverse vertical

### 148. What is `.gap-{size}` in flexbox?
`.gap-2` adds space between flex items (gutter).

### 149. What is `.position-{value}`?
Position utilities:
- `.position-static` - Default
- `.position-relative` - Relative positioning
- `.position-absolute` - Absolute positioning
- `.position-fixed` - Fixed positioning
- `.position-sticky` - Sticky positioning

### 150. What are positioning offset utilities?
- `.top-0`, `.bottom-0`, `.start-0`, `.end-0` - Position corners
- `.top-50` - 50% from top

### 151. What is `.overflow-{value}`?
- `.overflow-auto` - Auto scroll if needed
- `.overflow-hidden` - Hide overflow
- `.overflow-visible` - Show overflow
- `.overflow-scroll` - Always show scrollbars

### 152. What is `.float-{direction}`?
- `.float-start` - Float left
- `.float-end` - Float right
- `.float-none` - No float

### 153. What is `.clear-{direction}`?
- `.clear-start` - Clear left float
- `.clear-end` - Clear right float

### 154. What are sizing utilities?
- `.w-{value}` - Width (25, 50, 75, 100)
- `.h-{value}` - Height (25, 50, 75, 100)
- `.mw-100` - Max width 100%
- `.mh-100` - Max height 100%

### 155. What is `.min-vh-100`?
`.min-vh-100` sets minimum height to 100% of viewport height.

### 156. What are spacing utilities scale?
Bootstrap spacing scale uses:
- `0` = 0rem
- `1` = 0.25rem
- `2` = 0.5rem
- `3` = 1rem
- `4` = 1.5rem
- `5` = 3rem

### 157. What is `.text-decoration`?
- `.text-decoration-none` - Remove underline
- `.text-decoration-underline` - Add underline
- `.text-decoration-line-through` - Strikethrough

### 158. What is `.fw-{value}` for font weight?
- `.fw-light` - 300
- `.fw-normal` - 400
- `.fw-bold` - 700
- `.fw-bolder` - Bolder than parent

### 159. What is `.fs-{value}` for font size?
- `.fs-1` to `.fs-6` - Font sizes
- `.fs-base` - Base font size

### 160. What is `.lh-{value}` for line height?
- `.lh-1` - Line height 1
- `.lh-sm` - 1.25
- `.lh-base` - 1.5
- `.lh-lg` - 2

---

## Responsive Design

### 161. What are Bootstrap breakpoints?
- `xs` - < 576px
- `sm` - ≥ 576px
- `md` - ≥ 768px
- `lg` - ≥ 992px
- `xl` - ≥ 1200px
- `xxl` - ≥ 1400px

### 162. How do you use responsive utilities?
Add breakpoint to utility: `.d-none .d-md-block` (hidden on small, block on medium+).

### 163. What is `.d-none`?
`.d-none` sets display: none (hides element). Can use with breakpoints: `.d-md-none` (hide on md+).

### 164. What is `.d-block`?
`.d-block` sets display: block. Can use: `.d-md-block` (block on md+).

### 165. How do you hide elements on specific breakpoints?
```html
<!-- Hide on small screens -->
<div class="d-none d-md-block">Hidden on small</div>

<!-- Show only on medium screens -->
<div class="d-none d-md-block d-lg-none">Only on medium</div>
```

### 166. What is `.invisible`?
`.invisible` hides element but keeps its space (visibility: hidden). `.d-none` removes space.

### 167. How do you make images responsive?
```html
<img src="image.jpg" class="img-fluid" alt="Responsive image">
```

### 168. How do you make videos responsive?
```html
<div class="ratio ratio-16x9">
    <iframe src="https://www.youtube.com/embed/..." allowfullscreen></iframe>
</div>
```

### 169. What are common responsive ratios?
- `.ratio-1x1`
- `.ratio-4x3`
- `.ratio-16x9`
- `.ratio-21x9`

### 170. How do you create responsive tables?
```html
<div class="table-responsive">
    <table class="table">...</table>
</div>
```

### 171. How do you show/hide tables responsively?
Use `.table-{breakpoint}` to stack tables on smaller screens.

### 172. What is `.container-{breakpoint}`?
- `.container-sm` - Fixed width at sm breakpoint
- `.container-md` - Fixed width at md breakpoint
- `.container-lg` - Fixed width at lg breakpoint
- `.container-xl` - Fixed width at xl breakpoint

### 173. How do you create responsive text alignment?
```html
<p class="text-start text-md-center text-lg-end">
    Responsive text alignment
</p>
```

### 174. How do you create responsive display?
```html
<div class="d-block d-md-flex">
    Displays as block on small, flex on medium+
</div>
```

### 175. How do you create responsive margins?
```html
<div class="m-2 m-md-4 m-lg-5">
    Responsive margins
</div>
```

### 176. How do you create responsive padding?
```html
<div class="p-2 p-md-4 p-lg-5">
    Responsive padding
</div>
```

### 177. What is `.d-print-{value}`?
Print utilities: `.d-print-none` (hide on print), `.d-print-block` (block on print).

### 178. How do you make navbar collapse on small screens?
```html
<button class="navbar-toggler" data-bs-toggle="collapse" data-bs-target="#navbarNav">
    <span class="navbar-toggler-icon"></span>
</button>
<div class="collapse navbar-collapse" id="navbarNav">
    <!-- Nav items -->
</div>
```

### 179. How do you use CSS Grid with Bootstrap?
Combine Bootstrap grid with CSS Grid:
```html
<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr))">
    <div>Item 1</div>
    <div>Item 2</div>
</div>
```

### 180. How do you create responsive font sizes?
Use responsive text utilities: `.fs-1` to `.fs-6` (or custom CSS with media queries).

---

## JavaScript Components

### 181. Which Bootstrap components require JavaScript?
- Dropdowns
- Modals
- Tooltips
- Popovers
- Toasts
- Carousels
- Scrollspy
- Collapse
- Tabs
- Navbar toggle

### 182. How do you initialize Bootstrap JavaScript components?
```html
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
```

### 183. How do you use data attributes to activate components?
```html
<button data-bs-toggle="modal" data-bs-target="#myModal">
    Open Modal
</button>
```

### 184. How do you programmatically trigger a modal?
```javascript
const modal = new bootstrap.Modal(document.getElementById('myModal'));
modal.show();
modal.hide();
```

### 185. How do you listen to modal events?
```javascript
const myModal = document.getElementById('myModal');
myModal.addEventListener('shown.bs.modal', function() {
    console.log('Modal shown');
});
```

### 186. How do you programmatically show a toast?
```javascript
const toast = new bootstrap.Toast(document.getElementById('myToast'));
toast.show();
```

### 187. How do you initialize a tooltip?
```javascript
const tooltips = document.querySelectorAll('[data-bs-toggle="tooltip"]');
tooltips.forEach(tooltip => {
    new bootstrap.Tooltip(tooltip);
});
```

### 188. How do you initialize a popover?
```javascript
const popovers = document.querySelectorAll('[data-bs-toggle="popover"]');
popovers.forEach(popover => {
    new bootstrap.Popover(popover);
});
```

### 189. How do you control a carousel programmatically?
```javascript
const carousel = new bootstrap.Carousel(document.getElementById('myCarousel'));
carousel.next();
carousel.prev();
carousel.to(2);
```

### 190. How do you programmatically toggle collapse?
```javascript
const collapse = new bootstrap.Collapse(document.getElementById('myCollapse'));
collapse.toggle();
collapse.show();
collapse.hide();
```

### 191. How do you get dropdown instance?
```javascript
const dropdown = bootstrap.Dropdown.getInstance(document.getElementById('myDropdown'));
dropdown.show();
dropdown.hide();
```

### 192. What are Bootstrap component options?
Most components accept options object:
```javascript
new bootstrap.Modal(element, {
    backdrop: 'static',
    keyboard: false,
    focus: true
});
```

### 193. How do you destroy Bootstrap components?
```javascript
const modal = bootstrap.Modal.getInstance(element);
modal.dispose();
```

### 194. How do you check if component is shown?
```javascript
const modal = new bootstrap.Modal(element);
// Check by listening to events
element.addEventListener('shown.bs.modal', function() {
    // Modal is visible
});
```

### 195. How do you use Bootstrap with frameworks like React?
Use libraries like:
- `react-bootstrap` - React components
- `reactstrap` - React + Bootstrap 4
- Or use Bootstrap CSS with custom React components

### 196. How do you customize Bootstrap JavaScript behavior?
```javascript
// Change default modal options globally
bootstrap.Modal.DEFAULTS.backdrop = 'static';
bootstrap.Modal.DEFAULTS.keyboard = false;
```

### 197. What are Bootstrap plugins vs components?
- Plugins: Tooltip, Popover (jQuery plugins in Bootstrap 4)
- Components: Modal, Carousel (both have plugins and markup)

### 198. How do you add custom JavaScript to Bootstrap components?
```javascript
document.addEventListener('shown.bs.modal', function() {
    console.log('Any modal was shown');
}, true);
```

### 199. How do you prevent Bootstrap component default behavior?
```javascript
element.addEventListener('hide.bs.modal', function(e) {
    e.preventDefault();
    // Prevent modal from closing
});
```

### 200. How do you use Bootstrap with TypeScript?
Bootstrap 5 works with TypeScript. Components provide TypeScript definitions for IDE autocomplete and type checking.

---

## Summary

Bootstrap is a powerful CSS framework that provides:
- **12-column responsive grid system**
- **Pre-built UI components** (buttons, cards, modals, etc.)
- **Extensive utility classes** for spacing, colors, sizing
- **Mobile-first responsive design**
- **JavaScript components** for interactivity
- **Easy customization** via SCSS variables

**Key Takeaways:**
1. Always use viewport meta tag for responsive design
2. Use containers to wrap content
3. Grid system: 12 columns, responsive breakpoints
4. Components save development time
5. Utilities provide fine-grained control
6. JavaScript components require bootstrap.bundle.min.js
7. Mobile-first: start with small screens, enhance for larger

**Getting Started:**
1. Include Bootstrap CSS and JS
2. Use `.container` or `.container-fluid` as wrapper
3. Use `.row` and `.col` for layouts
4. Apply component classes for UI elements
5. Use utility classes for spacing and alignment
6. Customize with CSS variables or SCSS
