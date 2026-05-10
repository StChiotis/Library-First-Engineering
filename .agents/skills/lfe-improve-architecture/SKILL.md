---
name: lfe-improve-architecture
description: Find deepening opportunities in the codebase — turn shallow modules into deep ones. Use in Phase 5 (Hygiene sub-pipeline) or when scheduled by session count.
---

# LFE Improve Architecture

## Position in Pipeline
- **Phase**: 5 (Hygiene sub-pipeline, Step 2)
- **Persona**: Architect
- **Trigger**: Scheduled every 5 sessions (tracked in `pipeline_status.md`)

## Vocabulary

Use these terms exactly — don't substitute "component," "service," "API," or "boundary." See [LANGUAGE.md](./LANGUAGE.md) for full definitions.

- **Module** — anything with an interface and an implementation
- **Interface** — everything a caller must know to use the module
- **Implementation** — the code inside
- **Depth** — leverage at the interface: a lot of behavior behind a small interface
- **Seam** — where an interface lives; a place behavior can be altered without editing in place
- **Adapter** — a concrete thing satisfying an interface at a seam
- **Leverage** — what callers get from depth
- **Locality** — what maintainers get from depth

## Key Principles

- **Deletion test**: imagine deleting the module. If complexity vanishes, it was a pass-through. If complexity reappears across N callers, it was earning its keep.
- **The interface is the test surface.**
- **One adapter = hypothetical seam. Two adapters = real seam.**

## Process

### 1. Explore
Read the project's domain glossary (`CONTEXT.md`) and any ADRs first.

Then walk the codebase organically and note friction:
- Where does understanding one concept require bouncing between many small modules?
- Where are modules **shallow** — interface nearly as complex as the implementation?
- Where have pure functions been extracted just for testability, but real bugs hide in how they're called?
- Where do tightly-coupled modules leak across their seams?
- Which parts are untested, or hard to test through their current interface?

Apply the **deletion test** to anything suspected shallow.

### 2. Present candidates
Numbered list of deepening opportunities. For each:
- **Files** — which files/modules are involved
- **Problem** — why the current architecture causes friction
- **Solution** — plain English description of what would change
- **Benefits** — in terms of locality and leverage, and how tests would improve

**ADR conflicts**: if a candidate contradicts an existing ADR, only surface when friction is real enough to warrant revisiting. Mark clearly.

Do NOT propose interfaces yet. Ask: "Which of these would you like to explore?"

### 3. Grilling loop
Once the user picks a candidate, drop into a grilling conversation. Walk the design tree — constraints, dependencies, the shape of the deepened module.

Side effects inline:
- **New concept?** Add to `CONTEXT.md`
- **Fuzzy term?** Sharpen in `CONTEXT.md`
- **Candidate rejected with load-bearing reason?** Offer an ADR

See [INTERFACE-DESIGN.md](./INTERFACE-DESIGN.md) for exploring alternative interfaces.

### 4. Close the hygiene cycle
When the grilling loop ends (whether work was done or the human decided to skip), delete `.plans/hygiene_report.md`. Its informational purpose has been served — the human reviewed it, decided what to act on, and either acted or deferred. Leaving it in `.plans/` would cause the next hygiene sweep to flag it as orphaned per the Section 6.5 Coordination Contract Audit.
