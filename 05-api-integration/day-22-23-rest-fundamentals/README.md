# Days 22-23: REST API Fundamentals

## Learning Objectives

By the end of this section, you will:

- Understand the principles and constraints of REST architecture
- Know how to design RESTful endpoints for your applications
- Implement proper HTTP methods and status codes
- Create resource representations and handle relationships
- Document your API for other developers

## What is REST?

REST (Representational State Transfer) is an architectural style for designing networked applications. RESTful APIs use HTTP requests to perform CRUD (Create, Read, Update, Delete) operations on resources, which are represented as URLs.

### Key REST Principles

1. **Client-Server Architecture**: Separation of concerns between client and server
2. **Statelessness**: Each request contains all information needed to complete it
3. **Cacheability**: Responses must define themselves as cacheable or non-cacheable
4. **Uniform Interface**: Standard methods for resource manipulation
5. **Layered System**: Client cannot tell if it's connected directly to the server
6. **Code on Demand** (optional): Server can extend client functionality

## HTTP Methods

RESTful APIs use standard HTTP methods to perform operations on resources:

| Method | Purpose | Example |
|--------|---------|---------|
| GET | Retrieve data | `GET /api/posts` - Get all posts |
| POST | Create data | `POST /api/posts` - Create a new post |
| PUT | Update data (full) | `PUT /api/posts/1` - Replace post #1 |
| PATCH | Update data (partial) | `PATCH /api/posts/1` - Update some fields of post #1 |
| DELETE | Remove data | `DELETE /api/posts/1` - Delete post #1 |

## HTTP Status Codes

Proper status codes communicate the result of a request:

### Success Codes (2xx)
- `200 OK`: Standard success response
- `201 Created`: Resource successfully created
- `204 No Content`: Success but no response body

### Client Error Codes (4xx)
- `400 Bad Request`: Invalid syntax
- `401 Unauthorized`: Authentication required
- `403 Forbidden`: Authenticated but not authorized
- `404 Not Found`: Resource doesn't exist
- `422 Unprocessable Entity`: Validation errors

### Server Error Codes (5xx)
- `500 Internal Server Error`: Unexpected server error
- `503 Service Unavailable`: Server temporarily unavailable

## Resource Design

REST revolves around resources, which should be:

1. **Noun-based**: Use nouns, not verbs (e.g., `/api/posts` not `/api/getPosts`)
2. **Hierarchical**: Express relationships (e.g., `/api/posts/1/comments`)
3. **Plural**: Collections are plural (e.g., `/api/users` not `/api/user`)
4. **Consistent**: Follow same patterns throughout API

### URI Structure Best Practices

```
/api/resource_name            # Collection
/api/resource_name/:id        # Specific resource
/api/resource_name/:id/subresource  # Related resources
```

## Request & Response Formats

### Common Formats
- JSON (JavaScript Object Notation) - Most common
- XML (eXtensible Markup Language)
- YAML (YAML Ain't Markup Language)

### JSON Example

```json
// Request: POST /api/posts
{
  "title": "REST API Basics",
  "content": "This is a post about REST APIs",
  "author_id": 42
}

// Response
{
  "id": 123,
  "title": "REST API Basics",
  "content": "This is a post about REST APIs",
  "author_id": 42,
  "created_at": "2023-07-18T14:30:00Z"
}
```

## Pagination, Filtering & Sorting

For collections with many resources:

### Pagination
```
GET /api/posts?page=2&limit=10
```

### Filtering
```
GET /api/posts?status=published&author_id=42
```

### Sorting
```
GET /api/posts?sort=created_at:desc
```

## API Documentation

Good documentation is crucial for API adoption:

1. **OpenAPI/Swagger**: Industry standard specification
2. **API Blueprint**: Markdown-based documentation
3. **Postman Collections**: Interactive documentation

## Authentication & Authorization

Common methods for API security:

1. **API Keys**: Simple token in header or query parameter
2. **JWT (JSON Web Tokens)**: Encoded tokens with claims
3. **OAuth 2.0**: Industry standard for authorization
4. **Basic Auth**: Username/password encoded in headers

### JWT Example
```
GET /api/posts
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Practical Exercises

### Exercise 1: Design a Blog API
Design the endpoints for a blog API with:
- Posts (with CRUD operations)
- Comments on posts
- User profiles
- Categories for posts

### Exercise 2: HTTP Status Codes
For each scenario, determine the appropriate HTTP status code:
1. User creates a new post successfully
2. User tries to update a post that doesn't exist
3. User tries to access admin resources without proper permissions
4. Server encounters a database error
5. User requests a list of posts with invalid query parameters

### Exercise 3: Design Resource Relationships
Design the endpoints for the following relationships:
1. Posts belong to users
2. Comments belong to posts
3. Posts can have multiple categories
4. Users can like posts

## Mini-Project: Task Management API Design

Design a RESTful API for a task management application with:

1. Users who can create tasks
2. Tasks that belong to projects
3. Tasks that can be assigned to users
4. Tasks with priorities and due dates
5. Comments on tasks

Create a document outlining:
- All API endpoints
- HTTP methods for each endpoint
- Request and response formats
- Status codes for different scenarios
- Authentication strategy

## Additional Resources

- [REST API Tutorial](https://restfulapi.net/)
- [HTTP Status Codes](https://httpstatuses.com/)
- [JSON:API Specification](https://jsonapi.org/)
- [Swagger Documentation](https://swagger.io/docs/)
- [Postman Learning Center](https://learning.postman.com/) 