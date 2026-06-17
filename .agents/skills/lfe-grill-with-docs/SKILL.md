---
name: lfe-grill-with-docs
description: Deep alignment interview that challenges intent against the domain model, sharpens terminology, and writes back to CONTEXT.md and ADRs inline. Use at the start of any Major Change in the Architect sub-pipeline.
---

# LFE Grill With Docs

## Position in Pipeline
- **Phase**: 1 (Architect sub-pipeline, Step 1)
- **Persona**: Architect
- **Input**: Human intent (conversation)
- **Output**: `.plans/01_grill_summary.md`

## Process

Interview the human relentlessly about every aspect of the plan until shared understanding is reached. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask questions one at a time, waiting for feedback before continuing.

If a question can be answered by exploring the codebase or `.docs/`, explore instead of asking.

## During the Session

### Challenge against the glossary
When the human uses a term that conflicts with existing language in `CONTEXT.md`, call it out immediately. *"Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"*

### Sharpen fuzzy language
When the human uses vague or overloaded terms, propose a precise canonical term. *"You're saying 'account' — do you mean the Customer or the User? Those are different things."*

### Discuss concrete scenarios
When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force precision about boundaries between concepts.

### Cross-reference with code
When the human states how something works, check whether the code agrees. If contradiction found, surface it: *"Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"*

### Cross-context check (if applicable)
Before declaring any term, check whether `.docs/domain/CONTEXT-MAP.md` exists. If it does, verify whether the term is already declared in another context. If yes, prompt the Brain for the relationship classification (shared kernel / customer-supplier / translation / separate ways) and record the decision in CONTEXT-MAP.md.

### Update CONTEXT.md and Domain Rules Inline
When a new domain term or math/business rule is resolved, you MUST update the project's development documents IMMEDIATELY:
- **Canonical Terms**: Append directly to `CONTEXT.md` (root) and `.docs/domain/glossary.md` right there. Capture each as it happens rather than batching. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).
- **Logic, Rules, & Calculations**: Update `.docs/domain/domain-knowledge.md`.

Keep `CONTEXT.md` free of implementation details; include only terms meaningful to domain experts.

### Offer ADRs Sparingly
Create an Architectural Decision Record in `.docs/architecture/architecture-decisions.md` ONLY when all three are true:
1. **Hard to reverse** — cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — genuine alternatives existed and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).



## Coordination File Output

When the grill session completes, write `.plans/01_grill_summary.md` in this format:

```yaml
---
phase: architect
step: 1_grill
status: complete
timestamp: <ISO-8601>
source: n/a
---
```

```markdown
## Resolved Decisions
- <decision 1>
- <decision 2>

## Affected Modules
- <module list>

## Risk Zones
- <high-risk areas identified>

## Open Items
- <anything deferred>
```

Update `pipeline_status.md` coordination tracker to mark step 01 as ✅.
