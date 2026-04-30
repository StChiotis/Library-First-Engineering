# Documentation Floor Map (LFE Template)

> **LLM instruction:** Read this file immediately after `pipeline_status.md`. It tells you which file answers which question. Do not open files speculatively.

---

## How this library is organised

| Layer | File | Purpose |
|---|---|---|
| Entrance card | `pipeline_status.md` | Current session state and active mission. |
| Agent Guide | `LLM_AGENT_GUIDE.md` | Core instructions for any AI entering this repo. |
| Adapters | `.cursorrules`, etc. | IDE-specific protocol enforcement files. |
| Floor map | **this file** | Navigation index. |
| Shelf indexes | (Optional) | Within-file indices for large docs. |

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
