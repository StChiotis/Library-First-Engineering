---
name: lfe-whats-next
description: Pipeline navigator. Reads pipeline_status.md and coordination files to tell you exactly where you are and what to do next. Use at any point to orient.
---

# LFE What's Next

## Purpose
Instant orientation. Read the pipeline state and tell the human exactly what step comes next.

## Process

### 1. Read pipeline state
Read `pipeline_status.md` and extract:
- Active Persona
- Active Mission
- Pipeline Phase
- Coordination Files status

### 2. Scan coordination files
Check which files exist in `.plans/`:
- `01_grill_summary.md` — grill complete?
- `02_prd.md` — PRD complete?
- `03_slices.md` — slices approved?
- `active_plan.md` — plan written for current slice?
- `plan_critique.md` — pre-build critique verdict? (PASS / WARN / BLOCK)
- `builder_done.md` — implementation complete?
- `tdd_report.md` — TDD pass complete?
- `.plans/checks/*.md` — Inspector sub-skill findings (if enabled)?
- `critique.md` — Inspector 4-Eyes pass complete?
- `inspection_report.md` — inspection verdict?
- `diagnosis_report.md` — bug diagnosis (only on first inspection failure)?

### 3. Determine next action
Based on what exists and what doesn't, output exactly one block:

```
📍 Phase <N>: <Phase Name> (Slice <X> of <Y>)
   ✅ Completed: <list of completed steps>
   ⬜ Next step: <exact skill to run>
   🥸 Persona: <who should be active>
   💬 Run: /lfe-<skill-name>
```

### 4. Edge cases

**No coordination files exist + no active mission:**
```
📍 No active mission.
   💬 Start with: /lfe-boot
```

**Session appears to have crashed mid-pipeline:**
```
📍 ⚠️ Interrupted session detected.
   Last completed: <last file found>
   Resume from: /lfe-<next-skill>
   🥸 Persona: <who should be active>
```

**All slices complete, cleanup pending:**
```
📍 All slices implemented and verified.
   ⬜ Next step: Archivist cleanup
   💬 Run: /lfe-archivist
```

**Hygiene due (session count ≥ 5 since last sweep):**
```
📍 Mission complete.
   ⚠️ Architecture sweep due (5+ sessions since last).
   💬 Run: /lfe-improve-architecture
```

## Rules
- Never modify any files. This is a read-only skill.
- Output must be concise — max 5 lines.
- Use the coordination file presence as ground truth, not conversation context.
