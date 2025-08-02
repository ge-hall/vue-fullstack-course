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