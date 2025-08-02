# Module 10: Production Deployment & Optimization

**Duration:** 3-4 days  
**Branch:** `feature/module-10-deployment`  
**Section:** 🚀 **ADVANCED**

## Learning Objectives
- Deploy Nuxt application to Vercel with optimal configuration
- Implement advanced performance optimizations
- Set up monitoring, analytics, and error tracking
- Configure SEO optimization and meta management
- Establish production maintenance and monitoring workflows
- Implement security best practices for production

## Overview
This final module transforms your application into a production-ready solution. You'll deploy to Vercel, implement performance optimizations, set up monitoring systems, and establish processes for maintaining a production application.

## Tasks

### Task 10.1: Production Environment Configuration
**Objective:** Configure application for production deployment with proper environment management

**What you need to accomplish:**
- Configure production environment variables
- Set up database for production
- Optimize build configuration for production
- Configure security settings

**Documentation to consult:**
- [Nuxt 3 Deployment](https://nuxt.com/docs/getting-started/deployment)
- [Vercel Documentation](https://vercel.com/docs)
- [Environment Variables Best Practices](https://12factor.net/config)

**Production environment setup:**
```bash
# .env.production
# Database
DATABASE_URL="postgresql://prod_user:prod_password@prod_host:5432/prod_db?sslmode=require"

# Authentication
BETTER_AUTH_SECRET="super-secure-production-secret-key-min-32-chars"
BETTER_AUTH_URL="https://your-app.vercel.app"

# OAuth Providers
GOOGLE_CLIENT_ID="your-google-client-id"
GOOGLE_CLIENT_SECRET="your-google-client-secret"
GITHUB_CLIENT_ID="your-github-client-id"
GITHUB_CLIENT_SECRET="your-github-client-secret"

# Email Service
SMTP_HOST="smtp.sendgrid.net"
SMTP_PORT="587"
SMTP_USER="apikey"
SMTP_PASSWORD="your-sendgrid-api-key"

# File Storage
CLOUDINARY_CLOUD_NAME="your-cloud-name"
CLOUDINARY_API_KEY="your-api-key"
CLOUDINARY_API_SECRET="your-api-secret"

# Monitoring
SENTRY_DSN="your-sentry-dsn"
ANALYTICS_ID="your-analytics-id"

# Security
CORS_ORIGIN="https://your-app.vercel.app"
RATE_LIMIT_MAX="100"
RATE_LIMIT_WINDOW="900000"
```

**Production Nuxt configuration:**
```typescript
// nuxt.config.ts (production optimized)
export default defineNuxtConfig({
  // Development vs Production
  devtools: { enabled: process.env.NODE_ENV === 'development' },
  
  modules: [
    '@nuxtjs/tailwindcss',
    '@pinia/nuxt',
    '@better-auth/nuxt',
    '@nuxtjs/pwa',
    '@nuxtjs/seo',
    '@sentry/nuxt/module'
  ],

  // Runtime configuration
  runtimeConfig: {
    // Private (server-side only)
    databaseUrl: process.env.DATABASE_URL,
    betterAuthSecret: process.env.BETTER_AUTH_SECRET,
    googleClientSecret: process.env.GOOGLE_CLIENT_SECRET,
    githubClientSecret: process.env.GITHUB_CLIENT_SECRET,
    smtpPassword: process.env.SMTP_PASSWORD,
    cloudinaryApiSecret: process.env.CLOUDINARY_API_SECRET,
    sentryDsn: process.env.SENTRY_DSN,
    
    // Public (client-side accessible)
    public: {
      betterAuthUrl: process.env.BETTER_AUTH_URL,
      googleClientId: process.env.GOOGLE_CLIENT_ID,
      githubClientId: process.env.GITHUB_CLIENT_ID,
      cloudinaryCloudName: process.env.CLOUDINARY_CLOUD_NAME,
      analyticsId: process.env.ANALYTICS_ID,
      appUrl: process.env.BETTER_AUTH_URL || 'http://localhost:3000'
    }
  },

  // SEO configuration
  seo: {
    redirectToCanonicalSiteUrl: true
  },

  // PWA configuration
  pwa: {
    workbox: {
      enabled: true,
      mode: 'production'
    }
  },

  // Performance optimizations
  experimental: {
    payloadExtraction: false,
    renderJsonPayloads: true,
    typedPages: true
  },

  // Nitro (server) configuration
  nitro: {
    minify: true,
    compressPublicAssets: true,
    
    // Database connection pooling
    experimental: {
      wasm: true
    },
    
    // Production optimizations
    storage: {
      redis: {
        driver: 'redis',
        // Configure Redis for session storage in production
      }
    }
  },

  // Build optimizations
  build: {
    transpile: process.env.NODE_ENV === 'production' ? ['@headlessui/vue'] : []
  },

  // CSS optimization
  css: ['~/assets/css/main.css'],
  
  // Security headers
  routeRules: {
    '/**': {
      headers: {
        'X-Frame-Options': 'DENY',
        'X-Content-Type-Options': 'nosniff',
        'Referrer-Policy': 'strict-origin-when-cross-origin',
        'Permissions-Policy': 'camera=(), microphone=(), geolocation=()'
      }
    },
    // Cache static assets
    '/uploads/**': {
      headers: {
        'Cache-Control': 'public, max-age=31536000, immutable'
      }
    }
  }
})
```

**Database production setup:**
```typescript
// server/database/connection.ts (production)
import { drizzle } from 'drizzle-orm/postgres-js'
import postgres from 'postgres'

let connection: postgres.Sql | null = null
let db: ReturnType<typeof drizzle> | null = null

export function getDatabase() {
  if (!db) {
    const config = useRuntimeConfig()
    
    if (!connection) {
      // Production connection with SSL and pooling
      connection = postgres(config.databaseUrl, {
        max: 20, // Maximum connections
        idle_timeout: 20,
        connect_timeout: 60,
        ssl: process.env.NODE_ENV === 'production' ? 'require' : false,
        
        // Connection retry logic
        max_lifetime: 60 * 30, // 30 minutes
        prepare: false // Disable prepared statements for serverless
      })
    }
    
    db = drizzle(connection, {
      logger: process.env.NODE_ENV === 'development'
    })
  }
  
  return db
}

// Graceful shutdown for production
export async function closeDatabase() {
  if (connection) {
    await connection.end()
    connection = null
    db = null
  }
}

// Health check for production monitoring
export async function checkDatabaseHealth() {
  try {
    const db = getDatabase()
    await db.execute('SELECT 1')
    return { status: 'healthy', timestamp: new Date() }
  } catch (error) {
    return { 
      status: 'unhealthy', 
      error: error.message, 
      timestamp: new Date() 
    }
  }
}
```

**Acceptance criteria:**
- [ ] Production environment variables configured
- [ ] Database setup for production
- [ ] Security headers implemented
- [ ] Build optimizations applied
- [ ] Configuration validated

---

### Task 10.2: Vercel Deployment Configuration
**Objective:** Deploy application to Vercel with optimal configuration

**What you need to accomplish:**
- Configure Vercel project settings
- Set up custom domain and SSL
- Configure build and deployment settings
- Set up environment variables in Vercel

**Documentation to consult:**
- [Vercel Nuxt Deployment](https://vercel.com/docs/frameworks/nuxt)
- [Vercel Environment Variables](https://vercel.com/docs/concepts/projects/environment-variables)
- [Custom Domains](https://vercel.com/docs/concepts/projects/domains)

**Vercel configuration:**
```json
// vercel.json
{
  "version": 2,
  "builds": [
    {
      "src": "nuxt.config.ts",
      "use": "@nuxtjs/vercel-builder",
      "config": {
        "serverFiles": ["server/**"]
      }
    }
  ],
  "routes": [
    {
      "src": "/uploads/(.*)",
      "headers": {
        "Cache-Control": "public, max-age=31536000, immutable"
      }
    }
  ],
  "functions": {
    "server/api/**/*.ts": {
      "maxDuration": 30
    }
  },
  "crons": [
    {
      "path": "/api/cleanup",
      "schedule": "0 2 * * *"
    }
  ]
}
```

**Deployment script:**
```json
// package.json
{
  "scripts": {
    "build": "nuxt build",
    "generate": "nuxt generate",
    "preview": "nuxt preview",
    "deploy": "vercel --prod",
    "deploy:preview": "vercel",
    "postbuild": "npm run db:migrate:prod"
  }
}
```

**GitHub Actions for CI/CD:**
```yaml
# .github/workflows/deploy.yml
name: Deploy to Vercel

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      
      - run: npm run test:unit
      
      - run: npm run test:e2e
        env:
          CI: true
      
      - run: npm run test:lighthouse
        env:
          LHCI_GITHUB_APP_TOKEN: ${{ secrets.LHCI_GITHUB_APP_TOKEN }}

  deploy-preview:
    runs-on: ubuntu-latest
    needs: test
    if: github.event_name == 'pull_request'
    steps:
      - uses: actions/checkout@v4
      
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      
      - uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}

  deploy-production:
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      
      - uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      
      - run: npm run build
      
      - uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
```

**Custom domain setup:**
```bash
# Using Vercel CLI
vercel domains add yourdomain.com
vercel domains add www.yourdomain.com

# Configure DNS records
# A record: @ -> 76.76.19.61
# CNAME record: www -> cname.vercel-dns.com
```

**Environment variables setup script:**
```bash
#!/bin/bash
# scripts/setup-vercel-env.sh

# Production environment variables
vercel env add DATABASE_URL production
vercel env add BETTER_AUTH_SECRET production
vercel env add GOOGLE_CLIENT_SECRET production
vercel env add GITHUB_CLIENT_SECRET production
vercel env add SMTP_PASSWORD production
vercel env add CLOUDINARY_API_SECRET production
vercel env add SENTRY_DSN production

# Preview environment variables
vercel env add DATABASE_URL preview
vercel env add BETTER_AUTH_SECRET preview
# ... etc for preview environment
```

**Acceptance criteria:**
- [ ] Vercel project configured and deployed
- [ ] Custom domain set up with SSL
- [ ] Environment variables configured
- [ ] CI/CD pipeline working
- [ ] Preview deployments functional

---

### Task 10.3: Performance Optimization Implementation
**Objective:** Implement advanced performance optimizations for production

**What you need to accomplish:**
- Optimize bundle size and code splitting
- Implement caching strategies
- Configure CDN and asset optimization
- Set up performance monitoring

**Documentation to consult:**
- [Nuxt Performance](https://nuxt.com/docs/guide/concepts/rendering#performance)
- [Web Performance Best Practices](https://web.dev/fast/)
- [Vercel Edge Functions](https://vercel.com/docs/concepts/functions/edge-functions)

**Bundle optimization:**
```typescript
// nuxt.config.ts (performance focused)
export default defineNuxtConfig({
  // Code splitting
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all',
        },
      },
    },
  },

  // Tree shaking
  build: {
    analyze: process.env.ANALYZE === 'true',
    extractCSS: true,
    optimization: {
      minimize: true
    }
  },

  // Lazy loading
  components: {
    global: true,
    dirs: [
      '~/components',
      {
        path: '~/components/lazy',
        lazy: true
      }
    ]
  },

  // Route-based optimization
  routeRules: {
    // Homepage pre-rendered at build time
    '/': { prerender: true },
    
    // Auth pages cached for 1 hour
    '/login': { isr: 3600 },
    '/register': { isr: 3600 },
    
    // Dashboard using SSR
    '/dashboard': { ssr: true },
    
    // API routes optimized
    '/api/**': { cors: true, headers: { 'cache-control': 'max-age=300' } },
    
    // Static assets
    '/uploads/**': { headers: { 'cache-control': 'max-age=31536000' } }
  }
})
```

**Image optimization service:**
```typescript
// server/api/images/optimize.post.ts
import sharp from 'sharp'

export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  const { imageUrl, width, height, quality = 80 } = body

  try {
    // Fetch original image
    const response = await fetch(imageUrl)
    const buffer = await response.arrayBuffer()

    // Optimize with Sharp
    const optimized = await sharp(Buffer.from(buffer))
      .resize(width, height, { 
        fit: 'cover',
        withoutEnlargement: true 
      })
      .webp({ quality })
      .toBuffer()

    return {
      success: true,
      data: {
        optimizedImage: `data:image/webp;base64,${optimized.toString('base64')}`,
        originalSize: buffer.byteLength,
        optimizedSize: optimized.length,
        savings: Math.round((1 - optimized.length / buffer.byteLength) * 100)
      }
    }
  } catch (error) {
    throw createError({
      statusCode: 500,
      statusMessage: 'Image optimization failed'
    })
  }
})
```

**Caching strategy implementation:**
```typescript
// server/utils/cache.ts
import { LRUCache } from 'lru-cache'

// In-memory cache for API responses
const cache = new LRUCache<string, any>({
  max: 500,
  ttl: 1000 * 60 * 5 // 5 minutes
})

export function getCached<T>(key: string): T | null {
  return cache.get(key) || null
}

export function setCache<T>(key: string, value: T, ttl?: number): void {
  cache.set(key, value, { ttl })
}

export function invalidateCache(pattern: string): void {
  for (const key of cache.keys()) {
    if (key.includes(pattern)) {
      cache.delete(key)
    }
  }
}

// Cache middleware
export function withCache<T>(
  key: string, 
  fetcher: () => Promise<T>,
  ttl: number = 300000 // 5 minutes
): Promise<T> {
  return new Promise(async (resolve, reject) => {
    // Try cache first
    const cached = getCached<T>(key)
    if (cached) {
      resolve(cached)
      return
    }

    try {
      const result = await fetcher()
      setCache(key, result, ttl)
      resolve(result)
    } catch (error) {
      reject(error)
    }
  })
}
```

**Performance monitoring composable:**
```typescript
// composables/usePerformanceMonitoring.ts
export const usePerformanceMonitoring = () => {
  const metrics = ref({
    navigationTiming: null,
    vitals: {
      lcp: null,
      fid: null,
      cls: null,
      fcp: null,
      ttfb: null
    },
    customMetrics: {}
  })

  const recordCustomMetric = (name: string, value: number) => {
    metrics.value.customMetrics[name] = value
    
    // Send to analytics
    if (process.client) {
      gtag('event', 'timing_complete', {
        name,
        value: Math.round(value)
      })
    }
  }

  const measureAsync = async <T>(
    name: string, 
    operation: () => Promise<T>
  ): Promise<T> => {
    const start = performance.now()
    
    try {
      const result = await operation()
      const duration = performance.now() - start
      recordCustomMetric(name, duration)
      return result
    } catch (error) {
      const duration = performance.now() - start
      recordCustomMetric(`${name}_error`, duration)
      throw error
    }
  }

  const initWebVitals = () => {
    if (process.client) {
      import('web-vitals').then(({ getCLS, getFID, getFCP, getLCP, getTTFB }) => {
        getCLS((metric) => {
          metrics.value.vitals.cls = metric.value
          reportToAnalytics('CLS', metric.value)
        })

        getFID((metric) => {
          metrics.value.vitals.fid = metric.value
          reportToAnalytics('FID', metric.value)
        })

        getFCP((metric) => {
          metrics.value.vitals.fcp = metric.value
          reportToAnalytics('FCP', metric.value)
        })

        getLCP((metric) => {
          metrics.value.vitals.lcp = metric.value
          reportToAnalytics('LCP', metric.value)
        })

        getTTFB((metric) => {
          metrics.value.vitals.ttfb = metric.value
          reportToAnalytics('TTFB', metric.value)
        })
      })
    }
  }

  const reportToAnalytics = (metricName: string, value: number) => {
    // Report to Google Analytics
    if (typeof gtag !== 'undefined') {
      gtag('event', 'web_vital', {
        metric_name: metricName,
        metric_value: Math.round(value),
        custom_parameter: 'web_vitals'
      })
    }
  }

  return {
    metrics: readonly(metrics),
    recordCustomMetric,
    measureAsync,
    initWebVitals
  }
}
```

**Lazy loading implementation:**
```vue
<!-- components/LazyImage.vue -->
<template>
  <div class="relative overflow-hidden" :style="containerStyle">
    <!-- Placeholder -->
    <div 
      v-if="!loaded && !error"
      class="absolute inset-0 bg-gray-200 animate-pulse"
      :class="placeholderClass"
    />
    
    <!-- Low quality placeholder -->
    <img
      v-if="lqip && !loaded"
      :src="lqip"
      :alt="alt"
      class="absolute inset-0 w-full h-full object-cover filter blur-sm"
    />
    
    <!-- Main image -->
    <img
      v-show="loaded"
      ref="imageRef"
      :src="optimizedSrc"
      :alt="alt"
      :loading="eager ? 'eager' : 'lazy'"
      :class="imageClass"
      @load="onLoad"
      @error="onError"
    />
    
    <!-- Error state -->
    <div v-if="error" class="absolute inset-0 flex items-center justify-center bg-gray-100">
      <span class="text-gray-400">Failed to load image</span>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Props {
  src: string
  alt: string
  width?: number
  height?: number
  lqip?: string // Low Quality Image Placeholder
  eager?: boolean
  class?: string
}

const props = defineProps<Props>()

const loaded = ref(false)
const error = ref(false)
const imageRef = ref<HTMLImageElement>()

const optimizedSrc = computed(() => {
  if (!props.src) return ''
  
  // Use Cloudinary or similar service for optimization
  const config = useRuntimeConfig()
  if (config.public.cloudinaryCloudName) {
    return `https://res.cloudinary.com/${config.public.cloudinaryCloudName}/image/fetch/w_${props.width || 800},h_${props.height || 600},c_fill,f_auto,q_auto/${encodeURIComponent(props.src)}`
  }
  
  return props.src
})

const containerStyle = computed(() => ({
  width: props.width ? `${props.width}px` : '100%',
  height: props.height ? `${props.height}px` : 'auto',
  aspectRatio: props.width && props.height ? `${props.width}/${props.height}` : undefined
}))

const imageClass = computed(() => [
  'w-full h-full object-cover transition-opacity duration-300',
  props.class,
  {
    'opacity-0': !loaded,
    'opacity-100': loaded
  }
])

const onLoad = () => {
  loaded.value = true
  error.value = false
}

const onError = () => {
  error.value = true
  loaded.value = false
}

// Intersection Observer for lazy loading
onMounted(() => {
  if (!props.eager && process.client && imageRef.value) {
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting) {
          // Trigger loading
          observer.disconnect()
        }
      },
      { threshold: 0.1 }
    )
    
    observer.observe(imageRef.value)
  }
})
</script>
```

**Acceptance criteria:**
- [ ] Bundle size optimized and analyzed
- [ ] Caching strategies implemented
- [ ] Image optimization working
- [ ] Performance monitoring in place
- [ ] Core Web Vitals measured and optimized

---

### Task 10.4: SEO and Meta Management
**Objective:** Implement comprehensive SEO optimization and meta tag management

**What you need to accomplish:**
- Configure dynamic meta tags and SEO
- Implement structured data markup
- Set up sitemap generation
- Configure robots.txt and SEO best practices

**Documentation to consult:**
- [Nuxt SEO Kit](https://nuxtseo.com/)
- [Google Search Console](https://search.google.com/search-console)
- [Schema.org Structured Data](https://schema.org/)

**SEO configuration:**
```bash
# Install SEO modules
npm install @nuxtjs/seo
```

```typescript
// nuxt.config.ts (SEO configuration)
export default defineNuxtConfig({
  modules: [
    '@nuxtjs/seo'
  ],

  site: {
    url: 'https://your-app.vercel.app',
    name: 'TaskManager Pro',
    description: 'The most intuitive task management application for teams and individuals',
    defaultLocale: 'en'
  },

  seo: {
    redirectToCanonicalSiteUrl: true
  },

  // Global meta configuration
  app: {
    head: {
      title: 'TaskManager Pro - Intuitive Task Management',
      meta: [
        { charset: 'utf-8' },
        { name: 'viewport', content: 'width=device-width, initial-scale=1' },
        { hid: 'description', name: 'description', content: 'Streamline your workflow with TaskManager Pro. Collaborate with your team, manage projects, and track progress with our intuitive task management platform.' },
        { property: 'og:type', content: 'website' },
        { property: 'og:title', content: 'TaskManager Pro - Intuitive Task Management' },
        { property: 'og:description', content: 'Streamline your workflow with TaskManager Pro. Collaborate with your team, manage projects, and track progress.' },
        { property: 'og:image', content: 'https://your-app.vercel.app/og-image.png' },
        { name: 'twitter:card', content: 'summary_large_image' },
        { name: 'twitter:title', content: 'TaskManager Pro - Intuitive Task Management' },
        { name: 'twitter:description', content: 'Streamline your workflow with TaskManager Pro.' },
        { name: 'twitter:image', content: 'https://your-app.vercel.app/og-image.png' }
      ],
      link: [
        { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' },
        { rel: 'apple-touch-icon', href: '/apple-touch-icon.png' },
        { rel: 'canonical', href: 'https://your-app.vercel.app' }
      ]
    }
  }
})
```

**Dynamic meta tags composable:**
```typescript
// composables/useSEO.ts
export const useSEO = () => {
  const setPageMeta = (options: {
    title: string
    description: string
    ogImage?: string
    canonical?: string
    noindex?: boolean
    structuredData?: any
  }) => {
    const config = useRuntimeConfig()
    const route = useRoute()
    
    const canonical = options.canonical || `${config.public.appUrl}${route.path}`
    const ogImage = options.ogImage || `${config.public.appUrl}/og-image.png`

    useSeoMeta({
      title: options.title,
      description: options.description,
      ogTitle: options.title,
      ogDescription: options.description,
      ogImage: ogImage,
      ogUrl: canonical,
      twitterCard: 'summary_large_image',
      twitterTitle: options.title,
      twitterDescription: options.description,
      twitterImage: ogImage
    })

    useHead({
      link: [
        { rel: 'canonical', href: canonical }
      ],
      meta: options.noindex ? [
        { name: 'robots', content: 'noindex, nofollow' }
      ] : []
    })

    // Add structured data
    if (options.structuredData) {
      useJsonld(options.structuredData)
    }
  }

  const setBreadcrumbs = (breadcrumbs: Array<{ name: string, url: string }>) => {
    const structuredData = {
      '@context': 'https://schema.org',
      '@type': 'BreadcrumbList',
      itemListElement: breadcrumbs.map((crumb, index) => ({
        '@type': 'ListItem',
        position: index + 1,
        name: crumb.name,
        item: crumb.url
      }))
    }

    useJsonld(structuredData)
  }

  return {
    setPageMeta,
    setBreadcrumbs
  }
}
```

**Page-specific SEO implementation:**
```vue
<!-- pages/projects/[id].vue -->
<template>
  <div>
    <ProjectDetail :project="project" />
  </div>
</template>

<script setup lang="ts">
const route = useRoute()
const { setPageMeta, setBreadcrumbs } = useSEO()

// Fetch project data
const { data: project } = await $fetch(`/api/projects/${route.params.id}`)

if (!project) {
  throw createError({
    statusCode: 404,
    statusMessage: 'Project not found'
  })
}

// Set SEO meta tags
setPageMeta({
  title: `${project.name} - TaskManager Pro`,
  description: `Manage tasks and collaborate on ${project.name}. View project progress, assign tasks, and track milestones.`,
  canonical: `/projects/${project.id}`,
  structuredData: {
    '@context': 'https://schema.org',
    '@type': 'Project',
    name: project.name,
    description: project.description,
    creator: {
      '@type': 'Person',
      name: project.owner.name
    },
    dateCreated: project.createdAt,
    dateModified: project.updatedAt
  }
})

// Set breadcrumbs
setBreadcrumbs([
  { name: 'Home', url: '/' },
  { name: 'Projects', url: '/projects' },
  { name: project.name, url: `/projects/${project.id}` }
])
</script>
```

**Sitemap generation:**
```typescript
// server/api/sitemap.xml.ts
export default defineEventHandler(async (event) => {
  const config = useRuntimeConfig()
  const baseUrl = config.public.appUrl

  // Static pages
  const staticPages = [
    '',
    '/login',
    '/register',
    '/pricing',
    '/features',
    '/about',
    '/contact'
  ]

  // Dynamic pages (projects, etc.)
  const projects = await projectOps.getPublicProjects()
  const dynamicPages = projects.map(project => `/projects/${project.id}`)

  const allPages = [...staticPages, ...dynamicPages]

  const sitemap = `<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
${allPages.map(page => `
  <url>
    <loc>${baseUrl}${page}</loc>
    <lastmod>${new Date().toISOString()}</lastmod>
    <changefreq>weekly</changefreq>
    <priority>${page === '' ? '1.0' : '0.8'}</priority>
  </url>`).join('')}
</urlset>`

  setHeader(event, 'content-type', 'application/xml')
  return sitemap
})
```

**Robots.txt configuration:**
```typescript
// server/api/robots.txt.ts
export default defineEventHandler((event) => {
  const config = useRuntimeConfig()
  
  const robots = `User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/
Disallow: /login
Disallow: /register

Sitemap: ${config.public.appUrl}/sitemap.xml`

  setHeader(event, 'content-type', 'text/plain')
  return robots
})
```

**Open Graph image generation:**
```typescript
// server/api/og-image.get.ts
import sharp from 'sharp'

export default defineEventHandler(async (event) => {
  const query = getQuery(event)
  const title = query.title as string || 'TaskManager Pro'
  const description = query.description as string || 'Intuitive Task Management'

  try {
    // Create OG image with Sharp
    const svg = `
      <svg width="1200" height="630" xmlns="http://www.w3.org/2000/svg">
        <defs>
          <linearGradient id="bg" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" style="stop-color:#3b82f6"/>
            <stop offset="100%" style="stop-color:#1d4ed8"/>
          </linearGradient>
        </defs>
        <rect width="100%" height="100%" fill="url(#bg)"/>
        <text x="60" y="200" font-family="Arial, sans-serif" font-size="64" font-weight="bold" fill="white">
          ${title}
        </text>
        <text x="60" y="280" font-family="Arial, sans-serif" font-size="32" fill="rgba(255,255,255,0.8)">
          ${description}
        </text>
        <text x="60" y="550" font-family="Arial, sans-serif" font-size="24" fill="rgba(255,255,255,0.6)">
          taskmanager.pro
        </text>
      </svg>
    `

    const buffer = await sharp(Buffer.from(svg))
      .png()
      .toBuffer()

    setHeader(event, 'content-type', 'image/png')
    setHeader(event, 'cache-control', 'public, max-age=31536000, immutable')
    
    return buffer
  } catch (error) {
    throw createError({
      statusCode: 500,
      statusMessage: 'Failed to generate OG image'
    })
  }
})
```

**Acceptance criteria:**
- [ ] Dynamic meta tags implemented
- [ ] Structured data markup added
- [ ] Sitemap generation working
- [ ] Robots.txt configured
- [ ] Open Graph images generated
- [ ] SEO best practices applied

---

### Task 10.5: Monitoring and Analytics Setup
**Objective:** Implement comprehensive monitoring, error tracking, and analytics

**What you need to accomplish:**
- Set up error tracking with Sentry
- Implement analytics with Google Analytics
- Configure performance monitoring
- Set up uptime monitoring and alerting

**Documentation to consult:**
- [Sentry Documentation](https://docs.sentry.io/)
- [Google Analytics 4](https://developers.google.com/analytics/devguides/collection/ga4)
- [Vercel Analytics](https://vercel.com/analytics)

**Error tracking setup:**
```bash
# Install Sentry
npm install @sentry/nuxt
```

```typescript
// nuxt.config.ts (Sentry)
export default defineNuxtConfig({
  modules: [
    '@sentry/nuxt/module'
  ],

  sentry: {
    dsn: process.env.SENTRY_DSN,
    environment: process.env.NODE_ENV,
    
    // Performance monitoring
    tracesSampleRate: process.env.NODE_ENV === 'production' ? 0.1 : 1.0,
    
    // Release tracking
    release: process.env.VERCEL_GIT_COMMIT_SHA,
    
    // Source maps
    sourcemap: {
      enabled: true,
      deleteFilesAfterUpload: true
    }
  }
})
```

**Custom error handling:**
```typescript
// plugins/sentry.client.ts
import * as Sentry from '@sentry/vue'

export default defineNuxtPlugin((nuxtApp) => {
  const config = useRuntimeConfig()
  
  Sentry.init({
    app: nuxtApp.vueApp,
    dsn: config.public.sentryDsn,
    environment: process.env.NODE_ENV,
    
    integrations: [
      new Sentry.BrowserTracing({
        routingInstrumentation: Sentry.vueRouterInstrumentation(nuxtApp.$router)
      })
    ],
    
    beforeSend(event) {
      // Filter out development errors
      if (process.env.NODE_ENV === 'development') {
        return null
      }
      
      // Filter sensitive data
      if (event.exception) {
        const error = event.exception.values?.[0]
        if (error?.value?.includes('password') || error?.value?.includes('token')) {
          return null
        }
      }
      
      return event
    }
  })

  // Global error handler
  nuxtApp.vueApp.config.errorHandler = (error, context) => {
    Sentry.captureException(error, {
      contexts: {
        vue: {
          componentName: context?.$options?.name || 'Unknown',
          propsData: context?.$props
        }
      }
    })
  }
})
```

**Analytics setup:**
```typescript
// plugins/gtag.client.ts
export default defineNuxtPlugin(() => {
  const config = useRuntimeConfig()
  
  if (!config.public.analyticsId) return

  // Load Google Analytics
  useHead({
    script: [
      {
        src: `https://www.googletagmanager.com/gtag/js?id=${config.public.analyticsId}`,
        async: true
      }
    ]
  })

  // Initialize gtag
  window.dataLayer = window.dataLayer || []
  function gtag(...args: any[]) {
    window.dataLayer.push(arguments)
  }
  
  gtag('js', new Date())
  gtag('config', config.public.analyticsId, {
    page_title: document.title,
    page_location: window.location.href
  })

  // Make gtag available globally
  window.gtag = gtag
})
```

**Custom analytics tracking:**
```typescript
// composables/useAnalytics.ts
export const useAnalytics = () => {
  const trackEvent = (
    eventName: string,
    parameters: Record<string, any> = {}
  ) => {
    if (process.client && window.gtag) {
      window.gtag('event', eventName, {
        ...parameters,
        timestamp: Date.now()
      })
    }
  }

  const trackPageView = (pagePath: string, pageTitle?: string) => {
    if (process.client && window.gtag) {
      window.gtag('config', useRuntimeConfig().public.analyticsId, {
        page_path: pagePath,
        page_title: pageTitle || document.title
      })
    }
  }

  const trackUserAction = (action: string, category: string, label?: string) => {
    trackEvent('user_action', {
      event_category: category,
      event_label: label,
      action_type: action
    })
  }

  const trackPerformance = (metricName: string, value: number) => {
    trackEvent('performance_metric', {
      metric_name: metricName,
      metric_value: Math.round(value),
      custom_parameter: 'performance'
    })
  }

  const trackError = (error: Error, context?: Record<string, any>) => {
    trackEvent('error_occurred', {
      error_message: error.message,
      error_stack: error.stack?.substring(0, 500),
      ...context
    })
  }

  return {
    trackEvent,
    trackPageView,
    trackUserAction,
    trackPerformance,
    trackError
  }
}
```

**Health check and monitoring:**
```typescript
// server/api/health.get.ts
export default defineEventHandler(async (event) => {
  const checks = {
    timestamp: new Date().toISOString(),
    status: 'healthy',
    checks: {}
  }

  try {
    // Database health check
    const dbHealth = await checkDatabaseHealth()
    checks.checks.database = dbHealth

    // External services health check
    checks.checks.auth = await checkAuthService()
    checks.checks.email = await checkEmailService()

    // Memory usage
    if (process.memoryUsage) {
      const memory = process.memoryUsage()
      checks.checks.memory = {
        status: memory.heapUsed < 100 * 1024 * 1024 ? 'healthy' : 'warning', // 100MB threshold
        heapUsed: `${Math.round(memory.heapUsed / 1024 / 1024)}MB`,
        heapTotal: `${Math.round(memory.heapTotal / 1024 / 1024)}MB`
      }
    }

    // Determine overall status
    const hasUnhealthy = Object.values(checks.checks).some(
      (check: any) => check.status === 'unhealthy'
    )
    
    if (hasUnhealthy) {
      checks.status = 'unhealthy'
      setResponseStatus(event, 503)
    }

    return checks
  } catch (error) {
    checks.status = 'unhealthy'
    checks.checks.error = error.message
    setResponseStatus(event, 503)
    return checks
  }
})

async function checkAuthService() {
  try {
    // Test auth service
    return { status: 'healthy', timestamp: new Date() }
  } catch (error) {
    return { status: 'unhealthy', error: error.message }
  }
}

async function checkEmailService() {
  try {
    // Test email service
    return { status: 'healthy', timestamp: new Date() }
  } catch (error) {
    return { status: 'unhealthy', error: error.message }
  }
}
```

**Uptime monitoring script:**
```typescript
// scripts/uptime-monitor.ts
const monitorUrls = [
  'https://your-app.vercel.app/health',
  'https://your-app.vercel.app/api/health'
]

async function checkUptime() {
  for (const url of monitorUrls) {
    try {
      const response = await fetch(url)
      const data = await response.json()
      
      if (response.status !== 200 || data.status !== 'healthy') {
        await sendAlert(`Service unhealthy: ${url}`, data)
      }
    } catch (error) {
      await sendAlert(`Service down: ${url}`, { error: error.message })
    }
  }
}

async function sendAlert(message: string, data: any) {
  // Send to Slack, Discord, or email
  console.error('ALERT:', message, data)
  
  // You could integrate with services like:
  // - Slack webhooks
  // - Discord webhooks  
  // - Email notifications
  // - PagerDuty
}

// Run every 5 minutes
setInterval(checkUptime, 5 * 60 * 1000)
```

**Acceptance criteria:**
- [ ] Error tracking with Sentry implemented
- [ ] Google Analytics 4 configured
- [ ] Custom analytics events working
- [ ] Health check endpoints created
- [ ] Performance monitoring in place
- [ ] Uptime monitoring configured

## Challenge Extensions

### Advanced Monitoring
- Implement custom dashboards with Grafana
- Set up log aggregation with ELK stack
- Add real-time performance monitoring
- Create custom alerting rules

### Security Enhancements
- Implement Content Security Policy (CSP)
- Add security headers middleware
- Set up vulnerability scanning
- Implement rate limiting and DDoS protection

### Performance Optimization
- Implement edge caching strategies
- Add service worker for offline functionality
- Create progressive loading patterns
- Implement resource hints and preloading

### Business Intelligence
- Set up conversion tracking
- Implement A/B testing framework
- Add user behavior analytics
- Create business metrics dashboards

## Module Completion Checklist

Final production deployment checklist:
- [ ] Production environment fully configured
- [ ] Vercel deployment successful with custom domain
- [ ] Performance optimizations implemented and verified
- [ ] SEO optimization complete with meta tags and sitemap
- [ ] Monitoring and analytics fully operational
- [ ] Error tracking working and tested
- [ ] Health checks and uptime monitoring active
- [ ] Security headers and best practices implemented
- [ ] Load testing completed successfully
- [ ] Documentation updated for production deployment

## Course Completion

Congratulations! You have successfully completed the Vue Full-Stack Development Course. You now have:

### Technical Achievements
- A production-ready Nuxt 3 application
- Modern authentication with better-auth
- Real-time collaboration features
- Comprehensive testing suite
- Performance-optimized deployment

### Professional Skills
- Full-stack development expertise
- Modern development workflows
- Production deployment experience
- Monitoring and maintenance knowledge
- Security and performance best practices

### Next Steps
- Continue building advanced features
- Contribute to open source projects
- Explore microservices architecture
- Learn advanced DevOps practices
- Build your portfolio and apply for roles

Your task management application is now ready for real users and can serve as a strong foundation for your career in modern web development!