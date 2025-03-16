# Days 5-7: Git and Version Control

## Learning Objectives
By the end of these three days, you will:
- Understand version control and its importance
- Master basic Git commands and workflows
- Learn branching and merging strategies
- Collaborate using GitHub
- Set up and manage your learning project repository

## 1. Version Control Basics

### What is Version Control?
Version control is a system that records changes to files over time, allowing you to:
- Track changes in your code
- Collaborate with others
- Maintain different versions of your project
- Revert to previous states if needed

### Why Git?
- Distributed version control
- Industry standard
- Powerful branching and merging
- Large community and ecosystem
- Essential for modern development workflows

## 2. Basic Git Commands

### Setting Up Git
```bash
# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Initialize a new repository
git init

# Clone an existing repository
git clone https://github.com/username/repository.git
```

### Basic Workflow
```bash
# Check status of your repository
git status

# Add files to staging area
git add filename.txt    # Add specific file
git add .              # Add all files

# Commit changes
git commit -m "Your commit message"

# View commit history
git log
git log --oneline      # Compact view
```

### Working with Remotes
```bash
# Add a remote repository
git remote add origin https://github.com/username/repository.git

# Push changes to remote
git push origin main

# Pull changes from remote
git pull origin main

# Fetch changes without merging
git fetch origin
```

## 3. Branching and Merging

### Working with Branches
```bash
# Create a new branch
git branch feature-name

# Switch to a branch
git checkout feature-name
# or
git switch feature-name

# Create and switch to a new branch
git checkout -b feature-name

# List all branches
git branch
git branch -a  # Include remote branches
```

### Merging
```bash
# Merge a branch into current branch
git checkout main
git merge feature-name

# Handle merge conflicts
# Edit conflicted files
git add .
git commit -m "Resolve merge conflicts"
```

## 4. Advanced Git Concepts

### Stashing Changes
```bash
# Stash current changes
git stash

# List stashes
git stash list

# Apply stashed changes
git stash apply
git stash pop  # Apply and remove stash
```

### Reverting Changes
```bash
# Undo last commit (keep changes)
git reset HEAD~1

# Revert a specific commit
git revert commit-hash

# Reset to specific commit (dangerous)
git reset --hard commit-hash
```

## 5. GitHub Workflow

### Fork and Pull Request Model
1. Fork a repository on GitHub
2. Clone your fork locally
3. Create a feature branch
4. Make changes and commit
5. Push to your fork
6. Create a Pull Request

### Best Practices
- Write clear commit messages
- Keep commits atomic and focused
- Use meaningful branch names
- Review changes before committing
- Keep your fork updated with upstream

## 6. Project Setup

### Repository Structure
```
learning-project/
├── README.md
├── .gitignore
├── frontend/
│   ├── src/
│   └── package.json
├── backend/
│   ├── myproject/
│   └── requirements.txt
└── docs/
    └── CONTRIBUTING.md
```

### Essential Files
1. `.gitignore`:
```
# Node
node_modules/
build/
.env

# Python
__pycache__/
*.pyc
venv/
.env

# IDE
.vscode/
.idea/
```

2. `README.md`:
```markdown
# Project Name

## Description
Brief project description

## Setup
Installation instructions

## Usage
How to use the project

## Contributing
How to contribute
```

## 7. Exercises

1. Initialize a Git repository for your learning project
2. Create a `.gitignore` file with appropriate rules
3. Make your first commit and push to GitHub
4. Create a feature branch and practice merging
5. Simulate and resolve a merge conflict
6. Create a Pull Request on GitHub

## 8. Mini Project: Collaborative Feature

1. Fork a classmate's repository
2. Add a new feature
3. Create a Pull Request
4. Review and merge a Pull Request
5. Handle merge conflicts

## Resources

- [Git Documentation](https://git-scm.com/doc)
- [GitHub Guides](https://guides.github.com/)
- [Learn Git Branching](https://learngitbranching.js.org/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)

## Next Steps
Now that you have a solid foundation in version control, we'll move on to React basics in the next section. Remember to use Git for all your future projects!

## Common Git Scenarios and Solutions

### 1. Accidentally Committed Sensitive Data
```bash
# If you committed sensitive data (like API keys):

# 1. First, remove the sensitive file from Git history
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch path/to/sensitive_file" \
  --prune-empty --tag-name-filter cat -- --all

# 2. Force push the changes
git push origin main --force

# 3. Add the file to .gitignore
echo "sensitive_file" >> .gitignore

# 4. Change any compromised credentials immediately!
```

### 2. Wrong Branch Commits
```bash
# If you committed to the wrong branch:

# 1. Copy the commits
git log  # Note the commit hashes you need

# 2. Switch to correct branch
git checkout correct-branch

# 3. Cherry pick the commits
git cherry-pick <commit-hash>

# 4. Remove from wrong branch
git checkout wrong-branch
git reset --hard HEAD~1  # Be careful with reset!
```

### 3. Large Files Committed by Mistake
```bash
# If you accidentally committed large files:

# 1. Remove the file from Git history
git filter-branch --tree-filter 'rm -f path/to/large_file' HEAD

# 2. Clean up
git gc --prune=now
git push origin main --force

# 3. Add to .gitignore
echo "large_file" >> .gitignore
```

## Git Command Reference Card

### Daily Commands
```bash
# Start of day
git pull origin main     # Get latest changes
git checkout -b feature  # Create feature branch

# During development
git status              # Check status
git add .               # Stage changes
git commit -m "msg"     # Commit changes
git push origin feature # Push changes

# End of day
git push origin feature # Backup work
```

### Maintenance Commands
```bash
# Clean up local branches
git branch -d $(git branch --merged)

# Update remote tracking
git remote update origin --prune

# Clean up local repository
git gc
git prune
```

### Emergency Commands
```bash
# Undo last commit (keep changes)
git reset --soft HEAD^

# Discard all local changes
git reset --hard HEAD

# Remove untracked files
git clean -fd

# Temporarily store changes
git stash
git stash pop
```

## Git Configuration Best Practices

### 1. Global Git Configuration
```bash
# Set up your identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set up default branch name
git config --global init.defaultBranch main

# Set up line ending handling
# On Windows:
git config --global core.autocrlf true
# On Mac/Linux:
git config --global core.autocrlf input

# Set up merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

### 2. Repository-Specific Configuration
```bash
# Local user settings (if different from global)
git config user.name "Project-Specific Name"
git config user.email "project@example.com"

# Set up hooks directory
git config core.hooksPath .githooks
```

### 3. Useful Aliases
```bash
# Add these to your global Git config
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
``` 