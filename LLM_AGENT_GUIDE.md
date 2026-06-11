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
   - **Architect**: `/lfe-grill-with-docs` → `/lfe-to-prd` → `/lfe-to-issues` → `/lfe-architect` → `/lfe-plan-critique` *(🛡 auto-gate: PASS / WARN / BLOCK; max 2 plan revisions)*
   - **Builder**: `/lfe-builder` → `/lfe-tdd`
   - **Inspector**: `/lfe-zoom-out` → `/lfe-inspector` *(includes Cycle Guard + Sub-Skill Dispatch — see `.docs/quality/inspector-config.md`)* → `/lfe-diagnose` (if first failure)
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
| `/lfe-plan-critique` | `active_plan.md`, `02_prd.md`, `03_slices.md`, `.docs/` | `.plans/plan_critique.md` *(verdict: PASS / WARN / BLOCK)* |
| `/lfe-builder` | `active_plan.md` *(plus `diagnosis_report.md` on retry after failed inspection)* | Production code + `.plans/builder_done.md` |
| `/lfe-tdd` | `active_plan.md` + `builder_done.md` | `.plans/tdd_report.md` |
| `/lfe-security-check` *(opt-in sub-skill)* | `builder_done.md`, changed files | `.plans/checks/security_findings.md` |
| `/lfe-perf-check` *(opt-in sub-skill)* | `builder_done.md`, changed files | `.plans/checks/perf_findings.md` |
| `/lfe-complexity-check` *(opt-in sub-skill)* | `builder_done.md`, changed files | `.plans/checks/complexity_findings.md` |
| `/lfe-dep-audit` *(opt-in sub-skill)* | `builder_done.md`, manifest files | `.plans/checks/dep_findings.md` |
| `/lfe-mutation-verify` *(opt-in sub-skill)* | `builder_done.md`, impl + test files | `.plans/checks/mutation_findings.md` |
| `/lfe-inspector` | `tdd_report.md` + `.plans/checks/*.md` *(or `PROTOCOL_DEBT.md` after LFE-FORCE)* | `.plans/critique.md` then `.plans/inspection_report.md` |
| `/lfe-diagnose` *(conditional, 1st failure only)* | Failing behavior + `tdd_report.md` | `.plans/diagnosis_report.md` |
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

## 8.7 Self-Measurement

The agnostic core keeps **no self-measurement log**. An earlier per-session token-cost tracker was retired: it recorded agent self-estimates — not measurements — and so promised drift detection it could not honestly deliver. Adopters who want real cost telemetry should source it from their platform's billing/usage surfaces; platform distributions of LFE may add measured quality signals on top of the protocol.

## 8.8 Skill Invocation Authority (CRITICAL)

Skills are **dispatched by the framework**, not by the Brain. Each persona's sub-pipeline (or another skill via chaining) is responsible for invoking the next skill at the right moment, reading the coordination file the previous step left behind. The Brain interacts with you in natural-language intent ("build X", "fix Y", "is this safe?") and at the explicit human-approval gates — they do not type implementation skills directly.

**Brain-typeable skills** (the only ones a human may invoke directly):
| Skill | When |
|---|---|
| `/lfe-boot` | Start or resume any session — always the first thing |
| `/lfe-whats-next` | Re-orient when lost |
| `/lfe-scout` | Declare a Minor Fix at the Complexity Gate |
| `/lfe-extract-domain` | Restart Day 0 discovery |
| `LFE-FORCE` | Emergency break-glass keyword (not a slash command) |

**Agent-only skills** (the framework dispatches these from within the assembly line): every other skill in §9 below — Architect sub-pipeline (`/lfe-grill-with-docs`, `/lfe-to-prd`, `/lfe-to-issues`, `/lfe-architect`, `/lfe-plan-critique`), Builder sub-pipeline (`/lfe-builder`, `/lfe-tdd`), Inspector sub-pipeline (`/lfe-zoom-out`, `/lfe-inspector`, `/lfe-diagnose`), all Inspector specialist sub-skills (`/lfe-security-check`, `/lfe-perf-check`, `/lfe-complexity-check`, `/lfe-dep-audit`, `/lfe-mutation-verify`), Archivist (`/lfe-archivist`), Hygiene (`/lfe-hygiene`, `/lfe-improve-architecture`).

**Refusal protocol**: if the Brain types an agent-only skill out of sequence — `/lfe-builder` with no approved plan, an Inspector sub-skill outside dispatch context, `/lfe-archivist` with no `inspection_report.md` — refuse and route them through the assembly line. Respond with: *"This skill is dispatched by the framework, not invoked manually. Tell me what you want to accomplish and I'll run the correct sub-pipeline."*

**Why this rule exists**: the assembly line's guarantees (crash recovery, no-drift, file-based handoff) depend on every skill reading a coordination file the previous step wrote in the correct order. A skill invoked out of sequence reads stale or absent inputs, produces orphaned artifacts in `.plans/`, and breaks the next `/lfe-boot`'s ability to resume. Manual invocation of internal skills is the single biggest way users accidentally corrupt LFE's state.

## 9. Available Skills (22)
| Skill | Phase | Purpose |
|---|---|---|
| `/lfe-boot` | 0 | Session bootstrap and recovery |
| `/lfe-scout` | 0.5 | Minor fixes (max 3 files) |
| `/lfe-grill-with-docs` | 1.1 | Deep alignment interview + CONTEXT.md updates |
| `/lfe-to-prd` | 1.2 | PRD synthesis from grill output |
| `/lfe-to-issues` | 1.3 | Vertical slice breakdown |
| `/lfe-architect` | 1.4 | Implementation plan for current slice |
| `/lfe-plan-critique` | 1.5 | Pre-build 5-lens plan critique (AC, test feasibility, domain, structural, coherence) |
| `/lfe-builder` | 2.1 | Code implementation |
| `/lfe-tdd` | 2.2 | Red-green-refactor quality pass |
| `/lfe-zoom-out` | 3.1 | System context for unfamiliar code |
| `/lfe-inspector` | 3.2 | Verification against domain truth (with Cycle Guard + sub-skill dispatch) |
| `/lfe-security-check` | 3.2.a | Inspector sub-skill — OWASP Top-10 prompt analysis |
| `/lfe-perf-check` | 3.2.b | Inspector sub-skill — performance anti-pattern analysis |
| `/lfe-complexity-check` | 3.2.c | Inspector sub-skill — cyclomatic/cognitive complexity |
| `/lfe-dep-audit` | 3.2.d | Inspector sub-skill — dependency manifest review + Brain-run audit instruction |
| `/lfe-mutation-verify` | 3.2.e | Inspector sub-skill — prompt-based mutation reasoning for test quality |
| `/lfe-diagnose` | 3.3 | Bug diagnosis loop (conditional, 1st failure only) |
| `/lfe-archivist` | 4.1 | Documentation sync and cleanup |
| `/lfe-hygiene` | 5.1 | Structural audit |
| `/lfe-improve-architecture` | 5.2 | Deep module extraction |
| `/lfe-extract-domain` | Any | Domain knowledge rescue |
| `/lfe-whats-next` | Any | Pipeline navigation |

## 10. Project-Specific Bindings

> Populated per-project after the Day-0 domain extraction. Run `/lfe-extract-domain` to produce `CONTEXT.md` and `.docs/domain/domain-knowledge.md`, then record your project's bindings here.

- **Source of Truth:** see `CONTEXT.md` (repo root) and `.docs/domain/domain-knowledge.md`.
- **Logic Sovereignty:** define the canonical computation boundary (the Main Engine) for this project.
- **Domain / Boundary separation:** define the interface contract between domain logic and infrastructure.
- **Mental model (optional):** add a project-specific orientation analogy here if it helps agents ramp up faster.
