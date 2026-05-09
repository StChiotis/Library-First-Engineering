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
