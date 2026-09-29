#theme #design-system #tokens

# Agentic Design System — Theme Reference

The complete "look & feel" definition of the system, captured at the **primitive** layer. Because semantics and components *alias* primitives, changing the values below re-themes the entire system downstream — no semantic edits needed.

> A theme = **{ brand ramp · neutral ramp · radius base · spacing base · type ratio }**

---

## Theme at a glance

| Lever | Current value | Personality it sets |
|---|---|---|
| **Brand hue** | Blue — anchor `/500` = `#2b7fff` | Identity / primary action |
| **Neutral** | **Zinc** (cool gray) `/50–/950` | Overall mood — cool, modern, neutral |
| **Radius base** | `8px` (`radius/base` = `radius/lg`) | Friendly — not sharp, not pill-soft |
| **Spacing base** | `4px` unit, linear (8-pt grid) | Balanced density |
| **Type** | Inter · Tailwind size scale | Clean, screen-native |

Mood summary: **cool-neutral, modern, friendly-professional** — shadcn/Tailwind-aligned defaults with a blue brand.

---

## 1. Brand ramp — Blue

Anchor = `/500`. To re-theme the brand, **regenerate the whole ramp from a new hue** (don't swap `/500` alone).

| Step | Hex | Role |
|---|---|---|
| 50 | `#eef6ff` | info-subtle bg |
| 100 | `#dbeafe` | secondary fill |
| 200 | `#bedbff` | |
| 300 | `#8ec5ff` | |
| 400 | `#51a2ff` | |
| **500** | **`#2b7fff`** | **brand/primary · ring · anchor** |
| 600 | `#155dfc` | icon/info · primary-hover |
| 700 | `#1447e6` | secondary/foreground · primary-active |
| 800 | `#193cb8` | |
| 900 | `#1c398e` | |
| 950 | `#162455` | |

### Ramp contrast standard — `/500` acceptance criteria

**Read this before recommending any new brand or status color.** The `/500` anchor is the load-bearing step — it must clear specific WCAG contrast levels or the theme breaks accessibility.

| Use of `/500` | WCAG rule | Required ratio |
|---|---|---|
| **Fill / UI boundary** (button surface, ring, selected border, large 18px+ label) | 1.4.11 Non-text + 1.4.3 large | **≥ 3:1** (hard floor) |
| **Small label text on the fill** (≤14px button label) | 1.4.3 AA normal text | **≥ 4.5:1** (ideal) |

Measure `/500` against **both**:
1. its paired foreground (usually white `#ffffff`), and
2. the page background (`color/background/default` — white light / zinc-950 dark).

**Decision rule when picking a new `/500`:**
- If `/500` vs white ≥ **4.5:1** → ✅ perfect — usable as fill *and* small-text, zero caveats.
- If `/500` vs white is **3:1–4.5:1** → ⚠️ acceptable as a **fill only**. Button labels technically miss AA at ≤14px. Mitigate by using a **darker step (`/600`–`/700`) for any text, icon, or link** use of the brand color.
- If `/500` vs white < **3:1** → ❌ reject. Pick a darker hue or shift the anchor.

**Current blue/500 (`#2b7fff`) = 3.8:1 vs white** → middle band: ✅ fill, ❌ small text. This is an accepted brand constraint. That's exactly why `color/text/link` and `color/icon/info` resolve to **blue/600**, not /500 — the system bumps to a darker step wherever the brand becomes text/icons.

**The fill-vs-text split (apply to every accent ramp):**
- `/500` = fills (3:1 floor, 4.5:1 ideal)
- `/600`–`/700` = the accessible text / icon / link variant (must clear 4.5:1 vs the background it sits on)

**Also applies to status anchors:** red/green/yellow `/500` follow the same rule. Note yellow can't reach 4.5:1 with white, so warning uses **dark text** (zinc/900) on the fill, and icons/text remap to yellow/700 — same darker-step pattern.

---

## 2. Neutral ramp — Zinc (cool gray)

The biggest "mood per effort" lever. Swap to slate/stone/gray for a warmer or cooler feel. Drives nearly all surfaces, borders, and text.

| Step | Hex | Role (light · dark) |
|---|---|---|
| 50 | `#fafafa` | surface/raised · sidebar bg · **inverted (dark)** |
| 100 | `#f4f4f5` | background/muted · accent |
| 200 | `#e4e4e7` | border/default |
| 300 | `#d4d4d8` | input/border |
| 400 | `#a1a1aa` | text/disabled · placeholder (dark) |
| 500 | `#71717a` | text/secondary · placeholder (light) |
| 600 | `#52525b` | input/border (dark) |
| 700 | `#3f3f46` | input/bg (dark) · border (dark) |
| 800 | `#27272a` | surface/overlay (dark) · surface/muted (dark) |
| 900 | `#18181b` | default/foreground · surface/default (dark) · **inverted (light)** |
| 950 | `#09090b` | background/default (dark — page canvas) |

**Dark-mode zinc elevation:** `950` page → `900` cards/dialogs → `800` dropdowns/disabled → `700` active inputs.

---

## 3. Status ramps

Fixed semantic meaning — only re-theme if rebranding status colors (rarely).

| Status | Anchor `/500` | Notes |
|---|---|---|
| **Danger** — Red | `#ef4444` | brand/destructive + status/danger both anchor here |
| **Success** — Green | `#22c55e` | icons remap to `/700` (`#15803d`) for AA contrast |
| **Warning** — Yellow | `#eab308` | uses dark text (zinc/900) — yellow fails WCAG with white; icons/text remap to `/700` |

**Chart series (categorical, not status):**
`1 #e76e50` · `2 #2a9d90` · `3 #274754` · `4 #e8c468` · `5 #f4a362` (coral · teal · dark-teal · gold · peach)

---

## 4. Radius — personality lever

Follows the **shadcn `--radius` calc pattern**: one base, ± offsets. Re-theme by changing `radius/base` only.

```
sm  = base − 4   →  4px
md  = base − 2   →  6px
lg  = base       →  8px   ← anchor (radius/base)
xl  = base + 4   → 12px
```

| Token         | Value     |                                  |
| ------------- | --------- | -------------------------------- |
| none          | `0px`     |                                  |
| sm            | `4px`     |                                  |
| md            | `6px`     |                                  |
| **base / lg** | **`8px`** | anchor                           |
| xl            | `12px`    |                                  |
| 2xl           | `14px`    | custom — hand-tuned, large cards |
| 3xl           | `18px`    | custom                           |
| 4xl           | `21px`    | custom                           |
| full          | `9999px`  | pills, avatars                   |

**Personality scale:** `0` = technical/sharp (Linear, Vercel) · `8px` = friendly (current) · `12–16px+` = soft/consumer (Stripe-ish).

---

## 5. Spacing — linear 4px (8-point grid)

Spacing is **linear (+4px), not geometric** — UI spacing needs grid alignment, so no ratio. (Geometric ratios belong to type, not spacing.) Re-theme density by changing the base unit.

```
0 · 4 · 8 · 12 · 16 · 20 · 24 · 28 · 32 · 36 · 40 · 44 · 48 · 56 · 64 · 80 · 96 · 112 · 128
```
Base unit = **4px** · half-steps: `px(1) · 0-5(2) · 1-5(6) · 2-5(10) · 3-5(14)`

shadcn/Tailwind-aligned. Tighten semantic mappings for denser/pro feel, loosen for airier/consumer.

---

## 6. Typography

| Aspect | Value |
|---|---|
| **Sans (UI)** | `Inter` |
| **Mono** | `Roboto Mono` |
| **Serif** | `Georgia` |
| **Size scale** | Tailwind default: 12 · 14 · 16 · 18 · 20 · 24 · 30 · 36 · 48 · 60 · 72 · 96 · 128 |
| **Scale ratio** | ~1.125–1.25 (custom Tailwind scale, not single-ratio) |
| **Weights** | thin 100 → black 900 (medium 500 / semibold 600 most used) |
| **Line height** | none 100 · tight 125 · snug 137.5 · normal 150 · relaxed 162.5 · loose 200 (% values) |
| **Letter spacing** | tighter −2.5 → widest 10 |

Type scale is where a **geometric ratio** lives (1.125 subtle · 1.25 balanced · 1.333 dramatic) — pick one ratio to re-derive sizes.

---

## How to re-theme (workflow)

1. **Brand** → pick a new hue, **regenerate the full `50–950` ramp** (uicolors.app / OKLCH / Radix), don't change `/500` alone. **Validate the new `/500` against the Ramp contrast standard above** (≥3:1 fill floor, 4.5:1 ideal vs white) before accepting it — reject anything under 3:1.
2. **Neutral** → swap zinc for slate/stone/gray (regenerate ramp). Highest mood-impact per effort.
3. **Radius** → change `radius/base`; sm/md/xl follow the calc offsets.
4. **Spacing** → change the 4px base unit or tighten/loosen semantic spacing mappings.
5. **Type** → swap font-family and/or pick a new size-scale ratio.

Because the semantic + component tiers **alias** primitives, edits at this primitive layer cascade automatically. After editing the primitive JSONs: re-run `node sd.build.mjs` and update the `.dark {}` block in `tokens.css` for any dark-mode overrides.

---

*Source of truth: `Agentic-design-system/Tokens/primitives.tokens.json`. Token rules & semantic mappings: `agentic-design-system.md`.*
