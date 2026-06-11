# Retention Runbook

> **Owner:** project-operational doc — adapt freely to your project.
> **Read by:** anyone running a Hygiene sweep (`/lfe-hygiene` § 7 Retention Check) in this repository.
> **Authoritative rules:** [`.agents/skills/lfe-hygiene/SKILL.md`](../../.agents/skills/lfe-hygiene/SKILL.md) § 7 and the Retention Policy table in [`GOVERNANCE.md`](../protocol/GOVERNANCE.md). This runbook is the operational supplement — step-by-step procedures that link to the framework rules without restating them.

Every retention-managed file has a **hot tier** (active, in `.docs/quality/` or `.docs/architecture/`) and a **cold tier** (archived, in `.docs/archive/`). The Hygiene sweep — every 5 sessions per the Retention Policy — walks the table in `GOVERNANCE.md` and moves aged-out entries from hot to cold. Framework defaults apply unless your project declares a `## Retention Policy Overrides` section in `LLM_AGENT_GUIDE.md`: 15 sessions, 7 milestones, 5-session sweep cadence.

## The retention-managed files

| Hot file | Cold sibling | Hot retention rule | Archive trigger |
|---|---|---|---|
| `.docs/quality/CHANGELOG.md` | `.docs/archive/changelog-history.md` | 7 most recent milestones | When an 8th milestone ships |
| `.docs/architecture/architecture-decisions.md` | `.docs/archive/architecture-decisions-history.md` | All `Accepted`/`Proposed` ADRs + last 15 sessions of `Superseded`/`Deprecated` | At hygiene sweep |
| `.docs/quality/PROTOCOL_DEBT.md` | `.docs/archive/protocol-debt-history.md` | All `open`; `resolved` kept 1 hygiene cycle then archived | At hygiene sweep |
| `.docs/quality/known-issues.md` | `.docs/archive/known-issues-history.md` | All `open`; `resolved` / `won't-fix` kept 1 hygiene cycle | At hygiene sweep |

## Pre-flight (before any archival action)

1. Confirm the hot file ends with the canonical Cold Tier Pointer:
   > `**Archive:** Older entries are in [archive/<filename>-history.md](../archive/<filename>-history.md). Last archive sweep: session N.`
2. Confirm the cold sibling starts with a Hot Tier Pointer plus an Index table header.
3. Read the Retention Policy row in `GOVERNANCE.md` for the file under consideration and identify its rule.
4. If either pointer is missing or malformed, **stop** — that is a Hygiene violation; flag it before continuing.

## Per-file procedures

### CHANGELOG.md (7-milestone rolling window)
**Trigger:** an 8th milestone is about to be appended.
1. Identify the oldest milestone (an H2 section; entries are ordered most-recent-first).
2. Cut the oldest milestone (entire H2 + body, up to the next H2) from the hot file.
3. In the cold sibling: paste it immediately after the index table (newest archives sit closest to the index) and add an index row (date / title / archived-in session).
4. In the hot file: prepend the new milestone and update the Cold Tier Pointer's session count.
5. Verify: the hot file holds at most 7 milestone headings; the archive index gained exactly one row.

### architecture-decisions.md
**Trigger:** Hygiene sweep. Only `Superseded` or `Deprecated` ADRs older than (current session − 15) are candidates; `Accepted` and `Proposed` are never archived.
1. For each candidate, cut the full ADR section from the hot file.
2. In the cold sibling: paste after the index table and add an index row (id / date / title / status / archived-in session).
3. Remove the stale row from the hot file's index table; update the Cold Tier Pointer.
4. Check whether code comments, tests, or docs reference the archived ADR by number; update or annotate those references.

### PROTOCOL_DEBT.md (resolved entries kept 1 cycle)
**Trigger:** Hygiene sweep finds `resolved` rows at least one cycle (5 sessions) old.
1. Cut each aged-out row from the debt table; add it to the cold sibling's index (Date / Mission / Reason / Resolution / Archived-in session).
2. Update the Cold Tier Pointer.

### known-issues.md (resolved / won't-fix kept 1 cycle)
**Trigger:** Hygiene sweep finds entries under `## ✅ Resolved Issues` at least one cycle old.
1. Cut each aged-out entry (heading + body); add an index row in the cold sibling (Date opened / Title / Final status / Archived-in session), body below the index.
2. Update the Cold Tier Pointer.

## What not to do

- **Adopters: keep the LFE protocol surface read-only.** Project-specific retention behavior belongs in this runbook, in `pipeline_status.md`'s notes, or as explicit overrides under `## Retention Policy Overrides` in `LLM_AGENT_GUIDE.md` — never as edits to `lfe-archivist` / `lfe-hygiene` / `GOVERNANCE.md`. (Framework contributors change the protocol itself via upstream pull requests instead — see `CONTRIBUTING.md`.)
- **Never archive an `Accepted` or `Proposed` ADR.** Only `Superseded` and `Deprecated`, and only past the 15-session window.
- **Never delete an archived entry.** The cold tier is append-only; the index row is the audit trail.
- **Never move an entry without updating the hot file's Cold Tier Pointer session counter.**

## Cross-references

- Framework rule: [`.agents/skills/lfe-hygiene/SKILL.md`](../../.agents/skills/lfe-hygiene/SKILL.md) § 7 Retention Check.
- Policy table: [`GOVERNANCE.md`](../protocol/GOVERNANCE.md) § Retention Policy.
- Archivist retention scope: [`.agents/skills/lfe-archivist/SKILL.md`](../../.agents/skills/lfe-archivist/SKILL.md) Hard Rule 6.
- Cold-tier shelf index: [`.docs/archive/README.md`](../archive/README.md).
