# 🏛️ LFE Mission Control (Entrance Card)

| Category | Status / Value |
| :--- | :--- |
| **Integrity Score** | 🟢 [Integrity: 100%] |
| **Mission State** | [BLANK CANVAS] — fresh LFE template; no product domain loaded yet. Run `/lfe-boot`; on a blank canvas it routes straight to `/lfe-extract-domain` to capture your product's domain (Day 0). |
| **Active Persona** | — _(none — awaiting Day-0)_ |
| **Active Mission** | (none — run `/lfe-boot` → `/lfe-extract-domain` to define your first mission) |
| **Pipeline Phase** | (none — Day 0) |
| **Coordination Files** | 01 ⬜  02 ⬜  03 ⬜  plan ⬜  plan_critique ⬜  build ⬜  tdd ⬜  critique ⬜  inspect ⬜  *(clean)* |
| **Session Count** | 0 |
| **Last Architecture Sweep** | none yet (fresh template) |
| **Authorized Scope** | (none) — widened only while a mission is in-flight; reset to (none) at close (see `LLM_AGENT_GUIDE.md` §10) |

---

## 🎯 Current Mission
> *"**Welcome to your new LFE project.** This is a blank-canvas template: the Library-First Engineering framework — the personas, the Architect → Builder → Inspector → Archivist assembly line, and the full skill set — is fully wired, but no product domain is loaded yet. To begin, run `/lfe-boot`; on a blank canvas it routes straight to `/lfe-extract-domain`, which interviews you about your product and seeds the domain library (`CONTEXT.md`, `.docs/domain/`). From there the assembly line drives every change: plan in `.plans/`, build, verify, archive. New here? Read [USER_MANUAL.md](USER_MANUAL.md) for day-to-day usage; [LLM_AGENT_GUIDE.md](LLM_AGENT_GUIDE.md) is the agent protocol and the file-based coordination layer."*

### 📋 Active Constraints
1. **Docs-First**: `.docs/` is the Source of Truth.
2. **Logic Sovereignty**: Core logic is sacred.
3. **No Cowboy Coding**: All Major Changes require `.plans/active_plan.md`.
4. **Persona Discipline**: Tool-locking is active.
5. **Human Approval**: Two structural gates — slice approval + plan approval. Plus the `/lfe-plan-critique` auto-gate (PASS proceeds; WARN requires file-recorded `brain_confirmation`; BLOCK loops back, max 2 revisions).
6. **File-Based Coordination**: Skills read/write `.plans/` files, not conversation.
7. **Hygiene Scheduling**: Architecture sweep every 5 sessions.
8. **Skill Invocation**: Skills are agent-dispatched. Brain types only `/lfe-boot`, `/lfe-whats-next`, `/lfe-scout`, `/lfe-extract-domain`, or `LFE-FORCE` (see `LLM_AGENT_GUIDE.md` §8.8).

---

## 🧭 Navigation
- **Floor Map**: [.docs/README.md](.docs/README.md)
- **Protocol**: [.docs/protocol/](.docs/protocol/)
- **User Manual**: [USER_MANUAL.md](USER_MANUAL.md)
- **Domain Glossary**: [CONTEXT.md](CONTEXT.md) *(template until Day-0 populates it)*
- **Glossary Extension**: [.docs/domain/glossary.md](.docs/domain/glossary.md)
- **What's Next**: Run `/lfe-whats-next` for instant orientation
