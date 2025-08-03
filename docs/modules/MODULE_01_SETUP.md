---
title: "Module 1: Development Environment Setup"
description: "Set up your development environment with VS Code, Git, Node.js, and project structure"
layout: default
---

# Module 1: Development Environment Setup

[← Back to Course](../) | [📚 Next: Module 2](MODULE_02_VUE_FOUNDATION.html)

**Duration:** 1-2 days  
**Branch:** `feature/module-1-setup`

## Learning Objectives
- Configure a professional development environment
- Set up project structure following industry standards
- Establish version control workflow
- Configure environment variable management
- Prepare for team collaboration

## Overview
Before writing any code, you'll establish a solid foundation for development. This module focuses on tooling, project structure, and workflows that professional developers use daily.

## Tasks

### Task 1.1: VS Code Configuration
**Objective:** Set up VS Code with essential extensions for Vue/JavaScript development

**What you need to accomplish:**
- Install recommended VS Code extensions
- Configure workspace settings
- Set up code formatting and linting

**Documentation to consult:**
- [VS Code Extension Marketplace](https://marketplace.visualstudio.com/vscode)
- [VS Code Workspace Settings](https://code.visualstudio.com/docs/getstarted/settings)

**Required extensions (minimum):**
- Vue Language Features (Volar)
- TypeScript Vue Plugin (Volar)
- Tailwind CSS IntelliSense
- ESLint
- Prettier
- GitLens

**Hints:**
- Create a `.vscode/extensions.json` file to recommend extensions to other developers
- Configure auto-format on save
- Set up consistent indentation (2 spaces for Vue/JS)

**Acceptance criteria:**
- [ ] All required extensions installed
- [ ] Workspace settings configured
- [ ] Code formatting works on save
- [ ] Vue files have proper syntax highlighting

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] VS Code installed on your development machine
- [ ] Basic familiarity with VS Code interface
- [ ] Administrative privileges to install extensions

### Step-by-Step Implementation Approach

**1. Extension Installation Process**
- Open VS Code Extensions view (Ctrl+Shift+X or Cmd+Shift+X)
- Search for each required extension by exact name
- Install extensions one by one, restarting VS Code if prompted
- Verify each extension appears in your installed extensions list

**2. Workspace Configuration Setup**
- Create `.vscode` folder in your project root
- Add `settings.json` for workspace-specific settings
- Add `extensions.json` to recommend extensions to team members
- Configure formatting rules that align with project standards

**3. Auto-formatting Configuration**
- Enable "Format on Save" in VS Code settings
- Set Prettier as default formatter for JavaScript/Vue files
- Configure ESLint to work alongside Prettier
- Test formatting by creating a sample Vue file with messy formatting

**Key Decision Points:**
- **Global vs. Workspace settings:** Use workspace settings for project-specific rules, global for personal preferences
- **Extension conflicts:** If you have conflicting Vue extensions, disable older ones (like Vetur)
- **Formatting rules:** Choose between tabs vs. spaces (recommended: 2 spaces for Vue/JavaScript)

**Verification Steps:**
1. Create a test `.vue` file with incorrect formatting
2. Save the file and confirm it auto-formats
3. Check that syntax highlighting shows Vue template, script, and style sections in different colors
4. Verify IntelliSense suggestions appear when typing Vue directives

---

### Task 1.2: Git Repository Setup
**Objective:** Initialize project repository with proper structure and configuration

**What you need to accomplish:**
- Initialize Git repository
- Create proper .gitignore file
- Set up branch protection (if using GitHub)
- Configure commit message conventions

**Documentation to consult:**
- [Git Documentation](https://git-scm.com/doc)
- [GitHub Docs](https://docs.github.com/)
- [Conventional Commits](https://www.conventionalcommits.org/)

**Hints:**
```bash
# Example .gitignore structure should include:
node_modules/
.env
.env.local
dist/
.DS_Store
*.log
```

**Acceptance criteria:**
- [ ] Git repository initialized
- [ ] Proper .gitignore file created
- [ ] Initial commit made
- [ ] Remote repository connected (GitHub)
- [ ] README.md with project description

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Git installed and configured globally (`git --version` works)
- [ ] GitHub account created and accessible
- [ ] SSH key or personal access token configured for GitHub
- [ ] Basic understanding of Git workflow concepts

### Step-by-Step Implementation Approach

**1. Repository Initialization**
- Navigate to your project root directory in terminal
- Run `git init` to initialize the repository
- Check that `.git` folder was created (use `ls -la` to see hidden files)
- Configure local Git settings if not set globally

**2. .gitignore Configuration**
- Create `.gitignore` file in root directory
- Research what files Vue.js and Node.js projects typically ignore
- Include environment variables, build outputs, and system files
- Test that ignored files don't appear in `git status`

**3. Initial Repository Content**
- Create a meaningful README.md with project description and setup instructions
- Add any initial configuration files
- Stage files using `git add .` (verify with `git status`)
- Make first commit with descriptive message

**4. Remote Repository Connection**
- Create repository on GitHub with same name as local project
- Add remote origin using HTTPS or SSH URL
- Push initial commit to establish connection
- Verify repository appears correctly on GitHub

**Key Decision Points:**
- **Repository visibility:** Choose public for portfolio projects, private for sensitive work
- **Commit message style:** Establish convention (conventional commits recommended)
- **Branch strategy:** Decide on main branch name and feature branch naming

**Verification Steps:**
1. Confirm `git status` shows clean working directory
2. Test that `.env` files are properly ignored
3. Verify you can push and pull from remote repository
4. Check that GitHub repository displays your README correctly

---

### Task 1.3: Project Structure Setup
**Objective:** Create a scalable project structure for full-stack development

**What you need to accomplish:**
- Design folder structure for monorepo or separate repos
- Create necessary configuration files
- Set up package.json with scripts
- Plan for frontend/backend separation

**Documentation to consult:**
- [Vue.js Project Structure](https://vuejs.org/guide/scaling-up/sfc.html)
- [Node.js Project Structure Best Practices](https://nodejs.org/en/docs/guides/)

**Suggested structure:**
```
task-manager/
├── client/          # Vue frontend
├── server/          # Express backend
├── shared/          # Shared types/utilities
├── docs/           # Documentation
├── .env.example    # Environment template
├── .gitignore
├── README.md
└── package.json    # Root package.json
```

**Acceptance criteria:**
- [ ] Clear folder structure created
- [ ] Root package.json with workspace scripts
- [ ] Documentation folder with initial docs
- [ ] .env.example file created

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Node.js and npm installed (`node --version` and `npm --version` work)
- [ ] Understanding of monorepo vs. separate repository approaches
- [ ] Clear vision of your application's scope and requirements

### Step-by-Step Implementation Approach

**1. Directory Structure Planning**
- Research common full-stack project structures
- Decide between monorepo (single repository) vs. separate repositories
- Plan for future scalability and team collaboration
- Consider build tool requirements and deployment strategies

**2. Folder Creation and Organization**
- Create main directories following industry conventions
- Establish clear separation between frontend, backend, and shared code
- Add placeholder files to maintain folder structure in Git
- Document the purpose of each directory

**3. Root Package.json Setup**
- Initialize npm in project root with `npm init`
- Configure workspace if using monorepo approach
- Add scripts for common development tasks
- Include metadata about your project

**4. Documentation Structure**
- Create docs folder with initial documentation files
- Plan documentation strategy for different audiences
- Include setup instructions and architecture decisions
- Consider using markdown for consistency

**Key Decision Points:**
- **Monorepo vs. Multi-repo:** Monorepo simpler for course, multi-repo more realistic for large teams
- **Build tool integration:** Structure should support your chosen build tools
- **Deployment considerations:** Organize for easy deployment to platforms like Vercel

**Verification Steps:**
1. Verify folder structure makes sense to a new developer
2. Test that npm scripts run from root directory
3. Confirm documentation files are readable and helpful
4. Check that structure supports both development and production builds

---

### Task 1.4: Environment Variables Setup
**Objective:** Configure secure environment variable management

**What you need to accomplish:**
- Set up .env files for different environments
- Create .env.example template
- Configure environment loading
- Document environment variables

**Documentation to consult:**
- [Environment Variables in Node.js](https://nodejs.dev/learn/how-to-read-environment-variables-from-nodejs)
- [Vue.js Environment Variables](https://vitejs.dev/guide/env-and-mode.html)

**Environment variables to plan for:**
```bash
# Database
DATABASE_URL=
DB_HOST=
DB_PORT=
DB_NAME=
DB_USER=
DB_PASSWORD=

# Authentication
JWT_SECRET=
JWT_EXPIRE=

# API
API_BASE_URL=
PORT=

# External Services
VERCEL_URL=
```

**Acceptance criteria:**
- [ ] .env.example file with all required variables
- [ ] Local .env file configured (not committed)
- [ ] Environment loading tested
- [ ] Documentation for each variable

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Understanding of why environment variables are important for security
- [ ] Knowledge of the difference between .env and .env.example files
- [ ] Awareness of environment variable naming conventions

### Step-by-Step Implementation Approach

**1. Environment Variable Planning**
- Identify all configuration values that will vary between environments
- Research what variables typical Vue.js and Node.js applications need
- Plan for development, testing, and production environments
- Consider security implications of each variable

**2. .env.example Creation**
- Create comprehensive template file with all required variables
- Use placeholder values that clearly indicate what type of data is expected
- Add comments explaining the purpose of each variable
- Group related variables together for better organization

**3. Local Environment Setup**
- Copy .env.example to .env for your local development
- Fill in actual values for your development environment
- Test that variables load correctly in your applications
- Verify .env is properly ignored by Git

**4. Documentation and Team Setup**
- Document each environment variable's purpose and format
- Create setup instructions for new team members
- Plan for secure sharing of sensitive development values
- Consider using environment variable validation

**Key Decision Points:**
- **Variable naming:** Use UPPER_CASE with underscores for consistency
- **Default values:** Decide which variables should have defaults vs. be required
- **Environment separation:** Plan how development, staging, and production will differ

**Verification Steps:**
1. Confirm .env file is not tracked in Git (`git status` shouldn't show it)
2. Test that applications can read environment variables correctly
3. Verify .env.example contains all necessary variables with clear descriptions
4. Check that a new developer could set up their environment using your documentation

---

### Task 1.5: Development Scripts Setup
**Objective:** Create npm scripts for common development tasks

**What you need to accomplish:**
- Set up development server scripts
- Configure build scripts
- Add linting and formatting scripts
- Create database scripts (for later modules)

**Documentation to consult:**
- [npm Scripts Documentation](https://docs.npmjs.com/cli/v9/using-npm/scripts)
- [Package.json Scripts](https://docs.npmjs.com/cli/v9/configuring-npm/package-json#scripts)

**Example scripts structure:**
```json
{
  "scripts": {
    "dev": "concurrently \"npm run dev:client\" \"npm run dev:server\"",
    "dev:client": "cd client && npm run dev",
    "dev:server": "cd server && npm run dev",
    "build": "npm run build:client && npm run build:server",
    "lint": "eslint . --ext .vue,.js,.ts",
    "format": "prettier --write .",
    "db:migrate": "cd server && npm run db:migrate",
    "db:seed": "cd server && npm run db:seed"
  }
}
```

**Acceptance criteria:**
- [ ] All development scripts work
- [ ] Build scripts configured
- [ ] Linting and formatting scripts functional
- [ ] Scripts documented in README

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Package.json file created in project root
- [ ] Understanding of npm scripts and how they work
- [ ] Knowledge of tools like concurrently for running multiple scripts

### Step-by-Step Implementation Approach

**1. Script Planning and Dependencies**
- Research common development workflow patterns
- Install necessary packages like `concurrently` for running multiple scripts
- Plan scripts for development, building, testing, and maintenance tasks
- Consider cross-platform compatibility (Windows, Mac, Linux)

**2. Development Scripts Implementation**
- Create scripts that start both frontend and backend simultaneously
- Set up individual scripts for running services separately
- Configure hot reloading and auto-restart functionality
- Test that all development workflows function smoothly

**3. Build and Production Scripts**
- Create scripts for building frontend and backend for production
- Set up linting and formatting scripts for code quality
- Add scripts for database operations (migrations, seeding)
- Configure any necessary pre-build or post-build steps

**4. Documentation and Team Workflow**
- Document each script's purpose and usage in README
- Create clear instructions for common development tasks
- Establish coding standards and enforce them through scripts
- Plan for CI/CD integration in future modules

**Key Decision Points:**
- **Script naming:** Use consistent, descriptive names that are intuitive
- **Parallel vs. sequential:** Decide when to run scripts simultaneously vs. in sequence
- **Cross-platform support:** Ensure scripts work on different operating systems

**Verification Steps:**
1. Test each script individually to ensure they work correctly
2. Verify concurrent scripts can run together without conflicts
3. Confirm new team members can understand and use scripts from README
4. Check that scripts fail gracefully with helpful error messages

## Challenge Extensions

### Advanced Git Configuration
- Set up Git hooks for automated linting
- Configure commit message templates
- Set up automated branch naming conventions

### Docker Setup
- Create Dockerfile for development environment
- Set up docker-compose for local database
- Configure hot reloading in containers

### VS Code Advanced Configuration
- Set up debugging configuration
- Create custom code snippets
- Configure task automation

## Troubleshooting Common Issues

### Extension Not Working
- Check Vue files are properly detected
- Verify workspace trust settings
- Restart VS Code after configuration changes

### Git Issues
- Ensure Git is installed and configured globally
- Check SSH keys for GitHub access
- Verify remote repository permissions

### Environment Variables Not Loading
- Check file naming (.env not .env.txt)
- Verify file is in correct directory
- Ensure proper syntax (no spaces around =)

## Module Completion Checklist

Before moving to Module 2, ensure:
- [ ] VS Code is fully configured and working
- [ ] Git repository is set up with proper structure
- [ ] Environment variables are working
- [ ] All npm scripts execute without errors
- [ ] Documentation is complete and up-to-date
- [ ] Code has been committed and pushed to repository

## Next Module Preview
Module 2 will focus on initializing the Vue 3 frontend application with Vite and setting up the basic component structure. You'll be building on the foundation established in this module.