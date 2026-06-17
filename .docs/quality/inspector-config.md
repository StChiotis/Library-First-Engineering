# Inspector Sub-Skill Configuration

> **Owner**: Architect configures at project start; Brain may override per-mission.
> **Read by**: `/lfe-inspector` during Sub-Skill Dispatch (before writing `critique.md`).

Configure which specialist sub-skills the Inspector invokes for this project. Disabled sub-skills are silently skipped — no placeholder section appears in `critique.md`.

## Sub-Skill Registry

| Sub-skill | Enabled | Notes |
|---|---|---|
| `lfe-security-check` | true | OWASP Top-10 prompt analysis; always-on by default |
| `lfe-perf-check` | true | Resource leaks, N+1, algorithmic complexity |
| `lfe-complexity-check` | false | Disabled by default. Enable per-slice via `## Inspector Overrides` when the slice introduces substantial logic — long functions, deep nesting, or branching that would benefit from a cognitive-load audit. |
| `lfe-dep-audit` | true | Dependency file review + human-run audit instruction |
| `lfe-mutation-verify` | false | Disabled by default. Enable per-slice via `## Inspector Overrides` for high-correctness-stakes changes where you want to know whether existing tests would actually catch a bug — token-heavy; target use is small and selective. |
| `lfe-visual-check` | false | Off by default — inert on a non-visual project. Renders the changed UI surface and presents it for a human visual sign-off. **Auto-armed by the Visual Floor** whenever a changed file matches a visual file class (see Visual Floor Rules below); the floor is a minimum, so an override leaves it armed. |

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
6. `lfe-visual-check`

Each sub-skill writes its findings to `.plans/checks/<sub-skill-name>_findings.md`. Inspector aggregates all outputs into `critique.md` under labelled sections.

## Security Floor Rules

`lfe-security-check` has a **floor** for high-risk change classes — it runs regardless of the table above or any per-mission override whenever a slice's changed files (from `.plans/builder_done.md`) include either:

1. **Adopter-facing entry documents** — `pipeline_status.md`, `LLM_AGENT_GUIDE.md`, `USER_MANUAL.md`, `README.md`, or any adapter file (`CLAUDE.md`, `.cursorrules`, `.windsurfrules`, `.clinerules`, `.antigravityrules`, `.github/copilot-instructions.md`). These files steer every future session, so an unaudited rewrite there has outsized blast radius.
2. **CI workflow files** — `.github/workflows/**`. Workflow files run with repository tokens, the highest-risk automation surface. **Dormant** until the repo adds its first workflow file.

**Floor semantics:** the floor is a *minimum*. A per-mission `## Inspector Overrides` block may set `lfe-security-check: true` (redundant for these paths), but an override lowering it below the floor is ignored — with a warning to the Brain — for any slice touching a floor path class. The Inspector evaluates the floor after reading the config table + overrides, when computing the final enabled set.

## Visual Floor Rules

`lfe-visual-check` has a **floor** for visual change classes — it arms regardless of the table above or any per-mission override whenever a slice's changed files (from `.plans/builder_done.md`) match a **visual file class**:

- **Stylesheets** — `.css`, `.scss`, `.sass`, `.less`, `.styl`
- **Markup / templates** — `.html`, `.vue`, `.svelte`, `.astro`, `.hbs`, `.ejs`, `.pug`, `.njk`
- **Components** — `.jsx`, `.tsx`
- **Image assets** — `.png`, `.jpg` / `.jpeg`, `.gif`, `.svg`, `.webp`, `.avif`, `.ico`
- **Path classes** — files under `component/`, `view/`, `page/`, `template/`, or `style/` directories

This prose list is the source of truth for the visual file classes; a project may extend it (e.g. native-mobile or game-engine UI surfaces) by recording the addition here.

**Floor semantics:** the floor is a *minimum*, identical to the Security Floor — an override lowering `lfe-visual-check` below the floor is ignored (with a warning to the Brain) for any slice touching a visual file class. Beyond arming the sub-skill, the floor sets the **close condition**: the Inspector must not hand a visual slice to the Archivist until `inspection_report.md` carries both `visual_confirmed` and `visual_signoff` (the human visual sign-off — see GOVERNANCE Discipline Gates / [`LOOP_ARCHITECTURE.md`](../protocol/LOOP_ARCHITECTURE.md) Scenario 2.5).
