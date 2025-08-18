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

**Authentication controller implementation:**
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
      
      // Check if user exists (this will be replaced with database check)
      // For now, simulate with in-memory storage or file
      
      // Hash password
      const saltRounds = 12
      const hashedPassword = await bcrypt.hash(password, saltRounds)
      
      // Create user (mock for now)
      const user = {
        id: Date.now().toString(),
        firstName,
        lastName,
        email,
        password: hashedPassword,
        createdAt: new Date()
      }
      
      // Generate token
      const token = jwt.sign(
        { userId: user.id, email: user.email },
        process.env.JWT_SECRET!,
        { expiresIn: '7d' }
      )
      
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
      
      // Find user (mock for now)
      // This will be replaced with database query
      
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
    // For JWT, logout is handled client-side by removing token
    // Could implement token blacklisting here for enhanced security
    res.json({
      success: true,
      message: 'Logged out successfully'
    })
  }
}
```

**Rate limiting setup:**
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
```

**Acceptance criteria:**
- [ ] Registration endpoint creates users
- [ ] Login endpoint validates credentials
- [ ] Passwords properly hashed
- [ ] JWT tokens generated correctly
- [ ] Rate limiting implemented
- [ ] Proper error responses

---

### Task 4.5: CRUD Endpoints and API Documentation
**Objective:** Create basic CRUD endpoints and document the API

**What you need to accomplish:**
- Implement user profile endpoints
- Create basic project/task endpoints (mock data)
- Set up API documentation
- Add request/response logging

**Documentation to consult:**
- [OpenAPI Specification](https://swagger.io/specification/)
- [Swagger/OpenAPI with Express](https://swagger.io/docs/specification/about/)
- [API Documentation Best Practices](https://swagger.io/blog/api-documentation/api-documentation-best-practices/)

**User endpoints to implement:**
```typescript
// controllers/userController.ts
export const userController = {
  async getProfile(req: Request, res: Response, next: NextFunction) {
    try {
      // Get user from auth middleware
      const userId = req.user.id
      
      // Fetch user data (mock for now)
      const user = await getUserById(userId)
      
      res.json({
        success: true,
        data: {
          user: {
            id: user.id,
            firstName: user.firstName,
            lastName: user.lastName,
            email: user.email,
            createdAt: user.createdAt
          }
        }
      })
    } catch (error) {
      next(error)
    }
  },

  async updateProfile(req: Request, res: Response, next: NextFunction) {
    try {
      const userId = req.user.id
      const updates = req.validatedData.body
      
      // Update user (mock for now)
      const updatedUser = await updateUser(userId, updates)
      
      res.json({
        success: true,
        data: { user: updatedUser }
      })
    } catch (error) {
      next(error)
    }
  }
}
```

**API documentation setup:**
```typescript
// docs/swagger.ts
import swaggerJsdoc from 'swagger-jsdoc'
import swaggerUi from 'swagger-ui-express'

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'Task Manager API',
      version: '1.0.0',
      description: 'A task management application API'
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
          bearerFormat: 'JWT'
        }
      }
    }
  },
  apis: ['./src/routes/*.ts', './src/controllers/*.ts']
}

const specs = swaggerJsdoc(options)

export { specs, swaggerUi }
```

**Route documentation example:**
```typescript
/**
 * @openapi
 * /auth/register:
 *   post:
 *     summary: Register a new user
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
 *               lastName:
 *                 type: string
 *               email:
 *                 type: string
 *                 format: email
 *               password:
 *                 type: string
 *                 minLength: 8
 *     responses:
 *       201:
 *         description: User registered successfully
 *       400:
 *         description: Validation error
 */
```

**Acceptance criteria:**
- [ ] User CRUD endpoints implemented
- [ ] Mock project/task endpoints created
- [ ] API documentation accessible
- [ ] Request/response logging working
- [ ] Consistent response format

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