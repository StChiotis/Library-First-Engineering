## Summary
<!-- 1-3 bullet points: what changed and why -->

## Changes
<!-- List the key files or areas modified -->

## Test plan
<!-- How was this verified? Hygiene report, manual testing, etc. -->

## LFE compliance checklist
> Skills are dispatched by the framework, not invoked manually (see `LLM_AGENT_GUIDE.md` §8.8). The checks below describe outcomes; the agent running this PR will have produced them as side-effects of the pipeline.

- [ ] `active_plan.md` existed at some point in this branch's history (not LFE-FORCE)
- [ ] `plan_critique.md` reached `verdict: PASS` OR `verdict: WARN` with a non-null `brain_confirmation` before any `src/` edits
- [ ] Inspector phase ran (`inspection_report.md` `status: passed`, or `status: escalated` with explicit Brain triage option recorded)
- [ ] Archivist sync ran (`.docs/` updates + CHANGELOG entry where applicable)
- [ ] Hygiene findings reviewed (if a `/lfe-hygiene` sweep was due this cycle)
- [ ] Floor Map (`.docs/README.md`) updated if files were added/removed
- [ ] `pipeline_status.md` reflects current state
- [ ] No substantive files modified outside the stated scope
