---
name: lfe-security-check
description: Inspector sub-skill. Runs an OWASP Top-10 prompt-based security analysis on the current implementation diff. No external tooling required — pure LLM reasoning. Writes .plans/checks/security_findings.md. Called by lfe-inspector when enabled in inspector-config.md.
---

# LFE Security Check — OWASP Top-10 Prompt Analysis

## Position in Pipeline
- **Phase**: 3 (Inspector sub-skill)
- **Persona**: Inspector (read-only; no src/ writes)
- **Trigger**: Invoked by `/lfe-inspector` Sub-Skill Dispatch when `lfe-security-check: true` in `.docs/quality/inspector-config.md`
- **Output**: `.plans/checks/security_findings.md` — aggregated by Inspector into `critique.md`

## Mission
Systematically examine the implementation diff for security vulnerabilities using the OWASP Top-10 as a structured checklist. No external scanners, no Semgrep, no platform-specific tooling — reasoning over code only.

## Hard Rules
0. **Dispatch Context Required (refuse direct invocation)**: This skill is dispatched by `/lfe-inspector` Step 6 — it is not a Brain-typeable skill (per `LLM_AGENT_GUIDE.md` §8.8 Skill Invocation Authority). If invoked without `.plans/builder_done.md` AND `.plans/tdd_report.md` for the current slice, halt immediately and reply: *"`/lfe-security-check` is an Inspector sub-skill dispatched by `/lfe-inspector`. It cannot be run standalone. Run `/lfe-inspector` — the dispatcher will invoke this sub-skill if it is enabled in `.docs/quality/inspector-config.md` (or via an `## Inspector Overrides` section in `active_plan.md`)."* Direct invocation produces orphaned findings files and breaks the Inspector's aggregation logic.
1. **Prompt-Only Analysis**: All findings come from LLM reasoning over the diff — reason only, with no code execution or external API calls.
2. **Cite Evidence**: Every finding must quote the specific code line or pattern that triggers the concern.
3. **Severity Triage**: Rate each finding as Critical / High / Medium / Low. Only Critical blocks Builder re-run; others are advisory.
4. **Scope to Diff**: Analyse only the files changed in `builder_done.md`; skip unchanged code.

## Inputs
1. `.plans/builder_done.md` — list of changed files
2. `.plans/tdd_report.md` — context on what was implemented
3. `.plans/active_plan.md` — scope boundary
4. `.docs/engineering/engineering-standards.md` (if present) — project security baseline

## Workflow

### Step 1: Extract Diff Scope
From `builder_done.md`, identify the list of modified files. Read each changed file.

### Step 2: OWASP Top-10 Walkthrough
Reason through each category against the changed code:

| # | Category | Key Questions |
|---|---|---|
| A01 | Broken Access Control | Are permissions checked before data access? Any IDOR patterns? |
| A02 | Cryptographic Failures | Sensitive data transmitted/stored in plaintext? Weak algorithms (MD5, SHA1)? |
| A03 | Injection | SQL/NoSQL/OS/LDAP inputs sanitised or parameterised? Template injection possible? |
| A04 | Insecure Design | Business logic flaws that bypass security controls? |
| A05 | Security Misconfiguration | Debug modes, default credentials, unnecessary features enabled? |
| A06 | Vulnerable Components | New dependencies introduced? Known-risky version patterns? |
| A07 | Auth & Session Failures | Session tokens properly invalidated? Password hashing correct? |
| A08 | Integrity Failures | Deserialisation of untrusted data? Unsigned software updates? |
| A09 | Logging Failures | Sensitive data (PII, credentials) logged? Security events unlogged? |
| A10 | SSRF | User-controlled URLs passed to server-side HTTP clients? |

### Step 3: Write Findings File

Path: `.plans/checks/security_findings.md`

Begin the file with a YAML frontmatter block — the Inspector's dispatcher relies on `status: complete` to detect successful runs and skip them on crash recovery. A file without this frontmatter (or with any other `status` value) will be re-invoked rather than skipped.

```yaml
---
phase: inspector
step: security-check
kind: sub-skill
status: complete
timestamp: <ISO-8601>
source: .plans/builder_done.md
slice: <copied from active_plan.md>
---
```

Body:

```markdown
## Security Check Findings

**Scope**: <list of analysed files>
**Clean categories**: <OWASP categories with no findings>

### Critical
- <A0X — description — quoted code evidence>

### High
- <finding>

### Medium
- <finding>

### Low / Informational
- <finding>

### Summary
- Critical issues: <N> (block re-run until resolved)
- Advisory issues: <N> (human triages)
```

If no findings: write `No security concerns identified across all OWASP Top-10 categories.`

## Handoff
Return control to `/lfe-inspector`. Inspector aggregates this file's content into `critique.md` under a `## Security` section. Do not write to `critique.md` directly.
