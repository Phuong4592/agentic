# Flow Guide

How to compose and document multi-step user flows in Storybook.

---

## What is a flow in Storybook

A flow is a group of screen stories that represent the steps a user moves through to complete a task. In Storybook, a flow is a folder of screen story files under a shared `Screens/[Feature]/` title — each story is one state of the flow.

Unlike Figma, Storybook flows are:
- **Live** — real HTML and components, not static screenshots
- **Navigable** — each step is a separate story in the sidebar
- **Interactive** — components respond to hover, focus, and click

---

## Flow file structure

```
src/stories/screens/
  CreateProject/
    Step1-Details.stories.tsx      ← User fills out project details
    Step2-Members.stories.tsx      ← User adds team members
    Step3-Confirm.stories.tsx      ← User reviews and confirms
    Success.stories.tsx            ← Completion state
    Error.stories.tsx              ← Failure state (optional)
```

Each file title:
```
Screens/CreateProject/Step1-Details
Screens/CreateProject/Step2-Members
Screens/CreateProject/Step3-Confirm
Screens/CreateProject/Success
```

---

## Step story pattern

Each step in a flow should show both its default state and its key variation (invalid, empty, loaded):

```tsx
// Step1-Details.stories.tsx

export const Default: Story = {
  render: () => <Step1 />,          // empty form, ready to fill
};

export const Filled: Story = {
  render: () => <Step1 filled />,   // all fields populated
};

export const Invalid: Story = {
  render: () => <Step1 invalid />,  // form submitted with errors
};
```

**Rule:** The `Default` export always shows the state the user arrives at, not the completed state.

---

## Linking between steps

Use Storybook's `play` function to simulate user interaction within a step, and annotate the next step with a comment.

```tsx
import { userEvent, within } from '@storybook/test';

export const FilledAndContinue: Story = {
  render: () => <Step1 />,
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    // Fill the form
    await userEvent.type(canvas.getByLabelText('Project name'), 'Agentic UI');
    await userEvent.type(canvas.getByLabelText('Description'), 'Design system');
    // Click continue — in real flow this would navigate to Step2
    await userEvent.click(canvas.getByRole('button', { name: 'Continue' }));
  },
};
```

For navigation between steps (which Storybook doesn't handle natively), add a comment in the story:

```tsx
export const Default: Story = {
  name: 'Step 1 — Details',
  render: () => (
    <div>
      {/* Next step: Screens/CreateProject/Step2-Members */}
      <Step1 />
    </div>
  ),
};
```

---

## Flow index story

Add an index story at the top of the flow that documents the full sequence:

```tsx
// index.stories.tsx

export const FlowOverview: Story = {
  name: '0 – Flow overview',
  render: () => (
    <div className="p-8 flex flex-col gap-6 bg-[var(--color-background-default)]">
      <h1 className="text-xl font-semibold text-[var(--color-background-default-foreground)]">
        Create Project Flow
      </h1>
      <ol className="flex flex-col gap-2 text-sm text-[var(--color-text-secondary)]">
        <li>1. Step1-Details — User enters project name and description</li>
        <li>2. Step2-Members — User adds team members</li>
        <li>3. Step3-Confirm — User reviews and submits</li>
        <li>4. Success — Project created confirmation</li>
      </ol>
      <p className="text-xs text-[var(--color-text-secondary)]">
        See each step as a separate story in this folder.
      </p>
    </div>
  ),
};
```

Name it `'0 – Flow overview'` so it sorts to the top in the Storybook sidebar.

---

## State transitions within a step

When a user action changes the visible state within a step (e.g. opening a modal, showing a toast), use named exports to show each state:

```tsx
// Step3-Confirm.stories.tsx

export const Default: Story = {
  render: () => <Step3 />,
  name: 'Default — review screen',
};

export const WithDeleteConfirm: Story = {
  render: () => <Step3 deleteDialogOpen />,
  name: 'With delete confirmation dialog open',
};

export const Submitting: Story = {
  render: () => <Step3 submitting />,
  name: 'Submitting — loading state',
};
```

---

## Complete flow example

```
Screens/
  Auth/
    0-FlowOverview          ← index — documents the full flow
    Login                   ← entry point
    Login-Error             ← invalid credentials
    ForgotPassword          ← triggered from Login
    ResetPassword           ← triggered from email link
    ResetPassword-Success   ← completion
```

Each story is independently navigable in Storybook. Together they tell the full story of the auth flow.

---

## Flow vs component story — decision guide

| Question | Answer |
|---|---|
| Am I showing one component's states? | → Component story in `src/stories/[Component].stories.tsx` |
| Am I showing a real page with multiple components? | → Screen story in `src/stories/screens/[Feature]/` |
| Am I showing how multiple screens connect? | → Flow folder under `src/stories/screens/[Feature]/` |
| Is this a one-off composition for a specific project? | → Screen story, title `Screens/[ProjectName]/[Screen]` |

---

## Checklist for a complete flow

- [ ] Flow folder created under `src/stories/screens/[Feature]/`
- [ ] Index story (`0-FlowOverview`) documents the step sequence
- [ ] Each step has at least: `Default`, `Filled`/`Loading` where applicable, `Invalid`/`Error`
- [ ] Success and Error terminal states included
- [ ] `parameters.layout = 'fullscreen'` on all screen stories
- [ ] Providers added to decorators (TooltipProvider, SidebarProvider, etc.)
- [ ] Mock data defined at file top — not inline in render functions
- [ ] Next step referenced in a comment when steps link sequentially
