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
   - **Fallback (LFE-FORCE recovery)**: if `.plans/tdd_report.md` does NOT exist AND `.docs/quality/PROTOCOL_DEBT.md` has at least one unresolved entry, read the latest unresolved Protocol Debt entry instead and verify the hotfix described there directly against `src/`. Set `source: .docs/quality/PROTOCOL_DEBT.md` in the inspection report frontmatter — exact full path, because Archivist Step 3.6's matcher keys off this string verbatim. This path is the ONLY way to clear Protocol Debt — the framework MUST never deadlock here.
   - **Hard fail**: if neither input exists, halt and ask the human whether to escalate to `/lfe-extract-domain` (true black box) rather than fabricate a verification.

## Cycle Guard (check FIRST, before any other step)

Before running any verification, check whether this is a repeated failure on the same slice:

1. Read `inspection_report.md` if it exists. Check the `slice:` frontmatter field and `status:` value.
2. Read `diagnosis_report.md` if it exists.
3. If the current `active_plan.md` slice ID matches the `inspection_report.md` slice AND `inspection_report.md` shows `status: failed`, this is at minimum the **2nd failed cycle** on this slice.
4. **On the 2nd failed cycle**: halt. Do not re-invoke `/lfe-diagnose`. Present the Brain with three triage options:
   - **Option A — Accept and ship as known debt**: proceed to Archivist; log the open issue to `.docs/quality/known-issues.md` and a new `PROTOCOL_DEBT.md` entry.
   - **Option B — Escalate LFE-FORCE**: Brain invokes `LFE-FORCE` to apply a targeted patch; old debt entry stays open.
   - **Option C — Re-plan from scratch**: wipe execution files; loop back to Architect to re-design the slice from `03_slices.md`.

   The pipeline must not proceed until Brain selects an option. Record the outcome in a new `inspection_report.md` with `status: escalated` and the chosen triage option in the body.

---

## Workflow
1. **Orient**: Run `/lfe-zoom-out` on any unfamiliar modules to get system context before diving in.
2. **Verify Logic**: Compare implementation logic against formulas in the project's domain documentation.
3. **Verify Baselines**: If your project keeps validation snapshots in `.docs/quality/validation-baselines.md`, confirm the implementation matches them. (The file is a template; populated only when your project has reproducible golden outputs.)
4. **Verify TDD Report (or Protocol Debt entry)**: Read `.plans/tdd_report.md` and confirm test coverage matches the plan's requirements. If that file is absent because the work arrived via `LFE-FORCE`, follow Hard Rule #4's fallback: read the latest unresolved entry in `.docs/quality/PROTOCOL_DEBT.md` and verify the hotfix directly. Mark the verification's `source:` field accordingly.
5. **Instrument** (mission path only): If behavior is suspicious AND `source: .plans/tdd_report.md`, use `/lfe-diagnose` to build a repro loop and identify root cause. **Do NOT trigger `/lfe-diagnose` on the LFE-FORCE recovery path** — Diagnose returns to Builder, which would read a non-existent `active_plan.md`. See Step 7b instead.
6. **Sub-Skill Dispatch** (skip on LFE-FORCE recovery path):
   a. Read `.docs/quality/inspector-config.md` to determine which sub-skills are enabled.
   b. Check `active_plan.md` for an `inspector-config-override:` comment; any override takes precedence.
   c. For each enabled sub-skill, invoke it in this fixed order: `lfe-security-check` → `lfe-perf-check` → `lfe-complexity-check` → `lfe-dep-audit` → `lfe-mutation-verify`.
   d. Each sub-skill writes its output to `.plans/checks/<sub-skill-name>_findings.md`.
   e. After all sub-skills complete, read all `.plans/checks/*_findings.md` files. Prepare a labelled summary block per sub-skill for inclusion in `critique.md`.
   f. If no sub-skills are enabled, proceed without this block.

6b. **Reflect (4-Eyes Principle)**: Before writing the final report, write a `.plans/critique.md` acting as a "Devil's Advocate" against the implementation (or, on the LFE-FORCE path, against the hotfix). Look for edge cases, performance regressions, or undocumented technical debt. Frontmatter follows the contract in [`COORDINATION_FILES.md`](../../../.docs/protocol/COORDINATION_FILES.md):

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

Body: free-form Devil's Advocate analysis. If sub-skills ran (Step 6), append their findings under labelled sections:

```markdown
## Security
<content of .plans/checks/security_findings.md, or "Sub-skill not enabled.">

## Performance
<content of .plans/checks/perf_findings.md, or "Sub-skill not enabled.">

## Complexity
<content of .plans/checks/complexity_findings.md, or "Sub-skill not enabled.">

## Dependencies
<content of .plans/checks/dep_findings.md, or "Sub-skill not enabled.">

## Mutation Coverage
<content of .plans/checks/mutation_findings.md, or "Sub-skill not enabled.">
```

Mark the `critique ✅` checkbox in `pipeline_status.md`.
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
