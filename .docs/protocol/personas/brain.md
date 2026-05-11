# 🫵 The Brain — You

> **Persona index:** [PERSONAS.md](../PERSONAS.md)

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
| **Plan-Critique** *(auto-gate)* | The Brain has nothing to do on PASS — the gate opens automatically. The Brain only acts on **WARN**: (1) Read findings in `plan_critique.md` lenses 1–4. (2) Either confirm (the AI writes `brain_confirmation: <ISO-8601>` to the frontmatter — file-based signal, not conversational acceptance) or reject (loop back to revise the plan). On **BLOCK** revision-2 halt: (3) explicitly pick A (revert to PRD) / B (downgrade to WARN with confirmation) / C (abort). |
| **Bug verification** | (1) Reproduction confirmed in `inspection_report.md`. (2) Regression test exists in the codebase. (3) Inspector report carries `human_confirmed: true`. |
| **Inspection Cycle Guard** *(2nd-failure triage)* | On the 2nd failed inspection of the same slice, the Inspector halts with `status: escalated`. The Brain picks one of: A (accept as debt — log to `known-issues.md` + `PROTOCOL_DEBT.md`); B (`LFE-FORCE` hotfix); C (re-plan slice from `03_slices.md`). The chosen option must be recorded in the body of `inspection_report.md`. |
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
