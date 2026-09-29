# Figma Screen Build Skill

Step-by-step process for building product screens in Figma using figma-cli, based on the Agentic design system.

Read `artboard-setup.md` and `screen-flow.md` before building any screen.

---

## Pre-flight checklist

Before writing any script:

- [ ] Identify the target page (existing or new)
- [ ] Read the component markdown for every component used on screen
- [ ] Note the exact component set node IDs from `Machine Readable/figma-ids.md` or `component-directory.md`
- [ ] Note text style IDs from `Machine Readable/figma-ids.md`
- [ ] Know the variant string format for every component instance needed

---

## API Rules — Non-negotiable

figma-cli runs in `dynamic-page` document access mode. Every Figma API that accesses document state **must** use the async version. Using the sync version will throw and silently abort the script.

| Sync (forbidden) | Async (required) |
|---|---|
| `figma.currentPage = page` | `await figma.setCurrentPageAsync(page)` |
| `figma.variables.getLocalVariableCollections()` | `await figma.variables.getLocalVariableCollectionsAsync()` |
| `figma.variables.getLocalVariables()` | `await figma.variables.getLocalVariablesAsync()` |
| `figma.getNodeById(id)` | `await figma.getNodeByIdAsync(id)` |
| `figma.getStyleById(id)` | `await figma.getStyleByIdAsync(id)` |
| `node.textStyleId = id` | `await node.setTextStyleIdAsync(id)` |
| `figma.loadAllPagesAsync()` then `figma.currentPage =` | `await figma.setCurrentPageAsync(page)` directly |

**Never** call sync APIs. Scripts that mix sync and async will fail mid-execution with no output.

---

## Two-phase workflow

Build in two separate scripts — structure first, tokens second. Do not mix them.

**Why:** `setTextStyleIdAsync` resets fills when called. If you bind a fill then apply a text style, the fill is wiped. Building structure first and binding tokens in a dedicated pass avoids this entirely and makes each script easier to debug.

```
Script 1 — Structure   Create frames, instances, text nodes. No fills, no strokes.
Script 2 — Token bind  Walk the completed tree and bind all fills/strokes in one pass.
```

---

## Script 1 — Structure Template

```js
// ── LOAD FONTS
await figma.loadFontAsync({ family: 'Inter', style: 'Regular' });
await figma.loadFontAsync({ family: 'Inter', style: 'Medium' });
await figma.loadFontAsync({ family: 'Inter', style: 'SemiBold' });
await figma.loadFontAsync({ family: 'Inter', style: 'Bold' });

// ── TEXT STYLES
const TS = {
  displayLg:  'S:360e42fcf8d0a3cf4eb2485b95c0e84141a88ffe,',
  displayMd:  'S:27651ba6bc5e5c0455fb8445baece861071c26fb,',
  displaySm:  'S:15db8fbbac39a327203b87beb4a99b2ed6b97d14,',
  headingXl:  'S:1b92b1006c71a40db6efab661a432382291ff8cc,',
  headingLg:  'S:08d92dbcc200186cb0bec1dea8b0828c356f3f1c,',
  headingMd:  'S:ac4ad1a17a9607229272f4aa0bb08a9568513824,',
  headingSm:  'S:2cc1b8fea261bb345051432310577947dc41b6c9,',
  headingXs:  'S:fec7afeccd76fbce5ca016900727499c6946c3b7,',
  bodyLg:     'S:6d27909f70624948fe8e92ff8512a143b329e73f,',
  bodyMd:     'S:b432dbe1c961781d97dd277c33360e41907f23d7,',
  bodySm:     'S:643f17a7b0b91d933a70908efa73faf0412e1788,',
  bodyXs:     'S:e558112dda3a77fa4aca1eb5951b82477a6e3ecb,',
  labelLg:    'S:31462b34f613ea9f3563d85ba2ef25601ffcb3a4,',
  labelMd:    'S:ce5449863ea16bd16e46421e9fc5c8ac29e8acd0,',
  labelSm:    'S:67792eed009542c23d2eebd066a0c77e2341dc5e,',
};
const hxl = await figma.getStyleByIdAsync(TS.headingXl);
const bmd = await figma.getStyleByIdAsync(TS.bodyMd);
const lmd = await figma.getStyleByIdAsync(TS.labelMd);

// Helper: create text — NO fill binding here
async function mkText(chars, style, { align = 'LEFT', stretch = false } = {}) {
  const t = figma.createText();
  t.characters = chars;
  if (style) await t.setTextStyleIdAsync(style.id);
  t.textAlignHorizontal = align;
  t.textAutoResize = stretch ? 'HEIGHT' : 'WIDTH_AND_HEIGHT';
  if (stretch) t.layoutAlign = 'STRETCH';
  return t;
}

// ── NAVIGATE TO PAGE
const targetPage = figma.root.children.find(p => p.name === '/* PAGE NAME */');
await figma.setCurrentPageAsync(targetPage);

// ── COMPONENT NODES
const btnSet = await figma.getNodeByIdAsync('12:2893');   // button
const ifSet  = await figma.getNodeByIdAsync('49:10848');  // input-field

// ── MAIN FRAME — no fill yet
const frame = figma.createFrame();
frame.name = '/* e.g. Mobile / Auth – Login */';
frame.resize(390, 100);
frame.layoutMode = 'VERTICAL';
frame.primaryAxisSizingMode = 'AUTO';
frame.counterAxisSizingMode = 'FIXED';
frame.paddingTop = /* value */; frame.paddingBottom = /* value */;
frame.paddingLeft = 16; frame.paddingRight = 16;
frame.itemSpacing = 0;
frame.x = /* x */; frame.y = /* y */;
figma.currentPage.appendChild(frame);

// ── BUILD SECTIONS (see patterns below)
// No bindFill / bindStroke calls here — structure only

return { id: frame.id, name: frame.name, height: frame.height };
```

---

## Script 2 — Token Bind Template

Run this after Script 1 returns the frame ID. Replace `FRAME_ID` with the returned value.

```js
const allVars = await figma.variables.getLocalVariablesAsync();
const allCols = await figma.variables.getLocalVariableCollectionsAsync();

function sem(name) {
  const col = allCols.find(c => c.name === 'Semantics');
  if (!col) return null;
  return allVars.find(v => v.variableCollectionId === col.id && v.name === name) || null;
}
function prim(name) {
  const col = allCols.find(c => c.name === 'Primitives');
  if (!col) return null;
  return allVars.find(v => v.variableCollectionId === col.id && v.name === name) || null;
}
function bindFill(node, v) {
  if (!v) { console.warn('Variable not found for fill on', node.name); return; }
  node.fills = [figma.variables.setBoundVariableForPaint({ type: 'SOLID', color: { r: 0, g: 0, b: 0 } }, 'color', v)];
}
function bindStroke(node, v) {
  if (!v) { console.warn('Variable not found for stroke on', node.name); return; }
  node.strokes = [figma.variables.setBoundVariableForPaint({ type: 'SOLID', color: { r: 0, g: 0, b: 0 } }, 'color', v)];
}

const frame = await figma.getNodeByIdAsync('/* FRAME_ID */');

// Bind frame background
bindFill(frame, sem('color/background/default'));

// Walk sections and bind by node name
const bindings = [
  // { path: ['section-name', 'child-name'], fill: sem('token') }
  // { path: ['header', 'title'],    fill: sem('color/background/default/foreground') },
  // { path: ['header', 'subtitle'], fill: sem('color/text/secondary') },
];

for (const b of bindings) {
  let node = frame;
  for (const step of b.path) node = node.findOne(n => n.name === step) || node;
  if (b.fill)   bindFill(node, b.fill);
  if (b.stroke) bindStroke(node, b.stroke);
}

// Verify — check all fills are bound
const unbound = frame.findAll(n => n.fills && n.fills.length > 0 && !n.fills[0]?.boundVariables?.color && n.type !== 'INSTANCE');
return { bound: bindings.length, unbound: unbound.map(n => n.name) };
```

---

## Common Patterns

### Horizontal nav bar (space-between)

```js
const nav = figma.createFrame();
nav.name = 'nav-bar';
nav.layoutMode = 'HORIZONTAL';
nav.primaryAxisSizingMode = 'FIXED';
nav.counterAxisSizingMode = 'AUTO';
nav.layoutAlign = 'STRETCH';
nav.primaryAxisAlignItems = 'SPACE_BETWEEN';
nav.counterAxisAlignItems = 'CENTER';
nav.fills = [];
nav.paddingBottom = 16;

const leftInst = linkVariant.createInstance();
const leftLbl = leftInst.findOne(n => n.type === 'TEXT' && n.name === 'label');
if (leftLbl) leftLbl.characters = 'Back';

const rightInst = linkVariant.createInstance();
const rightLbl = rightInst.findOne(n => n.type === 'TEXT' && n.name === 'label');
if (rightLbl) rightLbl.characters = 'Help';

nav.appendChild(leftInst);
nav.appendChild(rightInst);
frame.appendChild(nav);
```

### Vertical content block (e.g. header)

No fill calls here — token binding happens in Script 2.

```js
const header = figma.createFrame();
header.name = 'header';
header.layoutMode = 'VERTICAL';
header.primaryAxisSizingMode = 'AUTO';
header.counterAxisSizingMode = 'FIXED';
header.layoutAlign = 'STRETCH';
header.fills = [];
header.itemSpacing = 12;
header.paddingBottom = 32;

const title = await mkText('Your heading here', hxl, { stretch: true });
title.name = 'title';
// ← no bindFill here

const subtitle = await mkText('Supporting copy here.', bmd, { stretch: true });
subtitle.name = 'subtitle';
// ← no bindFill here

header.appendChild(title);
header.appendChild(subtitle);
frame.appendChild(header);
```

### input-field instance

```js
// Variant names: 'State=Default' | 'State=Filled' | 'State=Invalid' | 'State=Disabled'
const variant = ifSet.children.find(c => c.name === 'State=Filled');
const inst = variant.createInstance();
inst.name = 'field-email';
inst.layoutAlign = 'STRETCH';

// Label text — node name is 'label-text' (not 'label')
const labelText = inst.findOne(n => n.type === 'TEXT' && n.name === 'label-text');
if (labelText) labelText.characters = 'Email Address';

// Fix label container clipping — always set after changing label text
const labelContainer = inst.findOne(n => n.name === 'label' && n.type === 'INSTANCE');
if (labelContainer) {
  labelContainer.primaryAxisSizingMode = 'AUTO';
  labelContainer.counterAxisSizingMode = 'AUTO';
  labelContainer.layoutAlign = 'STRETCH';
}

// Input value (filled state) or placeholder (default state)
const placeholder = inst.findOne(n => n.type === 'TEXT' && n.name === 'placeholder');
if (placeholder) placeholder.characters = 'user@example.com';

// Hide description if not needed
const desc = inst.findOne(n => n.type === 'TEXT' && n.name === 'description');
if (desc) desc.visible = false;
```

> **Label truncation bug:** The `label` instance inside `input-field` has `primaryAxisSizingMode: FIXED` (37px) by default. Always set it to `AUTO` after changing label text — otherwise the label clips silently.

### Button instance (full-width)

```js
// Variant string format: 'Type=Default, State=Enabled, Size=Large'
const variant = btnSet.children.find(c => c.name === 'Type=Default, State=Enabled, Size=Large');
const inst = variant.createInstance();
inst.name = 'btn-primary';

const lbl = inst.findOne(n => n.type === 'TEXT' && n.name === 'label');
if (lbl) lbl.characters = 'Sign In';

// Full-width: resize explicitly to container width minus padding, preserve component height
// Do NOT set primaryAxisSizingMode = 'FIXED' — inside a vertical auto-layout frame,
// that collapses the button height to ~14px.
// Container width (390) − paddingLeft (16) − paddingRight (16) = 358
inst.resize(358, 44); // 44px = Large size spec height
```

### Centered footer block

```js
const footer = figma.createFrame();
footer.name = 'footer';
footer.layoutMode = 'VERTICAL';
footer.primaryAxisSizingMode = 'AUTO';
footer.counterAxisSizingMode = 'FIXED';
footer.layoutAlign = 'STRETCH';
footer.fills = [];
footer.itemSpacing = 16;
footer.counterAxisAlignItems = 'CENTER';  // center-align all children
footer.paddingTop = 8;
```

### Spacer frame

```js
const spacer = figma.createFrame();
spacer.name = 'spacer';
spacer.resize(358, 32);   // full width of content area, desired height
spacer.fills = [];
spacer.layoutAlign = 'STRETCH';
frame.appendChild(spacer);
```

---

## Component Variant String Reference

Variant names must match exactly — use `node.children.map(c => c.name)` to inspect if unsure.

### Button (`12:2893`)

```
'Type=Default, State=Enabled, Size=Default'
'Type=Default, State=Enabled, Size=Large'
'Type=Default, State=Enabled, Size=Small'
'Type=Outline, State=Enabled, Size=Default'
'Type=Ghost, State=Enabled, Size=Default'
'Type=Link, State=Enabled, Size=Default'
'Type=Destructive, State=Enabled, Size=Default'
```

### Input-field (`49:10848`)

```
'State=Default'
'State=Filled'
'State=Invalid'
'State=Disabled'
```

### Input raw (`49:10806`)

```
'Type=Input, State=Default'
'Type=Input, State=Filled'
'Type=Input, State=Focused'
'Type=Input, State=Invalid'
'Type=Input, State=Disabled'
```

---

## Text Node Names Inside Components

These are the actual layer names inside component instances — use these in `findOne()`:

| Component | Layer | Node name |
|---|---|---|
| `input-field` | Field label text | `label-text` |
| `input-field` | Required asterisk | `label-required` |
| `input-field` | Input text / placeholder | `placeholder` |
| `input-field` | Helper / error text | `description` |
| `input-field` | Label wrapper instance | `label` |
| `button` | Button label | `label` |
| `button` | Leading icon | `leading-icon` |
| `button` | Trailing icon | `trailing-icon` |

---

## Cleanup — remove leftover frames

Scripts that fail mid-run leave partial frames on the canvas with the same name. Always run a cleanup pass after the final build to remove all duplicates, keeping only the frame with the target ID.

Run this as a **separate eval** after the build script returns successfully:

```js
const FRAME_NAME = '/* e.g. Mobile / Auth – Login */';
const KEEP_ID    = '/* frame.id returned by build script */';

const dupes = figma.currentPage.children.filter(
  n => n.name === FRAME_NAME && n.id !== KEEP_ID
);
dupes.forEach(n => n.remove());

return { removed: dupes.length, kept: KEEP_ID };
```

Run this every time, even if you think no duplicates exist — it's cheap and keeps the canvas clean.

---

## Verification

After cleanup, export a screenshot to verify visually before reporting done.

```js
// At the end of the build script, after frame is complete:
const png = await frame.exportAsync({ format: 'PNG', constraint: { type: 'SCALE', value: 1.5 } });
return { base64: figma.base64Encode(png) };
```

Then in terminal:
```bash
node src/index.js eval "..." 2>/dev/null | python3 -c "
import json,sys,base64
data = json.load(sys.stdin)
b = base64.b64decode(data['base64'])
open('/tmp/screen-preview.png','wb').write(b)
print('saved')
"
```

Read `/tmp/screen-preview.png` to visually check the result. Fix before reporting done.

**What to check:**
- Labels are not truncated
- Description rows are hidden where not needed
- Spacing between sections feels right vs the reference
- Full-width elements (inputs, primary button) span the full content width
- Text colors match semantic intent (default for headings, secondary for body copy)

---

## Build order

**Script 1 — Structure**
1. Load fonts + text styles
2. Navigate to target page
3. Load all component nodes (`getNodeByIdAsync`) at the top — not inline
4. Create main frame, append to page — no fill yet
5. Build sections top-to-bottom — no `bindFill` / `bindStroke` calls
6. Fix instances (label text, description visibility, label container sizing)
7. Return `{ id, name, height }`

**Script 2 — Token bind** (separate eval, using the returned frame ID)
8. Load variables (`getLocalVariablesAsync` + `getLocalVariableCollectionsAsync`)
9. Bind frame background fill
10. Walk every named text/frame node and bind fill/stroke to the correct semantic token
11. Run unbound check — `frame.findAll(...)` to confirm no raw fills remain
12. Return `{ bound, unbound }` — unbound list must be empty

**Script 3 — Cleanup + verify**
13. Remove leftover frames with the same name (keep by ID)
14. Export screenshot → visually check → fix if needed

---

## Known Gotchas

| Symptom | Cause | Fix |
|---|---|---|
| Script aborts with no output | Sync API called | Replace with async version — see API Rules table |
| Fill wiped after text style is applied | `setTextStyleIdAsync` resets fills — binding fill before text style loses it | Use two-phase workflow: structure (Script 1) then token bind (Script 2) |
| `color/text/default` variable not found | Token doesn't exist in this system | Use `color/background/default/foreground` for primary text on default bg |
| `bindFill` runs but `hasBoundVar` is false | Variable name wrong — `sem()` returns null silently | Add `console.warn` in `bindFill` when `v` is null; verify token name exists |
| Label shows "Label" instead of custom text | Used `n.name === 'label'` to find text | Use `n.name === 'label-text'` |
| Label text truncated to a few characters | `label` container is `primaryAxisSizingMode: FIXED` (37px) | Set to `AUTO` + `layoutAlign: STRETCH` after changing label text |
| Description gap below inputs | `description` node is empty but still takes space | Set `desc.visible = false` |
| Button height collapses to ~14px | Set `primaryAxisSizingMode = 'FIXED'` on instance inside vertical auto-layout — Figma treats primary axis as height and collapses it | Never set `primaryAxisSizingMode` on button instances. Use `resize(width, height)` directly |
| Button not full width | `layoutAlign = 'STRETCH'` doesn't work when button has `counterAxisSizingMode = 'FIXED'` | Use `resize(containerWidth − paddingL − paddingR, componentHeight)` explicitly |
| `setCurrentPageAsync` error | Used `figma.currentPage =` assignment | Use `await figma.setCurrentPageAsync(page)` |
| Text style not applied | Used `node.textStyleId = id` | Use `await node.setTextStyleIdAsync(style.id)` |
| Multiple leftover frames on canvas | Script failed mid-run, leaving partial frames | Run cleanup eval after build — filter by name, remove all except `KEEP_ID` |
