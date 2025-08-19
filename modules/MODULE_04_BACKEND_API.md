# Module 4: Backend API Development

**Duration:** 4-5 days  
**Branch:** `feature/module-4-backend-api`

## Application Context: TaskFlow Backend Foundation

### What You're Building
You're creating the backend API that powers **TaskFlow** - the server-side foundation that will handle user authentication, data storage, and all business logic. This module builds the API endpoints that your authentication forms will communicate with and establishes patterns for future project and task management features.

### TaskFlow API Features You're Building This Module
- **Authentication endpoints**: Registration, login, logout, and token management
- **User management**: Profile updates, account settings, and user data
- **API architecture**: RESTful design patterns and consistent response formats
- **Security foundation**: JWT tokens, password hashing, and rate limiting
- **Error handling**: Professional error responses and logging system

### User Stories for This Module
- **As a user**, I want my registration data securely stored so that I can access my account
- **As a user**, I want fast and reliable login so that I can quickly access TaskFlow
- **As a developer**, I want consistent API patterns so that adding features is predictable
- **As an admin**, I want proper security measures so that user data is protected

### API Endpoints You're Building
```
POST /api/v1/auth/register    - User registration
POST /api/v1/auth/login       - User authentication  
POST /api/v1/auth/logout      - Session termination
GET  /api/v1/users/profile    - User profile data
PUT  /api/v1/users/profile    - Update profile
```

### UI Integration Reference
📋 **See how APIs connect to UI**: [Module 4+ UI Mockups](../mockups/MODULE_04_TO_10_MOCKUPS.md)

The mockups show user profile pages, dashboard loading states, and error handling that your APIs will power.

## Learning Objectives
- Build RESTful APIs with Express.js and TypeScript
- Implement proper middleware architecture
- Create robust error handling and logging
- Design API route structure and documentation
- Implement request validation and security measures

## Overview
This module creates the server-side foundation for TaskFlow using Express.js. You'll build endpoints that support user authentication and establish patterns that will scale to handle projects, tasks, and team collaboration.

## TypeScript Setup for Backend Development

**Why TypeScript for Backend?**
This module introduces TypeScript for server-side development. TypeScript provides:
- **Type safety** - Catch errors at compile time instead of runtime
- **Better IDE support** - Auto-completion, refactoring, and error detection
- **Code documentation** - Types serve as inline documentation
- **Easier debugging** - More informative error messages and stack traces
- **Team collaboration** - Shared understanding of data structures and interfaces

### Prerequisites and Environment Setup

**Required tools:**
- Node.js 16+ (check with `node --version`)
- npm or yarn package manager
- TypeScript knowledge (interfaces, types, async/await)

**Global TypeScript installation (recommended):**
```bash
npm install -g typescript
```

**Verify TypeScript installation:**
```bash
tsc --version
```

### TypeScript Project Configuration

**Essential TypeScript configuration file (`tsconfig.json`):**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020"],
    "module": "commonjs",
    "moduleResolution": "node",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "removeComments": false,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "allowSyntheticDefaultImports": true,
    "types": ["node"]
  },
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "node_modules",
    "dist",
    "**/*.test.ts"
  ]
}
```

**Key configuration explanations:**
- **target: "ES2020"** - Compiles to modern JavaScript with async/await support
- **strict: true** - Enables all strict type checking options
- **outDir: "./dist"** - Compiled JavaScript output directory
- **rootDir: "./src"** - TypeScript source files directory
- **sourceMap: true** - Generates source maps for debugging

**Project structure to create:**
```
server/
├── src/
│   ├── controllers/
│   ├── middleware/
│   ├── routes/
│   ├── schemas/
│   ├── types/
│   ├── utils/
│   ├── app.ts
│   └── server.ts
├── dist/                 # Compiled JavaScript (auto-generated)
├── tsconfig.json
├── package.json
└── .env
```

**Development dependencies to install:**
```bash
# TypeScript core and Node.js types
npm install -D typescript @types/node

# Development tools
npm install -D nodemon ts-node concurrently

# Express.js and related types
npm install -D @types/express @types/cors

# Additional type definitions (install as needed)
npm install -D @types/bcrypt @types/jsonwebtoken @types/swagger-jsdoc @types/swagger-ui-express
```

**Why these development dependencies:**
- **typescript** - TypeScript compiler
- **@types/node** - Type definitions for Node.js APIs
- **ts-node** - Run TypeScript directly without compilation step
- **nodemon** - Auto-restart server on file changes
- **@types/express** - Type definitions for Express.js

This setup ensures you have a robust TypeScript environment that provides type safety, excellent developer experience, and production-ready compilation for your TaskFlow API.

## Tasks

### Task 4.1: Express.js Project Setup
**Objective:** Initialize Express.js server with TypeScript and essential middleware

**What you need to accomplish:**
- Set up Express.js with TypeScript
- Configure development and production environments
- Install and configure essential middleware
- Set up hot reloading for development

**Documentation to consult:**
- [Express.js Getting Started](https://expressjs.com/en/starter/installing.html)
- [Express.js with TypeScript](https://expressjs.com/en/advanced/best-practice-performance.html)
- [Node.js TypeScript Setup](https://nodejs.dev/learn/nodejs-with-typescript)

**Installation commands to research:**
```bash
# In your server/ directory
npm init -y
npm install express
npm install -D typescript @types/node @types/express
npm install -D nodemon ts-node concurrently
npm install cors helmet morgan compression
npm install -D @types/cors
```

**Understanding Express.js Middleware**

Before building your server, it's important to understand what middleware is and why each piece is essential for a production-ready API:

**What is Middleware?**
Middleware functions are functions that execute during the request-response cycle. They have access to the request object (`req`), response object (`res`), and the next middleware function in the application's request-response cycle. Think of middleware as a pipeline that each HTTP request flows through.

**Essential Middleware for TaskFlow API:**

1. **Security Middleware (`helmet`)** - Protects your API from common vulnerabilities by setting various HTTP headers
   - Prevents XSS attacks, clickjacking, and other security threats
   - Essential for any API that will be deployed to production

2. **Cross-Origin Resource Sharing (`cors`)** - Allows your frontend to communicate with your API
   - Controls which domains can access your API
   - Enables cookie/credential sharing between frontend and backend
   - Critical for frontend-backend communication

3. **Response Compression (`compression`)** - Reduces response size for better performance
   - Compresses JSON responses, reducing bandwidth usage
   - Improves API response times, especially for larger payloads

4. **Request Logging (`morgan`)** - Logs all HTTP requests for debugging and monitoring
   - Tracks all incoming requests with timestamps and response codes
   - Essential for debugging API issues and monitoring usage

5. **Body Parsing** - Converts incoming request data into usable JavaScript objects
   - `express.json()` - Parses JSON request bodies (for API calls)
   - `express.urlencoded()` - Parses form data (for HTML forms)

**Basic server structure to create:**
```typescript
// server/src/app.ts
import express from 'express'
import cors from 'cors'
import helmet from 'helmet'
import morgan from 'morgan'
import compression from 'compression'

const app = express()

// Security middleware - MUST be first for maximum protection
app.use(helmet())

// CORS middleware - Configure before other middleware
app.use(cors({
  origin: process.env.CLIENT_URL || 'http://localhost:5173', // Allow your frontend
  credentials: true // Allow cookies/auth headers
}))

// Performance middleware
app.use(compression()) // Compress responses for faster loading

// Logging middleware - Log all requests for debugging
app.use(morgan('combined')) // Use 'combined' format for detailed logs

// Body parsing middleware - Parse incoming request data
app.use(express.json({ limit: '10mb' })) // Parse JSON requests
app.use(express.urlencoded({ extended: true })) // Parse form data

// Health check endpoint - Verify server is running
app.get('/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date().toISOString() })
})

export default app
```

**Why This Middleware Order Matters:**
1. **Security first** - `helmet()` sets security headers before anything else
2. **CORS early** - Must be configured before routes that need CORS
3. **Compression before parsing** - More efficient to compress after parsing
4. **Logging before routes** - Captures all requests including those to your API routes
5. **Body parsing before routes** - Routes need access to parsed request data

**Development scripts to configure:**
```json
{
  "scripts": {
    "dev": "nodemon --exec ts-node src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "type-check": "tsc --noEmit"
  }
}
```

**Acceptance criteria:**
- [ ] Express server runs without errors
- [ ] TypeScript compilation works
- [ ] Hot reloading functional in development
- [ ] Basic middleware configured
- [ ] Health check endpoint responds

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Node.js 16+ installed and working
- [ ] Understanding of Express.js fundamentals
- [ ] Basic knowledge of TypeScript
- [ ] Familiarity with npm package management

### Step-by-Step Implementation Approach

**1. Project Initialization and Structure**
- Create server directory within your project structure
- Initialize npm project with appropriate package.json settings
- Set up TypeScript configuration for Node.js environment
- Plan folder structure for scalable API development

**2. Core Dependencies Installation**
- Install Express.js and TypeScript dependencies
- Add essential middleware packages for security and logging
- Set up development tools (nodemon, ts-node) for efficient workflow
- Configure type definitions for proper TypeScript support

**3. Basic Server Configuration**
- Create main application file with Express setup
- Configure essential middleware in correct order
- Set up environment variable handling
- Implement basic error handling and logging

**4. Development Workflow Setup**
- Configure npm scripts for development and production
- Set up hot reloading for efficient development
- Test TypeScript compilation and error reporting
- Verify integration with your existing project structure

**Key Decision Points:**
- **TypeScript strictness:** Balance between safety and development speed
- **Middleware selection:** Choose packages that fit your security and performance needs
- **Environment configuration:** Plan for development, staging, and production environments
- **Project structure:** Organize for team development and future scaling

**Verification Steps:**
1. Test that server starts successfully with `npm run dev`
2. Verify hot reloading by making changes to server code
3. Check that TypeScript errors are caught during development
4. Test health check endpoint returns proper response

---

### Task 4.2: API Route Structure and Organization
**Objective:** Design and implement a scalable API route architecture

**What you need to accomplish:**
- Create modular route structure
- Implement route controllers
- Set up API versioning
- Create route middleware patterns

**Documentation to consult:**
- [Express.js Routing](https://expressjs.com/en/guide/routing.html)
- [Express.js Router](https://expressjs.com/en/4x/api.html#router)
- [REST API Design Best Practices](https://restfulapi.net/rest-api-design-tutorial-with-example/)

**Understanding Modular API Architecture**

Before implementing routes, it's crucial to understand why we organize our API code into separate folders and files, and how TypeScript enhances this structure.

**Why Separate Folders and Files?**

In a well-structured API, we separate concerns to make our code:
- **Maintainable** - Easy to find and modify specific functionality
- **Scalable** - New features can be added without disrupting existing code
- **Testable** - Individual components can be tested in isolation
- **Collaborative** - Team members can work on different parts simultaneously

**Project Structure Explained:**

1. **`controllers/`** - Business logic and request handling
   - Contains the actual functions that process requests
   - Keeps route files clean and focused on routing logic
   - Makes business logic reusable and testable

2. **`routes/`** - URL path definitions and middleware orchestration
   - Defines which URLs map to which controller functions
   - Applies middleware (validation, authentication) to specific routes
   - Keeps routing logic separate from business logic

3. **`middleware/`** - Reusable functions that process requests
   - Authentication, validation, error handling functions
   - Can be applied to multiple routes without duplication
   - Promotes code reuse and consistency

4. **`types/`** - TypeScript type definitions
   - Defines data structures and interfaces
   - Ensures type safety across the application
   - Makes code self-documenting

5. **`utils/`** - Helper functions and utilities
   - Common functionality used across the application
   - Keeps main code files focused on their primary purpose

**TypeScript in API Development**

We're using TypeScript instead of plain JavaScript for several important benefits:

**Type Safety:**
```typescript
// TypeScript catches errors at compile time
interface User {
  id: string
  email: string
  firstName: string
}

// This would cause a TypeScript error if 'age' doesn't exist on User
function getUser(id: string): User {
  // TypeScript ensures we return the correct structure
  return { id, email: "test@example.com", firstName: "John" }
}
```

**Better IDE Support:**
- Auto-completion for object properties and methods
- Real-time error detection while coding
- Refactoring tools that understand your code structure

**Self-Documenting Code:**
- Function signatures show exactly what parameters are expected
- Return types make it clear what functions produce
- Interfaces document data structures

**Enhanced Debugging:**
- Compile-time error checking prevents many runtime errors
- Better stack traces and error messages
- IDE can catch issues before running the code

**Route structure to implement:**
```
server/src/
├── controllers/        # Route handlers
│   ├── authController.ts
│   ├── userController.ts
│   ├── projectController.ts
│   └── taskController.ts
├── routes/            # Route definitions
│   ├── auth.ts
│   ├── users.ts
│   ├── projects.ts
│   └── tasks.ts
├── middleware/        # Custom middleware
│   ├── auth.ts
│   ├── validation.ts
│   └── errorHandler.ts
├── types/            # TypeScript types
│   ├── express.d.ts
│   └── api.ts
└── utils/            # Utility functions
    ├── logger.ts
    └── responses.ts
```

**Understanding TypeScript-Enhanced Route Implementation**

The route file below demonstrates how TypeScript and modular architecture work together:

```typescript
// routes/auth.ts
import { Router } from 'express'
import { authController } from '../controllers/authController'
import { validateRequest } from '../middleware/validation'
import { registerSchema, loginSchema } from '../schemas/auth'

const router = Router()

// Notice how we chain middleware functions before the controller
router.post('/register', 
  validateRequest(registerSchema),  // Custom middleware (defined in Task 4.3)
  authController.register          // Controller function (defined in Task 4.4)
)

router.post('/login',
  validateRequest(loginSchema),    // Validation middleware
  authController.login            // Authentication controller
)

router.post('/logout',
  authController.logout           // Simple logout - no validation needed
)

export default router
```

**Key TypeScript Benefits in This Example:**

1. **Import Type Safety** - TypeScript ensures imported functions exist and have correct signatures
2. **Function Parameter Types** - The `validateRequest` function knows what schema types to expect
3. **Router Method Types** - TypeScript validates that we're using correct HTTP methods and middleware signatures
4. **Auto-completion** - Your IDE will suggest available controller methods and middleware functions

**Custom Middleware Functions**

Notice how we're using `validateRequest(registerSchema)` - this is a custom middleware function that will be defined in **Task 4.3**. This pattern allows us to:

- **Reuse validation logic** across multiple routes
- **Keep routes clean** by extracting complex logic to middleware
- **Chain multiple middleware** functions for complex request processing
- **Apply consistent error handling** across all routes

The `authController.register` and other controller functions will be defined in **Task 4.4**, demonstrating the separation between routing logic and business logic.

**API Versioning Setup**

API versioning is essential for maintaining backward compatibility as your API evolves. Here's why and how we implement it:
```typescript
// app.ts
import authRoutes from './routes/auth'
import userRoutes from './routes/users'

// API v1 routes
app.use('/api/v1/auth', authRoutes)
app.use('/api/v1/users', userRoutes)
```

**Why API Versioning Matters:**

1. **Backward Compatibility** - Existing client applications continue working when you add new features
2. **Gradual Migration** - Users can migrate to new API versions at their own pace
3. **Breaking Changes** - You can introduce breaking changes in new versions without affecting existing users
4. **Clear Communication** - Version numbers communicate the API's maturity and stability

**How Our Versioning Works:**

- **URL-based versioning** - Version is part of the URL path (`/api/v1/`)
- **Consistent structure** - All endpoints follow the same versioning pattern
- **Future-ready** - Easy to add v2, v3, etc. when needed

**Example API endpoints with versioning:**
```
POST /api/v1/auth/register    # Version 1 registration
POST /api/v1/auth/login       # Version 1 login
GET  /api/v1/users/profile    # Version 1 user profile

# Future versions might look like:
POST /api/v2/auth/register    # Version 2 with enhanced features
GET  /api/v2/users/profile    # Version 2 with additional user data
```

This approach allows TaskFlow to evolve its API over time while maintaining support for existing integrations.

**Acceptance criteria:**
- [ ] Route structure organized and modular
- [ ] Controllers separate from route definitions
- [ ] API versioning implemented
- [ ] Route patterns consistent
- [ ] TypeScript types defined

---

### Task 4.3: Request Validation and Error Handling
**Objective:** Implement robust request validation and comprehensive error handling

**What you need to accomplish:**
- Create request validation middleware using Zod
- Implement global error handling
- Set up proper HTTP status codes
- Create consistent error response format

**Documentation to consult:**
- [Zod with Express](https://zod.dev/?id=express-middleware)
- [Express.js Error Handling](https://expressjs.com/en/guide/error-handling.html)
- [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

**Dependencies to install first:**
```bash
npm install zod
```

## Understanding Express Middleware Function Signatures

Before building our validation and error handling files, it's crucial to understand how Express middleware works and how function signatures enable middleware chaining.

### What is Middleware Chaining?

Middleware chaining is Express's way of processing requests through a series of functions before reaching your final route handler. Think of it as an assembly line where each function can:

1. **Process the request** (validate, authenticate, log, etc.)
2. **Modify the request/response** (add data, set headers, etc.)  
3. **End the request** (send an error response)
4. **Pass control to the next middleware** (continue the chain)

### Standard Middleware Function Signature

```typescript
function middleware(req: Request, res: Response, next: NextFunction) {
  // Middleware logic here
  next() // Call next() to continue to the next middleware
}
```

**The three parameters:**
- **`req` (Request)** - Contains request data (body, headers, params, query, etc.)
- **`res` (Response)** - Used to send responses back to the client
- **`next` (NextFunction)** - Function that passes control to the next middleware

### Error Handling Middleware Signature

```typescript
function errorMiddleware(err: Error, req: Request, res: Response, next: NextFunction) {
  // Error handling logic here
}
```

**Key difference:** Error middleware has **four parameters** with `err` as the first parameter. Express automatically detects this signature and routes errors to these functions.

### How Middleware Chaining Works in Practice

```typescript
// Example route with multiple middleware
app.post('/api/v1/auth/register',
  helmet(),                           // 1. Security headers
  cors(),                            // 2. CORS handling  
  validateRequest(registerSchema),   // 3. Request validation
  rateLimiter,                      // 4. Rate limiting
  authController.register           // 5. Final route handler
)
```

**The execution flow:**
1. **Request arrives** → `helmet()` adds security headers → calls `next()`
2. **Control passes** → `cors()` handles CORS → calls `next()`
3. **Control passes** → `validateRequest()` validates data → calls `next()` OR sends error response
4. **Control passes** → `rateLimiter` checks rate limits → calls `next()` OR sends error response
5. **Control passes** → `authController.register` processes the request → sends final response

### The Power of the `next()` Function

The `next()` function is what makes chaining possible:

```typescript
// Middleware that continues the chain
function exampleMiddleware(req: Request, res: Response, next: NextFunction) {
  console.log('Processing request...')
  // Do some work
  next() // ✅ Continue to next middleware
}

// Middleware that ends the chain
function validationMiddleware(req: Request, res: Response, next: NextFunction) {
  if (!req.body.email) {
    return res.status(400).json({ error: 'Email required' }) // ✅ End chain with error
  }
  next() // ✅ Continue to next middleware
}

// Middleware that handles errors
function errorMiddleware(err: Error, req: Request, res: Response, next: NextFunction) {
  console.error(err)
  res.status(500).json({ error: 'Internal server error' }) // ✅ End chain with error response
  // Note: Error middleware typically doesn't call next() unless passing to another error handler
}
```

### Higher-Order Functions for Reusable Middleware

```typescript
// This pattern allows creating configurable middleware
function validateRequest(schema: z.ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    // Validation logic using the provided schema
    // This returns a middleware function with the standard signature
  }
}

// Usage: The function call returns a middleware function
app.post('/register', validateRequest(registerSchema), controller.register)
app.post('/login', validateRequest(loginSchema), controller.login)
```

**Why this pattern:**
- **Reusability** - Same validation logic works with different schemas
- **Configuration** - Each usage can have different parameters
- **Type Safety** - TypeScript ensures proper schema types

### Error Propagation in Middleware Chains

```typescript
function asyncMiddleware(req: Request, res: Response, next: NextFunction) {
  try {
    // Some async operation
    doSomethingAsync()
    next() // Continue if successful
  } catch (error) {
    next(error) // Pass error to error handling middleware
  }
}
```

**Important notes:**
- **`next()` with no arguments** - Continue to next middleware
- **`next(error)` with error** - Skip to error handling middleware
- **No `next()` call** - Chain stops (must send response)

This middleware architecture is what makes Express so powerful and flexible. Now let's build our specific middleware files using these patterns.

---

This task involves creating three interconnected files that work together to provide robust validation and error handling for your TaskFlow API. We'll build each file step-by-step to understand how they integrate.

---

## File 1: Request Validation Middleware (`middleware/validation.ts`)

**Purpose:** This middleware validates incoming requests against predefined schemas and ensures data integrity before it reaches your controllers.

### Step 1: Set up the basic structure and imports

```typescript
// middleware/validation.ts
import { Request, Response, NextFunction } from 'express'
import { z } from 'zod'
```

**What we're adding:**
- **Express types** - `Request`, `Response`, `NextFunction` provide TypeScript support for Express middleware
- **Zod import** - `z` gives us powerful schema validation capabilities

**Why this is needed:**
- TypeScript ensures we implement the middleware function signature correctly
- Zod provides runtime type checking that TypeScript alone cannot do

### Step 2: Create the validation function signature

```typescript
// middleware/validation.ts
import { Request, Response, NextFunction } from 'express'
import { z } from 'zod'

export function validateRequest(schema: z.ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    // Implementation will go here
  }
}
```

**What we're adding:**
- **Higher-order function** - `validateRequest` returns a middleware function
- **Schema parameter** - Accepts any Zod schema for flexible validation
- **Middleware signature** - Returns standard Express middleware function

**Why this pattern:**
- **Reusability** - Same function works with different schemas
- **Composition** - Can be easily chained with other middleware
- **Type safety** - Schema parameter ensures only valid Zod schemas are used

### Step 3: Implement the validation logic

```typescript
// middleware/validation.ts
import { Request, Response, NextFunction } from 'express'
import { z } from 'zod'

export function validateRequest(schema: z.ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      const validatedData = schema.parse({
        body: req.body,
        query: req.query,
        params: req.params
      })
      
      // Attach validated data to request
      req.validatedData = validatedData
      next()
    } catch (error) {
      // Error handling will be added next
    }
  }
}
```

**What we're adding:**
- **Schema parsing** - Validates request data against the provided schema
- **Data attachment** - Adds validated data to the request object for controllers
- **Multiple validation targets** - Checks body, query parameters, and URL parameters

**Why this approach:**
- **Comprehensive validation** - Covers all types of request data
- **Data transformation** - Zod can coerce and transform data (e.g., string to number)
- **Controller convenience** - Controllers get pre-validated data via `req.validatedData`

### Step 4: Add error handling

```typescript
// middleware/validation.ts
import { Request, Response, NextFunction } from 'express'
import { z } from 'zod'

export function validateRequest(schema: z.ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      const validatedData = schema.parse({
        body: req.body,
        query: req.query,
        params: req.params
      })
      
      // Attach validated data to request
      req.validatedData = validatedData
      next()
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({
          success: false,
          error: 'Validation failed',
          details: error.errors.map(err => ({
            field: err.path.join('.'),
            message: err.message
          }))
        })
      }
      next(error)
    }
  }
}
```

**What we're adding:**
- **Zod error detection** - Specifically handles Zod validation errors
- **User-friendly error format** - Transforms technical errors into readable messages
- **Field-specific feedback** - Shows which exact fields failed validation
- **Error forwarding** - Non-validation errors are passed to the global error handler

**Why this error handling:**
- **Client-friendly responses** - Frontend developers get actionable error information
- **Debugging support** - Field names and specific error messages help developers fix issues
- **Separation of concerns** - Only handles validation errors, forwards everything else

### Step 5: Add TypeScript declaration for the request object

You'll also need to extend the Express Request type to include the `validatedData` property:

```typescript
// types/express.d.ts
declare global {
  namespace Express {
    interface Request {
      validatedData?: any
    }
  }
}

export {}
```

**What we're adding:**
- **Type augmentation** - Extends Express's Request interface
- **Global declaration** - Makes the property available throughout your app

**Why this is needed:**
- **TypeScript support** - Prevents TypeScript errors when accessing `req.validatedData`
- **IDE autocompletion** - Your editor will recognize the property

### Completed File: `middleware/validation.ts`

```typescript
// middleware/validation.ts
import { Request, Response, NextFunction } from 'express'
import { z } from 'zod'

export function validateRequest(schema: z.ZodSchema) {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      const validatedData = schema.parse({
        body: req.body,
        query: req.query,
        params: req.params
      })
      
      // Attach validated data to request
      req.validatedData = validatedData
      next()
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({
          success: false,
          error: 'Validation failed',
          details: error.errors.map(err => ({
            field: err.path.join('.'),
            message: err.message
          }))
        })
      }
      next(error)
    }
  }
}
```

---

## File 2: Error Handler Middleware (`middleware/errorHandler.ts`)

**Purpose:** This middleware provides centralized error handling for your entire API, ensuring consistent error responses and proper logging.

### Step 1: Create the custom error class

```typescript
// middleware/errorHandler.ts
import { Request, Response, NextFunction } from 'express'

export class AppError extends Error {
  public statusCode: number
  public isOperational: boolean

  constructor(message: string, statusCode: number) {
    super(message)
    this.statusCode = statusCode
    this.isOperational = true
    Error.captureStackTrace(this, this.constructor)
  }
}
```

**What we're adding:**
- **Custom error class** - Extends JavaScript's built-in Error class
- **Status code property** - HTTP status code for the error
- **Operational flag** - Distinguishes expected errors from bugs
- **Stack trace capture** - Maintains error stack information

**Why this pattern:**
- **Structured errors** - Consistent error objects throughout your application
- **HTTP status codes** - Proper HTTP responses for different error types
- **Debugging support** - Stack traces help identify error sources
- **Error classification** - Distinguishes between expected errors and unexpected bugs

### Step 2: Set up the error handler function signature

```typescript
// middleware/errorHandler.ts
import { Request, Response, NextFunction } from 'express'

export class AppError extends Error {
  public statusCode: number
  public isOperational: boolean

  constructor(message: string, statusCode: number) {
    super(message)
    this.statusCode = statusCode
    this.isOperational = true
    Error.captureStackTrace(this, this.constructor)
  }
}

export function errorHandler(
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) {
  // Implementation will go here
}
```

**What we're adding:**
- **Error-first parameter** - Express error middleware requires error as first parameter
- **Standard middleware signature** - Must include all four parameters for Express to recognize it

**Why this signature:**
- **Express convention** - Error middleware must have exactly this signature
- **Framework integration** - Express automatically routes errors to this function

### Step 3: Add error processing and logging

```typescript
// middleware/errorHandler.ts
import { Request, Response, NextFunction } from 'express'

export class AppError extends Error {
  public statusCode: number
  public isOperational: boolean

  constructor(message: string, statusCode: number) {
    super(message)
    this.statusCode = statusCode
    this.isOperational = true
    Error.captureStackTrace(this, this.constructor)
  }
}

export function errorHandler(
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) {
  let error = { ...err }
  error.message = err.message

  // Log error for debugging
  console.error(err)

  // Error transformation will be added next
}
```

**What we're adding:**
- **Error cloning** - Creates a copy to avoid modifying the original
- **Error logging** - Records all errors for debugging and monitoring

**Why this approach:**
- **Immutability** - Doesn't modify the original error object
- **Debugging support** - Logs provide visibility into production issues
- **Monitoring foundation** - Console logs can be captured by logging services

### Step 4: Add specific error transformations

```typescript
// middleware/errorHandler.ts
import { Request, Response, NextFunction } from 'express'

export class AppError extends Error {
  public statusCode: number
  public isOperational: boolean

  constructor(message: string, statusCode: number) {
    super(message)
    this.statusCode = statusCode
    this.isOperational = true
    Error.captureStackTrace(this, this.constructor)
  }
}

export function errorHandler(
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) {
  let error = { ...err }
  error.message = err.message

  // Log error for debugging
  console.error(err)

  // Handle specific error types
  
  // Mongoose bad ObjectId (for future database integration)
  if (err.name === 'CastError') {
    const message = 'Resource not found'
    error = new AppError(message, 404)
  }

  // Mongoose duplicate key error
  if (err.code === 11000) {
    const message = 'Duplicate field value entered'
    error = new AppError(message, 400)
  }

  // Send error response
  res.status(error.statusCode || 500).json({
    success: false,
    error: error.message || 'Server Error'
  })
}
```

**What we're adding:**
- **Specific error handling** - Transforms database and validation errors into user-friendly messages
- **Status code assignment** - Maps different error types to appropriate HTTP status codes
- **Consistent response format** - All errors follow the same JSON structure

**Why these transformations:**
- **User experience** - Technical database errors become readable messages
- **Security** - Prevents exposing internal system details
- **API consistency** - All endpoints return errors in the same format
- **Future-proofing** - Ready for database integration in later modules

### Completed File: `middleware/errorHandler.ts`

```typescript
// middleware/errorHandler.ts
import { Request, Response, NextFunction } from 'express'

export class AppError extends Error {
  public statusCode: number
  public isOperational: boolean

  constructor(message: string, statusCode: number) {
    super(message)
    this.statusCode = statusCode
    this.isOperational = true
    Error.captureStackTrace(this, this.constructor)
  }
}

export function errorHandler(
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) {
  let error = { ...err }
  error.message = err.message

  // Log error for debugging
  console.error(err)

  // Handle specific error types
  
  // Mongoose bad ObjectId (for future database integration)
  if (err.name === 'CastError') {
    const message = 'Resource not found'
    error = new AppError(message, 404)
  }

  // Mongoose duplicate key error
  if (err.code === 11000) {
    const message = 'Duplicate field value entered'
    error = new AppError(message, 400)
  }

  // Send error response
  res.status(error.statusCode || 500).json({
    success: false,
    error: error.message || 'Server Error'
  })
}
```

---

## File 3: Validation Schemas (`schemas/auth.ts`)

**Purpose:** This file defines the validation rules for authentication endpoints, ensuring data quality and security for user registration and login.

### Step 1: Set up the basic schema structure

```typescript
// schemas/auth.ts
import { z } from 'zod'
```

**What we're adding:**
- **Zod import** - Provides schema definition and validation capabilities

**Why Zod for schemas:**
- **Type inference** - TypeScript types are automatically generated from schemas
- **Runtime validation** - Validates actual request data, not just compile-time types
- **Rich validation** - Built-in validators for emails, strings, numbers, dates, etc.

### Step 2: Create the registration schema foundation

```typescript
// schemas/auth.ts
import { z } from 'zod'

export const registerSchema = z.object({
  body: z.object({
    // Field definitions will be added step by step
  })
})
```

**What we're adding:**
- **Schema structure** - Defines that we're validating the request body
- **Object validation** - Ensures the body is an object with specific properties

**Why this structure:**
- **Request targeting** - Specifically validates the request body (not query or params)
- **Type safety** - Creates strongly-typed schema that integrates with middleware

### Step 3: Add name validation fields

```typescript
// schemas/auth.ts
import { z } from 'zod'

export const registerSchema = z.object({
  body: z.object({
    firstName: z.string().min(2).max(50),
    lastName: z.string().min(2).max(50),
    // Email and password will be added next
  })
})
```

**What we're adding:**
- **String validation** - Ensures names are strings, not numbers or objects
- **Length constraints** - Names must be 2-50 characters for reasonable user experience
- **Required fields** - Both firstName and lastName are mandatory

**Why these rules:**
- **Data quality** - Prevents empty or excessively long names
- **Database compatibility** - Ensures names fit typical database field sizes
- **User experience** - Reasonable limits that don't restrict normal use

### Step 4: Add email validation

```typescript
// schemas/auth.ts
import { z } from 'zod'

export const registerSchema = z.object({
  body: z.object({
    firstName: z.string().min(2).max(50),
    lastName: z.string().min(2).max(50),
    email: z.string().email(),
    // Password will be added next
  })
})
```

**What we're adding:**
- **Email validation** - Uses Zod's built-in email format validator
- **Required field** - Email is mandatory for account creation

**Why email validation:**
- **Format verification** - Ensures email addresses are properly formatted
- **Security foundation** - Valid emails are crucial for account verification and password resets
- **User experience** - Immediate feedback on email format errors

### Step 5: Add password validation with security requirements

```typescript
// schemas/auth.ts
import { z } from 'zod'

export const registerSchema = z.object({
  body: z.object({
    firstName: z.string().min(2).max(50),
    lastName: z.string().min(2).max(50),
    email: z.string().email(),
    password: z.string().min(8).regex(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/)
  })
})
```

**What we're adding:**
- **Minimum length** - Passwords must be at least 8 characters
- **Complexity requirements** - Must contain lowercase, uppercase, and numeric characters
- **Regex validation** - Enforces password complexity rules

**Why these password rules:**
- **Security** - Complex passwords are harder to crack
- **Industry standards** - Follows common password policy recommendations
- **User protection** - Prevents weak passwords that could compromise accounts

**Breaking down the regex:** `^(?=.*[a-z])(?=.*[A-Z])(?=.*\d))`
- `^` - Start of string
- `(?=.*[a-z])` - Must contain at least one lowercase letter
- `(?=.*[A-Z])` - Must contain at least one uppercase letter
- `(?=.*\d)` - Must contain at least one digit
- The pattern ensures all three requirements are met

### Step 6: Create the login schema

```typescript
// schemas/auth.ts
import { z } from 'zod'

export const registerSchema = z.object({
  body: z.object({
    firstName: z.string().min(2).max(50),
    lastName: z.string().min(2).max(50),
    email: z.string().email(),
    password: z.string().min(8).regex(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/)
  })
})

export const loginSchema = z.object({
  body: z.object({
    email: z.string().email(),
    password: z.string().min(1)
  })
})
```

**What we're adding:**
- **Simplified login validation** - Only requires email and password
- **Relaxed password rules** - Login accepts any non-empty password
- **Email validation** - Same email format requirements as registration

**Why different validation rules:**
- **Login flexibility** - Users might have passwords that predate new complexity requirements
- **User experience** - Don't prevent login for existing users with simpler passwords
- **Security balance** - Still validate email format and require non-empty password

### Completed File: `schemas/auth.ts`

```typescript
// schemas/auth.ts
import { z } from 'zod'

export const registerSchema = z.object({
  body: z.object({
    firstName: z.string().min(2).max(50),
    lastName: z.string().min(2).max(50),
    email: z.string().email(),
    password: z.string().min(8).regex(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/)
  })
})

export const loginSchema = z.object({
  body: z.object({
    email: z.string().email(),
    password: z.string().min(1)
  })
})
```

---

## Integration and Usage

These three files work together to provide comprehensive request validation and error handling:

1. **Schemas define the rules** - What data is expected and what formats are valid
2. **Validation middleware applies the rules** - Checks incoming requests against schemas
3. **Error handler provides fallback** - Handles both validation errors and unexpected issues

**How they integrate in routes:**
```typescript
// routes/auth.ts (from Task 4.2)
import { validateRequest } from '../middleware/validation'
import { registerSchema, loginSchema } from '../schemas/auth'

router.post('/register', 
  validateRequest(registerSchema),  // Uses all three files together
  authController.register
)
```

**Error flow:**
1. Request comes in → Validation middleware checks against schema
2. Invalid data → Validation middleware sends 400 error response
3. Valid data → Controller processes request
4. Controller error → Error handler sends appropriate error response

**Acceptance criteria:**
- [ ] Request validation middleware working
- [ ] Global error handler implemented  
- [ ] Consistent error response format
- [ ] Proper HTTP status codes used
- [ ] Validation errors user-friendly

---

### Task 4.4: Authentication Endpoints
**Objective:** Create user authentication endpoints with proper security

**What you need to accomplish:**
- Implement user registration endpoint
- Create login endpoint with session/token handling
- Add logout functionality
- Implement basic rate limiting

**Documentation to consult:**
- [bcrypt for Password Hashing](https://www.npmjs.com/package/bcrypt)
- [JSON Web Tokens](https://jwt.io/introduction)
- [Express Rate Limiting](https://www.npmjs.com/package/express-rate-limit)

**Dependencies to install:**
```bash
npm install bcrypt jsonwebtoken express-rate-limit
npm install -D @types/bcrypt @types/jsonwebtoken
```

## Understanding Authentication Security Concepts

Before building our authentication endpoints, let's understand the key security concepts we'll implement:

### Password Hashing with bcrypt
**Why hash passwords?**
- **Never store plain text passwords** - If your database is compromised, user passwords remain secure
- **One-way encryption** - Even with the hash, the original password cannot be recovered
- **Salt protection** - Each password gets a unique salt to prevent rainbow table attacks

### JSON Web Tokens (JWT)
**Why use JWTs for authentication?**
- **Stateless authentication** - Server doesn't need to store session data
- **Self-contained** - Token contains user information and expiration
- **Scalable** - Works across multiple servers without shared session storage

### Rate Limiting
**Why limit authentication attempts?**
- **Brute force protection** - Prevents attackers from trying many passwords
- **DDoS mitigation** - Limits request volume from individual IPs
- **Resource protection** - Prevents abuse of computationally expensive operations

---

## File 1: Authentication Controller (`controllers/authController.ts`)

**Purpose:** This controller handles user registration, login, and logout with proper security measures including password hashing and JWT token generation.

### Step 1: Set up the basic controller structure and imports

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'
```

**What we're adding:**
- **Express types** - For proper TypeScript middleware function signatures
- **bcrypt** - For secure password hashing and comparison
- **jsonwebtoken** - For creating and verifying JWT tokens
- **AppError** - Our custom error class from Task 4.3

**Why these dependencies:**
- **Type safety** - Express types ensure correct parameter usage
- **Security** - bcrypt uses industry-standard hashing algorithms
- **Authentication** - JWT provides stateless, scalable authentication
- **Error handling** - Consistent error responses using our custom error class

### Step 2: Create the controller object structure

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'

export const authController = {
  // Methods will be added step by step
}
```

**What we're adding:**
- **Export structure** - Makes controller methods available for route imports
- **Object pattern** - Groups related authentication functions together

**Why this pattern:**
- **Organization** - All auth-related functions in one place
- **Modularity** - Easy to import specific methods in routes
- **Scalability** - Easy to add new authentication methods later

### Step 3: Build the registration method foundation

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'

export const authController = {
  async register(req: Request, res: Response, next: NextFunction) {
    try {
      const { firstName, lastName, email, password } = req.validatedData.body
      
      // Implementation will be added step by step
      
    } catch (error) {
      next(error)
    }
  }
}
```

**What we're adding:**
- **Async function** - Registration involves async operations (hashing, database operations)
- **Destructuring** - Extracts validated data from the request body
- **Error handling** - Catches and forwards errors to our error handler middleware

**Why this approach:**
- **Data safety** - Uses pre-validated data from our validation middleware (Task 4.3)
- **Async support** - Properly handles asynchronous password hashing and database operations
- **Error propagation** - Forwards errors to centralized error handling

### Step 4: Add user existence check (mock implementation)

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'

// Mock user storage (will be replaced with database in Module 6)
let users: any[] = []

export const authController = {
  async register(req: Request, res: Response, next: NextFunction) {
    try {
      const { firstName, lastName, email, password } = req.validatedData.body
      
      // Check if user already exists
      const existingUser = users.find(user => user.email === email)
      if (existingUser) {
        return next(new AppError('User already exists with this email', 409))
      }
      
      // Password hashing will be added next
      
    } catch (error) {
      next(error)
    }
  }
}
```

**What we're adding:**
- **Mock storage** - Temporary in-memory user storage for development
- **Duplicate check** - Prevents registering the same email twice
- **Conflict error** - Returns 409 status code for duplicate email attempts

**Why this implementation:**
- **Development ready** - Works without database setup
- **Production patterns** - Same logic will work with real database
- **Security** - Prevents account enumeration attacks by consistent error handling

### Step 5: Implement secure password hashing

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'

// Mock user storage (will be replaced with database in Module 6)
let users: any[] = []

export const authController = {
  async register(req: Request, res: Response, next: NextFunction) {
    try {
      const { firstName, lastName, email, password } = req.validatedData.body
      
      // Check if user already exists
      const existingUser = users.find(user => user.email === email)
      if (existingUser) {
        return next(new AppError('User already exists with this email', 409))
      }
      
      // Hash password securely
      const saltRounds = 12
      const hashedPassword = await bcrypt.hash(password, saltRounds)
      
      // User creation will be added next
      
    } catch (error) {
      next(error)
    }
  }
}
```

**What we're adding:**
- **Salt rounds** - Controls the computational cost of hashing (12 is recommended for 2024)
- **Password hashing** - Converts plain text password to secure hash
- **Await operation** - Properly waits for the async hashing operation

**Why salt rounds = 12:**
- **Security balance** - High enough to prevent brute force, not so high it's slow
- **Future-proof** - Accounts for improving hardware capabilities
- **Industry standard** - Recommended by security experts for current applications

### Step 6: Create user object and JWT token

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'

// Mock user storage (will be replaced with database in Module 6)
let users: any[] = []

export const authController = {
  async register(req: Request, res: Response, next: NextFunction) {
    try {
      const { firstName, lastName, email, password } = req.validatedData.body
      
      // Check if user already exists
      const existingUser = users.find(user => user.email === email)
      if (existingUser) {
        return next(new AppError('User already exists with this email', 409))
      }
      
      // Hash password securely
      const saltRounds = 12
      const hashedPassword = await bcrypt.hash(password, saltRounds)
      
      // Create user object
      const user = {
        id: Date.now().toString(), // Simple ID generation for development
        firstName,
        lastName,
        email,
        password: hashedPassword,
        createdAt: new Date()
      }
      
      // Store user (in production, this would be a database save)
      users.push(user)
      
      // Generate JWT token
      const token = jwt.sign(
        { userId: user.id, email: user.email }, // Payload
        process.env.JWT_SECRET!, // Secret key
        { expiresIn: '7d' } // Token expires in 7 days
      )
      
      // Send response will be added next
      
    } catch (error) {
      next(error)
    }
  }
}
```

**What we're adding:**
- **User object creation** - Structured user data with hashed password
- **User storage** - Saves user to mock storage (will be database later)
- **JWT token generation** - Creates signed token for authentication
- **Token payload** - Contains user ID and email for identification
- **Token expiration** - 7-day expiry for security

**Why this JWT structure:**
- **Minimal payload** - Only essential information to reduce token size
- **Expiration** - Limits the damage if token is compromised
- **Signing** - Verifies token wasn't tampered with

### Step 7: Send registration response

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'

// Mock user storage (will be replaced with database in Module 6)
let users: any[] = []

export const authController = {
  async register(req: Request, res: Response, next: NextFunction) {
    try {
      const { firstName, lastName, email, password } = req.validatedData.body
      
      // Check if user already exists
      const existingUser = users.find(user => user.email === email)
      if (existingUser) {
        return next(new AppError('User already exists with this email', 409))
      }
      
      // Hash password securely
      const saltRounds = 12
      const hashedPassword = await bcrypt.hash(password, saltRounds)
      
      // Create user object
      const user = {
        id: Date.now().toString(),
        firstName,
        lastName,
        email,
        password: hashedPassword,
        createdAt: new Date()
      }
      
      // Store user
      users.push(user)
      
      // Generate JWT token
      const token = jwt.sign(
        { userId: user.id, email: user.email },
        process.env.JWT_SECRET!,
        { expiresIn: '7d' }
      )
      
      // Send success response
      res.status(201).json({
        success: true,
        data: {
          user: {
            id: user.id,
            firstName: user.firstName,
            lastName: user.lastName,
            email: user.email
            // Note: password is NOT included in response
          },
          token
        }
      })
    } catch (error) {
      next(error)
    }
  }
}
```

**What we're adding:**
- **201 status code** - Indicates successful resource creation
- **Consistent response format** - Matches our API response standards
- **User data filtering** - Excludes sensitive information (password, internal fields)
- **Token inclusion** - Provides token for immediate authentication

**Why exclude password from response:**
- **Security** - Never expose password hashes to clients
- **Data minimization** - Only send necessary information
- **API standards** - Common practice in REST APIs

### Step 8: Add login method

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'

// Mock user storage (will be replaced with database in Module 6)
let users: any[] = []

export const authController = {
  async register(req: Request, res: Response, next: NextFunction) {
    // ... registration implementation from above
  },

  async login(req: Request, res: Response, next: NextFunction) {
    try {
      const { email, password } = req.validatedData.body
      
      // Find user by email
      const user = users.find(u => u.email === email)
      if (!user) {
        return next(new AppError('Invalid credentials', 401))
      }
      
      // Validate password
      const isValidPassword = await bcrypt.compare(password, user.password)
      if (!isValidPassword) {
        return next(new AppError('Invalid credentials', 401))
      }
      
      // Generate token
      const token = jwt.sign(
        { userId: user.id, email: user.email },
        process.env.JWT_SECRET!,
        { expiresIn: '7d' }
      )
      
      res.json({
        success: true,
        data: {
          user: {
            id: user.id,
            firstName: user.firstName,
            lastName: user.lastName,
            email: user.email
          },
          token
        }
      })
    } catch (error) {
      next(error)
    }
  }
}
```

**What we're adding:**
- **User lookup** - Finds user by email address
- **Password comparison** - Uses bcrypt to safely compare passwords
- **Generic error messages** - Same message for user not found and wrong password
- **Token generation** - Same JWT process as registration

**Why generic error messages:**
- **Security** - Prevents email enumeration attacks
- **Consistency** - Makes it harder for attackers to determine valid emails
- **User experience** - Clear feedback without revealing system internals

### Step 9: Add logout method

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'

// Mock user storage (will be replaced with database in Module 6)
let users: any[] = []

export const authController = {
  async register(req: Request, res: Response, next: NextFunction) {
    // ... registration implementation
  },

  async login(req: Request, res: Response, next: NextFunction) {
    // ... login implementation
  },

  logout(req: Request, res: Response) {
    // For JWT tokens, logout is typically handled client-side by removing the token
    // In enhanced implementations, you could:
    // 1. Maintain a token blacklist
    // 2. Use shorter token expiry with refresh tokens
    // 3. Store token revocation in database
    
    res.json({
      success: true,
      message: 'Logged out successfully'
    })
  }
}
```

**What we're adding:**
- **Simple logout** - Provides endpoint for logout completion
- **Client-side responsibility** - JWT logout typically handled by removing token from client storage
- **Enhancement comments** - Documents more advanced logout strategies

**Why simple JWT logout:**
- **Stateless nature** - JWTs are designed to be stateless
- **Client responsibility** - Client removes token from storage (localStorage, cookies)
- **Simplicity** - No server-side session management required

### Completed File: `controllers/authController.ts`

```typescript
// controllers/authController.ts
import { Request, Response, NextFunction } from 'express'
import bcrypt from 'bcrypt'
import jwt from 'jsonwebtoken'
import { AppError } from '../middleware/errorHandler'

// Mock user storage (will be replaced with database in Module 6)
let users: any[] = []

export const authController = {
  async register(req: Request, res: Response, next: NextFunction) {
    try {
      const { firstName, lastName, email, password } = req.validatedData.body
      
      // Check if user already exists
      const existingUser = users.find(user => user.email === email)
      if (existingUser) {
        return next(new AppError('User already exists with this email', 409))
      }
      
      // Hash password securely
      const saltRounds = 12
      const hashedPassword = await bcrypt.hash(password, saltRounds)
      
      // Create user object
      const user = {
        id: Date.now().toString(),
        firstName,
        lastName,
        email,
        password: hashedPassword,
        createdAt: new Date()
      }
      
      // Store user
      users.push(user)
      
      // Generate JWT token
      const token = jwt.sign(
        { userId: user.id, email: user.email },
        process.env.JWT_SECRET!,
        { expiresIn: '7d' }
      )
      
      // Send success response
      res.status(201).json({
        success: true,
        data: {
          user: {
            id: user.id,
            firstName: user.firstName,
            lastName: user.lastName,
            email: user.email
          },
          token
        }
      })
    } catch (error) {
      next(error)
    }
  },

  async login(req: Request, res: Response, next: NextFunction) {
    try {
      const { email, password } = req.validatedData.body
      
      // Find user by email
      const user = users.find(u => u.email === email)
      if (!user) {
        return next(new AppError('Invalid credentials', 401))
      }
      
      // Validate password
      const isValidPassword = await bcrypt.compare(password, user.password)
      if (!isValidPassword) {
        return next(new AppError('Invalid credentials', 401))
      }
      
      // Generate token
      const token = jwt.sign(
        { userId: user.id, email: user.email },
        process.env.JWT_SECRET!,
        { expiresIn: '7d' }
      )
      
      res.json({
        success: true,
        data: {
          user: {
            id: user.id,
            firstName: user.firstName,
            lastName: user.lastName,
            email: user.email
          },
          token
        }
      })
    } catch (error) {
      next(error)
    }
  },

  logout(req: Request, res: Response) {
    res.json({
      success: true,
      message: 'Logged out successfully'
    })
  }
}
```

---

## File 2: Rate Limiting Middleware (`middleware/rateLimiter.ts`)

**Purpose:** This middleware protects authentication endpoints from brute force attacks and abuse by limiting the number of requests from each IP address.

### Step 1: Set up the rate limiter imports and basic structure

```typescript
// middleware/rateLimiter.ts
import rateLimit from 'express-rate-limit'
```

**What we're adding:**
- **express-rate-limit import** - Industry-standard rate limiting middleware for Express

**Why express-rate-limit:**
- **Battle-tested** - Used by thousands of production applications
- **Configurable** - Flexible options for different use cases
- **Memory efficient** - Built-in cleanup of expired rate limit data

### Step 2: Configure authentication-specific rate limiting

```typescript
// middleware/rateLimiter.ts
import rateLimit from 'express-rate-limit'

export const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // limit each IP to 5 requests per windowMs
  // Additional configuration will be added
})
```

**What we're adding:**
- **Time window** - 15-minute sliding window for rate limiting
- **Request limit** - Maximum 5 authentication attempts per IP per window
- **Export name** - Descriptive name indicating this is for authentication endpoints

**Why these limits:**
- **Security balance** - Allows legitimate retries while blocking brute force attacks
- **User experience** - Doesn't overly restrict normal usage patterns
- **Attack prevention** - 5 attempts per 15 minutes makes brute force impractical

### Step 3: Add custom error message and response format

```typescript
// middleware/rateLimiter.ts
import rateLimit from 'express-rate-limit'

export const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // limit each IP to 5 requests per windowMs
  message: {
    success: false,
    error: 'Too many authentication attempts, please try again later'
  },
  // Additional options will be added
})
```

**What we're adding:**
- **Custom error message** - User-friendly explanation of the rate limit
- **Consistent format** - Matches our API response format with success: false

**Why custom messages:**
- **User clarity** - Explains why the request was blocked
- **API consistency** - Matches the response format used throughout our API
- **Security awareness** - Informs users about security measures without revealing details

### Step 4: Configure headers and security options

```typescript
// middleware/rateLimiter.ts
import rateLimit from 'express-rate-limit'

export const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // limit each IP to 5 requests per windowMs
  message: {
    success: false,
    error: 'Too many authentication attempts, please try again later'
  },
  standardHeaders: true, // Return rate limit info in the `RateLimit-*` headers
  legacyHeaders: false, // Disable the `X-RateLimit-*` headers
})
```

**What we're adding:**
- **Standard headers** - Modern rate limit headers (RateLimit-Limit, RateLimit-Remaining, etc.)
- **Legacy headers disabled** - Removes older X-RateLimit-* headers for cleaner responses

**Why these header settings:**
- **Client awareness** - Headers inform clients about rate limit status
- **Standard compliance** - Uses the newer, standardized header format
- **Clean responses** - Avoids duplicate headers for the same information

### Step 5: Add additional rate limiters for different use cases

```typescript
// middleware/rateLimiter.ts
import rateLimit from 'express-rate-limit'

export const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // limit each IP to 5 requests per windowMs
  message: {
    success: false,
    error: 'Too many authentication attempts, please try again later'
  },
  standardHeaders: true,
  legacyHeaders: false
})

// General API rate limiter (more permissive)
export const generalLimiter = rateLimit({
  windowMs: 1 * 60 * 1000, // 1 minute
  max: 100, // limit each IP to 100 requests per minute
  message: {
    success: false,
    error: 'Too many requests, please try again later'
  },
  standardHeaders: true,
  legacyHeaders: false
})

// Strict limiter for sensitive operations
export const strictLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 3, // limit each IP to 3 requests per hour
  message: {
    success: false,
    error: 'Rate limit exceeded for this operation'
  },
  standardHeaders: true,
  legacyHeaders: false
})
```

**What we're adding:**
- **General limiter** - Higher limits for regular API endpoints
- **Strict limiter** - Very restrictive limits for sensitive operations
- **Flexibility** - Different rate limits for different security needs

**Why multiple limiters:**
- **Proportional security** - More sensitive operations get stricter limits
- **User experience** - Regular API usage isn't overly restricted
- **Attack mitigation** - Different attack vectors require different defenses

### Completed File: `middleware/rateLimiter.ts`

```typescript
// middleware/rateLimiter.ts
import rateLimit from 'express-rate-limit'

// Authentication rate limiter - strict limits for login/register
export const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // limit each IP to 5 requests per windowMs
  message: {
    success: false,
    error: 'Too many authentication attempts, please try again later'
  },
  standardHeaders: true, // Return rate limit info in the `RateLimit-*` headers
  legacyHeaders: false, // Disable the `X-RateLimit-*` headers
})

// General API rate limiter - more permissive for regular operations
export const generalLimiter = rateLimit({
  windowMs: 1 * 60 * 1000, // 1 minute
  max: 100, // limit each IP to 100 requests per minute
  message: {
    success: false,
    error: 'Too many requests, please try again later'
  },
  standardHeaders: true,
  legacyHeaders: false
})

// Strict limiter for sensitive operations (password reset, account changes)
export const strictLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 3, // limit each IP to 3 requests per hour
  message: {
    success: false,
    error: 'Rate limit exceeded for this operation'
  },
  standardHeaders: true,
  legacyHeaders: false
})
```

---

## Integration and Environment Setup

### Environment Variables Required

Create a `.env` file in your server directory:

```bash
# JWT Configuration
JWT_SECRET=your_super_secret_jwt_key_here_minimum_32_characters

# Server Configuration
CLIENT_URL=http://localhost:5173
PORT=3000
```

**Important security notes:**
- **JWT_SECRET** - Use a strong, random string (minimum 32 characters)
- **Never commit .env files** - Add `.env` to your `.gitignore`
- **Production secrets** - Use environment variable management in production

### Using the Controllers and Rate Limiters in Routes

```typescript
// routes/auth.ts (updated from Task 4.2)
import { Router } from 'express'
import { authController } from '../controllers/authController'
import { validateRequest } from '../middleware/validation'
import { registerSchema, loginSchema } from '../schemas/auth'
import { authLimiter } from '../middleware/rateLimiter'

const router = Router()

router.post('/register',
  authLimiter,                        // Rate limiting
  validateRequest(registerSchema),    // Validation
  authController.register            // Controller
)

router.post('/login',
  authLimiter,                       // Rate limiting
  validateRequest(loginSchema),      // Validation
  authController.login              // Controller
)

router.post('/logout',
  authController.logout             // Simple logout, no rate limiting needed
)

export default router
```

### Testing Your Implementation

**Manual testing with curl:**

```bash
# Test registration
curl -X POST http://localhost:3000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "John",
    "lastName": "Doe", 
    "email": "john@example.com",
    "password": "Password123"
  }'

# Test login
curl -X POST http://localhost:3000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "Password123"
  }'

# Test rate limiting (run this 6 times quickly)
for i in {1..6}; do
  curl -X POST http://localhost:3000/api/v1/auth/login \
    -H "Content-Type: application/json" \
    -d '{"email":"test","password":"test"}'
done
```

**Acceptance criteria:**
- [ ] Registration endpoint creates users with hashed passwords
- [ ] Login endpoint validates credentials and returns JWT tokens
- [ ] Passwords are properly hashed using bcrypt
- [ ] JWT tokens are generated correctly with proper expiration
- [ ] Rate limiting blocks excessive authentication attempts
- [ ] Proper error responses for all failure cases
- [ ] Environment variables configured for JWT secret

---

### Task 4.5: CRUD Endpoints and API Documentation
**Objective:** Create basic CRUD endpoints and document the API

**What you need to accomplish:**
- Implement user profile endpoints
- Create basic project/task endpoints (mock data)
- Set up API documentation with Swagger
- Add request/response logging

**Documentation to consult:**
- [OpenAPI Specification](https://swagger.io/specification/)
- [Swagger/OpenAPI with Express](https://swagger.io/docs/specification/about/)
- [API Documentation Best Practices](https://swagger.io/blog/api-documentation/api-documentation-best-practices/)

**Dependencies to install:**
```bash
npm install swagger-jsdoc swagger-ui-express
npm install -D @types/swagger-jsdoc @types/swagger-ui-express
```

## Understanding CRUD Operations

**CRUD** stands for the four basic operations you can perform on data:

- **C**reate - Add new records (POST requests)
- **R**ead - Retrieve existing records (GET requests)  
- **U**pdate - Modify existing records (PUT/PATCH requests)
- **D**elete - Remove records (DELETE requests)

These operations form the foundation of most APIs and correspond directly to HTTP methods. In TaskFlow, we'll implement CRUD operations for users, projects, and tasks.

**CRUD Examples:**
- **Create User Profile** - POST /api/v1/users (register creates, this updates)
- **Read User Profile** - GET /api/v1/users/profile
- **Update User Profile** - PUT /api/v1/users/profile
- **Delete User Account** - DELETE /api/v1/users/profile

---

## User Controller Implementation

Create a `controllers/userController.ts` file to handle user profile operations. This controller provides CRUD functionality for user data management after authentication.

**Purpose of the User Controller:**
The user controller manages user profile data and account settings. Unlike the auth controller which handles login/registration, this controller focuses on profile management for authenticated users.

```typescript
// controllers/userController.ts
import { Request, Response, NextFunction } from 'express'
import { AppError } from '../middleware/errorHandler'

// Import the same mock users array from authController for consistency
// In Module 6, this will be replaced with actual database operations
import { users } from './authController'

export const userController = {
  /**
   * Get current user's profile information
   * Requires authentication (user must be logged in)
   */
  async getProfile(req: Request, res: Response, next: NextFunction) {
    try {
      // Get user ID from authentication middleware (will be implemented in advanced tasks)
      const userId = req.user?.id || req.headers['user-id'] as string // Temporary fallback
      
      // Find user in mock storage
      const user = users.find(u => u.id === userId)
      if (!user) {
        return next(new AppError('User not found', 404))
      }
      
      res.json({
        success: true,
        data: {
          user: {
            id: user.id,
            firstName: user.firstName,
            lastName: user.lastName,
            email: user.email,
            createdAt: user.createdAt
            // Note: password is never included in responses
          }
        }
      })
    } catch (error) {
      next(error)
    }
  },

  /**
   * Update current user's profile information
   * Allows users to modify their firstName, lastName, etc.
   */
  async updateProfile(req: Request, res: Response, next: NextFunction) {
    try {
      const userId = req.user?.id || req.headers['user-id'] as string
      const updates = req.validatedData.body
      
      // Find and update user in mock storage
      const userIndex = users.findIndex(u => u.id === userId)
      if (userIndex === -1) {
        return next(new AppError('User not found', 404))
      }
      
      // Update user data (preserving password and other sensitive fields)
      users[userIndex] = {
        ...users[userIndex],
        ...updates,
        // Prevent updating sensitive fields through this endpoint
        id: users[userIndex].id,
        password: users[userIndex].password,
        createdAt: users[userIndex].createdAt
      }
      
      res.json({
        success: true,
        data: {
          user: {
            id: users[userIndex].id,
            firstName: users[userIndex].firstName,
            lastName: users[userIndex].lastName,
            email: users[userIndex].email,
            createdAt: users[userIndex].createdAt
          }
        }
      })
    } catch (error) {
      next(error)
    }
  },

  /**
   * Delete current user's account
   * Permanently removes user from the system
   */
  async deleteAccount(req: Request, res: Response, next: NextFunction) {
    try {
      const userId = req.user?.id || req.headers['user-id'] as string
      
      // Find and remove user from mock storage
      const userIndex = users.findIndex(u => u.id === userId)
      if (userIndex === -1) {
        return next(new AppError('User not found', 404))
      }
      
      users.splice(userIndex, 1)
      
      res.json({
        success: true,
        message: 'Account deleted successfully'
      })
    } catch (error) {
      next(error)
    }
  }
}
```

---

## API Documentation with Swagger

**What is Swagger?**
Swagger (now called OpenAPI) is a specification for describing REST APIs. It provides:
- **Interactive documentation** - Test endpoints directly from the browser
- **Auto-generated schemas** - API structure documented automatically
- **Client generation** - Generate API clients for different languages
- **Validation** - Ensure API responses match documented schemas

**Why use Swagger?**
- **Developer experience** - Easy to understand and test APIs
- **Team collaboration** - Shared understanding of API structure
- **Quality assurance** - Documentation stays in sync with code
- **Client integration** - Other developers can easily integrate with your API

### Step-by-Step Swagger Setup

**Step 1: Install Swagger dependencies**
```bash
npm install swagger-jsdoc swagger-ui-express
npm install -D @types/swagger-jsdoc @types/swagger-ui-express
```

**Step 2: Create the basic Swagger configuration**

Create `docs/swagger.ts`:

```typescript
// docs/swagger.ts
// Import Swagger tools for API documentation generation
import swaggerJsdoc from 'swagger-jsdoc'  // Parses JSDoc comments into OpenAPI spec
import swaggerUi from 'swagger-ui-express' // Serves interactive API documentation UI

// Configuration object for swagger-jsdoc
const options = {
  definition: {
    openapi: '3.0.0',  // OpenAPI specification version (latest stable)
    info: {
      title: 'TaskFlow API',                                    // API name displayed in docs
      version: '1.0.0',                                        // API version for tracking changes
      description: 'A comprehensive task management application API'  // Brief API description
    }
  },
  apis: [] // Array of file paths to scan for @openapi comments (populated in next step)
}

// Generate OpenAPI specification from configuration and JSDoc comments
const specs = swaggerJsdoc(options)

// Export both the generated specs and the UI middleware
export { specs, swaggerUi }
```

**Step 3: Add server configuration and security schemes**

```typescript
// docs/swagger.ts
import swaggerJsdoc from 'swagger-jsdoc'
import swaggerUi from 'swagger-ui-express'

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'TaskFlow API',
      version: '1.0.0',
      description: 'A comprehensive task management application API',
      contact: {                          // Support contact information
        name: 'TaskFlow Support',         // Displayed in API docs
        email: 'support@taskflow.com'     // Contact email for API questions
      }
    },
    servers: [                            // List of available API servers
      {
        url: process.env.API_URL || 'http://localhost:3000/api/v1',  // Base URL for API requests
        description: 'Development server' // Human-readable server description
      }
    ],
    components: {                         // Reusable components for API documentation
      securitySchemes: {                  // Authentication schemes available
        bearerAuth: {                     // Name for JWT authentication scheme
          type: 'http',                   // HTTP authentication type
          scheme: 'bearer',               // Bearer token authentication
          bearerFormat: 'JWT',            // Specifies JWT token format
          description: 'Enter JWT token obtained from login endpoint'  // User instructions
        }
      }
    }
  },
  apis: [] // File paths to scan for documentation comments (populated in next step)
}

// Generate the OpenAPI specification
const specs = swaggerJsdoc(options)

export { specs, swaggerUi }
```

**Step 4: Configure API file scanning**

```typescript
// docs/swagger.ts
import swaggerJsdoc from 'swagger-jsdoc'
import swaggerUi from 'swagger-ui-express'

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'TaskFlow API',
      version: '1.0.0',
      description: 'A comprehensive task management application API',
      contact: {
        name: 'TaskFlow Support',
        email: 'support@taskflow.com'
      }
    },
    servers: [
      {
        url: process.env.API_URL || 'http://localhost:3000/api/v1',
        description: 'Development server'
      }
    ],
    components: {
      securitySchemes: {
        bearerAuth: {
          type: 'http',
          scheme: 'bearer',
          bearerFormat: 'JWT',
          description: 'Enter JWT token obtained from login endpoint'
        }
      }
    }
  },
  // Configure swagger-jsdoc to scan these file patterns for @openapi comments
  apis: [
    './src/routes/*.ts',      // Scan all TypeScript files in routes directory for endpoint documentation
    './src/controllers/*.ts'  // Scan all TypeScript files in controllers directory for additional docs
  ]
}

// Parse the configuration and file comments to generate OpenAPI specification
const specs = swaggerJsdoc(options)

export { specs, swaggerUi }
```

**Step 5: Add Swagger route to your main app**

In your `app.ts` file, add the Swagger documentation route:

```typescript
// app.ts
// Import the generated OpenAPI specs and Swagger UI middleware
import { specs, swaggerUi } from './docs/swagger'

// ... other middleware (cors, helmet, etc.)

// Configure and serve interactive API documentation
app.use('/api-docs',                    // URL path where docs will be accessible
  swaggerUi.serve,                      // Serve static Swagger UI assets (CSS, JS, etc.)
  swaggerUi.setup(specs, {              // Setup interactive documentation with our API specs
    explorer: true,                     // Enable "Try it out" functionality for testing endpoints
    customCss: '.swagger-ui .topbar { display: none }',  // Hide Swagger's default header
    customSiteTitle: 'TaskFlow API Documentation'        // Browser tab title
  })
)

// ... your API routes (should come AFTER documentation route)
```

**Step 6: Add API documentation comments to your routes**

Add documentation comments above your route definitions:

```typescript
// routes/auth.ts
/**
 * @openapi
 * /auth/register:
 *   post:
 *     summary: Register a new user account
 *     description: Creates a new user account with email and password
 *     tags: [Authentication]
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             required:
 *               - firstName
 *               - lastName
 *               - email
 *               - password
 *             properties:
 *               firstName:
 *                 type: string
 *                 example: "John"
 *                 description: User's first name
 *               lastName:
 *                 type: string
 *                 example: "Doe"
 *                 description: User's last name
 *               email:
 *                 type: string
 *                 format: email
 *                 example: "john@example.com"
 *                 description: Valid email address
 *               password:
 *                 type: string
 *                 minLength: 8
 *                 example: "Password123"
 *                 description: Password with at least 8 characters, including uppercase, lowercase, and number
 *     responses:
 *       201:
 *         description: User registered successfully
 *         content:
 *           application/json:
 *             schema:
 *               type: object
 *               properties:
 *                 success:
 *                   type: boolean
 *                   example: true
 *                 data:
 *                   type: object
 *                   properties:
 *                     user:
 *                       type: object
 *                       properties:
 *                         id:
 *                           type: string
 *                         firstName:
 *                           type: string
 *                         lastName:
 *                           type: string
 *                         email:
 *                           type: string
 *                     token:
 *                       type: string
 *                       description: JWT authentication token
 *       400:
 *         description: Validation error
 *       409:
 *         description: User already exists
 */
router.post('/register',
  authLimiter,
  validateRequest(registerSchema),
  authController.register
)
```

### Completed Swagger Configuration File

```typescript
// docs/swagger.ts
import swaggerJsdoc from 'swagger-jsdoc'
import swaggerUi from 'swagger-ui-express'

const options = {
  definition: {
    openapi: '3.0.0',                   // OpenAPI specification version
    info: {
      title: 'TaskFlow API',            // Main API title shown in documentation
      version: '1.0.0',                 // API version for client compatibility tracking
      description: 'A comprehensive task management application API built with Express.js and TypeScript',
      contact: {                        // Support contact information
        name: 'TaskFlow Support',
        email: 'support@taskflow.com'
      },
      license: {                        // API license information
        name: 'MIT',
        url: 'https://opensource.org/licenses/MIT'
      }
    },
    servers: [                          // Available API server environments
      {
        url: process.env.API_URL || 'http://localhost:3000/api/v1',  // Dynamic server URL
        description: 'Development server'
      }
    ],
    components: {                       // Reusable OpenAPI components
      securitySchemes: {                // Authentication methods
        bearerAuth: {
          type: 'http',                 // HTTP authentication
          scheme: 'bearer',             // Bearer token scheme
          bearerFormat: 'JWT',          // JWT token format
          description: 'Enter JWT token obtained from login endpoint'
        }
      },
      responses: {                      // Common response schemas for reuse
        UnauthorizedError: {            // 401 Unauthorized response schema
          description: 'Authentication information is missing or invalid',
          content: {
            'application/json': {
              schema: {
                type: 'object',
                properties: {
                  success: { type: 'boolean', example: false },
                  error: { type: 'string', example: 'Unauthorized' }
                }
              }
            }
          }
        },
        ValidationError: {              // 400 Validation Error response schema
          description: 'Request validation failed',
          content: {
            'application/json': {
              schema: {
                type: 'object',
                properties: {
                  success: { type: 'boolean', example: false },
                  error: { type: 'string', example: 'Validation failed' },
                  details: {            // Array of specific field validation errors
                    type: 'array',
                    items: {
                      type: 'object',
                      properties: {
                        field: { type: 'string' },    // Field name that failed validation
                        message: { type: 'string' }   // Human-readable error message
                      }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  },
  // File paths to scan for @openapi documentation comments
  apis: [
    './src/routes/*.ts',      // Route definitions with endpoint documentation
    './src/controllers/*.ts'  // Controller files with additional schema documentation
  ]
}

// Generate complete OpenAPI specification from config and comments
const specs = swaggerJsdoc(options)

export { specs, swaggerUi }
```

---

## Viewing Your API Documentation

Once you've set up Swagger and started your server, you can view your API documentation by visiting:

**http://localhost:3000/api-docs**

**What you'll see on the documentation page:**

1. **API Overview** - Title, description, version, and contact information
2. **Server Information** - Available servers and base URLs
3. **Authentication** - JWT bearer token configuration with "Authorize" button
4. **Endpoint Groups** - Organized by tags (Authentication, Users, etc.)
5. **Interactive Testing** - Each endpoint has a "Try it out" button
6. **Request/Response Examples** - Sample data for testing
7. **Schema Definitions** - Data models and validation rules

**How to test endpoints:**
1. Click "Authorize" and enter your JWT token (from login response)
2. Navigate to any endpoint and click "Try it out"
3. Fill in required parameters and request body
4. Click "Execute" to make a real API call
5. View the response directly in the documentation

---

## Testing Your API Implementation

### Frontend Integration Testing (Recommended)

**Using Module 3 Forms to Test Your API**

Instead of just using curl commands, you can test your API endpoints using the actual frontend forms you built in Module 3. This provides a more realistic testing experience and validates the complete user workflow.

**Prerequisites:**
- Completed Module 3 (Vue.js forms with validation)
- Backend API server running on `http://localhost:3000`
- Frontend development server running on `http://localhost:5173`

### Step 1: Update Frontend API Configuration

In your Vue.js project from Module 3, create or update an API configuration file:

```typescript
// src/utils/api.ts
const API_BASE_URL = 'http://localhost:3000/api/v1'

// API client with error handling
export const apiClient = {
  async post(endpoint: string, data: any) {
    try {
      const response = await fetch(`${API_BASE_URL}${endpoint}`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(data)
      })
      
      const result = await response.json()
      
      if (!response.ok) {
        throw new Error(result.error || 'API request failed')
      }
      
      return result
    } catch (error) {
      console.error('API Error:', error)
      throw error
    }
  },

  async get(endpoint: string, token?: string) {
    try {
      const headers: any = {
        'Content-Type': 'application/json',
      }
      
      if (token) {
        headers.Authorization = `Bearer ${token}`
      }
      
      const response = await fetch(`${API_BASE_URL}${endpoint}`, {
        method: 'GET',
        headers
      })
      
      const result = await response.json()
      
      if (!response.ok) {
        throw new Error(result.error || 'API request failed')
      }
      
      return result
    } catch (error) {
      console.error('API Error:', error)
      throw error
    }
  }
}
```

### Step 2: Update Registration Form to Call Your API

Modify your registration form component from Module 3:

```vue
<!-- src/components/RegistrationForm.vue -->
<script setup lang="ts">
import { ref } from 'vue'
import { apiClient } from '@/utils/api'

// ... existing form validation code ...

const isSubmitting = ref(false)
const submitError = ref('')
const submitSuccess = ref('')

const handleSubmit = async () => {
  if (!isFormValid.value || isSubmitting.value) return
  
  isSubmitting.value = true
  submitError.value = ''
  submitSuccess.value = ''
  
  try {
    const response = await apiClient.post('/auth/register', {
      firstName: formData.value.firstName,
      lastName: formData.value.lastName,
      email: formData.value.email,
      password: formData.value.password
    })
    
    console.log('Registration successful:', response)
    submitSuccess.value = 'Registration successful! You can now log in.'
    
    // Store token for future requests
    localStorage.setItem('authToken', response.data.token)
    
    // Reset form
    resetForm()
    
  } catch (error: any) {
    submitError.value = error.message || 'Registration failed'
    console.error('Registration error:', error)
  } finally {
    isSubmitting.value = false
  }
}
</script>

<template>
  <!-- ... existing form template ... -->
  
  <!-- Add success/error messages -->
  <div v-if="submitSuccess" class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded">
    {{ submitSuccess }}
  </div>
  
  <div v-if="submitError" class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded">
    {{ submitError }}
  </div>
  
  <!-- Update submit button -->
  <button 
    @click="handleSubmit"
    :disabled="!isFormValid || isSubmitting"
    :class="{
      'opacity-50 cursor-not-allowed': !isFormValid || isSubmitting,
      'bg-blue-600 hover:bg-blue-700': isFormValid && !isSubmitting
    }"
    class="w-full text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline transition-colors"
  >
    {{ isSubmitting ? 'Registering...' : 'Register' }}
  </button>
</template>
```

### Step 3: Update Login Form to Call Your API

Modify your login form component:

```vue
<!-- src/components/LoginForm.vue -->
<script setup lang="ts">
import { ref } from 'vue'
import { apiClient } from '@/utils/api'

// ... existing form validation code ...

const isSubmitting = ref(false)
const submitError = ref('')
const submitSuccess = ref('')

const handleSubmit = async () => {
  if (!isFormValid.value || isSubmitting.value) return
  
  isSubmitting.value = true
  submitError.value = ''
  submitSuccess.value = ''
  
  try {
    const response = await apiClient.post('/auth/login', {
      email: formData.value.email,
      password: formData.value.password
    })
    
    console.log('Login successful:', response)
    submitSuccess.value = `Welcome back, ${response.data.user.firstName}!`
    
    // Store token for future requests
    localStorage.setItem('authToken', response.data.token)
    localStorage.setItem('userData', JSON.stringify(response.data.user))
    
  } catch (error: any) {
    submitError.value = error.message || 'Login failed'
    console.error('Login error:', error)
  } finally {
    isSubmitting.value = false
  }
}
</script>

<!-- Similar template updates as registration form -->
```

### Step 4: Test API Rate Limiting

Create a simple test component to verify rate limiting:

```vue
<!-- src/components/RateLimitTest.vue -->
<template>
  <div class="p-6 max-w-md mx-auto bg-white rounded-xl shadow-md">
    <h2 class="text-xl font-bold mb-4">Rate Limit Test</h2>
    <p class="mb-4 text-sm text-gray-600">
      Click the button rapidly to test the rate limiting (max 5 requests per 15 minutes)
    </p>
    
    <button 
      @click="testRateLimit"
      :disabled="isLoading"
      class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded mb-4"
    >
      {{ isLoading ? 'Testing...' : 'Test Rate Limit' }}
    </button>
    
    <div class="space-y-2">
      <div v-for="(result, index) in results" :key="index" 
           :class="{
             'text-red-600': result.includes('rate limit') || result.includes('error'),
             'text-green-600': result.includes('success'),
             'text-blue-600': !result.includes('error') && !result.includes('success')
           }"
           class="text-sm">
        {{ index + 1 }}. {{ result }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { apiClient } from '@/utils/api'

const isLoading = ref(false)
const results = ref<string[]>([])

const testRateLimit = async () => {
  isLoading.value = true
  results.value = []
  
  // Make 6 rapid requests to trigger rate limiting
  for (let i = 0; i < 6; i++) {
    try {
      await apiClient.post('/auth/login', {
        email: 'test@invalid.com',
        password: 'invalid'
      })
      results.value.push(`Request ${i + 1}: Unexpected success`)
    } catch (error: any) {
      if (error.message.includes('rate limit') || error.message.includes('Too many')) {
        results.value.push(`Request ${i + 1}: Rate limited ✓`)
      } else {
        results.value.push(`Request ${i + 1}: ${error.message}`)
      }
    }
    
    // Small delay to show progression
    await new Promise(resolve => setTimeout(resolve, 100))
  }
  
  isLoading.value = false
}
</script>
```

### Step 5: Frontend Testing Workflow

**Complete testing process using your Module 3 frontend:**

1. **Start both servers:**
   ```bash
   # Terminal 1 - Backend (Module 4)
   cd server/
   npm run dev
   
   # Terminal 2 - Frontend (Module 3)
   cd client/  # or your Vue project directory
   npm run dev
   ```

2. **Test Registration Flow:**
   - Open `http://localhost:5173` (your Vue frontend)
   - Navigate to registration form
   - Fill out the form with valid data
   - Submit and verify success message
   - Check browser Network tab to see API calls
   - Verify JWT token is stored in localStorage

3. **Test Login Flow:**
   - Use the same credentials from registration
   - Verify login success and user data storage
   - Test invalid credentials to see error handling

4. **Test Validation:**
   - Try submitting forms with invalid data
   - Verify frontend validation works
   - Submit forms that pass frontend but fail backend validation
   - Confirm error messages display properly

5. **Test Rate Limiting:**
   - Use the RateLimitTest component
   - Verify rate limiting kicks in after 5 attempts
   - Wait 15 minutes and test that limit resets

### Benefits of Frontend Testing

**Advantages over curl testing:**
- **Real user experience** - Test actual user workflows
- **Visual feedback** - See errors and success states in the UI
- **Form validation integration** - Test frontend and backend validation together
- **Token management** - Test JWT storage and usage
- **Error handling** - Verify error messages display correctly to users
- **Rate limiting visibility** - See how rate limiting affects real users

**What to verify:**
- [ ] Registration form successfully creates users
- [ ] Login form authenticates and stores tokens
- [ ] Form validation works on both frontend and backend
- [ ] Error messages are user-friendly and actionable
- [ ] Rate limiting prevents abuse while allowing normal usage
- [ ] Success states provide appropriate feedback
- [ ] Network requests match API documentation

This approach gives you confidence that your APIs work correctly with real frontend applications, not just isolated curl commands.

---

### Manual Testing Commands (Alternative Method)

**Create a test file:** `test-api.sh`

```bash
#!/bin/bash

# Colors for output
GREEN='\033[0;32m'
RED='\033[0;31m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

BASE_URL="http://localhost:3000/api/v1"

echo -e "${BLUE}=== TaskFlow API Testing Script ===${NC}\n"

# Test 1: Health Check
echo -e "${BLUE}1. Testing Health Check${NC}"
curl -s "$BASE_URL/health" | jq .
echo -e "\n"

# Test 2: Register User
echo -e "${BLUE}2. Testing User Registration${NC}"
REGISTER_RESPONSE=$(curl -s -X POST "$BASE_URL/auth/register" \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "Test",
    "lastName": "User",
    "email": "test@example.com",
    "password": "Password123"
  }')

echo "$REGISTER_RESPONSE" | jq .

# Extract token for subsequent requests
TOKEN=$(echo "$REGISTER_RESPONSE" | jq -r '.data.token // empty')
echo -e "\n"

# Test 3: Login
echo -e "${BLUE}3. Testing User Login${NC}"
LOGIN_RESPONSE=$(curl -s -X POST "$BASE_URL/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test@example.com",
    "password": "Password123"
  }')

echo "$LOGIN_RESPONSE" | jq .

# Update token if login was successful
NEW_TOKEN=$(echo "$LOGIN_RESPONSE" | jq -r '.data.token // empty')
if [ ! -z "$NEW_TOKEN" ]; then
  TOKEN="$NEW_TOKEN"
fi
echo -e "\n"

# Test 4: Get User Profile (requires authentication)
if [ ! -z "$TOKEN" ]; then
  echo -e "${BLUE}4. Testing Get User Profile${NC}"
  curl -s -X GET "$BASE_URL/users/profile" \
    -H "Authorization: Bearer $TOKEN" \
    -H "user-id: $(echo "$REGISTER_RESPONSE" | jq -r '.data.user.id')" | jq .
  echo -e "\n"

  # Test 5: Update User Profile
  echo -e "${BLUE}5. Testing Update User Profile${NC}"
  curl -s -X PUT "$BASE_URL/users/profile" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $TOKEN" \
    -H "user-id: $(echo "$REGISTER_RESPONSE" | jq -r '.data.user.id')" \
    -d '{
      "firstName": "Updated",
      "lastName": "Name"
    }' | jq .
  echo -e "\n"
fi

# Test 6: Rate Limiting
echo -e "${BLUE}6. Testing Rate Limiting (5 rapid requests)${NC}"
for i in {1..6}; do
  echo "Request $i:"
  curl -s -X POST "$BASE_URL/auth/login" \
    -H "Content-Type: application/json" \
    -d '{"email":"invalid","password":"invalid"}' | jq -r '.error // .message'
done
echo -e "\n"

# Test 7: Invalid Requests
echo -e "${BLUE}7. Testing Validation Errors${NC}"
curl -s -X POST "$BASE_URL/auth/register" \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "A",
    "email": "invalid-email",
    "password": "weak"
  }' | jq .

echo -e "\n${GREEN}Testing completed!${NC}"
echo -e "${BLUE}View API documentation at: http://localhost:3000/api-docs${NC}"
```

**Make the script executable and run it:**
```bash
chmod +x test-api.sh
./test-api.sh
```

### Quick Individual Tests

**Test registration:**
```bash
curl -X POST http://localhost:3000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "John",
    "lastName": "Doe",
    "email": "john@example.com",
    "password": "Password123"
  }' | jq .
```

**Test login and save token:**
```bash
TOKEN=$(curl -s -X POST http://localhost:3000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "Password123"
  }' | jq -r '.data.token')

echo "Token: $TOKEN"
```

**Test protected endpoint:**
```bash
curl -X GET http://localhost:3000/api/v1/users/profile \
  -H "Authorization: Bearer $TOKEN" \
  -H "user-id: YOUR_USER_ID" | jq .
```

### Development Testing Workflow

**For each new endpoint you create:**

1. **Manual curl test** - Verify basic functionality
2. **Swagger documentation test** - Use the interactive docs
3. **Error case testing** - Test invalid inputs and edge cases
4. **Rate limiting verification** - Ensure limits work properly
5. **Authentication testing** - Verify protected endpoints require auth

**Testing checklist for each task:**
- [ ] Endpoint responds with correct HTTP status codes
- [ ] Response format matches API standards
- [ ] Validation errors are user-friendly
- [ ] Rate limiting works as expected
- [ ] Authentication is enforced where required
- [ ] Swagger documentation is accurate and testable

**Acceptance criteria:**
- [ ] User CRUD endpoints implemented and tested
- [ ] Swagger documentation accessible at /api-docs
- [ ] Interactive API testing works in Swagger UI
- [ ] Manual testing script passes all tests
- [ ] Rate limiting and validation working correctly
- [ ] Consistent error response format across all endpoints

## Challenge Extensions

### Advanced Security Features
- Implement refresh token rotation
- Add account verification via email
- Create password reset functionality
- Implement account lockout after failed attempts

### Enhanced Validation
- Add custom validation rules
- Implement async validation (database checks)
- Create validation middleware composition
- Add request sanitization

### Performance Optimization
- Implement response caching
- Add request compression
- Create database query optimization
- Set up API monitoring

### Advanced Error Handling
- Implement error reporting/tracking
- Add request correlation IDs
- Create detailed error logging
- Set up error alerting

## Troubleshooting Common Issues

### TypeScript Compilation Errors
- Check tsconfig.json configuration
- Verify all types are properly imported
- Ensure proper interface definitions
- Check for missing type declarations

### Middleware Not Working
- Verify middleware order in app.ts
- Check middleware function signatures
- Ensure proper error handling in middleware
- Test middleware isolation

### Authentication Issues
- Verify JWT secret is set in environment
- Check token format and expiration
- Ensure proper header format
- Test password hashing/comparison

### Validation Errors
- Check Zod schema syntax
- Verify request body structure
- Test validation with different inputs
- Check error message formatting

## Module Completion Checklist

Before moving to Module 5, ensure:
- [ ] Express server runs without errors
- [ ] All authentication endpoints work
- [ ] Request validation functions properly
- [ ] Error handling provides good responses
- [ ] API documentation is accessible
- [ ] Rate limiting is implemented
- [ ] TypeScript compilation succeeds
- [ ] All tests pass (if implemented)

## Next Module Preview
Module 5 will focus on integrating PostgreSQL database with Drizzle ORM. You'll replace the mock data storage with a real database, create schemas, and implement proper data persistence for your API endpoints.