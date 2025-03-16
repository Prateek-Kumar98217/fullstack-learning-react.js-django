# Days 24-25: Django REST Framework

## Learning Objectives

By the end of this section, you will:

- Build robust API endpoints using Django REST Framework
- Implement serialization for converting Django models to JSON
- Create viewsets and routers for CRUD operations
- Add filtering, pagination, and sorting
- Implement token-based authentication
- Document your API using Swagger/OpenAPI

## Introduction to Django REST Framework

Django REST Framework (DRF) is a powerful toolkit for building Web APIs on top of Django. It provides:

- Serialization that supports both ORM and non-ORM data sources
- Class-based views for handling CRUD operations
- Authentication and permissions policies
- Browsable API with web interface for easy testing

## Setting Up Django REST Framework

### Installation

```bash
pip install djangorestframework

# Optional but recommended packages
pip install markdown       # Markdown support for the browsable API
pip install django-filter  # Filtering support
pip install drf-yasg       # Swagger/OpenAPI documentation
```

### Configuration

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'rest_framework',
    'django_filters',
    'drf_yasg',
]

REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
    'DEFAULT_FILTER_BACKENDS': [
        'django_filters.rest_framework.DjangoFilterBackend',
        'rest_framework.filters.SearchFilter',
        'rest_framework.filters.OrderingFilter',
    ],
}
```

## Models

Let's create a simple blog application with posts and comments:

```python
# models.py
from django.db import models
from django.contrib.auth.models import User

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    
    def __str__(self):
        return self.title

class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    text = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='comments')
    
    def __str__(self):
        return f'Comment by {self.author.username} on {self.post.title}'
```

## Serializers

Serializers convert Django models to JSON and validate incoming data:

```python
# serializers.py
from rest_framework import serializers
from .models import Post, Comment
from django.contrib.auth.models import User

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']

class CommentSerializer(serializers.ModelSerializer):
    author = UserSerializer(read_only=True)
    
    class Meta:
        model = Comment
        fields = ['id', 'text', 'created_at', 'author', 'post']
        read_only_fields = ['author']
    
    def create(self, validated_data):
        validated_data['author'] = self.context['request'].user
        return super().create(validated_data)

class PostSerializer(serializers.ModelSerializer):
    author = UserSerializer(read_only=True)
    comments = CommentSerializer(many=True, read_only=True)
    comment_count = serializers.IntegerField(source='comments.count', read_only=True)
    
    class Meta:
        model = Post
        fields = ['id', 'title', 'content', 'created_at', 'author', 'comments', 'comment_count']
        read_only_fields = ['author']
    
    def create(self, validated_data):
        validated_data['author'] = self.context['request'].user
        return super().create(validated_data)
```

## Views

DRF offers multiple view types. Here we'll use ViewSets:

```python
# views.py
from rest_framework import viewsets, permissions, filters
from django_filters.rest_framework import DjangoFilterBackend
from .models import Post, Comment
from .serializers import PostSerializer, CommentSerializer
from .permissions import IsAuthorOrReadOnly

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly, IsAuthorOrReadOnly]
    filter_backends = [DjangoFilterBackend, filters.SearchFilter, filters.OrderingFilter]
    filterset_fields = ['author']
    search_fields = ['title', 'content']
    ordering_fields = ['created_at', 'title']

class CommentViewSet(viewsets.ModelViewSet):
    queryset = Comment.objects.all()
    serializer_class = CommentSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly, IsAuthorOrReadOnly]
    filter_backends = [DjangoFilterBackend]
    filterset_fields = ['post', 'author']
```

## Custom Permissions

```python
# permissions.py
from rest_framework import permissions

class IsAuthorOrReadOnly(permissions.BasePermission):
    """
    Custom permission to only allow authors of an object to edit it.
    """
    def has_object_permission(self, request, view, obj):
        # Read permissions are allowed to any request
        if request.method in permissions.SAFE_METHODS:
            return True

        # Write permissions are only allowed to the author
        return obj.author == request.user
```

## URLs and Routers

DRF Routers automatically generate URLs for ViewSets:

```python
# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import PostViewSet, CommentViewSet

router = DefaultRouter()
router.register(r'posts', PostViewSet)
router.register(r'comments', CommentViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
    path('api-auth/', include('rest_framework.urls')),
]
```

## Authentication

### Token Authentication

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'rest_framework.authtoken',
]

# urls.py
from rest_framework.authtoken import views as token_views

urlpatterns = [
    # ...
    path('api-token-auth/', token_views.obtain_auth_token),
]
```

### JWT Authentication

```bash
pip install djangorestframework-simplejwt
```

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    # ...
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```

## API Documentation with Swagger

```python
# urls.py
from drf_yasg.views import get_schema_view
from drf_yasg import openapi
from rest_framework import permissions

schema_view = get_schema_view(
   openapi.Info(
      title="Blog API",
      default_version='v1',
      description="A simple blog API",
      terms_of_service="https://www.google.com/policies/terms/",
      contact=openapi.Contact(email="contact@blog.local"),
      license=openapi.License(name="BSD License"),
   ),
   public=True,
   permission_classes=(permissions.AllowAny,),
)

urlpatterns = [
    # ...
    path('swagger/', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
    path('redoc/', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
]
```

## Advanced Features

### Nested Routes

```python
# views.py
from rest_framework.decorators import action
from rest_framework.response import Response

class PostViewSet(viewsets.ModelViewSet):
    # ...
    
    @action(detail=True, methods=['get'])
    def comments(self, request, pk=None):
        post = self.get_object()
        comments = post.comments.all()
        serializer = CommentSerializer(comments, many=True, context={'request': request})
        return Response(serializer.data)
```

### Custom Actions

```python
# views.py
from rest_framework import status

class PostViewSet(viewsets.ModelViewSet):
    # ...
    
    @action(detail=True, methods=['post'], permission_classes=[permissions.IsAuthenticated])
    def like(self, request, pk=None):
        post = self.get_object()
        # Assuming a Like model exists
        like, created = Like.objects.get_or_create(post=post, user=request.user)
        
        if created:
            return Response({'status': 'post liked'}, status=status.HTTP_201_CREATED)
        else:
            return Response({'status': 'already liked'}, status=status.HTTP_200_OK)
```

## Testing Your API

```python
# tests.py
from django.urls import reverse
from rest_framework import status
from rest_framework.test import APITestCase
from django.contrib.auth.models import User
from .models import Post

class PostTests(APITestCase):
    def setUp(self):
        self.user = User.objects.create_user(username='testuser', password='testpassword')
        self.client.login(username='testuser', password='testpassword')
        
    def test_create_post(self):
        url = reverse('post-list')
        data = {'title': 'Test Post', 'content': 'This is a test post'}
        response = self.client.post(url, data, format='json')
        
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(Post.objects.count(), 1)
        self.assertEqual(Post.objects.get().title, 'Test Post')
        self.assertEqual(Post.objects.get().author, self.user)
```

## Practical Exercises

### Exercise 1: Todo API
Build a simple Todo API with:
- Task model with title, description, status, due_date
- CRUD operations
- Filtering by status and due_date
- Search by title

### Exercise 2: Implement Authentication
Add token-based authentication to your Todo API:
- JWT token setup
- User registration
- Login endpoint
- Protect routes

### Exercise 3: API Documentation
Document your Todo API using Swagger:
- Set up drf-yasg
- Generate API documentation
- Customize API schema information

## Mini-Project: Product Catalog API

Build a product catalog API with:

1. Categories (name, description)
2. Products (name, description, price, inventory, category)
3. Reviews (product, rating, comment, user)
4. User authentication with JWT
5. Filtering and searching
6. Documentation with Swagger

Implement:
- CRUD operations for all resources
- Nested routes (e.g., `/api/categories/{id}/products/`)
- Custom actions (e.g., mark product as featured)
- Appropriate permissions
- Comprehensive test suite

## Additional Resources

- [Django REST Framework Documentation](https://www.django-rest-framework.org/)
- [DRF Tutorial](https://www.django-rest-framework.org/tutorial/quickstart/)
- [Simple JWT Documentation](https://django-rest-framework-simplejwt.readthedocs.io/)
- [drf-yasg Documentation](https://drf-yasg.readthedocs.io/)
- [DRF Testing Guide](https://www.django-rest-framework.org/api-guide/testing/) 