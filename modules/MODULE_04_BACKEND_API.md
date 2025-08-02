# Module 4: Backend API Development

**Duration:** 4-5 days  
**Branch:** `feature/module-4-backend-api`

## Learning Objectives
- Build RESTful APIs with Express.js and TypeScript
- Implement proper middleware architecture
- Create robust error handling and logging
- Design API route structure and documentation
- Implement request validation and security measures

## Overview
This module focuses on creating a professional backend API using Express.js. You'll build endpoints that support your frontend forms, implement proper error handling, and establish patterns that will scale as your application grows.

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

**Basic server structure to create:**
```typescript
// server/src/app.ts
import express from 'express'
import cors from 'cors'
import helmet from 'helmet'
import morgan from 'morgan'
import compression from 'compression'

const app = express()

// Security middleware
app.use(helmet())
app.use(cors({
  origin: process.env.CLIENT_URL || 'http://localhost:5173',
  credentials: true
}))

// Utility middleware
app.use(compression())
app.use(morgan('combined'))
app.use(express.json({ limit: '10mb' }))
app.use(express.urlencoded({ extended: true }))

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date().toISOString() })
})

export default app
```

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

**Example route implementation:**
```typescript
// routes/auth.ts
import { Router } from 'express'
import { authController } from '../controllers/authController'
import { validateRequest } from '../middleware/validation'
import { registerSchema, loginSchema } from '../schemas/auth'

const router = Router()

router.post('/register', 
  validateRequest(registerSchema),
  authController.register
)

router.post('/login',
  validateRequest(loginSchema), 
  authController.login
)

router.post('/logout',
  authController.logout
)

export default router
```

**API versioning setup:**
```typescript
// app.ts
import authRoutes from './routes/auth'
import userRoutes from './routes/users'

// API v1 routes
app.use('/api/v1/auth', authRoutes)
app.use('/api/v1/users', userRoutes)
```

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

**Validation middleware to create:**
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

**Error handling middleware:**
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

  // Log error
  console.error(err)

  // Mongoose bad ObjectId
  if (err.name === 'CastError') {
    const message = 'Resource not found'
    error = new AppError(message, 404)
  }

  // Duplicate key error
  if (err.code === 11000) {
    const message = 'Duplicate field value entered'
    error = new AppError(message, 400)
  }

  res.status(error.statusCode || 500).json({
    success: false,
    error: error.message || 'Server Error'
  })
}
```

**Schema definitions to create:**
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