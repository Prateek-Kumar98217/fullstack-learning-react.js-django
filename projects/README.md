# Full-Stack Projects

This directory contains two parallel tracks of full-stack projects, allowing you to choose between traditional React+Django and modern Next.js+Django approaches.

## Track A: React + Django Projects

### 1. Basic Blog (Week 5-6)
- React frontend with React Router
- Django REST Framework backend
- Basic CRUD operations
- Simple authentication

### 2. E-commerce Platform (Week 6-7)
- Product catalog
- Shopping cart
- User authentication
- Order management
- Payment integration

### 3. Social Media Dashboard (Week 7-8)
- Real-time updates
- File uploads
- Complex state management
- Advanced authentication
- Performance optimization

## Track B: Next.js + Django Projects

### 1. Modern Blog (Week 9)
- Server-side rendering
- API routes for simple operations
- Django as headless CMS
- Authentication with NextAuth.js
- SEO optimization

### 2. E-commerce Store (Week 9-10)
- Static product pages
- Dynamic cart management
- Hybrid rendering
- Django REST Framework integration
- Stripe payment integration

### 3. Enterprise Dashboard (Week 10)
- Real-time data visualization
- Server components
- Complex data fetching
- Role-based access control
- Performance monitoring

## Project Structure

```
projects/
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

## Learning Objectives

### React + Django Path
1. Master REST API design
2. Handle CORS and security
3. Manage separate deployments
4. Implement client-side routing
5. Handle client-side state

### Next.js + Django Path
1. Leverage server components
2. Optimize with static generation
3. Use hybrid rendering patterns
4. Implement API routes
5. Manage server-side state

## Resources

### React + Django
- [Django REST Framework](https://www.django-rest-framework.org/)
- [React Router](https://reactrouter.com/)
- [Axios](https://axios-http.com/)
- [Redux Toolkit](https://redux-toolkit.js.org/)

### Next.js + Django
- [Next.js Documentation](https://nextjs.org/docs)
- [Django as a Headless CMS](https://www.django-cms.org/en/blog/2019/06/19/django-as-headless-cms/)
- [NextAuth.js](https://next-auth.js.org/)
- [Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components) 