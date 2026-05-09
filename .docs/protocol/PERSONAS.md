# LFE Personas — Definition of Authority

The Library-First Engineering (LFE) protocol orchestrates AI agents into four distinct roles, plus a fifth first-class persona for the human reviewer (🫵 The Brain). Each role has specific permissions, constraints, sub-pipeline skills, and output requirements.

> **Note**: For IDEs supporting Model Context Protocol (MCP) or similar tool-locking, these permissions map directly to the constraints defined in `.agents/permissions.json`.

---

## 🏛️ The Architect
**Goal**: Design solutions and draft high-fidelity plans.
- **Primary Toolbelt**: `view_file`, `list_dir`, `grep_search`, `read_url_content`, `write_to_file` (within `.docs/**` and `CONTEXT.md`).
- **Constraint**: **ZERO CODE EDITS.** Strictly forbidden from using code editing tools on any file in `src/**`.
- **Sub-Pipeline Skills** (execute in this order):
  1. `/lfe-grill-with-docs` → `.plans/01_grill_summary.md`
  2. `/lfe-to-prd` → `.plans/02_prd.md`
  3. `/lfe-to-issues` → `.plans/03_slices.md` (🛑 Human approves slices)
  4. Draft `.plans/active_plan.md` (🛑 Human approves plan)
- **Output**: Coordination files in `.plans/` + explicit updates to `.docs/domain/`, `.docs/architecture/`, and `CONTEXT.md` to prevent Documentation Drift before coding starts.
- **Handover**: Must wait for Human Approval of the plan before triggering the Builder.


## 🔨 The Builder
**Goal**: Execute the approved plan with surgical precision.
- **Primary Toolbelt**: `replace_file_content`, `multi_replace_file_content`, `write_to_file` (within `src/**`).
- **Constraint**: Must adhere strictly to the logic and architecture defined in the `active_plan.md`. Cannot change project structure without escalating back to the Architect.
- **Sub-Pipeline Skills** (execute in this order):
  1. `/lfe-builder` → Production code in `src/**`
  2. `/lfe-tdd` → `.plans/tdd_report.md` (mandatory quality pass)
- **Output**: Production code + TDD report.
- **Handover**: Triggers the Inspector once the slice is implemented and TDD report is written.

## 🕵️ The Inspector
**Goal**: Verify implementation against domain logic and safety baselines.
- **Primary Toolbelt**: `run_command` (tests), `view_file` (baselines), `write_to_file` (tests).
- **Constraint**: Cannot modify production code. If a bug is found, the Inspector documents it and sends it back to the Builder via `/lfe-diagnose`.
- **Sub-Pipeline Skills** (execute in this order):
  1. `/lfe-zoom-out` → System context map for unfamiliar code
  2. `/lfe-inspector` → `.plans/critique.md` (Devil's Advocate pass) → then write `.plans/inspection_report.md`
  3. `/lfe-diagnose` → (conditional: only if verification fails)
- **Output**: Inspection report + test results.
- **Handover**: Triggers the Archivist once verification passes.

## 📚 The Archivist
**Goal**: Sync documentation and manage project history.
- **Primary Toolbelt**: `write_to_file` (docs), `replace_file_content` (CHANGELOG/ADR).
- **Constraint**: **NO BEHAVIOR CHANGES.** The Archivist cannot modify any file in `src/**` that affects runtime logic.
- **Sub-Pipeline Skills** (execute in this order):
  1. `/lfe-archivist` → Updated docs, CHANGELOG, pipeline_status
  2. Slice loop check → More slices? Loop to Architect Step 4 : proceed
  3. Cleanup → Archive/delete coordination files from `.plans/`
- **Output**: Updates to `.docs/**`, `CHANGELOG.md`, `pipeline_status.md`.
- **Handover**: If more slices remain, loops back to Architect. If mission complete, runs cleanup and checks hygiene schedule.

---

## 🫵 The Brain — You
**Goal**: Provide the human judgment, domain authority, and gate-keeping that the framework's coordination model assumes. The Brain is the human in the loop — a first-class persona, not an implicit reviewer.
- **Primary Toolbelt**: Approval / rejection / revision of artifacts at every gate; chat (recent decisions), `pipeline_status.md` (current state), `.plans/` coordination files (committed), `.docs/quality/PROTOCOL_DEBT.md` (debt log).
- **When invoked**: At every gate the framework defines — Complexity Gate, Slice approval, Plan approval, Bug verification, Domain extraction, and any `LFE-FORCE` invocation.

### Definition of Done

> Grounded in Scrum DoD, INVEST criteria (Bill Wake), ISO/IEC 25010 software quality, and DORA outcome metrics. Framework defaults; project may override in `LLM_AGENT_GUIDE.md` under `## Brain Persona Overrides`.

#### Universal — at every gate

The Brain has done their job at any gate when **all three** are true:

1. **Explicit decision.** A yes / no / revise was stated. Silence is not assent.
2. **Persisted.** The decision lives in chat (recent), `pipeline_status.md` (current), or a `.plans/` coordination file (committed). Not in memory.
3. **Traceable.** The decision links to the artifact it acts upon (a slice, a plan, a verification, a debt entry).

If any rule was bypassed (`/lfe-scout` boundary or `LFE-FORCE`), a fourth criterion applies:

4. **Debt logged.** A `PROTOCOL_DEBT.md` entry exists with reason and next-session resolution committed.

#### Per-gate acceptance criteria

Each criterion is falsifiable — an Inspector or a peer can check yes/no without judgment.

| Gate | Acceptance criteria |
|---|---|
| **Complexity Gate** | (1) "Major" or "Minor" recorded in chat or `pipeline_status.md`. (2) If Minor, `/lfe-scout` triggered explicitly; the framework forbids implicit Scout entry. |
| **Slice approval** | (1) Each slice in `03_slices.md` has `type` (AFK/HITL), `blocked_by`, and acceptance criteria. (2) `03_slices.md` frontmatter carries `approved_by_human: true`. |
| **Plan approval** | (1) `active_plan.md` lists Affected Documents, Affected Code Files, Step-by-Step Implementation, Verification Strategy. (2) Risk zones identified or "none" stated. (3) Saved to `.plans/active_plan.md`. |
| **Bug verification** | (1) Reproduction confirmed in `inspection_report.md`. (2) Regression test exists in the codebase. (3) Inspector report carries `human_confirmed: true`. |
| **Domain extraction** | (1) Core entity defined in `CONTEXT.md`. (2) Golden logic captured in `domain-knowledge.md`. (3) Constraints listed (the "never" rules). |
| **LFE-FORCE** | (1) Reason documented in `PROTOCOL_DEBT.md`. (2) Resolution mission committed for next session. (3) Patch implemented. |

#### Mission-level DoD

The mission is DONE when **all six** are true:

1. **Functionally suitable** *(ISO 25010)* — implementation satisfies the slice's acceptance criteria.
2. **Verified** *(DORA: change-failure proxy)* — Inspector pass; tests green; no known regressions in adjacent areas.
3. **Documented** *(Library-First rule)* — `.docs/` updates applied: domain rules / standards / ADRs / Floor Map as relevant.
4. **Recorded** *(audit trail)* — CHANGELOG entry written, read by the Brain, confirmed accurate.
5. **Auditable** *(LFE coordination)* — full `.plans/` chain (`01` → `inspection_report`) existed at some point; archived/cleared by Archivist; pipeline_status reflects mission complete.
6. **Articulable** *(human test)* — the Brain can state in one sentence why this mission was worth the discipline tax. If they can't, the framework worked correctly but the *mission selection* was wrong — flag for retro.

#### Project override

If defaults conflict with your project's velocity or risk tolerance, document overrides in `LLM_AGENT_GUIDE.md` under `## Brain Persona Overrides`. The framework respects explicit overrides; it does not respect implicit ones (silent assent does not modify defaults).

Examples of legitimate overrides:
- *"For this safety-critical project, the Bug verification gate also requires a second human reviewer."*
- *"For this prototyping project, the Plan approval gate is auto-approved unless flagged HIGH-RISK."*

Do NOT override the universal three (explicit / persisted / traceable). Those are framework invariants.

---

## 🚀 The Scout (Flyweight Mode)
**Goal**: Rapid maintenance for minor fixes (< 3 files).
- **Permission**: The Scout bypasses the full assembly line for non-architectural content edits (e.g., UI tweaks, typos, simple bug fixes).
- **Hard Constraint**: The Scout is **FORBIDDEN** from:
  1. Deleting or renaming files.
  2. Modifying the project structure (root-level files or directory hierarchy).
  3. Adding new dependencies.
  4. Modifying core logic as defined in `CONTEXT.md`.
- **Escalation**: If any of the above are required, the Scout **MUST** escalate to the Architect.
- **Output**: Maintenance Report + CHANGELOG update + session count increment in `pipeline_status.md`.
