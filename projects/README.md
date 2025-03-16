# Full-Stack Projects

This directory contains a comprehensive set of projects to guide you through web development learning, from frontend basics to complex full-stack applications.

## Individual Project Directories

### Frontend Projects
- **Todo App**: A vanilla JavaScript todo application with local storage
- **TypeScript App**: A todo application refactored with TypeScript

### Backend Projects
- **Django Blog**: A full-featured blog application built with Django
- **Auth System**: A comprehensive authentication system with Django

### Full-Stack Projects
- **Blog Platform**: A complete React + Django REST blog platform
- **Portfolio**: A professional portfolio site (empty template for your work)
- **Final Project**: A capstone SaaS application project

## Track A: React + Django Projects

### 1. Basic Blog
- React frontend with React Router
- Django REST Framework backend
- Basic CRUD operations
- Simple authentication

### 2. E-commerce Platform
- Product catalog
- Shopping cart
- User authentication
- Order management
- Payment integration

### 3. Social Media Dashboard
- Real-time updates
- File uploads
- Complex state management
- Advanced authentication
- Performance optimization

## Track B: Next.js + Django Projects

### 1. Modern Blog
- Server-side rendering
- API routes for simple operations
- Django as headless CMS
- Authentication with NextAuth.js
- SEO optimization

### 2. E-commerce Store
- Static product pages
- Dynamic cart management
- Hybrid rendering
- Django REST Framework integration
- Stripe payment integration

### 3. Enterprise Dashboard
- Real-time data visualization
- Server components
- Complex data fetching
- Role-based access control
- Performance monitoring

## Project Structure

```
projects/
├── todo-app/               # Basic JavaScript todo application
├── typescript-app/         # TypeScript todo application
├── django-blog/            # Django-based blog
├── auth-system/            # Authentication system
├── blog-platform/          # Full-stack blog platform
├── portfolio/              # Portfolio site template
├── final-project/          # Capstone project template
├── react-django/
│   ├── blog/
│   │   ├── frontend/        # React frontend
│   │   └── backend/         # Django backend
│   ├── ecommerce/
│   │   ├── frontend/
│   │   └── backend/
│   └── dashboard/
│       ├── frontend/
│       └── backend/
└── nextjs-django/
    ├── blog/
    │   ├── frontend/        # Next.js frontend
    │   └── backend/         # Django backend
    ├── ecommerce/
    │   ├── frontend/
    │   └── backend/
    └── dashboard/
        ├── frontend/
        └── backend/
```

## Learning Path

The projects in this directory follow a logical progression:

1. **Frontend Basics**: Todo App → TypeScript App
2. **Backend Fundamentals**: Django Blog → Auth System
3. **Full-Stack Integration**: React-Django Projects → NextJS-Django Projects
4. **Advanced Applications**: Blog Platform → Final Project

Each project includes a detailed README.md with:
- Project goals and features
- Technology stack
- Implementation steps
- Learning objectives
- Next steps for enhancement

## Integration Patterns

### React + Django
1. Separate frontend and backend servers
2. CORS configuration
3. Token-based authentication
4. REST API communication

### Next.js + Django
1. API routes for simple operations
2. Django as main backend for complex operations
3. Hybrid authentication (NextAuth.js + Django)
4. Server-side data fetching

## Development Setup

### React + Django
```bash
# Backend
cd projects/react-django/blog/backend
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver

# Frontend
cd ../frontend
npm install
npm start
```

### Next.js + Django
```bash
# Backend
cd projects/nextjs-django/blog/backend
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver

# Frontend
cd ../frontend
npm install
npm run dev
```

## Deployment Considerations

### React + Django
- Separate deployments for frontend and backend
- CORS configuration required
- Static file serving from CDN
- API gateway recommended

### Next.js + Django
- Option for single-server deployment
- Built-in API routing
- Automatic static optimization
- Edge functions support

## Resources

### Frontend Development
- [MDN Web Docs](https://developer.mozilla.org/en-US/)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [React Documentation](https://reactjs.org/docs/getting-started.html)
- [Next.js Documentation](https://nextjs.org/docs)

### Backend Development
- [Django Documentation](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [JWT Authentication](https://jwt.io/introduction)

### Full-Stack Resources
- [Axios](https://axios-http.com/)
- [Redux Toolkit](https://redux-toolkit.js.org/)
- [Docker Documentation](https://docs.docker.com/)
- [AWS Documentation](https://docs.aws.amazon.com/) 