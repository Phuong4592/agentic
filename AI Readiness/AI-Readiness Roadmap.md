
# AI-Readiness Roadmap
## Based on Design Systems for AI — 15-factor assessment

Assessed against our actual system state. All file paths, token names, component IDs, and decisions are specific to this project.

---

## Our System — What We Have

**Figma file:** `YWfTOUTpFZ0BNxHobfUqme`
**Variable collections:** Primitives (`1:2`) → Semantics (`1:129`) → Components (`17:4484`)
**Build tools:** figma-cli (render-batch + eval) · Figma MCP (variables/tokens)
**Base:** shadcn/ui · Tailwind CSS · React · Figma

**Components built in Figma (Figma-audited R1–R8):**
button · badge · avatar · tabs · checkbox · radio · slider · switch · separator · breadcrumb · tooltip · progress · table · nav-button · nav-panel · toast · input · select · combobox · accordion · alert · alert-dialog · calendar · date-picker · drawer · input-otp · pagination · sidebar · sheet · button-group · item · card · dialog · empty · skeleton

**Component docs written (33 total):**
`Accordion.md` · `Alert.md` · `Avatar.md` · `Badge.md` · `Breadcrumb.md` · `Button.md` · `Button-group.md` · `Calendar.md` · `Card.md` · `Checkbox.md` · `Combobox.md` · `Date-picker.md` · `Dialog.md` · `Drawer.md` · `Empty.md` · `Input.md` · `Input-OTP.md` · `Item.md` · `Navigation Menu.md` · `Pagination.md` · `Progress.md` · `Radio.md` · `Select.md` · `Separator.md` · `Sheet.md` · `Sidebar.md` · `Skeleton.md` · `Slider.md` · `Switch.md` · `Table.md` · `Tabs.md` · `Toast.md` · `Tooltip.md`

**Covered in shared docs:** `label`, `bubble` (Tooltip.md), `menu-dropdown` (Form-shared.md)

**Storybook (35 components — all built and verified):**
35/35 have `.tsx` + story, Figma parity confirmed, and story verified.

---

## 15-Factor Assessment

| #   | Factor                                     | Status | State |
| --- | ------------------------------------------ | ------ | ----- |
| 1   | Machine-readable component metadata (JSON) | ✅      | 33/33 `.meta.json` complete. Schema includes `relationships` fields (`mustBeChildOf`, `composedWith`, `requires`, `triggers`, `blocksWhen`, `exposesState`). Updated for Calendar time picker, Combobox Tag Input, DatePicker. |
| 2   | Code-first usage examples                  | ✅      | 33/33 `.examples.tsx` complete. Validation passes. Updated for Calendar (Presets, DateTimePicker, month/year selector), Combobox (TagInput, TagInputWithSuggestions). |
| 3   | Design decisions with rationale            | ✅      | Usage Rules + Design Decisions sections in all 33 component docs. |
| 4   | llms.txt                                   | ✅      | Navigation index — one line per file, all 33 docs, artifacts, rules, tracking. |
| 5   | Consistent naming conventions              | ✅      | kebab-case components, `Type=` / `Size=` / `State=` throughout. |
| 6   | Semantic props and variants                | ✅      | Intent-based throughout — `Type=Destructive`, `State=Invalid`, not color names. |
| 7   | Three-tier token architecture              | ✅      | Primitives → Semantics → Components built and bound in Figma. |
| 8   | Component composition explicit             | ✅      | Structure + slot sections in all 33 docs. |
| 9   | Tokens in DTCG machine-readable format     | ✅      | 344 tokens across 3 DTCG files. Style Dictionary v5 → 339 CSS vars + Tailwind v4 + ES module. |
| 10  | Figma Code Connect + MCP                   | 🟡     | figma-cli connected. No Code Connect prop mappings yet. |
| 11  | Programmatically accessible examples       | ✅      | Storybook 10.4.1 running. **35/35 components built and verified** — Figma parity confirmed, stories pass verification. |
| 12  | Accessibility in structured format         | ✅      | Accessibility sections in all 33 docs. |
| 13  | Component usage tracking                   | ❌      | Not built — requires code repo. |
| 14  | Source of truth hierarchy documented       | ✅      | `agentic-design-system.md` → `## Source of Truth`. |
| 15  | Breaking changes with migration paths      | ✅      | `CHANGELOG.md` — all breaking changes recorded. |

**Score: 13 ✅ · 0 🟡 · 1 ❌ · 1 ✖ N/A** *(F10 Code Connect not applicable — requires published package + Dev Mode)*

---

## Phase 1 — Housekeeping ✅ COMPLETE

### ✅ 1.1 — Source of truth hierarchy (Factor 14)
### ✅ 1.2 — Update `llms.txt` content (Factor 4)

---

## Phase 2 — Component Documentation ✅ COMPLETE

All 33 components audited R1–R8 and documented. Markdown docs complete.

**Key updates this session (2026-06-03):**
- `Calendar.md` — Month-Year Selector rewritten: separate `June ∨` / `2026 ∨` buttons, three views (`days | months | years`), pill grid tokens. Time picker section added: custom popover (not native OS), HH/MM scrollable columns, bidirectional sync with text input.
- `Combobox.md` — `Type=Tag Input` added as new variant: free-form chip creation, Enter/comma confirms, Backspace removes, optional suggestions dropdown. Variant matrix updated to 19 variants. Do Not table updated.
- `Tooltip.md` — `delayDuration` updated from 700ms (Radix default) to 300ms (system default).
- `combobox.meta.json`, `calendar.meta.json` — updated to reflect above changes.

---

## Phase 3 — Machine-Readable Exports ✅ COMPLETE

### ✅ 3.1 — DTCG token export (Factor 9)
344 tokens. `primitives.tokens.json` · `semantics.tokens.json` · `components.tokens.json` · `tokens.tokens.json`.

### ✅ 3.2 — `.meta.json` per component (Factor 1)
33/33 complete. Validation passes. All `ready`.

### ✅ 3.3 — Code examples per component (Factor 2)
33/33 complete. Validation passes.

---

## Phase 4 — Technical Infrastructure

### ✖ 4.1 — Code Connect (Factor 10) — NOT APPLICABLE
Figma Code Connect requires the Figma Dev Mode plugin and a published npm package linked to a Figma file. Neither condition is met for this project. Marked as invalid — will not be implemented.

```tsx
// Example for button (node 12:2893):
figma.connect(Button, 'https://www.figma.com/design/YWfTOUTpFZ0BNxHobfUqme?node-id=12:2893', {
  props: {
    variant: figma.enum('Type', {
      Default: 'default', Outline: 'outline', Secondary: 'secondary',
      Ghost: 'ghost', Link: 'link', Destructive: 'destructive'
    }),
    size: figma.enum('Size', {
      Small: 'sm', Default: 'default', Large: 'lg',
      'Icon Small': 'icon', 'Icon Default': 'icon', 'Icon Large': 'icon'
    }),
    disabled: figma.boolean('State', { Disabled: true }),
    children: figma.string('Label')
  },
  example: ({ variant, size, disabled, children }) =>
    <Button variant={variant} size={size} disabled={disabled}>{children}</Button>
});
```

### ✅ 4.2 — Style Dictionary pipeline (Factor 9)
339 resolved tokens → `css/variables.css` · `css/tailwind-v4.css` · `js/tokens.mjs`.
Run: `cd tokens/ && node sd.build.mjs`

### ✅ 4.3 — Storybook (Factor 11) — COMPLETE 2026-06-03

All 35 components have `.tsx` (token-fixed), Figma parity, a written story, and a verified story.

| Component | .tsx tokens | Figma parity | Story written | Story verified |
|---|---|---|---|---|
| Separator | ✅ | ✅ | ✅ | ✅ |
| Button | ✅ | ✅ | ✅ | ✅ |
| Badge | ✅ | ✅ | ✅ | ✅ |
| Skeleton | ✅ | ✅ | ✅ | ✅ |
| Tooltip | ✅ | ✅ | ✅ | ✅ |
| Input | ✅ | ✅ | ✅ | ✅ |
| Textarea | ✅ | ✅ | ✅ | ✅ |
| Accordion | ✅ | ✅ | ✅ | ✅ |
| Alert | ✅ | ✅ | ✅ | ✅ |
| AlertDialog | ✅ | ✅ | ✅ | ✅ |
| Avatar | ✅ | ✅ | ✅ | ✅ |
| Breadcrumb | ✅ | ✅ | ✅ | ✅ |
| Card | ✅ | ✅ | ✅ | ✅ |
| Checkbox | ✅ | ✅ | ✅ | ✅ |
| Dialog | ✅ | ✅ | ✅ | ✅ |
| Drawer | ✅ | ✅ | ✅ | ✅ |
| Sheet | ✅ | ✅ | ✅ | ✅ |
| Pagination | ✅ | ✅ | ✅ | ✅ |
| Progress | ✅ | ✅ | ✅ | ✅ |
| RadioGroup | ✅ | ✅ | ✅ | ✅ |
| Select | ✅ | ✅ | ✅ | ✅ |
| Slider | ✅ | ✅ | ✅ | ✅ |
| Switch | ✅ | ✅ | ✅ | ✅ |
| Tabs | ✅ | ✅ | ✅ | ✅ |
| Toast | ✅ | ✅ | ✅ | ✅ |
| Button Group | ✅ | ✅ | ✅ | ✅ |
| Table | ✅ | ✅ | ✅ | ✅ |
| Empty | ✅ | ✅ | ✅ | ✅ |
| Input OTP | ✅ | ✅ | ✅ | ✅ |
| Item | ✅ | ✅ | ✅ | ✅ |
| Calendar | ✅ | ✅ | ✅ | ✅ |
| Date Picker | ✅ | ✅ | ✅ | ✅ |
| Navigation Menu | ✅ | ✅ | ✅ | ✅ |
| Sidebar | ✅ | ✅ | ✅ | ✅ |
| Combobox | ✅ | ✅ | ✅ | ✅ |

Full status in `Tracking/Storybook Status.md`. ⚠️ Note (2026-06-15): a focus-state parity gap was found and fixed in RadioGroup's Choice Card story (`focus-within:border-[var(--color-border-focus)]` was missing). Spot-checks like this may surface further gaps — treat "verified" as substantially complete, not exhaustively re-audited.

**New components built this session (not in previous roadmap):**
- `Calendar` — react-day-picker v8. Custom month/year selector (3 views: days/months/years). Presets variant. DateTimePicker variant with custom HH/MM time picker (Radix Popover + scrollable columns, bidirectional sync with text input).
- `DatePicker` — trigger + Radix Popover wrapping Calendar. Types: Default, Range.
- `NavigationMenu` — @radix-ui/react-navigation-menu. Trigger + panel (List/Grid/Featured layouts). `NavigationMenuPanelLink` with title + description.
- `Sidebar` — composable (SidebarProvider, Sidebar, SidebarHeader, SidebarLogo, SidebarBrand, SidebarContent, SidebarGroup, SidebarGroupLabel, SidebarMenuItem, SidebarSubItem, SidebarBadge, SidebarFooter, SidebarToggle). Cmd+B keyboard toggle. Types: Default, Floating.
- `Combobox` — @base-ui/react. Types: Basic, Search, Tag Input (free-form chip creation). Multi-select with chips. Tag Input: Enter/comma confirms chip, Backspace removes last chip, optional suggestions dropdown, duplicates ignored.

---

## Phase 5 — Governance

### ✅ 5.1 — CHANGELOG (Factor 15)
All breaking changes recorded. Rule: every breaking change recorded before shipping.

### ✅ 5.2 — Token validation script (Factor 9 extension)
`tokens/validate-contrast.mjs` — 24 pairs · 18 pass · 6 warn · 0 fail. Known warnings are accepted constraints.

### ☐ 5.3 — Component usage tracking (Factor 13)
Not actionable until code repo exists.

---

## Summary Checklist

```
PHASE 1 — Housekeeping — COMPLETE
[x] 1.1  Source of truth hierarchy
[x] 1.2  llms.txt rewritten as navigation index

PHASE 2 — Component Documentation — COMPLETE
[x] 2.1  33/33 components audited R1–R8 + documented
[x] 2.2  All docs have Accessibility + Behavior sections
[x] 2.3  Calendar, Combobox, Tooltip docs updated 2026-06-03

PHASE 3 — Machine-Readable Exports — COMPLETE
[x] 3.1  DTCG token export — 344 tokens
[x] 3.2  .meta.json — 33/33 complete, validation passes
[x] 3.3  .examples.tsx — 33/33 complete, validation passes

PHASE 4 — Technical Infrastructure
[ ] 4.1  Code Connect — not started
[x] 4.2  Style Dictionary v5 pipeline — 339 tokens → CSS + Tailwind v4 + ES module
[x] 4.3  Storybook — 35/35 built · 35/35 verified · code audit clean · 2026-06-03

PHASE 5 — Governance
[x] 5.1  CHANGELOG.md — all breaking changes backfilled
[x] 5.2  Token validation script — 0 failures
[ ] 5.3  Component usage tracking — after Phase 4
```

**Storybook parity + verify pass complete — 35/35 components.**
Remaining open items: Phase 4.1 Code Connect (N/A), Phase 5.3 usage tracking (blocked on code repo).
