# Screen Flow

How to build multi-screen flows and connect screens in Figma using the Agentic design system.

---

## What is a screen flow

A screen flow is a sequence of artboards connected by prototype links that show how a user moves through a feature — from entry point to completion. It documents both the happy path and key alternative states (errors, empty states, loading).

---

## Frame naming convention

Consistent naming makes flows navigable and makes Dev Mode references reliable.

**Format:** `[Breakpoint] / [Feature] – [Screen name]`

```
Desktop / Auth – Login
Desktop / Auth – Login (Error)
Desktop / Auth – Forgot password
Desktop / Auth – Reset password
Desktop / Dashboard – Overview
Desktop / Dashboard – Overview (Empty)
Mobile / Auth – Login
Mobile / Dashboard – Overview
```

**Rules:**
- Always prefix with the breakpoint: `Desktop /`, `Tablet /`, `Mobile /`
- Use ` – ` (space-dash-space) to separate feature from screen name
- Append state in parentheses: `(Error)`, `(Empty)`, `(Loading)`, `(Success)`
- Keep names short — 3–5 words after the breakpoint prefix

---

## Flow structure

Every flow should include these screen types:

| Screen type | Naming | Notes |
|---|---|---|
| Entry point | `– [Name]` | First screen the user sees |
| Interaction state | `– [Name] (Hover/Focus)` | Show only when non-obvious |
| Error / Invalid | `– [Name] (Error)` | Always include for forms |
| Empty state | `– [Name] (Empty)` | Include when list/table can be empty |
| Loading / Skeleton | `– [Name] (Loading)` | Include for async data |
| Success / Completion | `– [Name] (Success)` | Final state after action |

---

## Prototype connections

Use Figma's Prototype panel to connect screens.

**How to connect:**
1. Select the trigger element (button, link, input)
2. In the Prototype panel → drag the blue handle to the destination frame
3. Set the interaction: `On Click` / `On Hover`
4. Set the animation: `Smart Animate` for same-component state changes, `Dissolve` or `Move In/Out` for screen transitions

**Standard animations:**

| Transition type | Animation |
|---|---|
| Navigate to new screen | `Move In` — direction matches the navigation direction |
| Go back | `Move Out` — reverse direction |
| Modal / Dialog opens | `Dissolve` + `Smart Animate` zoom |
| Drawer opens | `Move In` from the drawer's side |
| State change (same screen) | `Smart Animate` |
| No animation needed | `Instant` |

---

## Overlay screens

Dialogs, drawers, sheets, and tooltips are not separate frames — they are positioned floating above the base screen frame within the same Figma page.

**How to set up:**
1. Keep the base screen frame as-is
2. Create a separate frame for the overlay content (e.g. `Dialog – Delete project`)
3. In the Prototype panel on the trigger: set destination to the overlay frame, set `Open Overlay` as the action
4. Position the overlay centered on the base screen (or slide-in for drawers)

**Do not** create a whole new artboard just to show an open modal — use Overlay connections instead.

---

## Flow organisation on the Figma canvas

Arrange screens in reading order: left to right, top to bottom.

```
[Entry]  →  [Step 1]  →  [Step 2]  →  [Success]
                ↓
           [Step 1 Error]
                ↓
           [Step 1 Empty]
```

**Rules:**
- Main happy path: left to right on a single row
- Error and alternative states: below the step they branch from
- Mobile and desktop versions of the same flow: separate rows, desktop above mobile
- Add a Flow label frame at the top-left of each flow group: `Flow: [Name]` in a coloured rectangle

---

## Page structure for flows

Each Figma page should contain one complete user flow. Split into multiple pages if a feature has distinct sub-flows.

**Recommended structure:**

```
Page: "Auth Flow"
  Row 1: Desktop — Login → Forgot → Reset → Success
  Row 2: Mobile  — Login → Forgot → Reset → Success
  Overlays (floating): Confirm email dialog

Page: "Dashboard Flow"
  Row 1: Desktop — Overview → Detail → Edit → Save (Success)
  Row 2: Mobile  — Overview → Detail
  Overlays: Delete confirm dialog, Settings sheet
```

---

## Connecting to Storybook for development

After finalising a flow, reference the Storybook stories for each component used:

1. Open `Machine Readable/component-directory.md`
2. Find each component used in the flow
3. Note the Storybook title — developers open `http://localhost:6006` and navigate there for the exact implementation
4. For state-specific behaviour (e.g. Invalid state ring, dropdown timing), reference the `meta.json` notes

**Handoff checklist for a flow:**

- [ ] All screens named correctly with breakpoint prefix
- [ ] Happy path fully connected with prototype links
- [ ] Error and empty states included for every form and data list
- [ ] Loading states shown for async operations
- [ ] Overlays set up as Figma Overlay connections (not separate artboards)
- [ ] Flow label frame added to the canvas
- [ ] Mobile and desktop versions aligned side by side
- [ ] Each component's Storybook story referenced in handoff notes
