# Shelf Index: archive

## Directory Mission
Cold-tier home for the framework's retention-managed files. Each archive file mirrors a hot-tier sibling in `.docs/quality/` or `.docs/architecture/`. Aged-out entries are moved here by the Hygiene sweep per the Retention Policy in [`GOVERNANCE.md`](../protocol/GOVERNANCE.md).

## File Registry

| File | Hot-tier sibling | Purpose | Status |
|---|---|---|---|
| `changelog-history.md` | [`../quality/CHANGELOG.md`](../quality/CHANGELOG.md) | Milestones older than the 7-entry rolling window. | Active |
| `architecture-decisions-history.md` | [`../architecture/architecture-decisions.md`](../architecture/architecture-decisions.md) | `superseded` / `deprecated` ADRs aged out beyond 15 sessions. | Active |
| `protocol-debt-history.md` | [`../quality/PROTOCOL_DEBT.md`](../quality/PROTOCOL_DEBT.md) | `resolved` LFE-FORCE entries one hygiene cycle after resolution. | Active |
| `known-issues-history.md` | [`../quality/known-issues.md`](../quality/known-issues.md) | `resolved` / `won't-fix` issues one hygiene cycle after closure. | Active |

> **Rule:** the cold tier is **append-only** — archived entries are never deleted; the index rows are the audit trail. Sweep procedure: [`RETENTION_RUNBOOK.md`](../quality/RETENTION_RUNBOOK.md).

> **Note to AI**: This is a local index — the project holds more files than these, though these are the only files in this specific directory. For a file not listed here, consult the Master Floor Map at `.docs/README.md`.
