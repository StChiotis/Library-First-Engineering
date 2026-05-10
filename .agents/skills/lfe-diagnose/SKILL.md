---
name: lfe-diagnose
description: Disciplined bug-diagnosis loop. Reproduce → Hypothesise → Fix. Use in the Inspector sub-pipeline (Step 3, conditional) when verification fails.
---

# LFE Diagnose

## Position in Pipeline
- **Phase**: 3 (Inspector sub-pipeline, Step 3 — conditional)
- **Persona**: Inspector (triggers), Builder (fixes)
- **Trigger**: Called by Inspector when `/lfe-inspector` verification fails
- **Input**: Failing behavior identified during inspection
- **Output**: Fix applied → returns to Builder sub-pipeline for re-verification

## Process
1. **Feedback Loop**: Build a deterministic repro (test/script) that reliably demonstrates the failure.
2. **Reproduce**: Run the loop and witness the failure. Confirm it matches the Inspector's reported issue.
3. **Hypothesise**: Generate 3-5 ranked, falsifiable hypotheses for the root cause.
4. **Instrument**: Add targeted probes to test hypotheses one at a time. Eliminate hypotheses until the root cause is confirmed.
5. **Fix**: Write a regression test that captures the bug → Apply the minimal fix → Watch the test pass.
6. **Persist Diagnosis**: Write `.plans/diagnosis_report.md` so the *why* of the fix survives a session crash. Schema below.
7. **Handoff**: Return to Builder sub-pipeline. The Builder runs the full test suite, then the Inspector re-verifies.

## Coordination File Output

Per the contract in [COORDINATION_FILES.md](../../../.docs/protocol/COORDINATION_FILES.md):

```yaml
---
phase: inspector
step: diagnose
status: complete
timestamp: <ISO-8601>
source: tdd_report.md
---
```

```markdown
## Failure Repro
- <command or test that reliably reproduces the bug>

## Hypotheses Considered
1. <hypothesis 1> — <eliminated / confirmed>
2. <hypothesis 2> — <eliminated / confirmed>

## Root Cause
- <one-paragraph explanation>

## Fix Summary
- <files touched and minimal change applied>
- Regression test: <test name>

## Risks / Notes for Inspector
- <anything the re-verification should look at carefully>
```

## Rules
- Use the project's domain glossary from `CONTEXT.md` when naming tests and describing the bug.
- Check `.docs/domain/` documentation to verify the expected behavior before assuming the code is wrong.
- The fix must be minimal — solve the diagnosed problem, nothing more.
- Always write `diagnosis_report.md` before handoff — a fix without a recorded *why* is invisible to crash recovery and to next-session archaeology.
