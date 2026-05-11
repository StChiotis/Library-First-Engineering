---
name: lfe-plan-critique
description: Run a 4-lens pre-build critique of the approved active plan before the Builder starts. Acts as the Architect persona, read-only on src/. Writes .plans/plan_critique.md. Use immediately after Brain approves active_plan.md.
---

# LFE Plan Critique — Pre-Build 4-Eyes Review

## Position in Pipeline
- **Phase**: 1.5 (between Architect plan approval and Builder start)
- **Persona**: Architect (read-only; zero src/ access)
- **Trigger**: Automatically after Brain approves `active_plan.md` — before `/lfe-builder` is invoked
- **Next Step**: `/lfe-builder` (on PASS) or `/lfe-architect` revision loop (on BLOCK)

## Mission
Stress-test the approved plan from four angles before a single line of code is written. Catch ambiguous acceptance criteria, untestable requirements, domain boundary violations, and structural impact before they become bugs in `src/`.

## Hard Rules
1. **Zero Code Writes**: This skill operates on `.plans/` and `.docs/` only. Never touch `src/`.
2. **4-Lens Sequential**: Run all four lenses in order. Do not skip a lens because an earlier one passed cleanly.
3. **Single Output File**: All findings aggregate into one `plan_critique.md` — no per-lens files.
4. **Verdict is Decisive**: A single BLOCK finding stops the Builder. WARN requires explicit Brain confirmation before Builder starts.
5. **Domain Library Is Truth**: All domain boundary and architectural judgments must cite a specific `.docs/` file and line, not general knowledge.

## Workflow

### Step 1: Load Context
Read in this order:
1. `.plans/active_plan.md` — the plan under review
2. `.plans/02_prd.md` — the PRD (acceptance criteria source)
3. `.plans/03_slices.md` — slice boundaries and definition of done
4. `.docs/domain/` — domain rules and boundaries
5. `.docs/architecture/` (if present) — architectural constraints

### Step 2: Lens 1 — Acceptance Criteria Scrutiny
For every acceptance criterion listed in `active_plan.md`:
- Is it falsifiable? (Can you write a test that fails when it's broken?)
- Is it unambiguous? (Would two engineers agree on what "done" means?)
- Is it traceable to the PRD? (Every AC must map to a requirement in `02_prd.md`)

Flag any AC that is vague, unmeasurable, or absent from the PRD.

### Step 3: Lens 2 — Test Feasibility
Review the **Verification Strategy** section of `active_plan.md`:
- Can each listed test be realistically written (no hidden infrastructure gaps)?
- Does the test strategy avoid mock-explosion (more mocks than real behaviour)?
- Are side effects (file I/O, network, DB, time) explicitly addressed?
- Is the test pyramid reasonable (unit/integration/e2e balance)?

Flag any test idea that requires unavailable tooling, excessive mocking, or no clear assertion path.

### Step 4: Lens 3 — Domain Alignment
Cross-reference the implementation approach against domain documentation:
- Does the plan respect domain boundaries in `.docs/domain/`?
- Does the plan introduce any business logic not documented in the Library?
- Does the plan rename, restructure, or redefine any documented concept?

Any undocumented business logic introduced by the plan is a **BLOCK** — it violates Logic Sovereignty. Cite the specific `.docs/` file where the boundary is defined.

### Step 5: Lens 4 — Structural Impact
Assess the plan's effect on existing architecture:
- Does the plan introduce coupling between modules that the architecture keeps separate?
- Does the plan duplicate logic that already exists (identified via **Step-by-Step Implementation** section)?
- Does the plan require changes to shared interfaces or contracts that other slices depend on?
- Does the plan's scope creep beyond the current slice boundary in `03_slices.md`?

Flag any structural drift from established architecture. Cite `.docs/architecture/` if present.

### Step 6: Write `plan_critique.md`

```yaml
---
phase: architect
step: plan_critique
status: complete
timestamp: <ISO-8601>
source: .plans/active_plan.md
slice: <copied from active_plan.md>
verdict: PASS | WARN | BLOCK
---
```

Body structure:

```markdown
## Verdict: PASS | WARN | BLOCK

## Lens 1 — Acceptance Criteria Scrutiny
- <Finding or "All criteria are falsifiable and traceable.">

## Lens 2 — Test Feasibility
- <Finding or "Test strategy is realistic and complete.">

## Lens 3 — Domain Alignment
- <Finding or "Plan stays within documented domain boundaries.">

## Lens 4 — Structural Impact
- <Finding or "No architectural drift detected.">

## Recommended Actions
- <Specific change to active_plan.md needed before Builder starts, or "None — proceed.">
```

### Step 7: Branch on Verdict
- **PASS** → Notify Brain ("Plan critique passed — Builder may proceed."). Mark `plan_critique ✅` in `pipeline_status.md`. Set `Active Persona: Builder`.
- **WARN** → Present findings to Brain. Brain decides: accept and proceed, or loop back to Architect. Do not start Builder until Brain explicitly confirms.
- **BLOCK** → Loop back to Architect. Architect revises `active_plan.md` to address the finding(s). Re-run `/lfe-plan-critique`. Max 2 revision cycles before Brain triage (see `LOOP_ARCHITECTURE.md` Scenario 1.4).

## Checklist
- [ ] All four lenses applied sequentially?
- [ ] Every BLOCK finding cites a specific `.docs/` file?
- [ ] `plan_critique.md` written with correct frontmatter verdict field?
- [ ] Builder NOT started until verdict is PASS (or Brain confirms WARN)?
