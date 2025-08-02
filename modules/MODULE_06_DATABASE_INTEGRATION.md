# Module 6: Database Integration with Nuxt

**Duration:** 3-4 days  
**Branch:** `feature/module-6-database-integration`  
**Section:** 🚀 **ADVANCED**

## Learning Objectives
- Integrate PostgreSQL database with Nuxt 3 server
- Implement Drizzle ORM in Nuxt server context
- Create type-safe database schemas and migrations
- Build server API routes for database operations
- Handle database connections and environment configuration

## Overview
This module transforms your Nuxt application into a full-stack solution by integrating PostgreSQL with Drizzle ORM. You'll leverage Nuxt's server-side capabilities to create API routes that handle database operations, replacing the separate Express backend with Nuxt's built-in server functionality.

## Tasks

### Task 6.1: PostgreSQL Setup and Configuration
**Objective:** Set up PostgreSQL database and configure connection in Nuxt environment

**What you need to accomplish:**
- Set up PostgreSQL database (local or cloud)
- Configure database connection in Nuxt
- Set up environment variables for database credentials
- Test database connectivity

**Documentation to consult:**
- [PostgreSQL Installation](https://www.postgresql.org/download/)
- [Nuxt 3 Runtime Config](https://nuxt.com/docs/guide/going-further/runtime-config)
- [Environment Variables in Nuxt](https://nuxt.com/docs/guide/going-further/runtime-config#environment-variables)

**Database setup options:**

**Option 1: Local PostgreSQL**
```bash
# Install PostgreSQL (macOS with Homebrew)
brew install postgresql
brew services start postgresql

# Create database
createdb task_manager_dev

# Connect and verify
psql task_manager_dev
```

**Option 2: Cloud PostgreSQL (Recommended)**
```bash
# Sign up for a service like:
# - Supabase (https://supabase.com/)
# - Neon (https://neon.tech/)
# - Railway (https://railway.app/)
# - Vercel Postgres (https://vercel.com/docs/storage/vercel-postgres)
```

**Environment configuration:**
```bash
# .env
DATABASE_URL="postgresql://username:password@localhost:5432/task_manager_dev"
# or for cloud providers:
# DATABASE_URL="postgresql://user:password@host:port/dbname?sslmode=require"

# Additional database config
DB_HOST=localhost
DB_PORT=5432
DB_NAME=task_manager_dev
DB_USER=your_username
DB_PASSWORD=your_password
```

**Nuxt runtime configuration:**
```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  runtimeConfig: {
    // Private keys (only available on server-side)
    databaseUrl: process.env.DATABASE_URL,
    dbHost: process.env.DB_HOST,
    dbPort: process.env.DB_PORT,
    dbName: process.env.DB_NAME,
    dbUser: process.env.DB_USER,
    dbPassword: process.env.DB_PASSWORD,
    
    // Public keys (exposed to client-side)
    public: {
      apiBase: '/api'
    }
  }
})
```

**Connection test script:**
```typescript
// scripts/test-db-connection.ts
import { Client } from 'pg'

const client = new Client({
  connectionString: process.env.DATABASE_URL
})

async function testConnection() {
  try {
    await client.connect()
    const result = await client.query('SELECT NOW()')
    console.log('Database connected successfully:', result.rows[0])
    await client.end()
  } catch (error) {
    console.error('Database connection failed:', error)
  }
}

testConnection()
```

**Acceptance criteria:**
- [ ] PostgreSQL database accessible
- [ ] Environment variables configured
- [ ] Nuxt runtime config set up
- [ ] Database connection successful
- [ ] Connection test script works

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Completed Module 5 (Nuxt Migration) successfully
- [ ] Understanding of database fundamentals and SQL
- [ ] Node.js and npm installed with proper permissions
- [ ] Familiarity with environment variable management

### Step-by-Step Implementation Approach

**1. Database Provider Selection and Setup**
- Research different PostgreSQL hosting options (local vs cloud)
- Compare providers like Supabase, Neon, Railway, and Vercel Postgres
- Consider factors like pricing, performance, and ease of setup
- Create your database instance and obtain connection credentials

**2. Local Development Environment Configuration**
- Set up your local .env file with database connection strings
- Configure Nuxt runtime config to properly handle environment variables
- Test environment variable loading in both development and production contexts
- Verify that sensitive data is properly protected from client-side exposure

**3. Database Connection Implementation**
- Install required packages for PostgreSQL connectivity
- Create database connection utilities following Nuxt best practices
- Implement connection pooling for optimal performance
- Test database connectivity with simple queries

**4. Production Readiness Preparation**
- Configure SSL requirements for production database connections
- Set up proper error handling for connection failures
- Implement connection retry logic for reliability
- Create health check endpoints for monitoring database status

**Key Decision Points:**
- **Local vs Cloud Database:** Choose based on your development workflow and deployment strategy
- **Connection Pooling:** Configure appropriate pool sizes for your expected traffic
- **SSL Configuration:** Ensure proper SSL setup for production security
- **Environment Management:** Separate development, staging, and production database configurations

**Verification Steps:**
1. Confirm database instance is accessible from your development environment
2. Test that environment variables load correctly in Nuxt runtime config
3. Verify database connection script executes without errors
4. Check that connection pooling works under simulated load

---

### Task 6.2: Drizzle ORM Setup and Configuration
**Objective:** Install and configure Drizzle ORM for type-safe database operations

**What you need to accomplish:**
- Install Drizzle ORM and PostgreSQL driver
- Configure Drizzle for Nuxt server environment
- Set up database configuration files
- Create database connection utility

**Documentation to consult:**
- [Drizzle ORM PostgreSQL Guide](https://orm.drizzle.team/docs/get-started-postgresql)
- [Drizzle ORM with Nuxt](https://orm.drizzle.team/docs/get-started-postgresql#nuxt)
- [Drizzle Configuration](https://orm.drizzle.team/docs/drizzle-config-file)

**Installation:**
```bash
# Install Drizzle ORM and dependencies
npm install drizzle-orm postgres
npm install -D drizzle-kit @types/pg

# Install additional utilities
npm install dotenv
```

**Drizzle configuration:**
```typescript
// drizzle.config.ts
import type { Config } from 'drizzle-kit'

export default {
  schema: './server/database/schema.ts',
  out: './server/database/migrations',
  driver: 'pg',
  dbCredentials: {
    connectionString: process.env.DATABASE_URL!
  },
  verbose: true,
  strict: true
} satisfies Config
```

**Database connection utility:**
```typescript
// server/database/connection.ts
import { drizzle } from 'drizzle-orm/postgres-js'
import postgres from 'postgres'

let connection: postgres.Sql | null = null
let db: ReturnType<typeof drizzle> | null = null

export function getDatabase() {
  if (!db) {
    const config = useRuntimeConfig()
    
    if (!connection) {
      connection = postgres(config.databaseUrl, {
        max: 10, // Connection pool size
        idle_timeout: 20,
        connect_timeout: 60
      })
    }
    
    db = drizzle(connection)
  }
  
  return db
}

// Graceful shutdown
export async function closeDatabase() {
  if (connection) {
    await connection.end()
    connection = null
    db = null
  }
}
```

**Package.json scripts:**
```json
{
  "scripts": {
    "db:generate": "drizzle-kit generate:pg",
    "db:migrate": "drizzle-kit migrate",
    "db:studio": "drizzle-kit studio",
    "db:drop": "drizzle-kit drop",
    "db:check": "drizzle-kit check"
  }
}
```

**Acceptance criteria:**
- [ ] Drizzle ORM installed and configured
- [ ] Database connection utility created
- [ ] Configuration files set up
- [ ] Drizzle Kit commands working
- [ ] Connection pooling configured

---

### Task 6.3: Database Schema Design and Migration
**Objective:** Create comprehensive database schemas for the task management application

**What you need to accomplish:**
- Design database schema for users, projects, and tasks
- Create Drizzle schema definitions with TypeScript types
- Set up database migrations
- Implement schema relationships and constraints

**Documentation to consult:**
- [Drizzle ORM Schema](https://orm.drizzle.team/docs/sql-schema-declaration)
- [Drizzle Relations](https://orm.drizzle.team/docs/rqb#relations)
- [Database Design Best Practices](https://orm.drizzle.team/docs/goodies#multi-project-schema)

**Schema definition:**
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

// Users table
export const users = pgTable('users', {
  id: uuid('id').defaultRandom().primaryKey(),
  firstName: varchar('first_name', { length: 50 }).notNull(),
  lastName: varchar('last_name', { length: 50 }).notNull(),
  email: varchar('email', { length: 255 }).notNull().unique(),
  passwordHash: varchar('password_hash', { length: 255 }).notNull(),
  emailVerified: boolean('email_verified').default(false),
  avatar: varchar('avatar', { length: 500 }),
  role: varchar('role', { length: 20 }).default('user'),
  createdAt: timestamp('created_at').defaultNow(),
  updatedAt: timestamp('updated_at').defaultNow()
}, (table) => {
  return {
    emailIdx: index('email_idx').on(table.email)
  }
})

// Projects table
export const projects = pgTable('projects', {
  id: uuid('id').defaultRandom().primaryKey(),
  name: varchar('name', { length: 100 }).notNull(),
  description: text('description'),
  ownerId: uuid('owner_id').references(() => users.id),
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

// Tasks table
export const tasks = pgTable('tasks', {
  id: uuid('id').defaultRandom().primaryKey(),
  title: varchar('title', { length: 200 }).notNull(),
  description: text('description'),
  projectId: uuid('project_id').references(() => projects.id),
  assigneeId: uuid('assignee_id').references(() => users.id),
  creatorId: uuid('creator_id').references(() => users.id),
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
}, (table) => {
  return {
    projectIdx: index('project_idx').on(table.projectId),
    statusIdx: index('status_idx').on(table.status),
    assigneeIdx: index('assignee_idx').on(table.assigneeId)
  }
})

// Project members table (many-to-many relationship)
export const projectMembers = pgTable('project_members', {
  id: uuid('id').defaultRandom().primaryKey(),
  projectId: uuid('project_id').references(() => projects.id),
  userId: uuid('user_id').references(() => users.id),
  role: varchar('role', { length: 20 }).default('member'),
  joinedAt: timestamp('joined_at').defaultNow()
})

// Define relationships
export const usersRelations = relations(users, ({ many }) => ({
  projects: many(projects),
  assignedTasks: many(tasks, { relationName: 'assignee' }),
  createdTasks: many(tasks, { relationName: 'creator' }),
  projectMemberships: many(projectMembers)
}))

export const projectsRelations = relations(projects, ({ one, many }) => ({
  owner: one(users, {
    fields: [projects.ownerId],
    references: [users.id]
  }),
  tasks: many(tasks),
  members: many(projectMembers)
}))

export const tasksRelations = relations(tasks, ({ one }) => ({
  project: one(projects, {
    fields: [tasks.projectId],
    references: [projects.id]
  }),
  assignee: one(users, {
    fields: [tasks.assigneeId],
    references: [users.id],
    relationName: 'assignee'
  }),
  creator: one(users, {
    fields: [tasks.creatorId],
    references: [users.id],
    relationName: 'creator'
  })
}))

export const projectMembersRelations = relations(projectMembers, ({ one }) => ({
  project: one(projects, {
    fields: [projectMembers.projectId],
    references: [projects.id]
  }),
  user: one(users, {
    fields: [projectMembers.userId],
    references: [users.id]
  })
}))

// Export types
export type User = typeof users.$inferSelect
export type NewUser = typeof users.$inferInsert
export type Project = typeof projects.$inferSelect
export type NewProject = typeof projects.$inferInsert
export type Task = typeof tasks.$inferSelect
export type NewTask = typeof tasks.$inferInsert
```

**Generate and run migrations:**
```bash
# Generate migration files
npm run db:generate

# Apply migrations to database
npm run db:migrate
```

**Acceptance criteria:**
- [ ] Complete schema designed and implemented
- [ ] Database migrations generated and applied
- [ ] Relationships properly defined
- [ ] TypeScript types exported
- [ ] Indexes created for performance

---

### Task 6.4: Database Operations and Queries
**Objective:** Create reusable database operation functions with proper error handling

**What you need to accomplish:**
- Build database operation utilities
- Implement CRUD operations for each entity
- Create query helpers with proper typing
- Add error handling and validation

**Documentation to consult:**
- [Drizzle Queries](https://orm.drizzle.team/docs/crud)
- [Drizzle Relational Queries](https://orm.drizzle.team/docs/rqb)
- [Error Handling Patterns](https://orm.drizzle.team/docs/crud#batch-api)

**Database operations utilities:**
```typescript
// server/database/operations/users.ts
import { eq, and } from 'drizzle-orm'
import { getDatabase } from '../connection'
import { users, type User, type NewUser } from '../schema'

export class UserOperations {
  private db = getDatabase()

  async create(userData: NewUser): Promise<User> {
    const [user] = await this.db
      .insert(users)
      .values(userData)
      .returning()
    
    return user
  }

  async findById(id: string): Promise<User | null> {
    const [user] = await this.db
      .select()
      .from(users)
      .where(eq(users.id, id))
      .limit(1)
    
    return user || null
  }

  async findByEmail(email: string): Promise<User | null> {
    const [user] = await this.db
      .select()
      .from(users)
      .where(eq(users.email, email))
      .limit(1)
    
    return user || null
  }

  async update(id: string, updates: Partial<NewUser>): Promise<User | null> {
    const [user] = await this.db
      .update(users)
      .set({ 
        ...updates, 
        updatedAt: new Date() 
      })
      .where(eq(users.id, id))
      .returning()
    
    return user || null
  }

  async delete(id: string): Promise<boolean> {
    const result = await this.db
      .delete(users)
      .where(eq(users.id, id))
    
    return result.rowCount > 0
  }

  async verifyEmail(id: string): Promise<User | null> {
    return this.update(id, { 
      emailVerified: true,
      updatedAt: new Date()
    })
  }
}

export const userOps = new UserOperations()
```

**Project operations with relations:**
```typescript
// server/database/operations/projects.ts
import { eq, desc, and } from 'drizzle-orm'
import { getDatabase } from '../connection'
import { 
  projects, 
  projectMembers, 
  users,
  type Project, 
  type NewProject 
} from '../schema'

export class ProjectOperations {
  private db = getDatabase()

  async create(projectData: NewProject): Promise<Project> {
    const [project] = await this.db
      .insert(projects)
      .values(projectData)
      .returning()
    
    return project
  }

  async findByUserId(userId: string): Promise<Project[]> {
    return await this.db
      .select({
        id: projects.id,
        name: projects.name,
        description: projects.description,
        color: projects.color,
        isArchived: projects.isArchived,
        createdAt: projects.createdAt
      })
      .from(projects)
      .leftJoin(projectMembers, eq(projects.id, projectMembers.projectId))
      .where(
        and(
          eq(projects.ownerId, userId),
          eq(projects.isArchived, false)
        )
      )
      .orderBy(desc(projects.createdAt))
  }

  async findWithMembers(projectId: string): Promise<any> {
    return await this.db.query.projects.findFirst({
      where: eq(projects.id, projectId),
      with: {
        owner: {
          columns: {
            id: true,
            firstName: true,
            lastName: true,
            email: true,
            avatar: true
          }
        },
        members: {
          with: {
            user: {
              columns: {
                id: true,
                firstName: true,
                lastName: true,
                email: true,
                avatar: true
              }
            }
          }
        },
        tasks: {
          orderBy: desc(tasks.createdAt),
          limit: 10
        }
      }
    })
  }

  async addMember(projectId: string, userId: string, role: string = 'member') {
    const [member] = await this.db
      .insert(projectMembers)
      .values({
        projectId,
        userId,
        role
      })
      .returning()
    
    return member
  }
}

export const projectOps = new ProjectOperations()
```

**Task operations with complex queries:**
```typescript
// server/database/operations/tasks.ts
import { eq, and, desc, sql } from 'drizzle-orm'
import { getDatabase } from '../connection'
import { tasks, type Task, type NewTask } from '../schema'

export class TaskOperations {
  private db = getDatabase()

  async create(taskData: NewTask): Promise<Task> {
    const [task] = await this.db
      .insert(tasks)
      .values(taskData)
      .returning()
    
    return task
  }

  async findByProject(projectId: string, filters?: {
    status?: string
    assigneeId?: string
    limit?: number
  }): Promise<Task[]> {
    let query = this.db
      .select()
      .from(tasks)
      .where(eq(tasks.projectId, projectId))

    if (filters?.status) {
      query = query.where(and(
        eq(tasks.projectId, projectId),
        eq(tasks.status, filters.status)
      ))
    }

    if (filters?.assigneeId) {
      query = query.where(and(
        eq(tasks.projectId, projectId),
        eq(tasks.assigneeId, filters.assigneeId)
      ))
    }

    return await query
      .orderBy(desc(tasks.createdAt))
      .limit(filters?.limit || 50)
  }

  async updateStatus(taskId: string, status: string): Promise<Task | null> {
    const updateData: any = { 
      status, 
      updatedAt: new Date() 
    }
    
    if (status === 'completed') {
      updateData.completedAt = new Date()
    }

    const [task] = await this.db
      .update(tasks)
      .set(updateData)
      .where(eq(tasks.id, taskId))
      .returning()
    
    return task || null
  }

  async getProjectStats(projectId: string) {
    const stats = await this.db
      .select({
        total: sql<number>`count(*)`,
        completed: sql<number>`count(*) filter (where status = 'completed')`,
        inProgress: sql<number>`count(*) filter (where status = 'in_progress')`,
        todo: sql<number>`count(*) filter (where status = 'todo')`
      })
      .from(tasks)
      .where(eq(tasks.projectId, projectId))

    return stats[0]
  }
}

export const taskOps = new TaskOperations()
```

**Acceptance criteria:**
- [ ] CRUD operations implemented for all entities
- [ ] Complex queries with relations working
- [ ] Proper TypeScript typing throughout
- [ ] Error handling implemented
- [ ] Performance optimized queries

---

### Task 6.5: Nuxt API Routes Implementation
**Objective:** Create Nuxt server API routes that utilize database operations

**What you need to accomplish:**
- Create API routes in Nuxt server directory
- Implement RESTful endpoints for database operations
- Add request validation and error handling
- Test API endpoints functionality

**Documentation to consult:**
- [Nuxt 3 Server API Routes](https://nuxt.com/docs/guide/directory-structure/server)
- [Nuxt 3 Server Utils](https://nuxt.com/docs/guide/directory-structure/server#server-utilities)
- [H3 Event Handler](https://h3.unjs.io/)

**API route structure:**
```
server/
├── api/
│   ├── auth/
│   │   ├── login.post.ts
│   │   ├── register.post.ts
│   │   └── me.get.ts
│   ├── users/
│   │   ├── [id].get.ts
│   │   └── [id].patch.ts
│   ├── projects/
│   │   ├── index.get.ts
│   │   ├── index.post.ts
│   │   ├── [id].get.ts
│   │   └── [id]/
│   │       ├── tasks.get.ts
│   │       └── tasks.post.ts
│   └── tasks/
│       ├── [id].get.ts
│       ├── [id].patch.ts
│       └── [id].delete.ts
```

**User API routes:**
```typescript
// server/api/users/[id].get.ts
import { userOps } from '~/server/database/operations/users'

export default defineEventHandler(async (event) => {
  try {
    const userId = getRouterParam(event, 'id')
    
    if (!userId) {
      throw createError({
        statusCode: 400,
        statusMessage: 'User ID is required'
      })
    }

    const user = await userOps.findById(userId)
    
    if (!user) {
      throw createError({
        statusCode: 404,
        statusMessage: 'User not found'
      })
    }

    // Remove sensitive data
    const { passwordHash, ...safeUser } = user
    
    return {
      success: true,
      data: { user: safeUser }
    }
  } catch (error) {
    throw createError({
      statusCode: error.statusCode || 500,
      statusMessage: error.statusMessage || 'Internal server error'
    })
  }
})
```

**Project API routes:**
```typescript
// server/api/projects/index.get.ts
import { projectOps } from '~/server/database/operations/projects'

export default defineEventHandler(async (event) => {
  try {
    // In a real app, get user ID from auth middleware
    const query = getQuery(event)
    const userId = query.userId as string
    
    if (!userId) {
      throw createError({
        statusCode: 401,
        statusMessage: 'Authentication required'
      })
    }

    const projects = await projectOps.findByUserId(userId)
    
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

```typescript
// server/api/projects/index.post.ts
import { z } from 'zod'
import { projectOps } from '~/server/database/operations/projects'

const createProjectSchema = z.object({
  name: z.string().min(1).max(100),
  description: z.string().optional(),
  color: z.string().regex(/^#[0-9A-F]{6}$/i).optional(),
  isPrivate: z.boolean().optional()
})

export default defineEventHandler(async (event) => {
  try {
    const body = await readBody(event)
    const validatedData = createProjectSchema.parse(body)
    
    // In a real app, get user ID from auth middleware
    const userId = body.ownerId
    
    if (!userId) {
      throw createError({
        statusCode: 401,
        statusMessage: 'Authentication required'
      })
    }

    const project = await projectOps.create({
      ...validatedData,
      ownerId: userId,
      settings: {
        isPrivate: validatedData.isPrivate || false,
        allowGuests: false,
        defaultTaskStatus: 'todo'
      }
    })
    
    return {
      success: true,
      data: { project }
    }
  } catch (error) {
    if (error.name === 'ZodError') {
      throw createError({
        statusCode: 400,
        statusMessage: 'Validation failed',
        data: error.errors
      })
    }
    
    throw createError({
      statusCode: error.statusCode || 500,
      statusMessage: error.statusMessage || 'Internal server error'
    })
  }
})
```

**Task API routes:**
```typescript
// server/api/projects/[id]/tasks.get.ts
import { taskOps } from '~/server/database/operations/tasks'

export default defineEventHandler(async (event) => {
  try {
    const projectId = getRouterParam(event, 'id')
    const query = getQuery(event)
    
    if (!projectId) {
      throw createError({
        statusCode: 400,
        statusMessage: 'Project ID is required'
      })
    }

    const filters = {
      status: query.status as string,
      assigneeId: query.assigneeId as string,
      limit: query.limit ? parseInt(query.limit as string) : undefined
    }

    const tasks = await taskOps.findByProject(projectId, filters)
    
    return {
      success: true,
      data: { tasks }
    }
  } catch (error) {
    throw createError({
      statusCode: error.statusCode || 500,
      statusMessage: error.statusMessage || 'Internal server error'
    })
  }
})
```

**Testing API routes:**
```bash
# Test with curl or use Nuxt DevTools
curl -X GET "http://localhost:3000/api/projects?userId=123"
curl -X POST "http://localhost:3000/api/projects" \
  -H "Content-Type: application/json" \
  -d '{"name":"Test Project","ownerId":"123"}'
```

**Acceptance criteria:**
- [ ] All API routes implemented and working
- [ ] Request validation with Zod schemas
- [ ] Proper error handling and HTTP status codes
- [ ] Database operations integrated
- [ ] API routes tested and functional

## Challenge Extensions

### Advanced Database Features
- Implement database transactions for complex operations
- Add soft delete functionality
- Create database seeders for development data
- Implement database backup and restore scripts

### Performance Optimization
- Add database query caching
- Implement connection pooling optimization
- Create database indexes for common queries
- Add query performance monitoring

### Security Enhancements
- Implement row-level security
- Add database audit logging
- Create data validation at database level
- Implement data encryption for sensitive fields

### Development Tools
- Set up Drizzle Studio for database management
- Create database documentation generation
- Implement database testing utilities
- Add migration rollback capabilities

## Troubleshooting Common Issues

### Connection Issues
- Verify database URL format and credentials
- Check network connectivity to database
- Ensure database server is running
- Test connection outside of Nuxt

### Migration Problems
- Check schema syntax for errors
- Verify migration files are generated correctly
- Ensure database permissions are sufficient
- Test migrations on fresh database

### Query Performance
- Analyze slow queries with EXPLAIN
- Add appropriate database indexes
- Optimize query structure
- Check for N+1 query problems

### Type Safety Issues
- Verify Drizzle schema types are exported
- Check TypeScript configuration
- Ensure proper type imports
- Test type inference in IDE

## Module Completion Checklist

Before moving to Module 7, ensure:
- [ ] PostgreSQL database connected and accessible
- [ ] Drizzle ORM configured and working
- [ ] Database schema designed and migrated
- [ ] All CRUD operations implemented
- [ ] Nuxt API routes functional
- [ ] Type safety maintained throughout
- [ ] Error handling implemented
- [ ] Database operations tested

## Next Module Preview
Module 7 will focus on implementing advanced authentication with better-auth. You'll replace the basic authentication system with a comprehensive solution that supports multiple providers, secure session management, and seamless integration with your Nuxt application and database.