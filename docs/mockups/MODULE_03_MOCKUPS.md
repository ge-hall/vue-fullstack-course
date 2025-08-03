---
title: "Module 3: Forms & Validation Mockups"
description: "Visual designs for form layouts, validation states, and user flows"
layout: default
---

# Module 3: Forms & Validation Mockups

[← Back to Course](../) | [📚 View Module 3](../modules/MODULE_03_FORMS_VALIDATION.html)

## Application Context: TaskFlow User Authentication

In Module 3, you're building the user authentication system for **TaskFlow**. This includes registration and login forms with comprehensive validation, providing users a smooth onboarding experience.

### What You're Building This Module
- User registration form with real-time validation
- Login form with "Remember me" functionality
- Password strength indicators and validation feedback
- Professional form error handling and user experience

### User Stories for This Module
- **As a new user**, I want to register for TaskFlow so that I can create and manage my projects
- **As a returning user**, I want to log into TaskFlow so that I can access my existing projects and tasks
- **As a security-conscious user**, I want strong password requirements so that my account is protected

## UI Mockups

### 1. Registration Page (Desktop)

```
┌─────────────────────────────────────────────────────────────────────────┐
│ TaskFlow                                           [Already have account?]│
│ [Logo]                                                       [Sign In] │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│                    ┌─────────────────────────────────┐                   │
│                    │         Create Account          │                   │
│                    │                                 │                   │
│                    │ ┌─────────────┐ ┌─────────────┐ │                   │
│                    │ │First Name   │ │Last Name    │ │                   │
│                    │ │John         │ │Smith        │ │                   │
│                    │ └─────────────┘ └─────────────┘ │                   │
│                    │                                 │                   │
│                    │ ┌─────────────────────────────┐ │                   │
│                    │ │Email Address                │ │                   │
│                    │ │john@example.com         ✓   │ │                   │
│                    │ └─────────────────────────────┘ │                   │
│                    │                                 │                   │
│                    │ ┌─────────────────────────────┐ │                   │
│                    │ │Password                 👁   │ │                   │
│                    │ │••••••••••••••••             │ │                   │
│                    │ └─────────────────────────────┘ │                   │
│                    │                                 │                   │
│                    │ Password Strength:              │                   │
│                    │ ████████░░ Strong               │                   │
│                    │ ✓ 8+ characters                 │                   │
│                    │ ✓ Uppercase letter              │                   │
│                    │ ✓ Lowercase letter              │                   │
│                    │ ✓ Number                        │                   │
│                    │ ✗ Special character             │                   │
│                    │                                 │                   │
│                    │ ┌─────────────────────────────┐ │                   │
│                    │ │Confirm Password         ✓   │ │                   │
│                    │ │••••••••••••••••             │ │                   │
│                    │ └─────────────────────────────┘ │                   │
│                    │                                 │                   │
│                    │ ☑ I agree to the Terms of      │                   │
│                    │   Service and Privacy Policy   │                   │
│                    │                                 │                   │
│                    │ ┌─────────────────────────────┐ │                   │
│                    │ │     Create Account          │ │                   │
│                    │ └─────────────────────────────┘ │                   │
│                    │                                 │                   │
│                    │        ─── or ───               │                   │
│                    │                                 │                   │
│                    │ ┌─────────────────────────────┐ │                   │
│                    │ │ 🔵 Continue with Google     │ │                   │
│                    │ └─────────────────────────────┘ │                   │
│                    │ ┌─────────────────────────────┐ │                   │
│                    │ │ 📘 Continue with GitHub     │ │                   │
│                    │ └─────────────────────────────┘ │                   │
│                    │                                 │                   │
│                    └─────────────────────────────────┘                   │
│                                                                         │
├─────────────────────────────────────────────────────────────────────────┤
│ © 2024 TaskFlow  |  Privacy  |  Terms  |  Help                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2. Registration Form - Error States

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    │         Create Account          │                   │
│                    │                                 │                   │
│                    │ ┌─────────────┐ ┌─────────────┐ │                   │
│                    │ │First Name   │ │Last Name    │ │                   │
│                    │ │             │ │Smith        │ │                   │
│                    │ └─────────────┘ └─────────────┘ │                   │
│                    │ ⚠ First name is required        │                   │
│                    │                                 │                   │
│                    │ ┌─────────────────────────────┐ │                   │
│                    │ │Email Address            ✗   │ │                   │
│                    │ │john@invalid             🔴  │ │                   │
│                    │ └─────────────────────────────┘ │                   │
│                    │ ⚠ Please enter a valid email   │                   │
│                    │                                 │                   │
│                    │ ┌─────────────────────────────┐ │                   │
│                    │ │Password                 👁   │ │                   │
│                    │ │••••••                       │ │                   │
│                    │ └─────────────────────────────┘ │                   │
│                    │                                 │                   │
│                    │ Password Strength:              │                   │
│                    │ ███░░░░░░░ Weak                 │                   │
│                    │ ✗ 8+ characters                 │                   │
│                    │ ✗ Uppercase letter              │                   │
│                    │ ✓ Lowercase letter              │                   │
│                    │ ✗ Number                        │                   │
│                    │ ✗ Special character             │                   │
│                    │                                 │                   │
│                    │ ┌─────────────────────────────┐ │                   │
│                    │ │Confirm Password         ✗   │ │                   │
│                    │ │••••••••                 🔴  │ │                   │
│                    │ └─────────────────────────────┘ │                   │
│                    │ ⚠ Passwords do not match       │                   │
└─────────────────────────────────────────────────────────────────────────┘
```

### 3. Login Page (Desktop)

```
┌─────────────────────────────────────────────────────────────────────────┐
│ TaskFlow                                           [Don't have account?] │
│ [Logo]                                                    [Sign Up]    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│                      ┌─────────────────────────────┐                     │
│                      │       Welcome Back          │                     │
│                      │                             │                     │
│                      │ ┌─────────────────────────┐ │                     │
│                      │ │Email Address            │ │                     │
│                      │ │john@example.com         │ │                     │
│                      │ └─────────────────────────┘ │                     │
│                      │                             │                     │
│                      │ ┌─────────────────────────┐ │                     │
│                      │ │Password             👁  │ │                     │
│                      │ │••••••••••••••••         │ │                     │
│                      │ └─────────────────────────┘ │                     │
│                      │                             │                     │
│                      │ ☑ Remember me               │                     │
│                      │           [Forgot Password?]│                     │
│                      │                             │                     │
│                      │ ┌─────────────────────────┐ │                     │
│                      │ │       Sign In           │ │                     │
│                      │ └─────────────────────────┘ │                     │
│                      │                             │                     │
│                      │        ─── or ───           │                     │
│                      │                             │                     │
│                      │ ┌─────────────────────────┐ │                     │
│                      │ │ 🔵 Continue with Google │ │                     │
│                      │ └─────────────────────────┘ │                     │
│                      │ ┌─────────────────────────┐ │                     │
│                      │ │ 📘 Continue with GitHub │ │                     │
│                      │ └─────────────────────────┘ │                     │
│                      │                             │                     │
│                      └─────────────────────────────┘                     │
│                                                                         │
├─────────────────────────────────────────────────────────────────────────┤
│ © 2024 TaskFlow  |  Privacy  |  Terms  |  Help                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 4. Login Form - Loading State

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      │       Welcome Back          │                     │
│                      │                             │                     │
│                      │ ┌─────────────────────────┐ │                     │
│                      │ │Email Address            │ │                     │
│                      │ │john@example.com         │ │                     │
│                      │ └─────────────────────────┘ │                     │
│                      │                             │                     │
│                      │ ┌─────────────────────────┐ │                     │
│                      │ │Password             👁  │ │                     │
│                      │ │••••••••••••••••         │ │                     │
│                      │ └─────────────────────────┘ │                     │
│                      │                             │                     │
│                      │ ☑ Remember me               │                     │
│                      │           [Forgot Password?]│                     │
│                      │                             │                     │
│                      │ ┌─────────────────────────┐ │                     │
│                      │ │   ⟳ Signing In...      │ │ (disabled)          │
│                      │ └─────────────────────────┘ │                     │
│                      │                             │                     │
└─────────────────────────────────────────────────────────────────────────┘
```

### 5. Mobile Registration (320px - 767px)

```
┌─────────────────────────────────┐
│ ← TaskFlow                      │
├─────────────────────────────────┤
│                                 │
│      Create Your Account        │
│                                 │
│ ┌─────────────────────────────┐ │
│ │First Name                   │ │
│ │John                         │ │
│ └─────────────────────────────┘ │
│                                 │
│ ┌─────────────────────────────┐ │
│ │Last Name                    │ │
│ │Smith                        │ │
│ └─────────────────────────────┘ │
│                                 │
│ ┌─────────────────────────────┐ │
│ │Email Address            ✓   │ │
│ │john@example.com             │ │
│ └─────────────────────────────┘ │
│                                 │
│ ┌─────────────────────────────┐ │
│ │Password                 👁   │ │
│ │••••••••••••••••             │ │
│ └─────────────────────────────┘ │
│                                 │
│ Password Strength: Strong       │
│ ████████░░                      │
│                                 │
│ ┌─────────────────────────────┐ │
│ │Confirm Password         ✓   │ │
│ │••••••••••••••••             │ │
│ └─────────────────────────────┘ │
│                                 │
│ ☑ I agree to Terms & Privacy   │
│                                 │
│ ┌─────────────────────────────┐ │
│ │     Create Account          │ │
│ └─────────────────────────────┘ │
│                                 │
│ ───────── or ─────────          │
│                                 │
│ [🔵 Google] [📘 GitHub]        │
│                                 │
│ Already have an account?        │
│ [Sign In]                       │
│                                 │
└─────────────────────────────────┘
```

### 6. Mobile Login (320px - 767px)

```
┌─────────────────────────────────┐
│ ← TaskFlow                      │
├─────────────────────────────────┤
│                                 │
│        Welcome Back             │
│                                 │
│ ┌─────────────────────────────┐ │
│ │Email Address                │ │
│ │john@example.com             │ │
│ └─────────────────────────────┘ │
│                                 │
│ ┌─────────────────────────────┐ │
│ │Password                 👁   │ │
│ │••••••••••••••••             │ │
│ └─────────────────────────────┘ │
│                                 │
│ ☑ Remember me                   │
│                                 │
│ ┌─────────────────────────────┐ │
│ │       Sign In               │ │
│ └─────────────────────────────┘ │
│                                 │
│ [Forgot your password?]         │
│                                 │
│ ───────── or ─────────          │
│                                 │
│ [🔵 Google] [📘 GitHub]        │
│                                 │
│ Don't have an account?          │
│ [Create Account]                │
│                                 │
└─────────────────────────────────┘
```

## Component Specifications

### Form Components

#### 1. BaseInput.vue
```vue
<!-- Props Interface -->
interface Props {
  name: string
  label: string
  type?: 'text' | 'email' | 'password'
  placeholder?: string
  required?: boolean
  autocomplete?: string
  showValidation?: boolean
}

<!-- Visual States -->
- Default: Gray border (#E5E7EB)
- Focus: Blue border (#3B82F6) + blue ring
- Valid: Green border (#10B981) + checkmark icon
- Invalid: Red border (#EF4444) + error icon
- Disabled: Gray background (#F3F4F6)
```

#### 2. PasswordStrengthIndicator.vue
```vue
<!-- Progress Bar States -->
- Very Weak: 1/5 bars, Red (#EF4444)
- Weak: 2/5 bars, Orange (#F59E0B)
- Fair: 3/5 bars, Yellow (#EAB308)
- Good: 4/5 bars, Blue (#3B82F6)
- Strong: 5/5 bars, Green (#10B981)

<!-- Requirement Checklist -->
✓ 8+ characters (Green when met)
✗ Uppercase letter (Gray when not met)
✓ Lowercase letter
✗ Number
✗ Special character
```

#### 3. SubmitButton.vue
```vue
<!-- Button States -->
- Default: Blue background (#3B82F6)
- Hover: Darker blue (#2563EB)
- Loading: Spinner + "Creating Account..." text
- Disabled: Gray background (#9CA3AF)
- Success: Green background + checkmark (brief)
```

#### 4. SocialLoginButton.vue
```vue
<!-- Google Button -->
- White background with Google colors
- Google logo + "Continue with Google"

<!-- GitHub Button -->
- Dark background (#1F2937)
- GitHub logo + "Continue with GitHub"
```

### Layout Components

#### 1. AuthLayout.vue
```vue
<!-- Desktop Layout -->
- Centered card (max-width: 400px)
- Card padding: 2rem (32px)
- Card shadow: Tailwind shadow-lg
- Background: Light gray (#F9FAFB)

<!-- Mobile Layout -->
- Full screen form
- Padding: 1rem (16px)
- No card styling
- Background: White
```

### Form Validation Patterns

#### Real-time Validation Triggers
- **Email**: Validate on blur (check format)
- **Password**: Validate on input (strength calculation)
- **Confirm Password**: Validate on input (match check)
- **Required Fields**: Validate on blur

#### Validation Visual Feedback
- **Success**: Green border + checkmark icon
- **Error**: Red border + error icon + error message
- **Loading**: Blue border + spinner icon
- **Neutral**: Gray border (default state)

#### Error Message Patterns
```typescript
// Error Message Examples
{
  required: "This field is required",
  email: "Please enter a valid email address",
  minLength: "Must be at least 8 characters",
  passwordMatch: "Passwords do not match",
  weakPassword: "Password is too weak"
}
```

## Responsive Behavior

### Desktop to Mobile Transitions
- **Form Width**: 400px fixed → 100% viewport width
- **Input Layout**: Two-column name fields → single column
- **Button Layout**: Social buttons side-by-side → stacked
- **Navigation**: Top nav links → back arrow + title

### Touch Interactions
- **Input Height**: 44px minimum for touch targets
- **Button Height**: 48px minimum
- **Spacing**: 16px minimum between interactive elements
- **Tap Feedback**: 150ms highlight on touch

## Form Validation Specifications

### Client-Side Validation Rules

#### Registration Form
```typescript
const registrationSchema = z.object({
  firstName: z.string()
    .min(2, "First name must be at least 2 characters")
    .max(50, "First name cannot exceed 50 characters"),
  
  lastName: z.string()
    .min(2, "Last name must be at least 2 characters")  
    .max(50, "Last name cannot exceed 50 characters"),
    
  email: z.string()
    .email("Please enter a valid email address")
    .max(255, "Email cannot exceed 255 characters"),
    
  password: z.string()
    .min(8, "Password must be at least 8 characters")
    .regex(/^(?=.*[a-z])/, "Must contain lowercase letter")
    .regex(/^(?=.*[A-Z])/, "Must contain uppercase letter")
    .regex(/^(?=.*\d)/, "Must contain number")
    .regex(/^(?=.*[@$!%*?&])/, "Must contain special character"),
    
  confirmPassword: z.string()
}).refine((data) => data.password === data.confirmPassword, {
  message: "Passwords do not match",
  path: ["confirmPassword"]
})
```

#### Login Form
```typescript
const loginSchema = z.object({
  email: z.string()
    .email("Please enter a valid email address"),
    
  password: z.string()
    .min(1, "Password is required"),
    
  rememberMe: z.boolean().optional()
})
```

### Password Strength Calculation
```typescript
interface PasswordStrength {
  score: 0 | 1 | 2 | 3 | 4 | 5
  label: 'Very Weak' | 'Weak' | 'Fair' | 'Good' | 'Strong'
  color: string
  requirements: {
    length: boolean      // 8+ characters
    uppercase: boolean   // A-Z
    lowercase: boolean   // a-z  
    number: boolean      // 0-9
    special: boolean     // @$!%*?&
  }
}
```

## Implementation Checklist

### Module 3 UI Requirements
- [ ] Registration form with all required fields
- [ ] Real-time validation with VeeValidate + Zod
- [ ] Password strength indicator with visual feedback
- [ ] Login form with "Remember me" functionality
- [ ] Social login buttons (Google, GitHub)
- [ ] Responsive design (desktop/tablet/mobile)
- [ ] Loading states for form submission
- [ ] Error handling with user-friendly messages
- [ ] Success states and feedback
- [ ] Proper form accessibility (ARIA, labels, focus)

### Accessibility Requirements
- [ ] All inputs have proper labels
- [ ] Error messages associated with inputs (aria-describedby)
- [ ] Form validation errors announced to screen readers
- [ ] Keyboard navigation works throughout forms
- [ ] Focus indicators visible and consistent
- [ ] Password visibility toggle accessible
- [ ] Submit button disabled during loading
- [ ] Error summary for screen readers

### UX Requirements
- [ ] Smooth transitions between form states
- [ ] Clear visual hierarchy and spacing
- [ ] Intuitive error message placement
- [ ] Progress indication during submission
- [ ] Success confirmation before redirect
- [ ] Mobile-optimized input types (email keyboard, etc.)
- [ ] Autofocus on first field
- [ ] Form persistence across page refreshes

This comprehensive form system provides TaskFlow users with a professional, secure, and user-friendly authentication experience that sets the foundation for the rest of the application.