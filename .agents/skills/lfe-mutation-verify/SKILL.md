---
name: lfe-mutation-verify
description: Inspector sub-skill. Prompt-based mutation testing — reasons about whether existing tests would catch hypothetical code mutations. No test runner required. Writes .plans/checks/mutation_findings.md. Called by lfe-inspector when enabled in inspector-config.md. Enable only for critical or safety-sensitive modules.
---

# LFE Mutation Verify — Prompt-Based Test Quality Reasoning

## Position in Pipeline
- **Phase**: 3 (Inspector sub-skill)
- **Persona**: Inspector (read-only; no src/ writes)
- **Trigger**: Invoked by `/lfe-inspector` Sub-Skill Dispatch when `lfe-mutation-verify: true` in `.docs/quality/inspector-config.md`
- **Output**: `.plans/checks/mutation_findings.md` — aggregated by Inspector into `critique.md`

## Mission
Reason about test quality by asking: "If we made this change to the production code, would the existing tests catch it?" This is the logical equivalent of a mutation testing tool, done entirely through LLM reasoning over the diff. It surfaces tests that pass for the wrong reason (coincidental correctness) and logic paths that have no discriminating test.

This skill is expensive in reasoning tokens. Enable selectively — critical business logic, security-sensitive paths, and mission-critical calculations.

## Hard Rules
1. **Prompt-Only**: No test runner, no mutation framework, no code execution.
2. **Diff-Scoped**: Reason only over implementation + test files in `builder_done.md`.
3. **Candidate Mutations**: For each production function changed, propose 3–5 plausible mutations; reason whether any test would catch them.
4. **Severity**: Escaped (no test catches the mutation) / Covered (mutation is caught by ≥1 test) / Partial (mutation is caught only by an integration test, not a unit test).

## Inputs
1. `.plans/builder_done.md` — list of changed implementation AND test files
2. `.plans/tdd_report.md` — test summary
3. `.plans/active_plan.md` — acceptance criteria (defines what "should be tested")

## Workflow

### Step 1: Extract Diff Scope
From `builder_done.md`, separate implementation files from test files. Read both.

### Step 2: Per-Function Mutation Candidates

For each changed production function or method:

1. **Identify the core logic** — the condition or computation that the function's correctness depends on.
2. **Propose 3–5 mutations** — simple changes that would be plausible bugs:
   - Off-by-one: `> n` → `>= n`, `i < len` → `i <= len`
   - Boundary reversal: `if (isValid)` → `if (!isValid)`
   - Return value change: return `null` / `0` / `false` where truthy expected
   - Operator swap: `+` → `-`, `&&` → `||`
   - Condition deletion: entire guard clause removed

3. **Predict test outcome for each mutation**: Would any test in the test files fail? Quote the specific assertion that would catch it. If no assertion would catch it, mark as **Escaped**.

### Step 3: Write Findings File

Path: `.plans/checks/mutation_findings.md`

```markdown
## Mutation Verify Findings

**Implementation files**: <list>
**Test files**: <list>

### Escaped Mutations (no test catches these)

| Function | Mutation | Why it Escapes |
|---|---|---|
| `functionName` | `> n` → `>= n` | No test asserts the boundary value exactly |

### Covered Mutations (caught by existing tests)
- `functionName` — all mutations caught by `test_name`

### Partial Coverage (integration-only, no unit test)
- `functionName` — caught by integration test `X` but no isolated unit assertion

### Summary
- Escaped mutations: <N> — recommend adding targeted assertions
- Covered: <N>
- Partial: <N>

### Recommended Test Additions
- <specific assertion to add for each escaped mutation>
```

If no implementation functions changed, or all mutations are covered: write summary with zero escapes.

## Handoff
Return control to `/lfe-inspector`. Inspector aggregates this file's content into `critique.md` under a `## Mutation Coverage` section. Do not write to `critique.md` directly.
