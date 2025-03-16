# Todo App Project

This project guides you through building a basic todo list application using HTML, CSS, and JavaScript. It's a perfect starting point for learning frontend fundamentals.

## Project Goals

- Create a functional todo list application
- Learn DOM manipulation with JavaScript
- Implement local storage for data persistence
- Practice CSS styling and responsive design
- Build a foundation for more complex applications

## Features

- Add new tasks to the todo list
- Mark tasks as completed
- Delete tasks from the list
- Filter tasks (All, Active, Completed)
- Clear all completed tasks
- Store tasks in local storage for persistence
- Responsive design for mobile and desktop
- Drag and drop for task reordering (optional enhancement)

## Technology Stack

- HTML5
- CSS3
- JavaScript (ES6+)
- Local Storage API

## Project Structure

```
todo-app/
├── index.html            # Main HTML file
├── css/                  # CSS styles
│   └── style.css         # Main stylesheet
├── js/                   # JavaScript functionality
│   ├── app.js            # Main application logic
│   └── storage.js        # Local storage handling
└── assets/               # Icons and images
    └── icons/            # SVG icons
```

## Implementation Steps

### 1. HTML Structure Setup
- Create the basic HTML structure
- Add form for task input
- Create task list container
- Add filter buttons
- Include necessary meta tags

### 2. Styling with CSS
- Style the container and form
- Design task items with completion toggles
- Create hover and active states
- Implement responsive design
- Style filter buttons and states

### 3. JavaScript Functionality
- Implement task addition
- Enable task completion toggling
- Add task deletion functionality
- Implement filtering logic
- Create clear completed function
- Set up local storage persistence

### 4. Testing and Refinement
- Test all functionality
- Check browser compatibility
- Test responsive design
- Optimize performance
- Fix any bugs

## Code Examples

### HTML Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Todo App</title>
  <link rel="stylesheet" href="css/style.css">
</head>
<body>
  <div class="container">
    <h1>Todo App</h1>
    <form id="todo-form">
      <input type="text" id="todo-input" placeholder="Add a new task...">
      <button type="submit">Add</button>
    </form>
    <div class="filters">
      <button class="filter active" data-filter="all">All</button>
      <button class="filter" data-filter="active">Active</button>
      <button class="filter" data-filter="completed">Completed</button>
    </div>
    <ul id="todo-list">
      <!-- Todo items will be added here -->
    </ul>
    <div class="actions">
      <span id="items-left">0 items left</span>
      <button id="clear-completed">Clear Completed</button>
    </div>
  </div>
  <script src="js/storage.js"></script>
  <script src="js/app.js"></script>
</body>
</html>
```

### JavaScript Core Logic

```javascript
// app.js example

// DOM Elements
const todoForm = document.getElementById('todo-form');
const todoInput = document.getElementById('todo-input');
const todoList = document.getElementById('todo-list');
const filters = document.querySelectorAll('.filter');
const itemsLeftSpan = document.getElementById('items-left');
const clearCompletedBtn = document.getElementById('clear-completed');

// State
let todos = [];
let currentFilter = 'all';

// Load todos from local storage
function loadTodos() {
  todos = getTodosFromStorage();
  renderTodos();
}

// Render todos based on current filter
function renderTodos() {
  todoList.innerHTML = '';
  
  const filteredTodos = todos.filter(todo => {
    if (currentFilter === 'active') return !todo.completed;
    if (currentFilter === 'completed') return todo.completed;
    return true;
  });
  
  filteredTodos.forEach(todo => {
    const li = document.createElement('li');
    li.className = todo.completed ? 'todo-item completed' : 'todo-item';
    li.innerHTML = `
      <input type="checkbox" ${todo.completed ? 'checked' : ''}>
      <span class="todo-text">${todo.text}</span>
      <button class="delete-btn">×</button>
    `;
    
    // Event listeners for the todo item
    li.querySelector('input').addEventListener('change', () => {
      toggleTodo(todo.id);
    });
    
    li.querySelector('.delete-btn').addEventListener('click', () => {
      deleteTodo(todo.id);
    });
    
    todoList.appendChild(li);
  });
  
  updateItemsLeft();
}

// Add a new todo
function addTodo(text) {
  const newTodo = {
    id: Date.now(),
    text,
    completed: false
  };
  
  todos.push(newTodo);
  saveTodosToStorage(todos);
  renderTodos();
}

// Toggle todo completion status
function toggleTodo(id) {
  todos = todos.map(todo => {
    if (todo.id === id) {
      return { ...todo, completed: !todo.completed };
    }
    return todo;
  });
  
  saveTodosToStorage(todos);
  renderTodos();
}

// Delete a todo
function deleteTodo(id) {
  todos = todos.filter(todo => todo.id !== id);
  saveTodosToStorage(todos);
  renderTodos();
}

// Update items left count
function updateItemsLeft() {
  const activeCount = todos.filter(todo => !todo.completed).length;
  itemsLeftSpan.textContent = `${activeCount} item${activeCount !== 1 ? 's' : ''} left`;
}

// Clear all completed todos
function clearCompleted() {
  todos = todos.filter(todo => !todo.completed);
  saveTodosToStorage(todos);
  renderTodos();
}

// Event Listeners
todoForm.addEventListener('submit', (e) => {
  e.preventDefault();
  const text = todoInput.value.trim();
  if (text) {
    addTodo(text);
    todoInput.value = '';
  }
});

filters.forEach(filter => {
  filter.addEventListener('click', () => {
    filters.forEach(f => f.classList.remove('active'));
    filter.classList.add('active');
    currentFilter = filter.dataset.filter;
    renderTodos();
  });
});

clearCompletedBtn.addEventListener('click', clearCompleted);

// Initialize app
document.addEventListener('DOMContentLoaded', loadTodos);
```

## Enhanced Features (Optional)

- **Drag and Drop**: Use the HTML5 Drag and Drop API to reorder tasks
- **Due Dates**: Add due dates to tasks with date picking
- **Categories/Tags**: Allow categorizing or tagging tasks
- **Dark Mode**: Add a light/dark theme toggle
- **Task Details**: Expand tasks to add notes or subtasks
- **Search**: Add a search function to find tasks
- **Animations**: Add CSS transitions and animations

## Learning Objectives

- DOM manipulation with JavaScript
- Event handling
- Local Storage usage
- CSS layout and styling techniques
- Working with forms and user input
- State management in JavaScript
- Filter and array operations

## Next Steps

After completing this project, you can:
- Refactor it using a framework like React, Vue, or Angular
- Add a backend to store tasks server-side
- Implement user authentication for personal todo lists
- Expand to a more complex project management tool
- Add more advanced features like recurring tasks or priorities 