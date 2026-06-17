---
name: lfe-to-issues
description: Break the PRD into independently-demoable vertical slice issues. Use in the Architect sub-pipeline after to-prd completes. Requires human approval of slices.
---

# LFE To Issues

## Position in Pipeline
- **Phase**: 1 (Architect sub-pipeline, Step 3)
- **Persona**: Architect
- **Input**: `.plans/02_prd.md`
- **Output**: `.plans/03_slices.md`
- **Gate**: 🛑 Human must approve slices before proceeding

## Process

### 1. Read input
Read `.plans/02_prd.md` as the source of truth — it carries this step's inputs, not the conversation.

### 2. Draft vertical slices
Break the PRD into **tracer bullet** slices. Each slice is a thin vertical cut through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

Slices are either:
- **AFK** — can be implemented and merged without human interaction
- **HITL** — requires a human decision, design review, or external dependency

Prefer AFK over HITL where possible.

**Vertical slice rules:**
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones

### 3. Present to human
Present the proposed breakdown as a numbered list. For each slice show:

- **Title**: short descriptive name
- **Type**: HITL / AFK
- **Blocked by**: which other slices must complete first
- **User stories covered**: which user stories this addresses

Ask the human:
- Does the granularity feel right?
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked as HITL and AFK?

**🛑 Iterate until the human approves the breakdown.**

### 4. Write coordination file
Save approved slices to `.plans/03_slices.md`:

```yaml
---
phase: architect
step: 3_slices
status: complete
timestamp: <ISO-8601>
source: .plans/02_prd.md
total_slices: <N>
approved_by_human: true
---
```

```markdown
## Slice 1: <Title>
- **Type**: AFK
- **Blocked by**: None
- **Acceptance criteria**:
  - [ ] Criterion 1
  - [ ] Criterion 2

## Slice 2: <Title>
- **Type**: AFK
- **Blocked by**: Slice 1
- **Acceptance criteria**:
  - [ ] Criterion 1
```

### AC-writing discipline
Write each acceptance criterion as a falsifiable, prose statement of the observable end-state — not as a literal search/regex string (reproducing a pattern in an AC re-triggers that pattern's own check). For slices that **create, edit, or archive structured documentation**, draft the ACs with the doc-edit AC-design patterns the Architect's Plan-Composition Discipline enumerates — section-name conformance, cross-reference semantic-inversion anchoring, byte-identity sidecar fixtures, section-boundary extraction caveats (numeric-prefix collision + non-numeric headings), cross-file scope symmetry, edit-mechanism ACs, and archive-move ACs — so the downstream `active_plan.md` inherits them rather than rediscovering them at Inspector time.

Update `pipeline_status.md` coordination tracker to mark step 03 as ✅.
