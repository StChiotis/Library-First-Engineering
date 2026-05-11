# Coordination File Contract

> **Canonical schema for `.plans/` files.** Every skill that writes a coordination file follows this contract so crash recovery and orphan-detection have a uniform target. The full lifecycle of these files is mapped in [LOOP_ARCHITECTURE.md](LOOP_ARCHITECTURE.md).

## Frontmatter (mandatory)

```yaml
---
phase: <architect | builder | inspector | archivist | hygiene>
step: <skill name without the lfe- prefix>
status: complete | failed | passed | escalated
timestamp: <ISO-8601>
source: <input path, or "n/a">
---
```

**Field notes:**
- `status`: most skills use `complete` / `failed`. Verification skills (`/lfe-inspector`) use `passed` / `failed` because the verdict — not the execution — is what downstream consumers branch on. `escalated` is reserved for `inspection_report.md` when the Cycle Guard halts on a 2nd consecutive failure and surfaces Brain triage.
- `source`: by default, the relative path inside `.plans/` (e.g., `.plans/tdd_report.md`). Cross-tier sources are allowed when a skill legitimately reads from outside `.plans/` — write the path relative to the repo root (e.g., `.docs/quality/PROTOCOL_DEBT.md` for the Inspector's LFE-FORCE recovery branch). Use `n/a` only when the skill takes no file input (e.g., `/lfe-grill-with-docs` reads conversation; `/lfe-hygiene` reads the whole repo).
- `slice` *(execution-tier files only)*: required on `active_plan.md`, `builder_done.md`, `tdd_report.md`, `inspection_report.md`, `diagnosis_report.md`. Copied from `active_plan.md` on every write. Lets `/lfe-builder` and `/lfe-hygiene` detect stale execution-tier files left over from a prior slice whose Partial Cleanup didn't complete (e.g., crash mid-cleanup). Omitted on the Inspector's LFE-FORCE recovery branch — there's no slice when there's no plan.

Skills MAY add typed fields below `source:` (e.g., `tests_passed: <N>`, `tests_failed: <N>` for `tdd_report.md`; `verdict: PASS | WARN | BLOCK` for `plan_critique.md`).

## Coordination File Registry

| File | Phase | Written by | Read by |
|---|---|---|---|
| `01_grill_summary.md` | 1 | `/lfe-grill-with-docs` | `/lfe-to-prd` |
| `02_prd.md` | 1 | `/lfe-to-prd` | `/lfe-to-issues` |
| `03_slices.md` | 1 | `/lfe-to-issues` | `/lfe-architect` |
| `active_plan.md` | 1 | `/lfe-architect` | `/lfe-plan-critique`, `/lfe-builder`, `/lfe-tdd` |
| `plan_critique.md` | 1.5 | `/lfe-plan-critique` | Brain / `/lfe-builder` (gate) |
| `builder_done.md` | 2 | `/lfe-builder` | `/lfe-tdd` (resume marker) |
| `tdd_report.md` | 2 | `/lfe-tdd` | `/lfe-inspector` |
| `critique.md` | 3 | `/lfe-inspector` (4-Eyes pass + sub-skill aggregation) | `/lfe-inspector` (self) |
| `.plans/checks/*.md` | 3 | Inspector sub-skills (`lfe-security-check`, `lfe-perf-check`, `lfe-complexity-check`, `lfe-dep-audit`, `lfe-mutation-verify`) | `/lfe-inspector` (aggregates into `critique.md`) |
| `inspection_report.md` | 3 | `/lfe-inspector` | `/lfe-archivist` |
| `diagnosis_report.md` | 3 | `/lfe-diagnose` | `/lfe-builder` (next iteration) |
| `hygiene_report.md` | 5 | `/lfe-hygiene` | `/lfe-improve-architecture` |

## Cleanup tiers

The Archivist enforces two cleanup tiers, defined in `lfe-archivist/SKILL.md`:

- **Partial Cleanup** (between slices): delete execution files — `plan_critique.md`, `active_plan.md`, `builder_done.md`, `tdd_report.md`, `critique.md`, `inspection_report.md`, `diagnosis_report.md`, and all files in `.plans/checks/`. **Keep** planning files — `01_grill_summary.md`, `02_prd.md`, `03_slices.md`.
- **Full Cleanup** (mission complete): delete every file in `.plans/` including `.plans/checks/`.

`hygiene_report.md` belongs to the Hygiene sub-pipeline. Its primary reader is the **human** (audit findings to review and triage). `/lfe-improve-architecture` may reference it for priority context but does not formally consume it — that skill walks the codebase fresh. The report is deleted by `/lfe-improve-architecture`'s final step at the end of the hygiene cycle, not by per-mission Archivist runs.

## Why this exists

Coordination files are the framework's crash-recovery substrate. If a session crashes mid-skill, the next `lfe-boot` reads `.plans/` to determine where to resume. A skill that does its work but writes nothing to disk creates a **black hole** — the work is invisible to recovery and may be silently re-executed in the next session. Every skill that produces a verifiable artifact MUST write it to a coordination file.
