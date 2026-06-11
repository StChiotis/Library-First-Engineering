# Documentation Floor Map (LFE Template)

> **LLM instruction:** Read this file immediately after `pipeline_status.md`. It tells you which file answers which question. Do not open files speculatively.

## 🧱 The Core Philosophy
> *"Thinking in the Human. Processing in the AI. Truth in the Documentation."*

- **`.docs/` (The Library)**: The absolute Source of Truth for this Documentation-as-Infrastructure framework. This folder contains only permanent, verified project knowledge.
- **`.plans/` (The Coordination Layer)**: The workspace for file-based coordination between pipeline steps. Contains numbered coordination files (`01_grill_summary.md`, `02_prd.md`, `03_slices.md`) and transient analysis documents. Files are created by skills and deleted by the Archivist when the mission is complete.

---

## How this library is organized

| Layer | File | Purpose |
|---|---|---|
| Entrance card | [`pipeline_status.md`](../pipeline_status.md) | Current session state, active mission, coordination file tracker, session count. |
| User Manual | [`USER_MANUAL.md`](../USER_MANUAL.md) | The human guide: how to drive the framework day-to-day. |
| Agent Guide | [`LLM_AGENT_GUIDE.md`](../LLM_AGENT_GUIDE.md) | Core instructions for any AI entering this repo (skill catalog, coordination table). |
| Adapters | [`.cursorrules`](../.cursorrules), [`.windsurfrules`](../.windsurfrules), [`.clinerules`](../.clinerules), [`.antigravityrules`](../.antigravityrules) | IDE-specific pointer stubs that reference the canonical [`LLM_AGENT_GUIDE.md`](../LLM_AGENT_GUIDE.md) — to change agent behavior, edit the canonical file (the stubs only point). Claude Code additionally gets native `/lfe-*` dispatch via pointer stubs in `.claude/commands/` — also pointer-only; the canonical skills live in `.agents/skills/`. |
| Copilot | [`.github/copilot-instructions.md`](../.github/copilot-instructions.md) | GitHub Copilot adapter. |
| System Prompt | [`.agents/adapters/system_prompt.txt`](../.agents/adapters/system_prompt.txt) | Raw LLM adapter (copy-paste for ChatGPT, Claude, etc.). |
| Change History | [`.docs/archive/`](./archive/) (see [Shelf Index](./archive/README.md)) | Cold-tier archives for retention-managed files: `changelog-history.md`, `architecture-decisions-history.md`, `protocol-debt-history.md`, `known-issues-history.md`. Populated by the Hygiene sweep per the Retention Policy in [`GOVERNANCE.md`](./protocol/GOVERNANCE.md). |
| Floor map | **this file** | Navigation index. |
| Agent Core | [`.agents/skills/`](../.agents/skills/) | Home for 22 LFE persona skills (sub-pipeline architecture + 5 specialist Inspector sub-skills + `lfe-plan-critique`). |
| Domain Language | [`CONTEXT.md`](../CONTEXT.md) (root) | Canonical glossary — all agents must use these terms. |
| Framework Roadmap | [GitHub Issues](https://github.com/StChiotis/Library-First-Engineering/issues) | Planned improvements for the LFE framework (tracked as Issues). |
| Project Roadmap | [`.docs/strategy/roadmap.md`](./strategy/roadmap.md) | What is the planned evolution of the product? |
| Architecture Decisions | [`.docs/architecture/`](./architecture/) | Architectural Decision Records. |
| Coordination | [`.plans/`](../.plans/) | Transaction log for pipeline steps (see below). |
| Legal & License | [`LICENSE`](../LICENSE), [`CONTRIBUTING.md`](../CONTRIBUTING.md) | Project legal and contribution guidelines. |
| Root Readme | [`README.md`](../README.md) | High-level project overview. |

---

## Coordination Layer (`.plans/`)

The pipeline uses file-based coordination. Each skill writes output to `.plans/`, and the next skill reads it as input. The full schema and registry live in [`COORDINATION_FILES.md`](./protocol/COORDINATION_FILES.md).

| File | Written by | Read by |
|---|---|---|
| `01_grill_summary.md` | `/lfe-grill-with-docs` | `/lfe-to-prd` |
| `02_prd.md` | `/lfe-to-prd` | `/lfe-to-issues` |
| `03_slices.md` | `/lfe-to-issues` | `/lfe-architect` |
| `active_plan.md` | `/lfe-architect` | `/lfe-plan-critique`, `/lfe-builder`, `/lfe-tdd` |
| `plan_critique.md` | `/lfe-plan-critique` | Brain / `/lfe-builder` (auto-gate) |
| `builder_done.md` | `/lfe-builder` | `/lfe-tdd` (resume marker) |
| `tdd_report.md` | `/lfe-tdd` | `/lfe-inspector` |
| `.plans/checks/*.md` | Inspector sub-skills (security/perf/complexity/dep/mutation) | `/lfe-inspector` (aggregates into `critique.md`) |
| `inspection_report.md` | `/lfe-inspector` | `/lfe-archivist` |
| `diagnosis_report.md` | `/lfe-diagnose` (conditional) | `/lfe-builder` (next iteration) |
| `hygiene_report.md` | `/lfe-hygiene` (every 5 sessions) | `/lfe-improve-architecture` |

---

## Protocol (`protocol/`)

| File | Answers the question… |
|---|---|
| [`ASSEMBLY_LINE.md`](./protocol/ASSEMBLY_LINE.md) | How do agents hand off work? (sub-pipelines + coordination layer) |
| [`COORDINATION_FILES.md`](./protocol/COORDINATION_FILES.md) | What is the frontmatter schema and registry for `.plans/` files? |
| [`GOVERNANCE.md`](./protocol/GOVERNANCE.md) | What are the rules for Logic Sovereignty, Domain Language, and coordination files? |
| [`PERSONAS.md`](./protocol/PERSONAS.md) | Persona index — links to individual contracts. |
| [`personas/`](./protocol/personas/README.md) | Individual persona contracts (one file per role). |
| [`INDUSTRY_STANDARDS.md`](./protocol/INDUSTRY_STANDARDS.md) | What optional CI/CD enhancements are available? |
| [`LOOP_ARCHITECTURE.md`](./protocol/LOOP_ARCHITECTURE.md) | How does the framework mechanically handle loops, crashes, and overrides? |
| [`SHELF_INDEX_TEMPLATE.md`](./protocol/SHELF_INDEX_TEMPLATE.md) | Standardized template for local directory indexes |
| [`README.md`](./protocol/README.md) | **Shelf Index**: Local navigation for the protocol layer. |

---

## Architecture (`architecture/`)

| File | Answers the question… |
|---|---|
| [`architecture-decisions.md`](./architecture/architecture-decisions.md) | **Your product's** architecture decisions (ADR 1+). Empty on a fresh clone. |
| [`engineering-standards.md`](./architecture/engineering-standards.md) | What are the coding and design conventions? |
| [`README.md`](./architecture/README.md) | **Shelf Index**: Local navigation for the architecture layer. |

---

## Domain (`domain/`)

| File | Answers the question… |
|---|---|
| [`domain-knowledge.md`](./domain/domain-knowledge.md) | What is the authoritative logic and math specification? |
| [`glossary.md`](./domain/glossary.md) | What are the canonical terms used in this project? |
| [`CONTEXT-MAP.md`](./domain/CONTEXT-MAP.md) | When to populate? Only if your project has multiple bounded contexts. Otherwise, root `CONTEXT.md` is sufficient. Format guidance is in the file itself. |
| [`README.md`](./domain/README.md) | **Shelf Index**: Local navigation for the domain layer. |

> **Note:** `CONTEXT.md` (at repo root) is the primary domain glossary maintained by `/lfe-grill-with-docs`. `glossary.md` may be used for extended domain reference.

---

## Strategy (`strategy/`)

| File | Answers the question… |
|---|---|
| [`roadmap.md`](./strategy/roadmap.md) | What is the planned evolution of the project? |

---

## Quality (`quality/`)

| File | Answers the question… |
|---|---|
| [`CHANGELOG.md`](./quality/CHANGELOG.md) | What changed recently? (7-milestone rolling window) |
| [`known-issues.md`](./quality/known-issues.md) | What bugs or technical debt currently exist? |
| [`PROTOCOL_DEBT.md`](./quality/PROTOCOL_DEBT.md) | What LFE-FORCE protocol bypasses need to be resolved? |
| [`validation-baselines.md`](./quality/validation-baselines.md) | What outputs/snapshots is the Inspector verifying against? |
| [`inspector-config.md`](./quality/inspector-config.md) | Which Inspector specialist sub-skills are enabled for this project? (incl. the Security Floor Rules) |
| [`RETENTION_RUNBOOK.md`](./quality/RETENTION_RUNBOOK.md) | How do I run a Hygiene retention sweep? (operational supplement) |
| [`README.md`](./quality/README.md) | **Shelf Index**: Local navigation for the quality layer. |

---

## Legal (`legal/`)

| File | Answers the question… |
|---|---|
| [`privacy-policy.md`](./legal/privacy-policy.md) | How is data handled? |

---

*Maintained by the Archivist role. Update this file whenever a new doc is created or a file is moved or archived; the retention sweep (every 5 sessions) walks the cold tier.*
