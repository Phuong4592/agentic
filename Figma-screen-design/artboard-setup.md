# Artboard Setup

How to create and configure Figma frames for screen design using the Agentic design system.

---

## Frame sizes

Use these frame widths. They map directly to the Tailwind breakpoints and are the reference sizes for all component designs.

| Name | Width | Tailwind breakpoint | When to use |
|---|---|---|---|
| Mobile | 390px | base (no prefix) | Primary mobile design target |
| Tablet | 768px | `md:` | Tablet layouts |
| Laptop | 1024px | `lg:` | Laptop — primary layout switch point (sidebar/nav changes here) |
| Desktop | 1280px | `xl:` | Standard desktop |
| Large desktop | 1440px | `xl:` (max-width constrained) | Common design target — maps to xl: in code |

**Height:** set to Hug Contents — let content define the frame height. Never hardcode a frame height unless designing a fixed-height viewport (e.g. a modal).

**How to create:**
1. Press `F` to activate the Frame tool
2. Type exact width in the W field (e.g. `390`)
3. In the Design panel → set Height to **Hug Contents**

---

## Grid styles

Grid styles are saved in the Figma file. Apply them to artboard frames — not to component frames.

| Style name | Columns | Gutter | Margin | Breakpoint |
|---|---|---|---|---|
| `Grid/Mobile` | 4 | 16px | 16px | 390px frame |
| `Grid/Tablet` | 8 | 24px | 32px | 768px frame |
| `Grid/Desktop` | 12 | 24px | 80px | 1280px+ frame |

**How to apply:**
1. Select the artboard frame
2. In the Design panel → Layout Grid → click `+`
3. Click the style icon → select `Grid/Mobile`, `Grid/Tablet`, or `Grid/Desktop`

**Rules:**
- Always apply the matching grid style to the matching frame size
- Use the grid to align components — gutters define where content can live
- The 80px desktop margin is a visual guide — it maps to `px-[80px]` or `container mx-auto` in code
- Never apply grid styles to component frames — only to page-level artboard frames

---

## Page layout structure

Every screen artboard should follow this structure inside the frame:

```
[Frame — e.g. "Desktop / Dashboard"]
  ├─ nav                 — Sidebar or NavigationMenu component
  ├─ main                — Main content area (fills remaining width)
  │    ├─ header         — Page title, breadcrumb, actions
  │    └─ content        — Cards, tables, forms, etc.
  └─ [overlay?]          — Dialog, Sheet, Drawer (positioned absolute)
```

Use Auto Layout on the artboard frame to stack sections vertically. Set to **Fill Container** width on all direct children.

---

## Page organisation in Figma

Structure your Figma pages by screen area or user flow — not by component.

**Recommended page naming:**

```
Cover              ← Project cover / thumbnail
📐 Components      ← Do not design screens here — library reference only
🏠 Dashboard       ← Dashboard screens (all breakpoints)
⚙️ Settings        ← Settings screens
🔐 Auth            ← Login, signup, reset password
📋 [Feature name]  ← One page per major feature
📱 Mobile          ← Mobile-specific screens if separated
```

**Rules:**
- One page per feature area — keep related screens together
- Name frames consistently: `[Breakpoint] / [Screen name]` e.g. `Desktop / Dashboard – Overview`
- Group frames for the same screen at different breakpoints side by side, left to right: Mobile → Tablet → Desktop

---

## Container and max-width

For pages wider than 1400px, content stays centred inside the container. The 80px margin on the Desktop grid represents this visually.

In Figma: on Desktop frames, the actual content area is `1280 - 80 - 80 = 1120px` wide. Align components to the grid column area, not the full frame edge.

In code: `container mx-auto px-4 md:px-8 max-w-[1400px]`

---

## Using components on artboards

1. Open the Assets panel (`Shift+I`) or press `/` to search
2. Search by component name (reference `component-directory.md` for exact names)
3. Drag onto the artboard
4. Set the correct variant props in the Design panel (right sidebar)
5. Set width to **Fill Container** for full-width components (inputs, cards, buttons in stacked layout)

**Width rules:**
- Full-width elements (inputs, cards, buttons in forms): `Fill Container`
- Fixed-width elements (badges, avatars, icons): `Fixed`, keep at spec size
- Never manually type pixel widths on component instances — use Fill Container or Auto sizing

---

## Checklist before designing

- [ ] Frame created at the correct width (390 / 768 / 1024 / 1280 / 1440)
- [ ] Grid style applied (`Grid/Mobile`, `Grid/Tablet`, or `Grid/Desktop`)
- [ ] Frame height set to Hug Contents
- [ ] Page named correctly with breakpoint prefix
- [ ] Auto Layout enabled on the artboard frame
