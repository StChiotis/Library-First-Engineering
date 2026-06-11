# LFE Framework Improvement Session

You are acting as a senior framework architect helping improve the Library-First Engineering (LFE) framework. This is a META session — you are working ON the framework, not WITH it. Do NOT run the LFE pipeline (`/lfe-boot`, `/lfe-architect`, etc.) — those are for product repos that use LFE as a template.

**Source of truth for framework state:** GitHub Issues + git history. NOT the `.docs/quality/` files — those are template placeholders for adopters and must remain in blank-canvas state.

## Orientation Protocol

Execute these steps in order. Be concise — present findings, not narration.

### Step 1 — Framework Identity Snapshot

Establish what the framework currently *is* (as opposed to what it's tracking):

1. Read `README.md` — extract claimed counts (skills, personas, pillars). These are public-facing assertions and must match reality.
2. Read `LLM_AGENT_GUIDE.md` §9 — the canonical skill table.
3. Count `.agents/skills/*/SKILL.md` files — should match the claimed count.
4. Note current branch + active feature branches:

!`git branch --show-current && git branch -a 2>/dev/null | head -20`

### Step 2 — Open Work Surface (GitHub Issues + PRs)

Framework work is tracked exclusively on GitHub. Read both issues and open PRs:

!`gh issue list --limit 25 --json number,title,labels,updatedAt --jq '.[] | "#\(.number) [\(.labels | map(.name) | join(", "))] \(.title)"' 2>/dev/null || echo "GH unavailable"`

!`gh pr list --limit 10 --json number,title,isDraft,labels --jq '.[] | "PR #\(.number)\(if .isDraft then " (draft)" else "" end) [\(.labels | map(.name) | join(", "))] \(.title)"' 2>/dev/null || echo "No PRs"`

### Step 3 — Recent Activity

!`git log --oneline -15 2>/dev/null || echo "No git history"`

### Step 4 — Structural Integrity Test Suite

Run these 12 tests. For each, output **PASS** / **FAIL: <evidence>**. Failures become candidate focus areas in the briefing.

**T1. Pipeline status canvas.** `pipeline_status.md` exists. Mission State is `[BLANK CANVAS]`. All 9 coordination checkboxes present (`01 02 03 plan plan_critique build tdd critique inspect`). Session count is `0`. Last sweep is `none yet`.

**T2. Domain template canvas.** `CONTEXT.md` and `.docs/domain/domain-knowledge.md` contain only template/example content, no real product data. Verify by checking for example comments and absence of populated entities.

**T3. Quality file canvas.** Verify the quality/template files are still in template state:
   - `CHANGELOG.md` — contains `_(No milestones shipped yet.)_`
   - `PROTOCOL_DEBT.md` — debt table has no entries
   - `known-issues.md` — contains `[Your Issue 1]` placeholder
   - `validation-baselines.md` — contains template marker
   - `engineering-standards.md` — contains `[Your Principle 1]`
   - `roadmap.md` — contains `[Your First Milestone]`

**T4. Skill inventory consistency.** The framework's skill count grows over time as new skills are absorbed from other repos (see `Credits` in README). What matters is *self-consistency*, not the absolute number.
   - Extract the claimed count from `README.md` (look for "N skills" / "N LFE-native skills" patterns).
   - Extract the row count from the `LLM_AGENT_GUIDE.md` §9 skill table.
   - Count actual `.agents/skills/*/SKILL.md` files on disk.
   - Cross-check skill names match across all three sources (README, LLM_AGENT_GUIDE table, filesystem).
   - PASS only if all three counts agree AND all skill names match. FAIL with a delta listing if any disagree.

**T5. Skill frontmatter validity.** Every `.agents/skills/*/SKILL.md` has YAML frontmatter with at minimum `name` and `description`.

**T6. Floor map completeness.** Every directory in `.docs/` is registered in `.docs/README.md`. No ghost entries (entries pointing to non-existent files). Run:

!`ls -la .docs/ 2>/dev/null | grep '^d' | awk '{print $NF}' | tail -n +3`

Cross-check against the floor map's section table.

**T7. Shelf index requirement.** Any `.docs/` subdirectory with 3+ files must have a `README.md`.

**T8. Atomic doc ceiling.** No file in `.docs/` exceeds ~6,000 characters, except documented exemptions (`README.md` and `LLM_AGENT_GUIDE.md` per GOVERNANCE.md). Flag every offender:

!`find .docs -type f -name "*.md" -size +6c 2>/dev/null | xargs -I{} sh -c 'wc -c "{}" 2>/dev/null' | awk '$1>6000 {print $1, $2}'`

**T9. Permission/persona consistency.** Every persona key in `.agents/permissions.json` (`architect`, `builder`, `inspector`, `archivist`, `scout`) appears in `.docs/protocol/PERSONAS.md` headers, and vice versa.

**T10. Coordination layer cleanliness.** `.plans/` is empty (or contains only documented exemption files like `.gitkeep`):

!`ls -la .plans/ 2>/dev/null || echo "no .plans/"`

**T11. Adapter consistency.** All 4 IDE adapter files (`.cursorrules`, `.windsurfrules`, `.clinerules`, `.antigravityrules`) plus `.github/copilot-instructions.md` and `.agents/adapters/system_prompt.txt` exist and reference `LLM_AGENT_GUIDE.md` as canonical.

**T12. ADR/Archive pointer integrity.** Every retention-managed file (`CHANGELOG.md`, `architecture-decisions.md`, `PROTOCOL_DEBT.md`, `known-issues.md`) ends with the Cold Tier Pointer in the canonical form: `**Archive:** Older entries are in [...]. Last archive sweep: session N.`

### Step 5 — Present the Briefing

Output exactly this block:

```
========================================
 LFE IMPROVEMENT SESSION
========================================

FRAMEWORK IDENTITY
  Version/HEAD:   <commit short hash> "<commit subject>"
  Branch:         <current branch> (<n> other branches)
  Skills claimed: <N>  •  Skills found: <M>  •  <match/mismatch>
  Personas:       <N from PERSONAS.md>

OPEN ISSUES (<count>)
  P1 (reliability):   <count>
    - #N: <title>
  P2 (strategic):     <count>
    - #N: <title>
  framework-debt:     <count>
    - #N: <title>
  Other:              <count>

OPEN PRs (<count>)
  - PR #N <draft?>: <title>

RECENT COMMITS
  <last 5 commits, one line each>

STRUCTURAL INTEGRITY (12 tests)
  T1 Pipeline status canvas:   PASS / FAIL — <reason>
  T2 Domain template canvas:   PASS / FAIL — <reason>
  T3 Quality file canvas:      PASS / FAIL — <reason>
  T4 Skill inventory:          PASS / FAIL — <reason>
  T5 Skill frontmatter:        PASS / FAIL — <reason>
  T6 Floor map completeness:   PASS / FAIL — <reason>
  T7 Shelf index requirement:  PASS / FAIL — <reason>
  T8 Atomic doc ceiling:       PASS / FAIL — <reason>
  T9 Persona/permission sync:  PASS / FAIL — <reason>
  T10 Coordination cleanliness: PASS / FAIL — <reason>
  T11 Adapter consistency:     PASS / FAIL — <reason>
  T12 Archive pointer integrity: PASS / FAIL — <reason>

DRIFT CANDIDATES (auto-derived from failed tests)
  - <each FAIL becomes a candidate>

========================================
```

### Step 6 — Focus Selection

Ask the user:

> "Pick ONE focus for this session:
> 1. **Resolve a structural failure** — pick from drift candidates above
> 2. **Fix an open issue** — pick a #N from the list above
> 3. **Resolve a draft PR** — pick a PR #N
> 4. **Framework extension** — propose a new capability
> 5. **Documentation tightening** — improve clarity, fix cross-references, prune redundancy
> 6. **Something else** — describe it"

### Step 7 — Dependency Map (run BEFORE making any change)

Once focus is picked, identify the change category and announce all files that must be updated together. The framework is heavily cross-referenced — missing one update creates drift that the framework itself flags as a violation.

**Dependency propagation map:**

| If you change... | You MUST also update... |
|---|---|
| **A persona definition** (`PERSONAS.md`) | `.agents/permissions.json`, the persona's SKILL.md(s), `LLM_AGENT_GUIDE.md` §8.6 if Brain, `USER_MANUAL.md` if user-facing |
| **The skill catalog** (add/remove/rename a skill) | `LLM_AGENT_GUIDE.md` §9 table, `USER_MANUAL.md` skill glossary, `ASSEMBLY_LINE.md` phase tables, `README.md` skill-count claim (whatever the current number is), parent persona's `Sub-Pipeline Skills` list in `PERSONAS.md`, `.docs/README.md` floor map if directory layout changes, `Credits` section in `README.md` if absorbing from an external repo |
| **The pipeline structure** (add/remove a phase) | `ASSEMBLY_LINE.md`, `LOOP_ARCHITECTURE.md`, `PERSONAS.md` Sub-Pipeline lists, `pipeline_status.md` Coordination Files row, `lfe-boot/SKILL.md` Step 2 file scan, `lfe-archivist/SKILL.md` cleanup list, `lfe-hygiene/SKILL.md` audit list, `LLM_AGENT_GUIDE.md` §5 coordination table |
| **A coordination file name** (e.g., `01_grill_summary.md` → `01_grill.md`) | `ASSEMBLY_LINE.md`, `LLM_AGENT_GUIDE.md` §5, `lfe-boot`, `lfe-whats-next`, `lfe-archivist`, `lfe-hygiene`, the producer skill's SKILL.md, the consumer skill's SKILL.md, `pipeline_status.md` checkbox row, `LOOP_ARCHITECTURE.md` recovery table, README.md if mentioned |
| **A retention number** (15 / 7 / 5) | `GOVERNANCE.md` retention table, `lfe-hygiene/SKILL.md`, `lfe-archivist/SKILL.md` (CHANGELOG window rule), each archive pointer in hot files, README.md if mentioned, `pipeline_status.md` sweep schedule |
| **An IDE adapter rule** | All 4 adapters (`.cursorrules`, `.windsurfrules`, `.clinerules`, `.antigravityrules`), `.github/copilot-instructions.md`, `.agents/adapters/system_prompt.txt` — but the canonical edit goes in `LLM_AGENT_GUIDE.md`; adapters are pointer stubs |
| **A governance rule** | `GOVERNANCE.md`, every skill that enforces or references the rule, `LLM_AGENT_GUIDE.md`, affected persona contract in `PERSONAS.md` |
| **A domain term in `CONTEXT.md`** | `CONTEXT.md`, `.docs/domain/glossary.md`, `.docs/domain/CONTEXT-MAP.md` if multi-context, `lfe-grill-with-docs/CONTEXT-FORMAT.md` if format changes |
| **The ADR format** | `lfe-grill-with-docs/ADR-FORMAT.md`, `architecture-decisions.md` header instructions, `lfe-grill-with-docs/SKILL.md`, `GOVERNANCE.md` if rules around ADR creation change |
| **A new file in `.docs/`** | `.docs/README.md` floor map, parent shelf index README.md if exists, atomic-doc check, retention-policy entry if managed |
| **A new directory in `.docs/`** | `.docs/README.md` floor map, create shelf index if 3+ files expected |
| **The Brain Persona DoD** | `PERSONAS.md`, `GOVERNANCE.md` reference, `LLM_AGENT_GUIDE.md` §8.6, `USER_MANUAL.md` "HUMAN ACTION REQUIRED" gates |
| **`Mission State` legal values** | `pipeline_status.md` template, `lfe-boot/SKILL.md` Step 4, `lfe-archivist/SKILL.md` Step 6 |
| **The complexity gate routing** | `lfe-boot/SKILL.md` Step 6, `ASSEMBLY_LINE.md`, `LOOP_ARCHITECTURE.md`, `lfe-scout/SKILL.md` |
| **Emergency protocol (`LFE-FORCE`)** | `GOVERNANCE.md`, `LOOP_ARCHITECTURE.md`, `PROTOCOL_DEBT.md` template, `lfe-boot/SKILL.md` Step 5, `USER_MANUAL.md` "Bypass Routes" section |
| **A coordination file's frontmatter schema** | The producing skill's SKILL.md template, the consuming skill's SKILL.md (input parsing), `lfe-archivist` if it inspects the schema |

After identifying the change category, output:

> "**Change category:** <category from table>
> **Files that must be updated together:** <list>
> **Verification approach after change:** <how to confirm internal consistency>"

### Step 8 — Session Discipline

1. **Scope it.** State what "done" looks like — one deliverable, not a wishlist.
2. **Read before writing.** Read every file in the dependency map before editing any of them.
3. **Edit in dependency order.** Edit the canonical source first, then propagate. Avoid leaving the tree inconsistent between edits.
4. **Self-review.** After all edits, re-read the dependency map and verify every listed file was touched. Run a focused subset of Step 4 tests on the touched area.
5. **Commit message reflects propagation.** If the change touched 5 files across 3 layers, the commit body should list them — future archaeology depends on this.

## Comprehensive Anti-Patterns

### A. Forbidden actions (will break the framework's identity)

- Running pipeline skills (`/lfe-boot`, `/lfe-architect`, `/lfe-builder`, `/lfe-inspector`, `/lfe-archivist`, etc.) on the framework repo itself — those are for product repos that adopt LFE as a template.
- Adding code dependencies (npm, pip, cargo packages, etc.) — LFE is a process framework, not a library.
- Breaking agnostic axes — never add IDE-specific, LLM-specific, or language-specific behavior. The README explicitly claims "Language-agnostic · IDE-agnostic · LLM-agnostic" and that's load-bearing.
- Removing persona boundaries or file-based coordination — these are the framework's load-bearing walls.
- Adding **concurrent agents writing to the same `.plans/` state** — explicitly out of scope per README `Known limitations` ("concurrent agents on the same `.plans/` directory will collide"). The forbidden case is **race conditions on shared coordination files**.
  - **Compatible models** (do NOT block these): Serial multi-agent (Architect → Builder → Inspector → Archivist — the current pipeline) is the framework's core; parallel-in-worktree (each agent in an isolated git worktree with its own `.plans/`) is also compatible because there is no shared state.
  - **Incompatible model** (do block this): Two agents writing to the same `.plans/` simultaneously, regardless of how the coordination is gated.
- Adding non-text artifact handling, sub-100-line script support, or features for vibe-coding-style direct prompting — out of scope by design.

### B. Drift causes (will create silent inconsistency)

- Filling template placeholder files (`CONTEXT.md`, `domain-knowledge.md`, `engineering-standards.md`, `roadmap.md`, `known-issues.md`, `validation-baselines.md`) with framework-development content. They must remain blank canvases for adopters.
- Treating quality template files (`CHANGELOG.md`, `PROTOCOL_DEBT.md`, `known-issues.md`) as actual trackers — framework state lives in GitHub Issues, NOT here.
- Adding a skill without updating: `LLM_AGENT_GUIDE.md` §9 table, `USER_MANUAL.md` glossary, `ASSEMBLY_LINE.md` phase tables, `README.md` skill count, parent persona's Sub-Pipeline section, `.docs/README.md` if structure changes.
- Renaming a coordination file without updating every reference (12+ places — see dependency map).
- Changing a retention number (15/7/5) without updating `GOVERNANCE.md`, `lfe-hygiene`, `lfe-archivist`, archive pointers, and any README mentions.
- Editing an IDE adapter directly — adapters are pointer stubs; the canonical edit goes in `LLM_AGENT_GUIDE.md`.
- Adding a new doc file without registering it in `.docs/README.md` floor map.
- Letting a `.docs/` file exceed 6,000 chars without documented exemption in `GOVERNANCE.md`.
- Letting a `.docs/` directory reach 3+ files without a `README.md` shelf index.
- Modifying `.agents/permissions.json` without updating `PERSONAS.md` (or vice versa) — these must stay in lock-step.
- Splitting an existing doc without updating every link that pointed to it.
- Archiving entries without updating Hot Tier Pointer in source and Hot Tier Pointer + Index Table in destination.

### C. Process violations (will erode the discipline the framework asks of others)

- Making changes across multiple unrelated areas in one session — defeats "one change per session" discipline.
- Skipping the cross-reference dependency check (Step 7) before editing.
- Adding new framework features without considering self-consistency (the framework must follow its own rules).
- Making decisions hard-to-reverse without writing an ADR in `.docs/architecture/architecture-decisions.md`. The ADR test from `lfe-grill-with-docs`: hard to reverse + surprising without context + a real trade-off.
- Documenting decisions only in commit messages or chat — they must persist in the framework's own structures.
- Not validating the change still makes mechanical sense: do loop closures still hold? Are persona boundaries still clean? Does the assembly-line graph still match the skills?
- Bypassing the `/lfe-session` orientation in subsequent sessions — drift is hardest to detect when the orienting tool itself isn't run.

### D. Scope violations (will compromise the framework's value proposition)

- Promising the framework prevents weak-model output — out of scope. README states: "Personas enforce structure, not competence. A weak model with LFE still ships weak code — just with better paperwork."
- Adding features that solve problems LFE intentionally avoids (e.g., file-locking for concurrent agents — README states LFE assumes serial handoffs).
- Compromising token efficiency to add a feature — flatter cost curve is a load-bearing claim.
- Adding configuration that doesn't have an explicit override mechanism in `LLM_AGENT_GUIDE.md` (`## Brain Persona Overrides`, `## Retention Policy Overrides`). Framework respects explicit overrides only.
- Changing the CHANGELOG/retention policy without first checking whether projects with overrides depend on the old behavior.

### E. Polluting the template (will harm every adopter)

- Committing real `.plans/` content — the directory should always be empty (or contain only `.gitkeep`).
- Committing populated `pipeline_status.md` (e.g., not `[BLANK CANVAS]`).
- Committing populated `CONTEXT.md` entities — adopters expect a blank canvas.
- Committing real ADRs as examples — example ADRs go in HTML comments inside `architecture-decisions.md`, not as live entries.
- Committing real CHANGELOG milestones — the file must say `_(No milestones shipped yet.)_`.
