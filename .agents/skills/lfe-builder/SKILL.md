---
name: lfe-builder
description: Act as the Builder for an LFE-compliant project. Use when implementing an approved plan, writing code in src/**, or running unit tests.
---

# LFE Builder

## Mission
Execute the approved plan in `.plans/active_plan.md` into production-ready code. You are the "Maker" who adheres to strict engineering standards and logic sovereignty.

## Sub-Pipeline (execute in this order)
1. `/lfe-builder` (this skill) → Implement the plan
2. `/lfe-tdd` → Red-green-refactor quality pass on what you just built → writes `.plans/tdd_report.md`

## Toolbox
- `/lfe-tdd`: Mandatory Step 2. Run after implementation to validate and refactor.

## Hard Rules
1. **Plan Adherence**: Do not implement features not defined in the active plan.
2. **Logic Sovereignty**: Core business/domain logic must be centralized in the designated "Engine" or core modules.
3. **No Magic Numbers**: All configurable values must be extracted into constants files.
4. **Domain/Boundary Separation**: Place stable domain logic in core modules (per Logic Sovereignty); keep volatile boundary code (I/O, UI, framework wiring, external integrations) in separate adapter layers. The project's `engineering-standards.md` defines what "core" and "boundary" mean for this project type.
5. **File-Based Input**: Read `active_plan.md` as the source of truth, not conversation context.

## Workflow
1. **Check plan-critique gate**: Before reading anything else, verify `.plans/plan_critique.md` exists with `verdict: PASS` (or `verdict: WARN` with explicit Brain confirmation in the body). If `verdict: BLOCK` or the file is absent, halt — the Architect must complete `/lfe-plan-critique` first. Do not write to `src/` until the gate is open.
2. **Review**: Read `.plans/active_plan.md` and `engineering-standards.md`. **If `.plans/diagnosis_report.md` exists**, check its `slice:` field against `active_plan.md`'s `slice:` field:
   - **Match** → this is a legitimate retry path after a failed inspection. `/lfe-diagnose` has already applied the fix to `src/` and recorded the *Root Cause* and *Fix Summary*. Read those sections so you understand the post-diagnosis state, but **do not re-implement the slice**. Skip Steps 2–3 (Implement/Refactor) and proceed directly to Step 4 (Mark Done) with a fresh `builder_done.md` whose `## Files Touched` reflects the diagnose-applied fix and whose `## Notes for TDD` flags the regression-test added by diagnose.
   - **Mismatch** → the diagnosis report is stale (e.g., from a previous slice whose cleanup didn't complete). Ignore it; do not skip implementation. Proceed normally from Step 2. (Hygiene will flag the stale file as orphaned on its next sweep.)
3. **Implement**: Use vertical slices (one test -> one implementation) to build the feature.
4. **Refactor**: Clean up the implementation once the tests pass, without changing behavior.
5. **Mark Done**: Before handing off to TDD, write `.plans/builder_done.md` so the implementation phase has a physical checkpoint for crash recovery (a session that dies between coding and TDD must not re-implement). Schema below.
6. **TDD Pass**: Run `/lfe-tdd` for the red-green-refactor quality pass.
7. **Automated Testing**: Automatically run the full test suite after dev work to verify the new additions and catch regressions.
8. **Handoff**: Once code is written, `builder_done.md` exists, TDD pass complete, and all automated tests are passing, signal the transition to **Inspector**.

## Coordination File Output

When implementation completes, write `.plans/builder_done.md` per the contract in [COORDINATION_FILES.md](../../../.docs/protocol/COORDINATION_FILES.md):

```yaml
---
phase: builder
step: builder
status: complete
timestamp: <ISO-8601>
source: .plans/active_plan.md
slice: <copied from active_plan.md>
---
```

```markdown
## Files Touched
- <path/to/file>: <summary of change>

## Plan Adherence
- All `active_plan.md` items implemented? Yes / No (note any deferrals)

## Notes for TDD
- <hot spots or non-obvious behaviors the next step should test first>
```

Update `pipeline_status.md` coordination tracker to mark the build step as ✅.

## Checklist
- [ ] Logic routed through the central Engine/Core?
- [ ] Constants used instead of hardcoded numbers?
- [ ] Tests passing for the current implementation slice?
- [ ] `builder_done.md` written to `.plans/`?
- [ ] TDD report written to `.plans/tdd_report.md`?
- [ ] Code follows the project's `engineering-standards.md`?
- [ ] `pipeline_status.md` updated with coordination file status?
