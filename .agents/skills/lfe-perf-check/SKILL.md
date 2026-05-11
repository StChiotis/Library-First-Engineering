---
name: lfe-perf-check
description: Inspector sub-skill. Analyses the implementation diff for common performance anti-patterns — resource leaks, N+1 queries, unbounded growth, algorithmic complexity. Pure LLM reasoning, no profilers. Writes .plans/checks/perf_findings.md. Called by lfe-inspector when enabled in inspector-config.md.
---

# LFE Performance Check — Prompt-Based Anti-Pattern Analysis

## Position in Pipeline
- **Phase**: 3 (Inspector sub-skill)
- **Persona**: Inspector (read-only; no src/ writes)
- **Trigger**: Invoked by `/lfe-inspector` Sub-Skill Dispatch when `lfe-perf-check: true` in `.docs/quality/inspector-config.md`
- **Output**: `.plans/checks/perf_findings.md` — aggregated by Inspector into `critique.md`

## Mission
Detect performance anti-patterns introduced by the current implementation through structured LLM reasoning over the diff. Findings are advisory unless a pattern is provably unbounded (e.g. O(n²) in a hot path with no upper bound on n).

## Hard Rules
0. **Dispatch Context Required (refuse direct invocation)**: This skill is dispatched by `/lfe-inspector` Step 6 — it is not a Brain-typeable skill (per `LLM_AGENT_GUIDE.md` §8.8 Skill Invocation Authority). If invoked without `.plans/builder_done.md` for the current slice, halt immediately and reply: *"`/lfe-perf-check` is an Inspector sub-skill dispatched by `/lfe-inspector`. It cannot be run standalone. Run `/lfe-inspector` — the dispatcher will invoke this sub-skill if it is enabled in `.docs/quality/inspector-config.md` (or via an `## Inspector Overrides` section in `active_plan.md`)."* Direct invocation produces orphaned findings files and breaks the Inspector's aggregation logic.
1. **Prompt-Only**: No profiling tools, no benchmarks, no execution. Reasoning over code only.
2. **Diff-Scoped**: Analyse only files listed in `builder_done.md`. Do not re-audit unchanged code.
3. **Severity**: Critical (provably unbounded, certain production impact) / High (likely hot-path) / Medium (probable overhead) / Low (style concern).
4. **Cite Line**: Every finding quotes the specific pattern from the code.

## Inputs
1. `.plans/builder_done.md` — list of changed files
2. `.plans/active_plan.md` — domain context and scope boundary
3. `.docs/domain/` — domain rules that might imply expected data volumes

## Workflow

### Step 1: Extract Diff Scope
From `builder_done.md`, identify and read modified files.

### Step 2: Anti-Pattern Checklist

Reason through each category against the changed code:

| Category | Patterns to Detect |
|---|---|
| **Resource Leaks** | Unclosed file handles, DB connections, sockets; missing `finally`/`defer`/`using` blocks; objects allocated in loops without release |
| **N+1 Query** | DB or API calls inside a loop; queries that could be batched; ORM lazy-load inside iteration |
| **Unbounded Growth** | Lists/maps that grow with no size limit; recursive calls with no guaranteed termination; caches with no eviction policy |
| **Algorithmic Complexity** | Nested loops over the same collection; sorting inside a frequently-called function; repeated full-scan where an index would help |
| **Blocking I/O** | Synchronous network or disk calls in async context; missing pagination on large result sets; missing timeouts on external calls |
| **Memory Anti-Patterns** | Large objects copied rather than referenced; excessive intermediate allocations in hot paths |

### Step 3: Write Findings File

Path: `.plans/checks/perf_findings.md`

Begin the file with a YAML frontmatter block — the Inspector's dispatcher relies on `status: complete` to detect successful runs and skip them on crash recovery.

```yaml
---
phase: inspector
step: perf-check
kind: sub-skill
status: complete
timestamp: <ISO-8601>
source: .plans/builder_done.md
slice: <copied from active_plan.md>
---
```

Body:

```markdown
## Performance Check Findings

**Scope**: <list of analysed files>
**Clean categories**: <categories with no findings>

### Critical
- <Pattern — description — quoted code evidence>

### High
- <finding>

### Medium
- <finding>

### Low / Informational
- <finding>

### Summary
- Critical issues: <N>
- Advisory issues: <N>
```

If no findings: write `No performance anti-patterns identified in the analysed diff.`

## Handoff
Return control to `/lfe-inspector`. Inspector aggregates this file's content into `critique.md` under a `## Performance` section. Do not write to `critique.md` directly.
