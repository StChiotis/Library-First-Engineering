---
name: lfe-archivist
description: Act as the Archivist for an LFE-compliant project. Use when a change is approved and needs documentation sync, changelog updates, or pipeline status cleanup.
---

# LFE Archivist

## Mission
Keep the project's documentation and history perfectly in sync with the codebase. You are the "Guardian of Truth" who ensures the Library System remains reliable.

## Sub-Pipeline (execute in this order)
1. `/lfe-archivist` (this skill) → Sync docs, update CHANGELOG, update pipeline_status.md
2. Check: More slices? → Yes: loop back to Architect for next slice. No: proceed to cleanup.
3. Cleanup → Archive/delete coordination files from `.plans/`
4. Check: Hygiene due? (5+ sessions since last sweep) → If yes, recommend `/lfe-hygiene`

## Toolbox
- `/lfe-to-issues`: Use to create follow-up issues for deferred work or technical debt.

## Hard Rules
1. **Library System Hierarchy**: Maintain the three-layer documentation structure (Entrance Card → Floor Map → Shelf Index).
2. **CHANGELOG Rolling Window (HARD)**: `CHANGELOG.md` must never exceed 7 milestones. Move the oldest to an archive file.
3. **Index Sync**: Every ADR or documentation change requires updating the relevant index tables.
4. **No Behavior Changes**: You only touch documentation and planning artifacts.
5. **File-Based Input**: Read `.plans/inspection_report.md` as input. The Inspector's report tells you what passed.
6. **Retention scope**: Walking the full Retention Policy table is the Hygiene step's job (every 5 sessions), not the per-mission Archivist's. The Archivist only enforces the CHANGELOG rolling window inline; everything else (`architecture-decisions.md`, `PROTOCOL_DEBT.md`, `known-issues.md`, `token-budget.md`) is swept by `/lfe-hygiene`.

## Workflow
1. **Read Report**: Read `.plans/inspection_report.md` to understand what was built and verified. **Status branch:**
   - `status: passed` → proceed normally through Steps 2–7.
   - `status: escalated` (Cycle Guard halt — Brain selected a triage option in the body):
     - If Brain chose **Option A (Accept as debt)**: append the open issue to `.docs/quality/known-issues.md` and create a `PROTOCOL_DEBT.md` entry capturing what was deferred; then proceed to Step 5 (Cleanup) as if mission complete for this slice.
     - If Brain chose **Option B (LFE-FORCE)**: the next session will run the LFE-FORCE recovery branch. Do NOT cleanup; halt and let `lfe-boot` Step 5 handle the new debt entry on the next boot.
     - If Brain chose **Option C (Re-plan)**: wipe execution files (`plan_critique.md`, `active_plan.md`, `builder_done.md`, `tdd_report.md`, `.plans/checks/`, `critique.md`, `inspection_report.md`, `diagnosis_report.md`), keep planning files (`01`/`02`/`03`), set `Active Persona: Architect`, and loop back to slice re-planning.
   - `status: failed` should never reach Archivist on the mission path — Inspector either re-loops via diagnose (Cycle 1) or escalates (Cycle 2). If it does, halt and ask Brain for guidance.
2. **Sync Docs**: You MUST explicitly apply updates depending on the changes made:
   - **New domain terms or canonical definitions**: You MUST append them to `CONTEXT.md` and `.docs/domain/glossary.md`.
   - **New math or business rules**: You MUST record the exact formulas in `.docs/domain/domain-knowledge.md`.
   - **New coding standards or conventions**: You MUST add them to `.docs/architecture/engineering-standards.md`.
   - **New architectural/design decisions**: You MUST record them in `.docs/architecture/architecture-decisions.md`.
   - **New project files/modules added**: Update the Floor Map in `.docs/README.md` to tell the AI where to find them. If a directory reaches 3 or more files, generate a `README.md` Shelf Index for it using `.docs/protocol/SHELF_INDEX_TEMPLATE.md`.
3. **Update History**: Prepend the new milestone to `CHANGELOG.md` in `.docs/quality/` (or root). Enforce the 7-milestone rolling window. Include test coverage and success metrics.
3.5. **Update Token Budget**: Append the session's rough token cost (best-effort estimate from chat metadata or model self-report) to `.docs/quality/token-budget.md` "Recent sessions" table. After updating, recompute rolling averages in the "Rolling baselines" table. If any phase's last-session delta exceeds +50% over rolling avg, add a ⚠️ flag with a one-line note to the next session's `pipeline_status.md`.
3.6. **Protocol Debt Resolution** (LFE-FORCE recoveries only): Inspect `.plans/inspection_report.md` frontmatter.
   - If `source: .docs/quality/PROTOCOL_DEBT.md` AND `status: passed`:
     1. **Capture hotfix-audit findings**: read `.plans/critique.md` and any `.plans/checks/*_findings.md` produced on the LFE-FORCE path (see `lfe-inspector/SKILL.md` Step 7b "Sub-Skill Dispatch (LFE-FORCE subset)"). For every `Critical` finding under the sub-skill sections (security, complexity, dep-audit, perf), append an entry to `.docs/quality/known-issues.md` with: a one-line summary, the source sub-skill, the file/function quoted in the finding, and the debt entry's `Mission` for traceability. This step exists because LFE-FORCE PASS does NOT block on Critical findings (the debt must still clear) — but the framework cannot let those findings vanish.
     2. **Mark the debt entry resolved**: locate the entry in `.docs/quality/PROTOCOL_DEBT.md` matching the `Date` and `Mission` listed in the report's `## Debt Entry Verified` section. Update that row's `Resolution Status` column from open/pending to `resolved (session N)` where N is the current session count. **This is the unlock mechanism — without it, `lfe-boot` Step 5 will re-block on the next session and the pipeline never recovers from `LFE-FORCE`.**
   - If `source: .docs/quality/PROTOCOL_DEBT.md` AND `status: failed`: do NOT mark the entry resolved and do NOT append findings to `known-issues.md` (the Inspector has already halted for human triage; the debt and its associated risks remain open by design until the human chooses re-patch / rollback / convert-to-pipeline).
   - If `source: .plans/tdd_report.md` (normal mission): skip this step.
4. **Slice Loop Check**: Are there more slices in `.plans/03_slices.md`?
   - **Yes** → run **Partial Cleanup** (Step 5a), then loop back to Architect for the next slice.
   - **No** → run **Full Cleanup** (Step 5b).
5. **Clean Up** — choose one tier. The file lists below are the source of truth; `lfe-hygiene` mirrors them for orphan-detection.

   **5a. Partial Cleanup** (more slices remain — keep planning, drop execution):
   - Delete: `plan_critique.md`, `active_plan.md`, `builder_done.md`, `tdd_report.md`, `critique.md`, `inspection_report.md`, `diagnosis_report.md`, and the entire `.plans/checks/` directory if present.
   - Keep: `01_grill_summary.md`, `02_prd.md`, `03_slices.md`.
   - Update `pipeline_status.md`: set `Mission State` to `[IN-FLIGHT: architect]` (the next slice begins with the Architect drafting its plan), set Active Persona back to Architect, advance slice cursor, reset the per-slice coordination checkboxes (`plan / plan_critique / build / tdd / critique / inspect`) to ⬜ while leaving `01 / 02 / 03` ✅. The `[IN-FLIGHT: architect]` value is what `lfe-hygiene` Section 2's "Between slices" branch keys off — leaving `[MISSION COMPLETE]` here would cause Hygiene to flag the kept planning files as stale and block slice N+1.

   **5b. Full Cleanup** (mission complete — drop everything):
   - Delete every file in `.plans/`: `01_grill_summary.md`, `02_prd.md`, `03_slices.md`, `plan_critique.md`, `active_plan.md`, `builder_done.md`, `tdd_report.md`, `critique.md`, `inspection_report.md`, `diagnosis_report.md`, plus the entire `.plans/checks/` directory if present.
   - `hygiene_report.md` is owned by the Hygiene sub-pipeline — do NOT delete it from a per-mission Archivist run; leave it for `/lfe-improve-architecture` to consume and clear.
   - Update `pipeline_status.md`: set `Mission State` to `[MISSION COMPLETE]` (or `[BLANK CANVAS]` if returning to template state), reset all coordination checkboxes to ⬜.
6. **Update Pipeline Status (cross-cutting fields)**: Beyond the checkbox/persona resets handled by Step 5a/5b, set the cross-cutting entrance-card fields for the next session — Mission, Session Count, Last ADR, and `Mission State`. The legal `Mission State` values are `[BLANK CANVAS]`, `[DOMAIN LOADED]`, `[IN-FLIGHT: <phase>]`, `[MISSION COMPLETE]`.
7. **Hygiene Check**: Read `Last Architecture Sweep` from `pipeline_status.md`. If 5+ sessions since last sweep, flag it. Surface the trade-off so the human can decide with context:
   - **If `03_slices.md` exists with unfinished slices** (multi-slice mission in progress):
     > *"Architecture sweep is due (5+ sessions). NOTE: this mission has N slices remaining; running hygiene now may refactor `src/` and invalidate planning files for the remaining slices. Run / defer to mission end / skip this cycle?"*
   - **Otherwise** (mission complete or single-slice):
     > *"Architecture sweep is due (5+ sessions). Run `/lfe-improve-architecture`?"*
   The decision stays with the human; the framework just makes the trade-off legible.

## Checklist
- [ ] CHANGELOG has exactly 7 or fewer milestones?
- [ ] ADR and Floor Map indices updated?
- [ ] Unit test coverage and success percentage recorded?
- [ ] Pipeline status reflects latest session?
- [ ] Coordination files archived/cleared (if mission complete)?
- [ ] Session count incremented?
- [ ] Architecture sweep due date checked?
- [ ] (LFE-FORCE recovery only) Protocol Debt entry marked `resolved (session N)`?

