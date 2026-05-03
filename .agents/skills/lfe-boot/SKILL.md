---
name: lfe-boot
description: Bootstrap an LFE-compliant session. Reads orientation docs, checks for interrupted sessions, and activates the project persona. Use at the start of any session.
---

# LFE Session Bootstrap

## Protocol

### Step 1 — Read the Entrance Card
Read `pipeline_status.md`. Extract and hold:
- **Integrity Score** (Verify it is green)
- **Active Persona** (Architect / Builder / Inspector / Archivist)
- **Active Mission** (The current goal)
- **Pipeline Phase** (Which phase and slice we're on)
- **Coordination Files** (Which steps are complete)
- **Session Count** (For hygiene scheduling)

### Step 2 — Check for Interrupted Session or State Anomaly
Scan `.plans/` for coordination files (`01_grill_summary.md`, `02_prd.md`, `03_slices.md`, `active_plan.md`, `tdd_report.md`, `inspection_report.md`).

- **Scenario A: Normal Interrupted Session**
  If files exist in `.plans/` and the entrance card state indicates the mission is in progress:
  > *"⚠️ Interrupted session detected. Last completed step: \<step\>. Resume from: /lfe-\<next-skill\>. Confirm?"*

- **Scenario B: State Anomaly & Zoom-Out Comparison**
  If files exist in `.plans/` but the entrance card indicates the mission is complete or ready:
  1. Do NOT delete or clear the files automatically.
  2. Invoke the **`/lfe-zoom-out`** skill instructions to perform a high-level comparison: Compare the changes defined in the `.plans/` files against the current codebase in `src/**`.
  3. Inform the user of your findings immediately:
     - *If the plans match the code*:
       > *"⚠️ State Anomaly detected: Files exist in `.plans/` but work was already completed and saved to `src/**`. The Archivist missed its cleanup step. Please clear the `.plans/` directory manually."*
     - *If the plans do NOT match the code*:
       > *"⚠️ State Anomaly detected: Files exist in `.plans/` but the changes were not saved to the codebase. This is a real interrupted session."*



### Step 3 — Read the Agnostic Core
Read the protocol definitions in:
1. `.docs/protocol/PERSONAS.md` (Verify your permissions)
2. `.docs/protocol/ASSEMBLY_LINE.md` (Verify the current stage)
3. `.docs/protocol/GOVERNANCE.md` (Confirm Logic Sovereignty)

### Step 4 — Check for Blank Canvas (Day 0)
Inspect the `Engine Status` from the entrance card (`pipeline_status.md`).
- **If it indicates `[BLANK CANVAS]`**: You MUST immediately execute the instructions of the `/lfe-extract-domain` skill to extract the project's domain requirements from the user right now. Do NOT wait for another command.
- **If not**: Proceed to Step 5.


### Step 5 — Complexity Gate
Ask the user:
> *"Is this a **Major Architectural Change** (Full Pipeline) or a **Minor Fix** (Scout Mode)?"*

### Step 6 — Report State
Output a single, concise block:

```
[LFE BOOT] Active Persona: <Persona>. Phase: <Phase>. Session #<N>.
```

If resuming: `[LFE BOOT] Resuming interrupted session. Next: /lfe-<skill>.`


## Orientation invariants
- **Docs win over code**: The documentation is the single source of truth.
- **Logic Sovereignty**: Core logic must live in designated core modules.
- **No cowboy coding**: Every major change starts with a plan in `.plans/`.
- **Persona Discipline**: Stick to the tool-locking constraints of your active persona.
- **File-Based Coordination**: Skills read/write coordination files in `.plans/`, not conversation context.
