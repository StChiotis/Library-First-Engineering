# Documentation Floor Map (LFE Template)

> **LLM instruction:** Read this file immediately after `pipeline_status.md`. It tells you which file answers which question. Do not open files speculatively.

## 🧱 The Core Philosophy
> *"Thinking in the Human. Processing in the AI. Truth in the Documentation."*

- **`.docs/` (The Library)**: The absolute Source of Truth for this Documentation-as-Infrastructure framework. This folder contains only permanent, verified project knowledge.
- **`.plans/` (The Coordination Layer)**: The workspace for file-based coordination between pipeline steps. Contains numbered coordination files (`01_grill_summary.md`, `02_prd.md`, `03_slices.md`) and transient analysis documents. Files are created by skills and deleted by the Archivist when the mission is complete.

---

## How this library is organised

| Layer | File | Purpose |
|---|---|---|
| Entrance card | `pipeline_status.md` | Current session state, active mission, coordination file tracker, session count. |
| Agent Guide | `LLM_AGENT_GUIDE.md` | Core instructions for any AI entering this repo (V2: skill catalog, coordination table). |
| Adapters | `.cursorrules`, `.windsurfrules`, `.clinerules`, `.antigravityrules` | IDE-specific protocol enforcement files. |
| Copilot | `.github/copilot-instructions.md` | GitHub Copilot adapter. |
| System Prompt | `.agents/adapters/system_prompt.txt` | Raw LLM adapter (copy-paste for ChatGPT, Claude, etc.). |
| Floor map | **this file** | Navigation index. |
| Agent Core | `.agents/skills/` | Home for 17 LFE persona skills (V2 sub-pipeline architecture). |
| Domain Language | `CONTEXT.md` (root) | Canonical glossary — all agents must use these terms. |
| Architecture Decisions | `docs/adr/` | Architectural Decision Records. |
| Coordination | `.plans/` | Transaction log for pipeline steps (see below). |

---

## Coordination Layer (`.plans/`)

The V2 pipeline uses file-based coordination. Each skill writes output to `.plans/`, and the next skill reads it as input.

| File | Written by | Read by |
|---|---|---|
| `01_grill_summary.md` | `/lfe-grill-with-docs` | `/lfe-to-prd` |
| `02_prd.md` | `/lfe-to-prd` | `/lfe-to-issues` |
| `03_slices.md` | `/lfe-to-issues` | `/lfe-architect` |
| `active_plan.md` | `/lfe-architect` | `/lfe-builder`, `/lfe-tdd` |
| `tdd_report.md` | `/lfe-tdd` | `/lfe-inspector` |
| `inspection_report.md` | `/lfe-inspector` | `/lfe-archivist` |

---

## Protocol (`protocol/`)

| File | Answers the question… |
|---|---|
| `ASSEMBLY_LINE.md` | How do agents hand off work? (V2 sub-pipelines + coordination layer) |
| `GOVERNANCE.md` | What are the rules for Logic Sovereignty, Domain Language, and coordination files? |
| `PERSONAS.md` | What are the constraints, tools, and sub-pipeline skills for each AI role? |
| `INDUSTRY_STANDARDS.md` | What optional CI/CD enhancements are available? |

---

## Architecture (`architecture/`)

| File | Answers the question… |
|---|---|
| `architecture-decisions.md` | What architectural decisions govern this codebase? (ADR Index) |
| `engineering-standards.md` | What are the coding and design conventions? |

---

## Domain (`domain/`)

| File | Answers the question… |
|---|---|
| `domain-knowledge.md` | What is the authoritative logic and math specification? |
| `glossary.md` | What are the canonical terms used in this project? |

> **Note:** `CONTEXT.md` (at repo root) is the primary domain glossary maintained by `/lfe-grill-with-docs`. `glossary.md` may be used for extended domain reference.

---

## Strategy (`strategy/`)

| File | Answers the question… |
|---|---|
| `roadmap.md` | What is the planned evolution of the project? |

---

## Quality (`quality/`)

| File | Answers the question… |
|---|---|
| `CHANGELOG.md` | What changed recently? (7-milestone rolling window) |
| `known-issues.md` | What bugs or technical debt currently exist? |
| `PROTOCOL_DEBT.md` | What LFE-FORCE protocol bypasses need to be resolved? |

---

## Legal (`legal/`)

| File | Answers the question… |
|---|---|
| `privacy-policy.md` | How is data handled? |

---

*Maintained by the Archivist role.*
