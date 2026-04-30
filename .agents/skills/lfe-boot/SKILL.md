---
name: lfe-boot
description: Bootstrap an LFE-compliant session. Reads orientation docs and activates the project persona. Use at the start of any session to ensure full orientation.
---

# LFE Session Bootstrap

## Protocol

### Step 1 — Read the Entrance Card
Read `pipeline_status.md`. Extract and hold:
- **Integrity Score** (Verify it is green)
- **Active Persona** (Architect / Builder / Inspector / Archivist)
- **Active Mission** (The current goal)

### Step 2 — Read the Agnostic Core
Read the protocol definitions in:
1. `.docs/protocol/PERSONAS.md` (Verify your permissions)
2. `.docs/protocol/ASSEMBLY_LINE.md` (Verify the current stage)
3. `.docs/protocol/GOVERNANCE.md` (Confirm Logic Sovereignty)

### Step 3 — Complexity Gate
Immediately ask the user:
> *"Is this a Major Architectural Change (Full Pipeline) or a Minor Fix (Scout Mode)?"*

### Step 4 — Report State
Output a structured orientation report confirming:
- Persona is active.
- Ground rules are loaded.
- Floor map and Glossary are understood.

## Orientation invariants
- **Docs win over code**: The documentation is the single source of truth.
- **Logic Sovereignty**: Core logic must live in designated core modules.
- **No cowboy coding**: Every major change starts with a plan in `.plans/`.
- **Persona Discipline**: Stick to the tool-locking constraints of your active persona.
