---
name: lfe-to-prd
description: Synthesise the grill session output into a structured Product Requirements Document. Use in the Architect sub-pipeline after grill-with-docs completes.
---

# LFE To PRD

## Position in Pipeline
- **Phase**: 1 (Architect sub-pipeline, Step 2)
- **Persona**: Architect
- **Input**: `.plans/01_grill_summary.md`
- **Output**: `.plans/02_prd.md`

## Process

1. **Read input**: Read `.plans/01_grill_summary.md`. Do NOT rely on conversation context — the file is the source of truth.

2. **Explore the repo** to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the PRD, and respect any ADRs in the area you're touching.

3. **Sketch modules**: Identify the major modules you will need to build or modify. Actively look for opportunities to extract deep modules that can be tested in isolation. Check with the human that these modules match their expectations.

4. **Write the PRD** using the template below and save it to `.plans/02_prd.md`.

## PRD Template

```yaml
---
phase: architect
step: 2_prd
status: complete
timestamp: <ISO-8601>
source: .plans/01_grill_summary.md
---
```

```markdown
## Problem Statement
The problem the user is facing, from the user's perspective.

## Solution
The solution to the problem, from the user's perspective.

## User Stories
1. As an <actor>, I want a <feature>, so that <benefit>

## Implementation Decisions
- Modules that will be built/modified
- Interfaces that will be modified
- Architectural decisions
- Schema changes
- API contracts

Do NOT include specific file paths or code snippets — they may become outdated.

## Testing Decisions
- Which modules will be tested
- What makes a good test (behavior, not implementation)
- Prior art for tests in the codebase

## Out of Scope
What is explicitly NOT part of this work.
```

Update `pipeline_status.md` coordination tracker to mark step 02 as ✅.
