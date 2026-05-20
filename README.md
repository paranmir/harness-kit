# agentlaw

A law-first governance kit for AI coding agents — install governance structure before writing code.

`agentlaw` gives any repository a governed starting structure that AI coding agents can read, follow, and maintain. It works for brand-new repositories and already-existing codebases alike. Drop it in, run the bootstrap, and the agent knows what the rules are before it writes a single line.

In practical terms, agentlaw gives your project a stable agreement layer:
law-first governance documents, memory and continuity records, planning and review
rules, verification gates, and agent setup instructions that make those rules
visible to the coding agent at the start of each session.

---

## For Humans

### Install

```bash
pipx install agentlaw
```

### Quick Start

```bash
# 1. Place agentlaw governance into your project
agentlaw init <your-project-dir>

# 2. Register the agentlaw-memory MCP server with your AI agent host.
#    The default `--setup-agents prompt` mode emits LLM-actionable
#    instructions — copy them into your agent and let it edit its host
#    config. Restart the host after the edit lands.

# 3. Open a fresh chat with your AI agent on the project and say:
#       Restore the session
#    The agent loads the harness context and you can start work.
```

### What agentlaw Provides

- **Law-first governance** — a constitution, root control procedures, and a
  law layer under `docs/law/*` that establish what the agent must read before
  implementation.
- **Memory and continuity** — `memory/*` plus the `agentlaw-memory` MCP server,
  so new sessions can restore the current goal, active plans, durable rules,
  preferences, and recent handoff state.
- **Planning and review** — plan templates, task classification, persona decks,
  review coverage, and acceptance-criterion oracles for non-trivial work.
- **Verification** — `agentlaw verify` checks scaffold integrity and drift; the
  runtime also exposes plan-review and memory verification flows through MCP.
- **Host hooks and Agent Skills** — `agentlaw init` installs routing files,
  reminder skills, and additive user-prompt hooks so Claude Code and Codex see
  the same governance entry points.
- **Agent setup** — host-specific setup guidance for Claude Code, Codex, Gemini
  CLI, and other MCP-capable agents without requiring every project to invent
  its own bootstrap instructions.
- **Bootstrap/update/fix flows** — `AGENTLAW_INIT_TOOL.md`,
  `AGENTLAW_UPDATE_TOOL.md`, and `AGENTLAW_FIX_TOOL.md` give agents concrete
  procedures for setup, kit upgrades, and governance-gap repair.

### Using agentlaw in Your AI Coding Session

Once agentlaw is initialized in your project and the `agentlaw-memory` MCP server is registered with your agent host, you drive the harness through natural-language triggers in your conversation. The agent maps each trigger to one of the harness's MCP tools and follows the procedure that the kit ships.

**Triggering session restore** — at the start of any new conversation on the project, say one of:

- "Restore the session" / "세션 복원해봐"
- "Pick up where we left off"
- "지금 어디까지 했어?"

The agent calls `agentlaw_session_restore`, the response packet carries your project's working set, every active plan body, the most recent session_save log entry, framework reminders (memory intent rule, write discipline, consult-before-answer rule), and a step-by-step reminder of the §Canonical Restore Route Mandatory Tier the agent must follow before answering. The first turn of every session is a context-loading turn; substantive work starts on turn two.

**Triggering session save** — when ending a session, before context compaction, or at any milestone, say one of:

- "Save the session" / "세션 저장해줘"
- "Wrap up and save state"
- "Snapshot what we did"

The agent calls `agentlaw_session_save` with the working frame, and the save tool surfaces a post-save verification obligation that you run after.

**When something feels off** — if the agent appears to be answering with stale context or missing rules, ask it to call `agentlaw_session_restore` again, or run `agentlaw mcp-recover --target . --client auto --json` to diagnose MCP connectivity from the shell side.

### Multi-project usage

Install `agentlaw` once with `pipx install agentlaw`, then bootstrap each project independently with `agentlaw init <dir> --setup-agents prompt`. Each initialized project gets its own `<dir>/.harness/index/meta.db`; the memory index is not shared across projects.

Host registration scope differs by agent host:

| Host | Registration scope | Multi-project behavior |
| --- | --- | --- |
| Claude Code | local, per-project and keyed by project path | each project sees only its own `.harness/index/meta.db` |
| Gemini CLI | project-local `.gemini/settings.json` | each project sees only its own `.harness/index/meta.db` |
| Codex | user-level, one global entry | the registration omits `--target`; `agentlaw run-mcp` resolves the target from the cwd where Codex is opened |

`agentlaw verify` checks target scaffold integrity; it does not enforce host MCP scope. Use `agentlaw agent-setup --verify` to check the host registration contract.

### What This Project Is

**The problem.** AI coding agents arrive at a repository without governance. They make plausible-looking changes that violate invariants the team has not written down. The team adds a CLAUDE.md or AGENTS.md to capture rules; the file grows; the agent reads less of it; the rules silently stop applying. The agent and the team need shared structure they can both rely on.

**The kit.** agentlaw installs that structure as governance scaffolding: a constitution, a law layer (memory, artifact, oracle, failure rules), root control tools (init / update / fix), contract documents, a memory subsystem (working set, logs, rules, preferences), and a runtime MCP server that surfaces the rules every session. The agent reads the law before it writes; the kit's verifier mechanically catches drift between the rules and the code.

**Recursive improvement.** The kit develops by using itself. Every plan that lands in the source repository goes through the same `AGENTLAW_INIT_TOOL.md` / `AGENTLAW_UPDATE_TOOL.md` / `AGENTLAW_FIX_TOOL.md` rules the kit ships. Every law change is mirrored to the public seed (this repository) and to the bundled package scaffold. The same `agentlaw verify` you run against your project also runs against the kit itself.

### What This Repository Contains

This public repository is the public seed and reader-facing reference for the
agentlaw harness. Install the package from PyPI; use this repository to inspect
the scaffold, law documents, contracts, planning protocol, and starter memory
layout that `agentlaw init` places into a governed target project.

- `AGENTLAW_CONSTITUTION.md` and `AGENTLAW_*_TOOL.md` — the top-level
  governance documents agents are expected to consult.
- `docs/law/*` — the core law layer: scope, input/output contract, memory,
  artifact, planning, oracle, stewardship, failure taxonomy, and enforcement
  rules.
- `docs/contracts/*` — shared contracts, including the MCP tool surface and
  update workflow.
- `docs/planning-protocol/*` — task classification, plan template, review
  method, persona decks, and persona-to-section map.
- `.agents/skills/*` and `.claude/skills/*` — Agent Skills for Codex-compatible
  and Claude-compatible hosts. They remind the agent to read the governing
  fix/init/update and plan-authoring procedures at the right time.
- `AGENTS.md` and `CLAUDE.md` — routing-only entry maps for agent hosts. They
  point to the law and root control documents; they are not parallel rule
  stores.
- `memory/*` — starter continuity files. In your own project these become the
  local working set, logs, rules, known facts, and preferences.
- `plans/*` — starter plan directories and the tech-debt tracker used by the
  harness structure.

This repository is not an authoring workspace dump. It should not carry local
runtime databases, generated index state, local operation logs, or
machine-specific paths.

### Hooks, Skills, and Memory Layout

After `agentlaw init`, a target project has three agent-visible reminder
channels:

- **User prompt hook** — supported hosts get an additive `UserPromptSubmit`
  hook that runs `agentlaw user-prompt-hook`. Claude Code stores this in
  `.claude/settings.json`; Codex stores it in `.codex/config.toml`. Existing
  hooks are preserved.
- **Agent Skills** — `.agents/skills/agentlaw-governance/` and
  `.claude/skills/agentlaw-governance/` route fix/init/update/governance work
  to `AGENTLAW_FIX_TOOL.md`, `AGENTLAW_INIT_TOOL.md`, or
  `AGENTLAW_UPDATE_TOOL.md`. The matching `agentlaw-plan-authoring` skills
  route plan creation, review, execution, oracle, and archive work to
  `docs/planning-protocol/*`.
- **Routing files** — `AGENTS.md` and `CLAUDE.md` tell the host where the
  governing documents live. They stay short by design.

The canonical memory layout is Markdown:

- `memory/working-set.md` — current goal, next actions, open questions, and
  handoff state.
- `memory/LOOKUP_RULES.md` — when to use memory tools versus current repository
  reads.
- `memory/known-facts/` — durable facts about the target project.
- `memory/logs/` — append-only decisions, corrections, session saves, and
  verification notes.
- `memory/rules/` — durable behavioral rules that do not belong in law.
- `memory/preferences.md` — user or maintainer preferences.

`.harness/` is runtime state. Its SQLite index at `.harness/index/meta.db`,
downloaded embedding model, and generated search state are rebuildable; they
should not be copied into this public seed or treated as source-of-truth
documentation.

### Requirements

- **Python** 3.11 or newer.
- **Operating systems**: Windows, Ubuntu, and macOS supported.
- **Disk**: the embedding model occupies roughly 500 MB once downloaded (cached under `<your-project>/.harness/models/`).
- **Runtime dependencies** are installed automatically by `pipx install agentlaw` from the PyPI package metadata.

### What You Get After `agentlaw init`

```
<your-project>/
├── AGENTLAW_CONSTITUTION.md         # highest authority
├── AGENTLAW_INIT_TOOL.md            # bootstrap entry
├── AGENTLAW_UPDATE_TOOL.md          # update flow
├── AGENTLAW_FIX_TOOL.md             # gap-fix flow
├── AGENTS.md                       # routing-only entry map for agents
├── CLAUDE.md                       # routing-only entry map for Claude Code
├── .agents/skills/                 # Codex-compatible Agent Skills
├── .claude/skills/                 # Claude-compatible Agent Skills
├── docs/
│   ├── law/                        # law layer
│   ├── contracts/                  # boundary contracts
│   ├── planning-protocol/          # plan/review method and personas
│   └── references/                 # local references
├── memory/                         # working-set, logs, rules, preferences
├── plans/                          # active/completed plans and tracker
└── .harness/                       # runtime tree (DB, embedding model)
```

The agent reads law, routing, active plans, and memory on every session restore;
you do not maintain a separate CLAUDE.md / AGENTS.md / .cursorrules as a
parallel rule store.

### Links

- **PyPI**: https://pypi.org/project/agentlaw/
- **Repository**: https://github.com/paranmir/agentlaw

---

## For AI Agents

This section is the agent-facing reading order for first-time entry into a project that uses agentlaw. If you are reading this README as a new session, follow the §Canonical Restore Route Mandatory Tier in `docs/law/MEMORY_AND_CONTINUITY_RULES.md` instead — that procedure is the binding one. This README is the on-ramp; the law file is the substance.

### Agent Bootstrap (first install)

When the user asks you to set up agentlaw in a project:

1. Install agentlaw: `pipx install agentlaw`.
2. Run `agentlaw init <target-project> --setup-agents prompt` in the target. The `prompt` default emits LLM-actionable instructions for registering the `agentlaw-memory` MCP server in your host config; read those instructions and edit your host config rather than letting init auto-write to user-level config (that requires `--setup-agents auto --yes`).
3. Restart your host after the config edit so the new MCP registration is picked up.
4. On the next agent session, call `agentlaw_session_restore` (MCP) or `agentlaw session-restore --target . --json` (CLI fallback) and follow the §Canonical Restore Route Mandatory Tier in the response.
5. If the MCP server is not visible in a new session despite restart, run `agentlaw mcp-recover --target . --client auto --json` to diagnose runtime + registration state.

### Layer-by-layer map (what each artifact class is for)

- **`AGENTLAW_CONSTITUTION.md`** — highest authority; structural invariants. Rare changes; never violate.
- **Root control tools** (`AGENTLAW_INIT_TOOL.md`, `AGENTLAW_UPDATE_TOOL.md`, `AGENTLAW_FIX_TOOL.md`) — agent-facing procedure documents. Init bootstraps a fresh project, Update incorporates kit upgrades into an existing target, Fix runs the gap-resolution protocol.
- **Agent Skills** (`.agents/skills/*`, `.claude/skills/*`) — host-visible
  reminders that route governance and plan-authoring work back to the root
  control tools and planning protocol.
- **`docs/law/*` (law layer)** — rules every session reads: memory and continuity, artifact rules, oracle and judgment, code authorship and stewardship, failure taxonomy, mechanical enforcement policy, starter specialization rules, scope, input/output contract.
- **`docs/contracts/*`** — boundary surfaces shared with the kit (MCP tool surface, shared baseline, update workflow).
- **`docs/references/*`** — research-and-context references; not authoritative.
- **`memory/*`** — derived continuity (working-set, logs, rules, preferences, lookup rules). Below law in authority.
- **`plans/active/*` and `plans/completed/*`** — work-in-flight and historical work. Active plans are read-on-restore.

### Governing hierarchy

Authority flows top-down:

1. `AGENTLAW_CONSTITUTION.md`
2. Root control tools
3. `docs/law/*` (law)
4. `docs/contracts/*`
5. `docs/references/*` (non-authoritative)
6. `memory/*`
7. `AGENTS.md` (routing only — never a rule store)

When two artifacts seem to conflict, the higher one wins. Memory never overrides law; references never override contracts; AGENTS.md is the entry map, not a source of rules.

### Restore procedure on every session start

§Canonical Restore Route Mandatory Tier (full body in `docs/law/MEMORY_AND_CONTINUITY_RULES.md`) requires 14 steps before composing a substantive response. Summary: confirm runtime integrity, read the working set, read every law file, read every active plan body, read the most recent session_save log entry, scan recent_logs titles, read every active rule's body, read `memory/preferences.md`, read `memory/LOOKUP_RULES.md`, scan the known-facts manifest, run a working-frame `memory_search` over `current_goal + next_actions + open_questions`, inspect governance drift, assemble the packet, surface gaps to the user. The runtime pre-fetches body fields into the restore packet so the substance is in your context without extra `Read` calls; the procedure is binding regardless.

### Critical rules — quick reference

One-line restatements; the binding text lives at the anchors.

- **Memory Intent Rule** — when the user expresses intent to remember, persist, or carry forward, resolve to one of `memory_write` / `promotion_proposal` / `associative_marker` / `explicit_non_save` before final response. Anchor: `docs/law/MEMORY_AND_CONTINUITY_RULES.md` §Memory Intent Rule.
- **Write Discipline** — silence is a valid answer; a write must clear the §Log Write Criterion three-question gate (and the §Item Write Criterion applicability gate for items). Volume is not the target; selectivity is. Anchor: §Write Discipline.
- **Read Routing Criterion** — classify the question (prior judgment / cross-session / current source) before reaching for `memory_search` vs `Grep`/`Read`. Anchor: §Read Routing Criterion.
- **Consult-Before-Answer Rule** — for memory-routed questions, consult memory **before** composing the answer, not after. Anchor: §Consult-Before-Answer Rule.
- **Self-Narration Prohibition** — governed artifact bodies and code comments describe current state only; revision history lives in plans, tracker entries, memory logs, and git, not in the body. Anchor: `docs/law/REPOSITORY_ARTIFACT_RULES.md` §Self-Narration Prohibition (paired with §Reasoning-Critical Inline Comments in `CODE_AUTHORSHIP_AND_STEWARDSHIP_RULES.md`).
- **Promotion Proposal Protocol** — runtime never selects promotion candidates; the agent judges whether durability + future operational relevance + authority gap all hold, then calls `memory_propose_promotion`. Anchor: §Promotion Proposal Protocol.

---

## License

MIT.
