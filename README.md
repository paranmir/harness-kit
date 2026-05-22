# Agentlaw-Governed Project

This repository has been initialized with `agentlaw`, a law-first governance
kit for AI coding agents.

## Package Source And Public Seed

Installable releases come from the PyPI package `agentlaw`. The public seed
repository at https://github.com/paranmir/agentlaw shows the target-facing
starter content that gets installed into projects.

## Start Here

### Entry path

1. Read `AGENTS.md` for the repository routing map.
2. Read `AGENTLAW_CONSTITUTION.md` for the highest-level agreement.
3. Read `docs/references/project-overview.md` for this project's purpose,
   audience, structure, and architecture map.

### Daily-use path

1. Restore the current session from `memory/working-set.md`.
2. Use `memory/LOOKUP_RULES.md` to decide which memory files or logs to read.
3. Keep durable project facts in approved artifacts such as `docs/law/*`,
   `docs/references/*`, `docs/plans/*`, and `memory/*`.

## Commands Users Should Know

Use these commands from a terminal. Replace `.` with a project path when you
are not already in the initialized project root.

```bash
# Install or update the CLI
pipx install agentlaw
pipx install "agentlaw[embeddings]"
pipx inject agentlaw sentence-transformers huggingface_hub
pipx upgrade agentlaw
agentlaw --version

# If an older 0.1.x install still reports 0.1.5 as current, force pipx
# to ask PyPI directly instead of a stale cache or mirror:
pipx upgrade agentlaw --pip-args "--no-cache-dir --index-url https://pypi.org/simple"

# Initialize or merge the harness into a project
agentlaw init . --skip-model --setup-agents prompt
agentlaw init . --merge --skip-model

# Register, inspect, or repair agent host integration
agentlaw agent-setup --client auto --target . --verify
agentlaw agent-setup --client codex --target . --apply --yes
agentlaw setup-status --target .
agentlaw setup-status --target . --after-update
agentlaw align --check --target .
agentlaw align --write --target .
agentlaw mcp-recover --target . --client auto --json

# Restore, verify, and operate this project
agentlaw session-restore --target . --json
agentlaw memory-runtime-check --target . --json
agentlaw memory-runtime-repair --target . --json
agentlaw verify .
agentlaw run-mcp --target .
agentlaw now --json
```

Global installation only makes the `agentlaw` command available. A project
does not opt into agentlaw until someone explicitly runs `agentlaw init` in
that project. After `agentlaw init` or `pipx upgrade agentlaw`, run
`agentlaw setup-status` and report the result before claiming the harness is
ready. The status report names optional pieces that were not installed,
host/MCP pieces that are not activated yet, and the concrete next action for
each gap.

## Harness Workflow

Use agentlaw as an operating loop, not only as a command list:

1. Restore context at the start of a session with `agentlaw_session_restore` or `agentlaw session-restore --target . --json`.
2. State the task and let the agent classify whether it is trivial, plan-required, fix/init/update, or release/deploy-adjacent.
3. For non-trivial work, require a reviewed plan before implementation. The plan review selects the right persona review passes, records user gates, and names acceptance oracles.
4. Implement from the reviewed plan, then run the listed oracle checks, `agentlaw verify .`, and any focused tests before archiving the plan.
5. Save session state with `agentlaw_session_save` at milestones and before context compaction.

### When to use init, update, fix, and align

- Use `AGENTLAW_INIT_TOOL.md` when bootstrapping agentlaw into a new project or re-initializing the scaffold.
- Use `AGENTLAW_UPDATE_TOOL.md` when the installed kit or scaffold has changed and this project needs those upstream harness updates merged without losing local project facts.
- Use `AGENTLAW_FIX_TOOL.md` when the harness is drifting, a tracker-policy violation happened, the agent bypassed a rule, MCP/memory state looks inconsistent, or the same governance failure repeats.
- Use `AGENTLAW_ALIGN_TOOL.md` and `agentlaw align --check --target .` when local laws, root controls, directories, or command surfaces changed and routing/README surfaces may be stale. Use `agentlaw align --write --target .` only for safe routing updates reported as autofixable.

### Plan, persona, and oracle review

The normal non-trivial path is plan first, implementation second, oracle last. A plan states the task contract, affected surfaces, user gates, risks, rollback paths, and acceptance criteria. The persona review checks that plan from specific lenses such as trigger coverage, acceptance criteria, affected surfaces, external contracts, user gates, and form-vs-substance. The oracle phase checks whether the completed work satisfies the plan's criteria before the plan is archived.

### If the harness looks broken

First ask the agent to restore context again. If MCP or host integration still looks wrong, run:

```bash
agentlaw setup-status --target . --client auto
agentlaw session-restore --target . --json
agentlaw mcp-recover --target . --client auto --json
agentlaw memory-runtime-check --target . --json
agentlaw memory-runtime-repair --target . --json
agentlaw verify .
```

If the problem is repeated, rule-related, or affects the agent's ability to follow the harness, treat it as fix-class work and tell the agent to start from `AGENTLAW_FIX_TOOL.md`.

### Host reminder hooks

`agentlaw init` registers a `UserPromptSubmit` reminder hook for supported
hosts:

- Claude Code receives `.claude/settings.json` entries that run
  `agentlaw user-prompt-hook`.
- Codex receives `.codex/config.toml` entries that run
  `agentlaw user-prompt-hook`.

Hook registration is additive. Existing host hooks are preserved, and this
repository should not treat host config as a rule store. The hook is a reminder
channel that points the agent back to the governing files in this repository.

### Agent Skills

This scaffold includes Agent Skills in both common host locations:

- `.agents/skills/agentlaw-governance/` and
  `.claude/skills/agentlaw-governance/` route fix/init/update/governance work
  to the matching root control document.
- `.agents/skills/agentlaw-plan-authoring/` and
  `.claude/skills/agentlaw-plan-authoring/` route plan creation, review,
  execution, oracle, and archive work to the planning protocol.
- `.agents/skills/agentlaw-post-task-retrospective/` and
  `.claude/skills/agentlaw-post-task-retrospective/` route task closeout
  lessons to skill, law/contract, test/verifier, memory/reference, tracker,
  or chat-only outcomes.

The duplicated directories are host compatibility paths, not separate rule
systems. Governing content remains in `AGENTLAW_*`, `docs/law/*`, and
`docs/planning-protocol/*`.

### Fix/update path

Before consequential governance work, use the matching root control document:

- `AGENTLAW_FIX_TOOL.md` for local drift, tracker-policy violations, or rule
  bypasses.
- `AGENTLAW_INIT_TOOL.md` for first-time or re-initialization work.
- `AGENTLAW_UPDATE_TOOL.md` for incorporating upstream harness changes.
- `AGENTLAW_ALIGN_TOOL.md` for local routing/readme reconciliation after
  harness structure or command surfaces change.

### Advanced-reference path

Use `docs/law/*` for governing rules, `docs/contracts/*` for tool and update
contracts, and `docs/planning-protocol/*` for plan format, review method, and
persona decks.

## Memory Layout

The canonical memory layer is plain Markdown under `memory/*`:

- `memory/working-set.md` — current goal, next actions, open questions, and
  handoff state.
- `memory/LOOKUP_RULES.md` — how to choose between memory tools and current
  repository reads.
- `memory/known-facts/` — durable facts about the project.
- `memory/logs/` — append-only decisions, corrections, session saves, and
  verification notes.
- `memory/rules/` — durable behavioral rules that do not belong in law.
- `memory/preferences.md` — user or maintainer preferences.

Derived runtime state belongs under `.harness/`, including the SQLite index at
`.harness/index/meta.db` and any downloaded embedding model. `.harness/` is
rebuildable runtime state, not the source of truth.

## Multi-project note

Codex uses a user-level MCP registration, so registering agentlaw with Codex
only makes the command available globally. It is not project setup. When Codex
is opened in a project that has not explicitly run `agentlaw init`,
`agentlaw run-mcp` exits instead of creating `.harness`.

## Project Specialization

After initialization, specialize the starter law and reference files for this
project. Derived runtime state belongs under `.harness/` and is recreated by
the `agentlaw` package.
