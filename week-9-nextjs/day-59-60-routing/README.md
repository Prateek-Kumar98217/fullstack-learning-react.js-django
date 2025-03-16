# Days 59-60: Next.js Routing and Navigation

## Learning Objectives
By the end of these two days, you will:
- Master Next.js 13+ App Router
- Understand file-based routing
- Implement dynamic routes
- Create nested layouts
- Handle loading and error states
- Use client-side navigation
- Implement route handlers (API routes)

## 1. App Router Fundamentals

### File Convention Routes
```plaintext
app/
├── page.tsx           # Home page (/)
├── about/
│   └── page.tsx      # About page (/about)
├── blog/
│   ├── page.tsx      # Blog index (/blog)
│   └── [slug]/       # Dynamic route
│       └── page.tsx  # Individual blog post (/blog/post-1)
└── layout.tsx        # Root layout
```

### Basic Page Component
```typescript
// app/page.tsx
export default function HomePage() {
  return (
    <main>
      <h1>Welcome to My Next.js App</h1>
      <p>This is the home page.</p>
    </main>
  )
}
```

## 2. Layouts and Templates

### Root Layout
```typescript
// app/layout.tsx
import { Inter } from 'next/font/google'
import './globals.css'

const inter = Inter({ subsets: ['latin'] })

export const metadata = {
  title: 'My Next.js App',
  description: 'Learning Next.js routing',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <header>
          <nav>{/* Navigation components */}</nav>
        </header>
        <main>{children}</main>
        <footer>{/* Footer content */}</footer>
      </body>
    </html>
  )
}
```

### Nested Layouts
```typescript
// app/blog/layout.tsx
export default function BlogLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <div className="blog-layout">
      <aside className="sidebar">
        {/* Blog sidebar content */}
      </aside>
      <section className="content">
        {children}
      </section>
    </div>
  )
}
```

## 3. Dynamic Routes

### Dynamic Segments
```typescript
// app/blog/[slug]/page.tsx
interface Props {
  params: { slug: string }
}

export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json())
  
  return posts.map((post) => ({
    slug: post.slug,
  }))
}

export default async function BlogPost({ params }: Props) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`).then(r => r.json())
  
  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
    </article>
  )
}
```

### Catch-all Segments
```typescript
// app/docs/[...slug]/page.tsx
interface Props {
  params: { slug: string[] }
}

export default function DocsPage({ params }: Props) {
  // params.slug is an array: ["a", "b", "c"] for /docs/a/b/c
  return (
    <div>
      <h1>Documentation</h1>
      <p>Current path: {params.slug.join('/')}</p>
    </div>
  )
}
```

## 4. Navigation

### Link Component
```typescript
// components/Navigation.tsx
import Link from 'next/link'

export default function Navigation() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/about">About</Link>
      <Link 
        href={{
          pathname: '/blog/[slug]',
          query: { slug: 'hello-world' },
        }}
      >
        Hello World Post
      </Link>
    </nav>
  )
}
```

### Programmatic Navigation
```typescript
'use client'

import { useRouter } from 'next/navigation'

export default function NavigationButtons() {
  const router = useRouter()

  return (
    <div>
      <button onClick={() => router.push('/about')}>
        Go to About
      </button>
      <button onClick={() => router.back()}>
        Go Back
      </button>
      <button onClick={() => router.forward()}>
        Go Forward
      </button>
      <button onClick={() => router.refresh()}>
        Refresh
      </button>
    </div>
  )
}
```

## 5. Loading and Error States

### Loading State
```typescript
// app/blog/loading.tsx
export default function Loading() {
  return (
    <div className="loading-spinner">
      <div className="spinner"></div>
      <p>Loading blog posts...</p>
    </div>
  )
}
```

### Error Handling
```typescript
// app/blog/error.tsx
'use client'

export default function Error({
  error,
  reset,
}: {
  error: Error
  reset: () => void
}) {
  return (
    <div className="error-container">
      <h2>Something went wrong!</h2>
      <p>{error.message}</p>
      <button onClick={() => reset()}>Try again</button>
    </div>
  )
}
```

## 6. Route Handlers (API Routes)

### Basic Route Handler
```typescript
// app/api/hello/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  return NextResponse.json({ message: 'Hello, World!' })
}
```

### Dynamic API Routes
```typescript
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const post = await fetchPost(params.id)
  
  if (!post) {
    return NextResponse.json(
      { error: 'Post not found' },
      { status: 404 }
    )
  }
  
  return NextResponse.json(post)
}

export async function PUT(
  request: Request,
  { params }: { params: { id: string } }
) {
  const body = await request.json()
  const updated = await updatePost(params.id, body)
  
  return NextResponse.json(updated)
}
```

## 7. Middleware

### Basic Middleware
```typescript
// middleware.ts
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  // Check if user is authenticated
  const isAuthenticated = request.cookies.has('auth-token')
  
  if (!isAuthenticated) {
    return NextResponse.redirect(new URL('/login', request.url))
  }
  
  return NextResponse.next()
}

export const config = {
  matcher: '/protected/:path*',
}
```

## 8. Exercises

1. Create a blog with nested routes and layouts
2. Implement dynamic routes with pagination
3. Add loading and error states
4. Create protected routes using middleware
5. Build a REST API using route handlers

## 9. Mini Project: Documentation Site

Create a documentation site that includes:
- Nested documentation structure
- Search functionality
- Dynamic routing for docs pages
- Loading states and error boundaries
- API endpoints for content
- Protected admin section

## Resources

- [Next.js Routing Docs](https://nextjs.org/docs/app/building-your-application/routing)
- [Next.js Navigation](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating)
- [Next.js Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers)
- [Next.js Middleware](https://nextjs.org/docs/app/building-your-application/routing/middleware)

## Next Steps
In the next section, we'll explore advanced Next.js features including data fetching patterns, authentication, and deployment strategies. 