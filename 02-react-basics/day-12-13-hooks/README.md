# Days 12-13: React Hooks

## Learning Objectives

By the end of this section, you will:

- Understand the purpose and benefits of React Hooks
- Use the core hooks: useState, useEffect, useContext, useRef
- Create custom hooks to encapsulate reusable logic
- Implement complex state management with useReducer
- Apply hooks in practical React applications
- Optimize performance with useMemo and useCallback

## Introduction to React Hooks

React Hooks were introduced in React 16.8 to allow functional components to use state and other React features that were previously only available in class components. Hooks provide a more direct API to React concepts and enable better code reuse and composition.

## Core Hooks

### useState

The `useState` hook lets you add state to functional components.

```jsx
import React, { useState } from 'react';

function Counter() {
  // Declare a state variable "count" with initial value 0
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

#### Key points:
- The `useState` hook returns an array with two elements: the current state value and a function to update it
- The update function replaces the state (not merging like `this.setState`)
- You can use multiple `useState` calls in a single component
- The initial state is only used on the first render

### useEffect

The `useEffect` hook lets you perform side effects in functional components.

```jsx
import React, { useState, useEffect } from 'react';

function DocumentTitleUpdater() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  }, [count]); // Only re-run if count changes

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

#### Key points:
- `useEffect` runs after every render by default
- The dependency array `[count]` means the effect only runs when `count` changes
- An empty dependency array `[]` means the effect only runs once (on mount)
- Return a cleanup function to handle component unmount or before re-running the effect

```jsx
useEffect(() => {
  const subscription = someExternalAPI.subscribe();
  
  // Cleanup function
  return () => {
    subscription.unsubscribe();
  };
}, []);
```

### useContext

The `useContext` hook provides a way to pass data through the component tree without prop drilling.

```jsx
import React, { createContext, useContext, useState } from 'react';

// Create a context with a default value
const ThemeContext = createContext('light');

function App() {
  const [theme, setTheme] = useState('light');
  
  return (
    <ThemeContext.Provider value={theme}>
      <div>
        <ThemedButton onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')} />
      </div>
    </ThemeContext.Provider>
  );
}

function ThemedButton({ onClick }) {
  // Get the value from context
  const theme = useContext(ThemeContext);
  
  return (
    <button 
      onClick={onClick}
      style={{ background: theme === 'light' ? '#fff' : '#333', 
               color: theme === 'light' ? '#333' : '#fff' }}
    >
      Toggle Theme
    </button>
  );
}
```

### useRef

The `useRef` hook provides a way to access DOM elements and persist values across renders without causing re-renders.

```jsx
import React, { useRef, useEffect } from 'react';

function FocusInput() {
  // Create a ref
  const inputRef = useRef(null);
  
  // Focus the input on mount
  useEffect(() => {
    inputRef.current.focus();
  }, []);
  
  return (
    <input ref={inputRef} type="text" />
  );
}
```

#### Common uses:
- Accessing DOM elements
- Keeping mutable values that don't trigger re-renders
- Storing previous values

## Advanced Hooks

### useReducer

The `useReducer` hook is an alternative to `useState` for complex state logic:

```jsx
import React, { useReducer } from 'react';

// Reducer function
function counterReducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return { count: 0 };
    default:
      throw new Error(`Unsupported action type: ${action.type}`);
  }
}

function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });
  
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
}
```

### useMemo

The `useMemo` hook memoizes expensive calculations to optimize performance:

```jsx
import React, { useState, useMemo } from 'react';

function ExpensiveCalculation({ list }) {
  const [filter, setFilter] = useState('');
  
  // Only recalculate when list or filter changes
  const filteredList = useMemo(() => {
    console.log('Filtering list...');
    return list.filter(item => item.includes(filter));
  }, [list, filter]);
  
  return (
    <div>
      <input 
        value={filter}
        onChange={e => setFilter(e.target.value)}
        placeholder="Filter items..."
      />
      <ul>
        {filteredList.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

### useCallback

The `useCallback` hook returns a memoized callback function:

```jsx
import React, { useState, useCallback } from 'react';

function ParentComponent() {
  const [count, setCount] = useState(0);
  
  // This function only changes when count changes
  const handleClick = useCallback(() => {
    console.log(`Button clicked, count: ${count}`);
  }, [count]);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <ChildComponent onClick={handleClick} />
    </div>
  );
}

// This component only re-renders when props change
const ChildComponent = React.memo(({ onClick }) => {
  console.log('ChildComponent rendered');
  return <button onClick={onClick}>Click me</button>;
});
```

## Custom Hooks

Custom hooks let you extract component logic into reusable functions:

```jsx
import { useState, useEffect } from 'react';

// Custom hook for fetching data
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchData() {
      try {
        setLoading(true);
        const response = await fetch(url);
        
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        
        const json = await response.json();
        setData(json);
        setError(null);
      } catch (err) {
        setError(err.message);
        setData(null);
      } finally {
        setLoading(false);
      }
    }

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Using the custom hook
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`https://api.example.com/users/${userId}`);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

## Rules of Hooks

1. **Only call hooks at the top level** of your component, not inside loops, conditions, or nested functions
2. **Only call hooks from React function components** or custom hooks, not regular JavaScript functions
3. **Hooks names should start with "use"** followed by a capital letter
4. **Be consistent with the order of hooks** across renders

## Practical Exercises

See the [exercises.md](exercises.md) file for interactive exercises focused on hooks.

## Mini-Project: Weather Dashboard

Create a weather dashboard application that:

1. Fetches weather data from a public API
2. Allows users to search for different cities
3. Displays current weather and 5-day forecast
4. Saves search history using local storage
5. Implements a theme toggle (light/dark mode)

Requirements:
- Use `useState` for local component state
- Use `useEffect` for API calls and side effects
- Use `useContext` for theme management
- Create at least one custom hook (e.g., `useFetch` or `useLocalStorage`)
- Implement error handling and loading states

## Additional Resources

- [React Hooks Documentation](https://reactjs.org/docs/hooks-intro.html)
- [Thinking in React Hooks](https://wattenberger.com/blog/react-hooks)
- [Collection of React Hooks](https://github.com/rehooks/awesome-react-hooks)
- [useHooks](https://usehooks.com/) - Easy to understand custom hook recipes
- [React Hook Form](https://react-hook-form.com/) - Performant form validation library 