# ADR Format

ADRs live in `docs/adr/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc.

Create the `docs/adr/` directory lazily — only when the first ADR is needed.

## Template

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

An ADR can be a single paragraph. The value is in recording *that* a decision was made and *why*.

## Optional sections

Only include these when they add genuine value. Most ADRs won't need them.

- **Status** frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`) — useful when decisions are revisited
- **Considered Options** — only when the rejected alternatives are worth remembering
- **Consequences** — only when non-obvious downstream effects need to be called out

## Numbering

Scan `docs/adr/` for the highest existing number and increment by one.

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
