# Module 1: Development Environment Setup

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

## Preliminary Concepts

### Understanding VS Code Extensions

VS Code extensions are add-on modules that extend the functionality of Visual Studio Code. They're similar to browser extensions or plugins in other software. Extensions can:

- **Add language support**: Provide syntax highlighting, code completion, and error checking for specific programming languages
- **Enhance productivity**: Add shortcuts, snippets, and automation tools
- **Integrate external tools**: Connect VS Code with version control systems, databases, or cloud services
- **Customize the editor**: Change themes, add visual enhancements, or modify the UI

Extensions run in a separate process from VS Code itself, ensuring they don't slow down the main editor. They're installed from the VS Code Marketplace and can be managed through the Extensions view.

### What Are Linters?

A linter is a static code analysis tool that examines your source code to flag programming errors, bugs, stylistic errors, and suspicious constructs. Think of it as an automated code reviewer that catches issues before you run your code.

**Why linters matter:**
- **Catch errors early**: Find bugs before runtime
- **Enforce consistency**: Maintain uniform code style across a team
- **Learn best practices**: Get suggestions for better code patterns
- **Save time**: Reduce manual code review effort

Common JavaScript linters include:
- **ESLint**: The most popular and configurable JavaScript linter
- **JSHint**: A simpler alternative focusing on error detection
- **StandardJS**: An opinionated linter with no configuration needed

### Language Server Protocol (LSP)

The Language Server Protocol is a standardized protocol developed by Microsoft that defines how development tools and language servers communicate. Before LSP, each editor needed custom integration for each programming language, leading to duplicated effort.

**How LSP works:**
1. A language server runs as a separate process
2. It analyzes your code and understands its structure
3. The editor communicates with the server using the LSP protocol
4. The server provides features like:
   - Code completion (IntelliSense)
   - Go to definition
   - Find all references
   - Hover information
   - Error diagnostics

**Benefits of LSP:**
- **Reusability**: One language server works with any LSP-compatible editor
- **Consistency**: Same features across different editors
- **Performance**: Language analysis runs in a separate process
- **Maintainability**: Language maintainers only need to build one server

For Vue development, Volar (Vue Language Features) is an LSP implementation that provides intelligent Vue support in VS Code.

### ESLint vs Prettier: Understanding the Difference

While both ESLint and Prettier help maintain code quality, they serve different purposes:

**ESLint:**
- **Purpose**: Code quality and error prevention
- **Focus**: Finding bugs, enforcing best practices, identifying problematic patterns
- **Examples**: 
  - Unused variables
  - Missing semicolons (if required)
  - Unreachable code
  - Use of deprecated methods
- **Configurability**: Highly configurable with hundreds of rules

**Prettier:**
- **Purpose**: Code formatting
- **Focus**: Consistent code style and appearance
- **Examples**:
  - Line length
  - Indentation (tabs vs spaces)
  - Quote style (single vs double)
  - Trailing commas
- **Philosophy**: Opinionated with limited configuration options

**Using them together:**
1. Prettier formats your code for consistent style
2. ESLint checks for code quality issues
3. Configure ESLint to skip formatting rules (use eslint-config-prettier)
4. Run Prettier first, then ESLint

**Example workflow:**
```javascript
// Before formatting (messy but valid code)
const data={name:"John",age:30,email:"john@example.com"};
console.log(data)

// After Prettier (formatted)
const data = { name: "John", age: 30, email: "john@example.com" };
console.log(data);

// ESLint might then warn:
// - 'data' is assigned but never used
// - Missing semicolon (if configured)
```

### Why These Tools Matter for Vue Development

In Vue development, these tools work together to provide a professional development experience:

1. **Volar (LSP)** understands Vue's template syntax, script sections, and style blocks
2. **ESLint with Vue plugin** catches Vue-specific issues like unused props or invalid template syntax
3. **Prettier with Vue support** formats .vue files consistently
4. **Tailwind CSS IntelliSense** provides autocomplete for utility classes
5. **GitLens** helps track code changes and collaborate with team members

This integrated tooling catches errors early, maintains consistency, and significantly improves development speed and code quality.

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

**Understanding Git Remotes:**
A remote in Git is a reference to a repository hosted elsewhere (like GitHub, GitLab, or Bitbucket). Think of it as a bookmark that tells Git where to push your code or pull updates from. The most common remote is called "origin" - this is just a conventional name for your main remote repository.

**Key concepts:**
- **Remote**: A version of your repository hosted on a server
- **Origin**: The default name for your primary remote repository
- **Upstream**: Often used for the original repository when working with forks
- **URL**: The address where your remote repository lives (HTTPS or SSH)

**Step-by-step process:**

1. **Create a repository on GitHub:**
   - Log into GitHub and click "New repository"
   - Name it the same as your local project for clarity
   - Choose public or private visibility
   - DO NOT initialize with README, .gitignore, or license (you already have these locally)

2. **Add the remote to your local repository:**
   ```bash
   # Using HTTPS (easier for beginners, requires password/token)
   git remote add origin https://github.com/yourusername/your-repo-name.git
   
   # Using SSH (recommended for frequent use, requires SSH key setup)
   git remote add origin git@github.com:yourusername/your-repo-name.git
   ```

3. **Verify the remote was added:**
   ```bash
   # List all remotes
   git remote -v
   
   # You should see:
   # origin  https://github.com/yourusername/your-repo-name.git (fetch)
   # origin  https://github.com/yourusername/your-repo-name.git (push)
   ```

4. **Push your code to the remote:**
   ```bash
   # Push your main branch to origin and set it as the upstream
   git push -u origin main
   
   # The -u flag sets up tracking, so future pushes can just use:
   git push
   ```

**Common issues and solutions:**
- **Authentication failed**: For HTTPS, you need a personal access token (not your password)
- **Permission denied**: For SSH, ensure your SSH key is added to GitHub
- **Remote already exists**: Remove it with `git remote remove origin` and add again
- **Nothing to push**: Make sure you've committed changes first

**Working with remotes:**
```bash
# View remote details
git remote show origin

# Change remote URL (e.g., switching from HTTPS to SSH)
git remote set-url origin git@github.com:yourusername/your-repo-name.git

# Remove a remote
git remote remove origin

# Rename a remote
git remote rename origin upstream
```

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

**Understanding Monorepos:**

A monorepo (monolithic repository) is a software development approach where multiple projects are stored in a single repository. In our case, we'll keep the Vue frontend and Express backend in the same repository but as separate npm projects.

**Benefits of a monorepo approach:**
- **Shared code**: Easy to share utilities, types, and configurations
- **Atomic commits**: Changes across frontend and backend can be committed together
- **Simplified tooling**: One set of linting rules, Git hooks, and CI/CD pipelines
- **Easier refactoring**: Rename or move code across projects with confidence
- **Single source of truth**: All code in one place makes it easier to understand the full system

**How it works:**
- Each folder (`client/` and `server/`) will have its own `package.json`
- They function as independent npm projects with their own dependencies
- The root `package.json` orchestrates running both projects together

**Suggested monorepo structure:**
```
task-manager/                 # Root directory (monorepo)
├── client/                   # Vue frontend (separate npm project)
│   ├── package.json         # Frontend dependencies & scripts
│   ├── src/                 # Vue source code
│   └── ...                  # Other frontend files
├── server/                   # Express backend (separate npm project)
│   ├── package.json         # Backend dependencies & scripts
│   ├── src/                 # Express source code
│   └── ...                  # Other backend files
├── shared/                   # Shared types/utilities
├── docs/                     # Documentation
├── .env.example             # Environment template
├── .gitignore               # Git ignore rules for entire monorepo
├── README.md                # Project overview
└── package.json             # Root orchestration scripts
```

**Purpose of root package.json:**

The root `package.json` serves as the conductor for your monorepo:
- **Orchestration**: Contains scripts to run both frontend and backend simultaneously
- **Workspace management**: Can use npm workspaces to manage dependencies
- **Common tasks**: Scripts for linting, formatting, or building the entire project
- **Project metadata**: Name, version, description for the overall project

Example root package.json:
```json
{
  "name": "task-manager",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "concurrently \"npm run dev:client\" \"npm run dev:server\"",
    "dev:client": "cd client && npm run dev",
    "dev:server": "cd server && npm run dev",
    "install:all": "npm install && cd client && npm install && cd ../server && npm install",
    "build": "npm run build:client && npm run build:server",
    "build:client": "cd client && npm run build",
    "build:server": "cd server && npm run build"
  }
}
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
- Understand the benefits of the monorepo approach for this project
- Plan how client and server will communicate during development
- Consider how shared code will be organized
- Think about deployment strategies for both frontend and backend

**2. Folder Creation and Organization**
- Create the root project directory (e.g., `task-manager`)
- Create `client/` and `server/` subdirectories
- Create `shared/`, `docs/`, and other supporting directories
- Initialize separate npm projects in client and server folders:
  ```bash
  # From root directory
  cd client && npm init -y
  cd ../server && npm init -y
  cd .. && npm init -y  # Root package.json
  ```

**3. Root Package.json Setup**
- The root package.json orchestrates the entire monorepo
- Install `concurrently` as a dev dependency to run multiple scripts:
  ```bash
  npm install --save-dev concurrently
  ```
  Note: `concurrently` is a utility that runs multiple npm scripts in parallel, perfect for starting both frontend and backend servers with a single command
- Add scripts that manage both projects:
  - `dev`: Runs both client and server in development mode
  - `install:all`: Installs dependencies for all projects
  - `build`: Builds both client and server for production
- Set `"private": true` to prevent accidental npm publication

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
- [ ] Monorepo structure created with client and server folders
- [ ] Environment variables are working
- [ ] Documentation is complete and up-to-date
- [ ] Code has been committed and pushed to repository

## Next Module Preview
Module 2 will focus on initializing the Vue 3 frontend application with Vite and setting up the basic component structure. You'll be building on the foundation established in this module.