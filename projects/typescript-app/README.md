# TypeScript Todo App

This project builds on the basic Todo App by refactoring it with TypeScript. It's designed to demonstrate how TypeScript enhances JavaScript applications with static typing and modern features.

## Project Goals

- Refactor the JavaScript Todo App using TypeScript
- Learn TypeScript fundamentals and type systems
- Implement type-safe programming practices
- Understand interfaces and type declarations
- Use TypeScript with DOM manipulation

## Features

- All features from the basic Todo App
- Type-safe code with TypeScript
- Class-based architecture
- Enhanced data models with interfaces
- Improved code organization using modules
- Type checking for DOM elements
- More robust error handling

## Technology Stack

- TypeScript
- HTML5
- CSS3
- Local Storage API
- Webpack (for bundling)

## Project Structure

```
typescript-app/
├── src/                   # Source code
│   ├── index.ts           # Entry point
│   ├── models/            # Data models
│   │   └── todo.ts        # Todo interface and types
│   ├── services/          # Services
│   │   └── storage.ts     # Storage service
│   ├── components/        # UI components
│   │   ├── app.ts         # Main app class
│   │   ├── todoList.ts    # Todo list component
│   │   └── todoItem.ts    # Todo item component
│   └── utils/             # Utility functions
│       └── domUtils.ts    # DOM helper functions
├── public/                # Public assets
│   ├── index.html         # HTML template
│   └── styles/            # CSS styles
│       └── style.css      # Main stylesheet
├── dist/                  # Built files (generated)
├── tsconfig.json          # TypeScript configuration
├── package.json           # Project dependencies
└── webpack.config.js      # Webpack configuration
```

## Implementation Steps

### 1. Setting Up the Environment

- Initialize a new TypeScript project
- Install required dependencies:
  ```bash
  npm init -y
  npm install --save-dev typescript webpack webpack-cli ts-loader
  npm install --save-dev @types/node
  ```
- Create a `tsconfig.json` file:
  ```json
  {
    "compilerOptions": {
      "target": "es6",
      "module": "es2015",
      "moduleResolution": "node",
      "strict": true,
      "sourceMap": true,
      "outDir": "./dist",
      "lib": ["dom", "es2015", "es2016", "es2017"]
    },
    "include": ["./src/**/*"]
  }
  ```
- Set up webpack configuration

### 2. Creating Type Definitions

- Define the Todo interface:
  ```typescript
  // models/todo.ts
  export interface Todo {
    id: number;
    text: string;
    completed: boolean;
    createdAt: Date;
  }

  export type TodoFilter = 'all' | 'active' | 'completed';
  ```

- Create a StorageService interface:
  ```typescript
  // services/storage.ts
  import { Todo } from '../models/todo';

  export interface StorageService {
    getTodos(): Todo[];
    saveTodos(todos: Todo[]): void;
    clearTodos(): void;
  }

  export class LocalStorageService implements StorageService {
    private storageKey: string = 'todos';

    getTodos(): Todo[] {
      const storedTodos = localStorage.getItem(this.storageKey);
      if (storedTodos) {
        return JSON.parse(storedTodos).map((todo: any) => ({
          ...todo,
          createdAt: new Date(todo.createdAt)
        }));
      }
      return [];
    }

    saveTodos(todos: Todo[]): void {
      localStorage.setItem(this.storageKey, JSON.stringify(todos));
    }

    clearTodos(): void {
      localStorage.removeItem(this.storageKey);
    }
  }
  ```

### 3. Building Components with TypeScript

- Create a main App class:
  ```typescript
  // components/app.ts
  import { Todo, TodoFilter } from '../models/todo';
  import { LocalStorageService } from '../services/storage';
  import { TodoList } from './todoList';

  export class App {
    private todos: Todo[] = [];
    private currentFilter: TodoFilter = 'all';
    private storageService: LocalStorageService;
    private todoList: TodoList;
    
    // DOM elements
    private todoForm: HTMLFormElement;
    private todoInput: HTMLInputElement;
    private filterButtons: NodeListOf<HTMLButtonElement>;
    private clearCompletedBtn: HTMLButtonElement;
    
    constructor() {
      this.storageService = new LocalStorageService();
      
      // Get DOM elements
      this.todoForm = document.getElementById('todo-form') as HTMLFormElement;
      this.todoInput = document.getElementById('todo-input') as HTMLInputElement;
      this.filterButtons = document.querySelectorAll('.filter') as NodeListOf<HTMLButtonElement>;
      this.clearCompletedBtn = document.getElementById('clear-completed') as HTMLButtonElement;
      
      // Initialize TodoList component
      const todoListElement = document.getElementById('todo-list') as HTMLUListElement;
      this.todoList = new TodoList(todoListElement, this.toggleTodo.bind(this), this.deleteTodo.bind(this));
      
      // Load todos and set up event listeners
      this.loadTodos();
      this.setupEventListeners();
    }
    
    private loadTodos(): void {
      this.todos = this.storageService.getTodos();
      this.renderTodos();
    }
    
    private renderTodos(): void {
      const filteredTodos = this.todos.filter(todo => {
        if (this.currentFilter === 'active') return !todo.completed;
        if (this.currentFilter === 'completed') return todo.completed;
        return true;
      });
      
      this.todoList.render(filteredTodos);
      this.updateItemsLeft();
    }
    
    private addTodo(text: string): void {
      const newTodo: Todo = {
        id: Date.now(),
        text,
        completed: false,
        createdAt: new Date()
      };
      
      this.todos.push(newTodo);
      this.storageService.saveTodos(this.todos);
      this.renderTodos();
    }
    
    private toggleTodo(id: number): void {
      this.todos = this.todos.map(todo => {
        if (todo.id === id) {
          return { ...todo, completed: !todo.completed };
        }
        return todo;
      });
      
      this.storageService.saveTodos(this.todos);
      this.renderTodos();
    }
    
    private deleteTodo(id: number): void {
      this.todos = this.todos.filter(todo => todo.id !== id);
      this.storageService.saveTodos(this.todos);
      this.renderTodos();
    }
    
    private updateItemsLeft(): void {
      const activeCount = this.todos.filter(todo => !todo.completed).length;
      const itemsLeftSpan = document.getElementById('items-left') as HTMLSpanElement;
      itemsLeftSpan.textContent = `${activeCount} item${activeCount !== 1 ? 's' : ''} left`;
    }
    
    private clearCompleted(): void {
      this.todos = this.todos.filter(todo => !todo.completed);
      this.storageService.saveTodos(this.todos);
      this.renderTodos();
    }
    
    private setupEventListeners(): void {
      this.todoForm.addEventListener('submit', (e: Event) => {
        e.preventDefault();
        const text = this.todoInput.value.trim();
        if (text) {
          this.addTodo(text);
          this.todoInput.value = '';
        }
      });
      
      this.filterButtons.forEach(button => {
        button.addEventListener('click', () => {
          this.filterButtons.forEach(btn => btn.classList.remove('active'));
          button.classList.add('active');
          this.currentFilter = button.dataset.filter as TodoFilter;
          this.renderTodos();
        });
      });
      
      this.clearCompletedBtn.addEventListener('click', () => {
        this.clearCompleted();
      });
    }
  }
  ```

### 4. Compile and Build

- Create a build script in package.json:
  ```json
  "scripts": {
    "build": "webpack --mode production",
    "start": "webpack serve --mode development --open"
  }
  ```
- Run the build command:
  ```bash
  npm run build
  ```

## Learning Objectives

- TypeScript syntax and features
- Interfaces and custom types
- Class-based programming in TypeScript
- Type safety and static typing
- Module systems in TypeScript
- DOM manipulation with TypeScript
- Compiling TypeScript to JavaScript

## Enhanced Features (Optional)

- **State Management**: Implement a more robust state management pattern
- **Unit Testing**: Add Jest tests for TypeScript components
- **Custom Type Guards**: Create custom type guards for advanced type checking
- **Generics**: Use TypeScript generics for reusable components
- **Advanced TypeScript Features**: Utilize utility types, mapped types, and conditional types

## Next Steps

After completing this project, you can:
- Expand it to use a framework like React with TypeScript
- Create a backend API with Node.js and TypeScript
- Explore advanced TypeScript features like decorators
- Add more complex data structures and algorithms
- Implement a state management library like Redux with TypeScript 