---
name: lfe-visual-check
description: Inspector sub-skill. Renders the changed UI surface, reasons over the result, and writes a text findings record with a human-action instruction and sign-off token — so a human visual sign-off can close a UI change. Artifact-free (saves no image files). Auto-arms on a visual-file touch; written by the Inspector at finalization. Called by lfe-inspector when armed via inspector-config.md.
---

# LFE Visual Check — Render, Reason, Hand the Verdict to the Human

## Position in Pipeline
- **Phase**: 3 (Inspector sub-skill)
- **Persona**: Inspector (read-only; writes only `.plans/checks/`)
- **Trigger**: Invoked by `/lfe-inspector` Sub-Skill Dispatch when armed — either `lfe-visual-check: true` in `.docs/quality/inspector-config.md` / `## Inspector Overrides`, **or** the **Visual Floor** auto-arms it because the slice's changed files touch a visual file class (the floor is a minimum; an override leaves it armed).
- **Output**: `.plans/checks/visual_findings.md` — aggregated by the Inspector into `critique.md`, and the basis for the human visual sign-off at the finalization gate.

## Mission
Give the Inspector **eyes**. Render the changed UI surface, reason over what it shows, and present it to the human for a visual verdict. The Inspector asserts the *technical* pass on its own authority; the **visual** verdict belongs to the human alone. This sub-skill renders and presents — the human alone declares whether it looks right.

## Hard Rules
0. **Dispatch Context Required (refuse direct invocation)**: This skill is dispatched by `/lfe-inspector` Step 6 — it is not a Brain-typeable skill (per `LLM_AGENT_GUIDE.md` §8.8 Skill Invocation Authority). If invoked without `.plans/builder_done.md` for the current slice, halt immediately and reply: *"`/lfe-visual-check` is an Inspector sub-skill dispatched by `/lfe-inspector`. It cannot be run standalone. Run `/lfe-inspector` — the dispatcher will invoke this sub-skill if it is armed in `.docs/quality/inspector-config.md` (or via an `## Inspector Overrides` section in `active_plan.md`), or auto-armed by the Visual Floor."* Direct invocation produces orphaned findings files and breaks the Inspector's aggregation logic.
1. **Artifact-free**: Reason over the in-context render and write prose only. This skill saves **no image files** — keeping the blank-canvas seal intact and the run self-cleaning. (Render tools return the pixels in-context; capture the reasoning, leave the bytes unsaved.)
2. **Render-then-reason**: Findings come from looking at the rendered surface, cited concretely (what element, what looks wrong).
3. **Defer the verdict**: Write the findings and a human-action instruction; the human declares the visual verdict at the finalization gate. This skill records no `visual_confirmed` / `visual_signoff` itself — that is the Inspector's finalization act, taken on the human's word.
4. **Diff-scoped**: Reason about the visual file classes changed in `builder_done.md`; skip unrelated surfaces.

## Inputs
1. `.plans/builder_done.md` — the changed files (which visual surfaces moved)
2. `.plans/active_plan.md` — the scope boundary and the optional `## UI Surface` field (a preview URL or route the renderer targets)
3. `.docs/` engineering or design baselines, if the project keeps them

## Workflow

### Step 1: Resolve the preview surface
Read `active_plan.md` for a `## UI Surface` field (a localhost preview URL or a route). When present, that is the render target. When absent, the surface degrades to a **manual instruction** (Step 2c).

### Step 2: Render (preferred → browser → manual)
- **2a — preferred renderer**: render the target with an available in-context preview/render tool. Capture a single in-context render and reason over it.
- **2b — browser renderer**: when the preferred renderer is unavailable, render the route with a browser-automation tool and reason over the in-context result.
- **2c — manual fallback**: when no renderer or no preview target is available, name the exact screen for the human to inspect and describe what to look for. The human looks; their report is the visual signal.

In every path: save no image files.

### Step 3: Reason over the result
Compare the rendered surface against the plan's intent and any design baseline. Note layout, overflow, alignment, contrast, missing/duplicated elements, and rendering errors. Cite each observation concretely.

### Step 4: Write the findings file
Path: `.plans/checks/visual_findings.md`. Begin with a YAML frontmatter block — the Inspector's dispatcher relies on `status: complete` to detect a successful run and skip it on crash recovery.

```yaml
---
phase: inspector
step: visual-check
kind: sub-skill
status: complete
timestamp: <ISO-8601>
source: .plans/builder_done.md
slice: <copied from active_plan.md>
---
```

Body:

```markdown
## Visual Check Findings

**Surface**: <preview URL / route, or "manual — <screen name>">
**Render path**: preferred | browser | manual
**Changed visual files**: <list>

### Observations
- <element — what it looks like — concern, or "renders as intended">

### Human-Action Instruction
- Look at the surface above. Reply with your visual verdict: **approve** (it looks right) or **reject** (describe what is wrong).
- On **approve**, the Inspector records `visual_confirmed: <ISO-8601>` and `visual_signoff: <token>` in `inspection_report.md`, and the close proceeds to the Archivist.
- On **reject**, the finalization gate routes the defect through the rework loop back to the Builder.

### Sign-off token (template)
- The token is **agent-transcribed** from the human's affirmative (the **same trust model as `brain_confirmation`** — a transcription convention, not a non-forgeable mechanism). Suggested form: `VISUAL-OK <ISO-8601> — "<the human's words>"`. The Inspector transcribes it verbatim into `visual_signoff` at finalization.
```

If the render shows a clean surface, write `Renders as intended across the changed visual files.` under Observations — and still write the Human-Action Instruction, because the visual sign-off is the human's to give.

## Handoff
Return control to `/lfe-inspector`. The Inspector aggregates this file under a `## Visual` section in `critique.md`, and at the finalization gate presents the Human-Action Instruction to obtain the visual sign-off. Write only this findings file; leave `critique.md` and the `inspection_report.md` sign-off fields to the Inspector.
