# LFE Loop Architecture: Exhaustive Scenario Map

This document maps every single possible mechanical loop, bypass, and recovery scenario in the Library-First Engineering (LFE) framework. 

A "Loop" is defined as a sequence of state changes. In LFE, these state changes are tracked via physical coordination files in the `.plans/` directory and the `pipeline_status.md` tracker.

---

## 1. The Primary Production Loops

### Scenario 1.1: The Happy Path (Single Slice)
*The standard flow for a small, well-defined feature.*
1. **Boot**: `/lfe-boot` -> Validates state.
2. **Architect**: `/lfe-grill-with-docs` -> Writes `01_grill_summary.md` -> `/lfe-to-prd` -> Writes `02_prd.md` -> `/lfe-to-issues` -> Writes `03_slices.md` -> Human approves -> `/lfe-architect` -> Writes `active_plan.md`.
3. **Builder**: `/lfe-builder` -> Modifies `src/` & Writes `builder_done.md` -> `/lfe-tdd` -> Writes `tdd_report.md`.
4. **Inspector**: `/lfe-inspector` -> Writes `critique.md` (4-Eyes) -> Verifies -> Writes `inspection_report.md` -> Human approves.
5. **Archivist**: `/lfe-archivist` -> Syncs docs & Deletes all `.plans/` execution files. -> **[Loop Closed]**

### Scenario 1.2: The Multi-Slice Loop (Vertical Slicing)
*The flow for a large feature broken into 3 testable chunks.*
1. **Architect**: Creates `03_slices.md` defining Slice 1, 2, and 3. Drafts plan for Slice 1.
2. **Execution**: Builder -> Inspector -> Archivist (Completes Slice 1).
3. **The Pivot**: Archivist checks `03_slices.md`. Sees Slice 2 is pending.
4. **The Partial Cleanup**: Archivist deletes execution files (`active_plan`, `tdd_report`, etc.) but keeps planning files (`01`, `02`, `03`).
5. **The Loop Back**: Control returns to Architect to draft the plan for Slice 2. -> **[Repeats until Slice 3 closes]**

---

## 2. The Feedback & Correction Loops

### Scenario 2.1: The Architect Rejection Loop
*The human stops the AI before code is written.*
1. **Trigger**: Human says "No" at the Slice Approval or Plan Approval gate.
2. **The Mechanical Reset**: Architect asks for human clarification -> Architect physically *overwrites* `03_slices.md` or `active_plan.md` with the new draft.
3. **The Loop Back**: Returns to the approval gate. -> **[Repeats until Human says Yes]**

### Scenario 2.2: The Bug Diagnosis Loop
*The Inspector finds a regression during verification.*
1. **Trigger**: `/lfe-inspector` fails its verification check.
2. **Cycle Guard**: Inspector checks whether `inspection_report.md` already records a `status: failed` for the **same slice ID** (see Cycle Guard in `lfe-inspector/SKILL.md`).
3. **First failure** → Diagnose: Inspector triggers `/lfe-diagnose` to find the root cause and writes `diagnosis_report.md`. Control returns to Builder to implement the fix.
4. **Re-Verify**: Builder runs `/lfe-tdd` → Inspector runs `/lfe-inspector`. -> **[One repeat allowed]**
5. **Second failure (same slice)** → **Halt**. Inspector does NOT trigger `/lfe-diagnose` again. Instead, Inspector presents Brain with three triage options:
   - **A — Accept as known debt**: proceed to Archivist; log issue to `known-issues.md` + `PROTOCOL_DEBT.md`.
   - **B — Escalate LFE-FORCE**: apply a targeted hotfix; old debt entry stays open.
   - **C — Re-plan from scratch**: wipe execution files; loop back to Architect from `03_slices.md`.
6. Brain selects option. Pipeline is blocked until selection is made.

### Scenario 1.4: The Pre-Build Critique Loop
*The Plan Critique finds a BLOCK before the Builder starts.*

The 2-revision limit is enforced via the `revision:` frontmatter field in `plan_critique.md` (Cycle Limits in `GOVERNANCE.md`). The counter is **file-based**, not conversational — a crash between attempts does not reset it.

1. **Trigger**: `/lfe-plan-critique` Step 0 detects no prior `plan_critique.md` → writes the file with `revision: 1`, `verdict: BLOCK`.
2. **Revision**: Architect presents findings to Brain and revises `active_plan.md` to address the block.
3. **Re-Critique**: `/lfe-plan-critique` re-runs. Step 0 reads the existing file (`revision: 1`, `verdict: BLOCK`) → this is Revision 2. Lenses re-run; new file written with `revision: 2`.
4. **Second BLOCK** (`revision: 2`, `verdict: BLOCK`) → **Halt at Step 0 on any subsequent invocation.** Do NOT re-run lenses. Present Brain with triage:
   - **A — Revert to PRD**: loop back to `/lfe-to-issues` to re-slice from `02_prd.md`.
   - **B — Accept WARN and proceed**: Brain explicitly downgrades the BLOCK to a WARN. `/lfe-plan-critique` updates the file in place: `verdict: WARN`, `brain_confirmation: <ISO-8601>`. The Builder's Step 1 gate now opens.
   - **C — Abort mission**: wipe `.plans/` execution files; mission cancelled.

**Crash recovery**: if the session dies between attempts, the next `/lfe-boot` finds `plan_critique.md` on disk with its `revision` field intact. Step 0 of the next `/lfe-plan-critique` invocation reads it and resumes the correct branch.

> *Rationale*: a plan that fails critique twice has a structural misalignment that repeated plan edits will not fix. The PRD or slice boundaries need to change.

### Scenario 2.3: The Domain Rescue Loop (Black Box)
*Any persona encounters undocumented, highly complex code.*
1. **Trigger**: Builder or Inspector gets confused by legacy/undocumented code.
2. **Halt**: Agent immediately halts execution.
3. **Escalate**: Persona switches to Architect and triggers `/lfe-extract-domain`.
4. **Rescue**: AI interviews the human to extract the missing rules -> Updates `CONTEXT.md`.
5. **Resume**: Agent switches back to original Persona and resumes the original task.

---

## 3. The Bypass & Maintenance Loops

### Scenario 3.1: The Minor Fix Loop (Flyweight)
*A typo, UI tweak, or CSS change touching < 3 files.*
1. **Boot**: Complexity Gate triggers. Human selects "Minor Fix".
2. **Scout**: `/lfe-scout` activates. Modifies code directly (bypassing Architect/Inspector).
3. **Close**: Scout updates `CHANGELOG.md` and increments session count. -> **[Loop Closed]**
> *Note on Crash Recovery: Because Scout intentionally bypasses the `.plans/` coordination layer, there is no file-based crash recovery for this loop. An interrupted Scout session requires a manual git reset.*

### Scenario 3.2: The Hygiene & Architecture Sweep Loop
*Preventing technical debt and documentation drift.*
1. **Trigger**: Archivist checks `pipeline_status.md` and sees "Session Count: 5".
2. **Audit**: Triggers `/lfe-hygiene` to scan the repo -> Writes `.plans/hygiene_report.md`.
3. **Refactor**: Triggers `/lfe-improve-architecture` (reads hygiene report) -> Extracts deep modules.
4. **Close**: Resets session count to 0 in `pipeline_status.md`. -> **[Loop Closed]**

### Scenario 3.3: The LFE-FORCE Emergency Loop
*Production is down. The human overrides all safety protocols.*
1. **Trigger**: Human types `LFE-FORCE`.
2. **Patch**: Agent applies hotfix directly to `src/`.
3. **Debt Logging**: Agent writes entry to `.docs/quality/PROTOCOL_DEBT.md`.
4. **The Block**: Next session starts. `/lfe-boot` detects unresolved Protocol Debt and locks the pipeline.
5. **Verify (with hotfix sub-skill audit)**: Inspector runs with the **Protocol Debt fallback** (see [`lfe-inspector/SKILL.md`](../../.agents/skills/lfe-inspector/SKILL.md) Hard Rule #4 and Step 7b): reads the latest unresolved `PROTOCOL_DEBT.md` entry, verifies the hotfix against `src/`, and runs the **LFE-FORCE sub-skill subset** (always: `lfe-security-check` + `lfe-complexity-check`; conditional: `lfe-dep-audit` on manifest changes, `lfe-perf-check` on hot-path edits; skipped: `lfe-mutation-verify`). All findings aggregate into `critique.md`. Writes `inspection_report.md` with `source: .docs/quality/PROTOCOL_DEBT.md` and the entry's `Date` + `Mission` in the body's `## Debt Entry Verified` section.
6. **Branch on outcome**:
   - **PASS** → Archivist runs **Step 3.6 (Protocol Debt Resolution)**: (a) appends any `Critical` sub-skill findings to `.docs/quality/known-issues.md` for traceability, then (b) locates the matching debt entry by `Date` + `Mission` and updates its `Resolution Status` to `resolved (session N)`. Cleanup proceeds. Next boot's Step 5 finds no unresolved debt → pipeline unlocks. ✅ *(Critical findings do NOT block PASS — the debt clears, but the issues are surfaced as known follow-ups.)*
   - **FAIL** → Inspector does NOT trigger `/lfe-diagnose` (no `active_plan.md` exists for the standard fix loop). Inspector writes `status: failed`, halts, and presents three triage options to the human:
     1. Issue another `LFE-FORCE` patch (creates a new debt entry; the old one stays open).
     2. Roll back the hotfix (revert `src/`; original entry closes as `rolled-back`).
     3. Convert to full pipeline (run `/lfe-grill-with-docs` to architect a retroactive plan, then build/test/verify normally).
   Pipeline remains blocked until the human chooses. The Archivist must NOT mark the entry resolved and does NOT append sub-skill findings to known-issues (they remain visible only in the failed `inspection_report.md` for the human to use during triage).

---

## 4. The Crash Recovery Loops (Interrupted Sessions)
*What happens if the IDE crashes, the API times out, or the laptop dies?*

| Point of Crash | State of `.plans/` | `lfe-boot` Recovery Action |
| :--- | :--- | :--- |
| **During Grill** | Empty | Restarts from scratch. |
| **After PRD** | `01`, `02` exist | Resumes at `/lfe-to-issues`. |
| **After Slices** | `01`, `02`, `03` exist | Resumes at `/lfe-architect`. |
| **After Plan** | `01`, `02`, `03`, `active_plan` exist | Resumes at `/lfe-plan-critique`. |
| **After Plan Critique (gate open)** | `plan_critique.md` exists with `verdict: PASS` OR (`verdict: WARN` AND `brain_confirmation` non-null) | Resumes at `/lfe-builder`. |
| **After Plan Critique (WARN unconfirmed)** | `plan_critique.md` exists with `verdict: WARN` AND `brain_confirmation: null` | Resumes at `/lfe-plan-critique` Step 0; Step 0 re-presents the WARN findings rather than re-running lenses. Brain decides: confirm (sets `brain_confirmation`) or revise plan. |
| **After Plan Critique (BLOCK, revision 1)** | `plan_critique.md` exists with `verdict: BLOCK` AND `revision: 1` | Resumes at `/lfe-architect` for plan revision. Next `/lfe-plan-critique` invocation will read this file in Step 0 and write the next attempt as `revision: 2`. |
| **After Plan Critique (BLOCK, revision 2)** | `plan_critique.md` exists with `verdict: BLOCK` AND `revision: 2` | Resumes at `/lfe-plan-critique` Step 0; Step 0 halts at Brain triage (Scenario 1.4) — does NOT re-run lenses. |
| **After Coding** | `active_plan`, `builder_done` exist | Resumes at `/lfe-tdd`. |
| **After TDD** | `tdd_report` exists | Resumes at `/lfe-inspector` (Cycle Guard runs first). |
| **During Sub-Skill Dispatch** | Some `.plans/checks/*.md` files exist; `critique.md` absent | Resumes at `/lfe-inspector` Step 6. Per-file resume rule: a sub-skill is skipped **only** when its findings file exists AND its frontmatter parses with `status: complete` — file presence alone is not sufficient. Sub-skills whose findings file is missing or has any other `status` are re-invoked. Then proceeds to aggregation. |
| **After Sub-Skill Dispatch (aggregation crash)** | All enabled sub-skills' findings files have `status: complete`; `critique.md` missing or partial | Resumes at `/lfe-inspector` Step 6.f — re-aggregate only. Do NOT re-run sub-skills (their outputs are reusable). |
| **During Inspect (1st failure already recorded)** | `inspection_report.md` exists with `status: failed`, `critique` exists | Resumes at `/lfe-diagnose` (Inspector has already failed once). |
| **During Inspect (2nd failure)** | `inspection_report.md` shows `status: escalated` with triage menu | Resumes by re-presenting Brain triage menu (Accept Debt / LFE-FORCE / Re-plan). |
| **During Diagnose**| `diagnosis_report` exists | Resumes at Builder to fix the bug. |
| **After Archive** | `.plans/` still full (State Anomaly) | Triggers `/lfe-zoom-out` to compare plans vs code. Asks human to manually delete `.plans/`. |