---
name: product-intake
description: "Use when you have product artifacts (PRD documents, design PNGs, Figma designs, or any combination) and need to produce a structured product spec before brainstorming and planning. Also use when starting a new feature and you have design files or requirements documents."
---

## Overview

Router skill that detects available product artifacts (PRDs, design PNGs, Figma designs), dispatches the appropriate extraction skills (`prd-parser` and/or `design-parser`), and merges their output into a single structured product spec with gap analysis.

## When to Use

- You have a PRD, design PNGs, or both and want to start building a feature
- You want to convert product artifacts into a structured spec before brainstorming
- Do NOT use when: you have no artifacts at all (use brainstorming directly)

## Input Detection

Determine what the user has from their message:

- **Check user's message** for file paths, folder paths, pasted text
- If path points to a **folder** — likely design PNGs — use `design-parser`
- If path points to a **.md or .pdf file** — likely PRD — use `prd-parser`
- If **text is pasted** (not a file path) — treat as inline PRD — use `prd-parser`
- If **Figma MCP server is available** — can use `design-parser` with MCP
- If **nothing provided** — tell user this skill requires at least one artifact, suggest brainstorming instead

## Process

1. **Detect available inputs** from user's message (see Input Detection above). If no inputs are detected, inform the user and suggest using brainstorming directly. Do not proceed to step 2.

2. **Run parsers in order:**
   - **Step A:** If PRD available, invoke the `prd-parser` skill. This produces the feature list and requirements.
   - **Step B:** If PNGs/Figma available, invoke the `design-parser` skill. Pass the feature list from Step A if available, so screens are organized under known features. If no PRD was provided, `design-parser` infers features from folder structure.

3. **Merge outputs** into a single product spec grouped by feature. Preserve the sub-section structure from each parser's output (e.g., Functional Requirements, Non-Functional Requirements sub-headings from `prd-parser`):
   - Each feature section contains: requirements (from `prd-parser`) + UI spec (from `design-parser`)
   - If only PRD: features have requirements but no UI spec section
   - If only PNGs: features have UI spec but requirements are inferred minimally from screen content

4. **Run gap analysis** — cross-reference requirements and UI:
   - **MISSING IN PRD:** design shows functionality not mentioned in PRD
   - **MISSING IN DESIGN:** PRD requires something with no corresponding screen
   - **VAGUE REQUIREMENT:** PRD mentions something without sufficient detail
   - **AMBIGUOUS UI:** design element is unclear, multiple interpretations possible

5. **Present gaps to user** with three options per gap:
   - **Resolve now** — user provides the answer, spec is updated
   - **Forward to brainstorming** — gap is marked "→ Brainstorming" for later exploration
   - **Skip for now** — gap is marked "⚠️ TBD" for later resolution

6. **User confirms/corrects** any approximate design values.

7. **Save product spec** to `docs/superpowers/specs/YYYY-MM-DD-<topic>-product-spec.md`
   - Topic is derived from: PRD title, design folder name, or ask user if neither is clear

8. **Tell user** the spec is ready and where it's saved.

9. **Instruct user:** "To continue, invoke brainstorming (`/superpowers:brainstorming`) and reference this product spec."

## Output Format

Save the product spec using this template:

````markdown
# Product Spec: <Topic>

## Source Artifacts
- PNGs: <path> (<count> screens)
- PRD: <path>
- Design Tokens: <path> (if provided)

---

## Feature: <Feature Name>

### Requirements
[from prd-parser output]

### Acceptance Criteria
[from prd-parser output]

### Edge Cases
[from prd-parser output]

### UI Specification
[from design-parser output — screens, component trees, design values]

### Gaps
| ID | Type | Description | Status |
|----|------|-------------|--------|

---

## Cross-Feature

### Navigation Flow
[screen-to-screen navigation extracted from designs]

### Shared Patterns
[components/patterns appearing across multiple features]

### Global Gaps
| ID | Type | Description | Status |
|----|------|-------------|--------|
````

## Connection to Brainstorming (Phase 1 — Manual Handoff)

- Product-intake does NOT invoke brainstorming automatically
- It saves the spec and instructs the user to invoke brainstorming manually
- User should reference the spec file when starting brainstorming
- Future Phase 2 (requires forking superpowers): automatic handoff

## Critical Rules

- **PRD is parsed first** (defines features), then PNGs (organized under features)
- **When only PNGs exist**, infer features from folder structure or screen groupings
- **Never invent requirements** — flag gaps instead
- **Never make architecture or implementation decisions**
- **Always present gaps to user** before saving the spec
- **Always ask user to confirm** approximate design values
