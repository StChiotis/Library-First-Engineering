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
1. **Read Report**: Read `.plans/inspection_report.md` to understand what was built and verified.
2. **Sync Docs**: You MUST explicitly apply updates depending on the changes made:
   - **New domain terms or canonical definitions**: You MUST append them to `CONTEXT.md` and `.docs/domain/glossary.md`.
   - **New math or business rules**: You MUST record the exact formulas in `.docs/domain/domain-knowledge.md`.
   - **New coding standards or conventions**: You MUST add them to `.docs/architecture/engineering-standards.md`.
   - **New architectural/design decisions**: You MUST record them in `.docs/architecture/architecture-decisions.md`.
   - **New project files/modules added**: Update the Floor Map in `.docs/README.md` to tell the AI where to find them. If a directory reaches 3 or more files, generate a `README.md` Shelf Index for it using `.docs/protocol/SHELF_INDEX_TEMPLATE.md`.
3. **Update History**: Prepend the new milestone to `CHANGELOG.md` in `.docs/quality/` (or root). Enforce the 7-milestone rolling window. Include test coverage and success metrics.
3.5. **Update Token Budget**: Append the session's rough token cost (best-effort estimate from chat metadata or model self-report) to `.docs/quality/token-budget.md` "Recent sessions" table. After updating, recompute rolling averages in the "Rolling baselines" table. If any phase's last-session delta exceeds +50% over rolling avg, add a ⚠️ flag with a one-line note to the next session's `pipeline_status.md`.
4. **Slice Loop Check**: Are there more slices in `.plans/03_slices.md`?
   - **Yes**: Update `pipeline_status.md` to next slice, set persona back to Architect. Do NOT clean up coordination files yet.
   - **No**: Proceed to cleanup.
5. **Clean Up**: Archive or delete coordination files from `.plans/` (01, 02, 03, active_plan, tdd_report, critique, inspection_report). Clear `active_plan.md` if not deleted.
6. **Update Pipeline Status**: Update the entrance card (Mission State, Last ADR, Mission, Session Count) for the next session. The legal `Mission State` values are `[BLANK CANVAS]`, `[DOMAIN LOADED]`, `[IN-FLIGHT: <phase>]`, `[MISSION COMPLETE]`.
7. **Hygiene Check**: Read `Last Architecture Sweep` from `pipeline_status.md`. If 5+ sessions since last sweep, flag it:
   > *"Architecture sweep is due (5+ sessions). Run `/lfe-improve-architecture`?"*

## Checklist
- [ ] CHANGELOG has exactly 7 or fewer milestones?
- [ ] ADR and Floor Map indices updated?
- [ ] Unit test coverage and success percentage recorded?
- [ ] Pipeline status reflects latest session?
- [ ] Coordination files archived/cleared (if mission complete)?
- [ ] Session count incremented?
- [ ] Architecture sweep due date checked?

