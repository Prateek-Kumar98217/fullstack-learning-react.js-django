# Django Blog Project

This project guides you through building a full-featured blog application using Django. It focuses on server-side development with Django's powerful features for content management, user authentication, and more.

## Project Goals

- Build a complete blog application using Django
- Implement user authentication and authorization
- Create data models with Django's ORM
- Use Django's template system for dynamic content
- Implement CRUD operations for blog posts
- Learn Django's form handling and validation
- Apply best practices for Django development

## Features

- User registration and authentication
- Create, read, update, and delete blog posts
- Comment system for posts
- User profiles with avatars
- Categories and tags for posts
- Search functionality
- Pagination for post lists
- Admin dashboard for content management
- Markdown support for post content
- Featured images for posts
- Social media sharing

## Technology Stack

- Django
- Python
- SQLite (development) / PostgreSQL (production)
- HTML/CSS
- Bootstrap for styling
- JavaScript (minimal)

## Project Structure

```
django-blog/
├── blog/                      # Main Django project
│   ├── manage.py              # Django management script
│   ├── blog/                  # Project configuration
│   │   ├── __init__.py
│   │   ├── settings.py        # Project settings
│   │   ├── urls.py            # URL configuration
│   │   ├── asgi.py            # ASGI configuration
│   │   └── wsgi.py            # WSGI configuration
│   ├── posts/                 # Posts app
│   │   ├── migrations/        # Database migrations
│   │   ├── __init__.py
│   │   ├── admin.py           # Admin interface
│   │   ├── apps.py            # App configuration
│   │   ├── models.py          # Data models
│   │   ├── forms.py           # Forms
│   │   ├── views.py           # Views
│   │   ├── urls.py            # URL patterns
│   │   └── tests.py           # Unit tests
│   ├── accounts/              # User accounts app
│   │   └── ...                # Similar structure as posts
│   ├── comments/              # Comments app
│   │   └── ...                # Similar structure as posts
│   ├── templates/             # HTML templates
│   │   ├── base.html          # Base template
│   │   ├── posts/             # Posts templates
│   │   ├── accounts/          # Accounts templates
│   │   └── comments/          # Comments templates
│   └── static/                # Static files
│       ├── css/               # CSS files
│       ├── js/                # JavaScript files
│       └── images/            # Image files
└── requirements.txt           # Project dependencies
```

## Implementation Steps

### 1. Setting Up the Django Project

- Create a virtual environment:
  ```bash
  python -m venv venv
  source venv/bin/activate  # On Windows: venv\Scripts\activate
  ```

- Install Django:
  ```bash
  pip install django
  ```

- Start a new Django project:
  ```bash
  django-admin startproject blog
  cd blog
  ```

- Create the necessary apps:
  ```bash
  python manage.py startapp posts
  python manage.py startapp accounts
  python manage.py startapp comments
  ```

- Update `settings.py` to include the new apps

### 2. Define Data Models

- Create the Post model:
  ```python
  # posts/models.py
  from django.db import models
  from django.conf import settings
  from django.urls import reverse
  from django.utils import timezone

  class Category(models.Model):
      name = models.CharField(max_length=100, unique=True)
      slug = models.SlugField(max_length=100, unique=True)
      description = models.TextField(blank=True)

      class Meta:
          verbose_name_plural = 'categories'

      def __str__(self):
          return self.name

      def get_absolute_url(self):
          return reverse('category_detail', kwargs={'slug': self.slug})

  class Post(models.Model):
      title = models.CharField(max_length=200)
      slug = models.SlugField(max_length=200, unique=True)
      author = models.ForeignKey(
          settings.AUTH_USER_MODEL,
          on_delete=models.CASCADE,
          related_name='blog_posts'
      )
      content = models.TextField()
      featured_image = models.ImageField(upload_to='featured_images/', blank=True, null=True)
      excerpt = models.TextField(blank=True)
      category = models.ForeignKey(
          Category,
          on_delete=models.SET_NULL,
          related_name='posts',
          null=True,
          blank=True
      )
      created_date = models.DateTimeField(default=timezone.now)
      published_date = models.DateTimeField(blank=True, null=True)
      updated_date = models.DateTimeField(auto_now=True)
      status = models.CharField(
          max_length=10,
          choices=[('draft', 'Draft'), ('published', 'Published')],
          default='draft'
      )

      class Meta:
          ordering = ['-published_date']
          indexes = [models.Index(fields=['-published_date'])]

      def __str__(self):
          return self.title

      def get_absolute_url(self):
          return reverse('post_detail', kwargs={'slug': self.slug})

      def publish(self):
          self.published_date = timezone.now()
          self.status = 'published'
          self.save()
  ```

- Create the Comment model:
  ```python
  # comments/models.py
  from django.db import models
  from django.conf import settings
  from posts.models import Post

  class Comment(models.Model):
      post = models.ForeignKey(
          Post,
          on_delete=models.CASCADE,
          related_name='comments'
      )
      author = models.ForeignKey(
          settings.AUTH_USER_MODEL,
          on_delete=models.CASCADE,
          related_name='comments'
      )
      content = models.TextField()
      created_date = models.DateTimeField(auto_now_add=True)
      approved = models.BooleanField(default=False)

      class Meta:
          ordering = ['created_date']

      def __str__(self):
          return f'Comment by {self.author} on {self.post}'

      def approve(self):
          self.approved = True
          self.save()
  ```

- Create a custom User Profile model:
  ```python
  # accounts/models.py
  from django.db import models
  from django.conf import settings
  from django.contrib.auth.models import User
  from django.db.models.signals import post_save
  from django.dispatch import receiver

  class Profile(models.Model):
      user = models.OneToOneField(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
      bio = models.TextField(max_length=500, blank=True)
      location = models.CharField(max_length=100, blank=True)
      birth_date = models.DateField(null=True, blank=True)
      avatar = models.ImageField(upload_to='avatars/', null=True, blank=True)
      website = models.URLField(blank=True)

      def __str__(self):
          return f'{self.user.username} Profile'

  @receiver(post_save, sender=User)
  def create_user_profile(sender, instance, created, **kwargs):
      if created:
          Profile.objects.create(user=instance)

  @receiver(post_save, sender=User)
  def save_user_profile(sender, instance, **kwargs):
      instance.profile.save()
  ```

### 3. Create Views and Templates

- Implement views for posts:
  ```python
  # posts/views.py
  from django.shortcuts import render, get_object_or_404, redirect
  from django.contrib.auth.decorators import login_required
  from django.utils import timezone
  from .models import Post, Category
  from .forms import PostForm
  from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger

  def post_list(request):
      posts = Post.objects.filter(
          status='published',
          published_date__lte=timezone.now()
      ).order_by('-published_date')
      
      # Pagination
      paginator = Paginator(posts, 5)  # Show 5 posts per page
      page = request.GET.get('page')
      
      try:
          posts = paginator.page(page)
      except PageNotAnInteger:
          # If page is not an integer, deliver first page
          posts = paginator.page(1)
      except EmptyPage:
          # If page is out of range, deliver last page of results
          posts = paginator.page(paginator.num_pages)
          
      return render(request, 'posts/post_list.html', {'posts': posts})

  def post_detail(request, slug):
      post = get_object_or_404(Post, slug=slug)
      return render(request, 'posts/post_detail.html', {'post': post})

  @login_required
  def post_new(request):
      if request.method == "POST":
          form = PostForm(request.POST, request.FILES)
          if form.is_valid():
              post = form.save(commit=False)
              post.author = request.user
              post.save()
              return redirect('post_detail', slug=post.slug)
      else:
          form = PostForm()
      return render(request, 'posts/post_edit.html', {'form': form})

  @login_required
  def post_edit(request, slug):
      post = get_object_or_404(Post, slug=slug)
      if request.user != post.author:
          return redirect('post_detail', slug=post.slug)
          
      if request.method == "POST":
          form = PostForm(request.POST, request.FILES, instance=post)
          if form.is_valid():
              post = form.save(commit=False)
              post.updated_date = timezone.now()
              post.save()
              return redirect('post_detail', slug=post.slug)
      else:
          form = PostForm(instance=post)
      return render(request, 'posts/post_edit.html', {'form': form})

  @login_required
  def post_draft_list(request):
      posts = Post.objects.filter(
          author=request.user,
          status='draft'
      ).order_by('-created_date')
      return render(request, 'posts/post_draft_list.html', {'posts': posts})

  @login_required
  def post_publish(request, slug):
      post = get_object_or_404(Post, slug=slug)
      if request.user != post.author:
          return redirect('post_detail', slug=post.slug)
          
      post.publish()
      return redirect('post_detail', slug=post.slug)

  @login_required
  def post_delete(request, slug):
      post = get_object_or_404(Post, slug=slug)
      if request.user != post.author:
          return redirect('post_detail', slug=post.slug)
          
      post.delete()
      return redirect('post_list')
  ```

- Create template files for the views

### 4. Set Up Authentication and User Accounts

- Implement user registration, login, and profile views
- Create templates for user authentication
- Set up authentication URLs

### 5. Implement the Comment System

- Create views and forms for comments
- Implement comment approval mechanism
- Add comment templates

### 6. Add Styling and Finalize

- Apply Bootstrap styling
- Implement responsive design
- Add social sharing functionality
- Implement search functionality

## Learning Objectives

- Django MVT (Model-View-Template) architecture
- Database modeling with Django ORM
- Django forms and form validation
- User authentication and authorization
- Django admin interface customization
- Template inheritance and reuse
- Django's class-based views vs. function-based views
- URL routing and reverse lookups
- Django signals
- Media file handling

## Next Steps

After completing this project, you can enhance it with:
- RESTful API using Django REST Framework
- Social authentication (login with Google, Facebook, etc.)
- Advanced text editor with WYSIWYG functionality
- Email notifications for new comments
- Tag system for posts
- Related posts functionality
- RSS feed
- Site-wide search with advanced filtering
- SEO optimizations
- Analytics integration 