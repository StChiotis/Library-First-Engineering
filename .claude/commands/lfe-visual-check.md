---
name: lfe-visual-check
description: Inspector sub-skill. Renders the changed UI surface, reasons over the result, and writes a text findings record with a human-action instruction and sign-off token — so a human visual sign-off can close a UI change. Artifact-free (saves no image files). Auto-arms on a visual-file touch; written by the Inspector at finalization. Called by lfe-inspector when armed via inspector-config.md.
---

> **Generated pointer — not the protocol.** This command exists so `/lfe-visual-check` dispatches natively in Claude Code. The canonical skill lives at `.agents/skills/lfe-visual-check/SKILL.md` (LFE-SOURCE).

Read `.agents/skills/lfe-visual-check/SKILL.md` now and execute its protocol exactly as written — including its position-in-pipeline preconditions and its coordination-file contract. Honor the Skill Invocation Authority rules (LLM_AGENT_GUIDE §8.8): if this skill is agent-dispatched only and was typed out of sequence, refuse and route through the assembly line. Any change to skill behavior belongs in the canonical file, never in this pointer.
