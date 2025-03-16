# Days 28-29: Advanced API Patterns

## Learning Objectives

By the end of this section, you will:

- Understand and implement GraphQL as an alternative to REST
- Set up real-time communication with WebSockets
- Implement advanced authentication patterns
- Design and implement API versioning strategies
- Use advanced patterns like CQRS and Event Sourcing
- Implement API testing and documentation best practices

## Introduction to Advanced API Patterns

As applications grow in complexity, traditional RESTful APIs can become limiting. This section explores advanced patterns that help build more scalable, efficient, and maintainable APIs.

## GraphQL

GraphQL is a query language for APIs that enables clients to request exactly the data they need, nothing more and nothing less.

### Key Benefits of GraphQL

1. **Precise Data Fetching**: Clients specify exactly what they need
2. **Single Request**: Get multiple resources in a single request
3. **Strong Typing**: Schema defines available data and operations
4. **Introspection**: API is self-documenting
5. **Versioning**: Evolve API without versions

### GraphQL with Django

```bash
pip install graphene-django
```

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'graphene_django',
]

GRAPHENE = {
    'SCHEMA': 'myapp.schema.schema'
}
```

### Schema Definition

```python
# schema.py
import graphene
from graphene_django import DjangoObjectType
from .models import Post, Comment

class PostType(DjangoObjectType):
    class Meta:
        model = Post
        fields = ("id", "title", "content", "created_at", "author", "comments")

class CommentType(DjangoObjectType):
    class Meta:
        model = Comment
        fields = ("id", "text", "created_at", "author", "post")

class Query(graphene.ObjectType):
    posts = graphene.List(PostType)
    post = graphene.Field(PostType, id=graphene.ID(required=True))
    comments_by_post = graphene.List(CommentType, post_id=graphene.ID(required=True))

    def resolve_posts(self, info):
        return Post.objects.all()

    def resolve_post(self, info, id):
        return Post.objects.get(pk=id)
    
    def resolve_comments_by_post(self, info, post_id):
        return Comment.objects.filter(post_id=post_id)

schema = graphene.Schema(query=Query)
```

### Mutations

```python
class CreatePostMutation(graphene.Mutation):
    class Arguments:
        title = graphene.String(required=True)
        content = graphene.String(required=True)

    post = graphene.Field(PostType)

    @classmethod
    def mutate(cls, root, info, title, content):
        # Assume we've added authentication
        user = info.context.user
        post = Post(title=title, content=content, author=user)
        post.save()
        return CreatePostMutation(post=post)

class Mutation(graphene.ObjectType):
    create_post = CreatePostMutation.Field()

schema = graphene.Schema(query=Query, mutation=Mutation)
```

### URL Configuration

```python
# urls.py
from django.urls import path
from graphene_django.views import GraphQLView
from django.views.decorators.csrf import csrf_exempt

urlpatterns = [
    path("graphql", csrf_exempt(GraphQLView.as_view(graphiql=True))),
]
```

### Consuming GraphQL in React

```bash
npm install @apollo/client graphql
```

```jsx
// App.jsx
import { ApolloClient, InMemoryCache, ApolloProvider } from '@apollo/client';

const client = new ApolloClient({
  uri: 'http://localhost:8000/graphql',
  cache: new InMemoryCache(),
});

function App() {
  return (
    <ApolloProvider client={client}>
      <div className="App">
        {/* Your components */}
      </div>
    </ApolloProvider>
  );
}
```

### Queries and Mutations

```jsx
import { gql, useQuery, useMutation } from '@apollo/client';

const GET_POSTS = gql`
  query GetPosts {
    posts {
      id
      title
      content
      createdAt
      author {
        username
      }
    }
  }
`;

const CREATE_POST = gql`
  mutation CreatePost($title: String!, $content: String!) {
    createPost(title: $title, content: $content) {
      post {
        id
        title
        content
      }
    }
  }
`;

function PostsList() {
  const { loading, error, data } = useQuery(GET_POSTS);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {data.posts.map(post => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </li>
        ))}
      </ul>
    </div>
  );
}

function CreatePostForm() {
  const [title, setTitle] = useState('');
  const [content, setContent] = useState('');
  const [createPost, { loading, error }] = useMutation(CREATE_POST, {
    refetchQueries: [{ query: GET_POSTS }],
  });

  const handleSubmit = e => {
    e.preventDefault();
    createPost({ variables: { title, content } });
    setTitle('');
    setContent('');
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Form fields */}
    </form>
  );
}
```

## Real-time APIs with WebSockets

WebSockets provide full-duplex communication channels over a single TCP connection, enabling real-time data exchange.

### Django Channels

```bash
pip install channels channels-redis
```

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'channels',
]

ASGI_APPLICATION = 'myproject.asgi.application'
CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {
            "hosts": [('127.0.0.1', 6379)],
        },
    },
}
```

### Consumers

```python
# consumers.py
import json
from channels.generic.websocket import AsyncWebsocketConsumer
from channels.db import database_sync_to_async
from .models import Post, Comment

class CommentConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.post_id = self.scope['url_route']['kwargs']['post_id']
        self.room_group_name = f'post_{self.post_id}'

        # Join room group
        await self.channel_layer.group_add(
            self.room_group_name,
            self.channel_name
        )

        await self.accept()

    async def disconnect(self, close_code):
        # Leave room group
        await self.channel_layer.group_discard(
            self.room_group_name,
            self.channel_name
        )

    # Receive message from WebSocket
    async def receive(self, text_data):
        data = json.loads(text_data)
        comment_text = data['text']
        user_id = self.scope['user'].id

        # Save comment to database
        comment = await self.create_comment(comment_text, user_id, self.post_id)

        # Send message to room group
        await self.channel_layer.group_send(
            self.room_group_name,
            {
                'type': 'comment_message',
                'id': comment.id,
                'text': comment.text,
                'created_at': comment.created_at.isoformat(),
                'author': comment.author.username,
            }
        )

    # Receive message from room group
    async def comment_message(self, event):
        # Send message to WebSocket
        await self.send(text_data=json.dumps({
            'id': event['id'],
            'text': event['text'],
            'created_at': event['created_at'],
            'author': event['author'],
        }))

    @database_sync_to_async
    def create_comment(self, text, user_id, post_id):
        from django.contrib.auth.models import User
        user = User.objects.get(id=user_id)
        post = Post.objects.get(id=post_id)
        comment = Comment.objects.create(
            text=text,
            author=user,
            post=post
        )
        return comment
```

### Routing

```python
# routing.py
from django.urls import re_path
from . import consumers

websocket_urlpatterns = [
    re_path(r'ws/posts/(?P<post_id>\w+)/comments/$', consumers.CommentConsumer.as_asgi()),
]
```

### ASGI Configuration

```python
# asgi.py
import os
from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack
from myapp.routing import websocket_urlpatterns

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

application = ProtocolTypeRouter({
    "http": get_asgi_application(),
    "websocket": AuthMiddlewareStack(
        URLRouter(
            websocket_urlpatterns
        )
    ),
})
```

### Using WebSockets in React

```jsx
import { useState, useEffect } from 'react';

function LiveComments({ postId }) {
  const [comments, setComments] = useState([]);
  const [newComment, setNewComment] = useState('');
  const [socket, setSocket] = useState(null);

  useEffect(() => {
    // Initial fetch of comments
    fetch(`/api/posts/${postId}/comments/`)
      .then(response => response.json())
      .then(data => setComments(data));

    // Connect to WebSocket
    const newSocket = new WebSocket(`ws://localhost:8000/ws/posts/${postId}/comments/`);
    
    newSocket.onopen = () => {
      console.log('WebSocket Connected');
    };
    
    newSocket.onmessage = (e) => {
      const data = JSON.parse(e.data);
      setComments(comments => [...comments, data]);
    };
    
    newSocket.onclose = () => {
      console.log('WebSocket Disconnected');
    };
    
    setSocket(newSocket);
    
    // Clean up on unmount
    return () => {
      newSocket.close();
    };
  }, [postId]);

  const sendComment = (e) => {
    e.preventDefault();
    if (socket && newComment) {
      socket.send(JSON.stringify({
        text: newComment
      }));
      setNewComment('');
    }
  };

  return (
    <div>
      <h2>Comments</h2>
      <ul>
        {comments.map(comment => (
          <li key={comment.id}>
            <p>{comment.text}</p>
            <small>By {comment.author} on {new Date(comment.created_at).toLocaleString()}</small>
          </li>
        ))}
      </ul>
      <form onSubmit={sendComment}>
        <input
          type="text"
          value={newComment}
          onChange={(e) => setNewComment(e.target.value)}
          placeholder="Write a comment..."
        />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

## Advanced Authentication Patterns

### OAuth 2.0 with Django

```bash
pip install django-oauth-toolkit
```

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'oauth2_provider',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'oauth2_provider.contrib.rest_framework.OAuth2Authentication',
    ]
}
```

### URLs

```python
# urls.py
from django.urls import path, include

urlpatterns = [
    # ...
    path('o/', include('oauth2_provider.urls', namespace='oauth2_provider')),
]
```

### Creating OAuth Applications

```python
from oauth2_provider.models import Application
from django.contrib.auth.models import User

user = User.objects.get(username="admin")
app = Application.objects.create(
    name="My App",
    client_type=Application.CLIENT_CONFIDENTIAL,
    authorization_grant_type=Application.GRANT_AUTHORIZATION_CODE,
    user=user
)
```

### OAuth Flow in React

```jsx
function OAuthLogin() {
  const handleLogin = () => {
    const clientId = 'your-client-id';
    const redirectUri = 'http://localhost:3000/callback';
    window.location.href = `http://localhost:8000/o/authorize/?response_type=code&client_id=${clientId}&redirect_uri=${redirectUri}`;
  };

  return (
    <button onClick={handleLogin}>Login with OAuth</button>
  );
}

function OAuthCallback() {
  const [token, setToken] = useState(null);
  const location = useLocation();
  
  useEffect(() => {
    const code = new URLSearchParams(location.search).get('code');
    
    if (code) {
      const clientId = 'your-client-id';
      const clientSecret = 'your-client-secret';
      const redirectUri = 'http://localhost:3000/callback';
      
      fetch('http://localhost:8000/o/token/', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded',
        },
        body: new URLSearchParams({
          grant_type: 'authorization_code',
          code,
          redirect_uri: redirectUri,
          client_id: clientId,
          client_secret: clientSecret,
        }),
      })
      .then(response => response.json())
      .then(data => {
        setToken(data.access_token);
        localStorage.setItem('token', data.access_token);
        localStorage.setItem('refresh_token', data.refresh_token);
      });
    }
  }, [location]);

  if (!token) return <p>Processing login...</p>;
  
  return <Redirect to="/" />;
}
```

## API Versioning

### URL-Based Versioning

```python
# urls.py
urlpatterns = [
    path('api/v1/', include('api.v1.urls')),
    path('api/v2/', include('api.v2.urls')),
]
```

### Header-Based Versioning

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_VERSIONING_CLASS': 'rest_framework.versioning.AcceptHeaderVersioning',
    'DEFAULT_VERSION': '1.0',
    'ALLOWED_VERSIONS': ['1.0', '2.0'],
    'VERSION_PARAM': 'version',
}
```

```python
# views.py
from rest_framework.views import APIView
from rest_framework.response import Response

class ExampleView(APIView):
    def get(self, request, format=None):
        if request.version == '1.0':
            return Response({'message': 'This is version 1.0'})
        else:
            return Response({'message': 'This is version 2.0', 'extra': 'New field'})
```

## CQRS (Command Query Responsibility Segregation)

CQRS separates read and write operations into different models.

```python
# commands.py
from dataclasses import dataclass
from django.db import transaction

@dataclass
class CreatePost:
    title: str
    content: str
    author_id: int

    def execute(self):
        from .models import Post
        from django.contrib.auth.models import User
        
        with transaction.atomic():
            author = User.objects.get(id=self.author_id)
            post = Post.objects.create(
                title=self.title,
                content=self.content,
                author=author
            )
            return post.id

# queries.py
class PostQueries:
    @staticmethod
    def get_post_by_id(post_id):
        from .models import Post
        return Post.objects.get(id=post_id)
    
    @staticmethod
    def get_posts_with_comments_count():
        from .models import Post
        from django.db.models import Count
        return Post.objects.annotate(comments_count=Count('comments')).all()

# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from .commands import CreatePost
from .queries import PostQueries
from .serializers import PostSerializer

class PostsView(APIView):
    def get(self, request):
        posts = PostQueries.get_posts_with_comments_count()
        serializer = PostSerializer(posts, many=True)
        return Response(serializer.data)
    
    def post(self, request):
        command = CreatePost(
            title=request.data.get('title'),
            content=request.data.get('content'),
            author_id=request.user.id
        )
        post_id = command.execute()
        post = PostQueries.get_post_by_id(post_id)
        serializer = PostSerializer(post)
        return Response(serializer.data, status=201)
```

## API Testing Strategies

### Integration Tests with Django REST Framework

```python
from rest_framework.test import APITestCase
from rest_framework import status
from django.urls import reverse
from django.contrib.auth.models import User
from .models import Post

class PostAPITests(APITestCase):
    def setUp(self):
        self.user = User.objects.create_user(username='testuser', password='testpass')
        self.client.login(username='testuser', password='testpass')
        
        # Create sample posts
        Post.objects.create(title='Test Post 1', content='Content 1', author=self.user)
        Post.objects.create(title='Test Post 2', content='Content 2', author=self.user)
    
    def test_list_posts(self):
        url = reverse('post-list')
        response = self.client.get(url)
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertEqual(len(response.data), 2)
    
    def test_create_post(self):
        url = reverse('post-list')
        data = {'title': 'New Post', 'content': 'New Content'}
        response = self.client.post(url, data, format='json')
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(Post.objects.count(), 3)
        self.assertEqual(Post.objects.latest('id').title, 'New Post')
        self.assertEqual(Post.objects.latest('id').author, self.user)
```

### Contract Testing with Pact

```bash
pip install pytest-django pytest-pact
```

```python
# conftest.py
import pytest
from pact import Consumer, Provider

@pytest.fixture
def client():
    return Consumer('FrontendApp').has_pact_with(Provider('BlogAPI'))

# test_pact.py
def test_get_posts(client):
    expected = [
        {'id': 1, 'title': 'Post 1', 'content': 'Content 1'},
        {'id': 2, 'title': 'Post 2', 'content': 'Content 2'}
    ]
    
    (client
        .given('posts exist')
        .upon_receiving('a request for all posts')
        .with_request('get', '/api/posts/')
        .will_respond_with(200, body=expected))
    
    with client:
        # Your API client call that would be used in your frontend
        response = requests.get(client.uri + '/api/posts/')
        assert response.status_code == 200
        assert response.json() == expected
```

## Comprehensive API Documentation

### OpenAPI with drf-spectacular

```bash
pip install drf-spectacular
```

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'drf_spectacular',
]

REST_FRAMEWORK = {
    # ...
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

SPECTACULAR_SETTINGS = {
    'TITLE': 'Blog API',
    'DESCRIPTION': 'A comprehensive API for a blog application',
    'VERSION': '1.0.0',
    'SERVE_INCLUDE_SCHEMA': False,
}

# urls.py
from drf_spectacular.views import SpectacularAPIView, SpectacularRedocView, SpectacularSwaggerView

urlpatterns = [
    # ...
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    path('api/docs/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'),
    path('api/redoc/', SpectacularRedocView.as_view(url_name='schema'), name='redoc'),
]
```

### Documenting ViewSets and Views

```python
from drf_spectacular.utils import extend_schema, OpenApiParameter, OpenApiExample

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer

    @extend_schema(
        summary="List all posts",
        description="Retrieve a list of all blog posts with pagination",
        responses={200: PostSerializer(many=True)},
        parameters=[
            OpenApiParameter(name='page', description='Page number', required=False, type=int),
            OpenApiParameter(name='search', description='Search term', required=False, type=str),
        ]
    )
    def list(self, request, *args, **kwargs):
        return super().list(request, *args, **kwargs)
    
    @extend_schema(
        summary="Create a new post",
        description="Create a new blog post. User must be authenticated.",
        request=PostSerializer,
        responses={201: PostSerializer},
        examples=[
            OpenApiExample(
                'Valid request example',
                value={
                    'title': 'My Blog Post',
                    'content': 'This is the content of my post'
                },
                request_only=True,
            ),
        ]
    )
    def create(self, request, *args, **kwargs):
        return super().create(request, *args, **kwargs)
```

## Practical Exercises

### Exercise 1: GraphQL API
Convert one of your REST endpoints to GraphQL, implementing both queries and mutations.

### Exercise 2: Real-time Comments
Implement a real-time comment system using WebSockets that shows new comments instantly without refreshing.

### Exercise 3: API Versioning
Implement versioning for your API and create a v2 with additional features.

### Exercise 4: Advanced Authentication
Implement OAuth 2.0 authentication for your API and create a client application that uses it.

## Mini-Project: Advanced API Platform

Extend your product catalog API from Days 24-25 with:

1. GraphQL endpoint alongside REST
2. Real-time product inventory updates with WebSockets
3. OAuth 2.0 authentication
4. API versioning (v1 and v2)
5. Comprehensive API documentation
6. Contract tests for frontend/backend integration

Technical requirements:
- Use Django with Graphene for GraphQL
- Implement Django Channels for WebSockets
- Use drf-spectacular for API documentation
- Implement proper testing for all components
- Create a simple React client that demonstrates all features

## Additional Resources

- [Graphene Django Documentation](https://docs.graphene-python.org/projects/django/en/latest/)
- [Django Channels Documentation](https://channels.readthedocs.io/en/stable/)
- [Django OAuth Toolkit](https://django-oauth-toolkit.readthedocs.io/)
- [Apollo Client (React)](https://www.apollographql.com/docs/react/)
- [drf-spectacular Documentation](https://drf-spectacular.readthedocs.io/) 