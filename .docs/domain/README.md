# Shelf Index: domain

## Directory Mission
Domain-layer documentation. Holds the project's authoritative logic/math specification, the extended glossary, and (for projects with multiple bounded contexts) a context map. The root [`CONTEXT.md`](../../CONTEXT.md) remains the primary canonical glossary maintained by `/lfe-grill-with-docs`; files in this directory provide the deeper reference layer beneath it.

## File Registry

| File | Purpose | Status |
|---|---|---|
| `domain-knowledge.md` | Authoritative logic, math, and business-rule specification — the Source of Truth for core domain rules. | Active |
| `glossary.md` | Extended domain glossary (beyond the canonical terms in root `CONTEXT.md`). | Active |
| `CONTEXT-MAP.md` | Multi-context map. Lazy-state template; populate only if the project has multiple bounded contexts. Single-context projects use root `CONTEXT.md` and ignore this file. | Active |

> **Note:** Root `CONTEXT.md` is the primary glossary per `CONTEXT-FORMAT.md`. This directory holds the extended domain reference (`glossary.md`), the canonical logic/math doc (`domain-knowledge.md`), and the multi-context map (`CONTEXT-MAP.md`, lazy).

> **Note to AI**: This is a local index. Do not assume these are the only files in the project, but these are the only files in this specific directory. If you need a file not listed here, consult the Master Floor Map at `.docs/README.md`.
