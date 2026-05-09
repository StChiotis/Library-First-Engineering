# LFE Governance — Source of Truth
 
 This document defines the rules for protecting the integrity of the project's core intelligence and maintaining a clean "Source of Truth."
 
 ---
 
 ## 🏛️ Logic Centralization
 Every LFE project must identify its "Source of Truth"—the directory or module containing core business logic, mathematical formulas, or sensitive domain rules.
 
 1. **Isolation**: Core logic should ideally be isolated from I/O, UI, and external side effects.
 2. **Explicit Logic**: No AI agent is allowed to "improvise" or "hallucinate" logic. All logic must be derived from the `.docs/domain/` documentation.
 3. **Verification**: Any change to core logic requires high test coverage and manual review by a human domain expert.

## 📚 The Library-First Rule
**Docs Win Over Code.**
- If the implementation in `src/` contradicts the documentation in `.docs/`, the code is considered "broken" or "drifting."
- The agent must either fix the code to match the docs or escalate to the Architect to update the docs (following an ADR).

## 📖 Domain Language Governance
**`CONTEXT.md` is the canonical glossary.**
- All agents must use the terms defined in `CONTEXT.md` when naming tests, writing code, or discussing architecture.
- If a term is used that conflicts with `CONTEXT.md`, the agent must call it out and resolve the conflict before proceeding.
- New terms are added to `CONTEXT.md` inline during `/lfe-grill-with-docs` sessions, following the format in `CONTEXT-FORMAT.md`.
- Architectural decisions are recorded as numbered sections inside the single canonical file `.docs/architecture/architecture-decisions.md`, following `ADR-FORMAT.md`. An ADR is only warranted when a decision is hard to reverse, surprising without context, and the result of a real trade-off.

## 📁 Coordination File Governance
**`.plans/` is the transaction log. It is sacred.**
- Every sub-pipeline step writes its output to a numbered file in `.plans/`.
- The next step reads that file as its sole input — never the conversation.
- Coordination files are created by their respective skills and deleted ONLY by the Archivist when the mission is complete.
- **No agent may delete coordination files prematurely.** If files are found orphaned, the Hygiene audit will flag them.
- `pipeline_status.md` tracks which coordination files exist and which steps are complete.

## 🧹 Contextual Hygiene
To prevent "Spaghetti Decay" and context window bloat:
1. **Shelf Indexes**: Every folder with 3 or more files must have a `README.md` index at the top of its files to allow agents to "scout" without reading every file.
2. **The Rolling Window**: Stale history in `CHANGELOG.md` or mission logs must be moved to `.docs/archive/` periodically.
3. **Implicit Confidence**: No file should exist in the repository that is not indexed or referenced in the Library System.
4. **Architecture Sweeps**: Scheduled every 5 sessions via session count in `pipeline_status.md`. Triggers `/lfe-hygiene` → `/lfe-improve-architecture`.
5. **Atomic Docs**: Any documentation file exceeding ~6,000 characters (~1,500 tokens) must be split into smaller atomic files to preserve context window economics. *(Exemption: Root-level orchestration files like `README.md` and `LLM_AGENT_GUIDE.md` are exempt from this rule to preserve cohesive onboarding).*

---

## 📦 Retention Policy

Sessions are the unit of time across LFE — not calendar months. Each retention-managed file has a **hot tier** (active, current) and a **cold tier** (archive). The Hygiene sweep (every 5 sessions) walks this table and moves aged-out entries from hot to cold.

| Document | Hot retention | Cold home | Archive trigger |
|---|---|---|---|
| `.docs/quality/CHANGELOG.md` | 7 most recent milestones *(existing rule)* | `.docs/archive/changelog-history.md` | When 8th milestone ships |
| `.docs/architecture/architecture-decisions.md` | All `accepted` / `proposed` ADRs + last 15 sessions of `superseded` / `deprecated` | `.docs/archive/architecture-decisions-history.md` | At hygiene sweep (every 5 sessions) |
| `.docs/quality/token-budget.md` *(created in Sprint 3)* | 15 most recent sessions | `.docs/archive/token-budget-history.md` | At hygiene sweep |
| `.docs/quality/PROTOCOL_DEBT.md` | All `open` entries; `resolved` kept 1 hygiene cycle then archived | `.docs/archive/protocol-debt-history.md` | At hygiene sweep |
| `.docs/quality/known-issues.md` | All `open`; `resolved` / `won't-fix` kept 1 hygiene cycle then archived | `.docs/archive/known-issues-history.md` | At hygiene sweep |
| `.plans/` *(coordination)* | Until mission complete; archivist clears at end-of-mission | N/A | At end-of-mission |

### Index discipline (mandatory)
- **Every hot file ends with a Cold Tier Pointer** in this exact form:
  > `**Archive:** Older entries are in [archive/<filename>-history.md](../archive/<filename>-history.md). Last archive sweep: session N.`
- **Every cold file starts with a Hot Tier Pointer plus an Index table.** The pointer names the active hot file and the trigger; the index table lists every archived entry with the session it was archived in.

### Project tunability
The numeric values in this table (15, 7, 5) are framework defaults. Projects override them in their own `LLM_AGENT_GUIDE.md` under a `## Retention Policy Overrides` section. The framework respects explicit overrides; it does not respect implicit ones.

---

## 🫵 The Brain Persona
The Brain Persona is bound by the contract in [PERSONAS.md](PERSONAS.md), including its Definition of Done.

---

## 🚨 Emergency Protocol (The "Break-Glass" Rule)
In rare cases of legitimate "Production Down" emergencies, the human may bypass the assembly line.

1. **Trigger**: The human must use the command keyword `LFE-FORCE`.
2. **Action**: The agent will acknowledge the emergency and perform the requested patch immediately, bypassing the Architect/Inspector phases.
3. **The Protocol Debt**: Every `LFE-FORCE` action **MUST** be recorded in `.docs/quality/PROTOCOL_DEBT.md`.
4. **Resolution**: The very next session **MUST** be an Archivist/Inspector mission to document the change, verify the logic, and clear the Protocol Debt.

---

## 🛡️ Protocol Enforcement
Agents are instructed to reject any user request that violates these governance rules unless the `LFE-FORCE` override is invoked. The agent must respond with:
> *"I cannot perform this action. It violates LFE Logic Sovereignty. Please run `/lfe-architect` to plan this change formally, or use `LFE-FORCE` if this is a documented emergency."*
