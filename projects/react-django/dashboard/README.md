# React-Django Social Media Dashboard

This project demonstrates an advanced social media analytics dashboard built with a React frontend and Django REST Framework backend. It features real-time updates, complex data visualization, and advanced state management.

## Features

- User authentication with role-based access control
- Real-time data updates using WebSockets
- Interactive data visualizations and charts
- Social media post scheduling and management
- Analytics tracking and reporting
- File uploads for media content
- Dark/light theme support
- Responsive design for mobile and desktop

## Project Structure

```
dashboard/
├── frontend/              # React frontend
│   ├── public/            # Static files
│   └── src/               # React source code
│       ├── components/    # Reusable components
│       ├── pages/         # Page components
│       ├── hooks/         # Custom React hooks
│       ├── store/         # Redux store configuration
│       ├── slices/        # Redux slices
│       ├── services/      # API services
│       ├── utils/         # Utility functions
│       └── assets/        # Images, icons, etc.
└── backend/               # Django backend
    ├── dashboard/         # Django project
    ├── analytics/         # Analytics app
    ├── accounts/          # User accounts app
    ├── scheduler/         # Post scheduler app
    ├── reports/           # Reports generation app
    └── api/               # API app
```

## Technology Stack

### Frontend
- React with functional components and hooks
- Redux Toolkit for state management
- React Query for server state
- Socket.IO for real-time communication
- D3.js or Chart.js for data visualization
- Material-UI or Tailwind CSS for styling

### Backend
- Django
- Django REST Framework
- Django Channels for WebSockets
- Celery for background tasks
- Redis for caching and as message broker
- PostgreSQL for the database

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

4. Set up environment variables:
   - Create a `.env` file with necessary configurations
   - Include database settings, secret key, and API keys

5. Run migrations:
   ```
   python manage.py migrate
   ```

6. Start the development server:
   ```
   python manage.py runserver
   ```

7. In a separate terminal, start Redis:
   ```
   redis-server
   ```

8. In another terminal, start Celery worker:
   ```
   celery -A dashboard worker --loglevel=info
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

### Analytics
- `GET /api/analytics/overview/`: Get dashboard overview
- `GET /api/analytics/posts/`: Get post performance metrics
- `GET /api/analytics/engagement/`: Get engagement metrics
- `GET /api/analytics/audience/`: Get audience demographics

### Social Media Management
- `GET /api/posts/`: List all scheduled posts
- `POST /api/posts/`: Create a new scheduled post
- `GET /api/posts/{id}/`: Get a specific post
- `PUT /api/posts/{id}/`: Update a post
- `DELETE /api/posts/{id}/`: Delete a post
- `POST /api/posts/{id}/publish/`: Publish a post immediately

### Authentication
- `POST /api/token/`: Get authentication token
- `POST /api/token/refresh/`: Refresh authentication token
- `GET /api/users/me/`: Get current user profile
- `PUT /api/users/me/`: Update user profile

### WebSocket Endpoints
- `/ws/analytics/`: Real-time analytics updates
- `/ws/notifications/`: User notifications

## Learning Objectives

- Implementing WebSockets for real-time communication
- Advanced state management with Redux Toolkit
- Creating reusable custom hooks
- Optimizing performance in React applications
- Implementing complex data visualizations
- Working with background tasks using Celery
- Managing file uploads and storage

## Next Steps

After completing this project, you can extend it with:
- Integration with multiple social media platforms
- AI-powered content suggestions
- Advanced reporting with export options
- User collaboration features
- Advanced role-based permissions
- Mobile app version
- Internationalization support 