# LFE Assembly Line — Workflow Protocol

This document defines the lifecycle of a task within a Library-First Engineering (LFE) repository.

```mermaid
graph TD
    Start([Session Start]) --> Boot[lfe-boot]

    Boot --> Resume{Interrupted<br>session?}
    Resume -- "Yes" --> ResumePoint[Resume from<br>last step]
    Resume -- "No" --> DebtCheck{Protocol Debt?}

    DebtCheck -- "Yes" --> Block[Force Inspector/Archivist]
    DebtCheck -- "No" --> Gate{Complexity Gate}

    Gate -- "Minor Fix" --> Scout[Scout Mode]
    Scout --> Archivist
    Gate -- "Major Change" --> Grill

    subgraph architect [Phase 1: Architect Sub-Pipeline]
        Grill[Step 1: lfe-grill-with-docs] --> PRD[Step 2: lfe-to-prd]
        PRD --> Issues[Step 3: lfe-to-issues]
        Issues --> SliceApprove{Human approves<br>slices?}
        SliceApprove -- "No" --> Issues
        SliceApprove -- "Yes" --> Plan[Step 4: lfe-architect]
    end

    Plan --> PlanApprove{Human approves<br>plan?}
    PlanApprove -- "No" --> Plan
    PlanApprove -- "Yes" --> Critique[Step 4b: lfe-plan-critique]

    Critique --> CritiqueVerdict{Verdict?}
    CritiqueVerdict -- "PASS" --> Build
    CritiqueVerdict -- "WARN" --> CritiqueWarn{Brain confirms?}
    CritiqueWarn -- "Yes" --> Build
    CritiqueWarn -- "No" --> Plan
    CritiqueVerdict -- "BLOCK (1st)" --> Plan
    CritiqueVerdict -- "BLOCK (2nd)" --> PlanTriage[🛑 Brain Triage:<br>Revert to PRD / Accept WARN / Abort]
    PlanTriage --> End

    subgraph builder [Phase 2: Builder Sub-Pipeline]
        Build[Step 1: lfe-builder] --> TDD[Step 2: lfe-tdd]
    end

    TDD --> ZoomOut

    subgraph inspector [Phase 3: Inspector Sub-Pipeline]
        ZoomOut[Step 1: lfe-zoom-out] --> Inspect[Step 2: lfe-inspector<br>Cycle Guard + Sub-Skill Dispatch]
        Inspect -- "Failed (1st)" --> Diagnose[Step 3: lfe-diagnose]
        Diagnose --> Build
        Inspect -- "Failed (2nd)" --> BrainTriage[🛑 Brain Triage:<br>Accept Debt / LFE-FORCE / Re-plan]
    end

    Inspect -- "Passed" --> Archive
    BrainTriage --> End

    subgraph archivist [Phase 4: Archivist Sub-Pipeline]
        Archive[Step 1: lfe-archivist]
        Archive --> SliceCheck{More slices?}
        SliceCheck -- "Yes" --> Partial[Partial Cleanup] --> Plan
        SliceCheck -- "No" --> Cleanup[Full Cleanup .plans/]
    end

    Cleanup --> HygieneGate{Hygiene due?<br>5+ sessions}
    HygieneGate -- "Not due" --> End
    HygieneGate -- "Due" --> Hygiene

    subgraph hygienephase [Phase 5: Hygiene Sub-Pipeline]
        Hygiene[Step 1: lfe-hygiene] --> ArchImprove[Step 2: lfe-improve-architecture]
    end

    ArchImprove --> End

    subgraph forcelane ["Manual override — bypass at any phase"]
        Force[LFE-FORCE] --> Patch[Direct Patch]
        Patch --> LogDebt[Log Protocol Debt]
        LogDebt --> NextSession[Next Session: Resolution]
    end

    End([Session End])
```

---

## Coordination Layer: `.plans/` as Transaction Log

Every skill writes its output to a physical file. The next skill reads that file, not the conversation.

```
.plans/
├── 01_grill_summary.md      ← Output of lfe-grill-with-docs
├── 02_prd.md                 ← Output of lfe-to-prd (reads 01)
├── 03_slices.md              ← Output of lfe-to-issues (reads 02)
├── active_plan.md            ← Output of lfe-architect for current slice (reads 03)
├── plan_critique.md          ← Output of lfe-plan-critique (4-lens pre-build review)
├── builder_done.md           ← Output of lfe-builder (crash-recovery checkpoint)
├── tdd_report.md             ← Output of lfe-tdd (reads active_plan + builder_done)
├── checks/                   ← Inspector sub-skill outputs (security/perf/complexity/dep/mutation)
│   ├── security_findings.md
│   ├── perf_findings.md
│   ├── complexity_findings.md
│   ├── dep_findings.md
│   └── mutation_findings.md
├── critique.md               ← Output of lfe-inspector (Devil's Advocate + sub-skill aggregation)
├── inspection_report.md      ← Output of lfe-inspector (status: passed | failed | escalated)
├── diagnosis_report.md       ← Output of lfe-diagnose (conditional, on 1st inspector fail only)
└── hygiene_report.md         ← Output of lfe-hygiene (every 5 sessions)
```

The frontmatter contract for these files lives in [`COORDINATION_FILES.md`](COORDINATION_FILES.md).

**Lifecycle:**
- Files are **created** as each step completes
- Files are **read** by the next step as input
- Files are **archived or deleted** only when the Archivist closes the mission
- If a session crashes → files remain → next session reads `pipeline_status.md` + existing files → resumes

---

## Phase 0: The Complexity Gate (Start of Session)
Before any work begins, `lfe-boot` orients and asks:

> *"Is this a **Major Architectural Change** (Full Pipeline) or a **Minor Fix** (Scout Mode)?"*

### 🟢 Choice A: Scout Mode (Skill-Only: `/lfe-scout`)
Use for: Typos, UI tweaks, minor content fixes, or non-architectural adjustments.
- **Activation**: The human **MUST** explicitly trigger the toolbelt via `/lfe-scout`.
- **Enforcement**: If the human requests a fix before running the skill, the agent must refuse and request the skill activation.
- **Limit**: Cannot Add/Delete/Rename files or change project structure.
- **Report**: A "Maintenance Report" must be generated upon completion.

### 🔴 Choice B: Full Pipeline (Rigorous)
Use for: New features, architectural changes, core logic edits, or complex debugging.
Proceed to **Phase 1**.

---

## Phase 1: Architect Sub-Pipeline
Each step reads the previous step's coordination file.

| Step | Skill | Input | Output | Gate |
|---|---|---|---|---|
| 1 | `/lfe-grill-with-docs` | Conversation | `.plans/01_grill_summary.md` | — |
| 2 | `/lfe-to-prd` | `01_grill_summary.md` | `.plans/02_prd.md` | — |
| 3 | `/lfe-to-issues` | `02_prd.md` | `.plans/03_slices.md` | 🛑 Human approves slices |
| 4 | `/lfe-architect` | `03_slices.md` (current slice) | `.plans/active_plan.md` | 🛑 Human approves plan |
| 4b | `/lfe-plan-critique` | `active_plan.md`, `02_prd.md`, `03_slices.md`, `.docs/` | `.plans/plan_critique.md` | 🛡 Auto-gate: PASS proceeds; WARN needs Brain; BLOCK loops back |

## Phase 2: Builder Sub-Pipeline

| Step | Skill | Input | Output |
|---|---|---|---|
| 1 | `/lfe-builder` | `active_plan.md` | Production code in `src/**` + `.plans/builder_done.md` |
| 2 | `/lfe-tdd` | `active_plan.md` + `builder_done.md` | `.plans/tdd_report.md` |

## Phase 3: Inspector Sub-Pipeline

| Step | Skill | Input | Output |
|---|---|---|---|
| 1 | `/lfe-zoom-out` | Codebase | System context map |
| 1.5 | `/lfe-inspector` Step 1.5 — Consult Plan-Critique | `plan_critique.md` | (no file) — Architect's WARN findings become **priority verification targets** for Steps 2–4; skipped on LFE-FORCE recovery |
| 2 | `/lfe-inspector` — Cycle Guard + Sub-Skill Dispatch | `tdd_report.md` *(or `PROTOCOL_DEBT.md` after LFE-FORCE)* + `.docs/quality/inspector-config.md` + `active_plan.md` (parses `## Inspector Overrides`) | `.plans/critique.md` then `.plans/inspection_report.md` |
| 2.a–e | Optional sub-skills *(opt-in via inspector-config.md; LFE-FORCE path uses a fixed subset — see below)*: `/lfe-security-check`, `/lfe-perf-check`, `/lfe-complexity-check`, `/lfe-dep-audit`, `/lfe-mutation-verify` | `builder_done.md` + changed files | `.plans/checks/<sub-skill>_findings.md` (aggregated into `critique.md`) |
| 3 | `/lfe-diagnose` (only on 1st failure of slice) | Failing behavior | `.plans/diagnosis_report.md` → back to Builder |
| —  | **2nd failure on same slice** → halt | — | `inspection_report.md` `status: escalated` + Brain triage menu (see `LOOP_ARCHITECTURE.md` Scenario 2.2) |

**LFE-FORCE recovery path** (Inspector Step 7b — when `source: .docs/quality/PROTOCOL_DEBT.md`): Sub-Skill Dispatch runs a **fixed subset** rather than reading `inspector-config.md`: always `lfe-security-check` + `lfe-complexity-check`; conditionally `lfe-dep-audit` (if hotfix touched a dependency manifest) and `lfe-perf-check` (if the debt entry flags a hot-path edit); skipped `lfe-mutation-verify`. Critical findings on PASS verification are captured to `.docs/quality/known-issues.md` by the Archivist before the debt entry resolves — the debt still clears, but the risks remain visible. See `LOOP_ARCHITECTURE.md` Scenario 3.3.

## Phase 4: Archivist Sub-Pipeline

| Step | Skill | Input | Output |
|---|---|---|---|
| 1 | `/lfe-archivist` | `inspection_report.md` | Updated docs, CHANGELOG, pipeline_status |
| 2 | Slice loop check | `03_slices.md` | Branch: Partial Cleanup (loop) or Full Cleanup (proceed) |
| 3a | Partial Cleanup *(more slices)* | Execution files | Delete `plan_critique / active_plan / builder_done / tdd_report / checks/ / critique / inspection_report / diagnosis_report`; keep `01 / 02 / 03` |
| 3b | Full Cleanup *(mission complete)* | All `.plans/` files | Delete every coordination file including `checks/` (except `hygiene_report.md`, owned by Phase 5) |

The exact file lists are enumerated in [`lfe-archivist/SKILL.md`](../../.agents/skills/lfe-archivist/SKILL.md) Step 5; `lfe-hygiene` mirrors them for orphan detection.

## Phase 5: Hygiene Sub-Pipeline (every 5 sessions)

| Step | Skill | Input | Output |
|---|---|---|---|
| 1 | `/lfe-hygiene` | Full repo | `.plans/hygiene_report.md` |
| 2 | `/lfe-improve-architecture` | `hygiene_report.md` + CONTEXT.md | Deepening opportunities |

---

## Failure Recovery
If at any point the agent becomes confused or encounters an undocumented "Black Box" in the code, it must:
1. Stop all execution.
2. Escalate to the **Architect**.
3. Run `/lfe-extract-domain` to interview the human and update the Library.

## Emergency Protocol
See `GOVERNANCE.md` for the `LFE-FORCE` break-glass rule.
