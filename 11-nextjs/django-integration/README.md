# Next.js + Django Integration Guide

This guide covers best practices and patterns for integrating Next.js with Django to create modern full-stack applications.

## Architecture Overview

### Option 1: Decoupled Architecture
```
[Next.js Frontend] <---> [Django REST API]
- Separate deployments
- Communication via REST/GraphQL
- Independent scaling
```

### Option 2: Hybrid Architecture
```
[Next.js Frontend + API Routes] <---> [Django Backend]
- API routes for simple operations
- Django for complex business logic
- Shared authentication
```

## Integration Patterns

### 1. Data Fetching

#### Server Components
```typescript
// app/posts/page.tsx
async function Posts() {
  const posts = await fetch('http://django-api.example/api/posts/', {
    headers: {
      'Authorization': `Bearer ${process.env.API_TOKEN}`
    }
  }).then(r => r.json())
  
  return <PostList posts={posts} />
}
```

#### API Routes
```typescript
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  const response = await fetch('http://django-api.example/api/posts/')
  const data = await response.json()
  return NextResponse.json(data)
}
```

### 2. Authentication

#### Using NextAuth.js with Django
```typescript
// app/api/auth/[...nextauth]/route.ts
import NextAuth from 'next-auth'
import CredentialsProvider from 'next-auth/providers/credentials'

const handler = NextAuth({
  providers: [
    CredentialsProvider({
      name: 'Django Backend',
      credentials: {
        username: { label: "Username", type: "text" },
        password: { label: "Password", type: "password" }
      },
      async authorize(credentials) {
        const res = await fetch('http://django-api.example/api/token/', {
          method: 'POST',
          body: JSON.stringify(credentials),
          headers: { "Content-Type": "application/json" }
        })
        const user = await res.json()
        return user
      }
    })
  ],
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.accessToken = user.access
      }
      return token
    },
    async session({ session, token }) {
      session.accessToken = token.accessToken
      return session
    }
  }
})

export { handler as GET, handler as POST }
```

### 3. Form Handling

```typescript
// components/CreatePost.tsx
'use client'

import { useState } from 'react'

export function CreatePost() {
  const [title, setTitle] = useState('')
  const [content, setContent] = useState('')

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    
    const response = await fetch('/api/posts', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ title, content })
    })
    
    if (response.ok) {
      // Handle success
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      {/* Form fields */}
    </form>
  )
}
```

## Django Configuration

### CORS Settings
```python
# settings.py
INSTALLED_APPS = [
    # ...
    'corsheaders',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    # ...
]

CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
    "https://your-nextjs-app.com",
]

CORS_ALLOW_CREDENTIALS = True
```

### JWT Authentication
```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=60),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),
}
```

## Development Setup

1. **Django Backend Setup**
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows

# Install dependencies
pip install django djangorestframework django-cors-headers djangorestframework-simplejwt

# Create project
django-admin startproject backend
cd backend

# Create app
python manage.py startapp api

# Run migrations
python manage.py migrate

# Start server
python manage.py runserver
```

2. **Next.js Frontend Setup**
```bash
# Create Next.js app
npx create-next-app@latest frontend --typescript
cd frontend

# Install dependencies
npm install next-auth @types/next-auth axios

# Create .env.local
echo "NEXTAUTH_SECRET=your-secret-here
NEXTAUTH_URL=http://localhost:3000
DJANGO_API_URL=http://localhost:8000" > .env.local

# Start development server
npm run dev
```

## Best Practices

1. **Environment Configuration**
   - Use environment variables for API URLs
   - Keep secrets in .env files
   - Use different configurations for development/production

2. **Error Handling**
   - Implement proper error boundaries
   - Handle network errors gracefully
   - Provide user-friendly error messages

3. **Security**
   - Implement CSRF protection
   - Use secure HTTP-only cookies
   - Validate input on both ends
   - Implement rate limiting

4. **Performance**
   - Use server components where possible
   - Implement caching strategies
   - Optimize API calls
   - Use connection pooling

## Common Pitfalls

1. **CORS Issues**
   - Ensure proper CORS configuration
   - Handle preflight requests
   - Set appropriate headers

2. **Authentication Flow**
   - Handle token expiration
   - Implement refresh token rotation
   - Secure token storage

3. **Data Synchronization**
   - Handle optimistic updates
   - Implement proper error recovery
   - Manage loading states

## Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [NextAuth.js](https://next-auth.js.org/)
- [Django CORS Headers](https://github.com/adamchainz/django-cors-headers)
- [Simple JWT](https://django-rest-framework-simplejwt.readthedocs.io/) 