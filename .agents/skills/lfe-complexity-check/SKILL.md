---
name: lfe-complexity-check
description: Inspector sub-skill. Analyses changed code for cyclomatic complexity, excessive nesting, oversized functions, and cognitive load indicators. Pure LLM reasoning — no external tooling. Writes .plans/checks/complexity_findings.md. Called by lfe-inspector when enabled in inspector-config.md.
---

# LFE Complexity Check — Prompt-Based Complexity Analysis

## Position in Pipeline
- **Phase**: 3 (Inspector sub-skill)
- **Persona**: Inspector (read-only; no src/ writes)
- **Trigger**: Invoked by `/lfe-inspector` Sub-Skill Dispatch when `lfe-complexity-check: true` in `.docs/quality/inspector-config.md`
- **Output**: `.plans/checks/complexity_findings.md` — aggregated by Inspector into `critique.md`

## Mission
Reason over changed code to identify complexity indicators that reduce maintainability. The goal is not to enforce arbitrary metrics but to surface patterns that will make future AI sessions harder to navigate and extend correctly.

## Hard Rules
0. **Dispatch Context Required (refuse direct invocation)**: This skill is dispatched by `/lfe-inspector` Step 6 — it is not a Brain-typeable skill (per `LLM_AGENT_GUIDE.md` §8.8 Skill Invocation Authority). If invoked without `.plans/builder_done.md` for the current slice, halt immediately and reply: *"`/lfe-complexity-check` is an Inspector sub-skill dispatched by `/lfe-inspector`. It cannot be run standalone. Run `/lfe-inspector` — the dispatcher will invoke this sub-skill if it is enabled in `.docs/quality/inspector-config.md` (or via an `## Inspector Overrides` section in `active_plan.md`)."* Direct invocation produces orphaned findings files and breaks the Inspector's aggregation logic.
1. **Prompt-Only**: Reason over the diff. Do not run linters or complexity calculators.
2. **Diff-Scoped**: Analyse only files listed in `builder_done.md`.
3. **Severity**: High (will actively confuse the next AI session) / Medium (increases risk) / Low (style concern).
4. **Context Matters**: A 25-line function that is purely a switch statement is less concerning than a 15-line function with 4 levels of nesting and side effects.

## Inputs
1. `.plans/builder_done.md` — list of changed files
2. `.plans/active_plan.md` — scope boundary

## Workflow

### Step 1: Extract Diff Scope
From `builder_done.md`, identify and read modified files.

### Step 2: Complexity Indicators Checklist

| Indicator | Threshold | Notes |
|---|---|---|
| Function/method length | > 20 lines | Count non-blank, non-comment lines |
| Cyclomatic complexity | > 10 branches | Count: `if/else/elif/switch case/catch/while/for/and/or` |
| Nesting depth | > 4 levels | Count indentation levels for control flow |
| Parameter count | > 5 parameters | Includes optional params; data objects count as 1 |
| Cognitive complexity | Subjective | Would a reader need to simulate execution to understand it? |
| God function | Any | A function that does setup, business logic, I/O, and error handling |
| Magic literals | Any | Unexplained numeric or string constants inside logic |

### Step 3: Write Findings File

Path: `.plans/checks/complexity_findings.md`

Begin the file with a YAML frontmatter block — the Inspector's dispatcher relies on `status: complete` to detect successful runs and skip them on crash recovery.

```yaml
---
phase: inspector
step: complexity-check
kind: sub-skill
status: complete
timestamp: <ISO-8601>
source: .plans/builder_done.md
slice: <copied from active_plan.md>
---
```

Body:

```markdown
## Complexity Check Findings

**Scope**: <list of analysed files>
**Clean files**: <files with no complexity concerns>

### High
- <file:function — indicator — evidence>

### Medium
- <finding>

### Low / Informational
- <finding>

### Summary
- High issues: <N>
- Advisory issues: <N>
- Refactor candidates: <list of function names>
```

If no findings: write `No complexity concerns identified in the analysed diff.`

## Handoff
Return control to `/lfe-inspector`. Inspector aggregates this file's content into `critique.md` under a `## Complexity` section. Do not write to `critique.md` directly.
