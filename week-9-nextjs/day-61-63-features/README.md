# Days 61-63: Advanced Next.js Features

## Learning Objectives
By the end of these three days, you will:
- Master advanced data fetching patterns
- Implement authentication and authorization
- Use server and client components effectively
- Optimize performance with caching
- Deploy Next.js applications
- Integrate with databases and external APIs
- Handle forms and file uploads

## 1. Advanced Data Fetching

### Server Components and Data
```typescript
// app/posts/page.tsx
import { Suspense } from 'react'
import { PostList } from './PostList'
import { LoadingSkeleton } from './LoadingSkeleton'

async function getPosts() {
  const res = await fetch('https://api.example.com/posts', {
    next: {
      revalidate: 3600,  // Revalidate every hour
      tags: ['posts'],   // Cache tag for manual revalidation
    }
  })
  
  if (!res.ok) throw new Error('Failed to fetch posts')
  return res.json()
}

export default async function PostsPage() {
  const posts = await getPosts()
  
  return (
    <Suspense fallback={<LoadingSkeleton />}>
      <PostList initialPosts={posts} />
    </Suspense>
  )
}
```

### Parallel Data Fetching
```typescript
// app/dashboard/page.tsx
import { Suspense } from 'react'
import { UserProfile, Posts, Analytics } from './components'

async function fetchDashboardData() {
  const [userPromise, postsPromise, analyticsPromise] = await Promise.all([
    fetch('https://api.example.com/user').then(r => r.json()),
    fetch('https://api.example.com/posts').then(r => r.json()),
    fetch('https://api.example.com/analytics').then(r => r.json())
  ])
  
  return {
    user: await userPromise,
    posts: await postsPromise,
    analytics: await analyticsPromise
  }
}

export default async function DashboardPage() {
  const { user, posts, analytics } = await fetchDashboardData()
  
  return (
    <div className="dashboard">
      <Suspense fallback={<div>Loading profile...</div>}>
        <UserProfile user={user} />
      </Suspense>
      
      <Suspense fallback={<div>Loading posts...</div>}>
        <Posts posts={posts} />
      </Suspense>
      
      <Suspense fallback={<div>Loading analytics...</div>}>
        <Analytics data={analytics} />
      </Suspense>
    </div>
  )
}
```

## 2. Authentication and Authorization

### NextAuth.js Integration
```typescript
// app/api/auth/[...nextauth]/route.ts
import NextAuth from 'next-auth'
import { authOptions } from '@/lib/auth'

const handler = NextAuth(authOptions)
export { handler as GET, handler as POST }

// lib/auth.ts
import { NextAuthOptions } from 'next-auth'
import GoogleProvider from 'next-auth/providers/google'
import { PrismaAdapter } from '@auth/prisma-adapter'
import { prisma } from '@/lib/prisma'

export const authOptions: NextAuthOptions = {
  adapter: PrismaAdapter(prisma),
  providers: [
    GoogleProvider({
      clientId: process.env.GOOGLE_ID!,
      clientSecret: process.env.GOOGLE_SECRET!,
    }),
  ],
  callbacks: {
    async session({ session, user }) {
      return {
        ...session,
        user: {
          ...session.user,
          id: user.id,
        },
      }
    },
  },
}
```

### Protected Routes
```typescript
// middleware.ts
import { withAuth } from 'next-auth/middleware'

export default withAuth({
  callbacks: {
    authorized({ req, token }) {
      // Only allow authenticated users to access /dashboard
      return token?.role === 'admin'
    },
  },
})

export const config = {
  matcher: ['/dashboard/:path*']
}
```

## 3. Database Integration

### Prisma Setup
```typescript
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
}

model Post {
  id        String   @id @default(cuid())
  title     String
  content   String
  published Boolean  @default(false)
  author    User     @relation(fields: [authorId], references: [id])
  authorId  String
  createdAt DateTime @default(now())
}
```

### Database Operations
```typescript
// lib/prisma.ts
import { PrismaClient } from '@prisma/client'

const globalForPrisma = global as unknown as { prisma: PrismaClient }

export const prisma = globalForPrisma.prisma || new PrismaClient()

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma

// app/api/posts/route.ts
import { prisma } from '@/lib/prisma'
import { NextResponse } from 'next/server'

export async function GET() {
  const posts = await prisma.post.findMany({
    include: {
      author: {
        select: {
          name: true,
          email: true,
        },
      },
    },
  })
  
  return NextResponse.json(posts)
}
```

## 4. Form Handling and File Uploads

### Form with Server Actions
```typescript
// app/posts/new/page.tsx
import { revalidatePath } from 'next/cache'

async function createPost(formData: FormData) {
  'use server'
  
  const title = formData.get('title')
  const content = formData.get('content')
  
  await prisma.post.create({
    data: {
      title: title as string,
      content: content as string,
      authorId: 'user-id', // Get from session
    },
  })
  
  revalidatePath('/posts')
}

export default function NewPostPage() {
  return (
    <form action={createPost}>
      <input type="text" name="title" required />
      <textarea name="content" required />
      <button type="submit">Create Post</button>
    </form>
  )
}
```

### File Uploads
```typescript
// app/api/upload/route.ts
import { writeFile } from 'fs/promises'
import { NextRequest, NextResponse } from 'next/server'

export async function POST(request: NextRequest) {
  const formData = await request.formData()
  const file = formData.get('file') as File
  
  if (!file) {
    return NextResponse.json(
      { error: 'No file uploaded' },
      { status: 400 }
    )
  }
  
  const bytes = await file.arrayBuffer()
  const buffer = Buffer.from(bytes)
  
  const path = `./public/uploads/${file.name}`
  await writeFile(path, buffer)
  
  return NextResponse.json({ 
    url: `/uploads/${file.name}` 
  })
}
```

## 5. Performance Optimization

### Image Optimization
```typescript
// components/OptimizedImage.tsx
import Image from 'next/image'

export function OptimizedImage({ src, alt }: { src: string; alt: string }) {
  return (
    <div className="image-container">
      <Image
        src={src}
        alt={alt}
        width={800}
        height={400}
        placeholder="blur"
        blurDataURL="data:image/jpeg;base64,..."
        priority={true}
      />
    </div>
  )
}
```

### Dynamic Imports
```typescript
// components/HeavyComponent.tsx
import dynamic from 'next/dynamic'

const Chart = dynamic(() => import('@/components/Chart'), {
  loading: () => <p>Loading chart...</p>,
  ssr: false, // Disable server-side rendering
})

export function Dashboard() {
  return (
    <div>
      <h1>Analytics Dashboard</h1>
      <Chart data={/* ... */} />
    </div>
  )
}
```

## 6. Deployment

### Environment Setup
```bash
# .env.local
DATABASE_URL="postgresql://..."
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="your-secret-key"
GOOGLE_ID="your-google-client-id"
GOOGLE_SECRET="your-google-client-secret"
```

### Build and Deploy
```bash
# Build the application
npm run build

# Start production server
npm start

# Deploy to Vercel
vercel deploy
```

## 7. Exercises

1. Implement authentication with NextAuth.js
2. Create a full-stack blog with Prisma
3. Add file upload functionality
4. Optimize images and implement lazy loading
5. Deploy your application to Vercel

## 8. Mini Project: Social Media Platform

Build a small social media platform with:
- User authentication
- Post creation with images
- Real-time likes and comments
- Profile management
- Admin dashboard
- Performance optimization
- Deployment to Vercel

## Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)
- [NextAuth.js Documentation](https://next-auth.js.org)
- [Vercel Documentation](https://vercel.com/docs)

## Next Steps
Congratulations! You've completed the Next.js section. Now you can move on to your final project, combining all the technologies you've learned throughout this course. 