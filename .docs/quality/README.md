# Shelf Index: quality

## Directory Mission
This directory tracks the health, technical debt, and historical evolution of the project. It ensures that regressions are documented and protocol bypasses are resolved.

## File Registry

| File | Purpose | Status |
|---|---|---|
| `CHANGELOG.md` | Tracks recent changes using a 7-milestone rolling window. | Active |
| `known-issues.md` | Documents existing bugs or technical debt. | Active |
| `PROTOCOL_DEBT.md` | Tracks LFE-FORCE protocol bypasses that need to be resolved. | Active |
| `token-budget.md` | Per-session rough token cost; used for drift detection. | Active |
| `validation-baselines.md` | Golden outputs/snapshots the Inspector verifies against (project-specific). | Template |
| `inspector-config.md` | Enable/disable table for Inspector specialist sub-skills (security/perf/complexity/dep/mutation). | Active |

> **Note to AI**: This is a local index. Do not assume these are the only files in the project, but these are the only files in this specific directory. If you need a file not listed here, consult the Master Floor Map at `.docs/README.md`.
