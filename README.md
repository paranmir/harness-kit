# Agentlaw-Governed Project

This repository has been initialized with `agentlaw`, a law-first governance
kit for AI coding agents.

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

# Initialize or merge the harness into a project
agentlaw init . --skip-model --setup-agents prompt
agentlaw init . --merge --skip-model

# Register, inspect, or repair agent host integration
agentlaw agent-setup --client auto --target . --verify
agentlaw agent-setup --client codex --target . --apply --yes
agentlaw mcp-recover --target . --client auto --json

# Restore, verify, and operate this project
agentlaw session-restore --target . --json
agentlaw memory-runtime-check --target . --json
agentlaw memory-runtime-repair --target . --json
agentlaw verify .
agentlaw run-mcp --target .
agentlaw now --json
```

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

The duplicated directories are host compatibility paths, not separate rule
systems. Governing content remains in `AGENTLAW_*`, `docs/law/*`, and
`docs/planning-protocol/*`.

### Fix/update path

Before consequential governance work, use the matching root control document:

- `AGENTLAW_FIX_TOOL.md` for local drift, tracker-policy violations, or rule
  bypasses.
- `AGENTLAW_INIT_TOOL.md` for first-time or re-initialization work.
- `AGENTLAW_UPDATE_TOOL.md` for incorporating upstream harness changes.

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

## Project Specialization

After initialization, specialize the starter law and reference files for this
project. Derived runtime state belongs under `.harness/` and is recreated by
the `agentlaw` package.
