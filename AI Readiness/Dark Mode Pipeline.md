# Dark Mode Pipeline

## Status snapshot (2026-06-15)

**Done — Figma side:**
- Added "Dark" mode (`599:0`) to **Semantics** collection (`VariableCollectionId:1:129`), alongside existing "Light" (`1:1`).
- 72 Semantics color variables given Dark-mode values, applied as proper `VARIABLE_ALIAS` references into **Primitives** (not hex) — matches Light mode's alias-to-Primitives pattern. Mapping lives in `/tmp/dark-mode-script.js` (figma-cli `eval -f`).
- Corrected 2 of the 72: `color/background/inverted` and `color/background/inverted/foreground` reverted to "same in both modes" (Light=Dark) — this token pair is a modal/sheet/drawer **backdrop scrim**, not a themed surface, so it must stay a dark overlay in both Light and Dark.
- Confirmed: spacing, radius, opacity, typography tokens need **no** per-mode values (mode-independent), even though Figma auto-copies a Dark column for every variable in the Semantics collection (harmless, can't be removed per-variable).

**Not done yet — everything below.**

---

## Open items in Figma (do before exporting)

1. **`color/chart/1-5`** — custom hexes, no Primitive scale to alias into. Need Dark variants decided (5 colors) before export, or accept Light=Dark for charts as an interim default.
2. **5 foreground-contrast risks** against new Dark fills (~2.8:1, below 3:1 AA):
   - `color/brand/primary/foreground` (white on `blue/400`)
   - `color/status/danger/foreground` (white on `red/400`)
   - `color/status/info/foreground` (white on `blue/400`)
   - `color/status/success/foreground` (`zinc/900` on `green/400`)
   - `color/status/warning/foreground` (`zinc/900` on `yellow/400`)
   Resolve via `tokens/validate-contrast.mjs` once Dark values are exported (see Phase 4).
3. **Components collection** (`VariableCollectionId:17:4484`, 44 vars, currently single mode "Mode 1" = `17:0`):
   - ~16 sizing/radius tokens (`button/size/*`, `badge/Badge-height-*`, `table/table-cell-*`) → alias Primitives, mode-independent, **no action**.
   - ~26 color tokens (`button/primary/bg/bg`, `button/outline/border/*`, `button/link/fg/*`, etc.) → alias into Semantics (now mode-aware). For Dark mode to actually propagate when a frame is set to Dark, **Components collection needs its own "Dark" mode added** (same name, so Figma's cross-collection mode-name matching resolves the Semantics alias via Dark too). No alias *targets* need to change — Figma auto-copies existing aliases into the new mode column and they resolve correctly because the target variable is now mode-aware.
   - 2 exceptions: `tooltip/bg` → `zinc/900` and `tooltip/fg` → `white`, aliased **directly to Primitives** (bypass Semantics). Decide: keep tooltip permanently dark in both themes (current behavior, probably fine), or re-point through a Semantics pair so it adapts.

---

## Phase 1 — Finalize Figma token values

- [ ] Decide & apply `color/chart/1-5` Dark values (5 vars, Semantics Dark mode `599:0`)
- [ ] Resolve the 5 foreground-contrast tokens (either lighten the Dark fill one more step, or pick a higher-contrast foreground)
- [ ] Add "Dark" mode to Components collection (`17:4484`), confirm aliases resolve through Semantics Dark
- [ ] Decide tooltip Primitive-direct exception (`tooltip/bg` / `tooltip/fg`)
- [ ] Spot-check via figma-cli: re-run the verification snippet on `color/brand/primary`, `color/background/default`, `color/background/inverted` to confirm values persisted (we hit one unexplained reversion this session — verify before moving to export)

---

## Phase 2 — Export both modes from Figma → DTCG JSON

Current `semantics.tokens.json` is single-valued DTCG (e.g. `"$value": "{color.zinc.50}"`, no mode key). Need a mode-aware export.

**Approach:** keep `semantics.tokens.json` as-is (Light = current default), add a parallel **`semantics.dark.tokens.json`** containing only the tokens whose Dark value differs from Light (the ~70 that actually changed, post chart/foreground fixes — `background/inverted` pair excluded since Light==Dark).

- [ ] Write export script (figma-cli `eval`): for each of the 110 Semantics variables, read `valuesByMode['1:1']` and `valuesByMode['599:0']`; resolve alias chain to Primitive token name (e.g. `VariableID:1:20` → `color.blue.400`); emit DTCG `$value: "{color.blue.400}"` refs.
- [ ] Output two files:
  - `semantics.tokens.json` — unchanged (Light values, current source of truth)
  - `semantics.dark.tokens.json` — only the differing tokens, same DTCG path structure, e.g.:
    ```json
    {
      "color": {
        "brand": {
          "primary": { "$value": "{color.blue.400}", "$type": "color" }
        }
      }
    }
    ```
- [ ] If Components collection gets Dark-mode color values (Phase 1), export `components.dark.tokens.json` the same way for the ~26 color tokens.

---

## Phase 3 — Extend `sd.build.mjs` for a `.dark` output

`sd.build.mjs` currently builds one pass: `primitives + semantics + components` → `:root` (outputReferences: false → fully resolved hex).

- [ ] Add a second Style Dictionary instance / platform pass:
  - `source`: `primitives.tokens.json`, `semantics.tokens.json`, `semantics.dark.tokens.json` (override), `components.tokens.json`, `components.dark.tokens.json` (override, if applicable)
  - Style Dictionary v5 supports layered sources where later files override earlier `$value`s for matching token paths — confirm override order works as expected with a quick test build before wiring fully.
  - `css/variables` format, `options.selector: '.dark'`, `outputReferences: false` (resolved hex, same as Light pass)
  - Output appended to `output/css/variables.css` (or a separate `variables-dark.css` imported after the light one — simpler to debug, pick one and be consistent)
  - Mirror the same for `tailwind-v4` platform if dark tokens need to surface in the `@theme` block too (likely not necessary if `.dark` overrides are plain CSS vars — Tailwind v4's `@custom-variant dark (&:is(.dark *))` just needs the `--color-*` custom properties to differ under `.dark`)
- [ ] Run `node sd.build.mjs`, confirm `output/css/variables.css` now has both `:root { ... }` and `.dark { ... }` blocks with the expected differing values
- [ ] Re-run `tokens/validate-contrast.mjs` against the `.dark` block (extend script to accept a selector/mode argument if it's currently `:root`-only)

---

## Phase 4 — Wire up `.dark` class toggle in `agentic-ui`

- [ ] Install `next-themes` (not currently a dependency)
- [ ] Add `ThemeProvider` (from `next-themes`) wrapping the app in `src/app/layout.tsx`, `attribute="class"`, `defaultTheme="system"`, `enableSystem`
- [ ] Confirm `tailwind-v4.css`'s `@custom-variant dark (&:is(.dark *))` directive (already present, line 6) now has matching `.dark { --color-*: ... }` overrides from Phase 3 to key off of
- [ ] Add a theme toggle control to Storybook preview (decorator) and/or a sample page, so component dark-mode rendering can be visually checked per-component
- [ ] Spot-check the components most affected by the Dark remap: Radio (brand/primary), Alert/AlertDialog (backdrop — the scrim fix from this session), Button (all variants), Badge/status colors (the 5 contrast-risk tokens from Phase 1)

---

## Phase 5 — Documentation & governance

- [ ] Update `agentic-design-system.md` — add a "Dark Mode" section to the token rules: mode list (Light/Dark), where mode-keyed values live (`*.dark.tokens.json`), build output (`.dark` selector), toggle mechanism (`next-themes`, `.dark` class on `<html>`)
- [ ] Update relevant component `.meta.json` `darkMode` fields (currently most say "No known dark mode differences — all tokens are semantic and mode-aware" — true for most, but flag any component using `color/background/inverted` as backdrop, or chart colors, explicitly)
- [ ] `CHANGELOG.md` entry — dark mode token additions are additive/non-breaking (existing `:root` values unchanged), but note as a notable feature addition
- [ ] Re-run full `validate-contrast.mjs` for both modes, record pass/warn/fail counts in `AI-Readiness Roadmap.md` (Phase 5.2 already tracks this for Light — extend to Dark)

---

## Open design decisions (need a call before Phase 1 can close)

| Decision | Options | Notes |
|---|---|---|
| `color/chart/1-5` dark values | (a) keep same as Light (b) lighten/desaturate each by one step (c) pick new custom hexes | No Primitive scale exists for these — whatever is chosen will be literal hex, not alias |
| 5 foreground-contrast tokens | (a) lighten Dark fill one more step (e.g. brand/primary Dark → `blue/300` instead of `400`) (b) keep fill, change foreground (e.g. `zinc/950` instead of `white`/`zinc/900`) | (a) cascades to other tokens aliasing the same fill — check side effects first |
| Tooltip Primitive-direct exception | (a) leave as-is (always dark tooltip) (b) re-point to Semantics pair so it adapts per mode | (a) is simplest and matches common UI convention (tooltips often stay dark) |
| `.dark` CSS output location | (a) append to `variables.css` (b) separate `variables-dark.css` | (b) easier to diff/debug during initial rollout |
