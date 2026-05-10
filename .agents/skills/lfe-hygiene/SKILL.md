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
- Check `.plans/` for orphaned coordination files:
  - If mission is marked complete in `pipeline_status.md` but coordination files (`01_grill_summary.md`, `02_prd.md`, `03_slices.md`, `tdd_report.md`, `critique.md`, `inspection_report.md`) still exist → flag as "Stale Coordination Files"
  - If `active_plan.md` exists but Active Persona is NOT Architect or Builder → flag as "Orphaned Plan"
- Verify the `Coordination Files` row in `pipeline_status.md` matches the actual files present in `.plans/`

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

### 6. Scaling Audit
- Verify there are no directories in `.docs/` with 3+ files missing a `README.md` Shelf Index.
- Verify there are no files in `.docs/` exceeding ~6,000 characters.

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

If fully clean, the report still gets written — body reads `Repo is LFE-Compliant. Zero Drift Detected.` Critical findings MUST be resolved by the Architect before any further refactoring.
