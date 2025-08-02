# Module 9: Testing & Quality Assurance

**Duration:** 3-4 days  
**Branch:** `feature/module-9-testing-qa`  
**Section:** 🚀 **ADVANCED**

## Learning Objectives
- Implement comprehensive testing strategies for Nuxt applications
- Write unit tests for components, composables, and utilities
- Create integration tests for API routes and database operations
- Build end-to-end tests with Playwright
- Set up automated testing workflows and quality gates
- Implement performance and accessibility testing

## Overview
This module establishes a robust testing foundation for your application, ensuring reliability, maintainability, and confidence in deployments. You'll learn industry-standard testing practices and implement automated quality assurance processes.

## Tasks

### Task 9.1: Unit Testing Setup with Vitest
**Objective:** Set up comprehensive unit testing environment for Vue components and utilities

**What you need to accomplish:**
- Configure Vitest for Nuxt applications
- Set up Vue Test Utils for component testing
- Create testing utilities and helpers
- Implement component testing patterns

**Documentation to consult:**
- [Vitest Documentation](https://vitest.dev/)
- [Vue Test Utils](https://test-utils.vuejs.org/)
- [Nuxt Testing Guide](https://nuxt.com/docs/getting-started/testing)

**Installation and configuration:**
```bash
# Install testing dependencies
npm install -D vitest @vitejs/plugin-vue
npm install -D @vue/test-utils happy-dom
npm install -D @nuxt/test-utils
npm install -D @pinia/testing
```

**Vitest configuration:**
```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  test: {
    environment: 'happy-dom',
    globals: true,
    setupFiles: ['./tests/setup.ts'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'tests/',
        '**/*.d.ts',
        '**/*.config.*',
        'coverage/**'
      ]
    }
  },
  resolve: {
    alias: {
      '~': new URL('./', import.meta.url).pathname,
      '@': new URL('./', import.meta.url).pathname
    }
  }
})
```

**Test setup file:**
```typescript
// tests/setup.ts
import { vi } from 'vitest'
import { config } from '@vue/test-utils'

// Mock Nuxt composables
vi.mock('#app', () => ({
  useRuntimeConfig: () => ({
    public: {
      apiBase: '/api'
    }
  }),
  navigateTo: vi.fn(),
  useCookie: vi.fn(),
  useRoute: vi.fn(() => ({
    params: {},
    query: {},
    path: '/'
  })),
  useRouter: vi.fn(() => ({
    push: vi.fn(),
    replace: vi.fn()
  }))
}))

// Mock $fetch
global.$fetch = vi.fn()

// Configure Vue Test Utils
config.global.mocks = {
  $t: (key: string) => key, // Mock i18n
  $fetch: vi.fn()
}

// Mock IntersectionObserver
global.IntersectionObserver = vi.fn(() => ({
  observe: vi.fn(),
  disconnect: vi.fn(),
  unobserve: vi.fn()
}))

// Mock ResizeObserver
global.ResizeObserver = vi.fn(() => ({
  observe: vi.fn(),
  disconnect: vi.fn(),
  unobserve: vi.fn()
}))
```

**Component testing utilities:**
```typescript
// tests/utils/component-helpers.ts
import { mount, VueWrapper } from '@vue/test-utils'
import { createTestingPinia } from '@pinia/testing'
import { vi } from 'vitest'

export interface TestingOptions {
  props?: Record<string, any>
  slots?: Record<string, any>
  global?: {
    plugins?: any[]
    mocks?: Record<string, any>
    stubs?: Record<string, any>
  }
}

export function createWrapper<T>(
  component: T,
  options: TestingOptions = {}
): VueWrapper<any> {
  const defaultOptions = {
    global: {
      plugins: [
        createTestingPinia({
          createSpy: vi.fn
        })
      ],
      mocks: {
        $t: (key: string) => key,
        $fetch: vi.fn()
      },
      stubs: {
        NuxtLink: true,
        ClientOnly: true
      }
    }
  }

  const mergedOptions = {
    ...defaultOptions,
    ...options,
    global: {
      ...defaultOptions.global,
      ...options.global
    }
  }

  return mount(component as any, mergedOptions)
}

export function createMockUser() {
  return {
    id: 'user-1',
    email: 'test@example.com',
    name: 'Test User',
    avatar: '/test-avatar.png'
  }
}

export function createMockProject() {
  return {
    id: 'project-1',
    name: 'Test Project',
    description: 'A test project',
    ownerId: 'user-1',
    color: '#3b82f6',
    isArchived: false,
    createdAt: new Date(),
    updatedAt: new Date()
  }
}

export function createMockTask() {
  return {
    id: 'task-1',
    title: 'Test Task',
    description: 'A test task',
    projectId: 'project-1',
    creatorId: 'user-1',
    status: 'todo',
    priority: 'medium',
    createdAt: new Date(),
    updatedAt: new Date()
  }
}
```

**Component test examples:**
```typescript
// tests/components/Base/Button.test.ts
import { describe, it, expect, vi } from 'vitest'
import { createWrapper } from '../../utils/component-helpers'
import BaseButton from '~/components/Base/Button.vue'

describe('BaseButton', () => {
  it('renders button with correct text', () => {
    const wrapper = createWrapper(BaseButton, {
      props: { variant: 'primary' },
      slots: { default: 'Click me' }
    })

    expect(wrapper.text()).toBe('Click me')
    expect(wrapper.find('button').exists()).toBe(true)
  })

  it('applies correct CSS classes for variant', () => {
    const wrapper = createWrapper(BaseButton, {
      props: { variant: 'primary' }
    })

    expect(wrapper.find('button').classes()).toContain('bg-blue-600')
  })

  it('emits click event when clicked', async () => {
    const wrapper = createWrapper(BaseButton)

    await wrapper.find('button').trigger('click')

    expect(wrapper.emitted('click')).toHaveLength(1)
  })

  it('is disabled when loading', () => {
    const wrapper = createWrapper(BaseButton, {
      props: { loading: true }
    })

    expect(wrapper.find('button').attributes('disabled')).toBeDefined()
  })

  it('shows loading spinner when loading', () => {
    const wrapper = createWrapper(BaseButton, {
      props: { loading: true }
    })

    expect(wrapper.find('.animate-spin').exists()).toBe(true)
  })
})
```

```typescript
// tests/components/Tasks/TaskCard.test.ts
import { describe, it, expect, vi } from 'vitest'
import { createWrapper, createMockTask } from '../../utils/component-helpers'
import TaskCard from '~/components/Tasks/TaskCard.vue'

describe('TaskCard', () => {
  const mockTask = createMockTask()

  it('renders task information correctly', () => {
    const wrapper = createWrapper(TaskCard, {
      props: {
        task: mockTask,
        projectId: 'project-1'
      }
    })

    expect(wrapper.text()).toContain(mockTask.title)
    expect(wrapper.text()).toContain(mockTask.description)
  })

  it('displays correct priority badge', () => {
    const highPriorityTask = { ...mockTask, priority: 'high' }
    const wrapper = createWrapper(TaskCard, {
      props: {
        task: highPriorityTask,
        projectId: 'project-1'
      }
    })

    const priorityBadge = wrapper.find('[data-testid="priority-badge"]')
    expect(priorityBadge.text()).toBe('high')
    expect(priorityBadge.classes()).toContain('bg-red-100')
  })

  it('updates status when select changes', async () => {
    const wrapper = createWrapper(TaskCard, {
      props: {
        task: mockTask,
        projectId: 'project-1'
      }
    })

    const select = wrapper.find('select')
    await select.setValue('completed')

    expect(wrapper.emitted('task-updated')).toHaveLength(1)
  })

  it('shows optimistic state for temporary tasks', () => {
    const tempTask = { ...mockTask, id: 'temp-123' }
    const wrapper = createWrapper(TaskCard, {
      props: {
        task: tempTask,
        projectId: 'project-1'
      }
    })

    expect(wrapper.find('.opacity-75').exists()).toBe(true)
  })
})
```

**Acceptance criteria:**
- [ ] Vitest configured and running
- [ ] Component testing utilities created
- [ ] Base component tests written
- [ ] Test coverage reporting working
- [ ] Mock utilities functional

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Completed Modules 1-8 with working application
- [ ] Understanding of testing concepts and best practices
- [ ] Familiarity with Vue components and Nuxt 3 features
- [ ] Knowledge of TypeScript and mocking concepts

### Step-by-Step Implementation Approach

**1. Testing Environment Setup**
- Install Vitest and necessary testing dependencies for Nuxt applications
- Configure Vitest to work with Vue components and Nuxt features
- Set up test environment with proper TypeScript and alias support
- Create testing utilities and setup files for consistent test configuration

**2. Component Testing Infrastructure**
- Create helper functions for mounting components with proper context
- Set up mocking utilities for Nuxt composables and external dependencies
- Configure test coverage reporting to track testing completeness
- Establish testing patterns and conventions for your team

**3. Unit Test Implementation**
- Write tests for base components focusing on props, events, and rendering
- Test composables and utility functions with various input scenarios
- Create tests for store actions and state management logic
- Implement snapshot testing for component output consistency

**4. Testing Workflow Integration**
- Add npm scripts for running tests and generating coverage reports
- Set up watch mode for efficient test-driven development
- Configure testing to work with your existing development workflow
- Establish testing standards and review processes

**Key Decision Points:**
- **Testing scope:** Balance between thorough coverage and maintainable test suite
- **Mocking strategy:** Decide what to mock vs. test with real implementations
- **Test organization:** Structure tests to be maintainable and discoverable
- **Coverage targets:** Set realistic but meaningful coverage goals

**Verification Steps:**
1. Test that all tests run successfully with `npm run test`
2. Verify coverage reports provide meaningful insights
3. Confirm component tests catch regressions effectively
4. Check that tests run efficiently in watch mode during development

---

### Task 9.2: Composables and Store Testing
**Objective:** Test business logic in composables and Pinia stores

**What you need to accomplish:**
- Test composable functions and their reactive behavior
- Test Pinia store actions and state mutations
- Mock external dependencies and API calls
- Test error handling and edge cases

**Documentation to consult:**
- [Testing Composables](https://vuejs.org/guide/scaling-up/testing.html#testing-composables)
- [Pinia Testing](https://pinia.vuejs.org/cookbook/testing.html)

**Composable tests:**
```typescript
// tests/composables/useAuth.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { useAuth } from '~/composables/useAuth'

// Mock the auth client
vi.mock('better-auth/client', () => ({
  createAuthClient: () => ({
    signIn: {
      email: vi.fn()
    },
    signUp: {
      email: vi.fn()
    },
    signOut: vi.fn(),
    getSession: vi.fn()
  })
}))

describe('useAuth', () => {
  beforeEach(() => {
    vi.clearAllMocks()
  })

  it('initializes with null user and session', () => {
    const { user, session } = useAuth()
    
    expect(user.value).toBeNull()
    expect(session.value).toBeNull()
  })

  it('sets loading state during sign in', async () => {
    const mockSignIn = vi.fn().mockResolvedValue({
      data: {
        user: { id: '1', email: 'test@example.com' },
        session: { id: 'session-1' }
      }
    })

    vi.mocked(createAuthClient).mockReturnValue({
      signIn: { email: mockSignIn }
    })

    const { signIn, loading } = useAuth()

    expect(loading.value).toBe(false)

    const signInPromise = signIn('test@example.com', 'password')
    expect(loading.value).toBe(true)

    await signInPromise
    expect(loading.value).toBe(false)
  })

  it('handles sign in success', async () => {
    const mockUser = { id: '1', email: 'test@example.com' }
    const mockSession = { id: 'session-1' }

    const mockSignIn = vi.fn().mockResolvedValue({
      data: { user: mockUser, session: mockSession }
    })

    vi.mocked(createAuthClient).mockReturnValue({
      signIn: { email: mockSignIn }
    })

    const { signIn, user, session } = useAuth()

    await signIn('test@example.com', 'password')

    expect(user.value).toEqual(mockUser)
    expect(session.value).toEqual(mockSession)
    expect(mockSignIn).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password'
    })
  })

  it('handles sign in error', async () => {
    const mockError = new Error('Invalid credentials')
    const mockSignIn = vi.fn().mockRejectedValue(mockError)

    vi.mocked(createAuthClient).mockReturnValue({
      signIn: { email: mockSignIn }
    })

    const { signIn } = useAuth()

    await expect(signIn('test@example.com', 'wrong')).rejects.toThrow('Invalid credentials')
  })
})
```

```typescript
// tests/composables/useRealTime.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { useRealTime } from '~/composables/useRealTime'

// Mock socket.io-client
const mockSocket = {
  on: vi.fn(),
  emit: vi.fn(),
  disconnect: vi.fn()
}

vi.mock('socket.io-client', () => ({
  io: vi.fn(() => mockSocket)
}))

describe('useRealTime', () => {
  beforeEach(() => {
    vi.clearAllMocks()
  })

  it('connects to WebSocket server', () => {
    const { connect, isConnected } = useRealTime()

    connect()

    expect(mockSocket.on).toHaveBeenCalledWith('connect', expect.any(Function))
    expect(mockSocket.on).toHaveBeenCalledWith('disconnect', expect.any(Function))
  })

  it('joins project room', () => {
    const { connect, joinProject } = useRealTime()

    connect()
    joinProject('project-1')

    expect(mockSocket.emit).toHaveBeenCalledWith('join_project', 'project-1')
  })

  it('emits task updates', () => {
    const { connect, emitTaskUpdate } = useRealTime()
    const mockTask = { id: 'task-1', title: 'Test Task' }

    connect()
    emitTaskUpdate('project-1', mockTask)

    expect(mockSocket.emit).toHaveBeenCalledWith('task_update', {
      projectId: 'project-1',
      task: mockTask,
      timestamp: expect.any(Date)
    })
  })
})
```

**Store tests:**
```typescript
// tests/stores/projects.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { setActivePinia, createPinia } from 'pinia'
import { useProjectsStore } from '~/stores/projects'
import { createMockProject } from '../utils/component-helpers'

// Mock $fetch
global.$fetch = vi.fn()

describe('Projects Store', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
    vi.clearAllMocks()
  })

  it('initializes with empty state', () => {
    const store = useProjectsStore()

    expect(store.projects).toEqual([])
    expect(store.loading).toBe(false)
    expect(store.error).toBeNull()
  })

  it('fetches projects successfully', async () => {
    const mockProjects = [createMockProject()]
    
    vi.mocked($fetch).mockResolvedValue({
      data: { projects: mockProjects }
    })

    const store = useProjectsStore()
    await store.fetchProjects()

    expect(store.projects).toEqual(mockProjects)
    expect(store.loading).toBe(false)
    expect(store.error).toBeNull()
  })

  it('handles fetch error', async () => {
    const mockError = new Error('Network error')
    vi.mocked($fetch).mockRejectedValue(mockError)

    const store = useProjectsStore()
    
    await expect(store.fetchProjects()).rejects.toThrow('Network error')
    expect(store.error).toBe('Network error')
    expect(store.loading).toBe(false)
  })

  it('creates project with optimistic update', async () => {
    const newProject = { name: 'New Project', description: 'Test' }
    const createdProject = { ...newProject, id: 'project-1' }

    vi.mocked($fetch).mockResolvedValue({
      data: { project: createdProject }
    })

    const store = useProjectsStore()
    
    // Start with empty projects
    expect(store.projects).toHaveLength(0)

    const resultPromise = store.createProject(newProject)

    // Should have optimistic update
    expect(store.projects).toHaveLength(1)
    expect(store.projects[0].id).toMatch(/^temp-/)

    await resultPromise

    // Should replace with real project
    expect(store.projects).toHaveLength(1)
    expect(store.projects[0]).toEqual(createdProject)
  })

  it('rolls back optimistic update on error', async () => {
    const newProject = { name: 'New Project', description: 'Test' }
    const mockError = new Error('Creation failed')

    vi.mocked($fetch).mockRejectedValue(mockError)

    const store = useProjectsStore()
    
    const resultPromise = store.createProject(newProject)

    // Should have optimistic update
    expect(store.projects).toHaveLength(1)

    await expect(resultPromise).rejects.toThrow('Creation failed')

    // Should roll back
    expect(store.projects).toHaveLength(0)
  })

  it('filters active projects', () => {
    const store = useProjectsStore()
    
    store.projects = [
      { ...createMockProject(), id: '1', isArchived: false },
      { ...createMockProject(), id: '2', isArchived: true },
      { ...createMockProject(), id: '3', isArchived: false }
    ]

    expect(store.activeProjects).toHaveLength(2)
    expect(store.activeProjects.every(p => !p.isArchived)).toBe(true)
  })
})
```

**Acceptance criteria:**
- [ ] Composable tests covering reactive behavior
- [ ] Store tests with optimistic updates
- [ ] Error handling tests implemented
- [ ] Mock utilities working correctly
- [ ] Edge cases covered

---

### Task 9.3: API Route Testing
**Objective:** Test server-side API routes and database operations

**What you need to accomplish:**
- Set up testing environment for API routes
- Test CRUD operations and business logic
- Mock database operations
- Test authentication and authorization

**Documentation to consult:**
- [Nuxt Test Utils](https://nuxt.com/docs/getting-started/testing#testing-in-a-nuxt-environment)
- [Supertest Documentation](https://github.com/ladjs/supertest)

**API testing setup:**
```bash
# Install API testing dependencies
npm install -D supertest
npm install -D @types/supertest
```

```typescript
// tests/server/setup.ts
import { setup, $fetch } from '@nuxt/test-utils'

export async function setupTestServer() {
  await setup({
    // Nuxt config overrides for testing
    server: true,
    build: false
  })
}

export function createTestUser() {
  return {
    id: 'test-user-1',
    email: 'test@example.com',
    name: 'Test User'
  }
}

export function createAuthHeaders(userId: string) {
  // Mock auth headers for testing
  return {
    'x-user-id': userId,
    'authorization': `Bearer mock-token-${userId}`
  }
}
```

**API route tests:**
```typescript
// tests/server/api/projects.test.ts
import { describe, it, expect, beforeAll, beforeEach, vi } from 'vitest'
import { $fetch, setup } from '@nuxt/test-utils'
import { setupTestServer, createTestUser, createAuthHeaders } from '../setup'

describe('/api/projects', () => {
  beforeAll(async () => {
    await setupTestServer()
  })

  beforeEach(() => {
    vi.clearAllMocks()
  })

  describe('GET /api/projects', () => {
    it('returns projects for authenticated user', async () => {
      const user = createTestUser()
      
      const response = await $fetch('/api/projects', {
        headers: createAuthHeaders(user.id)
      })

      expect(response).toEqual({
        success: true,
        data: {
          projects: expect.any(Array)
        }
      })
    })

    it('returns 401 for unauthenticated request', async () => {
      await expect($fetch('/api/projects')).rejects.toThrow('401')
    })

    it('filters projects by status', async () => {
      const user = createTestUser()
      
      const response = await $fetch('/api/projects?status=active', {
        headers: createAuthHeaders(user.id)
      })

      expect(response.data.projects.every(p => !p.isArchived)).toBe(true)
    })
  })

  describe('POST /api/projects', () => {
    it('creates new project', async () => {
      const user = createTestUser()
      const projectData = {
        name: 'Test Project',
        description: 'A test project'
      }

      const response = await $fetch('/api/projects', {
        method: 'POST',
        headers: createAuthHeaders(user.id),
        body: projectData
      })

      expect(response).toEqual({
        success: true,
        data: {
          project: expect.objectContaining({
            id: expect.any(String),
            name: projectData.name,
            description: projectData.description,
            ownerId: user.id
          })
        }
      })
    })

    it('validates required fields', async () => {
      const user = createTestUser()

      await expect($fetch('/api/projects', {
        method: 'POST',
        headers: createAuthHeaders(user.id),
        body: { description: 'Missing name' }
      })).rejects.toThrow('400')
    })

    it('validates project name length', async () => {
      const user = createTestUser()

      await expect($fetch('/api/projects', {
        method: 'POST',
        headers: createAuthHeaders(user.id),
        body: { name: 'a'.repeat(101) } // Too long
      })).rejects.toThrow('400')
    })
  })

  describe('PATCH /api/projects/:id', () => {
    it('updates project', async () => {
      const user = createTestUser()
      const projectId = 'existing-project-id'
      const updates = { name: 'Updated Name' }

      const response = await $fetch(`/api/projects/${projectId}`, {
        method: 'PATCH',
        headers: createAuthHeaders(user.id),
        body: updates
      })

      expect(response.data.project.name).toBe(updates.name)
    })

    it('returns 404 for non-existent project', async () => {
      const user = createTestUser()

      await expect($fetch('/api/projects/non-existent', {
        method: 'PATCH',
        headers: createAuthHeaders(user.id),
        body: { name: 'New Name' }
      })).rejects.toThrow('404')
    })

    it('prevents updating projects owned by other users', async () => {
      const user = createTestUser()
      const otherUserProjectId = 'other-user-project'

      await expect($fetch(`/api/projects/${otherUserProjectId}`, {
        method: 'PATCH',
        headers: createAuthHeaders(user.id),
        body: { name: 'Hacked Name' }
      })).rejects.toThrow('403')
    })
  })
})
```

**Database operation tests:**
```typescript
// tests/server/database/operations.test.ts
import { describe, it, expect, beforeEach, afterEach } from 'vitest'
import { getDatabase } from '~/server/database/connection'
import { projectOps } from '~/server/database/operations/projects'
import { userOps } from '~/server/database/operations/users'

describe('Database Operations', () => {
  let testDb: any
  let testUser: any

  beforeEach(async () => {
    testDb = getDatabase()
    
    // Create test user
    testUser = await userOps.create({
      firstName: 'Test',
      lastName: 'User',
      email: 'test@example.com',
      passwordHash: 'hashed-password'
    })
  })

  afterEach(async () => {
    // Clean up test data
    if (testUser) {
      await userOps.delete(testUser.id)
    }
  })

  describe('Project Operations', () => {
    it('creates project successfully', async () => {
      const projectData = {
        name: 'Test Project',
        description: 'Test description',
        ownerId: testUser.id
      }

      const project = await projectOps.create(projectData)

      expect(project).toEqual(expect.objectContaining({
        id: expect.any(String),
        name: projectData.name,
        description: projectData.description,
        ownerId: testUser.id,
        createdAt: expect.any(Date)
      }))
    })

    it('finds projects by user ID', async () => {
      // Create test projects
      const project1 = await projectOps.create({
        name: 'Project 1',
        ownerId: testUser.id
      })
      
      const project2 = await projectOps.create({
        name: 'Project 2',
        ownerId: testUser.id,
        isArchived: true
      })

      const projects = await projectOps.findByUserId(testUser.id)

      expect(projects).toHaveLength(1) // Only active projects
      expect(projects[0].name).toBe('Project 1')
    })

    it('updates project correctly', async () => {
      const project = await projectOps.create({
        name: 'Original Name',
        ownerId: testUser.id
      })

      const updated = await projectOps.update(project.id, {
        name: 'Updated Name',
        description: 'New description'
      })

      expect(updated?.name).toBe('Updated Name')
      expect(updated?.description).toBe('New description')
      expect(updated?.updatedAt).not.toEqual(project.createdAt)
    })

    it('deletes project successfully', async () => {
      const project = await projectOps.create({
        name: 'To Delete',
        ownerId: testUser.id
      })

      const deleted = await projectOps.delete(project.id)
      expect(deleted).toBe(true)

      const found = await projectOps.findById(project.id)
      expect(found).toBeNull()
    })
  })
})
```

**Authentication tests:**
```typescript
// tests/server/middleware/auth.test.ts
import { describe, it, expect, vi } from 'vitest'
import { createMockEvent } from '../utils/mock-event'
import authMiddleware from '~/server/middleware/auth'

describe('Auth Middleware', () => {
  it('allows access to public routes', async () => {
    const event = createMockEvent('/api/health')
    
    await authMiddleware(event)
    
    expect(event.context.user).toBeUndefined()
  })

  it('sets user context for authenticated requests', async () => {
    const mockUser = { id: 'user-1', email: 'test@example.com' }
    const event = createMockEvent('/api/projects', {
      headers: { authorization: 'Bearer valid-token' }
    })

    // Mock auth validation
    vi.mocked(validateAuthToken).mockResolvedValue(mockUser)

    await authMiddleware(event)

    expect(event.context.user).toEqual(mockUser)
  })

  it('handles invalid tokens gracefully', async () => {
    const event = createMockEvent('/api/projects', {
      headers: { authorization: 'Bearer invalid-token' }
    })

    vi.mocked(validateAuthToken).mockRejectedValue(new Error('Invalid token'))

    await authMiddleware(event)

    expect(event.context.user).toBeNull()
  })
})
```

**Acceptance criteria:**
- [ ] API route tests covering CRUD operations
- [ ] Database operation tests implemented
- [ ] Authentication middleware tests working
- [ ] Error handling tests included
- [ ] Test data cleanup working

---

### Task 9.4: End-to-End Testing with Playwright
**Objective:** Implement comprehensive E2E tests for user workflows

**What you need to accomplish:**
- Set up Playwright for Nuxt applications
- Create E2E tests for critical user journeys
- Test cross-browser compatibility
- Implement visual regression testing

**Documentation to consult:**
- [Playwright Documentation](https://playwright.dev/)
- [Playwright with Nuxt](https://playwright.dev/docs/test-runners)

**Playwright setup:**
```bash
# Install Playwright
npm install -D @playwright/test
npx playwright install
```

**Playwright configuration:**
```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: [
    ['html'],
    ['json', { outputFile: 'test-results/results.json' }]
  ],
  
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure'
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] }
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] }
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] }
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] }
    },
    {
      name: 'Mobile Safari',
      use: { ...devices['iPhone 12'] }
    }
  ],

  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI
  }
})
```

**E2E test utilities:**
```typescript
// tests/e2e/utils/fixtures.ts
import { test as base, expect } from '@playwright/test'

interface TestFixtures {
  authenticatedPage: any
  testUser: {
    email: string
    password: string
    id: string
  }
}

export const test = base.extend<TestFixtures>({
  testUser: async ({}, use) => {
    // Create test user or use existing one
    const user = {
      email: 'test@example.com',
      password: 'TestPassword123!',
      id: 'test-user-1'
    }
    await use(user)
  },

  authenticatedPage: async ({ page, testUser }, use) => {
    // Log in before each test
    await page.goto('/login')
    await page.fill('[name="email"]', testUser.email)
    await page.fill('[name="password"]', testUser.password)
    await page.click('button[type="submit"]')
    
    // Wait for redirect to dashboard
    await page.waitForURL('/dashboard')
    
    await use(page)
  }
})

export { expect } from '@playwright/test'
```

**Authentication flow tests:**
```typescript
// tests/e2e/auth.spec.ts
import { test, expect } from './utils/fixtures'

test.describe('Authentication', () => {
  test('user can sign up successfully', async ({ page }) => {
    await page.goto('/register')

    // Fill registration form
    await page.fill('[name="firstName"]', 'John')
    await page.fill('[name="lastName"]', 'Doe')
    await page.fill('[name="email"]', 'john.doe@example.com')
    await page.fill('[name="password"]', 'SecurePassword123!')
    await page.fill('[name="confirmPassword"]', 'SecurePassword123!')

    // Submit form
    await page.click('button[type="submit"]')

    // Check for success message
    await expect(page.locator('.success-message')).toContainText('Account created successfully')
  })

  test('user can log in successfully', async ({ page, testUser }) => {
    await page.goto('/login')

    await page.fill('[name="email"]', testUser.email)
    await page.fill('[name="password"]', testUser.password)
    await page.click('button[type="submit"]')

    // Should redirect to dashboard
    await expect(page).toHaveURL('/dashboard')
    await expect(page.locator('h1')).toContainText('Dashboard')
  })

  test('shows error for invalid credentials', async ({ page }) => {
    await page.goto('/login')

    await page.fill('[name="email"]', 'invalid@example.com')
    await page.fill('[name="password"]', 'wrongpassword')
    await page.click('button[type="submit"]')

    await expect(page.locator('.error-message')).toContainText('Invalid credentials')
  })

  test('user can log out', async ({ authenticatedPage }) => {
    // Click user menu
    await authenticatedPage.click('[data-testid="user-menu"]')
    
    // Click logout
    await authenticatedPage.click('[data-testid="logout-button"]')

    // Should redirect to login
    await expect(authenticatedPage).toHaveURL('/login')
  })
})
```

**Project management tests:**
```typescript
// tests/e2e/projects.spec.ts
import { test, expect } from './utils/fixtures'

test.describe('Project Management', () => {
  test('user can create a new project', async ({ authenticatedPage }) => {
    await authenticatedPage.goto('/projects')

    // Click create project button
    await authenticatedPage.click('[data-testid="create-project-btn"]')

    // Fill project form
    await authenticatedPage.fill('[name="name"]', 'Test Project')
    await authenticatedPage.fill('[name="description"]', 'A test project for E2E testing')
    
    // Submit form
    await authenticatedPage.click('button[type="submit"]')

    // Check project appears in list
    await expect(authenticatedPage.locator('[data-testid="project-card"]')).toContainText('Test Project')
  })

  test('user can view project details', async ({ authenticatedPage }) => {
    await authenticatedPage.goto('/projects')

    // Click on first project
    await authenticatedPage.click('[data-testid="project-card"]:first-child')

    // Should be on project detail page
    await expect(authenticatedPage.locator('h1')).toContainText('Test Project')
    await expect(authenticatedPage.locator('[data-testid="project-description"]')).toContainText('A test project')
  })

  test('user can edit project', async ({ authenticatedPage }) => {
    await authenticatedPage.goto('/projects/test-project-id')

    // Click edit button
    await authenticatedPage.click('[data-testid="edit-project-btn"]')

    // Update project name
    await authenticatedPage.fill('[name="name"]', 'Updated Project Name')
    await authenticatedPage.click('button[type="submit"]')

    // Check updated name appears
    await expect(authenticatedPage.locator('h1')).toContainText('Updated Project Name')
  })
})
```

**Task management tests:**
```typescript
// tests/e2e/tasks.spec.ts
import { test, expect } from './utils/fixtures'

test.describe('Task Management', () => {
  test.beforeEach(async ({ authenticatedPage }) => {
    await authenticatedPage.goto('/projects/test-project-id')
  })

  test('user can create a task', async ({ authenticatedPage }) => {
    // Click add task button
    await authenticatedPage.click('[data-testid="add-task-btn"]')

    // Fill task form
    await authenticatedPage.fill('[name="title"]', 'New Test Task')
    await authenticatedPage.fill('[name="description"]', 'Task description')
    await authenticatedPage.selectOption('[name="priority"]', 'high')

    await authenticatedPage.click('button[type="submit"]')

    // Check task appears in kanban board
    await expect(authenticatedPage.locator('[data-testid="task-card"]')).toContainText('New Test Task')
  })

  test('user can drag task between columns', async ({ authenticatedPage }) => {
    const taskCard = authenticatedPage.locator('[data-testid="task-card"]:first-child')
    const inProgressColumn = authenticatedPage.locator('[data-testid="column-in_progress"]')

    // Drag task from todo to in-progress
    await taskCard.dragTo(inProgressColumn)

    // Check task moved to in-progress column
    await expect(inProgressColumn.locator('[data-testid="task-card"]')).toContainText('New Test Task')
  })

  test('user can update task status', async ({ authenticatedPage }) => {
    const taskCard = authenticatedPage.locator('[data-testid="task-card"]:first-child')
    
    // Click on task to open details
    await taskCard.click()

    // Change status
    await authenticatedPage.selectOption('[name="status"]', 'completed')
    await authenticatedPage.click('[data-testid="save-task"]')

    // Check task moved to completed column
    await expect(authenticatedPage.locator('[data-testid="column-completed"] [data-testid="task-card"]')).toContainText('New Test Task')
  })

  test('user can delete task', async ({ authenticatedPage }) => {
    const taskCard = authenticatedPage.locator('[data-testid="task-card"]:first-child')
    
    // Click task options menu
    await taskCard.locator('[data-testid="task-menu"]').click()
    
    // Click delete
    await authenticatedPage.click('[data-testid="delete-task"]')
    
    // Confirm deletion
    await authenticatedPage.click('[data-testid="confirm-delete"]')

    // Check task is removed
    await expect(taskCard).not.toBeVisible()
  })
})
```

**Visual regression tests:**
```typescript
// tests/e2e/visual.spec.ts
import { test, expect } from './utils/fixtures'

test.describe('Visual Regression', () => {
  test('dashboard page matches screenshot', async ({ authenticatedPage }) => {
    await authenticatedPage.goto('/dashboard')
    await expect(authenticatedPage).toHaveScreenshot('dashboard.png')
  })

  test('project kanban board matches screenshot', async ({ authenticatedPage }) => {
    await authenticatedPage.goto('/projects/test-project-id')
    await expect(authenticatedPage).toHaveScreenshot('kanban-board.png')
  })

  test('mobile responsive design', async ({ page }) => {
    await page.setViewportSize({ width: 375, height: 667 }) // iPhone SE
    await page.goto('/dashboard')
    await expect(page).toHaveScreenshot('dashboard-mobile.png')
  })
})
```

**Acceptance criteria:**
- [ ] Playwright configured and working
- [ ] Authentication flow tests passing
- [ ] Project management tests implemented
- [ ] Task management tests working
- [ ] Cross-browser tests running
- [ ] Visual regression tests functional

---

### Task 9.5: Performance and Accessibility Testing
**Objective:** Implement automated performance and accessibility testing

**What you need to accomplish:**
- Set up Lighthouse performance testing
- Implement accessibility testing with axe-core
- Create performance regression testing
- Add load testing capabilities

**Documentation to consult:**
- [Lighthouse CI](https://github.com/GoogleChrome/lighthouse-ci)
- [axe-core Testing](https://github.com/dequelabs/axe-core)
- [Web Vitals](https://web.dev/vitals/)

**Performance testing setup:**
```bash
# Install performance testing tools
npm install -D lighthouse lighthouse-ci
npm install -D @axe-core/playwright
```

**Lighthouse CI configuration:**
```javascript
// .lighthouserc.js
module.exports = {
  ci: {
    collect: {
      url: [
        'http://localhost:3000/',
        'http://localhost:3000/login',
        'http://localhost:3000/dashboard',
        'http://localhost:3000/projects'
      ],
      startServerCommand: 'npm run dev',
      numberOfRuns: 3
    },
    assert: {
      assertions: {
        'categories:performance': ['warn', { minScore: 0.8 }],
        'categories:accessibility': ['error', { minScore: 0.9 }],
        'categories:best-practices': ['warn', { minScore: 0.8 }],
        'categories:seo': ['warn', { minScore: 0.8 }]
      }
    },
    upload: {
      target: 'temporary-public-storage'
    }
  }
}
```

**Accessibility tests:**
```typescript
// tests/e2e/accessibility.spec.ts
import { test, expect } from '@playwright/test'
import AxeBuilder from '@axe-core/playwright'

test.describe('Accessibility', () => {
  test('login page should not have accessibility violations', async ({ page }) => {
    await page.goto('/login')

    const accessibilityScanResults = await new AxeBuilder({ page }).analyze()

    expect(accessibilityScanResults.violations).toEqual([])
  })

  test('dashboard should be accessible', async ({ page }) => {
    // Login first
    await page.goto('/login')
    await page.fill('[name="email"]', 'test@example.com')
    await page.fill('[name="password"]', 'password')
    await page.click('button[type="submit"]')
    await page.waitForURL('/dashboard')

    const accessibilityScanResults = await new AxeBuilder({ page })
      .withTags(['wcag2a', 'wcag2aa', 'wcag21aa'])
      .analyze()

    expect(accessibilityScanResults.violations).toEqual([])
  })

  test('kanban board should be keyboard navigable', async ({ page }) => {
    await page.goto('/projects/test-project-id')

    // Test keyboard navigation
    await page.keyboard.press('Tab')
    await page.keyboard.press('Enter')

    // Should open task creation modal
    await expect(page.locator('[data-testid="task-modal"]')).toBeVisible()

    // Test ESC to close
    await page.keyboard.press('Escape')
    await expect(page.locator('[data-testid="task-modal"]')).not.toBeVisible()
  })

  test('form validation errors should be announced', async ({ page }) => {
    await page.goto('/login')

    // Submit empty form
    await page.click('button[type="submit"]')

    // Check for aria-live error announcements
    const errorRegion = page.locator('[aria-live="polite"]')
    await expect(errorRegion).toContainText('Email is required')
  })
})
```

**Performance tests:**
```typescript
// tests/e2e/performance.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Performance', () => {
  test('page load performance', async ({ page }) => {
    const startTime = Date.now()
    
    await page.goto('/dashboard')
    
    const endTime = Date.now()
    const loadTime = endTime - startTime

    // Page should load within 3 seconds
    expect(loadTime).toBeLessThan(3000)
  })

  test('measure Core Web Vitals', async ({ page }) => {
    await page.goto('/dashboard')

    // Measure LCP (Largest Contentful Paint)
    const lcp = await page.evaluate(() => {
      return new Promise((resolve) => {
        new PerformanceObserver((list) => {
          const entries = list.getEntries()
          const lastEntry = entries[entries.length - 1]
          resolve(lastEntry.startTime)
        }).observe({ entryTypes: ['largest-contentful-paint'] })
      })
    })

    expect(lcp).toBeLessThan(2500) // Good LCP is < 2.5s

    // Measure CLS (Cumulative Layout Shift)
    const cls = await page.evaluate(() => {
      return new Promise((resolve) => {
        let cls = 0
        new PerformanceObserver((list) => {
          for (const entry of list.getEntries()) {
            if (!entry.hadRecentInput) {
              cls += entry.value
            }
          }
          resolve(cls)
        }).observe({ entryTypes: ['layout-shift'] })

        setTimeout(() => resolve(cls), 5000)
      })
    })

    expect(cls).toBeLessThan(0.1) // Good CLS is < 0.1
  })

  test('bundle size should be reasonable', async ({ page }) => {
    const response = await page.goto('/dashboard')
    
    // Get the main JavaScript bundle size
    const jsRequests = []
    page.on('response', response => {
      if (response.url().includes('.js') && response.status() === 200) {
        jsRequests.push(response)
      }
    })

    await page.waitForLoadState('networkidle')

    let totalJSSize = 0
    for (const request of jsRequests) {
      const buffer = await request.body()
      totalJSSize += buffer.length
    }

    // Total JS should be less than 1MB
    expect(totalJSSize).toBeLessThan(1024 * 1024)
  })
})
```

**Load testing script:**
```typescript
// tests/load/basic-load.test.ts
import { test, expect } from '@playwright/test'

test.describe('Load Testing', () => {
  test('concurrent user simulation', async ({ browser }) => {
    const numberOfUsers = 10
    const promises = []

    for (let i = 0; i < numberOfUsers; i++) {
      promises.push(simulateUser(browser, i))
    }

    const results = await Promise.all(promises)
    
    // All users should complete successfully
    expect(results.every(result => result.success)).toBe(true)
    
    // Average response time should be reasonable
    const avgResponseTime = results.reduce((sum, result) => sum + result.responseTime, 0) / results.length
    expect(avgResponseTime).toBeLessThan(2000)
  })
})

async function simulateUser(browser: any, userId: number) {
  const context = await browser.newContext()
  const page = await context.newPage()

  const startTime = Date.now()

  try {
    // Login
    await page.goto('/login')
    await page.fill('[name="email"]', `user${userId}@example.com`)
    await page.fill('[name="password"]', 'password')
    await page.click('button[type="submit"]')

    // Navigate to projects
    await page.goto('/projects')
    await page.waitForSelector('[data-testid="project-card"]')

    // Create a task
    await page.click('[data-testid="add-task-btn"]')
    await page.fill('[name="title"]', `Task from user ${userId}`)
    await page.click('button[type="submit"]')

    await context.close()

    return {
      success: true,
      responseTime: Date.now() - startTime
    }
  } catch (error) {
    await context.close()
    return {
      success: false,
      responseTime: Date.now() - startTime,
      error: error.message
    }
  }
}
```

**Package.json test scripts:**
```json
{
  "scripts": {
    "test": "vitest",
    "test:unit": "vitest --reporter=verbose",
    "test:coverage": "vitest --coverage",
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "test:accessibility": "playwright test accessibility.spec.ts",
    "test:performance": "playwright test performance.spec.ts",
    "test:lighthouse": "lhci autorun",
    "test:all": "npm run test:unit && npm run test:e2e && npm run test:lighthouse"
  }
}
```

**Acceptance criteria:**
- [ ] Lighthouse performance testing configured
- [ ] Accessibility tests with axe-core working
- [ ] Performance regression tests implemented
- [ ] Load testing capabilities added
- [ ] Core Web Vitals measured
- [ ] Test automation scripts working

## Challenge Extensions

### Advanced Testing Strategies
- Implement mutation testing
- Add contract testing for APIs
- Create chaos engineering tests
- Build automated security testing

### CI/CD Integration
- Set up GitHub Actions workflows
- Implement test parallelization
- Add automatic test reporting
- Create deployment gates based on test results

### Monitoring and Observability
- Implement application monitoring
- Add error tracking and reporting
- Create performance monitoring dashboards
- Set up alerting for test failures

### Test Data Management
- Create test data factories
- Implement database seeding for tests
- Add test data cleanup strategies
- Build test environment management

## Module Completion Checklist

Before moving to Module 10, ensure:
- [ ] Unit testing framework fully configured
- [ ] Component and composable tests comprehensive
- [ ] API route tests covering all endpoints
- [ ] E2E tests for critical user journeys
- [ ] Performance and accessibility tests working
- [ ] Test coverage meeting quality standards
- [ ] All tests passing consistently
- [ ] Test automation ready for CI/CD

## Next Module Preview
Module 10 will focus on production deployment, optimization, and monitoring. You'll learn to deploy your application to Vercel, implement advanced performance optimizations, set up monitoring and analytics, and prepare your application for real-world usage.