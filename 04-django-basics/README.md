# Week 4: Django Basics

This section focuses on Django, a high-level Python web framework that encourages rapid development and clean, pragmatic design. You'll learn how to build robust web applications with Django's powerful features.

## Learning Path

| Day | Topic | Description |
|-----|-------|-------------|
| 22-23 | [Introduction to Django](day-22-23-intro/README.md) | Setting up Django, understanding the MVT architecture, and creating your first app |
| 24-25 | [Models & Databases](day-24-25-models/README.md) | Defining data models, working with Django ORM, and managing database migrations |
| 26-27 | [Views & Templates](day-26-27-views-templates/README.md) | Creating views, template rendering, and URL routing |
| 28-29 | [Forms & User Authentication](day-28-29-forms-auth/README.md) | Building forms, form validation, user authentication, and permissions |

## Why Django Matters

Django is a complete web framework that follows the "batteries-included" philosophy, providing everything developers need to build web applications quickly without reinventing the wheel. It emphasizes scalability, security, and maintainability, making it an excellent choice for full-stack development.

## What You'll Learn

- Django's Model-View-Template (MVT) architecture
- Database modeling and migrations with Django ORM
- Building views and templates for rendering HTML
- Creating and processing forms with validation
- Implementing user authentication and permissions
- Best practices for Django application structure

## Prerequisites

Before starting this section, you should be comfortable with:

- Python basics
- HTML and CSS fundamentals (Week 1)
- Basic understanding of databases and SQL
- HTTP and web request/response cycle

## Projects

Throughout this section, you'll build:

1. A blog application with posts and comments
2. A user authentication system
3. A data-driven dashboard

## Django's Role in Full-Stack Development

```
                                  ┌─────────────────┐
                                  │ Django Backend  │
┌──────────────┐                  ├─────────────────┤                  ┌──────────────┐
│              │                  │    Models       │                  │              │
│  Frontend    │                  │    Views        │                  │  Database    │
│  (React)     │◄────REST API────►│    Templates    │◄────ORM─────────►│  (PostgreSQL)│
│              │                  │    Forms        │                  │              │
└──────────────┘                  │    Auth         │                  └──────────────┘
                                  └─────────────────┘
                                          │
                                          ▼
                                  ┌─────────────────┐
                                  │    Admin        │
                                  │    Interface    │
                                  └─────────────────┘
```

Django provides several key components for full-stack development:

1. **Models**: Define your data structure and interact with databases
2. **Views**: Handle requests, process data, and return responses
3. **Templates**: Render HTML for the browser
4. **Forms**: Process and validate user-submitted data
5. **Authentication**: Manage users, sessions, and permissions
6. **Admin Interface**: Automatic admin UI for your models

## Resources

- [Django Official Documentation](https://docs.djangoproject.com/)
- [Django Tutorial](https://docs.djangoproject.com/en/stable/intro/tutorial01/)
- [Django for Beginners](https://djangoforbeginners.com/) (Book)
- [Django Girls Tutorial](https://tutorial.djangogirls.org/)
- [MDN Django Web Framework](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django) 