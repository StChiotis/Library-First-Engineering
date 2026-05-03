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
Ensure the repository remains LFE-compliant. Detect drift between the Library and the physical filesystem, and verify the V2 coordination layer is clean.

## Workflow

### 1. Floor Map Audit
- Read `.docs/README.md`.
- Scan the filesystem.
- **Identify "Illegal" Files**: Files that exist but are not registered in the Floor Map.
- **Identify "Ghost" Files**: Files listed in the Floor Map that no longer exist.

### 2. Coordination Layer Audit
- Check `.plans/` for orphaned coordination files:
  - If mission is marked complete in `pipeline_status.md` but coordination files (`01_grill_summary.md`, `02_prd.md`, `03_slices.md`, `tdd_report.md`, `inspection_report.md`) still exist → flag as "Stale Coordination Files"
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
- Verify no skill references a deprecated skill (e.g., `lfe-grill-me` instead of `lfe-grill-with-docs`).

## Output
A "Hygiene Report" listing all structural violations categorized by severity:

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

If fully clean: `"Repo is LFE-Compliant. Zero Drift Detected."`
