# Module 7: Advanced Authentication with Better-Auth

**Duration:** 4-5 days  
**Branch:** `feature/module-7-better-auth`  
**Section:** 🚀 **ADVANCED**

## Learning Objectives
- Implement modern authentication with better-auth library
- Set up multiple authentication providers (email, OAuth)
- Create secure session management and middleware
- Integrate authentication with Nuxt SSR and database
- Build comprehensive user authentication flows

## Overview
This module replaces your basic authentication system with better-auth, a modern, type-safe authentication library designed for full-stack applications. You'll implement secure authentication flows, session management, and user account features that work seamlessly with Nuxt's SSR capabilities.

## Tasks

### Task 7.1: Better-Auth Installation and Configuration
**Objective:** Install better-auth and configure it for Nuxt with database integration

**What you need to accomplish:**
- Install better-auth and required dependencies
- Configure better-auth with your database
- Set up authentication providers
- Configure session management

**Documentation to consult:**
- [Better-Auth Documentation](https://www.better-auth.com)
- [Better-Auth with Nuxt](https://www.better-auth.com/docs/frameworks/nuxt)
- [Better-Auth Database Integration](https://www.better-auth.com/docs/concepts/database)

**Installation:**
```bash
# Install better-auth and related packages
npm install better-auth
npm install @better-auth/drizzle
npm install @better-auth/nuxt

# Additional providers (optional)
npm install arctic  # For OAuth providers
```

**Better-auth configuration:**
```typescript
// server/lib/auth.ts
import { betterAuth } from "better-auth"
import { drizzleAdapter } from "@better-auth/drizzle"
import { getDatabase } from "../database/connection"

export const auth = betterAuth({
  database: drizzleAdapter(getDatabase(), {
    provider: "pg"
  }),
  
  // Email and password authentication
  emailAndPassword: {
    enabled: true,
    requireEmailVerification: true,
    sendResetPassword: async ({ user, url }) => {
      // Implement email sending logic
      console.log(`Reset password for ${user.email}: ${url}`)
    },
    sendVerificationEmail: async ({ user, url }) => {
      // Implement email sending logic
      console.log(`Verify email for ${user.email}: ${url}`)
    }
  },

  // Session configuration
  session: {
    expiresIn: 60 * 60 * 24 * 7, // 7 days
    updateAge: 60 * 60 * 24, // Update session every day
    cookieCache: {
      enabled: true,
      maxAge: 60 * 5 // 5 minutes
    }
  },

  // Security settings
  advanced: {
    crossSubDomainCookies: {
      enabled: false
    },
    useSecureCookies: process.env.NODE_ENV === 'production',
    generateId: false // Use database auto-generated IDs
  },

  // Rate limiting
  rateLimit: {
    window: 60, // 1 minute
    max: 100 // 100 requests per minute
  },

  // Additional configuration
  appName: "Task Manager",
  
  // Trusted origins for CORS
  trustedOrigins: [
    process.env.APP_URL || "http://localhost:3000"
  ]
})

export type Session = typeof auth.$Infer.Session
export type User = typeof auth.$Infer.User
```

**Nuxt configuration:**
```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: [
    '@better-auth/nuxt'
  ],
  
  betterAuth: {
    baseURL: process.env.BETTER_AUTH_URL || "http://localhost:3000"
  },

  runtimeConfig: {
    // Private environment variables
    betterAuthSecret: process.env.BETTER_AUTH_SECRET,
    betterAuthUrl: process.env.BETTER_AUTH_URL || "http://localhost:3000",
    
    // Email configuration (if using email verification)
    smtpHost: process.env.SMTP_HOST,
    smtpPort: process.env.SMTP_PORT,
    smtpUser: process.env.SMTP_USER,
    smtpPassword: process.env.SMTP_PASSWORD,
    
    public: {
      betterAuthUrl: process.env.BETTER_AUTH_URL || "http://localhost:3000"
    }
  }
})
```

**Environment variables:**
```bash
# .env
BETTER_AUTH_SECRET="your-secret-key-here"
BETTER_AUTH_URL="http://localhost:3000"

# Email configuration (optional)
SMTP_HOST="smtp.gmail.com"
SMTP_PORT="587"
SMTP_USER="your-email@gmail.com"
SMTP_PASSWORD="your-app-password"
```

**Acceptance criteria:**
- [ ] Better-auth installed and configured
- [ ] Database adapter connected
- [ ] Session management configured
- [ ] Environment variables set up
- [ ] Basic configuration working

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Completed Module 6 (Database Integration) successfully
- [ ] Understanding of authentication principles and security concepts
- [ ] Drizzle ORM properly configured and working
- [ ] Familiarity with TypeScript and modern authentication patterns

### Step-by-Step Implementation Approach

**1. Authentication Strategy Planning**
- Research better-auth capabilities and compare with other auth solutions
- Plan your authentication flows including email/password and OAuth providers
- Design your user schema and profile management structure
- Consider session management requirements for your application

**2. Better-Auth Installation and Basic Configuration**
- Install better-auth and required adapter packages
- Configure the basic authentication setup with your database
- Set up environment variables for authentication secrets
- Test basic authentication functionality with minimal configuration

**3. Database Integration and Schema Updates**
- Configure better-auth database adapter with your existing Drizzle setup
- Update your database schema to accommodate better-auth requirements
- Run necessary migrations to create authentication tables
- Test database integration to ensure proper table creation and relationships

**4. Security and Session Configuration**
- Configure session management settings including expiration and security
- Set up proper security headers and CORS configuration
- Implement rate limiting for authentication endpoints
- Configure trusted origins and security best practices

**Key Decision Points:**
- **Session Storage:** Choose between database sessions, JWT tokens, or hybrid approaches
- **Security Settings:** Balance security requirements with user experience
- **Provider Selection:** Decide which OAuth providers to support initially
- **Email Integration:** Plan email verification and password reset workflows

**Verification Steps:**
1. Confirm better-auth packages install without conflicts
2. Test that database adapter connects and creates required tables
3. Verify basic authentication configuration loads without errors
4. Check that environment variables are properly configured and secure

---

### Task 7.2: Database Schema Migration for Better-Auth
**Objective:** Update database schema to include better-auth required tables

**What you need to accomplish:**
- Generate better-auth database tables
- Migrate existing user data to new schema
- Update user operations to work with better-auth
- Test database integration

**Documentation to consult:**
- [Better-Auth Database Schema](https://www.better-auth.com/docs/concepts/database#schema)
- [Better-Auth with Drizzle](https://www.better-auth.com/docs/adapters/drizzle)

**Generate better-auth schema:**
```typescript
// server/database/auth-schema.ts
import { auth } from "../lib/auth"

// Export the better-auth schema
export const { user, session, account, verification } = auth.schema
```

**Update your existing schema:**
```typescript
// server/database/schema.ts
import { 
  pgTable, 
  uuid, 
  varchar, 
  text, 
  timestamp, 
  boolean,
  integer,
  jsonb,
  index
} from 'drizzle-orm/pg-core'
import { relations } from 'drizzle-orm'

// Import better-auth schema
export { user, session, account, verification } from './auth-schema'

// Update existing users table to extend better-auth user
export const userProfiles = pgTable('user_profiles', {
  id: uuid('id').primaryKey(),
  userId: varchar('user_id').references(() => user.id).notNull(),
  firstName: varchar('first_name', { length: 50 }),
  lastName: varchar('last_name', { length: 50 }),
  avatar: varchar('avatar', { length: 500 }),
  role: varchar('role', { length: 20 }).default('user'),
  preferences: jsonb('preferences').$type<{
    theme: 'light' | 'dark'
    notifications: boolean
    timezone: string
  }>(),
  createdAt: timestamp('created_at').defaultNow(),
  updatedAt: timestamp('updated_at').defaultNow()
})

// Projects table (updated to reference better-auth user)
export const projects = pgTable('projects', {
  id: uuid('id').defaultRandom().primaryKey(),
  name: varchar('name', { length: 100 }).notNull(),
  description: text('description'),
  ownerId: varchar('owner_id').references(() => user.id).notNull(),
  color: varchar('color', { length: 7 }).default('#3b82f6'),
  isArchived: boolean('is_archived').default(false),
  settings: jsonb('settings').$type<{
    isPrivate: boolean
    allowGuests: boolean
    defaultTaskStatus: string
  }>(),
  createdAt: timestamp('created_at').defaultNow(),
  updatedAt: timestamp('updated_at').defaultNow()
})

// Tasks table (updated to reference better-auth user)
export const tasks = pgTable('tasks', {
  id: uuid('id').defaultRandom().primaryKey(),
  title: varchar('title', { length: 200 }).notNull(),
  description: text('description'),
  projectId: uuid('project_id').references(() => projects.id),
  assigneeId: varchar('assignee_id').references(() => user.id),
  creatorId: varchar('creator_id').references(() => user.id).notNull(),
  status: varchar('status', { length: 20 }).default('todo'),
  priority: varchar('priority', { length: 20 }).default('medium'),
  dueDate: timestamp('due_date'),
  completedAt: timestamp('completed_at'),
  position: integer('position').default(0),
  tags: jsonb('tags').$type<string[]>(),
  attachments: jsonb('attachments').$type<{
    id: string
    name: string
    url: string
    size: number
    type: string
  }[]>(),
  createdAt: timestamp('created_at').defaultNow(),
  updatedAt: timestamp('updated_at').defaultNow()
})

// Project members table (updated)
export const projectMembers = pgTable('project_members', {
  id: uuid('id').defaultRandom().primaryKey(),
  projectId: uuid('project_id').references(() => projects.id),
  userId: varchar('user_id').references(() => user.id),
  role: varchar('role', { length: 20 }).default('member'),
  joinedAt: timestamp('joined_at').defaultNow()
})

// Update relations
export const userProfilesRelations = relations(userProfiles, ({ one }) => ({
  user: one(user, {
    fields: [userProfiles.userId],
    references: [user.id]
  })
}))

// Export updated types
export type UserProfile = typeof userProfiles.$inferSelect
export type NewUserProfile = typeof userProfiles.$inferInsert
export type Project = typeof projects.$inferSelect
export type NewProject = typeof projects.$inferInsert
export type Task = typeof tasks.$inferSelect
export type NewTask = typeof tasks.$inferInsert
```

**Data migration script:**
```typescript
// scripts/migrate-to-better-auth.ts
import { getDatabase } from '../server/database/connection'
import { user, userProfiles } from '../server/database/schema'
import { eq } from 'drizzle-orm'

async function migrateExistingUsers() {
  const db = getDatabase()
  
  try {
    // This would migrate data from your old users table
    // to the new better-auth structure
    console.log('Migration completed successfully')
  } catch (error) {
    console.error('Migration failed:', error)
  }
}

migrateExistingUsers()
```

**Generate and apply migrations:**
```bash
# Generate new migration
npm run db:generate

# Apply migration
npm run db:migrate
```

**Acceptance criteria:**
- [ ] Better-auth tables created in database
- [ ] Existing schema updated for compatibility
- [ ] Data migration completed
- [ ] All foreign key relationships working
- [ ] Database operations functional

---

### Task 7.3: Authentication API Routes and Server Setup
**Objective:** Set up better-auth API routes and server middleware

**What you need to accomplish:**
- Create better-auth API route handler
- Set up authentication middleware
- Implement protected route patterns
- Configure session handling

**Documentation to consult:**
- [Better-Auth API Routes](https://www.better-auth.com/docs/concepts/api)
- [Better-Auth Server Middleware](https://www.better-auth.com/docs/frameworks/nuxt#server-middleware)

**API route handler:**
```typescript
// server/api/auth/[...].ts
import { auth } from "~/server/lib/auth"

export default toNodeHandler(auth.handler)
```

**Authentication middleware:**
```typescript
// server/middleware/auth.ts
import type { Session, User } from "~/server/lib/auth"

declare global {
  namespace globalThis {
    var __auth_session: Session | null
    var __auth_user: User | null
  }
}

export default defineEventHandler(async (event) => {
  // Skip auth for non-API routes and auth routes
  if (!event.node.req.url?.startsWith('/api') || 
      event.node.req.url?.startsWith('/api/auth')) {
    return
  }

  try {
    const session = await getSession(event)
    
    // Attach session to global context
    globalThis.__auth_session = session
    globalThis.__auth_user = session?.user || null
    
    // Attach to event context
    event.context.session = session
    event.context.user = session?.user || null
  } catch (error) {
    // Handle auth errors gracefully
    event.context.session = null
    event.context.user = null
  }
})

// Helper function to get session from request
async function getSession(event: any) {
  const { auth } = await import("~/server/lib/auth")
  return await auth.api.getSession({ 
    headers: getHeaders(event) 
  })
}
```

**Protected route helper:**
```typescript
// server/utils/auth.ts
import type { User } from "~/server/lib/auth"

export function requireAuth(event: any): User {
  const user = event.context.user
  
  if (!user) {
    throw createError({
      statusCode: 401,
      statusMessage: 'Authentication required'
    })
  }
  
  return user
}

export function requireRole(event: any, requiredRole: string): User {
  const user = requireAuth(event)
  
  // You would implement role checking logic here
  // This might involve checking the user's role in your database
  
  return user
}

export function getAuthUser(event: any): User | null {
  return event.context.user || null
}
```

**Protected API route example:**
```typescript
// server/api/projects/index.get.ts
import { projectOps } from '~/server/database/operations/projects'

export default defineEventHandler(async (event) => {
  try {
    const user = requireAuth(event)
    
    const projects = await projectOps.findByUserId(user.id)
    
    return {
      success: true,
      data: { projects }
    }
  } catch (error) {
    throw createError({
      statusCode: error.statusCode || 500,
      statusMessage: error.statusMessage || 'Internal server error'
    })
  }
})
```

**Acceptance criteria:**
- [ ] Better-auth API routes working
- [ ] Authentication middleware implemented
- [ ] Protected routes require authentication
- [ ] Session handling functional
- [ ] Helper utilities created

---

### Task 7.4: Frontend Authentication Integration
**Objective:** Integrate better-auth with Nuxt frontend components and pages

**What you need to accomplish:**
- Set up better-auth client composables
- Create authentication forms with better-auth
- Implement authentication state management
- Build user account management features

**Documentation to consult:**
- [Better-Auth Client](https://www.better-auth.com/docs/concepts/client)
- [Better-Auth with Vue](https://www.better-auth.com/docs/frameworks/nuxt#client-side)

**Better-auth client setup:**
```typescript
// composables/useAuth.ts
import { createAuthClient } from "better-auth/client"

const authClient = createAuthClient({
  baseURL: useRuntimeConfig().public.betterAuthUrl
})

export const useAuth = () => {
  const user = ref(null)
  const session = ref(null)
  const loading = ref(false)

  // Sign in with email and password
  const signIn = async (email: string, password: string) => {
    loading.value = true
    try {
      const result = await authClient.signIn.email({
        email,
        password
      })
      
      if (result.data) {
        user.value = result.data.user
        session.value = result.data.session
        await navigateTo('/dashboard')
      }
      
      return result
    } catch (error) {
      throw error
    } finally {
      loading.value = false
    }
  }

  // Sign up with email and password
  const signUp = async (email: string, password: string, firstName: string, lastName: string) => {
    loading.value = true
    try {
      const result = await authClient.signUp.email({
        email,
        password,
        name: `${firstName} ${lastName}`
      })
      
      return result
    } catch (error) {
      throw error
    } finally {
      loading.value = false
    }
  }

  // Sign out
  const signOut = async () => {
    try {
      await authClient.signOut()
      user.value = null
      session.value = null
      await navigateTo('/login')
    } catch (error) {
      console.error('Sign out error:', error)
    }
  }

  // Get current session
  const getSession = async () => {
    try {
      const result = await authClient.getSession()
      if (result.data) {
        user.value = result.data.user
        session.value = result.data.session
      }
      return result.data
    } catch (error) {
      return null
    }
  }

  // Reset password
  const resetPassword = async (email: string) => {
    try {
      return await authClient.forgetPassword({
        email,
        redirectTo: '/reset-password'
      })
    } catch (error) {
      throw error
    }
  }

  return {
    user: readonly(user),
    session: readonly(session),
    loading: readonly(loading),
    signIn,
    signUp,
    signOut,
    getSession,
    resetPassword
  }
}
```

**Authentication plugin:**
```typescript
// plugins/auth.client.ts
export default defineNuxtPlugin(async () => {
  const { getSession } = useAuth()
  
  // Get session on app initialization
  await getSession()
})
```

**Login form component:**
```vue
<!-- components/Auth/LoginForm.vue -->
<template>
  <div class="max-w-sm mx-auto bg-white p-8 rounded-lg shadow-md">
    <h2 class="text-2xl font-bold mb-6 text-center">Sign In</h2>
    
    <Form
      :validation-schema="loginSchema"
      @submit="handleSubmit"
    >
      <FormGroup>
        <BaseInput
          name="email"
          type="email"
          label="Email Address"
          required
          autocomplete="email"
        />
        
        <BaseInput
          name="password"
          type="password"
          label="Password"
          required
          autocomplete="current-password"
        />
        
        <div class="flex items-center justify-between">
          <BaseCheckbox
            name="rememberMe"
            label="Remember me"
          />
          
          <NuxtLink
            to="/forgot-password"
            class="text-sm text-blue-600 hover:underline"
          >
            Forgot password?
          </NuxtLink>
        </div>
        
        <BaseButton
          type="submit"
          :loading="loading"
          class="w-full"
        >
          Sign In
        </BaseButton>
      </FormGroup>
    </Form>
    
    <div v-if="error" class="mt-4 p-3 bg-red-50 border border-red-200 rounded">
      {{ error }}
    </div>
    
    <div class="mt-6 text-center">
      <span class="text-gray-600">Don't have an account?</span>
      <NuxtLink to="/register" class="text-blue-600 hover:underline ml-1">
        Sign up
      </NuxtLink>
    </div>
  </div>
</template>

<script setup lang="ts">
import { z } from 'zod'

const loginSchema = z.object({
  email: z.string().email('Please enter a valid email address'),
  password: z.string().min(1, 'Password is required')
})

const { signIn, loading } = useAuth()
const error = ref('')

const handleSubmit = async (values: any) => {
  error.value = ''
  
  try {
    await signIn(values.email, values.password)
  } catch (err: any) {
    error.value = err.message || 'Sign in failed'
  }
}
</script>
```

**Registration form component:**
```vue
<!-- components/Auth/RegisterForm.vue -->
<template>
  <div class="max-w-md mx-auto bg-white p-8 rounded-lg shadow-md">
    <h2 class="text-2xl font-bold mb-6 text-center">Create Account</h2>
    
    <Form
      :validation-schema="registerSchema"
      @submit="handleSubmit"
    >
      <FormGroup>
        <div class="grid grid-cols-2 gap-4">
          <BaseInput
            name="firstName"
            label="First Name"
            required
          />
          <BaseInput
            name="lastName"
            label="Last Name"
            required
          />
        </div>
        
        <BaseInput
          name="email"
          type="email"
          label="Email Address"
          required
          autocomplete="email"
        />
        
        <BaseInput
          name="password"
          type="password"
          label="Password"
          required
          autocomplete="new-password"
        />
        
        <BaseInput
          name="confirmPassword"
          type="password"
          label="Confirm Password"
          required
          autocomplete="new-password"
        />
        
        <BaseButton
          type="submit"
          :loading="loading"
          class="w-full"
        >
          Create Account
        </BaseButton>
      </FormGroup>
    </Form>
    
    <div v-if="error" class="mt-4 p-3 bg-red-50 border border-red-200 rounded">
      {{ error }}
    </div>
    
    <div v-if="successMessage" class="mt-4 p-3 bg-green-50 border border-green-200 rounded">
      {{ successMessage }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { z } from 'zod'

const registerSchema = z.object({
  firstName: z.string().min(2, 'First name must be at least 2 characters'),
  lastName: z.string().min(2, 'Last name must be at least 2 characters'),
  email: z.string().email('Please enter a valid email address'),
  password: z.string()
    .min(8, 'Password must be at least 8 characters')
    .regex(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/, 'Password must contain uppercase, lowercase, and number'),
  confirmPassword: z.string()
}).refine((data) => data.password === data.confirmPassword, {
  message: "Passwords don't match",
  path: ["confirmPassword"],
})

const { signUp, loading } = useAuth()
const error = ref('')
const successMessage = ref('')

const handleSubmit = async (values: any) => {
  error.value = ''
  successMessage.value = ''
  
  try {
    const result = await signUp(
      values.email, 
      values.password, 
      values.firstName, 
      values.lastName
    )
    
    if (result.data) {
      successMessage.value = 'Account created successfully! Please check your email for verification.'
    }
  } catch (err: any) {
    error.value = err.message || 'Registration failed'
  }
}
</script>
```

**Authentication middleware for pages:**
```typescript
// middleware/auth.ts
export default defineNuxtRouteMiddleware((to) => {
  const { user } = useAuth()
  
  if (!user.value) {
    return navigateTo('/login')
  }
})
```

**Usage in protected pages:**
```vue
<!-- pages/dashboard.vue -->
<template>
  <div>
    <h1>Welcome, {{ user?.name }}</h1>
    <!-- Dashboard content -->
  </div>
</template>

<script setup>
definePageMeta({
  middleware: 'auth'
})

const { user } = useAuth()
</script>
```

**Acceptance criteria:**
- [ ] Authentication client composable working
- [ ] Login and registration forms functional
- [ ] Authentication state management working
- [ ] Protected routes require authentication
- [ ] User session persistence working

---

### Task 7.5: OAuth Providers and Advanced Features
**Objective:** Add OAuth authentication providers and implement advanced authentication features

**What you need to accomplish:**
- Set up OAuth providers (Google, GitHub)
- Implement email verification flow
- Add password reset functionality
- Create user profile management

**Documentation to consult:**
- [Better-Auth OAuth](https://www.better-auth.com/docs/concepts/oauth)
- [Better-Auth Email Verification](https://www.better-auth.com/docs/concepts/email-verification)

**OAuth provider setup:**
```typescript
// server/lib/auth.ts (updated)
import { betterAuth } from "better-auth"
import { drizzleAdapter } from "@better-auth/drizzle"
import { getDatabase } from "../database/connection"

export const auth = betterAuth({
  database: drizzleAdapter(getDatabase(), {
    provider: "pg"
  }),
  
  // Email and password authentication
  emailAndPassword: {
    enabled: true,
    requireEmailVerification: true
  },
  
  // OAuth providers
  socialProviders: {
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    },
    github: {
      clientId: process.env.GITHUB_CLIENT_ID!,
      clientSecret: process.env.GITHUB_CLIENT_SECRET!,
    }
  },

  // Email configuration
  emailVerification: {
    sendOnSignUp: true,
    autoSignInAfterVerification: true,
    sendVerificationEmail: async ({ user, url, token }) => {
      // Send verification email
      await sendEmail({
        to: user.email,
        subject: 'Verify your email address',
        html: `
          <h1>Verify your email</h1>
          <p>Click the link below to verify your email address:</p>
          <a href="${url}">Verify Email</a>
        `
      })
    }
  },

  // Other configurations...
})
```

**OAuth login buttons:**
```vue
<!-- components/Auth/OAuthButtons.vue -->
<template>
  <div class="space-y-3">
    <div class="relative">
      <div class="absolute inset-0 flex items-center">
        <div class="w-full border-t border-gray-300" />
      </div>
      <div class="relative flex justify-center text-sm">
        <span class="px-2 bg-white text-gray-500">Or continue with</span>
      </div>
    </div>
    
    <div class="grid grid-cols-2 gap-3">
      <BaseButton
        @click="signInWithGoogle"
        variant="outline"
        class="flex items-center justify-center gap-2"
      >
        <svg class="w-5 h-5" viewBox="0 0 24 24">
          <!-- Google icon SVG -->
        </svg>
        Google
      </BaseButton>
      
      <BaseButton
        @click="signInWithGitHub"
        variant="outline"
        class="flex items-center justify-center gap-2"
      >
        <svg class="w-5 h-5" viewBox="0 0 24 24">
          <!-- GitHub icon SVG -->
        </svg>
        GitHub
      </BaseButton>
    </div>
  </div>
</template>

<script setup lang="ts">
import { createAuthClient } from "better-auth/client"

const authClient = createAuthClient({
  baseURL: useRuntimeConfig().public.betterAuthUrl
})

const signInWithGoogle = async () => {
  await authClient.signIn.social({
    provider: 'google',
    callbackURL: '/dashboard'
  })
}

const signInWithGitHub = async () => {
  await authClient.signIn.social({
    provider: 'github',
    callbackURL: '/dashboard'
  })
}
</script>
```

**Email verification page:**
```vue
<!-- pages/verify-email.vue -->
<template>
  <div class="min-h-screen flex items-center justify-center">
    <div class="max-w-md w-full bg-white p-8 rounded-lg shadow-md">
      <div v-if="loading" class="text-center">
        <h2 class="text-2xl font-bold mb-4">Verifying Email...</h2>
        <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600 mx-auto"></div>
      </div>
      
      <div v-else-if="success" class="text-center">
        <div class="w-16 h-16 bg-green-100 rounded-full flex items-center justify-center mx-auto mb-4">
          <svg class="w-8 h-8 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
          </svg>
        </div>
        <h2 class="text-2xl font-bold mb-4">Email Verified!</h2>
        <p class="text-gray-600 mb-6">Your email has been successfully verified.</p>
        <BaseButton @click="navigateTo('/dashboard')" class="w-full">
          Continue to Dashboard
        </BaseButton>
      </div>
      
      <div v-else class="text-center">
        <h2 class="text-2xl font-bold mb-4">Verification Failed</h2>
        <p class="text-gray-600 mb-6">{{ error || 'Unable to verify email. Please try again.' }}</p>
        <BaseButton @click="navigateTo('/login')" variant="outline" class="w-full">
          Back to Login
        </BaseButton>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const route = useRoute()
const loading = ref(true)
const success = ref(false)
const error = ref('')

onMounted(async () => {
  const token = route.query.token as string
  
  if (!token) {
    error.value = 'Verification token is missing'
    loading.value = false
    return
  }

  try {
    const authClient = createAuthClient({
      baseURL: useRuntimeConfig().public.betterAuthUrl
    })
    
    await authClient.verifyEmail({
      token
    })
    
    success.value = true
  } catch (err: any) {
    error.value = err.message || 'Verification failed'
  } finally {
    loading.value = false
  }
})
</script>
```

**User profile management:**
```vue
<!-- components/User/ProfileForm.vue -->
<template>
  <div class="bg-white p-6 rounded-lg shadow-md">
    <h3 class="text-lg font-semibold mb-4">Profile Information</h3>
    
    <Form
      :validation-schema="profileSchema"
      :initial-values="initialValues"
      @submit="handleSubmit"
    >
      <FormGroup>
        <BaseInput
          name="firstName"
          label="First Name"
          required
        />
        
        <BaseInput
          name="lastName"
          label="Last Name"
          required
        />
        
        <BaseInput
          name="email"
          type="email"
          label="Email Address"
          disabled
        />
        
        <div class="flex justify-end">
          <BaseButton
            type="submit"
            :loading="loading"
          >
            Update Profile
          </BaseButton>
        </div>
      </FormGroup>
    </Form>
  </div>
</template>

<script setup lang="ts">
import { z } from 'zod'

const profileSchema = z.object({
  firstName: z.string().min(2, 'First name must be at least 2 characters'),
  lastName: z.string().min(2, 'Last name must be at least 2 characters'),
  email: z.string().email()
})

const { user } = useAuth()
const loading = ref(false)

const initialValues = computed(() => ({
  firstName: user.value?.name?.split(' ')[0] || '',
  lastName: user.value?.name?.split(' ')[1] || '',
  email: user.value?.email || ''
}))

const handleSubmit = async (values: any) => {
  loading.value = true
  
  try {
    // Update user profile
    await $fetch('/api/user/profile', {
      method: 'PATCH',
      body: values
    })
    
    // Show success message
  } catch (error) {
    // Handle error
  } finally {
    loading.value = false
  }
}
</script>
```

**Acceptance criteria:**
- [ ] OAuth providers working (Google, GitHub)
- [ ] Email verification flow implemented
- [ ] Password reset functionality working
- [ ] User profile management functional
- [ ] Account linking/unlinking working

## Challenge Extensions

### Advanced Security Features
- Implement two-factor authentication (2FA)
- Add device management and trusted devices
- Create account activity logging
- Implement suspicious activity detection

### Enhanced User Experience
- Add social account linking
- Implement progressive user onboarding
- Create user preference management
- Add account deletion and data export

### Enterprise Features
- Implement single sign-on (SSO)
- Add role-based access control (RBAC)
- Create organization management
- Implement audit logging

### Mobile and PWA Features
- Add biometric authentication
- Implement push notifications
- Create offline authentication support
- Add mobile app deep linking

## Troubleshooting Common Issues

### OAuth Configuration Issues
- Verify client ID and secret are correct
- Check redirect URLs in OAuth provider settings
- Ensure proper domain configuration
- Test OAuth flow in development and production

### Email Verification Problems
- Check email service configuration
- Verify email templates are working
- Test email delivery in development
- Check spam/junk folders

### Session Management Issues
- Verify cookie settings and domains
- Check session expiration configuration
- Test session persistence across requests
- Debug session storage and retrieval

### Database Integration Problems
- Verify better-auth tables are created
- Check foreign key relationships
- Test data synchronization
- Debug migration issues

## Module Completion Checklist

Before moving to Module 8, ensure:
- [ ] Better-auth fully integrated and working
- [ ] Multiple authentication providers functional
- [ ] Email verification flow working
- [ ] Password reset functionality implemented
- [ ] User profile management complete
- [ ] Session management working across SSR/client
- [ ] Authentication state properly managed
- [ ] All security features implemented

## Next Module Preview
Module 8 will focus on building advanced frontend features that leverage your authenticated users and database. You'll implement real-time updates, advanced state management, file uploads, and performance optimization techniques that make your application feel modern and responsive.