# App Layout Documentation

This document explains how the premium app shell works in Nuam RMS.

## 🏗️ Architecture Overview

The app shell consists of:
- **Header**: Top bar with logo, notifications, and user menu
- **Sidebar**: Left navigation with role-based filtering
- **Breadcrumbs**: Route-aware navigation breadcrumbs
- **Main Content**: Page content area

## 📁 File Structure

```
/src/layout/
├── AppLayout.tsx      # Main layout wrapper
├── Header.tsx         # Top bar component
├── Sidebar.tsx        # Left navigation
├── Breadcrumbs.tsx    # Route breadcrumbs
└── README.md          # This documentation

/src/config/
└── nav.ts            # Navigation configuration

/src/hooks/
├── useNav.ts         # Navigation filtering hook
└── useCan.ts         # Permission checking hook

/src/components/organisms/
├── UserMenu.tsx      # User dropdown menu
└── NotificationBell.tsx # Notification bell
```

## 🧭 Navigation Configuration

### `/src/config/nav.ts`

This is the single source of truth for navigation. Each item supports:

```typescript
interface NavItem {
  id: string;                    // Unique identifier
  label: string;                 // Display name
  path?: string;                 // Route path
  icon?: LucideIcon;             // Icon component
  roles?: string[];              // Required roles
  permissions?: string[];        // Required permissions
  featureFlag?: string;          // Feature flag requirement
  badge?: {                      // Badge configuration
    text: string;
    variant?: 'default' | 'secondary' | 'destructive' | 'outline';
  };
  children?: NavItem[];          // Sub-navigation items
  disabled?: boolean;            // Disable item
}
```

### Permission Logic

- **Roles**: User must have ANY of the specified roles
- **Permissions**: User must have ANY of the specified permissions
- **Wildcards**: `*`, `manage:all`, `read:all` grant access to everything
- **Children**: If no children are accessible, parent is hidden

## 🔐 Permission Hooks

### `useCan(required: string | string[])`

Checks if user has ANY of the required permissions:

```typescript
const canReadProjects = useCan('read:projects');
const canManageUsers = useCan(['manage:users', 'manage:all']);
```

### `useCanAll(required: string[])`

Checks if user has ALL required permissions:

```typescript
const canManageEverything = useCanAll(['manage:users', 'manage:projects']);
```

### `useHasRole(required: string | string[])`

Checks if user has ANY of the required roles:

```typescript
const isAdmin = useHasRole(['CEO', 'Director', 'Manager']);
```

## 🧭 Navigation Hooks

### `useNav()`

Returns filtered navigation items based on user permissions:

```typescript
const navItems = useNav(); // Only shows items user can access
```

### `useFlattenedNav()`

Returns all accessible navigation items (including children) as a flat array:

```typescript
const allNavItems = useFlattenedNav(); // Used for breadcrumbs
```

## 🎨 Component Features

### Header
- **Logo**: Clickable, navigates to dashboard
- **Mobile Menu**: Hamburger button for mobile sidebar
- **Notifications**: Bell icon with unread count
- **User Menu**: Dropdown with profile, settings, logout

### Sidebar
- **Collapsible**: Desktop can collapse to icons only
- **Mobile**: Drawer/Sheet on mobile devices
- **Role Filtering**: Only shows accessible items
- **Active States**: Highlights current route
- **Tooltips**: Shows labels when collapsed

### Breadcrumbs
- **Route Aware**: Auto-generated from current path
- **Clickable**: Navigate back to previous levels
- **Dynamic**: Shows actual page titles when available
- **Accessible**: Proper ARIA labels and semantic HTML

## 📱 Responsive Design

- **Desktop**: Full sidebar, collapsible
- **Tablet**: Collapsed sidebar by default
- **Mobile**: Hidden sidebar, hamburger menu

## ♿ Accessibility

- **Keyboard Navigation**: All interactive elements are keyboard accessible
- **ARIA Labels**: Proper labels for screen readers
- **Focus Management**: Logical tab order
- **Color Contrast**: WCAG AA compliant
- **Semantic HTML**: Proper heading structure and landmarks

## 🎯 Usage Examples

### Adding a New Navigation Item

1. Add to `/src/config/nav.ts`:
```typescript
{
  id: 'new-feature',
  label: 'New Feature',
  path: '/new-feature',
  icon: NewIcon,
  permissions: ['read:new-feature'],
}
```

2. Add route to `App.tsx`:
```typescript
<Route
  path="/new-feature"
  element={
    <ProtectedRoute permissions={["read:new-feature"]}>
      <AppLayout>
        <NewFeaturePage />
      </AppLayout>
    </ProtectedRoute>
  }
/>
```

### Conditional Rendering

```typescript
import { useCan } from '@/hooks/useCan';

function MyComponent() {
  const canManageUsers = useCan('manage:users');
  
  return (
    <div>
      {canManageUsers && <UserManagementButton />}
    </div>
  );
}
```

## 🔧 Customization

### Theming
All colors use Tailwind tokens from `tailwind.config.ts`. No hardcoded colors.

### Icons
Use Lucide React icons for consistency.

### Styling
Follow the existing patterns and use the design system tokens.
