---
name: lfe-mutation-verify
description: Inspector sub-skill. Prompt-based mutation testing — reasons about whether existing tests would catch hypothetical code mutations. No test runner required. Writes .plans/checks/mutation_findings.md. Called by lfe-inspector when enabled in inspector-config.md. Enable only for critical or safety-sensitive modules.
---

> **Generated pointer — not the protocol.** This command exists so `/lfe-mutation-verify` dispatches natively in Claude Code. The canonical skill lives at `.agents/skills/lfe-mutation-verify/SKILL.md` (LFE-SOURCE).

Read `.agents/skills/lfe-mutation-verify/SKILL.md` now and execute its protocol exactly as written — including its position-in-pipeline preconditions and its coordination-file contract. Honor the Skill Invocation Authority rules (LLM_AGENT_GUIDE §8.8): if this skill is agent-dispatched only and was typed out of sequence, refuse and route through the assembly line. Any change to skill behavior belongs in the canonical file, never in this pointer.
