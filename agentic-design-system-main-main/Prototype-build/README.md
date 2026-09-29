# HTML Screen Design Guide

How to design full product screens and user flows using HTML + Tailwind in Storybook.

---

## Contents

| File | What it covers |
|---|---|
| `prototype-build-skill.md` | **Start here for Next.js prototypes.** Requirements, 4 phases, token rules, component rules, common mistakes with root causes. |
| `layout-patterns.md` | Copy-paste HTML/Tailwind markup for: app shell, page header, form page, data page, dashboard card grid, settings page, empty state, auth page. Token reference at the end. |
| `screen-stories-guide.md` | How to write page-level Storybook stories — file structure, title convention (`Screens/Feature/Name`), providers as decorators, mock data, responsive breakpoints. |
| `flow-guide.md` | How to model multi-step user flows — folder structure, step naming, flow index story, linking steps with comments, state transitions within a step. |

---

## Quick start

1. Copy a layout from `layout-patterns.md` as your starting point
2. Create a story file at `src/stories/screens/[Feature]/[Screen].stories.tsx`
3. Follow the template in `screen-stories-guide.md`
4. Add multiple states: Default, Empty, Loading, Error
5. For multi-step flows: create a folder and follow `flow-guide.md`

---

## Prerequisites

- Components: `src/components/ui/` — all 35 components available
- Token reference: `agentic-design-system.md`
- Component lookup: `Machine Readable/component-directory.md`
- Storybook running: `http://localhost:6006`
