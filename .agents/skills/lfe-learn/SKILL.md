---
name: lfe-learn
description: Dynamic Library update skill. Triggered when the Brain explicitly teaches the system something new ("remember X", "from now on Y", "amend Z"). Classifies the lesson, routes it to the correct atomic doc in .docs/, proposes a diff, and writes after Brain confirms. Logs every change to CHANGELOG.md. Archivist persona.
---

# LFE Learn — Dynamic Library Update

## Position in Pipeline
- **Phase**: Any (invoked on-demand, not part of the sequential pipeline)
- **Persona**: Archivist
- **Trigger**: Brain uses one of these explicit teaching patterns:
  - `"remember: <rule or fact>"`
  - `"from now on: <behaviour>"`
  - `"amend: <existing rule> → <new rule>"`
  - `"forget: <thing we got wrong>"` (removes or strikes through an outdated rule)
- **Next Step**: Return to previous pipeline step (learning is non-blocking)

## Mission
Persist explicit human lessons into the Library so they survive context resets. The Library is the only memory that outlasts a session — verbal acknowledgement without a file write is a black hole. This skill closes that gap.

## Hard Rules
1. **Explicit Trigger Only**: Do not invoke speculatively. Only run when Brain uses one of the four explicit teaching patterns above.
2. **Propose Before Writing**: Always show the Brain a proposed diff (existing text → new text, or new entry to add) before committing to the file. Wait for explicit confirmation.
3. **Single Authoritative Location**: Each lesson lives in exactly one file. Do not duplicate across multiple docs.
4. **Changelog Always**: Every successful write is logged to `.docs/CHANGELOG.md` with timestamp, affected file, and a one-line summary of the change.
5. **Archivist Constraints**: No src/ writes. Library files only.

## Routing Table — Where Each Lesson Lives

| Lesson Type | Target File | When to use |
|---|---|---|
| Domain rule or business concept | `.docs/domain/domain-knowledge.md` | "Users can only X if Y", "A widget always has Z" |
| Technical convention or standard | `.docs/engineering/engineering-standards.md` | "All DB writes use the repository pattern", "Always validate at the API boundary" |
| Project context or background | `.docs/CONTEXT.md` | "This project runs on K8s", "The legacy module must not be touched" |
| Persona or workflow adjustment | `.docs/protocol/` (specific file) | Override a protocol step for this project |
| Glossary term | `.docs/domain/glossary.md` (if exists) | Define project-specific terminology |
| Architecture decision | `.docs/architecture/` (create if needed) | "We chose X over Y because Z" |

If the lesson does not fit any row, ask Brain to clarify the category before routing.

## Workflow

### Step 1: Parse the Teaching Moment
Extract:
- **What** is being taught (the rule, fact, or correction)
- **Type** (new rule / amendment / deletion / correction)
- **Scope** (project-wide vs. limited to a specific module or persona)

### Step 2: Classify and Route
Using the Routing Table, determine the target file. If ambiguous between two files, pick the narrower scope (domain > engineering > context).

### Step 3: Read the Target File
Read the current content of the target file. Locate the most appropriate section for insertion.

### Step 4: Propose the Diff
Present the Brain with:
```
Proposed change to <target file>:

BEFORE:
<existing text, or "N/A — new entry">

AFTER:
<new text with the lesson incorporated>

Confirm? (yes / no / revise)
```

Do not write until Brain says "yes" or an equivalent confirmation.

### Step 5: Write the Change
On Brain confirmation:
1. Write the updated content to the target file.
2. Append to `.docs/CHANGELOG.md`:

```markdown
| <ISO-8601 date> | lfe-learn | <target file> | <one-line summary of the lesson> |
```

3. Inform Brain: "Lesson persisted. Library updated."

### Step 6: Resume
Return control to the previous pipeline step or persona. Learning is non-blocking — it does not alter `pipeline_status.md` or consume a slot in the coordination file sequence.

## Checklist
- [ ] Explicit teaching trigger present (not speculative)?
- [ ] Target file identified from Routing Table?
- [ ] Diff proposed and Brain-confirmed before writing?
- [ ] CHANGELOG.md updated?
- [ ] Control returned to previous pipeline step?
