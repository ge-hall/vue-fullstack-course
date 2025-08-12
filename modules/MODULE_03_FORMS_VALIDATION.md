# Module 3: Form Handling and Validation

**Duration:** 2-3 days  
**Branch:** `feature/module-3-forms-validation`

## Application Context: TaskFlow User Authentication

### What You're Building
You're building the user authentication system for **TaskFlow** - the foundation that allows users to create accounts and securely access their projects and tasks. This module creates the registration and login experience that every TaskFlow user will encounter.

### TaskFlow Features You're Building This Module
- **User registration**: Complete signup flow with validation and password strength
- **User login**: Secure authentication with "Remember me" functionality
- **Form validation**: Real-time feedback with professional error handling
- **Social authentication**: OAuth integration prep (Google, GitHub buttons)
- **Responsive forms**: Mobile-optimized authentication experience

### User Stories for This Module
- **As a new user**, I want to register for TaskFlow so that I can create and manage my projects
- **As a returning user**, I want to log into TaskFlow so that I can access my existing projects and tasks
- **As a security-conscious user**, I want strong password requirements so that my account is protected
- **As a mobile user**, I want forms that work seamlessly on my device

### UI Mockups Reference
📋 **See detailed visual mockups**: [Module 3 UI Mockups](../mockups/MODULE_03_MOCKUPS.md)

The mockups show complete registration and login forms with validation states, password strength indicators, error handling, and responsive mobile layouts.

## Learning Objectives
- Master VeeValidate for form state management
- Implement Zod schemas for type-safe validation
- Create reusable form components
- Build user registration and authentication forms
- Handle form errors and provide excellent UX

## Overview
Professional applications require robust form handling with client-side validation, error management, and excellent user experience. This module implements the authentication forms that will be the gateway to TaskFlow's project management features.

## Tasks

### Task 3.1: VeeValidate Installation and Setup
**Objective:** Install and configure VeeValidate for Vue 3 form management with the Composition API

**What you need to accomplish:**
- Install VeeValidate and its validation rules package
- Configure global validation rules for TaskFlow forms
- Set up proper validation timing and error messages
- Test basic form validation with useForm composable

**Documentation to consult:**
- [VeeValidate Installation Guide](https://vee-validate.logaretm.com/v4/guide/installation)
- [VeeValidate Composition API](https://vee-validate.logaretm.com/v4/guide/composition-api/getting-started)
- [VeeValidate useForm](https://vee-validate.logaretm.com/v4/api/use-form)

**Acceptance criteria:**
- [ ] VeeValidate packages installed and working
- [ ] Global validation rules configured
- [ ] Basic form with useForm composable functional
- [ ] Error messages display correctly
- [ ] No console errors during validation

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Vue 3 project running from Module 2 (Task 2.1-2.5 completed)
- [ ] Tailwind CSS configured for form styling
- [ ] Basic understanding of Vue 3 Composition API
- [ ] TaskFlow application structure in place

### Step-by-Step Implementation Approach

**1. VeeValidate Package Installation**

**Install the core VeeValidate packages:**
```bash
# In your client/ directory
cd client/
npm install vee-validate @vee-validate/rules
```

**Package purposes:**
- **vee-validate**: Core form validation library with Composition API support
- **@vee-validate/rules**: Pre-built validation rules (required, email, min, etc.)

**Verify installation:**
Check that packages appear in your `client/package.json`:
```json
{
  "dependencies": {
    "vee-validate": "^4.12.4",
    "@vee-validate/rules": "^4.12.4"
  }
}
```

**2. Global Validation Rules Setup**

**Create validation configuration file:**
Create `client/src/plugins/validation.js`:
```javascript
import { defineRule, configure } from 'vee-validate'
import { 
  required, 
  email, 
  min, 
  max,
  confirmed,
  regex 
} from '@vee-validate/rules'

// Define commonly used validation rules for TaskFlow
defineRule('required', required)
defineRule('email', email)  
defineRule('min', min)
defineRule('max', max)
defineRule('confirmed', confirmed)
defineRule('regex', regex)

// Custom TaskFlow validation rules
defineRule('strongPassword', (value) => {
  if (!value) return 'Password is required'
  
  const hasUppercase = /[A-Z]/.test(value)
  const hasLowercase = /[a-z]/.test(value)  
  const hasNumber = /\d/.test(value)
  const hasSpecial = /[@$!%*?&]/.test(value)
  const isLongEnough = value.length >= 8

  if (!isLongEnough) return 'Password must be at least 8 characters'
  if (!hasUppercase) return 'Password must contain an uppercase letter'
  if (!hasLowercase) return 'Password must contain a lowercase letter'  
  if (!hasNumber) return 'Password must contain a number'
  if (!hasSpecial) return 'Password must contain a special character (@$!%*?&)'
  
  return true
})

defineRule('firstName', (value) => {
  if (!value) return 'First name is required'
  if (value.length < 2) return 'First name must be at least 2 characters'
  if (value.length > 50) return 'First name cannot exceed 50 characters'
  return true
})

defineRule('lastName', (value) => {
  if (!value) return 'Last name is required'  
  if (value.length < 2) return 'Last name must be at least 2 characters'
  if (value.length > 50) return 'Last name cannot exceed 50 characters'
  return true
})

// Configure VeeValidate behavior
configure({
  // When to trigger validation
  validateOnBlur: true,      // Validate when field loses focus
  validateOnChange: true,    // Validate when field value changes
  validateOnInput: false,    // Don't validate on every keystroke (performance)
  validateOnModelUpdate: true, // Validate when v-model updates

  // Default error message generator
  generateMessage: (ctx) => {
    const messages = {
      required: `${ctx.field} is required`,
      email: `${ctx.field} must be a valid email address`,
      min: `${ctx.field} must be at least ${ctx.rule.params[0]} characters`,
      max: `${ctx.field} cannot exceed ${ctx.rule.params[0]} characters`,
    }
    
    return messages[ctx.rule.name] || `${ctx.field} is invalid`
  }
})
```

**Why this approach?**
- **Pre-defined rules**: Common validations like email, required are handled
- **Custom rules**: TaskFlow-specific validation like strong passwords
- **Performance optimization**: Validates on blur/change, not every keystroke
- **Consistent messaging**: Standardized error messages across forms

**3. Import Validation Configuration in Main App**

**Update `client/src/main.js`:**
```javascript
import { createApp } from 'vue'
import { createRouter, createWebHistory } from 'vue-router'
import App from './App.vue'
import router from './router'
import './style.css'

// Import VeeValidate configuration
import './plugins/validation.js'

const app = createApp(App)
app.use(router)
app.mount('#app')
```

**Why import in main.js?**
- **Global setup**: Validation rules are available in all components
- **Early initialization**: Rules are defined before any forms render
- **Single configuration**: One place to manage all validation behavior

**4. Test VeeValidate with Basic Form**

**Create test form component `client/src/components/TestValidationForm.vue`:**
```vue
<template>
  <div class="max-w-md mx-auto p-6 bg-white border border-gray-200 rounded-lg">
    <h2 class="text-xl font-semibold mb-6">Test VeeValidate Setup</h2>
    
    <form @submit="onSubmit" class="space-y-4">
      <!-- Name field with custom validation -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          First Name
        </label>
        <input
          v-model="firstName"
          name="firstName"
          type="text"
          :class="firstNameClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
        />
        <span v-if="firstNameErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ firstNameErrorMessage }}
        </span>
      </div>
      
      <!-- Email field -->  
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Email
        </label>
        <input
          v-model="email"
          name="email"  
          type="email"
          :class="emailClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
        />
        <span v-if="emailErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ emailErrorMessage }}
        </span>
      </div>
      
      <!-- Password field with custom validation -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Password
        </label>
        <input
          v-model="password"
          name="password"
          type="password" 
          :class="passwordClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
        />
        <span v-if="passwordErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ passwordErrorMessage }}
        </span>
      </div>
      
      <!-- Submit button -->
      <button
        type="submit"
        :disabled="!meta.valid"
        class="w-full bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 disabled:bg-gray-300 disabled:cursor-not-allowed transition-colors"
      >
        Test Validation
      </button>
    </form>
    
    <!-- Debug information -->
    <div class="mt-6 p-4 bg-gray-50 rounded-lg">
      <h3 class="font-medium mb-2">Form State:</h3>
      <ul class="text-sm text-gray-600 space-y-1">
        <li>Valid: {{ meta.valid }}</li>
        <li>Dirty: {{ meta.dirty }}</li>
        <li>Touched: {{ meta.touched }}</li>
        <li>Values: {{ JSON.stringify(values) }}</li>
        <li>Errors: {{ JSON.stringify(errors) }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useForm, useField } from 'vee-validate'

// Initialize form with useForm composable
const { handleSubmit, values, errors, meta } = useForm({
  // Define initial values
  initialValues: {
    firstName: '',
    email: '',
    password: ''
  },
  // Optional: form-level validation schema would go here
})

// Set up individual fields with useField composable  
const { value: firstName, errorMessage: firstNameErrorMessage } = useField('firstName', 'firstName')
const { value: email, errorMessage: emailErrorMessage } = useField('email', 'email') 
const { value: password, errorMessage: passwordErrorMessage } = useField('password', 'strongPassword')

// Computed classes for visual validation feedback
const firstNameClasses = computed(() => {
  if (firstNameErrorMessage.value) return 'border-red-500'
  if (firstName.value && !firstNameErrorMessage.value) return 'border-green-500'
  return 'border-gray-300'
})

const emailClasses = computed(() => {
  if (emailErrorMessage.value) return 'border-red-500'  
  if (email.value && !emailErrorMessage.value) return 'border-green-500'
  return 'border-gray-300'
})

const passwordClasses = computed(() => {
  if (passwordErrorMessage.value) return 'border-red-500'
  if (password.value && !passwordErrorMessage.value) return 'border-green-500' 
  return 'border-gray-300'
})

// Handle form submission
const onSubmit = handleSubmit((values) => {
  alert('Form is valid! Values: ' + JSON.stringify(values, null, 2))
})
</script>
```

**Understanding useForm and useField:**

**useForm composable:**
- **handleSubmit**: Function that only runs if form is valid
- **values**: Reactive object with all form field values
- **errors**: Reactive object with validation errors
- **meta**: Form metadata (valid, dirty, touched, etc.)

**useField composable:**  
- **value**: Reactive field value (use with v-model)
- **errorMessage**: Current validation error message
- **First parameter**: Field name (must match form field)
- **Second parameter**: Validation rule name (defined in validation.js)

**5. Add Test Form to a Route for Testing**

**Update one of your existing views to include the test form:**

Add to `client/src/views/LoginView.vue` temporarily:
```vue
<template>
  <div class="min-h-screen bg-gray-50 py-12">
    <!-- Original login form placeholder -->
    <div class="text-center text-gray-500 mb-8">
      <p>Login form will be implemented in Task 3.4</p>
    </div>
    
    <!-- Test validation form -->
    <TestValidationForm />
  </div>
</template>

<script setup>
import TestValidationForm from '../components/TestValidationForm.vue'
</script>
```

**6. Test the VeeValidate Setup**

**Start your development server:**
```bash
cd client/
npm run dev
```

**Navigate to `/login` and test:**
1. **Empty fields**: Try submitting - should show required errors
2. **Invalid email**: Enter "test" - should show email format error  
3. **Weak password**: Enter "123" - should show strength requirements
4. **Valid data**: Fill correctly - submit button should activate
5. **Form state**: Check the debug panel shows correct meta information

**Expected behavior:**
- ✅ Validation errors appear on blur (when you click away from field)
- ✅ Errors disappear when you fix the input
- ✅ Border colors change: red (error), green (valid), gray (default)
- ✅ Submit button is disabled until form is valid
- ✅ Custom password validation shows specific error messages

**Key Decision Points Made for TaskFlow:**

**VeeValidate approach chosen:**
- **Composition API**: Modern Vue 3 approach with useForm/useField
- **Manual field binding**: Using useField for individual field control
- **Custom validation rules**: TaskFlow-specific rules defined globally
- **Visual feedback**: Border colors and error messages for UX

**Alternative approaches not used:**
- **Form/Field components**: More abstracted but less control
- **Schema-based validation**: Will add Zod in next task for complex forms
- **Inline rules**: Prefer global rules for consistency

**Performance optimizations:**
- **validateOnInput: false**: Prevents validation on every keystroke
- **Strategic validation timing**: Blur for user experience, change for reactivity

**Verification Steps:**
1. **Installation check**: VeeValidate packages in package.json
2. **Global rules test**: Custom firstName/strongPassword rules work
3. **Composables test**: useForm and useField provide expected functionality  
4. **Visual feedback test**: Border colors change based on validation state
5. **Form state test**: Submit button enables/disables correctly
6. **Error messaging test**: Clear, helpful error messages display

**Troubleshooting Common Issues:**

**VeeValidate not working:**
- Check import path in main.js is correct
- Verify validation.js file is in plugins/ directory
- Ensure field names match between useField and form elements

**Custom rules not found:**
- Check defineRule() calls are before component usage
- Verify rule names match exactly (case-sensitive)
- Confirm validation.js is imported in main.js

**TypeScript errors (if using TS):**
- Install @types/vee-validate if needed
- Use proper typing for useField parameters

---

### Task 3.2: Zod Schema Integration
**Objective:** Integrate Zod schemas with VeeValidate for powerful form validation with cross-field validation capabilities

**What you need to accomplish:**
- Install Zod and VeeValidate Zod adapter
- Create comprehensive validation schemas for TaskFlow authentication forms
- Replace individual validation rules with schema-based validation
- Test complex validation scenarios like password confirmation

**Documentation to consult:**
- [Zod Documentation](https://zod.dev/)
- [VeeValidate Zod Integration](https://vee-validate.logaretm.com/v4/guide/composition-api/validation#using-yup-or-zod)
- [toTypedSchema utility](https://vee-validate.logaretm.com/v4/api/to-typed-schema)

**Acceptance criteria:**
- [ ] Zod packages installed and configured
- [ ] Authentication schemas created with complex validation
- [ ] VeeValidate forms using Zod schemas instead of individual rules
- [ ] Cross-field validation working (password confirmation)
- [ ] Schema validation working in test components

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Task 3.1 completed (VeeValidate setup working)
- [ ] TestValidationForm component functional from previous task
- [ ] Understanding that Zod provides more powerful validation than individual rules
- [ ] TaskFlow authentication requirements understood

### Step-by-Step Implementation Approach

**1. Install Zod and VeeValidate Integration**

**Install the required packages:**
```bash
# In your client/ directory
cd client/
npm install zod @vee-validate/zod
```

**Package purposes:**
- **zod**: Schema validation library that works with plain JavaScript
- **@vee-validate/zod**: Adapter that connects Zod schemas to VeeValidate

**Verify installation:**
Check packages in `client/package.json`:
```json
{
  "dependencies": {
    "zod": "^3.22.4",
    "@vee-validate/zod": "^4.12.4"
  }
}
```

**2. Create TaskFlow Authentication Schemas**

**Create schema directory and authentication schemas:**
Create `client/src/schemas/auth.js`:
```javascript
import { z } from 'zod'

// Registration form schema with comprehensive validation
export const registrationSchema = z.object({
  firstName: z.string()
    .min(1, 'First name is required')
    .min(2, 'First name must be at least 2 characters')
    .max(50, 'First name cannot exceed 50 characters')
    .regex(/^[a-zA-Z\s'-]+$/, 'First name can only contain letters, spaces, hyphens, and apostrophes'),
    
  lastName: z.string()
    .min(1, 'Last name is required')
    .min(2, 'Last name must be at least 2 characters')
    .max(50, 'Last name cannot exceed 50 characters')
    .regex(/^[a-zA-Z\s'-]+$/, 'Last name can only contain letters, spaces, hyphens, and apostrophes'),
    
  email: z.string()
    .min(1, 'Email is required')
    .email('Please enter a valid email address')
    .max(255, 'Email address is too long')
    .toLowerCase(), // Normalize email to lowercase
    
  password: z.string()
    .min(1, 'Password is required')
    .min(8, 'Password must be at least 8 characters')
    .max(128, 'Password cannot exceed 128 characters')
    .regex(/[A-Z]/, 'Password must contain at least one uppercase letter')
    .regex(/[a-z]/, 'Password must contain at least one lowercase letter')
    .regex(/\d/, 'Password must contain at least one number')
    .regex(/[@$!%*?&]/, 'Password must contain at least one special character (@$!%*?&)'),
    
  confirmPassword: z.string()
    .min(1, 'Please confirm your password'),
    
  agreeToTerms: z.boolean()
    .refine(val => val === true, 'You must agree to the Terms of Service and Privacy Policy')
    
}).refine(data => data.password === data.confirmPassword, {
  message: "Passwords do not match",
  path: ["confirmPassword"] // Error shows on confirmPassword field
})

// Login form schema - simpler validation
export const loginSchema = z.object({
  email: z.string()
    .min(1, 'Email is required')
    .email('Please enter a valid email address'),
    
  password: z.string()
    .min(1, 'Password is required'),
    
  rememberMe: z.boolean().optional().default(false)
})

// Password reset request schema
export const resetPasswordSchema = z.object({
  email: z.string()
    .min(1, 'Email is required')
    .email('Please enter a valid email address')
})
```

**Understanding Zod Schema Features:**

**Basic validations:**
- `.min(1, 'message')`: Required field validation
- `.email('message')`: Email format validation
- `.max(50, 'message')`: Maximum length validation

**Advanced validations:**
- `.regex(/pattern/, 'message')`: Custom pattern matching
- `.toLowerCase()`: Data transformation
- `.refine()`: Custom validation logic
- `.optional().default(value)`: Optional fields with defaults

**Cross-field validation:**
```javascript
.refine(data => data.password === data.confirmPassword, {
  message: "Passwords do not match",
  path: ["confirmPassword"] // Attach error to specific field
})
```

**Why Zod is better than individual rules:**
- **Centralized validation**: All field rules in one place
- **Cross-field validation**: Built-in support for comparing fields
- **Data transformation**: Automatic data cleaning (like toLowerCase)
- **Composable**: Can combine and extend schemas
- **Reusable**: Same schema can be used on frontend and backend

**3. Update Test Form to Use Zod Schema**

**Replace your TestValidationForm to use Zod schema:**
Update `client/src/components/TestValidationForm.vue`:
```vue
<template>
  <div class="max-w-md mx-auto p-6 bg-white border border-gray-200 rounded-lg">
    <h2 class="text-xl font-semibold mb-6">Test Zod Schema Validation</h2>
    
    <form @submit="onSubmit" class="space-y-4">
      <!-- First Name -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          First Name *
        </label>
        <input
          v-model="firstName"
          name="firstName"
          type="text"
          :class="firstNameClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
          placeholder="Enter your first name"
        />
        <span v-if="firstNameErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ firstNameErrorMessage }}
        </span>
      </div>
      
      <!-- Last Name -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Last Name *
        </label>
        <input
          v-model="lastName"
          name="lastName"
          type="text"
          :class="lastNameClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
          placeholder="Enter your last name"
        />
        <span v-if="lastNameErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ lastNameErrorMessage }}
        </span>
      </div>
      
      <!-- Email -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Email *
        </label>
        <input
          v-model="email"
          name="email"  
          type="email"
          :class="emailClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
          placeholder="Enter your email address"
        />
        <span v-if="emailErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ emailErrorMessage }}
        </span>
      </div>
      
      <!-- Password -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Password *
        </label>
        <input
          v-model="password"
          name="password"
          type="password" 
          :class="passwordClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
          placeholder="Create a strong password"
        />
        <span v-if="passwordErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ passwordErrorMessage }}
        </span>
      </div>
      
      <!-- Confirm Password -->
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">
          Confirm Password *
        </label>
        <input
          v-model="confirmPassword"
          name="confirmPassword"
          type="password"
          :class="confirmPasswordClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-colors"
          placeholder="Confirm your password"
        />
        <span v-if="confirmPasswordErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ confirmPasswordErrorMessage }}
        </span>
      </div>
      
      <!-- Terms Agreement -->
      <div class="flex items-start space-x-2">
        <input
          v-model="agreeToTerms"
          name="agreeToTerms"
          type="checkbox"
          :class="agreeToTermsClasses"
          class="mt-1 w-4 h-4 text-blue-600 border-gray-300 rounded focus:ring-2 focus:ring-blue-500"
        />
        <label class="text-sm text-gray-700 leading-5">
          I agree to the Terms of Service and Privacy Policy *
        </label>
      </div>
      <span v-if="agreeToTermsErrorMessage" class="text-sm text-red-600 block">
        {{ agreeToTermsErrorMessage }}
      </span>
      
      <!-- Submit button -->
      <button
        type="submit"
        :disabled="!meta.valid"
        class="w-full bg-blue-600 text-white py-2 px-4 rounded-lg hover:bg-blue-700 disabled:bg-gray-300 disabled:cursor-not-allowed transition-colors"
      >
        Test Schema Validation
      </button>
    </form>
    
    <!-- Debug information -->
    <div class="mt-6 p-4 bg-gray-50 rounded-lg">
      <h3 class="font-medium mb-2">Form State:</h3>
      <ul class="text-sm text-gray-600 space-y-1">
        <li><strong>Valid:</strong> {{ meta.valid }}</li>
        <li><strong>Dirty:</strong> {{ meta.dirty }}</li>
        <li><strong>Values:</strong> {{ JSON.stringify(values, null, 2) }}</li>
        <li><strong>Errors:</strong> {{ JSON.stringify(errors, null, 2) }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useForm, useField } from 'vee-validate'
import { toTypedSchema } from '@vee-validate/zod'
import { registrationSchema } from '../schemas/auth.js'

// Convert Zod schema to VeeValidate typed schema
const validationSchema = toTypedSchema(registrationSchema)

// Initialize form with Zod schema
const { handleSubmit, values, errors, meta } = useForm({
  validationSchema, // Use the Zod schema for validation
  initialValues: {
    firstName: '',
    lastName: '',
    email: '',
    password: '',
    confirmPassword: '',
    agreeToTerms: false
  }
})

// Set up fields - no individual rules needed, schema handles everything
const { value: firstName, errorMessage: firstNameErrorMessage } = useField('firstName')
const { value: lastName, errorMessage: lastNameErrorMessage } = useField('lastName')
const { value: email, errorMessage: emailErrorMessage } = useField('email') 
const { value: password, errorMessage: passwordErrorMessage } = useField('password')
const { value: confirmPassword, errorMessage: confirmPasswordErrorMessage } = useField('confirmPassword')
const { value: agreeToTerms, errorMessage: agreeToTermsErrorMessage } = useField('agreeToTerms')

// Computed classes for visual validation feedback
const getFieldClasses = (errorMessage, value) => {
  if (errorMessage) return 'border-red-500'
  if (value && !errorMessage) return 'border-green-500'
  return 'border-gray-300'
}

const firstNameClasses = computed(() => getFieldClasses(firstNameErrorMessage.value, firstName.value))
const lastNameClasses = computed(() => getFieldClasses(lastNameErrorMessage.value, lastName.value))
const emailClasses = computed(() => getFieldClasses(emailErrorMessage.value, email.value))
const passwordClasses = computed(() => getFieldClasses(passwordErrorMessage.value, password.value))
const confirmPasswordClasses = computed(() => getFieldClasses(confirmPasswordErrorMessage.value, confirmPassword.value))
const agreeToTermsClasses = computed(() => {
  if (agreeToTermsErrorMessage.value) return 'border-red-500'
  return 'border-gray-300'
})

// Handle form submission
const onSubmit = handleSubmit((values) => {
  alert('Registration form is valid!\n\nData:\n' + JSON.stringify(values, null, 2))
})
</script>
```

**Key Changes from Individual Rules to Schema:**

**Before (individual rules approach):**
```javascript
// Task 3.1 approach
const { value: firstName, errorMessage: firstNameErrorMessage } = useField('firstName', 'firstName')
const { value: lastName, errorMessage: lastNameErrorMessage } = useField('lastName', 'lastName')
```

**After (schema-based approach):**
```javascript
// Task 3.2 approach
const validationSchema = toTypedSchema(registrationSchema)
const { value: firstName, errorMessage: firstNameErrorMessage } = useField('firstName')
const { value: lastName, errorMessage: lastNameErrorMessage } = useField('lastName')
```

**Benefits of schema approach:**
- **Single source of truth**: All validation logic in one schema file
- **Cross-field validation**: Password confirmation works automatically
- **Less repetitive code**: No need to define individual rules
- **Complex validation**: Multiple rules per field handled elegantly
- **Data transformation**: Email automatically converted to lowercase

**4. Test Complex Validation Scenarios**

**Start your dev server and test these scenarios:**
```bash
cd client/
npm run dev
```

**Navigate to `/login` and test:**

**1. Name validation:**
- Try "A" → "First name must be at least 2 characters"
- Try "123John" → "First name can only contain letters..."
- Try "John-Paul O'Connor" → Should be valid

**2. Email validation:**
- Try "test" → "Please enter a valid email address"
- Try "Test@Example.COM" → Valid, should normalize to "test@example.com"

**3. Password validation:**
- Try "weak" → Multiple specific error messages
- Try "password" → Missing uppercase, number, special character
- Try "Password123!" → Should be valid

**4. Cross-field validation (this is the key benefit):**
- Enter "Password123!" in password field
- Enter "Password456!" in confirm password → "Passwords do not match" 
- Change confirm password to "Password123!" → Error should disappear

**5. Terms agreement:**
- Leave unchecked → "You must agree to the Terms..."
- Check it → Error disappears

**Expected behavior:**
- ✅ More sophisticated validation messages than individual rules
- ✅ Cross-field validation works automatically (password confirmation)
- ✅ Data transformation (email converted to lowercase)
- ✅ Complex regex patterns work for names and passwords
- ✅ Form only submits when all schema requirements are met

**5. Create Login Schema Test**

**Create a simple login test component:**
Create `client/src/components/TestLoginForm.vue`:
```vue
<template>
  <div class="max-w-sm mx-auto p-6 bg-white border border-gray-200 rounded-lg mt-8">
    <h3 class="text-lg font-semibold mb-4">Login Schema Test</h3>
    
    <form @submit="onSubmit" class="space-y-4">
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">Email</label>
        <input
          v-model="email"
          type="email"
          :class="emailClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
          placeholder="Enter your email"
        />
        <span v-if="emailErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ emailErrorMessage }}
        </span>
      </div>
      
      <div>
        <label class="block text-sm font-medium text-gray-700 mb-1">Password</label>
        <input
          v-model="password"
          type="password"
          :class="passwordClasses"
          class="w-full px-3 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
          placeholder="Enter your password"
        />
        <span v-if="passwordErrorMessage" class="text-sm text-red-600 mt-1 block">
          {{ passwordErrorMessage }}
        </span>
      </div>
      
      <div class="flex items-center">
        <input v-model="rememberMe" type="checkbox" class="w-4 h-4 text-blue-600" />
        <label class="ml-2 text-sm text-gray-700">Remember me</label>
      </div>
      
      <button
        type="submit"
        :disabled="!meta.valid"
        class="w-full bg-green-600 text-white py-2 rounded-lg hover:bg-green-700 disabled:bg-gray-300"
      >
        Test Login
      </button>
    </form>
    
    <div class="mt-4 text-xs text-gray-500">
      Valid: {{ meta.valid }} | Values: {{ JSON.stringify(values) }}
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useForm, useField } from 'vee-validate'
import { toTypedSchema } from '@vee-validate/zod'
import { loginSchema } from '../schemas/auth.js'

const validationSchema = toTypedSchema(loginSchema)

const { handleSubmit, values, meta } = useForm({
  validationSchema,
  initialValues: {
    email: '',
    password: '',
    rememberMe: false
  }
})

const { value: email, errorMessage: emailErrorMessage } = useField('email')
const { value: password, errorMessage: passwordErrorMessage } = useField('password')  
const { value: rememberMe } = useField('rememberMe')

const emailClasses = computed(() => {
  if (emailErrorMessage.value) return 'border-red-500'
  if (email.value && !emailErrorMessage.value) return 'border-green-500'
  return 'border-gray-300'
})

const passwordClasses = computed(() => {
  if (passwordErrorMessage.value) return 'border-red-500'
  if (password.value && !passwordErrorMessage.value) return 'border-green-500'
  return 'border-gray-300'
})

const onSubmit = handleSubmit((values) => {
  alert('Login valid! Data: ' + JSON.stringify(values))
})
</script>
```

**Add the login test to your test page:**
Update `client/src/components/TestValidationForm.vue` by adding at the end of the template (before the final `</div>`):
```vue
    <!-- Add login form test -->
    <TestLoginForm />
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useForm, useField } from 'vee-validate'
import { toTypedSchema } from '@vee-validate/zod'
import { registrationSchema } from '../schemas/auth.js'
import TestLoginForm from './TestLoginForm.vue' // Add this import

// ... rest of component stays the same
</script>
```

**6. Compare Individual Rules vs Schema Approach**

**Individual Rules Approach (Task 3.1):**
```javascript
// Pros: Simple to understand, direct field-to-rule mapping
// Cons: Repetitive, no cross-field validation, scattered logic

// Multiple files with individual rule definitions
defineRule('firstName', (value) => { /* custom logic */ })
defineRule('lastName', (value) => { /* custom logic */ })
defineRule('strongPassword', (value) => { /* custom logic */ })

// Each field needs explicit rule
const { value: password } = useField('password', 'strongPassword')
```

**Schema-Based Approach (Task 3.2):**
```javascript
// Pros: Centralized, cross-field validation, reusable, composable
// Cons: Requires understanding schemas, additional dependency

// Single schema with all validation logic
const registrationSchema = z.object({
  firstName: z.string().min(2, 'message'),
  password: z.string().min(8, 'message').regex(/pattern/, 'message'),
  confirmPassword: z.string()
}).refine(/* cross-field validation */)

// No individual rule needed
const { value: password } = useField('password')
```

**When to use each approach:**
- **Individual rules**: Simple forms, learning VeeValidate basics, no cross-field validation needed
- **Schema approach**: Complex forms, cross-field validation, reusable validation logic, sharing with backend

**Key Decision Points Made for TaskFlow:**

**Schema organization chosen:**
- **File-based organization**: `schemas/auth.js` for authentication-related schemas
- **Comprehensive validation**: Detailed rules with specific error messages  
- **JavaScript approach**: Works without TypeScript complexity

**Performance considerations:**
- **Schema validation**: More efficient than multiple individual rule checks
- **toTypedSchema caching**: VeeValidate caches compiled schemas
- **Validation timing**: Same as before (validates on blur/change)

**Verification Steps:**
1. **Schema compilation**: Zod schemas work without errors in JavaScript
2. **Cross-field validation**: Password confirmation validates correctly
3. **Complex validation**: Multiple regex patterns and rules work together
4. **Error messages**: Clear, helpful validation messages display
5. **Form submission**: Only valid data can be submitted
6. **Data transformation**: Email normalization works automatically

**Troubleshooting Common Issues:**

**toTypedSchema not found:**
- Ensure `@vee-validate/zod` is installed: `npm install @vee-validate/zod`
- Check import: `import { toTypedSchema } from '@vee-validate/zod'`

**Schema validation not triggering:**
- Verify `validationSchema` is passed to `useForm`
- Check field names match schema properties exactly (case-sensitive)
- Remove any conflicting individual rule parameters from `useField`

**Cross-field validation not working:**
- Ensure the `.refine()` method is properly structured
- Check that the `path` array contains the correct field name
- Verify both fields are present in the form

**Schema import errors:**
- Check file path: `../schemas/auth.js`  
- Ensure proper export: `export const registrationSchema`
- Verify file extension matches (`.js` not `.ts`)

## Optional: TypeScript Integration

If you want to add TypeScript support later, you can:

1. **Rename schema file** from `auth.js` to `auth.ts`
2. **Add type exports**:
```typescript
export type RegistrationData = z.infer<typeof registrationSchema>
export type LoginData = z.infer<typeof loginSchema>
```
3. **Use types in components** for better IDE support:
```typescript
const onSubmit = handleSubmit((values: RegistrationData) => {
  // values is now fully typed
})
```

But this is completely optional - Zod works great with plain JavaScript!

---

### Task 3.3: Reusable Form Components
**Objective:** Build a complete set of reusable form components that integrate seamlessly with VeeValidate and Zod schemas

**What you need to accomplish:**
- Create TaskFlow-branded form components with consistent styling
- Build components that work with both individual validation rules and schemas
- Implement comprehensive accessibility features
- Design components for maximum reusability across TaskFlow forms

**Documentation to consult:**
- [Vue 3 Component Props](https://vuejs.org/guide/components/props.html)
- [VeeValidate useField](https://vee-validate.logaretm.com/v4/api/use-field)
- [Web Accessibility Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)

**Acceptance criteria:**
- [ ] BaseInput component with full validation integration
- [ ] BaseButton component with loading states
- [ ] PasswordStrengthIndicator for strong password feedback
- [ ] FormGroup component for consistent layout
- [ ] All components work with Zod schemas from Task 3.2
- [ ] Comprehensive accessibility features implemented

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Tasks 3.1 and 3.2 completed (VeeValidate and Zod working)
- [ ] BaseButton component from Module 2 available for reference
- [ ] Understanding of Vue 3 Composition API and props
- [ ] TaskFlow design system established (colors, spacing, etc.)

### Step-by-Step Implementation Approach

**1. Create TaskFlow BaseInput Component**

**Create the main form input component:**
Create `client/src/components/base/BaseInput.vue`:
```vue
<template>
  <div class="space-y-1">
    <!-- Label with required indicator -->
    <label 
      v-if="label" 
      :for="inputId" 
      class="block text-sm font-medium text-gray-700"
    >
      {{ label }}
      <span v-if="required" class="text-red-500 ml-1">*</span>
    </label>
    
    <!-- Input with validation styling -->
    <div class="relative">
      <input
        :id="inputId"
        :type="currentType"
        :placeholder="placeholder"
        :disabled="disabled"
        :autocomplete="autocomplete"
        :class="inputClasses"
        :aria-describedby="ariaDescribedBy"
        :aria-invalid="!!errorMessage"
        v-model="value"
        @blur="handleBlur"
      />
      
      <!-- Password visibility toggle -->
      <button
        v-if="type === 'password'"
        type="button"
        @click="togglePasswordVisibility"
        class="absolute inset-y-0 right-0 flex items-center pr-3 text-gray-400 hover:text-gray-600 transition-colors"
        :aria-label="showPassword ? 'Hide password' : 'Show password'"
      >
        <!-- Eye icon (show) -->
        <svg v-if="!showPassword" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
        </svg>
        
        <!-- Eye slash icon (hide) -->
        <svg v-else class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-3.029m5.858.908a3 3 0 114.243 4.243M9.878 9.878l4.242 4.242M9.878 9.878L3 3m6.878 6.878L18 18" />
        </svg>
      </button>
      
      <!-- Validation status icon -->
      <div v-if="showValidationIcon" class="absolute inset-y-0 right-0 flex items-center pr-3 pointer-events-none">
        <!-- Success checkmark -->
        <svg v-if="validationState === 'valid'" class="h-5 w-5 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
        </svg>
        
        <!-- Error X -->
        <svg v-else-if="validationState === 'invalid'" class="h-5 w-5 text-red-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        </svg>
      </div>
    </div>
    
    <!-- Error message -->
    <p 
      v-if="errorMessage" 
      :id="errorId"
      class="text-sm text-red-600 flex items-start space-x-1"
      role="alert"
    >
      <svg class="h-4 w-4 mt-0.5 flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
      <span>{{ errorMessage }}</span>
    </p>
    
    <!-- Help text -->
    <p 
      v-if="help && !errorMessage" 
      :id="helpId"
      class="text-sm text-gray-500"
    >
      {{ help }}
    </p>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import { useField } from 'vee-validate'

// Props definition
const props = defineProps({
  // Field identification
  name: {
    type: String,
    required: true
  },
  
  // Input attributes
  type: {
    type: String,
    default: 'text',
    validator: (value) => ['text', 'email', 'password', 'tel', 'url', 'search', 'number'].includes(value)
  },
  
  placeholder: String,
  disabled: Boolean,
  required: Boolean,
  autocomplete: String,
  
  // Component-specific props
  label: String,
  help: String,
  
  // Styling props
  size: {
    type: String,
    default: 'md',
    validator: (value) => ['sm', 'md', 'lg'].includes(value)
  }
})

// Generate unique IDs for accessibility
const inputId = ref(`input-${props.name}-${Math.random().toString(36).substr(2, 9)}`)
const errorId = ref(`error-${props.name}-${Math.random().toString(36).substr(2, 9)}`)
const helpId = ref(`help-${props.name}-${Math.random().toString(36).substr(2, 9)}`)

// Password visibility state
const showPassword = ref(false)

// VeeValidate integration
const { value, errorMessage, handleBlur } = useField(props.name)

// Password visibility toggle
const currentType = computed(() => {
  if (props.type === 'password' && showPassword.value) {
    return 'text'
  }
  return props.type
})

const togglePasswordVisibility = () => {
  showPassword.value = !showPassword.value
}

// Validation state for visual feedback
const validationState = computed(() => {
  if (errorMessage.value) return 'invalid'
  if (value.value && !errorMessage.value) return 'valid'
  return 'neutral'
})

// Show validation icons (but not for password fields with toggle)
const showValidationIcon = computed(() => {
  return props.type !== 'password' && validationState.value !== 'neutral'
})

// Dynamic classes based on state and size
const inputClasses = computed(() => {
  const baseClasses = [
    'block', 'w-full', 'border', 'rounded-lg',
    'transition-colors', 'duration-200',
    'focus:outline-none', 'focus:ring-2', 'focus:ring-offset-1'
  ]
  
  // Size-specific padding and text
  const sizeClasses = {
    sm: ['px-3', 'py-1.5', 'text-sm'],
    md: ['px-3', 'py-2', 'text-sm'],
    lg: ['px-4', 'py-3', 'text-base']
  }
  
  // State-specific styling
  if (validationState.value === 'invalid') {
    baseClasses.push(
      'border-red-500', 'text-red-900', 'placeholder-red-400',
      'focus:ring-red-500', 'focus:border-red-500'
    )
  } else if (validationState.value === 'valid') {
    baseClasses.push(
      'border-green-500', 'text-green-900', 'placeholder-green-400',
      'focus:ring-green-500', 'focus:border-green-500'
    )
  } else if (props.disabled) {
    baseClasses.push(
      'bg-gray-50', 'border-gray-200', 'text-gray-500', 
      'cursor-not-allowed'
    )
  } else {
    baseClasses.push(
      'border-gray-300', 'text-gray-900', 'placeholder-gray-400',
      'focus:ring-taskflow-primary', 'focus:border-taskflow-primary',
      'hover:border-gray-400'
    )
  }
  
  // Add password field padding to account for toggle button
  if (props.type === 'password') {
    sizeClasses[props.size].push('pr-10')
  }
  
  // Add validation icon padding for non-password fields
  if (showValidationIcon.value) {
    sizeClasses[props.size].push('pr-10')
  }
  
  baseClasses.push(...sizeClasses[props.size])
  return baseClasses.join(' ')
})

// ARIA describedby for accessibility
const ariaDescribedBy = computed(() => {
  const descriptions = []
  if (errorMessage.value) descriptions.push(errorId.value)
  if (props.help && !errorMessage.value) descriptions.push(helpId.value)
  return descriptions.length > 0 ? descriptions.join(' ') : undefined
})
</script>
```

**Understanding the BaseInput Component:**

**Key features implemented:**
- **VeeValidate integration**: Uses `useField` to connect to validation
- **Visual validation states**: Border colors change based on validation status
- **Password visibility toggle**: Eye icon to show/hide password
- **Accessibility**: Proper ARIA labels, describedby, and role attributes
- **Validation icons**: Checkmark for valid, X for invalid
- **Flexible sizing**: Small, medium, large size variants
- **Help text**: Optional help text that hides when errors show

**Component API:**
```javascript
// Basic usage
<BaseInput 
  name="email" 
  type="email" 
  label="Email Address" 
  placeholder="Enter your email"
  required
/>

// With help text
<BaseInput 
  name="password" 
  type="password" 
  label="Password" 
  help="Password must be at least 8 characters"
  required
/>

// Different sizes
<BaseInput name="search" size="sm" placeholder="Search..." />
<BaseInput name="title" size="lg" label="Project Title" />
```

**2. Create Enhanced BaseButton Component**

**Update the existing BaseButton or create TaskFlow-specific version:**
Create `client/src/components/base/BaseButton.vue` (enhanced version):
```vue
<template>
  <button
    :type="type"
    :disabled="disabled || loading"
    :class="buttonClasses"
    @click="handleClick"
  >
    <!-- Loading spinner -->
    <svg 
      v-if="loading" 
      class="animate-spin -ml-1 mr-2 h-4 w-4 text-current" 
      fill="none" 
      viewBox="0 0 24 24"
    >
      <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
      <path class="opacity-75" fill="currentColor" d="m4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
    </svg>
    
    <!-- Icon slot (optional) -->
    <slot name="icon" />
    
    <!-- Button text content -->
    <slot>
      <span v-if="!loading">{{ text }}</span>
      <span v-else>{{ loadingText || text }}</span>
    </slot>
  </button>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  // Button variants
  variant: {
    type: String,
    default: 'primary',
    validator: (value) => ['primary', 'secondary', 'outline', 'ghost', 'danger', 'success'].includes(value)
  },
  
  // Size variations
  size: {
    type: String,
    default: 'md',
    validator: (value) => ['sm', 'md', 'lg', 'xl'].includes(value)
  },
  
  // Button type for forms
  type: {
    type: String,
    default: 'button',
    validator: (value) => ['button', 'submit', 'reset'].includes(value)
  },
  
  // Button states
  disabled: Boolean,
  loading: Boolean,
  fullWidth: Boolean,
  
  // Button text (alternative to slot)
  text: String,
  loadingText: String
})

const emit = defineEmits(['click'])

// Enhanced button classes with TaskFlow styling
const buttonClasses = computed(() => {
  const classes = [
    'inline-flex', 'items-center', 'justify-center',
    'font-medium', 'rounded-lg', 'transition-all', 'duration-200',
    'focus:outline-none', 'focus:ring-2', 'focus:ring-offset-2'
  ]
  
  // Size-specific classes
  const sizeClasses = {
    sm: ['px-3', 'py-1.5', 'text-xs'],
    md: ['px-4', 'py-2', 'text-sm'], 
    lg: ['px-6', 'py-3', 'text-base'],
    xl: ['px-8', 'py-4', 'text-lg']
  }
  
  // TaskFlow variant classes
  const variantClasses = {
    primary: [
      'bg-taskflow-primary', 'text-white', 'border-transparent',
      'hover:bg-blue-700', 'focus:ring-blue-500',
      'disabled:bg-gray-300', 'disabled:cursor-not-allowed'
    ],
    secondary: [
      'bg-gray-600', 'text-white', 'border-transparent',
      'hover:bg-gray-700', 'focus:ring-gray-500',
      'disabled:bg-gray-300', 'disabled:cursor-not-allowed'
    ],
    outline: [
      'bg-transparent', 'text-taskflow-primary', 'border', 'border-taskflow-primary',
      'hover:bg-taskflow-primary', 'hover:text-white', 'focus:ring-blue-500',
      'disabled:border-gray-300', 'disabled:text-gray-300', 'disabled:cursor-not-allowed'
    ],
    ghost: [
      'bg-transparent', 'text-taskflow-primary', 'border-transparent',
      'hover:bg-blue-50', 'focus:ring-blue-500',
      'disabled:text-gray-300', 'disabled:cursor-not-allowed'
    ],
    danger: [
      'bg-red-600', 'text-white', 'border-transparent',
      'hover:bg-red-700', 'focus:ring-red-500',
      'disabled:bg-gray-300', 'disabled:cursor-not-allowed'
    ],
    success: [
      'bg-green-600', 'text-white', 'border-transparent',
      'hover:bg-green-700', 'focus:ring-green-500',
      'disabled:bg-gray-300', 'disabled:cursor-not-allowed'
    ]
  }
  
  // Add size classes
  classes.push(...sizeClasses[props.size])
  
  // Add variant classes
  classes.push(...variantClasses[props.variant])
  
  // Full width
  if (props.fullWidth) {
    classes.push('w-full')
  }
  
  // Loading state
  if (props.loading) {
    classes.push('cursor-wait')
  }
  
  return classes.join(' ')
})

// Handle click events
const handleClick = (event) => {
  if (props.disabled || props.loading) {
    event.preventDefault()
    return
  }
  
  emit('click', event)
}
</script>
```

**3. Create Password Strength Indicator Component**

**Build visual password strength feedback:**
Create `client/src/components/base/PasswordStrengthIndicator.vue`:
```vue
<template>
  <div v-if="password" class="space-y-2">
    <!-- Strength bar and label -->
    <div class="flex items-center justify-between">
      <span class="text-xs text-gray-600">Password strength:</span>
      <span :class="strengthLabelClasses" class="text-xs font-medium">
        {{ strengthLabel }}
      </span>
    </div>
    
    <!-- Visual strength bar -->
    <div class="flex space-x-1">
      <div
        v-for="i in 5"
        :key="i"
        :class="getBarClasses(i)"
        class="h-2 flex-1 rounded-full transition-colors duration-200"
      ></div>
    </div>
    
    <!-- Requirements checklist -->
    <div class="space-y-1">
      <div
        v-for="requirement in requirements"
        :key="requirement.key"
        class="flex items-center space-x-2 text-xs"
      >
        <!-- Check or X icon -->
        <svg v-if="requirement.met" class="h-3 w-3 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
        </svg>
        <svg v-else class="h-3 w-3 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        </svg>
        
        <!-- Requirement text -->
        <span :class="requirement.met ? 'text-green-700' : 'text-gray-500'">
          {{ requirement.text }}
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useField } from 'vee-validate'

const props = defineProps({
  name: {
    type: String,
    required: true
  }
})

// Get password value from VeeValidate
const { value: password } = useField(props.name)

// Password strength calculation
const strength = computed(() => {
  if (!password.value) return { score: 0, label: 'No password' }
  
  let score = 0
  const checks = {
    length: password.value.length >= 8,
    uppercase: /[A-Z]/.test(password.value),
    lowercase: /[a-z]/.test(password.value),
    number: /\d/.test(password.value),
    special: /[@$!%*?&]/.test(password.value)
  }
  
  // Calculate score
  if (checks.length) score++
  if (checks.uppercase) score++
  if (checks.lowercase) score++
  if (checks.number) score++
  if (checks.special) score++
  
  // Determine label and color
  const strengthLevels = {
    0: { label: 'Very Weak', color: 'red' },
    1: { label: 'Very Weak', color: 'red' },
    2: { label: 'Weak', color: 'orange' },
    3: { label: 'Fair', color: 'yellow' },
    4: { label: 'Good', color: 'blue' },
    5: { label: 'Strong', color: 'green' }
  }
  
  return {
    score,
    ...strengthLevels[score],
    checks
  }
})

// Requirements list with met status
const requirements = computed(() => [
  {
    key: 'length',
    text: '8+ characters',
    met: strength.value.checks?.length || false
  },
  {
    key: 'uppercase',
    text: 'Uppercase letter (A-Z)',
    met: strength.value.checks?.uppercase || false
  },
  {
    key: 'lowercase',
    text: 'Lowercase letter (a-z)',
    met: strength.value.checks?.lowercase || false
  },
  {
    key: 'number',
    text: 'Number (0-9)',
    met: strength.value.checks?.number || false
  },
  {
    key: 'special',
    text: 'Special character (@$!%*?&)',
    met: strength.value.checks?.special || false
  }
])

// Strength label styling
const strengthLabelClasses = computed(() => {
  const colorClasses = {
    red: 'text-red-600',
    orange: 'text-orange-600',
    yellow: 'text-yellow-600',
    blue: 'text-blue-600',
    green: 'text-green-600'
  }
  
  return colorClasses[strength.value.color] || 'text-gray-600'
})

// Individual strength bar styling
const getBarClasses = (barIndex) => {
  const { score, color } = strength.value
  const isActive = barIndex <= score
  
  if (!isActive) return 'bg-gray-200'
  
  const colorClasses = {
    red: 'bg-red-500',
    orange: 'bg-orange-500',
    yellow: 'bg-yellow-500',
    blue: 'bg-blue-500',
    green: 'bg-green-500'
  }
  
  return colorClasses[color] || 'bg-gray-200'
}

// Strength label text
const strengthLabel = computed(() => {
  return strength.value.label
})
</script>
```

**4. Create Form Layout Components**

**Create FormGroup for consistent spacing:**
Create `client/src/components/base/FormGroup.vue`:
```vue
<template>
  <div :class="groupClasses">
    <slot />
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  spacing: {
    type: String,
    default: 'md',
    validator: (value) => ['sm', 'md', 'lg', 'xl'].includes(value)
  }
})

const groupClasses = computed(() => {
  const spacingClasses = {
    sm: 'space-y-3',
    md: 'space-y-4',
    lg: 'space-y-6',
    xl: 'space-y-8'
  }
  
  return spacingClasses[props.spacing]
})
</script>
```

**Create FormCard for consistent form containers:**
Create `client/src/components/base/FormCard.vue`:
```vue
<template>
  <div :class="cardClasses">
    <!-- Header slot -->
    <div v-if="$slots.header || title" class="mb-6">
      <slot name="header">
        <h2 v-if="title" class="text-2xl font-bold text-gray-900 text-center">
          {{ title }}
        </h2>
        <p v-if="subtitle" class="text-gray-600 text-center mt-2">
          {{ subtitle }}
        </p>
      </slot>
    </div>
    
    <!-- Main content -->
    <slot />
    
    <!-- Footer slot -->
    <div v-if="$slots.footer" class="mt-6 pt-6 border-t border-gray-200">
      <slot name="footer" />
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  title: String,
  subtitle: String,
  
  maxWidth: {
    type: String,
    default: 'md',
    validator: (value) => ['sm', 'md', 'lg', 'xl', '2xl'].includes(value)
  },
  
  padding: {
    type: String,
    default: 'lg',
    validator: (value) => ['sm', 'md', 'lg', 'xl'].includes(value)
  },
  
  centered: {
    type: Boolean,
    default: true
  }
})

const cardClasses = computed(() => {
  const classes = ['bg-white', 'border', 'border-gray-200', 'rounded-lg', 'shadow-sm']
  
  // Max width
  const maxWidthClasses = {
    sm: 'max-w-sm',
    md: 'max-w-md',
    lg: 'max-w-lg',
    xl: 'max-w-xl',
    '2xl': 'max-w-2xl'
  }
  classes.push(maxWidthClasses[props.maxWidth])
  
  // Padding
  const paddingClasses = {
    sm: 'p-4',
    md: 'p-6',
    lg: 'p-8',
    xl: 'p-10'
  }
  classes.push(paddingClasses[props.padding])
  
  // Centering
  if (props.centered) {
    classes.push('mx-auto')
  }
  
  return classes.join(' ')
})
</script>
```

**5. Test All Components Together**

**Create comprehensive test form using all new components:**
Create `client/src/components/TestFormComponents.vue`:
```vue
<template>
  <div class="min-h-screen bg-gray-50 py-12">
    <FormCard title="Test TaskFlow Form Components" subtitle="Testing all form components together">
      <form @submit="onSubmit">
        <FormGroup spacing="lg">
          <!-- Name fields in a row -->
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <BaseInput 
              name="firstName"
              label="First Name"
              placeholder="Enter your first name"
              required
            />
            <BaseInput 
              name="lastName"
              label="Last Name"
              placeholder="Enter your last name"
              required
            />
          </div>
          
          <!-- Email field -->
          <BaseInput 
            name="email"
            type="email"
            label="Email Address"
            placeholder="Enter your email address"
            help="We'll never share your email with anyone else"
            required
          />
          
          <!-- Password with strength indicator -->
          <div class="space-y-3">
            <BaseInput 
              name="password"
              type="password"
              label="Password"
              placeholder="Create a strong password"
              required
            />
            <PasswordStrengthIndicator name="password" />
          </div>
          
          <!-- Confirm password -->
          <BaseInput 
            name="confirmPassword"
            type="password"
            label="Confirm Password"
            placeholder="Confirm your password"
            required
          />
          
          <!-- Form actions -->
          <div class="flex flex-col sm:flex-row gap-3 pt-4">
            <BaseButton 
              type="submit"
              :disabled="!meta.valid"
              :loading="isSubmitting"
              variant="primary"
              size="lg"
              fullWidth
              :loading-text="'Creating Account...'"
            >
              Create Account
            </BaseButton>
            
            <BaseButton 
              type="button"
              variant="outline"
              size="lg"
              fullWidth
              @click="resetForm"
            >
              Reset Form
            </BaseButton>
          </div>
        </FormGroup>
      </form>
      
      <!-- Debug information -->
      <div class="mt-8 p-4 bg-gray-50 rounded-lg">
        <h3 class="font-medium mb-2">Form Debug Info:</h3>
        <div class="text-sm text-gray-600 space-y-1">
          <p><strong>Valid:</strong> {{ meta.valid }}</p>
          <p><strong>Dirty:</strong> {{ meta.dirty }}</p>
          <p><strong>Errors:</strong> {{ JSON.stringify(errors) }}</p>
        </div>
      </div>
    </FormCard>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useForm } from 'vee-validate'
import { toTypedSchema } from '@vee-validate/zod'
import { registrationSchema } from '../schemas/auth.js'

// Import all the new components
import BaseInput from './base/BaseInput.vue'
import BaseButton from './base/BaseButton.vue'
import PasswordStrengthIndicator from './base/PasswordStrengthIndicator.vue'
import FormGroup from './base/FormGroup.vue'
import FormCard from './base/FormCard.vue'

// Form setup
const validationSchema = toTypedSchema(registrationSchema)
const { handleSubmit, resetForm, meta, errors } = useForm({
  validationSchema,
  initialValues: {
    firstName: '',
    lastName: '',
    email: '',
    password: '',
    confirmPassword: '',
    agreeToTerms: false
  }
})

const isSubmitting = ref(false)

// Form submission handler
const onSubmit = handleSubmit(async (values) => {
  isSubmitting.value = true
  
  try {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 2000))
    alert('Form submitted successfully!\n\n' + JSON.stringify(values, null, 2))
  } catch (error) {
    alert('Submission failed: ' + error.message)
  } finally {
    isSubmitting.value = false
  }
})
</script>
```

**6. Add Component Test to Application**

**Update LoginView to show the form components test:**
Update `client/src/views/LoginView.vue`:
```vue
<template>
  <div>
    <!-- Previous test components -->
    <TestValidationForm />
    
    <!-- New form components test -->
    <TestFormComponents />
  </div>
</template>

<script setup>
import TestValidationForm from '../components/TestValidationForm.vue'
import TestFormComponents from '../components/TestFormComponents.vue'
</script>
```

**Expected Component Testing Results:**

**Navigate to `/login` and test:**

1. **BaseInput functionality:**
   - Visual validation states (red/green borders)
   - Password visibility toggle
   - Help text and error messages
   - Accessibility features (screen reader friendly)

2. **BaseButton states:**
   - Loading spinner during form submission
   - Disabled state when form invalid
   - Different variants and sizes work

3. **PasswordStrengthIndicator:**
   - Real-time strength calculation
   - Visual progress bars
   - Requirements checklist updates
   - Color coding for strength levels

4. **Layout components:**
   - FormCard provides consistent container
   - FormGroup handles spacing properly
   - Responsive behavior on mobile

**Key Decision Points Made for TaskFlow:**

**Component design choices:**
- **VeeValidate integration**: Components work seamlessly with validation
- **Accessibility first**: Proper ARIA labels and semantic HTML
- **Visual feedback**: Clear validation states and password strength
- **TaskFlow branding**: Consistent colors and styling
- **Mobile-responsive**: Works well on all screen sizes

**Performance considerations:**
- **Computed properties**: Efficient class calculations
- **Minimal re-renders**: Only update when validation state changes
- **Lazy validation**: Components don't validate until interaction

**Verification Steps:**
1. **Component integration**: All components work with Zod schemas
2. **Accessibility testing**: Proper focus management and screen reader support
3. **Visual validation**: Border colors and icons change appropriately
4. **Password features**: Strength indicator and visibility toggle work
5. **Form submission**: Loading states and validation work correctly
6. **Responsive design**: Components work on mobile and desktop

**Troubleshooting Common Issues:**

**Components not validating:**
- Check that field names match schema properties
- Ensure useField is called with correct name parameter
- Verify schema is passed to useForm correctly

**Styling issues:**
- Confirm Tailwind CSS classes are working
- Check that taskflow-primary color is defined
- Verify computed classes are generating correctly

**Accessibility concerns:**
- Test with keyboard navigation only
- Use screen reader to verify announcements
- Check color contrast meets WCAG standards

**2. Base Form Components Development**
- Create input components that integrate with VeeValidate Field
- Implement proper error state styling and messaging
- Add support for different input types (text, email, password, etc.)
- Ensure components work with Zod schema validation

**3. Accessibility Implementation**
- Add proper ARIA labels and descriptions
- Implement keyboard navigation support
- Ensure screen reader compatibility
- Test with accessibility tools and guidelines

**4. Styling and UX Polish**
- Create consistent visual states (default, focus, error, disabled)
- Add loading states for form submission
- Implement smooth transitions and animations
- Test components across different screen sizes

**Key Decision Points:**
- **Component granularity:** Level of abstraction for different form elements
- **Styling approach:** Tailwind classes vs. CSS-in-JS vs. scoped styles
- **Validation feedback:** When and how to show validation errors
- **Accessibility level:** Basic compliance vs. enhanced accessibility features

**Verification Steps:**
1. Test components with various validation scenarios
2. Verify accessibility with screen readers and keyboard navigation
3. Confirm components work correctly across different browsers
4. Check that styling is consistent and responsive

---
- [ ] Components well-documented

---

### Task 3.4: User Registration Form
**Objective:** Build TaskFlow's complete user registration form using all the components and validation from previous tasks

**What you need to accomplish:**
- Create a production-ready registration form for TaskFlow
- Integrate all form components from Task 3.3 with Zod schema from Task 3.2
- Implement comprehensive form submission with loading states and error handling
- Add enhanced user experience features like social login buttons and terms agreement

**Documentation to consult:**
- [VeeValidate useForm](https://vee-validate.logaretm.com/v4/api/use-form)
- [Vue 3 Composables](https://vuejs.org/guide/reusability/composables.html)
- [Form UX Best Practices](https://uxdesign.cc/design-better-forms-96fadca0f49c)

**Acceptance criteria:**
- [ ] Complete TaskFlow registration form with all fields from schema
- [ ] Terms and conditions checkbox integration
- [ ] Social login buttons (Google, GitHub) with proper styling
- [ ] Form submission with realistic API simulation
- [ ] Success and error state handling
- [ ] Mobile-responsive design matching TaskFlow mockups

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] Tasks 3.1, 3.2, and 3.3 completed successfully
- [ ] All form components working (BaseInput, BaseButton, PasswordStrengthIndicator, etc.)
- [ ] Zod authentication schemas created and tested
- [ ] Understanding of TaskFlow's registration requirements from mockups

### Step-by-Step Implementation Approach

**1. Create TaskFlow Registration Form Component**

**Create the complete registration form:**
Create `client/src/components/forms/RegistrationForm.vue`:
```vue
<template>
  <div class="min-h-screen bg-gray-50 flex items-center justify-center py-12 px-4 sm:px-6 lg:px-8">
    <FormCard 
      title="Create Your Account" 
      subtitle="Join TaskFlow and start managing your projects today"
      maxWidth="md"
    >
      <!-- Registration form -->
      <form @submit="onSubmit" class="space-y-6">
        <FormGroup spacing="lg">
          <!-- Name fields in responsive grid -->
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <BaseInput 
              name="firstName"
              label="First Name"
              placeholder="Enter your first name"
              autocomplete="given-name"
              required
            />
            <BaseInput 
              name="lastName"
              label="Last Name"
              placeholder="Enter your last name"
              autocomplete="family-name"
              required
            />
          </div>
          
          <!-- Email field -->
          <BaseInput 
            name="email"
            type="email"
            label="Email Address"
            placeholder="Enter your email address"
            autocomplete="email"
            help="We'll use this to send you important account updates"
            required
          />
          
          <!-- Password with strength indicator -->
          <div class="space-y-3">
            <BaseInput 
              name="password"
              type="password"
              label="Password"
              placeholder="Create a strong password"
              autocomplete="new-password"
              required
            />
            <PasswordStrengthIndicator name="password" />
          </div>
          
          <!-- Confirm password -->
          <BaseInput 
            name="confirmPassword"
            type="password"
            label="Confirm Password"
            placeholder="Confirm your password"
            autocomplete="new-password"
            required
          />
          
          <!-- Terms agreement checkbox -->
          <BaseCheckbox
            name="agreeToTerms"
            required
          >
            <template #label>
              <span class="text-sm text-gray-700">
                I agree to TaskFlow's
                <router-link to="/terms" class="text-blue-600 hover:text-blue-500 underline">
                  Terms of Service
                </router-link>
                and
                <router-link to="/privacy" class="text-blue-600 hover:text-blue-500 underline">
                  Privacy Policy
                </router-link>
              </span>
            </template>
          </BaseCheckbox>
          
          <!-- Submit button -->
          <BaseButton 
            type="submit"
            :disabled="!meta.valid || !agreeToTerms"
            :loading="isSubmitting"
            variant="primary"
            size="lg"
            fullWidth
            :loadingText="'Creating Your Account...'"
          >
            Create Account
          </BaseButton>
        </FormGroup>
      </form>
      
      <!-- Divider -->
      <div class="mt-6">
        <div class="relative">
          <div class="absolute inset-0 flex items-center">
            <div class="w-full border-t border-gray-300" />
          </div>
          <div class="relative flex justify-center text-sm">
            <span class="px-2 bg-white text-gray-500">Or continue with</span>
          </div>
        </div>
      </div>
      
      <!-- Social login buttons -->
      <div class="mt-6 grid grid-cols-1 gap-3 sm:grid-cols-2">
        <BaseButton
          type="button"
          variant="outline"
          size="md"
          fullWidth
          :disabled="isSubmitting"
          @click="handleSocialLogin('google')"
        >
          <template #icon>
            <svg class="h-5 w-5" viewBox="0 0 24 24">
              <path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/>
              <path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/>
              <path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/>
              <path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/>
            </svg>
          </template>
          Continue with Google
        </BaseButton>
        
        <BaseButton
          type="button"
          variant="outline"
          size="md"
          fullWidth
          :disabled="isSubmitting"
          @click="handleSocialLogin('github')"
        >
          <template #icon>
            <svg class="h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M10 0C4.477 0 0 4.484 0 10.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0110 4.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.203 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.942.359.31.678.921.678 1.856 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0020 10.017C20 4.484 15.522 0 10 0z" clip-rule="evenodd"/>
            </svg>
          </template>
          Continue with GitHub
        </BaseButton>
      </div>
      
      <!-- Footer with login link -->
      <template #footer>
        <div class="text-center">
          <p class="text-sm text-gray-600">
            Already have an account?
            <router-link to="/login" class="font-medium text-blue-600 hover:text-blue-500">
              Sign in
            </router-link>
          </p>
        </div>
      </template>
      
      <!-- Success/Error Messages -->
      <div v-if="submitMessage" class="mt-4">
        <div 
          v-if="submitSuccess" 
          class="rounded-md bg-green-50 p-4 border border-green-200"
        >
          <div class="flex">
            <div class="flex-shrink-0">
              <svg class="h-5 w-5 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
            </div>
            <div class="ml-3">
              <p class="text-sm font-medium text-green-800">{{ submitMessage }}</p>
            </div>
          </div>
        </div>
        
        <div 
          v-else 
          class="rounded-md bg-red-50 p-4 border border-red-200"
        >
          <div class="flex">
            <div class="flex-shrink-0">
              <svg class="h-5 w-5 text-red-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.864-.833-2.634 0L3.98 16.5c-.77.833.192 2.5 1.732 2.5z" />
              </svg>
            </div>
            <div class="ml-3">
              <p class="text-sm font-medium text-red-800">{{ submitMessage }}</p>
            </div>
          </div>
        </div>
      </div>
    </FormCard>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { useForm, useField } from 'vee-validate'
import { toTypedSchema } from '@vee-validate/zod'
import { registrationSchema } from '../../schemas/auth.js'

// Import components
import BaseInput from '../base/BaseInput.vue'
import BaseButton from '../base/BaseButton.vue'
import BaseCheckbox from '../base/BaseCheckbox.vue'
import PasswordStrengthIndicator from '../base/PasswordStrengthIndicator.vue'
import FormGroup from '../base/FormGroup.vue'
import FormCard from '../base/FormCard.vue'

// Router for navigation after success
const router = useRouter()

// Form setup with Zod schema
const validationSchema = toTypedSchema(registrationSchema)
const { handleSubmit, resetForm, meta, errors } = useForm({
  validationSchema,
  initialValues: {
    firstName: '',
    lastName: '',
    email: '',
    password: '',
    confirmPassword: '',
    agreeToTerms: false
  }
})

// Get agreeToTerms field for submit button logic
const { value: agreeToTerms } = useField('agreeToTerms')

// Form submission state
const isSubmitting = ref(false)
const submitMessage = ref('')
const submitSuccess = ref(false)

// Form submission handler
const onSubmit = handleSubmit(async (values) => {
  isSubmitting.value = true
  submitMessage.value = ''
  
  try {
    // Simulate API call to register user
    console.log('Registering user with data:', values)
    
    // Simulate network delay
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    // Simulate potential API errors (uncomment to test error handling)
    // if (values.email === 'test@error.com') {
    //   throw new Error('This email address is already registered')
    // }
    
    // Success handling
    submitSuccess.value = true
    submitMessage.value = 'Account created successfully! Redirecting to dashboard...'
    
    // Reset form
    resetForm()
    
    // Simulate redirect to dashboard after success
    setTimeout(() => {
      router.push('/dashboard')
    }, 2000)
    
  } catch (error) {
    // Error handling
    submitSuccess.value = false
    submitMessage.value = error.message || 'Registration failed. Please try again.'
    
    // Focus first field with error or first field
    const firstErrorField = Object.keys(errors.value)[0]
    if (firstErrorField) {
      const fieldElement = document.querySelector(`[name="${firstErrorField}"]`)
      if (fieldElement) fieldElement.focus()
    }
  } finally {
    isSubmitting.value = false
  }
})

// Social login handlers
const handleSocialLogin = async (provider) => {
  console.log(`Initiating ${provider} login...`)
  
  try {
    // Simulate social login process
    submitMessage.value = `Redirecting to ${provider} login...`
    
    // In a real app, this would redirect to OAuth provider
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // Simulate successful social login
    submitSuccess.value = true
    submitMessage.value = `Successfully signed in with ${provider}! Redirecting...`
    
    setTimeout(() => {
      router.push('/dashboard')
    }, 1500)
    
  } catch (error) {
    submitSuccess.value = false
    submitMessage.value = `${provider} login failed. Please try again.`
  }
}
</script>
```

**2. Create BaseCheckbox Component**

**We need a checkbox component for the terms agreement:**
Create `client/src/components/base/BaseCheckbox.vue`:
```vue
<template>
  <div class="flex items-start">
    <div class="flex items-center h-5">
      <input
        :id="inputId"
        :name="name"
        type="checkbox"
        :disabled="disabled"
        :class="checkboxClasses"
        v-model="value"
        :aria-describedby="ariaDescribedBy"
        :aria-invalid="!!errorMessage"
      />
    </div>
    <div class="ml-3 text-sm">
      <label :for="inputId" class="cursor-pointer">
        <slot name="label">
          {{ label }}
          <span v-if="required" class="text-red-500 ml-1">*</span>
        </slot>
      </label>
      
      <!-- Error message -->
      <p 
        v-if="errorMessage" 
        :id="errorId"
        class="text-red-600 mt-1"
        role="alert"
      >
        {{ errorMessage }}
      </p>
      
      <!-- Help text -->
      <p 
        v-if="help && !errorMessage" 
        :id="helpId"
        class="text-gray-500 mt-1"
      >
        {{ help }}
      </p>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import { useField } from 'vee-validate'

const props = defineProps({
  name: {
    type: String,
    required: true
  },
  label: String,
  help: String,
  disabled: Boolean,
  required: Boolean
})

// Generate unique IDs for accessibility
const inputId = ref(`checkbox-${props.name}-${Math.random().toString(36).substr(2, 9)}`)
const errorId = ref(`error-${props.name}-${Math.random().toString(36).substr(2, 9)}`)
const helpId = ref(`help-${props.name}-${Math.random().toString(36).substr(2, 9)}`)

// VeeValidate integration
const { value, errorMessage } = useField(props.name)

// Dynamic checkbox classes
const checkboxClasses = computed(() => {
  const baseClasses = [
    'h-4', 'w-4', 'rounded', 'border-gray-300', 'text-blue-600',
    'focus:ring-blue-500', 'transition-colors', 'duration-200'
  ]
  
  if (errorMessage.value) {
    baseClasses.push('border-red-500', 'text-red-600', 'focus:ring-red-500')
  }
  
  if (props.disabled) {
    baseClasses.push('cursor-not-allowed', 'opacity-50')
  } else {
    baseClasses.push('cursor-pointer')
  }
  
  return baseClasses.join(' ')
})

// ARIA describedby for accessibility
const ariaDescribedBy = computed(() => {
  const descriptions = []
  if (errorMessage.value) descriptions.push(errorId.value)
  if (props.help && !errorMessage.value) descriptions.push(helpId.value)
  return descriptions.length > 0 ? descriptions.join(' ') : undefined
})
</script>
```

**3. Add Registration Form to Router**

**Create a dedicated registration page:**
Create `client/src/views/RegisterView.vue`:
```vue
<template>
  <div>
    <RegistrationForm />
  </div>
</template>

<script setup>
import RegistrationForm from '../components/forms/RegistrationForm.vue'
</script>
```

**Update your router to include the registration route:**
Update `client/src/router/index.js`:
```javascript
import { createRouter, createWebHistory } from 'vue-router'
import HomeView from '../views/HomeView.vue'
import LoginView from '../views/LoginView.vue'
import RegisterView from '../views/RegisterView.vue' // Add this import

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView
    },
    {
      path: '/login',
      name: 'login',
      component: LoginView
    },
    {
      path: '/register', // Add registration route
      name: 'register',
      component: RegisterView
    }
  ]
})

export default router
```

**4. Test the Complete Registration Form**

**Start your development server:**
```bash
cd client/
npm run dev
```

**Navigate to `/register` and test:**

**1. Form validation testing:**
- Try submitting empty form → All required field errors should show
- Fill invalid email → Email validation error should show
- Create weak password → Password strength indicator shows "weak"
- Create strong password → Password strength shows "strong" 
- Enter different confirm password → "Passwords do not match" error
- Leave terms unchecked → Submit button remains disabled
- Check terms → Submit button becomes enabled

**2. Form submission testing:**
- Fill form correctly → Submit button shows loading spinner
- Successful submission → Green success message shows
- Test error handling → Change email to "test@error.com" and uncomment error simulation

**3. Social login testing:**
- Click Google button → Shows "Redirecting to Google login..."
- Click GitHub button → Shows "Redirecting to GitHub login..."

**4. Responsive design testing:**
- Test on mobile (320px) → Form should stack properly
- Test on tablet (768px) → Name fields should be side-by-side
- Test on desktop (1024px+) → Form should be centered with proper max-width

**Expected behavior:**
- ✅ All validation from Zod schema works correctly
- ✅ Password strength indicator updates in real-time
- ✅ Terms checkbox controls submit button state
- ✅ Loading states work during form submission
- ✅ Success/error messages display appropriately
- ✅ Social login buttons trigger proper handling
- ✅ Form is mobile-responsive
- ✅ Accessibility features work (keyboard navigation, screen readers)

**Understanding the Complete Registration Implementation:**

**Form structure highlights:**
- **Responsive grid layout**: Name fields stack on mobile, side-by-side on larger screens
- **Progressive enhancement**: Form works without JavaScript, enhanced with validation
- **Accessibility first**: Proper labels, ARIA attributes, focus management
- **Visual feedback**: Loading states, success/error messages, validation icons
- **User experience**: Auto-focus on errors, clear success flow

**Zod schema integration:**
- **Cross-field validation**: Password confirmation works automatically
- **Complex validation**: All password requirements validated
- **Data transformation**: Email normalized to lowercase
- **Type safety**: Form data fully validated before submission

**State management:**
- **Loading states**: Submit button shows spinner during submission
- **Error handling**: Network errors displayed to user
- **Success handling**: Clear success message with redirect
- **Form reset**: Clean form after successful submission

**Key Decision Points Made for TaskFlow:**

**User experience choices:**
- **Terms checkbox controls submit**: Users must agree before submitting
- **Social login prominence**: Easy alternative registration methods
- **Clear error messaging**: Specific, actionable error messages
- **Loading feedback**: Users know when form is processing

**Technical architecture:**
- **Component composition**: Reusable components from Task 3.3
- **Validation architecture**: Zod schemas from Task 3.2
- **State management**: Local component state for form-specific logic
- **Navigation integration**: Vue Router for post-registration flow

**Verification Steps:**
1. **All form fields validate**: Names, email, passwords, terms checkbox
2. **Cross-field validation**: Password confirmation works
3. **Form submission**: Loading, success, and error states work
4. **Social authentication**: Buttons trigger appropriate handlers
5. **Responsive behavior**: Form works on all screen sizes
6. **Accessibility**: Keyboard navigation and screen reader support
7. **Error recovery**: Users can fix errors and resubmit

**Troubleshooting Common Issues:**

**Form not validating:**
- Check that all field names match registrationSchema properties exactly
- Verify toTypedSchema is properly converting Zod schema
- Ensure all required components are imported correctly

**Submit button not enabling:**
- Verify agreeToTerms field is properly connected
- Check that meta.valid is working with form validation
- Ensure BaseCheckbox component is working properly

**Styling issues:**
- Confirm all Tailwind CSS classes are available
- Check that FormCard and FormGroup components render correctly
- Verify responsive grid classes work on different screen sizes

**Social login not working:**
- Check handleSocialLogin function is properly bound
- Verify button click handlers are attached correctly
- Ensure router navigation works in the test environment

---

### Task 3.5: Login Form and Form Utilities
**Objective:** Complete TaskFlow's authentication system by building a production-ready login form and reusable form utilities

**What you need to accomplish:**
- Build TaskFlow's complete login form using all components from previous tasks
- Create reusable form composables for better code organization
- Implement "Remember me" functionality with localStorage integration
- Add comprehensive error handling and user feedback
- Create form utilities that can be reused across TaskFlow

**Documentation to consult:**
- [Vue 3 Composables Pattern](https://vuejs.org/guide/reusability/composables.html)
- [Local Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
- [VeeValidate Composables](https://vee-validate.logaretm.com/v4/guide/composition-api/getting-started)

**Acceptance criteria:**
- [ ] Complete TaskFlow login form with social login options
- [ ] Reusable form composables created (useFormSubmission, useRememberMe)
- [ ] "Remember me" functionality with localStorage
- [ ] Forgot password flow integration
- [ ] Error handling with user-friendly messages
- [ ] Mobile-responsive design matching TaskFlow mockups

## Implementation Guidance

### Getting Started
Before beginning this task, ensure you have:
- [ ] All previous tasks (3.1-3.4) completed successfully
- [ ] Registration form working and tested
- [ ] All form components available (BaseInput, BaseButton, BaseCheckbox, etc.)
- [ ] Understanding of TaskFlow's login requirements and user flow

### Step-by-Step Implementation Approach

**1. Create TaskFlow Login Form Component**

**Create the complete login form:**
Create `client/src/components/forms/LoginForm.vue`:
```vue
<template>
  <div class="min-h-screen bg-gray-50 flex items-center justify-center py-12 px-4 sm:px-6 lg:px-8">
    <FormCard 
      title="Welcome Back" 
      subtitle="Sign in to your TaskFlow account"
      maxWidth="sm"
    >
      <!-- Login form -->
      <form @submit="onSubmit" class="space-y-6">
        <FormGroup spacing="lg">
          <!-- Email field -->
          <BaseInput 
            name="email"
            type="email"
            label="Email Address"
            placeholder="Enter your email address"
            autocomplete="email"
            required
          />
          
          <!-- Password field -->
          <BaseInput 
            name="password"
            type="password"
            label="Password"
            placeholder="Enter your password"
            autocomplete="current-password"
            required
          />
          
          <!-- Remember me and forgot password -->
          <div class="flex items-center justify-between">
            <BaseCheckbox
              name="rememberMe"
              label="Remember me"
              :help="rememberMeHelp"
            />
            
            <router-link
              to="/forgot-password"
              class="text-sm font-medium text-blue-600 hover:text-blue-500 transition-colors"
            >
              Forgot your password?
            </router-link>
          </div>
          
          <!-- Submit button -->
          <BaseButton 
            type="submit"
            :disabled="!meta.valid"
            :loading="isSubmitting"
            variant="primary"
            size="lg"
            fullWidth
            :loadingText="'Signing you in...'"
          >
            Sign In
          </BaseButton>
        </FormGroup>
      </form>
      
      <!-- Divider -->
      <div class="mt-6">
        <div class="relative">
          <div class="absolute inset-0 flex items-center">
            <div class="w-full border-t border-gray-300" />
          </div>
          <div class="relative flex justify-center text-sm">
            <span class="px-2 bg-white text-gray-500">Or continue with</span>
          </div>
        </div>
      </div>
      
      <!-- Social login buttons -->
      <div class="mt-6 grid grid-cols-1 gap-3 sm:grid-cols-2">
        <BaseButton
          type="button"
          variant="outline"
          size="md"
          fullWidth
          :disabled="isSubmitting"
          @click="handleSocialLogin('google')"
        >
          <template #icon>
            <svg class="h-5 w-5" viewBox="0 0 24 24">
              <path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/>
              <path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/>
              <path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/>
              <path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/>
            </svg>
          </template>
          Google
        </BaseButton>
        
        <BaseButton
          type="button"
          variant="outline"
          size="md"
          fullWidth
          :disabled="isSubmitting"
          @click="handleSocialLogin('github')"
        >
          <template #icon>
            <svg class="h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
              <path fill-rule="evenodd" d="M10 0C4.477 0 0 4.484 0 10.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0110 4.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.203 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.942.359.31.678.921.678 1.856 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0020 10.017C20 4.484 15.522 0 10 0z" clip-rule="evenodd"/>
            </svg>
          </template>
          GitHub
        </BaseButton>
      </div>
      
      <!-- Footer with registration link -->
      <template #footer>
        <div class="text-center">
          <p class="text-sm text-gray-600">
            Don't have an account?
            <router-link to="/register" class="font-medium text-blue-600 hover:text-blue-500">
              Create account
            </router-link>
          </p>
        </div>
      </template>
      
      <!-- Success/Error Messages -->
      <div v-if="submitMessage" class="mt-4">
        <div 
          v-if="submitSuccess" 
          class="rounded-md bg-green-50 p-4 border border-green-200"
        >
          <div class="flex">
            <div class="flex-shrink-0">
              <svg class="h-5 w-5 text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
            </div>
            <div class="ml-3">
              <p class="text-sm font-medium text-green-800">{{ submitMessage }}</p>
            </div>
          </div>
        </div>
        
        <div 
          v-else 
          class="rounded-md bg-red-50 p-4 border border-red-200"
        >
          <div class="flex">
            <div class="flex-shrink-0">
              <svg class="h-5 w-5 text-red-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.864-.833-2.634 0L3.98 16.5c-.77.833.192 2.5 1.732 2.5z" />
              </svg>
            </div>
            <div class="ml-3">
              <p class="text-sm font-medium text-red-800">{{ submitMessage }}</p>
            </div>
          </div>
        </div>
      </div>
    </FormCard>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { useForm, useField } from 'vee-validate'
import { toTypedSchema } from '@vee-validate/zod'
import { loginSchema } from '../../schemas/auth.js'

// Import components
import BaseInput from '../base/BaseInput.vue'
import BaseButton from '../base/BaseButton.vue'
import BaseCheckbox from '../base/BaseCheckbox.vue'
import FormGroup from '../base/FormGroup.vue'
import FormCard from '../base/FormCard.vue'

// Import composables
import { useFormSubmission } from '../../composables/useFormSubmission.js'
import { useRememberMe } from '../../composables/useRememberMe.js'

// Router for navigation after success
const router = useRouter()

// Form setup with Zod schema
const validationSchema = toTypedSchema(loginSchema)
const { handleSubmit, resetForm, meta, errors, setValues } = useForm({
  validationSchema,
  initialValues: {
    email: '',
    password: '',
    rememberMe: false
  }
})

// Form submission composable
const { 
  isSubmitting, 
  submitMessage, 
  submitSuccess, 
  handleFormSubmission 
} = useFormSubmission()

// Remember me composable
const { 
  loadRememberedCredentials, 
  saveCredentials, 
  clearCredentials,
  getRememberMeHelp
} = useRememberMe()

// Remember me help text
const rememberMeHelp = computed(() => getRememberMeHelp())

// Load remembered credentials on mount
onMounted(() => {
  const remembered = loadRememberedCredentials()
  if (remembered) {
    setValues({
      email: remembered.email,
      password: '', // Never prefill password for security
      rememberMe: true
    })
  }
})

// Form submission handler
const onSubmit = handleSubmit(async (values) => {
  await handleFormSubmission(async () => {
    // Simulate API call to authenticate user
    console.log('Authenticating user with data:', { 
      email: values.email, 
      rememberMe: values.rememberMe 
      // Note: Don't log password in real app
    })
    
    // Simulate network delay
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    // Simulate potential API errors (uncomment to test error handling)
    // if (values.email === 'test@error.com') {
    //   throw new Error('Invalid email or password. Please try again.')
    // }
    
    // Handle remember me functionality
    if (values.rememberMe) {
      saveCredentials(values.email)
    } else {
      clearCredentials()
    }
    
    // Success handling
    submitSuccess.value = true
    submitMessage.value = 'Login successful! Redirecting to dashboard...'
    
    // Simulate successful login redirect
    setTimeout(() => {
      router.push('/dashboard')
    }, 1500)
    
    // Reset form for security
    resetForm()
  }, {
    successMessage: 'Login successful! Redirecting...',
    errorPrefix: 'Login failed: '
  })
})

// Social login handlers
const handleSocialLogin = async (provider) => {
  await handleFormSubmission(async () => {
    console.log(`Initiating ${provider} login...`)
    
    // Simulate social login process
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // Simulate successful social login
    setTimeout(() => {
      router.push('/dashboard')
    }, 1000)
  }, {
    successMessage: `Successfully signed in with ${provider}! Redirecting...`,
    errorPrefix: `${provider} login failed: `
  })
}
</script>
```

**2. Create Form Submission Composable**

**Create reusable form submission logic:**
Create `client/src/composables/useFormSubmission.js`:
```javascript
import { ref } from 'vue'

/**
 * Composable for handling form submission states and error management
 * Provides consistent loading, success, and error handling across forms
 */
export function useFormSubmission() {
  // Reactive state
  const isSubmitting = ref(false)
  const submitMessage = ref('')
  const submitSuccess = ref(false)
  
  /**
   * Handle form submission with consistent error handling
   * @param {Function} submitFunction - Async function to execute
   * @param {Object} options - Configuration options
   */
  const handleFormSubmission = async (submitFunction, options = {}) => {
    const {
      successMessage = 'Operation completed successfully!',
      errorPrefix = 'Operation failed: ',
      resetOnSuccess = false
    } = options
    
    // Reset state
    isSubmitting.value = true
    submitMessage.value = ''
    submitSuccess.value = false
    
    try {
      // Execute the form submission function
      await submitFunction()
      
      // Success handling
      if (!submitMessage.value) { // Only set if not already set by submitFunction
        submitSuccess.value = true
        submitMessage.value = successMessage
      }
      
    } catch (error) {
      // Error handling
      submitSuccess.value = false
      
      // Handle different error types
      let errorMessage = 'An unexpected error occurred. Please try again.'
      
      if (error.message) {
        errorMessage = error.message
      } else if (error.response?.data?.message) {
        errorMessage = error.response.data.message
      } else if (typeof error === 'string') {
        errorMessage = error
      }
      
      submitMessage.value = errorPrefix + errorMessage
      
      // Focus management for accessibility
      setTimeout(() => {
        const errorElement = document.querySelector('[role="alert"]')
        if (errorElement) {
          errorElement.focus()
        }
      }, 100)
      
    } finally {
      isSubmitting.value = false
    }
  }
  
  /**
   * Clear all submission state
   */
  const clearSubmissionState = () => {
    isSubmitting.value = false
    submitMessage.value = ''
    submitSuccess.value = false
  }
  
  /**
   * Set a custom error message
   * @param {string} message - Error message to display
   */
  const setError = (message) => {
    submitSuccess.value = false
    submitMessage.value = message
    isSubmitting.value = false
  }
  
  /**
   * Set a custom success message
   * @param {string} message - Success message to display
   */
  const setSuccess = (message) => {
    submitSuccess.value = true
    submitMessage.value = message
    isSubmitting.value = false
  }
  
  return {
    // Reactive state
    isSubmitting,
    submitMessage,
    submitSuccess,
    
    // Methods
    handleFormSubmission,
    clearSubmissionState,
    setError,
    setSuccess
  }
}
```

**3. Create Remember Me Composable**

**Create localStorage-based remember me functionality:**
Create `client/src/composables/useRememberMe.js`:
```javascript
import { ref } from 'vue'

const STORAGE_KEY = 'taskflow_remember_me'

/**
 * Composable for handling "Remember me" functionality with localStorage
 * Securely stores user email (never password) for convenience
 */
export function useRememberMe() {
  const isRemembered = ref(false)
  
  /**
   * Save user credentials to localStorage (email only for security)
   * @param {string} email - User's email address
   */
  const saveCredentials = (email) => {
    try {
      const credentials = {
        email,
        timestamp: Date.now()
      }
      localStorage.setItem(STORAGE_KEY, JSON.stringify(credentials))
      isRemembered.value = true
      
      console.log('Credentials saved for remember me functionality')
    } catch (error) {
      console.warn('Failed to save credentials to localStorage:', error)
    }
  }
  
  /**
   * Load remembered credentials from localStorage
   * @returns {Object|null} Stored credentials or null
   */
  const loadRememberedCredentials = () => {
    try {
      const stored = localStorage.getItem(STORAGE_KEY)
      if (!stored) return null
      
      const credentials = JSON.parse(stored)
      
      // Check if credentials are expired (30 days)
      const thirtyDaysAgo = Date.now() - (30 * 24 * 60 * 60 * 1000)
      if (credentials.timestamp < thirtyDaysAgo) {
        clearCredentials()
        return null
      }
      
      isRemembered.value = true
      return credentials
      
    } catch (error) {
      console.warn('Failed to load remembered credentials:', error)
      clearCredentials() // Clear invalid data
      return null
    }
  }
  
  /**
   * Clear remembered credentials from localStorage
   */
  const clearCredentials = () => {
    try {
      localStorage.removeItem(STORAGE_KEY)
      isRemembered.value = false
      console.log('Remembered credentials cleared')
    } catch (error) {
      console.warn('Failed to clear remembered credentials:', error)
    }
  }
  
  /**
   * Check if credentials are currently remembered
   * @returns {boolean} Whether credentials are remembered
   */
  const hasRememberedCredentials = () => {
    return loadRememberedCredentials() !== null
  }
  
  /**
   * Get help text for remember me checkbox
   * @returns {string} Help text explaining the feature
   */
  const getRememberMeHelp = () => {
    return 'We\'ll save your email address for faster sign-in next time'
  }
  
  /**
   * Get remembered email if available
   * @returns {string|null} Remembered email or null
   */
  const getRememberedEmail = () => {
    const credentials = loadRememberedCredentials()
    return credentials ? credentials.email : null
  }
  
  return {
    // Reactive state
    isRemembered,
    
    // Methods
    saveCredentials,
    loadRememberedCredentials,
    clearCredentials,
    hasRememberedCredentials,
    getRememberMeHelp,
    getRememberedEmail
  }
}
```

**4. Update LoginView and Router**

**Create dedicated login page:**
Update `client/src/views/LoginView.vue`:
```vue
<template>
  <div>
    <!-- Replace test components with production login form -->
    <LoginForm />
  </div>
</template>

<script setup>
import LoginForm from '../components/forms/LoginForm.vue'
</script>
```

**Update router to handle login redirection:**
Update `client/src/router/index.js` to add route guards:
```javascript
import { createRouter, createWebHistory } from 'vue-router'
import HomeView from '../views/HomeView.vue'
import LoginView from '../views/LoginView.vue'
import RegisterView from '../views/RegisterView.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView
    },
    {
      path: '/login',
      name: 'login',
      component: LoginView,
      meta: {
        requiresGuest: true // Redirect if already logged in
      }
    },
    {
      path: '/register',
      name: 'register',
      component: RegisterView,
      meta: {
        requiresGuest: true
      }
    },
    {
      path: '/dashboard',
      name: 'dashboard',
      // In a real app, this would be a proper dashboard component
      component: () => import('../views/DashboardView.vue'),
      meta: {
        requiresAuth: true // Require authentication
      }
    },
    {
      path: '/forgot-password',
      name: 'forgot-password',
      // Placeholder for forgot password functionality
      redirect: '/login' // For now, redirect back to login
    }
  ]
})

// Simple route guards (in a real app, you'd check actual auth state)
router.beforeEach((to, from, next) => {
  // For demo purposes, we'll just log navigation
  console.log(`Navigating from ${from.name} to ${to.name}`)
  
  // In a real app, you'd check authentication state here
  // if (to.meta.requiresAuth && !isAuthenticated()) {
  //   return next('/login')
  // }
  // if (to.meta.requiresGuest && isAuthenticated()) {
  //   return next('/dashboard')
  // }
  
  next()
})

export default router
```

**5. Create Simple Dashboard View for Testing**

**Create a placeholder dashboard:**
Create `client/src/views/DashboardView.vue`:
```vue
<template>
  <div class="min-h-screen bg-gray-50">
    <div class="max-w-7xl mx-auto py-6 sm:px-6 lg:px-8">
      <div class="px-4 py-6 sm:px-0">
        <div class="bg-white rounded-lg shadow p-6">
          <h1 class="text-3xl font-bold text-gray-900 mb-4">
            Welcome to TaskFlow Dashboard! 🎉
          </h1>
          <p class="text-gray-600 mb-6">
            You have successfully logged in. In a real application, this would be your project management dashboard.
          </p>
          
          <div class="flex space-x-4">
            <BaseButton
              variant="outline"
              @click="$router.push('/login')"
            >
              Back to Login
            </BaseButton>
            
            <BaseButton
              variant="outline"
              @click="$router.push('/register')"
            >
              Back to Register
            </BaseButton>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import BaseButton from '../components/base/BaseButton.vue'
</script>
```

**6. Test the Complete Authentication System**

**Start your development server:**
```bash
cd client/
npm run dev
```

**Test the complete authentication flow:**

**1. Login form validation:**
- Navigate to `/login`
- Try submitting empty form → Email and password required errors
- Enter invalid email → Email validation error
- Enter valid credentials → Form should submit

**2. Remember me functionality:**
- Check "Remember me" checkbox
- Submit form successfully
- Navigate back to `/login` → Email should be pre-filled
- Uncheck "Remember me" → Email should not be saved next time

**3. Social login:**
- Click Google/GitHub buttons → Should show loading and redirect to dashboard

**4. Navigation flow:**
- Successful login → Redirects to `/dashboard`
- Dashboard shows success message
- Links work to navigate between forms

**5. Error handling:**
- Uncomment error simulation in LoginForm.vue
- Use email "test@error.com" → Should show error message

**6. Responsive testing:**
- Test on mobile → Form should be mobile-optimized
- Test on desktop → Form should be centered with proper sizing

**Expected behavior:**
- ✅ Login form validates with Zod schema
- ✅ Remember me saves/loads email from localStorage
- ✅ Form submission shows loading states
- ✅ Success/error messages display correctly
- ✅ Social login buttons work
- ✅ Navigation between login/register/dashboard works
- ✅ Mobile-responsive design
- ✅ Accessibility features work

**Understanding the Complete Implementation:**

**Form composables benefits:**
- **useFormSubmission**: Consistent error handling across all forms
- **useRememberMe**: Secure credential persistence with expiration
- **Reusability**: Same composables can be used in other forms
- **Separation of concerns**: Business logic separated from UI logic

**Authentication flow:**
- **Login validation**: Email and password required with proper formatting
- **Remember me**: Saves only email (never password) with 30-day expiration
- **Success handling**: Clear feedback with automatic redirect
- **Error recovery**: Focus management and clear error messages

**Security considerations:**
- **No password storage**: Remember me only saves email address
- **Expiration handling**: Remembered credentials expire after 30 days
- **Error handling**: Generic error messages don't reveal system details
- **Form reset**: Sensitive data cleared after submission

**Key Decision Points Made for TaskFlow:**

**User experience:**
- **Remember me prominence**: Easy to find and understand
- **Social login options**: Quick alternative authentication
- **Clear navigation**: Easy to switch between login/register
- **Helpful error messages**: Specific, actionable feedback

**Technical architecture:**
- **Composable pattern**: Reusable business logic
- **Schema validation**: Consistent with registration form
- **State management**: Local component state with composables
- **Router integration**: Proper navigation flow

**Verification Steps:**
1. **Form validation**: Email and password fields validate correctly
2. **Remember me**: Email persistence works with localStorage
3. **Form submission**: Loading and success states work
4. **Error handling**: Network errors display properly
5. **Social authentication**: Buttons trigger correct flow
6. **Navigation**: Login → Dashboard → Back to forms works
7. **Responsive**: Works on all screen sizes
8. **Accessibility**: Keyboard navigation and screen reader support

**Troubleshooting Common Issues:**

**Remember me not working:**
- Check browser localStorage is enabled
- Verify STORAGE_KEY is consistent
- Check for localStorage quota exceeded

**Form composables not found:**
- Verify composables directory exists
- Check import paths are correct
- Ensure composables are properly exported

**Navigation not working:**
- Check router imports and setup
- Verify route names match navigation calls
- Ensure router-link components work

**Social login not triggering:**
- Check button click handlers are bound correctly
- Verify handleSocialLogin function exists
- Check for JavaScript errors in console

## Challenge Extensions

### Advanced Validation Features
- Implement async validation (email uniqueness)
- Add debounced validation for better UX
- Create custom validation rules
- Implement field-level validation timing

### Enhanced UX Features
- Add form auto-save functionality
- Implement smart form field suggestions
- Create multi-step form wizard
- Add form progress indicators

### Security Enhancements
- Implement CSRF protection
- Add rate limiting for form submissions
- Create password breach checking
- Implement secure password requirements

### Accessibility Improvements
- Add comprehensive ARIA support
- Implement keyboard navigation
- Create screen reader optimizations
- Add high contrast mode support

## Troubleshooting Common Issues

### VeeValidate Not Validating
- Check field names match schema keys
- Verify validation schema is properly imported
- Ensure Field components are used correctly
- Check for conflicting validation rules

### Zod Schema Errors
- Verify schema syntax is correct
- Check for TypeScript type mismatches
- Ensure proper error message configuration
- Test schema independently

### Form Submission Issues
- Check network requests in dev tools
- Verify form data structure
- Ensure proper error handling
- Test loading state management

### Styling Issues
- Verify Tailwind classes are applied
- Check component prop passing
- Ensure responsive design works
- Test different input states

## Module Completion Checklist

Before moving to Module 4, ensure:
- [ ] VeeValidate is properly configured
- [ ] Zod schemas work for all forms
- [ ] Reusable components function correctly
- [ ] Registration form validates properly
- [ ] Login form handles all cases
- [ ] Error handling provides good UX
- [ ] Forms are accessible and responsive
- [ ] Code is well-documented and tested

## Next Module Preview
Module 4 will focus on building the Express.js backend API that will handle form submissions, user authentication, and data management. You'll create RESTful endpoints that integrate with your frontend forms.