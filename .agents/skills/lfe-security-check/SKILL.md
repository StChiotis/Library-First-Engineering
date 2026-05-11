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
1. **Prompt-Only Analysis**: All findings come from LLM reasoning over the diff. Do not attempt to execute code or call external APIs.
2. **Cite Evidence**: Every finding must quote the specific code line or pattern that triggers the concern.
3. **Severity Triage**: Rate each finding as Critical / High / Medium / Low. Only Critical blocks Builder re-run; others are advisory.
4. **Scope to Diff**: Analyse only the files changed in `builder_done.md`. Do not re-audit unchanged code.

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
