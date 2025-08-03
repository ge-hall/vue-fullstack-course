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

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Node.js 16+ installed (`node --version`)
- [ ] Module 1 development environment setup completed
- [ ] Understanding of Vue 3 vs Vue 2 differences
- [ ] Basic familiarity with modern JavaScript (ES6+)

### Step-by-Step Implementation Approach

**1. Project Initialization Planning**
- Navigate to your `client/` directory (or create it if needed)
- Research the `npm create vue@latest` command options
- Plan which features to include during scaffold (TypeScript, Router, ESLint)
- Consider how this integrates with your existing project structure

**2. Vue Project Creation Process**
- Run the Vue creation command in your client directory
- Choose appropriate options for a professional application
- Review generated folder structure and understand each directory's purpose
- Install dependencies and verify the initial setup works

**3. Development Server Configuration**
- Start the development server and verify hot reload works
- Test that changes to Vue files reflect immediately in browser
- Configure any necessary proxy settings for backend integration
- Verify build process works for production

**4. Project Integration**
- Update your root package.json scripts to include client commands
- Ensure the Vue project works with your existing Git repository
- Verify environment variables can be accessed from Vue components
- Test that the project structure supports team development

**Key Decision Points:**
- **TypeScript adoption:** Recommended for larger projects and better tooling
- **Router inclusion:** Essential for SPA navigation (choose yes)
- **Testing framework:** Consider your team's testing strategy
- **PWA features:** Can be added later if not immediately needed

**Verification Steps:**
1. Confirm `npm run dev` starts development server successfully
2. Test that editing a Vue component triggers hot reload
3. Verify build command (`npm run build`) creates production-ready files
4. Check that the project integrates with your existing development workflow

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

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Vue 3 project successfully running
- [ ] Understanding of utility-first CSS approach
- [ ] Knowledge of PostCSS and how it works with Vite
- [ ] Basic familiarity with CSS custom properties

### Step-by-Step Implementation Approach

**1. Tailwind Installation and Setup**
- Install Tailwind CSS and its peer dependencies using npm
- Generate Tailwind and PostCSS configuration files
- Understand how Tailwind integrates with Vite's build process
- Configure content paths to include all Vue files for proper purging

**2. Configuration and Customization**
- Set up your main CSS file with Tailwind directives
- Configure content paths in tailwind.config.js for proper purging
- Define custom color palette that reflects your application's brand
- Set up custom spacing, typography, and component variants

**3. Integration with Vue Components**
- Import Tailwind styles in your main application entry point
- Test utility classes work correctly in Vue components
- Understand how to use Tailwind classes with dynamic Vue styling
- Configure any necessary CSS-in-JS patterns for dynamic styles

**4. Production Optimization**
- Verify Tailwind's purging removes unused CSS in production builds
- Test that custom configuration works in both development and production
- Ensure build sizes are optimized and performant
- Set up any additional PostCSS plugins if needed

**Key Decision Points:**
- **Custom theme extent:** Decide how much to customize vs. using defaults
- **Component abstraction:** Plan when to create CSS components vs. using utilities
- **Dark mode strategy:** Consider if you need dark mode support now or later
- **Design system integration:** Plan how Tailwind fits with your design tokens

**Verification Steps:**
1. Test that utility classes like `bg-blue-500` and `text-center` work in components
2. Verify your custom colors appear correctly when used
3. Confirm production build only includes used CSS classes
4. Check that responsive classes work at different screen sizes

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

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Vue Router included during project initialization (or installed separately)
- [ ] Understanding of client-side routing concepts
- [ ] Knowledge of Vue 3 Composition API for router usage
- [ ] Planning completed for your application's route structure

### Step-by-Step Implementation Approach

**1. Router Configuration Setup**
- Examine the generated router configuration if included during project setup
- Understand the difference between hash mode and history mode
- Configure the router with your preferred mode and base URL
- Plan your route hierarchy and nested route relationships

**2. Route Definition and Components**
- Create view components for each main route
- Define route objects with path, component, and metadata
- Set up nested routes for complex application areas
- Plan for dynamic routes with parameters (e.g., `/projects/:id`)

**3. Navigation Implementation**
- Use router-link components for declarative navigation
- Implement programmatic navigation with useRouter composable
- Set up navigation guards for authentication and authorization
- Handle route changes and loading states appropriately

**4. Advanced Router Features**
- Configure route metadata for titles, authentication requirements
- Set up lazy loading for route components
- Implement proper error handling for failed route navigation
- Add transition animations between route changes

**Key Decision Points:**
- **History mode vs. hash mode:** History mode preferred for SEO, hash mode for simple hosting
- **Route organization:** Flat vs. nested structure based on your application complexity
- **Code splitting:** Decide which routes should be lazy-loaded for performance
- **Navigation guard strategy:** Plan authentication and permission checking approach

**Verification Steps:**
1. Test that all routes navigate correctly via URL and router-link
2. Verify browser back/forward buttons work as expected
3. Confirm route parameters are passed correctly to components
4. Check that navigation guards prevent/allow access appropriately

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
- Use TypeScript interfaces or PropType definitions for prop validation
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
- **TypeScript integration:** Consider type safety for component interfaces

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