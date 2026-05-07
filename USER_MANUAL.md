# LFE User Manual: The Human Guide

Welcome to Library-First Engineering (LFE). This document is written for **you**—the human developer. 

While the rest of the `.docs/` directory is primarily written as strict protocol instructions for your AI agents, this manual strips away the theory and explains exactly how you operate this framework day-to-day.

Think of LFE like a car. The AI agents are the engine. The `.docs/` directory is the GPS. **You are the driver.** If you don't steer, the car doesn't move. If you override the GPS, the car gets lost.

---

## 1. Day 0: Starting a New Project
If you have just cloned the LFE template, your repository is a "Blank Canvas." The AI doesn't know what you are building yet.

1. **Open your AI chat** (Cursor, Windsurf, Cline, or whatever agent you use).
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

#### 🔨 Phase 2: The Builder (Coding)
1. **Implementation (`/lfe-builder`):** The Builder persona takes over and writes the actual code in `src/` based *only* on the approved plan.
2. **Testing (`/lfe-tdd`):** The Builder writes unit tests and ensures the code passes.

#### 🕵️ Phase 3: The Inspector (Verification)
1. **Verification (`/lfe-inspector`):** A new persona takes over. It acts as an independent auditor, checking the Builder's code against your core domain rules. 
2. **The 4-Eyes Principle (The Critique):** Before approving the code, the Inspector is forced to write a `.plans/critique.md` file. It must act as a "Devil's Advocate" to actively hunt for edge cases, performance regressions, or undocumented debt. If it finds a bug, it sends it back to the Builder.
3. **Handoff:** If it passes the 4-Eyes check, it asks for your final blessing. 
   - 🛑 **HUMAN ACTION REQUIRED:** Verify the app works visually/functionally. Say "Approved, proceed to Archivist."

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

## 6. The Skill Glossary
While the AI usually calls these automatically during the Assembly Line, here is a complete dictionary of every skill available in the LFE framework. You can manually type these commands at any time to force the AI into a specific behavior.

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

## Summary Cheat Sheet
- **Always start with:** `/lfe-boot`
- **If you get lost:** `/lfe-whats-next`
- **For big features:** Answer "Major Change" and approve the Architect's plans.
- **For tiny tweaks:** Run `/lfe-scout`
- **For emergencies:** Say `LFE-FORCE`
- **Never:** Let the AI write code without an approved plan.
