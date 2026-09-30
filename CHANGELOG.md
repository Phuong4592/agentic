# Changelog

All breaking changes, additions, and removals to the Agentic Design System.

Format: newest first. Each entry includes what changed, the migration path, and the reason.

> **Rule:** Every breaking change must be recorded here before it ships. A breaking change is any rename, removal, or token alias update that would cause a consumer (code, Figma instance, design spec) to break silently.

---

## 2026-06-03

### Added

**`AlertDialog.md` + `alert-dialog.meta.json`** — documentation separated from Alert
- AlertDialog now has its own dedicated markdown doc and meta.json (v3 schema)
- Previously shared with `Alert.md` and `alert.meta.json`
- No Figma or code changes — documentation split only

**`Textarea.md` + `textarea.meta.json`** — documentation separated from Input
- Textarea now has its own dedicated markdown doc and meta.json (v3 schema)
- Previously shared with `Input.md` and `input.meta.json`
- No Figma or code changes — documentation split only

**`Type=Tag Input` on Combobox** — new variant
- Free-form chip creation — user types and presses Enter or comma to create chips
- No predefined options list required (optional suggestions dropdown available)
- Backspace removes last chip when input is empty; duplicates silently ignored
- Figma: variant not yet built — Storybook and docs complete

**meta.json v3 schema** — all 35 components migrated
- New fields: `meta.storyFile`, `meta.knownIssues`, `meta.changelog`, `meta.darkMode`, `implementation`, `storybook`
- `tokens.states.invalid` added for all 7 form field components — documents red ring spec
- `behavior.typeGuide` added for components with Type variants
- `examples` field removed — examples.tsx files deleted, examples now live in Storybook stories

**Calendar `Type=Month-Year Selector`** — 3-view navigation
- Header now shows separate `Month ∨` and `Year ∨` buttons — each toggles independently
- Three views: `days | months | years`
- Months view: 4×3 pill grid. Years view: 4×3 pill grid (12-year range)
- Prev/next arrows navigate month / year / 12-year range depending on active view

**Calendar `Type=Date-Time Picker`** — custom time picker
- Replaced native OS `<input type="time">` with a custom Radix Popover
- Two interaction modes: type `HH:MM` directly OR click Clock icon to open HH/MM scrollable columns
- Bidirectional sync: typing updates columns; selecting from columns updates the input
- Minutes in 5-minute increments (00, 05, 10 … 55)
- Clock icon moved to trailing position

### Changed

**SelectItem — checkmark removed** — visual change, no API change
- Before: shadcn default — absolute positioned checkmark indicator (`pl-8` left padding)
- After: no checkmark — selected state uses `color/brand/primary` text color only (transparent bg)
- Spec: `State=Selected` → container fill transparent, label fill `color/brand/primary`
- Migration: no prop changes needed

**SelectContent — dropdown sizing updated**
- Container radius: `radius/md` → `radius/lg`
- Viewport padding: `p-1` (4px) → `p-0.5` (2px) — matches `spacing/component/xxs`
- Gap between items: none → `gap-0.5` (2px)
- Item height: auto → `h-8` (32px fixed)
- Item horizontal padding: `pl-8 pr-2` → `px-2` (8px)
- Item radius: `radius/sm` → `radius/md`
- Same changes applied to Combobox popup and Tag Input suggestions dropdown

**Invalid focus ring — all form fields** — visual fix, no API change
- Before: blue `color/ring` glow appeared on focused invalid fields (wrong)
- After: red `color/border/error` glow appears — blue ring suppressed
- Components fixed: Select, Input, Textarea, Combobox, Checkbox, RadioGroup, InputOTP
- Implementation: `[&[aria-invalid]]` compound CSS selector to beat `data-[state=open]` and `focus-visible` specificity

**DatePicker trigger icon — moved to trailing position**
- Before: leading icon (left of placeholder text)
- After: trailing icon (right of placeholder text) — matches standard date input convention
- No API change

**InputOTP active slot — ring-2 pattern (shadcn)**
- Before: inset shadow on slot + overflow-hidden on group → radius mismatch at corners
- After: `ring-2 ring-offset-0 z-10` on active slot + per-slot borders, no overflow-hidden on group
- Active slot ring: `color/border/focus` (default) or `color/border/error` (invalid group)
- Visual result: seamless ring matching slot corner radius — no mismatch

**NavigationMenu panel link — foreground token corrected**
- Before: `color/surface/default/foreground` (resolves same in light mode but wrong in dark)
- After: `color/surface/overlay/foreground` (correct paired token — panel fills with `color/surface/overlay`)

**Tooltip `delayDuration`** — system default changed
- Before: Radix UI default 700ms
- After: system default 300ms — set `delayDuration={300}` on `TooltipProvider` at app root
- `skipDelayDuration` remains at 300ms

### Fixed

**Calendar row spacing** — 2px gap between week rows
- `[&_tbody_tr+tr]:mt-0.5` on table — `mt-*` on `<tr>` doesn't work in table layout without `display:flex`

**Calendar outside day selected foreground**
- Outside days that are also selected (range-start/end) now show correct foreground
- `aria-selected:opacity-100 aria-selected:text-[color/brand/primary/foreground]` on `day_outside`

**AlertDialog footer gap** — `FullWidthFooter` story
- Gap between stacked buttons: `spacing/component/xs` (4px) → `spacing/component/sm` (8px)

**Drawer Responsive story** — rewritten with `useIsDesktop()` hook
- Before: CSS class overrides on Vaul drawer (ineffective — Vaul uses internal fixed styles)
- After: `useIsDesktop()` hook — renders `Dialog` on desktop, `Drawer` on mobile

### Removed

**`examples.tsx` files** — all 33 deleted
- Examples now live in `src/stories/*.stories.tsx`
- `storybook.stories` field in each meta.json lists all named story exports
- Validator updated: checks `storybook.file` instead of `examples.source`

---

## 2026-06-02

### Fixed

**Button `variant="destructive"` focus ring** — visual correction, no API change
- Before: inherited base `focus-visible:ring-2 ring-[var(--color-ring)] ring-offset-2` (blue ring + gap)
- After: `focus-visible:ring-0 [box-shadow:0_0_0_3px_rgba(220,38,38,0.4)]` — red glow, 3px spread, no offset
- Matches `focus/destructive` Figma effect style (red @40%, spread 3px)
- Also removed `ring-offset-2` from base (all variants — no gap between element and ring per spec)
- No prop or class name changes — visual only

### Added

**`InputOTPGroup` `invalid` prop** — non-breaking
- New: `invalid?: boolean` on `InputOTPGroup`
- When true: group border switches to `color/border/error`; active slots switch inset shadow to `color/border/error`
- Before: invalid state required fragile `className` override (unreliable with tailwind-merge on arbitrary CSS var values)

---

## 2026-05-28

### Added

**`color/status/offline`** · **`color/status/offline/foreground`** — new Semantics tokens
- `color/status/offline` → `color/zinc/200` (light gray fill)
- `color/status/offline/foreground` → `color/zinc/900` (dark text/icon on offline fill)
- Use when: offline presence dot in `avatar-indicator`, `Offline` badge variant, any UI element communicating a user or entity is not currently available
- Does not follow the 4-variant pattern (no `offline-subtle`) — a subtle tint would be too low-contrast for presence communication

**`color/surface/muted`** — new Semantics token
- Fill for muted component surfaces (avatar fallback bg, skeleton bones, dimmed containers)
- Distinct from `color/background/muted` (page canvas) — use surface tokens inside components, background tokens on the page

**Badge `Offline` variant** — new component variant
- Shape: Pill only
- Structure: dot-slot + leading-icon (hidden by default) + label — same as Online
- Dot fill: `color/status/offline`
- Wrap in `aria-live="polite"` — presence state updates in real time

**Badge `Notification counters` variant** — new component variant
- Shape: Pill, Size: Small only
- Structure: number-only — no icon slots
- Fill: `color/brand/primary` — same as Default
- Always wrap in `role="status"` or `aria-live="polite"` — count updates in real time
- Cap display at "99+" to prevent layout overflow

**`Skeleton`** — new component (code-only)
- shadcn `Skeleton` — single div, `animate-pulse`, `bg-muted`
- No Figma component set — Figma page kept as reference layout documentation
- No component tokens — uses Tailwind `bg-muted` class directly

### Changed

**`avatar-badge` → `avatar-indicator`** ⚠️ Breaking
- Figma component renamed from `_avatar-badge` to `avatar-indicator`
- Redesigned from scratch — removed `Type=Dot` / `Type=Icon` variants and ring stroke
- New variant matrix: `type=online` / `type=offline` × `size=Default` / `size=large` (4 variants total)
- Token changes: `color/status/success` (online) · `color/status/offline` (offline) — no stroke, no ring
- Size change: `size=Default` is now 8×8px (was 12×12px) · `size=large` is 12×12px
- Size pairing: use `size=Default` with `Size=SM` avatars · `size=large` with `Size=Default` and `Size=LG` avatars
- Migration: update all Figma instances from `_avatar-badge` → `avatar-indicator`; update `type=` prop; remove any ring stroke overrides

### Removed

**Badge `Avatar` variant** — removed
- Replaced by `avatar-indicator` sub-component inside the `avatar` component
- Migration: use `avatar` component with `Badge=True` and `avatar-indicator` type set to `online` or `offline`

---

## Pre-2026-05-28 (backfill — exact dates not recorded)

### Changed

**Button `Size=Medium` → `Size=Default`** ⚠️ Breaking
- Figma variant prop value renamed
- Affects: all button instances with `Size=Medium`
- Migration: change `Size=Medium` → `Size=Default` on all instances
- Reason: align with shadcn naming convention (`size="default"`)

**Button `Size=Icon Medium` → `Size=Icon Default`** ⚠️ Breaking
- Same as above for icon-only buttons
- Migration: change `Size=Icon Medium` → `Size=Icon Default`
- Reason: align with shadcn naming convention

**`color/icon/success` alias** — alias target updated ⚠️ May affect rendering
- Old alias: `color/zinc/900` (near-black — failed WCAG AA on success-subtle backgrounds)
- New alias: `color/green/700` (dark green — passes WCAG AA)
- Migration: no prop changes — token rebinding only. Re-inspect any icon using `color/icon/success` to verify contrast in context.
- Reason: WCAG contrast audit — 22 pairs checked, 5 remaps applied

### Removed

**Button `Size=Default (32px)`** ⚠️ Breaking
- 32px height button size removed
- Migration: use `Size=Small` (36px) for compact contexts
- Reason: adopted shadcn height scale — 32px is not part of the standard scale

**Button `Size=Icon (32×32)`** ⚠️ Breaking
- 32px icon-only button size removed
- Migration: use `Size=Icon Small` (36px)
- Reason: same as above

---

## How to record a future breaking change

Add an entry under the current date before the change ships:

```markdown
## YYYY-MM-DD

### Changed / Removed / Added

**[Component] [what changed]** ⚠️ Breaking  (omit ⚠️ if non-breaking)
- What it was before
- What it is now
- Migration: exact steps to update consumers
- Reason: why the change was made
```

Categories:
- **Added** — new component, variant, or token (non-breaking unless it changes existing behavior)
- **Changed** — rename, restructure, token alias update
- **Removed** — variant, prop value, or token deleted
- **Fixed** — bug fix that doesn't change the API
