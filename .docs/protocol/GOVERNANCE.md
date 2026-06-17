# LFE Governance — Source of Truth
 
 This document defines the rules for protecting the integrity of the project's core intelligence and maintaining a clean "Source of Truth."
 
 ---
 
 ## 🏛️ Logic Centralization
 Every LFE project must identify its "Source of Truth"—the directory or module containing core business logic, mathematical formulas, or sensitive domain rules.
 
 1. **Isolation**: Core logic should ideally be isolated from I/O, UI, and external side effects.
 2. **Explicit Logic**: Every AI agent derives all logic from the `.docs/domain/` documentation, stating the documented rule rather than improvising or hallucinating one.
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
- The next step reads that file as its sole input — the file stands in for the conversation.
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
6. **Portable Paths**: Keep dev-local personal paths out of committed narrative such as the `pipeline_status.md` entrance card — refer to the repository root as `<project-root>` rather than a machine-specific home directory, so the record stays portable across clones.

---

## 🗣️ Instruction-Voice Convention

Agent-facing instruction text — skills, protocol and persona contracts, adapters, the agent guide — is written in **positive/directive voice**: state what to do, subordinating what to avoid (a prohibition leaves the desired behavior unspecified, and a model can fixate on the negated token). Load-bearing negation (a genuine hard limit, a contract-critical prohibition) keeps its stark form deliberately, case by case. New framework text is reviewed against this convention.

---

## 📦 Retention Policy

Sessions are the unit of time across LFE — not calendar months. Each retention-managed file has a **hot tier** (active, current) and a **cold tier** (archive). The Hygiene sweep (every 5 sessions) walks this table and moves aged-out entries from hot to cold.

| Document | Hot retention | Cold home | Archive trigger |
|---|---|---|---|
| `.docs/quality/CHANGELOG.md` | 7 most recent milestones *(existing rule)* | `.docs/archive/changelog-history.md` | When 8th milestone ships |
| `.docs/architecture/architecture-decisions.md` | All `accepted` / `proposed` ADRs + last 15 sessions of `superseded` / `deprecated` | `.docs/archive/architecture-decisions-history.md` | At hygiene sweep (every 5 sessions) |
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

## 🔄 Correction Cycle Limits

To prevent infinite loops on irreducible failures, three cycle limits are protocol-enforced:

1. **Pre-build critique cycles** — Max **2 plan revisions** per slice on `BLOCK` verdicts from `/lfe-plan-critique`. The counter is **file-based**: stored in the `revision:` typed frontmatter field of `.plans/plan_critique.md` (schema in [`COORDINATION_FILES.md`](COORDINATION_FILES.md)). Step 0 of `/lfe-plan-critique` reads this field on every invocation — on a 2nd BLOCK (`revision: 2`), the skill halts and presents the Brain with three triage options (revert to PRD / accept WARN with file-recorded `brain_confirmation` / abort mission) instead of running lenses again. A crash between attempts does not reset the counter. See [`LOOP_ARCHITECTURE.md`](LOOP_ARCHITECTURE.md) Scenario 1.4.
2. **Post-build inspection cycles** — Max **2 consecutive failed inspections** per slice. On the 2nd failure, the Inspector does NOT re-trigger `/lfe-diagnose`; it halts and presents three triage options (accept as known debt / escalate LFE-FORCE / re-plan from scratch). See [`LOOP_ARCHITECTURE.md`](LOOP_ARCHITECTURE.md) Scenario 2.2.
3. **Finalization-rework rounds** — Max **5 rework rounds** per slice when the Brain rejects at the Inspector's finalization gate. The counter is **file-based**: the `rework_round:` typed field of `.plans/rework_directive.md` (schema in [`COORDINATION_FILES.md`](COORDINATION_FILES.md)), advanced only when the Brain's rejection text changes (its `directive_hash`) — so distinct defects count while a crash-resumed re-run of the same rejection holds steady. This axis is **orthogonal** to limit 2: a rework round writes neither `status: failed` nor a `diagnosis_report.md`, so the inspection Cycle Guard advances on mechanical failures alone. Distinct rework rounds re-traverse Builder → `/lfe-tdd` → Inspector without false-escalating, while a *mechanical* re-failure during a round still escalates via limit 2. On the 6th rejection the Inspector halts to a safe Brain triage menu (accept as a known issue / re-plan the slice / start a fresh mission). Worst case per slice: up to 5 rework rounds, each able to run the limit-2 mechanical sub-loop. See [`LOOP_ARCHITECTURE.md`](LOOP_ARCHITECTURE.md) Scenario 2.4.

The rationale: a structural problem resists fixing by repeated tweaks at the same level. The cycle limit forces escalation to a higher level (Brain triage, plan re-design, or accepted debt) instead of loop spinning that consumes tokens without converging.

---

## 🔁 Idempotency Principle

Skills that mutate persistent state (`CHANGELOG.md`, session count, `PROTOCOL_DEBT.md`, archive files) must be **safely re-runnable from any partial state**. A crash mid-skill leaves the re-run safe to apply the mutation exactly once.

The guard goes in the skill that mutates, **when the bug it prevents is being fixed** — not preemptively across every skill. Example guards: check the topmost CHANGELOG milestone before prepending; refuse to increment a session count whose marker is already set; check `Resolution Status` before marking a debt entry resolved. The marker can be a frontmatter field, a magic-comment in the file being mutated, or a sentinel file in `.plans/`. Every project chooses what fits.

This principle exists so the framework can recover from crashes without manual cleanup. It is not an invitation to add idempotency ceremony to skills that leave persistent state untouched.

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

---

## 🚦 Discipline Gates (Drift Resistance)

LFE relies on agent discipline rather than a runtime sandbox: the rules below are obligations the agent self-enforces and the human spot-checks. They put the protocol in the path of the actions most prone to silent drift. Each is a positive obligation; a platform distribution of LFE may additionally mechanize any of them as runtime hooks or tool-gateways (see [`INDUSTRY_STANDARDS.md`](INDUSTRY_STANDARDS.md) §6).

- **C1 — Terminal git posture.** Run no mutating git verb (`commit`, `reset`, `rebase`, `cherry-pick`, `revert`, `tag`, `push`) without an active mission. `merge`, push to `main`, force-push, and a legal-anchor tag additionally need explicit human confirmation — the human types `MERGE-OK`.
- **C2a — Boot precondition.** Take no substantive `Write`/`Edit` before `/lfe-boot` has run this session; orientation precedes mutation.
- **C2b — Scout boundary.** Invoke `/lfe-scout` only at a clean session boundary — never with an in-flight mission or a live coordination trail in `.plans/`.
- **C3 — Persona transition.** Change the Active-Persona value only as a dispatched skill step (an Architect→Builder handoff, the Inspector's finalization flip), never as a free-hand edit to `pipeline_status.md`.
- **C4 — No-mission guard.** Make no substantive change at an idle / `[MISSION COMPLETE]` slate with no coordination trail — route the work through `/lfe-boot` (full mission) or `/lfe-scout` (minor fix) first.
- **Mission-aware Authorized Scope.** An in-flight mission may widen a persona's write lane through an `Authorized Scope` row on the entrance card (see [`LLM_AGENT_GUIDE.md`](../../LLM_AGENT_GUIDE.md) §10); the Archivist resets it to `(none)` at mission close. An Authorized Scope never grants a non-Builder persona write access to `src/**` — `LFE-FORCE` stays the only path to patch `src/` outside the Builder.
- **Visual floor (unconditional).** A visual change never closes on a green technical pass alone. The Inspector must obtain and record a human visual sign-off (`visual_confirmed` + `visual_signoff` in `inspection_report.md`) before flipping to the Archivist. This is the one gate stated as an absolute floor rather than a speed-bump — a UI slice that skips it ships unseen.

**The honest ceiling.** With no runtime sandbox, nothing mechanically stops an agent with shell access from bypassing a prose rule — these are speed-bumps and loudness, not containment. The framework's defense is that drift is made *visible and deliberate*: every bypass is surfaced openly and routed through `/lfe-scout` or `LFE-FORCE` so the human sees it. A platform distribution may harden any of these into runtime hooks; the agnostic core keeps them as disciplines.
