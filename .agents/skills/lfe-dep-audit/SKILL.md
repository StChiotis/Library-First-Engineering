---
name: lfe-dep-audit
description: Inspector sub-skill. Reviews dependency manifest files (package.json, requirements.txt, go.mod, Cargo.toml, pom.xml) changed in the current diff for risky version patterns and stale majors. Emits a human-run audit instruction rather than executing tools. Writes .plans/checks/dep_findings.md. Called by lfe-inspector when enabled in inspector-config.md.
---

# LFE Dependency Audit — Manifest Review + Human-Run Instruction

## Position in Pipeline
- **Phase**: 3 (Inspector sub-skill)
- **Persona**: Inspector (read-only; no src/ writes)
- **Trigger**: Invoked by `/lfe-inspector` Sub-Skill Dispatch when `lfe-dep-audit: true` in `.docs/quality/inspector-config.md`
- **Output**: `.plans/checks/dep_findings.md` — aggregated by Inspector into `critique.md`

## Mission
Review any dependency manifest files touched by the current diff for known-risky version patterns, major version jumps, and unmaintained packages. Since actual vulnerability databases require tool execution, this skill emits a structured instruction block asking the Brain to run the appropriate audit command and paste the output — keeping the skill fully tool-agnostic.

## Hard Rules
0. **Dispatch Context Required (refuse direct invocation)**: This skill is dispatched by `/lfe-inspector` Step 6 — it is not a Brain-typeable skill (per `LLM_AGENT_GUIDE.md` §8.8 Skill Invocation Authority). If invoked without `.plans/builder_done.md` for the current slice, halt immediately and reply: *"`/lfe-dep-audit` is an Inspector sub-skill dispatched by `/lfe-inspector`. It cannot be run standalone. Run `/lfe-inspector` — the dispatcher will invoke this sub-skill if it is enabled in `.docs/quality/inspector-config.md` (or via an `## Inspector Overrides` section in `active_plan.md`)."* Direct invocation produces orphaned findings files and breaks the Inspector's aggregation logic.
1. **No Tool Execution**: Do not run `npm audit`, `pip audit`, `cargo audit`, or any CLI command. Reason over manifest contents only.
2. **Manifest-Scoped**: Only audit dependency files that appear in `builder_done.md`. Do not re-audit unchanged manifests.
3. **Emit Instruction Block**: Always emit a "Brain Action Required" block for each detected manifest type — even if no static concerns are found — because dynamic vulnerability data requires runtime tools.
4. **Severity**: High (version pinned to `*` or `latest`; known-risky major) / Medium (very old major; no upper bound) / Low (minor version drift).

## Inputs
1. `.plans/builder_done.md` — list of changed files (look for manifest files)
2. The manifest files themselves

## Workflow

### Step 1: Identify Changed Manifests
From `builder_done.md`, filter for dependency files:
- Node.js: `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`
- Python: `requirements.txt`, `pyproject.toml`, `Pipfile`, `setup.py`
- Go: `go.mod`, `go.sum`
- Rust: `Cargo.toml`, `Cargo.lock`
- Java/Kotlin: `pom.xml`, `build.gradle`
- Ruby: `Gemfile`, `Gemfile.lock`

If no manifest files changed: write a brief note and stop.

### Step 2: Static Pattern Analysis
For each changed manifest, reason over:

| Pattern | Risk |
|---|---|
| Version `*` or `latest` | High — non-deterministic; any future publish can break or compromise |
| Major version jump (e.g. `^2.x → 4.x`) | Medium — breaking changes or API surface change |
| Dependency with no pinned upper bound AND marked `*` | High |
| Dependency not seen in any prior version of the manifest | Medium — net-new dependency introduction |
| Obvious deprecated package names (e.g. `request`, `node-uuid`) | Medium — community-abandoned |
| Lock file absent or not updated alongside manifest | High — non-reproducible builds |

### Step 3: Emit Human-Run Audit Instruction

For each manifest type detected, include the appropriate command:

```
## Brain Action Required — Run Audit Command

Please run the following command(s) and paste the output into the chat:

[Node.js]  npm audit --audit-level=moderate
[Python]   pip audit   (or: safety check -r requirements.txt)
[Go]       govulncheck ./...
[Rust]     cargo audit
[Java]     mvn dependency:tree | grep -i cve   (or use OWASP Dependency-Check)
```

### Step 4: Write Findings File

Path: `.plans/checks/dep_findings.md`

Begin the file with a YAML frontmatter block — the Inspector's dispatcher relies on `status: complete` to detect successful runs and skip them on crash recovery.

```yaml
---
phase: inspector
step: dep-audit
kind: sub-skill
status: complete
timestamp: <ISO-8601>
source: .plans/builder_done.md
slice: <copied from active_plan.md>
---
```

Body:

```markdown
## Dependency Audit Findings

**Manifests reviewed**: <list of manifest files>

### Static Findings

#### High
- <package@version — pattern — risk>

#### Medium
- <finding>

#### Low / Informational
- <finding>

### Brain Action Required
<copy the audit instruction block from Step 3 here>

Paste the audit output here before proceeding. Inspector will re-assess if Critical or High CVEs are reported.

### Summary
- Static findings: <N high> / <N medium> / <N low>
- Audit command: pending Brain execution
```

If no manifest files changed: write `No dependency manifest files were modified in this diff. No audit required.`

## Handoff
Return control to `/lfe-inspector`. Inspector aggregates this file's content into `critique.md` under a `## Dependencies` section. Do not write to `critique.md` directly.
