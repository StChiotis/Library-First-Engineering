# LFE User Manual: The Human Guide

Welcome to Library-First Engineering (LFE). This document is written for **you**—the human developer. 

While the rest of the `.docs/` directory is primarily written as strict protocol instructions for your AI agents, this manual strips away the theory and explains exactly how you operate this framework day-to-day.

Think of LFE like a car. The AI agents are the engine. The `.docs/` directory is the GPS. **You are the driver.** If you don't steer, the car doesn't move. If you override the GPS, the car gets lost.

---

## 0. How You Interact with LFE (Read This First)

**You describe intent in natural language. The AI translates intent into the right skill sequence.** You do not need to memorize skill names.

The framework's guarantees — crash recovery, no documentation drift, repeatable quality — depend on each skill running in the right order with the right inputs. So the design is: **the AI invokes skills; you give intent and approve at gates.**

**The five commands you ever type yourself:**

| Command | When to type it |
|---|---|
| `/lfe-boot` | At the start of every session. Always. |
| `/lfe-whats-next` | If you're not sure where you are or what comes next. |
| `/lfe-scout` | When the AI asks at the Complexity Gate and you have a minor fix. |
| `/lfe-extract-domain` | To restart Day 0 discovery on a Blank Canvas. |
| `LFE-FORCE` | Emergency hotfix keyword. |

For everything else, **talk to the AI**: *"build feature X"*, *"fix this typo"*, *"review the security of this change"*. The AI knows which skill to run based on the active persona and the pipeline state.

The Skill Glossary at the bottom of this manual is a **reference for what the AI is doing on your behalf** — not a menu for you to invoke. If you try to bypass the assembly line by typing a skill name out of sequence (e.g., `/lfe-builder` before approving a plan), the AI is instructed to refuse and route you back through the pipeline. That refusal is the framework working as designed — it's how LFE prevents you from accidentally corrupting your own pipeline state.

---

## 1. Day 0: Starting a New Project
If you have just cloned the LFE template, your repository is a "Blank Canvas." The AI doesn't know what you are building yet.

1. **Open your AI chat:**
   - **In an IDE** (Cursor, Windsurf, Cline, Antigravity, Claude Code, GitHub Copilot): the adapter file in the repo root is auto-loaded — proceed directly. *(In Claude Code, the `/lfe-*` skills also dispatch natively as commands.)*
   - **In a raw LLM** (ChatGPT, Claude.ai, etc.): paste the content of [`.agents/adapters/system_prompt.txt`](.agents/adapters/system_prompt.txt) as your first message to bootstrap LFE compliance for the session.
2. **Type the command:** `/lfe-boot`
3. **The AI will detect the Blank Canvas** and immediately trigger the `/lfe-extract-domain` skill. 
4. **The Interview:** The AI will ask you questions about your app (What does it do? Who are the users? What are the core rules?).
5. **The Result:** The AI will populate your `CONTEXT.md` and `.docs/domain/domain-knowledge.md` files. Your repository is now initialized.

---

## 2. The Daily Workflow (End-to-End)
For every new feature, refactor, or major change, you will follow this exact loop. **Rule of thumb: One feature per session.**

### Step 1: Start the Engine
At the beginning of your session, open your AI chat and type:
> `/lfe-boot`

The AI will read `pipeline_status.md` and tell you exactly where you are. 

### Step 2: The Complexity Gate
The AI will ask you: *"Is this a Major Architectural Change or a Minor Fix?"*
- **If it's a new feature or complex logic:** Answer "Major Change." The AI will start the Full Assembly Line.
- **If it's a typo, UI tweak, or simple bug:** Answer "Minor Fix" and tell the AI to run `/lfe-scout`. (See *Bypass Routes* below).

### Step 3: The Assembly Line (Major Changes)
If you are doing a Major Change, the AI will cycle through four distinct "Personas." You don't have to memorize the prompts, but you **must** know when to say "Yes."

#### 🏛️ Phase 1: The Architect (Planning)
1. **The Grill (`/lfe-grill-with-docs`):** You tell the AI what you want to build. The Architect will cross-reference your request against the existing docs and ask you probing questions to resolve any ambiguity.
2. **The PRD (`/lfe-to-prd`):** The AI synthesizes your conversation into a Product Requirements Document.
3. **The Breakdown (`/lfe-to-issues`):** The AI breaks the PRD into "Vertical Slices" (independently testable chunks of work).
   - 🛑 **HUMAN ACTION REQUIRED:** The AI will stop and ask you: *"Do you approve these slices?"* Read them. If they look good, say "Yes."
4. **The Plan (`/lfe-architect`):** The AI drafts a strict, file-by-file blueprint for the first slice.
   - 🛑 **HUMAN ACTION REQUIRED:** The AI will stop again. *"Do you approve this implementation plan?"* Read it carefully. If it is correct, say "Yes, proceed to Builder."
5. **The Pre-Build Critique (`/lfe-plan-critique`):** Before any code is written, the Architect runs a 5-lens review of the approved plan — checking that every acceptance criterion is testable, the test strategy is feasible, the plan respects domain boundaries, there is no architectural drift, and the edits leave the surrounding text coherent (no dangling references). The output is `.plans/plan_critique.md` with a verdict:
   - **PASS** — proceed to Builder automatically.
   - **WARN** — findings are advisory; the AI surfaces them and asks you to confirm before continuing. When you confirm, the AI writes a timestamp into the file's frontmatter (`brain_confirmation`) — that's the file-based signal the Builder reads, not your conversational "yes". If the session crashes between you saying "yes" and the file write, you'll be asked again — by design, because the file is the truth.
   - **BLOCK** — the plan loops back to step 4 for revision. *Max 2 revisions per slice* — the counter lives in the file's frontmatter (`revision`), so the limit survives crashes. If it still fails on the 2nd attempt, the AI asks you to choose: revert to the PRD, accept WARN, or abort the mission.

#### 🔨 Phase 2: The Builder (Coding)
1. **Implementation (`/lfe-builder`):** The Builder persona takes over and writes the actual code in `src/` based *only* on the approved plan.
2. **Testing (`/lfe-tdd`):** The Builder writes unit tests and ensures the code passes.

#### 🕵️ Phase 3: The Inspector (Verification)
1. **Verification (`/lfe-inspector`):** A new persona takes over. It acts as an independent auditor, checking the Builder's code against your core domain rules.
2. **Cycle Guard:** Before inspecting, the AI checks whether this slice has already failed once. If yes, instead of looping forever, it stops on the second failure and asks you to triage: (A) accept as known debt, (B) escalate `LFE-FORCE`, or (C) re-plan the slice from scratch.
3. **Specialist Sub-Skills (opt-in + auto-armed):** If you have enabled them in `.docs/quality/inspector-config.md`, the Inspector also dispatches specialist passes — security (OWASP Top-10), performance, complexity, dependency audit, and mutation reasoning. On any slice that touches a UI file, the **Visual Floor** additionally auto-arms a visual-render pass that prepares the changed surface for your sign-off. Each writes findings to `.plans/checks/`, then the Inspector aggregates everything into `critique.md`.
4. **The 4-Eyes Principle (The Critique):** Before approving the code, the Inspector is forced to write a `.plans/critique.md` file. It must act as a "Devil's Advocate" to actively hunt for edge cases, performance regressions, or undocumented debt. If it finds a bug (and this is the **first** failure), it sends it back to the Builder via `/lfe-diagnose`.
5. **Finalization (your call):** A passing inspection is a *technical* pass — the machine's authority over logic, baselines, and tests. The final verdict is yours.
   - 🛑 **HUMAN ACTION REQUIRED:** Verify the app works — and for a UI change, that it *looks* right. Reply **Approved** to proceed to the Archivist, or **Reject** with what is wrong.
   - **On reject**, the fix travels back through the Builder as a *rework round*: the Inspector records your note in `.plans/rework_directive.md` and re-enters Phase 2, so the fix re-verifies through the full Builder → TDD → Inspector cycle. After 5 rework rounds on one slice the AI stops and offers a safe menu (accept as a known issue / re-plan the slice / fresh mission) rather than looping forever.
   - **Visual sign-off (UI changes only) — the close condition:** the Inspector will not close a UI slice until your visual sign-off is recorded (`visual_confirmed` + `visual_signoff` in `inspection_report.md`). A green technical pass cannot see the screen, so your eyes are the gate.

#### 📚 Phase 4: The Archivist (Cleanup)
1. **Documentation Sync (`/lfe-archivist`):** The AI updates your `CHANGELOG.md`, `README.md`, and any architecture files so the docs perfectly match the new code.
2. **Loop or Close:** If there are more slices, it loops back to the Architect. If the feature is done, it deletes the temporary planning files and closes the session.

---

## 3. Bypass Routes & Emergencies
You don't need a 4-step pipeline to change a button color. 

### The Scout (`/lfe-scout`)
Use this for **Minor Fixes** (typos, UI tweaks, simple bugs touching < 3 files). 
- **How:** Boot the session, declare a "Minor Fix", and tell the AI: *"Run `/lfe-scout` to fix the padding on the login button."*
- **Constraint:** The Scout is physically forbidden from renaming files, changing architecture, or adding dependencies. 

### The Break-Glass Override (`LFE-FORCE`)
Use this for **Production Emergencies** (e.g., the server is down, you need a patch *right now*).
- **How:** Tell the AI: *"LFE-FORCE: Fix the database connection string immediately."*
- **The Catch:** The AI will do it immediately, but it will log "Protocol Debt." Your very next session *must* be an Archivist/Inspector session to properly document what you just hotfixed.

---

## 4. Session Crashes (Interrupted Sessions)
If your IDE crashes, or you run out of tokens in the middle of a pipeline, **do not panic.**
LFE uses "File-Based Coordination." Everything the AI thought about was saved to the `.plans/` directory.

**To Recover:**
1. Open a new chat window.
2. Run `/lfe-boot`.
3. The AI will scan the `.plans/` folder, realize you crashed halfway through, and say: *"Interrupted session detected. Resume from Builder? "*
4. Say "Yes." It will read the files and continue as if nothing happened.

---

## 5. Scaling: Team & Production Features
As your project grows, you can "turn on" advanced enforcement layers.

### GitHub Actions (CI/CD Enforcement)
If you are working with a team, you can enforce LFE rules in the cloud.
- **How:** Set up a GitHub Action that reads `pipeline_status.md`. If someone opens a Pull Request claiming to be the "Architect", but the PR contains edits to source code (`src/`), the Action automatically rejects the PR. (See `.docs/protocol/INDUSTRY_STANDARDS.md`).

### Model Context Protocol (MCP Tool Gateways)
LFE relies on "Persona Discipline" (telling the AI not to use certain tools). As AI agents get more autonomous, they sometimes disobey prompts.
- **The Future-Proof Solution:** We have included `.agents/permissions.json`. This is an MCP-ready manifest. 
- **How:** In advanced IDEs (like Cline) or custom agent wrappers, you can use this JSON file to *physically disable* tools. If the active persona is "Architect", the system physically revokes the "write_to_file" tool for the `src/` directory. The AI literally cannot break the rules, even if it tries. 

---

## 6. The Skill Glossary (Reference Only)
This is a **reference for what the AI does on your behalf** during the Assembly Line — not a menu for you to invoke. Section 0 lists the five commands you ever type yourself; everything below is dispatched by the framework. If you try to call an agent-only skill out of sequence, the AI is instructed to refuse and route you back through the pipeline.

### 🧭 Navigation & Utility
| Command | Purpose |
|---|---|
| `/lfe-boot` | **Always start here.** Bootstraps the session, reads `pipeline_status.md`, and detects crashes. |
| `/lfe-whats-next` | Type this if the AI gets confused. It acts as a compass to re-orient the agent to the current pipeline phase. |
| `/lfe-extract-domain` | Used on Day 0 to interview you and extract business rules into `domain-knowledge.md`. |
| `/lfe-scout` | Flyweight mode. Used to bypass the strict assembly line for minor UI tweaks or typos (max 3 files). |

### 🏛️ Phase 1: The Architect (Planning)
| Command | Purpose |
|---|---|
| `/lfe-grill-with-docs` | A deep interview where the AI challenges your ideas against existing domain knowledge. |
| `/lfe-to-prd` | Synthesizes the interview into a formal Product Requirements Document. |
| `/lfe-to-issues` | Breaks the PRD into "Vertical Slices" for you to approve. |
| `/lfe-architect` | Drafts the strict file-by-file implementation plan. |

### 🔨 Phase 2: The Builder (Coding)
| Command | Purpose |
|---|---|
| `/lfe-builder` | Executes the approved plan, modifying `src/` code. |
| `/lfe-tdd` | Runs a Red-Green-Refactor quality pass and writes tests. |

### 🕵️ Phase 3: The Inspector (Verification)
| Command | Purpose |
|---|---|
| `/lfe-zoom-out` | Instructs the AI to map out unfamiliar codebase areas before touching anything. |
| `/lfe-inspector` | Verifies the code against domain truth and writes a Devil's Advocate critique. |
| `/lfe-diagnose` | A disciplined bug-diagnosis loop used only if verification fails. |

### 📚 Phase 4 & 5: Archiving & Maintenance
| Command | Purpose |
|---|---|
| `/lfe-archivist` | Syncs documentation, updates `CHANGELOG.md`, and cleans up `.plans/`. |
| `/lfe-hygiene` | Scheduled every 5 sessions. Audits the repo for missing indexes or bloated files. |
| `/lfe-improve-architecture` | Looks for "deepening" opportunities to refactor messy code into clean abstractions. |

---

## Evidence Discipline (Trust, Verified)

LFE personas back their "done" with proof rather than optimism. The rule — shared across the Builder, the TDD pass, and the Inspector — is simple: **a completion claim is only as good as the fresh tool output that backs it.**

- **Show the receipt.** "Tests pass" means the agent pastes the actual pass/fail counts from the run it just did; "no regression" means it shows the counts held or rose. A claim from memory does not count.
- **Route by confidence.** High (a tool just verified it) → state it plainly. Medium (inferred) → state it *with* the caveat that it's inferred. Low (recalled from training) → verify first, then state.
- **Catch hallucinations early.** A short checklist makes the agent pause and verify the moment it notices a tell: referencing a file it never opened this session, quoting a number with no source, contradicting the latest tool output, or assuming a dependency exists.

**Why it matters:** this is the framework's "No Blind Trust" principle — once the Inspector's alone — generalized to every persona that makes a claim, so confident-but-unverified "it works" surfaces less often and you spend less time hand-checking. It is pure agent-instruction guidance (no new tooling); the clause is written inline in each carrying contract and skill.

---

## Summary Cheat Sheet
- **Always start with:** `/lfe-boot`
- **If you get lost:** `/lfe-whats-next`
- **For big features:** Answer "Major Change" and approve the Architect's plans.
- **For tiny tweaks:** Run `/lfe-scout`
- **For emergencies:** Say `LFE-FORCE`
- **Never:** Let the AI write code without an approved plan.
