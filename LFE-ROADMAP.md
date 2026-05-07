# LFE Evolution Roadmap

This document tracks the planned improvements for the Library-First Engineering (LFE) framework. It is divided into **Immediate Mechanical Patches** (to fix identified logic gaps) and **Strategic Foresight** (to handle long-term scaling).

---

## 🔴 Priority 1: Mechanical Loop Seals (Reliability)
*These improvements resolve critical gaps in the "File-Based Coordination" model. They are scheduled for immediate implementation to ensure 100% crash recovery and deadlock prevention.*

1. **`lfe-builder` (Crash Recovery):** Force the Builder to physically write `builder_done.md` upon completion. Currently, a crash between coding and testing causes the AI to duplicate its work.
2. **`lfe-archivist` (The Slice Trap):** Update loop logic to enforce a "Partial Cleanup" (delete execution files, keep planning files `01`, `02`, `03`) between slices.
3. **`lfe-diagnose` (The Ghost Gap):** Force the diagnosis loop to write a physical `diagnosis_report.md` so the "Why" of a bug fix survives a session crash.
4. **`lfe-inspector` (The LFE-FORCE Deadlock):** Add an explicit mechanical exception allowing the Inspector to verify `PROTOCOL_DEBT.md` directly, preventing the system from locking up when no TDD report exists.
5. **`lfe-hygiene` (The Black Hole):** Force the Hygiene check to write a physical `hygiene_report.md`, and mandate that the Architect fixes these structural violations before performing any further refactoring.

---

## 🟡 Priority 2: Strategic Foresight (Scale & Efficiency)
*These improvements focus on the long-term maintainability of the project as the documentation library grows.*

1. **Recursive Discovery Loop:** Enable the AI to proactively identify "Knowledge Gaps" (shallow documentation) in the codebase and schedule discovery interviews automatically during the next boot.
2. **Feedback Persistence (`feedback_log.md`):** Capture human "No" responses and critiques at approval gates into a physical file so the AI learns from its mistakes across different sessions and models.
3. **Contextual Garbage Collection:** A periodic "Pruning" skill for the Archivist to move unused terms from `CONTEXT.md` to the archive, keeping active memory lean.
4. **Multi-Agent Locking System:** Implement a physical `lock` field in `pipeline_status.md` to prevent two agents (or an agent and a human) from colliding on the same planning files.
5. **Automated ADR Evolution:** Use the Hygiene sweep to detect "ADR Drift" (where code has evolved past an old architectural decision) and force an alignment session to update the project's laws.

---

> *"Reliability is our vision, efficiency the goal."*
