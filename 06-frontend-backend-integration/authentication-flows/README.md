# Authentication Flows

This section covers implementing secure authentication between your React frontend and Django backend.

## Authentication Approaches

There are several approaches to handling authentication in a full-stack application:

1. **Token-based Authentication**: The server issues a token upon successful login, which the client includes in subsequent requests
2. **JWT (JSON Web Tokens)**: A self-contained token format that can securely transmit information between parties
3. **Session-based Authentication**: The server maintains session state and issues a session cookie
4. **OAuth 2.0**: A protocol for authorization, often used for third-party authentication

This guide focuses primarily on Token-based authentication with JWT, as it's a popular choice for React-Django applications.

## Setting Up JWT Authentication in Django

Django REST Framework provides excellent JWT support through the `djangorestframework-simplejwt` package.

### Installation

```bash
pip install djangorestframework-simplejwt
```

### Configuration

1. Add to REST_FRAMEWORK settings in settings.py:

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
}
```

2. Configure JWT settings:

```python
from datetime import timedelta

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=30),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),
    'ROTATE_REFRESH_TOKENS': False,
    'BLACKLIST_AFTER_ROTATION': True,
    'UPDATE_LAST_LOGIN': False,

    'ALGORITHM': 'HS256',
    'SIGNING_KEY': SECRET_KEY,
    'VERIFYING_KEY': None,
    'AUDIENCE': None,
    'ISSUER': None,

    'AUTH_HEADER_TYPES': ('Bearer',),
    'AUTH_HEADER_NAME': 'HTTP_AUTHORIZATION',
    'USER_ID_FIELD': 'id',
    'USER_ID_CLAIM': 'user_id',

    'AUTH_TOKEN_CLASSES': ('rest_framework_simplejwt.tokens.AccessToken',),
    'TOKEN_TYPE_CLAIM': 'token_type',

    'JTI_CLAIM': 'jti',
}
```

3. Add JWT URLs to your project's urls.py:

```python
from rest_framework_simplejwt.views import (
    TokenObtainPairView,
    TokenRefreshView,
)

urlpatterns = [
    # ...
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
    # ...
]
```

## Implementing Authentication in React

### Managing JWT Tokens

1. Create an authentication service:

```javascript
// services/authService.js

// Store tokens in localStorage
const storeTokens = (tokens) => {
  localStorage.setItem('accessToken', tokens.access);
  localStorage.setItem('refreshToken', tokens.refresh);
};

// Remove tokens from localStorage
const removeTokens = () => {
  localStorage.removeItem('accessToken');
  localStorage.removeItem('refreshToken');
};

// Get access token
const getAccessToken = () => {
  return localStorage.getItem('accessToken');
};

// Get refresh token
const getRefreshToken = () => {
  return localStorage.getItem('refreshToken');
};

// Check if user is authenticated
const isAuthenticated = () => {
  return !!getAccessToken();
};

export default {
  storeTokens,
  removeTokens,
  getAccessToken,
  getRefreshToken,
  isAuthenticated
};
```

### Authentication Context

Create an authentication context to manage auth state across your application:

```javascript
// contexts/AuthContext.js
import React, { createContext, useState, useEffect } from 'react';
import authService from '../services/authService';

export const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Check if user is already authenticated
    const checkAuth = async () => {
      const isAuth = authService.isAuthenticated();
      setIsAuthenticated(isAuth);
      
      if (isAuth) {
        // Fetch user data
        try {
          const response = await fetch('http://localhost:8000/api/users/me/', {
            headers: {
              'Authorization': `Bearer ${authService.getAccessToken()}`
            }
          });
          
          if (response.ok) {
            const userData = await response.json();
            setUser(userData);
          } else {
            // If token is invalid, log out
            handleLogout();
          }
        } catch (error) {
          console.error('Error fetching user data:', error);
        }
      }
      
      setLoading(false);
    };
    
    checkAuth();
  }, []);

  const handleLogin = async (username, password) => {
    try {
      const response = await fetch('http://localhost:8000/api/token/', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ username, password })
      });
      
      if (response.ok) {
        const tokens = await response.json();
        authService.storeTokens(tokens);
        setIsAuthenticated(true);
        
        // Fetch user data after successful login
        const userResponse = await fetch('http://localhost:8000/api/users/me/', {
          headers: {
            'Authorization': `Bearer ${tokens.access}`
          }
        });
        
        if (userResponse.ok) {
          const userData = await userResponse.json();
          setUser(userData);
        }
        
        return { success: true };
      } else {
        const error = await response.json();
        return { success: false, error };
      }
    } catch (error) {
      console.error('Login error:', error);
      return { success: false, error: 'Network error' };
    }
  };

  const handleLogout = () => {
    authService.removeTokens();
    setIsAuthenticated(false);
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ 
      isAuthenticated, 
      user, 
      loading,
      login: handleLogin,
      logout: handleLogout
    }}>
      {children}
    </AuthContext.Provider>
  );
};
```

### Protected Routes

Use the authentication context to create protected routes:

```javascript
// components/ProtectedRoute.js
import React, { useContext } from 'react';
import { Navigate } from 'react-router-dom';
import { AuthContext } from '../contexts/AuthContext';

const ProtectedRoute = ({ children }) => {
  const { isAuthenticated, loading } = useContext(AuthContext);
  
  if (loading) {
    return <div>Loading...</div>;
  }
  
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }
  
  return children;
};

export default ProtectedRoute;
```

### Using Protected Routes

```javascript
// App.js
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import { AuthProvider } from './contexts/AuthContext';
import ProtectedRoute from './components/ProtectedRoute';
import Login from './pages/Login';
import Dashboard from './pages/Dashboard';
import Home from './pages/Home';

function App() {
  return (
    <AuthProvider>
      <BrowserRouter>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/login" element={<Login />} />
          <Route path="/dashboard" element={
            <ProtectedRoute>
              <Dashboard />
            </ProtectedRoute>
          } />
        </Routes>
      </BrowserRouter>
    </AuthProvider>
  );
}

export default App;
```

## Security Considerations

1. **Store tokens securely**: Consider using in-memory storage or HTTP-only cookies for production
2. **Implement token refresh**: Use the refresh token to get a new access token when it expires
3. **Set appropriate token lifetimes**: Short-lived access tokens enhance security
4. **Use HTTPS**: Always use HTTPS in production environments
5. **Validate tokens on the server**: Ensure tokens are valid and not expired
6. **Implement proper logout**: Clear tokens and invalidate them on the server

## Next Steps

After implementing authentication, proceed to the [Data Flow](../data-flow) section to learn how to manage API requests and responses between your frontend and backend. 