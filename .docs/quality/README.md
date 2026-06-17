# Shelf Index: quality

## Directory Mission
This directory tracks the health, technical debt, and historical evolution of the project. It ensures that regressions are documented and protocol bypasses are resolved.

## File Registry

| File | Purpose | Status |
|---|---|---|
| `CHANGELOG.md` | Tracks recent changes using a 7-milestone rolling window. | Active |
| `known-issues.md` | Documents existing bugs or technical debt. | Active |
| `PROTOCOL_DEBT.md` | Tracks LFE-FORCE protocol bypasses that need to be resolved. | Active |
| `validation-baselines.md` | Golden outputs/snapshots the Inspector verifies against (project-specific). | Template |
| `inspector-config.md` | Enable/disable table for Inspector specialist sub-skills (security/perf/complexity/dep/mutation/visual) + the Security Floor and Visual Floor Rules. | Active |
| `RETENTION_RUNBOOK.md` | Step-by-step procedure for the Hygiene retention sweep across the retention-managed files. Operational supplement to `lfe-hygiene` § 7. | Active |

> **Note to AI**: This is a local index — the project holds more files than these, though these are the only files in this specific directory. For a file not listed here, consult the Master Floor Map at `.docs/README.md`.
