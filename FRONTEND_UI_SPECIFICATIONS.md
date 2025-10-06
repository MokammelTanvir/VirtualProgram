# Virtual Day Program - Frontend UI/Dashboard Specifications

## Design System and UI Specifications

### Color Palette

```css
:root {
  /* Primary Colors */
  --primary-50: #eff6ff;
  --primary-100: #dbeafe;
  --primary-500: #3b82f6;
  --primary-600: #2563eb;
  --primary-700: #1d4ed8;

  /* Secondary Colors */
  --secondary-50: #f8fafc;
  --secondary-100: #f1f5f9;
  --secondary-500: #64748b;
  --secondary-600: #475569;
  --secondary-700: #334155;

  /* Success/Error Colors */
  --success-500: #10b981;
  --error-500: #ef4444;
  --warning-500: #f59e0b;

  /* Background Colors */
  --bg-primary: #ffffff;
  --bg-secondary: #f8fafc;
  --bg-tertiary: #f1f5f9;
}
```

### Typography

```css
/* Font Family */
font-family: "Inter", "system-ui", sans-serif;

/* Font Sizes */
--text-xs: 0.75rem; /* 12px */
--text-sm: 0.875rem; /* 14px */
--text-base: 1rem; /* 16px */
--text-lg: 1.125rem; /* 18px */
--text-xl: 1.25rem; /* 20px */
--text-2xl: 1.5rem; /* 24px */
--text-3xl: 1.875rem; /* 30px */
--text-4xl: 2.25rem; /* 36px */
```

## Dashboard Layout Structure

### 1. Main Layout Components

#### Sidebar Navigation

```
┌─────────────────┐
│ LOGO            │
├─────────────────┤
│ 📊 Dashboard    │
│ 📚 Programs     │
│ 📅 Sessions     │
│ 👥 Users        │
│ 💳 Payments     │
│ 📈 Analytics    │
│ ⚙️  Settings    │
├─────────────────┤
│ Profile         │
│ Logout          │
└─────────────────┘
```

#### Header Component

- User profile dropdown
- Notifications bell
- Search functionality
- Breadcrumb navigation

#### Main Content Area

- Dynamic content based on route
- Responsive grid system
- Card-based layout

### 2. Dashboard Pages Specification

## 🏠 Dashboard Home Page

### Layout Structure

```
┌──────────────────────────────────────────────────────┐
│ Header (Breadcrumb + Profile + Notifications)       │
├──────────────────────────────────────────────────────┤
│ Quick Stats Cards                                    │
│ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐     │
│ │ Total   │ │ Active  │ │ Revenue │ │ Users   │     │
│ │Programs │ │Sessions │ │ $12,543 │ │ 1,234   │     │
│ └─────────┘ └─────────┘ └─────────┘ └─────────┘     │
├──────────────────────────────────────────────────────┤
│ Main Content Grid                                    │
│ ┌──────────────────────┐ ┌──────────────────────┐   │
│ │ Recent Programs      │ │ Upcoming Sessions    │   │
│ │                      │ │                      │   │
│ └──────────────────────┘ └──────────────────────┘   │
│ ┌──────────────────────┐ ┌──────────────────────┐   │
│ │ Analytics Chart      │ │ Recent Activities    │   │
│ │                      │ │                      │   │
│ └──────────────────────┘ └──────────────────────┘   │
└──────────────────────────────────────────────────────┘
```

### Components Specification

#### Stats Cards

```typescript
interface StatsCard {
  title: string;
  value: string | number;
  change: {
    value: string;
    trend: "up" | "down" | "neutral";
  };
  icon: React.ComponentType;
  color: "blue" | "green" | "purple" | "orange";
}
```

#### Recent Programs Card

- List of latest 5 programs
- Quick actions (View, Edit, Delete)
- Status indicators
- Date created
- Enrollment count

#### Upcoming Sessions Card

- Next 5 scheduled sessions
- Time remaining countdown
- Join session button
- Participant count
- Session status

## 📚 Programs Management Page

### Page Layout

```
┌────────────────────────────────────────────────────────┐
│ Page Header                                            │
│ Programs Management    [Search Box]    [+ New Program]│
├────────────────────────────────────────────────────────┤
│ Filters & Controls                                     │
│ [All] [Draft] [Published] [Completed]  [Sort By ▼]    │
├────────────────────────────────────────────────────────┤
│ Programs Grid/List                                     │
│ ┌────────────┐ ┌────────────┐ ┌────────────┐         │
│ │ Program 1  │ │ Program 2  │ │ Program 3  │         │
│ │ [Image]    │ │ [Image]    │ │ [Image]    │         │
│ │ Title      │ │ Title      │ │ Title      │         │
│ │ Status     │ │ Status     │ │ Status     │         │
│ │ [Actions]  │ │ [Actions]  │ │ [Actions]  │         │
│ └────────────┘ └────────────┘ └────────────┘         │
└────────────────────────────────────────────────────────┘
```

### Program Card Component

```typescript
interface ProgramCard {
  id: string;
  title: string;
  description: string;
  thumbnail: string;
  status: "draft" | "published" | "in_progress" | "completed";
  instructor: {
    name: string;
    avatar: string;
  };
  stats: {
    enrollments: number;
    sessions: number;
    duration: string;
  };
  pricing: {
    amount: number;
    currency: string;
  };
  dates: {
    startDate: Date;
    endDate: Date;
  };
  actions: Array<{
    label: string;
    action: () => void;
    variant: "primary" | "secondary" | "danger";
  }>;
}
```

## 📅 Sessions Management Page

### Session Calendar View

```
┌────────────────────────────────────────────────────────┐
│ Sessions Calendar                                      │
│ ◀ October 2025 ▶                         [+ New Session]│
├────────────────────────────────────────────────────────┤
│ Calendar Grid                                          │
│ Su  Mo  Tu  We  Th  Fr  Sa                            │
│ 01  02  03  04  05  06  07                            │
│     •   ••      •                                     │
│ 08  09  10  11  12  13  14                            │
│ •       •   ••  •   •                                 │
│ ...                                                    │
├────────────────────────────────────────────────────────┤
│ Today's Sessions                                       │
│ ┌──────────────────────────────────────────────────┐   │
│ │ 🔴 Live Now: Web Development Basics              │   │
│ │ 10:00 AM - 12:00 PM | 23 participants           │   │
│ │ [Join] [Manage]                                  │   │
│ └──────────────────────────────────────────────────┘   │
└────────────────────────────────────────────────────────┘
```

### Session Details Modal

```typescript
interface SessionModal {
  session: {
    id: string;
    title: string;
    description: string;
    startTime: Date;
    endTime: Date;
    status: "scheduled" | "live" | "completed" | "cancelled";
    meetingUrl: string;
    participants: Array<{
      id: string;
      name: string;
      avatar: string;
      status: "joined" | "absent";
    }>;
    resources: Array<{
      id: string;
      name: string;
      type: string;
      url: string;
    }>;
  };
  actions: {
    onJoin: () => void;
    onEdit: () => void;
    onCancel: () => void;
    onDelete: () => void;
  };
}
```

## 👥 Users Management Page

### Users Table Layout

```
┌─────────────────────────────────────────────────────────────┐
│ Users Management                                            │
│ [Search Users]               [Filters ▼]    [+ Invite User]│
├─────────────────────────────────────────────────────────────┤
│ Users Table                                                 │
│ ┌────────┬──────────┬─────────┬─────────┬─────────┬────────┐│
│ │ Avatar │ Name     │ Email   │ Role    │ Status  │ Actions││
│ ├────────┼──────────┼─────────┼─────────┼─────────┼────────┤│
│ │ [IMG]  │ John Doe │ j@e.com │ Student │ Active  │ [...] ││
│ │ [IMG]  │ Jane S.  │ j@e.com │ Teacher │ Active  │ [...] ││
│ └────────┴──────────┴─────────┴─────────┴─────────┴────────┘│
├─────────────────────────────────────────────────────────────┤
│ Pagination: ◀ 1 2 3 4 5 ▶                                 │
└─────────────────────────────────────────────────────────────┘
```

### User Profile Modal

- Personal information editing
- Role management
- Program enrollments
- Activity history
- Account status controls

## 💳 Payments & Billing Page

### Revenue Dashboard

```
┌─────────────────────────────────────────────────────────────┐
│ Revenue Overview                                            │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐            │
│ │ This Month  │ │ Total       │ │ Pending     │            │
│ │ $12,543     │ │ $145,678    │ │ $2,456      │            │
│ │ +12.5%      │ │             │ │ 12 payments │            │
│ └─────────────┘ └─────────────┘ └─────────────┘            │
├─────────────────────────────────────────────────────────────┤
│ Revenue Chart (Last 6 months)                              │
│ [Interactive Line/Bar Chart]                               │
├─────────────────────────────────────────────────────────────┤
│ Recent Transactions                                         │
│ [Transactions Table with filters and search]               │
└─────────────────────────────────────────────────────────────┘
```

## 📈 Analytics Page

### Analytics Dashboard Components

1. **User Engagement Metrics**

   - Session attendance rates
   - Program completion rates
   - User activity heatmap

2. **Financial Analytics**

   - Revenue trends
   - Program profitability
   - Payment success rates

3. **Content Performance**

   - Most popular programs
   - Session engagement scores
   - Resource download statistics

4. **System Usage**
   - Peak usage hours
   - Device/browser statistics
   - Geographic distribution

## Mobile Responsive Design

### Breakpoints

```css
/* Mobile */
@media (max-width: 640px) {
}

/* Tablet */
@media (min-width: 641px) and (max-width: 1024px) {
}

/* Desktop */
@media (min-width: 1025px) {
}
```

### Mobile Navigation

- Collapsible hamburger menu
- Bottom navigation for quick actions
- Swipe gestures for navigation
- Touch-optimized controls

## Component Library Structure

```
components/
├── ui/                 # Basic UI components
│   ├── Button/
│   ├── Input/
│   ├── Modal/
│   ├── Card/
│   └── Table/
├── layout/             # Layout components
│   ├── DashboardLayout/
│   ├── Header/
│   ├── Sidebar/
│   └── Footer/
├── dashboard/          # Dashboard-specific components
│   ├── StatsCard/
│   ├── RecentPrograms/
│   ├── UpcomingSessions/
│   └── AnalyticsChart/
├── programs/           # Program management components
│   ├── ProgramCard/
│   ├── ProgramForm/
│   ├── ProgramFilters/
│   └── ProgramModal/
└── sessions/           # Session management components
    ├── SessionCalendar/
    ├── SessionCard/
    ├── LiveSession/
    └── SessionRecording/
```

## Accessibility (A11y) Requirements

### WCAG 2.1 AA Compliance

- Keyboard navigation support
- Screen reader compatibility
- Color contrast ratios (4.5:1 minimum)
- Alt text for images
- ARIA labels and descriptions
- Focus management
- Skip links

### Implementation

```typescript
// Example accessible button component
interface AccessibleButtonProps {
  children: React.ReactNode;
  onClick: () => void;
  ariaLabel?: string;
  disabled?: boolean;
  variant?: "primary" | "secondary" | "danger";
}

export function AccessibleButton({
  children,
  onClick,
  ariaLabel,
  disabled = false,
  variant = "primary",
}: AccessibleButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      aria-label={ariaLabel}
      className={`button button--${variant} ${
        disabled ? "button--disabled" : ""
      }`}
      tabIndex={disabled ? -1 : 0}
    >
      {children}
    </button>
  );
}
```

## Performance Optimization

### Code Splitting

```typescript
// Lazy loading for heavy components
const AnalyticsPage = lazy(() => import("../pages/analytics"));
const SessionRecording = lazy(
  () => import("../components/sessions/SessionRecording")
);

// Usage with Suspense
<Suspense fallback={<LoadingSpinner />}>
  <AnalyticsPage />
</Suspense>;
```

### Image Optimization

- Next.js Image component usage
- WebP format support
- Lazy loading implementation
- Responsive images

### Caching Strategy

- API response caching
- Static asset caching
- Service worker implementation
- Browser storage optimization

Following this UI specification, you can create a professional and user-friendly Virtual Day Program dashboard.
