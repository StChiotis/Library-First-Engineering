# LFE Personas — Definition of Authority

The Library-First Engineering (LFE) protocol orchestrates AI agents into four distinct roles, plus a fifth first-class persona for the human reviewer (🫵 The Brain). Each role has specific permissions, constraints, sub-pipeline skills, and output requirements.

> **Note**: Permissions defined in `.agents/permissions.json` are the enforcement contract, honored as agent discipline by default. Platforms with tool-locking (MCP gateways, runtime hooks) or CI/CD can enforce them mechanically — see [`INDUSTRY_STANDARDS.md §6`](INDUSTRY_STANDARDS.md).

---

## Persona Contracts

| Persona | Contract | Role |
|---|---|---|
| 🏛️ Architect | [architect.md](personas/architect.md) | Design solutions, draft plans |
| 🔨 Builder | [builder.md](personas/builder.md) | Execute the approved plan |
| 🕵️ Inspector | [inspector.md](personas/inspector.md) | Verify against domain truth |
| 📚 Archivist | [archivist.md](personas/archivist.md) | Sync docs, manage history |
| 🫵 Brain | [brain.md](personas/brain.md) | Human judgment, gate-keeping |
| 🚀 Scout | [scout.md](personas/scout.md) | Rapid minor fixes (Flyweight Mode) |

> **Note to AI**: Load only the contract file for your active persona — that one alone.

---

## Extended Persona Notes

### 🕵️ Inspector — Sub-Skill Dispatch
The Inspector MAY invoke specialist sub-skills during Phase 3, configured via `.docs/quality/inspector-config.md`. Sub-skills inherit the Inspector's tool and file constraints: read-only on `src/`, write access limited to `.plans/checks/`. Each sub-skill writes its own findings file; the Inspector aggregates them into `critique.md`. The Brain can override the config per-mission via a comment in `active_plan.md`. See `lfe-inspector/SKILL.md` Step 6 for dispatch protocol.

Available sub-skills (prompt-based reasoning; the five analysis sub-skills are tool-agnostic, while `lfe-visual-check` additionally renders via a preview/browser render tool with a manual fallback):
| Sub-skill | Purpose |
|---|---|
| `lfe-security-check` | OWASP Top-10 analysis |
| `lfe-perf-check` | Performance anti-pattern detection |
| `lfe-complexity-check` | Cyclomatic and cognitive complexity |
| `lfe-dep-audit` | Dependency manifest review + audit instruction |
| `lfe-mutation-verify` | Test quality via prompt-based mutation reasoning |
| `lfe-visual-check` | Renders the changed UI surface for a human visual sign-off (auto-armed by the Visual Floor on a UI-file touch; artifact-free) |

### Evidence Discipline (cross-persona)
Every claim-making persona shares one rule: a completion claim earns its place only when fresh tool output from this session backs it — "tests pass" ships with the pasted run counts, "no regression" shows the counts hold or rise, and facts come from what was just read rather than from memory. Each claim is confidence-routed (High = tool-verified → state it; Medium = inferred → state with the caveat; Low = recalled → verify first), and a hallucination-signal checklist (referencing an unopened file, quoting an unsourced number, going against the latest tool output, assuming an unconfirmed dependency) prompts a Pause → Verify → Correct. The canonical clause lives inline in the Builder, TDD, and Inspector contracts and skills; this note is the cross-persona index so the discipline stays discoverable. The Inspector's "No Blind Trust" Hard Rule is its local instance of this shared discipline.

