---
name: lfe-extract-domain
description: Conduct a "Day 0" interview with the founder to populate the project's domain-knowledge.md and CONTEXT.md. Use when the agent encounters an undocumented Black Box.
---

# LFE Domain Extraction (Day 0)

## Position in Pipeline
- **Phase**: Any (Failure Recovery)
- **Persona**: Architect
- **Trigger**: Called when any persona encounters an undocumented "Black Box" in the code, or at project initialization
- **Output**: `.docs/domain/domain-knowledge.md` + `CONTEXT.md`

## Mission
Extract the project's core logic, math, and terminology from the founder's mind and formalize it into the Library System.

## Workflow
1. **The Core Entity**: Ask *"What is the single most important noun in this project? What is its exact definition?"*
2. **The Golden Logic**: Ask *"What is the 'Golden Rule' or primary calculation that this project performs? Walk me through the logic step-by-step."*
3. **The Constraints**: Ask *"What are the project's absolute hard constraints (legal, safety, or architectural) — the rules that must always hold?"*
4. **The Vocabulary**: Identify synonyms or ambiguous terms used during the interview and map them to canonical terms.

## Output
Produce a draft for:
- `.docs/domain/domain-knowledge.md`
- `CONTEXT.md` — using the format defined in [CONTEXT-FORMAT.md](../lfe-grill-with-docs/CONTEXT-FORMAT.md)

## Rules
- Follow the `CONTEXT-FORMAT.md` standard for all glossary entries (one-sentence definitions, _Avoid_ aliases, relationships, example dialogue).
- If `CONTEXT.md` already exists, merge new terms into it rather than overwriting.
- If an ADR is warranted by a decision made during extraction, use the format in [ADR-FORMAT.md](../lfe-grill-with-docs/ADR-FORMAT.md).
- After extraction, update `pipeline_status.md` to reflect the new domain knowledge.
