---
name: lfe-tdd
description: Red-green-refactor quality pass on code the Builder just wrote. Use in the Builder sub-pipeline after lfe-builder completes implementation.
---

# LFE TDD

## Position in Pipeline
- **Phase**: 2 (Builder sub-pipeline, Step 2)
- **Persona**: Builder
- **Input**: `active_plan.md` + `.plans/builder_done.md` (resume marker — confirms implementation phase completed) + code written by lfe-builder
- **Output**: `.plans/tdd_report.md`

## Philosophy

**Core principle**: Tests verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't.

**Good tests** are integration-style: they exercise real code paths through public APIs. They describe _what_ the system does, not _how_. A good test reads like a specification.

**Bad tests** are coupled to implementation. They mock internal collaborators, test private methods, or verify through external means. Warning sign: your test breaks when you refactor, but behavior hasn't changed.

See [tests.md](./tests.md), [mocking.md](./mocking.md) for examples.

## Anti-Pattern: Horizontal Slices

**Write incrementally — one test, then its implementation — rather than all tests up front.** Writing all tests first is "horizontal slicing."

**Correct approach**: Vertical slices via tracer bullets. One test → one implementation → repeat.

```
WRONG (horizontal):
  RED:   test1, test2, test3, test4, test5
  GREEN: impl1, impl2, impl3, impl4, impl5

RIGHT (vertical):
  RED→GREEN: test1→impl1
  RED→GREEN: test2→impl2
  RED→GREEN: test3→impl3
```

## Workflow

### 1. Review
Read `active_plan.md` and review what the Builder implemented. Identify behaviors that need test coverage. Use the project's domain glossary for test names.

Confirm with the human which behaviors to prioritize for testing. **Testing everything is impossible — prioritize.**

### 2. Tracer Bullet
Write ONE test that confirms ONE thing:

```
RED:   Write test for first behavior → test fails
GREEN: Write minimal code to pass → test passes
```

### 3. Incremental Loop
For each remaining behavior:

```
RED:   Write next test → fails
GREEN: Minimal code to pass → passes
```

Rules:
- One test at a time
- Only enough code to pass current test
- Write only for the current test, not anticipated future ones
- Keep tests focused on observable behavior

### 4. Refactor
After all tests pass, look for refactor candidates (see [refactoring.md](./refactoring.md)):
- Extract duplication
- Deepen modules (see [deep-modules.md](./deep-modules.md))
- Apply SOLID principles where natural
- Run tests after each refactor step

**Refactor only once GREEN.** Reach green first, then refactor.

## Checklist Per Cycle

```
[ ] Test describes behavior, not implementation
[ ] Test uses public interface only
[ ] Test would survive internal refactor
[ ] Code is minimal for this test
[ ] No speculative features added
```

## Coordination File Output

When TDD pass completes, write `.plans/tdd_report.md`:

```yaml
---
phase: builder
step: tdd
status: complete
timestamp: <ISO-8601>
source: .plans/active_plan.md
slice: <copied from active_plan.md>
tests_passed: <N>
tests_failed: 0
---
```

```markdown
## Behaviors Tested
- <behavior 1>: PASS
- <behavior 2>: PASS

## Refactors Applied
- <refactor 1>

## Coverage Notes
- <any gaps or deferred items>
```

Update `pipeline_status.md` coordination tracker to mark tdd step as ✅.

## Evidence Discipline

The TDD step's signature claim is *"the tests pass"* — so it always ships with the pasted run counts that prove it.

A completion claim earns its place only when fresh tool output from this session backs it.
- "Tests pass" means *paste the run's pass/fail counts* from the run you just executed.
- "No regression" means *paste the counts and show they hold or rise* — a dropped count is a regression to report, rather than a number to round away.
- State facts about files, APIs, and dependencies from what you just read this session, rather than from memory.
- Treat hedge words — "should work", "probably", "I think it passes" — as a signal to stop and run the verifying step, then report the real result.

**Confidence routing (per claim).**

| Confidence | Basis | Action |
|---|---|---|
| High | Tool-verified this session | State it plainly. |
| Medium | Inferred from context | State it with the caveat that it is inferred. |
| Low | Recalled from training or memory | Verify first (read or run), then state. |

**Hallucination-signal checklist.** Pause → Verify → Correct the moment you catch yourself:
- referencing a file or symbol you have yet to open this session;
- quoting a number (count, version, size) without a source you just saw;
- stating something that runs against the latest tool output;
- importing or assuming a dependency you have yet to confirm exists.
