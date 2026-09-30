# Screen Stories Guide

How to write Storybook stories for full screens — keeping them organised and separate from component stories.

---

## Component stories vs screen stories

| | Component stories | Screen stories |
|---|---|---|
| **Purpose** | Document a single component's variants and states | Show a real product screen composed of many components |
| **Location** | `src/stories/[Component].stories.tsx` | `src/stories/screens/[Feature]/[Screen].stories.tsx` |
| **Title prefix** | `Forms/`, `Display/`, etc. | `Screens/[Feature]/` |
| **Scope** | One component | Full page layout |
| **Decorator** | None or minimal | App shell, providers, mock data |

---

## File structure

```
src/
  stories/
    Button.stories.tsx         ← component story (existing)
    Select.stories.tsx         ← component story (existing)
    screens/
      Auth/
        Login.stories.tsx
        Signup.stories.tsx
      Dashboard/
        Overview.stories.tsx
        Overview-Empty.stories.tsx
      Settings/
        General.stories.tsx
      [Feature]/
        [Screen].stories.tsx
```

---

## Story file template

```tsx
// src/stories/screens/Dashboard/Overview.stories.tsx

import * as React from 'react';
import type { Meta, StoryObj } from '@storybook/nextjs-vite';

// Import all components used in this screen
import { Button } from '@/components/ui/button';
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from '@/components/ui/table';
import { Badge } from '@/components/ui/badge';
// ... other imports

const meta = {
  title: 'Screens/Dashboard/Overview',
  // No component — screen stories don't wrap a single component
  parameters: {
    layout: 'fullscreen',  // always fullscreen for screens
  },
} satisfies Meta;

export default meta;
type Story = StoryObj<typeof meta>;

// ─── Default state ─────────────────────────────────────────────────────────────

export const Default: Story = {
  render: () => (
    <div className="min-h-screen bg-[var(--color-background-default)]">
      {/* Screen content — use layout patterns from layout-patterns.md */}
    </div>
  ),
};

// ─── Empty state ────────────────────────────────────────────────────────────────

export const Empty: Story = {
  render: () => (
    <div className="min-h-screen bg-[var(--color-background-default)]">
      {/* Same layout, no data — show Empty component */}
    </div>
  ),
};

// ─── Loading state ──────────────────────────────────────────────────────────────

export const Loading: Story = {
  render: () => (
    <div className="min-h-screen bg-[var(--color-background-default)]">
      {/* Skeleton placeholders where data would be */}
    </div>
  ),
};

// ─── Error state ────────────────────────────────────────────────────────────────

export const Error: Story = {
  render: () => (
    <div className="min-h-screen bg-[var(--color-background-default)]">
      {/* Alert component with error message */}
    </div>
  ),
};
```

---

## Title convention

```
Screens/[Feature]/[ScreenName]
```

| Feature | Example titles |
|---|---|
| Auth | `Screens/Auth/Login`, `Screens/Auth/Signup` |
| Dashboard | `Screens/Dashboard/Overview`, `Screens/Dashboard/Detail` |
| Settings | `Screens/Settings/General`, `Screens/Settings/Billing` |
| [Feature] | `Screens/Projects/List`, `Screens/Projects/Create` |

This groups screen stories under a `Screens/` top-level folder in the Storybook sidebar — separate from component stories.

---

## Required stories per screen

Every screen story file should include these exports:

| Export name | When to show |
|---|---|
| `Default` | Normal state with populated data |
| `Empty` | No data — use `Empty` component |
| `Loading` | Data fetching — use `Skeleton` components |
| `Error` | Failed data load — use `Alert` component |
| `[Variant]` | Any meaningful alternative layout (e.g. `WithSidebar`, `MobileLayout`) |

Not every state applies to every screen. Include only the ones that are meaningfully different.

---

## Decorators for screen stories

### With providers

If the screen uses components that need a provider (Tooltip, Sidebar, Toast), wrap in a decorator:

```tsx
const meta = {
  title: 'Screens/Dashboard/Overview',
  parameters: { layout: 'fullscreen' },
  decorators: [
    (Story) => (
      <TooltipProvider>
        <SidebarProvider>
          <Story />
        </SidebarProvider>
      </TooltipProvider>
    ),
  ],
} satisfies Meta;
```

### With app shell

If every screen in a feature uses the same app shell, define the decorator once in `meta`:

```tsx
const AppShellDecorator = (Story: React.ComponentType) => (
  <div className="flex h-screen bg-[var(--color-background-default)]">
    <SidebarProvider>
      {/* Sidebar */}
      <Sidebar type="default" collapsible="none">
        {/* nav items */}
      </Sidebar>
      {/* Main */}
      <main className="flex-1 overflow-y-auto">
        <Story />
      </main>
    </SidebarProvider>
  </div>
);

const meta = {
  title: 'Screens/Dashboard/Overview',
  parameters: { layout: 'fullscreen' },
  decorators: [AppShellDecorator],
} satisfies Meta;
```

---

## Mock data

Screen stories need realistic mock data. Define it at the top of the file, outside the meta object:

```tsx
const projects = [
  { id: '1', name: 'Agentic UI', status: 'active', date: '2026-05-01', owner: 'Phuong' },
  { id: '2', name: 'Design tokens', status: 'complete', date: '2026-04-15', owner: 'Alex' },
  { id: '3', name: 'Component library', status: 'draft', date: '2026-06-01', owner: 'Sara' },
];

const emptyProjects: typeof projects = [];
```

Use the same data shape for `Default` (populated) and `Empty` (empty array). This keeps the states consistent and realistic.

---

## Responsive screen stories

To show the same screen at multiple breakpoints, either:

**Option A — Separate stories:**
```tsx
export const Mobile: Story = {
  parameters: { viewport: { defaultViewport: 'mobile1' } },
  render: () => <MobileLayout />,
};

export const Desktop: Story = {
  parameters: { viewport: { defaultViewport: 'desktop' } },
  render: () => <DesktopLayout />,
};
```

**Option B — Storybook viewport addon:**
Use the viewport selector in the Storybook toolbar to toggle breakpoints on the same story.

Option B is preferred — keep one story, use the toolbar to check breakpoints.

---

## Naming checklist

- [ ] File lives under `src/stories/screens/[Feature]/`
- [ ] Title is `Screens/[Feature]/[ScreenName]`
- [ ] `parameters.layout` is `'fullscreen'`
- [ ] All states covered: Default, Empty, Loading, Error
- [ ] Providers added to decorators if needed
- [ ] Mock data defined at file top, not inline in render
