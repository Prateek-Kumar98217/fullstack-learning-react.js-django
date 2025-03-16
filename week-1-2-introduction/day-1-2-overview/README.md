# Day 1-2: Overview of Full-Stack Development & Tools

## Learning Objectives
By the end of these two days, you will:
- Understand what full-stack development means
- Learn about the technologies we'll be using (React, TypeScript, Django)
- Set up your development environment
- Write your first "Hello, World!" in React and Django

## Common Setup Issues and Solutions

### Node.js Installation
```bash
# If you see EACCES errors on npm install:
# DO NOT use sudo npm install
# Instead, fix permissions:
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
# Add to ~/.profile:
export PATH=~/.npm-global/bin:$PATH
```

### Python Virtual Environment
```bash
# If venv creation fails on Windows:
python -m pip install --user virtualenv
python -m virtualenv venv

# If activation fails:
# On Windows PowerShell, you might need to:
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### VS Code Extensions Installation
If you can't find extensions in VS Code:
1. Check your internet connection
2. Try Command Palette (Ctrl+Shift+P)
3. Search in Extensions view (Ctrl+Shift+X)
4. Install from VSIX if needed

## 1. What is Full-Stack Development?

Full-stack development refers to working with both the frontend (client-side) and backend (server-side) of web applications. A full-stack developer should be comfortable with:

- Frontend (Client-side):
  - HTML, CSS, JavaScript
  - Frontend frameworks (React in our case)
  - UI/UX principles
  - State management
  - API integration

- Backend (Server-side):
  - Server programming (Python/Django in our case)
  - Databases
  - API development
  - Server deployment
  - Security

## 2. Our Technology Stack

### React
- A JavaScript library for building user interfaces
- Component-based architecture
- Virtual DOM for efficient rendering
- Large ecosystem of libraries and tools

### TypeScript
- A typed superset of JavaScript
- Adds static typing to JavaScript
- Better tooling and error detection
- Enhanced code maintainability

### Django
- A high-level Python web framework
- Follows the "batteries included" philosophy
- Built-in admin interface
- Robust security features
- ORM for database operations

## 3. Setting Up Your Development Environment

### Required Tools
1. Code Editor
   ```bash
   # Download VS Code from:
   https://code.visualstudio.com/
   ```

2. Node.js and npm
   ```bash
   # Download from:
   https://nodejs.org/
   # Verify installation:
   node --version
   npm --version
   ```

3. Python
   ```bash
   # Download from:
   https://www.python.org/
   # Verify installation:
   python --version
   ```

4. Git
   ```bash
   # Download from:
   https://git-scm.com/
   # Verify installation:
   git --version
   ```

### VS Code Extensions
Install these essential extensions:
- ESLint
- Prettier
- Python
- Django
- GitLens
- React Developer Tools

## 4. First Steps

### Create a React App
```bash
# Make sure you're in the right directory
cd your-project-directory

# If create-react-app fails, try:
npx clear-npx-cache
# Then run:
npx create-react-app my-first-app --template typescript

# Start the development server
cd my-first-app
npm start

# If you see port conflicts, either:
# 1. Stop the process using the port:
#    Windows: netstat -ano | findstr :3000
#    Then: taskkill /PID <PID> /F
# 2. Or use a different port:
set PORT=3001 && npm start  # Windows
export PORT=3001 && npm start  # Unix
```

### Create a Django Project
```bash
# Create and activate virtual environment
python -m venv venv
# Windows:
venv\Scripts\activate
# Unix:
source venv/bin/activate

# Install Django
pip install django

# Create project
django-admin startproject myproject
cd myproject

# Create a requirements.txt
pip freeze > requirements.txt

# Run migrations
python manage.py migrate

# Create superuser (admin)
python manage.py createsuperuser

# Start development server
python manage.py runserver

# If you see port conflicts:
python manage.py runserver 8001
```

## Troubleshooting Checklist

Before asking for help, verify:
1. ✅ All required tools are installed (check versions)
2. ✅ Python virtual environment is activated
3. ✅ Node.js and npm are in PATH
4. ✅ All required VS Code extensions are installed
5. ✅ No conflicting processes on required ports
6. ✅ Project directories have correct permissions

## 5. Resources

### Official Documentation
- [React Documentation](https://reactjs.org/docs/getting-started.html)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [Django Documentation](https://docs.djangoproject.com/)

### Additional Learning Materials
- [MDN Web Docs](https://developer.mozilla.org/)
- [React Tutorial](https://reactjs.org/tutorial/tutorial.html)
- [TypeScript for JavaScript Programmers](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)
- [Django Tutorial](https://docs.djangoproject.com/en/stable/intro/tutorial01/)

## 6. Exercises

1. Set up all required development tools
2. Create a "Hello, World!" React application
3. Create a "Hello, World!" Django application
4. Push both projects to GitHub

## Next Steps
Tomorrow we'll dive deeper into HTML, CSS, and JavaScript fundamentals before moving on to React. 