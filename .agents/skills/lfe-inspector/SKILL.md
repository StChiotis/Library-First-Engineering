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
4. **File-Based Input**: Read `.plans/tdd_report.md` as input. The Builder's TDD report tells you what was tested and what was refactored.

## Workflow
1. **Orient**: Run `/lfe-zoom-out` on any unfamiliar modules to get system context before diving in.
2. **Verify Logic**: Compare implementation logic against formulas in the project's domain documentation.
3. **Verify Baselines**: Check current outputs against `validation-baselines.md` or equivalent snapshots.
4. **Verify TDD Report**: Read `.plans/tdd_report.md` and confirm test coverage matches the plan's requirements.
5. **Instrument**: If behavior is suspicious, use `/lfe-diagnose` to build a repro loop and identify root cause.
6. **Reflect (4-Eyes Principle)**: Before writing the final report, write a `.plans/critique.md` acting as a "Devil's Advocate" against the implementation. Look for edge cases, performance regressions, or undocumented technical debt.
7. **Write Report**: Save verification results to `.plans/inspection_report.md`:

```yaml
---
phase: inspector
step: inspection
status: passed | failed
timestamp: <ISO-8601>
source: .plans/tdd_report.md
---
```

```markdown
## Verification Results
- Logic match: PASS/FAIL
- Baseline match: PASS/FAIL
- TDD coverage: PASS/FAIL

## Issues Found
- <any issues, or "None">

## Recommendation
- Proceed to Archivist / Send back to Builder
```

8. **Handoff**: Once verification passes, ask for human finalization. Upon approval, signal transition to **Archivist**. Update `pipeline_status.md`.

## Checklist
- [ ] Matches domain documentation?
- [ ] Matches validation baselines?
- [ ] TDD report reviewed and confirmed?
- [ ] Regression tests added for fixes?
- [ ] Human has manually confirmed behavior?
- [ ] Inspection report written to `.plans/inspection_report.md`?
