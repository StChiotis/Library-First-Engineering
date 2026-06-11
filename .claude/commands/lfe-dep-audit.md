---
name: lfe-dep-audit
description: Inspector sub-skill. Reviews dependency manifest files (package.json, requirements.txt, go.mod, Cargo.toml, pom.xml) changed in the current diff for risky version patterns and stale majors. Emits a human-run audit instruction rather than executing tools. Writes .plans/checks/dep_findings.md. Called by lfe-inspector when enabled in inspector-config.md.
---

> **Generated pointer — not the protocol.** This command exists so `/lfe-dep-audit` dispatches natively in Claude Code. The canonical skill lives at `.agents/skills/lfe-dep-audit/SKILL.md` (LFE-SOURCE).

Read `.agents/skills/lfe-dep-audit/SKILL.md` now and execute its protocol exactly as written — including its position-in-pipeline preconditions and its coordination-file contract. Honor the Skill Invocation Authority rules (LLM_AGENT_GUIDE §8.8): if this skill is agent-dispatched only and was typed out of sequence, refuse and route through the assembly line. Any change to skill behavior belongs in the canonical file, never in this pointer.
