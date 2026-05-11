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

Per-mission overrides live in a typed body section of `.plans/active_plan.md` called **`## Inspector Overrides`**. The Inspector parses this section after reading this config table and before dispatching sub-skills; overrides take precedence over the table above.

**Schema** (the exact format the Inspector parses — informal text or scattered comments are ignored):

````markdown
## Inspector Overrides
```yaml
lfe-security-check: true
lfe-mutation-verify: true
lfe-perf-check: false
```
````

- Keys: any sub-skill name from the Registry above.
- Values: `true` (force enable) or `false` (force disable).
- Omit the section entirely when no overrides are needed.
- Unknown keys are ignored with a warning to the Brain. Missing keys fall through to the config table default.

The Architect's `active_plan.md` body template includes this section as optional. See `.agents/skills/lfe-architect/SKILL.md` Step 6.

## Dispatch Order

When multiple sub-skills are enabled, Inspector runs them in this fixed sequence:
1. `lfe-security-check`
2. `lfe-perf-check`
3. `lfe-complexity-check`
4. `lfe-dep-audit`
5. `lfe-mutation-verify`

Each sub-skill writes its findings to `.plans/checks/<sub-skill-name>_findings.md`. Inspector aggregates all outputs into `critique.md` under labelled sections.
