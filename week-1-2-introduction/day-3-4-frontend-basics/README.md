# Days 3-4: Frontend Development Basics

## Learning Objectives
By the end of these two days, you will:
- Master essential HTML5 elements and semantic markup
- Understand CSS layouts, flexbox, and grid
- Learn modern JavaScript (ES6+) features
- Build responsive web layouts
- Handle DOM manipulation and events

## Best Practices & Common Pitfalls

### HTML Best Practices
```html
<!-- ❌ Bad Practice -->
<div class="header">
  <div class="nav">
    <div class="nav-item">Home</div>
  </div>
</div>

<!-- ✅ Good Practice: Use semantic elements -->
<header>
  <nav>
    <ul>
      <li><a href="#home">Home</a></li>
    </ul>
  </nav>
</header>

<!-- ✅ Always include proper meta tags -->
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Your Page Title</title>
</head>

<!-- ✅ Use proper ARIA labels for accessibility -->
<button aria-label="Close menu" class="close-button">
  <span class="icon">×</span>
</button>
```

### CSS Best Practices
```css
/* ❌ Bad Practice: Hard-coded values */
.container {
  width: 1200px;
}

/* ✅ Good Practice: Responsive units */
.container {
  width: 90%;
  max-width: 1200px;
  margin: 0 auto;
}

/* ✅ Use CSS Custom Properties for maintainability */
:root {
  --primary-color: #007bff;
  --spacing-unit: 1rem;
  --border-radius: 4px;
}

.button {
  background-color: var(--primary-color);
  padding: var(--spacing-unit);
  border-radius: var(--border-radius);
}

/* ✅ Mobile-first media queries */
.card {
  width: 100%;  /* Mobile default */
}

@media (min-width: 768px) {
  .card {
    width: 50%;  /* Tablet */
  }
}

@media (min-width: 1024px) {
  .card {
    width: 33.333%;  /* Desktop */
  }
}
```

### JavaScript Best Practices
```javascript
// ❌ Bad Practice: Global variables
let data = [];

// ✅ Good Practice: Module pattern
const DataModule = {
  data: [],
  add(item) {
    this.data.push(item);
  },
  get() {
    return [...this.data];  // Return copy to prevent mutation
  }
};

// ❌ Bad Practice: Direct DOM manipulation in loops
items.forEach(item => {
  document.body.innerHTML += `<div>${item}</div>`;
});

// ✅ Good Practice: Document fragment
const fragment = document.createDocumentFragment();
items.forEach(item => {
  const div = document.createElement('div');
  div.textContent = item;
  fragment.appendChild(div);
});
document.body.appendChild(fragment);

// ✅ Good Practice: Error handling
async function fetchData() {
  try {
    const response = await fetch('api/data');
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Could not fetch data:', error);
    // Handle error appropriately
    throw error;
  }
}
```

## Accessibility Considerations

### Semantic HTML
- Use proper heading hierarchy (h1-h6)
- Use `<button>` for clickable elements
- Use `<nav>` for navigation
- Use `<main>` for main content
- Use `<article>` for independent content

### ARIA Roles and Labels
```html
<!-- Navigation landmark -->
<nav role="navigation" aria-label="Main menu">
  <!-- Menu items -->
</nav>

<!-- Form inputs -->
<label for="name">Name:</label>
<input 
  type="text" 
  id="name" 
  name="name" 
  aria-required="true"
  aria-describedby="name-help"
>
<span id="name-help">Enter your full name</span>
```

### Color Contrast
```css
/* Ensure sufficient color contrast */
:root {
  --text-color: #333;      /* Dark gray for text */
  --bg-color: #fff;        /* White background */
  --link-color: #0066cc;   /* Accessible blue */
}

/* Provide focus indicators */
:focus {
  outline: 3px solid #0066cc;
  outline-offset: 2px;
}
```

## 1. HTML5 Fundamentals

### Semantic HTML
```html
<!-- Example of semantic HTML structure -->
<header>
  <nav>
    <ul>
      <li><a href="#home">Home</a></li>
      <li><a href="#about">About</a></li>
    </ul>
  </nav>
</header>
<main>
  <article>
    <section>
      <h1>Main Content</h1>
      <p>Your content here...</p>
    </section>
  </article>
  <aside>
    <h2>Sidebar</h2>
  </aside>
</main>
<footer>
  <p>&copy; 2024</p>
</footer>
```

### Important HTML5 Elements
- `<header>`, `<nav>`, `<main>`, `<article>`, `<section>`, `<aside>`, `<footer>`
- `<figure>`, `<figcaption>`
- `<time>`, `<mark>`, `<details>`, `<summary>`
- Form elements: `<input>`, `<select>`, `<textarea>`, `<button>`

## 2. CSS Fundamentals

### Box Model
```css
.box {
  margin: 10px;
  border: 1px solid #000;
  padding: 15px;
  width: 200px;
  box-sizing: border-box;
}
```

### Flexbox
```css
.container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
}

.item {
  flex: 1;
  margin: 10px;
}
```

### CSS Grid
```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
  padding: 20px;
}
```

### Responsive Design
```css
/* Mobile First Approach */
.container {
  width: 100%;
  padding: 10px;
}

/* Tablet */
@media (min-width: 768px) {
  .container {
    width: 750px;
    margin: 0 auto;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    width: 960px;
  }
}
```

## 3. Modern JavaScript (ES6+)

### Variables and Scope
```javascript
// let and const
let count = 0;  // Mutable variable
const API_KEY = 'xyz123';  // Immutable constant

// Block scope
{
  let blockScoped = 'only available in this block';
  const alsoBlockScoped = 'same here';
}
```

### Arrow Functions
```javascript
// Traditional function
function add(a, b) {
  return a + b;
}

// Arrow function
const add = (a, b) => a + b;

// Arrow function with block
const multiply = (a, b) => {
  const result = a * b;
  return result;
};
```

### Template Literals
```javascript
const name = 'World';
const greeting = `Hello, ${name}!`;
```

### Destructuring
```javascript
// Object destructuring
const person = { name: 'John', age: 30 };
const { name, age } = person;

// Array destructuring
const numbers = [1, 2, 3];
const [first, second] = numbers;
```

### Spread/Rest Operator
```javascript
// Spread operator
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5];  // [1, 2, 3, 4, 5]

// Rest parameters
const sum = (...numbers) => numbers.reduce((a, b) => a + b, 0);
```

### Promises and Async/Await
```javascript
// Promise
const fetchData = () => {
  return new Promise((resolve, reject) => {
    // Async operation
    setTimeout(() => {
      resolve('Data fetched!');
    }, 1000);
  });
};

// Async/Await
const getData = async () => {
  try {
    const result = await fetchData();
    console.log(result);
  } catch (error) {
    console.error(error);
  }
};
```

## 4. DOM Manipulation

### Selecting Elements
```javascript
// Modern selectors
const element = document.querySelector('.class-name');
const elements = document.querySelectorAll('.class-name');

// Event handling
element.addEventListener('click', (e) => {
  console.log('Clicked!', e.target);
});
```

### Creating and Modifying Elements
```javascript
// Creating elements
const div = document.createElement('div');
div.textContent = 'New Element';
div.classList.add('new-class');

// Modifying elements
const parent = document.querySelector('.parent');
parent.appendChild(div);
```

## 5. Exercises

1. Build a responsive navigation menu using HTML and CSS
2. Create a grid layout of cards that responds to different screen sizes
3. Implement a form with validation using JavaScript
4. Build a simple todo list using DOM manipulation
5. Create a fetch request to a public API and display the results

## 6. Mini Project: Portfolio Page

Create a simple portfolio page that includes:
- Responsive navigation
- Hero section with your information
- Skills section using CSS Grid
- Project showcase using Flexbox
- Contact form with validation

## Resources

- [MDN Web Docs](https://developer.mozilla.org/)
- [CSS-Tricks](https://css-tricks.com/)
- [JavaScript.info](https://javascript.info/)
- [Can I Use](https://caniuse.com/)

## Next Steps
In the next section, we'll learn about Git and version control, which will help us manage our code as we build more complex applications. 