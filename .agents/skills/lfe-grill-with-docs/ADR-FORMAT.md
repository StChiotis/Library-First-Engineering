# ADR Format

ADRs live as numbered sections inside the single canonical file `.docs/architecture/architecture-decisions.md`. Each ADR is a `## ADR N: Title` heading followed by its content. There is no per-file numbering and no separate ADR directory — one file holds them all, with a top-of-file index table. The log starts empty; your first decision is **ADR 1**.

The Architect appends new ADRs to that file; the Archivist updates the index table.

## Template

```md
## ADR N: {Short title of the decision}

**Status:** Accepted | Proposed | Deprecated | Superseded by ADR-M
**Date:** YYYY-MM-DD

{1-3 sentences: what's the context, what did we decide, and why.}
```

An ADR can be a single paragraph. The value is in recording *that* a decision was made and *why*.

## Optional sections

Only include these when they add genuine value. Most ADRs won't need them.

- **Considered Options** — only when the rejected alternatives are worth remembering
- **Consequences** — only when non-obvious downstream effects need to be called out

## Numbering

Scan `.docs/architecture/architecture-decisions.md` for the highest existing `## ADR N:` heading and increment by one (starting at 1 when the log is empty). Then add a row to the index table at the top of the file.

## When to offer an ADR

All three must be true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — genuine alternatives existed and you picked one for specific reasons

### What qualifies

- **Architectural shape.** "We're using a monorepo."
- **Integration patterns between contexts.** "Ordering and Billing communicate via domain events."
- **Technology choices that carry lock-in.** Database, message bus, auth provider.
- **Boundary and scope decisions.** Explicit no-s are as valuable as yes-s.
- **Deliberate deviations from the obvious path.** Anything where a reasonable reader would assume the opposite.
- **Constraints not visible in the code.** Compliance, performance contracts, partner requirements.
- **Rejected alternatives when the rejection is non-obvious.**
