# Annotation Guide

How to annotate Figma designs for developer handoff using the Agentic design system.

---

## What to annotate

Not everything needs annotation. Figma's Dev Mode surfaces tokens, component names, and spacing automatically from bound variables. Annotate only what Dev Mode cannot surface on its own.

**Annotate manually:**
- Interaction states not visible in the static frame (hover, focus, active)
- Conditional visibility rules ("show this only when X")
- Error states and validation logic
- Responsive behaviour — what changes at each breakpoint
- Non-obvious spacing that uses semantic tokens
- Animation or transition instructions

**Dev Mode surfaces automatically — do not re-annotate:**
- Token names on fills, strokes, radius (shown when clicking a layer)
- Component names and variant props
- Spacing between auto-layout children (gap)
- Padding values

---

## Component annotation

When a component has multiple states, show them all in a dedicated state exploration frame next to the screen.

**Format:**
```
[Component name] — States
  Default → Hover → Focus → Disabled → Invalid
```

Label each state variant with the exact Figma prop value: `State=Hover`, `State=Invalid` — not informal names like "hovered" or "error state."

**Where to put it:**
- Create a separate frame labelled `[Screen name] – States` to the right of the main artboard
- Keep it at the same scale, not zoomed in

---

## Spacing annotation

Use Figma's built-in spacing measurement (hold `Option/Alt` while hovering between layers) for pixel values during review. For handoff notes, reference the token name — not the pixel value.

**Do:**
```
Gap between items → spacing/component/sm (8px)
Padding → spacing/component/lg (16px)
```

**Don't:**
```
Gap = 8px
Padding = 16
```

Pixel values are meaningless without the token — they can change if the base unit changes. The token name is stable.

**When to add spacing annotations:**
- Spacing between sections or groups of components (layout-level, not component-internal)
- When the spacing deviates from the standard grid (call out exceptions explicitly)
- When two components have a specific relationship that drives the spacing

---

## Token annotation

Only annotate tokens that are not automatically surfaced by Dev Mode (i.e. not bound as a Figma variable).

Common cases where manual annotation is needed:
- Text styles applied as Figma text style (Dev Mode shows the style name, not the token — add the token name)
- Overlay opacity (`opacity/overlay → 50%`)
- Shadow effect styles — add the token name (`shadows/md`) since Dev Mode shows the CSS values

**Format:** Use a small label close to the element, connected by a line if needed.

---

## Responsive annotation

When a layout changes at a breakpoint, annotate the change directly on the desktop artboard with a note pointing to what collapses, reorders, or changes.

**Standard annotations:**

| Change type | Annotation format |
|---|---|
| Sidebar collapses to icon | `→ lg: sidebar collapses to icon mode` |
| Layout switches from 2-col to 1-col | `→ md: stacks to single column` |
| Component hidden on mobile | `→ mobile: hidden` |
| Button becomes full-width on mobile | `→ mobile: w-full` |

Place these notes on the Desktop frame. The Mobile frame should show the result, not the instructions.

---

## State and interaction annotation

Use a numbered interaction legend for complex interactions. Place the legend below or beside the artboard.

**Format:**
```
① Clicking [element] → opens [component] (State=Open)
② Hover on [row] → shows [action buttons] (State=Hover)
③ Submitting empty form → [field] switches to State=Invalid
④ Selecting [item] → [other component] updates
```

For error states: always specify the error message text, not just the visual state.

---

## Handoff checklist

Before marking a screen ready for development:

- [ ] All interactive elements have their states shown (Default, Hover, Focus, Disabled, Invalid)
- [ ] Error messages are written out, not just shown as red borders
- [ ] Responsive changes annotated on the largest breakpoint frame
- [ ] Spacing between sections annotated with token names
- [ ] Conditional visibility rules documented (what shows/hides and when)
- [ ] Component variant props are set correctly — no "detached" overrides
- [ ] No hardcoded colours or sizes — all fills bound to variables
- [ ] Overlay/modal screens positioned as floating frames above the base screen in the flow
