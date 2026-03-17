---
name: design-parser
description: "Use when you have design screenshots (PNGs), Figma MCP access, or design tokens and need to extract UI specifications including component trees, states, interactions, and design values"
---

## Overview

Extracts UI specifications from design screenshots (PNGs) or Figma MCP. Reads image files, optional design tokens, and produces structured per-screen specifications including component trees, states, interactions, and design values.

## When to Use

- You have a folder of design PNGs (one folder per feature or one folder for all)
- You have Figma MCP server connected
- You have design tokens file (JSON/CSS) alongside PNGs
- Called by `product-intake` skill or used standalone
- Do NOT use when: no visual designs exist

## Input

- **Folder path** containing PNGs (scans recursively)
- **Optional:** design tokens file in the same folder (JSON or CSS) — use exact values when present
- **Optional:** feature list from `prd-parser` — organizes screens under known features
- **Alternative:** Figma MCP server (if available, use instead of PNGs)

## Process

1. **Scan input folder** — inventory all image files. If Figma MCP is the source, query MCP for screens instead of scanning folder.
2. If feature list provided (from prd-parser), organize screens under known features.
3. If no feature list, infer feature groupings from folder structure or screen similarity.
4. Read each image — extract per screen:
   - **Screen name** (from filename or inferred from content)
   - **Component tree** with full hierarchy (use indented tree notation)
   - For each component: type, visual properties, position in hierarchy
   - **States:** empty, focused, filled, error, loading, disabled (identify all visible states)
   - **Interactions:** tap targets, navigation destinations, swipe, toggle
   - **Responsive behavior** (if multiple device sizes shown in PNGs)
5. Extract design values:
   - If design tokens file present → use exact values (hex colors, px/dp sizes, font families)
   - If PNGs only → extract approximate values, mark confidence as "approximate"
6. Present extraction to user for confirmation/correction of approximate values.
7. Output the structured UI specification.

## Output Format

For each screen extracted from the designs, produce the following structure:

````markdown
#### Screen: <ScreenName>
**Source:** <filename.png>

**Component Tree:**
```
Screen: <ScreenName>
├── <Component> (<properties>)
│   ├── States: <state list>
│   └── <Child> (<properties>)
└── <Component> (<properties>)
```

**Interactions:**
- <Component> tap → <action/navigation target>

**Responsive Behavior:**
- Compact: <layout description>
- Medium/Expanded: <layout description>

#### Design Values
| Token | Value | Confidence | Source |
|-------|-------|------------|--------|
| Primary color | #3B82F6 | exact | tokens.json |
| Corner radius | ~12dp | approximate | screen.png |
````

## Critical Rules

- **Describe what it looks like, not how to build it** — output is a visual specification, not implementation guidance
- **No codebase mapping** — that's an implementation concern
- **No architecture decisions** — outside this skill's scope
- **Mark approximate values clearly** — never present guesses as exact
- **Ask user to confirm/correct approximate values** before finalizing
