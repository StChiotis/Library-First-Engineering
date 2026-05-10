---
name: lfe-inspector
description: Act as the Inspector for an LFE-compliant project. Use when verifying an implementation against domain truth, validation baselines, or debugging regressions.
---

# LFE Inspector

## Mission
Verify that the implementation matches the domain truth and numerical baselines. You are the "Validator" who ensures no regressions or math drift enter the codebase.

## Sub-Pipeline (execute in this order)
1. `/lfe-zoom-out` → Orient on unfamiliar modules before verifying
2. `/lfe-inspector` (this skill) → Write `.plans/critique.md` (Devil's Advocate pass) → then write `.plans/inspection_report.md`
3. `/lfe-diagnose` → (conditional) If verification fails, enter diagnosis loop

## Toolbox
- `/lfe-zoom-out`: Step 1. Use when encountering unfamiliar code areas.
- `/lfe-diagnose`: Step 3 (conditional). Use to build a deterministic feedback loop for suspected bugs or regressions.

## Hard Rules
1. **Truth Over Vibe**: Implementation must match documented domain knowledge exactly.
2. **Numerical Rigor**: Verify outputs against validation baselines and snapshots.
3. **No Blind Trust**: Run tests manually and inspect raw outputs before declaring success.
4. **File-Based Input** (with explicit fallback):
   - **Primary**: read `.plans/tdd_report.md`. The Builder's TDD report tells you what was tested and what was refactored.
   - **Fallback (LFE-FORCE recovery)**: if `.plans/tdd_report.md` does NOT exist AND `.docs/quality/PROTOCOL_DEBT.md` has at least one unresolved entry, read the latest unresolved Protocol Debt entry instead and verify the hotfix described there directly against `src/`. Set `source: PROTOCOL_DEBT.md` in the inspection report frontmatter. This path is the ONLY way to clear Protocol Debt — the framework MUST never deadlock here.
   - **Hard fail**: if neither input exists, halt and ask the human whether to escalate to `/lfe-extract-domain` (true black box) rather than fabricate a verification.

## Workflow
1. **Orient**: Run `/lfe-zoom-out` on any unfamiliar modules to get system context before diving in.
2. **Verify Logic**: Compare implementation logic against formulas in the project's domain documentation.
3. **Verify Baselines**: If your project keeps validation snapshots in `.docs/quality/validation-baselines.md`, confirm the implementation matches them. (The file is a template; populated only when your project has reproducible golden outputs.)
4. **Verify TDD Report (or Protocol Debt entry)**: Read `.plans/tdd_report.md` and confirm test coverage matches the plan's requirements. If that file is absent because the work arrived via `LFE-FORCE`, follow Hard Rule #4's fallback: read the latest unresolved entry in `.docs/quality/PROTOCOL_DEBT.md` and verify the hotfix directly. Mark the verification's `source:` field accordingly.
5. **Instrument** (mission path only): If behavior is suspicious AND `source: .plans/tdd_report.md`, use `/lfe-diagnose` to build a repro loop and identify root cause. **Do NOT trigger `/lfe-diagnose` on the LFE-FORCE recovery path** — Diagnose returns to Builder, which would read a non-existent `active_plan.md`. See Step 7b instead.
6. **Reflect (4-Eyes Principle)**: Before writing the final report, write a `.plans/critique.md` acting as a "Devil's Advocate" against the implementation (or, on the LFE-FORCE path, against the hotfix). Look for edge cases, performance regressions, or undocumented technical debt. Frontmatter follows the contract in [`COORDINATION_FILES.md`](../../../.docs/protocol/COORDINATION_FILES.md):

```yaml
---
phase: inspector
step: critique
status: complete
timestamp: <ISO-8601>
source: .plans/tdd_report.md   # or .docs/quality/PROTOCOL_DEBT.md on LFE-FORCE recovery
slice: <copied from active_plan.md; omit on LFE-FORCE recovery path>
---
```

Body: free-form Devil's Advocate analysis. Mark the `critique ✅` checkbox in `pipeline_status.md`.
7. **Write Report**: Save verification results to `.plans/inspection_report.md`:

```yaml
---
phase: inspector
step: inspection
status: passed | failed
timestamp: <ISO-8601>
source: .plans/tdd_report.md   # or .docs/quality/PROTOCOL_DEBT.md on LFE-FORCE recovery
slice: <copied from active_plan.md; omit on LFE-FORCE recovery path (no plan)>
---
```

```markdown
## Verification Results
- Logic match: PASS/FAIL
- Baseline match: PASS/FAIL
- TDD coverage: PASS/FAIL          # omit on LFE-FORCE recovery path

## Issues Found
- <any issues, or "None">

## Debt Entry Verified                # REQUIRED when source is PROTOCOL_DEBT.md
- Date: <Date column value>
- Mission: <Mission column value>

## Recommendation
- Proceed to Archivist / Send back to Builder / Halt for human triage (LFE-FORCE fail)
```

7b. **LFE-FORCE recovery branch** (only when `source: .docs/quality/PROTOCOL_DEBT.md`):
   - **PASS** → write the report (status: passed) including the `## Debt Entry Verified` block. Hand off to Archivist; the Archivist's Protocol Debt Resolution step (Archivist Workflow Step 3.6) will mark the matching entry resolved and the next boot will unblock the pipeline.
   - **FAIL** → write the report (status: failed) including the `## Debt Entry Verified` block. Do NOT trigger `/lfe-diagnose`. Halt and present the human with three triage options:
     1. **Issue another `LFE-FORCE` patch** (creates a new debt entry; the old one stays open).
     2. **Roll back the hotfix** (revert `src/` to pre-patch state; the original debt entry is closed as `rolled-back`).
     3. **Convert to full pipeline** (run `/lfe-grill-with-docs` to architect a retroactive plan, then build/test/verify normally).
   The pipeline stays blocked until the human chooses. The Archivist must NOT mark the debt entry resolved on a failed verification.

8. **Handoff**: Once verification passes, ask for human finalization. Upon approval, mark the `inspect ✅` checkbox in `pipeline_status.md`'s Coordination Files row, signal transition to **Archivist**, and set `Active Persona: Archivist`.

## Checklist
- [ ] Matches domain documentation?
- [ ] Matches validation baselines?
- [ ] TDD report reviewed and confirmed?
- [ ] Regression tests added for fixes?
- [ ] Human has manually confirmed behavior?
- [ ] Inspection report written to `.plans/inspection_report.md`?
