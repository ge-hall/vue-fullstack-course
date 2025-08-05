# Module 2: Frontend Foundation with Vue 3

**Duration:** 3-4 days  
**Branch:** `feature/module-2-vue-foundation`

## Application Context: TaskFlow Platform Foundation

### What You're Building
You're creating the foundational structure for **TaskFlow** - a collaborative task management platform similar to Trello or Asana. This module establishes the visual framework and navigation system that will support user authentication, project management, and team collaboration features in later modules.

### TaskFlow Features You're Building This Module
- **Landing page**: Modern, professional homepage with clear value proposition
- **Responsive navigation**: Header with logo, search, and user controls
- **Application layout**: Sidebar navigation + main content area structure  
- **Mobile-first design**: Hamburger menu and responsive breakpoints
- **Route structure**: Foundation for authentication and dashboard pages

### User Stories for This Module
- **As a visitor**, I want to see an attractive landing page so that I understand what TaskFlow offers
- **As a user**, I want intuitive navigation so that I can easily move between different areas of the application
- **As a mobile user**, I want a responsive interface so that I can use TaskFlow on any device

### UI Mockups Reference
📋 **See detailed visual mockups**: [Module 2 UI Mockups](../mockups/MODULE_02_MOCKUPS.md)

The mockups show the complete landing page design, responsive navigation patterns, and mobile menu implementations you'll be building.

## Learning Objectives
- Initialize a modern Vue 3 application with Vite
- Configure Tailwind CSS for utility-first styling
- Set up Vue Router for single-page application navigation
- Create a scalable component architecture
- Implement responsive layouts and navigation

## Overview
This module establishes the frontend foundation using Vue 3's Composition API, modern build tooling with Vite, and Tailwind CSS for rapid UI development. You'll create the visual structure that will house TaskFlow's project management features.

## Tasks

### Task 2.1: Vue 3 Project Initialization
**Objective:** Create a new Vue 3 project with Vite and configure the development environment

**What you need to accomplish:**
- Initialize Vue 3 project using Vite
- Configure project structure within your monorepo
- Set up development server with hot reload
- Configure basic build process

**Documentation to consult:**
- [Vue.js Getting Started](https://vuejs.org/guide/quick-start.html)
- [Vite Guide](https://vitejs.dev/guide/)
- [Vue CLI vs Vite](https://vuejs.org/guide/scaling-up/tooling.html)

**Commands to explore:**
```bash
# In your client/ directory
npm create vue@latest .
# or
yarn create vue .
```

**Configuration considerations:**
- Choose No for TypeScript support (we'll introduce TypeScript in later modules)
- Include Vue Router (Yes - essential for SPA navigation)
- Include ESLint for code quality (Yes)
- Skip PWA features for now (No)

**Acceptance criteria:**
- [ ] Vue 3 project successfully created
- [ ] Development server runs without errors
- [ ] Hot reload functionality working
- [ ] Project integrates with existing folder structure
- [ ] Initial commit made with project scaffold

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Node.js LTS version (20.x or later) installed (`node --version`)
- [ ] Module 1 development environment setup completed with npm scripts configured
- [ ] Understanding of Vue 3 vs Vue 2 differences
- [ ] Basic familiarity with modern JavaScript (ES6+)

### Step-by-Step Implementation Approach

**1. Project Initialization Planning**
- Navigate to your `client/` directory (or create it if needed)
- Research the `npm create vue@latest` command options
- Plan which features to include during scaffold:
  - TypeScript: No (we'll add it in advanced modules)
  - Vue Router: Yes (required for navigation)
  - ESLint: Yes (for code quality)
  - Prettier: Yes (for consistent formatting)
  - Vitest: No (we'll add testing later)
- Consider how this integrates with your existing monorepo structure from Module 1

**2. Vue Project Creation Process**
- Run the Vue creation command in your client directory:
  ```bash
  cd client/
  npm create vue@latest .
  ```
- When prompted, select:
  - Project name: `taskflow-client`
  - TypeScript: No
  - JSX Support: No
  - Vue Router: Yes
  - Pinia: No (we'll use simple state management for now)
  - Vitest: No
  - End-to-End Testing: No
  - ESLint: Yes
  - Prettier: Yes
- Install dependencies: `npm install`
- Review generated folder structure:
  ```
  client/
  ├── public/          # Static assets
  ├── src/
  │   ├── assets/      # Images, fonts, etc.
  │   ├── components/  # Vue components
  │   ├── router/      # Vue Router config
  │   ├── views/       # Page components
  │   ├── App.vue      # Root component
  │   └── main.js      # Application entry point
  ├── index.html       # HTML template
  ├── vite.config.js   # Vite configuration
  └── package.json     # Dependencies and scripts
  ```

**3. Development Server Configuration**
- Start the development server:
  ```bash
  npm run dev
  ```
- The server will start on `http://localhost:5173` (Vite's default port)
- **Understanding Hot Module Replacement (HMR):**
  - HMR allows you to see changes instantly without losing application state
  - When you edit a `.vue` file, only that component reloads
  - CSS changes apply immediately without page refresh
  - Try editing `src/App.vue` and watch the browser update automatically
- Configure Vite for backend integration in `vite.config.js`:
  ```javascript
  export default defineConfig({
    plugins: [vue()],
    server: {
      port: 3000,  // Change from default 5173 to 3000
      proxy: {
        '/api': {
          target: 'http://localhost:5000',  // Your Express backend from Module 1
          changeOrigin: true,
          secure: false
        }
      }
    }
  })
  ```
- Test the build process:
  ```bash
  npm run build
  npm run preview  # Preview production build locally
  ```

**4. Project Integration with Module 1 Setup**
- Update your root `package.json` scripts to manage both server and client:
  ```json
  {
    "scripts": {
      "dev": "npm run dev:server & npm run dev:client",
      "dev:server": "cd server && npm run dev",
      "dev:client": "cd client && npm run dev",
      "build": "npm run build:client && npm run build:server",
      "build:client": "cd client && npm run build",
      "build:server": "cd server && npm run build",
      "start": "cd server && npm start"
    }
  }
  ```
- Set up environment variables for the Vue app:
  1. Create `client/.env.development`:
     ```
     VITE_API_URL=http://localhost:5000/api
     VITE_APP_NAME=TaskFlow
     ```
  2. Access in Vue components:
     ```javascript
     const apiUrl = import.meta.env.VITE_API_URL
     ```
- Ensure Git integration:
  - The Vue project should already be part of your monorepo
  - Check that `client/node_modules` is in `.gitignore`
  - Verify `client/dist` (build output) is also ignored
- Test concurrent development:
  ```bash
  # From root directory
  npm run dev  # Should start both server and client
  ```

**Key Decision Points:**
- **TypeScript adoption:** Skip for now to focus on Vue fundamentals - we'll add it in Module 7
- **Router inclusion:** Essential for SPA navigation (always choose Yes)
- **State management (Pinia):** Not needed yet - we'll use component state for now
- **Testing framework:** Skip for initial setup - we'll add testing in Module 6
- **Code quality tools:** Always include ESLint and Prettier for consistent code

**Verification Steps:**
1. Confirm `npm run dev` starts development server on port 3000
2. Test Hot Module Replacement:
   - Edit text in `src/App.vue` - should update instantly
   - Change styles - should apply without refresh
   - Add a new component - should hot reload
3. Verify build process:
   - Run `npm run build` - should create `dist/` folder
   - Check `dist/` contains optimized HTML, JS, and CSS files
   - Run `npm run preview` to test production build
4. Test monorepo integration:
   - From root: `npm run dev` starts both server and client
   - API proxy works: `fetch('/api/health')` reaches your Express server
   - Environment variables load correctly

---

### Task 2.2: Tailwind CSS Integration
**Objective:** Configure Tailwind CSS 4 for utility-first styling with Vite integration

**What you need to accomplish:**
- Install Tailwind CSS 4 with the new Vite plugin
- Configure Tailwind for Vue.js components
- Set up custom design tokens for TaskFlow
- Test utility classes and responsive design

**Documentation to consult:**
- [Tailwind CSS with Vite](https://tailwindcss.com/docs/installation/using-vite)
- [Tailwind CSS Configuration](https://tailwindcss.com/docs/configuration)
- [Tailwind CSS Framework Guide for Vue](https://tailwindcss.com/docs/installation/framework-guides)

**Step-by-step installation for Tailwind CSS 4:**

**1. Install Tailwind CSS 4 with Vite Plugin**
```bash
# In your client/ directory
cd client/
npm install tailwindcss @tailwindcss/vite
```

**Why the new approach?**
- **@tailwindcss/vite**: The new official Vite plugin for Tailwind CSS 4
- **No PostCSS setup needed**: The Vite plugin handles everything automatically
- **Better performance**: Native Vite integration with faster builds
- **Simplified configuration**: Less setup compared to the traditional PostCSS approach

**2. Configure Vite Plugin**
Update your `client/vite.config.js` to include the Tailwind plugin:
```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [
    vue(),
    tailwindcss(),  // Add Tailwind CSS plugin
  ],
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: 'http://localhost:5000',
        changeOrigin: true,
        secure: false
      }
    }
  }
})
```

**3. Import Tailwind CSS**
Update your main CSS file `client/src/style.css` (or create it):
```css
@import "tailwindcss";

/* Custom TaskFlow styles */
:root {
  --taskflow-primary: #2563eb;
  --taskflow-secondary: #64748b;
  --taskflow-success: #059669;
  --taskflow-warning: #d97706;
  --taskflow-error: #dc2626;
}

/* Custom component styles can go here */
```

**4. Import CSS in Your Vue App**
Ensure the CSS is imported in `client/src/main.js`:
```javascript
import { createApp } from 'vue'
import { createRouter, createWebHistory } from 'vue-router'
import App from './App.vue'
import './style.css'  // Import Tailwind CSS

// Router setup...
const app = createApp(App)
app.use(router)
app.mount('#app')
```

**5. Configure Tailwind (Optional)**
Create `client/tailwind.config.js` for customization:
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{vue,js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        'taskflow': {
          primary: '#2563eb',
          secondary: '#64748b',
          accent: '#7c3aed',
          success: '#059669',
          warning: '#d97706',
          error: '#dc2626',
        }
      },
      fontFamily: {
        'sans': ['Inter', 'system-ui', 'sans-serif'],
      }
    },
  },
  plugins: [],
}
```

**Acceptance criteria:**
- [ ] Tailwind CSS 4 with Vite plugin properly installed
- [ ] Vite configuration updated with @tailwindcss/vite plugin
- [ ] Styles compile without errors using new import syntax
- [ ] TaskFlow custom design tokens defined
- [ ] Utility classes work in Vue components
- [ ] Hot reloading works with CSS changes

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Vue 3 project successfully running from Task 2.1
- [ ] Understanding of utility-first CSS approach
- [ ] Familiarity with Vite and how plugins work
- [ ] Basic knowledge of CSS imports and custom properties

### Step-by-Step Implementation Approach

**1. Tailwind 4 Installation with Vite Plugin**
- Install the new `@tailwindcss/vite` plugin (no PostCSS setup required)
- Configure the Vite plugin in your config file
- Understand how the new plugin simplifies the build process
- Import Tailwind using the new `@import "tailwindcss"` syntax

**2. TaskFlow Design System Setup**
- Import Tailwind in your main CSS file using the new syntax
- Define TaskFlow-specific color palette and design tokens
- Set up custom CSS variables for consistent theming
- Configure content paths for Vue file detection (optional with new plugin)

**3. Integration with Vue Components**
- Import Tailwind styles in your main application entry point
- Test utility classes work correctly in Vue components
- Understand how to use Tailwind classes with dynamic Vue styling
- Configure any necessary CSS-in-JS patterns for dynamic styles

**4. Production Optimization and Testing**
- Verify Tailwind's automatic CSS optimization in production builds
- Test that custom TaskFlow colors work in both development and production
- Ensure build sizes are optimized with the new Vite plugin
- Confirm hot reloading works seamlessly during development

**Key Decision Points:**
- **Vite plugin vs PostCSS:** Using the new @tailwindcss/vite plugin is recommended for better performance
- **TaskFlow design system:** Establish consistent colors and spacing for the application
- **Component patterns:** Plan when to use utility classes vs custom CSS components
- **Dark mode planning:** Consider TaskFlow's future dark mode requirements

**Verification Steps:**
1. Test basic utility classes in a Vue component:
   ```vue
   <template>
     <div class="bg-taskflow-primary text-white p-4 rounded-lg">
       <h1 class="text-2xl font-bold">TaskFlow Header</h1>
       <p class="text-sm opacity-90">Testing Tailwind CSS 4 integration</p>
     </div>
   </template>
   ```
2. Verify responsive classes work: `md:text-lg lg:text-xl`
3. Test custom colors: `bg-taskflow-primary`, `text-taskflow-secondary`
4. Confirm build optimization: run `npm run build` and check CSS bundle size
5. Test hot reloading: change classes and verify instant updates

---

### Task 2.3: Vue Router Configuration
**Objective:** Set up client-side routing for single-page application navigation with TaskFlow routes

**What you need to accomplish:**
- Configure Vue Router 4 with modern Composition API setup
- Create route definitions for TaskFlow application areas
- Build view components for each route
- Set up nested routing structure for complex features
- Implement basic navigation guards for future authentication
- Configure route metadata and navigation

**Documentation to consult:**
- [Vue Router 4 Installation](https://router.vuejs.org/installation.html)
- [Vue Router 4 Guide](https://router.vuejs.org/guide/)
- [Composition API with Router](https://router.vuejs.org/guide/advanced/composition-api.html)
- [Navigation Guards](https://router.vuejs.org/guide/advanced/navigation-guards.html)

**Step-by-step implementation:**

**1. Verify Router Installation**
Since you selected Vue Router during project creation, verify it's properly installed:
```bash
# Check if Vue Router is in package.json
cat client/package.json | grep vue-router
```

If not installed:
```bash
cd client/
npm install vue-router@4
```

**2. Router Configuration Setup**

Examine your existing `client/src/router/index.js` and update it:
```javascript
import { createRouter, createWebHistory } from 'vue-router'

// Import view components (we'll create these next)
import HomeView from '../views/HomeView.vue'
import DashboardView from '../views/DashboardView.vue'
import ProjectsView from '../views/ProjectsView.vue'
import ProjectDetailView from '../views/ProjectDetailView.vue'
import LoginView from '../views/auth/LoginView.vue'
import RegisterView from '../views/auth/RegisterView.vue'
import ProfileView from '../views/ProfileView.vue'
import NotFoundView from '../views/NotFoundView.vue'

const routes = [
  // Public routes
  {
    path: '/',
    name: 'Home',
    component: HomeView,
    meta: {
      title: 'TaskFlow - Collaborative Task Management',
      requiresAuth: false
    }
  },
  
  // Authentication routes
  {
    path: '/login',
    name: 'Login',
    component: LoginView,
    meta: {
      title: 'Login - TaskFlow',
      requiresAuth: false,
      redirectIfAuthenticated: true
    }
  },
  {
    path: '/register',
    name: 'Register', 
    component: RegisterView,
    meta: {
      title: 'Register - TaskFlow',
      requiresAuth: false,
      redirectIfAuthenticated: true
    }
  },
  
  // Protected routes (require authentication)
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: DashboardView,
    meta: {
      title: 'Dashboard - TaskFlow',
      requiresAuth: true
    }
  },
  {
    path: '/projects',
    name: 'Projects',
    component: ProjectsView,
    meta: {
      title: 'Projects - TaskFlow',
      requiresAuth: true
    }
  },
  {
    path: '/projects/:id',
    name: 'ProjectDetail',
    component: ProjectDetailView,
    meta: {
      title: 'Project Details - TaskFlow',
      requiresAuth: true
    },
    props: true // Pass route params as props
  },
  {
    path: '/profile',
    name: 'Profile',
    component: ProfileView,
    meta: {
      title: 'Profile - TaskFlow',
      requiresAuth: true
    }
  },
  
  // 404 catch-all route
  {
    path: '/:pathMatch(.*)*',
    name: 'NotFound',
    component: NotFoundView,
    meta: {
      title: '404 - Page Not Found'
    }
  }
]

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes,
  // Scroll behavior for better UX
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition
    } else {
      return { top: 0 }
    }
  }
})

// Global navigation guard (basic setup for future authentication)
router.beforeEach((to, from, next) => {
  // Update document title
  document.title = to.meta.title || 'TaskFlow'
  
  // For now, just proceed (we'll add auth logic in later modules)
  next()
})

export default router
```

**3. Create View Components**

Create the views folder structure and components:
```bash
# Create views directory structure
mkdir -p client/src/views/auth
```

**HomeView.vue** (Landing page):
```vue
<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100">
    <!-- Hero Section -->
    <div class="container mx-auto px-4 py-16">
      <div class="text-center">
        <h1 class="text-5xl font-bold text-gray-900 mb-6">
          Welcome to <span class="text-taskflow-primary">TaskFlow</span>
        </h1>
        <p class="text-xl text-gray-600 mb-8 max-w-2xl mx-auto">
          Streamline your team's productivity with our collaborative task management platform.
          Organize projects, assign tasks, and track progress in real-time.
        </p>
        
        <!-- CTA Buttons -->
        <div class="space-x-4">
          <router-link 
            to="/register" 
            class="bg-taskflow-primary text-white px-8 py-3 rounded-lg font-semibold hover:bg-blue-700 transition-colors"
          >
            Get Started Free
          </router-link>
          <router-link 
            to="/login" 
            class="bg-white text-taskflow-primary px-8 py-3 rounded-lg font-semibold border border-taskflow-primary hover:bg-blue-50 transition-colors"
          >
            Sign In
          </router-link>
        </div>
      </div>
      
      <!-- Features Preview -->
      <div class="mt-16 grid md:grid-cols-3 gap-8">
        <div class="text-center p-6 bg-white rounded-lg shadow-md">
          <div class="w-12 h-12 bg-taskflow-primary rounded-lg mx-auto mb-4 flex items-center justify-center">
            <span class="text-white text-xl">📋</span>
          </div>
          <h3 class="text-lg font-semibold mb-2">Project Management</h3>
          <p class="text-gray-600">Organize tasks into projects with customizable workflows</p>
        </div>
        
        <div class="text-center p-6 bg-white rounded-lg shadow-md">
          <div class="w-12 h-12 bg-taskflow-primary rounded-lg mx-auto mb-4 flex items-center justify-center">
            <span class="text-white text-xl">👥</span>
          </div>
          <h3 class="text-lg font-semibold mb-2">Team Collaboration</h3>
          <p class="text-gray-600">Work together seamlessly with real-time updates</p>
        </div>
        
        <div class="text-center p-6 bg-white rounded-lg shadow-md">
          <div class="w-12 h-12 bg-taskflow-primary rounded-lg mx-auto mb-4 flex items-center justify-center">
            <span class="text-white text-xl">📊</span>
          </div>
          <h3 class="text-lg font-semibold mb-2">Progress Tracking</h3>
          <p class="text-gray-600">Monitor project progress with intuitive dashboards</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
// This page doesn't need any reactive state yet
// We'll add analytics tracking and API calls in later modules
</script>
```

**DashboardView.vue** (Main app dashboard):
```vue
<template>
  <div class="p-6">
    <div class="mb-8">
      <h1 class="text-3xl font-bold text-gray-900">Dashboard</h1>
      <p class="text-gray-600 mt-2">Welcome back! Here's what's happening with your projects.</p>
    </div>
    
    <!-- Dashboard Stats -->
    <div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
      <div class="bg-white p-6 rounded-lg shadow-sm border">
        <h3 class="text-sm font-medium text-gray-500">Active Projects</h3>
        <p class="text-2xl font-bold text-taskflow-primary mt-2">12</p>
      </div>
      <div class="bg-white p-6 rounded-lg shadow-sm border">
        <h3 class="text-sm font-medium text-gray-500">Tasks Completed</h3>
        <p class="text-2xl font-bold text-taskflow-success mt-2">48</p>
      </div>
      <div class="bg-white p-6 rounded-lg shadow-sm border">
        <h3 class="text-sm font-medium text-gray-500">Team Members</h3>
        <p class="text-2xl font-bold text-taskflow-secondary mt-2">8</p>
      </div>
      <div class="bg-white p-6 rounded-lg shadow-sm border">
        <h3 class="text-sm font-medium text-gray-500">Due This Week</h3>
        <p class="text-2xl font-bold text-taskflow-warning mt-2">6</p>
      </div>
    </div>
    
    <!-- Quick Actions -->
    <div class="bg-white rounded-lg shadow-sm border p-6">
      <h2 class="text-xl font-semibold mb-4">Quick Actions</h2>
      <div class="space-x-4">
        <router-link 
          to="/projects" 
          class="bg-taskflow-primary text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-colors"
        >
          View All Projects
        </router-link>
        <button class="bg-gray-100 text-gray-700 px-4 py-2 rounded-lg hover:bg-gray-200 transition-colors">
          Create New Task
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
// Dashboard logic will be added in later modules
// For now, this is a static layout to test routing
</script>
```

**Basic Auth Views** (LoginView.vue and RegisterView.vue):
```vue
<!-- client/src/views/auth/LoginView.vue -->
<template>
  <div class="min-h-screen bg-gray-50 flex items-center justify-center">
    <div class="max-w-md w-full space-y-8 p-8">
      <div class="text-center">
        <h2 class="text-3xl font-bold text-gray-900">Sign in to TaskFlow</h2>
        <p class="mt-2 text-gray-600">Access your projects and collaborate with your team</p>
      </div>
      
      <!-- Login form will be implemented in Module 3 -->
      <div class="bg-white p-6 rounded-lg shadow-sm border">
        <p class="text-center text-gray-500">Login form will be implemented in Module 3</p>
        <div class="mt-4 text-center">
          <router-link 
            to="/dashboard" 
            class="text-taskflow-primary hover:underline"
          >
            Continue to Dashboard (Demo)
          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
// Login logic will be implemented in Module 3
</script>
```

**Other View Components** (create similar basic structures):
- `RegisterView.vue`
- `ProjectsView.vue` 
- `ProjectDetailView.vue`
- `ProfileView.vue`
- `NotFoundView.vue`

**4. Navigation Guards Setup**

The router already includes a basic navigation guard. Here's what it does:
```javascript
// Global navigation guard (basic setup for future authentication)
router.beforeEach((to, from, next) => {
  // Update document title based on route meta
  document.title = to.meta.title || 'TaskFlow'
  
  // TODO: Add authentication checks in Module 4
  // For now, just proceed to the route
  next()
})
```

**Future authentication logic** (will be implemented in Module 4):
```javascript
// Example of what we'll add later:
router.beforeEach((to, from, next) => {
  const isAuthenticated = checkAuthStatus() // Will implement later
  
  if (to.meta.requiresAuth && !isAuthenticated) {
    next('/login')
  } else if (to.meta.redirectIfAuthenticated && isAuthenticated) {
    next('/dashboard')
  } else {
    next()
  }
})
```

**Acceptance criteria:**
- [ ] Vue Router 4 properly configured with history mode
- [ ] All TaskFlow routes defined with proper metadata
- [ ] View components created for each route
- [ ] Navigation between routes works smoothly
- [ ] URL changes reflect in browser and are bookmarkable
- [ ] Basic navigation guards implemented for title updates
- [ ] Router-link components work correctly
- [ ] 404 page displays for invalid routes

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Vue Router included during project initialization (or installed separately)
- [ ] Understanding of client-side routing concepts
- [ ] Knowledge of Vue 3 Composition API for router usage
- [ ] Planning completed for your application's route structure

### Step-by-Step Implementation Approach

**1. Router Configuration Setup**
- Examine the generated router configuration from Vue project creation
- Use history mode (createWebHistory) for clean URLs without # symbol
- Configure router with proper base URL and scroll behavior
- Plan route hierarchy: public routes, auth routes, protected routes

**2. Route Definition and Components**
- Create comprehensive view components for TaskFlow application
- Define route objects with path, component, meta properties, and names
- Use route metadata for titles, authentication requirements, and other flags
- Implement dynamic routes with parameters and props passing

**3. Navigation Implementation and Testing**
- Use router-link components for declarative navigation with proper styling
- Test programmatic navigation with useRouter composable in components
- Set up basic navigation guards for title updates and future auth logic
- Implement proper scroll behavior and route transition handling

**4. Router Enhancement and Optimization**
- Configure comprehensive route metadata for SEO and auth
- Plan for lazy loading implementation in future performance optimization
- Implement proper 404 error handling with custom NotFound component
- Prepare structure for route transition animations

**Key Decision Points:**
- **History mode vs. hash mode:** History mode preferred for SEO, hash mode for simple hosting
- **Route organization:** Flat vs. nested structure based on your application complexity
- **Code splitting:** Decide which routes should be lazy-loaded for performance
- **Navigation guard strategy:** Plan authentication and permission checking approach

**Verification Steps:**
1. **Test route navigation:**
   - Navigate to each route via URL bar: `/`, `/login`, `/dashboard`, etc.
   - Click router-link components and verify smooth navigation
   - Test browser back/forward buttons work correctly

2. **Verify dynamic routes:**
   - Navigate to `/projects/123` and confirm ProjectDetail component loads
   - Check that route parameters are accessible in the component
   - Test different project IDs work correctly

3. **Check route metadata:**
   - Verify page titles update correctly when navigating
   - Confirm document.title changes reflect route meta.title

4. **Test error handling:**
   - Navigate to non-existent route like `/invalid-page`
   - Verify 404 NotFound component displays
   - Check that invalid routes don't break the application

5. **Validate router-link styling:**
   - Ensure navigation links have proper hover states
   - Verify active route styling (if implemented)
   - Test responsive navigation behavior

---

### Task 2.4: Component Architecture Setup
**Objective:** Create a scalable component structure following Vue 3 best practices

**What you need to accomplish:**
- Design component hierarchy
- Create base/shared components
- Set up component organization patterns
- Implement composition API patterns

**Documentation to consult:**
- [Vue 3 Component Basics](https://vuejs.org/guide/essentials/component-basics.html)
- [Composition API](https://vuejs.org/guide/typescript/composition-api.html)
- [Component Design Patterns](https://vuejs.org/guide/reusability/composables.html)

**Component structure to create:**
```
client/src/
├── components/
│   ├── base/           # Reusable base components
│   │   ├── BaseButton.vue
│   │   ├── BaseInput.vue
│   │   └── BaseModal.vue
│   ├── layout/         # Layout components
│   │   ├── AppHeader.vue
│   │   ├── AppSidebar.vue
│   │   └── AppFooter.vue
│   └── features/       # Feature-specific components
│       ├── auth/
│       ├── projects/
│       └── dashboard/
├── views/              # Route components
├── composables/        # Reusable composition functions
└── utils/             # Utility functions
```

**Base component example (BaseButton.vue):**
```vue
<template>
  <button
    :class="buttonClasses"
    :disabled="disabled"
    @click="$emit('click', $event)"
  >
    <slot />
  </button>
</template>

<script setup>
// Define props, emits, and computed classes
// Use Tailwind for styling variants
</script>
```

**Acceptance criteria:**
- [ ] Component folder structure created
- [ ] Base components implemented
- [ ] Layout components created
- [ ] Composition API patterns used
- [ ] Components properly exported

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Understanding of Vue 3 Composition API syntax
- [ ] Knowledge of component communication patterns (props, emits, slots)
- [ ] Familiarity with script setup syntax
- [ ] Planning completed for your component hierarchy

### Step-by-Step Implementation Approach

**1. Component Architecture Planning**
- Research Vue 3 component organization best practices
- Plan your component hierarchy from atomic to complex components
- Decide on naming conventions for different component types
- Consider reusability and maintainability in your structure

**2. Base Component Development**
- Create foundational components that will be reused throughout the app
- Focus on components like buttons, inputs, modals, and cards
- Use prop validation with Vue's built-in prop system
- Implement proper event emission patterns for component communication

**3. Layout Component Structure**
- Design components that handle application layout and navigation
- Create header, sidebar, footer, and main content area components
- Implement responsive behavior within layout components
- Set up proper slot usage for flexible content areas

**4. Composition API Integration**
- Use script setup syntax for cleaner component code
- Create reusable composables for shared logic
- Implement reactive state management within components
- Set up proper lifecycle hook usage for component behavior

**Key Decision Points:**
- **Component granularity:** Balance between reusability and simplicity
- **Prop vs. slot usage:** Decide when to use props vs. slots for component API
- **State management:** Plan for component-level vs. global state
- **Prop validation:** Use Vue's prop validation features for component safety

**Verification Steps:**
1. Test that base components work with different prop combinations
2. Verify component events are properly emitted and handled
3. Confirm layout components adapt to different content scenarios
4. Check that components follow consistent patterns and conventions

---

### Task 2.5: Responsive Layout Implementation
**Objective:** Create a responsive application layout with navigation

**What you need to accomplish:**
- Design mobile-first responsive layout
- Implement navigation header with menu
- Create sidebar for desktop navigation
- Add responsive behavior for different screen sizes

**Documentation to consult:**
- [Tailwind CSS Responsive Design](https://tailwindcss.com/docs/responsive-design)
- [Vue 3 Reactivity](https://vuejs.org/guide/essentials/reactivity-fundamentals.html)
- [CSS Grid and Flexbox](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout)

**Layout requirements:**
- Mobile: Hamburger menu with overlay
- Tablet: Collapsible sidebar
- Desktop: Fixed sidebar with main content area
- Responsive typography and spacing

**Responsive breakpoints to use:**
```css
/* Tailwind breakpoints */
sm: 640px   /* Mobile landscape */
md: 768px   /* Tablet */
lg: 1024px  /* Desktop */
xl: 1280px  /* Large desktop */
```

**Layout composition pattern:**
```vue
<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <AppHeader @toggle-sidebar="toggleSidebar" />
    
    <!-- Layout Grid -->
    <div class="flex">
      <!-- Sidebar -->
      <AppSidebar :is-open="sidebarOpen" />
      
      <!-- Main Content -->
      <main class="flex-1 p-4 lg:p-8">
        <router-view />
      </main>
    </div>
  </div>
</template>
```

**Acceptance criteria:**
- [ ] Responsive layout works on all screen sizes
- [ ] Navigation is accessible and functional
- [ ] Sidebar toggle works on mobile
- [ ] Content area adjusts properly
- [ ] Touch-friendly interface on mobile

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Layout components created in previous task
- [ ] Understanding of mobile-first responsive design principles
- [ ] Knowledge of Tailwind CSS responsive classes
- [ ] Familiarity with CSS Grid and Flexbox layouts

### Step-by-Step Implementation Approach

**1. Mobile-First Layout Planning**
- Design the mobile layout first, then enhance for larger screens
- Plan navigation patterns for different screen sizes
- Consider touch interactions and finger-friendly sizing
- Map out how content will reflow at different breakpoints

**2. Responsive Navigation Implementation**
- Create hamburger menu for mobile devices
- Implement sidebar that converts to overlay on small screens
- Add smooth transitions between mobile and desktop navigation states
- Ensure navigation is accessible via keyboard and screen readers

**3. Grid and Flexbox Layout System**
- Use Tailwind's responsive grid classes for main layout structure
- Implement flexible content areas that adapt to screen size
- Create consistent spacing and proportions across breakpoints
- Test layout behavior when content length varies

**4. Interactive Behavior and State Management**
- Implement sidebar toggle functionality with Vue reactivity
- Add responsive behavior for navigation state management
- Create smooth animations for layout transitions
- Handle edge cases like screen rotation and browser resize

**Key Decision Points:**
- **Breakpoint strategy:** Choose which breakpoints are most important for your users
- **Navigation pattern:** Decide between overlay, push, or reveal sidebar patterns
- **Content priority:** Plan what content is most important on smaller screens
- **Performance considerations:** Balance animations with smooth performance

**Verification Steps:**
1. Test layout on actual mobile devices, not just browser responsive mode
2. Verify navigation works with touch gestures and keyboard navigation
3. Confirm content remains readable and usable at all screen sizes
4. Check that interactive elements are large enough for touch interaction

## Challenge Extensions

### Advanced Component Patterns
- Implement compound components pattern
- Create renderless components
- Set up component composition with slots
- Add component lifecycle management

### Animation and Transitions
- Add Vue transition components
- Implement page transition animations
- Create micro-interactions with CSS
- Set up loading state animations

### Accessibility Improvements
- Add ARIA labels and roles
- Implement keyboard navigation
- Ensure color contrast compliance
- Add screen reader support

### Performance Optimization
- Set up dynamic imports for routes
- Implement component lazy loading
- Configure bundle splitting
- Add service worker basics

## Troubleshooting Common Issues

### Vite Build Issues
- Check import paths are correct
- Verify all dependencies are installed
- Ensure proper file extensions (.vue, .js)
- Check for circular dependencies

### Tailwind Not Working
- Verify PostCSS configuration
- Check content paths in config
- Ensure @tailwind directives are included
- Restart dev server after config changes

### Router Navigation Issues
- Check route definitions syntax
- Verify component imports
- Ensure router-view is in template
- Check for conflicting routes

### Component Communication
- Use props for parent-to-child data
- Use emits for child-to-parent events
- Consider provide/inject for deep nesting
- Avoid direct DOM manipulation

## Module Completion Checklist

Before moving to Module 3, ensure:
- [ ] Vue 3 application runs without errors
- [ ] Tailwind CSS styling works properly
- [ ] All routes navigate correctly
- [ ] Component structure is well-organized
- [ ] Layout is fully responsive
- [ ] Code follows Vue 3 best practices
- [ ] All components are properly documented
- [ ] Changes committed and pushed to repository

## Next Module Preview
Module 3 will focus on implementing robust form handling and validation using VeeValidate and Zod. You'll build user registration and login forms that provide excellent user experience and data validation.