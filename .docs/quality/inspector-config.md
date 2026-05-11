# Inspector Sub-Skill Configuration

> **Owner**: Architect configures at project start; Brain may override per-mission.
> **Read by**: `/lfe-inspector` during Sub-Skill Dispatch (before writing `critique.md`).

Configure which specialist sub-skills the Inspector invokes for this project. Disabled sub-skills are silently skipped — no placeholder section appears in `critique.md`.

## Sub-Skill Registry

| Sub-skill | Enabled | Notes |
|---|---|---|
| `lfe-security-check` | true | OWASP Top-10 prompt analysis; always-on by default |
| `lfe-perf-check` | true | Resource leaks, N+1, algorithmic complexity |
| `lfe-complexity-check` | false | Enable once codebase exceeds ~5 modules |
| `lfe-dep-audit` | true | Dependency file review + human-run audit instruction |
| `lfe-mutation-verify` | false | Enable only for critical or safety-sensitive modules |

## How to Override Per-Mission

The Brain may place a comment in `active_plan.md` like:
```
inspector-config-override: lfe-mutation-verify=true
```

The Inspector reads this override before dispatching sub-skills, giving it precedence over the table above.

## Dispatch Order

When multiple sub-skills are enabled, Inspector runs them in this fixed sequence:
1. `lfe-security-check`
2. `lfe-perf-check`
3. `lfe-complexity-check`
4. `lfe-dep-audit`
5. `lfe-mutation-verify`

Each sub-skill writes its findings to `.plans/checks/<sub-skill-name>_findings.md`. Inspector aggregates all outputs into `critique.md` under labelled sections.
