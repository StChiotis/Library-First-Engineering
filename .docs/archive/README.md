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
| `token-budget-history.md` | [`../quality/token-budget.md`](../quality/token-budget.md) | Per-session token budget records aged out beyond the 15-session rolling window. | Active |

> **Note to AI**: This is a local index. Do not assume these are the only files in the project, but these are the only files in this specific directory. If you need a file not listed here, consult the Master Floor Map at `.docs/README.md`.
