# Transitioning from React to Next.js

This guide will help you understand the key differences between React and Next.js, and how to adapt your existing React knowledge to Next.js development.

## 1. Key Differences

### React vs Next.js
| Feature | React | Next.js |
|---------|--------|---------|
| Rendering | Client-side only | Server-side, Static, and Client-side |
| Routing | Manual (React Router) | File-based routing |
| Data Fetching | useEffect + fetch | Server Components + fetch |
| Build Process | Manual configuration | Built-in optimization |
| API Routes | Separate backend needed | Integrated API routes |

## 2. Converting React Patterns to Next.js

### Component Structure
```typescript
// React Component
import { useState, useEffect } from 'react'

function Posts() {
  const [posts, setPosts] = useState([])
  
  useEffect(() => {
    fetch('/api/posts')
      .then(res => res.json())
      .then(setPosts)
  }, [])
  
  return <div>{/* render posts */}</div>
}

// Next.js Server Component
async function Posts() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json())
  return <div>{/* render posts */}</div>
}
```

### Routing
```typescript
// React Router
import { BrowserRouter, Routes, Route } from 'react-router-dom'

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  )
}

// Next.js App Router
app/
├── page.tsx           // Home route (/)
└── about/
    └── page.tsx      // About route (/about)
```

### Data Fetching
```typescript
// React Query/SWR in React
import { useQuery } from 'react-query'

function Dashboard() {
  const { data, isLoading } = useQuery('dashboard', fetchDashboardData)
  if (isLoading) return <Loading />
  return <div>{/* render data */}</div>
}

// Next.js Server Component
async function Dashboard() {
  const data = await fetchDashboardData()
  return <div>{/* render data */}</div>
}
```

## 3. Mental Model Shifts

1. **Server-First Thinking**
   - Default to server components
   - Move data fetching to the server
   - Use client components only when needed

2. **File-Based Organization**
   - Organize by routes instead of features
   - Co-locate related components
   - Use special files (layout.tsx, loading.tsx)

3. **Data Flow**
   - Server → Client instead of Client → Server
   - Parallel data fetching
   - Streaming and Suspense boundaries

## 4. Step-by-Step Migration Guide

1. **Prepare Your React App**
   - Identify client-side state
   - List all data fetching logic
   - Document routing structure

2. **Initialize Next.js**
   - Create new Next.js project
   - Copy over components
   - Update import statements

3. **Convert Components**
   - Start with leaf components
   - Move data fetching to server
   - Add loading states

4. **Update Routing**
   - Create app directory structure
   - Move pages to appropriate locations
   - Update navigation logic

5. **Optimize**
   - Implement caching strategies
   - Add error boundaries
   - Optimize images and fonts

## 5. Common Gotchas

1. **Server Components**
   - Can't use hooks
   - Can't use browser APIs
   - Must be async for data fetching

2. **Client Components**
   - Must use 'use client' directive
   - Can't use async/await at top level
   - State is client-side only

3. **Data Fetching**
   - Different caching strategies
   - Revalidation patterns
   - Error handling approaches

## 6. Exercise: Converting a React App

Take a simple React todo app and convert it to Next.js:

1. Create the file structure
2. Move state to server components
3. Implement API routes
4. Add loading states
5. Optimize performance

## Resources

- [Next.js Migration Guide](https://nextjs.org/docs/app/building-your-application/upgrading/app-router-migration)
- [React Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components)
- [Data Fetching Fundamentals](https://nextjs.org/docs/app/building-your-application/data-fetching) 