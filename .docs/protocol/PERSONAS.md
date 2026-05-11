# LFE Personas — Definition of Authority

The Library-First Engineering (LFE) protocol orchestrates AI agents into four distinct roles, plus a fifth first-class persona for the human reviewer (🫵 The Brain). Each role has specific permissions, constraints, sub-pipeline skills, and output requirements.

> **Note**: For IDEs supporting Model Context Protocol (MCP) or similar tool-locking, these permissions map directly to the constraints defined in `.agents/permissions.json`.

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

> **Note to AI**: Load only the contract file for your active persona. Do not load all files.

---

## Extended Persona Notes

### 🕵️ Inspector — Sub-Skill Dispatch
The Inspector MAY invoke specialist sub-skills during Phase 3, configured via `.docs/quality/inspector-config.md`. Sub-skills inherit the Inspector's tool and file constraints: read-only on `src/`, write access limited to `.plans/checks/`. Each sub-skill writes its own findings file; the Inspector aggregates them into `critique.md`. The Brain can override the config per-mission via a comment in `active_plan.md`. See `lfe-inspector/SKILL.md` Step 6 for dispatch protocol.

Available sub-skills (all prompt-only, tool-agnostic):
| Sub-skill | Purpose |
|---|---|
| `lfe-security-check` | OWASP Top-10 analysis |
| `lfe-perf-check` | Performance anti-pattern detection |
| `lfe-complexity-check` | Cyclomatic and cognitive complexity |
| `lfe-dep-audit` | Dependency manifest review + audit instruction |
| `lfe-mutation-verify` | Test quality via prompt-based mutation reasoning |

