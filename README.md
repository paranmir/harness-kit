# Agentlaw-Governed Project

This repository has been initialized with `agentlaw`, a law-first governance
kit for AI coding agents.

## Package Source And Public Seed

Installable releases come from the PyPI package `agentlaw`. The public seed
repository at https://github.com/paranmir/agentlaw shows the target-facing
starter content that gets installed into projects.

## Start Here

### First install bootstrap

When an LLM is asked to install agentlaw for this project, use the PyPI
package as the install source and then complete the project bootstrap:

1. Install or update the CLI with `pipx install agentlaw`
   or `pipx upgrade agentlaw`.
2. Run `agentlaw init . --setup-agents prompt` in the chosen
   project root. For an already-initialized project, use
   `agentlaw init . --merge`. Use `--skip-model` only when the target already
   has a complete embedding model or the user explicitly accepts FTS-only
   memory search.
3. Follow the emitted MCP registration instructions, then restart the agent
   host so the `agentlaw-memory` server is visible.
4. In the next session, call `agentlaw_session_restore` through MCP or run
   `agentlaw session-restore --target . --json` as the CLI fallback.
5. Run `agentlaw setup-status --target . --client auto` and report any
   `not installed`, `not activated`, or `unknown` next action before
   describing the harness as ready.
6. If MCP is still missing after restart, run
   `agentlaw mcp-recover --target . --client auto --json`.

### Entry path

1. Read `AGENTS.md` for the repository routing map.
2. Read `AGENTLAW_CONSTITUTION.md` for the highest-level agreement.
3. Read `agentlaw_docs/references/project-overview.md` for this project's purpose,
   audience, structure, and architecture map.

### Daily-use path

1. Restore the current session from `agentlaw_memory/working-set.md`.
2. Use `agentlaw_memory/LOOKUP_RULES.md` to decide which memory files or logs to read.
3. Keep durable project facts in approved artifacts such as `agentlaw_docs/law/*`,
   `agentlaw_docs/references/*`, `agentlaw_docs/plans/*`, and `agentlaw_memory/*`.

## Commands Users Should Know

Use these commands from a terminal. Replace `.` with a project path when you
are not already in the initialized project root.

```bash
# Install or update the CLI
pipx install agentlaw
pipx upgrade agentlaw
agentlaw --version

# If an older 0.1.x install still reports 0.1.5 as current, force pipx
# to ask PyPI directly instead of a stale cache or mirror:
pipx upgrade agentlaw --pip-args "--no-cache-dir --index-url https://pypi.org/simple"

# Initialize or merge the harness into a project
agentlaw init . --setup-agents prompt
agentlaw init . --merge

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

MCP host registration uses the installed `agentlaw` app executable with
`run-mcp` arguments. Do not register a pipx internal venv `python.exe` or a
generic `python -m agentlaw`; those paths drift when pipx changes its managed
environment location. If a host cannot resolve `agentlaw`, use the absolute
path from `where agentlaw` or `which agentlaw` and rerun
`agentlaw agent-setup --client <host> --target . --apply --yes`.

When `setup-status` prints `LLM disclosure required`, the agent must tell the
user the harness is not fully active before doing substantive work. Until MCP
tools, Agent Skills, hooks, and runtime restore are confirmed, the agent follows
`AGENTS.md`, the root control documents, and `agentlaw_docs/law/*` manually; non-trivial
plans stay non-executable unless the required plan-review path has run.

When an LLM installs agentlaw for a user, it should not stop at "installed."
It should explain the installed command path, whether optional embeddings were
installed or skipped, whether this project was initialized, what `setup-status`
reports about MCP, skills, hooks, and session restore, how the user can restore
the next session, and which commands are most likely to matter:
`agentlaw setup-status --target . --client auto` for readiness,
`agentlaw session-restore --target . --json` when MCP is not visible,
`agentlaw mcp-recover --target . --client auto --json` for integration
diagnosis, `agentlaw verify .` for scaffold integrity, and
`agentlaw align --check --target .` before claiming routing or README surfaces
are current.

## What The Main Pieces Do

`agentlaw init` gives this project its governance scaffold. It writes the
constitution, law documents, contracts, planning protocol, memory files, skills,
and optional host-registration instructions into the project. Installing the
CLI alone is not enough; this project becomes governed only after init or merge
has placed the scaffold here.

`setup-status` is the readiness check. It tells the agent whether the scaffold
exists, whether the local runtime database is usable, whether optional
embeddings are installed, whether host MCP registration appears active, whether
skills and reminder hooks are present, and whether session restore can work. It
exists to prevent the common false-readiness failure where an agent assumes the
harness is active just because the package is installed.

Session restore and session save are the continuity path. Restore loads the
working set, current rules, active plans, recent decisions, and memory lookup
guidance before the agent answers. Save records the current goal, decisions,
warnings, and next actions before handoff or context loss. They exist so the
agent does not rely on chat memory or stale local impressions.

The memory layer has two parts: Markdown files are the source of truth, and the
runtime index makes those files searchable through MCP. If the index drifts or
is missing, repair commands rebuild derived state from the Markdown files. This
keeps project memory inspectable, versionable, and recoverable.

Hooks and Agent Skills are reminder channels. Hooks fire when the host supports
prompt-time reminders, and skills route common work such as fix, update,
planning, and retrospective closeout to the right documents. They do not replace
the law layer; they make it harder for an agent to forget to read it.

`agentlaw verify` is the drift detector. It checks that the governance scaffold
and runtime shape are intact before the project is treated as harness-ready.

## Ideas And Influences

agentlaw is not a fork or reimplementation of these projects. The links below
name ideas that shaped the product:

- [OpenAI Harness Engineering](https://openai.com/index/harness-engineering/): agent-first harness design across context, tools, memory, checks, and recovery paths.
- [OpenAI Agent evals](https://developers.openai.com/api/docs/guides/agent-evals): traceable, repeatable agent-behavior oracles.
- [Ouroboros: What is an agent harness?](https://ouroboros.bot/what-is-an-agent-harness/): harness vocabulary and the two-step review/delivery flow: validate the plan, then verify delivery before archive.
- [Hermes Agent](https://hermes-agent.nousresearch.com/docs/) and its [learning loop](https://hermes-agent.ai/features/learning-loop): post-task retrospective route for skill, law/contract, test/verifier, memory/reference, tracker, or chat-only output.
- [Wang et al., "Label Words are Anchors"](https://arxiv.org/abs/2305.14160): rubric anchors that calibrate reviewers without narrowing judgment to listed examples.
- [JuliusBrussee/caveman](https://github.com/juliusbrussee/caveman): the concise-accuracy rule; keep identifiers, paths, commands, and failure conditions while removing filler and self-narrating history.
- [Agent Skills](https://agentskills.io/): small discoverable task descriptions with full procedures loaded only when needed.
- [Model Context Protocol](https://modelcontextprotocol.io/specification/2024-11-05/index): explicit tools, resources, prompts, logging, and trust boundaries instead of hidden chat memory.

## Harness Workflow

Use agentlaw as an operating loop, not only as a command list:

1. Restore context at the start of a session with `agentlaw_session_restore` or `agentlaw session-restore --target . --json`.
2. State the task and let the agent classify whether it is trivial, plan-required, fix/init/update, or release/deploy-adjacent.
3. For non-trivial work, require a reviewed plan before execution. The plan review selects the right persona review passes, records user gates, and names acceptance oracles.
4. Execute the reviewed plan, then run the listed oracle checks, `agentlaw verify .`, and any focused tests before archiving the plan.
5. Save session state with `agentlaw_session_save` at milestones and before context compaction.

### When to use init, update, fix, and align

- Use `AGENTLAW_INIT_TOOL.md` when bootstrapping agentlaw into a new project or re-initializing the scaffold.
- Use `AGENTLAW_UPDATE_TOOL.md` when the installed kit or scaffold has changed and this project needs those upstream harness updates merged without losing local project facts.
- Use `AGENTLAW_FIX_TOOL.md` when the harness is drifting, a tracker-policy violation happened, the agent bypassed a rule, MCP/memory state looks inconsistent, or the same governance failure repeats.
- Use `AGENTLAW_ALIGN_TOOL.md` and `agentlaw align --check --target .` when local laws, root controls, directories, or command surfaces changed and routing/README surfaces may be stale. Use `agentlaw align --write --target .` only for safe routing updates reported as autofixable.

These four root tools exist because different work needs different safeguards.
Init creates a first scaffold. Update merges a newer shared kit into an
already-localized project without erasing local law. Fix closes observed
governance gaps by choosing the owning layer and the least sufficient
correction. Align repairs routing surfaces after the rules or command surface
move, so agents and humans keep entering through the same path.

### Plan, persona, and oracle review

The normal non-trivial path is plan first, execution second, oracle last. A plan states the task contract, affected surfaces, user gates, risks, rollback paths, and acceptance criteria. The persona review checks that plan from specific lenses such as trigger coverage, acceptance criteria, affected surfaces, external contracts, user gates, and form-vs-substance. The oracle phase checks whether the completed work satisfies the plan's criteria before the plan is archived.

The plan-review gate is intentionally strict. The interview step must be based
on a real user touchpoint, and persona review starts only when the agent can
defend very high clarity about the goal, constraints, success conditions, and
context. Self-challenge is allowed to change the plan before it becomes final;
it is not just a note-taking step. After implementation, oracle checks can run
normally or be started and checked later for long-running work, so the agent
does not need to repeat expensive checks in one blocking call. The archive step
writes closure evidence into the completed plan and refuses to move the plan if
checks are still pending, failing, or recorded in an unauditable shape.

For code changes, the expected evidence loop is focused tests while editing,
then one full project pytest run at final readiness before commit, release, or
any public-ready claim. Evidence-only closeout work should use the harness
verifier and the relevant focused checks instead of rerunning the full suite.

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
systems. Governing content remains in `AGENTLAW_*`, `agentlaw_docs/law/*`, and
`agentlaw_docs/planning-protocol/*`.

### Fix/update path

Before consequential governance work, use the matching root control document:

- `AGENTLAW_FIX_TOOL.md` for local drift, tracker-policy violations, or rule
  bypasses.
- `AGENTLAW_INIT_TOOL.md` for first-time or re-initialization work.
- `AGENTLAW_UPDATE_TOOL.md` for incorporating upstream harness changes.
- `AGENTLAW_ALIGN_TOOL.md` for local routing/readme reconciliation after
  harness structure or command surfaces change.

### Advanced-reference path

Use `agentlaw_docs/law/*` for governing rules, `agentlaw_docs/contracts/*` for tool and update
contracts, and `agentlaw_docs/planning-protocol/*` for plan format, review method, and
persona decks.

## Memory Layout

The canonical memory layer is plain Markdown under `agentlaw_memory/*`:

- `agentlaw_memory/working-set.md` — current goal, next actions, open questions, and
  handoff state.
- `agentlaw_memory/LOOKUP_RULES.md` — how to choose between memory tools and current
  repository reads.
- `agentlaw_memory/known-facts/` — durable facts about the project.
- `agentlaw_memory/logs/` — append-only decisions, corrections, session saves, and
  verification notes.
- `agentlaw_memory/rules/` — durable behavioral rules that do not belong in law.
- `agentlaw_memory/preferences.md` — user or maintainer preferences.

Derived runtime state belongs under `.agentlaw/`, including the SQLite index at
`.agentlaw/index/meta.db` and any downloaded embedding model. `.agentlaw/` is
rebuildable runtime state, not the source of truth.

## Multi-project note

Codex uses a user-level MCP registration, so registering agentlaw with Codex
only makes the command available globally. It is not project setup. When Codex
is opened in a project that has not explicitly run `agentlaw init`,
`agentlaw run-mcp` exits instead of creating `.agentlaw`.

## Project Specialization

After initialization, specialize the starter law and reference files for this
project. Derived runtime state belongs under `.agentlaw/` and is recreated by
the `agentlaw` package.
