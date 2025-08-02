# Module 2: Frontend Foundation with Vue 3

**Duration:** 3-4 days  
**Branch:** `feature/module-2-vue-foundation`

## Learning Objectives
- Initialize a modern Vue 3 application with Vite
- Configure Tailwind CSS for utility-first styling
- Set up Vue Router for single-page application navigation
- Create a scalable component architecture
- Implement responsive layouts and navigation

## Overview
This module establishes the frontend foundation using Vue 3's Composition API, modern build tooling with Vite, and Tailwind CSS for rapid UI development. You'll create the basic structure that will support all future features.

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
- Choose TypeScript support (recommended)
- Include Vue Router
- Include ESLint for code quality
- Consider PWA features for future enhancement

**Acceptance criteria:**
- [ ] Vue 3 project successfully created
- [ ] Development server runs without errors
- [ ] Hot reload functionality working
- [ ] Project integrates with existing folder structure
- [ ] Initial commit made with project scaffold

---

### Task 2.2: Tailwind CSS Integration
**Objective:** Configure Tailwind CSS for utility-first styling approach

**What you need to accomplish:**
- Install and configure Tailwind CSS
- Set up purging for production builds
- Configure custom design tokens
- Create base styles and utilities

**Documentation to consult:**
- [Tailwind CSS Installation](https://tailwindcss.com/docs/installation)
- [Tailwind with Vite](https://tailwindcss.com/docs/guides/vite)
- [Tailwind Configuration](https://tailwindcss.com/docs/configuration)

**Installation steps to research:**
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**Configuration hints:**
- Set up content paths for Vue files
- Configure custom colors for your app theme
- Add custom spacing and typography scales
- Set up dark mode support (optional)

**Custom theme example:**
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          900: '#1e3a8a',
        }
      }
    }
  }
}
```

**Acceptance criteria:**
- [ ] Tailwind CSS properly installed and configured
- [ ] Styles compile without errors
- [ ] Custom design tokens defined
- [ ] Purging works in production build
- [ ] Basic utility classes functional

---

### Task 2.3: Vue Router Configuration
**Objective:** Set up client-side routing for single-page application navigation

**What you need to accomplish:**
- Configure Vue Router with modern setup
- Create route definitions for main application areas
- Set up nested routing structure
- Implement navigation guards (basic)

**Documentation to consult:**
- [Vue Router Installation](https://router.vuejs.org/installation.html)
- [Vue Router Guide](https://router.vuejs.org/guide/)
- [Navigation Guards](https://router.vuejs.org/guide/advanced/navigation-guards.html)

**Routes to plan for:**
```javascript
// Pseudocode for route structure
const routes = [
  { path: '/', component: 'Dashboard' },
  { path: '/login', component: 'Login' },
  { path: '/register', component: 'Register' },
  { path: '/projects', component: 'Projects' },
  { path: '/projects/:id', component: 'ProjectDetail' },
  { path: '/profile', component: 'Profile' },
  // Catch-all route for 404
]
```

**Navigation guard considerations:**
- Authentication checks
- Route-based permissions
- Loading states
- Redirect logic

**Acceptance criteria:**
- [ ] Vue Router properly configured
- [ ] All planned routes defined
- [ ] Navigation between routes works
- [ ] URL changes reflect in browser
- [ ] Basic navigation guards implemented

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