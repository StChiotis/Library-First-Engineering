---
name: lfe-hygiene
description: Conduct a structural audit of the repository to detect "Documentation Drift" or "Spaghetti Files." Use in Phase 5 (Hygiene sub-pipeline, Step 1).
---

# LFE Hygiene Audit

## Position in Pipeline
- **Phase**: 5 (Hygiene sub-pipeline, Step 1)
- **Persona**: Archivist
- **Trigger**: Scheduled every 5 sessions (tracked in `pipeline_status.md`) or manually by human
- **Next Step**: `/lfe-improve-architecture` (Step 2)

## Mission
Ensure the repository remains LFE-compliant. Detect drift between the Library and the physical filesystem, and verify the coordination layer is clean.

## Workflow

### 1. Floor Map Audit
- Read `.docs/README.md`.
- Scan the filesystem.
- **Identify "Illegal" Files**: Files that exist but are not registered in the Floor Map.
- **Identify "Ghost" Files**: Files listed in the Floor Map that no longer exist.

### 2. Coordination Layer Audit
The cleanup tiers in `lfe-archivist/SKILL.md` Step 5a/5b are the source of truth for which files should be present at each lifecycle moment. This audit checks for drift from those rules.

- **Mission complete** (`Mission State == [MISSION COMPLETE]` or `[BLANK CANVAS]`): no execution OR planning files should remain. If any of `01_grill_summary.md`, `02_prd.md`, `03_slices.md`, `plan_critique.md`, `active_plan.md`, `builder_done.md`, `tdd_report.md`, `critique.md`, `inspection_report.md`, `diagnosis_report.md` still exist, or if `.plans/checks/` contains any files → flag as "Stale Coordination Files".
- **Between slices** (`Mission State == [IN-FLIGHT: <phase>]` and Active Persona is Architect): planning files (`01_grill_summary.md`, `02_prd.md`, `03_slices.md`) MAY exist; execution files (`plan_critique.md`, `active_plan.md`, `builder_done.md`, `tdd_report.md`, `critique.md`, `inspection_report.md`, `diagnosis_report.md`, `.plans/checks/`) MUST NOT — flag any present as "Partial Cleanup Skipped".
- **Mid-mission**: `active_plan.md` exists but Active Persona is NOT Architect, plan-critique, or Builder → flag as "Orphaned Plan".
- **Plan critique checkpoint**: `plan_critique.md` should exist only between Plan Approval and Builder start. If `plan_critique.md` exists AND `builder_done.md` exists → the critique file should have been cleaned up by Partial Cleanup; flag as "Stale Plan Critique".
- **Hygiene cycle**: `hygiene_report.md` is owned by the Hygiene sub-pipeline; flag only if it persists outside an active hygiene cycle.
- Verify the `Coordination Files` row in `pipeline_status.md` matches the **mainline** files in `.plans/` (the nine checkboxes are `01 02 03 plan plan_critique build tdd critique inspect`). Conditional artifacts (`diagnosis_report.md`, `hygiene_report.md`, `.plans/checks/*.md`) intentionally have no checkbox — their presence on disk is not row-drift.

### 3. State Audit
- Verify `pipeline_status.md` reflects the current reality:
  - Active Persona matches the expected phase
  - Session Count is present and numeric
  - Last Architecture Sweep timestamp exists
  - Coordination file tracker matches filesystem
- Verify `CONTEXT.md` exists if grill-with-docs has been run (check for `01_grill_summary.md` in commit history)

### 4. History Audit
- Verify `CHANGELOG.md` respects the 7-milestone rolling window.
- Verify session count in `pipeline_status.md` is being incremented.

### 5. Skills Audit
- Verify every skill in `.agents/skills/` has a `SKILL.md` with YAML frontmatter (name + description).
- Verify no skill references a deprecated skill.
- Verify `.agents/permissions.json` maps exactly to the roles defined in `.docs/protocol/PERSONAS.md` (no documentation drift in tool gateways).
- **Sub-skill discovery (kind-based, not name-based)**: scan every `.plans/checks/*_findings.md` referenced as the output of a skill in `.agents/skills/`; identify sub-skills by their findings-file frontmatter `kind: sub-skill`. Name patterns (`-check`, `-audit`, `-verify`) are an unreliable signal — only the typed marker counts. For each discovered sub-skill, verify it is listed in `.docs/quality/inspector-config.md`. If a sub-skill exists but is absent from the config, flag as "Unconfigured Sub-Skill".

### 6. Scaling Audit
- Verify there are no directories in `.docs/` with 3+ files missing a `README.md` Shelf Index.
- Verify there are no files in `.docs/` exceeding ~6,000 characters. For any file that exceeds this threshold, check whether it has a Shelf Index header (`## Index` or similar). Flag files that are both oversized AND lack a Shelf Index as "Doc Size Violation" — these are candidates for splitting into atomic docs. Files with a Shelf Index that organise sub-entries are acceptable at larger sizes.

### 6.5 Coordination Contract Audit
The contract in [`COORDINATION_FILES.md`](../../../.docs/protocol/COORDINATION_FILES.md) defines a frontmatter schema and per-tier file lists. Drift between the contract and what skills actually do is the framework's most common silent-failure class. Two cheap mechanical checks:

- **Frontmatter conformance**: for every file currently in `.plans/`, parse the YAML frontmatter and verify required fields (`phase`, `step`, `status`, `timestamp`, `source`) are present with legal values. For execution-tier files (`active_plan.md`, `builder_done.md`, `tdd_report.md`, `inspection_report.md`, `diagnosis_report.md`), also verify `slice:` is present (unless `inspection_report.source` is `.docs/quality/PROTOCOL_DEBT.md`, in which case `slice` is legitimately absent). Flag any file with missing/illegal fields.
- **Cleanup-list parity**: the file lists in `lfe-archivist/SKILL.md` Step 5a (Partial Cleanup delete) and Step 5b (Full Cleanup delete) must match the lists in this skill's Section 2 audit. Read both skills, extract the lists, diff them. Flag any divergence — drift here means orphan-detection and cleanup are operating on different sets, which silently corrupts the multi-slice loop.

### 7. Retention Check
Walk the Retention Policy table in `.docs/protocol/GOVERNANCE.md`. For each row:
- Identify aged-out entries in the hot file per its retention rule.
- Move those entries to the corresponding cold file in `.docs/archive/`, preserving their full content and adding a row to the cold file's index table.
- Update the hot file's Cold Tier Pointer (`Last archive sweep: session N`) to the current session.
- If a hot file is missing its Cold Tier Pointer, or a cold file is missing its Hot Tier Pointer + Index, flag it as a violation.
- Honor any project overrides declared in `LLM_AGENT_GUIDE.md` under `## Retention Policy Overrides`.

## Output

Write `.plans/hygiene_report.md` per the contract in [COORDINATION_FILES.md](../../../.docs/protocol/COORDINATION_FILES.md). Findings must be persisted to a file — verbal-only output creates a black hole where findings are lost if the session ends, and the Architect cannot fix structural violations they never saw.

```yaml
---
phase: hygiene
step: hygiene
status: complete
timestamp: <ISO-8601>
source: n/a
---
```

```markdown
## 🔴 Critical (blocks next session)
- <violations>

## 🟡 Warning (should be fixed soon)
- <violations>

## 🟢 Clean
- <areas with no drift>

## Recommendation
- Proceed to `/lfe-improve-architecture` for architecture sweep? (Yes/No)
```

If fully clean, the report still gets written — body reads `Repo is LFE-Compliant. Zero Drift Detected.` Critical findings should be reviewed and triaged by the human before further refactoring; the framework surfaces the findings but does not block work — when to act on them is a human judgment call (typical defer reason: mid-mission, hold until completion).
