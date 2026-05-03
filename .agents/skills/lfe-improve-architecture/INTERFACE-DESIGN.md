# Interface Design

When exploring alternative interfaces for a deepening candidate. Based on "Design It Twice" (Ousterhout).

Uses the vocabulary in [LANGUAGE.md](LANGUAGE.md) — **module**, **interface**, **seam**, **adapter**, **leverage**.

## Process

### 1. Frame the problem space
Before exploring alternatives, write:
- Constraints any new interface must satisfy
- Dependencies it would rely on
- A rough illustrative code sketch to ground the constraints

### 2. Generate alternatives
Produce 3+ radically different interfaces for the deepened module:

- **Option A**: Minimize the interface — 1–3 entry points max. Maximise leverage per entry point.
- **Option B**: Maximise flexibility — support many use cases and extension.
- **Option C**: Optimise for the most common caller — make the default case trivial.
- **Option D** (if applicable): Design around ports & adapters for cross-seam dependencies.

Each option outputs:
1. Interface (types, methods, params + invariants, ordering, error modes)
2. Usage example showing how callers use it
3. What the implementation hides behind the seam
4. Trade-offs — where leverage is high, where it's thin

### 3. Present and compare
Compare by **depth** (leverage at interface), **locality** (where change concentrates), and **seam placement**.

Give your own recommendation: which design is strongest and why. If elements combine well, propose a hybrid. Be opinionated.
