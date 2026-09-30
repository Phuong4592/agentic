# Layout Patterns

Reusable HTML/Tailwind markup for common screen structures. All patterns use design system tokens and components.

**Rules:**
- Page-level layout uses `grid` — component-internal layout uses `flex`
- Colors/radius: CSS variables — spacing: Tailwind utilities
- Never hardcode pixel values — use spacing scale or tokens

---

## App shell — Sidebar + main content

The standard layout for product apps with persistent sidebar navigation.

```tsx
<div className="flex h-screen bg-[var(--color-background-default)] overflow-hidden">

  {/* Sidebar */}
  <SidebarProvider>
    <Sidebar type="default">
      <SidebarHeader>
        <SidebarLogo>
          {/* Logo here */}
        </SidebarLogo>
        <SidebarBrand title="App name" caption="Workspace" />
      </SidebarHeader>
      <SidebarContent>
        <SidebarGroup>
          <SidebarGroupLabel>Main</SidebarGroupLabel>
          <SidebarMenuItem icon={<LayoutAlt01 />} label="Dashboard" href="#" active />
          <SidebarMenuItem icon={<BarChart01 />} label="Analytics" href="#" />
        </SidebarGroup>
      </SidebarContent>
      <SidebarFooter>
        <SidebarMenuItem icon={<Settings01 />} label="Settings" href="#" />
      </SidebarFooter>
    </Sidebar>

    {/* Main content */}
    <main className="flex-1 overflow-y-auto">
      {/* Page content goes here */}
    </main>
  </SidebarProvider>

</div>
```

**Tokens used:**
- `color/background/default` — page canvas
- `color/sidebar/background` — sidebar (set by Sidebar component)
- `color/sidebar/border` — right edge (set by Sidebar component)

---

## Page header

Standard page header — title, description, and primary action.

```tsx
<div className="flex items-start justify-between gap-4 pb-6 border-b border-[var(--color-border-default)]">
  <div className="flex flex-col gap-1">
    <h1 className="text-2xl font-semibold text-[var(--color-background-default-foreground)]">
      Page title
    </h1>
    <p className="text-sm text-[var(--color-text-secondary)]">
      Supporting description or context.
    </p>
  </div>
  <Button>Primary action</Button>
</div>
```

**Variants:**
- No action: remove the `<Button>` and set `justify-between` → `justify-start`
- With breadcrumb: add `<Breadcrumb>` above the `<h1>`
- With tabs: add `<Tabs>` below the header block

---

## Form page

Single-column form layout — labels, inputs, and submit action.

```tsx
<div className="max-w-lg mx-auto py-8 px-4 flex flex-col gap-6">

  {/* Page header */}
  <div className="flex flex-col gap-1">
    <h1 className="text-xl font-semibold text-[var(--color-background-default-foreground)]">
      Form title
    </h1>
    <p className="text-sm text-[var(--color-text-secondary)]">
      Form description or instructions.
    </p>
  </div>

  {/* Form body */}
  <form className="flex flex-col gap-4">

    {/* Field group — related fields */}
    <div className="flex flex-col gap-4 p-4 rounded-[var(--radius-lg)] border border-[var(--color-border-default)] bg-[var(--color-surface-default)]">
      <p className="text-sm font-medium text-[var(--color-background-default-foreground)]">
        Section label
      </p>
      <div className="flex flex-col gap-3">
        <div className="flex flex-col gap-1.5">
          <Label htmlFor="name">Full name</Label>
          <Input id="name" placeholder="Enter your name" />
        </div>
        <div className="flex flex-col gap-1.5">
          <Label htmlFor="email">Email address</Label>
          <Input id="email" type="email" placeholder="name@company.com" />
        </div>
      </div>
    </div>

    {/* Form actions */}
    <div className="flex items-center justify-end gap-2 pt-2">
      <Button variant="outline">Cancel</Button>
      <Button type="submit">Save changes</Button>
    </div>

  </form>
</div>
```

**Notes:**
- `max-w-lg` constrains the form to a readable width
- Each field group wrapped in a card creates visual section separation
- Actions always right-aligned with `justify-end`

---

## Data page — table with header

Page with a filterable data table.

```tsx
<div className="flex flex-col gap-6 p-6">

  {/* Page header */}
  <div className="flex items-center justify-between">
    <div className="flex flex-col gap-1">
      <h1 className="text-xl font-semibold text-[var(--color-background-default-foreground)]">
        Table title
      </h1>
      <p className="text-sm text-[var(--color-text-secondary)]">24 items</p>
    </div>
    <Button>Add item</Button>
  </div>

  {/* Toolbar — search + filters */}
  <div className="flex items-center gap-2">
    <Input placeholder="Search..." className="max-w-xs" />
    <Select>
      <SelectTrigger className="w-36">
        <SelectValue placeholder="All statuses" />
      </SelectTrigger>
      <SelectContent>
        <SelectItem value="active">Active</SelectItem>
        <SelectItem value="inactive">Inactive</SelectItem>
      </SelectContent>
    </Select>
  </div>

  {/* Table */}
  <div className="rounded-[var(--radius-base)] border border-[var(--color-border-default)] overflow-hidden">
    <Table>
      <TableHeader>
        <TableRow>
          <TableHead>Name</TableHead>
          <TableHead>Status</TableHead>
          <TableHead>Date</TableHead>
          <TableHead className="text-right">Actions</TableHead>
        </TableRow>
      </TableHeader>
      <TableBody>
        {/* rows */}
      </TableBody>
    </Table>
  </div>

  {/* Pagination */}
  <div className="flex justify-end">
    <Pagination>
      {/* pagination items */}
    </Pagination>
  </div>

</div>
```

---

## Dashboard — card grid

Overview page with metric cards and a larger content area.

```tsx
<div className="flex flex-col gap-6 p-6">

  {/* Page header */}
  <h1 className="text-xl font-semibold text-[var(--color-background-default-foreground)]">
    Dashboard
  </h1>

  {/* Metric cards — 4 columns on desktop */}
  <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
    {metrics.map((m) => (
      <div
        key={m.label}
        className="flex flex-col gap-2 p-4 rounded-[var(--radius-lg)] border border-[var(--color-border-default)] bg-[var(--color-surface-default)]"
      >
        <p className="text-sm text-[var(--color-text-secondary)]">{m.label}</p>
        <p className="text-2xl font-semibold text-[var(--color-background-default-foreground)]">
          {m.value}
        </p>
        <p className="text-xs text-[var(--color-text-secondary)]">{m.change}</p>
      </div>
    ))}
  </div>

  {/* Main content area — 2/3 + 1/3 split on desktop */}
  <div className="grid grid-cols-1 lg:grid-cols-3 gap-4">

    {/* Primary content */}
    <div className="lg:col-span-2 rounded-[var(--radius-lg)] border border-[var(--color-border-default)] bg-[var(--color-surface-default)] p-4">
      {/* Chart, table, or list */}
    </div>

    {/* Secondary content */}
    <div className="rounded-[var(--radius-lg)] border border-[var(--color-border-default)] bg-[var(--color-surface-default)] p-4">
      {/* Activity feed, summary, etc. */}
    </div>

  </div>

</div>
```

**Grid tokens:**
- `grid-cols-4` → 12-column desktop grid simplified to 4 metric cards
- `lg:grid-cols-3` → 2/3 + 1/3 split at `lg:` (1024px) breakpoint
- All cards use `color/surface/default` — component surface, not page canvas

---

## Settings page — sidebar nav + content

Two-column layout for settings with a local nav.

```tsx
<div className="flex gap-8 p-6 max-w-4xl mx-auto">

  {/* Settings nav */}
  <nav className="w-48 shrink-0 flex flex-col gap-1">
    {['General', 'Profile', 'Security', 'Notifications', 'Billing'].map((item) => (
      <a
        key={item}
        href="#"
        className={cn(
          "px-3 py-2 rounded-[var(--radius-md)] text-sm transition-colors",
          item === 'General'
            ? "bg-[var(--color-background-accent)] text-[var(--color-background-accent-foreground)] font-medium"
            : "text-[var(--color-text-secondary)] hover:bg-[var(--color-background-accent)] hover:text-[var(--color-background-accent-foreground)]"
        )}
      >
        {item}
      </a>
    ))}
  </nav>

  {/* Settings content */}
  <div className="flex-1 flex flex-col gap-6">
    <div className="flex flex-col gap-1 pb-4 border-b border-[var(--color-border-default)]">
      <h2 className="text-lg font-semibold text-[var(--color-background-default-foreground)]">
        General
      </h2>
      <p className="text-sm text-[var(--color-text-secondary)]">
        Manage your workspace settings.
      </p>
    </div>
    {/* Settings fields */}
  </div>

</div>
```

---

## Empty state page

Full-page empty state — when a list or data table has no content.

```tsx
<div className="flex flex-col gap-6 p-6">

  {/* Page header */}
  <div className="flex items-center justify-between">
    <h1 className="text-xl font-semibold text-[var(--color-background-default-foreground)]">
      Projects
    </h1>
    <Button>New project</Button>
  </div>

  {/* Empty state — centered in content area */}
  <div className="flex items-center justify-center min-h-[400px]">
    <Empty
      title="No projects yet"
      description="Create your first project to get started."
      primaryAction={<Button>New project</Button>}
    />
  </div>

</div>
```

---

## Auth page — centered card

Login, signup, or verification page — card centered on a muted background.

```tsx
<div className="min-h-screen flex items-center justify-center bg-[var(--color-background-muted)] px-4">
  <div className="w-full max-w-sm flex flex-col gap-6 p-8 rounded-[var(--radius-xl)] border border-[var(--color-border-default)] bg-[var(--color-surface-default)]">

    {/* Logo + title */}
    <div className="flex flex-col items-center gap-2">
      <div className="h-10 w-10 rounded-[var(--radius-md)] bg-[var(--color-brand-primary)]" />
      <h1 className="text-xl font-semibold text-[var(--color-background-default-foreground)]">
        Sign in
      </h1>
      <p className="text-sm text-[var(--color-text-secondary)]">
        Enter your email to continue.
      </p>
    </div>

    {/* Form */}
    <form className="flex flex-col gap-3">
      <div className="flex flex-col gap-1.5">
        <Label htmlFor="email">Email</Label>
        <Input id="email" type="email" placeholder="name@company.com" />
      </div>
      <div className="flex flex-col gap-1.5">
        <Label htmlFor="password">Password</Label>
        <Input id="password" type="password" placeholder="••••••••" />
      </div>
      <Button type="submit" className="w-full mt-1">Sign in</Button>
    </form>

    {/* Footer link */}
    <p className="text-center text-sm text-[var(--color-text-secondary)]">
      Don't have an account?{' '}
      <a href="#" className="text-[var(--color-text-link)] hover:text-[var(--color-text-link-hover)]">
        Sign up
      </a>
    </p>

  </div>
</div>
```

---

## Token quick reference for layouts

| Context | Token |
|---|---|
| Page canvas background | `color/background/default` |
| Card / panel surface | `color/surface/default` |
| Muted page bg (auth, settings) | `color/background/muted` |
| Section divider | `color/border/default` |
| Page heading text | `color/background/default/foreground` |
| Supporting / secondary text | `color/text/secondary` |
| Card border | `color/border/default` |
| Card radius | `radius/lg` |
| Section padding | `p-6` (spacing/layout/sm = 24px) |
| Gap between sections | `gap-6` |
| Gap between fields | `gap-3` or `gap-4` |
