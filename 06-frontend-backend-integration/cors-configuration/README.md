# CORS Configuration

This section covers the implementation of Cross-Origin Resource Sharing (CORS) in Django to allow your React frontend to communicate with your Django backend.

## What is CORS?

Cross-Origin Resource Sharing (CORS) is a security feature implemented by browsers that restricts web pages from making requests to a different domain than the one that served the original page. This is a critical security concept called the Same-Origin Policy.

For a frontend (React) application to communicate with a backend (Django) API when they're hosted on different domains or ports, CORS must be properly configured on the backend.

## Setting Up CORS in Django

Django doesn't have built-in CORS support, but the `django-cors-headers` package makes it simple to implement.

### Installation

```bash
pip install django-cors-headers
```

### Configuration

1. Add to INSTALLED_APPS in settings.py:

```python
INSTALLED_APPS = [
    # ...
    'corsheaders',
    # ...
]
```

2. Add the middleware to MIDDLEWARE (order is important):

```python
MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    # ... other middleware
]
```

3. Configure CORS settings:

```python
# Allow requests from your React app
CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",  # React development server
    "https://yourdomain.com",  # Production domain
]

# Allow credentials (cookies, authorization headers)
CORS_ALLOW_CREDENTIALS = True

# Specify which HTTP methods are allowed
CORS_ALLOW_METHODS = [
    'DELETE',
    'GET',
    'OPTIONS',
    'PATCH',
    'POST',
    'PUT',
]

# Specify which headers can be included in requests
CORS_ALLOW_HEADERS = [
    'accept',
    'accept-encoding',
    'authorization',
    'content-type',
    'dnt',
    'origin',
    'user-agent',
    'x-csrftoken',
    'x-requested-with',
]
```

## Security Considerations

When configuring CORS, keep these security best practices in mind:

1. **Be specific with allowed origins**: Avoid using `CORS_ORIGIN_ALLOW_ALL = True` in production
2. **Limit allowed methods**: Only allow the HTTP methods your API needs
3. **Restrict allowed headers**: Only include headers your application requires
4. **Consider CSRF protection**: Especially important when using cookies for authentication
5. **Use HTTPS**: Especially for production environments

## Testing CORS Configuration

You can test your CORS configuration using:

1. The browser's developer console
2. A tool like Postman or curl
3. Simple fetch requests from your React application

## Example: Basic API Request from React

```javascript
// In your React component
const fetchData = async () => {
    try {
        const response = await fetch('http://localhost:8000/api/data/', {
            headers: {
                'Content-Type': 'application/json',
                // Include any needed auth headers
            },
            credentials: 'include', // For cookies
        });
        
        const data = await response.json();
        console.log(data);
    } catch (error) {
        console.error('Error fetching data:', error);
    }
};
```

## Next Steps

After setting up CORS, proceed to the [Authentication Flows](../authentication-flows) section to learn how to implement secure authentication between your frontend and backend. 