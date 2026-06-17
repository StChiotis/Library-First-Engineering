# LFE Industry Standards (Optional Enhancements)

The core LFE framework relies on local AI personas (Builder, Inspector) to maintain discipline. However, as projects scale or when working in team environments, enforcing these rules solely through LLM system prompts can fall short.

This document lists **industry-standard enhancements** that you can implement in your repository to enforce the LFE protocol automatically. 

> [!NOTE]
> **Why are these optional?**
> For solo founders or rapid prototypes, these configurations can introduce unnecessary overhead. Adopt these standards only when the project reaches a maturity level that requires strict, platform-level governance.

---

## 1. Automated Cloud Inspector (CI/CD)

Relying entirely on a local "Builder" agent to run tests can be risky if the agent hallucinates a passing result or skips the step.
- **Reference Standard**: GitHub Actions / GitLab CI pipelines.
- **Implementation**: Create a workflow (e.g., `.github/workflows/ci.yml`) that automatically runs your test suite and linting tools on every Pull Request.
- **LFE Benefit**: Acts as an incorruptible "Cloud Inspector" that verifies the work before it merges into the main branch.

## 2. Repository Governance Templates

Ensure that both humans and AI agents follow the LFE assembly line when submitting code or raising issues.
- **Reference Standard**: Issue and PR Templates.
- **Implementation**: Add `.github/PULL_REQUEST_TEMPLATE.md` with checklists confirming that:
  - An `.plans/active_plan.md` was followed.
  - `.plans/plan_critique.md` reached `verdict: PASS` (or `WARN` with a non-null `brain_confirmation`) before any `src/` edits.
  - Inspector phase produced a passing or explicitly-triaged `inspection_report.md`.
  - Unit tests have been written and run successfully.
  - The Archivist has updated `CHANGELOG.md` and relevant ADRs.
- **LFE Benefit**: Forces contributors (and AI agents auto-generating PR descriptions) to explicitly acknowledge LFE compliance — including the pre-build critique gate.

## 3. Zero-Trust Benchmark via CODEOWNERS

The LFE benchmark establishes a "Zero-Trust" rule for core logic. Platform-level enforcement is highly recommended for production systems.
- **Reference Standard**: GitHub `CODEOWNERS` file.
- **Implementation**: Create a `.github/CODEOWNERS` file mandating human review for changes to:
  - The designated "Engine" modules (Logic Sovereignty).
  - `.docs/` (Truth Home).
- **LFE Benefit**: Prevents AI agents from autonomously pushing unauthorized changes to the project's most critical paths.

## 4. Local Pre-commit Hooks

Catching errors before the CI pipeline runs saves time and prevents malformed code from entering the repository history.
- **Reference Standard**: Husky, `pre-commit`, or Lefthook.
- **Implementation**: Configure local git hooks to enforce automated formatting and run unit tests on changed files before allowing a commit.
- **LFE Benefit**: Assists the Builder persona by automatically enforcing formatting rules.

## 5. Automated Secret Scanning

AI agents can occasionally hallucinate or copy-paste sensitive credentials (API keys, tokens) into the source code.
- **Reference Standard**: GitHub Advanced Security, TruffleHog, or GitLeaks.
- **Implementation**: Enable repository-level secret scanning or include a scanning job in your CI pipeline.
- **LFE Benefit**: Adds a crucial safety net during the Builder phase.

## 6. MCP-Ready Tool Gateways (Physical Tool-Locking)

The default LFE protocol relies on "prompt discipline." As AI agents become more autonomous, they may occasionally disobey prompt instructions. The manifest at `.agents/permissions.json` exists as the **enforcement contract** for whenever physical tool-locking is available to you — it declares, per persona, which paths and tools are in bounds.

- **What the agnostic core ships: the contract only.** Runtime tool-locking is inherently platform-specific — every IDE/LLM exposes a different interception surface (MCP gateways, tool-hooks, agent wrappers), so a portable framework cannot configure it for every platform. That mechanization is exactly what **platform-specific distributions of LFE** exist for: a distribution wires this same manifest into its platform's native enforcement, so adopters on that platform get tool-locking out of the box.
- **The platform-neutral layer you can always adopt — CI/CD**: a GitHub Action or equivalent reads the same manifest and rejects PRs whose diffs violate persona constraints (e.g., a PR authored under the Architect persona that touches `src/`). This works identically for any LLM or IDE, which makes it the recommended team-scale enforcement for the agnostic core.
- **LFE Benefit**: one `.agents/permissions.json` contract, three consumption levels — prompt discipline today, CI/CD enforcement at team scale, and full runtime tool-locking when you adopt a platform distribution. No repository restructuring at any level.

### 6.1 Enforcement-Gate Family (concept)

The path-lock above is one member of a broader **enforcement-gate family** — the rules that close the structural gaps a momentum-optimizing agent can otherwise walk through (it can drift off-pipeline without breaking a single rule, because the rules sat outside the path of what it did). The agnostic core ships these as **agent obligations** — the Discipline Gates in [`GOVERNANCE.md`](GOVERNANCE.md); a platform distribution wires them into its native runtime so they hold mechanically. The family:

- **C1 — terminal git posture**: mutating git in two tiers — ordinary mutating git needs an active mission; `merge` / push-to-`main` / force-push / legal-anchor-tag additionally need a typed `MERGE-OK` confirmation.
- **C2a — boot precondition**: no substantive Write/Edit until `/lfe-boot` ran this session.
- **C2b — scout boundary**: `/lfe-scout` only at a clean session boundary, refused mid-mission.
- **C3 — persona transition**: the Active-Persona value changes only via an official skill-dispatched step, never a free-hand edit.
- **C4 — no-mission**: no substantive change at a completed/idle slate with no coordination trail.
- **mission-aware path-lock**: an in-flight mission's `Authorized Scope` entrance-card row extends the authorized write scope (never to `src/**` for a non-Builder persona).
- **visual floor**: the Inspector→Archivist close of a *visual slice* is withheld until `inspection_report.md` carries `visual_confirmed` + `visual_signoff`. This is the family's one **unconditional** member — the visual sign-off is the close condition, not a speed-bump.

**Doctrine — speed-bumps + loudness, not containment.** In the agnostic core nothing mechanically stops an agent with shell access from bypassing a prose rule; the family's value is making the cooperative path easiest and any drift loud and deliberate, so the human spots it. A platform distribution may promote any gate to a hard runtime block — the visual floor being the one that denies regardless. Claude-LFE is the reference distribution that mechanizes this family in its runtime.
