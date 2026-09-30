# Prototype Build Skill

How to build a fully functional HTML prototype in Next.js using the Agentic design system — from a flow document to a working clickable app.

This skill is built from lessons learned building real prototypes. Every phase exists because skipping it caused a real bug or wasted significant context.

---

## Requirements before starting

You need all of the following before writing a single line of code:

| Requirement | What it is |
|---|---|
| Flow document | Screen names, fields, actions, navigation, copy, validation rules |
| Component quick-reference | `Machine Readable/component-quick-reference.md` — scan all 35 components upfront |
| Component directory | `Machine Readable/component-directory.md` — maps every component to its Figma node and Storybook path |
| Token reference | `agentic-design-system.md` — spacing, colors, radius, typography |
| Layout patterns | `HTML-screen-design/layout-patterns.md` — copy-paste HTML for app shell, forms, tables, etc. |
| Running Storybook | `http://localhost:6006` — verify components before using them |

---

## Phase 0 — Plan before touching code

**Read the flow document completely.** Do not start coding until you have answered every question below.

### Screen inventory

List every screen, its route, and the primary action that exits it:

```
Screen name   | Route                  | Exit action → destination
Login         | /prototype/[app]/login | Submit → /home
Home          | /prototype/[app]/home  | Card tap → /jobs
```

### Component mapping

For each screen, map **every visible element** to a system component before writing any JSX. This is not optional — it is the step that prevents raw HTML from replacing components.

Scan `Machine Readable/component-quick-reference.md` first to know what exists. Then look up exact prop names in each component's `meta.json → implementation.exports`.

```
Screen: Pickup listing
  search bar          → Input with leading SearchLg icon
  favourite icon      → Button size=icon variant=ghost (icon buttons ARE buttons)
  cart icon           → Button size=icon variant=ghost
  discount tag        → Badge variant=outline shape=pill + Tag01 icon
  restaurant name     → plain text (no component)
  section heading     → plain text (no component)
```

**Two questions to ask for every element — in order:**

1. **Is this interactive?** — If it can be tapped, clicked, or focused, it is a button regardless of how it looks. A heart icon, a circular icon, a tag, a card — if it does something, it needs `Button`.
2. **Does a system component handle this display pattern?** — Badge for labels/tags/counts, Card for containers, Item for list rows, Alert for feedback. Visual appearance alone is not justification for skipping a component.

**Critical rules:**
- Visual difference from the component's default appearance is NOT justification for using raw HTML. Ask: *can I reach this look by styling the component?* If yes — style it.
- A "custom zone" (e.g. branded header) does not exempt everything inside it. Assess each element independently. The gradient being custom does not make the icon buttons inside it custom.
- If a component exists in the system, use it. Never build a raw div that replicates a component.

### Brand colors — explicit decision

If the screen uses colors not in our token system (e.g. a third-party brand's teal, green, orange):

**State the decision before writing code:**
- Option A — use hardcoded hex for brand-specific surfaces (gradient, active tab bg). Still use system tokens for all text, borders, and content-area colors.
- Option B — map the brand color to the nearest system token (e.g. `color/brand/primary` → brand teal).

Whichever option you choose, document it in a comment at the top of the file. Never silently mix hardcoded and token colors without acknowledging it.

### Icon verification

Before writing any icons, confirm exact names exist (icon library is **lucide-react**):

```bash
grep -iE "^declare const [A-Za-z]*Heart" node_modules/lucide-react/dist/lucide-react.d.ts
```

Run one query per icon group, capture all names, then write the import once.

### Navigation map

Draw the flow before coding transitions:

```
/login → [success] → /home
/home  → [my schedule] → /jobs
```

### Mock data file

Plan all data in one `mock-data.ts` file before building any screen. Never hardcode display strings inside components.

---

## Phase 1 — Shell setup

### File structure

```
src/app/prototype/[app-name]/
  layout.tsx       ← mobile or desktop shell
  page.tsx         ← redirect to first screen
  mock-data.ts     ← all mock data
  use-portal.ts    ← portal hook (mobile shell only)
  login/page.tsx
  home/page.tsx
  ...
```

### Mobile vs desktop shell

**For mobile prototypes (390px):** the phone frame must include a `#portal-root` div and use `transform: translateZ(0)` + `isolation: isolate` — otherwise Radix overlays (AlertDialog, Sheet, Popover, Drawer) escape the frame.

```tsx
// layout.tsx
style={{ transform: 'translateZ(0)', isolation: 'isolate' }}
// Inside the frame div, after content:
<div id="[app-name]-portal-root" />
```

```ts
// use-portal.ts
export function usePortal() {
  const [container, setContainer] = React.useState<HTMLElement | null>(null)
  React.useEffect(() => {
    setContainer(document.getElementById('[app-name]-portal-root'))
  }, [])
  return container
}
```

Pass `container={portal}` to every `AlertDialogContent`, `SheetContent`, `DrawerContent`, `PopoverContent`.

**For desktop prototypes:** standard `min-h-screen` layout. No portal override needed.

---

## Phase 2 — Mock data

Define all mock data before building any screen. Keep it in one file. Import into every page — never hardcode strings inline.

---

## Phase 3 — Build screens

Build one screen at a time. **Write the full file first, then run `tsc --noEmit` once and fix all errors together.** Running tsc after every individual fix multiplies token cost significantly.

### Per-screen checklist

**Before writing JSX:**
- [ ] Component mapping table complete — every element mapped to a component or explicitly marked as plain HTML
- [ ] All icon names confirmed with query
- [ ] Brand color decision documented if applicable
- [ ] Prop names looked up from `meta.json → implementation.exports`

**While writing JSX:**
- [ ] Every interactive element uses `Button` — size=icon for icon-only, variant=ghost for no background
- [ ] Every label/tag/count/status uses `Badge`
- [ ] Every list row uses `Item`
- [ ] Every container uses `Card` if it maps to surface/default + border/default + radius/lg
- [ ] All colors: `var(--color-*)` for content areas. If using brand hex — it is documented and scoped to brand surfaces only
- [ ] All spacing: Tailwind utilities (`p-3`, `gap-2`) — not `var(--spacing-*)` in className
- [ ] Icons: `lucide-react` only, PascalCase, `size-4` (h-4 w-4) for body-context. Icons inherit `currentColor` — let them track text color (Paired icon rule) rather than hardcoding a color class
- [ ] `aria-invalid`: always `condition || undefined` — never pass `false`
- [ ] Helper components defined outside the page function — never inside

**After writing the full file:**
- [ ] Run `npx tsc --noEmit --skipLibCheck` once
- [ ] Fix all errors in one pass before moving to the next screen

**Navigation:**
- [ ] All button/card clicks call `router.push('/prototype/[app]/[screen]')`
- [ ] Back buttons call `router.back()`

---

## Token rules — quick reference

**Colors (always CSS vars for content areas):**
```tsx
text-[var(--color-background-default-foreground)]  // primary text
text-[var(--color-text-secondary)]                 // supporting text
text-[var(--color-icon-muted)]                     // inline icons alongside text
bg-[var(--color-surface-default)]                  // card/panel surfaces
bg-[var(--color-background-default)]               // page canvas
border-[var(--color-border-default)]               // card borders
```

**Icons:**
- Standalone icon → `color/icon/default`
- Alongside muted text → `color/icon/muted`
- Status context → `color/icon/success` / `color/icon/danger` etc.
- Never use `color/text/*` on icons — different semantic group
- Body-context size → `h-4 w-4` (16px) · small/label → `h-3 w-3` (12px)

**`aria-invalid`:**
```tsx
// ✅ attribute absent when false — no red border
aria-invalid={someCondition || undefined}

// ❌ aria-invalid="false" still triggers [aria-invalid] CSS selector
aria-invalid={someCondition}
```

**Validation timing:** show invalid state on blur, not on keystroke:
```tsx
const [touched, setTouched] = React.useState(false)
<Input onBlur={() => setTouched(true)} aria-invalid={(touched && !isValid) || undefined} />
```

---

## Phase 4 — Self-audit before sharing

### Component audit
- [ ] Every interactive element uses `Button` — including icon-only elements, circular icons, and icon buttons inside branded/colored sections
- [ ] No custom divs replicating system components (Card, Alert, Item, Badge)
- [ ] All Alert instances use AlertTitle + AlertDescription + icon
- [ ] All Badge instances contain only the value, not a label+value pair
- [ ] All sole CTAs use primary button — no outline as the only button
- [ ] All list rows use the Item component — not manual flex divs
- [ ] All discount/label/count tags use Badge — not raw bordered divs

### Token audit
- [ ] Content-area colors use `var(--color-*)` — no hardcoded hex except documented brand surfaces
- [ ] Icons use `color/icon/*` tokens, not `color/text/*`
- [ ] Icon size is `h-4 w-4` in body contexts
- [ ] `aria-invalid` uses `|| undefined` everywhere
- [ ] If brand hex is used — scoped only to brand surfaces (gradient bg, active tab), never for text or borders

### React audit
- [ ] No components defined inside other components (causes focus loss on rerender)
- [ ] All helper components defined at module level
- [ ] Validation errors show on blur, not on keystroke

### Mobile shell audit (if applicable)
- [ ] `transform: translateZ(0)` + `isolation: isolate` on phone frame
- [ ] `#[app]-portal-root` div exists inside the frame
- [ ] All overlay components pass `container={portal}`

### Navigation audit
- [ ] Every CTA navigates correctly
- [ ] Back buttons work
- [ ] Empty/error states are reachable

---

## Common mistakes and fixes

| Mistake | Root cause | Fix |
|---|---|---|
| Icon button built as raw `<button>` | Looked different from Button default — assumed custom | Ask: *can I style Button to reach this look?* If yes, use Button. Visual difference is not an excuse. |
| Raw div used for discount/label tag | Built the visual first without checking if Badge exists | Component mapping in Phase 0 would have caught this |
| Everything in a colored header built as raw HTML | "Custom zone" thinking — one custom decision exempted the rest | Assess each element independently. The background being custom does not make the buttons inside it custom. |
| Hardcoded hex mixed silently with system tokens | No explicit decision made | State the brand color decision before writing code. Scope hex to brand surfaces only. |
| TS errors on icon names | Assumed names without checking | Run icon query in Phase 0 before writing any import |
| Multiple tsc fix cycles | Fixing one error at a time | Write full file first, run tsc once, fix all together |
| Alert shows plain text | Skipped Alert spec | Always use AlertTitle + AlertDescription + icon |
| Dialog escapes phone frame | No portal root | Add `transform: translateZ(0)` + portal root + `container={portal}` |
| Textarea loses focus on keystroke | Helper component defined inside page function | Define all helper components outside the page function |
| Red border on valid field | `aria-invalid={false}` | Use `aria-invalid={condition \|\| undefined}` |
| Wrong button style | Habit — "back = outline" | Single CTA = primary. Outline only when paired with primary |
| Icon wrong color | Parent text color cascading | Explicitly set `text-[var(--color-icon-*)]` on every icon |
| Validation fires on keystroke | No `touched` state | Use `onBlur` + `touched` for all validation display |

---

## Related documents

| Document | When to read |
|---|---|
| `Machine Readable/component-quick-reference.md` | Phase 0 — scan all 35 components before mapping |
| `Machine Readable/component-directory.md` | Phase 0 — get Figma node, Storybook path, tsx file |
| `Machine Readable/artifacts/components/[name].meta.json` | Phase 3 — get exact prop names from `implementation.exports` |
| `HTML-screen-design/layout-patterns.md` | Phase 1 — copy-paste shells for app shell, forms, auth page |
| `HTML-screen-design/screen-stories-guide.md` | Phase 1 — file structure, naming |
| `HTML-screen-design/flow-guide.md` | Multi-step flow composition |
| `agentic-design-system.md` | Token architecture, spacing scale, color semantic rules |
