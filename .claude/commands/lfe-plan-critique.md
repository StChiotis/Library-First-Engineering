---
name: lfe-plan-critique
description: Run a 5-lens pre-build critique of the approved active plan before the Builder starts. Acts as the Architect persona, read-only on src/. Writes .plans/plan_critique.md. Use immediately after Brain approves active_plan.md.
---

> **Generated pointer — not the protocol.** This command exists so `/lfe-plan-critique` dispatches natively in Claude Code. The canonical skill lives at `.agents/skills/lfe-plan-critique/SKILL.md` (LFE-SOURCE).

Read `.agents/skills/lfe-plan-critique/SKILL.md` now and execute its protocol exactly as written — including its position-in-pipeline preconditions and its coordination-file contract. Honor the Skill Invocation Authority rules (LLM_AGENT_GUIDE §8.8): if this skill is agent-dispatched only and was typed out of sequence, refuse and route through the assembly line. Any change to skill behavior belongs in the canonical file, never in this pointer.
