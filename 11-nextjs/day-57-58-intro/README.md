# Days 57-58: Introduction to Next.js

## Learning Objectives
By the end of these two days, you will:
- Understand what Next.js is and its benefits
- Set up a Next.js development environment
- Learn about Server-Side Rendering (SSR) and Static Site Generation (SSG)
- Create your first Next.js application
- Master the basic Next.js project structure

## 1. What is Next.js?

Next.js is a React framework that provides:
- Server-side rendering
- Static site generation
- API routes
- File-based routing
- Built-in optimizations
- TypeScript support
- Development environment

### Why Next.js?
- Better SEO through server-side rendering
- Improved performance
- Developer experience
- Built-in routing
- API routes without separate backend
- Automatic code splitting
- Image optimization

## 2. Setting Up Next.js

### Creating a New Project
```bash
# Create a new Next.js project
npx create-next-app@latest my-nextjs-app
cd my-nextjs-app

# If you want to use TypeScript
npx create-next-app@latest my-nextjs-app --typescript

# Start the development server
npm run dev
```

### Project Structure
```
my-nextjs-app/
├── app/                # App Router (Next.js 13+)
│   ├── layout.tsx     # Root layout
│   ├── page.tsx       # Home page
│   └── globals.css    # Global styles
├── public/            # Static files
│   └── images/        # Image assets
├── components/        # React components
├── lib/              # Utility functions
├── styles/           # CSS modules
├── .gitignore        # Git ignore file
├── next.config.js    # Next.js configuration
├── package.json      # Dependencies
└── tsconfig.json     # TypeScript configuration
```

## 3. Core Concepts

### Server-Side Rendering (SSR)
```typescript
// app/page.tsx
import { headers } from 'next/headers'

async function getData() {
  const headersList = headers()
  const response = await fetch('https://api.example.com/data', {
    headers: headersList
  })
  return response.json()
}

export default async function Page() {
  const data = await getData()
  
  return (
    <main>
      <h1>Server-Side Rendered Page</h1>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </main>
  )
}
```

### Static Site Generation (SSG)
```typescript
// app/blog/[slug]/page.tsx
import { generateStaticParams } from 'next/static'

export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json())
  
  return posts.map((post) => ({
    slug: post.slug,
  }))
}

export default async function BlogPost({ params }: { params: { slug: string } }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`).then(r => r.json())
  
  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
    </article>
  )
}
```

### Client-Side Components
```typescript
// components/Counter.tsx
'use client'

import { useState } from 'react'

export default function Counter() {
  const [count, setCount] = useState(0)
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  )
}
```

## 4. Styling in Next.js

### CSS Modules
```typescript
// styles/Card.module.css
.card {
  padding: 1rem;
  border: 1px solid #eaeaea;
  border-radius: 8px;
}

.title {
  margin: 0;
  font-size: 1.5rem;
}

// components/Card.tsx
import styles from '@/styles/Card.module.css'

export default function Card({ title, children }) {
  return (
    <div className={styles.card}>
      <h2 className={styles.title}>{title}</h2>
      {children}
    </div>
  )
}
```

### Global Styles
```typescript
// app/globals.css
:root {
  --max-width: 1100px;
  --border-radius: 12px;
  --font-mono: ui-monospace, monospace;
}

* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}

// app/layout.tsx
import './globals.css'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

## 5. Data Fetching

### Server-Side Data Fetching
```typescript
// app/users/page.tsx
async function getUsers() {
  const res = await fetch('https://api.example.com/users', {
    next: { revalidate: 3600 } // Revalidate every hour
  })
  
  if (!res.ok) {
    throw new Error('Failed to fetch users')
  }
  
  return res.json()
}

export default async function UsersPage() {
  const users = await getUsers()
  
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}
```

## 6. Exercises

1. Create a new Next.js application with TypeScript
2. Build a simple blog with SSG for posts
3. Implement a dynamic page with SSR
4. Create reusable components with CSS Modules
5. Practice data fetching with different methods

## 7. Mini Project: Personal Blog

Create a personal blog using Next.js that includes:
- Static homepage
- Dynamic blog posts with MDX
- Server-side rendered comments
- Client-side interactive components
- Optimized images using next/image
- Styled with CSS Modules

## Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Next.js Examples](https://github.com/vercel/next.js/tree/canary/examples)
- [Learn Next.js](https://nextjs.org/learn)
- [Vercel Platform](https://vercel.com)

## Troubleshooting

### Common Issues and Solutions

1. Build Errors
```bash
# Clear Next.js cache
rm -rf .next
# Reinstall dependencies
rm -rf node_modules
npm install
```

2. Image Optimization
```typescript
// Configure domains in next.config.js
module.exports = {
  images: {
    domains: ['example.com'],
  },
}
```

3. API Routes
```typescript
// Ensure API routes are in the correct location
// app/api/route.ts
export async function GET() {
  return Response.json({ message: 'Hello' })
}
```

## Next Steps
In the next section, we'll dive deeper into Next.js routing and navigation features. 