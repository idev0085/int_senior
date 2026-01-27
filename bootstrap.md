# Bootstrap Framework - 200 Basic Level Questions & Answers

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

### 29. What is the .row-cols class?
`.row-cols-{number}` sets the number of columns in a row. Example: `.row-cols-3` creates 3 equal-width columns.

### 30. How do you create unequal column widths?
```html
<div class="row">
    <div class="col-md-8">Wide column</div>
    <div class="col-md-4">Narrow column</div>
</div>
```

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

### 90. How do you create an alert in Bootstrap?
```html
<div class="alert alert-primary">Alert message</div>
<div class="alert alert-danger alert-dismissible fade show">
    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
    Message
</div>
```

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

### 100. How do you create a progress bar in Bootstrap?
```html
<div class="progress">
    <div class="progress-bar" role="progressbar" style="width: 25%"></div>
</div>
```

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
