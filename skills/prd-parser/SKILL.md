---
name: prd-parser
description: "Use when you have a PRD document (markdown, PDF, or pasted text) and need to extract structured requirements, acceptance criteria, and edge cases grouped by feature"
---

## Overview

Extracts structured requirements from PRD (Product Requirements Document) documents. Reads markdown files, PDFs, or pasted PRD text and produces requirements grouped by feature with consistent ID prefixes.

## When to Use

- You have a PRD file (markdown or PDF) or pasted PRD text
- You need structured requirements before planning
- Called by `product-intake` skill or used standalone
- Do NOT use when: no PRD exists (use brainstorming instead)

## Input

- **File path** to markdown (`.md`) or PDF (`.pdf`)
- **Pasted text** — if the input is not a valid file path, treat it as inline PRD content

## Process

1. Read the PRD document (file or pasted text)
2. Identify distinct features/modules described in the PRD
3. For each feature, extract:
   - **Functional requirements (FR-N):** what the system must do
   - **Non-functional requirements (NFR-N):** performance, security, scalability constraints
   - **Acceptance criteria (AC-N):** testable conditions for "done" (checkbox format)
   - **Edge cases (EC-N):** boundary conditions, error scenarios, unusual inputs
4. Flag vague or ambiguous requirements as gaps — never invent missing requirements
5. Present extracted requirements to user for review
6. Output the structured requirements

## Output Format

For each feature extracted from the PRD, produce the following structure:

```markdown
## Feature: <Feature Name>

### Requirements

#### Functional Requirements
- FR-1: <requirement>

#### Non-Functional Requirements
- NFR-1: <requirement>

### Acceptance Criteria
- [ ] AC-1: <testable criteria>

### Edge Cases
- EC-1: <edge case description>

### Gaps
| ID | Type | Description | Status |
|----|------|-------------|--------|
| GAP-1 | Vague requirement | <what's unclear> | ⚠️ TBD |
```

## Critical Rules

- **Extract, don't invent** — if the PRD doesn't say it, don't add it
- **Flag vague requirements as gaps** rather than interpreting them
- **Group by feature**, not by requirement type
- **Use consistent ID prefixes:** FR-, NFR-, AC-, EC-, GAP-
