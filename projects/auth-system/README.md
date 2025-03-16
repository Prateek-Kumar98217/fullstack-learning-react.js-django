# Authentication System Project

This project guides you through building a comprehensive authentication system with Django, focusing on security best practices, user management, and advanced authentication features.

## Project Goals

- Implement a secure authentication system using Django
- Learn about user authentication and authorization
- Understand password hashing and security
- Implement email verification and password reset
- Create OAuth integration for social logins
- Build user profile management functionality
- Learn about JWT-based authentication for APIs

## Features

- User registration and login
- Email verification for new accounts
- Password reset functionality
- Social authentication (Google, GitHub, etc.)
- Two-factor authentication
- User profile management
- Role-based permissions
- JWT authentication for API access
- Account lockout after failed attempts
- Session management
- Password strength validation
- Admin interface for user management

## Technology Stack

- Django and Django REST Framework
- Python
- PostgreSQL (recommended for production)
- Redis (for session storage and rate limiting)
- HTML/CSS (Bootstrap for the frontend)
- JavaScript (minimal)

## Project Structure

```
auth-system/
├── auth_project/                   # Main Django project
│   ├── manage.py                   # Django management script
│   ├── auth_project/               # Project configuration
│   │   ├── __init__.py
│   │   ├── settings.py             # Project settings
│   │   ├── urls.py                 # URL configuration
│   │   ├── asgi.py                 # ASGI configuration
│   │   └── wsgi.py                 # WSGI configuration
│   ├── accounts/                   # User accounts app
│   │   ├── migrations/             # Database migrations
│   │   ├── __init__.py
│   │   ├── admin.py                # Admin interface
│   │   ├── apps.py                 # App configuration
│   │   ├── models.py               # User and profile models
│   │   ├── forms.py                # Authentication forms
│   │   ├── views.py                # Views
│   │   ├── urls.py                 # URL patterns
│   │   ├── backends.py             # Custom auth backends
│   │   ├── validators.py           # Password validators
│   │   └── tests.py                # Unit tests
│   ├── api/                        # API app
│   │   └── ...                     # API-related files
│   ├── templates/                  # HTML templates
│   │   ├── base.html               # Base template
│   │   ├── accounts/               # Accounts templates
│   │   │   ├── login.html
│   │   │   ├── register.html
│   │   │   ├── password_reset.html
│   │   │   └── profile.html
│   │   └── emails/                 # Email templates
│   └── static/                     # Static files
│       ├── css/                    # CSS files
│       └── js/                     # JavaScript files
└── requirements.txt                # Project dependencies
```

## Implementation Steps

### 1. Setting Up the Django Project

- Create a virtual environment:
  ```bash
  python -m venv venv
  source venv/bin/activate  # On Windows: venv\Scripts\activate
  ```

- Install Django and dependencies:
  ```bash
  pip install django django-allauth django-rest-framework djangorestframework-simplejwt
  pip install django-otp django-two-factor-auth
  ```

- Start a new Django project:
  ```bash
  django-admin startproject auth_project
  cd auth_project
  ```

- Create the accounts app:
  ```bash
  python manage.py startapp accounts
  ```

- Update `settings.py` to include the new apps and authentication backends

### 2. User Model and Authentication

- Extend the Django User model or create a custom user model:
  ```python
  # accounts/models.py
  from django.db import models
  from django.contrib.auth.models import AbstractUser
  from django.utils.translation import gettext_lazy as _

  class User(AbstractUser):
      email = models.EmailField(_('email address'), unique=True)
      email_verified = models.BooleanField(default=False)
      phone_number = models.CharField(max_length=15, blank=True, null=True)
      
      # Use email for login
      USERNAME_FIELD = 'email'
      REQUIRED_FIELDS = ['username']
      
      def __str__(self):
          return self.email

  class Profile(models.Model):
      user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='profile')
      bio = models.TextField(max_length=500, blank=True)
      location = models.CharField(max_length=100, blank=True)
      birth_date = models.DateField(null=True, blank=True)
      avatar = models.ImageField(upload_to='avatars/', null=True, blank=True)
      
      def __str__(self):
          return f'{self.user.username} Profile'
  ```

- Set up a custom authentication backend:
  ```python
  # accounts/backends.py
  from django.contrib.auth.backends import ModelBackend
  from django.contrib.auth import get_user_model
  from django.db.models import Q

  User = get_user_model()

  class EmailBackend(ModelBackend):
      def authenticate(self, request, username=None, password=None, **kwargs):
          try:
              # Get the user by email or username
              user = User.objects.get(
                  Q(username=username) | Q(email=username)
              )
              
              # Check the password
              if user.check_password(password):
                  return user
                  
          except User.DoesNotExist:
              return None
              
      def get_user(self, user_id):
          try:
              return User.objects.get(pk=user_id)
          except User.DoesNotExist:
              return None
  ```

### 3. Registration and Login System

- Create registration and login forms:
  ```python
  # accounts/forms.py
  from django import forms
  from django.contrib.auth.forms import UserCreationForm, AuthenticationForm
  from django.contrib.auth import get_user_model

  User = get_user_model()

  class UserRegistrationForm(UserCreationForm):
      email = forms.EmailField(required=True)
      
      class Meta:
          model = User
          fields = ['username', 'email', 'password1', 'password2']
          
      def save(self, commit=True):
          user = super().save(commit=False)
          user.email = self.cleaned_data['email']
          if commit:
              user.save()
          return user

  class UserLoginForm(AuthenticationForm):
      username = forms.CharField(label='Email / Username')
      
      def __init__(self, *args, **kwargs):
          super().__init__(*args, **kwargs)
          self.fields['username'].widget.attrs.update({'autofocus': True})
  ```

- Implement registration and login views:
  ```python
  # accounts/views.py
  from django.shortcuts import render, redirect
  from django.contrib.auth import login, authenticate, logout
  from django.contrib.auth.decorators import login_required
  from django.contrib import messages
  from .forms import UserRegistrationForm, UserLoginForm

  def register_view(request):
      if request.method == 'POST':
          form = UserRegistrationForm(request.POST)
          if form.is_valid():
              user = form.save()
              # Send verification email
              send_verification_email(user)
              messages.success(request, 'Registration successful! Please check your email to verify your account.')
              return redirect('login')
      else:
          form = UserRegistrationForm()
      return render(request, 'accounts/register.html', {'form': form})

  def login_view(request):
      if request.method == 'POST':
          form = UserLoginForm(request, data=request.POST)
          if form.is_valid():
              username = form.cleaned_data.get('username')
              password = form.cleaned_data.get('password')
              user = authenticate(username=username, password=password)
              if user is not None:
                  login(request, user)
                  return redirect('profile')
      else:
          form = UserLoginForm()
      return render(request, 'accounts/login.html', {'form': form})

  def logout_view(request):
      logout(request)
      return redirect('login')

  @login_required
  def profile_view(request):
      return render(request, 'accounts/profile.html')
  ```

### 4. Email Verification and Password Reset

- Implement email verification functionality
- Set up password reset views and templates
- Configure email settings for sending verification and reset emails

### 5. Social Authentication

- Configure django-allauth for social authentication
- Set up authentication with Google, GitHub, etc.
- Create templates for social login buttons

### 6. Two-Factor Authentication

- Set up django-two-factor-auth
- Implement two-factor authentication views
- Add QR code generation for authenticator apps

### 7. API Authentication with JWT

- Set up Django REST Framework and SimpleJWT
- Create token views for obtaining, refreshing, and verifying tokens
- Implement protected API views using JWT authentication

### 8. Advanced Security Features

- Implement account lockout after failed attempts
- Add password strength validation
- Set up rate limiting for authentication endpoints
- Configure secure cookie settings

## Learning Objectives

- Security best practices for authentication systems
- Django's authentication framework
- Password hashing and verification
- OAuth and OpenID Connect protocols
- JWT-based authentication for APIs
- Two-factor authentication implementation
- Rate limiting and brute force protection
- Session management and security
- Email sending in Django
- User permission systems

## Next Steps

After completing this project, you can enhance it with:
- Single Sign-On (SSO) implementation
- Role-based access control with detailed permissions
- User activity logging and auditing
- Account deactivation and deletion workflows
- Password-less authentication with magic links
- IP-based access restrictions
- GDPR and privacy compliance features
- Security headers and CSP configuration
- Integration with external identity providers
- Advanced monitoring and analytics for authentication events 