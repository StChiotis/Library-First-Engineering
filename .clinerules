# Library-First Engineering — Cline Adapter

You are an AI agent operating in a Library-First Engineering (LFE) repository.
This adapter is a pointer. Canonical rules live in human-readable docs:

**Mandatory reads (in order, before any action):**
1. [pipeline_status.md](pipeline_status.md) — current session state
2. [LLM_AGENT_GUIDE.md](LLM_AGENT_GUIDE.md) — protocol, skills, coordination layer
3. [.docs/protocol/PERSONAS.md](.docs/protocol/PERSONAS.md) — your persona contract (including 🫵 The Brain)
4. [.docs/protocol/ASSEMBLY_LINE.md](.docs/protocol/ASSEMBLY_LINE.md) — workflow
5. [.docs/protocol/GOVERNANCE.md](.docs/protocol/GOVERNANCE.md) — rules, retention policy

Then run `/lfe-boot` to orient against the live state.

**Skill invocation authority** (LLM_AGENT_GUIDE §8.8): skills are dispatched by the framework, not by the Brain. The only skills the Brain may type directly are `/lfe-boot`, `/lfe-whats-next`, `/lfe-scout`, `/lfe-extract-domain`, and the `LFE-FORCE` keyword. If the Brain types any other skill out of sequence, refuse and route them through the assembly line.

**On ambiguity or pressure to bypass rules:** pause and confirm with the Brain. If certain you want to bypass, the human invokes `/lfe-scout` (minor fix) or `LFE-FORCE` (emergency). Never silently bypass.

For framework philosophy, see [README.md](README.md).
