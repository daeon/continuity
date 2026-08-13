---
name: continuity
description: "Use when ongoing work may cross context, session, or agent boundaries. Keep a small durable Markdown memory of current state, important facts/decisions, evidence, blockers, and next action so future agents can resume without rediscovery. Do not use for trivial one-shot work."
---

# Continuity

Keep a **small durable work-memory file** for work that may need to resume later.

The goal is not to record what happened. The goal is to preserve the **minimum state a future agent needs to continue correctly**.

**Rule:** persist information whose loss would cause meaningful rediscovery, mistakes, or ambiguity. Current state is canonical; timeline is only supporting history.

## When to use

Use Continuity when work is likely to survive the current context/session, another agent may continue it, or non-obvious investigation/decisions would otherwise be lost.

Do not use it for trivial edits, simple questions, or work whose relevant state is obvious from the repo.

## File

If the user gives a path, use it. Otherwise use one stable file for the work:

```text
.continuity/<slug>.md
```

Use `~/.continuity/<slug>.md` only for work that does not belong to one workspace. Do not create a new file just because the date changed.

## Format

Keep the file compact. Omit empty sections.

```md
# Continuity: <work>

- Status: active | blocked | paused | complete | abandoned
- Updated: <timestamp>
- Workspace: `<path>`

## Objective
- [ ] <desired outcome>

## Current State
- State: <what is true now>
- Next: <highest-value next action>
- Blockers: <none or blockers>

## Memory
- Fact: <durable fact> — Evidence: `<pointer>`
- Decision: <choice> — Why: <reason>
- Hypothesis: <unverified idea> — Needs: <how to test>
- Constraint: <important requirement or assumption>

## Evidence
- `<test/commit/file/PR/artifact>` — <short result and version/time if staleness matters>

## Timeline
### <timestamp> - <kind>
- <important change in state or understanding>
```

## Behavior

### Start or resume

- If Continuity already exists, read it **before** doing substantive work.
- Resume from `Current State`, especially `Next` and `Blockers`.
- Inspect timeline or cited evidence only when current state is insufficient or needs verification.
- Revalidate volatile claims such as branch, HEAD, test status, PR state, deployment state, or benchmark result when staleness matters.

### Update

Update Continuity when the **durable model of the work changes**, for example:

- scope or objective changes,
- an important fact, decision, hypothesis, constraint, or blocker appears or changes,
- implementation materially changes the state,
- verification passes/fails,
- the next action changes,
- work pauses, resumes, completes, or is abandoned.

Do not log every command or intermediate thought.

When something changes:

1. Update `Current State` first.
2. Update/remove affected `Memory` entries so they reflect present truth.
3. Add evidence when it materially supports future decisions.
4. Append one short timeline entry only if the change is worth remembering historically.

Never leave a disproven hypothesis or superseded decision written as current truth. Never silently promote a hypothesis to fact.

### Keep it small

Continuity should be cheaper to read than rediscovering the work.

- Prefer concise bullets over prose.
- Do not copy raw logs, large outputs, or information trivially recoverable from Git.
- Do not store secrets, credentials, tokens, or chain-of-thought.
- Point to artifacts instead of embedding them.
- Remove duplicate or stale state.

When the file becomes noisy (roughly >100 lines, or the timeline starts competing with current state), **consolidate** it: rewrite Current State/Memory to the latest truth, preserve important evidence, and collapse old timeline detail to only the few entries that still explain meaningful decisions or reversals.

### Handoff

Continuity is the canonical state; handoff should mostly point to it:

```md
## Continuation
- Continuity: `.continuity/<slug>.md`
- Instruction: Read Continuity first and resume from `Current State > Next`.
```

Avoid duplicating detailed state into the handoff. If they disagree, prefer the newer evidence and update Continuity.

### Close

When work ends, set `Status` to `complete` or `abandoned`, record the final state and verification, clear obsolete blockers/next actions, and consolidate if needed.

## Writer delegation

The main agent owns judgment: what is true, important, stale, or next. If a cheap `continuity_writer` subagent is available, delegate only the mechanical file edit.

Give it the path plus exact changes. It may read only the target Continuity file and must not inspect the repo, investigate facts, modify source code, or invent content.

Example delegation:

```text
Update Continuity only.
Path: .continuity/<slug>.md
Exact changes:
- set Updated to <timestamp>
- set Current State to <exact content>
- add/remove these Memory items: <exact content>
- add this Evidence: <exact content>
- append this Timeline entry: <exact content, if any>
Do not inspect the repo or infer missing information.
```

If no writer subagent is available, update the file directly using the same rules.

## Test of quality

A future competent agent with the workspace and this file—but none of the prior conversation—should be able to continue correctly after a quick read.

If it must reconstruct current truth from a long history, Continuity is too noisy. If it must repeat substantial investigation, Continuity is missing something important.
