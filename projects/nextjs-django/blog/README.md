# Modern Blog with Next.js and Django

A full-stack blog application built with Next.js 14 and Django REST Framework, demonstrating server components, API routes, and Django integration.

## Features

- Server-side rendering with Next.js
- Django REST Framework backend
- JWT authentication
- Rich text editor
- Image uploads
- SEO optimization
- Comments system
- Categories and tags
- Search functionality
- Admin dashboard

## Project Structure

```
blog/
├── frontend/                 # Next.js frontend
│   ├── app/                 # App router
│   │   ├── page.tsx        # Home page
│   │   ├── posts/          # Posts routes
│   │   ├── admin/          # Admin routes
│   │   └── api/            # API routes
│   ├── components/         # React components
│   ├── lib/                # Utility functions
│   └── types/              # TypeScript types
└── backend/                # Django backend
    ├── blog/              # Django project
    ├── api/               # Django app
    ├── posts/             # Posts app
    └── users/             # Users app
```

## Getting Started

### Backend Setup

1. Create virtual environment:
```bash
cd backend
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Run migrations:
```bash
python manage.py migrate
```

4. Create superuser:
```bash
python manage.py createsuperuser
```

5. Start development server:
```bash
python manage.py runserver
```

### Frontend Setup

1. Install dependencies:
```bash
cd frontend
npm install
```

2. Configure environment variables:
```bash
cp .env.example .env.local
# Edit .env.local with your settings
```

3. Start development server:
```bash
npm run dev
```

## API Endpoints

### Posts
- `GET /api/posts/` - List all posts
- `POST /api/posts/` - Create new post
- `GET /api/posts/{id}/` - Get post details
- `PUT /api/posts/{id}/` - Update post
- `DELETE /api/posts/{id}/` - Delete post

### Categories
- `GET /api/categories/` - List all categories
- `POST /api/categories/` - Create new category
- `GET /api/categories/{id}/` - Get category details

### Comments
- `GET /api/posts/{id}/comments/` - List post comments
- `POST /api/posts/{id}/comments/` - Add comment
- `DELETE /api/comments/{id}/` - Delete comment

### Authentication
- `POST /api/token/` - Get JWT token
- `POST /api/token/refresh/` - Refresh JWT token
- `POST /api/register/` - Register new user

## Development

### Backend Development

1. Create new app:
```bash
python manage.py startapp app_name
```

2. Add models:
```python
# app_name/models.py
from django.db import models

class YourModel(models.Model):
    name = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now_add=True)
```

3. Create serializer:
```python
# app_name/serializers.py
from rest_framework import serializers
from .models import YourModel

class YourModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = YourModel
        fields = '__all__'
```

4. Add views:
```python
# app_name/views.py
from rest_framework import viewsets
from .models import YourModel
from .serializers import YourModelSerializer

class YourModelViewSet(viewsets.ModelViewSet):
    queryset = YourModel.objects.all()
    serializer_class = YourModelSerializer
```

### Frontend Development

1. Create new page:
```typescript
// app/your-route/page.tsx
export default async function Page() {
  const data = await fetch('http://localhost:8000/api/endpoint/').then(r => r.json())
  
  return (
    <main>
      {/* Your content */}
    </main>
  )
}
```

2. Create new component:
```typescript
// components/YourComponent.tsx
'use client'

import { useState } from 'react'

export function YourComponent() {
  const [data, setData] = useState(null)
  
  return (
    <div>
      {/* Your component JSX */}
    </div>
  )
}
```

## Deployment

### Backend Deployment (Example with DigitalOcean)

1. Set up server
2. Install dependencies
3. Configure Gunicorn
4. Set up Nginx
5. Configure SSL

### Frontend Deployment (Example with Vercel)

1. Push to GitHub
2. Connect to Vercel
3. Configure environment variables
4. Deploy

## Contributing

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Next.js team for the amazing framework
- Django team for the robust backend framework
- All contributors who participate in this project 