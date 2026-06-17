---
name: lfe-plan-critique
description: Run a 5-lens pre-build critique of the approved active plan before the Builder starts. Acts as the Architect persona, read-only on src/. Writes .plans/plan_critique.md. Use immediately after Brain approves active_plan.md.
---

# LFE Plan Critique — Pre-Build 5-Lens Review

## Position in Pipeline
- **Phase**: 1.5 (between Architect plan approval and Builder start)
- **Persona**: Architect (read-only — no src/ write access)
- **Trigger**: Automatically after Brain approves `active_plan.md` — before `/lfe-builder` is invoked
- **Next Step**: `/lfe-builder` (on PASS) or `/lfe-architect` revision loop (on BLOCK)

## Mission
Stress-test the approved plan from five angles before a single line of code is written. Catch ambiguous acceptance criteria, untestable requirements, domain boundary violations, structural impact, and cross-edit incoherence before they become bugs in `src/`. The five lenses are judgment calls; a mechanical pre-pass (Step 1.5) first surfaces the objective, checkable defects into Lens 2 (glob reach, line-count and existence-check antipatterns) and Lens 5 (orphan-word candidates), so the judgment lenses reason over verified facts rather than guesses.

## Hard Rules
1. **Zero Code Writes**: This skill operates on `.plans/` and `.docs/` only — `src/` stays untouched.
2. **5-Lens Sequential**: Run all five lenses in order, every time — even when an earlier lens passed cleanly. Run the mechanical pre-pass (Step 1.5) first, so Lens 2 and Lens 5 reason over its findings.
3. **Single Output File**: All findings aggregate into one `plan_critique.md` — no per-lens files.
4. **Verdict is Decisive**: A single BLOCK finding stops the Builder. WARN requires an explicit, file-recorded Brain confirmation before Builder starts (see Step 7 — `brain_confirmation` frontmatter field).
5. **Domain Library Is Truth**: All domain boundary and architectural judgments must cite a specific `.docs/` file and line, not general knowledge.
6. **Revision Counter (file-based)**: The `revision:` field in `plan_critique.md` frontmatter is the **physical substrate** for the 2-revision limit (GOVERNANCE Cycle Limits). On every run, read any existing `plan_critique.md` first to determine the current revision number. On a 2nd BLOCK, halt and present Brain triage instead of looping back. This rule exists because the file is overwritten each run — without the counter, the limit would be conversation-only and lost on crash.

## Workflow

### Step 0: Counter Check (must run first)
Before loading any other context, check whether `.plans/plan_critique.md` already exists for the current slice:

1. **File does not exist** → this is **Revision 1**. Continue to Step 1.
2. **File exists** — read its frontmatter:
   - **`verdict: PASS` or `verdict: WARN` with `brain_confirmation` set** → the gate is already open; this skill should not have been invoked. Halt and instruct the Brain to proceed to `/lfe-builder`.
   - **`verdict: WARN` with `brain_confirmation: null`** → Brain has not yet confirmed; re-presenting the existing WARN is correct. Skip to Step 7 (Branch on Verdict) — re-present rather than re-running the lenses.
   - **`verdict: BLOCK` and `revision: 1`** → the Architect has revised `active_plan.md` and is re-running. This is **Revision 2**. Continue to Step 1.
   - **`verdict: BLOCK` and `revision: 2`** → the 2-revision limit is exhausted. **Halt immediately** — present the Brain with three triage options instead of re-running the lenses (per `LOOP_ARCHITECTURE.md` Scenario 1.4):
     - **A — Revert to PRD**: loop back to `/lfe-to-issues` from `02_prd.md`.
     - **B — Accept WARN and proceed**: Brain explicitly downgrades the BLOCK to a WARN and authorises Builder. If chosen, update `plan_critique.md` frontmatter: `verdict: WARN`, `brain_confirmation: <ISO-8601>`. Set `Active Persona: Builder`.
     - **C — Abort mission**: wipe `.plans/` execution files; mission cancelled.

Record the determined revision number — it must appear in the frontmatter of the file you eventually write.

### Step 1: Load Context
Read in this order:
1. `.plans/active_plan.md` — the plan under review
2. `.plans/02_prd.md` — the PRD (acceptance criteria source)
3. `.plans/03_slices.md` — slice boundaries and definition of done
4. `.docs/domain/` — domain rules and boundaries
5. `.docs/architecture/` (if present) — architectural constraints

### Step 1.5: Run the Mechanical Pre-Pass
Before the judgment lenses, scan `active_plan.md` for four objective, mechanically-checkable defect classes. This is **reasoning, not tooling** — read the plan's acceptance criteria and verification commands and reason over them directly; run no scripts. Carry each finding into the lens noted.

- **glob-count → Lens 2**: for every acceptance criterion that asserts a count over a glob, resolve the glob against the repository, report the files it actually reaches, and flag any stated count that contradicts that reach.
- **test-path → Lens 2**: flag any existence check that iterates a directory's children and tests derived paths instead of testing the target path directly — it silently tests the wrong paths and can false-negative on correct substrate.
- **line-count → Lens 2**: flag any acceptance criterion that pins an exact or narrow line count; content renders single- vs multi-line, so a tight assertion false-positives on a correct substrate.
- **orphan-word → Lens 5**: enumerate the orphan-prone words (*unchanged, now, still, previously, no longer, was always*) in text the plan preserves, as high-recall candidates. The pre-pass only lists them; **Lens 5 decides** whether each still reads correctly after the edit.

Record the results in the `## Mechanical Pre-Pass Findings (Step 1.5)` section of the output; the consuming lens must explicitly adjudicate each one — confirm it as a finding or dismiss it with a reason.

### Step 2: Lens 1 — Acceptance Criteria Scrutiny
For every acceptance criterion listed in `active_plan.md`:
- Is it falsifiable? (Can you write a test that fails when it's broken?)
- Is it unambiguous? (Would two engineers agree on what "done" means?)
- Is it traceable to the PRD? (Every AC must map to a requirement in `02_prd.md`)

Flag any AC that is vague, unmeasurable, or absent from the PRD.

For slices that **create, edit, or archive structured documentation**, additionally check each AC against the doc-edit / archive-move AC-design patterns the Architect's Plan-Composition Discipline enumerates (section-name conformance, cross-reference semantic-inversion anchoring, byte-identity sidecar fixtures, section-boundary extraction caveats, cross-file scope symmetry, edit-mechanism ACs, archive-move ACs). A doc-slice AC that omits the applicable pattern is a **WARN** — the downstream verification will likely escape the defect class that pattern guards.

### Step 3: Lens 2 — Test Feasibility
Review the **Verification Strategy** section of `active_plan.md`:
- Can each listed test be realistically written (no hidden infrastructure gaps)?
- Does the test strategy avoid mock-explosion (more mocks than real behaviour)?
- Are side effects (file I/O, network, DB, time) explicitly addressed?
- Is the test pyramid reasonable (unit/integration/e2e balance)?

Flag any test idea that requires unavailable tooling, excessive mocking, or no clear assertion path.

**Consume the Step 1.5 pre-pass here.** Fold in its glob-count, test-path, and line-count findings: a glob whose resolved reach contradicts a stated count is a **WARN**; an existence check that iterates a directory's children instead of testing the path directly is a **WARN**; an exact or narrow line-count AC is a **WARN** (recommend a widened tolerance band). Sanity-check each resolved glob's reach against the plan's intent.

### Step 4: Lens 3 — Domain Alignment
Cross-reference the implementation approach against domain documentation:
- Does the plan respect domain boundaries in `.docs/domain/`?
- Does the plan introduce any business logic not documented in the Library?
- Does the plan rename, restructure, or redefine any documented concept?

Any undocumented business logic introduced by the plan is a **BLOCK** — it violates Logic Sovereignty. Cite the specific `.docs/` file where the boundary is defined.

### Step 5: Lens 4 — Structural Impact
Assess the plan's effect on existing architecture:
- Does the plan introduce coupling between modules that the architecture keeps separate?
- Does the plan duplicate logic that already exists (identified via **Step-by-Step Implementation** section)?
- Does the plan require changes to shared interfaces or contracts that other slices depend on?
- Does the plan's scope creep beyond the current slice boundary in `03_slices.md`?

Flag any structural drift from established architecture. Cite `.docs/architecture/` if present.

### Step 5.5: Lens 5 — Coherence Simulation
Mentally execute the plan's edits and check that the *surrounding, preserved* text still reads correctly afterward. This lens catches the cross-edit incoherence the other four miss — text that referred to something an edit removes or changes elsewhere. Two scans:

- **Word-level (adjudicate the Step 1.5 orphan-word candidates):** for each orphan-prone word the pre-pass flagged in preserved text — *unchanged, now, still, previously, no longer, was always* — ask: does it still make sense after the edit, or does it now dangle (e.g. "the doc *still* references the old row" when the edit deletes that row; "*unchanged*" next to something the plan changes)? The pre-pass lists high-recall candidates; **you adjudicate** — most are fine, but each must be checked, not skipped. A genuine dangling reference is a **WARN**.
- **Section-level:** when an edit replaces a subsection, re-read the parent section's intro/framing paragraph — does it still accurately characterize all subsections (e.g. an intro saying "these are optional" when the edit made one mandatory; "four" when the edit made it five)? A framing/content mismatch is a **WARN**.

This lens answers the failure class where an edit's blast radius leaves stale references behind. Dogfood it on the plan's own diff.

### Step 6: Write `plan_critique.md`

```yaml
---
phase: architect
step: plan_critique
status: complete
timestamp: <ISO-8601>
source: .plans/active_plan.md
slice: <copied from active_plan.md>
verdict: PASS | WARN | BLOCK
revision: 1 | 2                        # from Step 0 counter check
brain_confirmation: <ISO-8601 | null>   # null on first write; set by Step 7 when Brain confirms a WARN
---
```

Body structure:

```markdown
## Verdict: PASS | WARN | BLOCK

## Lens 1 — Acceptance Criteria Scrutiny
- <Finding or "All criteria are falsifiable and traceable.">

## Lens 2 — Test Feasibility
- <Finding or "Test strategy is realistic and complete.">

## Lens 3 — Domain Alignment
- <Finding or "Plan stays within documented domain boundaries.">

## Lens 4 — Structural Impact
- <Finding or "No architectural drift detected.">

## Lens 5 — Coherence Simulation
- <Finding (dangling reference / framing mismatch) or "Edits leave surrounding text coherent; no orphaned references.">

## Mechanical Pre-Pass Findings (Step 1.5)
- <Summary of the pre-pass findings and how each was adjudicated, or "Clean.">

## Recommended Actions
- <Specific change to active_plan.md needed before Builder starts, or "None — proceed.">
```

### Step 7: Branch on Verdict
- **PASS** → Notify Brain ("Plan critique passed — Builder may proceed."). Mark `plan_critique ✅` in `pipeline_status.md`. Set `Active Persona: Builder`. Leave `brain_confirmation: null` in the file (PASS does not require explicit confirmation).
- **WARN** → Present findings to Brain. Brain decides: accept and proceed, or loop back to Architect.
  - **If Brain confirms**: re-write `plan_critique.md` with the same body but update frontmatter `brain_confirmation: <ISO-8601>`. This is the **file-based signal** the Builder's Step 1 gate parses; conversational confirmation is not sufficient. Set `Active Persona: Builder`.
  - **If Brain rejects**: loop back to Architect (Step 4) for plan revision. The next `/lfe-plan-critique` run will see this file as `verdict: WARN`, `brain_confirmation: null` (Step 0 will re-present rather than re-run lenses; if Brain wants a fresh critique on the revised plan, the Architect should delete the existing `plan_critique.md` so Step 0 treats it as Revision 1 of a re-scoped plan — document this in the plan revision notes).
- **BLOCK** → Loop back to Architect. Architect revises `active_plan.md` to address the finding(s). Re-run `/lfe-plan-critique`. Step 0 reads the existing file to detect this is **Revision 2**; on a 2nd BLOCK, Step 0 halts and presents Brain triage rather than re-running lenses (per Scenario 1.4 and the Cycle Limits in `GOVERNANCE.md`).

## Checklist
- [ ] Step 0 counter check executed before any lens work?
- [ ] Step 1.5 mechanical pre-pass run and its findings carried into Lens 2 + Lens 5?
- [ ] All five lenses applied sequentially?
- [ ] Each pre-pass finding explicitly adjudicated (confirmed or dismissed with reason)?
- [ ] Every BLOCK finding cites a specific `.docs/` file?
- [ ] `plan_critique.md` written with correct frontmatter (`verdict`, `revision`, `brain_confirmation`)?
- [ ] WARN path: `brain_confirmation` left `null` on initial write; updated to ISO-8601 only after Brain explicitly confirms?
- [ ] Builder NOT started until `verdict: PASS` OR (`verdict: WARN` AND `brain_confirmation` is non-null)?
