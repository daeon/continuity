<p align="center">
  <img src="continuity.svg" alt="Continuity — Agents forget. Work shouldn't." width="760" />
</p>

<p align="center">
  <strong>Durable working memory for coding agents — in one small Markdown file.</strong>
</p>

<p align="center">
  <img alt="Codex Skill" src="https://img.shields.io/badge/Codex-Skill-111827?style=flat-square" />
  <img alt="Markdown memory" src="https://img.shields.io/badge/memory-Markdown-2563EB?style=flat-square" />
  <img alt="Context light" src="https://img.shields.io/badge/context-lightweight-7C3AED?style=flat-square" />
  <img alt="No database" src="https://img.shields.io/badge/database-none-06B6D4?style=flat-square" />
</p>

# Continuity

**Resume work, not conversation.**

When a session ends or another agent takes over, the code may still be there, but the useful context isn't: what we learned, what failed, and what to do next.

Continuity keeps that context in one small Markdown file per workstream. The next agent reads the current state and continues instead of repeating the investigation. No database, transcript archive, or background service.

[SKILL.md](SKILL.md) defines the full format and rules. This README explains how to use it.

## 🧭 When to use it

Use Continuity for work that may cross sessions or agents: an investigation, implementation, experiment, or anything with findings you'd rather not rediscover.

Skip quick one-shot tasks and state that's already obvious from the workspace.

## 🚀 Install and use

Copy the downloaded `continuity` directory into a Codex skills location. From its parent directory:

```bash
mkdir -p ~/.agents/skills
cp -R continuity ~/.agents/skills/continuity
```

Or keep it repo-scoped with your other project skills.

Then ask:

```text
Use continuity for this investigation.

Continue this work using the existing continuity file.

Update continuity before handing this off.
```

Records live at `.continuity/<slug>.md` unless you provide another path. Keep one stable file per workstream. Use `~/.continuity/<slug>.md` only for work that isn't tied to a workspace.

For projects where you regularly resume work, this optional line in `AGENTS.md` helps the agent find the record:

```md
When resuming ongoing work, check `.continuity/` for relevant active state before rediscovering prior work.
```

## 🔁 How it works

The file has two parts. The **current snapshot** holds the objective, current state, useful memory, artifacts, evidence, and next action. The **change log** records meaningful findings, decisions, reversals, and failures.

On resume:

> Read the current snapshot. Check only what could invalidate `State > Next`, then continue from there. Read history only when needed.

The live workspace and authoritative sources still outrank the record. If they disagree, verify the source and repair Continuity. Don't repeat completed investigation without a reason.

When understanding changes, update the snapshot and append a change-log entry. Record meaningful changes, not every command. Keep facts separate from hypotheses, and preserve failed approaches when repeating them would waste work.

Reference every intentional generated artifact, excluding disposable scratch files. Use paths, URLs, or IDs with a short purpose rather than embedding outputs. Mark superseded artifacts without losing their pointers.

Keep the snapshot compact. Older completed phases can become a historical summary, but useful evidence and artifact pointers stay. Use one writer per file, or separate files for independent workstreams. Never store secrets, credentials, private chain-of-thought, or giant logs.

## 📝 Example

A small fictional record, with illustrative paths and results:

```md
# Continuity: cache-refresh

- Status: active
- Updated: 2026-08-12T16:10:00-07:00

## Objective
Prevent stale cached values after an update.

## State
- Now: Invalidation fix implemented; focused tests pass.
- Next: Run integration tests for updates across two workers.
- Blocked: none

## Memory
- Fact: The update path skipped cache invalidation. — Evidence: `src/cache.py` at `abc1234`
- Decision: Invalidate after a successful write. — Why: failed writes must not change cached state.
- Hypothesis: Another worker may retain stale values. — Test: two-worker integration test.

## Active Artifacts
- `patches/cache-refresh.diff` — implementation patch for review

## Current Evidence
- `pytest tests/test_cache.py` — 12 passed at `def5678`; integration tests not run.

## Change Log
### 2026-08-12T16:10:00-07:00 - verification
- Update: Focused tests passed after the invalidation fix.
- Evidence: `pytest tests/test_cache.py` — 12 passed at `def5678`.
- Artifacts: `patches/cache-refresh.diff` — patch under test.
- Next: Run the two-worker integration test.
```

The next agent has a concrete starting point. It checks what matters for the integration test rather than reopening the whole investigation.

## 🤝 Handoff and finish

A handoff points to the record instead of copying it:

```md
## Continuation
- Continuity: `.continuity/cache-refresh.md`
- Instruction: Read Continuity first and resume from `State > Next`.
```

When work ends, set the final status, update verification and artifact references, append the final event, and clear obsolete blockers or next actions.

## ✅ The practical test

Give a fresh agent the workspace and Continuity file, without the old conversation. Can it find the artifacts, avoid known dead ends, and continue correctly after a quick read?

That's the test. Keep changes that make this easier; don't add process just to remember more.
