# LLM Agent Guide — Library-First Engineering (LFE)

> **Canonical agent-rules source.** This is the single source of truth for AI-agent behavior in an LFE repository. The IDE adapters at the repo root (`CLAUDE.md`, `.cursorrules`, `.windsurfrules`, `.clinerules`, `.antigravityrules`, `.github/copilot-instructions.md`) and `.agents/adapters/system_prompt.txt` are pointer stubs that reference this file — do not edit them to change agent behavior; edit *here*. Projects may declare overrides in dedicated sections (`## Brain Persona Overrides`, `## Retention Policy Overrides`, etc.); the framework respects explicit overrides only.

## 1. Mission & Scope
This project follows the **Library-First Engineering (LFE)** protocol. Every AI agent working on this repo must treat documentation as the **Single Source of Truth (SSOT)** and follow a disciplined persona-based workflow with file-based coordination.

## 2. The Agnostic Protocol
Your behavior is strictly governed by the Agnostic Core documents. Before taking any action, you **MUST** ingest the current state of the project's protocol following the strict static ingestion order defined in Step 3 of [/lfe-boot](file:///.agents/skills/lfe-boot/SKILL.md) to maximize KV cache hits.

## 3. The Source-of-Truth Hierarchy & Boundaries

### 📁 Document Boundaries & Distributed Search (CRITICAL)
- **LLM/Agent Coordination (Internal State)**:
  - `.docs/README.md` (Navigation Floor Map)
  - `README.md` in any directory with 3+ files (Shelf Indexes for local navigation — always look for these first)
  - `.docs/protocol/` (Persona sub-pipelines and handoff protocol)
- **Project/Domain Development (Product)**:
  - `.docs/architecture/` (Patterns, coding standards; ADRs live as numbered sections in the single canonical [`architecture-decisions.md`](.docs/architecture/architecture-decisions.md))
  - `.docs/domain/` (Math and business rules SSOT)
  - `.docs/quality/` (CHANGELOG, known issues, protocol debt; retention rules for these files are defined in the Retention Policy section of [`GOVERNANCE.md`](.docs/protocol/GOVERNANCE.md))
  - `.docs/strategy/` (Roadmaps and goals)
  - `.docs/legal/` (Compliance)
  - `.docs/archive/` (Cold-tier home for retention-managed files; populated by the Hygiene sweep)

### 🔝 Conflict Resolution Hierarchy
When resolving conflicts, use this priority order:
1. **Legal & Compliance** – `.docs/legal/*`
2. **Domain Logic & Math** – `.docs/domain/domain-knowledge.md`
3. **Domain Language** – `CONTEXT.md`
4. **Architecture Decisions** – `.docs/architecture/architecture-decisions.md`
5. **Code Implementation** – `src/**`
6. **Planning** – `.plans/active_plan.md`


## 3.5 Multi-Context Handling

If `.docs/domain/CONTEXT-MAP.md` exists in your project:
1. Read CONTEXT-MAP.md before declaring any glossary term.
2. When a candidate term already exists in another context, prompt the Brain to classify the relationship: **shared kernel** (same meaning), **customer/supplier** (one upstream, other downstream), **translation** (concept maps but term differs), or **separate ways** (term collision, distinct concepts).
3. Record the classification in CONTEXT-MAP.md inline. ADR if hard to reverse.

If no CONTEXT-MAP.md exists, your project is single-context. Use root `CONTEXT.md` exclusively.

## 4. The Workflow (Sub-Pipelines)
1. **Orientation**: Run `/lfe-boot` to read `pipeline_status.md`, check for interrupted sessions, and load the Protocol.
2. **Complexity Gate**: Ask the human: *"Is this a Major Architectural Change or a Minor Fix?"*
3. **Execution**: Follow the persona sub-pipelines in strict order:
   - **Architect**: `/lfe-grill-with-docs` → `/lfe-to-prd` → `/lfe-to-issues` → `/lfe-architect`
   - **Builder**: `/lfe-builder` → `/lfe-tdd`
   - **Inspector**: `/lfe-zoom-out` → `/lfe-inspector` → `/lfe-diagnose` (if failed)
   - **Archivist**: `/lfe-archivist` → slice loop → cleanup
4. **Hygiene**: Scheduled every 5 sessions. Run `/lfe-hygiene` → `/lfe-improve-architecture`.
5. **Orientation Shortcut**: Run `/lfe-whats-next` at any point for instant pipeline orientation.

## 5. File-Based Coordination (CRITICAL)
**Every skill reads its input from a coordination file in `.plans/`, NOT from conversation context.** This prevents context window information loss between steps. The frontmatter schema and full registry live in [`COORDINATION_FILES.md`](.docs/protocol/COORDINATION_FILES.md).

| Step | Reads | Writes |
|---|---|---|
| `/lfe-grill-with-docs` | Conversation | `.plans/01_grill_summary.md` |
| `/lfe-to-prd` | `01_grill_summary.md` | `.plans/02_prd.md` |
| `/lfe-to-issues` | `02_prd.md` | `.plans/03_slices.md` |
| `/lfe-architect` | `03_slices.md` | `.plans/active_plan.md` |
| `/lfe-builder` | `active_plan.md` | Production code + `.plans/builder_done.md` |
| `/lfe-tdd` | `active_plan.md` + `builder_done.md` | `.plans/tdd_report.md` |
| `/lfe-inspector` | `tdd_report.md` *(or `PROTOCOL_DEBT.md` after LFE-FORCE)* | `.plans/critique.md` then `.plans/inspection_report.md` |
| `/lfe-diagnose` *(conditional)* | Failing behavior + `tdd_report.md` | `.plans/diagnosis_report.md` |
| `/lfe-hygiene` *(every 5 sessions)* | Full repo | `.plans/hygiene_report.md` |
| `/lfe-archivist` | `inspection_report.md` | Updated docs, CHANGELOG, pipeline_status.md |


Coordination files are archived/deleted ONLY by the Archivist when the mission is complete (or by the Hygiene sub-pipeline for `hygiene_report.md`). If a session crashes, the files remain for recovery.

## 6. Session Recovery
If coordination files exist in `.plans/` but `pipeline_status.md` does not show mission complete, the session was interrupted. The agent must:
1. Read `pipeline_status.md` for the last known state
2. Scan `.plans/` for which coordination files exist
3. Resume from the next step after the last written file

## 7. Bootstrapping
Every session must begin with the `/lfe-boot` command to ensure the agent is fully oriented to the current project state and can detect interrupted sessions.

## 8. Communication Protocol (Context Window Efficiency)
To preserve the context window, all AI agents are instructed via their system adapters to use extreme brevity (zero pleasantries, minimal output).
- **Detailed Answers**: When a detailed explanation or long answer is necessary, DO NOT output it into the chat. Instead, write the detailed response to a Markdown file and simply reply in the chat with a link to the file.
  - *Compliance*: Use `.plans/` for coordination files and transient analysis. Use `.docs/` ONLY for permanent, verified project knowledge.
- **Exception (The Grill Phase)**: During the Architect's planning phase (or when using `/lfe-grill-with-docs`), the agent MUST ask necessary probing questions. Even then, questions must be direct and strictly focused on resolving design dependencies.

## 8.6 The Brain Persona

The human reviewer is a first-class persona — 🫵 **The Brain — You**. The Brain is not an implicit reviewer; the framework defines a contract and Definition of Done for them, identical in shape to the AI personas. See [`PERSONAS.md`](.docs/protocol/PERSONAS.md) for the full contract (universal-at-every-gate invariants, per-gate acceptance criteria, mission-level DoD).

Project-specific overrides go under `## Brain Persona Overrides` in this file. Framework respects explicit overrides only — never implicit ones.

## 8.7 Token Budget Self-Reporting

The Archivist appends session token costs to `.docs/quality/token-budget.md` at end-of-mission. The agent should self-report rough per-phase token costs (e.g., from chat-metadata estimates) so the framework can detect drift over time. Phases that exceed +50% over their rolling average get flagged into the next session's `pipeline_status.md`.

## 9. Available Skills (16)
| Skill | Phase | Purpose |
|---|---|---|
| `/lfe-boot` | 0 | Session bootstrap and recovery |
| `/lfe-scout` | 0.5 | Minor fixes (max 3 files) |
| `/lfe-grill-with-docs` | 1.1 | Deep alignment interview + CONTEXT.md updates |
| `/lfe-to-prd` | 1.2 | PRD synthesis from grill output |
| `/lfe-to-issues` | 1.3 | Vertical slice breakdown |
| `/lfe-architect` | 1.4 | Implementation plan for current slice |
| `/lfe-builder` | 2.1 | Code implementation |
| `/lfe-tdd` | 2.2 | Red-green-refactor quality pass |
| `/lfe-zoom-out` | 3.1 | System context for unfamiliar code |
| `/lfe-inspector` | 3.2 | Verification against domain truth |
| `/lfe-diagnose` | 3.3 | Bug diagnosis loop (conditional) |
| `/lfe-archivist` | 4.1 | Documentation sync and cleanup |
| `/lfe-hygiene` | 5.1 | Structural audit |
| `/lfe-improve-architecture` | 5.2 | Deep module extraction |
| `/lfe-extract-domain` | Any | Domain knowledge rescue |
| `/lfe-whats-next` | Any | Pipeline navigation |
