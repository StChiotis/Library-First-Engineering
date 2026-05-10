# Coordination File Contract

> **Canonical schema for `.plans/` files.** Every skill that writes a coordination file follows this contract so crash recovery and orphan-detection have a uniform target. The full lifecycle of these files is mapped in [LOOP_ARCHITECTURE.md](LOOP_ARCHITECTURE.md).

## Frontmatter (mandatory)

```yaml
---
phase: <architect | builder | inspector | archivist | hygiene>
step: <skill name without the lfe- prefix>
status: complete | failed
timestamp: <ISO-8601>
source: <input file relative to .plans/, or "n/a">
---
```

Skills MAY add typed fields below `source:` (e.g., `tests_passed: <N>`, `tests_failed: <N>` for `tdd_report.md`).

## Coordination File Registry

| File | Phase | Written by | Read by |
|---|---|---|---|
| `01_grill_summary.md` | 1 | `/lfe-grill-with-docs` | `/lfe-to-prd` |
| `02_prd.md` | 1 | `/lfe-to-prd` | `/lfe-to-issues` |
| `03_slices.md` | 1 | `/lfe-to-issues` | `/lfe-architect` |
| `active_plan.md` | 1 | `/lfe-architect` | `/lfe-builder`, `/lfe-tdd` |
| `builder_done.md` | 2 | `/lfe-builder` | `/lfe-tdd` (resume marker) |
| `tdd_report.md` | 2 | `/lfe-tdd` | `/lfe-inspector` |
| `critique.md` | 3 | `/lfe-inspector` (4-Eyes pass) | `/lfe-inspector` (self) |
| `inspection_report.md` | 3 | `/lfe-inspector` | `/lfe-archivist` |
| `diagnosis_report.md` | 3 | `/lfe-diagnose` | `/lfe-builder` (next iteration) |
| `hygiene_report.md` | 5 | `/lfe-hygiene` | `/lfe-improve-architecture` |

## Cleanup tiers

The Archivist enforces two cleanup tiers, defined in `lfe-archivist/SKILL.md`:

- **Partial Cleanup** (between slices): delete execution files — `active_plan.md`, `builder_done.md`, `tdd_report.md`, `critique.md`, `inspection_report.md`, `diagnosis_report.md`. **Keep** planning files — `01_grill_summary.md`, `02_prd.md`, `03_slices.md`.
- **Full Cleanup** (mission complete): delete every file in `.plans/`.

`hygiene_report.md` belongs to the Hygiene sub-pipeline and is consumed by `/lfe-improve-architecture`, then cleared at end of the hygiene cycle — not by per-mission Archivist runs.

## Why this exists

Coordination files are the framework's crash-recovery substrate. If a session crashes mid-skill, the next `lfe-boot` reads `.plans/` to determine where to resume. A skill that does its work but writes nothing to disk creates a **black hole** — the work is invisible to recovery and may be silently re-executed in the next session. Every skill that produces a verifiable artifact MUST write it to a coordination file.
