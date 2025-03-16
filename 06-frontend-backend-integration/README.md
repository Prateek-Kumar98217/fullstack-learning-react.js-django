# Frontend-Backend Integration

This module focuses on connecting React frontends with Django backends to create cohesive full-stack applications.

## Learning Objectives

By the end of this module, you will be able to:

- Connect a React frontend to a Django REST API
- Handle CORS (Cross-Origin Resource Sharing) in Django
- Implement authentication between frontend and backend
- Manage API state in React applications
- Create protected routes in React based on authentication status
- Implement error handling for API requests

## Topics Covered

### 1. CORS Configuration

- Understanding Same-Origin Policy and CORS
- Configuring Django for CORS
- Using django-cors-headers
- Security considerations

### 2. Authentication Flows

- Token-based authentication
- JWT (JSON Web Tokens)
- Storing authentication state in React
- Protected routes and components

### 3. Data Flow

- Fetching data from Django APIs
- POST, PUT, DELETE operations
- Handling loading states
- Error handling and retry strategies

### 4. State Management with API Data

- Using React Context for global state
- Redux for API state management
- Caching considerations
- Optimistic updates

## Resources

### Documentation

- [Django CORS Headers](https://github.com/adamchainz/django-cors-headers)
- [JWT Authentication](https://django-rest-framework-simplejwt.readthedocs.io/)
- [React Router - Protected Routes](https://reactrouter.com/docs/en/v6)

### Exercises

1. **Basic Integration**: Connect a simple React app to a Django API
2. **Authentication**: Implement JWT authentication between React and Django
3. **Protected Dashboard**: Create a protected admin dashboard in React that requires authentication

## Project Ideas

- **User Profile System**: Create a user profile system with authentication where users can sign up, log in, and update their profile information
- **Task Management App**: Build a task management application with protected routes and user-specific data

## Next Steps

After completing this module, you'll be ready to build more complex full-stack applications in the [07-fullstack-applications](../07-fullstack-applications) module. 