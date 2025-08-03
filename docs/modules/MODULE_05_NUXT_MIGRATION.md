# Module 5: Nuxt Migration & Setup

**Duration:** 4-5 days  
**Branch:** `feature/module-5-nuxt-migration`  
**Section:** 🚀 **ADVANCED**

## Learning Objectives
- Understand Nuxt 3 architecture and benefits over traditional Vue SPAs
- Migrate existing Vue 3 application to Nuxt 3 framework
- Configure file-based routing and auto-imports
- Set up SSR/SSG rendering modes
- Integrate existing components and utilities into Nuxt structure

## Overview
This module marks the beginning of the Advanced Section. You'll transform your existing Vue application into a modern Nuxt 3 application, gaining server-side rendering, automatic optimizations, and a more scalable architecture. This migration will serve as the foundation for all subsequent advanced features.

## Pre-Migration Checklist

Before starting, ensure your current application has:
- [ ] Working Vue 3 application with Composition API
- [ ] Vue Router setup with defined routes
- [ ] Tailwind CSS configured and working
- [ ] VeeValidate forms with Zod validation
- [ ] Base components created and functional
- [ ] Express.js backend with basic endpoints

## Tasks

### Task 5.1: Nuxt 3 Project Setup
**Objective:** Create a new Nuxt 3 project and understand the framework structure

**What you need to accomplish:**
- Initialize new Nuxt 3 project alongside existing Vue app
- Understand Nuxt 3 directory structure and conventions
- Configure basic Nuxt settings
- Set up development environment

**Documentation to consult:**
- [Nuxt 3 Getting Started](https://nuxt.com/docs/getting-started/installation)
- [Nuxt 3 Directory Structure](https://nuxt.com/docs/guide/directory-structure)
- [Nuxt 3 Configuration](https://nuxt.com/docs/api/configuration/nuxt-config)

**Setup process:**
```bash
# Create new Nuxt project in your project root
npx nuxi@latest init client-nuxt
cd client-nuxt

# Install dependencies
npm install

# Start development server to verify setup
npm run dev
```

**Directory structure to understand:**
```
client-nuxt/
├── components/          # Auto-imported Vue components
├── composables/         # Auto-imported composition functions
├── layouts/            # Application layouts
├── middleware/         # Route middleware
├── pages/              # File-based routing
├── plugins/            # Nuxt plugins
├── public/             # Static assets
├── server/             # Server-side code
│   └── api/            # API routes
├── utils/              # Auto-imported utilities
├── app.vue             # Main app component
└── nuxt.config.ts      # Nuxt configuration
```

**Initial configuration to set up:**
```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  devtools: { enabled: true },
  css: ['~/assets/css/main.css'],
  modules: [
    '@nuxtjs/tailwindcss',
    '@pinia/nuxt'
  ],
  // SSR configuration
  ssr: true,
  // Development configuration
  nitro: {
    port: 3000
  }
})
```

**Acceptance criteria:**
- [ ] Nuxt 3 project successfully created
- [ ] Development server runs without errors
- [ ] Basic configuration applied
- [ ] Directory structure understood
- [ ] Hot reload working

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Completed Modules 1-4 successfully
- [ ] Understanding of the differences between SPA and universal applications
- [ ] Node.js 16+ installed
- [ ] Familiarity with Vue 3 Composition API

### Step-by-Step Implementation Approach

**1. Nuxt 3 Architecture Understanding**
- Research the benefits of server-side rendering vs. client-side rendering
- Understand Nuxt 3's file-based routing system
- Learn about auto-imports and how they differ from manual imports
- Study the Nuxt 3 directory conventions and their purposes

**2. Project Creation and Initial Setup**
- Create the Nuxt project alongside your existing Vue application
- Compare the generated structure with your current Vue app
- Test the development server and verify hot reload functionality
- Explore the default components and pages created by Nuxt

**3. Basic Configuration**
- Configure Nuxt for your development environment
- Set up essential modules like Tailwind CSS and Pinia
- Configure SSR settings based on your application needs
- Test that the configuration works as expected

**4. Development Environment Integration**
- Update your root package.json scripts to include Nuxt commands
- Ensure Nuxt project works with your existing development workflow
- Test that environment variables can be accessed in Nuxt
- Verify the project structure supports team development

**Key Decision Points:**
- **SSR vs. SPA mode:** Understand when to use each rendering mode
- **Module selection:** Choose Nuxt modules that provide value for your application
- **Migration strategy:** Plan how to gradually migrate components from Vue to Nuxt
- **Development workflow:** Integrate Nuxt into your existing development processes

**Verification Steps:**
1. Confirm Nuxt development server starts successfully
2. Test that file-based routing works by creating a simple page
3. Verify auto-imports work with a test component
4. Check that Tailwind CSS integration works correctly

---

### Task 5.2: Tailwind CSS Migration
**Objective:** Migrate Tailwind CSS configuration and styles to Nuxt

**What you need to accomplish:**
- Install and configure Tailwind CSS in Nuxt
- Migrate existing Tailwind configuration
- Transfer custom styles and design tokens
- Ensure styling consistency

**Documentation to consult:**
- [Nuxt Tailwind CSS Module](https://tailwindcss.nuxtjs.org/)
- [Tailwind CSS Configuration](https://tailwindcss.com/docs/configuration)

**Installation and setup:**
```bash
# Install Tailwind CSS module
npm install --save-dev @nuxtjs/tailwindcss

# Generate Tailwind config
npx tailwindcss init
```

**Configuration migration:**
```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: [
    '@nuxtjs/tailwindcss'
  ],
  tailwindcss: {
    cssPath: '~/assets/css/tailwind.css',
    configPath: 'tailwind.config.js',
    exposeConfig: false,
    injectPosition: 0,
    viewer: true
  }
})
```

**Copy existing Tailwind configuration:**
```javascript
// tailwind.config.js - migrate from your Vue project
module.exports = {
  content: [
    "./components/**/*.{js,vue,ts}",
    "./layouts/**/*.vue",
    "./pages/**/*.vue",
    "./plugins/**/*.{js,ts}",
    "./nuxt.config.{js,ts}",
    "./app.vue"
  ],
  theme: {
    extend: {
      // Copy your existing theme extensions
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          900: '#1e3a8a',
        }
      }
    }
  },
  plugins: []
}
```

**Create main CSS file:**
```css
/* assets/css/tailwind.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

/* Migrate any custom CSS from your Vue project */
```

**Acceptance criteria:**
- [ ] Tailwind CSS module installed and configured
- [ ] Existing Tailwind configuration migrated
- [ ] Custom styles working
- [ ] Design tokens preserved
- [ ] Hot reload working for styles

---

### Task 5.3: Component Migration
**Objective:** Migrate existing Vue components to Nuxt auto-import structure

**What you need to accomplish:**
- Transfer components to Nuxt components directory
- Adapt components for auto-import functionality
- Maintain component functionality and props
- Update component organization

**Documentation to consult:**
- [Nuxt 3 Components](https://nuxt.com/docs/guide/directory-structure/components)
- [Auto-imports in Nuxt](https://nuxt.com/docs/guide/concepts/auto-imports)

**Component migration strategy:**
```
Vue project structure → Nuxt structure
client/src/components/  → client-nuxt/components/

base/BaseButton.vue     → components/Base/Button.vue
base/BaseInput.vue      → components/Base/Input.vue
layout/AppHeader.vue    → components/App/Header.vue
features/auth/LoginForm.vue → components/Auth/LoginForm.vue
```

**Example component migration:**
```vue
<!-- components/Base/Button.vue -->
<template>
  <button
    :class="buttonClasses"
    :disabled="disabled"
    @click="$emit('click', $event)"
  >
    <slot />
  </button>
</template>

<script setup lang="ts">
// Define props and emits (same as Vue project)
interface Props {
  variant?: 'primary' | 'secondary' | 'danger'
  size?: 'sm' | 'md' | 'lg'
  disabled?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  variant: 'primary',
  size: 'md',
  disabled: false
})

const emit = defineEmits<{
  click: [event: MouseEvent]
}>()

// Component logic remains the same
const buttonClasses = computed(() => {
  // Your existing class logic
})
</script>
```

**Auto-import usage:**
```vue
<!-- pages/login.vue - No import statements needed! -->
<template>
  <div>
    <BaseButton @click="handleLogin">
      Sign In
    </BaseButton>
  </div>
</template>

<script setup>
// BaseButton is automatically available
const handleLogin = () => {
  // Login logic
}
</script>
```

**Nested component organization:**
```
components/
├── Base/
│   ├── Button.vue      # <BaseButton>
│   ├── Input.vue       # <BaseInput>
│   └── Modal.vue       # <BaseModal>
├── App/
│   ├── Header.vue      # <AppHeader>
│   └── Footer.vue      # <AppFooter>
└── Auth/
    ├── LoginForm.vue   # <AuthLoginForm>
    └── RegisterForm.vue # <AuthRegisterForm>
```

**Acceptance criteria:**
- [ ] All components migrated to Nuxt structure
- [ ] Auto-import functionality working
- [ ] Component functionality preserved
- [ ] Proper naming conventions followed
- [ ] Components render without errors

---

### Task 5.4: File-Based Routing Migration
**Objective:** Convert Vue Router configuration to Nuxt file-based routing

**What you need to accomplish:**
- Analyze existing route structure
- Create corresponding page files in Nuxt
- Implement dynamic routes and nested routing
- Handle route parameters and navigation

**Documentation to consult:**
- [Nuxt 3 Routing](https://nuxt.com/docs/getting-started/routing)
- [Dynamic Routes](https://nuxt.com/docs/guide/directory-structure/pages)
- [Nested Routes](https://nuxt.com/docs/guide/directory-structure/pages#nested-routes)

**Route mapping from Vue Router to Nuxt:**
```javascript
// Old Vue Router structure
const routes = [
  { path: '/', component: Dashboard },
  { path: '/login', component: Login },
  { path: '/register', component: Register },
  { path: '/projects', component: Projects },
  { path: '/projects/:id', component: ProjectDetail },
  { path: '/profile', component: Profile }
]

// New Nuxt file structure
pages/
├── index.vue           # / (Dashboard)
├── login.vue           # /login
├── register.vue        # /register
├── profile.vue         # /profile
├── projects/
│   ├── index.vue       # /projects
│   └── [id].vue        # /projects/:id
```

**Page component migration:**
```vue
<!-- pages/index.vue (Dashboard) -->
<template>
  <div>
    <h1>Dashboard</h1>
    <!-- Migrate your existing dashboard content -->
  </div>
</template>

<script setup>
// Use definePageMeta for page configuration
definePageMeta({
  title: 'Dashboard',
  description: 'User dashboard overview'
})

// Page-specific logic
</script>
```

**Dynamic route with parameters:**
```vue
<!-- pages/projects/[id].vue -->
<template>
  <div>
    <h1>Project: {{ project?.name }}</h1>
    <!-- Project detail content -->
  </div>
</template>

<script setup>
// Access route parameters
const route = useRoute()
const projectId = route.params.id

// Fetch project data
const { data: project } = await $fetch(`/api/projects/${projectId}`)
</script>
```

**Layout implementation:**
```vue
<!-- layouts/default.vue -->
<template>
  <div class="min-h-screen bg-gray-50">
    <AppHeader />
    <div class="flex">
      <AppSidebar />
      <main class="flex-1 p-4 lg:p-8">
        <slot />
      </main>
    </div>
  </div>
</template>
```

**Using layouts in pages:**
```vue
<!-- pages/dashboard.vue -->
<template>
  <div>
    <!-- Page content -->
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'default'
})
</script>
```

**Acceptance criteria:**
- [ ] All routes converted to file-based structure
- [ ] Dynamic routes working with parameters
- [ ] Navigation between pages functional
- [ ] Layouts properly implemented
- [ ] Route metadata configured

---

### Task 5.5: Composables and Utils Migration
**Objective:** Convert existing utilities and composables to Nuxt auto-import system

**What you need to accomplish:**
- Migrate utility functions to utils directory
- Convert reusable logic to composables
- Set up auto-imports for custom functions
- Test functionality in Nuxt context

**Documentation to consult:**
- [Nuxt 3 Composables](https://nuxt.com/docs/guide/directory-structure/composables)
- [Nuxt 3 Utils](https://nuxt.com/docs/guide/directory-structure/utils)
- [Auto-imports Configuration](https://nuxt.com/docs/guide/concepts/auto-imports)

**Form handling composable migration:**
```typescript
// composables/useFormSubmission.ts
export const useFormSubmission = () => {
  const isSubmitting = ref(false)
  const submitError = ref('')
  
  const handleSubmit = async (submitFn: Function) => {
    isSubmitting.value = true
    submitError.value = ''
    
    try {
      await submitFn()
    } catch (error: any) {
      submitError.value = error.message
    } finally {
      isSubmitting.value = false
    }
  }
  
  return {
    isSubmitting,
    submitError,
    handleSubmit
  }
}
```

**Utility functions migration:**
```typescript
// utils/validation.ts
export const formatErrorMessage = (error: string): string => {
  return error.charAt(0).toUpperCase() + error.slice(1)
}

export const debounce = <T extends (...args: any[]) => any>(
  func: T,
  wait: number
): ((...args: Parameters<T>) => void) => {
  let timeout: NodeJS.Timeout
  return (...args: Parameters<T>) => {
    clearTimeout(timeout)
    timeout = setTimeout(() => func(...args), wait)
  }
}
```

**Using auto-imported functions:**
```vue
<!-- pages/login.vue -->
<template>
  <div>
    <AuthLoginForm @submit="handleLogin" />
    <div v-if="submitError" class="error">
      {{ formatErrorMessage(submitError) }}
    </div>
  </div>
</template>

<script setup>
// Auto-imported composable and utility
const { handleSubmit, isSubmitting, submitError } = useFormSubmission()

const handleLogin = async (formData: any) => {
  await handleSubmit(async () => {
    // Login logic
    await $fetch('/api/auth/login', {
      method: 'POST',
      body: formData
    })
  })
}
</script>
```

**API client composable:**
```typescript
// composables/useApi.ts
export const useApi = () => {
  const config = useRuntimeConfig()
  
  const apiCall = async <T>(endpoint: string, options: any = {}): Promise<T> => {
    return await $fetch<T>(`${config.public.apiBase}${endpoint}`, {
      ...options,
      headers: {
        ...options.headers,
        // Add auth headers if needed
      }
    })
  }
  
  return {
    apiCall
  }
}
```

**Acceptance criteria:**
- [ ] All utilities migrated to utils directory
- [ ] Composables created for reusable logic
- [ ] Auto-imports working correctly
- [ ] Functions accessible without imports
- [ ] TypeScript types preserved

## Challenge Extensions

### Advanced Nuxt Features
- Implement server-side API routes in Nuxt
- Set up Nuxt plugins for global functionality
- Configure custom middleware for authentication
- Implement dynamic meta tags with useSeoMeta

### Performance Optimization
- Configure lazy loading for components
- Set up critical CSS inlining
- Implement image optimization with Nuxt Image
- Configure bundle analysis and optimization

### Developer Experience
- Set up VS Code extensions for Nuxt
- Configure debugging for SSR and client-side
- Implement hot module replacement optimization
- Set up type checking with Nuxt TypeScript

## Troubleshooting Common Issues

### Auto-import Not Working
- Check file naming conventions
- Verify component directory structure
- Restart development server
- Check Nuxt configuration for auto-import settings

### Styling Issues
- Verify Tailwind configuration paths
- Check CSS import order
- Ensure assets are properly referenced
- Test style isolation between components

### Routing Problems
- Check file naming in pages directory
- Verify dynamic route syntax
- Test navigation programmatically
- Check for conflicting route definitions

### Component Migration Issues
- Verify component prop types
- Check for Vue 3 breaking changes
- Test component functionality individually
- Ensure proper event handling

## Module Completion Checklist

Before moving to Module 6, ensure:
- [ ] Nuxt 3 project successfully created and running
- [ ] All components migrated and auto-importing
- [ ] File-based routing working for all pages
- [ ] Tailwind CSS styling preserved and working
- [ ] Composables and utilities auto-importing
- [ ] Development server runs without errors
- [ ] All existing functionality preserved
- [ ] TypeScript compilation successful

## Next Module Preview
Module 6 will focus on integrating PostgreSQL with Drizzle ORM in the Nuxt context. You'll learn to leverage Nuxt's server-side capabilities to create API routes and handle database operations efficiently within the Nuxt ecosystem.