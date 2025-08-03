# Module 2: Foundation Layout Mockups

## Application Context: TaskFlow Platform Foundation

In Module 2, you're building the foundational structure for **TaskFlow** - the collaborative task management platform. This module establishes the visual framework that will support all future features.

### What You're Building This Module
- Landing page with modern design
- Responsive navigation system
- Basic page routing structure
- Foundation for authentication flows

## UI Mockups

### 1. Landing Page (Desktop 1024px+)

```
┌─────────────────────────────────────────────────────────────────────────┐
│ TaskFlow                                    [Login] [Get Started]        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│        ┌─────────────────┐    Streamline Your Team's                    │
│        │                 │    Productivity with TaskFlow                │
│        │   Hero Image    │                                              │
│        │   (Task Board   │    Modern task management for teams          │
│        │   Illustration) │    who value simplicity and results.         │
│        │                 │                                              │
│        └─────────────────┘    [Start Free Trial] [Watch Demo]          │
│                                                                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐                     │
│  │ 📋 Projects │  │ 👥 Teams    │  │ 📊 Analytics│                     │
│  │ Organize    │  │ Collaborate │  │ Track       │                     │
│  │ your work   │  │ seamlessly  │  │ progress    │                     │
│  └─────────────┘  └─────────────┘  └─────────────┘                     │
│                                                                         │
│                        [Get Started Today]                             │
│                                                                         │
├─────────────────────────────────────────────────────────────────────────┤
│ © 2024 TaskFlow  |  Privacy  |  Terms  |  Contact                      │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2. Navigation Header (Desktop)

```
┌─────────────────────────────────────────────────────────────────────────┐
│ TaskFlow           [🔍 Search]           [🔔] [Profile ▼]               │
│ [Logo]             [                ]                                   │
└─────────────────────────────────────────────────────────────────────────┘
```

**Header Elements:**
- **Logo**: TaskFlow brand (links to dashboard when logged in)
- **Search**: Global search input (placeholder: "Search projects, tasks...")
- **Notifications**: Bell icon with badge for unread notifications
- **Profile**: User avatar + dropdown (settings, logout, help)

### 3. Main Application Layout (Post-Login)

```
┌─────────────────────────────────────────────────────────────────────────┐
│ TaskFlow           [🔍 Search]           [🔔] [Profile ▼]               │
├─────────────────────────────────────────────────────────────────────────┤
│ ☰ │ 📊 Dashboard                                                       │
│   │ 📁 My Projects      ┌─────────────────────────────────────────────┐ │
│   │ 👥 Shared Projects  │                                             │ │
│   │ 📋 All Tasks        │          Main Content Area                  │ │
│   │ 👤 Team Members     │                                             │ │
│   │ ⚙️  Settings        │         (Router View)                       │ │
│   │                     │                                             │ │
│   │ ──────────────      │                                             │ │
│   │ 📌 Pinned          │                                             │ │
│   │ • Project Alpha     │                                             │ │
│   │ • Marketing         │                                             │ │
│   │                     │                                             │ │
│   │ [+ New Project]     │                                             │ │
│   │                     └─────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────┘
```

**Sidebar Width**: 256px
**Sidebar Elements**:
- Navigation icons with labels
- Collapsible pinned projects section
- "New Project" CTA button

### 4. Mobile Navigation (320px - 767px)

#### Mobile Header
```
┌─────────────────────────────────┐
│ ☰  TaskFlow          [🔔][👤] │
└─────────────────────────────────┘
```

#### Mobile Menu (Overlay)
```
┌─────────────────────────────────┐
│ ✕  TaskFlow                     │
├─────────────────────────────────┤
│                                 │
│ 📊 Dashboard                    │
│ 📁 My Projects                  │
│ 👥 Shared Projects              │
│ 📋 All Tasks                    │
│ 👤 Team Members                 │
│ ⚙️  Settings                    │
│                                 │
│ ──────────────                  │
│ 📌 Pinned Projects              │
│ • Project Alpha                 │
│ • Marketing                     │
│                                 │
│ [+ New Project]                 │
│                                 │
│ ──────────────                  │
│ Help & Support                  │
│ Sign Out                        │
│                                 │
└─────────────────────────────────┘
```

### 5. Tablet Layout (768px - 1023px)

```
┌─────────────────────────────────────────────────────────────────┐
│ TaskFlow      [🔍 Search]       [🔔] [Profile ▼]              │
├─────────────────────────────────────────────────────────────────┤
│ [≡] │                                                          │
│     │            Main Content Area                             │
│     │                                                          │
│     │            (Sidebar becomes                              │
│     │             collapsible)                                 │
│     │                                                          │
└─────────────────────────────────────────────────────────────────┘
```

## Component Specifications

### Navigation Components
1. **AppHeader.vue**
   - Logo component (clickable, routes to dashboard)
   - Search input with icon and autocomplete
   - Notification bell with badge counter
   - Profile dropdown with avatar

2. **AppSidebar.vue**
   - Navigation menu with icons and labels
   - Pinned projects section (collapsible)
   - New Project button
   - User info at bottom

3. **MobileMenu.vue**
   - Slide-out overlay navigation
   - Full-screen menu on mobile
   - Touch-friendly tap targets (44px minimum)

### Layout Components
1. **DefaultLayout.vue**
   - Header + Sidebar + Main content structure
   - Responsive grid system
   - Handles mobile/desktop layout switching

2. **AuthLayout.vue**
   - Centered content for login/register pages
   - Minimal header with logo only
   - Clean, focused design

## Responsive Behavior

### Desktop to Tablet (1024px → 768px)
- Sidebar collapses to icon-only mode
- Search bar becomes condensed
- Content area adjusts to available space

### Tablet to Mobile (768px → 320px)
- Sidebar becomes hamburger menu overlay
- Search moves to dedicated page/modal
- Profile dropdown becomes bottom sheet

## Interactive States

### Navigation States
- **Default**: Standard appearance
- **Hover**: Subtle background color change
- **Active**: Highlighted background + bold text
- **Loading**: Skeleton loaders for dynamic content

### Mobile Menu States
- **Closed**: Hidden overlay
- **Opening**: Slide-in animation (300ms ease-out)
- **Open**: Full overlay with backdrop
- **Closing**: Slide-out animation (200ms ease-in)

## Implementation Checklist

### Module 2 UI Requirements
- [ ] Landing page with hero section and feature cards
- [ ] Responsive navigation header
- [ ] Sidebar navigation with menu items
- [ ] Mobile hamburger menu with overlay
- [ ] Basic routing between pages (/, /login, /register)
- [ ] Responsive layout that works on all screen sizes
- [ ] Proper touch targets for mobile (44px minimum)
- [ ] Smooth transitions between navigation states

### Accessibility Requirements
- [ ] Proper ARIA labels for navigation elements
- [ ] Keyboard navigation support (Tab, Enter, Escape)
- [ ] Screen reader friendly menu structure
- [ ] Focus indicators visible and consistent
- [ ] Skip to main content link
- [ ] Semantic HTML structure (nav, main, aside)

### Performance Requirements
- [ ] Fast page transitions (< 200ms)
- [ ] Smooth animations (60fps)
- [ ] Lazy loading for non-critical images
- [ ] Optimized images with proper sizing

This foundation sets up the visual and navigation framework that students will enhance throughout the remaining modules as they add authentication, project management, and collaboration features.