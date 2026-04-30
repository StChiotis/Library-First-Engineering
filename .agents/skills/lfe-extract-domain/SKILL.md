---
name: lfe-extract-domain
description: Conduct a "Day 0" interview with the founder to populate the project's domain-knowledge.md and CONTEXT.md.
---

# LFE Domain Extraction (Day 0)

## Mission
Extract the project's core logic, math, and terminology from the founder's mind and formalize it into the Library System.

## Workflow
1. **The Core Entity**: Ask "What is the single most important noun in this project? What is its exact definition?"
2. **The Golden Logic**: Ask "What is the 'Golden Rule' or primary calculation that this project performs? Walk me through the logic step-by-step."
3. **The Constraints**: Ask "What are the absolute 'Never' rules (legal, safety, or architectural) for this project?"
4. **The Vocabulary**: Identify synonyms or ambiguous terms used during the interview and map them to canonical terms in `CONTEXT.md`.

## Output
Produce a draft for:
- `.docs/domain/domain-knowledge.md`
- `CONTEXT.md` (Glossary section)
