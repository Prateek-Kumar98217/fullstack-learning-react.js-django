# React-Django Blog Project

This project demonstrates a basic blog application built with a React frontend and Django REST Framework backend.

## Features

- User authentication (login, register, logout)
- Create, read, update, and delete blog posts
- Comment system
- User profiles
- Rich text editor for content creation
- Responsive design

## Project Structure

```
blog/
├── frontend/           # React frontend
│   ├── public/         # Static files
│   └── src/            # React source code
│       ├── components/ # Reusable components
│       ├── pages/      # Page components
│       ├── context/    # React context API
│       ├── services/   # API services
│       └── utils/      # Utility functions
└── backend/            # Django backend
    ├── blog/           # Django project
    ├── posts/          # Blog posts app
    ├── accounts/       # User accounts app
    └── api/            # API app
```

## Technology Stack

### Frontend
- React
- React Router
- Axios
- Bootstrap or Material-UI

### Backend
- Django
- Django REST Framework
- Simple JWT for authentication
- PostgreSQL (recommended for production)

## Setup Instructions

### Backend Setup
1. Navigate to the backend directory:
   ```
   cd backend
   ```

2. Create a virtual environment:
   ```
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Install dependencies:
   ```
   pip install -r requirements.txt
   ```

4. Run migrations:
   ```
   python manage.py migrate
   ```

5. Create a superuser:
   ```
   python manage.py createsuperuser
   ```

6. Start the development server:
   ```
   python manage.py runserver
   ```

### Frontend Setup
1. Navigate to the frontend directory:
   ```
   cd frontend
   ```

2. Install dependencies:
   ```
   npm install
   ```

3. Start the development server:
   ```
   npm start
   ```

4. Access the application at `http://localhost:3000`

## API Endpoints

- `POST /api/token/`: Get authentication token
- `GET /api/posts/`: List all posts
- `POST /api/posts/`: Create a new post
- `GET /api/posts/{id}/`: Get a specific post
- `PUT /api/posts/{id}/`: Update a specific post
- `DELETE /api/posts/{id}/`: Delete a specific post
- `GET /api/posts/{id}/comments/`: Get comments for a post
- `POST /api/posts/{id}/comments/`: Add a comment to a post

## Learning Objectives

- Implementing RESTful APIs with Django REST Framework
- Managing frontend state with React
- Handling authentication between separate frontend and backend
- Implementing CRUD operations
- Managing relationships in a database

## Next Steps

After completing this project, you can extend it with:
- Categories and tags
- Search functionality
- Image uploads
- Social media sharing
- Analytics 