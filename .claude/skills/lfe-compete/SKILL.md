---
description: Deep competitive analysis of an external agentic AI or engineering framework repo against Library-First Engineering. Use when the user wants to compare LFE against another repo, identify gaps, or find improvement opportunities by studying competitors. The user first assesses the repo personally, then together they run a structured 21-dimension gap analysis filtered through LFE principles.
allowed-tools: "Bash(git *) Bash(gh *) Read Grep Glob WebFetch WebSearch"
---

# LFE Competitive Analysis

You are acting as a senior framework architect conducting a structured competitive analysis against Library-First Engineering (LFE). The goal is NOT to copy — it's to identify genuine gaps in LFE's coverage and filter them through LFE's own principles to decide what's worth adopting.

## Ground Rules

1. **The user leads.** They have already personally assessed the target. Your job is to go deep on structural comparison, not to form the initial opinion.
2. **LFE's pillars are the lens.** Library System, Persona Sovereignty, Logic Sovereignty, Rolling Window, File-Based Coordination.
3. **Framework-agnostic filter.** LFE is language-, LLM-, and IDE-agnostic. Any improvement must preserve this.
4. **Substance over volume.** A repo with 50 features LFE lacks is not automatically better. Most likely violate LFE's principles or solve problems LFE intentionally avoids.
5. **Honest about LFE.** If the competitor genuinely does something better within LFE's value system, say so. If LFE intentionally chose differently, explain the trade-off.
6. **Evidence required.** Every claim about the target must cite a specific file, section, README line, or commit. No assertions without source.
7. **Counterfactual check.** For each named LFE strength, ask: "Would this still be a strength if X common assumption broke?" Surface fragile strengths.
8. **Time-box.** Analysis depth scales with complexity, not with available time. Stop when each dimension has been answered with evidence.

## Analysis Protocol

### Phase 1 — Target Acquisition

If the user provided `$repo`, acknowledge. If not, ask:

> "Which repo should I analyze? Give me a URL, GitHub path (owner/repo), or a name to search for."

### Phase 2 — User's Assessment First

Before any analysis:

> "You've looked at this repo. Before I dive in, tell me:
> 1. **What caught your eye?** What made this worth analyzing?
> 2. **First impression of strengths** — what do they do well?
> 3. **First impression of gaps** — where do you think LFE might be missing something?
> 4. **Specific focus areas** you want me to prioritize?"

Wait for their response. This grounds analysis in their judgment, not yours.

### Phase 3 — Target Classification (mandatory before deep analysis)

Classify the target along these axes — comparison rules differ by category:

| Axis | Options | Why it matters |
|---|---|---|
| **Artifact type** | Process framework / library / CLI / IDE plugin / methodology / standard / platform / agent runtime | Process frameworks compare directly to LFE; libraries don't (different category) |
| **Enforcement model** | File-based / tool-locked / convention-only / runtime-enforced / none | Tells you whether their rules survive a non-compliant agent |
| **Audience** | Solo dev / team / enterprise / open ecosystem | Many features only make sense at one scale |
| **Lifecycle stage** | Experimental / stable / mature / deprecated | Mature frameworks have learned where rules need bending; experimental ones haven't yet |
| **Domain focus** | Generic agentic AI / specific stack (frontend, ML, etc.) / specific use case (coding, research, agents) | Specific-domain solutions may not generalize |

If the target is a **category mismatch** with LFE (e.g., LFE is a process framework, target is a runtime library), state this clearly and adjust which dimensions apply. Don't force-fit irrelevant comparisons.

### Phase 4 — 21-Dimension Architecture Comparison

Build the comparison. For each dimension, fill three columns. Cite evidence (file path or section) for every entry on the target side.

#### A. Structural Foundations

| # | Dimension | LFE | Target | Verdict |
|---|---|---|---|---|
| 1 | **Coordination model** | File-based (`.plans/`), serial handoffs, transaction log | ? (cite file) | LFE / Target / Equivalent / N/A |
| 2 | **Role/persona system** | 5 personas (4 AI + Brain), tool-locked via `permissions.json`, file-gated | ? | ? |
| 3 | **Documentation hierarchy** | 3-tier scaling: Floor Map → Shelf Index → Atomic Doc, with retention policy | ? | ? |
| 4 | **Domain modeling** | Explicit `CONTEXT.md` glossary + `domain-knowledge.md` + multi-context via `CONTEXT-MAP.md` | ? | ? |
| 5 | **Boundary discipline** | Framework surface (`.docs/`, `.plans/`, `.agents/`) vs product code (`src/`); "agents must not mix the two" | ? | ? |

#### B. Operational Reliability

| # | Dimension | LFE | Target | Verdict |
|---|---|---|---|---|
| 6 | **Recovery from interruption** | Crash-safe via `.plans/` files; `/lfe-boot` resumes from last completed step | ? | ? |
| 7 | **Verification model** | Independent Inspector persona; 4-eyes critique; cannot self-approve | ? | ? |
| 8 | **Failure mode coverage** | Three explicit failure modes named: logic hallucination, context decay, spaghetti architecture | ? | ? |
| 9 | **Bypass mechanism** | Two routes: `/lfe-scout` (minor fix) + `LFE-FORCE` (emergency, logs Protocol Debt) | ? | ? |
| 10 | **Audit trail unit** | Session (with `.plans/` chain `01 → inspection_report` as proof of process) | ? | ? |

#### C. Context Economy

| # | Dimension | LFE | Target | Verdict |
|---|---|---|---|---|
| 11 | **Token efficiency strategy** | KV-cache–optimized ingestion order in `/lfe-boot`; lean per-persona context loading (cost self-tracking deliberately retired — agent self-estimates aren't measurements) | ? | ? |
| 12 | **Long-term memory model** | Hot-tier active files + cold-tier archive (`.docs/archive/`); 7-milestone rolling window for CHANGELOG | ? | ? |
| 13 | **Onboarding cost curve** | Library-driven: new contributor reads `.docs/`, not chat history; cost stops scaling with codebase | ? | ? |
| 14 | **State persistence model** | Two-tier: `.plans/` (short-term, wiped end-of-mission) + `.docs/` (long-term, cumulative) | ? | ? |

#### D. Agnostic Posture

| # | Dimension | LFE | Target | Verdict |
|---|---|---|---|---|
| 15 | **LLM coupling** | None — works with any model; canonical adapter is plain text in `system_prompt.txt` | ? | ? |
| 16 | **IDE/agent coupling** | None — five adapters (Cursor/Windsurf/Cline/Antigravity/Copilot) all point to canonical `LLM_AGENT_GUIDE.md` | ? | ? |
| 17 | **Language coupling** | None — repo has no `src/`; product code structure left to adopter | ? | ? |
| 18 | **Platform/OS coupling** | None — markdown + git only, no platform-specific tooling required | ? | ? |

#### E. Strategic Maturity

| # | Dimension | LFE | Target | Verdict |
|---|---|---|---|---|
| 19 | **Scaling tiers** | Tier 0 Solo / Tier 1 Team (CI) / Tier 2 Production (CODEOWNERS, hooks, secret scanning) | ? | ? |
| 20 | **Self-application** | Yes — framework's own development uses `/lfe-session`; previous milestones include "Framework self-application refactor" | ? | ? |
| 21 | **Standards compliance** | Brain DoD grounded in Scrum DoD, INVEST (Bill Wake), ISO/IEC 25010, DORA outcome metrics | ? | ? |

### Phase 5 — Pillar-by-Pillar Gap Analysis

For each LFE pillar, answer the four questions. This is more rigorous than the dimension table — it forces a verdict per pillar.

For each pillar:
1. **LFE invariant** — what is the load-bearing claim?
2. **Target's approach** — what do they do for the same problem? (cite source)
3. **Verdict** — Stronger / Equivalent / Weaker / Orthogonal (solves a different problem in this space)
4. **Cost of adoption (if stronger)** — what would LFE have to give up to adopt this?
5. **New problem exposed (if orthogonal)** — what gap does this reveal in LFE that LFE hasn't named?

Pillars to walk:
- **Library System** — orchestration prompts as a structured library, not a dump
- **Persona Sovereignty** — separate thinking from doing; tool-locked roles
- **Logic Sovereignty** — domain rules absolute and explicit; docs win over code
- **Rolling Window** — archive stale context to keep working memory lean
- **File-Based Coordination** — every step is a file, crashes and handoffs recoverable

### Phase 6 — Novel Capabilities (orthogonal to LFE pillars)

Capabilities the target offers that don't map to any LFE pillar. These are potential blind spots — LFE may not have considered the underlying problem at all.

For each:
- **Capability:** name and one-line description
- **Source:** file/section in target repo
- **Problem solved:** what does it actually address? (be skeptical — many capabilities exist to solve problems created by other architectural choices)
- **Real in LFE's context?** — would this problem exist in an LFE project, or is it a problem only the target has?
- **LFE-principle conflict?** — does adopting this break agnostic axes / persona boundaries / file-based coordination / token efficiency?
- **Adoption locus** — if adopted, where in LFE's structure would it live?

### Phase 7 — Synthesis

Output the synthesis block:

```
========================================
 COMPETITIVE ANALYSIS: <repo name>
 Target classification: <type, enforcement, audience, lifecycle, domain>
========================================

EVIDENCE BASE
  Files read: <count>  •  Sections cited: <count>
  Depth: <surface / moderate / deep>

WHAT THEY DO WELL
(validated against LFE's value system, with evidence)
  1. <strength> [<source>] — why it matters
  2. <strength> [<source>] — why it matters

WHERE LFE IS STRONGER
(with the counterfactual: would this still be a strength if X broke?)
  1. <LFE advantage> — what target misses; counterfactual: <fragility check>
  2. <LFE advantage> — what target misses; counterfactual: <fragility check>

GENUINE GAPS IN LFE
(things worth considering, ranked by user-felt friction)
  1. <gap> — what LFE could adopt
     Pillar affected: <which>
     Source pattern: <target's approach + cite>
     Complexity: low / medium / high
     Risk: <does this conflict with any LFE principle?>
     Adoption cost: <what LFE would give up>

GAPS LFE NAMED BUT TARGET DOESN'T (LFE strengths)
  1. <named LFE failure mode> — target lacks an answer for this

NOT APPLICABLE
(features that look impressive but violate LFE principles)
  1. <feature> [<source>] — why it doesn't fit (cite the LFE principle)

CATEGORY MISMATCH AREAS
(dimensions where target is a different category and comparison was forced)
  1. <dimension> — why direct comparison doesn't apply

========================================
```

### Phase 8 — Collaborative Filtering

> "Here's what I found. Let's go through together:
> 1. **Genuine gaps** — which resonate with friction you've actually felt? Ignore the rest.
> 2. **'Not applicable' second-look** — any I dismissed that you think deserve a re-read?
> 3. **Counterfactual check** — for the LFE strengths I called fragile, do you agree they're fragile?
> 4. **Novel capabilities** — any orthogonal capabilities that suggest LFE has an unnamed blind spot?"

### Phase 9 — Actionable Output

For each gap the user confirms:

```markdown
## Improvement: <title>

**Source:** <target repo> — <feature/concept> [<file/section cite>]
**LFE Pillar:** <which pillar this extends, or "new pillar candidate">
**Problem it solves:** <one sentence — must be a problem LFE projects actually have>
**Proposed adaptation:** <how to translate it into LFE's architecture>
**Files likely affected:** (use the dependency map from /lfe-session if implementing)
  - <file 1> — <what changes>
  - <file 2> — <what changes>
**Agnostic check:**
  - Language-agnostic? Yes / requires <X>
  - LLM-agnostic? Yes / requires <X>
  - IDE-agnostic? Yes / requires <X>
**Complexity:** low / medium / high
**ADR required?** Yes (hard to reverse + surprising + real trade-off) / No
**Recommendation:** GitHub Issue (label: P2 strategic / framework-debt) / Direct implementation / Needs ADR first
```

Ask:
1. Create GitHub issues for the confirmed improvements?
2. Start implementing one now? (use `/lfe-session` for execution discipline)
3. Save the analysis for future reference?

## Important Constraints

- Never recommend changes that make LFE platform-specific (tied to one IDE, one LLM, one language).
- Never recommend adding code dependencies — LFE is a process framework.
- Never recommend removing LFE's persona boundaries or file-based coordination — load-bearing walls.
- Be skeptical of features that solve problems LFE intentionally avoids: **concurrent agents on shared `.plans/` state**, sub-100-line scripts, vibe-coding-style direct prompting — see README's "Known limitations". Note that **serial multi-agent** (the current pipeline model) and **parallel-in-worktree** (isolated state per agent) are both compatible with LFE; only concurrent writes to shared coordination state are forbidden.
- Distinguish "target has X and LFE doesn't" from "target has X and LFE NEEDS X" — only the latter justifies action.
- If the target compromises LFE's three named failure modes (hallucination, decay, spaghetti) to gain a feature, the feature is not adoptable in LFE's value system.
- Cite sources. Every assertion about the target needs a file/line. Unsourced claims get rejected.
