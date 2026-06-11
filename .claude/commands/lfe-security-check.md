---
name: lfe-security-check
description: Inspector sub-skill. Runs an OWASP Top-10 prompt-based security analysis on the current implementation diff. No external tooling required — pure LLM reasoning. Writes .plans/checks/security_findings.md. Called by lfe-inspector when enabled in inspector-config.md.
---

> **Generated pointer — not the protocol.** This command exists so `/lfe-security-check` dispatches natively in Claude Code. The canonical skill lives at `.agents/skills/lfe-security-check/SKILL.md` (LFE-SOURCE).

Read `.agents/skills/lfe-security-check/SKILL.md` now and execute its protocol exactly as written — including its position-in-pipeline preconditions and its coordination-file contract. Honor the Skill Invocation Authority rules (LLM_AGENT_GUIDE §8.8): if this skill is agent-dispatched only and was typed out of sequence, refuse and route through the assembly line. Any change to skill behavior belongs in the canonical file, never in this pointer.
