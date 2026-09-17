---
name: continuity
description: "Preserve a compact current snapshot and evidence-backed change log for ongoing work that may cross context, session, or agent boundaries. Use when findings, decisions, blockers, artifacts, or the next action could otherwise be lost. Resume from existing Continuity before repeating investigation. Skip trivial one-shot work."
---

# Continuity

Preserve the **minimum trustworthy record a future agent needs to resume correctly**.

Use two complementary layers:

- **Current snapshot:** the authoritative model of what is true now and what to do next.
- **Change log:** a concise, append-only record of meaningful transitions, findings, evidence, and artifacts.

Persist information whose loss would cause meaningful rediscovery, mistakes, or ambiguity. The snapshot answers *where work stands*; the log explains *how it got there*. Do not log routine activity.

## Use

Use Continuity when work may outlive the current context/session, another agent may continue it, or non-obvious work would otherwise be lost.

Skip trivial one-shot work or state that is obvious from the workspace.

Use the path the user provides; otherwise keep one stable file per workstream:

```text
.continuity/<slug>.md
```

Use `~/.continuity/<slug>.md` only for work not tied to one workspace.

## Record

Keep it compact. Omit empty sections.

```md
# Continuity: <work>

- Status: active | blocked | paused | complete | abandoned
- Created: <timestamp>
- Updated: <timestamp>
- Workspace: `<path>`

## Objective
<desired outcome>

## State
- Now: <what is true now>
- Next: <exact next action>
- Blocked: <none or blocker>

## Memory
- Fact: <durable fact> — Evidence: `<pointer>`
- Decision: <choice> — Why: <reason>
- Hypothesis: <unverified idea> — Test: <how to verify>
- Constraint: <requirement or assumption>

## Active Artifacts
- `<path / URL / id>` — <what it is and why it matters>

## Current Evidence
- `<file / command / commit / PR / result>` — <short result>

## Change Log
### <timestamp> - <kind>
- Update: <meaningful change, finding, decision, reversal, or failure>
- Evidence: `<path / command / URL / id>` <short result>
- Artifacts: `<path / URL / id>` <why it matters, if applicable>
- Next: <next action, if changed>

## Historical Summary
- <optional compacted summary of an older completed phase; retain artifact and evidence pointers>
```

## Rules

1. **Resume first.** If relevant Continuity exists, read its current snapshot before substantive work. Check only what could invalidate `State > Next`, then continue from there. Read history only when needed.
2. **Current truth wins.** Authoritative workspace/source state > Continuity > old conversation. If they conflict, verify and repair Continuity.
3. **Update both layers.** When understanding changes, update `State` and `Memory`, then append one `Change Log` entry. Never silently promote a hypothesis to fact or leave superseded state as current truth.
4. **Log meaningful transitions only.** Append an entry when scope, objective status, finding, contradiction, decision, blocker, risk, important artifact, verification result, handoff, or stop/resume state changes. Do not log routine commands or incidental activity.
5. **Preserve expensive failures.** Record a rejected approach or failed verification in the `Change Log` when repeating it would waste meaningful work, add risk, or cause confusion. Keep an active constraint or fact in `Memory` when it still affects the next action.
6. **Keep artifacts discoverable.** Add a reference to every intentional generated artifact: files, patches, reports, plans, screenshots, benchmark outputs, PRs, commits, or other deliverables. Skip only disposable temp/scratch files. Prefer stable paths/URLs/IDs and a short purpose. Do not embed the artifact. Place active artifacts in `Active Artifacts`; also cite them in the relevant change-log event. If superseded, mark them superseded rather than losing the reference.
7. **Point to evidence.** Keep concise pointers instead of copying logs or large outputs. Cite the evidence that supports each log entry when available. Revalidate volatile claims such as branch, HEAD, tests, deployments, benchmarks, or PR state when staleness matters.
8. **Keep it compact without erasing provenance.** Consolidate stale snapshot content. Do not delete useful history merely to shrink the file; compact old completed phases into `Historical Summary` while retaining artifact and evidence pointers. If a future agent must reconstruct the present from history, repair the snapshot.
9. **Single writer.** Do not let concurrent agents independently mutate the same Continuity file; serialize updates or use separate workstream files.
10. **Keep it safe.** Never store secrets, credentials, tokens, private chain-of-thought, giant outputs, or disposable scratch state.

## Handoff and close

Continuity is canonical. A handoff should mostly point to it:

```md
## Continuation
- Continuity: `.continuity/<slug>.md`
- Instruction: Read Continuity first and resume from `State > Next`.
```

When work ends, set the final status, state, verification, and artifact references; append a final change-log event; clear obsolete blockers/next actions; and compact stale history if needed.

## Writer

If a cheap `continuity_writer` is available, the main agent may delegate only the mechanical file mutation and must provide exact changes. The writer may read only the Continuity file; it must not investigate, modify source, decide what matters, or invent content.

## Quality test

A competent future agent with the workspace and Continuity—but none of the prior conversation—should be able to identify the objective, trust the current state, find all intentional generated artifacts, avoid known dead ends, and execute the next action after a quick read.
