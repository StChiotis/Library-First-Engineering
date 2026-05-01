# LLM Agent Guide — Library-First Engineering (LFE)

## 1. Mission & Scope
This project follows the **Library-First Engineering (LFE)** protocol. Every AI agent working on this repo must treat documentation as the **Single Source of Truth (SSOT)** and follow a disciplined persona-based workflow.

## 2. The Agnostic Protocol
Your behavior is strictly governed by the Agnostic Core documents. Before taking any action, you **MUST** ingest the current state of the project's protocol:
1. **[PERSONAS.md](file:///.docs/protocol/PERSONAS.md)**: Identify your tool permissions and constraints.
2. **[ASSEMBLY_LINE.md](file:///.docs/protocol/ASSEMBLY_LINE.md)**: Follow the Complexity Gate and Handoff Gate.
3. **[GOVERNANCE.md](file:///.docs/protocol/GOVERNANCE.md)**: Protect Logic Sovereignty.

## 3. The Source-of-Truth Hierarchy
When resolving conflicts, use this priority order:
1. **Legal & Compliance** – `.docs/legal/*`
2. **Domain Logic & Math** – `.docs/domain/domain-knowledge.md`
3. **Architecture** – `.docs/architecture/architecture-decisions.md`
4. **Code Implementation** – `src/**`
5. **Planning** – `.plans/active_plan.md`

## 4. The Workflow
1. **Orientation**: Run `/lfe-boot` to read `pipeline_status.md` and the Protocol.
2. **Complexity Gate**: Ask the human: *"Is this a Major Architectural Change or a Minor Fix?"*
3. **Execution**: Follow the persona assembly line (Architect → Builder → Inspector → Archivist).
4. **Hygiene**: Run `/lfe-hygiene` (manually triggered) to ensure zero protocol drift.

## 5. Bootstrapping
Every session must begin with the `/lfe-boot` command to ensure the agent is fully oriented to the current project state.

## 6. Communication Protocol (Context Window Efficiency)
To preserve the context window, all AI agents are instructed via their system adapters to use extreme brevity (zero pleasantries, minimal output).
- **Detailed Answers**: When a detailed explanation or long answer is necessary, DO NOT output it into the chat. Instead, write the detailed response to a Markdown file and simply reply in the chat with a link to the file.
  - *Compliance*: Use `.plans/` for short-lived, transient analysis (debug logs, scratchpads). Use `.docs/` ONLY for permanent, verified project knowledge.
- **Exception (The Grill Phase)**: During the Architect's planning phase (or when using `/lfe-grill-me`), the agent MUST ask necessary probing questions. Even then, questions must be direct and strictly focused on resolving design dependencies, avoiding conversational filler.
