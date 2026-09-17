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

---

# Continuity

**Resume work, not conversation.**

Coding agents are great at solving hard problems inside the context they have *right now*. Then the session ends, context gets compacted, another agent takes over, or you return tomorrow — and the expensive part of the work disappears.

**Continuity gives an agent a tiny durable cold-resume state.** It keeps the minimum useful state of ongoing work in `.continuity/<slug>.md` so a future agent can resume without replaying the whole conversation or rediscovering the investigation.

```text
session A                          session B
─────────                          ─────────
investigate ──┐                 ┌─ read state
implement     ├─> Continuity ───┤  resume
verify      ──┘   .md memory    └─ continue
```

No database. No embeddings. No vector store. No giant transcript.

Just the state that matters.

## ✨ Why Continuity?

Most agent memory systems try to remember **more**.

Continuity deliberately remembers **less — but better**.

It preserves things whose loss would cause meaningful rediscovery, mistakes, or ambiguity:

- what the agent is trying to accomplish,
- what is true **now**,
- important facts, decisions, hypotheses, and constraints,
- evidence worth returning to,
- references to generated artifacts,
- blockers,
- and the highest-value next action.

The timeline is secondary. **The current snapshot is canonical.**

That distinction keeps Continuity useful instead of turning it into another context dump.

## 🧠 The idea in 30 seconds

After an investigation, an agent might leave:

```md
# Continuity: ftp-parser-hardening

- Status: active
- Updated: 2026-08-12T16:10:00-07:00

## Objective
- [ ] Prevent malformed known FTP commands from bypassing validation

## State
- Now: Parser fix implemented; focused tests pass.
- Next: Run integration tests against lenient upstream server.
- Blocked: none

## Memory
- Fact: Outer helper scans only SP-form commands. — Evidence: `src/ftp.c:545`
- Decision: Reject malformed known-command separators instead of passthrough. — Why: prevents parser differential
- Hypothesis: Some upstream servers accept non-SP separators. — Needs: integration test

## Active Artifacts
- `patches/ftp-parser-hardening.diff` — implementation patch for review
- `reports/ftp-integration-results.md` — generated integration test report

## Current Evidence
- `pytest tests/ftp_parser.py` — 47 passed at `abc1234`
```

A fresh agent does not need the old chat. It reads the current snapshot, checks only what could invalidate `State > Next`, and keeps going. It reads history only when needed.

## 🚀 Install

Copy the `continuity` directory into a Codex skills location, for example:

```bash
mkdir -p ~/.agents/skills
cp -R continuity ~/.agents/skills/continuity
```

Or keep it repo-scoped with your other project skills.

Then invoke it naturally when work should survive the current context:

```text
Use continuity for this investigation.
```

```text
Continue this work using the existing continuity file.
```

```text
Update continuity before handing this off.
```

## ⚙️ How it behaves

Continuity has a deliberately small contract:

1. **Read before resuming.** If a continuity file exists, read the current snapshot first. Check only what could invalidate `State > Next`, then continue. Read history only when needed.
2. **Update the model, not the diary.** Record changes in durable understanding, not every command.
3. **Keep current truth current.** Superseded facts and disproven hypotheses do not linger as truth.
4. **Keep artifacts discoverable.** Reference every intentional generated artifact (except disposable temp/scratch files); mark superseded outputs instead of losing the pointer.
5. **Point to evidence.** Reference commits, files, tests, PRs, or logs instead of copying them.
6. **Consolidate aggressively.** If the file becomes noisy, compress history into the latest useful state.
7. **Revalidate volatile state.** Branch, HEAD, tests, deployments, benchmarks, and PR status can go stale.

## 🪶 Intentionally lightweight

Continuity is not trying to be:

| Not this | Instead |
|---|---|
| A transcript | A compressed work state |
| A vector database | A Markdown file |
| Chain-of-thought storage | Explicit facts and decisions |
| Command logging | Evidence pointers |
| Permanent truth | Revisable working memory |
| A second project tracker | Just enough state to resume |

The skill itself stays small because **a memory system that consumes too much context defeats its own purpose**.

## 🔁 Continuity + handoff

A handoff should not duplicate the state. It should point to it:

```md
## Continuation
- Continuity: `.continuity/ftp-parser-hardening.md`
- Instruction: Read Continuity first and resume from `State > Next`.
```

Continuity is the durable state. Handoff is just the routing message.

## 🧩 Example lifecycle

```text
Start work
   │
   ▼
Create .continuity/<slug>.md
   │
   ▼
Investigate / build / test
   │
   ├── understanding changed? ── yes ──> update Continuity
   │
   ▼
Context/session ends
   │
   ▼
Future agent reads Continuity
   │
   ▼
Check what could invalidate Next
   │
   ▼
Resume from Next
```

## 🧭 Reliability rules

Continuity follows a simple truth hierarchy:

```text
live workspace / authoritative source
              >
          Continuity
              >
        old conversation
```

If the record conflicts with the live workspace, verify the source of truth and repair Continuity. Preserve rejected approaches when repeating them would waste meaningful work or create risk. If multiple agents are active, serialize writes to one Continuity file or give each workstream its own file.

### Generated artifacts are part of continuity

Every intentional generated artifact should remain discoverable from the state file (excluding disposable temp/scratch files): generated files, patches, reports, plans, screenshots, benchmark outputs, PRs, commits, and other deliverables. Store a stable pointer and a one-line purpose—not the artifact contents. If a newer artifact replaces an older one, mark the old reference as superseded rather than erasing the trail.

## 🛡️ Memory hygiene

Continuity intentionally avoids storing:

- secrets, credentials, or tokens,
- private chain-of-thought,
- giant logs and command output,
- every command the agent ran,
- information trivially recoverable from Git,
- stale duplicate state.

A useful test is simple:

> **Would losing this information cause meaningful rediscovery, mistakes, or ambiguity?**

If not, it probably does not belong in Continuity.

## 💡 Design principle

The quality bar is not “did we record everything?”

It is:

> **Can a competent future agent, with the workspace and this file but none of the previous conversation, continue correctly after a quick read?**

If yes, Continuity is doing its job.

## 🌱 Why this could become a useful primitive

Continuity is deliberately generic. Debugging, implementation, research, experiments, migrations, performance investigations, and repo reviews all need the same underlying capability:

**preserve the smallest reliable state needed to continue.**

That makes Continuity useful as a shared memory layer beneath other agent skills instead of every workflow inventing its own logging format.

## 🔌 Optional repo bootstrap

For projects where cold-resume matters, add one lightweight instruction to `AGENTS.md`:

```md
When resuming ongoing work, check `.continuity/` for relevant active state before rediscovering prior work.
```

This keeps discovery reliable without adding hooks, databases, or background services.

## 🤝 Contributing

The best improvements make Continuity **smaller, clearer, or more reliable**.

Good contributions include:

- better consolidation rules,
- sharper resume behavior,
- compatibility improvements across agent workflows,
- real-world examples that expose failure modes,
- reductions in unnecessary instruction/context cost.

If a feature makes the skill significantly heavier, it should earn that complexity.

## ⭐ If this is useful

Star the repository so other agent builders can find it, and share the pattern with anyone building long-running Codex workflows.

**Resume work, not conversation.**
