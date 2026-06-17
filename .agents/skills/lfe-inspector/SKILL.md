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
3. **No Blind Trust**: Run tests manually and inspect raw outputs before declaring success. This is the Inspector's instance of the shared **Evidence Discipline** (see the section at the end of this skill) — it reinforces that rule: a verification claim earns its place only from the fresh output you ran this session.
4. **File-Based Input** (with explicit fallback):
   - **Primary**: read `.plans/tdd_report.md`. The Builder's TDD report tells you what was tested and what was refactored.
   - **Fallback (LFE-FORCE recovery)**: if `.plans/tdd_report.md` does NOT exist AND `.docs/quality/PROTOCOL_DEBT.md` has at least one unresolved entry, read the latest unresolved Protocol Debt entry instead and verify the hotfix described there directly against `src/`. Set `source: .docs/quality/PROTOCOL_DEBT.md` in the inspection report frontmatter — exact full path, because Archivist Step 3.6's matcher keys off this string verbatim. This path is the ONLY way to clear Protocol Debt — the framework MUST stay recoverable here (no deadlock).
   - **Hard fail**: if neither input exists, halt and ask the human whether to escalate to `/lfe-extract-domain` (true black box) rather than fabricate a verification.

## Cycle Guard

At the start of every Inspector run, determine the **cycle number** for the current slice and remember it. The cycle decision is enforced at the **failure point**, not at entry — the Builder may have fixed the issue and this attempt may pass cleanly.

### How to determine the cycle

1. Read `.plans/inspection_report.md` if it exists; note its `slice:` field and `status:` value.
2. Read `.plans/diagnosis_report.md` if it exists; note its `slice:` field.
3. Match against the current `active_plan.md` slice ID:
   - **No prior `inspection_report.md` for this slice** → this is **Cycle 1**.
   - **Prior `inspection_report.md` with `status: failed` AND prior `diagnosis_report.md` for the same slice** → this is **Cycle 2**.
   - **Prior `inspection_report.md` with `status: escalated`** → the previous run already halted at Cycle 2; pipeline should be paused awaiting Brain triage. Halt and re-present the menu.
   - **Prior `inspection_report.md` with `status: passed` (with or without a `.plans/rework_directive.md`)** → this is a finalization-rework round, not a mechanical failure. Treat it as **Cycle 1** for the diagnose ledger; the rework count lives separately in `rework_directive.md` (`rework_round`), leaving the Cycle-2 wall to mechanical failures alone. A genuine mechanical re-failure this round still writes `status: failed` and takes the normal Cycle-1 → diagnose path, so the two axes compose cleanly.

### Decision at the failure point

After running verification (Workflow steps 1–4):

- **Verification PASSES** → proceed normally regardless of cycle number.
- **Verification FAILS on Cycle 1** → trigger `/lfe-diagnose` as usual.
- **Verification FAILS on Cycle 2** → **halt** — write `.plans/inspection_report.md` with `status: escalated` instead of re-invoking `/lfe-diagnose`, and present the Brain with three triage options:
  - **Option A — Accept and ship as known debt**: proceed to Archivist; log the open issue to `.docs/quality/known-issues.md` and a new `PROTOCOL_DEBT.md` entry.
  - **Option B — Escalate LFE-FORCE**: Brain invokes `LFE-FORCE` to apply a targeted patch; old debt entry stays open.
  - **Option C — Re-plan from scratch**: wipe execution files; loop back to Architect to re-design the slice from `03_slices.md`.

  The pipeline waits for the Brain to select an option before proceeding. Record the chosen triage option in the body of the escalated `inspection_report.md`.

---

## Workflow
1. **Orient**: Run `/lfe-zoom-out` on any unfamiliar modules to get system context before diving in.
1.5. **Consult Plan-Critique (mission path only)**: Read `.plans/plan_critique.md`. The Architect's pre-build review is a structured pre-flagging of risk that should focus your verification:
   - `verdict: PASS` → brief read for context; no action required.
   - `verdict: WARN` with `brain_confirmation` set → extract findings under Lens 3 (Domain Alignment) and Lens 4 (Structural Impact) and treat them as **priority verification targets** in Steps 2–4. The Brain accepted the risk knowing these areas were flagged; confirm the Builder actually addressed them rather than glossing over.
   - `verdict: BLOCK` reaching the Inspector is a protocol violation (Builder Step 1 gate should have refused) — halt and report.
   - **LFE-FORCE recovery path** (no `plan_critique.md` exists) → skip this step.

   This step exists to make plan-critique a load-bearing artifact for downstream verification, not just a Builder gate signal.
2. **Verify Logic**: Compare implementation logic against formulas in the project's domain documentation.
3. **Verify Baselines**: If your project keeps validation snapshots in `.docs/quality/validation-baselines.md`, confirm the implementation matches them. (The file is a template; populated only when your project has reproducible golden outputs.)
4. **Verify TDD Report (or Protocol Debt entry)**: Read `.plans/tdd_report.md` and confirm test coverage matches the plan's requirements. If that file is absent because the work arrived via `LFE-FORCE`, follow Hard Rule #4's fallback: read the latest unresolved entry in `.docs/quality/PROTOCOL_DEBT.md` and verify the hotfix directly. Mark the verification's `source:` field accordingly.
5. **Instrument** (mission path only): If verification fails AND `source: .plans/tdd_report.md`, consult the **Cycle Guard** (above) before invoking diagnose:
   - **Cycle 1 failure** → use `/lfe-diagnose` to build a repro loop and identify root cause.
   - **Cycle 2 failure** → **halt**. Write `status: escalated` and present Brain triage menu; leave `/lfe-diagnose` uninvoked.
   - **LFE-FORCE recovery path** (`source: PROTOCOL_DEBT.md`) → skip `/lfe-diagnose` regardless of cycle. See Step 7b.
6. **Sub-Skill Dispatch** (mission path — for LFE-FORCE recovery branch see Step 7b):
   a. **Ensure dispatch directory exists**: `.plans/checks/` is owned by the Inspector. Create it if missing (idempotent — safe to call when already present). Sub-skills rely on the Inspector having prepared their parent directory rather than creating it themselves.
   b. **Load config**: Read `.docs/quality/inspector-config.md` to determine which sub-skills are enabled.
   c. **Read per-mission overrides**: Check `active_plan.md` for an `## Inspector Overrides` section (typed schema — see `lfe-architect/SKILL.md` body template). Any override takes precedence over the config table.
   d. **Dispatch in fixed order**: `lfe-security-check` → `lfe-perf-check` → `lfe-complexity-check` → `lfe-dep-audit` → `lfe-mutation-verify` → `lfe-visual-check`. **Floors are config-independent minimums**, evaluated when computing the final enabled set: `lfe-security-check` arms on the Security Floor classes, and `lfe-visual-check` arms on the **Visual Floor** — whenever a changed file in `builder_done.md` matches one of the visual file classes listed in `inspector-config.md`. An override leaves a floored sub-skill armed. For each enabled sub-skill, apply the **resume rule** before invoking:
      - **Skip** the sub-skill if `.plans/checks/<sub-skill-name>_findings.md` exists AND its YAML frontmatter parses with `status: complete`. This is the **only** valid skip signal — file presence alone is not sufficient (a crash mid-write leaves the file present but with no `status: complete` field).
      - **Invoke** otherwise (file absent, frontmatter unparseable, or `status` is any value other than `complete`). The sub-skill overwrites its findings file with a complete one.
   e. **Aggregate**: After dispatch completes, read every `.plans/checks/*_findings.md` whose frontmatter has `status: complete`. Build a labelled summary block per sub-skill for inclusion in `critique.md` (Step 6b).
   f. **Re-aggregation on crash recovery**: If `critique.md` is missing OR has incomplete sub-skill sections, but all enabled sub-skills' findings files already have `status: complete`, re-aggregate only, rather than re-running the sub-skills. The sub-skill outputs are reusable across the same slice; only `critique.md` needs to be rebuilt.
   g. **No sub-skills enabled**: skip this step entirely. `critique.md` will contain only the 4-Eyes Devil's Advocate body, with no labelled sub-skill sections.

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

## Visual
<content of .plans/checks/visual_findings.md, or "Sub-skill not armed.">
```

Mark the `critique ✅` checkbox in `pipeline_status.md`.
7. **Write Report**: Save verification results to `.plans/inspection_report.md`:

```yaml
---
phase: inspector
step: inspection
status: passed | failed | escalated
timestamp: <ISO-8601>
source: .plans/tdd_report.md   # or .docs/quality/PROTOCOL_DEBT.md on LFE-FORCE recovery
slice: <copied from active_plan.md; omit on LFE-FORCE recovery path (no plan)>
visual_confirmed: <ISO-8601 | null>   # OPTIONAL — set at Step 8(b) on a visual slice (the human's visual sign-off)
visual_signoff: <token | null>        # OPTIONAL — agent-transcribed from the Brain's approval (brain_confirmation trust model)
---
```

The `visual_confirmed` / `visual_signoff` fields are optional typed frontmatter that ride below `source:` (the same pattern as the `rework_round` field on `rework_directive.md`). They are absent on a non-visual slice and set at Step 8(b) on a visual one.

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

   **Sub-Skill Dispatch (LFE-FORCE subset)** — run *before* writing `inspection_report.md` so the report includes hotfix-audit findings:
   - Ensure `.plans/checks/` exists (mkdir if missing).
   - The LFE-FORCE subset bypasses `inspector-config.md` and runs a **fixed subset** appropriate for a hotfix audit:

   | Sub-skill | Run on LFE-FORCE? | Reason |
   |---|---|---|
   | `lfe-security-check` | **Always** | Hotfixes are the highest-risk window for accidentally introducing vulnerabilities; OWASP walk is cheap insurance. |
   | `lfe-complexity-check` | **Always** | A rushed patch is the canonical complexity-debt source; surface it now while it is still in memory. |
   | `lfe-dep-audit` | **Conditional** — run only if the hotfix diff touched a dependency manifest file. | Otherwise there is nothing for the audit to read. |
   | `lfe-perf-check` | **Conditional** — run only if the `PROTOCOL_DEBT.md` entry's `Mission` or `## Notes` flags a hot-path edit. | Default off because most hotfixes are correctness-only. |
   | `lfe-mutation-verify` | **Skip** | Hotfixes rarely add new tests; mutation reasoning over absent tests produces noise. |

   For each sub-skill that runs, apply the same resume rule as Step 6.d (skip on `status: complete`, invoke otherwise). Each sub-skill writes to `.plans/checks/<name>_findings.md` with frontmatter `slice` omitted (LFE-FORCE path has no plan, no slice).

   **After dispatch (verdict branch)**:
   - **PASS** → run Step 6b (4-Eyes critique) including aggregated sub-skill findings, then write `inspection_report.md` (status: passed) including the `## Debt Entry Verified` block. Hand off to Archivist; the Archivist's Protocol Debt Resolution step (Archivist Workflow Step 3.6) will mark the matching entry resolved and the next boot will unblock the pipeline. Note: a sub-skill `Critical` finding on PASS verification does NOT block resolution — it is appended to `.docs/quality/known-issues.md` by the Archivist as a known follow-up so the debt can still clear, but the issue is visible.
   - **FAIL** → run Step 6b for the critique anyway (the findings inform the triage choice), then write `inspection_report.md` (status: failed) including the `## Debt Entry Verified` block. Skip `/lfe-diagnose`; halt and present the human with three triage options:
     1. **Issue another `LFE-FORCE` patch** (creates a new debt entry; the old one stays open).
     2. **Roll back the hotfix** (revert `src/` to pre-patch state; the original debt entry is closed as `rolled-back`).
     3. **Convert to full pipeline** (run `/lfe-grill-with-docs` to architect a retroactive plan, then build/test/verify normally).
   The pipeline stays blocked until the human chooses. The Archivist leaves the debt entry unresolved on a failed verification.

8. **Finalization Gate (unified — three outcomes)**: Verification passing is a *technical* pass — the Inspector's own authority over logic, baselines, and test coverage. `inspection_report.md` already carries `status: passed` (Step 7) and the `inspect ✅` checkbox is set. The *visual* verdict belongs to the Brain alone. First determine whether this is a **visual slice** — any changed file in `builder_done.md` matches one of the visual file classes in `inspector-config.md` (the Visual Floor will already have armed `lfe-visual-check`, so `.plans/checks/visual_findings.md` is present). Then ask the Brain to finalize and branch:
   - **(a) APPROVE with visual confirmation present** — a non-visual slice, or a visual slice whose `inspection_report.md` already carries `visual_confirmed` + `visual_signoff` → the slice is done. If a `.plans/rework_directive.md` exists for this slice (a prior rework round has now converged), delete it. Set `Active Persona: Archivist` and hand off.
   - **(b) APPROVE but visual confirmation absent on a visual slice** → obtain it first. Present the `## Human-Action Instruction` from `.plans/checks/visual_findings.md`; on the Brain's visual approval, **rewrite** `inspection_report.md` recording `visual_confirmed: <ISO-8601>` and `visual_signoff: <token transcribed verbatim from the Brain's approval>`. Then proceed exactly as (a). This is a **protocol floor** (see GOVERNANCE Discipline Gates): the Inspector must not hand a visual slice to the Archivist until both fields are recorded — the visual sign-off is the close condition, so the close stays honest.
   - **(c) REJECT** — the Brain finds a defect the technical pass did not catch (most often a visual one) → go to **Step 8b** (the human-rejection rework re-entry).

8b. **Rework Re-Entry** (Brain rejected at finalization):
   1. Capture the Brain's defect description verbatim. Derive a short `directive_hash` from that text.
   2. Read `.plans/rework_directive.md` if it exists → its `rework_round` (call it `N0`) and `directive_hash`. Absent → `N0 = 0`.
   3. **Idempotency:** if the file exists AND its `directive_hash` matches the one just derived, this is a re-run of the same rejection — keep `N = N0`. Otherwise `N = N0 + 1`. (The increment keys on the on-disk directive-hash transition, not on a chat utterance, so a crash-resume of the same rejection stays exactly-once.)
   4. **Cap:** the finalization-rework limit is **5 rounds** (file-based in `rework_round`; see GOVERNANCE Correction Cycle Limits; project-overridable). When `N` exceeds 5, **halt** and present the Brain a safe triage menu — **A** accept as a known issue (log it to `.docs/quality/known-issues.md`, then proceed to Archivist), **B** re-plan the slice via the Architect (wipe execution files, loop back to `03_slices.md`), **C** start a fresh mission. The cap path stays within the pipeline (it offers no in-place patch).
   5. Write `.plans/rework_directive.md` — frontmatter `phase: inspector`, `step: rework`, `status: complete`, `timestamp`, `source: .plans/inspection_report.md`, `slice: <copied from active_plan.md>`, plus the typed fields `rework_round: N` and `directive_hash: <hash>` — with a `## Rework Directive` body: what is wrong, expected vs. observed, and (for a UI defect) which surface. This sentinel is the Builder's re-entry brief and the crash-recovery anchor: a present sentinel resumes the pipeline at the Builder.
   6. Delete any `.plans/diagnosis_report.md` for this slice, so the Builder's rework branch is unambiguous (the rework re-entry re-implements; it does not reuse a stale diagnose fix).
   7. Reset the per-slice coordination checkboxes (`build / tdd / critique / inspect`) to ⬜ in `pipeline_status.md` — the code is about to change, so the prior technical pass goes stale. (Checkboxes only advance ⬜→✅ during a run, so this reset is explicit, mirroring Partial Cleanup.)
   8. Set `Active Persona: Builder`, and re-enter Phase 2 at the Builder.
   **Guardrail:** the one legitimate path to fix a rejected defect is this persona flip back to the Builder. Editing `src/**` while the Active Persona is Inspector or Archivist is a path-lock violation — it would ship the fix unverified.

## Checklist
- [ ] Matches domain documentation?
- [ ] Matches validation baselines?
- [ ] TDD report reviewed and confirmed?
- [ ] Regression tests added for fixes?
- [ ] Finalization gate resolved — Brain approved (→ Archivist) or rejected (→ Step 8b rework re-entry to Builder)?
- [ ] Inspection report written to `.plans/inspection_report.md`?

## Evidence Discipline

A completion claim earns its place only when fresh tool output from this session backs it.
- "Tests pass" means *paste the run's pass/fail counts* from the run you just executed.
- "No regression" means *paste the counts and show they hold or rise* — a dropped count is a regression to report, rather than a number to round away.
- State facts about files, APIs, and dependencies from what you just read this session, rather than from memory.
- Treat hedge words — "should work", "probably", "I think it passes" — as a signal to stop and run the verifying step, then report the real result.

**Confidence routing (per claim).**

| Confidence | Basis | Action |
|---|---|---|
| High | Tool-verified this session | State it plainly. |
| Medium | Inferred from context | State it with the caveat that it is inferred. |
| Low | Recalled from training or memory | Verify first (read or run), then state. |

**Hallucination-signal checklist.** Pause → Verify → Correct the moment you catch yourself:
- referencing a file or symbol you have yet to open this session;
- quoting a number (count, version, size) without a source you just saw;
- stating something that runs against the latest tool output;
- importing or assuming a dependency you have yet to confirm exists.
