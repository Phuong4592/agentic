# Agentic Design System

A Figma-first, shadcn/ui + Tailwind CSS design system with a three-tier token architecture (**Primitives → Semantics → Components**), 36 documented components and AI-assisted build / audit / Storybook workflows. The personality is **cool-neutral, modern, friendly-professional**: zinc greys, one blue brand hue, 8px radius, flat bordered surfaces, Inter.

This project is a browser-ready recreation of that system: tokens as CSS custom properties, every source component rebuilt as a dependency-free React primitive, foundation specimen cards and a click-through UI kit.

## Sources
- GitHub: **https://github.com/Phuong4592/agentic** (branch `main`) — explore it for deeper context:
  - `agentic-ui/src/app/tokens.css` — 339 resolved tokens + `.dark` block (copied verbatim into `tokens/`)
  - `agentic-ui/src/components/ui/*.tsx` — component implementations (the inventory used here)
  - `agentic-ui/src/stories/screens/northwind/` — Northwind screen template (UI kit)
  - `agentic-design-system.md`, `agentic-theme.md`, `content-guidelines.md`, `Component Markdown (reference)/*.md` — rules and per-component specs
  - `Tokens/*.tokens.json` — DTCG source exported from Figma
- Figma file key referenced in the repo: `YWfTOUTpFZ0BNxHobfUqme` (not accessed here).
- Not recreated: `agentic-ui/src/app/prototype/{simon,grabfood,elevenlabs}` — third-party-brand prototypes, out of scope for this system's own look.

## Index
- `styles.css` — entry point (imports only). Link this one file.
- `tokens/` — `colors.css` (ramps + semantics + dark), `scales.css` (spacing, radius, shadow, motion, opacity, z, layout), `component-tokens.css` (button/badge/table/tooltip), `type-primitives.css` + `typography.css` (px scale + semantic text styles), `fonts.css`, `base.css` (resets, scrollbars, keyframes)
- `components/<group>/` — JSX + `.d.ts` + `.prompt.md` + one `*.card.html` and one `<group>.css` per group
- `guidelines/` — foundation specimen cards (Colors, Type, Spacing, Brand)
- `ui_kits/northwind/` — Northwind sales app (Dashboard, Orders)
- `SKILL.md` — Agent-Skill entry; `github.md` — source sync record; `thumbnail.html`

## Components
Namespace in cards / consumers: `window.AgenticDesignSystem_b6d96e`. Styling is class-based (`ag-*`) in the group CSS files, driven entirely by tokens.

- **actions/** — Button, ButtonGroup
- **forms/** — Label, Field, Input, Textarea, Select, Combobox, InputOTP
- **controls/** — Checkbox, RadioGroup, Switch, Slider
- **dates/** — Calendar, DatePicker
- **data/** — Badge, Avatar, Card (CardHeader, CardTitle, CardDescription, CardContent, CardFooter), Item, Table (TableHeader, TableBody, TableFooter, TableRow, TableHead, TableCell, TableCaption)
- **content/** — Separator, Skeleton, Empty, Accordion, Progress
- **feedback/** — Alert, Toast, Toaster, Tooltip
- **overlays/** — Dialog, AlertDialog, Sheet, Drawer
- **navigation/** — Breadcrumb, Pagination, Tabs, NavigationMenu, Sidebar (SidebarHeader, SidebarContent, SidebarGroup, SidebarMenuItem, SidebarSubItem, SidebarFooter, SidebarToggle)
- **chat/** — ChatBubble, ChatLog, TypingIndicator, SuggestionChip, ChatInput
- **icons/** — Icon

### Intentional additions
- **Icon** — the source imports `lucide-react`; this wrapper renders Lucide icons by name from the Lucide CDN so components stay npm-free.
- **Field** — packages the label → control → helper/error stack repeated throughout the source showcase (`page.tsx`).
- **Toaster** — simple stack standing in for Sonner's `<Toaster/>`.
- Compound Radix APIs (e.g. `SelectTrigger/SelectContent/SelectItem`, `TabsList/TabsTrigger`) are flattened into data-driven props (`options`, `tabs`, `items`) — same visuals, simpler API. Overlays take `open` + a `contained` flag for mocks.

## CONTENT FUNDAMENTALS
The system is deliberately **voice-neutral** (`content-guidelines.md`): mechanics are prescribed, personality is left to the consuming product.
- **Sentence case everywhere** — buttons, headings, labels, menu items, table headers. "Create project", never "Create New Project". Proper nouns/acronyms keep casing (Figma, API, ID).
- **Buttons start with a verb** and name the outcome, 1–3 words: "Save changes", "Add order", "Delete 3 items" (not "Submit", "OK", "New").
- **Errors: what happened + how to fix**, never blame: "This field can't be empty", "That password didn't work. Try again.", "File must be under 10MB".
- **Empty states say what's missing and the next step**: "No projects yet — create your first one to get started."
- **"Select/choose", not "click".** Numerals in UI ("3 items"). Oxford comma. No em dashes in product copy (use commas).
- **Dates/times consistent** within a product: "Jun 17, 2026" / "Jun 17" in Northwind; relative times in feeds ("5m ago", "1h ago").
- Metric copy is terse and data-first: "$128,420", "+12.5% vs last month", "Showing 1–10 of 128", "128 orders".
- Address the user as **you** (implied); the system never speaks as "I"/"we". **No emoji** anywhere in UI or docs examples.
- Sample data uses notable computer scientists (Ada Lovelace, Grace Hopper, Katherine Johnson…) and order ids like "#1042".

## VISUAL FOUNDATIONS
- **Color**: one brand hue — **blue/500 #2b7fff** for fills (primary button, active page, checked states, focus ring, user chat bubble). Because /500 is only 3.8:1 on white, text/icon/hover uses **/600 #155dfc** and pressed **/700 #1447e6**. Everything else is **zinc**: page #fff, raised/sidebar zinc/50, muted/accent zinc/100, borders zinc/200 (inputs zinc/300), secondary text zinc/600, foreground zinc/900. Status = red/green/yellow with *subtle* tints (50) + dark foregrounds (700) for badges/alerts; yellow always takes dark text. Chart palette is separate (coral, teal, dark-teal, gold, peach).
- **Type**: Inter for all UI, Roboto Mono for code/data, Georgia exists but is unused. Tailwind size scale; UI lives at **14px** (body, inputs, buttons) and **12px** (badges, tabs, captions, table headers). Weights: 400 body, 500 labels/buttons, 600 titles. Labels use leading-none; titles leading-snug.
- **Spacing**: linear 4px grid with 2/6/10/14 half-steps. Semantic component spacing: 2 (xxs) · 4 · 6 · 8 · 12 · 16 (card padding/gap) · 24 · 32. Controls are 36px (inputs/selects), buttons 36/40/44, table header 48.
- **Radius**: base 8px (`lg`) for cards, alerts, dialogs, menus, tab lists; 6px (`md`) for inputs, selects, checkboxes, badges, menu items; 12px for large buttons; 14px chat bubbles with one 4px "tail" corner; full for pills, avatars, switches.
- **Cards & surfaces**: **flat** — white, 1px zinc/200 border, 8px radius, 16px padding, **no shadow**. Dialogs, sheets, drawers and toasts-in-spec also have no shadow; elevation comes from the 50% zinc/900 scrim. Shadows appear only on floating menus (shadow-sm), date/combobox popovers (shadow-md) and the active segmented tab (shadow-sm).
- **Borders** carry structure: hairline dividers between rows, bottom-border accordions, 1px-bordered pagination squares, bordered button groups. Selected table rows get a 2px brand inset left edge.
- **Backgrounds**: plain solid surfaces only. No gradients, textures, illustrations, patterns or photography in the source. Imagery slots (avatars, Item thumbnails/covers) are neutral muted placeholders.
- **Focus**: 2px blue ring (`--color-ring`); inputs use blue border + 3px 20%-blue glow; invalid swaps both to red. Destructive button focus = 3px red @40%.
- **Hover**: fills step one shade (blue 500→600, secondary blue/100→200, outline/ghost → zinc/100 accent); links underline (4px offset); breadcrumb links darken from secondary to foreground. **Press**: one more step darker (blue/700, outline border → zinc/400). No scale/shrink.
- **Disabled**: 60% opacity (`--opacity-disabled`) for buttons/switch/slider/radio; inputs/selects/checkboxes instead swap to muted fill + zinc/100 border + zinc/400 text.
- **Motion**: quick and functional — 150ms color transitions, 200ms fade+zoom-95 for dialogs/menus, 300–500ms slides for sheets/drawers, chevrons rotate 180°, skeleton pulse, typing-dot bounce. Easing `standard (.2,0,0,1)` / `enter (0,0,.2,1)`. Respects reduced motion. No bounces or springs on UI.
- **Transparency/blur**: only the 50% scrim and the sidebar's 60–70% opacity labels. No backdrop blur.
- **Layout**: fixed app shell — 240px sidebar (56px collapsed, Cmd/Ctrl+B) + 56px top bar with border-bottom, scrollable main with 16px padding. Grids: 4 KPI cards, 2/3 + 1/3 split. Breakpoint frames 390/768/1024/1280/1440; 12-col desktop grid, 24px gutter, 80px margin, 1400 max.
- **Dark mode**: `.dark` class; zinc elevation 950 page → 900 card → 800 menu → 700 input; brand stays blue.

## ICONOGRAPHY
- **Lucide** (`lucide-react`) is the system icon set: outline, 24-unit grid, **2px stroke**, round caps; rendered at **16px** (`size-4`) in controls, **12px** in badges/chips, **14px** in trend/activity glyphs. A few components in the source import from `@untitledui/icons` (e.g. `CornerDownLeft`, `XClose`, `Loading02`) — here they map to Lucide equivalents (`corner-down-left`, `x`, `loader-circle`). **Flag:** Untitled UI glyphs are substituted with Lucide.
- Use the `Icon` component: `<Icon name="shopping-cart" />`. It loads `lucide@0.469.0` UMD from unpkg once. Colors inherit `currentColor`; set with `--color-icon-default|muted|disabled|brand|danger|success|warning`.
- Common glyphs: layout-dashboard, shopping-cart, users, package, file-text, chart-bar, settings, search, bell, plus, ellipsis, chevron-down/right/left, check, minus, x, circle-check, circle-alert, triangle-alert, info, trending-up/down, dollar-sign, clock, calendar, copy, eye, mail.
- No icon font, no PNG icons, **no emoji**, no unicode-as-icon (except "·" and "/" breadcrumb separators and "–" in date ranges).
- **No logo** exists in the source. Render the name in Inter ("Agentic Design System"); product demos use a 28px dark rounded monogram tile (Northwind "N"). Don't invent a mark.

## Fonts
Inter and Roboto Mono load from **Google Fonts** — the same families the source loads via `next/font/google`; no font binaries are in the repo. Drop .woff2 files into `assets/fonts/` and swap `tokens/fonts.css` to `@font-face` if you need offline/self-hosted fonts.
