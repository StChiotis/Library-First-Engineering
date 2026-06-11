# Shelf Index: domain

## Directory Mission
Home for this project's **domain Source of Truth** — the mathematics, business rules, and canonical terminology that core logic must derive from (per the Logic Centralization rules in [`GOVERNANCE.md`](../protocol/GOVERNANCE.md)). No agent improvises domain logic; it must trace back to the files here. The root [`CONTEXT.md`](../../CONTEXT.md) remains the primary canonical glossary maintained by `/lfe-grill-with-docs`; the files in this directory ship as templates and become your project's deeper reference layer at Day-0.

## File Registry

| File | Purpose | Status |
|---|---|---|
| `domain-knowledge.md` | Authoritative logic, math, and business-rule specification — the Source of Truth for core domain rules. | Active |
| `glossary.md` | Extended domain glossary (beyond the canonical terms in root `CONTEXT.md`). | Active |
| `CONTEXT-MAP.md` | Multi-context map. Lazy-state template; populate only if the project has multiple bounded contexts. Single-context projects use root `CONTEXT.md` and ignore this file. | Active |

> **Note:** Root `CONTEXT.md` is the primary glossary per `CONTEXT-FORMAT.md`. This directory holds the extended domain reference (`glossary.md`), the canonical logic/math doc (`domain-knowledge.md`), and the multi-context map (`CONTEXT-MAP.md`, lazy).

> **Note to AI**: This is a local index — the project holds more files than these, though these are the only files in this specific directory. For a file not listed here, consult the Master Floor Map at `.docs/README.md`.
