# Days 26-27: Consuming APIs in React

## Learning Objectives

By the end of this section, you will:

- Implement various methods for fetching data in React applications
- Manage API states (loading, success, error) efficiently
- Implement authentication and authorization for API requests
- Create custom hooks for data fetching
- Use React Query for advanced data management
- Implement real-world patterns like infinite scrolling and optimistic updates

## Introduction to API Consumption in React

Modern React applications typically interact with backend APIs to fetch and manipulate data. This section covers best practices, patterns, and libraries for consuming APIs effectively.

## Basic Data Fetching Methods

### 1. Fetch API

The native browser Fetch API provides a simple way to make HTTP requests:

```jsx
import { useState, useEffect } from 'react';

function PostList() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchPosts = async () => {
      try {
        const response = await fetch('https://api.example.com/posts');
        
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        
        const data = await response.json();
        setPosts(data);
        setLoading(false);
      } catch (error) {
        setError(error.message);
        setLoading(false);
      }
    };

    fetchPosts();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

### 2. Axios

Axios is a popular HTTP client with additional features beyond the Fetch API:

```jsx
import { useState, useEffect } from 'react';
import axios from 'axios';

function PostList() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchPosts = async () => {
      try {
        const response = await axios.get('https://api.example.com/posts');
        setPosts(response.data);
        setLoading(false);
      } catch (error) {
        setError(error.message);
        setLoading(false);
      }
    };

    fetchPosts();
  }, []);

  // Render logic same as before
}
```

## Creating Custom Fetch Hooks

To reuse fetch logic across components, create custom hooks:

```jsx
// hooks/useFetch.js
import { useState, useEffect } from 'react';

export function useFetch(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url, options);
        
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        
        const result = await response.json();
        setData(result);
        setLoading(false);
      } catch (error) {
        setError(error.message);
        setLoading(false);
      }
    };

    fetchData();
  }, [url, JSON.stringify(options)]);

  return { data, loading, error };
}

// Using the hook
function PostList() {
  const { data: posts, loading, error } = useFetch('https://api.example.com/posts');

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

## Handling Authentication

### JWT Authentication in React

```jsx
// hooks/useAuth.js
import { useState } from 'react';
import axios from 'axios';

export function useAuth() {
  const [user, setUser] = useState(null);
  const [token, setToken] = useState(localStorage.getItem('token'));
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  // Configure axios to use the token
  axios.defaults.headers.common['Authorization'] = token ? `Bearer ${token}` : '';

  const login = async (credentials) => {
    setLoading(true);
    setError(null);
    
    try {
      const response = await axios.post('https://api.example.com/api/token/', credentials);
      const { access, refresh, user: userData } = response.data;
      
      setToken(access);
      setUser(userData);
      localStorage.setItem('token', access);
      localStorage.setItem('refreshToken', refresh);
      
      return true;
    } catch (error) {
      setError(error.response?.data?.detail || 'Login failed');
      return false;
    } finally {
      setLoading(false);
    }
  };

  const logout = () => {
    setUser(null);
    setToken(null);
    localStorage.removeItem('token');
    localStorage.removeItem('refreshToken');
    delete axios.defaults.headers.common['Authorization'];
  };

  return { user, login, logout, loading, error };
}
```

## React Query

React Query is a powerful library for managing server state in React:

### Installation

```bash
npm install react-query
# or
yarn add react-query
```

### Basic Setup

```jsx
// App.jsx
import { QueryClient, QueryClientProvider } from 'react-query';
import { ReactQueryDevtools } from 'react-query/devtools';

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      {/* Your app components */}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  );
}
```

### Fetching Data with React Query

```jsx
import { useQuery, useMutation, useQueryClient } from 'react-query';
import axios from 'axios';

// Fetch function
const fetchPosts = async () => {
  const { data } = await axios.get('https://api.example.com/posts');
  return data;
};

function PostList() {
  const { data: posts, isLoading, error } = useQuery('posts', fetchPosts, {
    staleTime: 5 * 60 * 1000, // 5 minutes
    cacheTime: 10 * 60 * 1000, // 10 minutes
  });

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

### Mutations with React Query

```jsx
function CreatePost() {
  const queryClient = useQueryClient();
  const [title, setTitle] = useState('');
  const [content, setContent] = useState('');

  const createPost = async (newPost) => {
    const { data } = await axios.post('https://api.example.com/posts', newPost);
    return data;
  };

  const mutation = useMutation(createPost, {
    onSuccess: () => {
      // Invalidate and refetch
      queryClient.invalidateQueries('posts');
      // Clear form
      setTitle('');
      setContent('');
    },
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    mutation.mutate({ title, content });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={title}
        onChange={(e) => setTitle(e.target.value)}
        placeholder="Title"
      />
      <textarea
        value={content}
        onChange={(e) => setContent(e.target.value)}
        placeholder="Content"
      />
      <button type="submit" disabled={mutation.isLoading}>
        {mutation.isLoading ? 'Creating...' : 'Create Post'}
      </button>
      {mutation.isError && <p>Error: {mutation.error.message}</p>}
    </form>
  );
}
```

## Advanced Patterns

### 1. Infinite Scrolling

```jsx
import { useInfiniteQuery } from 'react-query';
import { useInView } from 'react-intersection-observer';
import { useEffect } from 'react';

function InfinitePostList() {
  const { ref, inView } = useInView();

  const fetchPosts = async ({ pageParam = 1 }) => {
    const response = await axios.get(`https://api.example.com/posts?page=${pageParam}`);
    return response.data;
  };

  const {
    data,
    isLoading,
    isError,
    error,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
  } = useInfiniteQuery('posts', fetchPosts, {
    getNextPageParam: (lastPage, pages) => {
      return lastPage.next_page || undefined;
    },
  });

  useEffect(() => {
    if (inView && hasNextPage) {
      fetchNextPage();
    }
  }, [inView, fetchNextPage, hasNextPage]);

  if (isLoading) return <p>Loading...</p>;
  if (isError) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h1>Posts</h1>
      {data.pages.map((page, i) => (
        <div key={i}>
          {page.results.map(post => (
            <div key={post.id} className="post">
              <h2>{post.title}</h2>
              <p>{post.content}</p>
            </div>
          ))}
        </div>
      ))}
      <div ref={ref}>
        {isFetchingNextPage ? <p>Loading more...</p> : hasNextPage ? <p>Load more</p> : <p>No more posts</p>}
      </div>
    </div>
  );
}
```

### 2. Optimistic Updates

```jsx
function OptimisticUpdateExample() {
  const queryClient = useQueryClient();

  // Toggle like mutation
  const likeMutation = useMutation(
    (postId) => axios.post(`https://api.example.com/posts/${postId}/like`),
    {
      // When mutate is called:
      onMutate: async (postId) => {
        // Cancel outgoing refetches
        await queryClient.cancelQueries('posts');

        // Snapshot the previous value
        const previousPosts = queryClient.getQueryData('posts');

        // Optimistically update
        queryClient.setQueryData('posts', old => {
          return old.map(post => {
            if (post.id === postId) {
              return { ...post, likes: post.likes + 1, isLiked: true };
            }
            return post;
          });
        });

        // Return a context object with the snapshot
        return { previousPosts };
      },
      // If the mutation fails, rollback
      onError: (err, postId, context) => {
        queryClient.setQueryData('posts', context.previousPosts);
      },
      // Always refetch after error or success
      onSettled: () => {
        queryClient.invalidateQueries('posts');
      },
    }
  );

  return (
    <div>
      {posts.map(post => (
        <div key={post.id}>
          <h2>{post.title}</h2>
          <button
            onClick={() => likeMutation.mutate(post.id)}
            disabled={likeMutation.isLoading}
          >
            {post.isLiked ? 'Liked' : 'Like'} ({post.likes})
          </button>
        </div>
      ))}
    </div>
  );
}
```

## Error Handling and Loading States

### Error Boundaries

```jsx
import { Component } from 'react';

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}

function App() {
  return (
    <ErrorBoundary fallback={<p>Failed to load posts. Please try again later.</p>}>
      <PostList />
    </ErrorBoundary>
  );
}
```

### Loading Skeletons

```jsx
function PostSkeleton() {
  return (
    <div className="post-skeleton">
      <div className="skeleton-title"></div>
      <div className="skeleton-content"></div>
    </div>
  );
}

function PostList() {
  const { data: posts, isLoading } = useQuery('posts', fetchPosts);

  return (
    <div>
      <h1>Posts</h1>
      {isLoading ? (
        <div>
          <PostSkeleton />
          <PostSkeleton />
          <PostSkeleton />
        </div>
      ) : (
        <div>
          {posts.map(post => (
            <div key={post.id} className="post">
              <h2>{post.title}</h2>
              <p>{post.content}</p>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}
```

## Testing API Interactions

```jsx
// PostList.test.jsx
import { render, screen, waitFor } from '@testing-library/react';
import { QueryClientProvider, QueryClient } from 'react-query';
import { rest } from 'msw';
import { setupServer } from 'msw/node';
import PostList from './PostList';

// Mock API responses
const server = setupServer(
  rest.get('https://api.example.com/posts', (req, res, ctx) => {
    return res(
      ctx.status(200),
      ctx.json([
        { id: 1, title: 'Post 1', content: 'Content 1' },
        { id: 2, title: 'Post 2', content: 'Content 2' },
      ])
    );
  })
);

// Setup server
beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

test('renders posts after fetching', async () => {
  const queryClient = new QueryClient();
  
  render(
    <QueryClientProvider client={queryClient}>
      <PostList />
    </QueryClientProvider>
  );

  // Initial loading state
  expect(screen.getByText(/loading/i)).toBeInTheDocument();

  // Wait for posts to load
  await waitFor(() => {
    expect(screen.getByText('Post 1')).toBeInTheDocument();
    expect(screen.getByText('Post 2')).toBeInTheDocument();
  });
});

test('handles API errors', async () => {
  // Override the handler for this test
  server.use(
    rest.get('https://api.example.com/posts', (req, res, ctx) => {
      return res(ctx.status(500));
    })
  );

  const queryClient = new QueryClient();
  
  render(
    <QueryClientProvider client={queryClient}>
      <PostList />
    </QueryClientProvider>
  );

  // Wait for error message
  await waitFor(() => {
    expect(screen.getByText(/error/i)).toBeInTheDocument();
  });
});
```

## Practical Exercises

### Exercise 1: Fetch and Display Data
Create a component that fetches and displays a list of items from the Django API you built on Day 24-25. Include loading and error states.

### Exercise 2: Post Data to API
Create a form that allows users to submit data to your API. Implement validation and error handling.

### Exercise 3: Authentication Flow
Implement a complete authentication flow (login, logout, protected routes) using the authentication endpoints from your Django API.

### Exercise 4: React Query Implementation
Refactor your components to use React Query for data fetching, with proper caching and invalidation.

## Mini-Project: Blog Dashboard

Build a blog dashboard that interacts with your Django API from Days 24-25:

1. User authentication with JWT
2. List of posts with pagination
3. Post details view with comments
4. Create/edit/delete posts
5. Add/remove comments
6. User profile view

Technical requirements:
- React Query for data fetching
- Form validation with Formik or React Hook Form
- Error boundaries for error handling
- Loading skeletons for loading states
- Optimistic updates for likes/comments
- Unit tests for key components

## Additional Resources

- [React Query Documentation](https://tanstack.com/query/latest)
- [Axios Documentation](https://axios-http.com/docs/intro)
- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
- [MSW (Mock Service Worker)](https://mswjs.io/)
- [React Error Boundary](https://reactjs.org/docs/error-boundaries.html) 