---
name: continuity
description: "Preserve minimal durable state for ongoing work that may cross context, session, or agent boundaries. Use when losing findings, decisions, blockers, generated artifacts, or the next action would cause rediscovery or mistakes. Resume from existing Continuity before repeating investigation. Skip trivial one-shot work."
---

# Continuity

Preserve the **minimum trustworthy state a future agent needs to resume correctly**.

Persist information whose loss would cause meaningful rediscovery, mistakes, or ambiguity. Maintain current truth; do not keep a diary.

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

## Artifacts
- `<path / URL / id>` — <what it is and why it matters>

## Evidence
- `<file / command / commit / PR / result>` — <short result>

## Recent Changes
- <meaningful transition, reversal, or rejected approach>
```

## Rules

1. **Resume first.** If relevant Continuity exists, read it before substantive work. Resume from `State > Next`; inspect evidence only as needed.
2. **Current truth wins.** Authoritative workspace/source state > Continuity > old conversation. If they conflict, verify and repair Continuity.
3. **Update the model, not the diary.** Change `State` and `Memory` when understanding changes. Never silently promote a hypothesis to fact or leave superseded state as current truth.
4. **Preserve expensive failures.** Record a rejected approach when repeating it would waste meaningful work, add risk, or cause confusion.
5. **Keep artifacts discoverable.** Add a reference to every intentional generated artifact: files, patches, reports, plans, screenshots, benchmark outputs, PRs, commits, or other deliverables. Skip only disposable temp/scratch files. Prefer stable paths/URLs/IDs and a short purpose. Do not embed the artifact. If superseded, mark it superseded rather than losing the reference.
6. **Point to evidence.** Keep concise pointers instead of copying logs or large outputs. Revalidate volatile claims such as branch, HEAD, tests, deployments, benchmarks, or PR state when staleness matters.
7. **Keep it small.** Remove duplication and stale history. `Recent Changes` is optional and should contain only a few changes still useful for resumption. If a future agent must reconstruct the present from history, consolidate.
8. **Single writer.** Do not let concurrent agents independently mutate the same Continuity file; serialize updates or use separate workstream files.
9. **Keep it safe.** Never store secrets, credentials, tokens, private chain-of-thought, giant outputs, or disposable scratch state.

## Handoff and close

Continuity is canonical. A handoff should mostly point to it:

```md
## Continuation
- Continuity: `.continuity/<slug>.md`
- Instruction: Read Continuity first and resume from `State > Next`.
```

When work ends, set the final status, state, verification, and artifact references; clear obsolete blockers/next actions and consolidate stale history.

## Writer

If a cheap `continuity_writer` is available, the main agent may delegate only the mechanical file mutation and must provide exact changes. The writer may read only the Continuity file; it must not investigate, modify source, decide what matters, or invent content.

## Quality test

A competent future agent with the workspace and Continuity—but none of the prior conversation—should be able to identify the objective, trust the current state, find all intentional generated artifacts, avoid known dead ends, and execute the next action after a quick read.
