# Days 1-2: Development Environment Setup

## Learning Objectives

By the end of this section, you will:

- Set up a professional development environment
- Understand essential developer tools
- Learn command line basics for efficient workflow
- Configure code editors for productivity
- Create a standardized project structure

## Introduction to Development Environments

A well-configured development environment is crucial for productivity and collaboration. This section covers the essential tools and configurations you'll need throughout your full-stack journey.

## Essential Tools

### 1. Code Editor: Visual Studio Code

VS Code provides a powerful environment for web development.

#### Installation

- [Download VS Code](https://code.visualstudio.com/download)

#### Recommended Extensions

- **ESLint**: JavaScript linting
- **Prettier**: Code formatting
- **Live Server**: Local development server
- **GitLens**: Enhanced Git capabilities
- **JavaScript (ES6) code snippets**: Helpful code shortcuts
- **Auto Rename Tag**: Auto-rename paired HTML/XML tags

#### Configuration

Create a `.vscode/settings.json` file in your project:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.tabSize": 2,
  "editor.wordWrap": "on",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "files.autoSave": "onFocusChange"
}
```

### 2. Command Line Interface (CLI)

#### Windows: PowerShell / WSL (Windows Subsystem for Linux)

For Windows, you have two good options:
- PowerShell: Built-in to Windows
- WSL: Provides a Linux environment on Windows

To install WSL:
1. Open PowerShell as administrator
2. Run: `wsl --install`

#### macOS: Terminal

Terminal is built into macOS and accessible via:
- Applications > Utilities > Terminal
- Spotlight (Cmd+Space): Type "Terminal"

#### Linux: Terminal

Most Linux distributions come with a terminal emulator.

### 3. Node.js & npm

Node.js allows you to run JavaScript on your computer, while npm (Node Package Manager) helps manage packages.

#### Installation

- [Download Node.js](https://nodejs.org/) (LTS version recommended)
- npm comes bundled with Node.js

#### Verify Installation

```bash
node --version
npm --version
```

### 4. Git

Git is essential for version control.

#### Installation

- Windows: [Git for Windows](https://gitforwindows.org/)
- macOS: `brew install git` (requires [Homebrew](https://brew.sh/))
- Linux: `sudo apt install git` (Ubuntu/Debian) or appropriate package manager

#### Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## Command Line Basics

### Navigation

```bash
pwd                 # Print working directory
ls                  # List files and directories
cd directory-name   # Change directory
cd ..               # Go up one directory
mkdir directory-name # Create directory
touch filename      # Create empty file
```

### File Operations

```bash
cp source destination    # Copy files
mv source destination    # Move or rename files
rm filename              # Remove file
rm -r directory-name     # Remove directory and contents
cat filename             # Display file contents
```

### Package Management

```bash
npm init                # Initialize a new npm project
npm install package-name # Install a package
npm install -g package-name # Install a global package
npm start               # Run the start script defined in package.json
```

## Project Structure 

A standard project structure helps organize code effectively.

```
project-name/
├── .vscode/             # VS Code settings
│   └── settings.json
├── .gitignore          # Files to ignore in Git
├── src/                # Source code
│   ├── index.html      # Main HTML file
│   ├── js/             # JavaScript files
│   │   └── main.js
│   ├── css/            # CSS styles
│   │   └── style.css
│   └── assets/         # Images, fonts, etc.
├── dist/               # Production build (generated)
├── node_modules/       # Dependencies (generated)
├── package.json        # Project metadata
├── package-lock.json   # Dependency lock file
└── README.md           # Project documentation
```

### .gitignore

Create a `.gitignore` file to exclude unnecessary files from version control:

```
# Dependencies
node_modules/

# Build output
dist/
build/

# Environment variables
.env
.env.local

# Editor files
.vscode/*
!.vscode/settings.json
.idea/

# OS files
.DS_Store
Thumbs.db

# Log files
*.log
npm-debug.log*
```

## Essential Terminal Commands for Web Development

### Web Development Workflow

```bash
# Start a new project
mkdir my-project
cd my-project
npm init -y
touch index.html
mkdir css js
touch css/style.css js/app.js

# Install development dependencies
npm install --save-dev lite-server

# Add script to package.json
# "scripts": { "dev": "lite-server" }

# Start development server
npm run dev
```

## Hands-On Exercises

### Exercise 1: Environment Setup
1. Install all required software
2. Configure VS Code with recommended extensions
3. Initialize a Git repository with proper configuration

### Exercise 2: Project Initialization
1. Create a new project with the standard structure
2. Initialize npm
3. Create a simple `.gitignore` file
4. Make your first commit

### Exercise 3: Command Line Practice
1. Navigate to your project directory using only the command line
2. Create a subdirectory structure for a web project
3. Create empty HTML, CSS, and JS files
4. List all files and verify the structure

## Mini-Project: Developer Profile Page

Create a personal developer profile page that:

1. Displays your name, photo, and brief bio
2. Lists the tools and technologies you are learning
3. Features proper HTML structure and basic CSS styling
4. Is committed to a Git repository

Requirements:
- Use VS Code to write your code
- Use Terminal/Command Line to navigate and manage files
- Commit your changes with appropriate messages
- Include a proper README.md explaining the project

## Additional Resources

- [VS Code Documentation](https://code.visualstudio.com/docs)
- [Command Line Crash Course](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Understanding_client-side_tools/Command_line)
- [Git Handbook](https://guides.github.com/introduction/git-handbook/)
- [npm Documentation](https://docs.npmjs.com/)
- [Web Development Setup Guide](https://www.freecodecamp.org/news/how-to-set-up-a-front-end-development-project/) 