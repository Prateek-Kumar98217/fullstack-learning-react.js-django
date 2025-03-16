# React Hooks Interactive Exercises

## 1. State Management Challenge

### Exercise: Shopping Cart
Build a shopping cart with the following features:
```typescript
// components/ShoppingCart.tsx
import { useState } from 'react'

interface Product {
  id: number
  name: string
  price: number
  quantity: number
}

export function ShoppingCart() {
  const [cart, setCart] = useState<Product[]>([])
  
  // TODO: Implement these functions
  const addToCart = (product: Product) => {
    // Add or update quantity
  }
  
  const removeFromCart = (productId: number) => {
    // Remove item from cart
  }
  
  const updateQuantity = (productId: number, quantity: number) => {
    // Update item quantity
  }
  
  return (
    <div>
      {/* Implement the UI */}
    </div>
  )
}
```

## 2. Effect Hooks Workshop

### Exercise: Data Fetching and Filtering
```typescript
// components/UserDirectory.tsx
import { useState, useEffect } from 'react'

interface User {
  id: number
  name: string
  email: string
  role: string
}

export function UserDirectory() {
  const [users, setUsers] = useState<User[]>([])
  const [filter, setFilter] = useState('')
  
  // TODO: Implement data fetching
  useEffect(() => {
    // Fetch users from API
  }, [])
  
  // TODO: Implement filtering
  const filteredUsers = users.filter(user => {
    // Filter users based on name or role
  })
  
  return (
    <div>
      {/* Implement search and display */}
    </div>
  )
}
```

## 3. Custom Hooks Lab

### Exercise: Form Management Hook
```typescript
// hooks/useForm.ts
import { useState } from 'react'

interface FormConfig<T> {
  initialValues: T
  validate?: (values: T) => Partial<Record<keyof T, string>>
  onSubmit: (values: T) => void | Promise<void>
}

export function useForm<T extends Record<string, any>>({
  initialValues,
  validate,
  onSubmit
}: FormConfig<T>) {
  // TODO: Implement form management logic
  
  return {
    // Return values, errors, handlers
  }
}

// Example usage:
function SignupForm() {
  const { values, errors, handleChange, handleSubmit } = useForm({
    initialValues: {
      username: '',
      email: '',
      password: ''
    },
    validate: (values) => {
      // Implement validation
    },
    onSubmit: async (values) => {
      // Handle form submission
    }
  })
  
  return (
    <form onSubmit={handleSubmit}>
      {/* Implement form fields */}
    </form>
  )
}
```

## 4. Context API Challenge

### Exercise: Theme Switcher
```typescript
// context/ThemeContext.tsx
import { createContext, useContext, useState } from 'react'

interface Theme {
  primary: string
  background: string
  text: string
}

const themes = {
  light: {
    primary: '#007bff',
    background: '#ffffff',
    text: '#000000'
  },
  dark: {
    primary: '#0056b3',
    background: '#1a1a1a',
    text: '#ffffff'
  }
}

// TODO: Implement theme context and provider

// Example usage:
function ThemedButton() {
  // TODO: Use theme context
  return (
    <button style={/* Apply theme styles */}>
      Themed Button
    </button>
  )
}
```

## 5. Performance Optimization

### Exercise: Memoization Workshop
```typescript
// components/ExpensiveList.tsx
import { useState, useMemo, useCallback } from 'react'

interface Item {
  id: number
  name: string
  category: string
}

export function ExpensiveList() {
  const [items, setItems] = useState<Item[]>([])
  const [filter, setFilter] = useState('')
  
  // TODO: Implement memoized filtering
  const filteredItems = useMemo(() => {
    // Filter and sort items
  }, [/* Add dependencies */])
  
  // TODO: Implement memoized event handler
  const handleItemClick = useCallback((id: number) => {
    // Handle item selection
  }, [/* Add dependencies */])
  
  return (
    <div>
      {/* Implement list rendering */}
    </div>
  )
}
```

## 6. Integration Challenge

### Exercise: Real-time Data Dashboard
Combine multiple hooks to create a dashboard that:
- Fetches data from multiple sources
- Updates in real-time
- Manages complex state
- Handles errors gracefully
- Implements caching

```typescript
// components/Dashboard.tsx
import { useState, useEffect, useCallback, useRef } from 'react'

interface DashboardData {
  stats: any
  alerts: any[]
  updates: any[]
}

export function Dashboard() {
  // TODO: Implement real-time dashboard
  // Use multiple hooks to manage different aspects
  // Implement WebSocket connection
  // Handle data updates
  // Manage error states
  // Implement cleanup
  
  return (
    <div>
      {/* Implement dashboard UI */}
    </div>
  )
}
```

## Completion Checklist

For each exercise:
1. ⬜ Implement the basic functionality
2. ⬜ Add error handling
3. ⬜ Implement loading states
4. ⬜ Add TypeScript types
5. ⬜ Write unit tests
6. ⬜ Optimize performance
7. ⬜ Add documentation

## Bonus Challenges

1. **State Machine**
   - Implement a complex form wizard using state machines
   - Handle multiple form steps
   - Manage validation between steps
   - Persist state between sessions

2. **Custom Hook Library**
   - Create a collection of reusable hooks
   - Document usage patterns
   - Write comprehensive tests
   - Create example components

3. **Advanced Context**
   - Implement nested contexts
   - Handle context updates efficiently
   - Create a context selector hook
   - Optimize re-renders

## Resources

- [React Hooks Documentation](https://reactjs.org/docs/hooks-intro.html)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/react.html)
- [Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
- [React Performance](https://reactjs.org/docs/optimizing-performance.html) 